---
title: Etiket varlık örneği söylenişi
titleSuffix: Azure Cognitive Services
description: Makine tarafından öğrenilen bir varlığı, LUS portalının amaç ayrıntısı sayfasında örnek bir örnekte bulunan alt bileşenler ile nasıl etiketleyeceğinizi öğrenin.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74135113"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Makine tarafından öğrenilen varlığı örnek bir mekan etiketleme

Bir varlığın bir örnek içinde etiketlenmesi, LUO 'nun varlık bir örneği olduğunu ve varlığın utterde görünebileceği yeri gösterir. 

## <a name="labeling-machine-learned-entity"></a>Makine tarafından öğrenilen Varlık etiketleme

`hi, please I want a cheese pizza in 20 minutes`tümceciğini göz önünde bulundurun. 

1. En soldaki metni seçin ve ardından varlığın en sağ metnini seçin. _Tüm sıra_ aşağıdaki görüntüde etiketlidir.

    > [!div class="mx-imgBorder"]
    > ![etiketi makine tarafından öğrenilen varlık](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Açılır penceredeki varlığı seçin. Etiketli tamamlanmış pizza sırası varlığı, etiketli tüm sözcükleri (Ingilizce 'de soldan sağa) içerir. 

> [!TIP]
> Açılır pencerede bulunan varlıklar, metnin göründüğü içeriğe göre değişir. Örneğin, 5 düzeyli bir makine tarafından öğrenilen bir varlığınız varsa ve 3. düzeyde (örneğin, bir etiketli varlık adı ile belirtilir) metin seçiyorsanız, açılır pencerede bulunan varlıkların 3. düzey (4 seviye alt bileşenleri). 

## <a name="review-labeled-text"></a>Etiketli metni gözden geçirme

Etiketledikten sonra, örneği gözden geçirin. LUO, etiketlemeye başladıktan sonra geçerli modeli örneğe uygular. Düz çizgi, metnin etiketlendiği anlamına gelir. 

> [!div class="mx-imgBorder"]
> ![makine tarafından öğrenilen varlığın tamamını etiketlendi](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Ne zaman eğitme

Geçerli modelin etiketli varlığınızı desteklemesi gerekiyorsa, ancak bu örnek, metni tahmin edilen ancak etiketlenmemiş olarak göstermeye devam ediyorsa, uygulamanızı eğitme.  

## <a name="confirm-predicted-entity"></a>Tahmin edilen varlığı Onayla

Görsel gösterge, utterance 'in üstündeyse, metnin tahmin _edildiğini ancak henüz etiketlenmediğini_belirtir. Tahmine bir etiketi açmak için, utterance ' i seçin ve ardından **varlık tahminleri Onayla**' yı seçin.

> [!div class="mx-imgBorder"]
> ![makine tarafından öğrenilen tüm varlığı tahmin edin](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Varlık paleti imletiyle boyayarak alt bileşen varlığını etiketleme

1. Tahmine dayalı olarak (örneğin, örnek utterine ilişkin görünen varlıklar) düzeltilmesi için varlık paletini açın. 

    > [!div class="mx-imgBorder"]
    > makine tarafından öğrenilen varlık için varlık paleti ![](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Varlık alt bileşenini seçin. Bu eylem, görsel olarak yeni bir imlece belirtilir. İmleç, portalda hareket ettirdiği şekilde fareyi izler. 

    > [!div class="mx-imgBorder"]
    > makine tarafından öğrenilen varlık için varlık paleti ![](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. Örnekte, varlığı imlece _boyayın_ . 

    > [!div class="mx-imgBorder"]
    > makine tarafından öğrenilen varlık için varlık paleti ![](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Eşleşen metin varlıklarını makine tarafından öğrenilen bir varlığa etiketleme

Eşleşen metin varlıkları, önceden oluşturulmuş varlıklar, normal ifade varlıkları ve liste varlıkları içerir. Makine tarafından öğrenilen varlığı oluştururken veya düzenlediğinizde, bunları bir alt bileşen için kısıtlamalar olarak makine tarafından öğrenilen bir varlığa eklersiniz. 

**Bu kısıtlamalar eklendikten sonra, eşleşen metni örnekteki metinde etiketlemenize gerek yoktur.**

## <a name="entity-prediction-errors"></a>Varlık tahmin hataları

Varlık tahmin hataları bir uyarı göstergesi gösterir. Bu, tahmin edilen varlığın etiketlenmiş varlıkla eşleşmediğini belirtir. 

> [!div class="mx-imgBorder"]
> makine tarafından öğrenilen varlık için varlık paleti ![](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Sonraki adımlar

Uygulamanızı tahmin kalitesini artırmak için [panoyu](luis-how-to-use-dashboard.md) kullanın ve [uç nokta utlerini gözden geçirin](luis-how-to-review-endpoint-utterances.md) .
