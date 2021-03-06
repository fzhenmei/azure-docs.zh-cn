---
title: "使用 Azure IoT Edge 创建透明网关设备 | Microsoft 文档"
description: "使用 Azure IoT Edge 创建可为多个设备处理信息的透明网关设备"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 12/04/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 25f4cea1908a0f9bdf387ddfed5f29e6d19bdd20
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2017
---
# <a name="create-an-iot-edge-device-that-acts-as-a-transparent-gateway---preview"></a>创建充当透明网关的 IoT Edge 设备 -预览版

本文提供了有关将 IoT Edge 设备用作透明网关的详细说明。 对于本文的其余部分，术语 IoT Edge 网关指的是用作透明网关的 IoT Edge 设备。 有关更多详细信息，请参阅[如何将 IoT Edge 设备用作网关][lnk-edge-as-gateway]，其中给出了概念概述。 

>[!NOTE]
>当前：
> * 如果网关与 IoT 中心断开连接，则下游设备无法通过网关进行身份验证。
> * IoT Edge 设备无法连接到 IoT Edge 网关。

## <a name="understand-the-azure-iot-device-sdk"></a>了解 Azure IoT 设备 SDK


安装在所有 IoT Edge 设备中的 Edge 中心将以下基元公开给下游设备：

* 设备到云和云到设备的消息
* 直接方法
* 设备孪生操作

目前，通过 IoT Edge 网关进行连接时，下游设备不能使用文件上传。

使用 Azure IoT 设备 SDK 将设备连接到 IoT Edge 网关时，需要：

* 使用引用网关设备主机名的连接字符串来设置下游设备；并且
* 确保下游设备信任网关设备用于接受连接的证书。

使用控件脚本安装 Azure IoT Edge 运行时的时候，将为边缘中心创建一个证书，正如在 [Windows][lnk-tutorial1-win] 和 [Linux][lnk-tutorial1-lin] 模拟设备上安装 IoT Edge 这一教程中那样。 该证书由边缘中心用来接受传入 TLS 连接，且连接到网关设备时，该证书必须受到下游设备的信任。

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们使用的是用于启用 IoT 中心 [X.509 CA 安全性][lnk-iothub-x509]的证书设置，其中包括与特定 IoT 中心关联的 X.509 CA 证书（IoT 中心所有者 CA）以及一系列安装在 IoT Edge 设备中通过该 CA 签名的证书。

>[!IMPORTANT]
>目前，IoT Edge 设备和下游设备只能使用 [SAS 令牌][lnk-iothub-tokens]来通过 IoT 中心进行身份验证。 该证书将只用于验证叶和网关设备之间的 TLS 连接。

我们的配置将“IoT 中心所有者 CA”用作：
* 所有 IoT Edge 设备上 IoT Edge 运行时的设置的签名证书，以及
* 安装在下游设备中的公钥证书。

这得出一个解决方案，该方案使得所有设备可使用任意一个 IoT Edge 设备作为网关（只要它们连接到同一个 IoT 中心）。

## <a name="create-the-certificates-for-test-scenarios"></a>创建测试方案的证书

可以使用[管理 CA 证书示例][lnk-ca-scripts]中描述的示例 Powershell 和 Bash 脚本生成自签名 IoT 中心所有者 CA 和用其签名的设备证书。

>[!IMPORTANT]
>此示例仅用于测试目的。 有关生产方案的信息，请参阅[保护 IoT 部署][lnk-iothub-secure-deployment]获取有关如何保护 IoT 解决方案和对证书进行相应预配的 Azure IoT 指南。


1. 从 GitHub 克隆 [Microsoft Azure IoT SDK 和适用于 C 的库]：

   ```
   git clone -b modules-preview https://github.com/Azure/azure-iot-sdk-c.git 
   ```

2. 若要安装证书脚本，请按照[管理 CA 证书示例][lnk-ca-scripts]的“步骤 1 - 初始设置”中的说明进行操作。 
3. 若要生成 IoT 中心所有者 CA，请按照“步骤 2 - 创建证书链”中的说明进行操作。 下游设备使用此文件来验证连接。
4. 若要为网关设备生成证书，请使用 Bash 或 PowerShell 说明：

### <a name="bash"></a>Bash

创建新的设备证书：

   ```
   ./certGen.sh create_edge_device_certificate myGateway
   ```

创建新文件：.\certs\new-edge-device.* 包含公钥和 PFX，.\private\new-edge-device.key.pem 包含设备私钥。
 
在 `certs` 目录中运行以下命令，以获取完整的设备公钥链：

   ```
   cat ./new-edge-device.cert.pem ./azure-iot-test-only.intermediate.cert.pem ./azure-iot-test-only.root.ca.cert.pem > ./new-edge-device-full-chain.cert.pem
   ```

### <a name="powershell"></a>Powershell

创建新的设备证书： 
   ```
   New-CACertsEdgeDevice myGateway
   ```

创建新的 myEdgeDevice* 文件，这些文件包含公钥、私钥和该证书的 PFX。 

在签名过程中提示输入密码时，请输入“1234”。

## <a name="configure-a-gateway-device"></a>配置网关设备

若要将 IoT Edge 设备配置为网关，只需配置为使用上一部分中创建的设备证书。

我们假定以下文件名来自上面的示例脚本：

| 输出 | Bash 脚本 | PowerShell |
| ------ | ----------- | ---------- |
| 设备证书 | `certs/new-edge-device.cert.pem` | `certs/new-edge-device.cert.pem` |
| 设备私钥 | `private/new-edge-device.cert.pem` | `private/new-edge-device.cert.pem` |
| 设备证书链 | `certs/new-edge-device-full-chain.cert.pem` | `certs/new-edge-device-full-chain.cert.pem` |
| IoT 中心所有者 CA | `certs/azure-iot-test-only.root.ca.cert.pem` | `RootCA.pem` |

将设备和证书信息提供给 IoT Edge 运行时。 
 
在 Linux 中，使用 Bash 输出：

   ```
   sudo iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/certs/azure-iot-test-only.root.ca.cert.pem
   ```

在 Windows 中，使用 PowerShell 输出：

   ```
   iotedgectl setup --connection-string {device connection string}
        --edge-hostname {gateway hostname, e.g. mygateway.contoso.com}
        --device-ca-cert-file {full path}/certs/new-edge-device.cert.pem
        --device-ca-chain-cert-file {full path}/certs/new-edge-device-full-chain.cert.pem
        --device-ca-private-key-file {full path}/private/new-edge-device.key.pem
        --owner-ca-cert-file {full path}/RootCA.pem
   ```

默认情况下，示例脚本不设置设备私钥的密码。 如果要设置密码，请添加以下参数：

   ```
   --device-ca-passphrase {passphrase}
   ```

此脚本将提示设置边缘代理证书的密码。 在该命令后重启 IoT Edge 运行时：

   ```
   iotedgectl restart
   ```

## <a name="configure-a-downstream-device"></a>配置下游设备

下游设备可以是使用 [Azure IoT 设备 SDK][lnk-devicesdk] 的任何应用程序，如[使用 .NET 将设备连接到 IoT 中心][lnk-iothub-getstarted]中描述的简单设备。

首先，下游设备应用程序必须信任 IoT 中心所有者 CA 证书，以便验证与网关设备的 TLS 连接。 通常可通过两种方法执行此步骤：在 OS 级别，或（对于某些语言而言）在应用程序级别。

例如，对于 .NET 应用程序，可以添加下片段来信任以 PEM 格式存储在路径 `certPath` 中的证书。 根据使用的脚本版本，路径可引用 `certs/azure-iot-test-only.root.ca.cert.pem`(Bash) 或 `RootCA.pem`(Powershell)。

   ```
   using System.Security.Cryptography.X509Certificates;
   
   ...

   X509Store store = new X509Store(StoreName.Root, StoreLocation.CurrentUser);
   store.Open(OpenFlags.ReadWrite);
   store.Add(new X509Certificate2(X509Certificate2.CreateFromCertFile(certPath)));
   store.Close();
   ```

在 OS 级别执行此步骤与在 Windows 之间和 Linux 分发版之间执行是不同的。

第二步是使用引用网关设备主机名的连接字符串来初始化 IoT Hub 设备 sdk。
通过将 `GatewayHostName` 属性追加到设备连接字符串来完成此步骤。 例如，此处为设备的示例设备连接字符串，我们向它追加了 `GatewayHostName` 属性：

   ```
   HostName=yourHub.azure-devices-int.net;DeviceId=yourDevice;SharedAccessKey=2BUaYca45uBS/O1AsawsuQslH4GX+SPkrytydWNdFxc=;GatewayHostName=mygateway.contoso.com
   ```

这两个步骤使设备应用程序连接到网关设备。

## <a name="next-steps"></a>后续步骤
[了解开发 IoT Edge 模块的要求和工具][lnk-module-dev]。

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-tutorial1-win]: tutorial-simulate-device-windows.md
[lnk-tutorial1-lin]: tutorial-simulate-device-linux.md
[lnk-edge-as-gateway]: ./iot-edge-as-gateway.md
[lnk-module-dev]: module-development.md
[lnk-iothub-getstarted]: ../iot-hub/iot-hub-csharp-csharp-getstarted.md
[lnk-iothub-x509]: ../iot-hub/iot-hub-x509ca-overview.md
[lnk-iothub-secure-deployment]: ../iot-hub/iot-hub-security-deployment.md
[lnk-iothub-tokens]: ../iot-hub/iot-hub-devguide-security.md#security-tokens 
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md
[lnk-iothub-devicetwins]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-c2d]: ../iot-hub/iot-hub-devguide-messages-c2d.md
[lnk-ca-scripts]: https://github.com/Azure/azure-iot-sdk-c/blob/modules-preview/tools/CACertificates/CACertificateOverview.md
[lnk-modbus-module]: https://github.com/Azure/iot-edge-modbus