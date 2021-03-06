---
title: "Azure 托管应用程序 PasswordBox UI 元素 | Microsoft Docs"
description: "介绍了 Azure 托管应用程序的 Microsoft.Common.PasswordBox UI 元素"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6d9f2b7cf56375d3a609cff20e928307c13bf2b8
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI 元素
一个可以用来提供和确认密码的控件。 [创建 Azure 托管应用程序](publish-service-catalog-app.md)时使用此元素。

## <a name="ui-sample"></a>UI 示例
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>架构
```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "",
    "validationMessage": ""
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="remarks"></a>备注
- 此元素不支持 `defaultValue` 属性。
- 有关 `constraints` 的实现详细信息，请参阅 [Microsoft.Common.TextBox](microsoft-common-textbox.md)。
- 如果 `options.hideConfirmation` 设置为 **true**，则用于确认用户密码的第二个文本框会被隐藏。 默认值为 **false**。

## <a name="sample-output"></a>示例输出
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>后续步骤
* 有关托管应用程序的简介，请参阅 [Azure 托管应用程序概述](overview.md)。
* 有关创建 UI 定义的简介，请参阅 [CreateUiDefinition 入门](create-uidefinition-overview.md)。
* 有关 UI 元素中的公用属性的说明，请参阅 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
