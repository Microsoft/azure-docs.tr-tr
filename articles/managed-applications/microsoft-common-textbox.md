---
title: Azure TextBox Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Common. TextBox Kullanıcı arabirimi öğesini açıklar. Biçimlendirilmemiş metin eklemek için kullanın.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a5f28b763023bb60bca8f79bcaa9f2169281e466
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151103"
---
# <a name="microsoftcommontextbox-ui-element"></a>Microsoft. Common. TextBox Kullanıcı arabirimi öğesi

Biçimlendirilmemiş metni düzenlemek için kullanılabilen bir denetim.

## <a name="ui-sample"></a>UI örneği

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
"my text value"
```

## <a name="remarks"></a>Açıklamalar

- `constraints.required` **true**olarak ayarlanırsa, metin kutusunun başarıyla doğrulanacak bir değeri olması gerekir. Varsayılan değer **false**.
- `constraints.regex`, JavaScript normal ifade deseninin. Belirtilmişse, metin kutusunun değeri, başarıyla doğrulanacak şekilde Düzenle eşleşmelidir. Varsayılan değer **null**.
- `constraints.validationMessage`, metin kutusunun değeri doğrulanamazsa görüntülenecek bir dizedir. Belirtilmezse, metin kutusunun yerleşik doğrulama iletileri kullanılır. Varsayılan değer **null**.
- `constraints.required` **false**olarak ayarlandığında `constraints.regex` için bir değer belirtmek mümkündür. Bu senaryoda, metin kutusunun başarıyla doğrulanması için bir değer gerekli değildir. Belirtilmişse, normal ifade düzeniyle eşleşmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
