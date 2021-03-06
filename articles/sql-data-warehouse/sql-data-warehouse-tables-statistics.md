---
title: "管理 SQL 数据仓库中表的统计信息 | Microsoft 文档"
description: "Azure SQL 数据仓库中表的统计信息入门。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: 4d5777e69b7ea3fa206bf8909c255b998be69e8a
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>管理 SQL 数据仓库中表的统计信息
> [!div class="op_single_selector"]
> * [概述][Overview]
> * [数据类型][Data Types]
> * [分布][Distribute]
> * [索引][Index]
> * [分区][Partition]
> * [统计信息][Statistics]
> * [临时][Temporary]
> 
> 

SQL 数据仓库对数据了解得越多，其针对数据执行查询的速度就越快。  可以通过收集数据统计信息的方式将数据的相关信息告知 SQL 数据仓库。 要优化查询，则收集数据统计信息是你能够做的最重要事情之一。 这是因为，SQL 数据仓库查询优化器是基于成本的优化器。 此优化器会对各种查询计划的成本进行比较，并选择成本最低的计划，该计划也应该是执行速度最快的计划。 例如，如果优化器估计你在查询中筛选的日期会返回 1 行数据，则与该优化器估计你选择的日期会返回 1 百万行数据的情况相比，其选择的计划可能会有很大的不同。

目前，创建和更新统计信息只能手动进行，但操作起来很简单。  不仅之后，我们就能基于单个列和索引自动创建和更新统计信息。  使用以下信息可大大加强数据统计信息的自动化管理。 

## <a name="getting-started-with-statistics"></a>统计信息入门
创建每个列的模板统计信息是开始使用统计信息的简单方式。 过期的统计信息会导致查询性能欠佳。 但是，随着数据的增长，基于所有列更新统计信息可能会消耗内存。 

下面是针对不同方案的一些建议：
| **方案** | 建议 |
|:--- |:--- |
| **入门** | 迁移到 SQL 数据仓库之后更新所有列 |
| **用于统计的最重要列** | 哈希分发键 |
| **用于统计的第二重要列** | 分区键 |
| **用于统计的其他重要列** | 日期、常用的 JOIN、GROUP BY、HAVING 和 WHERE |
| **统计信息更新频率**  | 保守：每日 <br></br> 加载或转换数据之后 |
| **采样** |  低于 10 亿行，使用默认采样 (20%) <br></br> 对于包含 10 亿行以上的表，最好是根据 2% 的范围生成统计信息 |

## <a name="updating-statistics"></a>更新统计信息

最佳实践之一是每天在添加新日期后，更新有关日期列的统计信息。 每次有新行载入数据仓库时，就会添加新的加载日期或事务日期。 这些操作会更改数据分布情况并使统计信息过时。 相反地，有关客户表中的国家/地区列的统计信息可能永远不需要更新，因为值的分布通常不会变化。 假设客户间的分布固定不变，将新行添加到表变化并不会改变数据分布情况。 但是，如果数据仓库只包含一个国家/地区，并且引入了来自新国家/地区的数据，从而导致存储了多个国家/地区的数据，那么，就绝对需要更新有关国家/地区列的统计信息。

在排查查询问题时，首先要询问的问题之一就是**“统计信息是最新的吗？”**

此问题不可以根据数据期限提供答案。 如果对基础数据未做重大更改，则最新的统计信息对象有可能非常陈旧。 如果行数有明显变化或给定列的值分布有重大变化，则需要更新统计信息。

由于系统未提供 DMV 来确定自上次更新统计信息以来表中的数据是否发生更改，因此，如果知道统计信息的期限的话，也许可以大致猜出更新状态。  可以使用以下查询来确定上次更新每个表的统计信息的时间。  

> [!NOTE]
> 请记住，如果给定列的值分布有重大变化，则应该更新统计信息，不管上次更新时间为何。  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

例如，数据仓库中的**日期列**往往需要经常更新统计信息。 每次有新行载入数据仓库时，就会添加新的加载日期或事务日期。 这些操作会更改数据分布情况并使统计信息过时。  相反地，客户表上性别列的统计信息可能永远不需要更新。 假设客户间的分布固定不变，将新行添加到表变化并不会改变数据分布情况。 不过，如果数据仓库只包含一种性别，而新的要求导致多种性别，则肯定需要更新性别列的统计信息。

有关更多说明，请参阅 MSDN 上的[统计信息][Statistics]。

## <a name="implementing-statistics-management"></a>实施统计信息管理
扩展数据加载过程通常是个不错的想法，可确保在加载结束时更新统计信息。 当表更改其大小和/或其值分布时，数据加载最为频繁。 因此，这是实施某些管理过程的合理位置。

下面提供了有关在加载过程中更新统计信息的一些指导原则：

* 确保加载的每个表至少包含一个更新的统计信息对象。 这会在统计信息更新过程中更新表大小（行计数和页计数）信息。
* 将重点放在参与 JOIN、GROUP BY、ORDER BY 和 DISTINCT 子句的列上
* 考虑更频繁地更新“递增键”列（例如事务日期），因为这些值不包含在统计信息直方图中。
* 考虑较不经常更新静态分布列。
* 请记住，每个统计信息对象是连续更新的。 仅实现 `UPDATE STATISTICS <TABLE_NAME>` 可能不太理想 - 尤其是对包含许多统计信息对象的宽型表而言。

> [!NOTE]
> 有关 [递增键] 的详细信息，请参阅 SQL Server 2014 基数估计模型白皮书。
> 
> 

有关更多说明，请参阅 MSDN 上的[基数估计][Cardinality Estimation]。

## <a name="examples-create-statistics"></a>示例：创建统计信息
以下示例演示如何使用各种选项来创建统计信息。 用于每个列的选项取决于数据特征以及在查询中使用列的方式。

### <a name="a-create-single-column-statistics-with-default-options"></a>A. 使用默认选项创建单列统计信息
若要基于某个列创建统计信息，只需提供统计信息对象的名称和列的名称。

此语法使用所有默认选项。 默认情况下，SQL 数据仓库在创建统计信息时**对 20% 的表采样**。

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

例如：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. 通过检查每个行创建单列统计信息
20% 的默认采样率足以应付大多数情况。 不过，可以调整采样率。

若要采样整个表，请使用此语法：

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

例如：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. 通过指定样本大小创建单列统计信息
或者，可以以百分比指定样本大小：

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. 只对某些行创建单列统计信息
另一个选项是对表中的部分行创建统计信息。 这称为筛选的统计信息。

例如，在计划查询大型分区表的特定分区时，可以使用筛选的统计信息。 只对分区值创建统计信息，统计信息的准确度会改善，并因而改善查询性能。

此示例会基于一系列的值创建统计信息。 可以轻松定义这些值以匹配分区中的值范围。

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> 若要让查询优化器在选择分布式查询计划时考虑使用筛选的统计信息，查询必须符合统计信息对象的定义。 使用上述示例，查询的 where 子句需要指定介于 2000101 和 20001231 之间的 col1 值。
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. 使用所有选项创建单列统计信息
当然，可以将选项组合在一起。 以下示例使用自定义样本大小创建筛选的统计信息对象：

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

有关完整参考，请参阅 MSDN 上的 [CREATE STATISTICS][CREATE STATISTICS]。

### <a name="f-create-multi-column-statistics"></a>F. 创建多列统计信息
若要创建多列统计信息，只需使用上述示例，但要指定更多的列。

> [!NOTE]
> 用于估计查询结果中行数的直方图只适用于统计信息对象定义中所列的第一个列。
> 
> 

在此示例中，直方图位于 product\_category。 跨列统计信息是根据 *product\_category* 和 *product\_sub_category* 计算的：

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

由于 product\_category 和 product\_sub\_category 之间存在关联，因此在同时访问这些列时，多列统计信息相当有用。

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. 基于表中的所有列创建统计信息
创建统计信息的方法之一是在创建表后发出 CREATE STATISTICS 命令。

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. 使用存储过程基于数据库中的所有列创建统计信息
SQL 数据仓库不提供相当于 SQL Server 中 [sp_create_stats][] 的系统存储过程。 此存储过程将基于数据库中尚不包含统计信息的每个列创建单列统计信息对象。

这可以帮助你开始进行数据库设计。 可以根据需要任意改写此存储过程。

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

若要使用此过程对表中的所有列创建统计信息，只需调用该过程即可。

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>示例：更新统计信息
要更新统计信息，可以：

1. 更新一个统计信息对象。 指定要更新的统计信息对象名称。
2. 更新表中的所有统计信息对象。 指定表名称，而不是一个特定的统计信息对象。

### <a name="a-update-one-specific-statistics-object"></a>A. 更新一个特定的统计信息对象
使用以下语法来更新特定的统计信息对象：

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

例如：

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

通过更新特定统计信息对象，可以减少管理统计信息所需的时间和资源。 在选择要更新的最佳统计信息对象之前，需要经过一定的思考。

### <a name="b-update-all-statistics-on-a-table"></a>B. 更新表中的所有统计信息
此示例演示了更新表中所有统计信息对象的一个简单方法。

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

例如：

```sql
UPDATE STATISTICS dbo.table1;
```

此语句很容易使用。 只要记住，这会更新表中的所有统计信息，因此执行的工作可能会超过所需的数量。 如果性能不是一个考虑因素，这绝对是保证拥有最新统计信息的最简单、最全面的操作方式。

> [!NOTE]
> 更新表中的所有统计信息时，SQL 数据仓库将执行扫描，以针对每个统计信息进行表采样。 如果表很大、包含许多列和许多统计信息，则根据需要更新各项统计信息可能比较有效率。
> 
> 

有关 `UPDATE STATISTICS` 过程的实现，请参阅[临时表][Temporary]一文。 实现方法与上述 `CREATE STATISTICS` 过程略有不同，但最终结果相同。

有关完整语法，请参阅 MSDN 上的[更新统计信息][Update Statistics]。

## <a name="statistics-metadata"></a>统计信息元数据
可以使用多个系统视图和函数来查找有关统计信息的信息。 例如，使用 stats-date 函数查看上次创建或更新统计信息的时间，即可了解统计信息对象是否可能已过时。

### <a name="catalog-views-for-statistics"></a>统计信息的目录视图
这些系统视图提供有关统计信息的信息：

| 目录视图 | 说明 |
|:--- |:--- |
| [sys.columns][sys.columns] |针对每个列提供一行。 |
| [sys.objects][sys.objects] |针对数据库中的每个对象提供一行。 |
| [sys.schemas][sys.schemas] |针对数据库中的每个架构提供一行。 |
| [sys.stats][sys.stats] |针对每个统计信息对象提供一行。 |
| [sys.stats_columns][sys.stats_columns] |针对统计信息对象中的每个列提供一行。 链接回到 sys.columns。 |
| [sys.tables][sys.tables] |针对每个表（包括外部表）提供一行。 |
| [sys.table_types][sys.table_types] |针对每个数据类型提供一行。 |

### <a name="system-functions-for-statistics"></a>统计信息的系统函数
这些系统函数适合用于处理统计信息：

| 系统函数 | 说明 |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |上次更新统计信息对象的日期。 |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |提供有关统计信息对象识别的值分布的摘要级别和详细信息。 |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>将统计信息列和函数合并成一个视图
此视图将统计信息相关的列以及 [STATS_DATE()][] 函数的结果合并在一起。

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>DBCC SHOW_STATISTICS() 示例
DBCC SHOW_STATISTICS() 显示统计信息对象中保存的数据。 这些数据包括三个组成部分。

1. 标头
2. 密度向量
3. 直方图

有关统计信息的标头元数据。 直方图显示统计信息对象的第一个键列中的值分布。 密度向量可度量跨列相关性。 SQLDW 使用统计信息对象中的任何数据来计算基数估计值。

### <a name="show-header-density-and-histogram"></a>显示标头、密度和直方图
此简单示例显示了统计信息对象的所有三个组成部分。

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

例如：

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>显示 DBCC SHOW_STATISTICS(); 的一个或多个组成部分
如果只想要查看特定部分，请使用 `WITH` 子句并指定要查看哪些部分：

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

例如：

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() 差异
相比于 SQL Server，在 SQL 数据仓库中，DBCC SHOW_STATISTICS() 的实现更加严格。

1. 未阐述的功能不受支持
2. 不能使用 Stats_stream
3. 不能联接特定统计信息子集的结果，例如 (STAT_HEADER JOIN DENSITY_VECTOR)
4. 不能针对消息隐藏设置 NO_INFOMSGS
5. 不能在统计信息名称的前后使用方括号
6. 不能使用列名来标识统计信息对象
7. 不支持自定义错误 2767

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅 MSDN 上的 [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS]。  要了解详细信息，请参阅有关[表概述][Overview]、[表数据类型][Data Types]、[分布表][Distribute]、[为表编制索引][Index]、[将表分区][Partition]和[临时表][Temporary]的文章。  有关最佳实践的详细信息，请参阅 [SQL 数据仓库最佳实践][SQL Data Warehouse Best Practices]。  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
