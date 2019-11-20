---
title: Sık sorulan sorular - Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Event Hubs ve bunların cevapları için sık sorulan sorular (SSS) bir listesini sağlar.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 05/15/2019
ms.author: shvija
ms.openlocfilehash: 66b11ef8e746222074eadab2348f8a2cf9dab39f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479142"
---
# <a name="event-hubs-frequently-asked-questions"></a>Olay hub'ları hakkında sık sorulan sorular

## <a name="general"></a>Genel

### <a name="what-is-an-event-hubs-namespace"></a>Bir Event Hubs ad alanı nedir?
Bir ad alanı için olay hub'ı / Kafka konularını kapsayan bir kapsayıcıdır. Bu, benzersiz bir veren [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Bir ad alanı, birden çok olay hub'ı / Kafka konularını barındırmak bir uygulama kapsayıcısı görev yapar. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Ne zaman yeni bir ad alanı oluşturabilirim? var olan bir ad alanını kullan?
Kapasite ayırmaları ([işleme birimleri (DTU 'lar)](#throughput-units)) ad alanı düzeyinde faturalandırılır. Bir ad alanı aynı zamanda bir bölgeyle ilişkilendirilir.

Aşağıdaki senaryolardan birinde var olan birini kullanmak yerine yeni bir ad alanı oluşturmak isteyebilirsiniz: 

- Yeni bir bölgeyle ilişkili bir olay hub 'ı gerekir.
- Farklı bir abonelikle ilişkili bir olay hub 'ı gerekir.
- Ayrı kapasite ayırması olan bir olay hub 'ınız olması gerekir (yani, eklenen Olay Hub 'ına sahip ad alanı için kapasite gereksinimi 40 TU eşiğini aşacağından ve adanmış kümeye gitmek istemezsiniz)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs temel ve standart katmanları arasındaki fark nedir?

Azure Event Hubs standart katmanı temel katmana sistemlerdekinden özellikleri sağlar. Standart aşağıdaki özellikler eklenmiştir:

* Olay saklama uzun
* Dahil edilen sayıdan daha fazla bilgi için bir fazla kullanım ücreti olan ek aracılı bağlantılar
* Birden fazla [tüketici grubu](event-hubs-features.md#consumer-groups)
* [Yakalama](event-hubs-capture-overview.md)
* [Kafka tümleştirme](event-hubs-for-kafka-ecosystem-overview.md)

Fiyatlandırma katmanları, Event Hubs adanmış, dahil olmak üzere hakkında daha fazla bilgi için bkz. [olay hub'ları fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Azure Event Hubs nerede kullanılabilir?

Azure Event hubs'ı desteklenen tüm Azure bölgelerinde kullanılabilir. Bir liste için ziyaret [Azure bölgeleri](https://azure.microsoft.com/regions/) sayfası.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Birden fazla event hubs'dan alıp göndermek için tek bir AMQP bağlantısı kullanabilir miyim?

Tüm event hubs aynı ad alanında olduğu sürece Evet.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Olaylar için maksimum bekletme süresi nedir?

Event Hubs standart katmanı şu anda yedi günde bir en yüksek bekletme süresi destekler. Olay hub'larının kalıcı veri deposu olarak amaçlanmamıştır. 24 saatten fazla uzun saklama süreleri, olay akışının aynı sistemlerde yeniden Yürüt kullanışlı olduğu senaryolar yöneliktir; Örneğin, denenmesi veya doğrulanması var olan veriler üzerinde yeni bir makine öğrenme modeli. Yedi gün sonra saklama ileti varsa, etkinleştirme [Event Hubs yakalama](event-hubs-capture-overview.md) depolama hesabı ya da Azure Data Lake hizmeti hesabı seçtiğiniz hub, olayı, olay hub'ından veri çeker. Etkinleştirme yakalama, satın alınan işleme birimlerine göre bir ücret doğurur.

### <a name="how-do-i-monitor-my-event-hubs"></a>My Event Hubs'ı nasıl izleyebilirim?
Olay hub'ları için kaynaklarınızın durumunu sağlayan ayrıntılı ölçümler yayan [Azure İzleyici](../azure-monitor/overview.md). Ayrıca, yalnızca ad alanı düzeyinde aynı zamanda varlık düzeyinde Event Hubs hizmeti genel durumunu değerlendirmek sağlarlar. Hangi izleme hakkında için sunulan bilgi [Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Güvenlik duvarında hangi bağlantı noktalarını açmalıyım? 
Aşağıdaki protokolleri, ileti göndermek ve almak için Azure Service Bus ile birlikte kullanabilirsiniz:

- Gelişmiş İleti Sıraya Alma Protokolü (AMQP)
- HTTP
- Apache Kafka

Azure Event Hubs ile iletişim kurmak için bu protokolleri kullanmak üzere açmanız gereken giden bağlantı noktaları için aşağıdaki tabloya bakın. 

| Protocol | Bağlantı Noktaları | Ayrıntılar | 
| -------- | ----- | ------- | 
| AMQP | 5671 ve 5672 | Bkz. [AMQP protokol Kılavuzu](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Bkz. [Kafka uygulamalardan Event Hubs kullanma](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Beyaz listeye hangi IP adreslerine ihtiyacım var?
Bağlantılarınız için beyaz listeye doğru IP adreslerini bulmak için şu adımları izleyin:

1. Komut isteminden aşağıdaki komutu çalıştırın: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. ' De `Non-authoritative answer`döndürülen IP adresini aklınızda edin. Ad alanını farklı bir kümeye geri yükledikten sonra değişikliğin değiştirileceği tek zaman nokta.

Ad alanınız için bölge yedekliliği kullanırsanız, birkaç ek adım yapmanız gerekir: 

1. İlk olarak, ad alanında Nslookup ' ı çalıştırırsınız.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. **Yetkili olmayan yanıt** bölümündeki adı aşağıdaki biçimlerden birinde olan bir yere göz önünde edin: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Her biri için S1, S2 ve S3 sonekleri ile her biri için Nslookup ' ı çalıştırarak üç kullanılabilirlik alanında çalışan üç örnek için IP adreslerini alın. 

## <a name="apache-kafka-integration"></a>Apache Kafka tümleştirme

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Event Hubs ile mevcut Kafka uygulamamı nasıl tümleştirebilirim?
Event Hubs, mevcut Apache Kafka tabanlı uygulamalarınız tarafından kullanılabilecek bir Kafka uç noktası sağlar. Bir yapılandırma değişikliği PaaS Kafka deneyimi sağlamak için gerekli olan. Bu, kendi Kafka kümesi çalıştırmak için bir alternatif sağlar. Event Hubs, Apache Kafka 1.0 ve daha yeni istemci sürümlerini destekler ve mevcut Kafka uygulamalarınızın, araçları ve çerçeveleri ile çalışır. Daha fazla bilgi için [Kafka depo için Event Hubs](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Yapılandırma değişiklikleri mevcut Uygulamam Event hubs'ı kurmak için yapılması gerekenler?
Bir Kafka özellikli olay Hub'ına bağlanmak için Kafka istemcisi yapılandırmalarını güncelleştirmeniz gerekir. Bir Event Hubs ad alanı oluşturma ve alma yapıldığını [bağlantı dizesi](event-hubs-get-connection-string.md). Bootstrap.servers 9093 için olay hub'ları FQDN ve bağlantı noktasını işaret edecek şekilde değiştirin. Kafka istemci (aynı alınan bağlantı dizesi) Event Hubs, Kafka etkin uç noktanıza doğrudan sasl.jaas.config güncelleştirmesi, ile düzeltmesi aşağıda gösterildiği gibi kimlik doğrulama:

Bootstrap.Servers={Your. EVENTHUBS. FQDN}: 9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule gereken kullanıcı adı = "$ConnectionString" parola = "{YOUR. EVENTHUBS. BAĞLANTI. DİZE} ";

Örnek:

Bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule gereken kullanıcı adı = "$ ConnectionString"password="Endpoint=sb://dummynamespace.servicebus.windows.net/; SharedAccessKeyName DummyAccessKeyName; = SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Not: SASL. jaas. config, ortamınızda desteklenen bir yapılandırma değilse, SASL Kullanıcı adı ve parolasını ayarlamak için kullanılan yapılandırmaları bulun ve bunun yerine bunları kullanın. Kullanıcı adı $ConnectionString ve Event hubs'ı bağlantı dizenizi parolasını ayarlayın.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Event hubs, Kafka etkin ileti/olay boyutu nedir?
Kafka özellikli Event Hubs için izin verilen en büyük ileti boyutu 1 MB'dir.

## <a name="throughput-units"></a>İşleme birimleri

### <a name="what-are-event-hubs-throughput-units"></a>Event Hubs işleme birimleri nedir?
Aktarım hızı olay hub'larındaki veri miktarı mega bayt veya 1 KB'lık olaylar (bin başına) sayısı, giriş ve çıkış Event Hubs ile tanımlar. Bu aktarım hızı, aktarım hızı birimlerini (işleme birimi) ölçülür. Event Hubs hizmeti kullanmaya başlayabilmeniz için önce işleme birimi satın alın. Açıkça ya da portalı veya olay hub'ları Resource Manager şablonlarını kullanarak Event Hubs işleme birimi seçebilirsiniz. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Üretilen iş birimleri, bir ad alanındaki tüm event hubs için uygulanır mı?
Evet, Event Hubs ad alanındaki tüm event hubs işleme birimleri (işleme birimi) uygulanır. Ad alanı düzeyinde işleme birimi satın alabilir ve bu ad alanı altında olay hub'ları arasında paylaşılan anlamına gelir. Her işleme birimi ad alanına aşağıdaki özellikleri sağlar:

- Yukarı giriş olayı (bir olay hub'ına gönderilen olaylar), ancak hiçbir fazla 1000 giriş olayı, yönetim işlemleri veya denetim saniyede 1 MB'a kadar saniye başına API çağrısı.
- En fazla saniye başına 2 MB çıkış olayı (bir olay hub'ınden tüketilen olaylar), ancak en fazla 4096 çıkış olayı.
- 84 GB'ye kadar olay depolama (varsayılan 24 saatlik saklama süresi için yeterli).

### <a name="how-are-throughput-units-billed"></a>Üretilen iş birimleri nasıl faturalandırılır?
Üretilen iş birimleri (işleme birimi), saatlik olarak faturalandırılır. Belirli bir saatte seçilen maksimum birim sayısını göre faturalandırılır. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>My üretilen iş birimlerine göre nasıl kullanım iyileştirebilirim?
Bir işleme birimi (işleme birimi) düşük başlatmak ve etkinleştirmek [otomatik şişme](event-hubs-auto-inflate.md). Otomatik şişme özellik trafiği/yük arttıkça, işleme birimi büyümesine olanak sağlar. İşleme birimi sayısına göre üst sınırını da ayarlayabilirsiniz.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Event Hubs'ın otomatik şişme özelliği nasıl çalışır?
Otomatik şişme, işleme birimleri (işleme birimi) ölçeği özelliği sağlar. Düşük işleme birimi satın alarak başlayın ve Otomatik ölçek, işleme birimi girişinizi arttıkça şişme anlamına gelir. Bu size uygun maliyetli bir seçenek ve tam denetim işleme birimi sayısı yönetmenizi sağlar. Bu özellik bir **yalnızca büyümenizi** özellik ve tamamen denetim işleme birimi sayısı güncelleştirerek ölçeği azaltma. 

Düşük aktarım hızı birimlerini (işleme birimi), örneğin, 2 işleme birimi başlamak isteyebilirsiniz. Trafiğiniz 15 işleme birimi için büyüme tahmin, etkinleştirme özelliği ad alanınızı otomatik şişme ve 15 işleme birimi için üst sınırı ayarlayın. Trafiğiniz büyüdükçe artık, işleme birimi otomatik olarak büyüyebilir.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>I açtığınızda ilişkilendirilmiş bir maliyet yoktur otomatik şişme özellik?
Var. **ücretsiz** bu özellik ile ilişkilendirilmiş. 

### <a name="how-are-throughput-limits-enforced"></a>Aktarım hızı sınırlarını nasıl zorlanır?
Toplam giriş üretilen işi veya bir ad alanındaki tüm event hubs'da toplam giriş olayı oranı toplam üretilen iş birimi kullanım sınırlarını aşıyorsa, Gönderenler azaltılır ve giriş kotasının aşıldığını belirten hatalar alır.

Toplam çıkış üretilen işi veya toplam çıkış olayı oranı, bir ad alanındaki tüm event hubs'da toplam üretilen iş birimi kullanım sınırlarını aşıyorsa, alıcılar azaltılır ve çıkış kotasının aşıldığını belirten hatalar alır. Böylece hiçbir gönderen olay tüketiminin yavaşlamasına neden olmaz ya da bir alıcı bir olay hub'ına gönderilen olaylar engellemez, giriş ve çıkış kotaları ayrı ayrı uygulanır.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Ayrılmış ve seçilen işleme birimleri (işleme birimi) sayısına bir sınır var mıdır?
Sunan çok kiracılı üzerinde üretilen iş birimleri (portalda en fazla 20 işleme birimi seçin ve aynı ad alanında 40 işleme birimi için yükseltmek için bir destek bileti Yükselt) en fazla 40 işleme birimi büyüyebilir. 40 işleme birimi, Event Hubs adlı Kaynak/Kapasite tabanlı modeli sunar. **Event Hubs Dedicated kümeleri**. Adanmış kümeleri kapasite birimleri (cu) olarak satılır.

## <a name="dedicated-clusters"></a>Adanmış kümeleri

### <a name="what-are-event-hubs-dedicated-clusters"></a>Event Hubs Dedicated kümeleri nelerdir?
Tek kiracılı dağıtımları En zorlu gereksinimler olan müşteriler için Event Hubs Dedicated kümeleri sunar. Bu teklif, işleme birimleri tarafından bağlanmadı kapasite tabanlı bir küme oluşturur. Bu, alma ve küme tarafından CPU ve bellek kullanımı gibi veri akışı kümeye yazılımınız anlamına gelir. Daha fazla bilgi için [Event Hubs Dedicated kümeleri](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Ne kadar tek kapasite birimi elde bana?
Ayrılmış bir küme için ne kadar içe alma akış ve, üreticiler, Tüketiciler, hangi, başlayan kümeniz işleme ve oranı ve çok daha fazlası gibi çeşitli faktörlere bağlıdır. 

Aşağıdaki tabloda, bizim test sırasında alanımız Kıyaslama sonuçlar gösterilir:

| Yükü şekli | Alıcılar | Giriş bant genişliği| Giriş iletileri | Çıkış bant genişliği | Çıkış iletileri | Toplam işleme birimi | CU başına işleme birimi |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB toplu | 2 | 400 MB/sn | 400 bin iletileri/sn | 800 MB/sn | 800 k iletileri/sn | 400 işleme birimi | 100 işleme birimi | 
| 10x10KB toplu | 2 | 666 MB/sn | 66.6 k iletileri/sn | 1.33 GB/sn | 133 k iletileri/sn | 666 işleme birimi | 166 işleme birimi |
| 6x32KB toplu | 1 | 1,05 GB/sn | 34 k iletileri / sn | 1,05 GB/sn | 34 k iletileri/sn | 1000 işleme birimi | 250 işleme birimi |

Testinizde aşağıdaki ölçütleri izin kullanıldı:

- Dört kapasite birimleri (cu) ile ayrılmış bir olay hub'ları kümesi kullanıldı. 
- Alma işlemi için kullanılan olay hub'ı 200 bölümler vardı. 
- Tüm bölümleri alma iki alıcı uygulamalar tarafından alınan veri alındı.

Sonuçları ile ayrılmış bir olay hub'ları kümesi elde edilebilecek olanların hakkında fikir verir. Ayrıca, dedicate küme mikro toplu ve uzun süreli saklama senaryolarınız için etkin Event Hubs yakalama ile birlikte gelir.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Event Hubs ayrılmış bir küme nasıl oluşturulur?
Event Hubs adanmış küme göndererek oluşturmak bir [kota artışı destek isteği](https://portal.azure.com/#create/Microsoft.Support) veya başvurarak [Event Hubs ekibine](mailto:askeventhubs@microsoft.com). Kümeye dağıtılır ve sizin tarafınızdan kullanılacak. Bu sayede almak için genellikle yaklaşık iki hafta sürer. Bu işlem tamamlandığında kadar geçici, Self Servis kümeyi dağıtmak için yaklaşık iki saat sürüyor, Azure portal veya Azure Resource Manager şablonları ile kullanılabilir.

## <a name="best-practices"></a>En iyi uygulamalar

### <a name="how-many-partitions-do-i-need"></a>Kaç tane bölümler ihtiyacım var?
Bölüm sayısı, oluşturma sırasında belirtilir ve 2 ile 32 arasında olmalıdır. Bölüm sayısı değiştirilemez; bu nedenle, bölüm sayısını ayarlarken uzun vadeli ölçeği dikkate almanız gerekir. Bölümler, tüketen uygulamalarda gerekli aşağı akış paralelliğiyle ilişkili bir veri düzenleme mekanizmasıdır. Bir olay hub'ındaki bölüm sayısı, sahip olmayı beklediğiniz eşzamanlı okuyucu sayısıyla doğrudan ilgilidir. Bölümler hakkında daha fazla bilgi için bkz. [bölümler](event-hubs-features.md#partitions).

Oluşturma sırasında 32 olan mümkün olan en yüksek değer olarak ayarlamak isteyebilirsiniz. Göndericilerin, geri kalan 31 bölümden oluşan 32 ' dan yalnızca tek bir bölüme gönderilmesi için yapılandırmadığınız müddetçe, birden fazla bölüme sahip olmanın sırayı korumadan birden çok bölüme gönderilmesine neden olacağını unutmayın. Önceki durumda, tüm 32 bölümlerdeki olayları okumanız gerekir. İkinci durumda, olay Işlemcisi ana bilgisayarında yapmanız gerekek yapılandırmadan farklı bir ek maliyet yoktur.

Event Hubs, bir tüketici grubu başına tek bölüm okuyucusu izin vermek için tasarlanmıştır. Çoğu durumda, varsayılan ayarı olan dört bölüm yeterli kullanın. Olay işleme ölçeğini arıyorsanız, ek bölümler eklemeyi düşünün isteyebilirsiniz. Belirli bir aktarım hızı sınırı yoktur bir bölüme, ancak ad alanınızdaki toplam üretilen iş tarafından üretilen iş birimlerinin sayısı sınırlıdır. Ad alanınız içinde üretilen iş birimlerinin sayısı arttıkça, ek bölümler kendi en fazla aktarım hızı elde etmek eşzamanlı okuyucu izin vermek isteyebilirsiniz.

Uygulamanızı benzeşim belirli bir bölüme sahip bir model varsa, ancak bölüm sayısının artırılması, hiçbir Avantajı'ndan olmayabilir. Daha fazla bilgi için [kullanılabilirlik ve tutarlılık](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Fiyatlandırma

### <a name="where-can-i-find-more-pricing-information"></a>Diğer fiyatlandırma bilgileri nerede bulabilirim?

Event Hubs fiyatlandırması hakkında tam bilgi için bkz. [olay hub'ları fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Event Hubs olaylarını 24 saatten uzun süre tutma ücreti var mıdır?

Event Hubs standart katmanı ileti bekletme yedi günde en fazla 24 saatten uzun süreler izin vermiyor. Boyutu depolanan olaylarının toplam sayısı, seçili üretilen iş birimi (aktarım hızı birimi başına 84 GB) için depolama sınırını aşarsa, izin verilen kullanımı aşan boyut yayımlanan Azure Blob Depolama fiyatı üzerinden ücretlendirilir. Her bir üretilen iş birimindeki depolama alanı kullanım sınırı, 24 saatlik saklama süreleri için tüm depolama maliyetlerini kapsar (varsayılan) aktarım hızı birimi en büyük giriş kullanım sınırında kullanıldığında bile.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Nasıl Event hubs'ı depolama boyutu hesaplanır ücretlendirilir ve nedir?

Tüm event hubs dahili ek yükler için olay üst bilgileri veya disk depolama yapıları dahil olmak üzere, depolanmış tüm olayların toplam boyutu gün boyunca ölçülür. Günün sonunda en büyük depolama boyutu hesaplanır. Günlük depolama alanı kullanım sınırı, gün boyunca seçilen en az aktarım hızı birimi sayısına göre hesaplanır (her bir aktarım hızı birimi 84 GB'lık kullanım sınırı sağlar). Toplam boyut hesaplanan günlük depolama sınırını aşarsa, fazla depolama Azure Blob Depolama fiyatları kullanılarak faturalandırılır (adresindeki **yerel olarak yedekli depolama** oranı).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Event hubs'ı giriş olayları nasıl hesaplanır?

Olay hub'ına gönderilen her olay Faturalanabilir mesaj olarak sayılır. Bir *giriş olayı* 64 KB veya daha küçük veri birimi olarak tanımlanır. Boyutu 64 KB eşit veya daha az olan herhangi bir olay, Faturalanabilir bir olay olarak kabul edilir. Olay 64 KB'den büyükse, Faturalanabilir olayların sayısı olay boyutu 64 KB'ın katlarına göre hesaplanır. Örneğin, olay hub'ına gönderilen 8 KB'lık olay bir olay olarak faturalandırılır ancak olay hub'ına gönderilen 96 KB'lık mesaj iki olay faturalandırılır.

Yönetim işlemleri ve denetim çağrıları kontrol noktaları gibi düzgün Faturalandırılabilir giriş olayları olarak kabul edilmez, ancak en fazla aktarım hızı birimi kullanım sınırından düşülür gibi bir olay hub'ından tüketilen olayların.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Aracılı bağlantı ücretleri Event Hubs için geçerli midir?

Bağlantı ücretleri, yalnızca AMQP protokolünü kullanıldığında uygulanır. Gönderen sistem veya cihazların sayısı ne olursa olsun, HTTP kullanarak olay göndermeye ilişkin herhangi bir bağlantı ücreti yoktur. Gibi amaçlarla AMQP'yi (örneğin, daha verimli olay akışı elde etmek veya IOT komut çift yönlü iletişim sağlamak ve denetim senaryoları) kullanmayı planlıyorsanız bkz [Event Hubs fiyatlandırma bilgileri](https://azure.microsoft.com/pricing/details/event-hubs/) kaç hakkında daha fazla ayrıntı için bağlantılar, her hizmet katmanında dahil edilir.

### <a name="how-is-event-hubs-capture-billed"></a>Event Hubs Yakalama nasıl faturalandırılır?

Ad alanındaki tüm olay hub'ı yakalama seçeneği etkin olduğunda yakalama özelliği etkinleştirilir. Event Hubs yakalama, satın alınan işleme birimi başına saatlik faturalandırılır. Aktarım hızı birimi sayısı arttıkça veya azaldıkça olarak Event Hubs yakalama faturalandırması bu değişiklikleri tam saatlik adımlar olarak yansıtır. Event Hubs yakalama faturalandırması hakkında daha fazla bilgi için bkz: [Event Hubs fiyatlandırma bilgileri](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Event Hubs yakalama için seçtiğim depolama hesabına ücret yansır?

Yakalama, sağladığınız bir olay hub'ında etkinleştirildiğinde bir depolama hesabını kullanır. Bu yapılandırma için herhangi bir değişiklik, depolama hesabınız olduğu gibi Azure aboneliğinize faturalandırılır.

## <a name="quotas"></a>Kotalar

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Event Hubs ile ilişkili herhangi bir kota var mı?

Tüm Event Hubs kotaları listesi için bkz. [kotalar](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Sorun giderme

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Neden bir ad alanını başka bir abonelikten sildikten sonra oluşturamadım? 
Bir aboneliğden bir ad alanını sildiğinizde, başka bir abonelikte aynı adla yeniden oluşturmadan önce 4 saat bekleyin. Aksi takdirde, aşağıdaki hata iletisini alabilirsiniz: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Event Hubs ve önerilen eylemlerinin tarafından oluşturulan özel durumları bazıları nelerdir?

Olası Event Hubs özel durumları listesi için bkz. [özel durumlar genel bakış](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Tanılama günlükleri

Event hubs'ı destekleyen iki tür [tanılama günlükleri](event-hubs-diagnostic-logs.md) -yakalama Hata günlüklerini ve işlem günlükleri - ikisi için de json'da temsil edilir ve Azure portalı üzerinden etkinleştirilebilir.

### <a name="support-and-sla"></a>Destek ve SLA

Event Hubs için teknik destek, aracılığıyla kullanılabilir [topluluk forumları](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Faturalandırma ve abonelik yönetim desteği ücretsiz olarak sunulmaktadır.

Sunduğumuz SLA hakkında daha fazla bilgi için bkz: [hizmet düzeyi sözleşmeleri](https://azure.microsoft.com/support/legal/sla/) sayfası.

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki bağlantıları inceleyerek Event Hubs hakkında daha fazla bilgi edinebilirsiniz:

* [Event Hubs’a genel bakış](event-hubs-what-is-event-hubs.md)
* [Olay Hub’ı oluşturma](event-hubs-create.md)
* [Olay hub'ları otomatik şişme](event-hubs-auto-inflate.md)
