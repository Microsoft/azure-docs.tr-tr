---
title: Azure Cosmos DB, avantajları ve ne zaman kullanılacağı için Azure SYNAPSE bağlantısı
description: Azure Cosmos DB için Azure SYNAPSE bağlantısı hakkında bilgi edinin. SYNAPSE bağlantısı, Azure Cosmos DB üzerinde işletimsel veriler üzerinde Azure SYNAPSE Analytics kullanarak neredeyse gerçek zamanlı analizler (HTAP) çalıştırmanızı sağlar.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 123c443e1afaf8eaded7021b963b68b3d8a8f554
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483784"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure Synapse Link nedir?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB için Azure SYNAPSE bağlantısı, Azure Cosmos DB ' de işletimsel veriler üzerinde neredeyse gerçek zamanlı analizler çalıştırmanıza olanak tanıyan, bulut tabanlı bir karma işlem ve analitik işleme (HTAP) özelliğidir. Azure SYNAPSE link, Azure Cosmos DB ile Azure SYNAPSE Analytics arasında sıkı bir şekilde sorunsuz bir tümleştirme oluşturur.

Tam yalıtılmış bir sütun deposu olan [Azure Cosmos DB analitik depo](analytical-store-introduction.md)kullanarak Azure SYNAPSE link, [Azure SYNAPSE Analytics](../synapse-analytics/overview-what-is.md) 'Te hiçbir ayıklama-dönüştürme-yükleme (ETL) analizinin ölçeğe karşı işletimsel verilerinize karşı izin vermez. İş analistleri, veri mühendisleri ve veri bilimcileri artık gerçek zamanlı iş zekası, analiz ve makine öğrenimi işlem hatlarını çalıştırmak için SYNAPSE Spark veya SYNAPSE SQL birbirinin yerine kullanabilir. Bunu yaparken Azure Cosmos DB'deki işlem tabanlı iş yüklerinizin performansı etkilemez. 

Aşağıdaki görüntüde Azure Cosmos DB ve Azure SYNAPSE Analytics ile Azure SYNAPSE bağlantısı tümleştirmesi gösterilmektedir: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Azure Cosmos DB ile Azure SYNAPSE Analytics tümleştirmesi için mimari diyagramı" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Larından

Geleneksel olarak çalışan iş yüklerinin performansına etkisini en aza indirerek büyük işletimsel veri kümelerini analiz etmek için, Azure Cosmos DB 'deki işletimsel veriler ayıklama-Dönüştür-yükle (ETL) işlem hatları tarafından ayıklanıp işlenir. ETL işlem hatları, çok operasyonel karmaşıklığa yol açmış birçok veri hareketi katmanı ve işlem iş yüklerinizde performans etkisi gerektirir. Ayrıca, işletimsel verileri kaynak zamanından çözümleme gecikmesini de artırır.

Azure Cosmos DB için Azure SYNAPSE bağlantısı, geleneksel ETL tabanlı çözümlerle karşılaştırıldığında, şöyle çeşitli avantajlar sunar:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Yönetilecek ETL işi olmayan Azaltılan karmaşıklık

Azure SYNAPSE bağlantısı, karmaşık veri hareketi olmadan Azure SYNAPSE Analytics kullanarak analitik depoya doğrudan Azure Cosmos DB erişmenizi sağlar. İşletimsel verilere yapılan tüm güncelleştirmeler, bir ETL veya değişiklik akışı işleri olmadan neredeyse gerçek zamanlı olarak analitik depoda görünür. Azure SYNAPSE Analytics 'ten, ek veri dönüştürmesi olmadan büyük ölçekli analiz, analitik depoya karşı çalıştırabilirsiniz.

### <a name="near-real-time-insights-into-your-operational-data"></a>İşletimsel verileriniz hakkında neredeyse gerçek zamanlı Öngörüler

Artık Azure SYNAPSE link kullanarak, neredeyse gerçek zamanlı olarak işletimsel verileriniz hakkında zengin Öngörüler elde edebilirsiniz. İşlem verilerini ayıklamak, dönüştürmek ve yüklemek için gereken birçok katman nedeniyle, ETL tabanlı sistemler, işletimsel verilerinizi analiz etmeye yönelik daha yüksek gecikme süresine sahiptir. Azure SYNAPSE Analytics ile Azure Cosmos DB analitik deponun yerel tümleştirmesiyle, işletimsel verileri neredeyse gerçek zamanlı olarak analiz ederek yeni iş senaryolarına olanak sağlayabilirsiniz. 

### <a name="no-impact-on-operational-workloads"></a>İşlemsel iş yükleri üzerinde hiçbir etkisi yoktur

Azure SYNAPSE bağlantısı ile, işlem işlemleri işlem yükü (satır tabanlı bir işlem deposu) için sağlanan aktarım hızı kullanılarak işlenirken, analitik sorguları Azure Cosmos DB analitik depoya (ayrı bir sütun deposu) karşı çalıştırabilirsiniz.  Analitik iş yükü, işletimsel verileriniz için sağlanan aktarım hızını tüketmeksizin işlem iş yükü trafiğinden bağımsız olarak sunulur.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Büyük ölçekli analiz iş yükleri için iyileştirildi

Azure Cosmos DB analitik depo, işlem çalıştırma sürelerine hiçbir bağımlılığı olmadan analitik iş yükleri için ölçeklenebilirlik, esneklik ve performans sağlamak üzere iyileştirilmiştir. Depolama teknolojisi, analiz iş yüklerinizi iyileştirmek için kendini yönetilirler. Azure SYNAPSE Analytics 'te yerleşik destek sayesinde, bu depolama katmanına erişmek basitlik ve yüksek performans sağlar.

### <a name="cost-effective"></a>Düşük maliyet

Azure SYNAPSE bağlantısı ile, işletimsel analizler için uygun maliyetli, tam olarak yönetilen bir çözüm edinebilirsiniz. İşletimsel verileri çözümlemek için geleneksel ETL işlem hatları 'nda gereken ek depolama ve işlem katmanlarını ortadan kaldırır. 

Analitik depo Azure Cosmos DB, veri depolama ve analitik okuma/yazma işlemleri ve yürütülen sorguları temel alan tüketim tabanlı fiyatlandırma modelini izler. Bu işlem, işlemsel iş yükleri için bugün yaptığınız şekilde herhangi bir üretilen iş sağlama gerektirmez. Azure SYNAPSE Analytics 'ten yüksek düzeyde esnek işlem altyapılarıyla verilerinize erişmek, depolama ve işlem çalıştırmanın genel maliyetini çok verimli hale getirir.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Yerel olarak kullanılabilir, genel olarak dağıtılmış, çok bölgeli yazar için analiz

Analitik sorguları, Azure Cosmos DB verilerin en yakın bölgesel kopyasına göre etkili bir şekilde çalıştırabilirsiniz. Azure Cosmos DB, genel olarak dağıtılmış analitik iş yüklerini birlikte etkin-etkin bir şekilde çalıştırmak için son derece özelliği sağlar.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>İşletimsel verileriniz için HTAP senaryolarını etkinleştirin

SYNAPSE link, Azure SYNAPSE Analytics Runtime desteği ile analitik depo Azure Cosmos DB birlikte getirir. Bu tümleştirme, büyük veri kümelerinde işletimsel verilerinize gerçek zamanlı güncelleştirmeler temelinde Öngörüler oluşturan bulut Yerel HTAP (karma işlem/analitik işleme) çözümleri oluşturmanıza olanak sağlar. Canlı eğilimler temelinde uyarı oluşturmak, neredeyse gerçek zamanlı panolar ve kullanıcı davranışına göre iş deneyimleri oluşturmak için yeni iş senaryolarının kilidini açar.

### <a name="azure-cosmos-db-analytical-store"></a>Analitik depo Azure Cosmos DB

Azure Cosmos DB analitik depo, Azure Cosmos DB işletimsel verilerinizin sütun odaklı bir gösterimidir. Bu analitik depo, verileri kopyalamadan ve işlem iş yüklerinizin performansını etkilemeden, büyük işletimsel veri kümelerinde hızlı, ekonomik bir sorgu için uygundur.

Analitik depo, Azure Cosmos DB tam olarak yönetilen bir yetenek ("otomatik eşitleme") olarak, işlemsel iş yüklerinizde neredeyse gerçek zamanlı olarak yüksek frekansta ekler, güncelleştirmeler, siler. Değişiklik akışı veya ETL gerekli değildir. 

Küresel olarak dağıtılmış bir Azure Cosmos DB hesabınız varsa, bir kapsayıcı için analitik depoyu etkinleştirdikten sonra, bu hesap için tüm bölgelerde kullanılabilir olacaktır. Analitik depo hakkında daha fazla bilgi için bkz. [Azure Cosmos DB analitik depoya genel bakış](analytical-store-introduction.md) makalesi.

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Azure SYNAPSE Analytics ile tümleştirme

SYNAPSE bağlantısı ile artık Azure SYNAPSE Analytics 'ten Azure Cosmos DB kapsayıcılarınıza doğrudan bağlanabilir ve analitik depoya ayrı bağlayıcı olmadan erişebilirsiniz. Azure SYNAPSE Analytics Şu anda [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) ve [sunucusuz SQL havuzuyla](../synapse-analytics/sql/on-demand-workspace-overview.md)SYNAPSE bağlantısını desteklemektedir.

Azure SYNAPSE Analytics tarafından desteklenen farklı analiz çalışma zamanları genelinde birlikte çalışabilirliğine sahip Azure Cosmos DB analitik mağazadan verileri eşzamanlı olarak sorgulayabilirsiniz. İşletimsel verileri çözümlemek için ek veri dönüştürmeleri gerekmez. Analitik depo verilerini kullanarak sorgulayabilir ve analiz edebilirsiniz:

* Scala, Python, Mini SQL ve C# için tam destek içeren SYNAPSE Apache Spark. SYNAPSE Spark, veri Mühendisliği ve veri bilimi senaryolarına orta

* T-SQL diline sahip sunucusuz SQL havuzu ve tanıdık BI araçları desteği (örneğin, Power BI Premium vb.)

> [!NOTE]
> Azure SYNAPSE Analytics 'ten Azure Cosmos DB kapsayıcıınızda hem analitik hem de işlem depolarına erişebilirsiniz. Ancak, işlemsel verilerinize büyük ölçekli analiz veya tarama çalıştırmak istiyorsanız, işlem iş yüklerinde performans etkisini önlemek için analitik depo kullanmanızı öneririz.

> [!NOTE]
> Azure Cosmos DB kapsayıcınızı ilgili bölgedeki SYNAPSE çalışma zamanına bağlayarak bir Azure bölgesinde düşük gecikme süresine sahip analiz çalıştırabilirsiniz.

Bu tümleştirme, farklı kullanıcılar için aşağıdaki HTAP senaryolarına izin vermez:

* Doğrudan SYNAPSE SQL aracılığıyla Azure Cosmos DB içindeki canlı işletimsel verilere erişmek üzere bir Power BI raporu modellemek ve yayınlamak isteyen bir bı mühendisi.

* Bir Azure Cosmos DB kapsayıcısında işletimsel verilerden SYNAPSE SQL ile sorgulama yaparak Öngörüler elde etmek isteyen bir veri analisti, ölçekteki verileri okuyun ve bu bulguları diğer veri kaynaklarıyla birleştirin.

* Bir veri bilimconu, modelini geliştirmek ve bu modeli karmaşık veri Mühendisliği yapmadan eğtirecek bir özellik bulmak için SYNAPSE Spark 'ı kullanmak istiyor. Ayrıca, Spark SYNAPSE aracılığıyla veri üzerindeki gerçek zamanlı Puanlama için Azure Cosmos DB, model gönderi çıkarımı sonuçlarını da yazabilir.

* El ile ETL işlemi olmadan Azure Cosmos DB kapsayıcıları üzerinde SQL veya Spark tabloları oluşturarak, müşteriler için veri erişilebilir hale getirmek isteyen bir veri mühendisi.

Azure Cosmos DB için Azure SYNAPSE Analytics çalışma zamanı desteği hakkında daha fazla bilgi için bkz. [Azure SYNAPSE Analytics for Cosmos DB support](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Azure Cosmos DB için Azure SYNAPSE bağlantısı ne zaman kullanılır?

SYNAPSE bağlantısı aşağıdaki durumlarda önerilir:

* Azure Cosmos DB müşterisiyseniz ve işletimsel verileriniz üzerinde analiz, bı ve makine öğrenimi çalıştırmak istiyorsanız. Bu gibi durumlarda, SYNAPSE link, işlem mağazalarınızın sağlanan verimini etkilemeden daha tümleşik bir analiz deneyimi sağlar. Örnek:

  * Azure Cosmos DB işletimsel verilerinize doğrudan ayrı bağlayıcılar kullanarak analiz veya bı çalıştırıyorsanız veya

  * İşletimsel verileri ayrı bir analiz sistemine ayıklamak için ETL süreçlerini çalıştırıyorsanız.
 
Bu gibi durumlarda, SYNAPSE link, işlem mağazalarınızın sağlanan verimini etkilemeden daha tümleşik bir analiz deneyimi sağlar.

Yüksek eşzamanlılık, iş yükü yönetimi ve birden çok veri kaynağında toplamaların sürekliliği gibi geleneksel veri ambarı gereksinimlerini arıyorsanız SYNAPSE bağlantısı önerilmez. Daha fazla bilgi için, [Azure Cosmos DB Için Azure SYNAPSE bağlantısı ile desteklenen genel senaryolar](synapse-link-use-cases.md)bölümüne bakın.

## <a name="limitations"></a>Sınırlamalar

* Azure Cosmos DB için Azure Synapse Link, SQL API ve MongoDB için Azure Cosmos DB API'de desteklenir. Gremlin API, Cassandra API ve Tablo API'si desteklenmez.

* Analitik depo yalnızca yeni kapsayıcılar için etkinleştirilebilir. Mevcut kapsayıcılar için analitik depolama kullanmak için, [Azure Cosmos DB geçiş araçlarını](cosmosdb-migrationchoices.md)kullanarak mevcut kapsayıcılarınızdaki verileri yeni kapsayıcılara geçirin. Yeni ve mevcut Azure Cosmos DB hesaplarında SYNAPSE bağlantısı sağlayabilirsiniz.

* Analitik depoyu açık olan kapsayıcılar için, analitik depodaki verilerinizin otomatik yedeklemesi ve geri yüklenmesi Şu anda desteklenmez. Bir veritabanı hesabında SYNAPSE bağlantısı etkinleştirildiğinde Azure Cosmos DB, her zaman olarak zamanlanan yedekleme aralığındaki kapsayıcıların işlem deposunda (yalnızca) otomatik olarak [yedekleme işlemlerine](./online-backup-and-restore.md) devam eder. Analitik depo açık olan bir kapsayıcı yeni bir hesaba geri yüklendiğinde, kapsayıcının yalnızca işlem deposu ile geri yükleneceği ve analitik depo etkinleştirilmediği unutulmamalıdır.

* Sağlanan SYNAPSE SQL ile Azure Cosmos DB Analytics deposuna erişim şu anda kullanılamıyor.

## <a name="security"></a>Güvenlik

SYNAPSE bağlantısı, Azure Cosmos DB görev açısından kritik verileriniz üzerinde neredeyse gerçek zamanlı analiz çalıştırmanızı sağlar. Kritik iş verilerinin hem işlem hem de analitik depolarda güvenli bir şekilde depolandığından emin olmak önemlidir. Azure Cosmos DB için Azure SYNAPSE bağlantısı, aşağıdaki özelliklerle bu güvenlik gereksinimlerini karşılamaya yardımcı olmak için tasarlanmıştır:

* **Özel uç noktalar kullanarak ağ yalıtımı** -işlem ve analitik mağazalardaki verilere yönelik olarak ağ erişimini denetleyebilirsiniz. Ağ yalıtımı, Azure SYNAPSE çalışma alanlarındaki yönetilen sanal ağlarda bulunan her bir mağaza için ayrı yönetilen özel uç noktalar kullanılarak yapılır. Daha fazla bilgi için bkz. [analitik depo için özel uç noktaları yapılandırma](analytical-store-private-endpoints.md) makalesi.

* **Müşteri tarafından yönetilen anahtarlarla veri şifreleme** -aynı müşteri tarafından yönetilen anahtarları otomatik ve şeffaf bir şekilde kullanarak işlem ve analitik mağazalardaki verileri sorunsuzca şifreleyebilirsiniz. Daha fazla bilgi için bkz. [müşteri tarafından yönetilen anahtarları yapılandırma](how-to-setup-cmk.md) makalesi.

* **Güvenli anahtar yönetimi** -SYNAPSE Spark ve SYNAPSE SUNUCUSUZ SQL havuzlarından analitik depodaki verilere erişmek, SYNAPSE Analytics çalışma alanlarında Azure Cosmos DB anahtarlarının yönetilmesini gerektirir. Spark işlerinde veya SQL betiklerinizde Azure Cosmos DB hesap anahtarlarını satır içi olarak kullanmak yerine, Azure SYNAPSE link daha güvenli yetenekler sağlar.

  * SYNAPSE sunucusuz SQL havuzları kullanırken, hesap anahtarlarını depolayarak SQL kimlik bilgilerini önceden oluşturarak ve bu işleve başvurarak Azure Cosmos DB analitik depoyu sorgulayabilirsiniz `OPENROWSET` . Daha fazla bilgi edinmek için bkz. [Azure 'da sunucusuz SQL havuzu Ile sorgulama SYNAPSE bağlantısı](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) makalesi.

  * SYNAPSE Spark kullanırken, hesap anahtarlarını bir Azure Cosmos DB veritabanına işaret eden bağlı hizmet nesnelerinde saklayabilir ve çalışma zamanında Spark yapılandırmasına başvurabilirsiniz. Daha fazla bilgi edinmek için bkz. [Apache Spark makalesini kullanarak adanmış BIR SQL havuzuna veri kopyalama](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) .


## <a name="pricing"></a>Fiyatlandırma

Azure SYNAPSE bağlantısının faturalandırma modeli, Azure Cosmos DB analitik mağaza ve SYNAPSE çalışma zamanını kullanarak tahakkuk eden maliyetleri içerir. Daha fazla bilgi edinmek için bkz. [analitik mağaza fiyatlandırması](analytical-store-introduction.md#analytical-store-pricing) ve [Azure SYNAPSE Analytics fiyatlandırma](https://azure.microsoft.com/pricing/details/synapse-analytics/) makaleleri Azure Cosmos DB.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki belgelere bakın:

* [Azure Cosmos DB analitik depoya genel bakış](analytical-store-introduction.md)

* [Azure Cosmos DB için Azure Synapse Link'i kullanmaya başlama](configure-synapse-link.md)
 
* [Azure Synapse Analytics çalışma zamanı tarafından desteklenen özellikler](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Azure Cosmos DB için Azure Synapse Link hakkında sık sorulan sorular](synapse-link-frequently-asked-questions.md)

* [Azure Cosmos DB için Azure Synapse Link kullanım örnekleri](synapse-link-use-cases.md)
