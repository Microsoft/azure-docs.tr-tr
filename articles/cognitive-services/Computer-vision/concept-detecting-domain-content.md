---
title: Etki alanına özgü içerik-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü hakkında daha ayrıntılı bilgi döndürmek için bir görüntü kategorisi etki alanı belirtmeyi öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946333"
---
# <a name="detect-domain-specific-content"></a>Etki alanına özgü içeriği algılama

Etiketleme ve üst düzey kategoriye ek olarak, Görüntü İşleme özelleştirilmiş veriler üzerinde eğitilen modelleri kullanarak etki alanına özgü daha fazla analiz da destekler.

Etki alanına özgü modelleri kullanmanın iki yolu vardır: kendilerine (kapsamlı analiz) veya kategori özelliği için bir geliştirme olarak.

### <a name="scoped-analysis"></a>Kapsamlı analiz

[Modeller/\<model\>/Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API 'sini çağırarak bir görüntüyü yalnızca seçilen alana özgü modeli kullanarak çözümleyebilirsiniz.

Aşağıda, belirtilen görüntü için **Modeller/Ünlüler/çözümle** API 'si tarafından döndürülen örnek bir JSON yanıtı verilmiştir:

![Satya Nadella duran, gülümseyen](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Gelişmiş kategori Analizi

Genel görüntü analizine ek olarak, etki alanına özgü modeller de kullanabilirsiniz. Bunu, [Çözümle](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API çağrısının *Ayrıntılar* parametresinde alana özgü modeller belirterek, [üst düzey kategori](concept-categorizing-images.md) parçası olarak yapabilirsiniz.

Bu durumda, ilk olarak 86 kategori taksonomi Sınıflandırıcısı çağırılır. Algılanan kategorilerden herhangi birinde eşleşen bir etki alanına özgü model varsa, görüntü bu modelden de geçirilir ve sonuçlar eklenir.

Aşağıdaki JSON yanıtı, etki alanına özgü çözümlemenin daha geniş bir kategori analizinde `detail` düğüm olarak nasıl dahil edileceğini gösterir.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>Etki alanına özgü modelleri listeleme

Şu anda Görüntü İşleme, etki alanına özgü aşağıdaki modelleri destekler:

| Ad | Açıklama |
|------|-------------|
| ünlüleri | Bu şekilde `people_` , kategoride sınıflandırılan görüntüler için desteklenen ünlülik tanıma |
| sakal | Yer işareti tanıma, `outdoor_` veya `building_` kategorilerinde sınıflandırılan görüntüler için desteklenir |

[Modeller](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API 'sini çağırmak, bu bilgileri her modelin uygulayabileceği kategorilerle birlikte döndürür:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Görüntüleri kategorilere ayırma](concept-categorizing-images.md)hakkında kavramlar öğrenin.
