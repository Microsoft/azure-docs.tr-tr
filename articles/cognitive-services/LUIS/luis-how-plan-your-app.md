---
title: Uygulamanızı planlayın-LUSıS
description: İlgili uygulama amaçlarını ve varlıklarını ana hatlarıyla oluşturun ve ardından Language Understanding akıllı hizmetler (LUU) içinde uygulama planlarınızı oluşturun.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018965"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>LUSıS uygulama şemanızı konu etki alanı ve veri ayıklama ile planlayın

Bir LUSıS uygulama şeması, konu [etki alanınız](luis-glossary.md#domain)ile ilgili [amaçları](luis-glossary.md#intent) ve [varlıkları](luis-glossary.md#entity) içerir. Amaç, Kullanıcı [araslarını](luis-glossary.md#utterance)sınıflandırır ve varlıklar, Kullanıcı dıklarındaki verileri ayıklar.

## <a name="identify-your-domain"></a>Etki alanınızı tanımla

Bir LUSıS uygulaması, konu etki alanı etrafında ortalanır. Örneğin, Bilet, fışıklar, otel ve Kiralama otomobillerini işleyen bir seyahat uygulamasına sahip olabilirsiniz. Başka bir uygulama uygulamayla ilgili içerik sunabilir, uygunluk çalışmalarını izleme ve amaçları ayarlama. Etki alanını tanımlamak, etki alanınız için uygun olan kelimeleri veya tümceleri bulmanıza yardımcı olur.

> [!TIP]
> LUSıS birçok yaygın senaryo için [önceden oluşturulmuş etki alanları](./howto-add-prebuilt-models.md) sunar. Önceden oluşturulmuş bir etki alanını uygulamanız için başlangıç noktası olarak kullanıp kullananağınızın olup olmadığını kontrol edin.

## <a name="identify-your-intents"></a>Amaçlarınızı tanımla

Uygulamanızın görevi için önemli olan [amaçları](luis-concept-intent.md) göz önünde bulundurun.

Bir uçuş ve Kullanıcı hedefindeki hava durumunu denetlemek için işlevlerle bir seyahat uygulaması örneğini ele alalım. `BookFlight` `GetWeather` Bu eylemler için ve amaçlarını tanımlayabilirsiniz.

Daha fazla işlev içeren daha karmaşık bir uygulamada, daha fazla amaç vardır ve bu amaçları dikkatle tanımlamanız gerekir, böylelikle amaçlar çok özgü değildir. Örneğin, `BookFlight` ve `BookHotel` ayrı amaçlar olması gerekebilir, ancak `BookInternationalFlight` `BookDomesticFlight` çok benzer olabilir.

> [!NOTE]
> Uygulamanızın işlevlerini gerçekleştirmek için ihtiyaç duyduğunuz kadar çok amaç kullanmak en iyi uygulamadır. Çok fazla amaç tanımlarsanız, YAĞıNLARı doğru şekilde sınıflandırılması zor olur. Çok az sayıda tanımlarsanız, bu durum örtüşeceğinden genel olabilir.

Genel Kullanıcı amacını belirlemeniz gerekmiyorsa, amaca yönelik tüm örnek Kullanıcı araslarını ekleyin `None` . Uygulamanız daha fazla amaç gerektiren bir şekilde büyürse, daha sonra oluşturabilirsiniz.

## <a name="create-example-utterances-for-each-intent"></a>Her amaç için örnek oluşturma

Kullanmaya başlamak için, her amaç için çok fazla sayıda konuşma oluşturmaktan kaçının. Hedefleri belirledikten sonra, amaç başına 15 ila 30 örnek oluşturma oluşturun. Her söylük, daha önce sağlandıklarından farklı olmalıdır. Genel sözcük sayısı, kelime seçimi, fiil zaman hali ve [noktalama işaretleri](luis-reference-application-settings.md#punctuation-normalization)de iyi bir şekilde bulunur.

Daha fazla bilgi için bkz. [lusıs uygulamaları için iyi BIR değer anlama](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Varlıklarınızı tanımla

Örnekte, ayıklanmak istediğiniz varlıkları belirler. Bir uçuş sağlamak için hedef, tarih, Airline, Bilet kategorisi ve seyahat sınıfı gibi bilgilere ihtiyacınız vardır. Bu veri türleri için varlıklar oluşturun ve sonra örnek söyleylerdeki [varlıkları](luis-concept-entity-types.md) işaretleyin. Varlıklar, amacı yerine getirmeye önem taşır.

Uygulamanızda hangi varlıkların kullanılacağını belirlerken, nesne türleri arasındaki ilişkileri yakalamak için farklı türde varlıkların olduğunu aklınızda bulundurun. [Luve Içindeki varlıklar](luis-concept-entity-types.md) , farklı türler hakkında daha fazla ayrıntı sağlar.

> [!TIP]
> LUSıS, yaygın, konuşma Kullanıcı senaryolarında [önceden oluşturulmuş varlıklar](./howto-add-prebuilt-models.md) sunmaktadır. Önceden oluşturulmuş varlıkları, uygulama geliştirme için bir başlangıç noktası olarak kullanmayı düşünün.

## <a name="resolution-with-intent-or-entity"></a>Amaç veya varlıkla ilgili çözüm mi var?

Çoğu durumda, özellikle doğal konuşma ile çalışırken, kullanıcılar birden fazla işlev veya amaç içerebilen bir utterlik sağlar. Bunu ele almak için, bir Thumb genel kuralı, çıkışın gösteriminin hem amaçlar hem de varlıklarda yapılabileceğini anlamaktır. Bu gösterim, istemci uygulama eylemleriniz için eşlenebilir ve amaçlar ile sınırlı olması gerekmez.

**Int-ENT** , eylemlerin (genellikle amaçlar olarak anlamış) varlık olarak yakalanabileceğini ve bu formda belirli bir eylemle eşleyebileceğiniz çıkış JSON ' de bu formda güvenebilmesini sağlayan kavramdır. _Olumsuzlama_ , tam ayıklama için hem amaç hem de varlık üzerinde bu güvenden yararlanmak için yaygın bir kullanımdır.

Sözcük seçimini göz önünde bulundurarak ancak farklı sonuçlara sahip olan aşağıdaki iki kuralı göz önünde bulundurun:

|İfade|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

İki ayrı amaç olması yerine, Machine Learning varlığıyla tek bir amaç oluşturun `FlightAction` . Machine Learning varlığı, eylemin ayrıntılarını hem zamanlama hem de iptal etme isteği ve kaynak ya da hedef konum için ayıklamalıdır.

`FlightAction`Varlık, makine öğrenimi varlığı ve alt varlıkların aşağıdaki sözde şemasında yapılandırılır:

* Fışıklı TAction
    * Eylem
    * Kaynak
    * Hedef

Ayıklamanın, alt varlıklara özellikler eklemesine yardımcı olmak. Kullanıcı arasları ' nda görmeyi düşündüğünüz sözlük ve tahmin yanıtında döndürülmesini istediğiniz değerler temelinde, özelliklerinizi seçersiniz.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [LUSıS geliştirme lifecylce öğrenme](luis-concept-app-iteration.md)