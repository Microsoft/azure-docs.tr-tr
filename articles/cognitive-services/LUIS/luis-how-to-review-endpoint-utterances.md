---
title: Kullanıcı utslerini gözden geçirme-LUSıS
titleSuffix: Azure Cognitive Services
description: Etkin öğrenimi tarafından yakalanan bir şekilde gözden geçirerek, okuma ve varlık kullanımı için varlıkları seçin; değişiklikleri kabul edin, eğitme ve yayımlayın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 67953f552b5b2bcdd7d13253548227e57dab8548
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132660"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Uç nokta utbotları inceleyerek LUO uygulamasını geliştirme

Doğru tahmine yönelik uç nokta dıklarını gözden geçirme işlemi, [etkin öğrenme](luis-concept-review-endpoint-utterances.md)olarak adlandırılır. Etkin öğrenme, uç nokta sorgularını yakalar ve kullanıcının gereken uç nokta utlerini seçer. Bu yazılanları inceleyerek, bu okuma dünyasının amacını seçin ve varlıkları işaretleyin. Bu değişiklikleri örnek söyleyenlerinde kabul edin ve yayımlayın. LUO daha sonra, bir daha doğru şekilde daha doğru şekilde tanımlanır.

Bir LUSıS uygulamasına katkıda bulunan çok sayıda kişi varsa, 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="enable-active-learning"></a>Etkin öğrenmeyi etkinleştir

Etkin öğrenmeyi etkinleştirmek için Kullanıcı sorgularını günlüğe yazmanız gerekir. Bu, `log=true` QueryString parametresi ve değeri ile [Endpoint sorgusu](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) çağırarak gerçekleştirilir.

## <a name="correct-intent-predictions-to-align-utterances"></a>Söylemeleri hizalamak için amaç tahminleri doğru

Her utterance görüntülenen önerilen bir amacı güdülür; **hedefi hizalı** sütun. 

> [!div class="mx-imgBorder"]
> [![gözden geçirme uç noktası, LUO 'nun emin olduğu](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Bu amacı kabul ediyorsanız onay işaretini seçin. Öneri katılmıyorum doğru hedefi hizalı hedefi aşağı açılan listeden seçin, sonra hizalanmış amaç sağındaki onay işaretini seçin. Onay işaretini seçtikten sonra, söylenişi hedefe taşınır ve **Gözden geçirme uç noktası uttasları** listesinden kaldırılır. 

> [!TIP]
> **Gözden geçirme uç noktası dıklarından** gelen tüm örnek varlık tahminlerini gözden geçirmek ve düzeltmek için amaç ayrıntıları sayfasına gitmeniz önemlidir.

## <a name="delete-utterance"></a>Utterance Sil

Her utterance gözden geçirme listesinden silinebilir. Silindikten sonra listeden yeniden görünmez. Uç noktasından aynı utterance kullanıcının girdiği olsa bile bu geçerlidir. 

Söylenişi 'i silmeniz gerekip gerekmediğini bilmiyorsanız, bunu hiçbiri amacına taşıyın veya `miscellaneous` gibi yeni bir amaç oluşturun ve bu amaca göre taşıyın. 

## <a name="disable-active-learning"></a>Etkin öğrenmeyi devre dışı bırak

Etkin öğrenmeyi devre dışı bırakmak için Kullanıcı sorgularını günlüğe eklemeyin. Bu, varsayılan değer false olduğu için, `log=false` QueryString parametresi ve değeri ile [Endpoint sorgusu](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) ayarlanarak ve QueryString değeri olmadan gerçekleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Önerilen konuşma etiket sonra performansı nasıl iyileştirir test etmek için test konsolunda seçerek erişebilirsiniz **Test** üst panelinde. Test Konsolu kullanarak uygulamanızı test etme hakkında yönergeler için bkz: [eğitme ve uygulamanızı test](luis-interactive-test.md).
