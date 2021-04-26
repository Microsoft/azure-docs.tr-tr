---
title: LUSıS ile makine öğrenimi özellikleri
description: Etiketlemek veya sınıflandırmak istediğiniz girişin nasıl tanınılacağı hakkında ipuçları sağlamak için bir dil modeline özellikler ekleyin.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: da85abdff3d1022659f2d4e83fd14c5ae6003fc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100546066"
---
# <a name="machine-learning-features"></a>Makine öğrenimi özellikleri

Machine Learning 'de bir *özellik*,   sistem engellerinizin ve öğrendiği verilerin bir ayırt edici nitelik veya özniteliğidir.

Makine öğrenimi özellikleri, bir kavramı ayırt eden şeyleri nerede aramak için lu, önemli ipuçları verir. Bu kişiler, LUıN kullanabileceği ipuçlarıyla, ancak zor kurallar değildir. LUO, verileri bulmak için bu ipuçlarını etiketle birlikte kullanır.

Bir özellik f (x) = y gibi bir işlev olarak açıklanabilir. Bu örnekte, özelliği, nitelik ayırt etmek için nereye bakacağınızı söyler. Şemanızı oluşturmaya yardımcı olması için bu bilgileri kullanın.

## <a name="types-of-features"></a>Özellik türleri

Özellikler, şema tasarımınızın gerekli bir parçasıdır. LUO, hem tümcecik listelerini hem de modelleri özellikler olarak destekler:

* Tümcecik listesi özelliği
* Özellik olarak model (amaç veya varlık)

## <a name="find-features-in-your-example-utterances"></a>Örnek aradıklarınızın özelliklerini bulun

LUSıS, dil tabanlı bir uygulama olduğundan, Özellikler metin tabanlıdır. Ayırmak istediğiniz nitelik belirten metni seçin. LUSıS için en küçük birim *belirtecidir*. Ingilizce dil için, belirteç boşluk veya noktalama işareti olmayan harflerin ve sayıların bitişik bir yayılımı.

Boşluklar ve noktalama belirteçleri belirteç olmadığından, özellik olarak kullanabileceğiniz metin ipuçlarına odaklanın. Sözcüklerin çeşitlemelerini dahil etmeyi unutmayın, örneğin:

* çoğul formlar
* fiil (ler)
* kısaltmaları
* Yazım ve yazım hataları

Metnin nitelik ayırt ettiğinden, şunları yapmak için şunu yapın:

* Tam bir sözcük veya tümcecik eşleştir: varlığa veya amaca bir özellik olarak bir normal ifade varlığı veya bir liste varlığı eklemeyi düşünün.
* Tarih, saat veya kişinin adı gibi bilinen bir kavramı eşleştirin: varlık veya amaç için bir özellik olarak önceden oluşturulmuş bir varlık kullanın.
* Zamana göre yeni örnekler öğrenin: varlık veya amaç için bir özellik olarak kavramın bazı örneklerinin bir tümcecik listesini kullanın.

## <a name="create-a-phrase-list-for-a-concept"></a>Kavram için tümcecik listesi oluşturma

Tümcecik listesi, kavram tanımlayan sözcüklerin veya tümceciklerin listesidir. Tümcecik listesi, belirteç düzeyinde büyük/küçük harfe duyarsız eşleşme olarak uygulanır.

Tümcecik listesi eklerken, özelliği **[genel](#global-features)** olarak ayarlayabilirsiniz. Genel bir özellik tüm uygulama için geçerlidir.

### <a name="when-to-use-a-phrase-list"></a>Tümcecik listesi ne zaman kullanılır?

Bu kavram için yeni öğeleri genelleştirmek ve tanımlamak için LUSıS uygulamanız gerektiğinde bir tümcecik listesi kullanın. Tümcecik listeleri, etki alanına özgü sözlük gibidir. Amaçlar ve varlıklar için anlama kalitesini geliştirir.

### <a name="how-to-use-a-phrase-list"></a>Tümcecik listesi kullanma

Bir tümcecik listesi ile, Lua benzer öğeleri belirlemek için bağlam ve genelleştirir, ancak tam metin eşleşmesi değildir. Tümcecik listesini kullanmak için şu adımları izleyin:

1. Makine öğrenimi varlığı ile başlayın:
    1. Örnek bir parametre ekleyin.
    1. Makine öğrenimi varlığı ile etiketleyin.
1. Tümcecik listesi ekleyin:
    1. Benzer anlamlara sahip sözcükler ekleyin. Olası her sözcük veya tümceciği eklemeyin. Bunun yerine, bir seferde birkaç sözcük veya tümce ekleyin. Sonra yeniden eğitme ve yayımlama.
    1. Önerilen kelimeleri gözden geçirin ve ekleyin.

### <a name="a-typical-scenario-for-a-phrase-list"></a>Tümcecik listesi için tipik bir senaryo

Tümcecik listesi için tipik bir senaryo, belirli bir fikrle ilgili kelimeleri iyileştirmeniz olur.

Tıbbi terimler, önemini artırmak için bir tümcecik listesi gerektirebilecek kelimelerin iyi bir örneğidir. Bu şartlara belirli fiziksel, kimyasal, bir veya soyut anlam bulunabilir. LUO, konu etki alanınız için tümcecik listesi olmadan bu koşulların önemli olduğunu bilmez.

Tıp şartlarını ayıklamak için:

1. Bu söyleyler dahilinde örnek oluşturma ve tıbbi terimleri etiketleme.
2. Konu etki alanında yer alan koşullara örnek olarak bir tümcecik listesi oluşturun. Bu tümcecik listesi, etiketlediğiniz gerçek terimi ve aynı kavramı tanımlayan diğer terimleri içermelidir.
3. Tümcecik listesini, tümcecik listesinde kullanılan kavramı çıkaran varlığa veya alt varlığa ekleyin. En yaygın senaryo, makine öğrenimi varlığının bir bileşenidir (alt öğesidir). Tümcecik listesinin tüm amaçlar veya varlıklar üzerinde uygulanması gerekiyorsa, tümcecik listesini genel ifade listesi olarak işaretleyin. **Enabledforallmodeller** bayrağı, bu model kapsamını API 'de denetler.

### <a name="token-matches-for-a-phrase-list"></a>Tümcecik listesi için belirteç eşleşmeleri

Tümcecik listesi her zaman belirteç düzeyinde geçerlidir. Aşağıdaki tabloda, **Ann** sözcüğünün bulunduğu bir tümcecik listesinin söz konusu sırada aynı karakterlerin çeşitlemelerini nasıl uyguladığı gösterilmektedir.


| **Ann** 'ın belirteç çeşitlemesi | Belirteç bulunduğunda tümcecik listesi eşleşiyor |
|--------------------------|---------------------------------------|
| **N**<br>**n**<br>           | Evet-belirteç **Ann**                  |
| **Ann 'ın**                    | Evet-belirteç **Ann**                  |
| **Gamze**                     | Hiçbir belirteç **anne** değildir                  |

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## <a name="a-model-as-a-feature-helps-another-model"></a>Özellik olarak bir model başka bir modele yardımcı olur

Başka bir modele (amaç veya varlık) bir özellik olarak model (amaç veya varlık) ekleyebilirsiniz. Bir özellik olarak var olan bir amacı veya varlığı ekleyerek, etiketli örnekleri olan iyi tanımlanmış bir kavram ekliyoruz.

Bir model özellik olarak eklendiğinde, özelliği şu şekilde ayarlayabilirsiniz:
* **[Gerekli](#required-features)**. Modelin tahmin uç noktasından döndürülmesi için gerekli bir özellik bulunması gerekir.
* **[Genel](#global-features)**. Genel bir özellik tüm uygulama için geçerlidir.

### <a name="when-to-use-an-entity-as-a-feature-to-an-intent"></a>Bir varlık bir özellik olarak ne zaman kullanılır?

Bu varlığın algılanması amaç için önemli olduğunda amaca bir özellik olarak bir varlık ekleyin.

Örneğin, amaç, **Bookuçuş** gibi bir uçuş için kayıt yapmak için ise ve varlık bilet bilgileri (örneğin, koltuk, kaynak ve hedef sayısı gibi) ise, Bilet-bilgi varlığını bulmak, **bookuçuş** hedefini tahmin etmek için önemli bir ağırlık içermelidir.

### <a name="when-to-use-an-entity-as-a-feature-to-another-entity"></a>Bir varlığın başka bir varlığa özellik olarak ne zaman kullanılacağı

Varlık (a), varlık (B) için önemli olduğunda bu varlığın (A) algılanabilmesi durumunda başka bir varlığa (B) bir özellik olarak eklenmelidir.

Örneğin, bir sevkiyat adresi varlığı sokak adresi bir alt varlık içinde bulunuyorsa, sokak adresi alt varlığı bulma işlemi, sevkiyat adresi varlığı için tahmine göre önemli bir ağırlık ekler.

* Sevkiyat Adresi (makine öğrenimi varlığı):

    * Cadde numarası (alt varlık)
    * Sokak adresi (alt varlık)
    * Şehir (alt varlık)
    * Eyalet veya Il (alt varlık)
    * Ülke/bölge (alt varlık)
    * Posta kodu (alt varlık)

## <a name="nested-subentities-with-features"></a>Özelliklerle iç içe geçmiş alt varlıklar

Makine öğrenimi alt varlığı, üst varlığa bir kavram olduğunu gösterir. Üst öğe başka bir alt varlık veya en üst varlık olabilir. Alt varlığın değeri, üst öğesi için bir özellik görevi görür.

Bir alt varlık bir özellik olarak hem bir ifade listesi hem de model (başka bir varlık) içerebilir.

Alt varlık bir tümcecik listesine sahip olduğunda, kavram sözlüğünü artırır, ancak tahmine yönelik JSON yanıtına hiçbir bilgi eklemez.

Alt varlık başka bir varlık özelliğine sahip olduğunda, JSON yanıtı o varlığın ayıklanan verilerini içerir.


## <a name="required-features"></a>Gerekli özellikler

Modelin tahmin uç noktasından döndürülmesi için gerekli bir özellik bulunması gerekir. Gelen verilerinizin özelliği ile eşleşmesi gerektiğini bildiğiniz durumlarda gerekli bir özelliği kullanın.

Söylenişi metni gerekli özellik ile eşleşmiyorsa ayıklanmaz.

Gerekli bir özellik makine öğrenimi olmayan bir varlık kullanır:

* Normal ifade varlığı
* Liste varlığı
* Önceden oluşturulmuş varlık

Modelinize ilişkin verilerde emin değilseniz, özelliği gereken şekilde ayarlayın. Gerekli bir özellik bulunmazsa hiçbir şey döndürmez.

Sevkiyat Adresi örneğine devam ediliyor:

Sevkiyat Adresi (makine tarafından öğrenilen varlık)

 * Cadde numarası (alt varlık)
 * Sokak adresi (alt varlık)
 * Cadde adı (alt varlık)
 * Şehir (alt varlık)
 * Eyalet veya Il (alt varlık)
 * Ülke/bölge (alt varlık)
 * Posta kodu (alt varlık)

### <a name="required-feature-using-prebuilt-entities"></a>Önceden oluşturulmuş varlıklar kullanılarak gerekli özellik

Şehir, eyalet ve ülke/bölge gibi önceden oluşturulmuş varlıklar genellikle kapalı bir liste kümesidir ve bu, zaman içinde çok değiştirmeyeceği anlamına gelir. Bu varlıklar ilgili önerilen özelliklere sahip olabilir ve bu özellikler gerekli olarak işaretlenebilir. Ancak, `isRequired` bayrağı yalnızca atandığı varlıkla ilgilidir ve hiyerarşiyi etkilemez. Önceden oluşturulmuş alt varlık özelliği bulunamazsa, bu, üst varlığın algılanmasını ve dönmesini etkilemez.

Gerekli bir özelliğe örnek olarak, adresleri algılamak istediğinizi göz önünde bulundurun. Cadde numarasını bir gereksinim yapmayı düşünebilirsiniz. Bu, bir kullanıcının "1 Microsoft Way" veya "bir Microsoft Way" girmesini sağlar ve her ikisi de cadde numarası alt varlığı için "1" rakamı olarak çözümlenir. Daha fazla bilgi için [önceden oluşturulmuş varlık ](luis-reference-prebuilt-entities.md) makalesine bakın.

### <a name="required-feature-using-list-entities"></a>Liste varlıklarını kullanan gerekli özellik

Bir [liste varlığı](reference-entity-list.md) , kendi eş anlamlılarıyla birlikte kurallı adların bir listesi olarak kullanılır. Gerekli bir özellik olarak, söylenişi kurallı adı ya da bir eş anlamlıyı içermiyorsa, varlık tahmin uç noktasının bir parçası olarak döndürülmez.

Şirketinizin yalnızca sınırlı bir ülke/bölge kümesine sevk edildiğini varsayın. Müşterinizin ülkeye/bölgeye başvurması için çeşitli yollar içeren bir liste varlığı oluşturabilirsiniz. LUSıS, utterance 'in metni içinde tam bir eşleşme bulamazsa, bu durumda varlık (liste varlığının gerekli özelliğine sahip olan) tahminde döndürülmez.

|Kurallı ad|Eş Anlamlı Sözcükler|
|--|--|
|Birleşik Devletler|ABD<br>U. S. A<br>ABD<br>ABD<br>0|

Sohbet bot gibi bir istemci uygulaması, yardım almak için bir takip sorusu isteyebilir. Bu, müşterinin ülke/bölge seçiminin sınırlı ve *gerekli* olduğunu anlamalarına yardımcı olur.

### <a name="required-feature-using-regular-expression-entities"></a>Normal ifade varlıklarını kullanan gerekli özellik

Gerekli özellik olarak kullanılan bir [normal ifade varlığı](reference-entity-regular-expression.md) zengin metin eşleştirme özellikleri sağlar.

Sevkiyat Adresi örneğinde, ülke/bölge posta kodlarının sözdizimi kurallarını yakalayan bir normal ifade oluşturabilirsiniz.

## <a name="global-features"></a>Genel Özellikler

En yaygın kullanım, belirli bir modele bir özellik uygulamak olsa da, Özelliği uygulamanızın tamamına uygulamak için **genel bir özellik** olarak yapılandırabilirsiniz.

Genel bir özellik için en yaygın kullanım, uygulamaya ek bir sözlük eklemektir. Örneğin, müşterileriniz birincil bir dil kullanıyorsa, ancak aynı utde başka bir dil kullanabiliyor olması beklendiğinde, ikincil dilden sözcükler içeren bir özellik ekleyebilirsiniz.

Kullanıcı herhangi bir amaç veya varlık genelinde ikincil dili kullanmayı beklediği için, ikincil dilden sözcük listesini ifade listesine ekleyin. Tümcecik listesini genel özellik olarak yapılandırın.

## <a name="combine-features-for-added-benefit"></a>Eklenen avantaj için özellikleri birleştirin

Bir nitelik veya kavramı anlatmak için birden fazla özellik kullanabilirsiniz. Ortak eşleştirme şunu kullanmaktır:

* Tümcecik listesi özelliği: birden çok ifade listesini aynı modelde özellikler olarak kullanabilirsiniz.
* Özellik olarak model: [önceden oluşturulmuş varlık](luis-reference-prebuilt-entities.md), [normal ifade varlığı](reference-entity-regular-expression.md), [liste varlığı](reference-entity-list.md). 

### <a name="example-ticket-booking-entity-features-for-a-travel-app"></a>Örnek: bir seyahat uygulaması için bilet ayırma varlık özellikleri  

Temel bir örnek olarak, uçuş rezervasyonu _hedefi_ ve bilet ayırma _varlığı_ ile bir uçuş için bir uygulama düşünün. Bilet kayıt varlığı, bir uçak anahtarını bir ayırma sisteminde kitaba eklemek için bilgileri yakalar. 

Bilet defterine yönelik makine öğrenme varlığı, kaynak ve hedef yakalayan iki alt varlık içerir. Özelliklerin en üst düzey varlığa değil her bir alt varlığa eklenmesi gerekir.

:::image type="content" source="media/luis-concept-features/ticket-booking-entity.png" alt-text="Bilet kayıt varlığı şeması":::

Bilet ayırma varlığı, _kaynak_ ve _hedef_ dahil olmak üzere alt varlıklar içeren bir makine öğrenimi varlıklarıdır. Bu alt Varlıklar her ikisi de coğrafi bir konum gösterir. Konumların ayıklanmasına yardımcı olmak ve _kaynak_ ile _hedef_ arasında ayrım yapmak için her bir alt varlığın özelliği olmalıdır.

|Tür|Kaynak alt varlık |Hedef alt varlık|
|--|--|--|
|Özellik olarak model oluşturma|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) önceden oluşturulmuş varlık|[geographyV2](luis-reference-prebuilt-geographyv2.md?tabs=V3) önceden oluşturulmuş varlık|
|Tümcecik listesi|**Kaynak sözcükler**: `start at` , `begin from` , `leave`|**Hedef sözcükler**: `to` , `arrive` , `land at` , `go` , `going` , `stay` , `heading`|
|Tümcecik listesi|Havaalanı kodları-hem kaynak hem de hedef için aynı liste|Havaalanı kodları-hem kaynak hem de hedef için aynı liste|
|Tümcecik listesi|Havaalanı adları-hem kaynak hem de hedef için aynı liste|Havaalanı kodları-hem kaynak hem de hedef için aynı liste|

Kişilerin Havaalanı kodlarını ve Havaalanı adlarını kullandığını düşünüyorsanız, LUO 'dan her iki tümcecik türünü kullanan tümcecik listeleri olmalıdır. Havaalanı kodları bir sohbet botu girilen metinle daha yaygın olabilir. Havaalanı adları, konuşma özellikli bir sohbet botu gibi konuşulan konuşmayla daha yaygın olabilir.

Özelliklerin eşleşme ayrıntıları yalnızca modeller için değil, yalnızca modeller tahmin JSON ' ta döndürüldüğünden, tümcecik listeleri için döndürülür.

#### <a name="ticket-booking-labeling-in-the-intent"></a>Amaç içinde bilet ayırma etiketleme

Makine öğrenimi varlığı oluşturduktan sonra, bir amaca örnek eklemek ve ana varlığı ve tüm alt varlıkları etiketlemek gerekir.

Bilet kayıt örneği için, örnekteki örnekleri `TicketBooking` varlıkla ve metinde bulunan tüm alt varlıklarla birlikte etiketleyin.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity.png" alt-text="Etiket örnek utbotları":::

### <a name="example-pizza-ordering-app"></a>Örnek: Pizza sıralama uygulaması

İkinci bir örnek için, bir pizza siparişi veren bir uygulama düşünün. Bu, birisinin, bir kişi tarafından sipariş olduğu pizza ait ayrıntılar dahil olmak üzere pizza siparişleri alır. Sıra işlemenin tamamlanabilmesi için, pizza 'nin her ayrıntısı mümkünse ayıklanmalıdır.

Bu örnekteki makine öğrenimi varlığı, iç içe geçmiş alt varlıklar, tümcecik listeleri, önceden oluşturulmuş varlıklar ve özel varlıklarla daha karmaşıktır.

:::image type="content" source="media/luis-concept-features/pizza-order-entity.png" alt-text="Pizza sırası varlık şeması":::

Bu örnek, alt varlık düzeyinde ve alt varlık düzeyinin alt kısmındaki özellikleri kullanır. Bu düzey, özellik olarak hangi tür ifade listesi veya model, varlık tasarımınızın önemli bir parçasıdır.

Alt varlıklarda varlığı algılamaya yardımcı olan özellikler olarak birçok ifade listesi bulunabilir, ancak her alt varlık bir özellik olarak yalnızca bir model içerir. Bu [pizza](https://github.com/Azure/pizza_luis_bot/blob/master/CognitiveModels/MicrosoftPizza.json)uygulamasında bu modeller birincil olarak listeler.

:::image type="content" source="media/luis-concept-features/intent-example-utterances-machine-learning-entity-pizza.png" alt-text="Etiketlenmiş örnek utbotları ile pizza sırası amacı":::

Doğru etiketlenmiş örnek, varlıkların iç içe geçmiş olduğunu göstermek için bir şekilde görüntülenir. 


## <a name="best-practices"></a>En iyi uygulamalar

[En iyi yöntemleri](luis-concept-best-practices.md)öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

* Uygulama modellerinizi tahmin çalışma zamanına göre [genişletin](schema-change-prediction-runtime.md) .
* LUSıS uygulamanıza özellikler ekleme hakkında daha fazla bilgi edinmek için bkz. [özellik ekleme](luis-how-to-add-features.md) .
