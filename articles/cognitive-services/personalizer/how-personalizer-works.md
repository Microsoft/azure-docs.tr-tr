---
title: Kişiselleştirici nasıl çalıştığı-kişiselleştirici
titleSuffix: Azure Cognitive Services
description: Kişiselleştirici, bir bağlamda hangi eylemin kullanılacağını saptamak için makine öğrenimini kullanır. Her öğrenme döngüsünün, işleme ve yeniden arama yoluyla kendisine gönderdiğiniz veriler üzerinde özel olarak eğitilen bir modeli vardır. Her öğrenme döngüsü birbirleriyle tamamen bağımsızdır.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 902bf84ebf090cf9f0f886ad1e774ff7bdfeca93
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490749"
---
# <a name="how-personalizer-works"></a>Kişiselleştirme nasıl çalışır?

Kişiselleştirici, bir bağlamda hangi eylemin kullanılacağını saptamak için makine öğrenimini kullanır. Her öğrenme döngüsünün, işleme ve **yeniden** arama yoluyla kendisine gönderdiğiniz veriler üzerinde özel olarak **eğitilen bir** modeli vardır. Her öğrenme döngüsü birbirleriyle tamamen bağımsızdır. Kişiselleştirmek istediğiniz her bir bölüm veya davranış için bir öğrenme döngüsü oluşturun.

Her döngü için, geçerli bağlamı temel alarak **Rank API 'sini** şu şekilde çağırın:

* Olası eylemlerin listesi: üst eyleme seçilecek içerik öğeleri.
* [Bağlam özelliklerinin](concepts-features.md)listesi: Kullanıcı, içerik ve bağlam gibi ilgili verileri bağlamsal olarak.

**Derecelendirme** API 'si şu iki seçenekten birini kullanmanıza karar verir:

* _Yararlanma_: geçmiş verilere göre en iyi eyleme karar vermeye yönelik geçerli model.
* _Keşfet_: en üstteki eylem yerine farklı bir eylem seçin.

**Reward** API 'si:

* Her bir derece çağrısının özelliklerini ve yeniden puanlarını kaydederek modeli eğitmek için veri toplar.
* , _Öğrenme ilkesinde_belirtilen yapılandırmaya göre modeli güncelleştirmek için bu verileri kullanır.

## <a name="architecture"></a>Mimari

Aşağıdaki görüntüde, derece ve geri aramaları çağırmanın mimari akışı gösterilmektedir:

![alternatif metin](./media/how-personalizer-works/personalization-how-it-works.png "Kişiselleştirmenin çalışması")

1. Kişiselleştirici, eylemin derecesini belirlemede bir iç AI modeli kullanır.
1. Hizmet, geçerli modelden mı yararlanacağına karar verir veya model için yeni seçimler keşfetmeyi belirler.  
1. Derecelendirme sonucu EventHub öğesine gönderilir.
1. Kişiselleştirmede bir ödül alındığında, bu, EventHub öğesine gönderilir. 
1. Rank ve ödül, bağıntılı.
1. AI modeli, bağıntı sonuçlarına göre güncelleştirilir.
1. Çıkarım altyapısı yeni modelle güncellenir. 

## <a name="research-behind-personalizer"></a>Kişiselleştirici arkasında araştırma

Kişiselleştirici, Microsoft Research 'daki incelemeler, araştırma etkinlikleri ve devam eden araştırma dahil olmak üzere [pekiştirmeye dayalı Learning](concepts-reinforcement-learning.md) 'in alanında son teknoloji bilimine ve araştırmasına dayalıdır.

## <a name="terminology"></a>Terminoloji

* **Öğrenme döngüsü**: uygulamanızın, kişiselleştirmeye faydalanabilir her bölümü için bir öğrenme döngüsü oluşturabilirsiniz. Kişiselleştirmek için birden fazla deneyim varsa, her biri için bir döngü oluşturun. 

* **Eylemler**: Eylemler, ürün veya promosyon gibi, aralarından seçim yapabileceğiniz içerik öğeleridir. Kişiselleştirici, sıralama API 'SI aracılığıyla kullanıcılarınıza, _Reward eylemi_olarak bilinen, göstermek için en iyi eylemi seçer. Her eylem, sıralama isteğiyle gönderilen özelliklere sahip olabilir.

* **Bağlam**: daha doğru bir derece sağlamak için, içeriğiniz hakkında bilgi sağlayın, örneğin:
    * Kullanıcı.
    * Bulundukları cihaz. 
    * Geçerli saat.
    * Geçerli durumla ilgili diğer veriler.
    * Kullanıcı veya bağlamla ilgili geçmiş verileri.

    Özel uygulamanız farklı bağlam bilgilerine sahip olabilir. 

* **[Özellikler](concepts-features.md)** : bir içerik öğesi veya Kullanıcı bağlamı hakkındaki bilgi birimi.

* **Reward**: Kullanıcı, 0 ile 1 arasında bir puan olarak döndürülen API 'Yi sırasıyla derecelendirme API 'sine nasıl yanıtladığını gösteren bir ölçüdür. 0-1 değeri, iş mantığınızla belirlenir ve bu seçenek, nasıl kişiselleştirmenin iş hedeflerine nasıl ulaştığından yardımcı olur. 

* **Araştırma**: kişiselleştirici hizmeti, en iyi eylemi döndürmek yerine, Kullanıcı için farklı bir eylem seçerse, ne zaman araştırma yapılır. Kişiselleştirici hizmeti, ve ' yi inceleyerek devam eden kullanıcı davranışına uyum sağlayabilir. 

* **Deneme süresi**: Bu etkinlik için sıralama çağrısının başladığı andan Itibaren, kişiselleştirici hizmetin bir azalma için beklediği süre miktarı.

* **Etkin olmayan olaylar**: etkin olmayan bir olay, derece olarak adlandırduğunuz bir olaydır, ancak istemci uygulama kararlarından dolayı kullanıcının sonucu göreceğinden emin olmadığınızı unutmayın. Etkin olmayan olaylar, kişiselleştirme sonuçları oluşturup depolamanıza olanak tanır ve ardından makine öğrenimi modelini etkilemeden daha sonra atmayı seçebilirsiniz.

* **Model**: bir kişiselleştirici modeli, Kullanıcı davranışı hakkında öğrenilen tüm verileri yakalar, daha fazla bilgi almak için göndereceğiniz bağımsız değişkenlerin birleşiminden eğitim verileri alma ve öğrenme ilkesi tarafından belirlenen bir eğitim davranışı. 

* **Öğrenme ilkesi**: her olay üzerinde kişiselleştirici, makine öğrenimi algoritmalarının nasıl çalıştığını etkileyen bazı meta parametrelere göre belirlenir. Yeni kişiselleştirme döngüleri, bir varsayılan öğrenme Ilkesiyle başlatılır ve bu da Orta performans elde edebilir. [Değerlendirme](concepts-offline-evaluation.md)çalıştırılırken, kişiselleştirici, özel olarak döngülerinizin kullanım örneklerine Iyileştirilmiş yeni öğrenme ilkeleri oluşturabilir. Kişiselleştirmede, değerlendirme sırasında oluşturulan her bir döngü için en iyi duruma getirilmiş ilkelerle önemli ölçüde daha iyi işlem yapılır.

## <a name="example-use-cases-for-personalizer"></a>Kişiselleştirici için örnek kullanım örnekleri

* Amaç açıklaması & belirsizliği ortadan kaldırma: her kullanıcıya kişiselleştirilmiş bir seçenek sunarak kullanıcılarınızın amacı net bir deneyimle daha iyi bir deneyim sunmasına yardımcı olun.
* Menüler için varsayılan öneriler & Seçenekler: bot 'ın, bir ımpersonal menüsü veya alternatifler listesi sunmak yerine, ilk adımla en olası öğeyi kişiselleştirilmiş bir şekilde önermesini sağlayabilirsiniz.
* Bot nitelikleri & tonu: ton, ayrıntı ve yazma stili değişebilen botlar için, bu nitelikleri kişiselleştirilmiş yollarla değiştirmeyi göz önünde bulundurun.
* Bildirim & uyarı içeriği: kullanıcılara daha fazla etkileşim kurmak için uyarı için hangi metnin kullanılacağına karar verin.
* Bildirim & uyarı zamanlaması: kullanıcılara daha fazla gitmek için ne zaman bildirim gönderileceğini kişiselleştirilmiş olarak öğreniyor.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Web uygulamasında kişiselleştirici kullanma

Bir Web uygulamasına döngü eklemek şunları içerir:

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
* "İlk öneri," ister X ister X ister misiniz? "önerilerini etkinleştirmek için derecelendirme API çağrılarını kullanın ya da "X 'i mi kullanıyorsunuz?" ve Kullanıcı yalnızca şunları doğrulayabilirler; kullanıcıya bir menü arasından seçim yapmak zorunda oldukları seçenekleri verme aksine. Örneğin, "kahve" botu sıralamak istiyorum: "bir Double espresu istiyor musunuz?". Bu şekilde, daha doğrudan bir öneriyle ilgili olduğu için, ödül sinyali de güçlü olur.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Bir öneri çözümü ile kişiselleştirici kullanma

Büyük bir kataloğu, daha sonra, derece API 'sine gönderilen 30 olası eylem olarak sunulabilen birkaç öğeye filtre uygulamak için öneri motorunuz kullanın.

Kişiselleştirme altyapılarını, kişiselleştirici ile kullanabilirsiniz:

* [Öneri çözümünü](https://github.com/Microsoft/Recommenders/)ayarlayın. 
* Bir sayfa görüntülenirken, önerilerin kısa bir listesini almak için öneri modelini çağırın.
* Öneri çözümünün çıkışını derecelendirmek için kişiselleştirme çağrısı yapın.
* Reward API çağrısıyla Kullanıcı eyleminiz hakkında geri bildirim gönderin.


## <a name="pitfalls-to-avoid"></a>Kaçınmak için

* Kişiselleştirilmiş davranışın tüm kullanıcılar genelinde keşfedilmeden, ancak belirli kullanıcılar için hatırlanması gereken bir şey olmadığı ya da kullanıcıya özgü alternatifler listesinden geldiği bir şey olmadığı kişiselleştirici kullanmayın. Örneğin, bir 20 olası menü öğesi listesinden ilk bir pizza sırası önermek için kişiselleştirici kullanılması faydalı olur, ancak çocuklarla ilgili yardım ("Grandma" gibi) için gerekli olduğunda kullanıcıların ilgili kişi listesinden çağrılabilecek iletişim, şunun genelinde kişiselleştirilebilir Kullanıcı tabanınız.


## <a name="adding-content-safeguards-to-your-application"></a>Uygulamanıza içerik korumaları ekleme

Uygulamanız kullanıcılara gösterilen içerikte büyük sapmaya izin veriyorsa ve bu içeriklerin bazıları güvenli olmayabilir veya bazı kullanıcılar için uygun değilse, kullanıcılarınızın kabul edilebilir olarak görmesini engellemek için doğru korumalarının sağlandığından emin olmak için önceden planlamanız gerekir içeriði. Korumaları uygulamak için en iyi model şunlardır:
    * Derecelendirmek için eylemlerin listesini alın.
    * Hedef kitle için önemli olmayan olanları filtreleyin.
    * Yalnızca bu uygulanabilir eylemleri derecelendir.
    * Kullanıcıya en üst dereceli işlemleri görüntüleyin.

Bazı mimarilerde, yukarıdaki sıra uygulamak zor olabilir. Bu durumda, derecelendirmeden sonra korumaları uygulamaya yönelik alternatif bir yaklaşım vardır, ancak koruma dışında kalan eylemlerin kişiselleştirici modeli eğilmesi için kullanılması gerekir.

* Öğrenme devre dışı bırakıldıktan sonra derecelendirmek için eylemlerin listesini alın.
* İşlemleri sıralama.
* En iyi eylemin uygun olup olmadığını denetleyin.
    * En iyi eylem önemli ise, bu derece öğrenmeyi etkinleştirin ve sonra kullanıcıya gösterin.
    * En iyi eylem önemli değilse, bu sıralama için öğrenmeyi etkinleştirmeyin ve kendi mantığınızla veya alternatif yaklaşımlarınızın kullanıcıya ne göstermesi gerektiğine karar verin. İkinci en iyi derecelendirilen seçeneği kullansanız bile, bu sıralama için öğrenmeyi etkinleştirmeyin.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Kişiselleştirici için yeterli verimlilik doğrulanıyor

[Çevrimdışı değerlendirmeler](how-to-offline-evaluation.md) gerçekleştirerek, kişiselleştirmede düzenli aralıklarla etkili bir şekilde izleyebilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

[Kişiselleştirici 'yi nerede kullanacağınızı](where-can-you-use-personalizer.md)anlayın.
[Çevrimdışı değerlendirmeler](how-to-offline-evaluation.md) gerçekleştirme
