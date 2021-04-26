---
title: Azure Veri Gezgini uzun süreli günlük tutma için tümleştirin | Microsoft Docs
description: Veri depolama maliyetlerini azaltmak için uzun süreli saklama için Azure Veri Gezgini Azure 'da günlük gönderin.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/21/2021
ms.author: bagol
ms.openlocfilehash: c7d9ef58d4bb15afe59c6734ce887c2cc3c07c26
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836292"
---
# <a name="integrate-azure-data-explorer-for-long-term-log-retention"></a>Uzun süreli günlük tutma için Azure Veri Gezgini tümleştirme

<!--Info not included:>

Script - can't xref out to a private github repo from docs
-->

Varsayılan olarak, Azure Sentinel 'e alınan Günlükler Azure Izleyici Log Analytics depolanır. Bu makalede, uzun süreli saklama için Azure Veri Gezgini (ADX) uygulamasına göndererek Azure Sentinel 'de bekletme maliyetlerinin nasıl azaltılacağı açıklanmaktadır.

Günlükleri ADX 'te depolamak, verilerinizi sorgulama yeteneğinizi koruyarak maliyetleri azaltır ve özellikle de verileriniz büyüdükçe yararlı olur. Örneğin, güvenlik verileri zaman içinde değeri kaybedebilir; ancak, yasal gereksinimler için günlükleri tutmanız veya eski verilerde düzenli araştırmalar çalıştırmak gerekebilir.

## <a name="about-azure-data-explorer"></a>Azure Veri Gezgini hakkında

ADX, günlük ve veri analizi için yüksek iyileştirilen büyük bir veri analizi platformudur. ADX, sorgu dili olarak kusto sorgu dili (KQL) kullandığından, Azure Sentinel veri depolama için iyi bir alternatiftir. Veri depolama alanı için ADX kullanılması, platformlar arası sorguları çalıştırmanıza ve hem ADX hem de Azure Sentinel üzerinde verileri görselleştirmenize olanak sağlar.

Daha fazla bilgi için bkz. ADX [belgeleri](/azure/data-explorer/) ve [blogu](https://azure.microsoft.com/en-us/blog/tag/azure-data-explorer/).

### <a name="when-to-integrate-with-adx"></a>ADX ile tümleştirme ne zaman

Azure Sentinel, tam SıEM ve SOAR özellikleri, hızlı dağıtım ve yapılandırma yanı sıra SOC ekipleri için gelişmiş, yerleşik güvenlik özellikleri sağlar. Ancak, Azure Sentinel 'de güvenlik verilerinin depolanması değeri birkaç aydan sonra kesilebilir, bu da SOC kullanıcıları daha yeni verilere erişirken sık erişime gerek kalmaz.

Belirli tablolara, düzenli araştırmalar veya denetimler gibi bazı durumlarda erişmeniz gerekiyorsa, verilerinizin Azure Sentinel 'de bekletilmesini artık uygun maliyetli olmadığını göz önünde bulundurabilir. Bu noktada, verileri ADX içinde depolamanız önerilir, ancak yine de Azure Sentinel 'de çalıştırdığınız aynı KQL sorgularını kullanarak araştırmanıza olanak sağlar.

ADX 'teki verilere, [log ANALYTICS ADX proxy özelliğini](//azure/azure-monitor/logs/azure-monitor-data-explorer-proxy)kullanarak doğrudan Azure Sentinel 'ten erişebilirsiniz. Bunu yapmak için, günlük aramanızda veya çalışma kitaplarında çapraz küme sorguları kullanın. 

> [!IMPORTANT]
> Analitik kurallar, UEBA ve araştırma grafı dahil olmak üzere çekirdek SıEM özellikleri, ADX içinde depolanan verileri desteklemez.
>

> [!NOTE]
> ADX ile tümleştirme, verilerinizde denetim ve ayrıntı düzeyi de sağlar. Daha fazla bilgi için bkz. [tasarım konuları](#design-considerations).
> 
## <a name="send-data-directly-to-azure-sentinel-and-adx-in-parallel"></a>Verileri doğrudan Azure Sentinel 'e ve ADX 'e paralel olarak gönderin

Algılamalar, olay araştırmaları, tehdit Arayıcı, UEBA vb. gibi Azure Sentinel 'de *güvenlik değeri olan* tüm verileri sürdürmek isteyebilirsiniz. Bu verilerin Azure 'da tutulması, genellikle güvenlik Operations Center (SOC) kullanıcıları avantajlarına sahiptir; burada genellikle 3-12 ay depolama alanı yeterlidir.

Ayrıca, *güvenlik değerinden bağımsız* olarak tüm verilerinizi aynı anda ADX 'e gönderilmek üzere, daha uzun bir süre içinde depolayabileceğiniz şekilde yapılandırabilirsiniz. Aynı anda hem Azure Sentinel hem de ADX 'e veri gönderilirken bazı çoğaltmayla sonuçlarken, Azure Sentinel 'de bekletme maliyetlerini azaltmanız durumunda maliyet tasarrufları önemli olabilir.

> [!TIP]
> Bu seçenek ayrıca, Azure Sentinel 'de depolanan güvenlik verilerini, ADX içinde depolanan işletimsel veya uzun süreli verilerle zenginleştirmek gibi veri depolarında verileri yaymanızı sağlar. Daha fazla bilgi için bkz. [Azure izleyici kullanarak çapraz kaynak sorgulama azure Veri Gezgini](/azure/azure-monitor/logs/azure-monitor-data-explorer-proxy).
>

Aşağıdaki görüntüde, ADX 'teki tüm verilerinizi nasıl koruuyorsanız, günlük kullanım için yalnızca güvenlik verilerinizi Azure Sentinel 'e gönderebilirsiniz.

:::image type="content" source="media/store-logs-in-adx/store-data-in-sentinel-and-adx-in-parallel.png" alt-text="Verileri ADX ve Azure Sentinel 'de paralel olarak depolayın.":::

Bu mimari seçeneğini uygulama hakkında daha fazla bilgi için bkz. [Azure Veri Gezgini izleme](/azure/architecture/solution-ideas/articles/monitor-azure-data-explorer).

## <a name="export-data-from-log-analytics-into-adx"></a>Log Analytics verileri ADX 'e aktarma

Verilerinizi doğrudan ADX 'e göndermek yerine, bir ADX Olay Hub 'ı veya Azure Data Factory aracılığıyla Log Analytics verilerinizi ADX 'e aktarmayı seçebilirsiniz.

### <a name="data-export-architecture"></a>Veri dışa aktarma mimarisi

Aşağıdaki görüntüde, Azure Izleyici alma işlem hattı aracılığıyla dışarıya aktarılmış verilerin örnek akışı gösterilmektedir. Verileriniz varsayılan olarak Log Analytics yönlendirilir, ancak Azure depolama hesabına veya Olay Hub 'ına aktarmak üzere de yapılandırabilirsiniz.

:::image type="content" source="media/store-logs-in-adx/export-data-from-azure-monitor.png" alt-text="Azure Izleyici-mimarisinden verileri dışarı aktarın.":::

Veri dışarı aktarma kurallarını yapılandırırken, dışarı aktarmak istediğiniz günlük türlerini seçin. Yapılandırıldıktan sonra, Log Analytics Alım uç noktasına ulaşan ve seçilen tablolar için çalışma alanınıza hedeflenen yeni veriler, depolama hesabınıza veya Olay Hub 'ınıza aktarılmalıdır.

Dışarı aktarma için verileri yapılandırırken aşağıdaki noktalara dikkat edin:

|Değerlendirme  | Ayrıntılar |
|---------|---------|
|**İçe aktarılmış verilerin kapsamı**     |  Belirli bir tablo için dışarı aktarma yapılandırıldıktan sonra, bu tabloya gönderilen tüm veriler özel durum olmadan dışarı aktarılabilir. Verilerinizin filtrelenmiş bir alt kümesi dışarı aktarılmış veya belirli olaylara dışarı aktarmayı sınırlandırırken desteklenmiyor.       |
|**Konum gereksinimleri**     |   Hem Azure Izleyici/Azure Sentinel çalışma alanı hem de hedef konumu (bir Azure depolama hesabı veya Olay Hub 'ı) aynı coğrafi bölgede bulunmalıdır.      |
|**Desteklenen tablolar**     | Özel günlük tabloları gibi, hiçbir tablo dışa aktarma için desteklenmez, ancak desteklenmez. <br><br>Daha fazla bilgi için bkz. [Azure izleyici 'de çalışma alanı verileri dışarı aktarma](/azure/azure-monitor/logs/logs-data-export) ve [desteklenen tablolar listesi](/azure/azure-monitor/logs/logs-data-export#supported-tables)Log Analytics.         |
|     |         |

### <a name="data-export-methods-and-procedures"></a>Veri dışa aktarma yöntemleri ve yordamları

Azure Sentinel 'ten ADX 'e veri aktarmak için aşağıdaki yordamlardan birini kullanın:

- **BIR ADX Olay Hub 'ı aracılığıyla**. Log Analytics verileri bir olay hub 'ına aktarın, buradan ADX 'e aktarabilirsiniz. Bu yöntem, Azure Sentinel ve ADX 'teki bazı verileri (ilk X ay) depolar.

- **Azure depolama ve Azure Data Factory aracılığıyla**. Log Analytics verilerinizi Azure Blob depolama alanına aktarın, ardından verileri ADX 'e daha fazla dışa aktarmak için düzenli bir kopyalama işi çalıştırmak için Azure Data Factory kullanılır. Bu yöntem, yalnızca Azure Sentinel/Log Analytics ' de bekletme sınırının ne zaman yaklaştığında Azure Data Factory, çoğaltmaktan kaçınarak verileri kopyalamanızı sağlar.

### <a name="adx-event-hub"></a>[ADX Olay Hub 'ı](#tab/adx-event-hub)

Bu bölümde, Azure Sentinel verilerinin Log Analytics bir olay hub 'ına nasıl aktarılacağı açıklanmaktadır ve burada ADX 'e aktarabilirsiniz. Verileri [doğrudan Azure Sentinel 'e ve ADX 'e paralel olarak göndermeye](#send-data-directly-to-azure-sentinel-and-adx-in-parallel)benzer şekilde, bu yöntem, verilerin Log Analytics ulaştığı gibi ADX 'e akışı sırasında bazı veri çoğaltmasından oluşur.

Aşağıdaki görüntüde, bir olay hub 'ına aktarılmış verilerin bir örnek akışı gösterilmektedir ve buradan ADX 'e alınır.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-event-hub.png" alt-text="ADX Olay Hub 'ı aracılığıyla verileri ADX 'e aktarın.":::

Önceki görüntüde gösterilen mimari, her *X* ayda bir sık erişilmesi gereken veriler için olay yönetimi, görsel araştırmalar, tehdit Arayıcı, gelişmiş görselleştirmeler, ueba ve daha fazlası dahil olmak üzere tam Azure Sentinel SIEM deneyimini sağlar. Aynı zamanda, Bu mimaride doğrudan ADX 'te veya Azure Sentinel aracılığıyla ADX ara sunucu özelliği sayesinde uzun süreli verileri sorgulayabilirsiniz. ADX 'te uzun süreli veri depolamaya yapılan sorgular, Azure Sentinel 'ten ADX 'e hiçbir değişiklik yapılmadan alınabilir.

> [!TIP]
> Bu yordam hakkında daha fazla bilgi için bkz. [öğretici: alma ve sorgu izleme verileri Azure Veri Gezgini](/azure/data-explorer/ingest-data-no-code).
>

**Bir olay hub 'ı aracılığıyla ADX 'e veri aktarmak için**:

1. **Log Analytics verilerinin bir olay hub 'ına verilmesini yapılandırın**. Daha fazla bilgi için bkz. [Azure izleyici 'de çalışma alanı verileri dışarı aktarma Log Analytics](/azure/azure-monitor/platform/logs-data-export).

1. **BIR ADX kümesi ve veritabanı oluşturun**. Daha fazla bilgi için bkz.

    - [Azure Veri Gezgini kümesi ve veritabanı oluşturma](/azure/data-explorer/create-cluster-database-portal)
    - [Azure Veri Gezgini kümeniz için doğru işlem SKU 'sunu seçin](/azure/data-explorer/manage-cluster-choose-sku)

1. **Hedef tablolar oluşturun**. Ham veriler öncelikle ham verilerin depolandığı, kullanıldığı ve genişletildiği bir ara tabloya alınır.

    Tüm yeni verilere uygulanan bir işleve benzer bir güncelleştirme ilkesi, genişletilmiş verileri Azure Sentinel 'deki özgün tabloyla aynı şemaya sahip son tabloya almak için kullanılır.

    Ham tablodaki saklama süresini **0** gün olarak ayarlayın. Veriler yalnızca düzgün şekilde biçimlendirilen tabloda depolanır ve dönüştürüldükten hemen sonra ham tabloda silinir.

    Daha fazla bilgi için bkz. [Azure Veri Gezgini 'de alma ve sorgu izleme verileri](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Tablo eşlemesi oluşturun**. JSON tablolarını, kayıtların ham olaylar tablosuna bir olay hub 'ından geldiği şekilde nasıl dönedikleri tanımlamak için eşleyin. Daha fazla bilgi için bkz. [ölçüm ve günlük verileri için güncelleştirme Ilkesi oluşturma](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Bir güncelleştirme Ilkesi oluşturun ve Ham kayıtlar tablosuna ekleyin**. Bu adımda, güncelleştirme ilkesi olarak adlandırılan bir işlev oluşturun ve verileri alma sırasında dönüştürülecek şekilde hedef tabloya ekleyin.

    > [!NOTE]
    > Bu adım yalnızca, ADX içinde, Azure Sentinel ile aynı şemaya ve formata sahip veri tabloları sağlamak istediğinizde gereklidir.
    >

    Daha fazla bilgi için bkz. [Azure Veri Gezgini bir olay hub 'ı bağlama](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **ADX içindeki ham veri tablosu ve Olay Hub 'ı arasında bir veri bağlantısı oluşturun**. ADX ' i, verilerin Olay Hub 'ına nasıl verileceği hakkındaki ayrıntıları ile yapılandırın.

    [Azure Veri Gezgini belgelerindeki](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) yönergeleri kullanın ve aşağıdaki ayrıntıları belirtin:

    - **Hedef**. Ham verilerin bulunduğu belirli bir tabloyu belirtin.
    - **Biçim**. `.json`Tablo biçimi olarak belirtin.
    - **Uygulanacak eşleme**. Yukarıdaki [4. adımda](#mapping) oluşturulan eşleme tablosunu belirtin.


1. **Hedef tablo için bekletme değiştirme**. [Varsayılan Azure Veri Gezgini bekletme ilkesi](/azure/data-explorer/kusto/management/retentionpolicy) , ihtiyacınız olan kadar uzun olabilir.

    Bekletme ilkesini bir yıla güncelleştirmek için aşağıdaki komutu kullanın:

    ```kusto
    .alter-merge table <tableName> policy retention softdelete = 365d recoverability = disabled
    ```
### <a name="azure-storage--azure-data-factory"></a>[Azure depolama/Azure Data Factory](#tab/azure-storage-azure-data-factory)

Bu bölümde, Azure Sentinel verilerinin Log Analytics Azure depolama 'ya nasıl aktarılacağı açıklanmaktadır; burada Azure Data Factory, verileri ADX içine aktarmak için düzenli bir iş çalıştırabilir.

Azure Storage ve Azure Data Factory kullanımı, Azure depolama 'dan verileri yalnızca Azure Sentinel/Log Analytics içindeki saklama sınırına yakın olduğunda kopyalamanızı sağlar. Veri yinelemesi yoktur ve ADX *yalnızca* Azure Sentinel 'de bekletme sınırından daha eski olan verilere erişmek için kullanılır.

> [!TIP]
> Azure Storage 'ı kullanmaya yönelik mimari ve eski verileriniz için Azure Data Factory, bu yöntem, genel olarak daha büyük maliyet tasarrufu sağlayabilir.
>
Aşağıdaki görüntüde, dışarı aktarılmış verilerin bir Azure depolama 'ya örnek akışı gösterilmektedir; burada Azure Data Factory, bir normal işi daha sonra ADX içine aktarmak üzere çalışır.

:::image type="content" source="media/store-logs-in-adx/ingest-data-to-adx-via-azure-storage-azure-data-factory.png" alt-text="Azure depolama ve Azure Data Factory aracılığıyla ADX 'e veri aktarın.":::

**Azure depolama ile verileri ADX 'e aktarmak ve Azure Data Factory**:

1. **Log Analytics verilerinin bir olay hub 'ına verilmesini yapılandırın**. Daha fazla bilgi için bkz. [Azure izleyici 'de çalışma alanı verileri dışarı aktarma Log Analytics](/azure/azure-monitor/logs/logs-data-export?tabs=portal#enable-data-export).

1. **BIR ADX kümesi ve veritabanı oluşturun**. Daha fazla bilgi için bkz.

    - [Azure Veri Gezgini kümesi ve veritabanı oluşturma](/azure/data-explorer/create-cluster-database-portal)
    - [Azure Veri Gezgini kümeniz için doğru işlem SKU 'sunu seçin](/azure/data-explorer/manage-cluster-choose-sku)

1. **Hedef tablolar oluşturun**. Ham veriler öncelikle ham verilerin depolandığı, kullanıldığı ve genişletildiği bir ara tabloya alınır.

    Tüm yeni verilere uygulanan bir işleve benzer bir güncelleştirme ilkesi, genişletilmiş verileri Azure Sentinel 'deki özgün tabloyla aynı şemaya sahip son tabloya almak için kullanılır.

    Ham tablodaki saklama süresini **0** gün olarak ayarlayın. Veriler yalnızca düzgün şekilde biçimlendirilen tabloda depolanır ve dönüştürüldükten hemen sonra ham tabloda silinir.

    Daha fazla bilgi için bkz. [Azure Veri Gezgini 'de alma ve sorgu izleme verileri](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. <a name="mapping"></a>**Tablo eşlemesi oluşturun**. JSON tablolarını, kayıtların ham olaylar tablosuna bir olay hub 'ından geldiği şekilde nasıl dönedikleri tanımlamak için eşleyin. Daha fazla bilgi için bkz. [ölçüm ve günlük verileri için güncelleştirme Ilkesi oluşturma](/azure/data-explorer/ingest-data-no-code?tabs=diagnostic-metrics).

1. **Bir güncelleştirme Ilkesi oluşturun ve Ham kayıtlar tablosuna ekleyin**. Bu adımda, güncelleştirme ilkesi olarak adlandırılan bir işlev oluşturun ve verileri alma sırasında dönüştürülecek şekilde hedef tabloya ekleyin.

    > [!NOTE]
    > Bu adım yalnızca, ADX içinde, Azure Sentinel ile aynı şemaya ve formata sahip veri tabloları sağlamak istediğinizde gereklidir.
    >

    Daha fazla bilgi için bkz. [Azure Veri Gezgini bir olay hub 'ı bağlama](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs).

1. **ADX içindeki ham veri tablosu ve Olay Hub 'ı arasında bir veri bağlantısı oluşturun**. ADX ' i, verilerin Olay Hub 'ına nasıl verileceği hakkındaki ayrıntıları ile yapılandırın.

    [Azure Veri Gezgini belgelerindeki](/azure/data-explorer/ingest-data-no-code?tabs=activity-logs) yönergeleri kullanın ve aşağıdaki ayrıntıları belirtin:

    - **Hedef**. Ham verilerin bulunduğu belirli bir tabloyu belirtin.
    - **Biçim**. `.json`Tablo biçimi olarak belirtin.
    - **Uygulanacak eşleme**. Yukarıdaki [4. adımda](#mapping) oluşturulan eşleme tablosunu belirtin.

1. **Azure Data Factory** Işlem hattını ayarlama:

    - Azure depolama ve Azure Veri Gezgini için bağlı hizmetler oluşturun. Daha fazla bilgi için bkz.

        - [Azure Data Factory kullanarak Azure Blob depolamada verileri kopyalama ve dönüştürme](/azure/data-factory/connector-azure-blob-storage)
        - [Azure Data Factory kullanarak Azure Veri Gezgini veri kopyalama](/azure/data-factory/connector-azure-data-explorer).

    - Azure depolama 'dan bir veri kümesi oluşturun. Daha fazla bilgi için bkz. [Azure Data Factory veri kümeleri](/azure/data-factory/concepts-datasets-linked-services).

    - **LastModifiedDate** özelliklerine göre kopyalama işlemi ile bir veri işlem hattı oluşturun.

        Daha fazla bilgi için bkz. [ **LastModifiedDate** ile yeni ve değiştirilmiş dosyaları kopyalama Azure Data Factory](/azure/data-factory/solution-template-copy-new-files-lastmodifieddate).

---

## <a name="design-considerations"></a>Tasarım konusunda dikkat edilmesi gerekenler

Azure Sentinel verilerinizi ADX 'te depolarken aşağıdaki öğeleri göz önünde bulundurun:

|Değerlendirme  |Description  |
|---------|---------|
|**Küme boyutu ve SKU**     | Kümenizdeki düğüm sayısını ve VM SKU 'sunu dikkatle planlayın. Bu faktörler, işlem gücü miktarını ve sık erişimli önbelleğinizin (SSD ve bellek) boyutunu saptacaktır. Önbelleğin daha büyük olması, daha fazla verinin daha yüksek bir performans ile sorgulanabileceksiniz. <br><br>Farklı yapılandırmalara oynayabileceği ve elde edilen maliyeti görebileceğiniz [ADX boyutlandırma hesaplayıcısını](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html)ziyaret etmeniz önerilir. <br><br>ADX 'in, küme yüküne göre gerektiğinde düğüm ekleme/kaldırma için akıllı kararlar veren bir otomatik ölçeklendirme özelliği de vardır. Daha fazla bilgi için bkz. [Azure Veri Gezgini 'da küme yatay ölçeklendirmeyi yönetme (genişleme) ve değişiklik talebini karşılamak için](/azure/data-explorer/manage-cluster-horizontal-scaling).      |
|**Sık/soğuk önbellek**     | ADX, etkin önbellekteki veri tabloları üzerinde denetim sağlar ve sonuçları daha hızlı döndürür. ADX kümenizde büyük miktarda veriniz varsa, sık erişimli önbellekte bulunan veriler üzerinde daha fazla ayrıntı düzeyi elde etmeniz için tabloları aya göre bölmek isteyebilirsiniz. <br><br>Daha fazla bilgi için bkz. [önbellek ilkesi (sık erişimli ve soğuk önbellek)](/azure/data-explorer/kusto/management/cachepolicy)       |
|**Bekletme**     |   ADX ' te, verilerin bir veritabanından veya tek bir tablodan ne zaman kaldırılacağını yapılandırabilirsiniz, bu da depolama maliyetlerinin sınırlandırmasının önemli bir parçasıdır. <br><br> Daha fazla bilgi için bkz. [bekletme ilkesi](/azure/data-explorer/kusto/management/retentionpolicy).       |
|**Güvenlik**     |  Birçok ADX ayarı, kimlik yönetimi, şifreleme vb. gibi verilerinizi korumanıza yardımcı olabilir. Özellikle rol tabanlı erişim denetimi (RBAC) için ADX, bir tablo içindeki veritabanlarına, tablolara veya hatta satırlara erişimi kısıtlamak üzere yapılandırılabilir. Daha fazla bilgi için bkz. Azure Veri Gezgini ve [satır düzeyi güvenlik](/azure/data-explorer/kusto/management/rowlevelsecuritypolicy) ['de güvenlik](/azure/data-explorer/security) .|
|**Veri paylaşımı**     |   ADX, iş ortakları ve satıcılar gibi diğer taraflara ait veri parçalarının kullanılabilmesini sağlar ve hatta diğer taraflardan veri satın alabilir. Daha fazla bilgi için bkz. Azure [veri paylaşma kullanarak azure Veri Gezgini veri paylaşma](/azure/data-explorer/data-share).      |
| **Diğer maliyet bileşenleri** | Aşağıdaki yöntemler için diğer maliyet bileşenlerini göz önünde bulundurun: <br><br>**BIR ADX Olay Hub 'ı aracılığıyla veri dışa aktarma**: <br>-Dışarı aktarılmış GB başına ücretlendirilen veri dışa aktarma maliyetlerini Log Analytics. <br>-İşleme birimine göre ücretlendirilen Olay Hub 'ı maliyetleri.  <br><br>**Verileri Azure depolama ve Azure Data Factory Ile dışarı aktarın**: <br>-Dışarı aktarılmış GB başına ücretlendirilen Log Analytics veri dışa aktarma. <br>-Bir depolanan GBs tarafından ücretlendirilen Azure Storage. <br>-Azure Data Factory, etkinlik kopyası başına ücretlendirilir.
|     |         |

## <a name="next-steps"></a>Sonraki adımlar

Verilerinizi nerede depoladığınızı göz önüne alarak Azure Sentinel kullanarak araştırma ve araştırmaya devam edin.

Daha fazla bilgi için bkz.

- [Öğretici: Azure Sentinel ile olayları araştırın](tutorial-investigate-cases.md)
- [Azure Sentinel ile tehditler için Hunt](hunting.md)
