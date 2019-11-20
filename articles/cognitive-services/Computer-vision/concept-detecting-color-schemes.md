---
title: Renk düzeni Algılama-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si kullanarak görüntülerde renk düzenini algılamayla ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: e0fa85b8a90ea57d9b81bd2eeaa6d080b7582acd
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945284"
---
# <a name="detect-color-schemes-in-images"></a>Görüntülerde renk düzenlerini Algıla

Görüntü İşleme, görüntüdeki renkleri çözümleyerek üç farklı öznitelik sağlar: baskın ön plan rengi, baskın arka plan rengi ve resim için bir bütün olarak görüntü için baskın renkler kümesi. Döndürülen renkler kümesine aittir: siyah, mavi, kahverengi, gri, yeşil, turuncu, pembe, mor, kırmızı, deniz mavisi, beyaz ve sarı. 

Görüntü İşleme, baskın renklerin ve doygunluk birleşimine göre görüntüdeki en canlı rengi temsil eden bir vurgu rengi de ayıklar. Vurgu rengi onaltılık HTML renk kodu olarak döndürülür. 

Görüntü İşleme Ayrıca bir görüntünün siyah ve beyaz olduğunu gösteren bir Boole değeri döndürür.

## <a name="color-scheme-detection-examples"></a>Renk düzeni algılama örnekleri

Aşağıdaki örnek, örnek görüntünün renk düzenini algılayarak Görüntü İşleme tarafından döndürülen JSON yanıtını gösterir. Bu durumda, örnek resim siyah ve beyaz bir görüntü değildir, ancak baskın ön plan ve arka plan renkleri siyahtır ve görüntü için baskın renkler siyah ve beyazdır.

![Bir kişinin silueti ile gün içinde Sıradağlar](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Baskın renk örnekleri

Aşağıdaki tabloda, her bir örnek görüntü için döndürülen ön plan, arka plan ve görüntü renkleri gösterilmektedir.

| Image | Baskın renkler |
|-------|-----------------|
|![Yeşil arka plana sahip beyaz çiçek](./Images/flower.png)| Ön plan Siyah<br/>Arka plan Beyaz<br/>Lerde Siyah, beyaz, yeşil|
![İstasyon aracılığıyla çalıştırılan eğitme](./Images/train_station.png) | Ön plan Siyah<br/>Arka plan Siyah<br/>Lerde Siyah |

### <a name="accent-color-examples"></a>Vurgu rengi örnekleri

 Aşağıdaki tabloda, her örnek resim için döndürülen vurgu rengi onaltılık HTML renk değeri olarak gösterilmektedir.

| Image | Vurgu rengi |
|-------|--------------|
|![Bir dağ rock on gün sonra duran bir kişi](./Images/mountain_vista.png) | #BB6D10 |
|![Yeşil arka plana sahip beyaz çiçek](./Images/flower.png) | #C6A205 |
|![İstasyon aracılığıyla çalıştırılan eğitme](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Siyah & beyaz algılama örnekleri

Aşağıdaki tabloda, örnek görüntülerde Görüntü İşleme siyah ve beyaz değerlendirme gösterilmektedir.

| Image | Siyah & beyaz? |
|-------|----------------|
|![Manhattan içindeki binalara ilişkin siyah ve beyaz bir resim](./Images/bw_buildings.png) | true |
|![Mavi Ev ve ön bahçe](./Images/house_yard.png) | false |

## <a name="next-steps"></a>Sonraki adımlar

[Görüntü türlerini algılama](concept-detecting-image-types.md)kavramlarını öğrenin.
