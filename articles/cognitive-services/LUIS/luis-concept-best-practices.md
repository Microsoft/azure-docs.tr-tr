---
title: En iyi yöntemler-LUSıS
titleSuffix: Azure Cognitive Services
description: LUSıS uygulamanızın modelinden en iyi sonuçları elde etmek için lu, en iyi uygulamaları öğrenin.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 64d67edaf5affbc908fba7b6c261096589bc84d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487624"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Bilişsel hizmetler ile bir dil için uygulama geliştirmek için en iyi uygulamalar
LUSıS uygulamanızı derlemek için uygulama yazma işlemini kullanın: 

* Yapı dil modelleri (amaçlar ve varlıklar)
* Birkaç eğitim örneği ekleyin (amaç başına 15-30)
* Uç noktada Yayımla
* Uç noktadan test 

Uygulamanız [yayımlandıktan](luis-how-to-publish-app.md)sonra, uç noktadan özellikler, yayımlama ve test için geliştirme yaşam döngüsünü kullanın. Daha fazla örnek ekleyerek bir sonraki yazma döngüsüne başlamamayın çünkü bu, LUSıS 'ın gerçek dünyada Kullanıcı utcileri ile modelinizi öğrenmesinin izin vermez. 

Her iki örneğin ve uç nokta çeşidinin geçerli kümesi, bir bütün olarak, yüksek tahmin puanlarının döndürülünceye kadar bu noktaları genişletmeyin. [Etkin öğrenimi](luis-concept-review-endpoint-utterances.md)kullanarak puanları geliştirir. 




## <a name="do-and-dont"></a>Do ve değil
Aşağıdaki liste, LUSıS uygulamalarına yönelik en iyi yöntemleri içerir:

|Yapın|Yapmayın|
|--|--|
|[Ayrı amaçlar tanımlayın](#do-define-distinct-intents)<br>[Amaçlar 'a tanımlayıcı ekleme](#do-add-descriptors-to-intents) |[Amaçlar için birçok örnek ekleme](#dont-add-many-example-utterances-to-intents)<br>[Birkaç veya basit varlık kullanın](#dont-use-few-or-simple-entities) |
|[Her amaç için çok genel ve çok özel arasında bir tatlı noktası bulun](#do-find-sweet-spot-for-intents)|[LUSıS 'yi eğitim platformu olarak kullanma](#dont-use-luis-as-a-training-platform)|
|[Uygulamanızı sürümleriyle tekrarlayarak oluşturun](#do-build-your-app-iteratively-with-versions)<br>[Model ayrıştırma için varlık oluşturma](#do-build-for-model-decomposition)|[Diğer biçimleri yoksayarak aynı biçimdeki birçok örnek utde ekleyin](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Sonraki yinelemelerde desenler ekleme](#do-add-patterns-in-later-iterations)|[Amaç ve varlıkların tanımını karıştırma](#dont-mix-the-definition-of-intents-and-entities)|
|Hiçbiri amacı dışında [tüm amaçlar genelinde utbotlarınızı dengeleyin](#balance-your-utterances-across-all-intents) .<br>[Hiçbiri amacına örnek ekleme](#do-add-example-utterances-to-none-intent)|[Tüm olası değerlerle tanımlayıcı oluştur](#dont-create-descriptors-with-all-the-possible-values)|
|[Etkin öğrenme için öneri özelliğinden yararlanın](#do-leverage-the-suggest-feature-for-active-learning)|[Çok fazla desen ekleme](#dont-add-many-patterns)|
|[Toplu test ile uygulamanızın performansını izleyin](#do-monitor-the-performance-of-your-app)|[Her tek örnekle eğitim ve yayımlama eklendi](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>Ayrı amaçlar tanımlama
Her bir amaca ait sözlük 'in yalnızca bu amaca yönelik olduğundan ve farklı bir amaç ile çakışmadığından emin olun. Örneğin, hava yolu fışıkları ve oteller gibi seyahat düzenlemelerini işleyen bir uygulamaya sahip olmak istiyorsanız, bu konu alanlarının farklı amaçlar halinde veya aynı amaca göre, utterance içindeki belirli veriler için varlıklar kullanabilirsiniz.

İki amaç arasındaki sözlük aynı ise, amacı birleştirin ve varlıkları kullanın. 

Aşağıdaki örnek noktaları göz önünde bulundurun:

|Örnek konuşmalar|
|--|
|Uçuş kitabı|
|Otel kitabı|

`Book a flight` ve `Book a hotel` aynı `book a `sözlüğünü kullanır. Bu biçim aynı şekilde aynıdır, bu nedenle `flight` farklı kelimeleri ve ayıklanan varlıklar olarak `hotel` aynı amaç olmalıdır. 

## <a name="do-add-descriptors-to-intents"></a>Amaçlar 'a tanımlayıcı ekleme

Tanımlayıcılar, bir amaç için özellikleri tanımlamaya yardımcı olur. Bir tanımlayıcı, bu amaç için önemli olan sözcüklerin bir ifade listesi veya bu amaç için önemli bir varlık olabilir. 

## <a name="do-find-sweet-spot-for-intents"></a>Amaçlar için tatlı noktası bulun
Amaçlarınızın örtüşmesini öğrenmek için LUSıS 'den tahmin verileri kullanın. Çakışan amaçlar LUSıS 'yi şaşırtır. Sonuç, en üst Puanlama hedefinin başka bir amaç için çok yakın olması olur. LUSıS her seferinde eğitim verileri aracılığıyla tam olarak aynı yolu kullanmadığından, çakışan bir amaç eğitimin ilk veya ikinci bir şansına sahiptir. Bu çevir/flop gerçekleşmemesi için her bir amaç için utterance 'in puanına sahip olmasını istiyorsunuz. Amaçlar için iyi ayrım, her seferinde beklenen en iyi amaç ile sonuçlanmalıdır. 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Uygulamanızı sürümleriyle tekrarlayarak oluşturma

Her yazma döngüsünün, var olan sürümden klonlanan yeni bir [Sürüm](luis-concept-version.md)içinde olması gerekir. 

## <a name="do-build-for-model-decomposition"></a>Model ayrıştırma için derleme yap

Model ayrıştırma işleminin tipik bir süreci vardır:

* istemci uygulamanın kullanıcı amaçları temelinde **Amaç** oluşturun
* gerçek dünya Kullanıcı girişine göre 15-30 örnek ekleme
* örnek utde üst düzey veri kavramını etiketle
* veri kavramını alt bileşenlere Böl
* alt bileşenlere tanımlayıcı (Özellikler) ekleme
* amaca tanımlayıcı (Özellikler) ekleme 

Amacınızı oluşturduktan ve örnek bir şekilde, aşağıdaki örnekte varlık ayrıştırma açıklanmaktadır. 

Bir utterde çıkarmak istediğiniz tüm veri kavramlarını tanımlayarak başlayın. Bu, makineniz tarafından öğrenilen varlıktır. Ardından, tümceciğini parçalarını parçalara ayırın. Bu, alt bileşenleri (varlıklar olarak), tanımlayıcılar ve kısıtlamalarla birlikte tanımlamayı içerir. 

Örneğin, bir adresi ayıklamak istiyorsanız, en iyi makine tarafından öğrenilen varlık `Address`olarak çağrılabilir. Adresi oluştururken, sokak adresi, şehir, eyalet ve posta kodu gibi bazı alt bileşenlerinden bazılarını yapın. 

Posta kodunu normal bir **ifadeye geçirerek bu** öğeleri oluşturmaya devam edin. Cadde adresini bir cadde numarası (önceden oluşturulmuş bir sayı kullanarak), sokak adı ve sokak türü bölümlerine ayırın. Cadde türü, Avenue, Circle, Road ve Lane gibi bir **tanımlayıcı** listesi ile açıklanabilir.

V3 yazma API 'SI, model ayrıştırma için izin verir. 

## <a name="do-add-patterns-in-later-iterations"></a>Daha sonraki yinelemelerde desenler ekleme

Desenler eklemeden önce uygulamanın nasıl davrandığını anlamanız gerekir [, çünkü desenler](luis-concept-patterns.md) daha fazla örneğe göre daha fazla ağırlıklı ve güvenle eğrilticektir. 

Uygulamanızın nasıl davranacağını anladıktan sonra, uygulamanıza uygulanan desenler ekleyin. Bunları her [yineleme](luis-concept-app-iteration.md)ile eklemeniz gerekmez. 

Model tasarımınızın başına ekleme konusu yoktur, ancak model, modelin bu şekilde test edildikten sonra modeli nasıl değiştirdiğinin daha kolay bir şekilde görülebilir. 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Tüm Amaçlar genelinde deterlerinizi dengeleyin

LULAR tahminlerinin doğru olması için, her bir amaç içindeki (hiçbiri amacı hariç) örnek miktarları görece eşit olmalıdır. 

100 örnek ile ilgili bir amaç varsa ve 20 örnek elde ediyorsanız, 100-utterance amacına daha yüksek bir tahmin ücreti sunulacaktır.  

## <a name="do-add-example-utterances-to-none-intent"></a>None amacına örnek ekleme

Bu amaç, uygulamanızın dışındaki her şeyi gösteren geri dönüş amacını gösterir. Lusıs uygulamanızın geri kalanında her 10 örnek için, None hedefine bir örnek söylenişi ekleyin.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Etkin öğrenme için öneri özelliğinden yararlanın

Amaçlar için daha fazla örnek ekleme yerine, [etkin öğrenme](luis-how-to-review-endpoint-utterances.md)'Nin **Gözden geçirme uç noktası araslarını** düzenli olarak kullanın. Uygulama sürekli olarak uç nokta alımı aldığından, bu liste büyümekte ve değişiyor.

## <a name="do-monitor-the-performance-of-your-app"></a>Uygulamanızın performansını izleme

Bir [Batch test](luis-concept-batch-test.md) kümesi kullanarak tahmin doğruluğunu izleyin. 

[Örnek](luis-concept-utterance.md) Aralık veya uç nokta utaları olarak kullanılmayan ayrı bir Aralık kümesini saklayın. Test kümesi için uygulamayı iyileştirmeye devam edin. Test kümesini gerçek Kullanıcı utslerini yansıtacak şekilde uyarlayın. Uygulamanın her bir yinelemesini veya sürümünü değerlendirmek için bu test kümesini kullanın. 

## <a name="dont-add-many-example-utterances-to-intents"></a>Amaçlar için pek çok örnek eklemeyin

Uygulama yayımlandıktan sonra, yalnızca geliştirme yaşam döngüsü sürecinde etkin öğrenmeden gelen utbotları ekleyin. Söyleylikleri çok benzer ise, bir desen ekleyin. 

## <a name="dont-use-few-or-simple-entities"></a>Birkaç veya basit varlık kullanmayın

Varlıklar, veri ayıklama ve tahmin için oluşturulmuştur. Her bir amaç, amaca göre verileri tanımlayan makine tarafından öğrenilen varlıkların olması önemlidir. Bu, istemci uygulamanızın ayıklanan varlığı kullanması gerekmiyorsa bile, LUTO 'NıN amacı tahmin etmesine yardımcı olur. 

## <a name="dont-use-luis-as-a-training-platform"></a>LUSıS 'yi eğitim platformu olarak kullanmayın

LUL, bir dil modelinin etki alanına özeldir. Genel bir doğal dil eğitimi platformu olarak çalışması amaçlıyordu. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>Diğer biçimleri yoksayarak aynı biçimdeki birçok örnek ekleme

Lua, bir amaç için Çeşitlemeler bekliyor. Söyleyler aynı genel anlamı elde ederken farklılık gösterebilir. Çeşitlemeler, söylenişi uzunluğu, sözcük seçimi ve sözcük yerleşimini içerebilir. 

|Aynı biçimi kullanma|Değişen biçim kullan|
|--|--|
|Seattle için bir bilet satın alın<br>Paris 'e bilet satın alın<br>Orlando 'ya bilet satın alın|Seattle 'da 1 bilet satın alın<br>Kırmızı gözlük bir sonraki Pazartesi ile iki adet koltuk ayırın<br>Yay kesmek için 3 bilet ve Orlando 'ye kitap eklemek istiyorum|

İkinci sütun farklı yüklemleri (satın alma, ayırma, kitap), farklı miktarlar (1, iki, 3) ve sözcüklerin farklı düzenlemelerini kullanır, ancak hepsi de seyahat için hava yolu biletleri satın alma amacı ile aynıdır. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>Amaç ve varlıkların tanımını karıştırmayın

Botunuzun yapması için gereken tüm eylemler için bir amaç oluşturun. Varlıkları bu eylemi mümkün kılan parametreler olarak kullanın. 

Hava yolu fışıklarından kitap oluşturacak bir bot için, bir **muhasebeci** amaç oluşturun. Her hava yolu veya her hedef için bir amaç oluşturmayın. Bu veri parçalarını [varlık](luis-concept-entity-types.md) olarak kullanın ve örnekleri örnek olarak işaretleyin. 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>Tüm olası değerlerle tanımlayıcı oluşturma

Tanımlayıcı [tümceciği listelerinde](luis-concept-feature.md) birkaç örnek sağlayın, ancak her sözcüğe değil. LUSıS genelleştirir ve hesabı dikkate alır. 

## <a name="dont-add-many-patterns"></a>Çok sayıda desen eklemeyin

Çok fazla [desen](luis-concept-patterns.md)eklemeyin. LUYA daha az örnekle hızlı bir şekilde öğrenmektir. Gereksiz yere sistemi aşırı yüklemeyin.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Her tek örnekle eğitim ve yayımlama

Eğitim ve yayımlamadan önce 10 veya 15 parametre ekleyin. Bu, tahmin doğruluğunun etkisini görmenizi sağlar. Tek bir söylenişi eklemek, puan üzerinde görünür bir etkiye sahip olmayabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanızı LUSıS uygulamanızda nasıl [planlayacağınızı](luis-how-plan-your-app.md) öğrenin.
