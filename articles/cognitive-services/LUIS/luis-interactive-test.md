---
title: LUSıS portalında test uygulaması
titleSuffix: Azure Cognitive Services
description: Uygulamanızı iyileştirmek ve dilini anlamak için uygulamanızda sürekli çalışmak üzere Language Understanding (LUO) kullanın.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: ebc86d1cf91cf79ab83b0f49d9898a91d8be8a75
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500276"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Lusıs uygulamanızı Lua portalında test etme

Bir uygulamayı [test etmek](luis-concept-test.md) , yinelemeli bir işlemdir. LUSıS uygulamanıza eğitim ettikten sonra, amaçları ve varlıkların doğru şekilde tanınıp tanınmadığını görmek için örnek bir şekilde test edin. Aksi takdirde, LUSıS uygulaması, eğit ve test güncelleştirmelerini yeniden yapın. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Bir söylenişi test etme

1. **Uygulamalarım** sayfasında adını seçerek uygulamanıza erişin. 

1. **Test** slayt paneli paneline erişmek için uygulamanızın üst panelinde **Test** ' i seçin.

    ![Eğitim & test uygulaması sayfası](./media/luis-how-to-interactive-test/test.png)

1. Metin kutusuna bir söylenişi girin ve ENTER ' u seçin. **Testte**istediğiniz sayıda test ututlik yazabilirsiniz, ancak aynı anda yalnızca bir tane ile bir tane yazın.

1. Utterance, en üst amacı ve puanı, metin kutusunun altındaki söylik listesine eklenir.

    ![Etkileşimli test, yanlış amacı tanımlıyor](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Puanı İncele

**İnceleme** panelinde test sonucunun ayrıntılarını inceleyebilirsiniz. 
 
1. **Test** slayt paneli açıkken, karşılaştırmak istediğiniz bir utterlik için **İncele** ' yi seçin. 

    ![Test sonuçlarıyla ilgili daha fazla ayrıntı görmek için Inceleme düğmesini seçin](./media/luis-how-to-interactive-test/inspect.png)

1. **İnceleme** paneli görüntülenir. Panel, en iyi Puanlama amacını ve tanımlanan tüm varlıkları içerir. Panel, seçili utterlik 'in sonucunu gösterir.

    ![Panel, en iyi Puanlama amacını ve tanımlanan tüm varlıkları içerir. Panel, seçili utterlik 'in sonucunu gösterir.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Doğru en iyi Puanlama hedefi

1. En üst Puanlama hedefi yanlış ise **Düzenle** düğmesini seçin.

1.  Aşağı açılan listede, utterance için doğru amacı seçin.

    ![Doğru amacı seçin](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Yaklaşım sonuçlarını görüntüle

**[Yayımlama](luis-how-to-publish-app.md#enable-sentiment-analysis)** sayfasında yaklaşım **Analizi** yapılandırılırsa, test sonuçları, utde bulunan yaklaşımı içerir. 

![Yaklaşım analiziyle test bölmesinin görüntüsü](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Eşleşen düzenin hedefini düzeltin

[Desenler](luis-concept-patterns.md) kullanıyorsanız ve söyleyeni bir desenle eşleşirse, ancak yanlış amaç tahmin edildiğinde, deseni **Düzenle** bağlantısını seçin ve ardından doğru amacı seçin.

## <a name="compare-with-published-version"></a>Yayımlanan sürümle karşılaştır

Yayınlanan [uç nokta](luis-glossary.md#endpoint) sürümüyle uygulamanızın etkin sürümünü test edebilirsiniz. **İnceleme** panelinde, **yayımlandı ile karşılaştır**' ı seçin. Yayımlanan modele karşı herhangi bir test, Azure abonelik kotası bakiyesinden düşülür. 

![Yayımlanla Karşılaştır](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Test panelinde Endpoint JSON görüntüleme
Karşılaştırma için döndürülen JSON bitiş noktasını göster ' i seçerek **JSON görünümünü göster**' i seçebilirsiniz.

![Yayımlanan JSON yanıtı](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Test panelinde ek ayarlar

### <a name="luis-endpoint"></a>LUSıS uç noktası

Birkaç LUO uç noktanız varsa, test için kullanılan uç noktayı değiştirmek için testin yayımlanan bölmesindeki **ek ayarlar** bağlantısını kullanın. Hangi uç noktanın kullanılacağını bilmiyorsanız varsayılan **Starter_Key**' ı seçin. 

![Ek ayarlar bağlantısı vurgulanmış şekilde test paneli](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Bing Yazım Denetimi düzeltmelerini test panelinde görüntüle

Yazım denetimi düzeltmelerini görüntüleme gereksinimleri: 

* Yayımlanan uygulama
* [Hizmet anahtarı](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)Bing yazım denetimi. Hizmet anahtarı depolanmaz ve her tarayıcı oturumu için sıfırlanması gerekir. 

Test bölmesi sonuçlarına [Bing yazım denetimi v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) hizmetini eklemek için aşağıdaki yordamı kullanın. 

1. **Test** bölmesinde bir utterance girin. Söylenişi tahmin edildiğinde, girdiğiniz utterlik 'in altında **[Denetle](#inspect-score)** ' yi seçin. 

1. **İnceleme** bölmesi açıldığında, **[Yayımlanla Karşılaştır](#compare-with-published-version)** ' ı seçin. 

1. **Yayımlanan** Panel açıldığında, **[ek ayarlar](#additional-settings-in-test-panel)** ' ı seçin.

1. Açılır iletişim kutusunda **Bing yazım denetimi etkinleştir** ' i işaretleyin ve anahtarı girip **bitti**' yi seçin. 
    Bing Yazım Denetimi hizmet anahtarı ![girin](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. `book flite to seattle` gibi yanlış yazımla bir sorgu girin ve ENTER ' u seçin. `flite` sözcüğünün yanlış yazımı, LUVE ' a gönderilen sorguda değiştirilmiştir ve sonuçta elde edilen JSON, `query`ve sorguda düzeltilen yazımı `alteredQuery`olarak gösterir.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="batch-testing"></a>Toplu işe testi
Toplu iş testi [kavramlarını](luis-concept-batch-test.md) görün ve toplu iş türlerini [nasıl](luis-how-to-batch-test.md) test leyeceğinizi öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

Test, LUP uygulamanızın doğru amaçları ve varlıkları algıladığını gösteriyorsa, daha fazla yer etiketleyerek veya özellik ekleyerek LUSıS uygulamanızın doğruluğunu iyileştirebilmek için çalışabilirsiniz. 

* [LUSıS ile önerilen bir şekilde etiketleme](luis-how-to-review-endpoint-utterances.md) 
* [LUSıS uygulamanızın performansını artırmak için özellikleri kullanın](luis-how-to-add-features.md) 
