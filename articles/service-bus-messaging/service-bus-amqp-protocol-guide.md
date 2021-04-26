---
title: Azure Service Bus ve Event Hubs protokol kılavuzunda AMQP 1,0 | Microsoft Docs
description: Azure Service Bus ve Event Hubs AMQP 1,0 ifadelerine ve açıklamasına yönelik protokol Kılavuzu
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 8d346aeef74e1f67d3d525c061d40314ee5342aa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531016"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>Azure Service Bus ve Event Hubs protokol kılavuzunda AMQP 1,0

Gelişmiş Ileti sıraya alma protokolü 1,0, zaman uyumsuz, güvenli ve iletileri iki taraf arasında güvenilir bir şekilde aktarmak için standartlaştırılmış bir çerçeveleme ve aktarım protokolüdür. Azure Service Bus mesajlaşma ve Azure Event Hubs 'in birincil protokolüdür.  

AMQP 1,0, Microsoft ve Red hat gibi bir ara yazılım satıcılarının yanı sıra, finansal hizmetler sektörünü temsil eden JP Morgan Chase gibi çok sayıda mesajlaşma ara yazılımı sunan geniş sektör işbirliğinin sonucudur. AMQP Protokolü ve uzantı belirtimleri için teknik standartlaştırma Forumu OASDıR ve ISO/ıEC 19494:2014 olarak uluslararası bir standart olarak resmi onay elde etti. 

## <a name="goals"></a>Hedefler

Bu makale, AMQP 1,0 mesajlaşma belirtiminin, [Oasin AMQP Technical komite](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=amqp) tarafından geliştirilen uzantı belirtimleri ve bu belirtimlere nasıl uyguladığı ve bu belirtimlerde nasıl Azure Service Bus açıklar hakkında temel kavramları özetler.

Amaç, AMQP 1,0 aracılığıyla Azure Service Bus etkileşime girebilmek için herhangi bir platformda var olan AMQP 1,0 istemci yığınını kullanan herhangi bir geliştirici içindir.

[Apache Qpid proton](https://qpid.apache.org/proton/index.html) veya [AMQP.net Lite](https://github.com/Azure/amqpnetlite)gibi yaygın genel amaçlı AMQP 1,0 yığınları, oturumlar veya bağlantılar gibi tüm çekirdek AMQP 1,0 protokol öğelerini uygular. Bu temel öğeler bazen daha yüksek düzey bir API ile sarmalanır; Apache proton, iki zorunlu Messenger API 'SI ve reaktif aktör API 'SI de sunar.

Aşağıdaki tartışmada, AMQP bağlantıları, oturumlarının ve bağlantıların yönetiminin yanı sıra çerçeve aktarımlarının ve akış denetiminin işleme ilgili yığın tarafından (Apache proton-C gibi) işlendiğini ve uygulama geliştiricilerinden belirli bir ilgi olması gerekmemesi gerektiğini varsaydık. Bağlama özelliği gibi birkaç API temel özelliğinin varlığını ve daha sonra bazı ve işlem şekilleri ve işlemler içeren bir *Gönderen* ve *alıcı* soyutlama nesneleri formunu oluşturmayı soyutlıyoruz `send()` `receive()` .

İleti tarama veya oturumların yönetimi gibi Azure Service Bus gelişmiş özellikleri tartışılırken, bu özellikler AMQP terimlerinde açıklanmıştır, ancak aynı zamanda bu kabul edilen API soyutlamalarından oluşan katmanlı bir sözde uygulama olarak açıklanmaktadır.

## <a name="what-is-amqp"></a>AMQP nedir?

AMQP bir çerçeveleme ve aktarım protokolüdür. Çerçeveleme, bir ağ bağlantısının her iki yönünde akan ikili veri akışları için yapı sağladığı anlamına gelir. Yapı, bağlı taraflar arasında değiş tokuş edilecek şekilde, *çerçeveler* olarak adlandırılan farklı veri blokları için delineation sağlar. Aktarım Özellikleri, hem iletişim kuran tarafların çerçevelerin ne zaman aktarılacağı hakkında paylaşılan bir anlama kurabildiğini ve aktarımların ne zaman tamamlandığının kabul edileceğini de unutmayın.

Birkaç ileti Aracısı tarafından hala kullanımda olan AMQP çalışma grubu tarafından üretilen erken kullanım dışı olan taslak sürümlerinin aksine, çalışma grubunun son ve standartlaştırılmış AMQP 1,0 protokolü, bir ileti aracısının veya bir ileti aracısının içindeki varlıklar için belirli bir topolojinin olmaması halinde değildir.

Protokol, Azure Service Bus olduğu gibi kuyrukları ve yayımlama/abone olma varlıkları destekleyen ileti aracılarıyla etkileşim kurmak için simetrik eşler arası iletişim için kullanılabilir. Azure Event Hubs olduğu gibi, etkileşim desenlerinin normal kuyruklardan farklı olduğu mesajlaşma altyapısıyla etkileşim için de kullanılabilir. Bir olay hub 'ı, olaylar kendisine gönderildiğinde bir sıra gibi davranır, ancak olaylar bundan okunmadığında bir seri depolama hizmeti gibi davranır; bir bant sürücüsüne benzer. İstemci, kullanılabilir veri akışına bir konum seçer ve daha sonra bu uzaklığa ait tüm olayları mevcut en son kullanılabilir hale görür.

AMQP 1,0 protokolü genişletilebilir olacak şekilde tasarlanmıştır ve yeteneklerini geliştirmek için daha fazla belirtim sağlar. Bu belgede ele alınan üç uzantı belirtimleri bunu gösterir. Mevcut HTTPS/WebSockets altyapısı üzerinden iletişim için yerel AMQP TCP bağlantı noktalarını yapılandırmak zor olabilir. Bağlama belirtimi, AMQP 'nin WebSockets üzerinden nasıl gösterileceğini tanımlar. Yönetim amaçlarıyla bir istek/yanıt olarak mesajlaşma altyapısına etkileşim kurmak veya gelişmiş işlevsellik sağlamak için AMQP yönetim belirtimi gerekli temel etkileşim temel öğelerini tanımlar. Federal yetkilendirme modeli tümleştirmesi için AMQP talepler tabanlı güvenlik belirtimi, bağlantılarla ilişkili yetkilendirme belirteçlerini ilişkilendirmeyi ve yenilemeyi tanımlar.

## <a name="basic-amqp-scenarios"></a>Temel AMQP senaryoları

Bu bölümde, bağlantı, oturum ve bağlantı oluşturmayı ve kuyruklar, konular ve abonelikler gibi Service Bus varlıklara ve bunlara ileti aktarmayı içeren Azure Service Bus ile AMQP 1,0 temel kullanımı açıklanmaktadır.

AMQP 'nin nasıl çalıştığı hakkında bilgi edinmek için en yetkili Kaynak [amqp 1,0 belirtimidir](http://docs.oasis-open.org/amqp/core/v1.0/amqp-core-overview-v1.0.html), ancak bu belirtim, protokolü öğretmek için tam olarak kılavuz uygulamaya yazıldı. Bu bölüm, Service Bus AMQP 1,0 ' i nasıl kullandığını açıklamak için gereken çok sayıda terminoloji konusuna odaklanır. AMQP 'ye daha kapsamlı bir giriş ve AMQP 1,0 hakkında daha geniş bir açıklama için [Bu video kursu][this video course]inceleyebilirsiniz.

### <a name="connections-and-sessions"></a>Bağlantılar ve oturumlar

AMQP, iletişim programları *kapsayıcılarını* çağırır; Bunlar, bu kapsayıcıların içindeki iletişim kuran varlıklar olan *düğümleri* içerir. Kuyruk böyle bir düğüm olabilir. AMQP çoğullama için izin veriyor, bu nedenle düğümler arasındaki birçok iletişim yolu için tek bir bağlantı kullanılabilir; Örneğin, bir uygulama istemcisi aynı anda bir kuyruktan alabilir ve aynı ağ bağlantısı üzerinden başka bir sıraya de gönderebilir.

![Kapsayıcılar arasındaki oturumları ve bağlantıları gösteren diyagram.][1]

Ağ bağlantısı bu nedenle kapsayıcıya bağlanır. Alıcı rolündeki kapsayıcı tarafından, gelen TCP bağlantılarını dinleyen ve kabul eden bir kapsayıcıya giden TCP yuvası bağlantısı kuran istemci rolünde kapsayıcı tarafından başlatılır. Bağlantı el sıkışması, protokol sürümü için anlaşma, aktarım düzeyi güvenliği (TLS/SSL) ve bağlantı kapsamındaki bir kimlik doğrulama/yetkilendirme el sıkışması ve SASL 'yi temel alır.

Azure Service Bus her zaman TLS kullanımını gerektirir. TCP bağlantı noktası 5671 üzerinden bağlantıları destekler, bu, TCP bağlantısının AMQP protokol el sıkışması girmeden önce TLS ile üzeri olduğu ve ayrıca sunucunun AMQP tarafından önceden tanımlanmış model kullanılarak TLS ile bağlantı için zorunlu bir yükseltme sunduğu TCP bağlantı noktası 5672 üzerinden bağlantı kurulmasını destekler. AMQP WebSockets Binding, daha sonra AMQP 5671 bağlantılarına denk gelen TCP bağlantı noktası 443 üzerinden bir tünel oluşturur.

Bağlantıyı ve TLS 'yi ayarladıktan sonra Service Bus iki SASL mekanizması seçeneği sunar:

* SASL PLAIN, genellikle bir sunucuya Kullanıcı adı ve parola kimlik bilgilerini geçirmek için kullanılır. Service Bus hesaplara sahip değildir, ancak hakları olan ve bir anahtarla ilişkilendirilen [paylaşılan erişim güvenliği kuralları](service-bus-sas.md)olarak adlandırılır. Kullanıcı adı olarak bir kuralın adı ve anahtar (Base64 kodlamalı metin olarak) parola olarak kullanılır. Seçili kuralla ilişkili haklar bağlantıda izin verilen işlemleri yönetir.
* SASL anonım, istemci daha sonra açıklanan talep tabanlı güvenlik (CBS) modelini kullanmak istediğinde SASL yetkilendirmesini atlamak için kullanılır. Bu seçenekle, istemcinin yalnızca CBS uç noktasıyla etkileşime girebileceği ve CBS el sıkışması işleminin tamamlanabilmesi için kısa bir süre için bir istemci bağlantısı anonim olarak oluşturulabilir.

Aktarım bağlantısı kurulduktan sonra, kapsayıcılar, her biri işlemek istedikleri maksimum kare boyutunu bildirir ve bir boşta kalma zaman aşımından sonra bağlantı üzerinde hiçbir etkinlik yoksa bağlantıyı kesmeniz tek taraflı.

Ayrıca, kaç tane eşzamanlı kanal desteklendiğini de bildirir. Kanal, bağlantının en üstünde tek yönlü, giden, sanal bir aktarım yoludur. Oturum, iki yönlü iletişim yolu oluşturmak için, birbirine bağlı kapsayıcıların her birinden bir kanal alır.

Oturumlarda pencere tabanlı akış denetim modeli vardır; bir oturum oluşturulduğunda her bir taraf alma penceresinde kabul etmek için kaç kare kabul etmek istediğinizi bildirir. Taraflar Exchange çerçeveleri olarak, aktarılan çerçeveler, pencere dolduğunda pencere ve aktarımların durmasına ve bu pencere, *akış* kullanımı kullanılarak sıfırlanmaya veya genişletilene kadar, bu pencereyi ve bu zaman, bu iki taraf arasında değiş tokuş edilen protokol düzeyi hareketleri için AMQP *terimidir.*

Bu pencere tabanlı model, yaklaşık olarak pencere tabanlı akış denetimi için TCP kavramıyla benzerdir, ancak yuva içindeki oturum düzeyindedir. Protokolün çok eş zamanlı oturumlara izin verme kavramı, yüksek öncelikli trafiğin, üst düzey bir hızlı kulvar üzerinde olduğu gibi, daha fazla kısıtlanmış normal trafikle devam edebilmesi için vardır.

Azure Service Bus Şu anda her bağlantı için tam olarak bir oturum kullanır. En büyük Service Bus çerçeve boyutu Service Bus standart için 262.144 bayttır (256-K bayt). Service Bus Premium ve Event Hubs için 1.048.576 (1 MB) olur. Service Bus herhangi bir oturum düzeyi daraltma penceresi uygulamaz, ancak bağlantı düzeyi akış denetimi kapsamında pencereyi düzenli olarak sıfırlar ( [sonraki bölüme](#links)bakın).

Bağlantılar, kanallar ve oturumlar kısa ömürlü. Temeldeki bağlantı daraltıladıysanız, bağlantılar, TLS tüneli, SASL yetkilendirme bağlamı ve oturumlarının yeniden kurulması gerekir.

### <a name="amqp-outbound-port-requirements"></a>AMQP giden bağlantı noktası gereksinimleri

TCP üzerinden AMQP bağlantıları kullanan istemciler, yerel güvenlik duvarında açılan 5671 ve 5672 bağlantı noktalarını gerektirir. Bu bağlantı noktalarıyla birlikte, [Enablelinkredirect](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.enablelinkredirect) özelliği etkinse ek bağlantı noktaları açmanız gerekebilir. `EnableLinkRedirect` , iletileri alırken tek bir atlama atlanmasına yardımcı olan yeni bir mesajlaşma özelliğidir ve bu sayede aktarım hızını artırır. İstemci, aşağıdaki görüntüde gösterildiği gibi, 104XX bağlantı noktası aralığı üzerinden doğrudan arka uç hizmetiyle iletişim kurmaya başlayacaktır. 

![Hedef bağlantı noktalarının listesi][4]

Bu bağlantı noktaları güvenlik duvarı tarafından engelleniyorsa, bir .NET istemcisi bir SocketException ("erişim izinleri tarafından yasaklanmış bir şekilde bir yuvaya erişmek için girişimde bulunuldu") ile başarısız olur. Bu özellik bağlantı dizesinde ayarı yaparak devre dışı bırakılabilir `EnableAmqpLinkRedirect=false` ve bu da istemcilerin 5671 numaralı bağlantı noktası üzerinden uzak hizmetle iletişim kurmasına zorlar.


### <a name="links"></a>Bağlantılar

AMQP iletileri bağlantılar üzerinden aktarır. Bağlantı, bir oturum üzerinden oluşturulan ve iletilerin bir yönde aktarılmasını sağlayan bir iletişim yoludur; Aktarım durumu anlaşması bağlantının üzerinde ve bağlı taraflar arasında çift yönlü olur.

![İki kapsayıcı arasında bağlantı bağlantısı taşıyan oturumu gösteren ekran görüntüsü.][2]

Bağlantılar, her zaman kapsayıcı tarafından herhangi bir zamanda ve var olan bir oturum üzerinden oluşturulabilir, bu da, aktarım ve aktarım yolunun başlatılması, yuva bağlantısını oluşturan tarafın özel bir ayrıcalıksıdır.

Bağlantı başlatma kapsayıcısı, karşı kapsayıcının bir bağlantıyı kabul etmesini ve gönderici ya da alıcının bir rolünü seçip seçtiğine ilişkin bir rol ister. Bu nedenle, her iki kapsayıcı da, son modellenen bağlantı çiftleri ile tek yönlü veya çift yönlü iletişim yolları oluşturmayı başlatabilir.

Bağlantılar adlandırılmış ve düğümlerle ilişkili. Başlangıçta belirtildiği gibi düğümler, bir kapsayıcı içindeki iletişim varlıklarıdır.

Service Bus, bir düğüm bir kuyruk, konu başlığı, abonelik ya da bir kuyruğun ya da aboneliğin bir veya daha fazla alt sırayı doğrudan eşdeğerdir. Bu nedenle, AMQP 'de kullanılan düğüm adı, Service Bus ad alanı içindeki varlığın göreli adıdır. Bir sıra adlandırılmışsa `myqueue` , bu da AMQP düğüm adıdır. Konu aboneliği, HTTP API kuralını bir "abonelikler" kaynak koleksiyonu olarak sıralayarak izler ve bu nedenle, **MyTopic** adlı bir abonelik **Sub** , AMQP düğüm adı **MyTopic/abonelikler/Sub** olur.

Bağlanan istemci, bağlantı oluşturmak için yerel bir düğüm adı kullanmak için de gereklidir; Service Bus bu düğüm adlarıyla ilgili değildir ve bunları yorumlamaz. AMQP 1,0 istemci yığınları genellikle bu kısa ömürlü düğüm adlarının istemci kapsamında benzersiz olmasını güvence altına almak için bir düzen kullanır.

### <a name="transfers"></a>Girişinde

Bir bağlantı kurulduktan sonra iletiler bu bağlantı üzerinden aktarılabilir. AMQP 'de bir aktarım, bir iletiyi gönderenden bir bağlantı üzerinden alıcıya taşıyan bir açık protokol hareketi ( *Aktarım* performansı) ile yürütülür. Bir aktarım "kapatıldığı" anlamına gelir, yani her iki taraf da bu aktarımın sonucunu bir paylaşılan olarak öğrenmiş olur.

![Gönderen ve alıcı ile bunun sonucunda elde edilen bir ileti arasındaki aktarımı gösteren bir diyagram.][3]

En basit durumda, gönderen iletileri "önceden kapatılmış" olarak, istemcinin sonuç ile ilgilenmediği ve alıcının işlemin sonucu hakkında herhangi bir geri bildirim sağlamayan anlamına gelir. Bu mod, AMQP protokol düzeyinde Service Bus desteklenir, ancak istemci API 'Lerinden hiçbirinde gösterilmez.

Normal durumda, iletilerin kapanmamış olarak gönderilmesi ve alıcı, daha sonra *değerlendirme* ve ret kullanımını kabul veya reddetme olduğunu gösterir. Reddetme, alıcı herhangi bir nedenden dolayı iletiyi kabul edemediğinde oluşur ve reddetme iletisi AMQP tarafından tanımlanan bir hata yapısı olan neden hakkında bilgi içerir. Service Bus içindeki iç hatalar nedeniyle iletiler reddedildiyse, hizmet, destek istekleri dosyayorsanız personeli desteklemek için tanılama ipuçları sağlamak üzere kullanılabilecek ek bilgileri bu yapıda geri döndürür. Hatalar hakkında daha sonra hatalarla ilgili daha fazla bilgi edinebilirsiniz.

Özel bir Red formu, alıcının aktarıma hiçbir teknik nesne bulunmadığını ve aktarımı kapatma konusunda hiçbir ilgi olmadığını gösteren *serbest bırakılmış* durumdur. Bu durumda, örneğin bir ileti Service Bus istemcisine teslim edildiğinde ve istemci iletiyi işlemeden kaynaklanan işi gerçekleştiremediği için iletiyi "iptal" seçerse, bu durum vardır; ileti teslimi hata durumunda değil. Bu durumun bir çeşitlemesi, serbest bırakıldığında ileti üzerinde değişiklik yapılmasına izin veren *değiştirme* durumudur. Bu durum Service Bus tarafından mevcut değil.

AMQP 1,0 belirtimi *alındı* olarak adlandırılan ve özellikle bağlantı kurtarmayı işlemeye yardımcı olan bir daha fazla değerlendirme durumu tanımlar. Bağlantı kurtarma, önceki bağlantı ve oturum kaybedildiğinde bağlantının durumunun ve yeni bir bağlantı ve oturumun üzerine bekleyen teslimleri reconstituting sağlar.

Service Bus bağlantı kurtarmayı desteklemez; istemci, kapanmamış bir ileti aktarımı bekleyen Service Bus bağlantıyı kaybederse, bu ileti aktarımı kaybedilir ve istemcinin yeniden bağlanması, bağlantıyı yeniden oluşturulması ve aktarımı yeniden denemesi gerekir.

Bu nedenle, Service Bus ve Event Hubs "en az bir kez", gönderenin, depolanan ve kabul edilen ileti için uygun olabileceği ancak "tam olarak bir kez" aktarılmasını, sistemin bağlantıyı kurtarmaya çalıştığı ve ileti aktarımının çoğaltılmasından kaçınmak için teslim durumuna anlaşmaya devam ettiğinin bir kez desteklememe.

Olası yinelenen göndermeleri dengelemek için Service Bus, kuyruklar ve konular üzerinde isteğe bağlı bir özellik olarak yinelenen saptamayı destekler. Yinelenen algılama, Kullanıcı tanımlı bir zaman penceresi sırasında tüm gelen iletilerin ileti kimliklerini kaydeder ve aynı pencerede aynı ileti kimlikleriyle gönderilen tüm iletileri sessizce bırakır.

### <a name="flow-control"></a>Akış denetimi

Daha önce ele alınan oturum düzeyi akış denetim modelinin yanı sıra, her bağlantının kendi akış denetim modeli vardır. Oturum düzeyi akış denetimi, kapsayıcının çok fazla çerçeveyi aynı anda işlemesini korumasından korur, bağlantı düzeyi akış denetimi, uygulamayı bir bağlantıdan işlemek istediği kaç ileti ve ne zaman ücretlendirdiğine koyar.

![Kaynak, hedef, kaynak bağlantı noktası, hedef bağlantı noktası ve protokol adını gösteren günlüğün ekran görüntüsü. İlk satırda 10401 (0x28 A 1) hedef bağlantı noktası siyah olarak özetlenmiştir.][4]

Bir bağlantı üzerinde, aktarımlar yalnızca gönderenin yeterli *bağlantı kredisi* olduğunda gerçekleşebilir. Bağlantı kredisi, bir bağlantı kapsamındaki *akış* kullanımını kullanan alıcı tarafından ayarlanan bir sayaçtır. Gönderene bağlantı kredisi atandığında, iletileri teslim ederek bu kredisi kullanmaya çalışır. Her ileti teslimi kalan bağlantı kredisi 1 ' i azaltır. Bağlantı kredisi kullanıldığında, teslimler durur.

Alıcı rolünde Service Bus olduğunda, iletileri hemen göndermek için, gönderene daha fazla bağlantı kredisi sağlar. Bağlantı kredisi kullanıldığında, Service Bus zaman zaman, bağlantı kredisi bakiyesini güncelleştirmek üzere gönderene bir *akış* gönderir.

Gönderen rolünde, bekleyen herhangi bir bağlantı kredisi kullanacak şekilde ileti gönderir Service Bus.

API düzeyindeki bir "alma" çağrısı, istemci tarafından Service Bus gönderilen bir *akışa* çevrilir ve Service Bus ilk kullanılabilir, kilitlenmemiş iletiyi kuyruktan ayırarak, kilitleyerek ve aktararak bu kredisi tüketir. Teslim için hazır bir ileti yoksa, söz konusu varlıkla oluşturulan herhangi bir bağlantı tarafından bekleyen kredi, varış sırasına göre kaydedilir ve bekleyen kredilerin kullanılabilmesi için iletiler kilitlenir ve aktarılır.

Aktarım *,* *kabul edilen* veya *yayınlanan* Terminal durumlarından birine kapatılmışsa bir iletideki kilit serbest bırakılır. Terminal durumu *kabul edildiğinde* ileti Service Bus kaldırılır. Service Bus kalır ve aktarım diğer durumlardan birine ulaştığında sonraki alıcıya teslim edilir. Service Bus, yinelenen ret veya yayınlar nedeniyle varlık için izin verilen en fazla teslimat sayısına ulaştığında iletiyi varlığın sahipsiz kuyruğuna otomatik olarak taşıtır.

Service Bus API 'Leri bugün bu tür bir seçeneği doğrudan kullanıma sunmasa da, alt düzey AMQP protokol istemcisi, çok sayıda bağlantı kredisi vererek ve daha sonra başka bir etkileşim olmadan kullanılabilir hale geldiğinde iletileri alarak, her alma isteği için bir kredi birimi veren "çekme stili" etkileşimini açmak için bağlantı kredi modelini kullanabilir. Push, [Messagingfactory. PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) veya [MessageReceiver. prefetchcount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) özelliği ayarları aracılığıyla desteklenir. Bu değerler sıfır dışında olduğunda AMQP istemcisi bunu bağlantı kredisi olarak kullanır.

Bu bağlamda, varlık içindeki ileti üzerinde kilit süresinin dolma saatinin, ileti tel çalışırken değil, varlıktan ne zaman alındığına ilişkin saatin çalıştığını anlamak önemlidir. İstemci bağlantı kredisi vererek ileti almaya hazır olma durumunu gösterdiğinde, bu nedenle iletileri ağda etkin bir şekilde çekmek ve işlemeye hazır olması beklenir. Aksi takdirde ileti kilidinin teslim edilmeden önce ileti kilidinin geçerliliği bitmiş olabilir. Bağlantı kredisi akış denetimi kullanımı, alıcıya dağıtılan kullanılabilir iletilerle başa çıkma durumunu doğrudan yansıtmalıdır.

Özet bölümünde aşağıdaki bölümlerde, farklı API etkileşimleri sırasında, veri akışının şematik bir genel bakışı sağlanmıştır. Her bölümde farklı bir mantıksal işlem açıklanmaktadır. Bu etkileşimlerin bazıları "yavaş" olabilir, ancak bu durum yalnızca gerektiğinde gerçekleştirilebilir. İleti gönderici oluşturmak, ilk ileti gönderilene veya istenene kadar ağ etkileşimine neden olmayabilir.

Aşağıdaki tablodaki oklar, performasel akış yönünü gösterir.

#### <a name="create-message-receiver"></a>İleti alıcısı oluştur

| İstemci | Service Bus |
| --- | --- |
| --> iliştirme (<br/>ad = {bağlantı adı},<br/>tanıtıcı = {sayısal tanıtıcı},<br/>rol =**alıcı**,<br/>Kaynak = {varlık adı},<br/>hedef = {istemci bağlantı KIMLIĞI}<br/>) |İstemci, varlığa alıcı olarak iliştirir |
| Bağlantının sonuna ekleme yanıtlarını Service Bus |<--Attach (<br/>ad = {bağlantı adı},<br/>tanıtıcı = {sayısal tanıtıcı},<br/>rol =**Gönderen**,<br/>Kaynak = {varlık adı},<br/>hedef = {istemci bağlantı KIMLIĞI}<br/>) |

#### <a name="create-message-sender"></a>İleti gönderici oluştur

| İstemci | Service Bus |
| --- | --- |
| --> iliştirme (<br/>ad = {bağlantı adı},<br/>tanıtıcı = {sayısal tanıtıcı},<br/>rol =**Gönderen**,<br/>kaynak = {istemci bağlantı KIMLIĞI},<br/>Hedef = {varlık adı}<br/>) |Eylem yok |
| Eylem yok |<--Attach (<br/>ad = {bağlantı adı},<br/>tanıtıcı = {sayısal tanıtıcı},<br/>rol =**alıcı**,<br/>kaynak = {istemci bağlantı KIMLIĞI},<br/>Hedef = {varlık adı}<br/>) |

#### <a name="create-message-sender-error"></a>İleti gönderici oluştur (hata)

| İstemci | Service Bus |
| --- | --- |
| --> iliştirme (<br/>ad = {bağlantı adı},<br/>tanıtıcı = {sayısal tanıtıcı},<br/>rol =**Gönderen**,<br/>kaynak = {istemci bağlantı KIMLIĞI},<br/>Hedef = {varlık adı}<br/>) |Eylem yok |
| Eylem yok |<--Attach (<br/>ad = {bağlantı adı},<br/>tanıtıcı = {sayısal tanıtıcı},<br/>rol =**alıcı**,<br/>Kaynak = null,<br/>Target = null<br/>)<br/><br/><--ayır (<br/>tanıtıcı = {sayısal tanıtıcı},<br/>kapalı =**doğru**,<br/>hata = {hata bilgisi}<br/>) |

#### <a name="close-message-receiversender"></a>İleti alıcısını/göndereni kapat

| İstemci | Service Bus |
| --- | --- |
| --> ayır (<br/>tanıtıcı = {sayısal tanıtıcı},<br/>kapalı =**doğru**<br/>) |Eylem yok |
| Eylem yok |<--ayır (<br/>tanıtıcı = {sayısal tanıtıcı},<br/>kapalı =**doğru**<br/>) |

#### <a name="send-success"></a>Gönderme (başarılı)

| İstemci | Service Bus |
| --- | --- |
| --> aktarımı (<br/>teslimat kimliği = {sayısal tanıtıcı},<br/>teslim-etiket = {ikili tanıtıcı},<br/>Kapatılan =**false**,, daha fazla =**false**,<br/>State =**null**,<br/>Özgeçmişi =**false**<br/>) |Eylem yok |
| Eylem yok |<--Disposition (<br/>rol = alıcı,<br/>ilk = {Delivery ID},<br/>Son = {teslim KIMLIĞI},<br/>Kapatılan =**doğru**,<br/>durum =**kabul edildi**<br/>) |

#### <a name="send-error"></a>Gönder (hata)

| İstemci | Service Bus |
| --- | --- |
| --> aktarımı (<br/>teslimat kimliği = {sayısal tanıtıcı},<br/>teslim-etiket = {ikili tanıtıcı},<br/>Kapatılan =**false**,, daha fazla =**false**,<br/>State =**null**,<br/>Özgeçmişi =**false**<br/>) |Eylem yok |
| Eylem yok |<--Disposition (<br/>rol = alıcı,<br/>ilk = {Delivery ID},<br/>Son = {teslim KIMLIĞI},<br/>Kapatılan =**doğru**,<br/>durum =**reddedildi**(<br/>hata = {hata bilgisi}<br/>)<br/>) |

#### <a name="receive"></a>Al

| İstemci | Service Bus |
| --- | --- |
| --> akışı (<br/>bağlantı-kredi = 1<br/>) |Eylem yok |
| Eylem yok |< aktarımı (<br/>teslimat kimliği = {sayısal tanıtıcı},<br/>teslim-etiket = {ikili tanıtıcı},<br/>Kapatılan =**false**,<br/>daha fazla =**yanlış**,<br/>State =**null**,<br/>Özgeçmişi =**false**<br/>) |
| --> eğilimi (<br/>rol =**alıcı**,<br/>ilk = {Delivery ID},<br/>Son = {teslim KIMLIĞI},<br/>Kapatılan =**doğru**,<br/>durum =**kabul edildi**<br/>) |Eylem yok |

#### <a name="multi-message-receive"></a>Birden çok ileti alma

| İstemci | Service Bus |
| --- | --- |
| --> akışı (<br/>bağlantı-kredi = 3<br/>) |Eylem yok |
| Eylem yok |< aktarımı (<br/>teslimat kimliği = {sayısal tanıtıcı},<br/>teslim-etiket = {ikili tanıtıcı},<br/>Kapatılan =**false**,<br/>daha fazla =**yanlış**,<br/>State =**null**,<br/>Özgeçmişi =**false**<br/>) |
| Eylem yok |< aktarımı (<br/>teslimat kimliği = {sayısal tanıtıcı + 1},<br/>teslim-etiket = {ikili tanıtıcı},<br/>Kapatılan =**false**,<br/>daha fazla =**yanlış**,<br/>State =**null**,<br/>Özgeçmişi =**false**<br/>) |
| Eylem yok |< aktarımı (<br/>teslimat kimliği = {sayısal tanıtıcı + 2},<br/>teslim-etiket = {ikili tanıtıcı},<br/>Kapatılan =**false**,<br/>daha fazla =**yanlış**,<br/>State =**null**,<br/>Özgeçmişi =**false**<br/>) |
| --> eğilimi (<br/>rol = alıcı,<br/>ilk = {Delivery ID},<br/>Son = {teslim KIMLIĞI + 2},<br/>Kapatılan =**doğru**,<br/>durum =**kabul edildi**<br/>) |Eylem yok |

### <a name="messages"></a>İletiler

Aşağıdaki bölümlerde, standart AMQP ileti bölümlerinden hangi özelliklerin Service Bus tarafından kullanıldığı ve Service Bus API kümesine nasıl eşlendikleri açıklanmaktadır.

Uygulamanın tanımlaması gereken herhangi bir özelliği AMQP 'nin `application-properties` haritasına eşlenmelidir.

#### <a name="header"></a>üst bilgi

| Alan Adı | Kullanım | API adı |
| --- | --- | --- |
| üretilen |- |- |
| Priority |- |- |
| ttl |Bu ileti için yaşam süresi |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| ilk-tanışma |- |- |
| teslimat sayısı |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Alan Adı | Kullanım | API adı |
| --- | --- | --- |
| ileti kimliği |Bu ileti için uygulama tanımlı, serbest biçimli tanımlayıcı. Yinelenen algılama için kullanılır. |[Ileti](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| user-id |Uygulama tanımlı kullanıcı tanımlayıcısı, Service Bus tarafından yorumlanmaz. |Service Bus API 'SI aracılığıyla erişilemez. |
| kullanıcısı |Uygulama tanımlı hedef tanımlayıcısı, Service Bus tarafından yorumlanmaz. |[Kime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Uygulama tanımlı ileti amacı tanımlayıcısı, Service Bus tarafından yorumlanmaz. |[Etiketle](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Yanıtla |Uygulama tanımlı yanıt yolu göstergesi, Service Bus tarafından yorumlanmaz. |[ReplyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| correlation-id |Uygulama tanımlı bağıntı tanımlayıcısı, Service Bus tarafından yorumlanmaz. |[CorrelationId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| içerik türü |Service Bus tarafından Yorumlanmayan gövde için uygulama tanımlı içerik türü göstergesi. |[ContentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| İçerik kodlama |Service Bus tarafından Yorumlanmayan gövde için uygulama tanımlı içerik kodlama göstergesi. |Service Bus API 'SI aracılığıyla erişilemez. |
| mutlak-süre sonu |İletinin süresinin dolacağını bildirir. Girişte (üst bilgi TTL 'SI gözlemlenmiştir) yoksayıldı, çıkışta yetkilidir. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| oluşturma zamanı |İletinin oluşturulma saatini bildirir. Service Bus tarafından kullanılmıyor |Service Bus API 'SI aracılığıyla erişilemez. |
| Grup Kimliği |İlgili bir ileti kümesi için uygulama tanımlı tanımlayıcı. Service Bus oturumları için kullanılır. |[Kimliği](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Grup sırası |Bir oturumun içindeki iletinin göreli sıra numarasını tanımlayan sayaç. Service Bus tarafından yoksayılır. |Service Bus API 'SI aracılığıyla erişilemez. |
| Yanıtla-grup kimliği |- |[Replytosessionıd](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>İleti ek açıklamaları

AMQP ileti özelliklerinin parçası olmayan ve ileti üzerinde olduğu gibi diğer Service Bus ileti özellikleri de iletilir `MessageAnnotations` .

| Ek açıklama eşleme anahtarı | Kullanım | API adı |
| --- | --- | --- |
| x-opt-zamanlanmış-sıraya alma zamanı | İletinin varlıkta ne zaman görüneceğini bildirir |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc) |
| x-opt-Partition-Key | İletinin hangi bölümde yer almalıdır belirleyen uygulama tanımlı anahtar. | [PartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey) |
| x-opt-bölüm-anahtar | Bir işlem, bir Aktarım kuyruğu aracılığıyla iletileri göndermek için kullanılacak olan uygulama tanımlı bölüm anahtarı değeri. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey) |
| x-opt-geri sıraya alma zamanı | İletiyi sıraya alma gerçek süresini temsil eden hizmet tarafından tanımlanan UTC saati. Girişte yoksayıldı. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc) |
| x-opt-sıra numarası | Bir iletiye atanan hizmet tanımlı benzersiz sayı. | [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber) |
| x-opt-kayması | İletinin hizmet tarafından tanımlanan sıraya alınan sıra numarası. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber) |
| x-opt-kilitlendi-Until | Hizmet tanımlı. İletinin kuyrukta/abonelikte kilitlenebileceği tarih ve saat. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc) |
| x-opt-sahipsiz-kaynak | Hizmet tanımlı. İleti atılacak ileti sırasından alınmışsa, özgün iletinin kaynağı. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource) |

### <a name="transaction-capability"></a>İşlem yeteneği

Bir hareket, iki veya daha fazla işlemi tek bir yürütme kapsamında bir araya toplar. Doğası gereği, bu tür bir işlem, belirli bir işlem grubuna ait tüm işlemlerin başarılı veya başarısız bir şekilde ortaklaşa bulunduğundan emin olmalıdır.
İşlemler bir tanımlayıcıya göre gruplandırılır `txn-id` .

İşlem etkileşimi için istemci, `transaction controller` birlikte gruplanmış işlemleri denetleyen bir olarak davranır. Service Bus hizmeti bir olarak davranır `transactional resource` ve tarafından istenen işi gerçekleştirir `transaction controller` .

İstemci ve hizmet, istemci tarafından belirlenen bir üzerinden iletişim kurar `control link` . `declare`Ve `discharge` iletileri sırasıyla işlem ayırmak ve gerçekleştirmek üzere denetim bağlantısı üzerinden denetleyici tarafından gönderilir (işlemsel çalışmanın deyonumu temsil etmez). Gerçek gönderme/alma Bu bağlantı üzerinde gerçekleştirilmez. İstenen her işlemsel işlem, açıkça istenen şekilde tanımlanır `txn-id` ve bu nedenle bağlantı üzerindeki herhangi bir bağlantıda meydana gelebilir. Bir denetim bağlantısı varsa, bu durum oluşturulan işlem dışı işlemler varsa, bu durumda tüm işlemler hemen geri alınır ve üzerinde daha fazla işlemsel iş gerçekleştirmeye çalışır ve hataya neden olur. Denetim bağlantısındaki iletiler önceden kapatılmamalıdır.

Her bağlantının, işlem başlatmak ve sonlandırmak için kendi denetim bağlantısını başlatması vardır. Hizmet, olarak işlev gören özel bir hedef tanımlar `coordinator` . İstemci/denetleyici bu hedefe bir denetim bağlantısı kurar. Denetim bağlantısı bir varlığın sınırının dışında, diğer bir deyişle, birden fazla varlık için işlemleri başlatmak ve ücretlendirmeden aynı denetim bağlantısı kullanılabilir.

#### <a name="starting-a-transaction"></a>İşlem başlatma

İşlemsel çalışmaya başlamak için. denetleyici `txn-id` , düzenleyiciden bir almalıdır. Bu, bir tür iletisi göndererek bunu yapar `declare` . Bildirim başarılı olursa, düzenleyici, atandı sonucunu taşıyan bir değerlendirme sonucu ile yanıt verir `txn-id` .

| İstemci (denetleyici) | Yön | Service Bus (düzenleyici) |
| :--- | :---: | :--- |
| ekleme<br/>ad = {bağlantı adı},<br/>... ,<br/>rol =**Gönderen**,<br/>hedef =**Düzenleyici**<br/>) | ------> |  |
|  | <------ | ekleme<br/>ad = {bağlantı adı},<br/>... ,<br/>Target = Koordinatör ()<br/>) |
| aktarımı<br/>teslim-kimlik = 0,...)<br/>{AmqpValue (**Declare ()**)}| ------> |  |
|  | <------ | çıkarma <br/> ilk = 0, son = 0, <br/>durum =**beyan** edilen (<br/>**TXN-id**= {işlem kimliği}<br/>))|

#### <a name="discharging-a-transaction"></a>Bir işlemin şarjını kaldır

Denetleyici, düzenleyiciye bir ileti göndererek işlem işini sonlanır `discharge` . Denetleyici, işlem çalışmasının işaretini, Boşalma gövdesinde ayarlamaya göre kaydetmeyi veya geri almayı beklediğini belirtir `fail` . Düzenleyici, kabul işlemi tamamlayamazsa ileti, ' ı taşıyan bu sonuç ile reddedilir `transaction-error` .

> Note: Fail = true bir işlemin geri alınması anlamına gelir ve fail = false, COMMIT anlamına gelir.

| İstemci (denetleyici) | Yön | Service Bus (düzenleyici) |
| :--- | :---: | :--- |
| aktarımı<br/>teslim-kimlik = 0,...)<br/>{AmqpValue (Declare ())}| ------> |  |
|  | <------ | çıkarma <br/> ilk = 0, son = 0, <br/>durum = beyan edilen (<br/>TXN-id = {işlem KIMLIĞI}<br/>))|
| | . . . <br/>İşlemsel çalışma<br/>diğer bağlantılarda<br/> . . . |
| aktarımı<br/>teslim-kimlik = 57,...)<br/>{AmqpValue (<br/>**Disşarj (TXN-id = 0, <br/> başarısız = false)**)}| ------> |  |
| | <------ | çıkarma <br/> ilk = 57, son = 57, <br/>State =**kabul edildi ()**)|

#### <a name="sending-a-message-in-a-transaction"></a>Bir işlemde ileti gönderme

Tüm işlem işleri, `transactional-state` TXN-id ' y i taşıyan işlem teslim durumuyla yapılır. İleti gönderme durumunda, işlem durumu iletinin aktarım çerçevesi tarafından yürütülür. 

| İstemci (denetleyici) | Yön | Service Bus (düzenleyici) |
| :--- | :---: | :--- |
| aktarımı<br/>teslim-kimlik = 0,...)<br/>{AmqpValue (Declare ())}| ------> |  |
|  | <------ | çıkarma <br/> ilk = 0, son = 0, <br/>durum = beyan edilen (<br/>TXN-id = {işlem KIMLIĞI}<br/>))|
| aktarımı<br/>tanıtıcı = 1,<br/>teslimat kimliği = 1, <br/>**durum = <br/> TransactionalState ( <br/> TXN-ID = 0)**)<br/>te| ------> |  |
| | <------ | çıkarma <br/> ilk = 1, son = 1, <br/>State =**Transactionalstate ( <br/> TXN-ID = 0, <br/> Outcome = kabul edildi ()**)))|

#### <a name="disposing-a-message-in-a-transaction"></a>Bir işlemde bir ileti atılıyor

İleti değerlendirmesi gibi işlemleri içerir `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer` . Bu işlemleri bir işlem içinde gerçekleştirmek için, öğesini `transactional-state` disposition ile geçirin.

| İstemci (denetleyici) | Yön | Service Bus (düzenleyici) |
| :--- | :---: | :--- |
| aktarımı<br/>teslim-kimlik = 0,...)<br/>{AmqpValue (Declare ())}| ------> |  |
|  | <------ | çıkarma <br/> ilk = 0, son = 0, <br/>durum = beyan edilen (<br/>TXN-id = {işlem KIMLIĞI}<br/>))|
| | <------ |aktarımı<br/>tanıtıcı = 2,<br/>teslimat kimliği = 11, <br/>durum = null)<br/>te|  
| çıkarma <br/> ilk = 11, son = 11, <br/>State =**Transactionalstate ( <br/> TXN-ID = 0, <br/> Outcome = kabul edildi ()**)))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Gelişmiş Service Bus özellikleri

Bu bölümde, AMQP 'ye yönelik taslak uzantılarına dayalı Azure Service Bus gelişmiş özellikleri ele alınmaktadır. Bu, şu anda AMQP için OASSıS Technical komite 'da geliştirilmiştir Service Bus, bu taslakların en son sürümlerini uygular ve bu Taslaklar standart duruma ulaştığında tanıtılan değişiklikleri benimsemektedir.

> [!NOTE]
> Service Bus mesajlaşma gelişmiş işlemleri bir istek/yanıt düzeniyle desteklenir. Bu işlemlerin ayrıntıları, [Service Bus: istek-yanıt tabanlı Işlemlerde AMQP 1,0](service-bus-amqp-request-response.md)makalesinde açıklanmaktadır.
> 
> 

### <a name="amqp-management"></a>AMQP yönetimi

AMQP yönetim belirtimi, bu makalede ele alınan taslak uzantılarının ilklarıdır. Bu belirtim, AMQP üzerinden mesajlaşma altyapısına sahip yönetim etkileşimlerine izin veren AMQP protokolünün üst kısmında katmanlı bir protokoller kümesi tanımlar. Belirtim, bir ileti altyapısı içindeki varlıkları yönetmek için *oluşturma*, *okuma*, *güncelleştirme* ve *silme* gibi genel işlemleri ve sorgu işlemleri kümesini tanımlar.

Bu hareketlerin hepsi, istemci ile mesajlaşma altyapısı arasında bir istek/yanıt etkileşimi gerektirir ve bu nedenle belirtim, istemci mesajlaşma altyapısına bağlanır, bir oturum başlatır ve ardından bir çift bağlantı oluşturur. Tek bir bağlantıda, istemci Gönderici olarak davranır ve diğeri de alıcı olarak davranır ve bu sayede çift yönlü kanal görevi gören bir dizi bağlantı oluşturur.

| Mantıksal Işlem | İstemci | Service Bus |
| --- | --- | --- |
| Istek yanıt yolu oluştur |--> iliştirme (<br/>ad = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>rol =**Gönderen**,<br/>kaynak =**null**,<br/>target = "myentity/$management"<br/>) |Eylem yok |
| Istek yanıt yolu oluştur |Eylem yok |\<-- attach(<br/>ad = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>rol =**alıcı**,<br/>Kaynak = null,<br/>target = "myentity"<br/>) |
| Istek yanıt yolu oluştur |--> iliştirme (<br/>ad = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>rol =**alıcı**,<br/>Source = "myentity/$management",<br/>target = "myclient $ ID"<br/>) | |
| Istek yanıt yolu oluştur |Eylem yok |\<-- attach(<br/>ad = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>rol =**Gönderen**,<br/>Source = "myentity",<br/>target = "myclient $ ID"<br/>) |

Bu bağlantı çiftinin yerinde olması, istek/yanıt uygulamasının basittir: istek, bu kalıbı anlayan mesajlaşma altyapısı içindeki bir varlığa gönderilen iletidir. Bu istek iletisinde, *Özellikler* bölümündeki *Yanıtla* alanı, yanıtın teslim edileceği bağlantının *hedef* tanımlayıcısına ayarlanır. İşleme varlığı, isteği işler ve ardından yanıtı, *hedef* tanımlayıcısı belirtilen *Yanıtla* tanımlayıcıyla eşleşen bağlantı üzerinden sunar.

Bu model, istemci kapsayıcısının ve yanıt hedefi için istemci tarafından oluşturulan tanımlayıcının tüm istemcilerde benzersiz olmasını ve güvenlik nedenleriyle tahmin edilmesi için de zorlaştırır.

Yönetim Protokolü ve aynı kalıbı kullanan diğer tüm protokoller için kullanılan ileti alışverişi uygulama düzeyinde gerçekleşir; Yeni AMQP protokol düzeyi hareketleri tanımlamaz. Böylece, uygulamalar uyumlu AMQP 1,0 yığınlarıyla Bu uzantılardan hemen faydalanabilir.

Service Bus, şu anda yönetim belirtiminin temel özelliklerinden hiçbirini uygulamıyor, ancak yönetim belirtimi tarafından tanımlanan istek/yanıt deseninin, talep tabanlı güvenlik özelliği ve aşağıdaki bölümlerde ele alınan gelişmiş özelliklerin neredeyse hepsi için temeli vardır:

### <a name="claims-based-authorization"></a>Talep tabanlı yetkilendirme

AMQP talep tabanlı yetkilendirme (CBS) belirtimi taslağı, yönetim belirtim isteği/yanıtı düzeniyle oluşturulur ve Federal güvenlik belirteçlerinin AMQP ile nasıl kullanılacağına ilişkin genelleştirilmiş bir modeli açıklar.

Giriş bölümünde tartışılan AMQP 'nin varsayılan güvenlik modeli, SASL 'yi temel alır ve AMQP bağlantı anlaşması ile tümleşir. SASL 'nin kullanılması, bir dizi mekanizmaların, SASL üzerinde hiçbir protokolde yararlanabileceği herhangi bir protokolün tanımlandığı bir Genişletilebilir model sağladığından yararlanır. Bu mekanizmalar arasında, "HARICI" Kullanıcı adları ve parolaların aktarılması için "düz", açık kimlik doğrulama/yetkilendirme ve kimlik doğrulaması ve/veya yetkilendirme kimlik bilgilerini ve belirteçleri bağlamaya izin veren çok çeşitli ek mekanizmaların, "anonım" olması gerekir.

AMQP 'nin SASL tümleştirmesi iki dezavantaja sahiptir:

* Tüm kimlik bilgileri ve belirteçler bağlantı kapsamlandırılır. Bir mesajlaşma altyapısı, varlık başına ayrı erişim denetimi sağlamak isteyebilir; Örneğin, belirteç taşıyıcının A kuyruğuna gönderilmesine izin verme, ancak B kuyruğuna değil. Bağlantıya bağlı yetkilendirme bağlamı ile, tek bir bağlantı kullanmak mümkün değildir ve sıra A ve sıra B için farklı erişim belirteçleri kullanabilirsiniz.
* Erişim belirteçleri genellikle sınırlı bir süre için geçerlidir. Bu geçerlilik, kullanıcının belirteçleri düzenli olarak yeniden al ve kullanıcının erişim izinleri değiştiyse yeni bir belirteç vermeyi reddetmek için belirteç veren için bir fırsat sağlar. AMQP bağlantıları uzun süreler için en son olabilir. SASL modeli, bağlantı zamanında bir belirteç ayarlamak için bir şans sağlar. Bu, ileti altyapısının, belirtecin süresi sona erdiğinde istemcinin bağlantısını kesmesinin gerektiği veya erişim hakları olan bir istemci ile devam eden iletişimin iptal edilmesi riskini kabul etmesi riskini kabul etmesi için gerekli olan bir istemciye izin verme riskini kabul etmesi için gereken bir işlem sağlar.

Service Bus tarafından uygulanan AMQP CBS belirtimi, bu sorunların her ikisi için de zarif bir geçici çözüm sağlar: bir istemcinin, erişim belirteçlerini her bir düğümle ilişkilendirilmesini ve ileti akışını kesmeden bu belirteçleri kullanım dışı bırakmadan önce güncelleştirmesini sağlar.

CBS, mesajlaşma altyapısı tarafından sağlanacak *$CBS* adlı bir sanal yönetim düğümünü tanımlar. Yönetim düğümü, mesajlaşma altyapısındaki diğer tüm düğümler adına belirteçleri kabul eder.

Protokol hareketi, yönetim belirtimi tarafından tanımlanan bir istek/yanıt değiş tokuşu olur. Bu, istemcinin *$CBS* düğümüne sahip bir bağlantı çifti oluşturduğu ve sonra giden bağlantıda bir istek aktardığı ve ardından gelen bağlantı üzerindeki yanıtı beklediği anlamına gelir.

İstek iletisi aşağıdaki uygulama özelliklerine sahiptir:

| Anahtar | İsteğe Bağlı | Değer Türü | Değer Içeriği |
| --- | --- | --- | --- |
| operation |Hayır |string |**Put belirteci** |
| tür |Hayır |string |Yerleştirmekte olan belirtecin türü. |
| name |Hayır |string |Belirtecin uygulandığı "hedef kitle". |
| dolmadan |Yes |timestamp |Belirtecin süre sonu zamanı. |

*Name* özelliği, belirtecin ilişkilendirilacağı varlığı tanımlar. Service Bus kuyruk veya konu/abonelik yoludur. *Type* özelliği, belirteç türünü tanımlar:

| Belirteç türü | Belirteç açıklaması | Gövde türü | Notlar |
| --- | --- | --- | --- |
| JWT |JSON Web Token (JWT) |AMQP değeri (dize) | |
| ServiceBus. Windows. net: sastoken |Service Bus SAS belirteci |AMQP değeri (dize) |- |

Belirteçler yapılandırmacısı hakları. Service Bus üç temel hak biliyor: "Gönder" gönderimi, "dinlemek" almayı ve "Yönet", varlıkların işlenmesine izin vermez. Service Bus SAS belirteçleri, ad alanı veya varlıkta yapılandırılan kurallara başvurur ve bu kurallar, haklarla yapılandırılır. Belirteç bu kuralla ilişkilendirilen anahtarla imzalanmak, belirtecin ilgili hakları ifade etmelerini sağlar. *PUT belirtecini* kullanan bir varlıkla ilişkili belirteç, bağlı istemcinin, belirteç hakları başına varlıkla etkileşime geçmesini sağlar. *Gönderen* rolünü istemcinin aldığı bir bağlantı, "Gönder" hakkını gerektirir; *alıcı* rolünü almak Için "dinler" hakkı gerekir.

Yanıt iletisinde aşağıdaki *uygulama özellikleri* değerleri bulunur

| Anahtar | İsteğe Bağlı | Değer Türü | Değer Içeriği |
| --- | --- | --- | --- |
| durum kodu |Hayır |int |HTTP yanıt kodu **[RFC2616]**. |
| durum-açıklama |Yes |string |Durumun açıklaması. |

İstemci, *yerleştirme belirtecini* sürekli olarak ve mesajlaşma altyapısındaki herhangi bir varlık için çağırabilir. Belirteçler, geçerli istemcinin kapsamına alınır ve geçerli bağlantıya bağlanır, yani bağlantı düşerse sunucu tüm korunan belirteçleri bırakır.

Geçerli Service Bus uygulama yalnızca "ANONYMOUS" SASL yöntemiyle birlikte CBS 'ye izin veriyor. SASL el sıkışması öncesinde her zaman bir SSL/TLS bağlantısı bulunmalıdır.

Bu nedenle, anonım mekanizmanın seçili AMQP 1,0 istemcisi tarafından desteklenmesi gerekir. Anonim erişim, ilk oturum oluşturma da dahil olmak üzere ilk bağlantı anlaşmasını, bağlantıyı kimin oluşturtığını bilmeksizin Service Bus olmadan meydana geldiğini gösterir.

Bağlantı ve oturum kurulduktan sonra, *$CBS* düğümüne bağlantıları eklemek ve *PUT-Token* isteğini göndermek izin verilen tek operasyonlardır. Bağlantı kurulduktan sonra 20 saniye içindeki bazı varlık düğümleri için bir *PUT-Token* isteği kullanılarak geçerli bir belirtecin başarıyla ayarlanması gerekir, aksi takdirde bağlantı, Service Bus tarafından tek taraflı bırakılır.

İstemci daha sonra belirteç süre sonunu izlemekten sorumludur. Bir belirtecin süresi dolarsa Service Bus, ilgili varlıkla bağlantılı tüm bağlantıları hemen bırakır. Sorun oluşmasını önlemek için, istemci, düğüm belirtecini, aynı *PUT belirteci* hareketiyle sanal *$CBS* yönetim düğümü aracılığıyla ve farklı bağlantılarda akan yük trafiğinin bir yolu olmadan değiştirebilir.

### <a name="send-via-functionality"></a>Gönderme işlevleri

[Gönderme/aktarma göndericisi](service-bus-transactions.md#transfers-and-send-via) , Service Bus 'ın belirli bir iletiyi başka bir varlık aracılığıyla hedef varlığa iletmesine imkan tanıyan bir işlevdir. Bu özellik, tek bir işlemdeki varlıklar arasında işlem gerçekleştirmek için kullanılır.

Bu işlevle, bir gönderici oluşturup bağlantısını kurarsınız `via-entity` . Bağlantıyı kurarken, bu bağlantıdaki iletilerin/aktarımların gerçek hedefini oluşturmak için ek bilgiler geçirilir. İliştirme başarılı olduktan sonra, bu bağlantı üzerinde gönderilen tüm iletiler, *-varlık* aracılığıyla *hedef varlığa* otomatik olarak iletilir. 

> Note: Bu bağlantıyı oluşturmadan önce, kimlik doğrulamasının hem *ile varlık* hem de *hedef varlık* için gerçekleştirilmesi sağlanmalıdır.

| İstemci | Yön | Service Bus |
| :--- | :---: | :--- |
| ekleme<br/>ad = {bağlantı adı},<br/>rol = Gönderen,<br/>kaynak = {istemci bağlantı KIMLIĞI},<br/>hedef =**{VIA-varlık}**,<br/>**Properties = Map [( <br/> com. Microsoft: aktarım-hedefi-adresi = <br/> {hedef-varlık})]** ) | ------> | |
| | <------ | ekleme<br/>ad = {bağlantı adı},<br/>rol = alıcı,<br/>kaynak = {istemci bağlantı KIMLIĞI},<br/>hedef = {VIA-varlık},<br/>Properties = Map [(<br/>com. Microsoft: transfer-Destination-Address =<br/>{Hedef-varlık})] ) |

## <a name="next-steps"></a>Sonraki adımlar

AMQP hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları ziyaret edin:

* [Service Bus AMQP 'ye Genel Bakış]
* [Service Bus bölümlenmiş kuyruklar ve konular için AMQP 1,0 desteği]
* [Windows Server için Service Bus AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Service Bus AMQP 'ye Genel Bakış]: service-bus-amqp-overview.md
[Service Bus bölümlenmiş kuyruklar ve konular için AMQP 1,0 desteği]: 
[AMQP in Service Bus for Windows Server]: /previous-versions/service-bus-archive/dn574799(v=azure.100)