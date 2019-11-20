---
title: Görüntü türü algılama-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si görüntü türü algılama özelliği ile ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e89fd977129113fa88af1acccd6b05f0bbe90243
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945204"
---
# <a name="detecting-image-types-with-computer-vision"></a>Görüntü İşleme ile görüntü türlerini algılama

[Görüntüyü çözümle](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 'si ile görüntü işleme görüntünün içerik türünü analiz edebilir. Bu, bir resmin küçük resim veya çizgi çizimi olduğunu gösterir.

## <a name="detecting-clip-art"></a>Küçük resim algılanıyor

Görüntü İşleme, bir görüntüyü analiz eder ve aşağıdaki tabloda açıklandığı gibi görüntünün bir ölçeğinde 0 ' dan 3 ' e kadar küçük resim olma olasılığını göstermektedir.

| Value | Anlamı |
|-------|---------|
| 0 | Küçük resim değil |
| 1\. | Belirsiz |
| 2 | Normal-küçük resim |
| 3 | İyi klip-resim |

### <a name="clip-art-detection-examples"></a>Küçük resim algılama örnekleri

Aşağıdaki JSON yanıtları, örnek görüntülerin küçük resim olma olasılığını derecelendirerek ne Görüntü İşleme döndüğünü gösterir.

![Cheese diliminin küçük resim görüntüsü](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Mavi Ev ve ön bahçe](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Çizgi çizimleri algılanıyor

Görüntü İşleme bir görüntüyü analiz eder ve görüntünün çizgi çizimi olup olmadığını gösteren bir Boole değeri döndürür.

### <a name="line-drawing-detection-examples"></a>Çizgi çizim algılama örnekleri

Aşağıdaki JSON yanıtları, örnek görüntülerin çizgi çizimlerinin olup olmadığını belirten Görüntü İşleme ne getirdiğini gösterir.

![Lion 'ın çizgi çizme resmi](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Yeşil arka plana sahip beyaz çiçek](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Görüntü türlerini algılamayı öğrenmek için [görüntü başvurusunu çözümle](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) belgelerine bakın.
