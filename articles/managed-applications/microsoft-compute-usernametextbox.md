---
title: Azure UserNameTextBox Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. COMPUTE. UserNameTextBox Kullanıcı arabirimi öğesini açıklar. Kullanıcıların Windows veya Linux kullanıcı adlarını sağlamasına olanak sağlar.
services: managed-applications
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 47dff5fb23da7bf816e46cf8e3e5696faadc7409
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151516"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft. COMPUTE. UserNameTextBox UI öğesi

Windows ve Linux Kullanıcı adları için yerleşik doğrulamaya sahip bir metin kutusu denetimi.

## <a name="ui-sample"></a>UI örneği

![Microsoft. COMPUTE. UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
"Example name"
```

## <a name="remarks"></a>Açıklamalar

- `constraints.required` **true**olarak ayarlanırsa, metin kutusunun başarıyla doğrulanacak bir değeri olması gerekir. Varsayılan değer **true**'dur.
- `osPlatform` belirtilmelidir ve **Windows** ya da **Linux**olabilir.
- `constraints.regex`, JavaScript normal ifade deseninin. Belirtilmişse, metin kutusunun değeri, başarıyla doğrulanacak şekilde Düzenle eşleşmelidir. Varsayılan değer **null**.
- `constraints.validationMessage`, metin kutusunun değeri `constraints.regex`tarafından belirtilen doğrulamada başarısız olduğunda görüntülenecek bir dizedir. Belirtilmezse, metin kutusunun yerleşik doğrulama iletileri kullanılır. Varsayılan değer **null**.
- Bu öğe, `osPlatform`için belirtilen değeri temel alan yerleşik doğrulamaya sahiptir. Yerleşik doğrulama, özel bir normal ifadeyle birlikte kullanılabilir. `constraints.regex` için bir değer belirtilmişse, hem yerleşik hem de özel doğrulamalar tetiklenir.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
