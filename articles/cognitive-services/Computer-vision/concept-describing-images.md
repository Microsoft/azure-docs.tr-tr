---
title: Görüntü açıklamaları-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si resim açıklaması özelliğiyle ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945259"
---
# <a name="describe-images-with-human-readable-language"></a>İnsan tarafından okunabilen dilin görüntülerini açıklama

Görüntü İşleme bir görüntüyü çözümleyebilir ve içeriğini açıklayan bir insan tarafından okunabilen tümce oluşturabilir. Algoritma aslında farklı görsel özelliklere göre birkaç açıklama döndürür ve her açıklamaya Güvenirlik puanı verilir. Nihai çıktı, en yüksek ve en düşük güvenle sıralanan açıklamaların bir listesidir.

## <a name="image-description-example"></a>Görüntü açıklaması örneği

Aşağıdaki JSON yanıtında, görsel özelliklerine göre örnek resim açıklanırken ne Görüntü İşleme döndürdüğü gösterilmektedir.

![Manhattan içindeki binalara ilişkin siyah ve beyaz bir resim](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Görüntüleri etiketleme](concept-tagging-images.md) ve [görüntüleri kategorilere ayırma](concept-categorizing-images.md)hakkında kavramlar öğrenin.
