---
title: Azure Data Factory XML biçimi
description: Bu konu başlığı altında, Azure Data Factory XML biçimiyle nasıl ele alınacağını açıklamaktadır.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/29/2020
ms.author: jingwang
ms.openlocfilehash: a4082e1e00ecb1d3b0157fcc35616704eaab0aae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386365"
---
# <a name="xml-format-in-azure-data-factory"></a>Azure Data Factory XML biçimi

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**XML dosyalarını ayrıştırmak** istediğinizde bu makaleye uyun. 

XML biçimi şu bağlayıcılar için desteklenir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md) [,,](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md). Kaynak olarak desteklenir, ancak havuz değildir.

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, XML veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

| Özellik         | Açıklama                                                  | Gerekli |
| ---------------- | ------------------------------------------------------------ | -------- |
| tür             | Veri kümesinin Type özelliği **XML** olarak ayarlanmalıdır. | Yes      |
| location         | Dosya (ler) in konum ayarları. Her dosya tabanlı bağlayıcının, altında kendi konum türü ve desteklenen özellikleri vardır `location` . **Bağlayıcı makalesi-> veri kümesi özellikleri bölümünde ayrıntılara bakın**. | Yes      |
| encodingName     | Test dosyalarını okumak/yazmak için kullanılan kodlama türü. <br>İzin verilen değerler şunlardır: "UTF-8", "UTF-16", "UTF-16TO", "UTF-32", "UTF-32TO", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JıS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "" IBM861 "," IBM863 "," IBM864 "," IBM865 "," IBM869 "," IBM870 "," IBM01140 "," IBM01141 "," IBM01142 "," IBM01143 "," IBM01144 "," IBM01145 "," IBM01146 "," IBM01147 "," IBM01148 "," IBM01149 "," ISO-2022-JP "," ISO-2022-KR "," ISO-8859-1 "," ISO-8859-2 "," ISO-8859-3 "," ISO-8859-4 "," ISO-8859-5 "," ISO-8859-6 "," ISO-8859-7 "," ISO-8859-8 "," ISO-8859-9 "," ISO-8859-13 " , "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| No       |
| nullValue | Null değerin dize gösterimini belirtir.<br/>Varsayılan değer boş bir **dizedir**. | No |
| sıkıştırma | Dosya sıkıştırmayı yapılandırmak için özellik grubu. Etkinlik yürütmesi sırasında sıkıştırma/açma işlemi yapmak istediğinizde bu bölümü yapılandırın. | No |
| tür<br>(*altında `compression`*) | XML dosyalarını okumak/yazmak için kullanılan sıkıştırma codec bileşeni. <br>İzin verilen değerler **şunlardır bzip2**, **gzip**, **söndür**, **zipsöndür**, **targzip**, **tar**, **Snappy** veya **lz4**. Varsayılan değer sıkıştırılmaz.<br>**Şu anda** kopyalama etkinliği "Snappy" & "lz4" desteklemez ve eşleme veri akışı "zipsöndür", "targzip" ve "tar" değerlerini desteklemez.<br>Örneğin, dosya tabanlı havuz veri deposuna **açılan** dosyaları açmak için kopyalama **etkinliği kullanılırken,** /  /  varsayılan dosyalar klasörüne çıkarılır: `<path specified in dataset>/<folder named as source compressed file>/` , `preserveZipFileNameAsFolder` / `preserveCompressionFileNameAsFolder` sıkıştırılmış dosyaların adının klasör yapısı olarak korunup korunmayacağını denetlemek için [etkinlik kaynağını kopyalama](#xml-as-source) üzerinde kullanın. | Hayır.  |
| düzey<br/>(*altında `compression`*) | Sıkıştırma oranı. <br>İzin verilen değerler **en iyi** veya **en hızlardır**.<br>- **En hızlı:** Elde edilen dosya en iyi şekilde sıkıştırılmasa bile, sıkıştırma işleminin mümkün olduğunca hızlı bir şekilde tamamlanmalıdır.<br>- **En iyi**: işlemin tamamlanmasını daha uzun sürse bile sıkıştırma işlemi en iyi şekilde sıkıştırılmalıdır. Daha fazla bilgi için bkz. [sıkıştırma düzeyi](/dotnet/api/system.io.compression.compressionlevel) konusu. | No       |

Azure Blob depolamada bir XML veri kümesi örneği aşağıda verilmiştir:

```json
{
    "name": "XMLDataset",
    "properties": {
        "type": "Xml",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, XML kaynağı tarafından desteklenen özelliklerin bir listesini sağlar.

Şema eşlemesinden XML verilerinin ve havuz veri deposunun/biçiminin nasıl [eşlenebileceği](copy-activity-schema-and-type-mapping.md)hakkında bilgi edinin. XML dosyalarını önizlerken, veriler JSON hiyerarşisiyle gösterilir ve alanları işaret etmek için JSON yolunu kullanırsınız.

### <a name="xml-as-source"></a>Kaynak olarak XML

Aşağıdaki özellikler, etkinlik ***\* kaynağını \**** kopyalama bölümünde desteklenir. [XML Bağlayıcısı davranışından](#xml-connector-behavior)daha fazla bilgi edinin.

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | Kopyalama etkinliği kaynağının Type özelliği **XMLSource** olarak ayarlanmalıdır. | Yes      |
| formatSettings | Bir özellik grubu. Aşağıdaki **XML okuma ayarları** tablosuna bakın. | No       |
| storeSettings | Veri deposundan veri okuma hakkında bir özellik grubu. Her dosya tabanlı bağlayıcının, altında kendi desteklenen okuma ayarları vardır `storeSettings` . **Bağlayıcı makalesi-> kopyalama etkinliği özellikleri bölümünde ayrıntılara bakın**. | No       |

Altında desteklenen **XML okuma ayarları** `formatSettings` :

| Özellik      | Açıklama                                                  | Gerekli |
| ------------- | ------------------------------------------------------------ | -------- |
| tür          | FormatSettings türü **XmlReadSettings** olarak ayarlanmalıdır. | Yes      |
| validationMode | XML şemasının doğrulanması gerekip gerekmediğini belirtir.<br>İzin verilen değerler **none** (varsayılan, doğrulama yok), **XSD** (XSD kullanarak doğrula), **DTD** (DTD kullanarak doğrula). | No |
| öznitelikleri | XML dosyalarını ayrıştırırken ad alanının etkinleştirilip etkinleştirilmeyeceğini belirtir. İzin verilen değerler: **true** (varsayılan), **false**. | No |
| Namespaceöneklerini | XML dosyası ayrıştırılırken alanları adlandırmak için kullanılan, önek eşleme için ad alanı URI 'SI.<br/>Bir XML dosyasında ad alanı ve ad alanı etkinse, varsayılan olarak, alan adı XML belgesinde olduğu gibidir.<br>Bu haritada ad alanı URI 'SI için tanımlanmış bir öğe varsa, alan adı `prefix:fieldName` . | No |
| detectDataType | Tamsayı, Çift ve Boole veri türlerinin algılanmayacağı. İzin verilen değerler: **true** (varsayılan), **false**.| No |
| compressionProperties | Belirli bir sıkıştırma codec bileşeni için verileri açmak üzere bir özellik grubu. | No       |
| preserveZipFileNameAsFolder<br>(*`compressionProperties` -> `type` as `ZipDeflateReadSettings`*)  | Giriş veri kümesi **Zipsöndür** sıkıştırma ile yapılandırıldığında geçerlidir. Kaynak ZIP dosya adının kopyalama sırasında klasör yapısı olarak korunup korunmayacağını gösterir.<br>- **True (varsayılan)** olarak ayarlandığında Data Factory daraltılmış dosyaları içine yazar `<path specified in dataset>/<folder named as source zip file>/` .<br>- **False** olarak ayarlandığında Data Factory ZIP dosyalarını doğrudan öğesine yazar `<path specified in dataset>` . Yarış veya beklenmedik davranışlara engel olmak için farklı kaynak ZIP dosyalarında yinelenen dosya adlarında bulunmadığından emin olun.  | No |
| preserveCompressionFileNameAsFolder<br>(*`compressionProperties` -> `type` `TarGZipReadSettings` veya `TarReadSettings` olarak*) | Giriş veri kümesi **targık** sıkıştırması ile yapılandırıldığında geçerlidir /  . Kaynak sıkıştırılmış dosya adının kopyalama sırasında klasör yapısı olarak korunup korunmayacağını gösterir.<br>- **True (varsayılan)** olarak ayarlandığında Data Factory, açılan dosyaları içine yazar `<path specified in dataset>/<folder named as source compressed file>/` . <br>- **False** olarak ayarlandığında Data Factory açılan dosyaları doğrudan öğesine yazar `<path specified in dataset>` . Yarış veya beklenmedik davranışlara engel olmak için farklı kaynak dosyalarında yinelenen dosya adlarında bulunmadığından emin olun. | No |

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışlarını eşleme ' de, aşağıdaki veri depolarında XML biçiminde okuma ve yazma yapabilirsiniz: [Azure Blob depolama](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md#mapping-data-flow-properties)ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#mapping-data-flow-properties). XML veri kümesi veya [satır içi veri kümesi](data-flow-source.md#inline-datasets)kullanarak XML dosyalarını işaret edebilirsiniz.

### <a name="source-properties"></a>Kaynak özellikleri

Aşağıdaki tabloda bir XML kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz. [XML Bağlayıcısı davranışından](#xml-connector-behavior)daha fazla bilgi edinin. Satır içi veri kümesi kullanırken, [veri kümesi özellikleri](#dataset-properties) bölümünde açıklanan özelliklerle aynı olan ek dosya ayarlarını görürsünüz. 

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Joker karakter yolları | Joker karakterle eşleşen tüm dosyalar işlenecek. Veri kümesinde ayarlanan klasör ve dosya yolunu geçersiz kılar. | No | String [] | Yavaya Cardyolları |
| Bölüm kök yolu | Bölümlenmiş dosya verileri için bölümlenmiş klasörleri sütun olarak okumak üzere bir bölüm kök yolu girebilirsiniz | Hayır | Dize | Partitionrootyolu |
| Dosya listesi | Kaynağınızın işlenecek dosyaları listeleyen bir metin dosyasına işaret edip etmediğini belirtir | No | `true` veya `false` | Si |
| Dosya adının depolanacak sütun | Kaynak dosya adı ve yolu ile yeni bir sütun oluşturma | Hayır | Dize | rowUrlColumn |
| Tamamlandıktan sonra | İşlemden sonra dosyaları silin veya taşıyın. Dosya yolu, kapsayıcı kökünden başlar | No | Sil: `true` veya `false` <br> Geçiş `['<from>', '<to>']` | purgeFiles <br>moveFiles |
| Son değiştirme ölçütü | En son değiştirildiklerinde dosyaları filtrelemek için seçin | No | Timestamp | Modıfıedafter <br>modifiedBefore |
| Doğrulama modu | XML şemasının doğrulanması gerekip gerekmediğini belirtir. | No | `None` (varsayılan, doğrulama yok)<br>`xsd` (XSD kullanarak doğrula)<br>`dtd` (DTD kullanarak doğrula). | validationMode |
| Ad alanları | XML dosyalarını ayrıştırırken ad alanının etkinleştirilip etkinleştirilmeyeceğini belirtir. | No | `true` (varsayılan) veya `false` | öznitelikleri |
| Ad alanı ön eki çiftleri | XML dosyası ayrıştırılırken alanları adlandırmak için kullanılan, önek eşleme için ad alanı URI 'SI.<br/>Bir XML dosyasında ad alanı ve ad alanı etkinse, varsayılan olarak, alan adı XML belgesinde olduğu gibidir.<br>Bu haritada ad alanı URI 'SI için tanımlanmış bir öğe varsa, alan adı `prefix:fieldName` . | No | Desenli dizi`['URI1'->'prefix1','URI2'->'prefix2']` | Namespaceöneklerini |
| Dosya bulunamamış izin ver | True ise bir dosya bulunmazsa bir hata oluşturulmaz | hayır | `true` veya `false` | ıgnorenofilesfound |

### <a name="xml-source-script-example"></a>XML kaynak betiği örneği

Aşağıdaki komut dosyası, veri akışı modu kullanılarak veri akışlarını eşleme içindeki bir XML kaynak yapılandırmasına örnektir.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

Aşağıdaki betik, satır içi veri kümesi modunu kullanan bir XML kaynak yapılandırmasına örnektir.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'xml',
    fileSystem: 'filesystem',
    folderPath: 'folder',
    validationMode: 'xsd',
    namespaces: true) ~> XMLSource
```

## <a name="xml-connector-behavior"></a>XML Bağlayıcısı davranışı

Kaynak olarak XML kullanırken aşağıdakileri göz önünde edin.

- XML öznitelikleri:

    - Bir öğenin öznitelikleri, hiyerarşideki öğenin alt alanları olarak ayrıştırılır.
    - Öznitelik alanının adı, kalıbı izler `@attributeName` .

- XML şeması doğrulaması:

    - Şemayı doğrulamadan veya XSD veya DTD kullanarak şemayı doğrulamaya seçebilirsiniz.
    - XML dosyalarını doğrulamak için XSD veya DTD kullanırken, XSD/DTD 'nin, göreli yol aracılığıyla XML dosyaları içine yerleştirilmesi gerekir.

- Ad alanı işleme:

    - Ad alanı, veri akışı kullanılırken devre dışı bırakılabilir, bu durumda ad alanını tanımlayan öznitelikler normal öznitelikler olarak ayrıştırılacaktır.
    - Ad alanı etkinleştirildiğinde, öğe ve özniteliklerin adları,       `namespaceUri,elementName` ve varsayılan olarak, stili izler `namespaceUri,@attributeName` . Kaynaktaki her bir ad alanı için ad alanı öneki tanımlayabilir, bu durumda öğe ve özniteliklerin adları, `definedPrefix:elementName` ya da bunun yerine, veya özniteliklerini izler `definedPrefix:@attributeName` .

- Değer sütunu:

    - Bir XML öğesinde hem basit metin değeri hem de öznitelikler/alt öğeleri varsa, basit metin değeri, yerleşik alan adı ile bir "değer sütunu" değeri olarak ayrıştırılır `_value_` . Ayrıca, öğesinin ad alanını devralır ve geçerli olur.

## <a name="next-steps"></a>Sonraki adımlar

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)