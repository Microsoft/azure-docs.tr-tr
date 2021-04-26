---
title: Azure Data Factory için ORC biçimi
description: Bu konu, Azure Data Factory ' de ORC biçimi ile nasıl başa çıkılacağını açıklamaktadır.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: jingwang
ms.openlocfilehash: 8973692b90cc9d6caa852616bf2962371d25abfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386467"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure Data Factory için ORC biçimi

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Orc dosyalarını ayrıştırmak veya VERILERI orc biçimine yazmak** istediğinizde bu makaleye uyun. 

ORC biçimi şu bağlayıcılar için desteklenir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,,](connector-hdfs.md) [http](connector-http.md)ve [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm ORC veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

| Özellik         | Açıklama                                                  | Gerekli |
| ---------------- | ------------------------------------------------------------ | -------- |
| tür             | Veri kümesinin Type özelliği **orc** olarak ayarlanmalıdır. | Yes      |
| location         | Dosya (ler) in konum ayarları. Her dosya tabanlı bağlayıcının, altında kendi konum türü ve desteklenen özellikleri vardır `location` . **Bağlayıcı makalesi-> veri kümesi özellikleri bölümünde ayrıntılara bakın**. | Yes      |
| compressionCodec         | ORC dosyalarına yazarken kullanılacak sıkıştırma codec bileşeni. ORC dosyalarından okurken, veri fabrikaları dosya meta verilerini temel alarak sıkıştırma codec 'ini otomatik olarak belirlenir.<br>Desteklenen türler None, **zlib**, **Snappy** (varsayılan) ve **LZO** **'tur**. Not Şu anda Copy etkinliği Read/Write ORC dosyaları sırasında LZO 'yi desteklemez. | No      |

Azure Blob depolamada ORC veri kümesinin bir örneği aşağıda verilmiştir:

```json
{
    "name": "OrcDataset",
    "properties": {
        "type": "Orc",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            }
        }
    }
}
```

Aşağıdaki noktalara dikkat edin:

* Karmaşık veri türleri (örn. MAP, LIST, STRUCT) Şu anda yalnızca veri akışlarında desteklenir, kopyalama etkinliğinde desteklenmez. Veri akışlarında karmaşık türleri kullanmak için, şema veri kümesinde boş bırakarak dosya şemasını veri kümesinde içeri aktarmayın. Ardından, kaynak dönüşümünde projeksiyonu içeri aktarın.
* Sütun adında boşluk desteklenmiyor.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, ORC kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="orc-as-source"></a>ORC as kaynağı

Aşağıdaki özellikler, etkinlik ***\* kaynağını \**** kopyalama bölümünde desteklenir.

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | Kopyalama etkinliği kaynağının Type özelliği **Orcsource** olarak ayarlanmalıdır. | Yes      |
| storeSettings | Veri deposundan veri okuma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının, altında kendi desteklenen okuma ayarları vardır `storeSettings` . **Bağlayıcı makalesi-> kopyalama etkinliği özellikleri bölümünde ayrıntılara bakın**. | No       |

### <a name="orc-as-sink"></a>Havuz olarak ORC

Aşağıdaki özellikler, etkinlik ***\* havuzunu \**** Kopyala bölümünde desteklenir.

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | Kopyalama etkinliği havuzunun Type özelliği **Orcsink** olarak ayarlanmalıdır. | Yes      |
| formatSettings | Bir özellik grubu. Aşağıdaki **orc yazma ayarları** tablosuna bakın. |    No      |
| storeSettings | Veri deposuna veri yazma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının altında kendi desteklenen yazma ayarları vardır `storeSettings` . **Bağlayıcı makalesi-> kopyalama etkinliği özellikleri bölümünde ayrıntılara bakın**. | No       |

Altında desteklenen **orc yazma ayarları** `formatSettings` :

| Özellik      | Açıklama                                                  | Gerekli                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tür          | FormatSettings türü **Orcwritesettings** olarak ayarlanmalıdır. | Yes                                                   |
| maxRowsPerFile | Bir klasöre veri yazarken, birden fazla dosyaya yazmayı ve dosya başına en fazla satırı belirtmeyi seçebilirsiniz.  | No |
| Dosyaadıöneki | Yapılandırıldığında geçerlidir `maxRowsPerFile` .<br> Birden çok dosyaya veri yazarken dosya adı önekini belirtin, bu düzende sonuçlandı: `<fileNamePrefix>_00000.<fileExtension>` . Belirtilmemişse, dosya adı ön eki otomatik olarak oluşturulur. Kaynak dosya tabanlı depo veya [bölüm seçeneği etkinleştirilmiş veri deposu](copy-activity-performance-features.md)olduğunda bu özellik uygulanmaz.  | No |

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışlarını eşleme bölümünde, aşağıdaki veri depolarında ORC biçimini okuyabilir ve yazabilirsiniz: [Azure Blob depolama](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md#mapping-data-flow-properties)ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Orc veri kümesini veya [satır içi veri kümesini](data-flow-source.md#inline-datasets)kullanarak orc dosyalarını işaret edebilirsiniz.

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda bir ORC kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

Satır içi veri kümesi kullanırken, [veri kümesi özellikleri](#dataset-properties) bölümünde açıklanan özelliklerle aynı olan ek dosya ayarlarını görürsünüz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Biçimlendir | Biçim olmalıdır `orc` | evet | `orc` | biçim |
| Joker karakter yolları | Joker karakterle eşleşen tüm dosyalar işlenecek. Veri kümesinde ayarlanan klasör ve dosya yolunu geçersiz kılar. | hayır | String [] | Yavaya Cardyolları |
| Bölüm kök yolu | Bölümlenmiş dosya verileri için bölümlenmiş klasörleri sütun olarak okumak üzere bir bölüm kök yolu girebilirsiniz | hayır | Dize | Partitionrootyolu |
| Dosya listesi | Kaynağınızın işlenecek dosyaları listeleyen bir metin dosyasına işaret edip etmediğini belirtir | hayır | `true` veya `false` | Si |
| Dosya adının depolanacak sütun | Kaynak dosya adı ve yolu ile yeni bir sütun oluşturma | hayır | Dize | rowUrlColumn |
| Tamamlandıktan sonra | İşlemden sonra dosyaları silin veya taşıyın. Dosya yolu, kapsayıcı kökünden başlar | hayır | Sil: `true` veya `false` <br> Geçiş `[<from>, <to>]` | purgeFiles <br> moveFiles |
| Son değiştirme ölçütü | En son değiştirildiklerinde dosyaları filtrelemek için seçin | hayır | Timestamp | Modıfıedafter <br> modifiedBefore |
| Dosya bulunamamış izin ver | True ise bir dosya bulunmazsa bir hata oluşturulmaz | hayır | `true` veya `false` | ıgnorenofilesfound |

### <a name="source-example"></a>Kaynak örneği

Bir ORC kaynak yapılandırmasının ilişkili veri akışı betiği şunlardır:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    rowUrlColumn: 'fileName',
    format: 'orc') ~> OrcSource
```

### <a name="sink-properties"></a>Havuz özellikleri

Aşağıdaki tabloda bir ORC havuzu tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **Ayarlar** sekmesinde düzenleyebilirsiniz.

Satır içi veri kümesi kullanırken, [veri kümesi özellikleri](#dataset-properties) bölümünde açıklanan özelliklerle aynı olan ek dosya ayarlarını görürsünüz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Biçimlendir | Biçim olmalıdır `orc` | evet | `orc` | biçim |
| Klasörü temizle | Hedef klasör, yazma işleminden önce silinirse | hayır | `true` veya `false` | kesilemedi |
| Dosya adı seçeneği | Yazılan verilerin adlandırma biçimi. Varsayılan olarak, biçimdeki bölüm başına bir dosya `part-#####-tid-<guid>` | hayır | Model: dize <br> Bölüm başına: dize [] <br> Sütunda veri olarak: dize <br> Tek dosyaya çıkış: `['<fileName>']` | filePattern <br> Partitionbir dosya adı <br> rowUrlColumn <br> Partitionbir dosya adı |

### <a name="sink-example"></a>Havuz örneği

Bir ORC havuzu yapılandırmasının ilişkili veri akışı betiği:

```
OrcSource sink(
    format: 'orc',
    filePattern:'output[n].orc',
    truncate: true,
    allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> OrcSink
```

## <a name="using-self-hosted-integration-runtime"></a>Şirket içinde barındırılan Integration Runtime kullanma

> [!IMPORTANT]
> Şirket içinde barındırılan Integration Runtime, örneğin şirket içi ve bulut veri depoları arasında geçiş için, ORC dosyalarını **olduğu gibi** KOPYALAMADıYSANıZ, ır makinenizde **64 bit JRE 8 (Java Runtime Environment) veya OpenJDK** ve **Microsoft Visual C++ 2010 yeniden dağıtılabilir paketini** yüklemeniz gerekir. Daha ayrıntılı bilgi edinmek için aşağıdaki paragrafı inceleyin.

ORC dosya serileştirme/seri hale getirme ile şirket içinde barındırılan IR üzerinde çalışan kopyalama için, ADF, Visual x + s için kayıt defterini denetleyerek, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* OpenJDK için sistem değişkenini belirterek, Java çalışma zamanını bulur *`JAVA_HOME`* .

- **JRE 'yi kullanmak için**: 64 bit ır, 64 BIT JRE gerektirir. [Buradan](https://go.microsoft.com/fwlink/?LinkId=808605)bulabilirsiniz.
- **OpenJDK 'yi kullanmak için**: ır sürüm 3,13 ' den itibaren desteklenmektedir. OpenJDK 'nin diğer tüm gerekli Derlemeleriyle jvm.dll, şirket içinde barındırılan IR makinesine paketleyin ve sistem ortam değişkeni JAVA_HOME uygun şekilde ayarlayın.
- **Visual C++ 2010 yeniden dağıtılabilir paketini yüklemek için**: Visual C++ 2010 yeniden dağıtılabilir paketi, şirket IÇINDE barındırılan IR yüklemeleri ile birlikte yüklenmez. [Buradan](https://www.microsoft.com/download/details.aspx?id=14632)bulabilirsiniz.

> [!TIP]
> Şirket içinde barındırılan Integration Runtime kullanarak ORC formatına/biçiminden veri kopyalarsanız ve "Java çağrılırken bir hata oluştu" hatası ile karşılaşırsanız, ileti: **Java. lang. OutOfMemoryError: Java yığın alanı**", `_JAVA_OPTIONS` Bu kopyayı güçlendiren JVM için Min/Max yığın boyutunu ayarlamak üzere ŞIRKET içinde barındırılan IR barındıran makineye bir ortam değişkeni ekleyebilirsiniz, sonra işlem hattını yeniden çalıştırın.

![Şirket içinde barındırılan IR 'de JVM yığın boyutunu ayarlama](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Örnek: değişkeni `_JAVA_OPTIONS` değeri ile ayarlayın `-Xms256m -Xmx16g` . Bayrak, `Xms` bir Java sanal makinesi (JVM) için ilk bellek ayırma havuzunu belirtir, ancak `Xmx` en fazla bellek ayırma havuzunu belirtir. Bu, JVM 'nin bellek miktarı ile başlatıldığı `Xms` ve en fazla bellek miktarını kullanabileceği anlamına gelir `Xmx` . ADF, varsayılan olarak en az 64 MB ve en fazla 1G kullanır.

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
