---
title: Apache Kafka App 'ten Olay Hub 'ı kullanma-Azure Event Hubs | Microsoft Docs
description: Bu makalede, Azure Event Hubs tarafından Apache Kafka desteği hakkında bilgi sağlanır.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: 1cd6d06b610ccccb8c80818d3bdec726ed94e875
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505323"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Apache Kafka uygulamalardan Azure Event Hubs kullanma
Event Hubs, kendi Apache Kafka kümenizi çalıştırmaya alternatif olarak, mevcut Apache Kafka istemci uygulamaları tarafından kullanılabilen Apache Kafka® üretici ve tüketici API 'Leri ile uyumlu bir uç nokta sağlar. Event Hubs, sürüm 1,0 ve üzeri sürümlerde Apache Kafka üreticisi ve tüketici API 'Lerini destekler.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Kafka için Event Hubs neler sağlar?

Apache Kafka özelliği için Event Hubs, Azure Event Hubs 'nin en üstünde yer alan ve Apache Kafka sunucu sürümleri 1,0 ve üzeri için oluşturulmuş Apache Kafka istemcilerle uyumlu olan ve Event Hubs ile okuma ve yazma için destekler. 

Event Hubs Kafka uç noktasını, mevcut Kafka kuruluma kıyasla kod değişikliği yapmadan ve yalnızca yapılandırmayı değiştirebilmeniz gerekir: yapılandırmalarda bağlantı dizesini, Kafka kümenize işaret etmek yerine olay hub 'ınız tarafından sunulan Kafka uç noktasını işaret etmek üzere güncelleştirebilir. Daha sonra, Kafka protokolünü kullanan uygulamalarınızdan akış olaylarını Event Hubs ' a başlatabilirsiniz. 

Kavramsal olarak, Kafka ve Event Hubs çok benzerdir: her ikisi de akış verileri için oluşturulan bölümlenmiş günlüklerdir ve bu, istemcinin hangi tutulan günlüğün okumak istediğini bir parçasını kontrol eder. Aşağıdaki tabloda Kafka ve Event Hubs arasındaki kavramlar eşlenir.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka ve Event hub kavramsal eşleme

| Kafka kavram | Event Hubs kavram|
| --- | --- |
| Küme | Ad Alanı |
| Konu | Olay Hub'ı |
| Bölüm | Bölüm|
| Tüketici grubu | Tüketici grubu |
| Uzaklık | Uzaklık|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Apache Kafka ve Event Hubs arasındaki önemli farklılıklar

[Apache Kafka](https://kafka.apache.org/) , genellikle tam olarak yönetilen, bulut Yerel bir hizmet olan Event Hubs yüklemeniz ve çalıştırmanız gereken bir yazılımdır. Yönetilmesi ve izlenmesi gereken hiçbir sunucu, disk veya ağ yok ve göz önünde bulundurulacak veya yapılandırabileceğiniz aracılar yok. Tam etki alanı adına sahip bir uç nokta olan bir ad alanı oluşturun ve ardından bu ad alanı içinde Event Hubs (konular) oluşturun. 

Event Hubs ve ad alanları hakkında daha fazla bilgi için bkz. [Event Hubs özellikleri](event-hubs-features.md#namespace). Bulut hizmeti olarak Event Hubs, uç nokta olarak tek bir kararlı sanal IP adresi kullanır, bu nedenle istemcilerin bir küme içindeki aracılar veya makineler hakkında bilmeleri gerekmez. Event Hubs aynı protokolü uygulasa da, bu fark tüm bölümlerin tüm Kafka trafiğinin bir kümenin tüm aracıları için güvenlik duvarı erişimi gerektirmek yerine bu uç nokta aracılığıyla tahmin edilebilir olduğu anlamına gelir.   

Event Hubs ölçek, satın aldığınız kaç üretilen iş birimi ile, her bir üretilen iş birimi için her bir saniyede 1 megabaytı, her bir saniyede da bir saniyede 1000 olay veya çıkış içindeki birimin iki katı kadar entitling. [Otomatik olarak Şişir](event-hubs-auto-inflate.md) özelliğini kullanıyorsanız, işleme sınırına ulaştığınızda Event Hubs üretilen iş birimlerini otomatik olarak ölçeklendirebilir. Bu özellik, Apache Kafka protokol desteğiyle de çalışır.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>İş yükünüz için doğru çözüm Apache Kafka mı?

Apache Kafka kullanarak uygulama oluşturma işleminden sonra, Azure Event Hubs [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)ve [Azure Event Grid](../event-grid/overview.md)de içeren bir veya daha fazla hizmetin parçası olduğunu anlamak da yararlı olacaktır. 

Apache Kafka ticari dağıtımların bazı sağlayıcıları, tüm mesajlaşma platformu gereksinimleriniz için Apache Kafka tek bir durdurulduğunda bir mağaza olduğunu önerirken, gerçekliğin Apache Kafka uygulamadığından, örneğin, [rekabet eden tüketici](/azure/architecture/patterns/competing-consumers) sırası deseninin, her türlü kurumsal mesajlaşma senaryosu için temel alınan ve bir ileti tarafından başlatılan bir işin yaşam döngüsünü izlemeye izin veren bir düzeyde  [Publish-Subscribe](/azure/architecture/patterns/publisher-subscriber) desteği yoktur ve bunlar, bir ileti tarafından başlatılan bir işin yaşam döngüsünü izleme ya da hatalı iletileri çok sayıda kurumsal mesajlaşma senaryosu için temel alan bir etkin olmayan ileti kuyruğuna uygulamaz.

Desenler arasındaki farkları ve hangi modelin hangi hizmet tarafından en iyi ele alındığı anlamak için lütfen [Azure kılavuzundaki zaman uyumsuz mesajlaşma seçeneklerini](/azure/architecture/guide/technology-choices/messaging) gözden geçirin. Apache Kafka Kullanıcı olarak, Kafka ile ilgili olan iletişim yollarının, en az temel karmaşıklıkla ve Event Grid ya da Service Bus kullanarak daha güçlü bir özellik ile gerçekleştirilmiş olduğunu fark edebilirsiniz. 

Apache Kafka arabirimi için Event Hubs aracılığıyla kullanılamayan Apache Kafka belirli özelliklerine ihtiyacınız varsa veya uygulama düzeniniz [Event Hubs kotalarını](event-hubs-quotas.md)aşarsa, [Azure HDInsight 'ta yerel bir Apache Kafka kümesi](../hdinsight/kafka/apache-kafka-introduction.md)de çalıştırabilirsiniz.  

## <a name="security-and-authentication"></a>Güvenlik ve kimlik doğrulaması
Kafka için bir Event Hubs olay yayımlamanızda veya kullandığınızda, istemciniz Event Hubs kaynaklarına erişmeye çalışıyor. Kaynaklara yetkili bir varlık kullanılarak erişildiğinden emin olmak istiyorsunuz. İstemcileriniz ile Apache Kafka Protokolü kullanırken, SASL mekanizmalarını kullanarak kimlik doğrulama ve şifreleme için yapılandırmanızı ayarlayabilirsiniz. Kafka için Event Hubs kullandığınızda TLS şifrelemesi gerekir (Event Hubs ile iletim içindeki tüm veriler TLS şifreli olarak). Yapılandırma dosyanızdaki SASL_SSL seçeneği belirtilerek yapılabilir. 

Azure Event Hubs, güvenli kaynaklarınıza erişim yetkisi vermek için birden çok seçenek sağlar. 

- OAuth 2.0
- Paylaşılan erişim imzası (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs, **OAuth 2,0** uyumlu merkezi bir yetkilendirme sunucusu sağlayan Azure Active Directory (Azure AD) ile tümleşir. Azure AD ile, istemci kimliklerinize ayrıntılı izinler vermek için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) kullanabilirsiniz. Bu özelliği, mekanizmaya yönelik protokol ve **Oauthtaşıyıcı** için **SASL_SSL** belirterek, Kafka istemcileriniz ile kullanabilirsiniz. Erişimin kapsamını belirlemek için Azure rolleri ve düzeyleri hakkında daha fazla bilgi için bkz. [Azure AD ile erişim yetkisi verme](authorize-access-azure-active-directory.md).

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler
```

#### <a name="shared-access-signature-sas"></a>Paylaşılan Erişim İmzası (SAS)
Event Hubs Ayrıca, Kafka kaynakları için Event Hubs temsilci erişimi için **paylaşılan erişim imzaları (SAS)** sağlar. OAuth 2,0 belirteç tabanlı mekanizmayı kullanarak erişimi yetkilendirmek, SAS üzerinde üstün güvenlik ve kullanım kolaylığı sağlar. Yerleşik roller Ayrıca, Kullanıcı tarafından saklanması ve yönetilmesi gereken ACL tabanlı yetkilendirme gereksinimini ortadan kaldırabilir. Bu özelliği protokol için **SASL_SSL** ve mekanizma için **düz** olarak belirterek Kafka istemcileriniz ile kullanabilirsiniz. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> `{YOUR.EVENTHUBS.CONNECTION.STRING}`Event Hubs ad alanınız için bağlantı dizesiyle değiştirin. Bağlantı dizesini alma hakkında yönergeler için bkz. [Event Hubs bağlantı dizesi alma](event-hubs-get-connection-string.md). Örnek bir yapılandırma aşağıda verilmiştir: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> SAS kimlik doğrulaması Kafka istemcilerle kullanılırken, SAS anahtarı yeniden üretildiğinde kurulan bağlantıların bağlantısı kesilmez. 


#### <a name="samples"></a>Örnekler 
Bir olay hub 'ı oluşturmak ve SAS veya OAuth kullanarak buna erişmek için adım adım yönergeler içeren bir **öğretici** için bkz. [hızlı başlangıç: Kafka protokolünü kullanarak Event Hubs veri akışı](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Kafka için Event Hubs ile OAuth 'ın nasıl kullanılacağını gösteren daha fazla **örnek** için bkz. [GitHub 'da örnekler](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Diğer Event Hubs özellikleri 

Apache Kafka özelliğinin Event Hubs, Azure Event Hubs 'da bulunan üç protokollerden biridir, HTTP ve AMQP 'yi ele alır. Geçerli Apache Kafka üreticileri Apache Kafka aracılığıyla yayımlamaya devam edebilmesi için, bu protokollerden herhangi biriyle yazabilir ve diğer kişilerle okuyabilirsiniz, ancak okuyucunuz Azure Stream Analytics veya Azure Işlevleri gibi Event Hubs ' AMQP arabirimi ile yerel tümleştirmeden faydalanabilir. Daha seyrek olarak, Azure Event Hubs 'yi AMQP yönlendirme ağlarında bir hedef uç nokta olarak kolayca tümleştirmenize, ancak Apache Kafka tümleştirmeler aracılığıyla veri okuyabilirsiniz.  

Ayrıca, Azure Blob depolama ve Azure Data Lake Storage aracılığıyla çok maliyetli verimli uzun süreli arşivlemeyi sağlayan [yakalama](event-hubs-capture-overview.md)gibi özellikler Event Hubs ve [coğrafi olağanüstü durum kurtarma](event-hubs-geo-dr.md) , ayrıca Event Hubs for Kafka özelliği ile de çalışır.

## <a name="apache-kafka-feature-differences"></a>Apache Kafka özellik farklılıkları 

Apache Kafka için Event Hubs amacı, Azure Event hub 'ının Apache Kafka API 'sine kilitlenen ve aksi halde bir Apache Kafka kümesi tarafından desteklenen uygulamalara erişim sağlamaktır. 

[Yukarıda](#is-apache-kafka-the-right-solution-for-your-workload)açıklandığı gibi, Azure Messaging filo, çok sayıda mesajlaşma senaryosu için zengin ve sağlam kapsam sağlar ve şu özellikler Şu anda Apache Kafka API 'si için Event Hubs ' desteği aracılığıyla desteklenmese de, istenen özelliğin nerede ve nasıl kullanılabilir olduğunu işaret ediyoruz.

### <a name="transactions"></a>İşlemler

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) , ileti işlemeden kaynaklanan giden mesajları, bir işlemin tutarlılık koruması altında birden çok hedef varlığa gönderen iletiler gönderilirken ileti ve oturumları almaya ve kapatmanıza izin veren güçlü işlem desteğine sahiptir. Özellik kümesi, bir dizideki her bir iletinin yalnızca bir kez işlenmesine izin verir, ancak aynı anda başka bir tüketicinin Apache Kafka, aynı iletileri yanlışlıkla yeniden işlemesini engeller. Service Bus, işlem iletisi iş yükleri için önerilen hizmettir.

### <a name="compression"></a>Sıkıştırma

Apache Kafka istemci tarafı [sıkıştırma](https://cwiki.apache.org/confluence/display/KAFKA/Compression) özelliği, birden çok iletinin bir toplu işini üretici tarafında tek bir iletiyle sıkıştırır ve toplu işi Tüketici tarafında açar. Apache Kafka Aracısı toplu işi özel bir ileti olarak değerlendirir.

Bu özellik Azure Event Hubs ' çoklu protokol modeliyle gürültü ' ye temelde göre, toplu halde gönderilen iletilerin, aracıdan ve herhangi bir protokol aracılığıyla tek tek alınabilir olmasını sağlar. 

Herhangi bir olay hub 'ı olayının yükü bir bayt akışıdır ve içerik, seçtiğiniz bir algoritmayla sıkıştırılabilir. Apache avro Encoding biçimi yerel olarak sıkıştırmayı destekler.

### <a name="log-compaction"></a>Günlük düzenleme

Apache Kafka günlük sıkıştırma, bir bölümden her bir anahtarın son kaydının hariç olmasına izin veren bir özelliktir. Bu özellik, bir Apache Kafka konusunu son değer eklenen bir öncekini geçersiz kılarsa anahtar-değer deposuna etkin bir şekilde döndürür. Bu özellik şu anda Azure Event Hubs tarafından uygulanmıyor. Anahtar-değer depolama alanı, sık sık güncelleştirmelerle birlikte [Azure Cosmos DB](../cosmos-db/introduction.md)gibi veritabanı Hizmetleri tarafından en iyi şekilde desteklenmektedir. Daha fazla ayrıntı için lütfen Event Hubs Federasyon kılavuzundaki [günlük projeksiyonu](event-hubs-federation-overview.md#log-projections) konusuna bakın. 

### <a name="kafka-streams"></a>Kafka akışlar

Kafka akışları, açık kaynak projenin Apache Kafka bir parçası olan, ancak Apache Kafka olay akışı aracılarından ayrı olan Stream Analytics için bir istemci kitaplığıdır. 

Azure Event Hubs müşterilerinin Kafka akışları desteğini sormasının en yaygın nedeni, "ksqlDB" ürünüyle ilgilendikleri içindir. "ksqlDB", "hizmet olarak yazılım", hizmet olarak platform, hizmet olarak altyapı veya "ksqlDB" desteğini kullanma izni verilen başka benzer çevrimiçi hizmetler [lisanslı](https://github.com/confluentinc/ksql/blob/master/LICENSE) bir şirkete ait olan özel bir paylaşılan kaynak projem ' dir. Pratikte, ksqlDB kullanıyorsanız, Kafka kendiniz çalıştırmalısınız ya da confluent 'in bulut tekliflerini kullanmanız gerekir. Lisanslama Koşulları, lisans tarafından dışlanan bir amaç için hizmet sunan Azure müşterilerini de etkileyebilir.

Tek başına ve ksqlDB olmadan, Kafka Streams birçok alternatif çerçeve ve hizmetten daha az özelliğe sahiptir, çoğu, yerleşik akış SQL arabirimlerine sahiptir ve hepsi de Azure Event Hubs ile tümleştirilebilir.

- [Azure Akış Analizi](../stream-analytics/stream-analytics-introduction.md)
- [Azure SYNAPSE Analytics (Event Hubs yakalama aracılığıyla)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flınk](event-hubs-kafka-flink-tutorial.md)
- [Akka akışları](event-hubs-kafka-akka-streams-tutorial.md)

Listelenen hizmetler ve çerçeveler genellikle bağdaştırıcılar aracılığıyla çok çeşitli kaynaklardan olay akışları ve başvuru verileri alabilir. Kafka akışları yalnızca Apache Kafka verileri alabilir ve analiz projeleriniz bu nedenle Apache Kafka kilitlidir. Diğer kaynaklardaki verileri kullanmak için, Öncelikle Apache Kafka verileri Kafka Connect çerçevesiyle içeri aktarmanız gerekir.

Azure 'da Kafka Streams çerçevesini kullanmanız gerekiyorsa, [HDInsight üzerinde Apache Kafka](../hdinsight/kafka/apache-kafka-introduction.md) , size bu seçeneği sağlar. HDInsight üzerinde Apache Kafka, Apache Kafka tüm yapılandırma yönleri üzerinde tam denetim sağlar ve Azure platformunun hata/güncelleştirme etki alanı yerleştirmesini, tümleştirmeyi izlemek için ağ yalıtımına kadar çeşitli özelliklerle tamamen tümleşiktir. 

## <a name="next-steps"></a>Sonraki adımlar
Bu makale, Kafka için Event Hubs bir giriş sağlamıştır. Daha fazla bilgi için bkz. [Azure Event Hubs için Apache Kafka Geliştirici Kılavuzu](apache-kafka-developer-guide.md).
