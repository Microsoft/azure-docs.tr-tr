---
title: Slider Kullanıcı arabirimi öğesi
description: Azure portal için Microsoft. Common. Slider Kullanıcı arabirimi öğesini açıklar. Kullanıcıların bir seçenek aralığından bir değer ayarlamanızı sağlar.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/10/2020
ms.author: tomfitz
ms.openlocfilehash: bb168a4bff6d2570f539307530232b5063bb535c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87100046"
---
# <a name="microsoftcommonslider-ui-element"></a>Microsoft. Common. Slider Kullanıcı arabirimi öğesi

Kaydırıcı denetimi, kullanıcıların izin verilen bir dizi değerden seçim yapmasına imkan tanır.

## <a name="ui-sample"></a>UI örneği

:::image type="content" source="./media/managed-application-elements/microsoft-common-slider.png" alt-text="Microsoft. Common. Slider":::

## <a name="schema"></a>Şema

```json
{
    "name": "memorySize",
    "type": "Microsoft.Common.Slider",
    "min": 1,
    "max": 64,
    "label": "Memory",
    "subLabel": "MB",
    "defaultValue": 24,
    "showStepMarkers": false,
    "toolTip": "Pick the size in MB",
    "constraints": {
        "required": false
    },
    "visible": true
}
```

## <a name="sample-output"></a>Örnek çıktı

```json
26
```

## <a name="remarks"></a>Açıklamalar

- `min`Ve `max` değerleri gereklidir. Kaydırıcı için başlangıç ve bitiş noktalarını ayarlar.
- `showStepMarkers`Özellik varsayılan olarak true değerini alır. Adım işaretleyicileri yalnızca Min-Max aralığının 100 veya daha az olduğu durumlarda gösterilir.


## <a name="next-steps"></a>Sonraki adımlar

* UI tanımları oluşturmaya giriş için bkz. [Createuıdefinition ile çalışmaya başlama](create-uidefinition-overview.md).
* UI öğelerindeki ortak özelliklerin açıklaması için bkz. [Createuıdefinition Elements](create-uidefinition-elements.md).
