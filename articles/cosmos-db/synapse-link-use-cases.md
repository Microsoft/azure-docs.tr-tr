---
title: Azure Cosmos DB için Azure SYNAPSE bağlantısı ile gerçek zamanlı analiz kullanım örnekleri
description: Azure Cosmos DB için Azure SYNAPSE bağlantısının Tedarik Zinciri Analizi, tahmin, raporlama, gerçek zamanlı kişiselleştirme ve ıOT tahmine dayalı bakımda nasıl kullanıldığını öğrenin.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: ramkris
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 253257564ffc96108f282d7de3dbd9f5e8ce68fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104869880"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-near-real-time-analytics-use-cases"></a>Azure Cosmos DB için Azure Synapse Link: Gerçek zamanlıya yakın analiz için kullanım örnekleri
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB için [Azure SYNAPSE bağlantısı](synapse-link.md) , işletimsel veriler üzerinde neredeyse gerçek zamanlı analizler çalıştırmanızı sağlayan bir bulut Yerel karma işlem ve analitik Işleme (htap) özelliğidir. SYNAPSE link, Azure Cosmos DB ile Azure SYNAPSE Analytics arasında sıkı sorunsuz bir tümleştirme oluşturur.

İşletimsel veriler üzerinde neredeyse gerçek zamanlı analizler için, sektör kullanım örneklerinin Bu bulut Yerel HTAP özelliğinden yararlanabileceği anlaşılması merak ediyor olabilirsiniz. Azure Cosmos DB için Azure SYNAPSE bağlantısı 'nın üç yaygın kullanım durumu aşağıda verilmiştir:

* Tedarik Zinciri Analizi, tahmin & raporlama
* Gerçek zamanlı kişiselleştirme
* Tahmine dayalı bakım, ıOT senaryolarında anomali algılama

> [!NOTE]
> Azure Cosmos DB için Azure SYNAPSE bağlantısı, kurumsal takımların neredeyse gerçek zamanlı analizler üzerinde çalıştığı senaryoyu hedefler. Bu çözümlemeler, Azure Cosmos DB oluşturulan işlem uygulamaları genelinde oluşturulan işletimsel veriler üzerinde ETL olmadan çalıştırılır. Bu, birden çok veri kaynağında iş yükü yönetimi, yüksek eşzamanlılık ve kalıcılık toplamaları gibi geleneksel veri ambarı gereksinimleri olduğunda ayrı bir veri ambarı gereksinimini değiştirmez.

## <a name="supply-chain-analytics-forecasting--reporting"></a>Tedarik Zinciri Analizi, tahmin & raporlama

Araştırma çalışmaları, tedarik zinciri işlemlerinde büyük veri analizlerinin gömülme işlemlerini, sırasıyla teslim sürelerine ve tedarik zinciri verimliliğine yol gösterecek şekilde gösterir.

Üreticiler, eski Kurumsal kaynak planlama (ERP) ve tedarik zinciri yönetimi (SCM) sistemlerinin kısıtlamalarını ortadan kesmek için bulut Yerel teknolojilerine ekleme yapılır. Çalışma zincirleriyle her dakikada (sipariş, Sevkiyat, işlem verileri) işletimsel verileri artan hacime sahip olan üreticiler, üreticilerin işletimsel bir veritabanı olması gerekir. Bu işletimsel veritabanı, eğrinin önünde kalmak için bir dizi gerçek zamanlı bağlamsal zekaya ulaşmak üzere veri hacimlerini ve analitik platformu işlemek için ölçeklendirmelidir.

Aşağıdaki mimaride, tedarik zinciri analizinden Cloud Native işletimsel veritabanı ve SYNAPSE bağlantısı olarak Azure Cosmos DB kullanma gücü gösterilmektedir:

:::image type="content" source="./media/synapse-link-use-cases/supply-chain-analytics.png" alt-text="Tedarik zinciri analizinden Azure Cosmos DB için Azure SYNAPSE bağlantısı " border="false":::

Önceki mimariye bağlı olarak, Azure Cosmos DB için SYNAPSE bağlantısı ile aşağıdaki kullanım örneklerini elde edebilirsiniz:

* Tahmine dayalı işlem hattını **hazırlayın & eğitme işlem hattı:** Machine Learning 'i kullanarak tedarik zinciri genelinde işletimsel veriler üzerinde Öngörüler oluşturun. Bu şekilde, envanter, operasyon maliyetlerini düşürebilmeniz ve müşteriler için sipariş teslim sürelerini azaltabilirsiniz.

  SYNAPSE bağlantısı, el ile ETL işlemi olmadan Azure Cosmos DB değişen işletimsel verileri analiz etmenizi sağlar. Size ek maliyet, gecikme süresi ve operasyonel karmaşıklıktan tasarruf sağlar. SYNAPSE bağlantısı, veri mühendislerinin ve veri bilimcilerinin sağlam tahmine dayalı işlem hatları oluşturmasına olanak sağlar:

  * Azure SYNAPSE Analytics 'te Apache Spark havuzlarıyla yerel tümleştirmeyi kullanarak Azure Cosmos DB analitik depolamadan işletimsel verileri sorgulayın. Karmaşık veri Mühendisliği olmadan etkileşimli bir not defteri veya zamanlanmış uzak işlerinde verileri sorgulayabilirsiniz.

  * Spark ML algoritmaları ile Azure SYNAPSE Analytics 'te Azure ML tümleştirmesiyle Machine Learning (ML) modelleri oluşturun.

  * İşletimsel neredeyse gerçek zamanlı Puanlama için Azure Cosmos DB model çıkarımı sonrasında sonuçları geri yazın.

* **İşletimsel raporlama:** Tedarik zinciri ekiplerinin gerçek zamanlı, doğru işletimsel veriler üzerinde esnek ve özel raporlara ihtiyacı vardır. Bu raporlar, tedarik zinciri verimliliği, karlılık ve üretkenliğin anlık görüntü görünümünü elde etmek için gereklidir. Veri analistlerinin ve diğer önemli hissedarların işletmeyi sürekli olarak yeniden değerlendirmesine ve işlem maliyetlerini azaltmak için ince ayar 'e alan belirlemesine olanak sağlar. 

  Azure Cosmos DB için SYNAPSE bağlantısı zengin iş zekası (BI)/Reporting senaryolarına izin verebilir:

  * Sunucusuz SQL havuzuyla yerel tümleştirmeyi ve T-SQL dilinin tam ifade düzeyini kullanarak Azure Cosmos DB analitik mağazadan işletimsel verileri sorgulayın.

  * Tanıdık BI araçları için sunucusuz SQL havuzu desteği aracılığıyla Azure Cosmos DB üzerinde otomatik yenileme bı panolarını modelleme ve yayımlama. Örneğin, Azure Analysis Services, Power BI Premium, vb.

Aşağıda toplu iş & akışı verileri Azure Cosmos DB içine veri tümleştirmesi için bazı kılavuzluk verilmiştir:

* **Toplu veri tümleştirmesi & düzenleme:** Tedarik zincirleriyle daha karmaşık olan tedarik zinciri veri platformları, çeşitli veri kaynakları ve biçimleriyle tümleştirilecek. Azure SYNAPSE, Azure Data Factory ile aynı veri tümleştirme altyapısı ve deneyimleriyle yerleşik olarak sunulur. Bu tümleştirme, veri mühendislerinin ayrı bir düzenleme altyapısı olmadan zengin veri işlem hatları oluşturmalarına olanak tanır:

  * 85 + desteklenen veri kaynaklarından verileri [Azure Data Factory Azure Cosmos DB](../data-factory/connector-azure-cosmos-db.md)'a taşıyın.

  * [Eşleme veri akışları ile ilişkisel ve hiyerarşik ve sıradüzenli eşlemeler](../data-factory/how-to-sqldb-to-cosmosdb.md)dahil Azure Cosmos DB için kod ücretsiz ETL işlem hatları yazın.

* **Akış veri tümleştirmesi & işleme:** Endüstriyel IoT (' Floor-Store ', bağlı lojistik, vb.) büyümesi sayesinde, öngörüleri oluşturmak için geleneksel yavaş hareketli verilerle tümleştirilmesi gereken bir akış düzeyinde üretilen gerçek zamanlı verilerin bir açılımı vardır. Azure Stream Analytics, Azure 'da [çok çeşitli senaryolarla](../stream-analytics/streaming-technologies.md)veri ETL ve işleme için önerilen bir hizmettir. Azure Stream Analytics, [yerel bir veri havuzu olarak Azure Cosmos DB](../stream-analytics/stream-analytics-documentdb-output.md)destekler.

## <a name="real-time-personalization"></a>Gerçek zamanlı kişiselleştirme

Günümüzde satıcılar, hem müşterilerin hem de işletmenin taleplerini karşılayan güvenli ve ölçeklenebilir e-ticaret çözümleri dermelidir. Bu e-ticaret çözümlerinin, müşterileri özelleştirilmiş ürünler ve tekliflerle, işlemleri hızlı ve güvenli bir şekilde işlemesi, işleme ve müşteri hizmetleri 'ne odaklanmanız gerekir. Azure Cosmos DB için en son SYNAPSE bağlantısı ile birlikte Azure Cosmos DB, perakendecilerin müşteriler için gerçek zamanlı olarak kişiselleştirilmiş öneriler oluşturmasına izin verir. Bunlar, aşağıdaki mimaride gösterildiği gibi anında Öngörüler için düşük gecikme süresi ve tunlı tutarlılık ayarlarını kullanırlar:

:::image type="content" source="./media/synapse-link-use-cases/real-time-personalization.png" alt-text="Gerçek zamanlı kişiselleştirme Azure Cosmos DB için Azure SYNAPSE bağlantısı" border="false":::

Azure Cosmos DB kullanım durumu için SYNAPSE bağlantısı:

* Tahmine dayalı işlem hattını **hazırlayın & eğitme işlem hattı:** SYNAPSE Spark ve makine öğrenimi modellerini kullanarak iş birimleriniz veya müşteri segmentlerinizde işletimsel verilerle ilgili Öngörüler oluşturabilirsiniz. Bu, son kullanıcı gereksinimlerinizi karşılayacak şekilde, son kullanıcı deneyimlerini ve hedeflenen pazarlamayı tahmin etmek için, hedef müşteri segmentlerine kişiselleştirilmiş teslimi dönüştürür.

## <a name="iot-predictive-maintenance"></a>IOT tahmine dayalı bakım

Endüstriyel ıOT yenilikleri, makinelerin alt zamanlarını büyük ölçüde azalttı ve sektör alanları genelinde genel verimliliği artırmıştır. Bu tür yeniliklerden biri, bulutun kenarındaki makineler için tahmine dayalı bakım analizinden biridir.

IoT tahmine dayalı bakımda Azure Cosmos DB için Azure SYNAPSE bağlantısının bulut Yerel HTAP özelliklerini kullanan bir mimari aşağıda verilmiştir:

:::image type="content" source="./media/synapse-link-use-cases/iot-predictive-maintenance.png" alt-text="IOT tahmine dayalı bakımda Azure Cosmos DB için Azure SYNAPSE bağlantısı" border="false" :::

Azure Cosmos DB kullanım örnekleri için SYNAPSE bağlantısı:

* Tahmine dayalı işlem hattını **hazırlayın & eğitme işlem hattı:** IoT cihaz algılayıcılarından geçmiş işletimsel veriler, anomali algılayıcıları gibi tahmine dayalı modelleri eğitmek için kullanılabilir. Bu anomali algılayıcılar daha sonra gerçek zamanlı izleme için kenara dağıtılabilir. Böyle bir sanallaştırıntı döngüsü, tahmine dayalı modellerin sürekli yeniden eğitimine izin verir.

* **İşletimsel raporlama:** Dijital ikizi girişimlerin büyümesi sayesinde, şirketler, her makinenin dijital bir kopyasını oluşturmak için çok sayıda sensörden çok miktarda işletimsel veri topluyor. Bu veri güçleri, son kullanılan veriler üzerinde gerçek zamanlı uygulamalara ek olarak geçmiş veriler üzerindeki eğilimleri anlamak için gerekir.

## <a name="sample-scenario-htap-for-azure-cosmos-db"></a>Örnek senaryo: Azure Cosmos DB için HTAP

Neredeyse bir yıllık yılda, esnek ölçeklendirme, anahtar temelli küresel dağıtım, çok bölgeli yazma çoğaltması gerektiren görev açısından kritik uygulamalar için Azure Cosmos DB, düşük gecikme süresi ve her ikisi de kendi işlem iş yüklerinde bulunan & yazma işlemleri için yüksek kullanılabilirlik için kullanılmıştır.
 
Aşağıdaki listede, Azure Cosmos DB kullanılarak işletimsel verilerle desteklenen çeşitli iş yükü desenlerinin bir özeti gösterilmektedir:

* Gerçek zamanlı uygulamalar & Hizmetleri
* Olay akışı işleme
* Bı panoları
* Büyük veri analizi
* Makine öğrenimi

Azure SYNAPSE Azure Cosmos DB bağlantısı, yalnızca işlemsel iş yüklerini güçlendirin ve ayrıca geçmiş işletimsel veriler üzerinde neredeyse gerçek zamanlı analitik iş yükleri gerçekleştirebilir. İşlem iş yüklerinden hiçbir ETL gereksinimi ve garantili performans yalıtımı olmadan gerçekleşir.

Aşağıdaki görüntüde Azure Cosmos DB kullanarak iş yükü desenleri gösterilmektedir: :::image type="content" source="./media/synapse-link-use-cases/synapse-link-workload-patterns.png" alt-text="Azure Cosmos DB iş yükü desenleri Için Azure SYNAPSE link" border="false":::

Tek bir gerçek zamanlı veritabanı, bir envanter yönetimi platformunun hem işlem hem de analitik gereksinimlerini güçlendiren şekilde Azure Cosmos DB seçme avantajlarının yanı sıra, bir e-ticaret şirketi şirket xyz örneğini 20 ülkede/bölgede küresel işlemlerle birlikte ele alalım.

* CompanyXYZ 'in Core Business, envanter yönetimi sistemine bağlıdır. bu nedenle kullanılabilirlik & güvenilirliği, çekirdek ve gereksinimlerdir. Azure Cosmos DB kullanmanın avantajları:

  * Azure altyapısıyla derin tümleştirme ve saydam çok bölgeli yazma işlemleri, genel çoğaltma Azure Cosmos DB, bölgesel kesintilere karşı sektörde önde gelen [% 99,999 yüksek kullanılabilirlik](high-availability.md) sağlar.

* CompanyXYZ 'in tedarik zinciri ortakları ayrı coğrafi konumlarda olabilir, ancak yerel işlemlerini desteklemek için dünya genelindeki ürün envanterinin tek bir görünümünü görmeleri gerekebilir. Buna, diğer tedarik zinciri ortakları tarafından yapılan güncelleştirmeleri gerçek zamanlı olarak okuyabilme gereksinimi dahildir. Ayrıca, yüksek aktarım hızı ile diğer iş ortaklarıyla çakışmalar hakkında endişelenmeden güncelleştirme yapabiliriz. Azure Cosmos DB kullanmanın avantajları:

  * Benzersiz çok bölgeli yazma çoğaltma Protokolü ve mandal, yazma için iyileştirilmiş işlem deposu sayesinde, Azure Cosmos DB hem dizin oluşturulan okuma hem de genel olarak 99. yüzdede bulunan yazma işlemleri için 10 MS 'den az gecikme süresi garanti eder.

  * İşlem deposunda [gerçek zamanlı dizin oluşturma](index-policy.md) ile her iki toplu iş & akış veri akışı alımı.

  * Azure Cosmos DB işlemsel depolama, iş gereksiniminden en yakın [kullanılabilirlik vs performans dengelerinin](./consistency-levels.md) elde edilebilmesi için güçlü ve nihai tutarlılık seviyelerinin üç aşırı uç daha fazla seçenek sunar.

* CompanyXYZ 'in tedarik zinciri ortakları, yüzlerce ve milyonlarca istekten oluşan trafik desenlerinin büyük ölçüde dalgalanmasının yanı sıra envanter yönetimi platformunun trafikte beklenmedik bir burstılmayla uğraşması gerekir.  Azure Cosmos DB kullanmanın avantajları:

  * Azure Cosmos DB işlem deposu, yatay bölümleme kullanılarak depolamanın ve üretilen işin esnek ölçeklenebilirliğini destekler. Autopilot modunda yapılandırılan kapsayıcılar ve veritabanları, iş yükünün kullanılabilirliğini, gecikme süresini, verimini veya küresel olarak performansını etkilemeden uygulama ihtiyaçlarına göre sağlanan aktarım hızını otomatik olarak ve anında ölçeklendirebilir.

* CompanyXYZ, tedarik zinciri ortağı, iş birimleri ve işlevler genelinde analizler ve içgörüler sağlamak için sistem genelinde geçmiş envanter verilerini barındırmak üzere bir güvenli analiz platformu kurmaya ihtiyaç duyuyor. Analiz platformunun, sistem genelinde, geleneksel BI/raporlama kullanım örnekleri, gelişmiş analiz kullanım örnekleri ve işletimsel envanter verileri üzerinden tahmine dayalı akıllı çözümler sağlaması gerekir. Azure Cosmos DB için SYNAPSE bağlantısı kullanmanın avantajları:

  * Tam yalıtılmış bir sütun deposu olan [Azure Cosmos DB analitik depo](analytical-store-introduction.md)kullanarak, SYNAPSE link, [Azure SYNAPSE Analytics](../synapse-analytics/overview-what-is.md) 'Te hiçbir ayıklama-dönüştürme-yükleme (ETL) analizinin ölçekteki küresel olarak dağıtılmış işletimsel verilere karşı yapılmasını mümkün değildir.  İş analistleri, veri mühendisleri ve veri bilimcileri artık, Azure Cosmos DB üzerinde işlem iş yüklerinin performansını etkilemeden neredeyse gerçek zamanlı iş zekası, analiz ve makine öğrenimi işlem hatlarını çalıştırmak için SYNAPSE Spark veya SYNAPSE SQL 'i birlikte çalışabilen bir şekilde kullanabilir. Daha fazla bilgi için [Azure Cosmos DB SYNAPSE bağlantısının avantajları](synapse-link.md) bölümüne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki belgelere bakın:

* [Azure Cosmos DB için Azure Synapse Link](synapse-link.md) 

* [Azure Cosmos DB Analiz Deposu](analytical-store-introduction.md)

* [Azure Cosmos DB için Azure SYNAPSE bağlantısıyla çalışma](configure-synapse-link.md)

* [Azure Cosmos DB için Azure Synapse Link hakkında sık sorulan sorular](synapse-link-frequently-asked-questions.md)

* [Azure Synapse Analytics’te Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md)

* [Azure SYNAPSE Analytics 'te sunucusuz SQL havuzu çalışma zamanı desteği](../synapse-analytics/sql/on-demand-workspace-overview.md)