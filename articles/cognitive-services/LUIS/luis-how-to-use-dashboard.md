---
title: Pano-Language Understanding-LUO
titleSuffix: Azure Cognitive Services
description: Eğitilen uygulamanızın panosundan hedefleri ve varlıkları onarın. Panoda, düzeltilmesi gereken amaçları vurgularla genel uygulama bilgileri görüntülenir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d9ae126753f55349f9bf3eefd20bc4d222866af1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888210"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>Uygulamanızı geliştirmek için panoyu kullanma

Örnek utbotları kullanırken eğitilen uygulamanızın amaçları ile ilgili sorunları bulup giderin. Panoda, düzeltilmesi gereken amaçları vurgularla genel uygulama bilgileri görüntülenir. 

Pano analizini gözden geçirin yinelemeli bir işlemdir ve modelinizi değiştirirken yineleyin ve modelinizi geliştirebilirsiniz.

Bu sayfada, _yalnızca desen_ uygulamaları olarak bilinen amaçlar içinde herhangi bir örnek yok bulunmayan uygulamalar için ilgili Analize sahip olmayacaktır. 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>Panodan hangi sorunlar düzeltilenebilir?

Panoda giderilen üç sorun şunlardır:

|Sorun|Grafik rengi|Açıklama|
|--|--|--|
|Veri dengesizliği|-|Bu durum, örnek sayısı önemli ölçüde farklılık gösterdiği zaman oluşur. Tüm Amaçlar, her türlü örnek _utde aynı_ sayıda örneğe sahip olmalıdır; hiçbiri hariç. Bu, uygulamadaki toplam kullanım miktarının %10 ' dan %15 ' e sahip olmalıdır.<br><br> Veriler imlenebilir ancak amaç doğruluğu belirli bir eşiğin üzerinde ise, bu dengesizliği bir sorun olarak bildirilmemiştir.<br><br>**Bu sorunla başlayın-diğer sorunların temel nedeni olabilir.**|
|Belirsiz tahmin|Orange|Bu durum, en üstteki amaç ve sonraki amaç puanlarının, bir sonraki eğitimin üzerinde tulabilecekleri, [negatif örnekleme](luis-how-to-train.md#train-with-all-data) veya daha fazla örnek ekleme amacına bağlı olarak yakın olduğu durumlarda meydana gelir. |
|Yanlış tahminler|Kırmızı|Bu durum, etiketlenmiş amaç (içinde olduğu amaç) için bir örnek bir tahmin olmadığında oluşur.|

Doğru tahminler mavi renkle gösterilir.

Panoda bu sorunlar gösterilir ve hangi amaçlardan etkilendiğine ve uygulamayı geliştirmek için ne yapmanız gerektiğini size bildirir. 

## <a name="before-app-is-trained"></a>Uygulama eğitilmadan önce 

Uygulamayı eğmadan önce, pano düzeltme önerisi içermez. Bu önerileri görmek için uygulamanızı eğitme.  

## <a name="check-your-publishing-status"></a>Yayımlama durumunuzu denetleyin

**Yayımlama durumu** kartı, etkin sürümün son yayımlama bilgileri hakkında bilgi içerir. 

Etkin sürümün, düzeltilmesi istediğiniz sürüm olduğunu denetleyin. 

![Pano, uygulamanın dış hizmetlerini, yayımlanan bölgelerini ve toplu uç nokta isabetlerini gösterir.](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

Bu, tüm dış Hizmetleri, yayımlanan bölgeleri ve toplanmış uç nokta isabetlerini de gösterir. 

## <a name="review-training-evaluation"></a>Eğitim değerlendirmesini gözden geçirin

**Eğitim değerlendirme** kartı, uygulamanın genel doğruluk alanının toplu özetini içerir. Puan, amaç kalitesini gösterir. 

![Eğitim değerlendirme kartı, uygulamanızın genel doğruluğu hakkındaki ilk bilgi alanını içerir.](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

Grafik, doğru şekilde tahmin edilen hedefleri ve farklı renklerle ilgili sorun bölgelerini gösterir. Uygulamayı önerilerle geliştirdıkça bu puan artar. 

Önerilen düzeltmeler sorun türüne göre ayrılır ve uygulamanız için en önemidir. Her amaç için sorunları gözden geçirmeyi ve gidermeyi tercih ediyorsanız, sayfanın en altında **[hatalar Içeren amaçlar](#intents-with-errors)** kartını kullanın. 

Her bir sorun alanının düzeltilmesi gereken amaçları vardır. Amaç adını seçtiğinizde, **Amaç** sayfası, söylemeye uygulanan bir filtre ile açılır. Bu filtre, soruna neden olan söyleymelere odaklanabilmenizi sağlar.

### <a name="compare-changes-across-versions"></a>Sürümler arasındaki değişiklikleri karşılaştırın

Uygulamada değişiklik yapmadan önce yeni bir sürüm oluşturun. Yeni sürümde, amacın örnek ara penceresinde önerilen değişiklikleri yapıp yeniden eğitme. Pano sayfasının **eğitim değerlendirme** kartında, değişiklikleri karşılaştırmak için **eğitilen sürümden değişikliği göster** ' i kullanın. 

![Sürümler arasındaki değişiklikleri karşılaştırın](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>Örnek işleme ve yeniden eğitim ekleyerek veya düzenleyerek sürümü onarın

Uygulamanızı düzeltmeye yönelik birincil yöntem, örnek işleme ve yeniden tren eklemek veya düzenlemek olacaktır. Yeni veya değiştirilmiş deterslar, [değişen](luis-concept-utterance.md)söyleylerdeki yönergeleri izlemelidir.

Örnek ekleme örnekleri şunları yapan birisi tarafından yapılmalıdır:

* , farklı amaçlarındıklarla neler olduğunu yüksek ölçüde anlayabiliyor.
* bir amaca göre, başka bir amaç ile nasıl karışabildiğini bilir.
* , birbirleriyle genellikle karıştı olan iki amaç tek bir amaç halinde daraltılacağına karar verebilir. Bu durumda, farklı veriler varlıklarla birlikte kullanıma alınmalıdır.

### <a name="patterns-and-phrase-lists"></a>Desenler ve tümcecik listeleri

Analiz sayfası, [desenlerin](luis-concept-patterns.md) veya [tümcecik listelerinin](luis-concept-feature.md)ne zaman kullanılacağını göstermez. Bunları eklerseniz, yanlış veya belirsiz tahminlerde yardımcı olabilir, ancak veri dengesizliği ile ilgili yardım vermez. 

### <a name="review-data-imbalance"></a>Veri dengesizliği gözden geçirme

Bu sorunla başlayın-diğer sorunların temel nedeni olabilir.

**Veri dengesizliği** amaç listesi, veri dengesizliği ' nı düzeltmek için daha fazla söylenmesi gereken amaçları gösterir. 

**Bu sorunu onarmak için**:

* Hedefe daha fazla konuşma ekleyin ve ardından yeniden eğitme. 

Panoda önerilmediği takdirde, yok amacını eklemeyin.

> [!Tip]
> Hangi amaçlarla ilgili daha fazla bilgi gerektiren hızlı bir görsel kılavuz olarak, sayfa üzerindeki üçüncü bölümü, **utterer (sayı)** ayarıyla birlikte **Amaç uyarınca** kullanın.  
    veri dengesizliği olan amaçları bulmak için ' Utterslar (sayı) ' ![kullanın.](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>Yanlış tahminleri gözden geçirin

**Yanlış tahmin** hedefi listesi, belirli bir amaç için örnek olarak kullanılan ancak farklı amaçlar için tahmin edilen, utterler içeren amaçları gösterir. 

**Bu sorunu onarmak için**:

* Söz konusu hedefe daha fazla özel olacak şekilde düzenleyin ve yeniden eğitme.
* Söyleyceler çok yakından hizalanmışsa ve yeniden eğitmeniz halinde hedefleri birleştirin.

### <a name="review-unclear-predictions"></a>Belirsiz tahminleri gözden geçirin

**Belirsiz tahmin** hedefi listesi, en yakın Rival kadar çok önemli olmayan tahminlere sahip olan amaçları, [negatif örnekleme](luis-how-to-train.md#train-with-all-data)nedeniyle sonraki eğitimin en üst amacına göre değişiklik gösterebilir.

**Bu sorunu giderme**.

* Söz konusu hedefe daha fazla özel olacak şekilde düzenleyin ve yeniden eğitme.
* Söyleyceler çok yakından hizalanmışsa ve yeniden eğitmeniz halinde hedefleri birleştirin.

## <a name="utterances-per-intent"></a>Amaç başına söylenme

Bu kart, amaçlar genelinde uygulamanın genel durumunu gösterir. Amaçları ve yeniden eğitme yaparken, sorunlar için bu karta göz atma konusuna geçin.

Aşağıdaki grafikte, neredeyse düzeltilme sorunu olan iyi şekilde dengelenmiş bir uygulama gösterilmektedir.

![Aşağıdaki grafikte, neredeyse düzeltilme sorunu olan iyi şekilde dengelenmiş bir uygulama gösterilmektedir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

Aşağıdaki grafikte, çok sayıda sorunu gidermek için kötü bir dengeli uygulama gösterilmektedir.

![Aşağıdaki grafikte, neredeyse düzeltilme sorunu olan iyi şekilde dengelenmiş bir uygulama gösterilmektedir.](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

Amaç hakkında bilgi almak için her bir amaç çubuğunun üzerine gelin. 

![Aşağıdaki grafikte, neredeyse düzeltilme sorunu olan iyi şekilde dengelenmiş bir uygulama gösterilmektedir.](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

Bu sorunla birlikte en çok sorunlu hedefleri üzerine odaklanabilmeniz için amaçları sorun türüne göre düzenlemek için **sıralama ölçütü** özelliğini kullanın. 

## <a name="intents-with-errors"></a>Hatalı amaçlar

Bu kart, belirli bir amaç için sorunları incelemenizi sağlar. Bu kartın varsayılan görünümü en sorunlu hedefdir, bu sayede çabalarınızın nereye odaklanacağı hakkında bilgi sahibi olabilirsiniz.

![Hatalar içeren amaçlar, belirli bir amaç için sorunları incelemenizi sağlar. Kart, en çok sorunlu amaçlar için varsayılan olarak filtrelenir. bu sayede çabalarınıza odaklanmayı bilirsiniz.](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

Üstteki halka grafiğinde, üç sorun türü genelinde amaç ile ilgili sorunlar gösterilmektedir. Üç sorun türünde sorunlar varsa, her türün aşağıdaki bir grafiği vardır ve tüm Rival hedefleri ile birlikte. 

### <a name="filter-intents-by-issue-and-percentage"></a>Hedefleri soruna ve yüzdeye göre filtrele

Kartın bu bölümü, hata eşiğin dışında kalan örnek bulmalar bulmanıza olanak tanır. İdeal tahminlerden önemli olmasını isteyebilirsiniz. Bu yüzde iş ve müşteri odaklı olur. 

İşletmeniz için rahat bir şekilde sahip olduğunuz eşik yüzdelerini saptayın. 

Filtre, belirli bir sorunla olan amaçları bulmanıza olanak sağlar:

|Filtre|Önerilen yüzde|Amaç|
|--|--|--|
|En sorunlu amaçlar|-|**Buradan başlayın** -bu amaca göre, uygulamayı diğer düzeltmelerden daha fazla düzeltme.|
|Aşağıdaki tahminleri doğru yapın|%60|Bu, seçilen amaç içindeki, doğru olan, ancak eşiğin altında güven puanı bulunan utterslar yüzdesinin yüzdesidir. |
|Yukarıdaki belirsiz Öngörüler|%15|Bu, en yakın Rival amacına karışarak seçili amaç içindeki dıklarının yüzdesidir.|
|Yukarıdaki yanlış tahminler|%15|Bu, seçilen amaç içindeki, yanlış tahmin edilen madıklarından yüzdesidir. |

### <a name="correct-prediction-threshold"></a>Doğru tahmin eşiği

Size güvenilir bir tahmin güvenilirliği puanı nedir? Uygulama geliştirmenin başlangıcında %60, Hedefinizdeki olabilir. Seçilen amaç içinde düzeltilmesi gereken tüm çakışmaları bulmak için aşağıdaki %60 oranında **doğru tahminleri** kullanın.

### <a name="unclear-or-incorrect-prediction-threshold"></a>Belirsiz veya yanlış tahmin eşiği

Bu iki filtre, eşiğin ötesinde seçili amaç içindeki uttasları bulmanıza olanak tanır. Bu iki yüzdeyi hata yüzdeleri olarak düşünebilirsiniz. Tahmine dayalı olarak% 10-15 hata oranı rahat olursa, bu değerin üzerindeki tüm çakışmaları bulmak için filtre eşiğini %15 olarak ayarlayın. 

## <a name="next-steps"></a>Sonraki adımlar

* [Azure kaynaklarınızı yönetin](luis-how-to-azure-subscription.md)
