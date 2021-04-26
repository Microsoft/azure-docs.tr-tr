---
title: Resim kategorisi-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si resim kategorisi özelliği ile ilgili kavramları öğrenin.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 97a6a08730ae0c87df3b65185d48297e2338e69b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768116"
---
# <a name="categorize-images-by-subject-matter"></a>Görüntüleri konuya göre kategorilere ayırın

Etiketlere ve açıklamaya ek olarak, Görüntü İşleme görüntüde algılanan taksonomi tabanlı kategorileri döndürür. Etiketlerin aksine, Kategoriler bir üst/alt öğe hiyerarşisinde düzenlenir ve bunlardan daha az (86, binlerce etiketin aksine) bulunur. Tüm kategori adları Ingilizce 'Dir. Kategori oluşturma işlemi, veya daha yeni Etiketler modeli ile yapılabilir.

## <a name="the-86-category-concept"></a>86 kategori kavramı

Bilgisayar görme, aşağıdaki diyagramda 86 kategorilerinin listesini kullanarak bir görüntüyü büyük ölçüde veya özel olarak kategorilere ayırmaz. Metin biçiminde tam taksonomi için bkz. [Kategori Taksonomisi](category-taxonomy.md).

![Kategori sınıflandırmasında tüm kategorilerin gruplanmış listeleri](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Resim kategorisi örnekleri

Aşağıdaki JSON yanıtında, örnek görüntüyü görsel özelliklerine göre kategorilere ayırırken ne Görüntü İşleme döndürdüğü gösterilmektedir.

![Apartman binasının çatı üzerindeki kadın](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

Aşağıdaki tabloda tipik bir görüntü kümesi ve her görüntü için Görüntü İşleme tarafından döndürülen kategori gösterilmektedir.

| Görüntü | Kategori |
|-------|----------|
| ![Aile olarak bir arada bulunan dört kişi](./Images/family_photo.png) | people_group |
| ![Bir Grassy alanında oturan bir pupa](./Images/cute_dog.png) | animal_dog |
| ![Bir dağ rock on gün sonra duran bir kişi](./Images/mountain_vista.png) | outdoor_mountain |
| ![Bir tablodaki içerik içeriğini bir satır içinde oluşturma](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>API’yi kullanma

Kategori özelliği, [görüntüyü çözümle](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-ga/operations/56f91f2e778daf14a499f21b) API 'sinin bir parçasıdır. Bu API 'YI yerel bir SDK aracılığıyla veya REST çağrıları aracılığıyla çağırabilirsiniz. `Categories` **Visualfeatures** sorgu parametresine dahil edin. Ardından, tam JSON yanıtını aldığınızda, bu dizeyi yalnızca bölüm içeriği için ayrıştırın `"categories"` .

* [Hızlı başlangıç: Görüntü İşleme REST API veya istemci kitaplıkları](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)

## <a name="next-steps"></a>Sonraki adımlar

[Görüntülerin etiketlenmesi](concept-tagging-images.md) ve [görüntülerin açıklanmasıyla](concept-describing-images.md)ilgili kavramları öğrenin.
