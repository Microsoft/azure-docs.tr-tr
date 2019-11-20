---
title: Azure Data Factory sınırlandırılmış metin biçimi
description: Bu konu başlığı altında, Azure Data Factory sınırlandırılmış metin biçimiyle nasıl başa çıkılabileceğinizi açıklamaktadır.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: b710492b3416371d8d554945a60160261e5a8c6a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73674833"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory sınırlandırılmış metin biçimi

**Sınırlandırılmış metin dosyalarını ayrıştırmak veya verileri sınırlandırılmış metin biçimine yazmak**istediğinizde bu makaleye uyun. 

Ayrılmış metin biçimi şu bağlayıcılar için desteklenir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [ Google Cloud Storage](connector-google-cloud-storage.md), [,](connector-hdfs.md) [http](connector-http.md)ve [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, sınırlandırılmış metin veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

| Özellik         | Açıklama                                                  | Gerekli |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | Veri kümesinin Type özelliği, **Delimitedtext**olarak ayarlanmalıdır. | Evet      |
| location         | Dosya (ler) in konum ayarları. Her dosya tabanlı bağlayıcının kendi konum türü ve `location`altında desteklenen özellikleri vardır.  | Evet      |
| columnDelimiter  | Bir dosyadaki sütunları ayırmak için kullanılan karakter (ler). Şu anda, çok char sınırlayıcısı yalnızca veri akışı eşlemesi için desteklenir ancak kopyalama etkinliği değildir. <br>Varsayılan değer **virgüldür `,`** , sütun sınırlayıcısı boş bir dize olarak tanımlandığında, tüm satır tek bir sütun olarak alınır. | Hayır       |
| rowDelimiter     | Tek karakter veya "\r\n" bir dosyadaki satırları ayırmak için kullanılır.<br>Varsayılan değer **: ["\r\n", "\r", "\n"]** ve **"\n" ya da "\r\n"** veri akışını eşleyerek ve kopyalama etkinliğini sırasıyla yazma sırasında aşağıdaki değerlerden herhangi biri. <br>`rowDelimiter` sınırlayıcı (boş dize) olarak ayarlandığında, `columnDelimiter` sınırlayıcı (boş dize) olarak ayarlanmalıdır ve bu da tüm içeriği tek bir değer olarak değerlendirmek anlamına gelir. | Hayır       |
| quoteChar        | Sütun sınırlayıcısı içeriyorsa, tırnak işareti için tek karakter değeri. <br>Varsayılan değer `"`**çift tırnak** olur. <br>Veri akışını eşlemek için `quoteChar` boş bir dize olamaz. <br>Kopyalama etkinliği için `quoteChar` boş bir dize olarak tanımlandığında, tırnak işareti yok ve sütun değeri tırnak içine alınmaz ve sütun sınırlayıcısı ve kendisini atlamak için `escapeChar` kullanılır. | Hayır       |
| escapeChar       | Tırnak işaretli bir değer içindeki tırnak işareti için tek karakter.<br>Varsayılan değer **ters eğik çizgi `\`** . <br>Veri akışını eşlemek için `escapeChar` boş bir dize olamaz. <br/>Kopyalama etkinliği için `escapeChar` boş bir dize olarak tanımlandığında, `quoteChar` boş bir dize olarak ayarlanması ve bu durumda tüm sütun değerlerinin sınırlayıcı içermediğinden emin olması gerekir. | Hayır       |
| firstRowAsHeader | İlk satırın, sütun adlarıyla bir başlık satırı olarak değerlendirilip değerlendirilmeyeceğini belirtir.<br>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir. | Hayır       |
| nullValue        | Null değerin dize gösterimini belirtir. <br>Varsayılan değer boş bir **dizedir**. | Hayır       |
| encodingName     | Test dosyalarını okumak/yazmak için kullanılan kodlama türü. <br>İzin verilen değerler şunlardır: "UTF-8", "UTF-16", "UTF-16IN", "UTF-32", "UTF-32TO", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JıS", "CP875", "CP866", "IBM00858", "IBM273", "IBM437", "IBM500", "", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ıSO-2022-KR", "ıSO-8859-1", "ıSO-8859-2", "ıSO-8859-3", "ıSO-8859-4", "ıSO-8859-5", "ıSO-8859-6", "ıSO-8859-7", "ıSO-8859-8", "ıSO-8859-9", "ıSO-8859-13", "ıSO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 ".<br>Veri akışı eşleme, UTF-7 kodlamasını desteklemez. | Hayır       |
| compressionCodec | Metin dosyalarını okumak/yazmak için kullanılan sıkıştırma codec bileşeni. <br>İzin verilen değerler şunlardır **bzip2**, **gzip**, **söndür**, **zipsöndür**, **Snappy**veya **lz4**. dosyasını kaydederken kullanmak için. <br>Şu anda kopyalama etkinliği "Snappy" & "lz4" desteklemez ve eşleme veri akışı "Zipsöndür" seçeneğini desteklemez. | Hayır       |
| compressionLevel | Sıkıştırma oranı. <br>İzin verilen değerler **en iyi** veya **en hızlardır**.<br>**en hızlı - :** elde edilen dosya en iyi şekilde sıkıştırılmasa bile sıkıştırma işleminin mümkün olduğunca çabuk olması gerekir.<br>**en iyi**- : işlemin tamamlanmasını daha uzun sürse bile sıkıştırma işlemi en iyi şekilde sıkıştırılmalıdır. Daha fazla bilgi için bkz. [sıkıştırma düzeyi](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) konusu. | Hayır       |

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
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, ayrılmış metin kaynağı ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="delimited-text-as-source"></a>Kaynak olarak ayrılmış metin 

Aşağıdaki özellikler, kopyalama etkinliği ***\*kaynak\**** bölümünde desteklenir.

| Özellik       | Açıklama                                                  | Gerekli |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Kopyalama etkinliği kaynağının Type özelliği, **Delimitedtextsource**olarak ayarlanmalıdır. | Evet      |
| formatSettings | Bir özellik grubu. Aşağıdaki **ayrılmış metin okuma ayarları** tablosuna bakın. | Hayır       |
| storeSettings  | Veri deposundan veri okuma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının, `storeSettings`altında kendi desteklenen okuma ayarları vardır. | Hayır       |

`formatSettings`altında desteklenen **sınırlandırılmış metin okuma ayarları** :

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings türü, **Delimitedtextreadsetting**olarak ayarlanmalıdır. | Evet      |
| skipLineCount | Giriş dosyalarından veri okurken atlanacak **boş olmayan** satır sayısını belirtir. <br>Hem skipLineCount hem de firstRowAsHeader parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur. | Hayır       |

### <a name="delimited-text-as-sink"></a>Havuz olarak ayrılmış metin

Aşağıdaki özellikler, kopyalama etkinliği ***\*havuzu\**** bölümünde desteklenir.

| Özellik       | Açıklama                                                  | Gerekli |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | Kopyalama etkinliği kaynağının Type özelliği, **Delimitedtextsink**olarak ayarlanmalıdır. | Evet      |
| formatSettings | Bir özellik grubu. Aşağıdaki **ayrılmış metin yazma ayarları** tablosuna bakın. |          |
| storeSettings  | Veri deposuna veri yazma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının `storeSettings`altında kendi desteklenen yazma ayarları vardır.  | Hayır       |

`formatSettings`altında desteklenen **sınırlandırılmış metin yazma ayarları** :

| Özellik      | Açıklama                                                  | Gerekli                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings türü **DelimitedTextWriteSetting**olarak ayarlanmalıdır. | Evet                                                   |
| fileExtension | Çıkış dosyalarını adlandırmak için kullanılan dosya uzantısı, örneğin `.csv`, `.txt`. `fileName` output ıstreamtext veri kümesinde belirtilmediğinde belirtilmelidir. | Çıkış veri kümesinde dosya adı belirtilmediğinde Evet |

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki [kaynak dönüşümden](data-flow-source.md) ve [Havuz dönüşümünde](data-flow-sink.md) ayrıntıları öğrenin.

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
