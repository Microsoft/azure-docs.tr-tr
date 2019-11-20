---
title: LUSıS uygulamanızı test etme
titleSuffix: Azure Cognitive Services
description: Sınama, luve ile tanınan amaçlardan ve varlıklardan oluşan bir yanıt alma sürecine örnektir.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486678"
---
# <a name="testing-example-utterances-in-luis"></a>LUSıS 'de test örneği

Sınama, luve ile tanınan amaçlardan ve varlıklardan oluşan bir yanıt alma sürecine örnektir. 

Lua 'yı etkileşimli olarak test edebilir, bir seferde tek bir söylik sağlayabilir veya bir tanınlik sağlayabilirsiniz. Test ederken, geçerli etkin modelin tahmini yanıtını yayınlanan modelin tahmin yanıtı ile karşılaştırabilirsiniz. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Sınamadaki puan nedir?
Tahmin puanları hakkında daha fazla bilgi için bkz. [tahmin puanı](luis-concept-prediction-score.md) kavramları.

## <a name="interactive-testing"></a>Etkileşimli test
Etkileşimli test, LUO portalının **Test** panelinden yapılır. Amaçlarınızın ve varlıkların nasıl tanımlandığını ve puanlanmadığını görmek için bir söylenişi girebilirsiniz. LUSıS, test panelinde bir utterde beklediği sürece amaçları ve varlıkları tahmin etmemişse, yeni bir utterlik olarak **amacı** sayfasına kopyalayın. Daha sonra varlıklar için bu utterlik parçalarını etiketleyip LUO 'nun eğit. 

## <a name="batch-testing"></a>Toplu işe testi
Tek seferde birden fazla farklı şekilde test ediyorsanız bkz. [Batch test](luis-concept-batch-test.md) .

## <a name="endpoint-testing"></a>Uç nokta testi
[Uç noktasını](luis-glossary.md#endpoint) uygulamanızın en fazla iki sürümü ile kullanarak test edebilirsiniz. **Üretim** uç noktası olarak uygulamanızın ana veya canlı sürümü ile, **hazırlama** uç noktasına ikinci bir sürüm ekleyin. Bu yaklaşım, bir utterance 'in üç sürümünü sağlar: [Luo](luis-reference-regions.md) Web sitesinin test bölmesindeki geçerli model ve iki farklı uç noktada iki sürüm. 

Tüm uç nokta testleri kullanım kotanıza doğru sayılır. 

## <a name="do-not-log-tests"></a>Testleri günlüğe kaydetme
Bir uç noktaya karşı test ederseniz ve söylenişi 'in günlüğe kaydedilmesini istemiyorsanız, `logging=false` sorgu dizesi yapılandırmasını kullanmayı unutmayın.

## <a name="where-to-find-utterances"></a>Utterslar nerede bulunur?
LUıS, **uygulama** listesi sayfasından ve LUIS [yazma API 'lerinin](https://go.microsoft.com/fwlink/?linkid=2092087)yanı sıra LUIS portalında indirileceği, sorgu günlüğünde bulunan tüm günlüğe kaydedilen tüm detersliği depolar. 

Herhangi bir söyleyki, [lusıs](luis-reference-regions.md) Web sitesinin **[Endpoint deterliği](luis-how-to-review-endpoint-utterances.md)** sayfasında listelenmemiştir. 

## <a name="remember-to-train"></a>Eğitme etmeyi unutmayın
Modelde değişiklik yaptıktan sonra lu, [eğitme](luis-how-to-train.md) yapmayı unutmayın. LUO uygulamasındaki değişiklikler, uygulama eğitilene kadar sınama bölümünde görülmez. 

## <a name="best-practices"></a>En iyi uygulamalar
[En iyi yöntemleri](luis-concept-best-practices.md)öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

* Aradıklarınızı [test etme](luis-interactive-test.md) hakkında daha fazla bilgi edinin.
