---
title: Yüz Algılama-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si yüz algılama özelliği ile ilgili kavramları öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: bfb352c68b910a114e13041da4e8e86529e52040
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778850"
---
# <a name="face-detection-with-computer-vision"></a>Görüntü İşleme ile yüz algılama

Görüntü İşleme, görüntü içindeki insan yüzlerini algılayabilir ve algılanan her yüz için yaş, cinsiyet ve dikdörtgen oluşturabilir. 

> [!NOTE]
> Bu özellik ayrıca Azure [yüz](../face/index.yml) hizmeti tarafından sunulur. Yüz tanıma ve poz algılama gibi daha ayrıntılı yüz analizi için bu alternatif bölümüne bakın. 

## <a name="face-detection-examples"></a>Yüz algılama örnekleri

Aşağıdaki örnek, tek bir insan yüzü içeren bir görüntü için Görüntü İşleme tarafından döndürülen JSON yanıtını gösterir.

![Görüntü Analizi Damdaki Kadının Yüzü](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

Sonraki örnekte, birden çok insan yüzü içeren bir görüntü için döndürülen JSON yanıtı gösterilmektedir.

![Vizyon aile fotoğraf yüzünüzü analiz etme](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>API’yi kullanma

Yüz algılama özelliği, [görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API 'sinin bir parçasıdır. Bu API 'YI yerel bir SDK aracılığıyla veya REST çağrıları aracılığıyla çağırabilirsiniz. `Faces` **Visualfeatures** sorgu parametresine dahil edin. Ardından, tam JSON yanıtını aldığınızda, bu dizeyi yalnızca bölüm içeriği için ayrıştırın `"faces"` .

* [Hızlı başlangıç: Görüntü İşleme REST API veya istemci kitaplıkları](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
