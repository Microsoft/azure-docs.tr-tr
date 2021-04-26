---
title: Azure Cosmos DB için Azure Synapse Link hakkında sık sorulan sorular
description: Faturalandırma, analitik mağaza, güvenlik ve analitik depoda yaşam süresi gibi alanlarda Azure Cosmos DB için SYNAPSE bağlantısı hakkında sık sorulan soruların yanıtlarını alın.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 906651f8c48824e391879e0a579c6587231e7dfd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483869"
---
# <a name="frequently-asked-questions-about-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure Synapse Link hakkında sık sorulan sorular
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB için Azure SYNAPSE bağlantısı Azure Cosmos DB ile Azure SYNAPSE Analytics arasında sıkı bir tümleştirme oluşturur. Müşterilerin, işletimsel iş yüklerinden ve ETL işlem hattı olmadan tam performans yalıtımıyla neredeyse gerçek zamanlı analizler çalıştırmasına olanak sağlar. Bu makalede Azure Cosmos DB için Synapse Link hakkında sık sorulan soruların yanıtları yer almaktadır.

## <a name="general-faq"></a>Genel SSS

### <a name="is-azure-synapse-link-supported-for-all-azure-cosmos-db-apis"></a>Azure SYNAPSE bağlantısı tüm Azure Cosmos DB API 'Leri için destekleniyor mu?

Azure SYNAPSE link, Azure Cosmos DB SQL (Core) API 'SI ve MongoDB için Azure Cosmos DB API 'SI için desteklenir. 

### <a name="is-azure-synapse-link-supported-for-multi-region-azure-cosmos-db-accounts"></a>Azure SYNAPSE bağlantısı çok bölgeli Azure Cosmos DB hesapları için destekleniyor mu?

Evet, çok bölgeli Azure Cosmos hesaplarında, analitik depoda depolanan veriler de küresel olarak dağıtılır. Yazma bölgesinin tek veya birden fazla olmasından bağımsız olarak Azure Synapse Analytics'ten gerçekleştirilen analiz sorguları, en yakın yerel bölgeden yanıtlanabilir.

Analitik mağaza desteğiyle çok bölgeli bir Azure Cosmos DB hesabını yapılandırmayı planlarken, hesap oluşturma sırasında tüm gerekli bölgelerin eklenmesini öneririz.

### <a name="can-i-choose-to-enable-azure-synapse-link-for-only-certain-region-and-not-all-regions-in-a-multi-region-account-set-up"></a>Azure SYNAPSE bağlantısını, çok bölgeli bir hesap kümesindeki tüm bölgeler için değil, yalnızca belirli bir bölge için etkinleştirmeyi seçebilir miyim?

Çok bölgeli bir hesap için Azure SYNAPSE bağlantısı etkinleştirildiğinde, analitik depo tüm bölgelerde oluşturulur. Temel alınan veriler işlem deposunda işleme ve işlem tutarlılığı için iyileştirilmiştir.

### <a name="is-analytical-store-supported-in-all-azure-cosmos-db-regions"></a>Analitik depo tüm Azure Cosmos DB bölgelerinde destekleniyor mu?

Evet.

### <a name="is-backup-and-restore-supported-for-azure-synapse-link-enabled-accounts"></a>Azure SYNAPSE link etkin hesapları için yedekleme ve geri yükleme destekleniyor mu?

Analitik depoyu açık olan kapsayıcılar için, analitik depodaki verilerinizin otomatik yedeklemesi ve geri yüklenmesi Şu anda desteklenmez. 

Bir veritabanı hesabında SYNAPSE bağlantısı etkinleştirildiğinde Azure Cosmos DB, her zaman olarak zamanlanan yedekleme aralığındaki kapsayıcıların işlem deposunda (yalnızca) otomatik olarak [yedekleme işlemlerine](./online-backup-and-restore.md) devam eder. Analitik depo açık olan bir kapsayıcı yeni bir hesaba geri yüklendiğinde, kapsayıcının yalnızca işlem deposu ile geri yükleneceği ve analitik depo etkinleştirilmediği unutulmamalıdır. 

### <a name="can-i-disable-the-azure-synapse-link-feature-for-my-azure-cosmos-db-account"></a>Azure Cosmos DB hesabım için Azure SYNAPSE Link özelliğini devre dışı bırakabilir miyim?

Şu an için Synapse Link özelliğini hesap düzeyinde etkinleştirdikten sonra devre dışı bırakmanız mümkün değildir. Synapse Link özelliği hesap düzeyinde etkin olduğunda ve analiz deposu kullanan kapsayıcı bulunmadığında herhangi bir şekilde faturalandırma gerçekleştirilmeyeceğini unutmayın.

Özelliği kapatmanız gerekiyorsa iki seçeneğiniz vardır. Birincisi, Azure Cosmos DB hesabını silip yeniden oluşturmak ve gerekirse verileri geçirmektir. İkinci seçenek, başka bir hesaba veri geçirme konusunda yardım almak için bir destek bileti açmaktır.

### <a name="does-analytical-store-have-any-impact-on-cosmos-db-transactional-slas"></a>Analitik deponun Cosmos DB işlem SLA 'Ları üzerinde herhangi bir etkisi var mı?

Hayır, hiçbir etkisi yoktur.

## <a name="azure-cosmos-db-analytical-store"></a>Analitik depo Azure Cosmos DB

### <a name="can-i-enable-analytical-store-on-existing-containers"></a>Mevcut kapsayıcılar üzerinde analitik depolamayı etkinleştirebilir miyim?

Şu anda analitik depo yalnızca yeni kapsayıcılar için etkinleştirilebilir (hem yeni hem de mevcut hesaplarda).

### <a name="can-i-disable-analytical-store-on-my-azure-cosmos-db-containers-after-enabling-it-during-container-creation"></a>Azure Cosmos DB kapsayıcılarındaki analitik depolamayı, kapsayıcı oluşturma sırasında etkinleştirildikten sonra devre dışı bırakabilir miyim?

Analiz deposu, Azure Cosmos DB kapsayıcısı oluşturma sırasında etkinleştirildikten sonra devre dışı bırakılamaz.

### <a name="is-analytical-store-supported-for-azure-cosmos-db-containers-with-autoscale-provisioned-throughput"></a>Analitik depolama, otomatik ölçeklendirme sağlanan işleme ile Azure Cosmos DB kapsayıcıları için destekleniyor mu?

Evet, analitik depo otomatik ölçeklendirme sağlanan aktarım hızı ile kapsayıcılarda etkinleştirilebilir.

### <a name="is-there-any-effect-on-azure-cosmos-db-transactional-store-provisioned-rus"></a>Azure Cosmos DB işlemsel depoda sağlanan bir efekt var mı?

Azure Cosmos DB, işlem ve analitik iş yükleri arasında performans yalıtımını garanti eder. Bir kapsayıcıda analitik deponun etkinleştirilmesi, Azure Cosmos DB işlem deposunda sağlanan RU/s 'yi etkilemez. Analitik mağazaya yönelik işlemler (okuma & yazma) ve depolama maliyetleri de ayrı olarak ücretlendirilir. Daha fazla bilgi için bkz. [analitik mağaza Azure Cosmos DB fiyatlandırması](analytical-store-introduction.md#analytical-store-pricing) .

### <a name="can-i-restrict-network-access-to-azure-cosmos-db-analytical-store"></a>Analitik mağaza Azure Cosmos DB ağ erişimini kısıtlayabilir miyim?

Evet seçeneği, yönetilen bir [Özel uç nokta](analytical-store-private-endpoints.md) yapılandırabilir ve analitik depolama alanının ağ erişimini Azure SYNAPSE yönetilen sanal ağına kısıtlayabilir. Yönetilen özel uç noktalar, analitik deponuza özel bir bağlantı kurar. 

Azure SYNAPSE Analytics çalışma alanındaki aynı Azure Cosmos DB hesabına hem işlem deposu hem de analitik mağaza özel uç noktaları ekleyebilirsiniz. Yalnızca analitik sorgular çalıştırmak istiyorsanız, SYNAPSE Analytics çalışma alanında yalnızca analitik özel uç noktasını etkinleştirmek isteyebilirsiniz.

### <a name="can-i-use-customer-managed-keys-with-the-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitik mağaza ile müşteri tarafından yönetilen anahtarları kullanabilir miyim?

Aynı müşteri tarafından yönetilen anahtarları otomatik ve saydam bir şekilde kullanarak işlem ve analitik mağazalardaki verileri sorunsuzca şifreleyebilirsiniz. Müşteri tarafından yönetilen anahtarları analitik depolarla birlikte kullanmak için, Azure Key Vault erişim ilkenizde Azure Cosmos DB hesabınızın sistem tarafından atanan yönetilen kimliğini kullanmanız gerekir. Bu, [burada](how-to-setup-cmk.md#using-managed-identity)açıklanmıştır. Daha sonra, hesabınızda analitik depoyu etkinleştirebilmelisiniz.

### <a name="are-delete-and-update-operations-on-the-transactional-store-reflected-in-the-analytical-store"></a>, Analitik depoya yansıtılan işlem deposunda silme ve güncelleştirme işlemleri mi var?

Evet, işlem deposundaki verilerin silmeleri ve güncelleştirmeleri analitik depoya yansıtılır. Analitik deponun, analitik TTL ölçütlerine uyan tüm öğelerin tüm sürümlerini tutması için, kapsayıcıda yaşam süresi (TTL) değerini geçmiş verileri içerecek şekilde yapılandırabilirsiniz. Daha fazla ayrıntı için bkz. [ANALITIK TTL 'ye genel bakış](analytical-store-introduction.md#analytical-ttl) .

### <a name="can-i-connect-to-analytical-store-from-analytics-engines-other-than-azure-synapse-analytics"></a>Azure SYNAPSE Analytics dışındaki analiz altyapılarından analitik depoya bağlanabilir miyim?

Analiz deposuna erişmek ve sorgu çalıştırmak için Azure Synapse Analytics tarafından sağlanan çeşitli çalışma zamanlarını kullanmanız gerekir. Analiz deposunu sorgulamak ve analiz etmek için şu bileşenleri kullanabilirsiniz:

* Scala, Python, Mini SQL ve C# için tam destekle SYNAPSE spark. Synapse Spark, veri mühendisliği ve veri bilimi senaryolarında temel bir rol oynar.
* T-SQL diline sahip sunucusuz SQL havuzu ve tanıdık BI araçları desteği (örneğin, Power BI Premium vb.)

### <a name="can-i-connect-to-analytical-store-from-synapse-sql-provisioned"></a>SYNAPSE SQL tarafından sağlanan analitik depoya bağlanabilir miyim?

Şu anda, SYNAPSE SQL tarafından sağlanan analitik depoya erişilemez.

### <a name="can-i-write-back-the-query-aggregation-results-from-synapse-back-to-the-analytical-store"></a>Sorgu toplama sonuçlarını SYNAPSE 'den analitik depoya geri yazabilir miyim?

Analitik depo, Azure Cosmos DB kapsayıcısında salt bir salt okuma deposudur. Bu nedenle, toplama sonuçlarını doğrudan analitik depoya geri yazamaz, ancak başka bir kapsayıcının Azure Cosmos DB işlem deposuna yazabilir, daha sonra bir hizmet katmanı olarak yararlanılabilir.

### <a name="is-the-autosync-replication-from-transactional-store-to-the-analytical-store-asynchronous-or-synchronous-and-what-are-the-latencies"></a>İşlemsel depodan analitik depoya zaman uyumsuz veya zaman uyumlu olarak yapılan ve gecikme sürelerinin ne olduğu ile olan bir oto Sync çoğaltması

Otomatik eşitleme gecikmesi genellikle 2 dakika içinde olur. Çok sayıda kapsayıcı içeren paylaşılan üretilen iş veritabanı durumlarında, tek tek kapsayıcıların otomatik eşitleme gecikmesi daha yüksek olabilir ve 5 dakikaya kadar sürebilir. Bu gecikme süresinin senaryolarınıza uygun olduğunu öğrenmek istiyoruz. Bunun için lütfen [Azure Cosmos DB ekibine](mailto:cosmosdbsynapselink@microsoft.com)ulaşın.

### <a name="are-there-any-scenarios-where-the-items-from-the-transactional-store-are-not-automatically-propagated-to-the-analytical-store"></a>İşlem deposundaki öğelerin otomatik olarak analitik depoya yayılmadığı senaryolar var mı?

Kapsayıcıda belirli öğeler [analiz için iyi tanımlanmış şemayı](analytical-store-introduction.md#analytical-schema)ihlal ediyor ise, bu, analitik depoya dahil edilmez. Analiz için iyi tanımlanmış şema ile engellenen senaryolar varsa, yardım için [Azure Cosmos DB ekibine](mailto:cosmosdbsynapselink@microsoft.com) e-posta gönderin.

### <a name="can-i-partition-the-data-in-analytical-store-differently-from-transactional-store"></a>Analitik depodaki verileri işlem deposundan farklı şekilde bölümleyebilir miyim?

Analiz deposundaki veriler, işlem deposundaki parçaların yatay bölümlemesi temel alınarak bölümlenir. Şu an için analiz deposunda farklı bir bölümleme stratejisi belirlemek mümkün değildir.

### <a name="can-i-customize-or-override-the-way-transactional-data-is-transformed-into-columnar-format-in-the-analytical-store"></a>İşlem verilerinin analitik depodaki sütunlu biçime nasıl dönüştürüleceklerini özelleştirebilir veya geçersiz kılabilir miyim?

Şu anda, işlem deposundan otomatik olarak analitik depoya yayıldığında veri öğelerini dönüştüremiyoruz. Bu sınırlama tarafından engellenen senaryolarınız varsa [Azure Cosmos DB ekibine](mailto:cosmosdbsynapselink@microsoft.com)e-posta gönderin.

### <a name="is-analytical-store-supported-by-terraform"></a>Terraform analiz deposunu destekliyor mu?

Şu anda Terraform analiz deposu kapsayıcılarını desteklemiyor. Daha fazla bilgi için [Terraform GitHub Sorunları](https://github.com/hashicorp/terraform/issues)'na bakın.

## <a name="analytical-time-to-live-ttl"></a>Analitik yaşam süresi (TTL)

### <a name="is-ttl-for-analytical-data-supported-at-both-container-and-item-level"></a>Her iki kapsayıcıda ve öğe düzeyinde desteklenen analitik veriler için TTL mi?

Şu an için analiz verilerine yönelik TTL değeri yalnızca kapsayıcı düzeyinde yapılandırılabilir ve öğe düzeyinde analiz TTL değerini ayarlama desteği sunulmaz.

### <a name="after-setting-the-container-level--analytical-ttl-on-an-azure-cosmos-db-container-can-i-change-to-a-different-value-later"></a>Azure Cosmos DB kapsayıcısında kapsayıcı düzeyi analitik TTL ayarlandıktan sonra, daha sonra farklı bir değere değiştirebilir miyim?

Evet, analitik TTL geçerli herhangi bir değere güncelleştirilebilen olabilir. Analitik TTL hakkında daha fazla bilgi için bkz. [ANALITIK TTL](analytical-store-introduction.md#analytical-ttl) makalesi.

### <a name="can-i-update-or-delete-an-item-from-the-analytical-store-after-it-has-been-ttld-out-from-the-transactional-store"></a>Bir öğeyi, işlem deposundan TTL kapatıldıktan sonra analiz deposundan güncelleştirebilir veya silebilir miyim?

Tüm işlem güncelleştirmeleri ve silmeleri analitik depoya kopyalanır ancak öğe, işlem deposundan temizlendiyse, analitik depoda güncelleştirilemez. Daha fazla bilgi için bkz. [ANALITIK TTL](analytical-store-introduction.md#analytical-ttl) makalesi.

## <a name="billing"></a>Faturalandırma

### <a name="what-is-the-billing-model-of-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure SYNAPSE bağlantısının faturalandırma modeli nedir?

Azure SYNAPSE bağlantısının faturalandırma modeli, Azure Cosmos DB analitik mağaza ve SYNAPSE çalışma zamanını kullanarak tahakkuk eden maliyetleri içerir. Daha fazla bilgi edinmek için bkz. [analitik mağaza fiyatlandırması](analytical-store-introduction.md#analytical-store-pricing) ve [Azure SYNAPSE Analytics fiyatlandırma](https://azure.microsoft.com/pricing/details/synapse-analytics/) makaleleri Azure Cosmos DB.

### <a name="what-is-the-billing-impact-if-i-enable-synapse-link-in-my-azure-cosmos-db-database-account"></a>Azure Cosmos DB veritabanı hesabmda SYNAPSE bağlantısını etkinleştirdiğimde faturalandırma etkisi nedir?

Yok. Yalnızca bir analitik mağaza etkin kapsayıcısını oluşturduğunuzda ve verileri yüklemeye başladığınızda ücretlendirilirsiniz.


## <a name="security"></a>Güvenlik

### <a name="what-are-the-ways-to-authenticate-with-the-analytical-store"></a>Analitik depoda kimlik doğrulaması yapma yolları nelerdir?

Analitik depo ile kimlik doğrulaması, bir işlem deposuyla aynıdır. Belirli bir veritabanı için birincil veya salt okunurdur anahtarıyla kimlik doğrulaması yapabilirsiniz. Spark Not defterlerindeki Azure Cosmos DB anahtarlarının yapıştırmayı engellemek için Azure SYNAPSE Studio 'daki bağlı hizmetten yararlanabilirsiniz. Bu bağlı hizmete erişim, çalışma alanına erişimi olan herkes için kullanılabilir.

SYNAPSE sunucusuz SQL havuzları kullanırken, hesap anahtarlarını depolayarak SQL kimlik bilgilerini önceden oluşturarak ve bunları OPENROWSET işlevine başvurarak Azure Cosmos DB analitik depoyu sorgulayabilirsiniz. Daha fazla bilgi edinmek için bkz. [Azure 'da sunucusuz SQL havuzu Ile sorgulama SYNAPSE bağlantısı](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) makalesi.

## <a name="synapse-run-times"></a>SYNAPSE çalışma zamanları

### <a name="what-are-the-currently-supported-synapse-run-times-to-access-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitik depoya erişmek için şu anda desteklenen SYNAPSE çalışma zamanları nelerdir?

|Azure SYNAPSE çalışma zamanı |Geçerli destek |
|---------|---------|
|Azure SYNAPSE Spark havuzları | Okuma, yazma (işlem deposu aracılığıyla), tablo, geçici görünüm |
|Azure SYNAPSE sunucusuz SQL havuzu    | Okuma, görüntüleme |
|Azure SYNAPSE SQL sağlandı   |  Kullanılamaz |

### <a name="do-my-azure-synapse-spark-tables-sync-with-my-azure-synapse-serverless-sql-pool-tables-the-same-way-they-do-with-azure-data-lake"></a>Azure SYNAPSE Spark tablolarım, Azure Data Lake ile aynı şekilde Azure SYNAPSE sunucusuz SQL havuzu tablolarım ile eşitlensin mi?

Şu anda bu özellik kullanılamıyor.

### <a name="can-i-do-spark-structured-streaming-from-analytical-store"></a>Analitik depodan Spark yapılandırılmış akışı yapabilir miyim?

Azure Cosmos DB için Spark yapılandırılmış akış desteği, işlem deposunun değişiklik akışı işlevselliği kullanılarak uygulanır ve henüz analitik depolamadan desteklenmez.

### <a name="is-streaming-supported"></a>Akış destekleniyor mu?

Analitik depodan veri akışını desteklemiyoruz.

## <a name="azure-synapse-studio"></a>Azure Synapse Studio

### <a name="in-the-azure-synapse-studio-how-do-i-recognize-if-im-connected-to-an-azure-cosmos-db-container-with-the-analytics-store-enabled"></a>Azure SYNAPSE Studio 'da, analiz deposu etkinken Azure Cosmos DB kapsayıcısına bağlandığımı nasıl anlayabilirim?

Analitik depo ile etkinleştirilen bir Azure Cosmos DB kapsayıcısı aşağıdaki simgeye sahiptir:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/analytical-store-icon.png" alt-text="Analitik depolama ile etkin Azure Cosmos DB kapsayıcısı-simge":::

Bir işlem deposu kapsayıcısı aşağıdaki simgeyle temsil edilir:

:::image type="content" source="./media/synapse-link-frequently-asked-questions/transactional-store-icon.png" alt-text="İşlem deposu ile etkin Azure Cosmos DB kapsayıcısı-simge":::
 
### <a name="how-do-you-pass-azure-cosmos-db-credentials-from-azure-synapse-studio"></a>Azure SYNAPSE Studio 'dan Azure Cosmos DB kimlik bilgilerini nasıl geçitirsiniz?

Bağlı hizmet, Azure Cosmos DB veritabanlarına erişimi olan kullanıcı tarafından oluşturulurken şu anda Azure Cosmos DB kimlik bilgileri geçirilir. Bu mağazaya erişim, çalışma alanına erişimi olan diğer kullanıcılar tarafından kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure SYNAPSE bağlantısının avantajları](synapse-link.md#synapse-link-benefits) hakkında bilgi edinin

* [Azure SYNAPSE bağlantısı ve Azure Cosmos DB arasında tümleştirme](synapse-link.md#synapse-link-integration)hakkında bilgi edinin.
