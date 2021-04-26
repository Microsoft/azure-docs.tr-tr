---
title: Azure Data Factory sınırlandırılmış metin biçimi
description: Bu konu başlığı altında, Azure Data Factory sınırlandırılmış metin biçimiyle nasıl başa çıkılabileceğinizi açıklamaktadır.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: jingwang
ms.openlocfilehash: 1cff49e4ddb7423e1e5956e1436cefd18abe6dfe
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107083"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory sınırlandırılmış metin biçimi

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Sınırlandırılmış metin dosyalarını ayrıştırmak veya verileri sınırlandırılmış metin biçimine yazmak** istediğinizde bu makaleye uyun. 

Ayrılmış metin biçimi şu bağlayıcılar için desteklenir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,,](connector-hdfs.md) [http](connector-http.md)ve [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, sınırlandırılmış metin veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

| Özellik         | Açıklama                                                  | Gerekli |
| ---------------- | ------------------------------------------------------------ | -------- |
| tür             | Veri kümesinin Type özelliği, **Delimitedtext** olarak ayarlanmalıdır. | Yes      |
| location         | Dosya (ler) in konum ayarları. Her dosya tabanlı bağlayıcının, altında kendi konum türü ve desteklenen özellikleri vardır `location` .  | Yes      |
| columnDelimiter  | Bir dosyadaki sütunları ayırmak için kullanılan karakter (ler). <br>Varsayılan değer **virgüldür `,`**. Sütun sınırlayıcısı boş dize olarak tanımlandığında, bu da sınırlayıcı olmadığında, tüm satır tek bir sütun olarak alınır.<br>Şu anda, boş dize olarak sütun sınırlayıcısı veya birden çok char yalnızca veri akışı eşlemesi için desteklenir ancak kopyalama etkinliği değildir.  | No       |
| rowDelimiter     | Tek karakter veya "\r\n" bir dosyadaki satırları ayırmak için kullanılır. <br>Varsayılan değer **: ["\r\n", "\r", "\n"]** ve **"\n" ya da "\r\n"** veri akışını eşleyerek ve kopyalama etkinliğini sırasıyla yazma sırasında aşağıdaki değerlerden herhangi biri. <br>Satır sınırlayıcısı sınırlayıcı (boş dize) olarak ayarlandığında, sütun sınırlayıcısı de sınırlayıcı (boş dize) olarak ayarlanmalıdır ve bu da tüm içeriği tek bir değer olarak değerlendirmek anlamına gelir.<br>Şu anda, boş dize olarak satır sınırlayıcısı yalnızca veri akışını eşlemek için desteklenir ancak kopyalama etkinliği değildir. | No       |
| quoteChar        | Sütun sınırlayıcısı içeriyorsa, tırnak işareti için tek karakter değeri. <br>Varsayılan değer **çift tırnak** olur `"` . <br>`quoteChar`Boş dize olarak tanımlandığında, tırnak işareti yok ve sütun değeri tırnak içine alınmaz ve `escapeChar` sütun sınırlayıcısı ve kendisini atlamak için kullanılır. | No       |
| escapeChar       | Tırnak işaretli bir değer içindeki tırnak işareti için tek karakter.<br>Varsayılan değer **ters eğik `\`** çizgidir. <br>`escapeChar`Boş dize olarak tanımlandığında, `quoteChar` boş bir dize olarak ayarlanmalıdır ve bu durumda tüm sütun değerlerinin sınırlayıcı içermediğinden emin olun. | No       |
| firstRowAsHeader | İlk satırın, sütun adlarıyla bir başlık satırı olarak değerlendirilip değerlendirilmeyeceğini belirtir.<br>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir.<br>Üst bilgi olarak ilk satır yanlış olduğunda, Kullanıcı arabirimi veri önizleme ve arama etkinliği çıkışı otomatik olarak sütun adları oluştur Prop_ {n} (0 ' dan başlayarak), kopyalama etkinliği kaynaktan havuza [Açık eşleme](copy-activity-schema-and-type-mapping.md#explicit-mapping) gerektirir ve sütunları sıraya göre konumlandırır (Column_ 1 ' den başlayarak)  | No       |
| nullValue        | Null değerin dize gösterimini belirtir. <br>Varsayılan değer boş bir **dizedir**. | No       |
| encodingName     | Test dosyalarını okumak/yazmak için kullanılan kodlama türü. <br>İzin verilen değerler şunlardır: "UTF-8", "UTF-16", "UTF-16TO", "UTF-32", "UTF-32TO", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JıS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "" IBM861 "," IBM863 "," IBM864 "," IBM865 "," IBM869 "," IBM870 "," IBM01140 "," IBM01141 "," IBM01142 "," IBM01143 "," IBM01144 "," IBM01145 "," IBM01146 "," IBM01147 "," IBM01148 "," IBM01149 "," ISO-2022-JP "," ISO-2022-KR "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 " , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".<br>Veri akışı eşleme, UTF-7 kodlamasını desteklemez. | No       |
| compressionCodec | Metin dosyalarını okumak/yazmak için kullanılan sıkıştırma codec bileşeni. <br>İzin verilen değerler **şunlardır bzip2**, **gzip**, **söndür**, **zipsöndür**, **targzip**, **tar**, **Snappy** veya **lz4**. Varsayılan değer sıkıştırılmaz. <br>**Şu anda** kopyalama etkinliği "Snappy" & "lz4" desteklemez ve eşleme veri akışı "zipsöndür", "targzip" ve "tar" değerlerini desteklemez. <br>Örneğin, dosya tabanlı havuz veri deposuna **açılan** dosyaları açmak için kopyalama **etkinliği kullanılırken,** /  /  varsayılan dosyalar klasörüne çıkarılır: `<path specified in dataset>/<folder named as source compressed file>/` , `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` sıkıştırılmış dosyaların adının klasör yapısı olarak korunup korunmayacağını denetlemek için [etkinlik kaynağını kopyalama](#delimited-text-as-source) üzerinde kullanın. | No       |
| compressionLevel | Sıkıştırma oranı. <br>İzin verilen değerler **en iyi** veya **en hızlardır**.<br>- **En hızlı:** Elde edilen dosya en iyi şekilde sıkıştırılmasa bile, sıkıştırma işleminin mümkün olduğunca hızlı bir şekilde tamamlanmalıdır.<br>- **En iyi**: işlemin tamamlanmasını daha uzun sürse bile sıkıştırma işlemi en iyi şekilde sıkıştırılmalıdır. Daha fazla bilgi için bkz. [sıkıştırma düzeyi](/dotnet/api/system.io.compression.compressionlevel) konusu. | No       |

Azure Blob depolamada sınırlandırılmış metin veri kümesi örneği aşağıda verilmiştir:

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "escapeChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, ayrılmış metin kaynağı ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="delimited-text-as-source"></a>Kaynak olarak ayrılmış metin 

Aşağıdaki özellikler, etkinlik ***\* kaynağını \**** kopyalama bölümünde desteklenir.

| Özellik       | Açıklama                                                  | Gerekli |
| -------------- | ------------------------------------------------------------ | -------- |
| tür           | Kopyalama etkinliği kaynağının Type özelliği, **Delimitedtextsource** olarak ayarlanmalıdır. | Yes      |
| formatSettings | Bir özellik grubu. Aşağıdaki **ayrılmış metin okuma ayarları** tablosuna bakın. |  No       |
| storeSettings  | Veri deposundan veri okuma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının, altında kendi desteklenen okuma ayarları vardır `storeSettings` . | No       |

Desteklenen **sınırlandırılmış metin okuma ayarları** `formatSettings` :

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | FormatSettings türü, **Delimitedtextreadsettings** olarak ayarlanmalıdır. | Yes      |
| skipLineCount | Giriş dosyalarından veri okurken atlanacak **boş olmayan** satır sayısını belirtir. <br>Hem skipLineCount hem de firstRowAsHeader parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur. | No       |
| compressionProperties | Belirli bir sıkıştırma codec bileşeni için verileri açmak üzere bir özellik grubu. | No       |
| preserveZipFileNameAsFolder<br>(*`compressionProperties` -> `type` as `ZipDeflateReadSettings`*) |  Giriş veri kümesi **Zipsöndür** sıkıştırma ile yapılandırıldığında geçerlidir. Kaynak ZIP dosya adının kopyalama sırasında klasör yapısı olarak korunup korunmayacağını gösterir.<br>- **True (varsayılan)** olarak ayarlandığında Data Factory daraltılmış dosyaları içine yazar `<path specified in dataset>/<folder named as source zip file>/` .<br>- **False** olarak ayarlandığında Data Factory ZIP dosyalarını doğrudan öğesine yazar `<path specified in dataset>` . Yarış veya beklenmedik davranışlara engel olmak için farklı kaynak ZIP dosyalarında yinelenen dosya adlarında bulunmadığından emin olun.  | No |
| preserveCompressionFileNameAsFolder<br>(*`compressionProperties` -> `type` `TarGZipReadSettings` veya `TarReadSettings` olarak*)  | Giriş veri kümesi **targık** sıkıştırması ile yapılandırıldığında geçerlidir /  . Kaynak sıkıştırılmış dosya adının kopyalama sırasında klasör yapısı olarak korunup korunmayacağını gösterir.<br>- **True (varsayılan)** olarak ayarlandığında Data Factory, açılan dosyaları içine yazar `<path specified in dataset>/<folder named as source compressed file>/` . <br>- **False** olarak ayarlandığında Data Factory açılan dosyaları doğrudan öğesine yazar `<path specified in dataset>` . Yarış veya beklenmedik davranışlara engel olmak için farklı kaynak dosyalarında yinelenen dosya adlarında bulunmadığından emin olun. | No |

```json
"activities": [
    {
        "name": "CopyFromDelimitedText",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                },
                "formatSettings": {
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 3,
                    "compressionProperties": {
                        "type": "ZipDeflateReadSettings",
                        "preserveZipFileNameAsFolder": false
                    }
                }
            },
            ...
        }
        ...
    }
]
```

### <a name="delimited-text-as-sink"></a>Havuz olarak ayrılmış metin

Aşağıdaki özellikler, etkinlik ***\* havuzunu \**** Kopyala bölümünde desteklenir.

| Özellik       | Açıklama                                                  | Gerekli |
| -------------- | ------------------------------------------------------------ | -------- |
| tür           | Kopyalama etkinliği kaynağının Type özelliği, **Delimitedtextsink** olarak ayarlanmalıdır. | Yes      |
| formatSettings | Bir özellik grubu. Aşağıdaki **ayrılmış metin yazma ayarları** tablosuna bakın. |    No      |
| storeSettings  | Veri deposuna veri yazma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının altında kendi desteklenen yazma ayarları vardır `storeSettings` .  | No       |

Altında desteklenen **sınırlandırılmış metin yazma ayarları** `formatSettings` :

| Özellik      | Açıklama                                                  | Gerekli                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tür          | FormatSettings türü **DelimitedTextWriteSettings** olarak ayarlanmalıdır. | Yes                                                   |
| fileExtension | Çıktı dosyalarını adlandırmak için kullanılan dosya uzantısı, örneğin, `.csv` `.txt` . `fileName`Output DelimitedText veri kümesinde belirtilmediğinde belirtilmelidir. Çıktı veri kümesinde dosya adı yapılandırıldığında, havuz dosya adı olarak kullanılır ve dosya uzantısı ayarı yok sayılır.  | Çıkış veri kümesinde dosya adı belirtilmediğinde Evet |
| maxRowsPerFile | Bir klasöre veri yazarken, birden fazla dosyaya yazmayı ve dosya başına en fazla satırı belirtmeyi seçebilirsiniz.  | No |
| Dosyaadıöneki | Yapılandırıldığında geçerlidir `maxRowsPerFile` .<br> Birden çok dosyaya veri yazarken dosya adı önekini belirtin, bu düzende sonuçlandı: `<fileNamePrefix>_00000.<fileExtension>` . Belirtilmemişse, dosya adı ön eki otomatik olarak oluşturulur. Kaynak dosya tabanlı depo veya [bölüm seçeneği etkinleştirilmiş veri deposu](copy-activity-performance-features.md)olduğunda bu özellik uygulanmaz.  | No |

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışlarını eşleme bölümünde, şu veri depolarında sınırlandırılmış metin biçimini okuyabilir ve yazabilirsiniz: [Azure Blob depolama](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md#mapping-data-flow-properties)ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda, ayrılmış bir metin kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Joker karakter yolları | Joker karakterle eşleşen tüm dosyalar işlenecek. Veri kümesinde ayarlanan klasör ve dosya yolunu geçersiz kılar. | hayır | String [] | Yavaya Cardyolları |
| Bölüm kök yolu | Bölümlenmiş dosya verileri için bölümlenmiş klasörleri sütun olarak okumak üzere bir bölüm kök yolu girebilirsiniz | hayır | Dize | Partitionrootyolu |
| Dosya listesi | Kaynağınızın işlenecek dosyaları listeleyen bir metin dosyasına işaret edip etmediğini belirtir | hayır | `true` veya `false` | Si |
| Çok satırlı satırlar | Kaynak dosya birden çok satıra yayılan satırlar içeriyor. Çok satırlı değerler tırnak içinde olmalıdır. | Hayır `true` veya `false` | multiLineRow |
| Dosya adının depolanacak sütun | Kaynak dosya adı ve yolu ile yeni bir sütun oluşturma | hayır | Dize | rowUrlColumn |
| Tamamlandıktan sonra | İşlemden sonra dosyaları silin veya taşıyın. Dosya yolu, kapsayıcı kökünden başlar | hayır | Sil: `true` veya `false` <br> Geçiş `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Son değiştirme ölçütü | En son değiştirildiklerinde dosyaları filtrelemek için seçin | hayır | Timestamp | Modıfıedafter <br> modifiedBefore |
| Dosya bulunamamış izin ver | True ise bir dosya bulunmazsa bir hata oluşturulmaz | hayır | `true` veya `false` | ıgnorenofilesfound |

### <a name="source-example"></a>Kaynak örneği

Aşağıdaki görüntü, veri akışlara eşlenen bir ayrılmış metin kaynağı yapılandırmasına örnektir.

![Delimitedmetin kaynağı](media/data-flow/delimited-text-source.png)

İlişkili veri akışı betiği:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    multiLineRow: true,
    wildcardPaths:['*.csv']) ~> CSVSource
```

> [!NOTE]
> Veri akışı kaynakları, Hadoop dosya sistemleri tarafından desteklenen sınırlı bir Linux glob kümesini destekler

### <a name="sink-properties"></a>Havuz özellikleri

Aşağıdaki tabloda, ayrılmış bir metin havuzunun desteklediği özellikler listelenmiştir. Bu özellikleri **Ayarlar** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Klasörü temizle | Hedef klasör, yazma işleminden önce silinirse | hayır | `true` veya `false` | kesilemedi |
| Dosya adı seçeneği | Yazılan verilerin adlandırma biçimi. Varsayılan olarak, biçimdeki bölüm başına bir dosya `part-#####-tid-<guid>` | hayır | Model: dize <br> Bölüm başına: dize [] <br> Dosyayı sütun verileri olarak adlandır: dize <br> Tek dosyaya çıkış: `['<fileName>']` <br> Klasörü sütun verileri olarak adlandır: dize | filePattern <br> Partitionbir dosya adı <br> rowUrlColumn <br> Partitionbir dosya adı <br> rowFolderUrlColumn |
| Tümünü teklif | Tüm değerleri tırnak içine alın | hayır | `true` veya `false` | quoteAll |
| Üst bilgi | Çıkış dosyalarına müşteri üstbilgileri ekleme | hayır | `[<string array>]` | üst bilgi |

### <a name="sink-example"></a>Havuz örneği

Aşağıdaki görüntü, veri akışları eşleme içindeki ayrılmış metin havuzu yapılandırmasına bir örnektir.

![Delimitedmetin havuzu](media/data-flow/delimited-text-sink.png)

İlişkili veri akışı betiği:

```
CSVSource sink(allowSchemaDrift: true,
    validateSchema: false,
    truncate: true,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> CSVSink
```

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
