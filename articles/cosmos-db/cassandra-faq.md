---
title: Azure Cosmos DB için Cassandra API hakkında sık sorulan sorular
description: Azure Cosmos DB Cassandra API hakkında sık sorulan soruların yanıtlarını alın.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96030841"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Azure Cosmos DB Cassandra API hakkında sık sorulan sorular
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Bu makalede, Azure Cosmos DB içindeki Apache Cassandra ve Cassandra API arasındaki işlevsellik farkları açıklanmaktadır. Ayrıca, Azure Cosmos DB Cassandra API hakkında sık sorulan soruların yanıtlarını da sağlar.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Apache Cassandra ve Cassandra API arasındaki önemli farklılıklar

- Apache Cassandra, Bölüm anahtarının boyutunda 100 MB 'lik bir sınır önerir. Azure Cosmos DB için Cassandra API, bölüm başına 20 GB 'a kadar izin verir.
- Apache Cassandra, dayanıklı işlemeleri devre dışı bırakmanızı sağlar. Kayıt günlüğüne yazmayı atlayabilir ve doğrudan memtables 'a gidebilirsiniz. Bu, düğüm, memtables 'ın disk üzerindeki SSTables 'a boşaltılmadan önce gelmesi durumunda veri kaybına yol açabilir. Azure Cosmos DB, veri kaybını önlemeye yardımcı olmak için her zaman sürekli olarak işlemeler uygular.
- İş yükü birçok değişiklik veya silme işlemi içeriyorsa Apache Cassandra performansı azalyor olabilir. Bu nedenle, okuma iş yükünün en son verileri getirmek için atlamasını gerektiren kaldırıldı. İş yükünde çok sayıda değişiklik veya silme işlemi olduğunda Cassandra API, azalmayacak okuma performansını görmez.
- Yüksek değişim iş yükleri senaryolarında SSTables 'ı diskte birleştirmek için düzenleme işleminin çalıştırılması gerekir. (Apache Cassandra yazmaları yalnızca Append olduğundan birleştirme gerekir. Birden çok güncelleştirme düzenli olarak birleştirilmek zorunda olan tek tek kalıcı girişler olarak depolanır. Bu durum, sıkıştırma sırasında daha fazla okuma performansına da yol açabilir. API, sıkıştırma uygulamadığından, bu performans etkisi Cassandra API olmaz.
- 1. çoğaltma faktörünü Apache Cassandra ile ayarlamak mümkündür. Ancak, verileri içeren tek düğüm aşağı doğru kaldığında düşük kullanılabilirliğe neden olur. Bu, Azure Cosmos DB için Cassandra API bir sorun değildir çünkü her zaman yineleme faktörü 4 ' ün (çekirdek 3 ' ü) vardır.
- Apache Cassandra 'da düğüm ekleme veya kaldırma, mevcut düğümler bazı belirteç aralıklarından bazılarını yeni düğüme taşırken, el ile müdahale ve yeni düğümdeki yüksek CPU kullanımı gerektirir. Bu durum, var olan bir düğümün yetkisini alırken aynıdır. Ancak Cassandra API, hizmet veya uygulamada gözlemlenen herhangi bir sorun olmadan ölçeği ölçeklendirir.
- Apache Cassandra 'da olduğu gibi kümedeki her bir düğümde **num_tokens** ayarlama gereksinimi yoktur. Azure Cosmos DB düğümleri ve belirteç aralıklarını tam olarak yönetir.
- Cassandra API tam olarak yönetilir. Apache Cassandra 'da kullanılan Repair ve yetkisini alma gibi **nodetool** komutlarına gerek yoktur.

## <a name="other-frequently-asked-questions"></a>Diğer sık sorulan sorular

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Cassandra API hangi protokol sürümü destekler?

Azure Cosmos DB için Cassandra API CQL sürüm 3. x ' i destekler. CQL uyumluluğu, genel [Apache Cassandra GitHub deposuna](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile)dayalıdır. Diğer protokolleri destekleme hakkında geri bildiriminiz varsa, [Kullanıcı sesli geri bildirimi](https://feedback.azure.com/forums/263030-azure-cosmos-db) veya e-posta gönderme bilgilerini bizimle öğrenin [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Bir tablo gereksinimi için neden üretilen iş üretimi seçiliyor?

Azure Cosmos DB, kapsayıcının varsayılan aktarım hızını tabloyu oluşturduğunuz yere göre ayarlar: Azure portal veya CQL.

Azure Cosmos DB, performans ve gecikme süresi, işlemler üzerinde üst sınırlarla garanti sağlar. Bu garanti, altyapının kiracının işlemleri üzerinde idare zorlaması mümkün olduğunda mümkündür. Verimlilik ayarı, platform bu kapasiteyi ayırdığından ve işlem başarısını garanti ettiğinden garantili aktarım hızı ve gecikme süresi almanızı sağlar.
[Değişiklik verimini](manage-scale-cassandra.md) , uygulamanızın mevsimlerinden faydalanabilir şekilde esnek ve maliyetleri tasarrufu sağlayabilirsiniz.

Verimlilik kavramı [Azure Cosmos DB makalesinde Istek birimleri](request-units.md) bölümünde açıklanmaktadır. Bir tablo için üretilen iş, temel alınan fiziksel bölümler arasında eşit olarak dağıtılır.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>CQL aracılığıyla oluşturulan bir tablonun üretilen işi nedir?

Azure Cosmos DB, işleme sağlamak için bir para birimi olarak saniye başına Istek birimi (RU/sn) kullanır. CQL aracılığıyla oluşturulan tablolarda varsayılan olarak 400 RU vardır. RU 'yi Azure portal değiştirebilirsiniz.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Aktarım hızı kullanıldığında ne olur?

Azure Cosmos DB, performans ve gecikme süresi, işlemler üzerinde üst sınırlarla garanti sağlar. Bu garanti, altyapının kiracının işlemleri üzerinde idare zorlaması mümkün olduğunda mümkündür. Verimlilik ayarı, platform bu kapasiteyi ayırdığından ve işlem başarısını garanti ettiğinden garantili aktarım hızı ve gecikme süresi almanızı sağlar.

Bu kapasitenin üzerine gittiğinizde, kapasitenizin kullanıldığını belirten aşağıdaki hata iletisini alırsınız:

**0x1001 aşırı yüklü: "Istek hızı büyük olduğu için istek işlenemiyor"** 

Hangi işlemlerin (ve bunların hacimlerin) Bu soruna neden olduğunu görmeniz önemlidir. Azure portal ölçümlerle sağlanan kapasiteyi kullanarak tüketilen kapasite hakkında bir fikir edinebilirsiniz. Daha sonra, tüm temeldeki bölümlerde kapasitenin neredeyse eşit olarak tüketildiğinden emin olmanız gerekir. Bir bölümün en fazla üretilen işi tükettiğini görürseniz, iş yükünü eğtiğini görürsünüz.

Kullanım ölçümleri, iş hızının saat boyunca, gün içinde ve yedi güne kadar, bölümler arasında veya toplu olarak nasıl kullanıldığını gösterir. Daha fazla bilgi için bkz. [Azure Cosmos DB ölçümlerle izleme ve hata ayıklama](use-metrics.md).

Tanılama günlükleri [Azure Cosmos DB tanılama günlüğü](./monitor-cosmos-db.md) makalesinde açıklanmaktadır.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Birincil anahtar Azure Cosmos DB bölüm anahtarı kavramıyla mi eşlenir?

Evet, bölüm anahtarı varlığı doğru konuma yerleştirmek için kullanılır. Azure Cosmos DB, fiziksel bir bölümde depolanan doğru mantıksal bölümü bulmak için kullanılır. Bölümleme kavramı, [bölüm ve ölçek Azure Cosmos DB](partitioning-overview.md) makalesinde de açıklanacaktır. Burada önemli bir bölüm, bir mantıksal bölümün 20 GB 'lik sınırın üzerinde geçmemelidir.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Bir bölümün dolu olduğunu belirten bir bildirim aldığımda ne olur?

Azure Cosmos DB, hizmet düzeyi sözleşmesine (SLA) dayalı bir sistemdir. Gecikme süresi, aktarım hızı, kullanılabilirlik ve tutarlılık garantisi sayesinde sınırsız ölçek sağlar. Bu sınırsız depolama, anahtar kavram olarak bölümlendirme kullanılarak verilerin yatay ölçeğini temel alır. Bölümleme kavramı, [bölüm ve ölçek Azure Cosmos DB](partitioning-overview.md) makalesinde de açıklanacaktır.

Mantıksal bölüm başına varlık veya öğe sayısı için 20 GB sınırına uymalısınız. Uygulamanızın iyi ölçeklendirdiğinden emin olmak için, tüm bilgileri tek bir bölümde depolayarak ve sorgulayarak bir sıcak bölüm *oluşturmamalıdır* . Bu hata yalnızca verileriniz çarpıtılmış ise gelebilir: Yani, bir bölüm anahtarı için çok fazla veriniz (20 GB 'den fazla). Depolama portalını kullanarak verilerin dağıtımını bulabilirsiniz. Bu hatayı gidermenin yolu, tabloyu yeniden oluşturmak ve verilerin daha iyi dağıtımına izin veren parçalı bir birincil (bölüm anahtarı) seçmek.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Cassandra API milyonlarca veya milyarlarca bölüm anahtarı içeren bir anahtar değer deposu olarak kullanabilir miyim?

Azure Cosmos DB, depolama alanını ölçeklendirerek sınırsız sayıda veri depolayabilirler. Bu depolama, aktarım hızını birbirinden bağımsızdır. Evet, doğru birincil/bölüm anahtarını belirterek anahtar ve değerleri depolamak ve almak için Cassandra API her zaman kullanabilirsiniz. Bu tek anahtarlar kendi mantıksal bölümlerini alır ve sorunlar olmadan fiziksel bir bölüme çok daha fazla ulaşın.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Cassandra API birden fazla tablo oluşturabilir miyim?

Evet, Cassandra API birden fazla tablo oluşturmak mümkündür. Bu tabloların her biri, üretilen iş ve depolama için birim olarak değerlendirilir.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Art arda birden fazla tablo oluşturabilir miyim?

Azure Cosmos DB hem veri hem de denetim düzlemi etkinlikleri için kaynak tarafından yönetilen sistemdir. Koleksiyonlar ve tablolar gibi kapsayıcılar, belirli bir üretilen iş kapasitesi için sağlanan çalışma zamanı varlıklarıdır. Bu kapsayıcıların hızlı bir şekilde art arda oluşturulması beklenen bir etkinlik değildir ve kısıtlanmış olabilir. Tabloları hemen bırakan veya oluşturan testleriniz varsa, bunları boş bırakmayı deneyin.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Oluşturabileceğiniz en fazla tablo sayısı nedir?

Tablo sayısında fiziksel sınır yoktur. Çok sayıda tablonuz varsa (Toplam sabit boyutun 10 TB 'den fazla olması), her zamanki on veya yüzlerce, e-posta gönderilmesi gerekir [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Oluşturabileceğiniz en fazla keyboşluk sayısı nedir?

Anahtar uzayları sayısında, meta veri kapsayıcıları olduklarından fiziksel sınır yoktur. Çok sayıda keyspaces varsa, e-posta gönderin [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Normal bir tablodan başlattıktan sonra çok miktarda veri getirebilir miyim?

Evet. Tek tek dağıtılmış bölümler olduğu varsayılarak, daha fazla veri göndermiş olduğunuz sürece depolama kapasitesi otomatik olarak yönetilir ve artar. Böylece, düğümleri yönetmeniz ve sağlamaktan çok daha fazla veri içe aktarabilir ve daha fazlasını yapabilirsiniz. Ancak, neredeyse veri artışını benimsemeyi bekleme yapıyorsanız, daha düşük bir başlangıç yapmak ve hemen artırmak yerine, [beklenen aktarım](set-throughput.md) hızına doğrudan sağlanması daha mantıklı olur.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>API davranışını yapılandırmak için YAML dosya ayarlarını kullanabilir miyim?

Azure Cosmos DB için Cassandra API, işlemleri yürütmek için protokol düzeyinde uyumluluk sağlar. Yönetim, izleme ve yapılandırma karmaşıklığını ortadan kaldırır. Bir geliştirici/Kullanıcı olarak kullanılabilirlik, silinmiş pullar, anahtar önbelleği, satır önbelleği, Bloom filtresi ve diğer birçok ayar hakkında endişelenmeniz gerekmez. Cassandra API, yapılandırma ve yönetim ek yükü olmadan ihtiyaç duyduğunuz okuma ve yazma performansını sağlamaya odaklanır.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Cassandra API düğüm ekleme, küme durumu ve düğüm durumu komutlarını destekliyor mu?

Cassandra API, Kapasite planlamasını basitleştirir ve verimlilik ve depolama için esneklik taleplerini yanıt verir. Azure Cosmos DB, ihtiyacınız olan üretilen işi temin edersiniz. Daha sonra, düğümleri ekleme, silme veya yönetme konusunda endişelenmenize gerek kalmadan, günde istediğiniz sayıda zaman kadar ölçeklendirebilir ve azaltabilirsiniz. Düğüm ve küme yönetimi için araçları kullanmanıza gerek yoktur.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Basit/ağ gibi anahtar uzayı oluşturma için çeşitli yapılandırma ayarlarına ne olur?

Azure Cosmos DB, kullanılabilirlik ve düşük gecikme süreleri açısından genel dağıtım sağlar. Çoğaltmaları veya diğer şeyleri ayarlamanıza gerek yoktur. Yazma işlemleri, her zaman yazdığınız herhangi bir bölgede (performans garantisi sağlarken) işlenir.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Tablo meta verileri için çeşitli ayarlarla ne olur?

Azure Cosmos DB, okuma, yazma ve işleme için performans garantisi sağlar. Bu nedenle, yapılandırma ayarlarından herhangi birine dokunmadan ve yanlışlıkla onları işleyerek endişelenmeniz gerekmez. Bu ayarlar Bloom filtresi, önbelleğe alma, okuma onarım şansı, gc_grace ve sıkıştırma memtable_flush_period içerir.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Cassandra tablolarında yaşam süresi destekleniyor mu?

Evet, TTL destekleniyor.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>İş verimini birlikte altyapıyı nasıl izleyebilirim?

Azure Cosmos DB, altyapıyı yönetme ve izleme konusunda endişelenmenize yardımcı olan bir platform hizmetidir. Örneğin, daha önce çeşitli araçlarla düğüm durumu, çoğaltma durumu, GC ve işletim sistemi parametrelerini izlemeniz gerekmez. Yalnızca, kısıtlı olup olmadığını görmek için Portal ölçümlerinde bulunan aktarım hızını bilmeniz ve daha sonra bu iş üretimini artırabilir veya azaltabilirsiniz. Seçenekleriniz şunlardır:

- [SLA 'ları](./monitor-cosmos-db.md) izleme
- [Ölçümleri](use-metrics.md) kullanma
- [Tanılama günlüklerini](./monitor-cosmos-db.md) kullanma

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Cassandra API hangi istemci SDK 'Ları çalışabilir?

CQLv3 kullanan Apache Cassandra SDK istemci sürücüleri istemci programları için kullanılmıştır. Kullandığınız başka sürücüleriniz varsa veya sorun yaşıyorsanız, adresine e-posta gönderin [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) .

### <a name="are-composite-partition-keys-supported"></a>Bileşik bölüm anahtarları destekleniyor mu?

Evet, bileşik bölüm anahtarları oluşturmak için normal sözdizimini kullanabilirsiniz.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Veri yüklemesi için sstableloader kullanabilir miyim?

Hayır, sstableloader desteklenmez.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Şirket içi Apache Cassandra kümesini Cassandra API ile değiştirebilir miyim?

Azure Cosmos DB, bir bulut ortamında işlem yükü olmadan en iyi duruma getirilmiş bir deneyim sunar. Eşleştirme gerektiriyorsa, [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) senaryonuzun bir açıklamayla e-posta gönderin. Şirket içi veya bulut Cassandra kümesini Azure Cosmos DB Cassandra API ile eşleştirmeye yardımcı olmaya yönelik bir sunum üzerinde çalışıyoruz.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Cassandra API tam yedeklemeler sağlar mi?

Azure Cosmos DB, tüm API 'lerde dört saatlik aralıklarla gerçekleştirilen iki ücretsiz tam yedekleme sağlar. Bu nedenle, bir yedekleme zamanlaması ayarlamanız gerekmez. 

Bekletme ve sıklığı değiştirmek istiyorsanız, e-posta gönderin [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) veya bir destek talebi yükseltin. Yedekleme özelliği hakkında bilgi, [Azure Cosmos DB makalesinde otomatik çevrimiçi yedekleme ve geri yükleme](online-backup-and-restore.md) bölümünde verilmiştir.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Bir bölge aşağı gittiğinde Cassandra API hesabı yük devretmeyi nasıl işler?

, Azure Cosmos DB küresel olarak dağıtılmış platformundan Cassandra API boratır. Uygulamanızın veri merkezi kapalı kalma süresine yol açabildiğinden emin olmak için Azure portal hesap için en az bir bölge etkinleştirin. Daha fazla bilgi için bkz. [Azure Cosmos DB Ile yüksek kullanılabilirlik](high-availability.md).

Hesap için istediğiniz kadar bölge ekleyebilir ve yük devretme önceliği sağlayarak hesaba yük devredebileceği yeri kontrol edebilirsiniz. Veritabanını kullanmak için, çok fazla bir uygulama sağlamanız gerekir. Bunu yaptığınızda, müşterileriniz kesinti yaşar.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Cassandra API, varsayılan olarak bir varlığın tüm özniteliklerini dizinlidir mi?

Hayır. Cassandra API, Apache Cassandra 'ya benzer bir şekilde davranan [İkincil dizinleri](cassandra-secondary-index.md)destekler. API, varsayılan olarak her özniteliği dizinlemez.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Yeni Cassandra API SDK 'sını öykünücü ile yerel olarak kullanabilir miyim?

Evet, bu desteklenir. Bunun nasıl etkinleştirileceği hakkındaki ayrıntıları, [yerel geliştirme ve test için Azure Cosmos DB öykünücüsünü kullanma](local-emulator.md#cassandra-api) makalesinde bulabilirsiniz.


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Apache Cassandra kümelerinden verileri Azure Cosmos DB 'a nasıl geçirebilirim?

Azure Cosmos DB öğreticide, [verilerinizi Cassandra API geçirme hesabınızda](cassandra-import-data.md) geçiş seçenekleri hakkında bilgi edinebilirsiniz.


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Cassandra API özellikleriyle ilgili geri bildirimde bulunabilirim?

[Kullanıcı sesli geri bildirimi](https://feedback.azure.com/forums/263030-azure-cosmos-db)aracılığıyla geri bildirim sağlayın.

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Cosmos DB Cassandra API bir hesabı ölçeklendirmeye](manage-scale-cassandra.md)başlayın.
