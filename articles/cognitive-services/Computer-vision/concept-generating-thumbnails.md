---
title: Akıllı kırpılan küçük resimler-Görüntü İşleme
titleSuffix: Azure Cognitive Services
description: Görüntü İşleme API'si kullanarak görüntülerin küçük resimlerini oluşturma ile ilgili kavramlar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4874910f37b49990a659b48af0cf27921c3fcd5e
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945227"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>Görüntü İşleme ile akıllı kırpılmış küçük resimler oluşturma

Küçük resim, bir görüntünün azaltılmış boyutlu bir gösterimidir. Küçük resimler, daha ekonomik, düzen kullanımı kolay bir şekilde görüntüleri ve diğer verileri temsil etmek için kullanılır. Görüntü İşleme API'si, belirli bir görüntü için sezgisel küçük resimler oluşturmak üzere görüntüyü yeniden boyutlandırmayla birlikte akıllı kırpma kullanır.

Görüntü İşleme küçük resim oluşturma algoritması aşağıdaki gibi çalışmaktadır:

1. Görüntüdeki dikkat _çekici_ &mdash;öğeleri kaldırın ve görüntü alanının ana nesne (ler) göründüğü alanını tanımlayan alanı belirler.
1. Görüntüyü, _ilgilendiğiniz tanımlı alana_ göre kırpın.
1. En boy oranını hedef küçük resim boyutlarına uyacak şekilde değiştirin.

## <a name="area-of-interest"></a>İlgilendiğiniz alan

Bir görüntüyü karşıya yüklediğinizde, Görüntü İşleme API'si *ilgilendiğiniz alanı*belirleyecek şekilde analiz eder. Daha sonra bu bölgeyi, görüntünün nasıl kırpılacağını anlamak için kullanabilir. Ancak, kırpma işlemi, belirtilmişse her zaman istenen en boy oranıyla eşleşir.

Bunun yerine, aynı *ilgi alanına* ait ham sınırlayıcı kutu koordinatlarını de alabilir. Böylece, bu bilgileri özgün görüntüyü değiştirmek için kullanabilirsiniz.

## <a name="examples"></a>Örnekler

Oluşturulan küçük resim, aşağıdaki görüntüde gösterildiği gibi yükseklik, genişlik ve akıllı kırpma için belirlediğiniz değere göre farklılık gösterir.

![Çeşitli kırpma yapılandırmalarının yanında bir Sıradağlar resmi](./Images/thumbnail-demo.png)

Aşağıdaki tabloda örnek görüntüler için Görüntü İşleme tarafından oluşturulan tipik küçük resimler gösterilmektedir. Küçük resimler, akıllı kırpma etkinken, belirtilen hedef yüksekliği ve 50 piksellik genişlik için oluşturulmuştur.

| Image | Küçük resim |
|-------|-----------|
|![Bir kişinin silueti ile gün içinde Sıradağlar](./Images/mountain_vista.png) | ![Bir kişinin silueti ile gün içinde Sıradağlar ın küçük resmi](./Images/mountain_vista_thumbnail.png) |
|![Yeşil arka plana sahip beyaz çiçek](./Images/flower.png) | ![Vizyon çiçek küçük resmini analiz etme](./Images/flower_thumbnail.png) |
|![Apartman binasının çatı üzerindeki kadın](./Images/woman_roof.png) | ![apartman binasının çatı üzerindeki kadın küçük resmi](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>Sonraki adımlar

[Görüntüleri etiketleme](concept-tagging-images.md) ve [görüntüleri kategorilere ayırma](concept-categorizing-images.md)hakkında bilgi edinin.
