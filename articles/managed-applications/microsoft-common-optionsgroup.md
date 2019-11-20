---
title: Azure Options grubu Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Common. Options grubu Kullanıcı arabirimi öğesini açıklar. Kullanıcıların yönetilen bir uygulama dağıtımında kullanılabilir seçeneklerden seçim yapmasına olanak sağlar.
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
ms.openlocfilehash: 1210c24687c0cd1f38e33674d297dd37fe4d2995
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151890"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Microsoft. Common. Options grubu UI öğesi

Kullanılabilir seçeneklerin bulunduğu bir seçim denetimi.

## <a name="ui-sample"></a>UI örneği

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
"two"
```

## <a name="remarks"></a>Açıklamalar

- `constraints.allowedValues` etiketi bir öğe için görüntüleme metintir ve değeri seçildiğinde öğenin çıkış değeridir.
- Belirtilmişse, varsayılan değer `constraints.allowedValues`mevcut bir etiket olmalıdır. Belirtilmemişse, `constraints.allowedValues` ilk öğe varsayılan olarak seçilidir. Varsayılan değer **null**.
- `constraints.allowedValues` en az bir öğe içermelidir.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
