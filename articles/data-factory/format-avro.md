---
title: Azure Data Factory avro biçimi
description: Bu konu, Azure Data Factory ' de avro biçimi ile nasıl başa çıkılacağını açıklamaktadır.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: jingwang
ms.openlocfilehash: 4be499e8e304f34b1cab10aed41b5b98a5f24e9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392604"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory avro biçimi

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Avro dosyalarını ayrıştırmak veya verileri avro biçimine yazmak** istediğinizde bu makaleye uyun. 

Avro biçimi şu bağlayıcılar için desteklenir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, avro veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

| Özellik         | Açıklama                                                  | Gerekli |
| ---------------- | ------------------------------------------------------------ | -------- |
| tür             | Veri kümesinin Type özelliği **avro** olarak ayarlanmalıdır. | Yes      |
| location         | Dosya (ler) in konum ayarları. Her dosya tabanlı bağlayıcının, altında kendi konum türü ve desteklenen özellikleri vardır `location` . **Bağlayıcı makalesi-> veri kümesi özellikleri bölümünde ayrıntılara bakın**. | Yes      |
| avroCompressionCodec | Avro dosyalarına yazılırken kullanılacak sıkıştırma codec bileşeni. Avro dosyalarından okurken, Data Factory dosya meta verilerine bağlı olarak sıkıştırma codec bileşenini otomatik olarak belirler.<br>Desteklenen türler şunlardır "**none**" (varsayılan), "**söndür**", "**Snappy**". Şu anda kopyalama etkinliği, okuma/yazma avro dosyaları sırasında Snappy 'yi desteklemez. | No       |

> [!NOTE]
> Sütun adında boşluk, avro dosyaları için desteklenmez.

Aşağıda, Azure Blob depolamada bir avro veri kümesi örneği verilmiştir:

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, avro kaynak ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="avro-as-source"></a>Kaynak olarak avro

Aşağıdaki özellikler, etkinlik ***\* kaynağını \**** kopyalama bölümünde desteklenir.

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | Kopyalama etkinliği kaynağının Type özelliği **Avrosource** olarak ayarlanmalıdır. | Yes      |
| storeSettings | Veri deposundan veri okuma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının, altında kendi desteklenen okuma ayarları vardır `storeSettings` . **Bağlayıcı makalesi-> kopyalama etkinliği özellikleri bölümünde ayrıntılara bakın**. | No       |

### <a name="avro-as-sink"></a>Avro as havuzu

Aşağıdaki özellikler, etkinlik ***\* havuzunu \**** Kopyala bölümünde desteklenir.

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | Kopyalama etkinliği kaynağının Type özelliği **Avrosink** olarak ayarlanmalıdır. | Yes      |
| formatSettings          | Bir özellik grubu. Aşağıdaki **avro yazma ayarları** tablosuna bakın.| No      |
| storeSettings | Veri deposuna veri yazma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının altında kendi desteklenen yazma ayarları vardır `storeSettings` . **Bağlayıcı makalesi-> kopyalama etkinliği özellikleri bölümünde ayrıntılara bakın**. | No       |

Altında desteklenen **avro yazma ayarları** `formatSettings` :

| Özellik      | Açıklama                                                  | Gerekli                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| tür          | FormatSettings türü **Avrowritesettings** olarak ayarlanmalıdır. | Yes                                                   |
| maxRowsPerFile | Bir klasöre veri yazarken, birden fazla dosyaya yazmayı ve dosya başına en fazla satırı belirtmeyi seçebilirsiniz.  | No |
| Dosyaadıöneki | Yapılandırıldığında geçerlidir `maxRowsPerFile` .<br> Birden çok dosyaya veri yazarken dosya adı önekini belirtin, bu düzende sonuçlandı: `<fileNamePrefix>_00000.<fileExtension>` . Belirtilmemişse, dosya adı ön eki otomatik olarak oluşturulur. Kaynak dosya tabanlı depo veya [bölüm seçeneği etkinleştirilmiş veri deposu](copy-activity-performance-features.md)olduğunda bu özellik uygulanmaz.  | No |

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışlarını eşleme bölümünde şu veri depolarında avro biçimi okuyabilir ve yazabilirsiniz: [Azure Blob depolama](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md#mapping-data-flow-properties)ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda, bir avro kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Joker karakter yolları | Joker karakterle eşleşen tüm dosyalar işlenecek. Veri kümesinde ayarlanan klasör ve dosya yolunu geçersiz kılar. | hayır | String [] | Yavaya Cardyolları |
| Bölüm kök yolu | Bölümlenmiş dosya verileri için bölümlenmiş klasörleri sütun olarak okumak üzere bir bölüm kök yolu girebilirsiniz | hayır | Dize | Partitionrootyolu |
| Dosya listesi | Kaynağınızın işlenecek dosyaları listeleyen bir metin dosyasına işaret edip etmediğini belirtir | hayır | `true` veya `false` | Si |
| Dosya adının depolanacak sütun | Kaynak dosya adı ve yolu ile yeni bir sütun oluşturma | hayır | Dize | rowUrlColumn |
| Tamamlandıktan sonra | İşlemden sonra dosyaları silin veya taşıyın. Dosya yolu, kapsayıcı kökünden başlar | hayır | Sil: `true` veya `false` <br> Geçiş `['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Son değiştirme ölçütü | En son değiştirildiklerinde dosyaları filtrelemek için seçin | hayır | Timestamp | Modıfıedafter <br> modifiedBefore |
| Dosya bulunamamış izin ver | True ise bir dosya bulunmazsa bir hata oluşturulmaz | hayır | `true` veya `false` | ıgnorenofilesfound |

### <a name="sink-properties"></a>Havuz özellikleri

Aşağıdaki tabloda, bir avro havuzu tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **Ayarlar** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Klasörü temizle | Hedef klasör, yazma işleminden önce silinirse | hayır | `true` veya `false` | kesilemedi |
| Dosya adı seçeneği | Yazılan verilerin adlandırma biçimi. Varsayılan olarak, biçimdeki bölüm başına bir dosya `part-#####-tid-<guid>` | hayır | Model: dize <br> Bölüm başına: dize [] <br> Sütunda veri olarak: dize <br> Tek dosyaya çıkış: `['<fileName>']`  | filePattern <br> Partitionbir dosya adı <br> rowUrlColumn <br> Partitionbir dosya adı |
| Tümünü teklif | Tüm değerleri tırnak içine alın | hayır | `true` veya `false` | quoteAll |

## <a name="data-type-support"></a>Veri türü desteği

### <a name="copy-activity"></a>Kopyalama etkinliği
Kopyalama etkinliğinde avro [karmaşık veri türleri](https://avro.apache.org/docs/current/spec.html#schema_complex) desteklenmez (kayıtlar, numaralandırmalar, diziler, Haritalar, birleşimler ve sabit).

### <a name="data-flows"></a>Veri akışları
Veri akışlarında avro dosyalarla çalışırken, karmaşık veri türlerini okuyup yazabilir, ancak önce veri kümesinden fiziksel şemayı temizlediğinizden emin olun. Veri akışlarında, mantıksal projeksiyonunu ayarlayabilir ve karmaşık yapılar olan sütunları türetebilirsiniz, ardından bu alanları bir avro dosyasına otomatik olarak eşleyebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
