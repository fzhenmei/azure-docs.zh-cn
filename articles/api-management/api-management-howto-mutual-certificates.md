---
title: "使用客户端证书身份验证保护后端服务 - Azure API 管理 | Microsoft 文档"
description: "了解如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 885315b9f610d5f1703acd0f292f7b3347462b34
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2017
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>如何使用 Azure API 管理中的客户端证书身份验证确保后端服务安全
API 管理提供的功能可确保使用客户端证书安全地访问 API 的后端服务。 本指南介绍如何在 API 发布者门户中管理证书，以及如何将 API 配置为使用证书访问其后端服务。

有关如何使用 API 管理 REST API 来管理证书的信息，请参阅 [Azure API 管理 REST API 证书实体][Azure API Management REST API Certificate entity]。

## <a name="prerequisites"> </a>先决条件
本指南介绍如何将 API 管理服务实例配置为使用客户端证书身份验证访问 API 的后端服务。 执行本主题中的步骤之前，用户应将后端服务配置为进行客户端证书身份验证（[要在 Azure 网站中配置证书身份验证，请参阅此文][to configure certificate authentication in Azure WebSites refer to this article]），并能够访问证书及证书的密码，以便在 API 管理发布者门户中执行上传操作。

## <a name="step1"> </a>上传客户端证书
若要开始，请单击 API 管理服务的 Azure 门户中的“发布者门户”。 这会转到 API 管理发布者门户。

![API 发布者门户][api-management-management-console]

> 如果尚未创建 API 管理服务实例，请参阅[创建 API 管理服务实例][Create an API Management service instance]。
> 
> 

单击左侧“API 管理”菜单中的“安全”，并单击“客户端证书”。

![客户端证书][api-management-security-client-certificates]

若要上传新证书，请单击“上传证书”。

![上传证书][api-management-upload-certificate]

浏览到证书，并输入证书的密码。

> 证书必须采用 **.pfx** 格式。 允许使用自签名证书。
> 
> 

![上传证书][api-management-upload-certificate-form]

单击“上传”上传证书。

> 此时会验证证书密码。 如果不正确，则会显示错误消息。
> 
> 

![上传的证书][api-management-certificate-uploaded]

证书在上传后显示在“客户端证书”选项卡中。如果有多个证书，请记下其使用者或指纹的最后四个字符。在将 API 配置为使用证书时，这些信息用于选择证书，详见下面的[将 API 配置为使用客户端证书进行网关身份验证][Configure an API to use a client certificate for gateway authentication]部分。

> 若要在使用某个证书（例如自签名证书）时关闭证书链验证，请执行此常见问题[项](api-management-faq.md#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)中描述的步骤。
> 
> 

## <a name="step1a"> </a>删除客户端证书
若要删除证书，请单击需删除的证书旁边的“删除”。

![删除证书][api-management-certificate-delete]

单击“是，将其删除”进行确认。

![确认删除][api-management-confirm-delete]

如果证书被某个 API 使用，则会显示警告屏幕。 删除证书之前，必须先将其从配置为使用该证书的 API 中移除。

![确认删除][api-management-confirm-delete-policy]

## <a name="step2"> </a>将 API 配置为使用客户端证书进行网关身份验证
在左侧的“API 管理”菜单中单击“API”，接着单击所需 API 的名称，并单击“安全”选项卡。

![API 安全][api-management-api-security]

从“使用凭据”下拉列表中选择“客户端证书”。

![客户端证书][api-management-mutual-certificates]

从“客户端证书”下拉列表中选择所需证书。 如果有多个证书，则可查看其使用者或指纹的最后四个字符（如上一部分所述），以便确定正确的证书。

![选择证书][api-management-select-certificate]

单击“保存”保存对 API 所做的配置更改。

> 此更改立即生效，调用对该 API 的操作时，会使用证书在后端服务器上进行身份验证。
> 
> 

![保存 API 更改][api-management-save-api]

> 为 API 的后端服务指定网关身份验证的证书时，此证书会成为该 API 的策略的一部分，可以在策略编辑器中查看。
> 
> 

![证书策略][api-management-certificate-policy]

## <a name="self-signed-certificates"></a>自签名证书

如果使用自签名证书，将需要禁用证书链验证使 API 管理能够与后端系统进行通信，否则，它将返回 500 错误代码。 若要配置此项，可以使用 [`New-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend)（适用于新后端）或 [`Set-AzureRmApiManagementBackend`](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend)（适用于现有后端）PowerShell cmdlet 并将 `-SkipCertificateChainValidation` 参数设置为 `True`。

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="next-steps"></a>后续步骤
如需详细了解如何通过其他方式（例如 HTTP 基本密钥或共享密钥身份验证）确保后端服务的安全，请观看以下视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Last-mile-Security/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-mutual-certificates/api-management-management-console.png
[api-management-security-client-certificates]: ./media/api-management-howto-mutual-certificates/api-management-security-client-certificates.png
[api-management-upload-certificate]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate.png
[api-management-upload-certificate-form]: ./media/api-management-howto-mutual-certificates/api-management-upload-certificate-form.png
[api-management-certificate-uploaded]: ./media/api-management-howto-mutual-certificates/api-management-certificate-uploaded.png
[api-management-api-security]: ./media/api-management-howto-mutual-certificates/api-management-api-security.png
[api-management-mutual-certificates]: ./media/api-management-howto-mutual-certificates/api-management-mutual-certificates.png
[api-management-select-certificate]: ./media/api-management-howto-mutual-certificates/api-management-select-certificate.png
[api-management-save-api]: ./media/api-management-howto-mutual-certificates/api-management-save-api.png
[api-management-certificate-policy]: ./media/api-management-howto-mutual-certificates/api-management-certificate-policy.png
[api-management-certificate-delete]: ./media/api-management-howto-mutual-certificates/api-management-certificate-delete.png
[api-management-confirm-delete]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete.png
[api-management-confirm-delete-policy]: ./media/api-management-howto-mutual-certificates/api-management-confirm-delete-policy.png



[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: http://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps



