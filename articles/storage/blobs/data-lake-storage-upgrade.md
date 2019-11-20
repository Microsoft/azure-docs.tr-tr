---
title: Büyük veri analizi Çözümlerinizi Azure Data Lake Storage 1. Azure Data Lake Storage 2. ' dan yükseltin
description: Çözümünüzü kullanmak için yükseltin Azure Data Lake Storage 2.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 11/01/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: rugopala
ms.openlocfilehash: d86e2cca31487a73e089124e9552b574bcc9166f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73584388"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Büyük veri analizi Çözümlerinizi Azure Data Lake Storage 1. Azure Data Lake Storage 2. ' dan yükseltin

Büyük veri analizi çözümlerinizde Azure Data Lake Storage 1. kullanıyorsanız bu kılavuz, Azure Data Lake Storage 2. kullanmak için bu çözümleri yükseltmenize yardımcı olur. Bu belgeyi, çözümünüzün Data Lake Storage 1. sahip olduğu bağımlılıkları değerlendirmek için kullanabilirsiniz. Bu kılavuzda ayrıca yükseltmenin nasıl planlanacağı ve gerçekleştirileceği gösterilmektedir.

Aşağıdaki görevlerde size yardımcı olacak:

: heavy_check_mark: yükseltme hazırlığını değerlendirin

: heavy_check_mark: bir yükseltmeyi planlayın

: heavy_check_mark: yükseltmeyi gerçekleştirme

## <a name="assess-your-upgrade-readiness"></a>Yükseltme hazırlığını değerlendirin

Amacınız, Data Lake Storage 1. mevcut olan tüm yetenekler Data Lake Storage 2. kullanılabilir hale getirilir. Bu yetenekler, örneğin SDK, CLı vb., Data Lake Storage 1. ve Data Lake Storage 2. arasında farklılık gösterebilir. Data Lake Storage 1. ile çalışan uygulama ve hizmetlerin Data Lake Storage 2. benzer şekilde çalışabilebilmesi gerekir. Son olarak, bazı yetenekler Data Lake Storage 2. hemen kullanılamaz. Bu belgede kullanıma sunulduklarında, bunları bu belgede duyuracağız.

Bu sonraki bölümlerde, Data Lake Storage 2. ne kadar en iyi şekilde yükseltileceği ve bunu yapmak için en anlamlı hale getirme kararı vermenize yardımcı olur.

### <a name="data-lake-storage-gen1-solution-components"></a>Data Lake Storage 1. çözüm bileşenleri

Büyük olasılıkla, analiz çözümlerinizde veya işlem hatlarında Data Lake Storage 1. kullandığınızda, genel uçtan uca işlevselliğe ulaşmak için kullandığınız birçok ek teknoloji vardır. Bu [makalede](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) veri akışının, verileri almak, işlemek ve tüketme dahil çeşitli bileşenleri açıklanmaktadır.

Ayrıca, bu bileşenleri sağlamak, yönetmek ve izlemek için çapraz kesme bileşenleri vardır. Bileşenlerin her biri, bunlara en uygun arabirimi kullanarak Data Lake Storage 1. çalışır. Çözümünüzü Data Lake Storage 2. sürümüne yükseltmeyi planlarken, kullanılan arabirimlerin farkında olmanız gerekir. Her arabirim farklı gereksinimlere sahip olduğundan hem yönetim arabirimlerini hem de veri arabirimlerini yükseltmeniz gerekir.

![Data Lake Storage çözüm bileşenleri](./media/data-lake-storage-upgrade/solution-components.png)

Yukarıdaki **Şekil 1** ' de, çoğu analiz çözümlerinde göreceğiniz işlevsellik bileşenleri gösterilmektedir.

**Şekil 2** ' de, bu bileşenlerin belirli teknolojiler kullanılarak nasıl uygulandığı hakkında bir örnek gösterilmektedir.

**Şekil 1** ' de depolama işlevi Data Lake Storage 1. (**Şekil 2**) tarafından sağlanır. Veri akışındaki çeşitli bileşenlerin REST API 'Leri veya Java SDK 'sını kullanarak Data Lake Storage 1. nasıl etkileşime gireceğini aklınızda bulunabilirsiniz. Ayrıca, çapraz kesme işlevi bileşenlerinin Data Lake Storage 1. ile nasıl etkileşime gireceğini de aklınızda bulabilirsiniz. Sağlama bileşeni Azure Kaynak şablonlarını kullanır, ancak Azure Izleyici günlüklerini kullanan Izleme bileşeni, Data Lake Storage 1. gelen işletimsel verileri kullanır.

Data Lake Storage 1. kullanarak bir çözümü Data Lake Storage 2. ile yükseltmek için, verileri ve meta verileri kopyalamanız, veri akışlarını yeniden açmanız ve sonra tüm bileşenlerin Data Lake Storage 2. ile çalışabilebilmesi gerekir.

Aşağıdaki bölümler, daha iyi kararlar almanıza yardımcı olacak bilgiler sağlamaktadır:

: heavy_check_mark: platform özellikleri

: heavy_check_mark: programlama arabirimleri

: heavy_check_mark: Azure ekosistemi

: heavy_check_mark: Iş ortağı ekosistemi

: heavy_check_mark: Işletimsel bilgiler

Her bölümde, yükseltmeniz için "gereken-haves" i belirleyebileceksiniz. Yeteneklerin kullanılabilir olduğundan emin olduktan veya yerinde geçici çözümler bulunduğundan emin olduğunuzda, bu kılavuzun [yükseltme planlaması](#planning-for-an-upgrade) bölümüne ilerleyin.

### <a name="platform-capabilities"></a>Platform özellikleri

Bu bölümde, Data Lake Storage 2. Şu anda kullanılabilir olan Data Lake Storage 1. platform özellikleri açıklanmaktadır.

| | Data Lake Storage Gen1 | Data Lake Storage 2.-hedef | Data Lake Storage 2.-kullanılabilirlik durumu  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Veri organizasyonu| Klasör ve dosya olarak depolanan verileri destekler | Nesneler/Blobların yanı sıra klasörler ve dosyalar- [bağlantı](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) olarak depolanan verileri destekler | Klasör ve dosya olarak depolanan verileri destekler – *Şimdi kullanılabilir* <br><br> Nesne/blob olarak depolanan verileri destekler- *henüz kullanılamaz* |
| uzayına| İktir | İktir |  *Şimdi kullanılabilir*  |
| API  | HTTPS üzerinden REST API | HTTP/HTTPS üzerinden REST API| *Şimdi kullanılabilir* |
| Sunucu tarafı API 'SI| [Web, ile uyumlu REST API](https://msdn.microsoft.com/library/azure/mt693424.aspx) | Azure Blob hizmeti REST API [Data Lake Storage 2. REST API](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | Data Lake Storage 2. REST API – *Şimdi kullanılabilir* <br><br> Azure Blob hizmeti REST API: *Şimdi kullanılabilir*       |
| Hadoop dosya sistemi Istemcisi | Evet ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Evet ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Şimdi kullanılabilir*  |  
| Veri Işlemleri – yetkilendirme  | Dosya ve klasör düzeyi POSIX Access Control listeleri (ACL 'Ler) Azure Active Directory kimliklere göre  | Dosya ve klasör düzeyi POSIX Access Control (ACL), hesap düzeyindeki yetkilendirme rolü tabanlı Access Control ([RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac)) Için Azure Active Directory kimlikler [paylaşma anahtarına](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) göre kapsayıcılara erişim | *Şimdi kullanılabilir* |
| Veri Işlemleri – Günlükler  | Evet | Evet | *Şimdi kullanılabilir* (Önizleme). [Bilinen sorunlara](data-lake-storage-known-issues.md)bakın.<br><br> Azure Izleme tümleştirmesi – *henüz kullanılamıyor* |
| Bekleyen şifreleme verileri | Azure Keykasasında hizmet tarafından yönetilen anahtarlar ve müşteri tarafından yönetilen anahtarlarla saydam, sunucu tarafı | Azure Keykasasında hizmet tarafından yönetilen anahtarlar ve müşteri anahtarları ile yönetilen anahtarlarla saydam, sunucu tarafı | Hizmet tarafından yönetilen anahtarlar – *Şimdi kullanılabilir*<br><br> Müşteri tarafından yönetilen anahtarlar – *Şimdi kullanılabilir*  |
| Yönetim işlemleri (örn. hesap oluşturma) | Hesap yönetimi için Azure tarafından sunulan [rol tabanlı erişim denetimi](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | Hesap yönetimi için Azure tarafından sunulan [rol tabanlı erişim denetimi](https://docs.microsoft.com/azure/role-based-access-control/overview) (RBAC) | *Şimdi kullanılabilir*|
| Geliştirici SDK 'Ları | .NET, Java, Python, Node. js  | .NET, Java, Python, Node. js, C++, Ruby, php, Go, Android, iOS| Blob SDK- *Şu anda kullanılabilir*. Azure Data Lake Storage 2. SDK- *henüz kullanılamıyor*  |
| |Paralel analiz iş yükleri için iyileştirilmiş performans. Yüksek aktarım hızı ve ıOPS. | Paralel analiz iş yükleri için iyileştirilmiş performans. Yüksek aktarım hızı ve ıOPS. | *Şimdi kullanılabilir* |
| Sanal ağ (VNet) desteği  | [Sanal ağ tümleştirmesini kullanma](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Azure depolama için hizmet uç noktası kullanma](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Şimdi kullanılabilir* |
| Boyut sınırları | Hesap boyutları, dosya boyutları veya dosya sayısı sınırı yoktur | Hesap boyutları veya dosya sayısı için sınır yoktur. Dosya boyutu 5 TB ile sınırlıdır. | *Şimdi kullanılabilir*|
| Coğrafi yedeklilik| Yerel olarak yedekli (LRS) | Yerel olarak yedekli (LRS) bölge yedekli (ZRS) coğrafi olarak yedekli (GRS) Okuma Erişimli Coğrafi olarak yedekli (RA-GRS) daha fazla bilgi için [buraya](https://docs.microsoft.com/azure/storage/common/storage-redundancy) bakın| *Şimdi kullanılabilir* |
| Bölgesel kullanılabilirlik | [Buraya](https://azure.microsoft.com/regions/) bakın | Tüm [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Şimdi kullanılabilir*                                                                                                                           |
| Fiyat                                       | [Fiyatlandırmaya](https://azure.microsoft.com/pricing/details/data-lake-store/) bakın                                                                            | [Fiyatlandırmaya](https://azure.microsoft.com/pricing/details/storage/data-lake/) bakın                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Kullanılabilirlik Hizmet Düzeyi Anlaşması                            | [SLA 'ya bakın](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [SLA 'ya bakın](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Şimdi kullanılabilir*                                                                                                                           |
| Veri Yönetimi                             | Dosya süre sonu                                                                                                                                        | Yaşam döngüsü ilkeleri                                                                                                                                                                                                                                                                                                                                                                                                          | Yaşam döngüsü ilkeleri *Şu anda kullanılabilir* (Önizleme). [Bilinen sorunlara](data-lake-storage-known-issues.md)bakın.                                                                                                                     |

### <a name="programming-interfaces"></a>Programlama arabirimleri

Bu tablo, özel uygulamalarınız için kullanılabilir olan API kümelerini açıklar. ACL ve dizin düzeyindeki işlemler için SDK desteği henüz desteklenmiyor.

|  API kümesi                           |  Data Lake Storage Gen1                                                                                                                                                                                                                                                                                                   | Data Lake Storage 2. için kullanılabilirlik, paylaşılan anahtar kimlik doğrulaması ile | Data Lake Storage 2. için kullanılabilirlik-OAuth kimlik doğrulaması ile                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| .NET SDK                  | [Bağlantı](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet) | *Şimdi kullanılabilir* | *Şimdi kullanılabilir* |
| Java SDK                | [Bağlantı](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Şimdi kullanılabilir*                                                      | *Şimdi kullanılabilir*                                     |
| Node.js                | [Bağlantı](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Şimdi kullanılabilir*                                                     | *Şimdi kullanılabilir*                                                                                           |
| Python SDK                   | [Bağlantı](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Şimdi kullanılabilir*                                                      | *Şimdi kullanılabilir*                                        |
| REST API                 | [Bağlantı](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Şimdi kullanılabilir*                                                      | *Şimdi kullanılabilir*                                                                                                                                               |
| PowerShell | [Bağlantı](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | *Şimdi kullanılabilir* |
| CLI                 | [Bağlantı](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Şimdi kullanılabilir*                                                      | *Şimdi kullanılabilir*                                                               |
| Azure Resource Manager şablonları-yönetim             | [Template1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Template2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Template3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Şimdi kullanılabilir*                                                      | *Şimdi kullanılabilir*                                          |

### <a name="azure-ecosystem"></a>Azure ekosistemi

Data Lake Storage 1. kullanırken, uçtan uca işlem hatlarınızın çeşitli Microsoft hizmetlerini ve ürünlerini kullanabilirsiniz. Bu hizmetler ve ürünler doğrudan veya dolaylı olarak Data Lake Storage 1. çalışır. Bu tablo, Data Lake Storage 1. çalışmak üzere değiştirdiğimiz hizmetlerin listesini gösterir ve hangilerinin Şu anda Data Lake Storage 2. uyumlu olduğunu gösterir.

| **Alan**             | **Data Lake Storage 1. için kullanılabilirlik**                                                                                                                                    | **Data Lake Storage 2. için kullanılabilirlik – paylaşılan anahtar kimlik doğrulaması ile**                                                                                                           | **Data Lake Storage 2. için kullanılabilirlik – OAuth ile**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Analiz çerçevesi  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Şimdi kullanılabilir*                                                                                                                                                              | *Şimdi kullanılabilir*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [Hdınsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3,6- *şu anda* HDInsight 4,0- *henüz kullanılabilir değil*      | HDInsight 3,6 ESP – *Şimdi kullanılabilir* <br><br>  HDInsight 4,0 ESP- *henüz kullanılamıyor*                                                                 |
|                      | Databricks Runtime 3,1 ve üzeri                                                                                                                                               | [Databricks Runtime 5,1 ve üzeri](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *-Şimdi kullanılabilir* | [Databricks Runtime 5,1 ve üzeri](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *artık kullanılabilir*                                                                                              |
|                      | [SQL Veri Ambarı](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Desteklenmiyor*                                                                                                                                                              | *Şimdi kullanılabilir* [bağlantı](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Veri tümleştirmesi     | [Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Sürüm 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *artık kullanılabilir* sürüm 1 – *desteklenmiyor*                               | [Sürüm 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *Şimdi kullanılabilir* <br><br> Sürüm 1 – *desteklenmiyor*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Desteklenmiyor*                                                                                                                                                              | *Desteklenmiyor*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Şimdi kullanılabilir*                                                                                                                                                          | *Şimdi kullanılabilir*                                                                                                                             |
|                      | [Veri Kataloğu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Henüz kullanılamıyor*                                                                                                                                                          | *Henüz kullanılamıyor*                                                                                                                             |
|                      | [Logic Apps](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Şimdi kullanılabilir*                                                                                                                                                          | *Şimdi kullanılabilir*                                                                                                                             |
| IoT                  | [Event Hubs – yakala](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Şimdi kullanılabilir*                                                                                                                                                          | *Şimdi kullanılabilir*                                                                                                                             |
|                      | [Akış Analizi](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Şimdi kullanılabilir*                                                                                                                                                          | *Şimdi kullanılabilir*                                                                                                                             |
| Tüketim          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Şimdi kullanılabilir*                                                                                                                                                          | *Şimdi kullanılabilir*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Henüz kullanılamıyor*                                                                                                                                                          | *Henüz kullanılamıyor*                                                                                                                             |
|                      | [Analysis Services](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Henüz kullanılamıyor*                                                                                                                                                          | *Henüz kullanılamıyor*                                                                                                                             |
| Üretkenlik         | [Azure Portal](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Desteklenmiyor*                                                                                                                                                              | Hesap Yönetimi *– Şimdi kullanılabilir* <br><br>Veri işlemleri *–*  *henüz kullanılamıyor*                                                                   |
|                      | [Visual Studio için Data Lake araçları](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Henüz kullanılamıyor*                                                                                                                                                          | *Henüz kullanılamıyor*                                                                                                                             |
|                      | [Azure Depolama Gezgini](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Şimdi kullanılabilir*                                                                                                                                                              | *Şimdi kullanılabilir*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Henüz kullanılamıyor*                                                                                                                                                          | *Henüz kullanılamıyor*                                                                                                                             |

### <a name="partner-ecosystem"></a>İş ortağı ekosistemi

Bu tabloda, Data Lake Storage 1. ile çalışmak üzere değiştirilmiş üçüncü taraf hizmetlerin ve ürünlerin bir listesi gösterilir. Ayrıca hangi hangilerinin Data Lake Storage 2. uyumlu olduğunu gösterir.

| Alan            | Partner  | Ürün/hizmet  | Data Lake Storage 1. için kullanılabilirlik                                                                                                                                               | Data Lake Storage 2. için kullanılabilirlik – paylaşılan anahtar kimlik doğrulaması ile                                                   | Data Lake Storage 2. için kullanılabilirlik – OAuth ile                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Analiz çerçevesi | Cloudera     | CDH                  | [Bağlantı](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Henüz kullanılamıyor*                                                                                                  | [Bağlantı](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Ala 'lar                | [Bağlantı](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Desteklenmiyor*                                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | HortonWorks  | HDP 3,0              | [Bağlantı](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | Qubole       |                      | [Bağlantı](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
| ETL                 | StreamSets   |                      | [Bağlantı](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | Informatica  |                      | [Bağlantı](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | Attunity     |                      | [Bağlantı](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | Alteryx      |                      | [Bağlantı](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | ImanisData   |                      | [Bağlantı](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Henüz kullanılamıyor*                                                                                                  | *Henüz kullanılamıyor*                                                                                                  |
|                     | WANdisco     |                      | [Bağlantı](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Bağlantı](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Bağlantı](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>İşletimsel bilgiler

Data Lake Storage 1., işlem hatlarınızı kullanmanıza yardımcı olan belirli bilgileri ve verileri diğer hizmetlere gönderir. Bu tabloda Data Lake Storage 2. ' de karşılık gelen desteğin kullanılabilirliği gösterilmektedir.

| Veri türü                                                                                       | Data Lake Storage 1. için kullanılabilirlik                                                                 | Data Lake Storage 2. için kullanılabilirlik                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Faturalama için ticaret ekibine gönderilen ve daha sonra müşterilere kullanılabilir hale getirilen faturalandırma verileri-ölçümler  | *Şimdi kullanılabilir*                                                                                             | *Şimdi kullanılabilir*                                                                                                                           |
| Etkinlik günlükleri                                                                                          | [Bağlantı](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Destek bileti kullanılarak belirli bir süre günlük günlükler için tek kapalı istekler, *artık* mevcut *olmayan* Azure izleme tümleştirmesi-kullanılamıyor |
| Tanılama günlükleri                                                                                        | [Bağlantı](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | *Şimdi kullanılabilir* (Önizleme). [Bilinen sorunlara](data-lake-storage-known-issues.md)bakın. <br>Azure Izleme tümleştirmesi- *henüz kullanılamıyor* |
| Ölçümler                                                                                                | *Desteklenmiyor*                                                                                               | *Şimdi kullanılabilir-* [bağlantı](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Yükseltme planlaması

Bu bölümde, bu kılavuzun [yükseltme hazırlığını değerlendirme](#assess-your-upgrade-readiness) bölümünü gözden geçirdiğinizi ve tüm bağımlılıklarınızın karşılandığını kabul edersiniz. Data Lake Storage 2. hala kullanılamayan yetenekler varsa, lütfen yalnızca ilgili geçici çözümleri biliyorsanız devam edin. Aşağıdaki bölümler, işlem hatlarınızın yükseltmesini nasıl planlayabileceğine ilişkin yönergeler sağlar. Gerçek yükseltmenin gerçekleştirilmesi, bu kılavuzun [yükseltme Işlemi gerçekleştiriliyor](#performing-the-upgrade) bölümünde açıklanmaktadır.

### <a name="upgrade-strategy"></a>Yükseltme stratejisi

Yükseltmenin en kritik bölümü stratejiye karar verebilir. Bu karar, sizin için kullanılabilir olan seçimleri tespit eder.

Bu tabloda veritabanları, Hadoop kümeleri vb. geçirmek için kullanılan bazı iyi bilinen stratejiler listelenmiştir. Kılavuzumuzdaki benzer stratejileri benimseme ve bağlamımızı uyarlayacağız.

| Strateji                   | Ları                                                                                  | Simgeler                                                           | Ne zaman kullanılır?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Yükselt ve Shift                 | İnden.                                                                                 | Verilerin üzerine kopyalanması, işlerin taşınması ve giriş ve çıkış işlemlerini taşımak için kapalı kalma süresi gerektirir                                             | Daha basit çözümler için, aynı Gen1 hesabına erişen birden fazla çözüm yoktur ve bu nedenle hızlı denetlenen bir biçimde birlikte taşınabilir.                                                  |
| Kopyalama-bir kez ve kopyalama artımlı | Kaynak sistem hala etkin durumdayken arka planda kopya gerçekleştirerek kapalı kalma süresini azaltın. | Giriş ve çıkış taşımak için kapalı kalma süresi gerektirir.                   | Üzerine kopyalanacak veri miktarı büyüktür ve yaşam ve kaydırma ile ilişkili kapalı kalma süresi kabul edilemez. Geçiş işleminden önce hedef sistemde önemli üretim verileriyle test edilmesi gerekebilir. |
| Paralel benimseme              | En az kesinti süresi, uygulamaların kendi takdirine göre geçiş yapmasına olanak tanır.           | İki sistem arasında 2 yönlü eşitlemenin en ayrıntılı olması gerekir. | Data Lake Storage 1. oluşturulan uygulamaların her seferinde tek seferde çıkarılamıyor ve artımlı bir biçimde taşınması gerekir.                                                      |

Stratejilerin her birine yönelik adımlarla ilgili daha fazla ayrıntı aşağıda verilmiştir. Adımlarda, yükseltmede yer alan bileşenlerle yapabilecekleriniz listelenir. Buna genel kaynak sistemi, genel hedef sistem, kaynak sistem için giriş kaynakları, kaynak sistem için çıkış hedefleri ve kaynak sistemde çalışan işler dahildir.

Bu adımların öngörücü olmaması gerekmez. Bu kişiler, her stratejiyi nasıl düşüntiğimiz hakkında Framework 'ü ayarlamak için tasarlanmıştır. Uygulanmakta olduğunu görtiğimiz için her strateji için örnek olay incelemeleri sunuyoruz.

#### <a name="lift-and-shift"></a>Yükselt ve Shift

1. Kaynak sistem-giriş kaynaklarını, işleri, çıkış hedeflerini duraklatın.
2. Kaynak sistemdeki tüm verileri hedef sisteme kopyalayın.
3. Tüm giriş kaynaklarını hedef sisteme işaret edin. Hedef sistemden çıkış hedefini işaret edin.
4. Tüm işleri hedef sisteme taşıyın, değiştirin ve çalıştırın.
5. Kaynak sistemi kapatın.

#### <a name="copy-once-and-copy-incremental"></a>Kopyalama ve kopyalama-artımlı

1. Kaynak sistemden hedef sisteme verileri kopyalayın.
2. Kaynak sistemdeki artımlı verileri, düzenli aralıklarla hedef sisteme kopyalayın.
3. Hedef sistemden çıkış hedefini işaret edin.
4. Hedef sistemdeki tüm işleri taşıyın, değiştirin ve çalıştırın.
5. Giriş kaynaklarını kolay bir şekilde hedef sisteme işaret edin.
6. Tüm giriş kaynakları hedef sisteme işaret ettikten sonra.
    1. Artımlı kopyalamayı devre dışı bırakın.
    2. Kaynak sistemi kapatın.

#### <a name="parallel-adoption"></a>Paralel benimseme

1. Hedef sistemi ayarlayın.
2. Kaynak sistem ve hedef sistem arasında iki yönlü bir çoğaltma ayarlayın.
3. Giriş kaynaklarını hedef sisteme artımlı olarak işaret edin.
4. İşleri hedef sisteme artımlı olarak taşıyın, değiştirin, çalıştırın.
5. Hedef sistemden artımlı olarak çıkış hedeflerini işaret edin.
6. Tüm özgün giriş kaynakları, işler ve çıkış hedefi hedef sistemle çalıştıktan sonra, kaynak sistemi kapatın.

### <a name="data-upgrade"></a>Veri yükseltme

Yükseltme işlemini gerçekleştirmek için kullandığınız genel strateji (Bu kılavuzun [yükseltme stratejisi](#upgrade-strategy) bölümünde açıklanmıştır), veri yükseltmeniz için kullanabileceğiniz araçları tespit eder. Aşağıda listelenen araçlar geçerli bilgileri temel alır ve önerilerdir. 

#### <a name="tools-guidance"></a>Araçlar Kılavuzu

| Strateji                       | Araçlar                                                                                                             | Ları                                                                                                                             | Dikkat edilmesi gerekenler                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Yükselt ve Shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Yönetilen bulut hizmeti                                                                                                                | Hem veri hem de ACL 'Ler Şu anda kopyalanabilir.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | İyi bilinen Hadoop tarafından sağlanmış araç Izinleri, yani ACL 'Ler bu araçla kopyalanabilir                                                   | Aynı anda hem Data Lake Storage 1. hem de Gen2 'e bağlanabilecek bir küme gerektirir.                                                                                                                                                                                   |
| **Kopyalama-bir kez ve kopyalama artımlı** | Azure Data Factory                                                                                                    | Yönetilen bulut hizmeti                                                                                                                | Hem veri hem de ACL 'Ler Şu anda kopyalanabilir. ADF 'de artımlı kopyalamayı desteklemek için, verilerin zaman serisi bir biçimde düzenlenmelidir. Artımlı kopyalar arasındaki en kısa Aralık [15 dakikadır](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Paralel benimseme**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Azure Data Lake Storage bağlı saf Hadoop ortamı kullanılıyorsa tutarlı çoğaltmayı destekle, iki yönlü çoğaltmayı destekler | Saf Hadoop ortamı kullanmıyorsanız, çoğaltmada bir gecikme olabilir.                                                                                                                                                                                                                                                  |

Yukarıdaki veri/meta veri kopyalama araçlarının (örneğin: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)) dahil etmeden Data Lake Storage 2. yükseltmek için Data Lake Storage 1. işleyebilen üçüncü taraflar olduğunu unutmayın. Veri geçişini ve iş yükü geçişini gerçekleştiren bir "tek seferlik mağaza" deneyimi sağlar. Ekosistemi dışında olan herhangi bir araç için bant dışı bir yükseltme gerçekleştirmeniz gerekebilir.

#### <a name="considerations"></a>Dikkat edilmesi gerekenler

* Geçerli kılavuz, Gen1 hesabı bilgilerinizi temel alan herhangi bir otomatik Gen2 hesabı işlemi içermediğinden, yükseltmenin herhangi bir bölümünü başlatmadan önce Data Lake Storage 2. hesabını el ile oluşturmanız gerekir. [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) ve [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account)için hesap oluşturma süreçlerini karşılaştırtığınızdan emin olun.

* Data Lake Storage 2., yalnızca boyutu 5 TB 'a kadar olan dosyaları destekler. Data Lake Storage 2. yükseltmek için, Data Lake Storage 1. dosyaları boyutu 5 TB 'tan küçük olacak şekilde yeniden boyutlandırmanız gerekebilir.

* ACL 'Leri kopyalayameyen veya ACL 'Ler üzerine kopyalamak istemediğiniz bir araç kullanırsanız, hedefteki ACL 'Leri uygun en üst düzeyde el ile ayarlamanız gerekir. Bunu Depolama Gezgini kullanarak yapabilirsiniz. Kopyaladığınız dosya ve klasörlerin devralınmasını sağlamak için bu ACL 'Lerin varsayılan ACL 'ler olduğundan emin olun.

* Data Lake Storage 1., ACL 'Leri ayarlayabileceğiniz en üst düzey hesabın kökünde yer alabilir. Ancak Data Lake Storage 2. ' de, ACL 'Leri ayarlayabileceğiniz en üst düzey, tüm hesap değil, bir kapsayıcıdaki kök klasöründedir. Bu nedenle, hesap düzeyinde varsayılan ACL 'Ler ayarlamak istiyorsanız, Data Lake Storage 2. hesabınızdaki tüm dosya sistemleri genelinde bunları yinelemeceksiniz.

* Dosya adlandırma kısıtlamaları iki depolama sistemi arasında farklıdır. Bu farklar özellikle, ikincisi daha kısıtlamalı kısıtlamalara sahip olduğundan Data Lake Storage 2. Data Lake Storage 1. ' dan kopyalama sırasında ele alınır.

### <a name="application-upgrade"></a>Uygulama yükseltme

Data Lake Storage 1. veya Data Lake Storage 2. üzerinde uygulamalar oluşturmanız gerektiğinde önce uygun bir programlama arabirimi seçmeniz gerekir. Bu arabirimde bir API çağırırken, uygun URI 'yi ve uygun kimlik bilgilerini sağlamanız gerekir. Bu üç öğenin temsili, API, URI ve kimlik bilgilerinin nasıl sağlandığı, uygulama yükseltmesinin bir parçası olarak Data Lake Storage 1. ve Data Lake Storage Gen2.So arasında farklı bir şekilde, bu üç yapıları uygun şekilde eşlemeniz gerekir.

#### <a name="uri-changes"></a>URI değişiklikleri

Buradaki ana görev, bir `abfss://` öneki olan URI 'nin `adl://` bir ön ekine sahip URI ' y i çevirmelidir.

Data Lake Storage 1. URI şeması [burada](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) ayrıntılı olarak belirtilmiştir, ancak büyük ölçüde *adl://mydatalakestore.azuredatalakestore.net/\<bir\>.*

Data Lake Storage 2. dosyalara erişmek için URI şeması [burada](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) ayrıntılı olarak açıklanmıştır, ancak büyük ölçüde `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`.

Mevcut uygulamalarınızı ilerlemeniz ve Data Lake Storage 2. olduğunu göstermek için URI 'Leri uygun şekilde değiştirdiğinizden emin olmanız gerekir. Ayrıca, uygun kimlik bilgilerini eklemeniz gerekir. Son olarak, özgün uygulamaları devre dışı bırakma ve yeni uygulamayla değiştirme işlemlerinin genel yükseltme stratejinize yakın bir şekilde hizalanması gerekecektir.

#### <a name="custom-applications"></a>Özel uygulamalar

Uygulamanızın Data Lake Storage 1. kullandığı arabirime bağlı olarak, Data Lake Storage 2. uyarlamak için onu değiştirmeniz gerekir.

##### <a name="rest-apis"></a>REST API'leri

Uygulamanız Data Lake Storage REST API 'Leri kullanıyorsa, uygulamanızı Data Lake Storage 2. REST API 'Lerini kullanacak şekilde değiştirmeniz gerekir. Bağlantılar, *programlama arabirimleri* bölümünde verilmiştir.

##### <a name="sdks"></a>SDK’lar

*Yükseltme hazırlığını değerlendirme* bölümünde çağrıldığı gibi, SDK 'lar Şu anda kullanılamıyor. Uygulamalarınızın Data Lake Storage 2. için bağlantı noktası istiyorsanız, desteklenen SDK 'ların kullanılabilir olmasını beklemeniz önerilir.

##### <a name="powershell"></a>PowerShell

Yükseltme hazırlığını değerlendirme bölümünde çağrıldığında, PowerShell desteği şu anda veri düzlemi için kullanılamaz.

Yönetim düzlemi komutlamalarını Data Lake Storage 2. uygun olanlarla değiştirebilirsiniz. Bağlantılar, *programlama arabirimleri* bölümünde verilmiştir.

##### <a name="cli"></a>CLI

*Yükseltme hazırlığını değerlendirmek* için çağrılan şekılde, CLI desteği şu anda veri düzlemi için kullanılamaz.

Yönetim düzlemi komutlarını Data Lake Storage 2. uygun olanlarla değiştirebilirsiniz. Bağlantılar, *programlama arabirimleri* bölümünde verilmiştir.

### <a name="analytics-frameworks-upgrade"></a>Analiz çerçeveleri yükseltme

Uygulamanız, depodaki veriler hakkında açık dosya ve klasör yolları gibi meta veriler oluşturursa, depolama verileri/meta veri yükseltme işleminden sonra ek eylemler gerçekleştirmeniz gerekir. Bu, genellikle mağaza verilerinde Katalog verileri oluşturan Azure HDInsight, Databricks vb. gibi analiz çerçevelerinden de doğrudur.

Analiz çerçeveleri, Data Lake Storage 1. ve Gen2 gibi uzak depolarda depolanan verilerle ve meta verilerle çalışır. Bu nedenle, teorik olarak altyapılar geçici olabilir ve yalnızca işlerin depolanan verilere karşı çalışması gerektiğinde oluşturulabilir.

Ancak, performansı iyileştirmek için analiz çerçeveleri, uzak depoda depolanan dosya ve klasörlere açık başvurular oluşturabilir ve ardından bunları tutmak için bir önbellek oluşturur. Uzak veri değişikliği URI 'SI, örneğin, daha önce Data Lake Storage 1. veri depolayan bir küme ve Data Lake Storage 2. depolamak için, aynı kopyalanmış içerik için URI farklı olacaktır. Bu nedenle, verilerin ve meta veri yükseltmenin ardından Bu altyapıların önbellekler için de güncelleştirilmeleri veya yeniden başlatılması gerekir

Planlama sürecinin bir parçası olarak, uygulamanızı belirlemeniz ve artık Data Lake Storage 2. ' de depolanan verileri göstermek için meta veri bilgilerinin nasıl yeniden başlatılabileceğinizi belirlemeniz gerekir. Aşağıda, yükseltme adımlarıyla ilgili olarak size yardımcı olacak yaygın olarak benimsenen analiz çerçeveleri için rehberlik verilmiştir.

#### <a name="azure-databricks"></a>Azure Databricks

Seçtiğiniz yükseltme stratejisine bağlı olarak, adımlar farklı olur. Geçerli bölümde "yaşam ve-kaydırma" stratejisini seçtiğinizi varsayılmaktadır. Ayrıca, bir Data Lake Storage 1. hesabındaki verilere erişmek için kullanılan mevcut Databricks çalışma alanının, Data Lake Storage 2. hesabına kopyalanmış verilerle çalışması beklenir.

İlk olarak, Gen2 hesabını oluşturduğunuzdan ve sonra uygun bir araç kullanarak Gen1 ' den Gen2 ' ye kopyalanmış olduğunuzdan emin olun. Bu araçlar, bu kılavuzun [veri yükseltme](#data-upgrade) bölümünde çağırılır.

Daha sonra, Data Lake Storage 2. desteklemesi gereken Databricks çalışma zamanı 5,1 veya üstünü kullanmaya başlamak için mevcut Databricks kümenizi [yükseltin](https://docs.azuredatabricks.net/user-guide/clusters/index.html) .

Bu adımlar, mevcut Databricks çalışma alanının Data Lake Storage 1. hesabındaki verilere nasıl eriştiği üzerine dayalıdır. Adl://URI 'Leri [doğrudan](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) not defterlerinden veya [bağlama noktaları](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs)aracılığıyla çağırarak yapılabilir.

Tam adl://URI 'Leri sağlayarak doğrudan Not defterlerden erişiyorsanız, her bir not defteri üzerinde gidip yapılandırmayı karşılık gelen Data Lake Storage 2. URI 'sine erişecek şekilde değiştirmelisiniz.

Bundan sonra Data Lake Storage 2. hesaba işaret edecek şekilde yeniden yapılandırmanız gerekir. Daha fazla değişiklik yapmanız gerekmez ve Not defterlerinin daha önce olduğu gibi çalışması gerekir.

Diğer yükseltme stratejilerinden birini kullanıyorsanız, gereksinimlerinizi karşılamak için yukarıdaki adımların bir çeşidini oluşturabilirsiniz.

### <a name="azure-ecosystem-upgrade"></a>Azure ekosistemi yükseltmesi

Bu kılavuzun [Azure ekosistemi](#azure-ecosystem) bölümünde çağırılan her bir araç ve hizmetin, Data Lake Storage 2. çalışacak şekilde yapılandırılması gerekir.

İlk olarak, Data Lake Storage 2. ile kullanılabilir tümleştirme olduğundan emin olun.

Daha sonra, yukarıdaki (örneğin, URI ve kimlik bilgileri) çağrılan öğelerin değiştirilmesi gerekecektir. Data Lake Storage 1. ile birlikte çalışan mevcut örneği değiştirebilir veya Data Lake Storage 2. ile çalışacak yeni bir örnek oluşturabilirsiniz.

### <a name="partner-ecosystem-upgrade"></a>İş ortağı ekosistemi yükseltmesi

Lütfen Data Lake Storage 2. ile çalışabilmeleri için bileşen ve araçları sağlayan iş ortağıyla birlikte çalışın. 

## <a name="performing-the-upgrade"></a>Yükseltme gerçekleştiriliyor

### <a name="pre-upgrade"></a>Yükseltme öncesi

Bunun bir parçası olarak, *yükseltme hazırlığını değerlendirin* ve bu kılavuzun [yükseltmeyi planlama](#planning-for-an-upgrade) bölümünde, gerekli tüm bilgileri aldığınızı ve gereksinimlerinizi karşılayacak bir plan oluşturdunuz demektir. Muhtemelen bu aşamada bir test görevi olacak.

### <a name="in-upgrade"></a>Yükseltme içi

Seçtiğiniz stratejiye ve çözümünüzün karmaşıklıklarından birine bağlı olarak, bu aşama kısa bir süre veya çok sayıda iş yükünün Data Lake Storage 2. için artımlı olarak taşınmasını bekleyen bir genişletilmiş yol olabilir. Bu, yükseltmenin en önemli parçasıdır.

### <a name="post-upgrade"></a>Yükseltme sonrası

Geçiş işlemiyle işiniz bittiğinde, son adımlar kapsamlı doğrulama içerir. Bu, verilerin güvenilir bir şekilde kopyalanarak doğrulanması, ACL 'Lerin doğru şekilde ayarlandığının doğrulanması ve uçtan uca işlem hatlarının doğru şekilde çalıştığını doğrulamak için sınırlı değildir. Doğrulama işlemi tamamlandıktan sonra artık eski işlem hatlarınızı kapatabilir, kaynak Data Lake Storage 1. hesaplarınızı silebilir ve Data Lake Storage 2. tabanlı çözümlerinizde tam hızda çalışmaya devam edebilirsiniz.

## <a name="conclusion"></a>Sonuç

Bu belgede sunulan kılavuzluk, Data Lake Storage 2. kullanmak için çözümünüzü yükseltmenize yardımcı olmalıdır. 

Daha fazla sorunuz varsa veya geri bildirimde bulunun, [Azure geri bildirim Forumu](https://feedback.azure.com/forums/327234-data-lake)' nda aşağıdaki açıklamaları sağlayın veya geri bildirim sağlayın.
