---
title: LUSıS uygulamanızı oluşturmaya yönelik en iyi uygulamalar
description: LUSıS uygulamanızın modelinden en iyi sonuçları elde etmek için en iyi uygulamaları öğrenin.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: d5fa2a1e865a4f54de268e7ad756d1d4363f3b78
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500218"
---
# <a name="best-practices-for-building-a-language-understanding-luis-app"></a>Dil anlama (LUSıS) uygulaması oluşturmak için en iyi uygulamalar
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
|[Şemanızı planlayın](#do-plan-your-schema)|[Plan olmadan derleme ve yayımlama](#dont-publish-too-quickly)|
|[Ayrı amaçlar tanımlayın](#do-define-distinct-intents)<br>[Amaçlar 'a özellik ekleme](#do-add-features-to-intents)<br>
[Makine tarafından öğrenilen varlıkları kullanma](#do-use-machine-learned-entities) |[Amaçlar için birçok örnek ekleme](#dont-add-many-example-utterances-to-intents)<br>[Birkaç veya basit varlık kullanın](#dont-use-few-or-simple-entities) |
|[Her amaç için çok genel ve çok özel arasında bir tatlı noktası bulun](#do-find-sweet-spot-for-intents)|[LUSıS 'yi eğitim platformu olarak kullanma](#dont-use-luis-as-a-training-platform)|
|[Uygulamanızı sürümleriyle tekrarlayarak oluşturun](#do-build-your-app-iteratively-with-versions)<br>[Model ayrıştırma için varlık oluşturma](#do-build-for-model-decomposition)|[Diğer biçimleri yoksayarak aynı biçimdeki birçok örnek utde ekleyin](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[Sonraki yinelemelerde desenler ekleme](#do-add-patterns-in-later-iterations)|[Amaç ve varlıkların tanımını karıştırma](#dont-mix-the-definition-of-intents-and-entities)|
|Hiçbiri amacı dışında [tüm amaçlar genelinde utbotlarınızı dengeleyin](#balance-your-utterances-across-all-intents) .<br>[Hiçbiri amacına örnek ekleme](#do-add-example-utterances-to-none-intent)|[Tüm olası değerlerle tümcecik listeleri oluştur](#dont-create-phrase-lists-with-all-the-possible-values)|
|[Etkin öğrenme için öneri özelliğinden yararlanın](#do-leverage-the-suggest-feature-for-active-learning)|[Çok fazla desen ekleme](#dont-add-many-patterns)|
|[Toplu test ile uygulamanızın performansını izleyin](#do-monitor-the-performance-of-your-app)|[Her tek örnekle eğitim ve yayımlama eklendi](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-plan-your-schema"></a>Şemanızı planlayın

Uygulamanızın şemasını oluşturmaya başlamadan önce, bu uygulamayı kullanmayı planladığınız neleri ve yeri belirlemeniz gerekir. Planlamanızın daha kapsamlı ve özel olması, uygulamanız daha iyi hale gelir.

* Hedeflenen kullanıcıları Araştır
* Uygulamanızı temsil etmek için uçtan uca kişilik tanımlama-ses, Avatar, sorun işleme (proaktif, reaktif)
* Mevcut çözümlere teslim etmek veya bu uygulama için yeni bir çözüm oluşturmak üzere kanalların kullanıcı etkileşimini (metin, konuşma) tanımla
* Uçtan uca Kullanıcı yolculuğu
    * Bu uygulamanın yapması ve bunu yapması beklensin mi? * Ne yapması gerektiğine ilişkin öncelikler nelerdir?
    * Ana kullanım örnekleri nelerdir?
* Veri toplama-verileri toplama ve hazırlama hakkında [bilgi edinin](data-collection.md)

## <a name="do-define-distinct-intents"></a>Ayrı amaçlar tanımlama
Her bir amaca ait sözlük 'in yalnızca bu amaca yönelik olduğundan ve farklı bir amaç ile çakışmadığından emin olun. Örneğin, hava yolu fışıkları ve oteller gibi seyahat düzenlemelerini işleyen bir uygulamaya sahip olmak istiyorsanız, bu konu alanlarının farklı amaçlar halinde veya aynı amaca göre, utterance içindeki belirli veriler için varlıklar kullanabilirsiniz.

İki amaç arasındaki sözlük aynı ise, amacı birleştirin ve varlıkları kullanın.

Aşağıdaki örnek noktaları göz önünde bulundurun:

|Örnek konuşmalar|
|--|
|Uçuş kitabı|
|Otel kitabı|

`Book a flight` ve `Book a hotel` aynı sözlüğünü kullanın `book a ` . Bu biçim aynı olduğu için aynı amaç, `flight` ve ayıklanan varlıkların farklı kelimeleri ile aynı amaç olmalıdır `hotel` .

## <a name="do-add-features-to-intents"></a>Amaçlar 'a özellik ekleme

Özellikler bir amaç için kavramları açıkla. Bir özellik, bu amaç için önemli olan sözcüklerin bir ifade listesi veya bu amaç için önemli bir varlık olabilir.

## <a name="do-find-sweet-spot-for-intents"></a>Amaçlar için tatlı noktası bulun
Amaçlarınızın örtüşmesini öğrenmek için LUSıS 'den tahmin verileri kullanın. Çakışan amaçlar LUSıS 'yi şaşırtır. Sonuç, en üst Puanlama hedefinin başka bir amaç için çok yakın olması olur. LUSıS her seferinde eğitim verileri aracılığıyla tam olarak aynı yolu kullanmadığından, çakışan bir amaç eğitimin ilk veya ikinci bir şansına sahiptir. Bu çevir/flop gerçekleşmemesi için her bir amaç için utterance 'in puanına sahip olmasını istiyorsunuz. Amaçlar için iyi ayrım, her seferinde beklenen en iyi amaç ile sonuçlanmalıdır.

## <a name="do-use-machine-learned-entities"></a>Makine tarafından öğrenilen varlıkları kullanma

Makine tarafından öğrenilen varlıklar uygulamanıza uyarlanmış ve etiketleme işleminin başarılı olmasını gerektirir. Makine tarafından öğrenilen varlıkları kullanmıyorsanız yanlış aracı kullanıyor olabilirsiniz.

Makine tarafından öğrenilen varlıklar, diğer varlıkları özellikler olarak kullanabilir. Bu diğer varlıklar, normal ifade varlıkları veya liste varlıkları gibi özel varlıklar olabilir veya önceden oluşturulmuş varlıkları özellik olarak kullanabilirsiniz.

[Etkin makine tarafından öğrenilen varlıklar](luis-concept-entity-types.md#machine-learned-ml-entity)hakkında bilgi edinin.

<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>Uygulamanızı sürümleriyle tekrarlayarak oluşturma

Her yazma döngüsünün, var olan sürümden klonlanan yeni bir [Sürüm](./luis-concept-app-iteration.md)içinde olması gerekir.

## <a name="do-build-for-model-decomposition"></a>Model ayrıştırma için derleme yap

Model ayrıştırma işleminin tipik bir süreci vardır:

* istemci uygulamanın kullanıcı amaçları temelinde **Amaç** oluşturun
* gerçek dünya Kullanıcı girişine göre 15-30 örnek ekleme
* örnek utde üst düzey veri kavramını etiketle
* veri kavramını alt varlıklara Böl
* alt varlıklara özellikler ekleme
* amaçlar 'a özellik ekleme

Amacınızı oluşturduktan sonra, örnek bir şekilde, aşağıdaki örnekte varlık ayrıştırma açıklanmaktadır.

Bir utterde çıkarmak istediğiniz tüm veri kavramlarını tanımlayarak başlayın. Bu, makine öğrenimi varlığınız. Ardından, tümceciğini parçalarını parçalara ayırın. Bu, alt varlıkları ve özellikleri tanımlamayı içerir.

Örneğin, bir adresi ayıklamak istiyorsanız, en iyi makine öğrenimi varlığı çağrılabilir `Address` . Adresi oluştururken, sokak adresi, şehir, eyalet ve posta kodu gibi alt varlıklarından bazılarını belirleyebilirsiniz.

Bu öğelerin çıkarılması ile devam edin:
* Posta kodunun gerekli bir özelliği normal ifade varlığı olarak ekleniyor.
* Cadde adresini parçalar halinde kaldırma:
    * Önceden oluşturulmuş bir varlık için gerekli özelliği olan bir **cadde numarası** .
    * **Cadde adı**.
    * Avenue, Circle, Road ve Lane gibi sözcükler dahil olmak üzere liste varlığının gerekli özelliğine sahip bir **cadde türü** .

V3 yazma API 'SI, model ayrıştırma için izin verir.

## <a name="do-add-patterns-in-later-iterations"></a>Daha sonraki yinelemelerde desenler ekleme

Desenler eklemeden önce uygulamanın nasıl davrandığını anlamanız gerekir [, çünkü desenler](luis-concept-patterns.md) daha fazla örneğe göre daha fazla ağırlıklı ve güvenle eğrilticektir.

Uygulamanızın nasıl davranacağını anladıktan sonra, uygulamanıza uygulanan desenler ekleyin. Bunları her [yineleme](luis-concept-app-iteration.md)ile eklemeniz gerekmez.

Model tasarımınızın başına ekleme konusu yoktur, ancak model, modelin bu şekilde test edildikten sonra modeli nasıl değiştirdiğinin daha kolay bir şekilde görülebilir.

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>Tüm Amaçlar genelinde deterlerinizi dengeleyin

LULAR tahminlerinin doğru olması için, her bir amaç içindeki (hiçbiri amacı hariç) örnek miktarları görece eşit olmalıdır.

100 örnek ile ilgili bir amaç varsa ve 20 örnek elde ediyorsanız, 100-utterance amacına daha yüksek bir tahmin ücreti sunulacaktır.

## <a name="do-add-example-utterances-to-none-intent"></a>None amacına örnek ekleme

Bu amaç, uygulamanızın dışındaki her şeyi gösteren geri dönüş amacını gösterir. Lusıs uygulamanızın geri kalanında her 10 örnek için, None hedefine bir örnek söylenişi ekleyin.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>Etkin öğrenme için öneri özelliğinden yararlanın

Amaçlar için daha fazla örnek ekleme yerine, [etkin öğrenme](luis-how-to-review-endpoint-utterances.md)'Nin **Gözden geçirme uç noktası araslarını** düzenli olarak kullanın. Uygulama sürekli olarak uç nokta alımı aldığından, bu liste büyümekte ve değişiyor.

## <a name="do-monitor-the-performance-of-your-app"></a>Uygulamanızın performansını izleme

Bir [Batch test](./luis-how-to-batch-test.md) kümesi kullanarak tahmin doğruluğunu izleyin.

[Örnek](luis-concept-utterance.md) Aralık veya uç nokta utaları olarak kullanılmayan ayrı bir Aralık kümesini saklayın. Test kümesi için uygulamayı iyileştirmeye devam edin. Test kümesini gerçek Kullanıcı utslerini yansıtacak şekilde uyarlayın. Uygulamanın her bir yinelemesini veya sürümünü değerlendirmek için bu test kümesini kullanın.

## <a name="dont-publish-too-quickly"></a>Çok hızlı bir şekilde yayımlama

Uygulamanızı [uygun bir planlama](#do-plan-your-schema)yapmadan çok hızlı bir şekilde yayımlamak, şöyle birçok soruna neden olabilir:

* Uygulamanız, kabul edilebilir bir performans düzeyinde gerçek senaryonuza çalışmayacak.
* Şema (amaçlar ve varlıklar) uygun olmaz ve şemadan sonra istemci uygulama mantığını geliştirdiyseniz sıfırdan yeniden yazmanız gerekebilir. Bu durum, üzerinde çalıştığınız projenin beklenmedik gecikmelerini ve ek maliyetini sağlar.
* Modele eklediğiniz söyleyimler hata ayıklamanın ve tanımlanması zor olan örnek söylenişi kümesine doğru bir şekilde yol açabilir. Ayrıca, belirli bir şemayı tamamladıktan sonra belirsizlik kaldırmayı zorlaştırır.

## <a name="dont-add-many-example-utterances-to-intents"></a>Amaçlar için pek çok örnek eklemeyin

Uygulama yayımlandıktan sonra, yalnızca geliştirme yaşam döngüsü sürecinde etkin öğrenmeden gelen utbotları ekleyin. Söyleylikleri çok benzer ise, bir desen ekleyin.

## <a name="dont-use-few-or-simple-entities"></a>Birkaç veya basit varlık kullanmayın

Varlıklar, veri ayıklama ve tahmin için oluşturulmuştur. Her bir amaç, amaç içindeki verileri tanımlayan makine öğrenimi varlıkları olmalıdır. Bu, istemci uygulamanızın ayıklanan varlığı kullanması gerekmiyorsa bile, LUTO 'NıN amacı tahmin etmesine yardımcı olur.

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

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>Tüm olası değerlerle ifade listeleri oluşturmayın

[Tümcecik listelerinde](luis-concept-feature.md) birkaç örnek sağlayın, ancak her sözcüğe veya ifadeye uygulanmaz. LUSıS genelleştirir ve hesabı dikkate alır.

## <a name="dont-add-many-patterns"></a>Çok sayıda desen eklemeyin

Çok fazla [desen](luis-concept-patterns.md)eklemeyin. LUYA daha az örnekle hızlı bir şekilde öğrenmektir. Gereksiz yere sistemi aşırı yüklemeyin.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>Her tek örnekle eğitim ve yayımlama

Eğitim ve yayımlamadan önce 10 veya 15 parametre ekleyin. Bu, tahmin doğruluğunun etkisini görmenizi sağlar. Tek bir söylenişi eklemek, puan üzerinde görünür bir etkiye sahip olmayabilir.

## <a name="next-steps"></a>Sonraki adımlar

* Uygulamanızı LUSıS uygulamanızda nasıl [planlayacağınızı](luis-how-plan-your-app.md) öğrenin.