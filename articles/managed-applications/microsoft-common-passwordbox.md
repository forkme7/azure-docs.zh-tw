---
title: Azure PasswordBox UI 元素 | Microsoft Docs
description: 描述 Azure 入口網站的 Microsoft.Common.PasswordBox UI 元素。
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
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 19c027819b83f10a7a3de714d690964507311da0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Microsoft.Common.PasswordBox UI 元素
可使用控制項來提供及確認密碼。

## <a name="ui-sample"></a>UI 範例
![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>結構描述
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

## <a name="remarks"></a>備註
- 此元素不支援 `defaultValue` 屬性。
- 如需 `constraints` 的實作詳細資料，請參閱 [Microsoft.Common.TextBox](microsoft-common-textbox.md)。
- 如果將 `options.hideConfirmation` 設為 **true**，就會將確認使用者密碼的第二個文字方塊加以隱藏。 預設值為 **false**。

## <a name="sample-output"></a>範例輸出
```json
"p4ssw0rd"
```

## <a name="next-steps"></a>後續步驟
* 如需建立 UI 定義的簡介，請參閱[開始使用 CreateUiDefinition](create-uidefinition-overview.md)。
* 如需 UI 元素中通用屬性的說明，請參閱 [CreateUiDefinition 元素](create-uidefinition-elements.md)。
