---
title: Azure Data Factory değişken etkinliğini ayarla
description: Bir Data Factory işlem hattında tanımlanmış var olan bir değişkenin değerini ayarlamak için değişken ayarla etkinliğini nasıl kullanacağınızı öğrenin
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/07/2020
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.openlocfilehash: 113829dd35c14b5efae39c55a8085dcd2c1ecef4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786167"
---
# <a name="set-variable-activity-in-azure-data-factory"></a>Azure Data Factory değişken etkinliğini ayarla
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bir Data Factory işlem hattında tanımlanmış dize, bool veya dizi türünde bir değişkenin değerini ayarlamak için değişken ayarla etkinliğini kullanın.

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | Gerekli
-------- | ----------- | --------
name | İşlem hattının etkinlik adı | evet
açıklama | Etkinliğin ne yaptığını açıklayan metin | hayır
tür | **SetVariable** olarak ayarlanmalıdır | evet
değer | Değişkenin atandığı dize sabiti veya ifade nesnesi değeri | evet
variableName | Bu etkinlik tarafından ayarlanan değişkenin adı | evet

## <a name="incrementing-a-variable"></a>Bir değişkeni artırma

Azure Data Factory değişkenleri içeren yaygın bir senaryo, bir Until veya ForEach etkinliği içinde bir yineleyici olarak bir değişken kullanmaktır. Bir değişken kümesi etkinliğinde, alanında ayarlanan değişkene başvurulamıyor `value` . Bu sınırlamaya geçici çözüm olarak, geçici bir değişken ayarlayın ve ardından ikinci bir değişken kümesi etkinliği oluşturun. İkinci değişken kümesi etkinliği yineleyicinin değerini geçici değişken olarak ayarlar. 

Aşağıda bu düzenin bir örneği verilmiştir:

![Artış değişkeni](media/control-flow-set-variable-activity/increment-variable.png "Artış değişkeni")

``` json
{
    "name": "pipeline3",
    "properties": {
        "activities": [
            {
                "name": "Set I",
                "type": "SetVariable",
                "dependsOn": [
                    {
                        "activity": "Increment J",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "i",
                    "value": {
                        "value": "@variables('j')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "Increment J",
                "type": "SetVariable",
                "dependsOn": [],
                "userProperties": [],
                "typeProperties": {
                    "variableName": "j",
                    "value": {
                        "value": "@string(add(int(variables('i')), 1))",
                        "type": "Expression"
                    }
                }
            }
        ],
        "variables": {
            "i": {
                "type": "String",
                "defaultValue": "0"
            },
            "j": {
                "type": "String",
                "defaultValue": "0"
            }
        },
        "annotations": []
    }
}
```


## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen ilgili bir denetim akışı etkinliği hakkında bilgi edinin: 

- [Değişken Etkinlik Ekleme](control-flow-append-variable-activity.md)
