---
title: Varlık türleri-LUSıS
description: Bir varlık, tahmin çalışma zamanında bir Kullanıcı noktasından verileri ayıklar. _İsteğe bağlı_, ikincil amaç, varlığı bir özellik olarak kullanarak amaç veya diğer varlıkların tahminini de artırır.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: 44cffecd653ec2ec748e73d01dc86a87cfcd7de9
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500337"
---
# <a name="entities-in-luis"></a>LUSıS içindeki varlıklar

Bir varlık, kullanıcının amacına uygun bir öğedir. Varlıklar, söyleyden ayıklanabilen verileri tanımlar ve kullanıcının gerekli eylemini tamamlaması için gereklidir. Örnek:

|İfade|Amaç tahmin edildi|Ayıklanan varlıklar|Açıklama|
|--|--|--|--|
|Merhaba nasılsın?|Karşılama|-|Ayıklanacak bir şey yok.|
|Küçük bir pizza sıralamak istiyorum|orderPizza| küçük | "Size" varlığı "küçük" olarak ayıklanır.|
|Yatak odası ışığını kapat|Kapatma| "yatak odası" | "Oda" varlığı "yatak odası" olarak ayıklanır.|
|4406 ile biten tasarruf hesabındaki dengeyi denetle|Çek bakiyesi| "tasarruf", "4406" | "accountType" varlığı "tasarruf" olarak ayıklanır ve "accountNumber" varlığı "4406" olarak ayıklanır.|
|New York 'a 3 bilet satın alın|buyTickets| "3", "New York" | "Bilet Scount" varlığı "3" olarak ayıklanır ve "hedef" varlığı "New York" olarak ayıklanır.|

Varlıklar isteğe bağlıdır, ancak önerilir. Uygulamanızdaki her kavram için varlık oluşturmanız gerekmez, yalnızca şu durumlar için:

* İstemci uygulaması için veri gerekiyor veya 
* Varlık, başka bir varlık veya amaç için bir ipucu veya sinyal görevi görür. Özellikler olarak varlıklar hakkında daha fazla bilgi edinmek için [varlıklara özellikler olarak](#entities-as-features)gidin.

## <a name="entity-types"></a>Varlık türleri

Bir varlık oluşturmak için, buna bir ad ve bir tür vermeniz gerekir. LUSıS 'de birçok varlık türü vardır. 

### <a name="list-entity"></a>Varlık listeleme

Liste varlığı, sabit ve kapalı bir ilgili sözcük kümesini eş anlamlılarıyla birlikte temsil eder. Birden çok eş anlamlı veya çeşitlemeyi tanımak ve bunlara yönelik normalleştirilmiş bir çıktıyı ayıklamak için liste varlıklarını kullanabilirsiniz. Geçerli listeye göre yeni kelimelerin önerilerini görmek için *öner* seçeneğini kullanın. 

Bir liste varlığı makine öğrenilmez, yani LUSıS 'in liste varlıkları için ek değerler bulamayacağını belirtir. LUO, yanıtta bir varlık olarak herhangi bir listedeki öğeyle eşleşen herhangi bir eşleşmeyi işaretler.

Liste varlıklarındaki eşleştirme, büyük/küçük harfe duyarlıdır ve ayıklanmak üzere tam eşleşme olması gerekmez. Liste varlığı eşleştirilirken normalleştirilmiş değerler de kullanılır. Örnek:

|Normalleştirilmiş değer|Eş Anlamlı Sözcükler|
|--|--|
|Küçük|SM, SML, küçük, en küçük|
|Orta|MD, MDM, normal, ortalama, orta|
|Büyük|LG, LRG, Big|

Daha fazla bilgi için bkz. [varlık başvurusunu listeleme makalesi](reference-entity-list.md) .

### <a name="regex-entity"></a>Regex varlığı

Normal ifade varlığı, sağladığınız normal ifade düzenine göre bir varlığı ayıklar. Büyük/küçük harf durumunu yoksayar ve kültürel türevini yoksayar. Normal ifade, yapılandırılmış metin veya belirli bir biçimde beklenen, önceden tanımlanmış alfasayısal değerler dizisi için idealdir. Örnek:

|Varlık|Normal ifade|Örnek|
|--|--|--|
|Uçuş numarası|Uçuş [A-Z] {2} [0-9]{4}| 1234 olarak uçuş|
|Kredi Kartı Numarası|[0-9]{16}|5478789865437632|

Daha fazla bilgi için [Regex Entities başvuru makalesine](reference-entity-regular-expression.md) bakın.

### <a name="prebuilt-entity"></a>Önceden Oluşturulmuş Varlık

LUO, ad, tarih, sayı ve para birimi gibi ortak veri türlerini tanınması için önceden oluşturulmuş varlıklar kümesi sunar.  Önceden oluşturulmuş varlıkların davranışı sabittir. Önceden oluşturulmuş varlık desteği, LUO uygulamasının kültürüne göre farklılık gösterir. Örnek:

|Önceden oluşturulmuş varlık|Örnek değer|
|--|--|
|Kişi adı|James, Bill, Tom|
|DatetimeV2|2019-05-02, 2 Mayıs, 10:00 2 Mayıs 'ta Mayıs 2019|

Daha fazla bilgi için [önceden oluşturulmuş varlıklar başvuru makalesine](./luis-reference-prebuilt-entities.md) bakın.

### <a name="patternany-entity"></a>Model. any varlığı

Bir model. Herhangi bir varlık, varlığın nerede başladığını ve bittiğini işaretlemek için yalnızca bir desen şablonunda kullanılan değişken uzunluklu bir yer tutucudur. Bu, belirli bir kuralı veya kalıbı izler ve sabit bir sözlü yapıda cümleler için en iyi şekilde kullanılır. Örnek:

|Örnek konuşma|Desen|Varlık|
|--|--|--|
|Zaten bir burger olabilir mi?|{Yemek} [Lütfen] [?] olabilir| Burger
|Bir pizza sahip olabilir miyim?|{Yemek} [Lütfen] [?] olabilir| Pizza
|Harika Gatsby 'yi nereden bulabilirim?|{BookName} nerede bulabilirim?| Harika Gatsby|

Daha fazla bilgi için bkz [. herhangi bir varlık başvuru makalesi](./reference-entity-pattern-any.md) .

### <a name="machine-learned-ml-entity"></a>Makine öğrenilmiş (ML) varlık

Makine tarafından öğrenilen varlık, etiketlenmiş örneklere göre varlıkları ayıklamak için bağlamını kullanır. LUSıS uygulamaları oluşturmak için tercih edilen varlıktır. Makine öğrenimi algoritmalarını kullanır ve etiketlemeye, uygulamanıza başarıyla uyarlanmasını gerektirir. Her zaman iyi biçimlendirilmemiş ancak aynı anlamı olan verileri tanımlamak için bir ML varlığı kullanın. 

|Örnek konuşma|Ayıklanan *ürün* varlığı|
|--|--|
|Bir kitap satın almak istiyorum.|Kitabın|
|Bu şokaları şu şekilde alabilir miyim?|ayakkabılar|
|Bu kısaltmayı sepetme ekleyin.|şortu|

Makine tarafından öğrenilen varlıklar hakkında [buradan](./reference-entity-machine-learned-entity.md)daha fazla bilgi edinebilirsiniz.

Daha fazla bilgi için [makine öğrenilen varlık başvurusu makalesine](./reference-entity-pattern-any.md) bakın.

#### <a name="ml-entity-with-structure"></a>Yapıya sahip ML varlığı

Bir ML varlığı, her birinin kendi özelliklerine sahip olabilen daha küçük bir alt varlıklardan oluşabilir. Örneğin, *Adres* aşağıdaki yapıya sahip olabilir:

* Adres: 4567 Ana cadde, NY, 98052, USA
    * Bina numarası: 4567
    * Cadde adı: Ana cadde
    * Durum: NY
    * Posta kodu: 98052
    * Ülke: ABD


### <a name="building-effective-ml-entities"></a>Etkili ML varlıkları oluşturma

Makine tarafından öğrenilen varlıkları etkin bir şekilde derlemek için aşağıdaki en iyi yöntemleri izleyin:

* Alt varlıklar içeren bir makine öğrenmiş bir varlığınız varsa, varlığın ve alt varlıkların farklı siparişlerinin ve varyantlarını etiketlendirdikleri şekilde göründüğünden emin olun. Etiketlenmiş örnek, tüm geçerli formları içermeli ve görüntülenen ve olmayan varlıkları ve ayrıca utterance içinde de yeniden sipariş etmelidir.

* Varlıkları çok sabit bir küme ile fazla sığdırmayı önleyin. Model iyi genelleştirilmeyen ve makine öğrenimi modellerinde yaygın bir sorun olduğunda aşırı yerleştirme oluşur. Bu, uygulamanın yeni örnek türlerinde yeterli şekilde çalışmamayacağı anlamına gelir. Bu durumda, uygulamanın sağladığınız sınırlı örneklerden daha fazla Genelleştirme yapabilmesi için etiketlenmiş örnek çeşitlerini de beklemelisiniz.

* Etiketlemenin amaç genelinde tutarlı olması gerekir. Bu, bu varlığı içeren *hiçbir* amaç içinde sağladığınız diğer şartları da içerir. Aksi takdirde model sıraları etkin bir şekilde belirleyemeyecektir.

## <a name="entities-as-features"></a>Özellik olarak varlıklar

Varlıkların başka bir önemli işlevi, bunları başka amaçlar veya varlıklar için özellikler olarak veya ayırt etmek için kullanılır.

### <a name="entities-as-features-for-intents"></a>Amaçlar için özellik olarak varlıklar

Varlıkları bir amaç için sinyal olarak kullanabilirsiniz. Örneğin, söylenişi 'teki belirli bir varlığın varlığı, hangi amacın altına düştüğünü ayırt edebilir.

|Örnek konuşma|Varlık|Amaç|
|--|--|--|
|*New York 'a bir fimi* kitabı koyun.|Şehir|Kitap kolu|
|*Ana konferans odasını* bana kitap.|Yı|Odayı ayır|

### <a name="entities-as-feature-for-entities"></a>Varlıklar için özellik olarak varlıklar

Varlıkları diğer varlıkların varlığına ait bir gösterge olarak da kullanabilirsiniz. Bunun yaygın bir örneği, başka bir ML varlığının özelliği olarak önceden oluşturulmuş bir varlık kullanmaktır.
Bir uçuş yeri sistemi oluşturuyorsanız ve söyleyiniz "Cairo 'dan Istanbul 'a bir uçuş olarak kitap" gibi görünüyorsa, ML varlıkları olarak *kaynak şehri* ve *hedef şehriniz* olur. İyi bir uygulama, önceden oluşturulmuş `GeographyV2` varlığı her iki varlık için bir özellik olarak kullanmaktır.

Daha fazla bilgi için [GeographyV2 Entities başvuru makalesine](./luis-reference-prebuilt-geographyv2.md) bakın.

Varlıkları, diğer varlıklar için gereken özellikler olarak da kullanabilirsiniz. Bu, ayıklanan varlıkların çözümüne yardımcı olur. Örneğin, bir pizza sıralama uygulaması oluşturuyorsanız ve bir `Size` ml varlığınız varsa, `SizeList` liste varlığı oluşturabilir ve bunu varlık için gerekli bir özellik olarak kullanabilirsiniz `Size` . Uygulamanız, utterance 'ten ayıklanan varlık olarak normalleştirilmiş değeri döndürür. 

Daha fazla bilgi için bkz. [Özellikler](luis-concept-feature.md) ve önceden [oluşturulmuş varlıklar](./luis-reference-prebuilt-entities.md) , külünüzle sunulan önceden oluşturulmuş varlık çözümlemesi hakkında daha fazla bilgi edinin. 


## <a name="entity-prediction-status-and-errors"></a>Varlık tahmin durumu ve hataları

CSIS portalı, varlık için etiketlediğiniz varlıktan farklı bir varlık tahmine sahip olduğunda aşağıdaki gibi görünür. Bu farklı puan, geçerli eğitilen modele dayalıdır. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="Lua portalı, varlığın örnek bir değer için seçtiğiniz varlıktan farklı bir varlık tahmini ne zaman olduğunu gösterir":::

Hataya neden olan metin, örnek söylenişi içinde vurgulanır ve örnek söylenişi çizgisi, kırmızı bir üçgen olarak gösterildiği gibi, sağa doğru bir hata göstergesi içerir. 

Varlık hatalarını çözümlemek için aşağıdakilerden birini veya daha fazlasını deneyin:

* Vurgulanan metin yanlış etiketlendi. Düzeltmek için etiketi gözden geçirin, düzeltin ve uygulamayı yeniden eğitme. 
* Varlığın kavramını belirlemesine yardımcı olmak için varlık için bir [özellik](luis-concept-feature.md) oluşturun.
* Varlıkla daha fazla [örnek](luis-concept-utterance.md) ekleme ve etiket ekleyin.
* Tahmin uç noktasında alınan her türlü, varlığın kavramını belirlemenize yardımcı olabilecek, [etkin öğrenme önerilerini gözden geçirin](luis-concept-review-endpoint-utterances.md) .


## <a name="next-steps"></a>Sonraki adımlar

* İyi örnek [araslar](luis-concept-utterance.md)hakkında bilgi edinin.
* LUSıS uygulamanıza varlık ekleme hakkında daha fazla bilgi edinmek için bkz. [varlık ekleme](luis-how-to-add-entities.md) .
* LUSıS [uygulama sınırları](./luis-limits.md)hakkında daha fazla bilgi edinin. 
* Makine öğrenimi varlığı kullanarak bir noktadan yapılandırılmış verilerin nasıl ayıklanacağını öğrenmek için bir [öğretici](tutorial-machine-learned-entity.md) kullanın.
