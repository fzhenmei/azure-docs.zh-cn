# [Service Fabric 文档](/azure/service-fabric)
# 概述
## [什么是 Service Fabric？](service-fabric-overview.md)

# 快速入门
## [创建 .NET 应用程序](service-fabric-quickstart-dotnet.md)
## [部署 Linux 容器应用程序](service-fabric-quickstart-containers-linux.md)
## [部署 Windows 容器应用程序](service-fabric-quickstart-containers.md)
## Java 快速入门
### [部署 Spring Boot 应用程序](service-fabric-quickstart-java-spring-boot.md)
### [部署 Reliable Services 应用程序](service-fabric-quickstart-java-reliable-services.md)

# 教程
## 部署 .NET 应用
### [1 - 生成 .NET 应用程序](service-fabric-tutorial-create-dotnet-app.md)
### [2 - 部署应用程序](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3 - 配置 CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4 - 监视和诊断](service-fabric-tutorial-monitoring-aspnet.md)

## 将现有 .NET 应用容器化
### [1 - 使用 Docker Compose 部署 .NET 应用](service-fabric-host-app-in-a-container.md)
### [2 - 监视容器](service-fabric-tutorial-monitoring-wincontainers.md)

## 创建 Linux 容器应用
### [1- 创建容器映像](service-fabric-tutorial-create-container-images.md)
### [2 - 打包和部署容器](service-fabric-tutorial-package-containers.md)
### [3 - 故障转移和缩放](service-fabric-tutorial-containers-failover.md)

## 创建和管理群集
### 1- 在 Azure 上创建群集
#### [1a- 创建 Windows 群集](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1b- 创建 Linux 群集](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2- 缩放群集](service-fabric-tutorial-scale-cluster.md)
### [3- 升级群集运行时](service-fabric-tutorial-upgrade-cluster.md)
### [4- 部署 API 管理与 Service Fabric](service-fabric-tutorial-deploy-api-management.md)

# 示例
## [代码示例](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric CLI](samples-cli.md)

# 概念
## [了解微服务](service-fabric-overview-microservices.md)
## [大图](service-fabric-content-roadmap.md)
## [应用程序方案](service-fabric-application-scenarios.md)
## [模式和方案](service-fabric-patterns-and-scenarios.md)
## [体系结构](service-fabric-architecture.md)
## [术语](service-fabric-technical-overview.md)

## [支持的编程模型](service-fabric-choose-framework.md)
### [容器](service-fabric-containers-overview.md)
#### [Docker Compose（预览）](service-fabric-docker-compose.md)
#### [资源调控](service-fabric-resource-governance.md)
### [Reliable Services](service-fabric-reliable-services-introduction.md)
#### [Reliable Services 生命周期 - C#](service-fabric-reliable-services-lifecycle.md)
#### [Reliable Services 生命周期 - Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
#### [Reliable Collection 指导原则和建议](service-fabric-reliable-services-reliable-collections-guidelines.md)
#### [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [事务和锁](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
#### [可靠的并发队列](service-fabric-reliable-services-reliable-concurrent-queue.md)
#### [Reliable Collection 序列化](service-fabric-reliable-services-reliable-collections-serialization.md)
#### [可靠状态管理器和 Reliable Collection 内部](service-fabric-reliable-services-reliable-collections-internals.md)
#### [高级使用率](service-fabric-reliable-services-advanced-usage.md)

### [Reliable Actors](service-fabric-reliable-actors-introduction.md)
#### [体系结构](service-fabric-reliable-actors-platform.md)
#### [生命周期和垃圾回收](service-fabric-reliable-actors-lifecycle.md)
#### [状态管理](service-fabric-reliable-actors-state-management.md)
#### [多形性](service-fabric-reliable-actors-polymorphism.md)
#### [重新进入](service-fabric-reliable-actors-reentrancy.md)
#### [类型序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

## 应用程序和服务
### [应用程序模型](service-fabric-application-model.md)
### [应用程序和服务清单](service-fabric-application-and-service-manifests.md)
### [托管模型](service-fabric-hosting-model.md)

### [服务状态](service-fabric-concepts-state.md)
### [服务分区](service-fabric-concepts-partitioning.md)
### [应用程序的可伸缩性](service-fabric-concepts-scalability.md)
### [服务的可用性](service-fabric-availability-services.md)
### [副本和实例生命周期](service-fabric-concepts-replica-lifecycle.md)
### [重新配置](service-fabric-concepts-reconfiguration.md)

### [服务通信](service-fabric-connect-and-communicate-with-services.md)
#### [DNS 服务](service-fabric-dnsservice.md)
#### [反向代理](service-fabric-reverseproxy.md)
#### [配置反向代理以进行安全通信](service-fabric-reverseproxy-configure-secure-communication.md)
#### [反向代理诊断](service-fabric-reverse-proxy-diagnostics.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [应用程序生命周期](service-fabric-application-lifecycle.md)
#### [应用程序升级](service-fabric-application-upgrade.md)
##### [配置](service-fabric-visualstudio-configure-upgrade.md)
##### [应用程序升级参数](service-fabric-application-upgrade-parameters.md)
##### [应用程序升级中的数据序列化](service-fabric-application-upgrade-data-serialization.md)
##### [应用程序升级的高级主题](service-fabric-application-upgrade-advanced.md)
#### [管理多个环境的应用程序](service-fabric-manage-multiple-environment-app-configuration.md)
#### [使用故障分析来测试应用](service-fabric-testability-overview.md)
#### [ImageStoreConnectionString 设置](service-fabric-image-store-connection-string.md)

### [服务资源](service-fabric-service-manifest-resources.md)

## [群集](service-fabric-deploy-anywhere.md)
### [群集安全性](service-fabric-cluster-security.md)
### [Linux 和 Windows 之间的功能差异](service-fabric-linux-windows-differences.md)
### Azure 上的群集
#### [节点类型和 VM 规模集](service-fabric-cluster-nodetypes.md)
#### [群集网络模式](service-fabric-patterns-networking.md)
### [群集资源管理器](service-fabric-cluster-resource-manager-introduction.md)
#### [体系结构](service-fabric-cluster-resource-manager-architecture.md)
#### [描述群集](service-fabric-cluster-resource-manager-cluster-description.md)
#### [应用程序组概述](service-fabric-cluster-resource-manager-application-groups.md)
#### [配置群集 Resource Manager 设置](service-fabric-cluster-resource-manager-configure-services.md)
#### [资源消耗度量值](service-fabric-cluster-resource-manager-metrics.md)
#### [使用服务相关性](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [服务放置策略](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [管理群集](service-fabric-cluster-resource-manager-management-integration.md)
#### [群集碎片整理](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [均衡群集](service-fabric-cluster-resource-manager-balancing.md)
#### [限制](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [服务移动](service-fabric-cluster-resource-manager-movement-cost.md)

## 监视和诊断
### [监视和诊断应用程序](service-fabric-diagnostics-overview.md)
### 生成事件
#### [生成平台级别事件](service-fabric-diagnostics-event-generation-infra.md)
##### [运行通道](service-fabric-diagnostics-event-generation-operational.md)
##### [Reliable Services 事件](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors 事件](service-fabric-reliable-actors-diagnostics.md)
##### [性能指标](service-fabric-diagnostics-event-generation-perf.md)
##### [监视服务远程处理](service-fabric-reliable-serviceremoting-diagnostics.md)
#### [生成应用程序级别事件](service-fabric-diagnostics-event-generation-app.md)
### 检查应用程序和群集运行状况
#### [监视 Service Fabric 运行状况](service-fabric-health-introduction.md)
#### [报告并检查服务运行状况](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [添加自定义运行状况报告](service-fabric-report-health.md)
#### [使用系统运行状况报告进行故障排除](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [查看运行状况报告](service-fabric-view-entities-aggregated-health.md)
### 聚合事件
#### [使用 EventFlow 聚合事件](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### 使用 Azure 诊断聚合事件
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### 分析事件
#### [使用 Application Insights 分析事件](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [使用 OMS 分析事件](service-fabric-diagnostics-event-analysis-oms.md)
### [排查本地群集的故障](service-fabric-troubleshoot-local-cluster-setup.md)

## [与 API 管理集成](service-fabric-api-management-overview.md)

# 操作指南
## 设置开发环境
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
### [设置 Service Fabric CLI](service-fabric-cli.md)

## 规划和准备
### [计划群集容量](service-fabric-cluster-capacity.md)
### [独立群集部署计划](service-fabric-cluster-standalone-deployment-preparation.md)
### [为灾难恢复做准备](service-fabric-disaster-recovery.md)
### [规划应用程序容量](service-fabric-capacity-planning.md)

## 创建第一个...
### [Visual Studio 中的 C# 应用程序](service-fabric-create-your-first-application-in-visual-studio.md)
### [Windows 容器应用程序](service-fabric-get-started-containers.md)
### [Linux 容器应用程序](service-fabric-get-started-containers-linux.md)
### [基于 Windows 的 C# Reliable Services 应用程序](service-fabric-reliable-services-quick-start.md)
### [基于 Linux 的 Java Reliable Services 应用程序](service-fabric-reliable-services-quick-start-java.md)
### [基于 Linux 的 C# Reliable Services 应用程序](service-fabric-create-your-first-linux-application-with-csharp.md)
### [基于 Windows 的 C# Reliable Actors 应用程序](service-fabric-reliable-actors-get-started.md)
### [基于 Linux 的 Java Reliable Actors 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
### [基于 Windows 的来宾可执行应用程序](quickstart-guest-app.md)
### [独立群集](service-fabric-get-started-standalone-cluster.md)

## 构建应用程序

### 生成来宾可执行服务
#### [部署来宾可执行文件](service-fabric-deploy-existing-app.md)
#### [部署多个来宾可执行文件](service-fabric-deploy-multiple-apps.md)
### 生成容器服务
#### [容器安全性](service-fabric-securing-containers.md)
#### [Docker Compose（预览）](service-fabric-docker-compose.md)
#### [容器和服务的资源调控](service-fabric-resource-governance.md)
#### [卷和日志记录驱动程序](service-fabric-containers-volume-logging-drivers.md)
#### [容器内的服务](service-fabric-services-inside-containers.md)
#### [容器网络模式](service-fabric-networking-modes.md)

### 生成 Reliable Services 服务
#### Reliable Collections
##### [使用 Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [可靠的并发队列](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection 序列化](service-fabric-reliable-services-reliable-collections-serialization.md)

#### 与服务进行通信
##### [与 Reliable Services 通信](service-fabric-reliable-services-communication.md)

##### [服务远程处理 - C#](service-fabric-reliable-services-communication-remoting.md)
##### [服务远程处理 - Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [保护通信 - C#](service-fabric-reliable-services-secure-communication.md)
##### [保护通信 - Java](service-fabric-reliable-services-secure-communication-java.md)

#### [配置](service-fabric-reliable-services-configuration.md)
#### [发送通知](service-fabric-reliable-services-notifications.md)
#### [备份和还原](service-fabric-reliable-services-backup-restore.md)

### 生成 Reliable Actors 服务
#### [发送通知](service-fabric-reliable-actors-events.md)
#### [设置计时器和提醒](service-fabric-reliable-actors-timers-reminders.md)
#### [配置 KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [配置通信设置](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [配置 ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [迁移旧的 Java 应用程序以支持 Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [配置反向代理以进行安全通信](service-fabric-reverseproxy-configure-secure-communication.md)

### [生成 ASP.NET Core 服务](service-fabric-add-a-web-frontend.md)

### 配置安全性
#### [管理应用程序机密](service-fabric-application-secret-management.md)  
#### [配置应用程序的安全策略](service-fabric-application-runas-security.md)

## 在 Windows/VS 开发环境中工作
### [在 Visual Studio 中管理应用程序](service-fabric-manage-application-in-visual-studio.md)
### [在 Visual Studio 中配置安全连接](service-fabric-visualstudio-configure-secure-connections.md)
### [在 VS 中调试 .NET 服务](service-fabric-debugging-your-application.md)
### [常见错误和异常](service-fabric-errors-and-exceptions.md)
### [在本地监视和诊断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [在 Windows 上设置 Linux 群集](service-fabric-local-linux-cluster-windows.md)

## 在 Linux/Eclipse 开发环境中工作
### [用于 Java 开发的 Eclipse 插件入门](service-fabric-get-started-eclipse.md)
### [在 Eclipse 中调试 Java 服务](service-fabric-debugging-your-application-java.md)
### [在本地监视和诊断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## 从云服务迁移
### [比较云服务和 Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [迁移到 Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [建议的做法](/azure/architecture/service-fabric/migrate-from-cloud-services)

## 管理应用程序生命周期
### [打包应用程序](service-fabric-package-apps.md)
### [将参数与配置文件配合使用](service-fabric-how-to-parameterize-configuration-files.md)
### [使用参数指定端口号](service-fabric-how-to-specify-port-number-using-parameters.md)
### [指定环境变量](service-fabric-how-to-specify-environment-variables.md)

### 部署或删除应用程序
#### [在本地群集上部署应用程序](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure 资源管理器](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
#### [FabricClient API](service-fabric-deploy-remove-applications-fabricclient.md)

### 升级应用程序
#### [使用 Azure PowerShell 进行升级](service-fabric-application-upgrade-tutorial-powershell.md)
#### [使用 Visual Studio 进行升级](service-fabric-application-upgrade-tutorial.md)
#### [排查应用程序升级故障](service-fabric-application-upgrade-troubleshooting.md)

### 测试应用程序和服务
#### [测试服务到服务通信](service-fabric-testability-scenarios-service-communication.md)
#### 模拟失败
##### [使用受控的混沌](service-fabric-controlled-chaos.md)
##### [使用测试操作](service-fabric-testability-actions.md)
##### [运行工作负荷期间](service-fabric-testability-workload-tests.md)
##### [使用测试方案](service-fabric-testability-scenarios.md)
##### [使用节点转换 API](service-fabric-node-transition-apis.md)

### 设置持续集成
#### [使用 VSTS 设置持续集成](service-fabric-set-up-continuous-integration.md)
#### [使用 Jenkins 部署 Linux 应用程序](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## 创建和管理群集
### Azure 上的群集
#### 创建
##### [Azure portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure 资源管理器](service-fabric-cluster-creation-via-arm.md)
#### 缩放
##### [手动](service-fabric-cluster-scale-up-down.md)
##### [以编程方式](service-fabric-cluster-programmatic-scaling.md)
#### [升级](service-fabric-cluster-upgrade.md)
#### [设置访问控制](service-fabric-cluster-security-roles.md)
#### [配置](service-fabric-cluster-fabric-settings.md)
#### [打开负载均衡器的端口](create-load-balancer-rule.md)
#### [管理群集证书](service-fabric-cluster-security-update-certs-azure.md)
#### [删除](service-fabric-cluster-delete.md)

### 独立群集
#### 创建
##### [在本地创建](service-fabric-cluster-creation-for-windows-server.md)
##### [使用证书进行保护](service-fabric-windows-cluster-x509-security.md)  
##### [使用 Windows 安全进行保护](service-fabric-windows-cluster-windows-security.md)
##### [独立包的内容](service-fabric-cluster-standalone-package-contents.md)
#### [缩放](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [设置访问控制](service-fabric-cluster-security-roles.md)
#### [配置](service-fabric-cluster-manifest.md)
#### [升级](service-fabric-cluster-upgrade-windows-server.md)

### [可视化群集](service-fabric-visualizing-your-cluster.md)
### [连接到安全群集](service-fabric-connect-to-secure-cluster.md)
### [修补群集节点](service-fabric-patch-orchestration-application.md)

## 监视和诊断
### OMS
#### [设置 OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)
#### [添加 OMS 代理](service-fabric-diagnostics-oms-agent.md)
#### [监视容器](service-fabric-diagnostics-oms-containers.md)
### 性能监视
#### [通过 WAD 监视性能](service-fabric-diagnostics-perf-wad.md)

# 引用
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI (az sf)](/cli/azure/sf)
## [Service Fabric CLI (sfctl)](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [Java API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [服务模型 XML 架构](service-fabric-service-model-schema.md)
## [环境变量](service-fabric-environment-variables-reference.md)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/)
## [常见问题](service-fabric-common-questions.md)
## [学习路径](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [定价](https://azure.microsoft.com/pricing/details/service-fabric/)
## [定价计算器](https://azure.microsoft.com/pricing/calculator/)
## [代码示例](http://aka.ms/servicefabricsamples)
## [支持选项](service-fabric-support.md)
## [服务更新](https://azure.microsoft.com/updates/?product=service-fabric)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)