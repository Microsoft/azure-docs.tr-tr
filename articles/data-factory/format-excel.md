---
title: Azure Data Factory Excel biçimi
description: Bu konu başlığı altında, Excel biçimi Azure Data Factory nasıl ele alınacağını açıklamaktadır.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: jingwang
ms.openlocfilehash: bef29bc958253be0498442f842dda67105ce799b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386535"
---
# <a name="excel-format-in-azure-data-factory"></a>Azure Data Factory Excel biçimi
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Excel dosyalarını ayrıştırmak** istediğinizde bu makaleye uyun. Azure Data Factory hem ". xls" hem de ". xlsx" destekler.

Excel biçimi şu bağlayıcılar için desteklenir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md). Kaynak olarak desteklenir, ancak havuz değildir. 

**Not**: ". xls" biçimi [http](connector-http.md)kullanılırken desteklenmez. 

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Excel veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

| Özellik         | Açıklama                                                  | Gerekli |
| ---------------- | ------------------------------------------------------------ | -------- |
| tür             | Veri kümesinin Type özelliği **Excel** olarak ayarlanmalıdır.   | Yes      |
| location         | Dosya (ler) in konum ayarları. Her dosya tabanlı bağlayıcının, altında kendi konum türü ve desteklenen özellikleri vardır `location` . | Yes      |
| sheetname ayar çağırın        | Verilerin okunacağı Excel çalışma sayfası adı.                       | `sheetName`Veya belirtin`sheetIndex` |
| Sheetındex | 0 ' dan başlayarak, verileri okumak için Excel çalışma sayfası dizini. | `sheetName`Veya belirtin`sheetIndex` |
| aralık            | Seçmeli verileri bulmak için verilen çalışma sayfasındaki hücre aralığı, ör.:<br>-Belirtilmemiş: tüm çalışma sayfasını boş olmayan ilk satır ve sütundan tablo olarak okur<br>- `A3`: verilen hücreden başlayarak bir tabloyu okur, aşağıdaki tüm satırları ve tüm sütunları sağa dinamik olarak algılar<br>- `A3:H5`: Bu sabit aralığı tablo olarak okur<br>- `A3:A3`: Bu tek hücreyi okur | No       |
| firstRowAsHeader | Belirtilen çalışma sayfasındaki/aralıktaki ilk satırın, sütun adlarıyla üst bilgi satırı olarak değerlendirilip değerlendirilmeyeceğini belirtir.<br>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir. | No       |
| nullValue        | Null değerin dize gösterimini belirtir. <br>Varsayılan değer boş bir **dizedir**. | No       |
| sıkıştırma | Dosya sıkıştırmayı yapılandırmak için özellik grubu. Etkinlik yürütmesi sırasında sıkıştırma/açma işlemi yapmak istediğinizde bu bölümü yapılandırın. | No |
| tür<br/>(*altında `compression`*) | JSON dosyalarını okumak/yazmak için kullanılan sıkıştırma codec bileşeni. <br>İzin verilen değerler **şunlardır bzip2**, **gzip**, **söndür**, **zipsöndür**, **targzip**, **tar**, **Snappy** veya **lz4**. Varsayılan değer sıkıştırılmaz.<br>**Şu anda** kopyalama etkinliği "Snappy" & "lz4" desteklemez ve eşleme veri akışı "zipsöndür", "targzip" ve "tar" değerlerini desteklemez.<br>Bu **dosyaları açmak ve dosya tabanlı** havuz veri deposuna yazmak için kopyalama **etkinliğini kullanırken dosyalar** klasörüne ayıklanır: `<path specified in dataset>/<folder named as source zip file>/` . | Hayır.  |
| düzey<br/>(*altında `compression`*) | Sıkıştırma oranı. <br>İzin verilen değerler **en iyi** veya **en hızlardır**.<br>- **En hızlı:** Elde edilen dosya en iyi şekilde sıkıştırılmasa bile, sıkıştırma işleminin mümkün olduğunca hızlı bir şekilde tamamlanmalıdır.<br>- **En iyi**: işlemin tamamlanmasını daha uzun sürse bile sıkıştırma işlemi en iyi şekilde sıkıştırılmalıdır. Daha fazla bilgi için bkz. [sıkıştırma düzeyi](/dotnet/api/system.io.compression.compressionlevel) konusu. | No       |

Azure Blob depolamada Excel veri kümesinin bir örneği aşağıda verilmiştir:

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Excel kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="excel-as-source"></a>Kaynak olarak Excel 

Aşağıdaki özellikler, etkinlik ***\* kaynağını \**** kopyalama bölümünde desteklenir.

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | Kopyalama etkinliği kaynağının Type özelliği **ExcelSource** olarak ayarlanmalıdır. | Yes      |
| storeSettings | Veri deposundan veri okuma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının, altında kendi desteklenen okuma ayarları vardır `storeSettings` . | No       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışlarını eşleme bölümünde, Excel biçimini aşağıdaki veri depolarında okuyabilirsiniz: [Azure Blob depolama](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md#mapping-data-flow-properties)ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#mapping-data-flow-properties). Excel veri kümesi veya [satır içi veri kümesi](data-flow-source.md#inline-datasets)kullanarak Excel dosyalarını işaret edebilirsiniz.

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda bir Excel kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz. Satır içi veri kümesi kullanırken, [veri kümesi özellikleri](#dataset-properties) bölümünde açıklanan özelliklerle aynı olan ek dosya ayarlarını görürsünüz.

| Ad                      | Açıklama                                                  | Gerekli | İzin verilen değerler                                            | Veri akışı betiği özelliği         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| Joker karakter yolları           | Joker karakterle eşleşen tüm dosyalar işlenecek. Veri kümesinde ayarlanan klasör ve dosya yolunu geçersiz kılar. | hayır       | String []                                                  | Yavaya Cardyolları                     |
| Bölüm kök yolu       | Bölümlenmiş dosya verileri için bölümlenmiş klasörleri sütun olarak okumak üzere bir bölüm kök yolu girebilirsiniz | hayır       | Dize                                                    | Partitionrootyolu                 |
| Dosya listesi             | Kaynağınızın işlenecek dosyaları listeleyen bir metin dosyasına işaret edip etmediğini belirtir | hayır       | `true` veya `false`                                         | Si                          |
| Dosya adının depolanacak sütun | Kaynak dosya adı ve yolu ile yeni bir sütun oluşturma       | hayır       | Dize                                                    | rowUrlColumn                      |
| Tamamlandıktan sonra          | İşlemden sonra dosyaları silin veya taşıyın. Dosya yolu, kapsayıcı kökünden başlar | hayır       | Sil: `true` veya `false` <br> Geçiş `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| Son değiştirme ölçütü   | En son değiştirildiklerinde dosyaları filtrelemek için seçin | hayır       | Timestamp                                                 | Modıfıedafter <br> modifiedBefore |
| Dosya bulunamamış izin ver | True ise bir dosya bulunmazsa bir hata oluşturulmaz | hayır | `true` veya `false` | ıgnorenofilesfound |

### <a name="source-example"></a>Kaynak örneği

Aşağıdaki görüntü, veri akışı modu kullanılarak veri akışlarını eşlerken Excel kaynak yapılandırmasına bir örnektir.

![Excel kaynağı](media/data-flow/excel-source.png)

İlişkili veri akışı betiği:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

Satır içi veri kümesi kullanıyorsanız, eşleme veri akışı 'nda aşağıdaki kaynak seçeneklerini görürsünüz.

![Excel kaynak satır içi veri kümesi](media/data-flow/excel-source-inline-dataset.png)

İlişkili veri akışı betiği:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)