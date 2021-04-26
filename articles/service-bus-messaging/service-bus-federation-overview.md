---
title: İleti çoğaltma ve çapraz bölge Federasyonu-Azure Service Bus | Microsoft Docs
description: Bu makalede, Azure Service Bus ile olay çoğaltmaya ve çapraz bölge federasyona genel bakış sunulmaktadır.
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: e47f633fcd9248eab6f47936aa7c45877decc1fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880836"
---
# <a name="message-replication-and-cross-region-federation"></a>İleti çoğaltma ve bölgeler arası federasyon

Ad alanları içinde Azure Service Bus, çeşitli yönlendirme desenlerinin uygulanmasına izin vermek için, [oto iletme kullanarak zincirleme sıra ve konu abonelikleri topolojilerini oluşturmayı](service-bus-auto-forwarding.md) destekler. Örneğin, kendi gönderme veya alma izinlerine sahip oldukları ve gerektiğinde geçici olarak askıya alınılabilecek ve bunları uygulamaya özel diğer varlıklara bağlamak için adanmış kuyruklar içeren iş ortakları sağlayabilirsiniz. Ayrıca, karmaşık çok aşamalı yönlendirme topolojileri oluşturabilir ya da, konuların kuyruk benzeri aboneliklerini boşaltacak ve abone başına daha fazla depolama kapasitesine izin veren posta kutusu stili kuyruklar oluşturabilirsiniz. 

Birçok gelişmiş çözüm Ayrıca, bu ve diğer desenleri uygulamak için iletilerin ad alanı sınırları genelinde çoğaltılmasını gerektirir. İletilerin birden çok, farklı uygulama kiracılar veya birden çok farklı Azure bölgesinde ilişkili ad alanları arasında akışı olması gerekebilir. 

Çözümünüz farklı bölgelerde birden çok Service Bus ad alanını koruyacak ve iletileri kuyruklar ile konular arasında çoğaltacak ve/veya [azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure IoT Hub](../iot-fundamentals/iot-introduction.md)ya da [Apache Kafka](https://kafka.apache.org)gibi kaynak ve hedeflerle ileti alışverişi yapmanız gerekir. 

Bu senaryolar, bu makalenin odaklanmaktadır. 

## <a name="federation-patterns"></a>Federasyon desenleri

İletileri, kuyruklar ve konular gibi Service Bus varlıklar arasında veya Service Bus ile diğer kaynak ve hedefler arasında taşımak isteyebileceğiniz çok sayıda olası bekletme vardır. 

[Event Hubs](../service-bus-messaging/service-bus-federation-overview.md)için benzer bir desen kümesiyle karşılaştırıldığında, ileti kuyrukları tüketicilerinin tek bir ileti üzerinden özel sahipliğini taahhüdünü, ileti tesliminde varış sırasını korumasına ve aracının [rekabet tüketicileri](/azure/architecture/patterns/competing-consumers)arasında dengeli bir şekilde dağılımını koordine ettiğinden, kuyruk benzeri varlıkların Federasyonu daha karmaşıktır. 

Üst [sınır](https://en.wikipedia.org/wiki/CAP_theorem)kısıtlamaları da dahil olmak üzere, birden çok bölgede eşzamanlı olarak kullanılabilen bir sıranın birleştirilmiş bir görünümünü sağlamayı zorlaştırarak ve bölge dışı olarak dağıtılan [tüketicilere](/azure/architecture/patterns/competing-consumers) , iletilerin özel olarak sahipliğini almasına olanak tanıyan, en küçük bir engel vardır. Bu tür coğrafi olarak dağıtılmış bir sıra, yalnızca iletilerden değil, her iletinin teslim durumunun, müşteriler tarafından kullanılabilir hale getirilmesiyle önce tamamen tutarlı çoğaltma yapılmasını gerektirir. Tek bir kuramsal, bölgesel olarak dağıtılmış bir kuyruğun tam tutarlılığının amacı, müşterilerin Federasyon senaryolarını düşündüğünde sahip olduğu tüm Azure Service Bus müşterilerinin sahip olduğu anahtar hedefle doğrudan çakışmadır: çözümleri için en yüksek kullanılabilirlik ve güvenilirlik. 

Burada sunulan desenler kullanılabilirlik ve güvenilirliğe odaklanırken, hem bilgi kaybını hem de iletileri yinelenen olarak işlemesini en iyi şekilde önleyin. 

### <a name="resiliency-against-regional-availability-events"></a>Bölgesel kullanılabilirlik olaylarına karşı dayanıklılık 

En yüksek kullanılabilirlik ve güvenilirlik Service Bus için en üst işlem öncelikleridir, ancak bir üreticinin veya tüketicinin ağ veya ad çözümleme sorunları nedeniyle veya Service Bus varlığının geçici olarak yanıt vermemeye veya hata döndürbildiği durumlarda, atandığı "birincil Service Bus" ile konuşmasının önlediği pek çok yol vardır. Belirlenen ileti işlemcisi de kullanılamaz hale gelebilir.

Bu tür koşullar, bir olağanüstü durum kurtarma durumunda olduğu gibi bölgesel dağıtımı iptal etmek isteyeceksiniz. ancak bazı uygulamaların iş senaryosu, en son birkaç dakika veya hatta saniye içinde olmayan kullanılabilirlik olaylarından etkileniyor olabilir. Azure Service Bus, genellikle, karma bulut ortamlarında ve ağ kenarında bulunan istemcilerle (örneğin, perakende mağazalarında, Restoran, bankacılık dallarında, üretim sitelerinde, lojistik tesislerinde ve havaalanlarında) kullanılır. Farklı bir bölgedeki ikincil bir uç noktaya ulaşılabilirken, herhangi bir sitenin atanmış Service Bus uç noktasına ulaşabilmesi için bir ağ yönlendirme veya tıkanıklık sorunu olabilir. Aynı zamanda, bu sitelerden gelen ve aynı anda hem birincil hem de ikincil Service Bus uç noktalarına erişimi olmayan sistem işleme iletilerini hala açığa çıkarmayabilir. 

Ağ yönlendirme sorunlarından veya bir Service Bus varlığının geçici kullanılabilirlik sorunlarından etkilenmeye yönelik düşük iş toleransına sahip bu tür bir karma bulut ve uç uygulama için çok sayıda pratik örnek vardır. Bu kişiler, perakende sitelerdeki ödemeleri işlemeyi, Havaalanı kapıları oluşturmayı ve restoran ve restoran ve güvenilir iletişim yolunun kullanılamadığı her zaman standya yönelik mobil telefon emirlerini de içerir.

Bu kategoride üç farklı dağıtılmış desen tartışıyoruz: "tümü-etkin" çoğaltma, "etkin-Pasif" çoğaltma ve "sıvı Lover" çoğaltma. 

#### <a name="all-active-replication"></a>All-Active çoğaltma

"Tümü-etkin" çoğaltma deseninin, aynı mantıksal konunun (veya kuyruğun) etkin bir kopyasının birden çok ad alanında (veya bölgede) kullanılabilir olması ve tüm iletilerin sıraya alınmış oldukları yere bakılmaksızın tüm çoğaltmalarda kullanılabilir olması sağlanır. Düzen genellikle her yayımcıya göre ileti sırasını korur. 

![Tüm etkin desenler](media/service-bus-federation-overview/mirrored-topics.jpg)

Çizimde gösterildiği gibi, model genellikle Service Bus konu başlığı altında yer alır. Çoğaltma şemasına katılacak her ad alanı için bir konu. Bu konuların her birinde, iletilerin çoğaltılacağı diğer konuların herhangi biri için bir "çoğaltma aboneliği" bulunur. Yukarıdaki çizimde, yalnızca bir dizi konuya ve bu nedenle ilgili diğer konu için tek bir çoğaltma aboneliğine sahip olduğumuz. *{N1, N2, N3}* adlı üç ad alanına sahip bir senaryoda, ad alanı *N1* içindeki bir konu, biri *N3* *içindeki ilgili* konu için bir tane olmak üzere iki çoğaltma aboneliğine sahip olur. 

Her çoğaltma aboneliğinde, bir SQL filtresi ifadesi ( `replicated <> 1` ) ve BIR SQL eylemi () birleştiren bir kural vardır `set replicated = 1` . Kuralın filtresi, yalnızca özel özelliğin `replication` ayarlanmamışsa veya `1` Bu abonelik için uygun değere sahip olmayan iletilerin, bu aboneliğin her Seçili iletideki değer için de doğru şekilde ayarlandığını sağlar `1` . Bu efekt, ileti ilgili konuya kopyalanırsa, artık ters yönde çoğaltmaya uygun değildir ve bu nedenle çoğaltmalar arasında ileti sıçramasını önliyoruz.

İlgili kurala sahip bir abonelik, bu gibi Azure CLı kullanılarak herhangi bir konuya kolayca eklenebilir.

```azurecli

az servicebus topic subscription rule create --resource-group myresourcegroup \
   --namespace mynamespace --topic-name mytopic 
   --subscription-name replication --name replication \
   --action-sql-expression "set replication = 1" \
   --filter-sql-expression "replication IS NULL"
```

Bir kuyruğu modellemek için her konu, tüm tüketicilerinin paylaştığı yalnızca bir normal abonelikle (çoğaltma aboneliklerinden başka) sınırlandırılır.

> Tüm etkin çoğaltma modeli, konuların her birine gönderilen her iletinin bir kopyasını yerleştirir. Bu, her bölgedeki uygulama kodunuzun tüm iletileri göreceği ve işleyecek anlamına gelir.
> Bu model, verilerin birden çok bölgeye paylaşılması veya gereksiz işleme genel olarak isteniyorsa senaryolar için uygundur. Her iletiyi, düzenli bir kuyrukta olduğu gibi yalnızca bir kez işlemek gerekirse, aşağıdaki iki desenden birini göz önünde bulundurmanız gerekir.  

#### <a name="active-passive-replication"></a>Active-Passive çoğaltma

"Aktif-pasif" çoğaltma şekli, bir önceki düzenin, ileti göndermek ve almak için uygulama tarafından etkin bir şekilde kullanıldığı ve iletilerin, birincil konunun kullanılamaz durumda veya ulaşılamaz hale gelebileceği durumlar için ikincil bir konuya çoğaltılacağı önceki deseninin çeşitçeşididir. 

![Etkin Pasif model](media/service-bus-federation-overview/mirrored-topics-passive.jpg)

Bu model ve önceki model arasındaki önemli fark, çoğaltmanın birincil konudan ikincil konuya tek yönlü olduğu bir modeldir. İkincil konu hiçbir zaman birincil olmaz, ancak birincil konunun geçici olarak kullanılamaz olduğu durumlarda için bir yedekleme seçeneğidir. 

Bu düzenin kullanımı, yinelenen işlemenin en aza indirmesine yardımcı olmaya çalışır. Çoğaltma sırasında `TimeToLive` ileti özelliği, çoğaltılan iletilerde, birincili bir başarısızlığın bir yük devretmeye yol açacağından beklenen süreyi yansıtan bir süre olarak ayarlanır. Örneğin, kullanım örneği senaryonuz, birincil başlangıçtan sorunları gösteren iletilerin alınması sırasında en fazla 1 dakikalık bir tüketicinin bir yandan ikincisine geçiş gerektiriyorsa, ikincil sunucu ideal olarak birincil olarak erişebildiğiniz tüm iletilere sahip olmalıdır, ancak sorunlar görünmeden önce birinciden zaten işlenmiş olan en az sayıda ileti olmalıdır. `TimeToLive`Yineleme sırasında 2 dakika, bu süreyi iki kez ayarlarsanız ( `set sys.TimeToLive = '0:2:0'` kural eyleminde), ikincil ileti yalnızca 2 dakikalık iletileri korur ve eski olanları atar. Bu, alıcı ikinciye geçtiğinde, işlenen son sunucudan daha eski iletileri hızla okuyup atabilir ve ardından henüz görmemiş ilk iletiden işleme alabilir. Gerçek saklama süresi, belirli bir kullanım örneğine ve hızlı bir şekilde istediğiniz zamana göre değişir ve uygulamanızda ikinciye geçiş yapabilir. `TimeToLive`Ayar, birkaç saniye ile günlere kadar olan aralıkta kabul edilir. 

Uygulama ikincili kullanılırken doğrudan ikincil konuya de yayımlayabilir, bu da herhangi bir normal konu gibi davranır. İkinciye geçiş yapıldıktan sonra, tüketici, çoğaltılan iletilerin ve doğrudan ikincil üzerinde yayınlanan iletilerin bir karışımını görür. Bu nedenle, uygulama ilk olarak birinciye yeniden yayımlamayı ve tüketiciye tekrar ikinciye geçmeden önce yerel olarak yayınlanan iletilerin boşaltılmasına izin vermeyi bilmelidir. Birincil yeniden kullanılabilir hale getirildikten sonra çoğaltma otomatik olarak devam ettiğinden, tüketici bu süre boyunca birincil olarak yayımlanan yeni iletiler de alır ve bu sırada biraz daha yüksek bir gecikme süresine sahip olur. 

> Bu model, iletilerin yalnızca bir kez işlenmesi gerektiği senaryolar için uygundur. Uygulamanın ikincil ' de yük devretme penceresi süresi boyunca yinelenenleri bulacağından ve geri geçiş sırasında yinelenen öğeleri bulacağından, uygulamanın Birincilden işlediği iletileri takip etmek için birlikte çalışması gerekir.
> Serbest bırakma ölçütü en iyi uygulama tarafından sağlanmış olmalıdır `MessageId` . `EnqueuedTimeUtc`Değer aynı zamanda bir filigran göstergesi olarak da uygundur, ancak uygulamanın herhangi bir dağıtılmış sistemle birlikte birincil ve ikincil arasında bazı saat sayısına (birkaç saniye) izin verilmesi gerekir.


#### <a name="spillover-replication"></a>Sıvı Lover çoğaltması

"Kırpılan Lover" çoğaltma şekli, birden çok bölgedeki birden çok Service Bus varlığın etkin/etkin kullanımını, Service Bus sağlıklı olduğu senaryoya göre, ancak *Tüketici* bekleyen ileti sayısıyla veya devre dışı bırakmakla uğraşmak için izin verir. Bunun nedeni, tüketici işlemini destekleyen bir veritabanının yavaş veya kullanılamaz durumda olabilir. Bu model, düz kuyruklar ve konu abonelikleri ile birlikte kullanılabilir.  

![Sıvı Lover çoğaltması](media/service-bus-federation-overview/spillover.jpg)  

Çizimde gösterildiği gibi, sıvı Lover çoğaltma deseninin iletileri bir kuyruğun veya aboneliğin ilişkili [atılacak ileti sırasından](service-bus-dead-letter-queues.md) , farklı bir ad alanındaki eşleştirilmiş bir sıraya veya konuya çoğaltır. 

Bir hata durumu olmadan, iki ad alanı paralel olarak kullanılır, her biri genel ileti trafiğinin bir alt kümesini ve bu alt kümeyi işleyen ilişkili tüketicilerini alır. Tüketicilerden biri yüksek hata oranları ortaya geçirirken veya sona erdiğinde, ilgili iletiler teslim sayısını aşarak veya süresi dolduğu zaman atılacak ileti kuyruğunda sona erer. Daha sonra çoğaltma görevleri onları seçer ve eşleştirilmiş kuyrukta yeniden kuyruğa alınır ve burada bunlar daha sonra uygun olmayan tüketiciye sunulur. 

İşlem belirli bir süre içinde gerçekleşmelidir, `TimeToLive` kuyruk ve/veya iletiler için, işleme devam etmek için, aynı zamanda, örneğin, `TimeToLive` izin verilen sürenin yarısı olarak ayarlanmış olabilir.

[Tüm etkin düzende](#all-active-replication)olduğu gibi, uygulama, iletinin, kuyruk çifti arasında sıçramamaları için bir kez daha önce çoğaltıldığına, ancak bileşik bir düzende teslim edilemeyen ileti sırası olarak davranan bir yardımcı sıraya nakledilip bu iletiye bir gösterge ekleyebilir.

> Bu model, en önemli sorunun, tüketicilerinin veya kaynaklardaki kullanılabilirlik sorunlarını savunmak ve ayrıca eşleştirilmiş sıralardan birindeki trafik artışlarını yeniden dağıtmak için uygun olan senaryolar için uygundur. Ayrıca, tüketiciler her iki kuyruktan de okunmadığında ad uzaylarından birine karşı koruma sağlar, ancak süre sonu tarafından belirlenen çoğaltma gecikmesi `TimeToLive` Bu zaman penceresi içindeki iletilere ve kullanılamayan ad alanına neden olabilir. 

### <a name="latency-optimization"></a>Gecikme süresi iyileştirmesi 

Konular, bilgileri birden çok tüketiciye dağıtmak için kullanılır. Bazı durumlarda, özellikle de geniş coğrafi dağıtım içeren tüketiciler, bir konudan iletileri bir ikincil ad alanında tüketicilere yakın bir konuya çoğaltmak yararlı olabilir.

![Gecikme süresi Iyileştirmesi](media/service-bus-federation-overview/latency-optimization.jpg)  

Örneğin, bölgesel ve kıta hub 'lar arasında veri paylaşımı yaparken, bilgilerin yalnızca Hub 'lar arasında aktarılması ve tüketicilerin bu hub 'lardan verilerin kopyalarını elde etme konusunda daha etkilidir. 

Çoğaltma aktarımları toplu olarak yapılabilir, böylece müşteriler çoğunlukla iletileri tek tek alabilir ve kullanabilir. Kuzey Amerika ve Avrupa arasındaki 100 MS tabanlı bir temel ağ gecikmesi ile, her ileti, aynı bölgedeki bir varlıkla karşılaştırıldığında, iletileri almak ve bunları kapatma amacıyla uzak bir varlığa yönelik iki gidiş dönüş için 200 ms daha uzun sürer. 

### <a name="validation-reduction-and-enrichment"></a>Doğrulama, azaltma ve zenginleştirme

İletiler, kendi çözümünüzün dışındaki istemciler tarafından Service Bus kuyruğu veya konuya gönderilebilir.

![Doğrulama, azaltma, zenginleştirme](media/service-bus-federation-overview/validation.jpg)  

Bu tür iletiler, belirli bir şemayla uyumluluğu denetlemeyi gerektirebilir ve uyumlu olmayan iletilerin bırakılmasına veya kullanımdan kaldırılmasına izin verebilir. Bazı iletiler verileri atlayarak karmaşıklığa karşı azaltılmalıdır ve başvuru verileri aramalarına göre verilerin eklenmesiyle bazılarının zenginleştirmesi gerekebilir. İşlemler, çoğaltma görevinde özel işlevlerle gerçekleştirilebilir. 

### <a name="stream-to-queue-replication"></a>Kuyruğa akışa çoğaltma

Azure Event Hubs, gelen olayların Extreme hacimlerini işlemeye yönelik ideal bir çözümdür. Ancak, Apache Kafka gibi Event Hubs veya benzer altyapılar, birden fazla tüketicinin yinelenen işleme karşı aynı kaynaktaki iletileri aynı anda işleyebildiği, hizmet tarafından yönetilen [rekabet eden bir tüketici](/azure/architecture/patterns/competing-consumers) modeli sağlar ve son olarak bu iletileri işlendikten sonra kapatın. 

![Tümleştirme](media/service-bus-federation-overview/hub-to-queue.jpg)

Kuyruk çoğaltması akışı, tek bir olay hub 'ı bölümünün içeriğini veya tam bir olay hub 'ının içeriğini iletilerin güvenli, işlem içinde ve rekabet tüketicileriyle birlikte işlenebildiği Service Bus kuyruğuna aktarır. Bu çoğaltma, konular ve oturum tabanlı çoğullama ile yönlendirme de dahil olmak üzere, bu iletiler için diğer tüm Service Bus yeteneklerini de kullanmanıza imkan tanıyor. 

### <a name="consolidation-and-normalization"></a>Birleştirme ve normalleştirme 

Genel çözümler genellikle kendi işleme özelliklerine sahip olmak dahil olmak üzere büyük ölçüde bağımsız olan bölgesel fikirlerden oluşur ancak Supra bölgesel ve genel Perspektifler veri tümleştirmesi gerektirir ve bu nedenle, yerel perspektifte ilgili bölgesel fikirde değerlendirilen aynı ileti verilerinin merkezi bir birleştirmesi olur. 

![Dan](media/service-bus-federation-overview/merge.jpg)

Normalleştirme, iki veya daha fazla gelen ileti dizisinin aynı tür bilgileri (ancak farklı yapılar veya farklı kodlamalar ile) ve iletilerin tüketilebilmesi için dönüştürülmesini veya dönüştürülmesini sağlamalıdır. 

Normalleştirme, uçtan uca şifrelenmiş yüklerin şifresini çözme ve aşağı akış tüketicisi için farklı anahtarlar ve algoritmalarla yeniden şifreleme gibi şifreleme işi de içerebilir. 

### <a name="splitting-and-routing"></a>Bölme ve yönlendirme

Service Bus konuları ve bunların abonelik kuralları, genellikle belirli bir hedef kitle için bir ileti akışını filtrelemek için kullanılır ve bu hedef kitle, daha sonra filtrelenmiş kümeyi bir abonelikten almış olur. 

![Bölme](media/service-bus-federation-overview/split.jpg)

Bu iletiler için hedef kitleleri küresel olarak dağıtılan veya farklı uygulamalara ait olan küresel bir sistemde, çoğaltma, bu abonelikten gelen iletileri, tüketildikleri yerden farklı bir ad alanındaki kuyruğa veya konuya aktarmak için kullanılabilir.

### <a name="replication-applications-in-azure-functions"></a>Azure Işlevleri 'nde çoğaltma uygulamaları

Yukarıdaki desenleri uygulamak, yapılandırmak ve çalıştırmak istediğiniz çoğaltma görevleri için ölçeklenebilir ve güvenilir bir yürütme ortamı gerektirir. Azure 'da, durum bilgisiz görevlere en uygun çalışma zamanı ortamı [Azure işlevleridir](../azure-functions/functions-overview.md). 

Azure Işlevleri, çoğaltma görevlerinin yanı sıra gizli dizileri yönetmek zorunda kalmadan, kaynak ve hedef hizmetlerin rol tabanlı erişim denetimi kuralları ile tümleştirilen bir [Azure yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md) altında çalışabilir. Açık kimlik bilgileri gerektiren çoğaltma kaynakları ve hedefler için, Azure Işlevleri [Azure Key Vault](../key-vault/general/overview.md)içindeki sıkı erişim denetimli depolamada bu kimlik bilgileri için yapılandırma değerlerini tutabilir.

Azure Işlevleri ayrıca çoğaltma görevlerinin tüm Azure mesajlaşma hizmetleri için Azure sanal ağları ve [hizmet uç](../virtual-network/virtual-network-service-endpoints-overview.md) noktalarıyla doğrudan tümleştirilmesine olanak tanır ve [Azure izleyici](../azure-monitor/overview.md)ile kolayca tümleşiktir.

En önemlisi Azure Işlevleri, Azure [Event Hubs](../azure-functions/functions-bindings-service-bus.md), [azure IoT Hub](../azure-functions/functions-bindings-event-iot.md), [Azure Service Bus](../azure-functions/functions-bindings-service-bus.md), [Azure Event Grid](../azure-functions/functions-bindings-event-grid.md)ve [Azure kuyruk depolaması](../azure-functions/functions-bindings-storage-queue.md)için önceden oluşturulmuş, ölçeklenebilir Tetikleyiciler ve çıkış bağlamaları, [kbbitmq](https://github.com/azure/azure-functions-rabbitmq-extension)için özel uzantıları ve [Apache Kafka](https://github.com/azure/azure-functions-kafka-extension). Birçok tetikleyici, belgeli ölçümlere göre aynı anda yürütülen örneklerin sayısını ölçeklendirerek dinamik olarak işleme gereksinimlerine uyum sağlar. 

Azure Işlevleri tüketim planı sayesinde, önceden oluşturulmuş Tetikleyiciler, çoğaltma için kullanılabilir bir ileti olmadığında bile sıfıra ölçeklendirebilir, bu da yapılandırmanın ölçeklendirmeye hazır tutulması için hiçbir ücret ödemenize neden olur. Tüketim planı kullanmanın alt tarafında, bu durumdan "uyandırma" çoğaltma görevlerinin gecikmesi, altyapının çalıştığı barındırma planlarından önemli ölçüde daha yüksek bir durumdur.  

Tüm bunların aksine, Apache Kafka [Mirrormaker](http://kafka.apache.org/documentation/#basic_ops_mirror_maker) gibi ileti ve olay için en yaygın çoğaltma motorları, bir barındırma ortamı sağlamanıza ve çoğaltma altyapısını kendiniz ölçeklendirmenize gerek duyar. Bu, güvenlik ve ağ özelliklerini yapılandırmayı ve tümleştirmeyi ve izleme verilerinin akışını kolaylaştırmanın yanı sıra, yine de akışa özel çoğaltma görevleri ekleme şansınız yoktur. 

## <a name="next-steps"></a>Sonraki Adımlar

Bu makalede, Azure 'daki olay ve mesajlaşma çoğaltma çalışma zamanı gibi bir dizi Federasyon desenini araştırdık ve Azure Işlevleri rolü açıklanmıştı. 

Daha sonra, Azure Işlevleri ile bir Replicator uygulamasını nasıl ayarlayacağınızı ve ardından Event Hubs ile çeşitli diğer olay ve mesajlaşma sistemleri arasında olay akışlarını nasıl çoğaltacağınızı okumak isteyebilirsiniz:

- [Azure Işlevleri 'nde çoğaltma uygulamaları](service-bus-federation-replicator-functions.md)
- [Service Bus varlıkları arasında olayları çoğaltma](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy)
- [Olayları Azure Event Hubs yönlendirme](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub)
- [Azure Event Hubs olay alma](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/EventHubCopyToServiceBus)

[1]: ./media/service-bus-auto-forwarding/IC628632.gif