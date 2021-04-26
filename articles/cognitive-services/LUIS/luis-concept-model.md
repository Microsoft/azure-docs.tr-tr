---
title: Modellerle tasarım-LUSıS
description: Dil anlama çeşitli model türleri sağlar. Bazı modeller birden fazla şekilde kullanılabilir.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316520"
---
# <a name="design-with-intent-and-entity-models"></a>Amaç ve varlık modelleriyle tasarım

Dil anlama, uygulama şemanızı tanımlamanız için iki tür model sağlar. Uygulama şemanız, yeni bir kullanıcı tarafından tahminten alacağınız bilgileri belirler.

Uygulama şeması, [makine öğretimi](#authoring-uses-machine-teaching)kullanarak oluşturduğunuz modellerden oluşturulmuştur:
* [Amaç](#intents-classify-utterances) sınıflandırma Kullanıcı kullanıcıları
* [Varlıklar](#entities-extract-data) , söylenişi 'ten veri ayıklar

## <a name="authoring-uses-machine-teaching"></a>Yazma, makine öğretimi kullanıyor

LUSıS 'in makine eğitim yöntemi bir makine için kavramları kolayca öğretebilir. _Makine öğrenimini_ anlamak, lusıs kullanmak için gerekli değildir. Bunun yerine, öğretmen olarak bir kavram, kavram örnekleri sunarak ve diğer ilgili kavramları kullanarak bir kavramın nasıl modellendirildiğini açıklayan bir kavram ile iletişim kurar. Ayrıca, öğretmen olarak, tahmin hatalarını tanımlayarak ve düzelterek LUSıS 'nin modelini etkileşimli bir şekilde geliştirebilirsiniz.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>Amaçlar ifadeleri sınıflandırır

Amaç hakkında daha fazla bilgi almak için bir amaç sınıflandırmalar örnek olarak KULLANıLıR. Bir amaç içindeki örnek söyleyler, utterance 'in pozitif örnekleri olarak kullanılır. Bu aynı söyleyler, diğer tüm amaçlar için negatif örnekler olarak da kullanılır.

Bir kullanıcının, bir kitabı ve müşterinin sevkiyat adresine ihtiyacı olan bir uygulamayı sıralamasını belirlemesi gereken bir uygulamayı düşünün. Bu uygulamanın iki amacı vardır: `OrderBook` ve `ShippingLocation` .

Aşağıdaki söylenişi, amaç için **olumlu bir örnektir** ve `OrderBook` ve amaçları için **negatif bir örnektir** `ShippingLocation` `None` :

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>Varlıklar verileri ayıklar

Bir varlık, söyleyden ayıklanmasını istediğiniz bir veri birimini temsil eder. Makine öğrenimi varlığı, aynı zamanda makine öğrenimi varlıkları olan alt varlıklar içeren en üst düzey bir varlıktır.

Makine öğrenimi varlığına bir örnek, düzlem bileti için bir sıradır. Kavramsal olarak, tarih, saat, bilgisayar lisansı sayısı, ilk sınıf veya Coach, kaynak konumu, hedef konumu ve yemek seçimi gibi çok daha küçük veri birimlerine sahip tek bir işlemdir.

## <a name="intents-versus-entities"></a>Amaçlar ve varlıklar

Bir amaç, varlıkların, utterden çıkarılan veri parçaları olduğu sürece _tam_ olarak istenen sonucu elde edilir. Genellikle amaçları, istemci uygulamanın gerçekleşmesi gereken eylemlere bağlıdır. Varlıklar, bu eylemi gerçekleştirmek için gereken bilgileri sağlar. Bir programlama perspektifinden bir amaç bir yöntem çağrısını tetikleyecektir ve varlıklar bu yöntem çağrısında parametreler olarak kullanılır.

Bu söylenişi _bir amaç içermeli ve_ varlıkları _olabilir:_

`Buy an airline ticket from Seattle to Cairo`

Bu söylenişi tek bir amaç içerir:

* Düzlem bileti satın alma

Bu söylenişi birçok varlık _içerebilir_ :

* Seattle (Origin) ve Cairo (hedef) konumları
* Tek bir bilet miktarı

## <a name="entity-model-decomposition"></a>Varlık modeli ayrıştırma

LUıS yazma API 'Leri ile _model ayrıştırma_ 'yı destekler ve bir kavramı daha küçük parçalara ayırır. Bu, çeşitli parçaların oluşturulması ve tahmin edilebileceği şekilde modellerinizi güvenle oluşturmanıza olanak sağlar.

Model ayrıştırma aşağıdaki bölümlere sahiptir:

* [hedefleri](#intents-classify-utterances)
    * [özelliklerinde](#features)
* [makine öğrenimi varlıkları](reference-entity-machine-learned-entity.md)
    * alt varlıklar (ayrıca makine öğrenimi varlıkları)
        * [özelliklerinde](#features)
            * [tümcecik listesi](luis-concept-feature.md)
            * [normal ifadeler](reference-entity-regular-expression.md), [listeler](reference-entity-list.md)ve [önceden oluşturulmuş varlıklar](luis-reference-prebuilt-entities.md) gibi [makine öğrenimi olmayan varlıklar](luis-concept-feature.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>Özellikler

Bir [özellik](luis-concept-feature.md) , sistem engelinizdeki verilerin bir ayırt edici nitelik veya özniteliğidir. Machine Learning özellikleri, bir kavramı ayırt edecek şeyleri aramak için lu, önemli ipuçları verir. Bu kişiler, LUıN tarafından kullanılabilecek ancak zor kuralları olmayan bir ipuçlardır. Bu ipuçları, verileri bulmak için etiketlerle birlikte kullanılır.

## <a name="patterns"></a>Desenler

[Desenler](luis-concept-patterns.md) , birkaç söylenme çok benzer olduğunda doğruluğu artırmak için tasarlanmıştır. Bir desen, çok daha fazla sayıda daha fazla bilgi sağlamadan bir amaç için daha fazla doğruluk elde etmenizi sağlar.

## <a name="extending-the-app-at-runtime"></a>Uygulamayı çalışma zamanında genişletme

Uygulamanın şeması (modeller ve Özellikler) eğitilir ve tahmin uç noktasına yayımlandı. Tahmine karşı tahmin etmek için kullanıcının utterkiyle birlikte [yeni bilgileri](schema-change-prediction-runtime.md)tahmin uç noktasına geçirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* [Amaçları](luis-concept-intent.md) ve [varlıkları](luis-concept-entity-types.md)anlayın.
* [Özellikler](luis-concept-feature.md) hakkında daha fazla bilgi edinin