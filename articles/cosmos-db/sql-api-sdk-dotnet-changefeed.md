---
title: "Azure Cosmos DB：.NET 更改源处理器 API、SDK 和资源 | Microsoft Docs"
description: "了解有关更改源处理器 API 和 SDK 的全部信息，包括发布日期、停用日期和 .NET 更改源处理器 SDK 各版本之间所做的更改。"
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: maquaran
ms.openlocfilehash: 7aa58b9a0fe4ca9e162722d277a951f8ac25013a
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2017
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 更改源处理器 SDK：下载和发行说明
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST 资源提供程序](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

|   |   |
|---|---|
|**SDK 下载**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 文档**|[更改源处理器库 API 参考文档](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**入门**|[更改源处理器 .NET SDK 入门](change-feed.md)|
|**当前受支持的框架**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>发行说明

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* 增加了对 .NET Standard 2.0 的支持。 程序包现在支持 `netstandard2.0` 和 `net451` Framework 名字对象。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 及更高版本。
* 兼容 [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 及更高版本。

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* 修复了更改源为空或无任何工作挂起时计算剩余工作估计值时的一个问题。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* 添加了一个获取更改源中剩余待处理工作估计值的方法。
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 及更高版本。

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* 兼容 [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.14.1 及更低版本。

## <a name="release--retirement-dates"></a>发布和停用日期
Microsoft 至少会在停用 SDK 的 **12 个月**之前发出通知，以便顺利转换到更新的/受支持的版本。

新特性和功能以及优化仅添加到当前 SDK，因此建议始终尽早升级到最新的 SDK 版本。 

使用已停用的 SDK 对 Cosmos DB 发出的任何请求都会被服务拒绝。

<br/>

| 版本 | 发布日期 | 停用日期 |
| --- | --- | --- |
| [1.2.0](#1.2.0) |2017 年 10 月 31 日 |--- |
| [1.1.1](#1.1.1) |2017 年 8 月 29 日 |--- |
| [1.1.0](#1.1.0) |2017 年 8 月 13 日 |--- |
| [1.0.0](#1.0.0) |2017 年 7 月 7 日 |--- |


## <a name="faq"></a>常见问题
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另请参阅
若要了解有关 Cosmos DB 的详细信息，请参阅 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服务页。 

