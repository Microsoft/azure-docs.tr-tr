---
title: Nasıl ve nasıl kullanılacağı-kişiselleştirici
description: Kişiselleştirici, uygulamanızın, deneyimi daha iyi hale getirmek, daha iyi iş sonuçları elde etmek veya üretkenliği artırmak için görüntülenecek doğru öğe, eylem veya ürünü seçebileceğiniz herhangi bir durumda uygulanabilir.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 87179950c33a6facb59ce499aa0ae393e53fb37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91777233"
---
# <a name="where-and-how-to-use-personalizer"></a>Kişiselleştiriciye nerede ve nasıl kullanılacağı

Uygulamanızın, deneyimi daha iyi hale getirmek, daha iyi iş sonuçları elde etmek veya üretkenliği artırmak için, görüntülenecek doğru eylemi (içeriği) seçmek üzere görüntülemesi gereken herhangi bir durumda kişiselleştirici kullanın.

Kişiselleştirici kullanıcıyı hangi eylemin (içerik) göstermek için pekiştirmeye dayalı öğrenimini kullanır. Seçim, hizmete gönderilen verilerin miktarına, kalitesine ve dağıtımına bağlı olarak büyük ölçüde farklılık gösterebilir.

## <a name="example-use-cases-for-personalizer"></a>Kişiselleştirici için örnek kullanım örnekleri

* **Amaç açıklaması & belirsizliği ortadan** kaldırma: kişiselleştirilmiş bir seçenek sunarak kullanıcılarınızın amacı net bir deneyimle daha iyi bir deneyim sağlanmasına yardımcı olur.
* Menüler için **varsayılan öneriler** & seçenekler: bot 'ın, bir ımpersonal menüsü veya alternatifler listesi sunmak yerine, ilk adımla en olası öğeyi kişiselleştirilmiş bir şekilde önermesini sağlayabilirsiniz.
* **Bot nitelikleri & tonu**: ton, ayrıntı ve yazma stili değişebilen botlar için, bu nitelikleri değiştirmeyi göz önünde bulundurun.
* **Bildirim & uyarı içeriği**: kullanıcılara daha fazla etkileşim kurmak için uyarı için hangi metnin kullanılacağına karar verin.
* **Bildirim & uyarı zamanlaması**: kullanıcılara daha fazla gitmek için ne zaman bildirim gönderileceğini kişiselleştirilmiş olarak öğreniyor.


## <a name="expectations-required-to-use-personalizer"></a>Kişiselleştirici kullanmak için gereken beklentiler

Aşağıdaki yönergeleri karşıladığınız veya uygulayabileceğiniz durumlarda kişiselleştirici uygulayabilirsiniz.

|Yönerge|Açıklama|
|--|--|
|İş hedefi|Uygulamanız için bir iş veya kullanılabilirlik hedefi vardır.|
|Content|Uygulamanızda, kullanıcıların bu hedefi iyileştirebilecekleri bağlamsal kararı veren bir yerdir.|
|İçerik miktarı|Çağrı başına sıralama için 50 taneden az eylem var.|
|Verileri topla|En iyi seçenek, toplu Kullanıcı davranışından ve toplam ödül puanından öğrenilmesi gerekir.|
|Ahlak kullanım|Kişiselleştirme için makine öğrenmesinin kullanımı, sizin belirlediğiniz [kullanım kılavuzları](ethics-responsible-use.md) ve seçimlerdir.
|En iyi tek seçenek|Bağlamsal karar, sınırlı bir seçenek kümesinden en iyi seçenek (eylem) sıralaması olarak ifade edilebilir.|
|Puanlanmış sonuç|Uygulamanız için ne kadar uygun olan dereceli seçim, Kullanıcı davranışının bazı yönlerinin ölçüleceği ve bir _[ödül puanında](concept-rewards.md)_ ifade edilebilir.|
|İlgili zamanlama|Ödül puanı, çok fazla sayıda bağlama veya dış etken getirmez. Deneme süresi, hala uygun olduğu sürece, yeniden elde edilen puanınızın hesaplanabileceği kadar düşüktür.|
|Yeterli bağlam özellikleri|Doğru seçim yapmaya yardımcı olacağını düşündüğünüz ve kullanıcıya özel tanımlanabilir bilgileri içermeyen en az 5 [özellik](concepts-features.md) listesi olarak derece kapsamını ifade edebilirsiniz.|
|Yeterli eylem özellikleri|Her içerik seçimi, eylem ve, kişiselleştirmenin doğru seçimi yapmasını sağlayacak en az 5 [özellik](concepts-features.md) listesi olarak, _eylem_ hakkında bilgi sahibi olursunuz.|
|Günlük veriler|Sorun zaman içinde Drifts, en iyi kişiselleştirmeye devam etmek için yeterli olay vardır (haber veya şekilde Tercihler gibi). Kişiselleştirici gerçek dünyada sürekli değişikliğe uyum sağlar, ancak yeni desenleri bulma ve kapatma hakkında bilgi almak için yeterli olay ve veri yoksa sonuçlar en uygun olmayacaktır. Genellikle yeterince gerçekleşen bir kullanım durumu seçmeniz gerekir. Günde en az 500 kez gerçekleşen kullanım örneklerini aramak için göz önünde bulundurun.|
|Geçmiş verileri|Uygulamanız, en az 100.000 etkileşimin geçmişini biriktirmek için verileri yeterince uzun bir süre koruyabilir. Bu, Kişiselleştiriciye çevrimdışı değerlendirmeler ve ilke iyileştirmesi gerçekleştirmek için yeterli veri toplamasına olanak sağlar.|

Kişiselleştirilmiş davranışın tüm kullanıcılar arasında keşfedilmeden bir şey olmadığı **kişiselleştirici kullanmayın** . Örneğin, bir 20 olası menü öğesi listesinden ilk bir pizza sırası önermek için kişiselleştirici kullanılması faydalı olur, ancak çocuklarla yardım gerektiğinde ("Grandma" gibi), Kullanıcı tabanınız genelinde kişiselleştirilebilir bir şey değildir.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Web uygulamasında kişiselleştirici kullanma

Bir Web uygulamasına bir öğrenme döngüsü eklemek şunları içerir:

* Hangi eylemlerin ve özelliklerin hangi şekilde kişiselleştirileceğini, hangi bağlam özelliklerinin kullanılacağını ve ne olacağını belirleyin.
* Uygulamanızda kişiselleştirme SDK 'sına bir başvuru ekleyin.
* Kişiselleştirmek için hazırsanız derecelendirme API 'sini çağırın.
* EventID 'yi depolayın. Daha sonra ödül API 'si ile bir ödül gönderirsiniz.
1. Kullanıcının kişiselleştirilmiş sayfanızı gördüğünüze emin olduktan sonra olayı etkinleştir ' i çağırın.
1. Derecelendirilen içeriğin Kullanıcı seçimini bekleyin.
1. Derecelendirme API 'sinin çıktısının ne kadar iyi olduğunu belirtmek için Reward API çağrısı yapın.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Sohbet bot ile kişiselleştirici kullanma

Bu örnekte, kullanıcıyı her seferinde bir menü veya seçenek serisini göndermek yerine varsayılan bir öneride bulunmak için nasıl kişiselleştirme kullanacağınızı öğreneceksiniz.

* Bu örnek için [kodu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) alın.
* Bot çözümünüzü ayarlayın. LUSıS uygulamanızı yayımladığınızdan emin olun.
* Bot için Rank ve Reward API çağrılarını yönetin.
    * LUSıS amacını işlemeyi yönetmek için kod ekleyin. **Hiçbiri** , en üst amaç olarak döndürülmezse veya en üstteki amaç puanı iş mantığı eşiğinin altındaysa, amaçları derecelendirmek için amaçlar listesini kişiselleştiriciye gönderin.
    * Kullanıcı listesini, sıralama API 'SI yanıtından en üst dereceli amaç olan ilk amacı olan seçilebilir bağlantılar olarak gösterir.
    * Kullanıcının seçimini yakalayın ve bunu Reward API çağrısında gönderin.

### <a name="recommended-bot-patterns"></a>Önerilen bot desenleri

* Her bir kullanıcı için sonuçları önbelleğe alma aksine, her bir Kesinleştirme gerektiğinde, kişiselleştirici derecelendirme API 'SI çağrıları yapın. Belirsizliği ortadan kaldırmak bir kişi için zaman içinde değişebilir ve derecelendirme API 'sinin farkları keşfetmesine izin vermek genel öğrenmeyi hızlandıracaktır.
* Çok sayıda kullanıcıyla ortak olan ve kişiselleştirmek için yeterli veri sahibi olan bir etkileşim seçin. Örneğin, giriş soruları, konuşma grafiğinde yalnızca birkaç kullanıcının ulaşabileceği daha küçük anlayışlardan daha iyi uyum sağlayabilir.
* "İlk öneri," ister X ister X ister misiniz? "önerilerini etkinleştirmek için derecelendirme API çağrılarını kullanın ya da "X 'i mi kullanıyorsunuz?" ve Kullanıcı yalnızca şunları doğrulayabilirler; kullanıcıya bir menü arasından seçim yapmak zorunda oldukları seçenekleri verme aksine. Örneğin, Kullanıcı: "kahve" bot 'ı sıralamak istiyorum: "bir Double espresu istiyor musunuz?" Bu şekilde, daha doğrudan bir öneriyle ilgili olduğu için, ödül sinyali de güçlü olur.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Bir öneri çözümü ile kişiselleştirici kullanma

Birçok şirket öneri altyapıları, pazarlama ve kampanya araçları, kitle segmentleme ve kümeleme, birlikte çalışan filtreleme ve diğer yollarla müşterilerin büyük bir katalogdan müşterilere yönelik ürünler önermesini sağlar.

[Microsoft Recommenders GitHub deposu](https://github.com/Microsoft/Recommenders) , jupi Not defterleri olarak sağlanan öneri sistemleri oluşturmak için örnekler ve en iyi uygulamalar sağlar. Bu, Xderin FM, çın ÖIB, ALS, RBM, DKN gibi yaygın birçok yaklaşım için, verileri hazırlamaya, model oluşturmaya, model oluşturmaya, değerlendirmeye, ayarlamaya ve kullanıma hazır hale getirmeye yönelik örnekler sağlar.

Kişiselleştirici, mevcut olduğunda bir öneri altyapısıyla çalışabilir.

* Öneri motorları büyük miktarda öğe alır (örneğin, 500.000) ve yüzlerce veya binlerce seçenekten oluşan bir alt küme (ilk 20 gibi) önerilir.
* Kişiselleştirmede, aralarında çok fazla bilgi içeren az sayıda eylem sürer ve belirli bir zengin bağlam için bunları gerçek zamanlı olarak derecelendirirken, çoğu öneri altyapısı yalnızca kullanıcılar, ürünler ve etkileşimleri hakkında birkaç öznitelik kullanır.
* Kişiselleştirici, Kullanıcı tercihlerini her zaman keşfetmeye olarak çalışabilen üzere tasarlanmıştır. Bu, örneğin Haberler, canlı olaylar, canlı topluluk içeriği, günlük güncelleştirme içeren içerik veya mevsimsel içerik gibi içeriğin hızlı şekilde değiştiği daha iyi sonuçlar verir.

Yaygın olarak kullanılan bir kullanım, bir öneri altyapısının çıkışını (örneğin, belirli bir müşteri için ilk 20 ürün) almak ve bunu, kişiselleştirici için giriş eylemleri olarak kullanmaktır.

## <a name="adding-content-safeguards-to-your-application"></a>Uygulamanıza içerik korumaları ekleme

Uygulamanız kullanıcılara gösterilen içerikte büyük sapmaya izin veriyorsa ve bu içeriklerin bazıları güvenli olmayabilir veya bazı kullanıcılar için uygun değilse, kullanıcılarınızın kabul edilebilir içeriği görmesini engellemek için doğru korumalarının yerinde olduğundan emin olmak için önceden planlamanız gerekir. Korumaları uygulamak için en iyi model şunlardır:
    * Derecelendirmek için eylemlerin listesini alın.
    * Hedef kitle için önemli olmayan olanları filtreleyin.
    * Yalnızca bu uygulanabilir eylemleri derecelendir.
    * Kullanıcıya en üst dereceli işlemleri görüntüleyin.

Bazı mimarilerde, yukarıdaki sıra uygulamak zor olabilir. Bu durumda, derecelendirmeden sonra korumaları uygulamaya yönelik alternatif bir yaklaşım vardır ancak bir sağlama yapılması gerekir. bu nedenle, koruma modelinin eğilmesi için, korumalar dışında kalan eylemler kullanılmaz.

* Öğrenme devre dışı bırakıldıktan sonra derecelendirmek için eylemlerin listesini alın.
* İşlemleri sıralama.
* En iyi eylemin uygun olup olmadığını denetleyin.
    * En iyi eylem önemli ise, bu derece öğrenmeyi etkinleştirin ve sonra kullanıcıya gösterin.
    * En iyi eylem önemli değilse, bu sıralama için öğrenmeyi etkinleştirmeyin ve kendi mantığınızla veya alternatif yaklaşımlarınızın kullanıcıya ne göstermesi gerektiğine karar verin. İkinci en iyi derecelendirilen seçeneği kullansanız bile, bu sıralama için öğrenmeyi etkinleştirmeyin.


## <a name="next-steps"></a>Sonraki adımlar

[Etics & sorumlu kullanımı](ethics-responsible-use.md).
