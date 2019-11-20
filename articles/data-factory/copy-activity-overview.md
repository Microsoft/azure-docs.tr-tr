---
title: Etkinliği Azure Data Factory Kopyala
description: Azure Data Factory kopyalama etkinliği hakkında bilgi edinin. Desteklenen bir kaynak veri deposundan verileri desteklenen bir havuz veri deposuna kopyalamak için kullanabilirsiniz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: fa2876b88a520480813ebfb8af8219d53c32057a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075553"
---
# <a name="copy-activity-in-azure-data-factory"></a>Etkinliği Azure Data Factory Kopyala

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-data-movement-activities.md)
> * [Geçerli sürüm](copy-activity-overview.md)

Azure Data Factory, şirket içinde ve bulutta bulunan veri depoları arasında veri kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Verileri kopyaladıktan sonra, diğer etkinlikleri kullanarak daha fazla dönüşüm ve analiz yapabilirsiniz. Ayrıca, iş zekası (BI) ve uygulama tüketimine yönelik dönüştürme ve analiz sonuçlarını yayımlamak için kopyalama etkinliğini kullanabilirsiniz.

![Kopyalama etkinliğinin rolü](media/copy-activity-overview/copy-activity.png)

Kopyalama etkinliği bir [tümleştirme çalışma zamanı](concepts-integration-runtime.md)üzerinde yürütülür. Farklı veri kopyalama senaryolarında farklı türlerde tümleştirme çalışma zamanları kullanabilirsiniz:

* Herhangi bir IP 'den internet üzerinden herkese açık olarak erişilebilen iki veri deposu arasında veri kopyalarken kopyalama etkinliği için Azure tümleştirme çalışma zamanı ' nı kullanabilirsiniz. Bu tümleştirme çalışma zamanı güvenli, güvenilir, ölçeklenebilir ve [küresel olarak kullanılabilir](concepts-integration-runtime.md#integration-runtime-location).
* Şirket içinde veya erişim denetimi olan bir ağda (örneğin, bir Azure sanal ağı) bulunan veri depolarından ve buralardan veri kopyalarken, kendi kendine barındırılan tümleştirme çalışma zamanı ayarlamanız gerekir.

Tümleştirme çalışma zamanının her kaynak ve havuz veri deposuyla ilişkilendirilmesi gerekir. Kopyalama etkinliğinin hangi tümleştirme çalışma zamanının kullanılacağını nasıl belirlediği hakkında bilgi için bkz. [HANGI IR kullanacağınızı belirleme](concepts-integration-runtime.md#determining-which-ir-to-use).

Verileri bir kaynaktan havuza kopyalamak için kopyalama etkinliğini çalıştıran hizmet şu adımları gerçekleştirir:

1. Bir kaynak veri deposundan veri okur.
2. Serileştirme/seriyi kaldırma, sıkıştırma/açma, sütun eşleme vb. gerçekleştirir. Giriş veri kümesi, çıkış veri kümesi ve kopyalama etkinliğinin yapılandırmasına bağlı olarak bu işlemleri gerçekleştirir.
3. Veri havuz/hedef veri deposuna yazar.

![Kopyalama etkinliğine genel bakış](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Desteklenen veri depoları ve biçimler

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Desteklenen dosya biçimleri

Dosyaları kopyalamak için kopyalama etkinliğini iki dosya tabanlı veri deposu arasında olduğu gibi kullanabilirsiniz. Bu durumda, veriler serileştirme veya serisini kaldırma olmadan etkin bir şekilde kopyalanır.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Örneğin, aşağıdaki kopyalama etkinliklerini yapabilirsiniz:

* Şirket içi SQL Server veritabanından veri kopyalayın ve verileri Parquet biçiminde Azure Data Lake Storage 2. yazın.
* Metin (CSV) biçimindeki dosyaları şirket içi bir dosya sisteminden kopyalayın ve avro biçiminde Azure Blob depolama alanına yazın.
* ZIP dosyalarını şirket içi bir dosya sisteminden kopyalayın, sıkıştırmasını açıp Azure Data Lake Storage 2. yazın.
* Verileri Azure Blob depolama alanından gzip sıkıştırılmış metin (CSV) biçiminde kopyalayın ve Azure SQL veritabanı 'na yazın.
* Serileştirme/seri durumdan çıkarma veya sıkıştırma/sıkıştırmayı gerektiren çok sayıda etkinlik.

## <a name="supported-regions"></a>Desteklenen bölgeler

Kopyalama etkinliğini sağlayan hizmet, [Azure tümleştirme çalışma zamanı konumlarında](concepts-integration-runtime.md#integration-runtime-location)listelenen bölgelerde ve coğrafi olarak genel kullanıma sunulmuştur. Dünya çapında topolojisi genellikle bölgeler arası atlama önler verimli veri taşıma sağlar. Belirli bir bölgedeki Data Factory ve veri hareketinin kullanılabilirliğini denetlemek için bkz. [bölgelere göre ürünler](https://azure.microsoft.com/regions/#services) .

## <a name="configuration"></a>Yapılandırma

Kopyalama etkinliğini Azure Data Factory kullanmak için şunları yapmanız gerekir:

1. **Kaynak veri deposu ve havuz veri deposu için bağlı hizmetler oluşturun.** Yapılandırma bilgileri ve desteklenen özellikler için bağlayıcı makalesinin "bağlı hizmet özellikleri" bölümüne bakın. Desteklenen bağlayıcıların listesini, bu makalenin [desteklenen veri depoları ve biçimleri](#supported-data-stores-and-formats) bölümünde bulabilirsiniz.
2. **Kaynak ve havuz için veri kümeleri oluşturun.** Yapılandırma bilgileri ve desteklenen özellikler için kaynak ve havuz bağlayıcı makalelerinin "veri kümesi özellikleri" bölümlerine bakın.
3. **Kopyalama etkinliğiyle bir işlem hattı oluşturun.** Sonraki bölümde, bir örnek sağlar.

### <a name="syntax"></a>Sözdizimi

Bir kopyalama etkinliğinin aşağıdaki şablonu desteklenen özelliklerin kapsamlı bir listesini içerir. Senaryonuza uygun olanları belirtin.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>Söz dizimi ayrıntıları

| Özellik | Açıklama | Gerekli mi? |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği için `Copy` olarak ayarlayın | Yes |
| inputs | Kaynak verilere işaret eden oluşturduğunuz veri kümesini belirtin. Kopyalama etkinliği yalnızca tek bir girişi destekler. | Yes |
| outputs | Havuz verilerine işaret eden oluşturduğunuz veri kümesini belirtin. Kopyalama etkinliği yalnızca tek bir çıktıyı destekler. | Yes |
| typeProperties | Kopyalama etkinliğini yapılandırmak için özellikleri belirtin. | Yes |
| source | Kopyalama kaynağı türünü ve verileri almak için karşılık gelen özellikleri belirtin.<br/><br/>Daha fazla bilgi için [desteklenen veri depoları ve biçimleri](#supported-data-stores-and-formats)bölümünde listelenen bağlayıcı makalesindeki "etkinlik özelliklerini kopyalama" bölümüne bakın. | Yes |
| sink | Kopyalama havuz türünü ve verileri yazmak için karşılık gelen özellikleri belirtin.<br/><br/>Daha fazla bilgi için [desteklenen veri depoları ve biçimleri](#supported-data-stores-and-formats)bölümünde listelenen bağlayıcı makalesindeki "etkinlik özelliklerini kopyalama" bölümüne bakın. | Yes |
| translator | Kaynak havuzu için açıkça bir sütun eşlemelerini belirtin. Bu özellik, varsayılan kopyalama davranışı gereksinimlerinizi karşılamıyorsa geçerlidir.<br/><br/>Daha fazla bilgi için bkz. [kopyalama etkinliğinde şema eşleme](copy-activity-schema-and-type-mapping.md). | Hayır |
| dataIntegrationUnits | [Azure Integration Runtime](concepts-integration-runtime.md) 'ın veri kopyalama için kullandığı güç miktarını temsil eden bir ölçü belirtin. Bu birimler daha önce bulut veri taşıma birimi (DMU) olarak bilinirdi. <br/><br/>Daha fazla bilgi için bkz. [veri tümleştirme birimleri](copy-activity-performance.md#data-integration-units). | Hayır |
| parallelCopies | Kaynaktan veri okurken ve havuza veri yazarken kopyalama etkinliğinin kullanmasını istediğiniz paralellik belirleyin.<br/><br/>Daha fazla bilgi için bkz. [paralel kopya](copy-activity-performance.md#parallel-copy). | Hayır |
| enableStaging<br/>stagingSettings | Verileri kaynaktan havuza doğrudan kopyalamak yerine, blob depolamada geçici verilerin gösterilip gösterilmeyeceğini belirtin.<br/><br/>Faydalı senaryolar ve yapılandırma ayrıntıları hakkında daha fazla bilgi için bkz. [aşamalı kopya](copy-activity-performance.md#staged-copy). | Hayır |
| enableskipıncompatiblerow<br/>redirectıncompatiblerowsettings| Kaynaktan havuza veri kopyaladığınızda uyumsuz satırları nasıl işleyeceğinizi seçin.<br/><br/>Daha fazla bilgi için bkz. [hata toleransı](copy-activity-fault-tolerance.md). | Hayır |

## <a name="monitoring"></a>İzleme

Azure Data Factory **Author & İzleyici** Kullanıcı arabirimindeki veya program aracılığıyla kopyalama etkinliğini izleyebilirsiniz.

### <a name="monitor-visually"></a>Görsel olarak izleme

Kopyalama etkinliği çalıştırmasını görsel olarak izlemek için veri fabrikanıza gidin ve sonra **yazar & İzleyicisi**' ne gidin. **İzleme** sekmesinde, eylemler sütunundaki **etkinlik çalıştırmasını görüntüle** düğmesinin bulunduğu **işlem** hattı çalıştırmaları listesini görürsünüz:

![İşlem hattı çalıştırmalarını izleme](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

İşlem hattı çalıştırmasında etkinliklerin listesini görmek için **etkinlik çalıştırmalarını görüntüle** ' yi seçin. **Eylemler** sütununda, kopyalama etkinliği girişi, çıkışı, hataları (kopyalama etkinliği başarısız olursa) bağlantılarını ve ayrıntıları görürsünüz:

![Etkinlik çalıştırmalarını izleme](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Kopyalama etkinliğinin yürütme ayrıntılarını ve performans özelliklerini görmek için **Eylemler** sütunundaki **Ayrıntılar** düğmesini seçin. Birim/satır sayısı/kaynaktan havuz, aktarım hızı, kopyalama etkinliğinin ilgili sürelerine ve kopyalama senaryonuz için kullanılan yapılandırmalara kadar olan veri sayısı gibi bilgiler görürsünüz.

>[!TIP]
>Bazı senaryolarda, kopyalama izleme sayfasının en üstünde **performans ayarlama ipuçları** da görürsünüz. Bu ipuçları, belirlenen performans sorunlarını ve kopyalama aktarım hızını artırmak için nelerin değiştirileceği hakkında bilgi sağlar. Bir örnek için, bu makalenin [performans ve ayarlama](#performance-and-tuning) bölümüne bakın.

Örnek: Azure Data Lake Store
![etkinlik çalıştırma ayrıntılarını Izlemek **Için Amazon S3 'Ten kopyalayın**](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Örnek: Azure SQL veritabanından Azure SQL veri ambarı 'Na kopyalama**
![izleme etkinliği çalıştırma ayrıntıları](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Program aracılığıyla izleyin

Kopyalama etkinliği yürütme ayrıntıları ve performans özellikleri de aynı zamanda **kopyalama etkinliği çalıştırma sonucu** > **çıktı** bölümünde döndürülür. Aşağıda, döndürülebilecek özelliklerin tamamı listelenmiştir. Yalnızca kopyalama senaryonuz için geçerli olan özellikleri görürsünüz. Etkinlik çalıştırmalarını izleme hakkında daha fazla bilgi için bkz. işlem [hattı çalıştırmasını izleme](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Özellik adı  | Açıklama | Birim |
|:--- |:--- |:--- |
| dataRead | Kaynaktan okunan veri miktarı. | Int64 değeri, bayt cinsinden |
| dataWritten | Havuza yazılan veri miktarı. | Int64 değeri, bayt cinsinden |
| filesRead | Dosya depolamadan kopyalama sırasında kopyalanan dosya sayısı. | Int64 değeri (birim) |
| filesWritten | Dosya depolama alanına kopyalama sırasında kopyalanan dosya sayısı. | Int64 değeri (birim) |
| Kaynakcepeakconnections | Kopyalama etkinliği sırasında kaynak veri deposuna kurulan en yüksek eşzamanlı bağlantı sayısı. | Int64 değeri (birim) |
| sinkPeakConnections | Kopyalama etkinliği sırasında havuz veri deposuna kurulan en yüksek eşzamanlı bağlantı sayısı. | Int64 değeri (birim) |
| rowsRead | Kaynaktan okunan satır sayısı (ikili kopya için geçerli değildir). | Int64 değeri (birim) |
| rowsCopied | Havuza kopyalanmış satır sayısı (ikili kopya için geçerli değildir). | Int64 değeri (birim) |
| rowsSkipped | Atlanan uyumsuz satır sayısı. `enableSkipIncompatibleRow` true olarak ayarlayarak, uyumsuz satırların atlanmasını etkinleştirebilirsiniz. | Int64 değeri (birim) |
| copyDuration | Kopya çalıştırmasının süresi. | Int32 değeri, saniye cinsinden |
| throughput | Veri aktarımı oranı. | Kayan nokta sayısı, KBps cinsinden |
| Kaynakcepeakconnections | Kopyalama etkinliği sırasında kaynak veri deposuna kurulan en yüksek eşzamanlı bağlantı sayısı. | Int32 değeri (birim yok) |
| sinkPeakConnections| Kopyalama etkinliği sırasında havuz veri deposuna kurulan en yüksek eşzamanlı bağlantı sayısı.| Int32 değeri (birim yok) |
| sqlDwPolyBase | Veriler SQL veri ambarı 'na kopyalandığında PolyBase 'in kullanılıp kullanılmayacağını belirtir. | Boole |
| redshiftUnload | Redshift öğesinden veri kopyalanırken KALDıRMA kullanılıp kullanılmayacağını belirtir. | Boole |
| hdfsDistcp | Veriler, ne zaman bir sunucudan kopyalanırsa kullanılır. | Boole |
| effectiveIntegrationRuntime | Etkinlik çalıştırmasının gücü için kullanılan Integration Runtime (IR) veya çalışma zamanları `<IR name> (<region if it's Azure IR>)`biçiminde. | Metin (dize) |
| usedDataIntegrationUnits | Kopyalama sırasında etkili veri tümleştirme birimi. | Int32 değeri |
| usedParallelCopies | Kopyalama sırasında etkili parallelCopies. | Int32 değeri |
| redirectRowPath | `redirectIncompatibleRowSettings` özelliğinde yapılandırdığınız blob depolamada atlanan uyumsuz satırların yolu. Bu makalenin ilerleyen kısımlarında [hata toleransı](#fault-tolerance) bölümüne bakın. | Metin (dize) |
| executionDetails | Kopyalama etkinliğinin aşamaları hakkında daha fazla ayrıntı ve ilgili adımlar, süreler, konfigürasyonlar vb. Bu bölümü değiştirebileceğinden, bu bölümü ayrıştırmayı önermiyoruz.<br/><br/>Data Factory Ayrıca, `detailedDurations`altında çeşitli aşamalarda harcanan ayrıntılı süreleri (saniye cinsinden) raporlar. Bu adımların süreleri özeldir. Yalnızca verilen kopyalama etkinliği için uygulanan süreler görüntülenir:<br/>**Sıraya alma süresi** (`queuingDuration`): kopyalama etkinliğinin gerçekten tümleştirme çalışma zamanında başlamasından önceki zaman miktarı. Şirket içinde barındırılan bir IR kullanırsanız ve bu değer büyükse, IR kapasitesini ve kullanımını kontrol edin ve iş yükünüze göre ölçeği büyütme veya genişletme. <br/>**Kopyalama öncesi betik süresi** (`preCopyScriptDuration`): kopyalama etkinliğinin IR üzerinde başladığı zaman ve kopyalama etkinliğinin, havuz veri deposundaki kopyalama öncesi betiğini çalıştırmayı bitirdiğinde geçen süre. Kopyalama öncesi betiği yapılandırdığınızda uygulanır. <br/>**İlk bayta kalan süre** (`timeToFirstByte`): önceki adımın sonu ile IR 'nin kaynak veri deposundan ilk baytı aldığı zaman arasında geçen süre. Dosya tabanlı olmayan kaynaklar için geçerlidir. Bu değer büyükse, sorguyu veya sunucuyu denetleyin ve iyileştirin.<br/>**Aktarım süresi** (`transferDuration`): önceki adımın sonu ile IR 'nin tüm verileri kaynaktan havuza aktardığı zaman arasında geçen süre. | Dizi |
| Perfönerisi | Performans ayarlama ipuçlarını kopyalayın. Ayrıntılar için bkz. [performans ve ayarlama](#performance-and-tuning) . | Dizi |

```json
"output": {
    "dataRead": 6198358,
    "dataWritten": 19169324,
    "filesRead": 1,
    "sourcePeakConnections": 1,
    "sinkPeakConnections": 2,
    "rowsRead": 39614,
    "rowsCopied": 39614,
    "copyDuration": 1325,
    "throughput": 4.568,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 4,
    "usedParallelCopies": 1,
    "executionDetails": [
        {
            "source": {
                "type": "AzureBlobStorage"
            },
            "sink": {
                "type": "AzureSqlDatabase"
            },
            "status": "Succeeded",
            "start": "2019-08-06T01:01:36.7778286Z",
            "duration": 1325,
            "usedDataIntegrationUnits": 4,
            "usedParallelCopies": 1,
            "detailedDurations": {
                "queuingDuration": 2,
                "preCopyScriptDuration": 12,
                "transferDuration": 1311
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "Sink Azure SQL Database: The DTU utilization was high during the copy activity run. To achieve better performance, you are suggested to scale the database to a higher tier than the current 1600 DTUs.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2043368",
            "RuleName": "AzureDBTierUpgradePerfRecommendRule"
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Şema ve veri türü eşlemesi

Kopyalama etkinliğinin kaynak verilerinizi havuzunuzu nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşleme](copy-activity-schema-and-type-mapping.md) .

## <a name="fault-tolerance"></a>Hataya dayanıklılık

Varsayılan olarak, kopyalama etkinliği verileri kopyalamayı ve kaynak veri satırları havuz Veri satırlarıyla uyumsuz olduğunda bir hata döndürür. Kopyalamanın başarılı olması için kopyalama etkinliğini, uyumsuz satırları atlayıp günlüğe kaydetmek ve yalnızca uyumlu verileri kopyalamak üzere yapılandırabilirsiniz. Ayrıntılar için bkz. [kopyalama etkinliği hata toleransı](copy-activity-fault-tolerance.md) .

## <a name="performance-and-tuning"></a>Performans ve ayar

[Kopyalama etkinliği performans ve ölçeklenebilirlik Kılavuzu](copy-activity-performance.md) , Azure Data Factory ' deki kopyalama etkinliği aracılığıyla veri hareketinin performansını etkileyen temel faktörleri açıklar. Ayrıca, test sırasında gözlemlenen performans değerlerini listeler ve kopyalama etkinliğinin performansının nasıl iyileştirileceği açıklanır.

Bazı senaryolarda, Data Factory bir kopyalama etkinliği çalıştırdığınızda, aşağıdaki örnekte gösterildiği gibi [kopyalama etkinliği izleme sayfasının](#monitor-visually)en üstünde **performans ayarlama ipuçları** görürsünüz. İpuçları, belirtilen kopya çalıştırması için tanımlanan performans sorunlarını bildirir. Ayrıca kopyalama aktarım hızını artırmak için nelerin değiştirileceği hakkında bilgiler sağlar. Performans ayarlama ipuçları şu anda, verileri Azure SQL veri ambarı 'na kopyalarken PolyBase kullanma, veri deposu tarafındaki kaynağın performans sorunu ve kaldırılması durumunda Azure Cosmos DB ru veya Azure SQL veritabanı DTU 'ları gereksiz hazırlanmış kopyalar.

**Örnek: Azure SQL veritabanı 'na kopyalama, bir performans ayarlama ipucuyla**

Bu örnekte, bir kopya çalıştırması sırasında, Data Factory havuz Azure SQL veritabanı 'nda yüksek DTU kullanımını izler. Bu durum, yazma işlemlerini yavaşlatır. Öneri, Azure SQL veritabanı katmanındaki DTU 'ları artırmaya yönelik bir öneridir:

![Performansı ayarlama ipuçlarıyla izleme kopyalama](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

## <a name="incremental-copy"></a>Artımlı kopyalama
Data Factory, Delta verilerini bir kaynak veri deposundan bir havuz veri deposuna artımlı olarak kopyalamanızı sağlar. Ayrıntılar için bkz. [öğretici: artımlı olarak veri kopyalama](tutorial-incremental-copy-overview.md).

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki Hızlı Başlangıç kılavuzlarımız, öğreticilerimiz ve örneklerimizle bakın:

- [Verileri bir konumdan aynı Azure Blob depolama hesabındaki başka bir konuma kopyalama](quickstart-create-data-factory-dot-net.md)
- [Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalama](tutorial-copy-data-dot-net.md)
- [Şirket içi SQL Server veritabanından Azure 'a veri kopyalama](tutorial-hybrid-copy-powershell.md)
