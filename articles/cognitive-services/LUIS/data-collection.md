---
title: Veri toplama
description: Uygulamanızı geliştirirken toplanacak örnek verileri öğrenin
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98738093"
---
# <a name="data-collection-for-your-app"></a>Uygulamanız için veri toplama

Language Understanding (LUSıS) uygulaması, uygulama geliştirmenin bir parçası olarak verilere ihtiyaç duyuyor.

## <a name="data-used-in-luis"></a>LUSıS 'de kullanılan veriler

LUSıS, [amaçları](luis-concept-intent.md) için ve [VARLıKLARıN](luis-concept-entity-types.md)ayıklanması için lusıs uygulamanızı eğitme ve test etmek üzere veri olarak metin kullanır. Hem eğitim hem de test için aşağıda özel olarak adlandırılan farklı veri kümeleri oluşturmak için yeterli veri kümesine sahip olmanız gerekir.  Bu kümelerden her birinde bulunan veriler çakışmamalıdır.

## <a name="training-data-selection-for-example-utterances"></a>Eğitim verileri seçimi örnek olarak

Eğitim kümesi için aşağıdaki ölçütlere göre utterler ' i seçin:

* **Gerçek veriler en iyisidir**:
    * **İstemci uygulamasından gerçek veriler**: istemci uygulamanızdan gerçek veriler olan detersliği seçin.  Müşteri, kendi sorgusu ile bir Web formu gönderirse ve bir bot oluşturuyorsanız, Web formu verilerini kullanarak başlayabilirsiniz.
    * **Kitle-kaynak verileri**: mevcut herhangi bir veriniz yoksa, utalrakları inceleyin.  Uygulamanızın göreceği gerçek verilerin en iyi sonucu elde etmek için senaryonuz için gerçek Kullanıcı popülasyoninizden alınmış kaynak çeklerini yeniden deneyin. Crowd-kaynaklarca insanlar, bilgisayar tarafından oluşturulan dıklarla daha iyidir.  Belirli desenler üzerinde oluşturulan yapay bir veri kümesi oluşturduğunuzda, bu, insanların oluşturulmasıyla ilgili olarak gördüğünüz doğal çeşitlemelerin büyük bir kısmını ortaya sürmez ve üretimde en iyi şekilde bilgi vermez.
* **Veri çeşitlemesi**:
    * **Bölge çeşitlemesi**: her bir amaca ait verilerin, _oluşturma (kelime_ seçimi) ve _dilbilgisi_ dahil olmak üzere mümkün olduğunca farklı olduğundan emin olun.  Tatil günleri hakkında ık ilkeleri hakkında bir amaç öğretme yapıyorsanız, hizmet verdiğiniz tüm bölgeler için kullanılan koşulları temsil eden uttatile sahip olduğunuzdan emin olun.  Örneğin, Avrupa 'da kişiler soru `taking a holiday` sorabilir `taking vacation days` .
    * **Dil çeşitlemesi**: ikinci bir dilde iletişim kuran farklı yerel dillere sahip kullanıcılarınız varsa, yerel olmayan hoparlörleri temsil eden utterlere sahip olduğunuzdan emin olun.
    * **Giriş çeşitlemesi**: veri giriş yolunuzu değerlendirin. Bir kişiden, bölümden veya giriş cihazında (mikrofona) veri topluyorsanız, büyük olasılıkla uygulamanızın tüm giriş yolları hakkında bilgi edinmesi için önemli olabilecek çeşitliliğe sahip olursunuz.
    * **Noktalama ayrımlılık**: insanların metin uygulamalarında değişen noktalama düzeylerini kullandığını düşünün ve noktalama işaretlerinin nasıl kullanıldığına ilişkin bir çeşitliliğe sahip olduğunuzdan emin olun. Konuşmadan gelen verileri kullanıyorsanız, herhangi bir noktalama işareti yoktur, bu nedenle verilerinizin ikisi de olmamalıdır.
* **Veri dağıtımı**: amaçlar genelinde yayılan verilerin, istemci uygulamanızın aldığı veri yayılmasını temsil ettiğinden emin olun. LUIS uygulamanız, bir Leave (%50%) zamanlama isteği olan utbotları sınıflandırıp, ancak inquiring hakkında kalan gün sayısı (%20), onay yaprakları (%20) için de bkz. ve bazı kapsam dışı ve BT sohbeti (%10) daha sonra, veri kümesinde her bir utterlik türünün örnek yüzdeleri olmalıdır.
* **Tüm veri formlarını kullan**: lusıs uygulamanız birden çok formda veri alıyorsa, bu formları eğitim yapanlara eklediğinizden emin olun. Örneğin, istemci uygulamanız hem konuşma hem de yazılı metin girişi alırsa, konuşmadan metne oluşturulmuş yazılarınızın yanı sıra yazılı utaldıklarla aynı şekilde sahip olmanız gerekir.  İnsanların nasıl yazdıklarından ve konuşma tanıma ve yazım hatalarını yazarken farklı hataların nasıl konuşdukları hakkında farklı Çeşitlemeler görürsünüz.  Bu çeşitlemenin hepsi eğitim verilerinizde temsil edilmelidir.
* **Pozitif ve olumsuz örnekler**: bir Luo uygulaması öğretmek için, amaç (pozitif) ve ne olmadığı (negatif) hakkında bilgi vermelidir. LUSıS 'de, utterslar yalnızca tek bir amaç için pozitif olabilir. Hedefe bir söylenişi eklendiğinde, Lua otomatik olarak aynı örneğin diğer tüm amaçlar için negatif bir örnek oluşturur.
* **Uygulama kapsamı dışındaki veriler**: uygulamanız, tanımlı amaçlarınızın dışında kalan söyleyenler görülecektir, bunları sağladığınızdan emin olun. Belirli bir tanımlı amaca atanmamış örnekler, **none** amacı ile etiketlenecek.  Bu, tanımlı amaçların kapsamı dışında kalan önemli süreleri doğru bir şekilde tahmin etmek için, **hiçbiri** amacı için gerçekçi örneklere sahip olmak önemlidir.

    Örneğin, çıkış zamanına odaklanmış bir HR bot oluşturuyorsanız ve üç amaç varsa:
    * Ayrılın zamanlama veya düzenleme
    * kullanılabilir ayrılma günlerini sorgula
    * Bırakmayı Onayla/onayı Kaldır

    Bu amaçların her ikisini de kapsayan, ancak aynı zamanda uygulamanın şu şekilde sunması gereken potansiyel olarak, bu iki amacı kapsadığınızdan emin olmak istiyorsunuz:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Nadir örnekler**: uygulamanızın nadir örneklere ve yaygın örneklere sahip olmaları gerekecektir.  Uygulamanız nadir bir örnek görmüyorsa, bunları üretimde belirleyemeyecektir. Gerçek veriler kullanıyorsanız, LUYA Uygulamanızın üretimde nasıl çalıştığını daha doğru bir şekilde tahmin edebilirsiniz.

### <a name="quality-instead-of-quantity"></a>Miktar yerine kalite

Daha fazla veri eklemeden önce mevcut verilerinizin kalitesini göz önünde bulundurun.  LUSıS ile makine öğretimi kullanıyorsunuz.  Etiketlerinizin ve tanımladığınız makine öğrenimi özelliklerinin birleşimi, LUıN uygulamanızın kullandığı şeydir.  En iyi tahmini hale getirmek için etiket miktarına güvenmez.  Örnek çeşitliliğe ve LUO Uygulamanızın üretimde göreceği, en önemli parçasıdır.

### <a name="preprocessing-data"></a>Ön işleme verileri

Aşağıdaki ön işleme adımları daha iyi bir LUO uygulaması oluşturmaya yardımcı olur:

* **Yinelenenleri kaldırma**: yinelenen utbotslar, hiçbir şekilde kalmaz, ancak bunları kaldırmak, etiketleme süresini kaydeder.
* **Aynı istemci uygulama ön Işlemini Uygula**: BASIS tahmin uç noktasını çağıran istemci uygulamanız, metni luya göndermeden önce çalışma zamanında veri işleme uygular, aynı şekilde işlenen VERILER üzerinde Luo uygulamasını eğmelisiniz. Örneğin, istemci uygulamanız LUYA girişlerinde yazım düzeltmek için [Bing yazım denetimi](../bing-spell-check/overview.md) KULLANıYORSA, luya eklemeden önce eğitime ve test aradıklarınızı düzeltin.
* **İstemci uygulamasının kullanmayan yeni temizleme işlemleri uygulamayın**: istemci uygulamanız, dilbilgisi veya noktalama gibi herhangi bir temizlik olmadan doğrudan konuşma tarafından oluşturulan metinleri kabul ediyorsa, uttaklarınızın eksik noktalama işaretleri ve hesap için gereken diğer yanlış tanıma dahil olmak üzere aynısını yansıtması gerekir.
* **Verileri temizleme**: bozuk bir konuşma tanıma, yanlışlıkla Anahtarlık veya yanlış yazılmış/yanlış yazılan metinle alabileceğiniz hatalı biçimlendirilmiş girişten kurtumayın. Uygulamanız bunlar gibi girdileri görebiliyorsanız, bunların üzerinde eğitilmek ve test edilmesi önemlidir. Uygulamanızı anlaması beklenmez, _yanlış biçimlendirilmiş bir giriş_ hedefi ekleyin. Bu verileri, LUSıS uygulamanızın çalışma zamanında doğru yanıtı tahmin etmeye yardımcı olmak için etiketleyin. İstemci uygulamanız, gibi uygun olmayan göz ardı edilebilir bir yanıt seçebilir `Please try again` .

### <a name="labeling-data"></a>Verileri etiketleme

* **Metni doğru olarak etiketle: örnek söyleyenlerdeki**, etiketli bir varlığın tüm formlarına sahip olması gerekir. Buna yanlış yazılan, yanlış yazılan ve hatalı çevrilmiş metinler dahildir.

### <a name="data-review-after-luis-app-is-in-production"></a>LUSıS uygulaması üretimde veri incelemesi

Bir uygulamayı üretime dağıttıktan sonra gerçek söylenişi trafiğini izlemek için [uç nokta balonları gözden geçirin](luis-concept-review-endpoint-utterances.md) .  Bu, uygulamanızı iyileştirecek gerçek verilerle eğitimlerinizi güncelleştirmenize olanak tanır. Crowd kaynaklı veya gerçek olmayan senaryo verileriyle oluşturulan tüm uygulamalar, gerçek kullanımına göre iyileştirilmelidir.

## <a name="test-data-selection-for-batch-testing"></a>Toplu test için test verileri seçimi

Eğitim için yukarıda listelenen tüm ilkeler, [test kümesi](./luis-how-to-batch-test.md)için kullanmanız gereken söyler için geçerlidir. Amaçlar ve varlıklar genelinde dağıtımın, gerçek dağılımı mümkün olduğunca yakından yansıtmasının güvence altına aldığından emin olun.

Test kümesindeki eğitim kümesinden gelen utbotları yeniden kullanmayın. Bu, sonuçlarınızı yanlış bir şekilde daha uygun hale getiremez ve LUO Uygulamanızın üretimde nasıl gerçekleştirileceğini doğru şekilde belirtir.

Uygulamanızın ilk sürümü yayımlandıktan sonra, test ayarlamış olduğunuz üretim dağılımını yansıttığından ve zaman içinde gerçekçi performansı izleyebilmeniz için test kümesini gerçek trafikten gelen dıklarla güncelleştirmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [LUBUNUN, tahminden önce verilerinizi nasıl değiştiren hakkında bilgi edinin](luis-concept-data-alteration.md)