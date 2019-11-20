---
title: include dosyası
description: include dosyası
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a2eafd6bb34b897f3492ddcffd6841f0fabc4ca7
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034555"
---
Bir sistem tasarlarken, bu sisteme yönelik olası tehditleri anlamak ve sistem tasarlandıkça ve uygun hale getirebileceği için uygun savunma savunmaları eklemek önemlidir. Bir saldırganın bir sistemin güvenliğinin nasıl tehlikeye alamadığını anlamak için, ürünü güvenlikle birlikte Başlat ' dan tasarlamak önemlidir.

## <a name="security-starts-with-a-threat-model"></a>Güvenlik, tehdit modeliyle başlar

Microsoft, ürünleri için uzun süredir kullanılan tehdit modellerine sahiptir ve şirketin tehdit modelleme sürecini herkese açık bir şekilde yaptı. Şirket deneyimi, modellemenin en çok hangi tehditlerle ilgili olarak bilindiğinin ötesinde beklenmedik avantajlar olduğunu gösterir. Örneğin, geliştirme ekibinin dışındaki diğer kişilerle açık bir tartışma için bir duyurmanın oluşturur ve bu da üründeki yeni fikirlere ve geliştirmelere neden olabilir.

Tehdit modellemesinin amacı, bir saldırganın bir sistemin güvenliğini tehlikeye atabilir ve uygun azaltmaları doğru olduğundan emin olmanızı sağlar. Tehdit modellemesi, sistem dağıtıldıktan sonra değil, sistem tasarlandıktan sonra tasarım ekibini azaltıcı etkenleri göz önünde bulundurmaya zorlar. Bu olgu önemli ölçüde önemlidir, çünkü bu, alandaki bir cihaz için güvenlik savunması için uygun olmadığından, hataya açıktır ve müşterilerin risk altında kalmasını sağlar.

Birçok geliştirme ekibi, müşterilerin faydaladığı sistem için işlevsel gereksinimleri yakalayan harika bir iş görür. Bununla birlikte, başka birinin sistemi kötüye kullanmasından daha zor olmayan yollar belirlemek daha zordur. Tehdit modellemesi, geliştirme ekiplerinin bir saldırganın neler yapabileceğini ve neden olabileceğini anlamasına yardımcı olabilir. Tehdit modellemesi, sistemdeki güvenlik tasarımı kararları hakkında bir tartışma oluşturan, Ayrıca, tasarıma güvenlik açısından yapılan değişiklikler hakkında bir tartışma oluşturan yapısal bir işlemdir. Tehdit modeli yalnızca bir belge olsa da, bu belge, bilgi sürekliliği, öğrenilen derslerin saklanması ve yeni ekibin hızlı bir şekilde eklenmesine yardımcı olmak için ideal bir yolu da temsil eder. Son olarak, tehdit modellemesinin bir sonucu, müşterilerinize sağlamak istediğiniz güvenlik taahhütlerini göz önünde bulundurmanız gerekir. Bu taahhütler, tehdit modellemesi ile birlikte Nesnelerin İnterneti (IoT) çözümünüzün test edilmesine ve bunlara yönelik sınamalara sahip.

### <a name="when-to-do-threat-modeling"></a>Tehdit modellemesi ne zaman yapılır

[Tehdit modellemesi](https://www.microsoft.com/en-us/sdl/adopt/threatmodeling.aspx) , tasarım aşamasına dahil ettiğinizde en büyük değeri sunar. Tasarlarken tehditleri ortadan kaldırmak için değişiklik yapma konusunda en büyük esnekliğe sahip olursunuz. Tehditleri tasarıma göre ortadan kaldırmak istenen bir sonucudur. Hafifletmeyi eklemekten, test etenden ve bunların güncel kalmasını sağlamaya ve üstelik bu tür bir eleme işleminin her zaman mümkün olmadığından çok daha kolay. Bir ürün daha fazla yetişkin haline geldiği için tehditleri ortadan kaldırmak daha zor hale gelir ve sonunda geliştirmede erken tehdit modelinden daha fazla iş ve çok daha zor bir denge gerektirir.

### <a name="what-to-consider-for-threat-modeling"></a>Tehdit modellemesi için göz önüne alınması gerekenler

Çözüme bir bütün olarak bakmanız ve ayrıca aşağıdaki alanlara odaklanmanız gerekir:

* Güvenlik ve gizlilik özellikleri
* Arızaları güvenlikle ilgili olan özellikler
* Bir güven sınırına dokunmaya yönelik özellikler

### <a name="who-performs-threat-modeling"></a>Tehdit modelleme yapan kim

Tehdit modellemesi, diğer gibi bir işlemdir. Tehdit modeli belgesini çözümün diğer bileşenleri gibi değerlendirmek ve doğrulamak iyi bir fikirdir. Birçok geliştirme ekibi, müşterilerin faydaladığı sistem için işlevsel gereksinimleri yakalayan harika bir iş görür. Bununla birlikte, başka birinin sistemi kötüye kullanmasından daha zor olmayan yollar belirlemek daha zordur. Tehdit modellemesi, geliştirme ekiplerinin bir saldırganın neler yapabileceğini ve neden olabileceğini anlamasına yardımcı olabilir.

### <a name="how-to-perform-threat-modeling"></a>Tehdit modellemesini gerçekleştirme

Tehdit modelleme işlemi dört adımdan oluşur; adımlar şunlardır:

* Uygulamayı modelleme
* Tehditleri listeleme
* Tehditleri azaltma
* Azaltmaları doğrulama

#### <a name="the-process-steps"></a>İşlem adımları

Bir tehdit modeli oluştururken göz önünde bulundurmanız gereken üç Thumb kuralı:

1. Başvuru mimarisinden bir diyagram oluşturun.

2. İlk olarak başlangıç. Genel bir bakış edinin ve bir bütün olarak sistemi, derin bir şekilde anlayın. Bu yaklaşım, doğru yerlerde derinlemesine bir bakış sağlamanıza yardımcı olur.

3. İşlemi, işlem sürücüsüne izin vermez. Modelleme aşamasında bir sorun bulmanız ve incelemek istiyorsanız,! adresine gidin! Bu adımları takip etmeniz gerekmez.

#### <a name="threats"></a>Tehditler

Tehdit modelinin dört temel öğesi şunlardır:

* Web Hizmetleri, Win32 Hizmetleri ve * Nix Daemon 'ları gibi süreçler. Bazı karmaşık varlıklar (örneğin, alan ağ geçitleri ve algılayıcılar), bu alanlarda teknik ayrıntıya gitme mümkün olmadığında bir işlem olarak soyutlenebilir.

* Veri depoları (bir yapılandırma dosyası veya veritabanı gibi her yerden veri depolanır)

* Veri akışı (verilerin uygulamadaki diğer öğeler arasında taşındığı)

* Dış varlıklar (sistemle etkileşime sahip ancak uygulama denetimi altında değil, örnek kullanıcıları ve uydu akışlarını içerir)

Mimari diyagramdaki tüm öğeler çeşitli tehditlere tabidir; Bu makalede, Ilerleme. [Tehdit modellemesini tekrar okuyun ve](https://blogs.msdn.microsoft.com/larryosterman/2007/09/04/threat-modeling-again-stride/) ilerleme öğeleri hakkında daha fazla bilgi edinin.

Uygulama diyagramının farklı öğeleri belirli bir Ilerleme tehditlerine tabidir:

* Süreçler ilerme tabidir
* Veri akışları TıD 'ye tabidir
* Veri depoları, veri depoları günlük dosyaları olduğunda TıD 'ye ve bazen R 'ye tabidir.
* Dış varlıklar SRD 'ye tabidir

## <a name="security-in-iot"></a>IoT 'de güvenlik

Bağlı özel amaçlı cihazların önemli sayıda olası etkileşim yüzeyi alanı ve etkileşim deseni vardır; bunların hepsi, bu cihazlara dijital erişimi güvenli hale getirmek için bir çerçeve sağlamak üzere göz önünde bulundurulmalıdır. "Dijital erişim" terimi burada, erişim güvenliği 'nin fiziksel erişim denetimi aracılığıyla sağlandığı doğrudan cihaz etkileşimi aracılığıyla yürütülen işlemlerden ayırt edilebilmesi için kullanılır. Örneğin, cihazı kapıya kilidi olan bir odaya yerleştirme. Fiziksel erişim yazılım ve donanım kullanılarak reddedilemez, ancak önde gelen fiziksel erişimin sistem girişimi ' ne kadar oluşmasını engellemek için ölçüler alınabilir.

Etkileşim modellerini araştırırken, "cihaz denetimi" ve "cihaz verileri" ni aynı ilgi düzeyine göre inceleyin. "Cihaz denetimi", davranışını durumuna ya da ortamının durumuna doğru şekilde değiştirme veya davranışlarını sağlayan herhangi bir kişi tarafından bir cihaza sunulan herhangi bir bilgi olarak sınıflandırılabilirler. "Cihaz verileri", bir cihazın durumu ve ortamının gözlemlenen durumu hakkında başka bir tarafa yaydığı herhangi bir bilgi olarak sınıflandırılabilirler.

En iyi güvenlik uygulamalarını iyileştirmek için, tehdit modelleme alıştırmanın bir parçası olarak tipik bir IoT mimarisinin birkaç bileşene/bölgeye bölünmesi önerilir. Bu bölgeler, bu bölümün tamamında tamamen açıklanmıştır ve şunları içerir:

* Aygıtların
* Alan ağ geçidi,
* Bulut ağ geçitleri ve
* servislere.

Bölgeler, bir çözümü segmentlere ayırmak için geniş bir yoldur; Her bölge genellikle kendi veri ve kimlik doğrulama ve yetkilendirme gereksinimlerine sahiptir. Bölgeler, daha yüksek güven bölgelerinde hasar yalıtma ve düşük güven bölgelerinin etkilerini kısıtlamak için de kullanılabilir.

Her bölge, aşağıdaki diyagramda noktalı kırmızı çizgi olarak belirtilen bir güven sınırı ile ayrılır. Bir kaynaktan diğerine veri/bilgi geçişini temsil eder. Bu geçiş sırasında veri/bilgiler, kimlik sahtekarlığı, Izinsiz, geri çevirme, bilgilerin açıklanması, hizmet reddi ve ayrıcalık yükselmesi (ilerme) ile ilgili olabilir.

![IoT güvenlik bölgeleri](media/iot-security-architecture/iot-security-architecture-fig1.png) 

Her sınır içinde gösterilen bileşenler de tabi, bu da çözümün tam 360 tehdit modelleme görünümünü sağlar. Aşağıdaki bölümlerde, bileşenlerin her biri ve belirli güvenlik konuları ve yerinde yerleştirilmelidir.

Aşağıdaki bölümlerde, genellikle bu bölgelerde bulunan standart bileşenler tartışıyordu.

### <a name="the-device-zone"></a>Cihaz bölgesi

Cihaz ortamı, fiziksel erişimin ve/veya "yerel ağ" ağa yönelik eşler arası dijital erişiminin uygun olduğu cihaz etrafındaki en yakın fiziksel alandır. "Yerel ağ", farklı olan, ancak büyük olasılıkla köprülenmiş ve genel Internet 'e ait olan bir ağ olduğu varsayılır ve cihazların eşler arası iletişimine izin veren herhangi bir kısa erimli kablosuz radyo teknolojisini içerir. Böyle bir yerel ağın yanılsamasını oluşturan herhangi bir ağ sanallaştırma *teknolojisi içermez ve* aynı zamanda, bir veya daha fazla ağ alanı arasında iletişim kurmasını gerektiren genel operatör ağlarını içermez uçtan uca iletişim ilişkisi.

### <a name="the-field-gateway-zone"></a>Alan ağ geçidi bölgesi

Alan ağ geçidi, iletişim Etkinleştirici ve potansiyel olarak cihaz denetim sistemi ve cihaz veri işleme merkezi olarak davranan bir cihaz/gereç veya bazı genel amaçlı sunucu bilgisayar yazılımıdır. Alan ağ geçidi bölgesi, alan ağ geçidinin kendisini ve ona bağlı tüm cihazları içerir. Adından da anlaşılacağı gibi, alan ağ geçitleri adanmış veri işleme tesislerinin dışında davranır, genellikle konuma bağlıdır, büyük olasılıkla fiziksel olarak izinsiz erişim sağlar ve sınırlı işlemsel yedekliliğe sahiptir. Hepsi, bir alan ağ geçidinin, işlevinin ne olduğunu bilirken, bir BT 'nin bir şey ve çok daha fazla şey olduğunu varsayalım.

Bir alan ağ geçidi, erişim ve bilgi akışını yönetirken etkin bir rolü olan bir boyutundaydı trafik yönlendiricisinden farklıdır, yani bu, bir uygulama ve ağ bağlantısı ya da oturum terminalidir. Bir NAT aygıtı veya güvenlik duvarı, açık bağlantı veya oturum terminalleri olmadığından, ancak bunlar üzerinden yapılan bir yol (veya blok) bağlantısı ya da oturumları olmadığından alan ağ geçitleri olarak nitelemez. Alan ağ geçidi iki ayrı yüzey alanına sahiptir. Bunlardan biri, kendisine bağlı olan ve bölgenin içini temsil eden cihazların yanı sıra tüm dış taraflar ve bölgenin kenarı olan bir yüzlük olur.

### <a name="the-cloud-gateway-zone"></a>Bulut ağ geçidi bölgesi

Bulut ağ geçidi, genellikle bu tür sistemlerin bir Federasyonu olan bulut tabanlı bir denetim ve veri analizi sistemine bağlı olarak, ortak ağ alanı genelinde birden çok farklı siteden cihazlar veya alan ağ geçitleri arasında uzaktan iletişim sağlayan bir sistemdir. Bazı durumlarda, bir bulut ağ geçidi, tablet veya telefon gibi terminallerdeki özel amaçlı cihazlara erişimi hemen kolaylaştırabilir. Burada açıklanan bağlamda, "Cloud", ekli cihazlar veya alan ağ geçitleri ile aynı siteye bağlı olmayan ayrılmış bir veri işleme sistemine başvurmaktır. Ayrıca, bir bulut bölgesinde, işlemsel ölçüler hedeflenen fiziksel erişimi engeller ve "genel bulut" altyapısına açık değildir.  

Bir bulut ağ geçidi, bulut ağ geçidini ve tüm bağlı cihazlarını veya alan ağ geçitlerini diğer ağ trafiğinden yalıtılmış olarak bir ağ sanallaştırma kaplamasına eşlenebilir. Bulut ağ geçidi, cihaz verileri için bir cihaz denetim sistemi veya işlem veya depolama olanağı değildir; bulut ağ geçidine sahip bu tesisler arabirimi. Bulut ağ geçidi bölgesi, tüm alan ağ geçitleri ve cihazlarıyla birlikte doğrudan veya dolaylı olarak bağlı olan bulut ağ geçidinin kendisini içerir. Bölgenin kenarı, tüm dış tarafların iletişim kurduğu ayrı bir yüzey alanıdır.

### <a name="the-services-zone"></a>Hizmetler bölgesi

Bir "hizmet", bu bağlam için, veri toplama ve analizler için bir alan ya da bulut ağ geçidi aracılığıyla cihazlarıyla arabirim içeren herhangi bir yazılım bileşeni ya da modülle, komut ve denetim için tanımlanır. Hizmetler, hizmet araçları. Bunlar, kimlik geçitleri ve diğer alt sistemler arasındaki kimlik bilgileri altında davranır, verileri depolar ve analiz eder, veri öngörülerine veya zamanlamalarına göre cihazlara komutlar olarak çalışabilen ve yetkili son kullanıcılara bilgi ve denetim özellikleri sunar.

### <a name="information-devices-versus-special-purpose-devices"></a>Bilgi-cihazlar özel amaçlı cihazlara karşı

Bilgisayarlar, telefonlar ve tabletler öncelikle etkileşimli bilgi cihazlarıdır. Telefonlar ve tabletler, pil ömrünü en üst düzeye çıkarmak için açık olarak iyileştirilmiştir. Tercihen, bir kişiyle hemen etkileşim kurmayan ya da müzik yürütme veya sahibini belirli bir konuma göre sağlama gibi hizmetleri sağlamayan kısmen devre dışı bırakır. Bir sistem perspektifinden, bu bilgi teknolojisi cihazları genellikle kişiler 'e yönelik proxy olarak hareket eder. Bunlar "insanlar" ve "kişi sensörler" giriş toplamaktadır.

Basit Sıcaklık sensörlerinden, içindeki binlerce bileşeni olan karmaşık fabrika üretim satırlarına kadar özel amaçlı cihazlar farklıdır. Bu cihazlar, amaç açısından çok daha kapsamlıysa ve Kullanıcı arabirimi sağlamasa bile, büyük ölçüde fiziksel dünyadaki varlıklarla tümleştirilebilen veya bunlarla tümleştirilmiştir. Ortam durumları ölçerek, vanalar, denetim Servos, denetim servis, ses alarmları, anahtar ışıkları ve diğer birçok görevi işler. Bunlar, bir bilgi cihazının çok genel, çok pahalı, çok büyük veya çok Brittle olduğu konusunda çalışmaya yardımcı olurlar. Somut amaç, kendi teknik tasarımını ve üretim ve zamanlanan ömür işlemleri için kullanılabilir parasal bütçeyi hemen belirler. Bu iki anahtar faktörün birleşimi kullanılabilir operasyonel enerji bütçesini, fiziksel ayak izini ve bu nedenle kullanılabilir depolama, işlem ve güvenlik yeteneklerini kısıtlar.

Otomatik veya uzaktan denetlenebilir cihazlarla "yanlış" olursa, örneğin fiziksel hatalar veya Denetim mantığı kusurları izinsiz erişime ve düzenlemeye karşı bir şekilde yapılır. Üretim lotları yok edilebilir, binalar gevkülmüş veya aşağı yazılabilir, insanlar da Red veya hatta zar olabilir. Bu, çalınan kredi kartının sınırını aşan birisinden farklı bir hasar sınıfına ait. İşlemleri yapan cihazların güvenlik çubuğu ve ayrıca, sonuçta nesnelerin taşınmasına neden olan komutlarla sonuçlanan algılayıcı verileri, herhangi bir e-ticaret veya bankacılık senaryosundan daha yüksek olmalıdır.

### <a name="device-control-and-device-data-interactions"></a>Cihaz denetimi ve cihaz verileri etkileşimleri

Bağlı özel amaçlı cihazların önemli sayıda olası etkileşim yüzeyi alanı ve etkileşim deseni vardır; bunların hepsi, bu cihazlara dijital erişimi güvenli hale getirmek için bir çerçeve sağlamak üzere göz önünde bulundurulmalıdır. "Dijital erişim" terimi burada, erişim güvenliği 'nin fiziksel erişim denetimi aracılığıyla sağlandığı doğrudan cihaz etkileşimi aracılığıyla yürütülen işlemlerden ayırt edilebilmesi için kullanılır. Örneğin, cihazı kapıya kilidi olan bir odaya yerleştirme. Fiziksel erişim yazılım ve donanım kullanılarak reddedilemez, ancak önde gelen fiziksel erişimin sistem girişimi ' ne kadar oluşmasını engellemek için ölçüler alınabilir.

Etkileşim modellerini keşfederken, tehdit modellemesi sırasında aynı dikkat düzeyiyle "cihaz denetimi" ve "cihaz verileri" bölümüne bakın. "Cihaz denetimi", davranışını durumuna ya da ortamının durumuna doğru şekilde değiştirme veya davranışlarını sağlayan herhangi bir kişi tarafından bir cihaza sunulan herhangi bir bilgi olarak sınıflandırılabilirler. "Cihaz verileri", bir cihazın durumu ve ortamının gözlemlenen durumu hakkında başka bir tarafa yaydığı herhangi bir bilgi olarak sınıflandırılabilirler.

## <a name="performing-threat-modeling-for-the-azure-iot-reference-architecture"></a>Azure IoT başvuru mimarisi için tehdit modellemesi gerçekleştiriliyor

Microsoft, daha önce özetlenen çerçeveyi Azure IoT için tehdit modellemesi yapmak üzere kullanır. Aşağıdaki bölümde IoT için tehdit modellemesini ve tanımlanan tehditleri nasıl ele alınacağını göstermek için Azure IoT başvuru mimarisinin somut örneği kullanılmaktadır. Bu örnek, odağın dört ana alanını tanımlar:

* Cihazlar ve veri kaynakları,
* Veri aktarımı,
* Cihaz ve olay Işleme ve
* Sununuzda

![Azure IoT için tehdit modellemesi](media/iot-security-architecture/iot-security-architecture-fig2.png)

Aşağıdaki diyagramda, Microsoft 'un Microsoft Threat Modeling Tool tarafından kullanılan bir veri akışı diyagramı modeli kullanılarak Microsoft 'un IoT mimarisinin basitleştirilmiş bir görünümü sunulmaktadır:

![MS Threat Modeling Tool kullanarak Azure IoT için tehdit modellemesi](media/iot-security-architecture/iot-security-architecture-fig3.png)

Mimarinin cihaz ve ağ geçidi yeteneklerini ayırdığından emin olmak önemlidir. Bu yaklaşım, kullanıcının daha güvenli ağ geçidi cihazlarından yararlanmasını sağlar: güvenli protokoller kullanarak bulut ağ geçidiyle iletişim kurabiliyor ve bu, genellikle yerel bir cihazın bir termostat gibi daha fazla işlem yükü gerektirir. kendi kendine sağlayın. Azure hizmetleri bölgesinde, bulut ağ geçidinin Azure IoT Hub hizmeti tarafından temsil edildiği varsayılır.

### <a name="device-and-data-sourcesdata-transport"></a>Cihaz ve veri kaynakları/veri aktarımı

Bu bölümde, tehdit modellemesinin merceği aracılığıyla daha önce özetlenen mimari incelenmektedir ve bazı ilgili kaygıların nasıl ele alınacağını gösteren bir genel bakış sağlanır. Bu örnek, bir tehdit modelinin temel öğelerine odaklanır:

* Süreçler (hem denetim hem de dış öğeleriniz altında)
* İletişim (veri akışları olarak da bilinir)
* Depolama (veri depoları olarak da bilinir)

#### <a name="processes"></a>İşlemler

Azure IoT mimarisinde özetlenen kategorilerin her birinde, bu örnek farklı aşamalar verileri/içindeki bir çok farklı tehdidi hafifletmeye çalışır: işlem, iletişim ve depolama. Aşağıda, "işlem" kategorisinin en yaygın olanlara genel bakış verilmiştir ve bu tehditlerin en iyi şekilde nasıl azaldıklara ilişkin bir genel bakış sunulmaktadır:

**Sızdırma (ler)** : bir saldırgan, yazılım veya donanım düzeyinde bir cihazdan şifreleme anahtarı malzemesini ayıklayabilir ve ardından, ana malzemenin bulunduğu cihaz kimliği altında farklı bir fiziksel veya sanal cihazla sisteme erişebilir. alındı. İyi bir çizim, popüler Prankster araçları olan herhangi bir TV 'yi açmak için uzak denetimlerdir.

**Hizmet reddi (D)** : bir cihaz, radyo sıklıklarının veya üstün kabloların etkilenerek çalışmaya veya iletişim kurmasına karışarak işlenebilir. Örneğin elektrik veya ağ bağlantısı kesilen bir güvenlik kamerası veri iletemez.

**Değişiklik (T)** : bir saldırgan, cihaz üzerinde çalışan yazılımı kısmen veya tamamen değiştirebilir, bu durum, önemli malzemeler veya anahtar tutan şifreleme tesislerinde bulunan yazılımın orijinal kimliğini kullanmasına izin verebilir. malzemeler, ıllicıt programı tarafından kullanılabilir. Örneğin, bir saldırgan, iletişim yolundaki cihazdan gelen verileri kesme ve gizleme ve çalınan anahtar malzemesiyle kimliği doğrulanmış yanlış verilerle değiştirmek için ayıklanan anahtar malzemesini kullanabilir.

**Bilgilerin açığa çıkması (ı)** : cihaz, yönetilen yazılım çalıştırıyorsa, bu tür bir yazılım, verileri yetkisiz taraflara sızdırabilir. Örneğin, bir saldırgan ayıklanan anahtar malzemesini, cihaz ile bir denetleyici veya alan ağ geçidi ya da bulut ağ geçidi ile ilgili bilgi almak için bir bağlantı ve ağ geçidi ya da bulut ağ geçidi arasındaki iletişim yoluna eklemek

**Ayrıcalık yükselmesi (E)** : belirli bir işlevi yapan bir cihaz, başka bir şey yapmak için zorlanabilir. Örneğin, yarı bir şekilde programlanan bir vana, her şey açmak için karmaşık olabilir.

| **Bileşen** | **Tehdit** | **Mayı** | **Esini** | **Paylaşır** |
| --- | --- | --- | --- | --- |
| Cihaz |S |Cihaza kimlik atama ve cihazın kimliğini doğrulama |Cihazı veya cihazın bir bölümünü başka bir cihazla değiştirme. Doğru cihazla konuştuolduğunuzu nasıl anlarsınız? |Aktarım Katmanı Güvenliği (TLS) veya IPSec kullanarak cihazın kimliğini doğrulama. Altyapı, tam asimetrik şifrelemeyi işleyemeyen cihazlarda önceden paylaşılan anahtar (PSK) kullanmayı desteklemelidir. Azure AD 'den yararlanın, [OAuth](https://www.rfc-editor.org/pdfrfc/rfc6755.txt.pdf) |
|| TR |Cihazdan anahtar ve diğer şifreleme malzemelerinin ayıklanmayı olanaksız hale getirerek cihaza WIT kanıtlama mekanizmaları uygulayın. |Risk, başka birinin cihazı (fiziksel girişim) üzerinde değişiklik getirme durumunda olur. Bu cihazın kurcalanmadığından nasıl emin olabilirsiniz. |En etkili risk azaltma, anahtarların okuyamayacağı özel on yonga devresi içinde depolanmasını sağlayan, ancak anahtarı kullanan ancak anahtarı hiçbir şekilde açıklayabilen şifreleme işlemleri için kullanılabilen bir Güvenilir Platform Modülü (TPM) özelliğidir. Cihazın bellek şifrelemesi. Cihaz için anahtar yönetimi. Kod imzalanıyor. |
|| A |Cihaza erişim denetimi yapın. Yetkilendirme düzeni. |Cihaz, bir dış kaynaktan gelen komutlara bağlı olarak tek tek eylemlerin yapılmasına izin veriyorsa ve hatta güvenliği aşılmış algılayıcılar, aksi takdirde erişilemeyen işlemler gerçekleştirmeye izin verir. |Cihaz için yetkilendirme şemasına sahip olma |
| Alan ağ geçidi |S |Alan ağ geçidinin bulut ağ geçidine (CERT tabanlı, PSK veya talep tabanlı gibi) doğrulanması. |Birisi alan ağ geçidine sızabilir, kendisini herhangi bir cihaz olarak sunabilir. |TLS RSA/PSK, IPSec, [RFC 4279](https://tools.ietf.org/html/rfc4279). Genel olarak cihazlarındaki aynı anahtar depolama ve kanıtlama sorunları-en iyi durum TPM kullanır. IPSec için, kablosuz algılayıcı ağlarını (WSN) desteklemeye yönelik 6LowPAN uzantısı. |
|| TR |Alan ağ geçidini izinsiz (TPM?) karşı koruyun |Alan ağ geçidine konuştuğu bir bulut ağ geçidi ile ilgili sızdırma saldırıları, bilgilerin açığa çıkmasına ve verilerin değiştirilmesine neden olabilir |Bellek şifreleme, TPM, kimlik doğrulama. |
|| A |Alan ağ geçidi için erişim denetimi mekanizması | | |

Bu kategoride tehditler için bazı örnekler aşağıda verilmiştir:

**Sızdırma**: bir saldırgan, yazılım veya donanım düzeyinde bir cihazdan şifreleme anahtarı malzemesini ayıklayabilir ve sonra da anahtar malzemesinin bulunduğu cihaz kimliği altında farklı bir fiziksel veya sanal cihazla sisteme erişebilir alındığı yer.

**Hizmet reddi**: bir cihaz radyo sıklıklarının veya üstün kabloların etkilenerek çalışmaya veya iletişime geçilerek oluşturulabilir. Örneğin elektrik veya ağ bağlantısı kesilen bir güvenlik kamerası veri iletemez.

**Değişiklik**: bir saldırgan, cihaz üzerinde çalışan yazılımı kısmen veya bir şekilde değiştirebilir, bu durum, önemli malzemeler ya da anahtar tutan şifreleme tesislerinde bulunan yazılımın orijinal kimliğini kullanmasına izin verebilir. malzemeler, ıllicıt programı tarafından kullanılabilir.

**Müdahale**: boş bir koridordaki 'ın görünür-bit resmini gösteren bir izleme Kamerası, böyle bir koridordaki fotoğrafına hedeflenmiş olabilir. Bir duman veya yangın algılayıcısı, birinin altında daha açık tutan bir kişi raporluyor. Her iki durumda da cihaz Teknik olarak sisteme tamamen güvenilir olabilir, ancak bu işlem, yönetilen bilgileri raporlar.

**Değişiklik**: bir saldırgan, iletişim yolundaki cihazdan gelen verileri kesme ve gizleme ve çalınan anahtar malzemesiyle kimlik doğrulayan yanlış verilerle değiştirmek için ayıklanan anahtar malzemesinden yararlanabilir.

**Değişiklik**: bir saldırgan, cihaz üzerinde çalışan yazılımı kısmen veya tamamen değiştirebilir ve bu durum, anahtar malzemesi veya şifreleme tesisleri anahtar tutan şifreleme tesislerinde bulunan yazılımın orijinal kimliğini kullanmasına izin verebilir. malzemeler, ıllicıt programı tarafından kullanılabilir.

**Bilgilerin açığa çıkması**: cihaz, yönetilen yazılım çalıştırıyorsa, bu tür bir yazılım, verileri yetkisiz taraflara sızdırabilir.

**Bilgilerin açığa çıkması**: bir saldırgan, bir bilgisayar ile bir denetleyici veya alan ağ geçidi ya da bulut ağ geçidi ile ilgili bilgi almak için bir iletişim yoluna kendisini eklemek üzere ayıklanan anahtar malzemesini kullanabilir.

**Hizmet reddi**: cihaz kapatılabilir veya iletişim mümkün olmayan bir moda açılabilir (birçok endüstriyel makinede bilerek olur).

**Değişiklik**: cihaz, denetim sistemine bilinmeyen bir durumda çalışacak şekilde yeniden yapılandırılabilir (bilinen ayarlama parametreleri dışında) ve bu nedenle yanlış yorumlanabilen verileri sağlar

**Ayrıcalık yükselmesi**: belirli bir işlevi yapan bir cihaz, başka bir şey yapmak için zorlanabilir. Örneğin, yarı bir şekilde programlanan bir vana, her şey açmak için karmaşık olabilir.

**Hizmet reddi**: cihaz, iletişimin mümkün olmadığı bir duruma açılabilir.

**Değişiklik**: cihaz, denetim sistemine bilinmeyen bir durumda çalışacak şekilde yeniden yapılandırılabilir (bilinen ayarlama parametreleri dışında) ve bu nedenle yanlış yorumlanabilen verileri sağlar.

**Yanıltma/ret/Red**: güvenli değilse (tüketici uzak denetimlerinde nadiren olduğu gibi), bir saldırgan bir cihazın durumunu anonim olarak değiştirebilir. İyi bir çizim, popüler Prankster araçları olan herhangi bir TV 'yi açmak için uzak denetimlerdir.

#### <a name="communication"></a>İletişim

Cihazlar, cihazlar ve alan ağ geçitleri ile cihaz ve bulut ağ geçidi arasındaki iletişim yolunun etrafındaki tehditler. Aşağıdaki tabloda, cihazda/VPN üzerinde açık yuvaların çevresinde bazı yönergeler bulunur:

| **Bileşen** | **Tehdit** | **Mayı** | **Esini** | **Paylaşır** |
| --- | --- | --- | --- | --- |
| Cihaz IoT Hub |DEĞERI |TID Trafiği şifrelemek için TLS (PSK/RSA) |Cihaz ve ağ geçidi arasındaki iletişimi dinleyen ya da engelliyor |Protokol düzeyinde güvenlik. Özel protokollerle bunları nasıl koruyacağınızı belirlemeniz gerekir. Çoğu durumda, iletişim cihazdan IoT Hub gerçekleşir (cihazın bağlantıyı başlattığı). |
| Cihazdan cihaza |DEĞERI |TID Trafiği şifrelemek için TLS (PSK/RSA). |Cihazlar arasında geçiş sırasında verileri okuma. Verilerle müdahale. Yeni bağlantılarla cihazı aşırı yükleme |Protokol düzeyinde güvenlik (MQTT/AMQP/HTTP/CoAP). Özel protokollerle bunları nasıl koruyacağınızı belirlemeniz gerekir. DoS tehdidi için risk azaltma, bir bulut veya alan ağ geçidi aracılığıyla cihazları eşler ve yalnızca ağa yönelik istemci olarak davranır. Eşleme, ağ geçidi tarafından aracılı yapıldıktan sonra eşler arasında doğrudan bağlantıya neden olabilir |
| Dış varlık cihazı |DEĞERI |Dış varlığın cihaza güçlü eşleştirme |Cihaza olan bağlantıyı dinleyen. Cihazla iletişimi engelliyor |Dış varlığı, NFC/Bluetooth LE cihaz ile güvenli bir şekilde eşleştiriliyor. Cihazın işletimsel panelini denetleme (fiziksel) |
| Alan ağ geçidi bulutu ağ geçidi |DEĞERI |Trafiği şifrelemek için TLS (PSK/RSA). |Cihaz ve ağ geçidi arasındaki iletişimi dinleyen ya da engelliyor |Protokol düzeyinde güvenlik (MQTT/AMQP/HTTP/CoAP). Özel protokollerle bunları nasıl koruyacağınızı belirlemeniz gerekir. |
| Cihaz bulutu ağ geçidi |DEĞERI |Trafiği şifrelemek için TLS (PSK/RSA). |Cihaz ve ağ geçidi arasındaki iletişimi dinleyen ya da engelliyor |Protokol düzeyinde güvenlik (MQTT/AMQP/HTTP/CoAP). Özel protokollerle bunları nasıl koruyacağınızı belirlemeniz gerekir. |

Bu kategoride tehditler için bazı örnekler aşağıda verilmiştir:

**Hizmet reddi**: kısıtlanmış cihazlar genellikle bir ağ üzerinde gelen bağlantıları veya istenmeyen veri birimlerini etkin bir şekilde dinlerken DOS tehdidi altındadır. Bu, bir saldırgan paralel olarak çok sayıda bağlantı açabildiğinde veya hizmet vermez yavaş veya cihaz, istenmeyen trafikle taşalabilir. Her iki durumda da cihaz, ağ üzerinde çalışamaz şekilde işlenebilir.

**Sızdırma, bilgilerin açıklanması**: kısıtlı cihazlar ve özel amaçlı cihazlar genellikle parola veya PIN koruması gibi bir veya ağa güvenen bir güvenlik özelliğine sahiptir ve bu durum, şu durumlarda bilgilere erişim izni verir bir cihaz aynı ağ üzerinde ve bu ağ genellikle yalnızca paylaşılan bir anahtarla korunur. Bu, cihaz veya ağ ile paylaşılan gizli dizi açıklandığında, cihazı denetlemek veya cihazdan alınan verileri gözlemlemek mümkündür.  

**Sızdırma**: bir saldırgan yayını ele geçirebilir veya kısmen geçersiz kılabilir ve kaynağı taklit edebilir (ortadaki Man)

**Değişiklik**: bir saldırgan yayını ele geçirebilir veya kısmen geçersiz kılabilir ve yanlış bilgi gönderebilir 

**Bilgilerin açıklanması:** bir saldırgan bir yayını yayında bırakabilir ve yetkilendirme **hizmeti 'nin reddedilmesine** gerek kalmadan bilgi alabilir: bir saldırgan, yayın sinyalini düşürebilir ve bilgi dağıtımını reddedebilir

#### <a name="storage"></a>Depolama

Her cihaz ve alan ağ geçidinin bir depolama biçimi vardır (verileri sıraya alma, işletim sistemi (OS) görüntü depolaması için geçici).

| **Bileşen** | **Tehdit** | **Mayı** | **Esini** | **Paylaşır** |
| --- | --- | --- | --- | --- |
| Cihaz depolama |TR |Depolama şifrelemesi, günlükleri imzalama |Depolama alanından (PII verileri) veri okuma, telemetri verileriyle müdahale etme. Kuyruğa alınmış veya önbelleğe alınmış komut denetimi verileriyle müdahale. Önbelleğe alınmış veya yerel olarak kuyruğa alınan yapılandırma ya da bellenim güncelleştirme paketlerinin güvenliği, işletim sistemi ve/veya sistem bileşenlerine yol açabilir |Şifreleme, ileti kimlik doğrulama kodu (MAC) veya dijital imza. Mümkün olduğunda, kaynak erişim denetim listeleri (ACL 'Ler) veya izinler aracılığıyla güçlü erişim denetimi. |
| Cihaz işletim sistemi görüntüsü |TR | |İşletim sistemi/IŞLETIM sistemi bileşenleri değiştirme |Salt okuma işletim sistemi bölümü, imzalanmış işletim sistemi görüntüsü, şifreleme |
| Alan ağ geçidi depolaması (verileri sıraya alma) |TR |Depolama şifrelemesi, günlükleri imzalama |Depolama alanından (PII verileri) veri okuma, telemetri verileriyle müdahale etme, kuyruğa alınmış veya önbelleğe alınmış komut denetimi verileriyle değişiklik. Önbelleğe alınmış veya yerel olarak sıraya alınmış bir yapılandırma ya da bellenim güncelleştirme paketi (cihazlar veya alan ağ geçidi için hedeflenen), işletim sistemi ve/veya sistem bileşenlerine yol açabilir |BitLocker |
| Alan ağ geçidi işletim sistemi görüntüsü |TR | |İşletim sistemi/IŞLETIM sistemi bileşenleri değiştirme |Salt okuma işletim sistemi bölümü, imzalanmış işletim sistemi görüntüsü, şifreleme |

### <a name="device-and-event-processingcloud-gateway-zone"></a>Cihaz ve olay işleme/bulut ağ geçidi bölgesi

Bulut ağ geçidi, genellikle bu tür sistemlerin bir Federasyonu olan bulut tabanlı bir denetim ve veri analizi sistemine bağlı olarak, ortak ağ alanı genelinde birden çok farklı siteden cihazlar veya alan ağ geçitleri arasında uzaktan iletişim sağlayan bir sistemdir. Bazı durumlarda, bir bulut ağ geçidi, tablet veya telefon gibi terminallerdeki özel amaçlı cihazlara erişimi hemen kolaylaştırabilir. Burada açıklanan bağlamda, "Cloud", bağlı cihazlar veya alan ağ geçitleri ile aynı siteye bağlı olmayan bir adanmış veri işleme sistemine başvurmaktır ve işlemsel ölçüler hedeflenen fiziksel erişimi engeller ancak " genel bulut "altyapısı. Bir bulut ağ geçidi, bulut ağ geçidini ve tüm bağlı cihazlarını veya alan ağ geçitlerini diğer ağ trafiğinden yalıtılmış olarak bir ağ sanallaştırma kaplamasına eşlenebilir. Bulut ağ geçidi, cihaz verileri için bir cihaz denetim sistemi veya işlem veya depolama olanağı değildir; bulut ağ geçidine sahip bu tesisler arabirimi. Bulut ağ geçidi bölgesi, tüm alan ağ geçitleri ve cihazlarıyla birlikte doğrudan veya dolaylı olarak bağlı olan bulut ağ geçidinin kendisini içerir.

Bulut ağ geçidi, alan ağ geçidi ve cihazların bağlandığı açık uç noktalara sahip bir hizmet olarak çalışan, genellikle özel bir yerleşik yazılım parçasıdır. Bu nedenle, güvenlik göz önünde bulundurularak tasarlanmalıdır. Bu hizmeti tasarlamak ve oluşturmak için [SDL](https://www.microsoft.com/sdl) işlemini izleyin.

#### <a name="services-zone"></a>Hizmetler bölgesi

Bir denetim sistemi (veya denetleyicisi) bir veya birden çok cihazı denetlemek ve/veya cihaz verilerini sunu ve/veya analiz etmek ve/veya çözümlemek için bir cihaz ya da bir alan ağ geçidi ya da bulut ağ geçidi olan bir yazılım çözümüdür ya da sonraki denetim amaçları. Denetim sistemleri, bu tartışma kapsamındaki tek varlıklardır ve kişilerle etkileşimi hemen kolaylaştırmaya yardımcı olabilir. Özel durumlar, bir kişinin cihazı kapatmasına veya diğer özellikleri değiştirmesine izin veren ve dijital olarak erişilebilen işlevsel eşdeğeri olmayan bir anahtar gibi cihazlarda bulunan ara fiziksel denetim yüzeyleridir.

Ara fiziksel denetim yüzeyleri, bir eşdeğer işlevin uzaktan başlatılabileceği ya da uzak girişle giriş çakışmalarının önlenebildiği gibi, fiziksel denetim yüzeyi işlevini kısıtlayan, Denetim yüzeyleri kavramsal olarak, cihazın paralel olarak bağlı olabileceği diğer herhangi bir uzaktan denetim sistemiyle aynı temel işlevsellikten yararlanan yerel bir denetim sistemine iliştirildi. Bulut bilgi işlem için en önemli tehditler, [Cloud Security Alliance (CSA)](https://cloudsecurityalliance.org/articles/csa-releases-top-threats-to-cloud-computing-deep-dive/) sayfasında okunabilir.

## <a name="additional-resources"></a>Ek kaynaklar

Daha fazla bilgi için aşağıdaki makalelere bakın:

* [SDL Threat Modeling Tool](https://www.microsoft.com/sdl/adopt/threatmodeling.aspx)
* [Microsoft Azure IoT başvuru mimarisi](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/)
