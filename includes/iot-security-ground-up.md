---
title: include dosyası
description: include dosyası
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 04/24/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 128a2e8b200f1323b88aad635f27c1b686ecbed2
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789709"
---
Nesnelerin İnterneti (IoT), dünya çapındaki işletmeler için benzersiz güvenlik, gizlilik ve uyumluluk sorunları doğurur. Bu sorunların yazılım etrafında ve nasıl uygulandıklarını gösteren geleneksel siber teknolojisinin aksine, IoT ve fiziksel çalışma LDS yakınlarından ne olur? IoT çözümlerini korumak, cihazların güvenli şekilde sağlanması, bu cihazlar ile bulut arasında güvenli bağlantı sağlanması ve işlem ve depolama sırasında buluttaki veri korumasının güvenliğini sağlamayı gerektirir. Bununla birlikte, bu işlevselliğe göre çalışarak kaynak kısıtlı cihazlar, dağıtımların coğrafi dağıtımı ve bir çözüm içinde çok sayıda cihaz vardır.

Bu makalede IoT çözüm Hızlandırıcıların güvenli ve özel bir Nesnelerin İnterneti bulut çözümü nasıl sağladığı anlatılmaktadır. Çözüm Hızlandırıcılar, uçtan uca her aşamada yerleşik olarak bulunan güvenlikle birlikte uçtan uca tam bir çözüm sunar. Microsoft 'ta, güvenli yazılım geliştirme, Microsoft 'un güvenli yazılım geliştirmeye yönelik uzun bir deneyimde olan yazılım mühendisliği uygulamasının bir parçasıdır. Bunu sağlamak için güvenlik geliştirme yaşam döngüsü (SDL), Işlemsel güvenlik güvencesi (OSA) ve Microsoft dijital Crimes birimi gibi altyapı düzeyinde güvenlik hizmetleri ile bağlanmış temel bir geliştirme yöntemsidir Güvenlik Yanıt Merkezi ve Microsoft kötü amaçlı yazılımdan koruma merkezi.

Çözüm Hızlandırıcılar, IoT cihazlarından veri sağlamayı, bağlanmayı ve depolamayı kolay ve saydam ve bunların çoğunu güvenli hale getirmeye yönelik benzersiz özellikler sunar. Bu makalede, güvenlik, gizlilik ve uyumluluk sorunlarını ele almak için Azure IoT Çözüm Hızlandırıcıları güvenlik özellikleri ve dağıtım stratejileri incelenir.

## <a name="introduction"></a>Tanıtım

Nesnelerin İnterneti (IoT), geleceğin, maliyetleri azaltmak, geliri artırmak ve işletmelerini dönüştürmek için işletmelerin anında ve gerçek dünya fırsatlarından oluşan bir tekliftir. Ancak pek çok işletme, güvenlik, gizlilik ve uyumluluk konusundaki kaygıları nedeniyle kuruluşlarında IoT 'yi dağıtmaya yönelik olarak tasarlanmıştır. Büyük bir sorun olduğu IoT altyapısının benzersiz bir noktası, siber ve fiziksel çalışma LDS 'yi birlikte birleştiren, bu iki çalışma LDS 'de bulunan ayrı riskleri kapsayan bir arada gelir. IoT güvenliği, cihazlarda çalışan kodun bütünlüğünü sağlamaya, cihaz ve Kullanıcı kimlik doğrulaması sağlamaya, cihazların temiz sahipliğini tanımlamaya (Bu aygıtlar tarafından oluşturulan verilerin yanı sıra bu cihazlar tarafından oluşturulan veriler) ve siber ve fiziksel saldırılara karşı dayanıklı olmasını sağlamaya aittir.

Bundan sonra gizlilik sorunu vardır. Şirketler, toplanan verilerin yanı sıra, kimin neleri görebileceğini, kimin erişimi denetlediğini vb. veri toplama ile ilgili saydamlığın olmasını ister. Son olarak, donatımın genel güvenlik sorunları ve bunları çalıştıran kişilerin yanı sıra endüstri uyumluluğuna bakım sorunları vardır.

Güvenlik, gizlilik, saydamlık ve uyumluluk sorunları söz konusu olduğunda, doğru IoT çözüm sağlayıcısı seçildiğinde bir zorluk ortadan kalmaktadır. Çeşitli satıcılar tarafından sunulan tek tek IoT yazılımı ve hizmet parçalarını birlikte toplayın, güvenlik, gizlilik, saydamlık ve uyumluluk bölümünde, algılanarak tek tek düzeltme sağlamak zor olabilecek boşluklar sağlar. Doğru IoT yazılım ve hizmet sağlayıcının seçimi, yoğun hizmetler çalıştıran, büyük ve coğrafi olarak yayılan, ancak aynı zamanda güvenli ve şeffaf bir biçimde ölçeklenebilen sağlayıcıları bulmaya dayalıdır. Benzer şekilde, seçili sağlayıcının milyarlarca makine üzerinde çalışan güvenli yazılım geliştirme ve bu yeni dünyanın bu yeni Nesnelerin İnterneti dünyasının ortaya geldiği tehdidin bir deneyim yaşamasına yardımcı olur.

## <a name="secure-infrastructure-from-the-ground-up"></a>Baştan sona güvenli altyapı

[Microsoft bulut](https://azure.microsoft.com) altyapısı, 127 ülkede/bölgede 1.000.000.000 'den fazla müşteriyi destekler. Microsoft 'un kurumsal yazılım oluşturma ve dünyanın en büyük çevrimiçi hizmetler sürümlerini çalıştırma konusunda çizim yaparken, Microsoft Bulut, gelişmiş güvenlik, gizlilik, uyumluluk ve tehdit azaltma uygulamalarının daha yüksek düzeylerini sağlar birçok müşterinin kendi kendine ulaşabilme.

[Güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl/) , güvenlik gereksinimlerini tüm yazılım yaşam döngüsüne katıştıran zorunlu bir şirket genelinde geliştirme süreci sağlar. SDL, işlemsel etkinliklerin güvenlik uygulamalarının aynısını izlemesini sağlamak için Microsoft 'un Işletimsel güvenlik güvencesi (OSA) sürecinde oluşan kapsamlı güvenlik kurallarını kullanır. Microsoft, uyumluluk yükümlülükleriyle buluşduğu ve Microsoft dijital Crimes birimi de dahil olmak üzere üstün seviyeler oluşturulmasıyla ilgili geniş güvenlik çabalarıyla birlikte çalışarak, üçüncü taraf denetim firmaları ile de aynı zamanda, Microsoft Güvenlik Yanıt Merkezi ve Microsoft kötü amaçlı yazılımdan koruma merkezi.

## <a name="microsoft-azure---secure-iot-infrastructure-for-your-business"></a>İşletmeniz için Microsoft Azure güvenli IoT altyapısı

Microsoft Azure, sürekli büyüyen bir tümleşik bulut hizmetleri koleksiyonunu (analiz, makine öğrenimi, depolama, güvenlik, ağ iletişimi ve Web), korumaya yönelik sektörde önde gelen bir taahhütte birleştiren tam bir bulut çözümü sunar. verilerinizin gizliliği. Microsoft 'un [ihlal](https://azure.microsoft.com/blog/red-teaming-using-cutting-edge-threat-simulation-to-harden-the-microsoft-enterprise-cloud/) etme stratejisi, saldırıları taklit eden, Azure 'un algılaması yeteneğini test etmeyi, gelişmekte olan tehditlere karşı korumayı ve ihlallerden kurtulmasını sağlayan, özel bir *Red ekibi* kullanır. Microsoft 'un [küresel olay yanıtı](https://www.microsoft.com/en-us/TrustCenter/Security/DesignOpSecurity) ekibi, saldırıları ve kötü amaçlı etkinliklerin etkilerini azaltmak için saatin etrafında çalışmaktadır. Takım, olay yönetimi, iletişim ve kurtarma için kurulan yordamları izler ve iç ve dış iş ortakları ile keşfedilebilir ve öngörülebilir arabirimler kullanır.

Microsoft 'un sistemleri sürekli yetkisiz giriş algılama ve önleme, hizmet saldırısı önleme, düzenli sızma testi ve tehditleri belirlemenize ve hafifletmeye yardımcı olan adli araçları sağlar. [Multi-Factor Authentication](../articles/active-directory/authentication/multi-factor-authentication.md) , son kullanıcıların ağa erişmesi için ek bir güvenlik katmanı sağlar. Hem uygulama hem de ana bilgisayar sağlayıcısı için Microsoft, erişim denetimi, izleme, kötü amaçlı yazılımdan koruma, güvenlik açığı taraması, düzeltme ekleri ve yapılandırma yönetimini sunmaktadır.

Çözüm Hızlandırıcıları, Azure platformunda yerleşik olarak bulunan güvenlik ve gizliliğinizin yanı sıra, tüm Microsoft yazılımlarının güvenli geliştirilmesi ve çalışması için SDL ve OSA işlemleriyle birlikte yararlanır. Bu yordamlar, herhangi bir çözümün güvenliğine temel olarak altyapı koruması, ağ koruması ve kimlik ve yönetim özellikleri sağlar.

[IoT Çözüm Hızlandırıcıları](../articles/iot-fundamentals/iot-introduction.md) içindeki [Azure IoT Hub](../articles/iot-hub/about-iot-hub.md) , IoT cihazları ve [Azure Machine Learning](../articles/machine-learning/studio/what-is-machine-learning.md) [gibi Azure hizmetleri arasında güvenilir ve güvenli çift yönlü iletişim sağlayan, tam olarak yönetilen bir hizmet sunar. ](../articles/stream-analytics/stream-analytics-introduction.md)Cihaz başına güvenlik kimlik bilgileri ve erişim denetimi kullanarak Azure Stream Analytics.

Azure IoT çözüm hızlandırıcılarına yerleşik olarak bulunan güvenlik ve gizlilik özelliklerini en iyi şekilde iletmek için, bu makale, paketi üç birincil güvenlik alanına ayırır.

![Azure IoT çözüm hızlandırıcıları](media/iot-security-ground-up/securing-iot-ground-up-fig3.png)

### <a name="secure-device-provisioning-and-authentication"></a>Güvenli cihaz sağlama ve kimlik doğrulama

Çözüm, her bir cihaz için, işlem sırasında cihazla iletişim kurmak üzere IoT altyapısı tarafından kullanılabilen her bir cihaz için benzersiz bir kimlik anahtarı sunarak cihazları güvenli hale getirmeye karşı hızlandırırlar. İşlem hızlı ve kolay bir şekilde ayarlanır. Kullanıcı tarafından seçilen bir cihaz KIMLIĞIYLE oluşturulan anahtar, cihaz ve Azure IoT Hub arasındaki tüm iletişimde kullanılan bir belirtecin temelini oluşturur.

Cihaz kimlikleri, üretim sırasında bir cihazla ilişkilendirilebilir (yani, bir donanım güven modülünde düzden) veya mevcut bir sabit kimliği ara sunucu (örneğin, CPU seri numarası) olarak kullanabilir. Cihazdaki bu tanımlama bilgilerinin değiştirilmesi basit olmadığından, temeldeki cihaz donanımının değiştiği ancak mantıksal cihazın aynı kalması durumunda mantıksal cihaz kimliklerini tanıtmak önemlidir. Bazı durumlarda, cihaz kimliği ilişkilendirmesi cihaz dağıtım zamanında gerçekleşebilir (örneğin, kimliği doğrulanmış bir alan Mühendisi, çözüm arka ucu ile iletişim kurarken fiziksel olarak yeni bir cihaz yapılandırır). [Azure IoT Hub Identity kayıt defteri](../articles/iot-hub/iot-hub-devguide.md) , bir çözüm için cihaz kimliklerinin ve güvenlik anahtarlarının güvenli bir şekilde depolanmasını sağlar. Bir grup veya cihaz kimliği grubu, cihaz erişimi üzerinde komple denetim sağlayan bir izin verilenler listesine veya engellenenler listesine eklenebilir.

Bulutta Azure IoT Hub Access Control Policies, etkinleştirmeyi etkinleştirin ve cihaz kimliklerini devre dışı bırakarak, gerektiğinde bir cihazın IoT dağıtımıyla ilişkilendirmesini kaldırmak için bir yol sağlar. Cihazların bu ilişkisi ve ilişkilendirmesi, her bir cihaz kimliğini temel alır.

Ek cihaz güvenlik özellikleri şunlardır:

* Cihazlar, istenmeyen ağ bağlantılarını kabul etmez. Tüm bağlantıları ve yolları yalnızca giden bir biçimde oluştururlar. Bir cihazın arka uca bir komut alabilmesi için cihazın, bekleyen komutların işlemesini denetlemek üzere bir bağlantı başlatması gerekir. Cihaz ve IoT Hub arasındaki bir bağlantı güvenli bir şekilde kurulduktan sonra, buluttan cihaza ve cihaza olan iletiler saydam olarak gönderilebilir.

* Cihazlar yalnızca, bir Azure IoT Hub gibi, eşlendikleri bilinen hizmetlere giden yollara bağlanır veya yollar kurar.

* Sistem düzeyinde yetkilendirme ve kimlik doğrulama cihaz başına kimlikler kullanır, erişim kimlik bilgileri ve izinleri anında geri alınamaz.

### <a name="secure-connectivity"></a>Güvenli bağlantı

İleti dayanıklılığı, herhangi bir IoT çözümünün önemli bir özelliğidir. Komut ve/veya cihazlardan veri alma gereksinimi, IoT cihazlarının Internet üzerinden bağlı olması veya güvenilir olmayan diğer benzer ağlar tarafından altı çizili hale getirilmez. Azure IoT Hub, iletilerle yanıt olarak bir bildirimler sistemi aracılığıyla bulut ve cihazlar arasında ileti alma dayanıklılığı sağlar. Mesajlaşma için ek dayanıklılık, IoT Hub telemetri için yedi güne kadar ve komutlar için iki gün boyunca önbelleğe alınmış olarak gerçekleştirilir.

Kaynak kısıtlanmış bir ortamda kaynakların ve işlemin tasarrufunu sağlamak için verimlilik önemlidir. Popüler http protokolünün sektör standardı güvenli sürümü olan HTTPS (HTTP Secure), Azure IoT Hub tarafından desteklenir ve verimli iletişim sağlar. Azure IoT Hub tarafından desteklenen Gelişmiş İleti Sıraya Alma Protokolü (AMQP) ve Message Queuing telemetri aktarımı (MQTT), yalnızca kaynak kullanımı ve ayrıca güvenilir ileti teslimi açısından verimlilik için tasarlanmıştır. 

Ölçeklenebilirlik, çok çeşitli cihazlarla güvenle birlikte çalışabilme olanağı gerektirir. Azure IoT Hub, IP özellikli ve IP özellikli olmayan cihazlara güvenli bağlantı sağlar. IP özellikli cihazlar, güvenli bir bağlantı üzerinden IoT Hub doğrudan bağlanabilir ve iletişim kurabilir. IP etkin olmayan cihazlar, kaynak sınırlamalı ve yalnızca zWAVE, ZigBee ve Bluetooth gibi kısa mesafeli iletişim protokolleriyle bağlanır. Bu cihazları toplamak için bir alan ağ geçidi kullanılır ve bulut ile güvenli çift yönlü iletişimi etkinleştirmek için protokol çevirisi gerçekleştirir.

Ek bağlantı güvenliği özellikleri şunlardır:

* Cihazlar ve Azure IoT Hub arasındaki iletişim yolu veya ağ geçitleri ile Azure IoT Hub arasında, X. 509.440 protokolü kullanılarak Azure IoT Hub kimliği doğrulanmış endüstri standardı Aktarım Katmanı Güvenliği (TLS) kullanılarak güvenliği sağlanır.

* Cihazları istenmeyen gelen bağlantılardan korumak için Azure IoT Hub cihazla hiçbir bağlantı açmaz. Cihaz tüm bağlantıları başlatır.

* Azure IoT Hub, cihazların iletilerini depolar ve cihazın bağlanmasını bekler. Bu komutlar, güç veya bağlantı sorunları nedeniyle, bu komutları almak için iki gün boyunca depolanır. Azure IoT Hub her cihaz için cihaz başına kuyruğu korur.

### <a name="secure-processing-and-storage-in-the-cloud"></a>Bulutta güvenli işleme ve depolama

Bulutta Verileri işlemeye yönelik şifreli iletişimlerden, çözüm Hızlandırıcılar, verilerin güvende tutulmasına yardımcı olur. Güvenlik anahtarlarının ek şifrelemesini ve yönetimini uygulamak için esneklik sağlar.

Kullanıcı kimlik doğrulaması ve yetkilendirme için Azure Active Directory (AAD) kullanan Azure IoT Çözüm Hızlandırıcıları, buluttaki veriler için ilke tabanlı bir yetkilendirme modeli sunarak, denetlenebilecek ve incelenebilecek kolay erişim yönetimini sağlar. Bu model ayrıca buluttaki verilere ve Azure IoT çözüm hızlandırıcılarına bağlı cihazlara neredeyse anında iptal yapılmasını sağlar.

Veriler bulutta olduktan sonra, Kullanıcı tanımlı herhangi bir iş akışında işlenebilir ve depolanabilir. Verilerin her bir kısmına erişim, kullanılan depolama hizmetine bağlı olarak Azure Active Directory ile denetlenir.

IoT altyapısı tarafından kullanılan tüm anahtarlar, güvenli depolamada bulutta depolanır ve bu durumda, anahtarların yeniden sağlanması gerekir. Veriler [Azure Cosmos DB](../articles/cosmos-db/introduction.md) veya [SQL veritabanlarında](../articles/sql-database/sql-database-faq.md)depolanabilir ve bu da, istenen güvenlik düzeyinin tanımını etkinleştirir. Ayrıca Azure, herhangi bir yetkisiz erişim veya yetkisiz erişim konusunda sizi uyarmak için verilerinize yönelik tüm erişimleri izleyip denetlemek için bir yol sağlar.

## <a name="conclusion"></a>Sonuç

Nesnelerin İnterneti, işletmelerden en çok önemli olan şeyler ile başlar. IoT, maliyetleri azaltarak, geliri artırarak ve iş dönüştürerek bir işletmeye harika değer sunabilir. Bu dönüştürmenin başarısı büyük ölçüde doğru IoT yazılım ve hizmet sağlayıcısına bağlı olarak değişir. Bu, yalnızca iş ihtiyaçlarını ve gereksinimlerini anlayarak bu dönüşüme catalyzes bir sağlayıcı bulma, Ayrıca önemli tasarım konuları olarak güvenlik, gizlilik, saydamlık ve uyumluluk ile oluşturulan hizmet ve yazılımları da sağlar. Microsoft, güvenli yazılım ve Hizmetleri geliştirmeyle ve dağıtmaya yönelik kapsamlı bir deneyimle sahiptir ve bu yeni Nesnelerin İnterneti yaşına lider olmaya devam eder.

Çözüm Hızlandırıcıları, tasarıma göre güvenlik önlemleri oluşturur, verimliliği artırmak için varlıkların güvenli bir şekilde izlenmesini sağlar, yeniliklere olanak tanımak için işletimsel performansı ister ve işletmeleri dönüştürmek için gelişmiş veri analizi sağlar. Güvenlik, birden çok güvenlik özelliği ve tasarım desenlerine yönelik katmanlı yaklaşımla, çözüm Hızlandırıcılar, herhangi bir işi dönüştürmek üzere güvenilebildiğiniz bir altyapıyı dağıtmaya yardımcı olur.

## <a name="additional-information"></a>Ek Bilgi

Her çözüm hızlandırıcısı, Azure Hizmetleri örnekleri oluşturur, örneğin:

* [**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/): bulutu cihazlara bağlayan ağ geçidiniz. Her Hub için milyonlarca bağlantı ölçeklendirebilir ve çözümü güvenli hale getirmeye yardımcı olmak için cihaz başına kimlik doğrulama desteğiyle büyük hacimlerde veri işleyebilirsiniz.

* [**Azure Cosmos DB**](https://azure.microsoft.com/services/cosmos-db/): öznitelik, yapılandırma ve güvenlik özellikleri gibi sağladığınız cihazların meta verilerini yöneten yarı yapılandırılmış veriler için ölçeklenebilir, tam dizinli bir veritabanı hizmetidir. Azure Cosmos DB yüksek performanslı ve yüksek performanslı işleme, verilerin şemaya belirsiz dizin oluşturma ve zengin bir SQL sorgu arabirimi sunar.

* [**Azure Stream Analytics**](https://azure.microsoft.com/services/stream-analytics/): bulutta, algılayıcılardan, altyapıdan ve uygulamalardan gerçek zamanlı içgörüler elde etmek için hızlı bir şekilde düşük maliyetli bir analiz çözümü geliştirmenize ve dağıtmanıza olanak tanıyan gerçek zamanlı akış işleme. Bu tam olarak yönetilen hizmetin verileri, yüksek aktarım hızı, düşük gecikme süresi ve dayanıklılık sağlarken herhangi bir birime ölçeklendirebilir.

* [**Azure Uygulama Hizmetleri**](https://azure.microsoft.com/services/app-service/): her yerden verilere bağlanan güçlü web uygulamaları ve mobil uygulamalar oluşturmak için bir bulut platformudur; bulutta veya şirket içinde. iOS, Android ve Windows için ilgi çekici mobil uygulamalar oluşturun. Hizmet olarak yazılım (SaaS) ve kurumsal uygulamalarla, onlarca bulut tabanlı hizmetlere ve kurumsal uygulamalara yönelik kullanıma hazır bağlantı ile tümleştirin. Web uygulamaları ve API 'Leri her zamankinden daha hızlı derlemek için en sevdiğiniz dil ve IDE (.NET, Node. js, PHP, Python veya Java) kodu.

* [**Logic Apps**](https://azure.microsoft.com/services/app-service/logic/): Azure App Service Logic Apps özelliği, IoT çözümünüzü mevcut iş kolu sistemlerinizle tümleştirmenize ve iş akışı süreçlerini otomatikleştirmenize yardımcı olur. Logic Apps, geliştiricilerin bir tetikleyiciden başlayan iş akışlarını tasarlamasına ve sonra iş süreçlerinizle tümleştirilecek güçlü bağlayıcılar kullanan kurallar ve eylemler yürütebilmesini sağlar. Logic Apps SaaS, bulut tabanlı ve şirket içi uygulamalarla büyük bir geniş ekosisteme kullanıma hazır bağlantı sunar.

* [**Azure Blob depolama**](https://azure.microsoft.com/services/storage/): cihazlarınızın buluta gönderdikleri veriler için güvenilir, ekonomik bulut depolama.