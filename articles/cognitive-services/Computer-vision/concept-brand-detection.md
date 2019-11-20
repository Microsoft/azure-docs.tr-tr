---
title: Marka Algılama-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Bu makalede, nesne algılamada özelleştirilmiş bir mod ele alınmaktadır; Görüntü İşleme API'si kullanarak marka ve/veya amblem algılama.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 117beca1284f28c75c1ac772425423f732b8a236
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718624"
---
# <a name="detect-popular-brands-in-images"></a>Görüntülerde popüler markaların algılanması

Marka algılama, resimlerde veya videoda ticari markaların tanımlanması için binlerce küresel logoın bir veritabanını kullanan [nesne algılamasında](concept-object-detection.md) özelleştirilmiş bir moddur. Bu özelliği örneğin, sosyal medya veya en popüler medya ürün yerleştirmesi üzerinde en popüler olan markalar öğrenmek için kullanabilirsiniz.

Görüntü İşleme Hizmeti belirli bir görüntüde marka logoları olup olmadığını algılar; Bu durumda, bir marka adı, Güvenirlik puanı ve logo etrafındaki bir sınırlayıcı kutunun koordinatlarını döndürür.

Yerleşik logo veritabanı, tüketici elektroniği, giyme ve daha birçok konuda popüler markaların yer aldığı bir şeydir. Aradığınız marka Görüntü İşleme Hizmeti tarafından algılanmadığını fark ederseniz, [özel görüntü işleme](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) hizmetini kullanarak kendi amblem algıanızı oluşturma ve eğitim konusunda daha iyi bir işlem yapabilirsiniz.

## <a name="brand-detection-example"></a>Marka algılama örneği

Aşağıdaki JSON yanıtları, örnek görüntülerde markalar algılayarak ne Görüntü İşleme döndüğünü gösterir.

![Microsoft etiketi ve logosu içeren kırmızı bir gömlek](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

Bazı durumlarda, marka algılayıcısı hem logo görüntüsünü hem de stilize marka adını iki ayrı logo olarak seçer.

![Üzerinde bir Microsoft etiketi ve logosu olan gri bir elter](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>API’yi kullanma

Marka algılama özelliği, [görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API 'sinin bir parçasıdır. Bu API 'YI yerel bir SDK aracılığıyla veya REST çağrıları aracılığıyla çağırabilirsiniz. **Visualfeatures** sorgu parametresine `Brands` ekleyin. Ardından, tam JSON yanıtını aldığınızda `"brands"` bölümünün içeriği için dizeyi ayrıştırın.

* [Hızlı başlangıç: Görüntü İşleme .NET SDK](./quickstarts-sdk/csharp-sdk.md)
* [Hızlı başlangıç: bir görüntüyü çözümleme (REST API)](./quickstarts/csharp-analyze.md)
