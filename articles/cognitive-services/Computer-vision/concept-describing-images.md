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
ms.openlocfilehash: c517fa98bfc17d4702a51d4990e860b2ed7aaefd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778940"
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

## <a name="use-the-api"></a>API’yi kullanma

Görüntü açıklaması özelliği, [görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API 'sinin bir parçasıdır. Bu API 'YI yerel bir SDK aracılığıyla veya REST çağrıları aracılığıyla çağırabilirsiniz. `Description` **Visualfeatures** sorgu parametresine dahil edin. Ardından, tam JSON yanıtını aldığınızda, bu dizeyi yalnızca bölüm içeriği için ayrıştırın `"description"` .

* [Hızlı başlangıç: Görüntü İşleme REST API veya istemci kitaplıkları](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Sonraki adımlar

[Görüntülerin etiketlenmesi](concept-tagging-images.md) ve [görüntülerin kategorilere ayrılması](concept-categorizing-images.md)ile ilgili kavramları öğrenin.
