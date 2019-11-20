---
title: Azure açılır Kullanıcı arabirimi öğesi | Microsoft Docs
description: Azure portal için Microsoft. Common. DropDown Kullanıcı arabirimi öğesini açıklar. Yönetilen bir uygulamayı dağıttığınızda kullanılabilir seçenekler arasından seçim yapmak için kullanın.
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
ms.openlocfilehash: e1db91362d17d9100199f0b30119f8024f00a903
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151348"
---
# <a name="microsoftcommondropdown-ui-element"></a>Microsoft. Common. DropDown UI öğesi

Açılan liste içeren seçim denetimi.

## <a name="ui-sample"></a>UI örneği

![Microsoft. Common. DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Şema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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
- Belirtilmişse, varsayılan değer `constraints.allowedValues`mevcut bir etiket olmalıdır. Belirtilmemişse, `constraints.allowedValues` ilk öğe seçilir. Varsayılan değer **null**.
- `constraints.allowedValues` en az bir öğe içermelidir.
- Zorunlu olmayan bir değere öykünmek için, `constraints.allowedValues`bir etiketi ve değeri `""` (boş dize) olan bir öğe ekleyin.

## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
