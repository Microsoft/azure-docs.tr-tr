---
title: Modellerle tasarım-LUSıS
titleSuffix: Azure Cognitive Services
description: Dil anlama çeşitli model türleri sağlar. Bazı modeller birden fazla şekilde kullanılabilir.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4cbca96ed95167615f3ff2876e27e546d08d92f5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507720"
---
# <a name="design-with-intent-and-entity-models"></a>Amaç ve varlık modelleriyle tasarım 

Dil anlama çeşitli model türleri sağlar. Bazı modeller birden fazla şekilde kullanılabilir. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 yazma, makine öğretimi kullanıyor

LUO, kişilerin bir makine için kavramları kolayca öğretebilmesini sağlar. Makine daha sonra, akıllı uygulamaları desteklemek için kullanılabilecek modeller (sınıflandırıcılar ve ayıklayıcıları gibi işlevsel farklılıklar) oluşturabilir. Bu, makine öğrenimi tarafından desteklenmektedir, makine öğrenimini anlamak için gerekli değildir. Bunun yerine, makine öğretmenler kavramların olumlu ve olumsuz örneklerini göstererek ve bir kavramın diğer ilgili kavramları kullanarak nasıl modellenmesi gerektiğini açıklayarak, kavramları halde ile iletişim kurar. Öğretmenler Ayrıca, tahmin hatalarını tanımlayarak ve düzelterek LUSıS 'nin modelini etkileşimli bir şekilde geliştirir. 

## <a name="v3-authoring-model-decomposition"></a>V3 yazma modeli ayrıştırma

LUıS, v3 yazma API 'Leri ile model _ayrıştırma_ 'yı destekler ve modeli daha küçük parçalara ayırır. Bu, çeşitli parçaların oluşturulması ve tahmin edilebileceği şekilde modellerinizi güvenle oluşturmanıza olanak sağlar.

Model ayrıştırma aşağıdaki bölümlere sahiptir:

* [hedefleri](#intents-classify-utterances)
    * Özellikler tarafından sunulan [tanımlayıcılar](#descriptors-are-features)
* [makine tarafından öğrenilen varlıklar](#machine-learned-entities)
    * alt [Bileşenler](#entity-subcomponents-help-extract-data) (ayrıca makine tarafından öğrenilen varlıklar)
        * Özellikler tarafından sunulan [tanımlayıcılar](#descriptors-are-features) 
        * (normal ifadeler ve listeler) gibi makine tarafından öğrenilen varlıklar tarafından belirtilen [kısıtlamalar](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 yazma modelleri

LUıS, v2 yazma API 'Leri ile bileşik varlıkları destekler. Bu, benzer model ayrıştırma sağlar ancak v3 model ayrıştırma ile aynı değildir. Önerilen model mimarisi, v3 yazma API 'Lerinde model ayrıştırma 'ye taşınmadır. 

## <a name="intents-classify-utterances"></a>Amaç sınıflandır

Amaç hakkında daha fazla bilgi almak için bir amaç sınıflandırmalar örnek olarak KULLANıLıR. Bir amaç içindeki örnek söyleyler, utterance 'in pozitif örnekleri olarak kullanılır. Bu aynı söyleyler, diğer tüm amaçlar için negatif örnekler olarak da kullanılır.

Bir kullanıcının, bir kitabı ve müşterinin sevkiyat adresine ihtiyacı olan bir uygulamayı sıralamasını belirlemesi gereken bir uygulamayı düşünün. Bu uygulamanın iki amacı vardır: `OrderBook` ve `ShippingLocation`.

Aşağıdaki söylenişi, `OrderBook` amacı için **olumlu bir örnektir** ve `ShippingLocation` ve `None` amaçları için negatif bir **örnektir** : 

`Buy the top-rated book on bot architecture.`

İyi tasarlanmış amaçlarda, örnek dıklarıyla birlikte yüksek bir amaç tahmininin sonucu. 

## <a name="entities-extract-data"></a>Varlıklar veri ayıklama

Bir varlık, söyleyden ayıklanmasını istediğiniz bir veri birimini temsil eder. 

### <a name="machine-learned-entities"></a>Makine tarafından öğrenilen varlıklar

Makine tarafından öğrenilen bir varlık, alt bileşenleri içeren ve makine tarafından öğrenilen varlıklar olan üst düzey bir varlıktır. 

**Makine tarafından öğrenilen bir varlık kullanın**:

* istemci uygulaması için alt bileşenler gerektiğinde
* makine öğrenimi algoritmasının varlıkları parçalara ayırma konusunda yardımcı olmak için

Her alt bileşen şunları içerebilir:

* alt bileşeni
* kısıtlamalar (normal ifade varlığı veya liste varlığı)
* tanımlayıcılar (tümcecik listesi gibi özellikler) 

Makine tarafından öğrenilen varlık bir örnek, düzlem bileti için bir sıradır. Kavramsal olarak, tarih, saat, bilgisayar lisansı sayısı, ilk sınıf veya Coach, kaynak konumu, hedef konumu ve yemek seçimi gibi çok daha küçük veri birimlerine sahip tek bir işlemdir.


### <a name="entity-subcomponents-help-extract-data"></a>Varlık alt bileşenleri veri ayıklamasına yardımcı olur

Alt bileşen, makine tarafından öğrenilen üst varlık içinde makine tarafından öğrenilen bir alt varlıktır. 

Alt **bileşeni kullanarak**şunları yapın:

* makine tarafından öğrenilen varlığın (üst varlık) parçalarını parçalara ayırın.

Aşağıda, her farklı veri parçasına sahip makine tarafından öğrenilen bir varlık temsil etmektedir:

* Seyahat sıralaması (makine tarafından öğrenilen varlık)
    * DateTime (önceden oluşturulmuş datetimeV2)
    * Konum (makine tarafından öğrenilen varlık)
        * Kaynak (`from`gibi bağlam aracılığıyla rol bulundu)
        * Hedef (rol, `to`gibi bir bağlamda bulundu)
    * Sepat (makine tarafından öğrenilen varlık)
        * Miktar (önceden oluşturulmuş sayı)
        * Kalite (tümcecik listesi tanımlayıcısı ile makine tarafından öğrenilen varlık)
    * Meals (makine tarafından öğrenilen varlık, liste varlığının kısıtlaması, yiyecek seçenekleri olarak)

Kaynak konumu ve hedef konum gibi bu verilerden bazılarının, büyük olasılıkla `from` ve `to`gibi bir ifade ile öğrenilmesi gerekir. Verilerin diğer bölümleri, tam dize eşleşmeleri (`Vegan`) veya önceden oluşturulmuş varlıklar (`Seattle` geographyV2 ve `Cairo`) ile ayıklanamaz. 

Seçtiğiniz modeller ve bunları nasıl yapılandırdığınız ile verilerin nasıl eşleştiğini ve ayıklanacağını tasarlayacaksınız.

### <a name="constraints-are-text-rules"></a>Kısıtlamalar metin kurallarıdır

Kısıtlama, normal ifade varlığı veya bir liste varlığı gibi makine tarafından öğrenilen bir varlık tarafından sunulan metin eşleştirme kuralıdır. Kısıtlama, tahmine göre sınırlamak ve istemci uygulaması için gereken varlık çözümünü sağlamak için tahmin sırasında uygulanır. Alt bileşeni yazarken bu kuralları tanımlarsınız. 

**Kısıtlama kullanın**:
* Ayıklanacak metnin tam olarak bilindiğinizde.

Kısıtlamalar şunlardır:

* [normal ifade](reference-entity-regular-expression.md) varlıkları
* varlıkları [Listele](reference-entity-list.md) 
* [önceden oluşturulmuş](luis-reference-prebuilt-entities.md) varlıklar

Uçak Bileti örneğine devam ettiğinizde, Havaalanı kodları tam metin eşleşmeleri için bir liste varlığı içinde olabilir. 

Havaalanı listesi için, Seattle için liste girişi, şehir adı, `Seattle` ve Seattle için eş anlamlılar, kapsayan kasaların ve şehirlerin yanı sıra Seattle için Havaalanı kodu içerir:

|`Seattle` listesi varlık eşanlamlıları|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

Havaalanı kodları için yalnızca 3 harfli kodları tanımak istiyorsanız, kısıtlama olarak bir normal ifade kullanın. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>Amaçlar ve varlıklar

Bir amaç, varlıkların, utterden çıkarılan veri parçaları olduğu sürece _tam_ olarak istenen sonucu elde edilir. Genellikle amaçları, istemci uygulamanın gerçekleştirmesi gereken eylemlere ve varlıkların bu eylemi gerçekleştirmek için gereken bilgilere bağlıdır. Bir programlama perspektifinden bir amaç bir yöntem çağrısını tetikleyecektir ve varlıklar bu yöntem çağrısında parametreler olarak kullanılır.

Bu söylenişi _bir amaç içermeli ve_ varlıkları _olabilir:_

`Buy a airline ticket from Seattle to Cairo`

Bu söylenişi tek bir amaç içerir:

* Düzlem bileti satın alma

Bu söylenişi birçok varlık _içerebilir_ :

* Seattle (Origin) ve Cairo (hedef) konumları
* Tek bir bilet miktarı

## <a name="descriptors-are-features"></a>Tanımlayıcılar özelliklerdir

Bir tanımlayıcı, bir modele, tümce listeleri ve varlıklar dahil olmak üzere eğitim zamanında uygulanan bir özelliktir. 

**Şunları yapmak istediğinizde bir tanımlayıcı kullanın**:

* tanımlayıcı tarafından tanımlanan sözcüklerin ve tümceciklerin önemini artırın
* LUSıS 'in tanımlayıcı için önermeye yönelik yeni metin veya tümcecikleri olması önerilir
* Eğitim verilerinde bir hatayı çözme

## <a name="next-steps"></a>Sonraki adımlar

* [Amaçları](luis-concept-intent.md) ve [varlıkları](luis-concept-entity-types.md)anlayın. 