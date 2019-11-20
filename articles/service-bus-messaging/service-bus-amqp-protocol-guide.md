---
title: AMQP 1.0 protokol Kılavuzu Azure Service Bus ve Event Hubs | Microsoft Docs
description: İfadeler ve Azure Service Bus ve Event Hubs AMQP 1.0 açıklamasını protokol Kılavuzu
services: service-bus-messaging,event-hubs
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: d2d3d540-8760-426a-ad10-d5128ce0ae24
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c99f4491af8fe3e5f0f0ed7a264995ae3ec5911f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60749453"
---
# <a name="amqp-10-in-azure-service-bus-and-event-hubs-protocol-guide"></a>AMQP 1.0 protokol Kılavuzu Azure Service Bus ve Event Hubs

Gelişmiş ileti sıraya alma protokolü 1.0 zaman uyumsuz olarak, güvenli ve güvenilir bir şekilde iki taraflar arasında iletileri aktarmak için standartlaştırılmış bir çerçeve ve Aktarım Protokolü ' dir. Azure Service Bus Mesajlaşması ve Azure Event Hubs birincil protokolüdür. Her iki hizmet de HTTPS destekler. Ayrıca desteklenen özel SBMP Protokolü AMQP ile değiştiriliyor kullanılmamaya.

AMQP 1.0 ara yazılım satıcılarının, Microsoft ve Red Hat gibi JP Morgan finansal hizmetler sektöründe temsil eden kestirmeden sonuca gitme gibi çok sayıda ileti ara yazılım kullanıcılarla birlikte duruma geniş Sektör İşbirliği sonucudur. AMQP protokol ve uzantı özellikleriyle ilgili teknik Standardizasyon forum OASIS olduğunu ve ISO/IEC 19494 olarak uluslararası bir standart olarak resmi onay kazanmıştır.

## <a name="goals"></a>Hedefleri

Bu makalede kısaca AMQP 1.0 belirtimi küçük bir OASIS AMQP teknik komitesi içinde şu anda kesin taslak uzantı özelliklerini yanı sıra Mesajlaşma temel kavramlarını özetlemektedir ve Azure Service Bus açıklar uygular ve bu belirtimlerle oluşturur.

Azure hizmet veri yolu AMQP 1.0 üzerinden etkileşim kurabilmesi için herhangi bir platformda tüm mevcut AMQP 1.0 istemcisi yığını kullanarak herhangi bir geliştirici için hedeftir.

Apache Proton veya AMQP.NET Lite gibi ortak genel amaçlı AMQP 1.0 yığınları zaten tüm çekirdek AMQP 1.0 protokollerini uygulayın. Bu temel hareketlerini bazen daha üst düzey bir API ile sarılır; Apache Proton bile iki, kesinlik temelli Messenger API ve reaktif Reaktör API sunar.

Aşağıdaki tartışmada AMQP bağlantıları, oturumları ve bağlantıları yönetimini ve, çerçeve aktarımları ve akış denetimi (gibi Apache Proton-C) ilgili yığın tarafından işlenir ve kadar varsa belirli dikkat gerektirmez varsayıyoruz Uygulama geliştiricilerden. Birkaç API temelleri yeteneği gibi bağlamak ve çeşit varlığını bağlamalarında varsayıyoruz *gönderen* ve *alıcı* soyutlama nesneleri, daha sonra sahip bazı şeklini `send()` ve `receive()` işlemleri, sırasıyla.

Azure Service Bus ileti taraması veya yönetim oturumu, gibi gelişmiş özellikler tartışırken AMQP koşulları, aynı zamanda bu varsayılan API soyutlama üzerinde katmanlı bir sözde uygulaması olarak bu özellikleri açıklanmıştır.

## <a name="what-is-amqp"></a>AMQP nedir?

AMQP çerçeveleme ve aktarım protokolüdür. Çerçeveleme anlamına gelir, bir ağ bağlantısının her iki yönde akmasını ikili veri akışı için yapı sağlar. Yapı verileri, adlı farklı bloklarını için kabul edilebilir açıklıkta sağlar *çerçeveler*, bağlı taraflar değiştirilmek üzere. Aktarımı Özellikleri iletişim kuran iki taraf hakkında ne zaman çerçeveleri aktarılan ve ne zaman aktarımları tam olarak ele alınacaktır paylaşılan bir anlayış kurup emin olun.

Birkaç ileti aracıları tarafından hala kullanılmakta olan AMQP çalışma grubu tarafından üretilen süresi dolmuş taslak önceki sürümlerden farklı olarak bir ileti Aracısı ya da belirli bir topoloji için varlığını çalışma grubunun son ve standartlaştırılmış AMQP 1.0 protokol belgenizdeki değil Varlık içinde bir ileti Aracısı.

Protokolü gibi Azure Service Bus kuyrukları ve varlıklarını yayımlama/abone olma destekleyen ileti aracıları ile etkileşim için simetrik eşler arası iletişim için kullanılabilir. Bu da ileti altyapısı ile etkileşim sağlamak için Azure Event Hubs ile olduğu gibi etkileşim desenleri normal sıralarından farklı olduğu kullanılabilir. Bir olay hub'ı olayları için gönderildiğinde bir sıra gibi davranır, ancak ondan olayları okunduğunda daha seri depolama hizmeti gibi davranır; biraz bir bant sürücüsü de benzer. İstemci bir uzaklık kullanılabilir veri akımına seçer ve ardından bu uzaklığı için en son kullanılabilir tüm olayları sunulur.

AMQP 1.0 protokol belirtimleri yeteneklerini artırmak daha fazla etkinleştirme, Genişletilebilir olmak üzere tasarlanmıştır. Bu belgede açıklanan üç uzantı özellikleri bu göstermektedir. Altyapınız HTTPS/WebSockets üzerinden iletişim için yerel AMQP TCP bağlantı noktalarını yapılandırma zor olabilir. Bağlama belirtimi WebSockets üzerinden AMQP katman nasıl yapılacağını tanımlar. Yönetim amacıyla veya gelişmiş işlevsellik sağlamak için bir istek/yanıt biçimde Mesajlaşma altyapısı ile etkileşim kurmak için AMQP yönetim belirtimi gerekli temel etkileşim temelleri tanımlar. Federal yetkilendirme modeli tümleştirme için AMQP talep tabanlı güvenlik belirtimi ilişkilendirmek ve bağlantılarla ilgili yetkilendirme belirteçleri yenileme işlemini tanımlar.

## <a name="basic-amqp-scenarios"></a>Temel AMQP senaryoları

Bu bölüm, bağlantılar, oturumları ve bağlantıları oluşturma ve aktarma ileti kuyrukları, konular ve abonelikler gibi Service Bus varlıkları gelen ve giden içeren Azure Service Bus ile AMQP 1.0 temel kullanımını açıklar.

AMQP 1.0 belirtimi AMQP nasıl çalıştığı hakkında bilgi edinmek için en yetkili kaynağı olduğu halde belirtimi tam olarak Uygulama Kılavuzu ve protokolü öğretmek değildir yazılmıştır. Bu bölüm, hizmet veri yolu AMQP 1.0 nasıl kullandığını tanımlamak için gerektiği şekilde kadar terminolojisi giriş üzerinde odaklanır. AMQP daha kapsamlı bir giriş, yanı sıra daha geniş bir AMQP 1.0 tartışılması, gözden geçirebilirsiniz [bu video dersi][this video course].

### <a name="connections-and-sessions"></a>Bağlantılar ve oturumlar

AMQP iletişim kuran programları çağırır *kapsayıcıları*; bu içeren *düğümleri*, kapsayıcıların içinde iletişim kuran varlıkları olduğu. Bir kuyruk, bir düğüm olabilir. Tek bir bağlantı, düğümler arasında birçok iletişim yolları için kullanılabilmesi için çoğullama için AMQP sağlar; Örneğin, bir uygulama istemcisi eşzamanlı olarak bir kuyruktan alabilir ve aynı ağ bağlantısı üzerinden başka bir kuyruğa gönderebilirsiniz.

![][1]

Ağ bağlantısı, bu nedenle kapsayıcıdaki sabitlenmiştir. Kapsayıcıda bir giden TCP yuvası bağlantısı için bir kapsayıcı dinler ve gelen TCP bağlantılarını kabul alıcı rolünde yapmadan istemci rolü tarafından başlatılır. Bağlantı el sıkışması bildirme veya Aktarım Katmanı Güvenliği (TLS/SSL) kullanımı ve bir kimlik doğrulama/yetkilendirme el sıkışması SASL üzerinde temel bağlantı kapsamda anlaşması Protokolü sürüm içerir.

Azure Service Bus TLS her zaman gerektirir. TCP bağlantısı AMQP protokol anlaşma girmeden önce TLS ile ilk yayılan gerçekleştirilmesine TCP bağlantı noktası 5671, bağlantılarını destekler ve sunucu bağlantısı, zorunlu bir yükseltme hemen teklif gerçekleştirilmesine 5672 TCP bağlantı noktası üzerinden bağlantıları da destekliyor TLS için AMQP belirtilen modeli kullanarak. AMQP WebSockets bağlama, ardından AMQP 5671 bağlantı eşdeğer olan TCP bağlantı noktası 443 üzerinden bir tüneli oluşturur.

TLS ve bağlantı ayarladıktan sonra Service Bus iki SASL mekanizması seçeneklerini sunar:

* SASL DÜZ, kullanıcı adı ve parola kimlik bilgilerini bir sunucuya geçirmek için yaygın olarak kullanılır. Service Bus hesapları, ancak adlandırılmış yok [paylaşılan erişim güvenlik kuralları](service-bus-sas.md), hakları confer ve bir anahtar ile ilişkilendirilir. Bir kuralın adını, kullanıcı adı ve anahtarı kullanılır (metin Base64 ile kodlanmış gibi) parola olarak kullanılır. Seçilen kuralla ilişkili haklar bağlantısından işlemlerini yönetir.
* Anonim SASL istemci daha sonra açıklanan talep tabanlı güvenlik (CBS) modeli kullanmak istediğinde SASL yetkilendirme atlamak için kullanılır. Bu seçenek belirtilmişse, istemci bağlantısı kısa sırasında istemci, yalnızca CBS uç noktası ile etkileşim kurabilir ve CBS el sıkışması tamamlamanız gerekir süre için anonim olarak kurulabilir.

Aktarım bağlantısı kurulduktan sonra her kapsayıcı işlemek iradeye sahip oldukları en büyük çerçeve boyutu bildirme ve bağlantıda hiçbir etkinlik yoksa bir boşta kalma zaman aşımından sonra bunlar tüketicisi kesin.

Ayrıca kaç tane eş zamanlı kanalları desteklenen bildirin. Bir kanal, bağlantı üzerine bir aktarım tek yönlü, giden, sanal yoludur. Oturum, kapsayıcıların bir çift yönlü iletişim yolunun oluşturmak üzere birbirine bağlı bir kanaldan alır.

Oturumlarının bir pencere tabanlı akış denetimi modeli vardır; oturum oluşturulduğunda, her iki taraf çerçeve içine kabul edebileceğiniz bildirir, pencere alırsınız. Taraflar exchange çerçeve penceresi ve aktarımları penceresi dolu olduğunda ve pencere sıfırlama veya genişletilmiş kullanarak alır kadar Durdur aktarılan çerçeve dolgu olarak *akış performative* (*performative* olduğu AMQP terimi iki taraflar arasında alışverişi protokol düzeyinde hareketler için).

Bu pencere tabanlı model, pencere tabanlı akış denetimi, ancak yuva oturum düzeyinde TCP kavramı kabaca benzerdir. Yüksek öncelikli trafik kısıtlanmış normal trafik gibi bir Otoyol express şeridi rushed böylece protokolün birden fazla eşzamanlı oturum için izin verme kavramı var.

Azure Service Bus şu anda, her bağlantı için tam olarak bir oturum kullanır. Service Bus en fazla çerçeve boyutu, Service Bus standart ve Event Hubs için 262.144 bayt (256 K bayt) boyutudur. Bu, Service Bus Premium için 1.048.576 (1 MB) olur. Service Bus belirli oturum düzeyi azaltma pencerelerinden getirmediğinden, ancak pencere düzenli olarak bağlantı düzeyi akış denetimi bir parçası olarak sıfırlar (bkz [sonraki bölümde](#links)).

Bağlantılar, Kanallar ve oturumları kısa ömürlü. Bağlantılar, temel alınan bağlantı daraltır, TLS tünelinin SASL yetkilendirme bağlamı ve oturumları oluşturulmaları gerekir.

### <a name="links"></a>Bağlantılar

AMQP bağlantıları üzerinden iletileri aktarır. Bir bağlantı, tek yönlü aktarma iletileri sağlayan bir oturumu üzerinden oluşturulan iletişim bir yoludur; bağlantı ve bağlı taraflar arasında çift yönlü Aktarım durumu anlaşma biter.

![][2]

Bağlantılar dilediğiniz zaman ve AMQP HTTP ve MQTT, başlatma aktarımları aktarma yolu ve özel bir ayrıcalık oluşturma tarafın olduğu gibi birçok diğer protokolleri farklı kılan bir var olan oturumu üzerinden ya da kapsayıcı tarafından oluşturulabilir Yuva bağlantısı.

Bir bağlantı kabul etmek için ters kapsayıcı bağlantı başlatma kapsayıcı sorar ve gönderen veya alıcı rolü seçer. Bu nedenle, kapsayıcı oluşturma tek yönlü başlatabilir ya da çift yönlü iletişim yolları, ikinci ile bağlantı çiftleri olarak modellenir.

Bağlantılar adlı ve düğümleri ile ilişkili. Başlangıçta belirtildiği gibi düğümler bir kapsayıcı içinde iletişim kuran varlıklardır.

Hizmet veri yolu, bir düğüm doğrudan bir kuyruk, konu, bir abonelik veya bir kuyrukta veya abonelikte teslim edilemeyen iletiler sırasına eşdeğerdir. AMQP içinde kullanılan düğüm adı bu nedenle göreli Service Bus ad alanı içinde varlığın adıdır. Bir kuyruk adlandırılmışsa `myqueue`, AMQP düğüm adını da olmasıdır. Konu aboneliği "abonelikler" kaynak koleksiyonu ve bu nedenle, bir abonelik sıralı olarak HTTP API kuralı izler **alt** bir konuya **mytopic** AMQP düğüm adına sahip  **Abonelikler/mytopic/sub**.

Bağlanan istemcinin bağlantılar oluşturmak için bir yerel düğüm adı kullanmak için de gereklidir; Service Bus bu düğüm adları hakkında belirleyici değildir ve bunları yorumlamaz. AMQP 1.0 istemcisi yığınları genel bir düzeni güvence altına almak için bu kısa ömürlü düğüm adları istemci kapsamı içinde benzersiz olan kullanın.

### <a name="transfers"></a>Aktarımları

Bağlantı kurulduktan sonra iletileri o bağlantı üzerinden aktarılabilir. AMQP içinde bir aktarımı ile bir açık protokol hareket yürütülür ( *aktarımı* performative), bir ileti gönderenden alıcıya bir bağlantı üzerinden taşır. Bu "kapatıldığında", bir aktarım her iki taraf bu aktarım sonucunu paylaşılan bir anlayış sağladıktan anlamı tamamlanmıştır.

![][3]

En basit durumda, istemcinin sonucu ilgilenen değildir ve alıcı işlemin sonucu hakkında Geri bildirimlerinizi sağlamaz anlamına gelen "önceden kapatılmış," ileti göndermek gönderen seçebilirsiniz. Bu mod, hizmet veri yolu AMQP protokol düzeyinde tarafından desteklenen ancak istemci API'leri hiçbirinde sunulmamıştır.

Normal iletileri gönderilen kapatılmamış ve alıcı sonra onay veya ret kullanarak gösterir durumda *değerlendirme* performative. Herhangi bir nedenle alıcı iletiyi kabul edilemiyor ve reddetme iletisi AMQP tarafından tanımlanan bir hata yapı nedeni hakkında bilgi içeren ret gerçekleşir. Service Bus içinde iç hata nedeniyle ileti reddedilirse, hizmet tanılama ipuçları sağlamak için destek isteklerini dosyalama, destek personelinin için kullanılabilir, yapı içindeki ek bilgileri döndürür. Daha sonra hatalarıyla ilgili ayrıntıları öğrenin.

Özel bir reddetme formudur *yayımlanan* durumu, alıcı, aktarımı teknik hiçbir itiraz, ancak aktarma sonlandırma içinde hiçbir ilgi de sahip olduğunu gösterir. İleti işlemesini kaynaklanan iş gerçekleştirilemiyor çünkü bu durumda, örneğin, bir ileti bir Service Bus istemciye teslim edilir ve "iletisini iptal etmek" istemci seçer var; ileti teslimi, hatalı değil. Bu durumda bir çeşididir *değiştiren* yapılacak değişiklikler yayımlanır gibi veren durumu. Bu durum, şu anda Service Bus tarafından kullanılmaz.

Daha fazla değerlendirme AMQP 1.0 belirtimi tanımlar adlı durumu *alınan*, bağlantı kurtarma işlemek için özellikle yardımcı olur. Bir bağlantı ve herhangi bir yeni bağlantı ve oturumu, oturum ve önceki bağlantı zaman kayıp üzerine teslimler bekleyen durumunu reconstituting bağlantı kurtarma sağlar.

Service Bus bağlantı kurtarmayı desteklemez; istemci için Service Bus kapatılmamış bir ileti ile bağlantısı kesilirse aktarım beklemede, bu ileti aktarım kaybolur ve istemci yeniden gerekir, bağlantıyı yeniden oluşturun ve aktarımı yeniden deneyin.

Bu nedenle, Service Bus ve Event Hubs "en az bir kez" nerede gönderen depolanır ve kabul ileti olabilirsiniz, ancak değil destek "tam bir kez" aktarımlarına nerede sistem bağlantıyı kurtarılmaya çalışılacak AMQP düzeyinde bunu destekleyen ve İleti aktarma yinelemesinden kaçınmak için teslim durumunu belirlemeye devam edin.

Olası yinelenen gönderir dengelemek için Service Bus kuyrukları ve konuları üzerinde yinelenen öğe algılamasını isteğe bağlı bir özellik olarak destekler. Yinelenen algılama tüm gelen iletilerin iletisi kimlikleri bir kullanıcı tanımlı bir zaman penceresi sırasında kaydeder ve ardından o aynı pencereyi sırasında aynı ileti kimliği ile gönderilen tüm iletilerin sessiz bir şekilde bırakır.

### <a name="flow-control"></a>Akış denetimi

Her bağlantı, daha önce açıklanan oturum düzeyi akış denetimi modeli yanı sıra kendi akış denetimi modeli içerir. Oturum düzeyi akış denetimi kapsayıcı bağlantı düzeyi akış denetimi uygulamanın ne kadar iletileri bir bağlantıdan işlemek istediği sorumlu koyar sonra ve, çok fazla kareleri işlemek zorunda kalmaktan korur.

![][4]

Gönderen yeterli sahip olduğunda, bir bağlantı üzerinde aktarımlarını yalnızca oluşabilir *bağlantı kredi*. Bağlantı alacak olan bir sayaç kullanarak bir alıcı tarafından kümesi *akış* performative, bağlantı kapsamı. Gönderen bağlantısı kredi atandığında, iletileri teslim ederek, kredi kullanmayı dener. Her ileti teslim azaltır, kalan bağlantı 1 kredi. Bağlantı kredi kullanıldığı zaman teslimler durdurun.

Service Bus alıcı rolde olduğunda, iletiler hemen gönderilebilir, anında gönderen bol miktarda bağlantı kredi ile sağlar. Service Bus bağlantı kredi kullanıldıkça, bazen gönderir bir *akış* performative göndereni bağlantı kredi bakiyesi güncelleştirilecek.

Gönderen rolü'nde, Service Bus bekleyen bağlantı krediler kullanılacak iletileri gönderir.

Bir API düzeyinde "Al" çağrı dönüştürecektir bir *akış* performative Service Bus istemci tarafından ve Service Bus gönderilen tüketir, kredi ilk kullanılabilir, kilidi ileti, kilitleme, kuyruğu'ndan yararlanarak ve Bu aktarma. Varsa ileti teslim için hazır bekleyen krediler herhangi bir bağlantı ile belirli bir varlık varış sırayla kayıtlı olarak kalır, ve iletiler kilitli ve tüm bekleyen iade kullanmak kullanılabilir oldukça aktarılan kurdu.

Bir iletinin kilidini serbest aktarımı terminal durumlarından birine kapatıldığında *kabul*, *reddedilen*, veya *yayımlanan*. Terminal durumuna olduğunda Service Bus'tan ileti silinir *kabul*. Service Bus kalır ve aktarım diğer durumlardan ulaştığında sonraki alıcıya teslim edilir. Yinelenen redleri veya sürümler nedeniyle varlık için izin verilen en yüksek teslimat sayısı ulaştığında Service Bus iletiyi otomatik olarak varlığın teslim edilemeyen iletiler kuyruğuna taşır.

Service Bus API'lerine doğrudan böyle bir seçenek bugün açığa çıkarmayın olsa da, alt düzey AMQP protokolünü istemci bağlantı kredi modeli, bir "anında iletme-style" modeli tarafından iade alma her istek için bir birim veren "çekme-style" etkileşimi etkinleştirmek için kullanabilirsiniz çok sayıda kesme KREDİLERİ bağlamak ve başka hiçbir etkileşimi olmadan kullanılabilir oldukça iletileri alırsınız. Anında iletme aracılığıyla desteklenir [MessagingFactory.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) veya [MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver) özelliği ayarları. Sıfır olmayan olduklarında AMQP istemci bağlantı kredi olarak kullanır.

Bu bağlamda ileti, ileti kablo eklenmez varlıktan alındığında varlık içinde ileti üzerindeki kilit sona erme saati başladığını anlamak önemlidir. İstemci bağlantı kredi göndererek iletileri almak için hazır olma durumu gösteren olduğunda, bu nedenle iletileri ağda etkin bir şekilde çekme ve onları işlemeye hazır beklenmektedir. Aksi takdirde iletisi bile teslim edilmeden önce ileti kilidi sona ermiştir. Bağlantı-kredi akış kontrolü kullanarak doğrudan alıcısına gönderilen kullanılabilir iletiler için hemen hazır olma durumu yansıtmalıdır.

Özet olarak, aşağıdaki bölümlerde farklı API etkileşimleri sırasında performative akışa şematik bir genel bakış sağlar. Her bölümde, farklı bir mantıksal işlemi açıklanmaktadır. Bu etkileşimlerin bazıları "tembel yani bunlar yalnızca gerekli olduğunda gerçekleştirilebilir," olabilir. İlk ileti gönderildiğinde veya istenen kadar iletiyi gönderenin oluşturma ağ etkileşim neden.

Aşağıdaki tabloda yer oklar performative akış yönü.

#### <a name="create-message-receiver"></a>İleti alıcısı oluşturma

| İstemci | Service Bus |
| --- | --- |
| --> () ekleme<br/>adı = {bağlantı adı}<br/>işleme {sayısal tanıtıcı} =<br/>Rol =**alıcı**,<br/>Kaynak = {varlık adı}<br/>Hedef {istemci bağlantı kimliği} =<br/>) |İstemci varlık alıcısı olarak ekler. |
| Service Bus yanıtları, bağlantıyı sonuna ekleme |<--(ekleme<br/>adı = {bağlantı adı}<br/>işleme {sayısal tanıtıcı} =<br/>Rol =**gönderen**,<br/>Kaynak = {varlık adı}<br/>Hedef {istemci bağlantı kimliği} =<br/>) |

#### <a name="create-message-sender"></a>İletiyi gönderenin oluşturma

| İstemci | Service Bus |
| --- | --- |
| --> () ekleme<br/>adı = {bağlantı adı}<br/>işleme {sayısal tanıtıcı} =<br/>Rol =**gönderen**,<br/>Kaynak = {istemci bağlantı kimliği}<br/>Hedef {varlık adı} =<br/>) |Eylem yok |
| Eylem yok |<--(ekleme<br/>adı = {bağlantı adı}<br/>işleme {sayısal tanıtıcı} =<br/>Rol =**alıcı**,<br/>Kaynak = {istemci bağlantı kimliği}<br/>Hedef {varlık adı} =<br/>) |

#### <a name="create-message-sender-error"></a>İletiyi gönderenin (hata) oluşturma

| İstemci | Service Bus |
| --- | --- |
| --> () ekleme<br/>adı = {bağlantı adı}<br/>işleme {sayısal tanıtıcı} =<br/>Rol =**gönderen**,<br/>Kaynak = {istemci bağlantı kimliği}<br/>Hedef {varlık adı} =<br/>) |Eylem yok |
| Eylem yok |<--(ekleme<br/>adı = {bağlantı adı}<br/>işleme {sayısal tanıtıcı} =<br/>Rol =**alıcı**,<br/>Kaynak = null,<br/>Hedef = null<br/>)<br/><br/><--(Ayır<br/>işleme {sayısal tanıtıcı} =<br/>closed=**true**,<br/>hata = {hata bilgisi}<br/>) |

#### <a name="close-message-receiversender"></a>Alıcı iletiyi kapat/gönderen

| İstemci | Service Bus |
| --- | --- |
| --> ayırma ()<br/>işleme {sayısal tanıtıcı} =<br/>closed=**true**<br/>) |Eylem yok |
| Eylem yok |<--(Ayır<br/>işleme {sayısal tanıtıcı} =<br/>closed=**true**<br/>) |

#### <a name="send-success"></a>Gönder (başarılı)

| İstemci | Service Bus |
| --- | --- |
| Aktarım ('--><br/>teslim-ID = {sayısal tanıtıcı}<br/>teslim etiketi {ikili tanıtıcı} =<br/>Kapatılan =**false**,, daha =**false**,<br/>Durum =**null**,<br/>sürdürme =**false**<br/>) |Eylem yok |
| Eylem yok |<--değerlendirme ()<br/>Rol alıcı =<br/>ilk {teslim ID} =<br/>Son {teslim ID} =<br/>settled=**true**,<br/>Durum =**kabul edildi**<br/>) |

#### <a name="send-error"></a>Gönder (hata)

| İstemci | Service Bus |
| --- | --- |
| Aktarım ('--><br/>teslim-ID = {sayısal tanıtıcı}<br/>teslim etiketi {ikili tanıtıcı} =<br/>Kapatılan =**false**,, daha =**false**,<br/>Durum =**null**,<br/>sürdürme =**false**<br/>) |Eylem yok |
| Eylem yok |<--değerlendirme ()<br/>Rol alıcı =<br/>ilk {teslim ID} =<br/>Son {teslim ID} =<br/>settled=**true**,<br/>Durum =**reddedilen**()<br/>hata = {hata bilgisi}<br/>)<br/>) |

#### <a name="receive"></a>Al

| İstemci | Service Bus |
| --- | --- |
| Akış ('--><br/>bağlantı-kredi = 1<br/>) |Eylem yok |
| Eylem yok |< (Aktarım<br/>teslim-ID = {sayısal tanıtıcı}<br/>teslim etiketi {ikili tanıtıcı} =<br/>settled=**false**,<br/>Daha fazla =**false**,<br/>Durum =**null**,<br/>sürdürme =**false**<br/>) |
| --> disposition(<br/>Rol =**alıcı**,<br/>ilk {teslim ID} =<br/>Son {teslim ID} =<br/>settled=**true**,<br/>Durum =**kabul edildi**<br/>) |Eylem yok |

#### <a name="multi-message-receive"></a>Birden çok ileti alma

| İstemci | Service Bus |
| --- | --- |
| Akış ('--><br/>bağlantı-kredi = 3<br/>) |Eylem yok |
| Eylem yok |< (Aktarım<br/>teslim-ID = {sayısal tanıtıcı}<br/>teslim etiketi {ikili tanıtıcı} =<br/>settled=**false**,<br/>Daha fazla =**false**,<br/>Durum =**null**,<br/>sürdürme =**false**<br/>) |
| Eylem yok |< (Aktarım<br/>teslim-ID = {sayısal tanıtıcı + 1}<br/>teslim etiketi {ikili tanıtıcı} =<br/>settled=**false**,<br/>Daha fazla =**false**,<br/>Durum =**null**,<br/>sürdürme =**false**<br/>) |
| Eylem yok |< (Aktarım<br/>teslim-ID = {sayısal tanıtıcı + 2},<br/>teslim etiketi {ikili tanıtıcı} =<br/>settled=**false**,<br/>Daha fazla =**false**,<br/>Durum =**null**,<br/>sürdürme =**false**<br/>) |
| --> disposition(<br/>Rol alıcı =<br/>ilk {teslim ID} =<br/>Son = {teslim kimliği + 2}<br/>settled=**true**,<br/>Durum =**kabul edildi**<br/>) |Eylem yok |

### <a name="messages"></a>İletiler

Aşağıdaki bölümlerde, hangi özellikler standart AMQP ileti bölümlerden Service Bus tarafından kullanılır ve bunların hizmet veri yolu API'sini kümesine nasıl eşleneceğine açıklanmaktadır.

AMQP için 's tanımlar için uygulaması gereken herhangi bir özellik eşlenmelidir `application-properties` eşleme.

#### <a name="header"></a>üst bilgi

| Alan Adı | Kullanım | API adı |
| --- | --- | --- |
| durable |- |- |
| priority |- |- |
| ttl |Bu iletinin yaşam süresi |[TimeToLive](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| first-acquirer |- |- |
| delivery-count |- |[DeliveryCount](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="properties"></a>properties

| Alan Adı | Kullanım | API adı |
| --- | --- | --- |
| ileti kimliği |Bu ileti için uygulama tanımlı, serbest biçimli tanımlayıcı. Yinelenen algılama için kullanılır. |[MessageID](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Kullanıcı Kimliği |Service Bus tarafından yorumlanır değil, uygulama tanımlı kullanıcı tanımlayıcısı. |Service Bus API'sini aracılığıyla erişilebilir değil. |
| to |Service Bus tarafından yorumlanır değil, hedef uygulama tanımlı tanımlayıcısı. |[Alıcı](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| subject |Service Bus tarafından yorumlanır değil, uygulama tarafından tanımlanan ileti amaçlı tanımlayıcısı. |[Etiket](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Yanıtla |Uygulama tanımlı yanıt yolu göstergesi, Service Bus tarafından yorumlanır değil. |[replyTo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Bağıntı Kimliği |Service Bus tarafından yorumlanır değil, uygulama tanımlı bağıntı tanımlayıcısı. |[Bağıntı Kimliği](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| içerik türü |İçerik türü, Service Bus tarafından yorumlanır değil gövdesi için uygulama tanımlı göstergesi. |[contentType](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| İçerik kodlama |Service Bus tarafından yorumlanır değil gövdesi için göstergesi içerik kodlamasını uygulama tanımlı. |Service Bus API'sini aracılığıyla erişilebilir değil. |
| süre sonu mutlak |Hangi mutlak anında iletinin geçerlilik süresinin bildirir. Giriş (üstbilgisi TTL gözlemlenen değerdir), göz ardı çıktıyı yetkili. |[ExpiresAtUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| oluşturma zamanı |Bildirir, aynı zamanda iletinin oluşturulduğu. Service Bus tarafından kullanılmıyor |Service Bus API'sini aracılığıyla erişilebilir değil. |
| Grup Kimliği |Uygulama tanımlı ilgili bir dizi ileti için tanımlayıcı. Service Bus oturumları için kullanılır. |[oturum kimliği](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |
| Grup Sırası |İletinin bir oturumu içinde göreli sıra numarası tanımlayan sayacı. Service Bus tarafından yok sayılır. |Service Bus API'sini aracılığıyla erişilebilir değil. |
| yanıt için Grup Kimliği |- |[ReplyToSessionId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) |

#### <a name="message-annotations"></a>İleti ek açıklamaları

AMQP ileti özellikleri bir parçası değildir ve boyunca olarak geçirilir birkaç diğer service bus ileti özellikleri vardır `MessageAnnotations` ileti üzerinde.

| Ek açıklama harita anahtarı | Kullanım | API adı |
| --- | --- | --- |
| x-opt-zamanlanan-kuyruğa-saat | Bildirir, aynı zamanda varlık üzerinde ileti görüntülenmelidir |[ScheduledEnqueueTime](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.scheduledenqueuetimeutc?view=azure-dotnet) |
| x iyileştirilmiş bölüm anahtarı | Hangi bölümünün belirleyen uygulama tanımlı anahtar içinde ileti ulaşmalı. | [partitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey?view=azure-dotnet) |
| x-opt-aracılığıyla-bölüm anahtarı | Aktarım kuyruk aracılığıyla iletileri göndermek için kullanılacak bir işlem olduğunda uygulamada tanımlanan bölüm anahtarı değeri. | [ViaPartitionKey](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.viapartitionkey?view=azure-dotnet) |
| x iyileştirilmiş sıraya zaman | Hizmet tanımlı UTC saati enqueuing ileti gerçek süresini temsil eden. Giriş yok sayılır. | [EnqueuedTimeUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedtimeutc?view=azure-dotnet) |
| x iyileştirilmiş sıra numarası | Bir ileti atanan hizmet tarafından tanımlanan benzersiz bir numara. | [sequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber?view=azure-dotnet) |
| x iyileştirilmiş uzaklığı | İletinin sıra numarası sıraya alınan hizmet tanımlı. | [EnqueuedSequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.enqueuedsequencenumber?view=azure-dotnet) |
| x-opt-kilitli-kadar | Hizmet tanımlı. Tarih ve saat yapılana kadar kuyruk/aboneliğe ileti kilitlenir. | [LockedUntilUtc](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.lockeduntilutc?view=azure-dotnet) |
| x iyileştirilmiş teslim edilemeyen iletiler kaynak | Hizmet tanımlı. Edilemeyen kuyruktan kaynak özgün iletinin ileti alınmazsa. | [DeadLetterSource](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.deadlettersource?view=azure-dotnet) |

### <a name="transaction-capability"></a>İşlem özelliği

Bir işlem, iki veya daha fazla işlem yürütme kapsam birleştirerek gruplandırır. Doğası gereği bu tür bir işlem işlemlerinin belirli bir gruba ait tüm işlemleri başarılı veya başarısız ortaklaşa emin olmanız gerekir.
İşlemler tarafından bir tanımlayıcı gruplandırılır `txn-id`.

İşlem etkileşimi için istemci gören bir `transaction controller` , birlikte gruplandırılmalıdır işlemleri kontrol eder. Service Bus hizmeti görür bir `transactional resource` ve iş tarafından istenen şekilde gerçekleştirir `transaction controller`.

İstemci ve hizmet üzerinden iletişim bir `control link` , istemci tarafından oluşturulmuş. `declare` Ve `discharge` iletileri, denetleyici tarafından ayırmak ve işlemleri sırasıyla tamamlamak için denetim bağlantısı üzerinden gönderilir (işlem tabanlı iş düzenleme temsil ettikleri değil). Gerçek gönderme ve alma gerçekleştirilmez bu bağlantıya. İstenen işlem her işlem açıkça olduğundan istenen ile tanımlanan `txn-id` ve bu nedenle herhangi bir bağlantı bağlantıda ortaya çıkabilir. Oluşturulan olmayan taburcu işlemleri varken denetim bağlantıyı kapattıysanız, ardından tüm işlemleri hemen geri alınır ve bunlar üzerinde daha fazla işlem tabanlı iş gerçekleştirmeyi dener hatasına neden. Denetimi bağlantı iletileri önceden kapatılmış olması gerekir.

İlk ve son işlemler için kendi denetimi bağlantı başlatmak her bağlantısı vardır. Hizmet olarak işlevler özel bir hedef tanımlar bir `coordinator`. İstemci/denetleyicisi bu hedef denetim bağlantı kurar. Denetim bağlantı varlığın sınırları dışında diğer bir deyişle, aynı denetim bağlantısını başlatmak ve taburcu işlemleri için birden fazla varlık için kullanılabilir.

#### <a name="starting-a-transaction"></a>Bir işlem başlatılıyor

İşlem işe başlamak için. Denetleyici almalısınız bir `txn-id` Düzenleyicisi'nden. Bunu göndererek yapar bir `declare` iletiyi yazın. Bildirimi başarılı olursa Düzenleyici atanan taşıyan bir değerlendirme sonucu ile yanıt veren `txn-id`.

| İstemci (denetleyicisi) | | Hizmet veri yolu (Düzenleyicisi) |
| --- | --- | --- |
| (ekleme<br/>adı = {bağlantı adı}<br/>... ,<br/>Rol =**gönderen**,<br/>Hedef =**Düzenleyicisi**<br/>) | ------> |  |
|  | <------ | (ekleme<br/>adı = {bağlantı adı}<br/>... ,<br/>target=Coordinator()<br/>) |
| Aktarım)<br/>teslim-id = 0,...)<br/>{ AmqpValue (**Declare()** )}| ------> |  |
|  | <------ | Değerlendirme) <br/> İlk = 0, 0 = <br/>Durum =**Declared**()<br/>**işlemleri kimliği**{işlem kimliği} =<br/>))|

#### <a name="discharging-a-transaction"></a>Bir işlem discharging

Denetleyici göndererek işlem tabanlı iş sonucuna bir `discharge` Düzenleyici ileti. Denetleyici tamamlama veya işlem tabanlı iş ayarlayarak geri istediği gösterir `fail` taburcu gövdesi bayrağı. Düzenleyici taburcu tamamlayamıyor ise, ileti ile bu sonucu taşıyan reddedilir `transaction-error`.

> Not: başarısız = true başvuran bir işlem ve hata geri almak için = false kaydetmeye başvuruyor.

| İstemci (denetleyicisi) | | Hizmet veri yolu (Düzenleyicisi) |
| --- | --- | --- |
| Aktarım)<br/>teslim-id = 0,...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | Değerlendirme) <br/> İlk = 0, 0 = <br/>Durum = bildirilen ()<br/>işlemleri kimliği = {işlem ID}<br/>))|
| | . . . <br/>İşlem çalışma<br/>diğer bağlantılar<br/> . . . |
| Aktarım)<br/>teslim-id = 57,...)<br/>{ AmqpValue (<br/>**Taburcu (işlemleri-id = 0,<br/>başarısız = false)** )}| ------> |  |
| | <------ | Değerlendirme) <br/> first=57, last=57, <br/>Durum =**kabul()** )|

#### <a name="sending-a-message-in-a-transaction"></a>Bir işlemde bir ileti gönderme

Tüm işlem iş işlem teslim durumu ile yapılır `transactional-state` , işlemleri kimliği taşır. İleti gönderme söz konusu olduğunda durumu işlemsel ileti aktarım çerçeve tarafından yürütülür. 

| İstemci (denetleyicisi) | | Hizmet veri yolu (Düzenleyicisi) |
| --- | --- | --- |
| Aktarım)<br/>teslim-id = 0,...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | Değerlendirme) <br/> İlk = 0, 0 = <br/>Durum = bildirilen ()<br/>işlemleri kimliği = {işlem ID}<br/>))|
| Aktarım)<br/>tanıtıcı = 1,<br/>teslim-id = 1, <br/>**Durum =<br/>TransactionalState (<br/>işlemleri-id = 0)** )<br/>{} Yükü| ------> |  |
| | <------ | Değerlendirme) <br/> ilk = 1, 1 = <br/>Durum =**TransactionalState (<br/>işlemleri-id = 0,<br/>outcome=Accepted()** ))|

#### <a name="disposing-a-message-in-a-transaction"></a>Bir işlemde bir ileti ile atılıyor

İleti değerlendirme gibi işlemler içeren `Complete`  /  `Abandon`  /  `DeadLetter`  /  `Defer`. Bir işlem içinde bu işlemleri gerçekleştirmek için geçirin `transactional-state` değerlendirme ile.

| İstemci (denetleyicisi) | | Hizmet veri yolu (Düzenleyicisi) |
| --- | --- | --- |
| Aktarım)<br/>teslim-id = 0,...)<br/>{ AmqpValue (Declare())}| ------> |  |
|  | <------ | Değerlendirme) <br/> İlk = 0, 0 = <br/>Durum = bildirilen ()<br/>işlemleri kimliği = {işlem ID}<br/>))|
| | <------ |Aktarım)<br/>tanıtıcı, 2 =<br/>teslim Kimliği 11 = <br/>Durum = null)<br/>{} Yükü|  
| Değerlendirme) <br/> ilk 11 =, 11, son = <br/>Durum =**TransactionalState (<br/>işlemleri-id = 0,<br/>outcome=Accepted()** ))| ------> |


## <a name="advanced-service-bus-capabilities"></a>Service Bus Gelişmiş Özellikler

Bu bölümde, Azure Service Bus'ın amqp, Taslak uzantıları için AMQP OASIS teknik komitesi içinde şu anda geliştirilmekte dayalı gelişmiş özelliklerini kapsar. Service Bus, bu taslakları en son sürümlerine uygular ve bu taslakları standart durumu ulaşırken sunulan değişiklikler devralır.

> [!NOTE]
> Service Bus Mesajlaşma hizmeti Gelişmiş işlemler bir istek/yanıt modeli aracılığıyla desteklenir. Bu işlemlerin ayrıntılarını makalesinde açıklanan [hizmet veri yolu AMQP 1.0: istek-yanıt tabanlı işlemler](service-bus-amqp-request-response.md).
> 
> 

### <a name="amqp-management"></a>AMQP Yönetimi

Bu makalede ele alınan taslak Uzantıları'nın ilk AMQP yönetim özelliğidir. Bu belirtim ve AMQP protokolünü üzerinde katmanlanmış protokolleri, Mesajlaşma altyapısı sayesinde yönetim etkileşimleri AMQP üzerinden izin kümesini tanımlar. Belirtimi genel işlemleri gibi tanımlar *oluşturma*, *okuma*, *güncelleştirme*, ve *Sil* içinde bunları yönetmek için bir Mesajlaşma altyapısı ve sorgu işlemleri bir dizi.

Bu hareketlerini Mesajlaşma altyapısı ile istemci arasında bir istek/yanıt etkileşimi gerektirir ve bu nedenle bu etkileşim modeli AMQP üstünde nasıl belirtimi tanımlar: Mesajlaşma altyapısı için istemci bağlanır bir oturum başlatır ve ardından bir çift bağlantı oluşturur. Bir bağlantı, istemci gönderen olarak davranır ve diğer alıcısı, bu nedenle bir çift yönlü kanalı olarak davranıp bağlantılar oluşturma görevi görür.

| Mantıksal işlem | İstemci | Service Bus |
| --- | --- | --- |
| İstek yanıt yolu oluşturun |--> () ekleme<br/>adı = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>Rol =**gönderen**,<br/>Kaynak =**null**,<br/>target = "myentity / $Yönetimi"<br/>) |Eylem yok |
| İstek yanıt yolu oluşturun |Eylem yok |\<--(ekleme<br/>adı = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>Rol =**alıcı**,<br/>Kaynak = null,<br/>target = "myentity"<br/>) |
| İstek yanıt yolu oluşturun |--> () ekleme<br/>adı = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>Rol =**alıcı**,<br/>Kaynak = "myentity / $Yönetimi"<br/>target = "myclient$ id"<br/>) | |
| İstek yanıt yolu oluşturun |Eylem yok |\<--(ekleme<br/>adı = {*bağlantı adı*},<br/>tanıtıcı = {*sayısal tanıtıcı*},<br/>Rol =**gönderen**,<br/>Kaynak "myentity" =<br/>target = "myclient$ id"<br/>) |

Bu bağlantılar çiftinin hazır olması, istek/yanıt uygulaması oldukça basittir: bir istek bu düzen anlayan Mesajlaşma altyapısı içinde bir varlık için gönderilen bir iletidir. Bu isteği-iletisinde, *Yanıtla* alanındaki *özellikleri* bölümü ayarlandığında *hedef* bağlantının yanıt teslim etmek için oturum tanımlayıcısı. İşleme varlık isteği işler ve ardından yanıt bağlantı üzerinden gönderir, *hedef* tanımlayıcısıyla eşleşip belirtilen *Yanıtla* tanımlayıcısı.

Düzen açıktır istemci kapsayıcı ve istemci tarafından oluşturulan tanımlayıcısını yanıt hedef için tüm istemciler arasında ve güvenlik nedenleriyle, ayrıca tahmin etmek zor benzersiz olmasını gerektirir.

Yönetim protokolünün ve aynı deseni kullanan diğer tüm protokoller için kullanılan ileti alışverişlerinde uygulama düzeyinde gerçekleşir; Yeni AMQP protokol düzeyinde hareket tanımlamayın. Uygulamalar bu uzantıları uyumlu AMQP 1.0 yığınları ile hemen avantajlarından yararlanabilmeniz bilerek, olmasıdır.

Service Bus şu anda yönetim özellikleri'nin temel özellikleri hiçbirini uygulamıyor ancak yönetim belirtimi tarafından tanımlanan istek/yanıt deseni için neredeyse tüm Gelişmiş ve talep tabanlı güvenlik özelliği için temel Aşağıdaki bölümlerde ele özellikleri:

### <a name="claims-based-authorization"></a>Talep tabanlı yetkilendirme

AMQP talep tabanlı yetkilendirme (CBS) belirtimi taslak üzerinde yönetim belirtimi istek/yanıt düzeni oluşturur ve güvenlik belirteçleri ile AMQP kullanma için genelleştirilmiş bir modeli açıklar.

AMQP giriş ele alınan varsayılan güvenlik modelini SASL üzerinde temel alır ve AMQP bağlantı el sıkışması ile tümleşir. SASL kullanarak kendisi için bir dizi mekanizmaları tanımlanmışsa, resmi olarak üzerinde SASL leans herhangi bir iletişim kuralı yararlanabilir gelen genişletilebilir bir model sağlayan avantajına sahiptir. Bu mekanizmalar arasında aktarımını kullanıcı adları ve parolalar, TLS düzeyinde güvenlik, açık kimlik doğrulama/yetkilendirme ve çok çeşitli geçirilmesine izin verildi ek mekanizmaları olmaması ifade etmek için "anonim" bağlamak için "dış" "DÜZ" içindir kimlik doğrulama ve/veya yetkilendirme kimlik bilgileri veya belirteçleri.

AMQP'ın SASL tümleştirme iki engelleri vardır:

* Tüm kimlik bilgilerini ve belirteçleri bağlantı belirlenir. Varlık başına temelinde fark yaratan bir erişim denetimi sağlamak bir Mesajlaşma altyapısıdır isteyebilirsiniz; Örneğin, bir kuyruk, ancak b sıraya göndermek taşıyıcı belirteci verme Bağlantıda bağlantılı yetkilendirme bağlamı ile tek bir bağlantı ve henüz kuyruk A ve b kuyruk için farklı erişim belirteçleri kullanmak mümkün değildir
* Erişim belirteçleri genellikle yalnızca sınırlı bir süre için geçerlidir. Bu geçerlilik belirteçleri düzenli aralıklarla yeniden almanız gerektirir ve kullanıcının erişim izinlerini değiştirilirse, yeni bir belirteç verme reddetmek için belirteci veren bir fırsat sağlar. AMQP bağlantıları, uzun süreler için son. SASL modeli yalnızca belirteç süresi dolana veya sözleşme istemcisi ile sürekli iletişim izin verme riskini kabul etmek gerekli olduğunda istemci bağlantısını kesmek için vardır ya da ileti altyapısını anlamına bağlantı zamanında bir belirteç ayarlamak için bir fırsat sağlar kimin ait erişim hakları arada iptal edilmiş.

Service Bus tarafından uygulanan AMQP CBS belirtimi zarif bir geçici çözüm hem de bu sorunların sağlar: Bir istemci erişim belirteçleri her düğümle ilişkilendirilecek ve, ileti akışı kesintiye uğratmadan süresi dolmadan önce bu belirteçleri güncelleştirmek için sağlar.

CBS adlı bir sanal yönetim düğümü tanımlar *$cbs*, Mesajlaşma altyapısı tarafından sağlanacak. Yönetim düğümünde herhangi bir düğüm ileti altyapısındaki adına belirteçleri kabul eder.

Yönetim belirtimi tarafından tanımlanan istek/yanıt exchange Protokolü harekettir. Anlamına gelir istemci bağlantıları ile bir çift oluşturur *$cbs* düğümünü ve ardından bir istek giden bağlantıyı geçirir ve gelen bağlantıya yanıt bekler.

İstek iletisinde aşağıdaki uygulama özelliklere sahiptir:

| Anahtar | İsteğe bağlı | Değer türü | Değer içeriği |
| --- | --- | --- | --- |
| İşlemi |Hayır |string |**PUT-token** |
| türü |Hayır |string |Put yöntemi uygulanan Belirtecin türü. |
| name |Hayır |string |Belirtecin geçerli olduğu "audience". |
| süre sonu |Evet |timestamp |Belirteç süre sonu zamanı. |

*Adı* özelliği ile belirteç olmalıdır ilişkili varlık tanımlar. Service Bus kuyruk veya konu/abonelik yoludur. *Türü* özelliği tanımlar belirteç türü:

| Belirteç türü | Belirteç açıklaması | Gövde türü | Notlar |
| --- | --- | --- | --- |
| amqp:jwt |JSON Web Token (JWT) |AMQP değer (dize) |Henüz kullanılamıyor. |
| amqp:swt |Basit Web belirteci (SWT) |AMQP değer (dize) |AAD/ACS tarafından verilen SWT belirteçlerini yalnızca desteklenen |
| servicebus.Windows.NET:sastoken |Service Bus SAS belirteci |AMQP değer (dize) |- |

Belirteçleri hakları confer. Service Bus hakkında üç temel hakları bilir: Gönderme, alma, "Dinleme" etkinleştirir etkinleştirir "Gönder" ve "Yönet" çağırmanın varlıklar sağlar. AAD/ACS tarafından açıkça verilen SWT belirteçlerini talepler olarak söz konusu hakları içerir. Service Bus SAS belirteçlerini ad alanı veya varlık üzerinde yapılandırılan kuralları başvurun ve bu kurallar haklarıyla yapılandırılmış. Bu kuralla ilişkili anahtar ile belirteç imzalama böylece belirteci hızlı ilgili hakları sağlar. Bir varlık kullanmayla ilişkili belirteci *put belirteci* belirteci hakları her varlık için ile etkileşim kurmak için bağlı istemci izin verir. Burada istemci vereceğine bağlantı *gönderen* rolü gerektirir "Gönder" sağ; almayı *alıcı* rolünün "Dinleme" doğru olması gerekir.

Yanıt iletisi aşağıdaki sahip *uygulama özellikleri* değerleri

| Anahtar | İsteğe bağlı | Değer türü | Değer içeriği |
| --- | --- | --- | --- |
| Durum kodu |Hayır |int |HTTP yanıt kodu **[RFC2616]** . |
| Durum açıklaması |Evet |string |Durum açıklaması. |

İstemci çağırabilirsiniz *put belirteci* sürekli olarak ve mesajlaşma altyapısı herhangi bir varlık için. Belirteçleri geçerli istemci için kapsamlı ve bağlantılı geçerli bağlantıda bağlantı düştüğünde tutulan tarafından istenen belirteçleri sunucu bıraktığı anlamına gelir.

Geçerli Service Bus uygulaması CBS yalnızca "Anonim" SASL yöntemi ile birlikte sağlar SSL/TLS bağlantı SASL el sıkışması önce her zaman mevcut olmalıdır.

Anonim mekanizması, bu nedenle seçilen bir AMQP 1.0 istemcisi tarafından desteklenmelidir. İlk bağlantı el sıkışması ilk oturumu oluşturma da dahil olmak üzere Service Bus yapıldığından emin anonim erişimi olan bağlantı oluşturma bilerek anlamına gelir.

Oturumu ve bağlantı kurulduğunda sonra bağlantılar ekleme *$cbs* düğüm ve gönderme *put belirteci* yalnızca verilen işlem isteği. Geçerli bir belirteç kullanılarak başarılı bir şekilde ayarlanmalıdır. bir *put belirteci* istek bağlantı kurulduktan sonra bazı varlık düğümünün 20 saniye içinde aksi halde bağlantı tüketicisi Service Bus tarafından düşer.

Belirteç sona erme izlemek için daha sonra istemci sorumludur. Bir belirtecin süresi dolduğunda, Service Bus tüm bağlantıları ilgili varlık bağlantı en kısa sürede bırakır. Sorunun oluşmasını önlemek için istemci düğümü için belirteç sanal aracılığıyla herhangi bir zamanda yeni bir tane değiştirebilirsiniz *$cbs* aynı yönetim düğümü *put belirteci* hareket ve olmadan alma akıştaki şekilde farklı bağlantılarda bu akışlar trafiği.

### <a name="send-via-functionality"></a>Gönderme aracılığıyla işlevi

[Gönderme aracılığıyla / aktarım gönderen](service-bus-transactions.md#transfers-and-send-via) ileri bir hedef varlık aracılığıyla başka bir varlık için belirli bir ileti veri yolu hizmet sağlayan bir işlevdir. Bu özellik, tek bir işlemde varlıklar üzerinde işlemler gerçekleştirmek için kullanılır.

Bu işlevler sayesinde, bir gönderici oluşturun ve bağlantısını kurmak `via-entity`. Bağlantı kurulurken, doğru hedef iletileri/aktarımlarının bu bağlantıyı kurmak için ek bilgi geçirilir. İliştirme başarılı silindikten sonra bu bağlantıya gönderilen tüm iletiler için otomatik olarak iletilen *hedef varlık* aracılığıyla *varlık aracılığıyla*. 

> Not: Kimlik doğrulaması sahip her ikisi için gerçekleştirilecek *varlık aracılığıyla* ve *hedef varlık* Bu bağlantı kurmadan önce.

| İstemci | | Service Bus |
| --- | --- | --- |
| (ekleme<br/>adı = {bağlantı adı}<br/>Rol göndereni =<br/>Kaynak = {istemci bağlantı kimliği}<br/>Hedef = **{aracılığıyla-entity}** ,<br/>**properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )]** ) | ------> | |
| | <------ | (ekleme<br/>adı = {bağlantı adı}<br/>Rol alıcı =<br/>Kaynak = {istemci bağlantı kimliği}<br/>Hedef = {yoluyla varlık},<br/>properties=map [(<br/>com.microsoft:transfer-destination-address=<br/>{destination-entity} )] ) |

## <a name="next-steps"></a>Sonraki adımlar

AMQP hakkında daha fazla bilgi için aşağıdaki bağlantıları ziyaret edin:

* [Hizmet veri yolu AMQP genel bakış]
* [Service Bus bölümlenmiş kuyruklar ve konular için AMQP 1.0 desteği]
* [Windows Server için hizmet veri yolu AMQP]

[this video course]: https://www.youtube.com/playlist?list=PLmE4bZU0qx-wAP02i0I7PJWvDWoCytEjD
[1]: ./media/service-bus-amqp-protocol-guide/amqp1.png
[2]: ./media/service-bus-amqp-protocol-guide/amqp2.png
[3]: ./media/service-bus-amqp-protocol-guide/amqp3.png
[4]: ./media/service-bus-amqp-protocol-guide/amqp4.png

[Hizmet veri yolu AMQP genel bakış]: service-bus-amqp-overview.md
[Service Bus bölümlenmiş kuyruklar ve konular için AMQP 1.0 desteği]: service-bus-partitioned-queues-and-topics-amqp-overview.md
[Windows Server için hizmet veri yolu AMQP]: https://msdn.microsoft.com/library/dn574799.aspx
