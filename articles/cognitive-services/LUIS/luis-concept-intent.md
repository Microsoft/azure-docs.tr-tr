---
title: Amaçlar ve varlıklar-LUSıS
titleSuffix: Azure Cognitive Services
description: Tek bir amaç, kullanıcının gerçekleştirmek istediği bir görevi veya eylemi temsil eder. Bu, kullanıcının ifadesinde ortaya konan hedeftir. Uygulamanızda almak istedikleri eylemlere karşılık gelen bir amaç kümesi tanımlayın.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 8e76e3e7683d43a7a39bc0c168a29016a988c705
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499419"
---
# <a name="intents-in-your-luis-app"></a>LUSıS uygulamanızdaki amaçlar

Amaç, kullanıcının gerçekleştirmek istediği bir görevi veya eylemi temsil eder. Bu, kullanıcının [utterliği](luis-concept-utterance.md)içinde ifade edilen bir amaç veya hedeftir.

Uygulamanızda almak istedikleri eylemlere karşılık gelen bir amaç kümesi tanımlayın. Örneğin, bir seyahat uygulaması çeşitli amaçlar tanımlar:

Seyahat uygulaması amaçları   |   Örnek konuşmalar   |
------|------|
 BookFlight     |   "RIO sonraki haftada bir uçuş <br/> "24th 'da RIO 'ya giriş" <br/> "Sonraki Pazar için bir düzlem bileti gerekir.    |
 Karşılama     |   "Selam" <br/>"Merhaba" <br/>"Günaydın"  |
 CheckWeather | "Boston 'da olduğu gibi hava durumu nedir?" <br/> "Bu hafta sonu için tahminleri göster" |
 Yok         | "Bana bir tanımlama bilgisi tarifi al"<br>"Lamanlar kazanın mi?" |

Tüm uygulamalar, geri dönüş amacı olan önceden tanımlanmış "[none](#none-intent)" hedefi ile gelir.

## <a name="prebuilt-domains-provide-intents"></a>Önceden oluşturulmuş etki alanları amaçları sağlar
Tanımladığınız amaçlardan ek olarak, önceden oluşturulan hedefleri [önceden oluşturulmuş etki alanlarından](./howto-add-prebuilt-models.md)birini kullanarak kullanabilirsiniz.

## <a name="return-all-intents-scores"></a>Tüm Amaçlar puanlarını döndür
Tek bir amaç için bir söylenişi atarsınız. Luara, uç noktada bir söylenişi aldığında, varsayılan olarak o zaman için en üst amacı döndürür.

Utterance için tüm amaçlar için puanları istiyorsanız, tahmin API 'sinin sorgu dizesinde bir bayrak sağlayabilirsiniz.

|Tahmin API sürümü|Bayrak|
|--|--|
|V2|`verbose=true`|
|Yüklemesinde|`show-all-intents=true`|

## <a name="intent-compared-to-entity"></a>Varlıkla karşılaştırılan amaç
Amaç, uygulamanın kullanıcı için yapması gereken eylemi temsil eder ve tüm söylik 'i temel alır. Bir söylenişi yalnızca bir tane en üst Puanlama amacına sahip olabilir, ancak birçok varlık olabilir.

<a name="how-do-intents-relate-to-entities"></a>

Kullanıcının _amacı_ , checkhava durumu () işlevine yapılan bir çağrı gibi istemci uygulamanızda bir eylem tetikleyeceğinden bir amaç oluşturun. Ardından, eylemi yürütmek için gereken parametreleri temsil eden varlıklar oluşturun.

|Amaç   | Varlık | Örnek konuşma   |
|------------------|------------------------------|------------------------------|
| CheckWeather | {"tür": "konum", "varlık": "Seattle"}<br>{"Type": "Builtin. datetimeV2. Date", "Entity": "Yarın", "Çözüm": "2018-05-23"} | Hava durumu ne gibi `Seattle` `tomorrow` ? |
| CheckWeather | {"tür": "date_range", "varlık": "Bu hafta sonu"} | Şunu göster `this weekend` |
||||

## <a name="prebuilt-domain-intents"></a>Önceden oluşturulmuş etki alanı amaçları

[Önceden oluşturulan etki alanları](./howto-add-prebuilt-models.md) , utterlerle amaçlar sağlar.

## <a name="none-intent"></a>Amaç yok

**Hiçbiri** amacı oluşturulur ancak boş bırakılır. **Hiçbiri** amacı gerekli bir amaç değildir ve silinemez veya yeniden adlandırılamaz. Bu amacı etki alanınız dışında kalan ifadelerle doldurun.

**Hiçbiri** amacı her uygulama için önemli olan geri dönüş amacı ve toplam dikkat sayısının %10 ' u olmalıdır. Bu, uygulama etki alanında (konu alanı) önemli olmayan LUSıS utssıs 'leri öğretmek için kullanılır. **Hiçbiri** amacı için herhangi bir söylik eksiz DEĞILSENIZ, Lua, etki alanı dışındaki bir şekilde etki alanı amaçlardakilerden birine zorlar. Bu, bu tahmin puanlarını, deterlik için yanlış bir amaç öğretmek için eğriltebilir.

Bir söylenişi, None amacı olarak tahmin edildiğinde, istemci uygulaması daha fazla soru sorabilir veya kullanıcıyı geçerli seçeneklere yönlendirecek bir menü sağlayabilir.

## <a name="negative-intentions"></a>Negatif amaçları
" **Bir otomobil istiyorum"** ve " **araba istemiyorum"** gibi negatif ve pozitif amaçları tespit etmek istiyorsanız, iki amaç oluşturabilir (bir pozitif ve bir negatif) ve her biri için uygun bir Aralık ekleyebilirsiniz. Ya da tek bir amaç oluşturabilir ve iki farklı pozitif ve negatif terimi bir varlık olarak işaretleyebilirsiniz.

## <a name="intents-and-patterns"></a>Amaçlar ve desenler

Bir normal ifade olarak kısmen veya bütün olarak tanımlanabilen örnek utsunuz varsa, bir [desen](luis-concept-patterns.md)ile eşleştirilmiş [normal ifade varlığını](luis-concept-entity-types.md#regex-entity) kullanmayı düşünün.

Bir normal ifade varlığı kullanmak, düzenin eşleşmesi için veri ayıklamasını garanti eder. Bu eşleştirme, tam bir amacı garanti eder.

## <a name="intent-balance"></a>Amaç bakiyesi
Uygulama etki alanı amaçları, her amaç genelinde bir dizi kullanım bakiyesine sahip olmalıdır. 10 utkollarla bir amaç ve 500 utterer ile başka bir amaç yoktur. Bu dengeli değildir. Bu durumda, amaçlarının çoğunun bir [düzende](luis-concept-patterns.md)yeniden düzenlenebilmeme olup olmadığını görmek için 500 utterler ile amacı gözden geçirin.

**Hiçbir** amaç bakiyeye dahil değildir. Bu amaç, uygulamadaki toplam söyleyinin %10 ' un %10 ' a sahip olmalıdır.

## <a name="intent-limits"></a>Amaç limitleri
Bir modele kaç amaç ekleyebileceğiniz hakkında bilgi edinmek için [limitleri](luis-limits.md#model-boundaries) gözden geçirin.

### <a name="if-you-need-more-than-the-maximum-number-of-intents"></a>En fazla amaç sayısından daha fazlasına ihtiyacınız varsa
İlk olarak, sisteminizin çok fazla amaç kullanıp kullanmadığını göz önünde bulundurun.

### <a name="can-multiple-intents-be-combined-into-single-intent-with-entities"></a>Birden çok amaç, varlıklarla tek amaç halinde birleştirilebilir
Çok benzer amaçlar, LUID 'leri aralarında ayırt etmek daha zor hale getirir. Amaçlar, kullanıcının istediği ana görevleri yakalamaya yetecek kadar farklılaştırılmalıdır, ancak kodunuzun aldığı her yolu yakalamaya gerek kalmaz. Örneğin, Bookuçuş ve FlightCustomerService bir seyahat uygulamasında ayrı amaçlar olabilir, ancak Bookuluslararası Aluçuş ve BookDomesticFlight çok benzerdir. Sisteminizin bunları ayırt etmek gerekiyorsa, amaçları yerine varlıkları veya diğer mantığı kullanın.

### <a name="dispatcher-model"></a>Dağıtıcı modeli
LUVE QnA Maker uygulamalarını [dağıtma modeliyle](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps)birleştirme hakkında daha fazla bilgi edinin.

### <a name="request-help-for-apps-with-significant-number-of-intents"></a>Önemli sayıda amaç içeren uygulamalar için yardım isteyin
Amaç sayısını azalttıktan veya amaçlarınızı birden çok uygulamaya bölmek sizin için desteğe başvurun. Azure aboneliğiniz destek hizmetleri içeriyorsa, [Azure teknik desteği](https://azure.microsoft.com/support/options/)'ne başvurun.

## <a name="next-steps"></a>Sonraki adımlar

* Amaçlar ile ilgili önemli sözcükler olan [varlıklar](luis-concept-entity-types.md)hakkında daha fazla bilgi edinin
* LUSıS uygulamanızda [amaçları ekleme ve yönetme](luis-how-to-add-intents.md) hakkında bilgi edinin.
* Hedef [en iyi uygulamaları](luis-concept-best-practices.md) gözden geçirme