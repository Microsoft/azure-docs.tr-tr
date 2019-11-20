---
title: İçerik etiketleri-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si resimler etiketleme özelliğiyle ilgili kavramları öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ad01751f7f7c573352c8fd15cc0f8cc2ebbbe700
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945117"
---
# <a name="applying-content-tags-to-images"></a>Resimlere içerik etiketleri uygulama

Görüntü İşleme, nesnelerin binlerce tanınabilir nesne, canlı, manzara ve eylemlere göre, etiketleri döndürür. Belirsiz veya herkesçe bilinmeyen etiketler söz konusu olduğunda, API yanıtı, etiketin anlamının bilinen bir ortama ilişkin bağlamda açıklığa kavuşturulması için "ipuçları" sağlar. Etiketler taksonomi olarak tanınmaz ve hiçbir devralma hiyerarşisi yoktur. Bir içerik etiketi koleksiyonu, tam tümceler halinde biçimlendirilmiş insan tarafından okunabilir dilde görüntülenen bir görüntü 'açıklamasının' temelini oluşturur. Şu noktada görüntü açıklaması için desteklenen tek dilin İngilizce olduğunu unutmayın.

Bir görüntüyü karşıya yükledikten veya bir görüntü URL 'SI belirttikten sonra, görüntüde tanımlanan nesneleri, canlı kullanımları ve eylemleri temel alan algoritma çıkış etiketleri Görüntü İşleme. Etiketleme yalnızca temel konu ile sınırlı kalmayıp ortam (iç mekân veya dış mekân), mobilyalar, aletler, bitkiler, hayvanlar, aksesuarlar, araçlar ve benzeri öğeleri de kapsar.

## <a name="image-tagging-example"></a>Resim etiketleme örneği

Aşağıdaki JSON yanıtında, örnek görüntüde algılanan görsel özellikleri etiketlerken ne Görüntü İşleme döndürdüğü gösterilmektedir.

![Mavi Ev ve ön bahçe](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Görüntüleri kategorilere ayırma](concept-categorizing-images.md) ve [görüntüleri açıklama](concept-describing-images.md)hakkında kavramlar öğrenin.
