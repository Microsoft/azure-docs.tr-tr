---
title: Etkinliği Azure Data Factory Kopyala
description: Azure Data Factory kopyalama etkinliği hakkında bilgi edinin. Desteklenen bir kaynak veri deposundan verileri desteklenen bir havuz veri deposuna kopyalamak için kullanabilirsiniz.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387912"
---
# <a name="copy-activity-in-azure-data-factory"></a>Etkinliği Azure Data Factory Kopyala

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-data-movement-activities.md)
> * [Güncel sürüm](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory, şirket içinde ve bulutta bulunan veri depoları arasında veri kopyalamak için kopyalama etkinliğini kullanabilirsiniz. Verileri kopyaladıktan sonra, diğer etkinlikleri kullanarak daha fazla dönüşüm ve analiz yapabilirsiniz. Ayrıca, iş zekası (BI) ve uygulama tüketimine yönelik dönüştürme ve analiz sonuçlarını yayımlamak için kopyalama etkinliğini kullanabilirsiniz.

![Kopyalama etkinliğinin rolü](media/copy-activity-overview/copy-activity.png)

Kopyalama etkinliği bir [tümleştirme çalışma zamanı](concepts-integration-runtime.md)üzerinde yürütülür. Farklı veri kopyalama senaryolarında farklı türlerde tümleştirme çalışma zamanları kullanabilirsiniz:

* Herhangi bir IP 'den internet üzerinden herkese açık olarak erişilebilen iki veri deposu arasında veri kopyalarken kopyalama etkinliği için Azure tümleştirme çalışma zamanı ' nı kullanabilirsiniz. Bu tümleştirme çalışma zamanı güvenli, güvenilir, ölçeklenebilir ve [küresel olarak kullanılabilir](concepts-integration-runtime.md#integration-runtime-location).
* Şirket içinde veya erişim denetimi olan bir ağda (örneğin, bir Azure sanal ağı) bulunan veri depolarından ve buralardan veri kopyalarken, kendi kendine barındırılan tümleştirme çalışma zamanı ayarlamanız gerekir.

Tümleştirme çalışma zamanının her kaynak ve havuz veri deposuyla ilişkilendirilmesi gerekir. Kopyalama etkinliğinin hangi tümleştirme çalışma zamanının kullanılacağını nasıl belirlediği hakkında bilgi için bkz. [HANGI IR kullanacağınızı belirleme](concepts-integration-runtime.md#determining-which-ir-to-use).

Verileri bir kaynaktan havuza kopyalamak için kopyalama etkinliğini çalıştıran hizmet şu adımları gerçekleştirir:

1. Kaynak veri deposundan verileri okur.
2. Serileştirme/seriyi kaldırma, sıkıştırma/açma, sütun eşleme vb. gerçekleştirir. Giriş veri kümesi, çıkış veri kümesi ve kopyalama etkinliğinin yapılandırmasına bağlı olarak bu işlemleri gerçekleştirir.
3. Verileri havuz/hedef veri deposuna yazar.

![Kopyalama etkinliğine genel bakış](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Desteklenen veri depoları ve biçimleri

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Desteklenen dosya biçimleri

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Kopyalama etkinliğini, dosyaları iki dosya tabanlı veri deposu arasında olduğu gibi kopyalamak için kullanabilirsiniz. Bu durumda, veriler serileştirme veya seri durumundan çıkarma yapılmadan verilerin verimli bir şekilde kopyalanabilmesi. Ayrıca, belirli bir biçimin dosyalarını ayrıştırarak veya oluşturabilirsiniz, örneğin, aşağıdakileri yapabilirsiniz:

* SQL Server veritabanından verileri kopyalayın ve Parquet biçiminde Azure Data Lake Storage 2. yazın.
* Metin (CSV) biçimindeki dosyaları şirket içi bir dosya sisteminden kopyalayın ve avro biçiminde Azure Blob depolama alanına yazın.
* ZIP dosyalarını şirket içi bir dosya sisteminden kopyalayın, açık olarak açıp Azure Data Lake Storage 2. ve ayıklanan dosyaları yazın.
* Verileri Azure Blob depolama alanından gzip sıkıştırılmış metin (CSV) biçiminde kopyalayın ve Azure SQL veritabanı 'na yazın.
* Serileştirme/seri durumdan çıkarma veya sıkıştırma/sıkıştırmayı gerektiren çok sayıda etkinlik.

## <a name="supported-regions"></a>Desteklenen bölgeler

Kopyalama etkinliğini sağlayan hizmet, [Azure tümleştirme çalışma zamanı konumlarında](concepts-integration-runtime.md#integration-runtime-location)listelenen bölgelerde ve coğrafi olarak genel kullanıma sunulmuştur. Genel olarak kullanılabilir topoloji, genellikle bölgeler arası atlamaları önleyen etkili veri hareketini sağlar. Belirli bir bölgedeki Data Factory ve veri hareketinin kullanılabilirliğini denetlemek için bkz. [bölgelere göre ürünler](https://azure.microsoft.com/regions/#services) .

## <a name="configuration"></a>Yapılandırma

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Genel olarak, Azure Data Factory kopyalama etkinliğini kullanmak için şunları yapmanız gerekir:

1. **Kaynak veri deposu ve havuz veri deposu için bağlı hizmetler oluşturun.** Desteklenen bağlayıcıların listesini, bu makalenin [desteklenen veri depoları ve biçimleri](#supported-data-stores-and-formats) bölümünde bulabilirsiniz. Yapılandırma bilgileri ve desteklenen özellikler için bağlayıcı makalesinin "bağlı hizmet özellikleri" bölümüne bakın. 
2. **Kaynak ve havuz için veri kümeleri oluşturun.** Yapılandırma bilgileri ve desteklenen özellikler için kaynak ve havuz bağlayıcı makalelerinin "veri kümesi özellikleri" bölümlerine bakın.
3. **Kopyalama etkinliğiyle bir işlem hattı oluşturun.** Sonraki bölümde bir örnek sağlanmaktadır.

### <a name="syntax"></a>Syntax

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
| tür | Kopyalama etkinliği için, `Copy` | Yes |
| girişi | Kaynak verilere işaret eden oluşturduğunuz veri kümesini belirtin. Kopyalama etkinliği yalnızca tek bir girişi destekler. | Yes |
| çıkışı | Havuz verilerine işaret eden oluşturduğunuz veri kümesini belirtin. Kopyalama etkinliği yalnızca tek bir çıktıyı destekler. | Yes |
| typeProperties | Kopyalama etkinliğini yapılandırmak için özellikleri belirtin. | Yes |
| kaynak | Kopyalama kaynağı türünü ve verileri almak için karşılık gelen özellikleri belirtin.<br/>Daha fazla bilgi için [desteklenen veri depoları ve biçimleri](#supported-data-stores-and-formats)bölümünde listelenen bağlayıcı makalesindeki "etkinlik özelliklerini kopyalama" bölümüne bakın. | Yes |
| Ev | Kopyalama havuz türünü ve verileri yazmak için karşılık gelen özellikleri belirtin.<br/>Daha fazla bilgi için [desteklenen veri depoları ve biçimleri](#supported-data-stores-and-formats)bölümünde listelenen bağlayıcı makalesindeki "etkinlik özelliklerini kopyalama" bölümüne bakın. | Yes |
| çevirmen | Kaynaktan havuza açık sütun eşlemeleri belirtin. Bu özellik, varsayılan kopyalama davranışı gereksinimlerinizi karşılamıyorsa geçerlidir.<br/>Daha fazla bilgi için bkz. [kopyalama etkinliğinde şema eşleme](copy-activity-schema-and-type-mapping.md). | No |
| Veri tümleştirme birimleri | [Azure Integration Runtime](concepts-integration-runtime.md) 'ın veri kopyalama için kullandığı güç miktarını temsil eden bir ölçü belirtin. Bu birimler daha önce bulut veri taşıma birimi (DMU) olarak bilinirdi. <br/>Daha fazla bilgi için bkz. [veri tümleştirme birimleri](copy-activity-performance-features.md#data-integration-units). | No |
| Paralellkopyalar | Kaynaktan veri okurken ve havuza veri yazarken kopyalama etkinliğinin kullanmasını istediğiniz paralellik belirleyin.<br/>Daha fazla bilgi için bkz. [paralel kopya](copy-activity-performance-features.md#parallel-copy). | No |
| koruyup | Veri kopyalama sırasında meta verilerin/ACL 'Lerin korunup korunmayacağını belirtin. <br/>Daha fazla bilgi için bkz. [meta verileri koruma](copy-activity-preserve-metadata.md). |No |
| Enablehazırlama<br/>stagingSettings | Verileri kaynaktan havuza doğrudan kopyalamak yerine, blob depolamada geçici verilerin gösterilip gösterilmeyeceğini belirtin.<br/>Faydalı senaryolar ve yapılandırma ayrıntıları hakkında daha fazla bilgi için bkz. [aşamalı kopya](copy-activity-performance-features.md#staged-copy). | No |
| Enableskipıncompatiblerow<br/>Redirectıncompatiblerowsettings| Kaynaktan havuza veri kopyaladığınızda uyumsuz satırları nasıl işleyeceğinizi seçin.<br/>Daha fazla bilgi için bkz. [hata toleransı](copy-activity-fault-tolerance.md). | No |

## <a name="monitoring"></a>İzleme

Azure Data Factory kopyalama etkinliğini hem görsel hem de programlı olarak bir şekilde izleyebilirsiniz. Ayrıntılar için bkz. [kopyalama etkinliğini izleme](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Artımlı kopya

Data Factory, Delta verilerini bir kaynak veri deposundan bir havuz veri deposuna artımlı olarak kopyalamanızı sağlar. Ayrıntılar için bkz. [öğretici: artımlı olarak veri kopyalama](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Performans ve ayar

[Kopyalama etkinliği izleme](copy-activity-monitoring.md) deneyimi, etkinlik çalıştırabileceğiniz her biri için kopyalama performansı istatistiklerini gösterir. [Kopyalama etkinliği performans ve ölçeklenebilirlik Kılavuzu](copy-activity-performance.md) , Azure Data Factory ' deki kopyalama etkinliği aracılığıyla veri hareketinin performansını etkileyen temel faktörleri açıklar. Ayrıca, test sırasında gözlemlenen performans değerlerini listeler ve kopyalama etkinliğinin performansının nasıl iyileştirileceği açıklanır.

## <a name="resume-from-last-failed-run"></a>Son başarısız çalıştırmayı geri edin

Kopyalama etkinliği, büyük boyutlu dosyaları dosya tabanlı mağazalar arasında ikili biçimde kopyaladığınızda son başarısız çalıştıralım işlemini destekler, örneğin, Amazon S3 ' den Azure Data Lake Storage 2. ' ye veri geçirmek için. Şu dosya tabanlı bağlayıcılar için geçerlidir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,](connector-hdfs.md)ve [SFTP](connector-sftp.md).

Kopyalama etkinliği özgeçmişi aşağıdaki iki şekilde yararlanabilirsiniz:

- **Etkinlik düzeyi yeniden deneme:** Kopyalama etkinliği için yeniden deneme sayısı ayarlayabilirsiniz. İşlem hattı yürütmesi sırasında, bu kopyalama etkinliği başarısız olursa, sonraki otomatik yeniden deneme son deneme hata noktasından başlar.
- **Başarısız etkinlikten yeniden çalıştır:** İşlem hattı yürütme tamamlandıktan sonra, ADF Kullanıcı arabirimi izleme görünümündeki veya programlı olarak başarısız etkinlikten yeniden çalıştır tetikleyebilirsiniz. Başarısız etkinlik bir kopyalama etkinliğidir, işlem hattı Bu etkinlikten yalnızca yeniden çalıştırılmaz, ancak aynı zamanda önceki çalıştırmanın hata noktasından de sürdürülür.

    ![Kopyalama özgeçmişi](media/copy-activity-overview/resume-copy.png)

Birkaç noktaya göz önünde:

- Dosya düzeyinde özgeçmişde gerçekleşir. Bir dosya kopyalanırken kopyalama etkinliği başarısız olursa, bir sonraki çalıştırmasında, bu belirli dosya yeniden kopyalanacaktır.
- Özgeçmişin düzgün çalışması için yeniden başlatma arasında kopyalama etkinliği ayarlarını değiştirmeyin.
- Amazon S3, Azure blob, Azure Data Lake Storage 2. ve Google Cloud Storage 'dan verileri kopyaladığınızda kopyalama etkinliği, rastgele sayıda kopyalanmış dosyanın içinden sürdürülür. Kaynak olarak dosya tabanlı bağlayıcıların geri kalanı için, kopyalama etkinliği, genellikle onlarca binlerce ve dosya yollarının uzunluğuna bağlı olarak değişiklik gösterdiği gibi, sınırlı sayıda dosyadan sürdürmeyi destekler; Bu sayıdan daha fazla dosya yeniden yönlendirme sırasında yeniden kopyalanacaktır.

İkili dosya kopyalama işleminden farklı senaryolar için kopyalama etkinliği yeniden çalıştırma başlangıçtan başlar.

## <a name="preserve-metadata-along-with-data"></a>Meta verileri verilerle birlikte koruyun

Verileri kaynaktan havuza kopyalarken, Data Lake geçişi gibi senaryolarda, kopyalama etkinliğini kullanarak verilerle birlikte meta verileri ve ACL 'Leri de korumayı seçebilirsiniz. Ayrıntılar için bkz. [meta verileri koruma](copy-activity-preserve-metadata.md) .

## <a name="schema-and-data-type-mapping"></a>Şema ve veri türü eşleme

Kopyalama etkinliğinin kaynak verilerinizi havuzunuzu nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşleme](copy-activity-schema-and-type-mapping.md) .

## <a name="add-additional-columns-during-copy"></a>Kopyalama sırasında ek sütunlar ekleme

Kaynak veri deposundan havuza veri kopyalamanın yanı sıra, havuza kopyalamak üzere ek veri sütunları da eklemek için yapılandırabilirsiniz. Örnek:

- Dosya tabanlı kaynaktan kopyalama yapıldığında, verilerin hangi dosyadan geldiğini izlemek için göreli dosya yolunu ek bir sütun olarak depolayın.
- Belirtilen kaynak sütununu başka bir sütun olarak çoğaltın. 
- İşlem hattı adı/işlem hattı KIMLIĞI gibi ADF sistem değişkenlerini eklemek için ADF ifadesi içeren bir sütun ekleyin veya yukarı akış etkinliğinin çıktısından diğer dinamik değeri saklayın.
- Aşağı akış tüketimi gereksinimini karşılamak için statik değeri olan bir sütun ekleyin.

Kopyalama etkinliği kaynağı sekmesine aşağıdaki yapılandırmayı bulabilirsiniz. Ayrıca, bu ek sütunları, tanımlı sütun adlarınızı kullanarak her zamanki gibi kopyalama etkinlik [şeması eşlemesinde](copy-activity-schema-and-type-mapping.md#schema-mapping) da eşleyebilirsiniz. 

![Kopyalama etkinliğine ek sütunlar ekleme](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Bu özellik, en son veri kümesi modeliyle birlikte kullanılır. Bu seçeneği kullanıcı arabiriminden görmüyorsanız, yeni bir veri kümesi oluşturmayı deneyin.

Program aracılığıyla yapılandırmak için `additionalColumns` kopyalama etkinlik kaynağınıza özelliği ekleyin:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| additionalColumns | Havuza kopyalamak için ek veri sütunları ekleyin.<br><br>Dizideki her nesne `additionalColumns` bir ek sütunu temsil eder. , `name` Sütun adını tanımlar ve `value` Bu sütunun veri değerini gösterir.<br><br>İzin verilen veri değerleri şunlardır:<br>- **`$$FILEPATH`** -ayrılmış bir değişken, kaynak dosyaların göreli yolunu veri kümesinde belirtilen klasör yoluna depolayacağını gösterir. Dosya tabanlı kaynağa uygulayın.<br>- **`$$COLUMN:<source_column_name>`** -ayrılmış bir değişken stili, belirtilen kaynak sütununun başka bir sütun olarak yinelendiğini belirtir<br>- **İfadesini**<br>- **Statik değer** | No |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Havuz tablolarını otomatik oluştur

Verileri SQL Database/Azure SYNAPSE Analytics 'e kopyalarken, hedef tablo yoksa, kopyalama etkinliği kaynak verilere göre otomatik olarak oluşturulmasını destekler. Verileri yüklemeye ve SQL Database/Azure SYNAPSE Analytics 'i değerlendirmenize hızlı bir şekilde başlamanıza yardımcı olacak. Veri alındıktan sonra havuz tablosu şemasını gereksinimlerinize göre gözden geçirebilir ve ayarlayabilirsiniz.

Bu özellik, herhangi bir kaynaktaki verileri aşağıdaki havuz veri depolarına kopyalarken desteklenir. *ADF yazma Kullanıcı arabirimi* – > *kopyalama etkinliği havuzu* – > *Tablo seçeneği* – > *otomatik olarak tablo oluştur* veya `tableOption` kopyalama etkinliği havuz yükünde özellik aracılığıyla) seçeneğini bulabilirsiniz.

- [Azure SQL Veritabanı](connector-azure-sql-database.md)
- [Azure SQL veritabanı yönetilen örneği](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Havuz tabloları oluşturma](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Hataya dayanıklılık

Varsayılan olarak, kopyalama etkinliği verileri kopyalamayı ve kaynak veri satırları havuz Veri satırlarıyla uyumsuz olduğunda bir hata döndürür. Kopyalamanın başarılı olması için kopyalama etkinliğini, uyumsuz satırları atlayıp günlüğe kaydetmek ve yalnızca uyumlu verileri kopyalamak üzere yapılandırabilirsiniz. Ayrıntılar için bkz. [kopyalama etkinliği hata toleransı](copy-activity-fault-tolerance.md) .

## <a name="data-consistency-verification"></a>Veri tutarlılığı doğrulama

Verileri kaynaktan hedef depoya taşıdığınızda, Azure Data Factory kopyalama etkinliği, verilerin kaynaktan yalnızca kaynak ve hedef depo arasında tutarlı bir şekilde kopyalanmadığından emin olmak için ek veri tutarlılığı doğrulaması yapmanız için bir seçenek sunar. Veri taşıma sırasında tutarsız dosyalar bulunduğunda, kopyalama etkinliğini durdurabilir veya geri kalanı kopyalamaya devam ederek hata toleransı ayarını tutarsız dosyaları atlayacak şekilde ayarlayabilirsiniz. Kopyalama etkinliğinde oturum günlüğü ayarını etkinleştirerek Atlanan dosya adlarını alabilirsiniz. Ayrıntılar için [kopyalama etkinliğinde veri tutarlılığı doğrulaması '](copy-activity-data-consistency.md) na bakın.

## <a name="session-log"></a>Oturum günlüğü
Kopyalanmış dosya adlarınızı günlüğe kaydedebilir ve bu sayede, verilerin kaynaktan hedef depoya başarıyla kopyalanmadığından ve ayrıca kopyalama etkinliği oturum günlüklerini inceleyerek kaynak ve hedef depo arasında tutarlı olmasını sağlamanıza yardımcı olabilirsiniz. Ayrıntılar için bkz. [kopyalama etkinliğinde oturum günlüğü](copy-activity-log.md) .

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki hızlı başlangıçlara, öğreticilere ve örneklere bakın:

- [Verileri bir konumdan aynı Azure Blob depolama hesabındaki başka bir konuma kopyalama](quickstart-create-data-factory-dot-net.md)
- [Azure Blob depolamadan Azure SQL veritabanı 'na veri kopyalama](tutorial-copy-data-dot-net.md)
- [SQL Server veritabanından Azure 'a veri kopyalama](tutorial-hybrid-copy-powershell.md)
