---
title: Azure Blob depolama alanına/konumundan veri kopyalama
description: "Azure Data Factory blob verilerini kopyalamayı öğrenin. Örneğimizi kullanın: Azure Blob depolama ve Azure SQL veritabanı 'na veri kopyalama."
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: bec8160f-5e07-47e4-8ee1-ebb14cfb805d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9aeef3a2f6a43346a7637c3e2497979632b04762
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683269"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Blob depolama alanına veya buradan veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-azure-blob-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-azure-blob-storage.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Azure Blob Storage Bağlayıcısı](../connector-azure-blob-storage.md).


Bu makalede, Azure Blob depolamaya veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

## <a name="overview"></a>Genel Bakış
Desteklenen herhangi bir kaynak veri deposundan verileri Azure Blob depolama alanına veya Azure Blob depolamadan desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının bir listesi verilmiştir. Örneğin **, verileri bir** SQL Server veritabanından veya Azure **SQL veritabanından Azure** BLOB depolama alanına taşıyabilirsiniz. Azure Blob depolama **alanından** Azure SQL veri ambarı **'na** veya bir Azure Cosmos DB koleksiyonuna veri kopyalayabilirsiniz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Desteklenen senaryolar
**Azure Blob depolama alanından** aşağıdaki veri depolarına veri kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Aşağıdaki veri depolarındaki verileri **Azure Blob depolama alanına**kopyalayabilirsiniz:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> Kopyalama etkinliği, genel amaçlı Azure depolama hesaplarından ve sık/seyrek erişimli blob depolamadaki verilerin kopyalanmasını destekler. Etkinlik, **blok, ekleme veya sayfa Bloblarından okumayı**destekler, ancak **yalnızca blok bloblarına yazmayı**destekler. Azure Premium Storage, sayfa Blobları tarafından desteklenen bir havuz olarak desteklenmez.
>
> Veri başarıyla hedefe kopyalandıktan sonra kopyalama etkinliği kaynaktaki verileri silmez. Başarılı bir kopyadan sonra kaynak verileri silmeniz gerekiyorsa, verileri silmek ve işlem hattındaki etkinliği kullanmak için [özel bir etkinlik](data-factory-use-custom-activities.md) oluşturun. Bir örnek için [GitHub 'da blob veya klasör silme örneğine](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/DeleteBlobFileFolderCustomActivity)bakın.

## <a name="get-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak bir Azure Blob depolama alanına/kaynağından veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Bu makalede, verileri bir Azure Blob depolama konumundan başka bir Azure Blob depolama konumuna kopyalamak için bir işlem hattı oluşturmaya yönelik bir [yönerge](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) bulunur. Azure Blob depolama alanından Azure SQL veritabanına veri kopyalamak için bir işlem hattı oluşturmaya yönelik bir öğretici için bkz. [öğretici: kopyalama sihirbazını kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md).

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Bir **Veri Fabrikası**oluşturun. Bir veri fabrikası bir veya daha fazla işlem hattı içerebilir.
2. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun. Örneğin, bir Azure Blob depolama alanından Azure SQL veritabanına veri kopyalıyorsanız, Azure depolama hesabınızı ve Azure SQL veritabanınızı veri fabrikanıza bağlamak için iki bağlı hizmet oluşturursunuz. Azure Blob depolamaya özgü bağlantılı hizmet özellikleri için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun. Son adımda bahsedilen örnekte, blob kapsayıcısını ve girdi verilerini içeren klasörü belirtmek için bir veri kümesi oluşturursunuz. Ve, blob depolamadan kopyalanmış verileri tutan Azure SQL veritabanında SQL tablosunu belirtmek için başka bir veri kümesi oluşturursunuz. Azure Blob depolamaya özgü veri kümesi özellikleri için bkz. [veri kümesi özellikleri](#dataset-properties) bölümü.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun. Daha önce bahsedilen örnekte, BlobSource değerini kaynak ve SqlSink olarak kopyalama etkinliği için havuz olarak kullanacaksınız. Benzer şekilde, Azure SQL veritabanından Azure Blob depolama alanına kopyalama yapıyorsanız, kopyalama etkinliğinde SqlSource ve BlobSink kullanın. Azure Blob depolamaya özgü kopyalama etkinliği özellikleri için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü. Bir veri deposunu kaynak veya havuz olarak kullanma hakkında ayrıntılı bilgi için, veri deponuzdaki önceki bölümde yer alan bağlantıya tıklayın.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız.  Bir Azure Blob depolama alanına/konumundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımları içeren örnekler için, bu makalenin [JSON örnekleri](#json-examples-for-copying-data-to-and-from-blob-storage  ) bölümüne bakın.

Aşağıdaki bölümlerde, Azure Blob depolamaya özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Azure depolama alanını bir Azure Data Factory 'ye bağlamak için kullanabileceğiniz iki tür bağlı hizmet vardır. Bunlar: **Azurestorage** Linked Service ve **Azurestokıgesas** bağlı hizmeti. Azure depolama bağlı hizmeti, veri fabrikasına Azure depolama 'ya küresel erişim sağlar. Ancak, Azure Storage SAS (paylaşılan erişim Imzası) bağlı hizmeti, veri fabrikasını Azure depolama 'ya kısıtlı/zamana bağlı erişimle sağlar. Bu iki bağlı hizmet arasında başka farklılık yoktur. Gereksinimlerinize uygun olan bağlı hizmeti seçin. Aşağıdaki bölümler, bu iki bağlı hizmet hakkında daha fazla ayrıntı sağlar.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Bir Azure Blob depolamada giriş veya çıkış verilerini temsil edecek bir veri kümesi belirtmek için, veri kümesinin Type özelliğini: **AzureBlob**olarak ayarlayın. Veri kümesinin **Linkedservicename** özelliğini Azure Storage veya Azure Storage SAS bağlı hizmeti adı olarak ayarlayın.  Veri kümesinin tür özellikleri **BLOB kapsayıcısını** ve BLOB depolama alanındaki **klasörü** belirtir.

Veri kümelerini tanımlamaya yönelik özellikler & JSON bölümlerinin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

Data Factory, Azure blob: Int16, Int32, Int64, Single, Double, Decimal, Byte [], bool, String, Guid, DateTime, gibi okunan şemaya bağlı veri kaynakları için "Structure" içinde tür bilgilerini sağlamak için aşağıdaki CLS uyumlu .NET tabanlı tür değerlerini destekler. DateTimeOffset, TimeSpan. Data Factory, verileri bir kaynak veri deposundan havuz veri deposuna taşırken tür dönüştürmeleri otomatik olarak gerçekleştirir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu, biçimi vb. hakkında bilgi sağlar. **AzureBlob** DataSet türündeki veri kümesi Için typeproperties bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| folderPath |BLOB depolama alanındaki kapsayıcının ve klasörün yolu. Örnek: myblobcontainer\myblobfolder\ |Evet |
| fileName |Blobun adı. Dosya adı isteğe bağlıdır ve büyük/küçük harfe duyarlıdır.<br/><br/>Bir dosya adı belirtirseniz, etkinlik (kopyalama dahil) belirli bir Blobun üzerinde kullanılır.<br/><br/>Dosya adı belirtilmediğinde, Copy, giriş veri kümesi için folderPath içindeki tüm Blobları içerir.<br/><br/>Bir çıkış veri kümesi için **dosya adı** belirtilmediğinde ve etkinlik havuzunda **preservehierarchy** belirtilmemişse, oluşturulan dosyanın adı şu biçimde olacaktır: `Data.<Guid>.txt` (örneğin:: Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt |Hayır |
| partitionedBy |partitionedBy, isteğe bağlı bir özelliktir. Bu uygulamayı, zaman serisi verileri için dinamik bir folderPath ve filename belirtmek üzere kullanabilirsiniz. Örneğin, folderPath her saat veri için parametreleştirilebilirler. Ayrıntılar ve örnekler için [partitionedBy özelliğini kullanma bölümüne](#using-partitionedby-property) bakın. |Hayır |
| formatını | Şu biçim türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **orcformat**, **parquetformat**. Biçim ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](data-factory-supported-file-and-compression-formats.md#text-format), [JSON biçimi](data-factory-supported-file-and-compression-formats.md#json-format), [avro Format](data-factory-supported-file-and-compression-formats.md#avro-format), [orc biçimi](data-factory-supported-file-and-compression-formats.md#orc-format)ve [Parquet biçim](data-factory-supported-file-and-compression-formats.md#parquet-format) bölümleri. <br><br> Dosyaları dosya tabanlı mağazalar (ikili kopya) arasında **olduğu gibi kopyalamak** istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın. |Hayır |
| masıyla | Verilerin türünü ve sıkıştırma düzeyini belirtin. Desteklenen türler şunlardır: **gzip**, **söndür**, **bzip2**ve **zipsöndür**. Desteklenen düzeyler şunlardır: **en iyi** ve **en hızlı**. Daha fazla bilgi için bkz. [Azure Data Factory dosya ve sıkıştırma biçimleri](data-factory-supported-file-and-compression-formats.md#compression-support). |Hayır |

### <a name="using-partitionedby-property"></a>PartitionedBy özelliğini kullanma
Önceki bölümde belirtildiği gibi, **Partitionedby** özelliği, [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md)ile zaman serisi verileri için dinamik bir FolderPath ve filename belirtebilirsiniz.

Zaman serisi veri kümeleri, zamanlama ve dilimler hakkında daha fazla bilgi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) ve [& yürütme makaleleri zamanlama](data-factory-scheduling-and-execution.md) .

#### <a name="sample-1"></a>Örnek 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Bu örnekte, {Slice}, belirtilen (YYYYMMDDHH) biçimindeki Data Factory sistem değişkeni değeri ile değiştirilmiştir. Dilimcstart, dilimin başlangıç zamanına başvurur. FolderPath her bir dilim için farklıdır. Örneğin: wikidatagateway/wisvahili amptadataout/2014100103 veya wikidatagateway/wisvahili amptadataout/2014100104

#### <a name="sample-2"></a>Örnek 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Bu örnekte, monthestart 'ın Year, month, Day ve Time, folderPath ve fileName özellikleri tarafından kullanılan ayrı değişkenlere ayıklanır.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış veri kümeleri ve ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir. Ancak, etkinliğin **typeproperties** bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir. Verileri bir Azure Blob depolamadan taşıyorsanız, kopyalama etkinliğinde kaynak türünü **Blobsource**olarak ayarlarsınız. Benzer şekilde, verileri bir Azure Blob depolama alanına taşıyorsanız, kopyalama etkinliğindeki havuz türünü **Blobsink**olarak ayarlarsınız. Bu bölüm, BlobSource ve BlobSink tarafından desteklenen özelliklerin bir listesini sağlar.

**Blobsource** , **typeproperties** bölümünde aşağıdaki özellikleri destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| öz |Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. |True (varsayılan değer), yanlış |Hayır |

**Blobsink** , aşağıdaki özellikleri **typeproperties** bölümünü destekler:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| copyBehavior |Kaynak BlobSource veya FileSystem olduğunda kopyalama davranışını tanımlar. |<b>Preservehierarchy</b>: Hedef klasördeki dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu hedef dosyanın hedef klasöre göreli yolu ile aynıdır.<br/><br/><b>DÜZEDEN hiyerarşi</b>: kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyindedir. Hedef dosyaların otomatik olarak üretilen adı var. <br/><br/><b>Mergefiles</b>: kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya/BLOB adı belirtilmişse, birleştirilmiş dosya adı belirtilen ad olur; Aksi takdirde, otomatik olarak oluşturulan dosya adı olacaktır. |Hayır |

**Blobsource** Ayrıca geriye dönük uyumluluk için bu iki özelliği destekler.

* **Treatemptyasnull**: null veya boş dizenin null değer olarak değerlendirilip değerlendirilmeyeceğini belirtir.
* **Skipheaderlinecount** -kaç satır atlanması gerektiğini belirtir. Yalnızca giriş veri kümesi TextFormat kullanıyorsa geçerlidir.

Benzer şekilde, **Blobsink** geriye dönük uyumluluk için aşağıdaki özelliği destekler.

* **Blobwriteraddheader**: bir çıktı veri kümesine yazılırken sütun tanımlarının üst bilgisinin eklenip eklenmeyeceğini belirtir.

Veri kümeleri artık aynı işlevleri uygulayan aşağıdaki özellikleri destekler: **Treatemptyasnull**, **skiplinecount**, **firstrowasheader**.

Aşağıdaki tabloda, bu blob kaynak/havuz özelliklerinin yerine yeni veri kümesi özelliklerinin kullanılmasına ilişkin yönergeler sağlanmaktadır.

| Kopyalama etkinliği özelliği | DataSet özelliği |
|:--- |:--- |
| BlobSource üzerinde skipHeaderLineCount |skipLineCount ve firstRowAsHeader. Önce satırlar atlanır, ardından ilk satır üst bilgi olarak okundu. |
| BlobSource üzerinde treatEmptyAsNull |giriş veri kümesinde treatEmptyAsNull |
| BlobSink üzerinde blobWriterAddHeader |çıkış veri kümesinde firstRowAsHeader |

Bu özelliklerle ilgili ayrıntılı bilgi için bkz. [TextFormat bölümü belirtme](data-factory-supported-file-and-compression-formats.md#text-format) .

### <a name="recursive-and-copybehavior-examples"></a>özyinelemeli ve copyBehavior örnekleri
Bu bölümde, özyinelemeli ve copyBehavior değerlerinin farklı birleşimleri için kopyalama işleminin ortaya çıkan davranışı açıklanmaktadır.

| öz | copyBehavior | Sonuç davranışı |
| --- | --- | --- |
| doğru |preserveHierarchy |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>hedef klasör Klasör1, kaynak ile aynı yapıyla oluşturulmuştur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| doğru |DÜZEDEN hiyerarşisi |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Folder1<br/>Fıle1 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>dosya2 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>file3 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>File4 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>File5 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp; |
| doğru |mergeFiles |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1 + dosya2 + File3 + File4 + dosya 5 içerikleri, otomatik olarak oluşturulan dosya adı ile tek bir dosyada birleştirilir |
| false |preserveHierarchy |Aşağıdaki yapıyla Klasör1 kaynak klasörü için: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Klasör1 hedef klasörü aşağıdaki yapıyla oluşturulur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/><br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte alınmaz. |
| false |DÜZEDEN hiyerarşisi |Aşağıdaki yapıyla Klasör1 kaynak klasörü için:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Klasör1 hedef klasörü aşağıdaki yapıyla oluşturulur<br/><br/>Folder1<br/>Fıle1 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/>dosya2 için otomatik olarak oluşturulan ad &nbsp;&nbsp;&nbsp;&nbsp;<br/><br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte alınmaz. |
| false |mergeFiles |Aşağıdaki yapıyla Klasör1 kaynak klasörü için:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>Klasör1 hedef klasörü aşağıdaki yapıyla oluşturulur<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1 + dosya2 içerikleri, otomatik olarak oluşturulan dosya adı ile tek bir dosyada birleştirilir. Fıle1 için otomatik olarak oluşturulan ad<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte alınmaz. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>İzlenecek yol: blob depolamadan veri kopyalamak için kopyalama Sihirbazı 'nı kullanma
Azure Blob depolama alanı 'na/konumundan hızlı bir şekilde veri kopyalama bölümüne bakalım. Bu kılavuzda, hem kaynak hem de hedef veri deposu türü: Azure Blob depolama. Bu izlenecek işlem hattı, verileri bir klasörden aynı blob kapsayıcısındaki başka bir klasöre kopyalar. Bu izlenecek yol, blob depolamayı kaynak veya havuz olarak kullanırken ayarları veya özellikleri göstermek kasıtlı olarak basittir.

### <a name="prerequisites"></a>Ön koşullar
1. Henüz bir tane yoksa, genel amaçlı bir **Azure depolama hesabı** oluşturun. Blob depolamayı bu kılavuzda hem **kaynak** hem de **hedef** veri deposu olarak kullanırsınız. Azure depolama hesabınız yoksa, oluşturma adımları için [Depolama hesabı oluşturma](../../storage/common/storage-quickstart-create-account.md) makalesine bakın.
2. Depolama hesabında **adfblobconnector** adlı bir blob kapsayıcısı oluşturun.
4. **Adfblobconnector** kapsayıcısında **Input** adlı bir klasör oluşturun.
5. Aşağıdaki içerikle birlikte bir dosya dosyası oluşturun ve [Azure Depolama Gezgini](https://azurestorageexplorer.codeplex.com/) gibi araçları kullanarak **giriş** klasörüne yükleyin **.**
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Veri Fabrikası oluşturma
1. [Azure portalında](https://portal.azure.com) oturum açın.
2. Sol üst köşedeki **kaynak oluştur ' a** tıklayın, **Intelligence + Analytics**' e tıklayın ve **Data Factory**' ye tıklayın.
3. **Yeni Data Factory** bölmesinde:  
    1. **Ad**Için **Adfblobconnectordf** girin. Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Şu hatayı alırsanız: `*Data factory name “ADFBlobConnectorDF” is not available`, veri fabrikasının adını değiştirin (örneğin, yournameADFBlobConnectorDF) ve yeniden oluşturmayı deneyin. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.
    2. Azure **aboneliğinizi** seçin.
    3. Kaynak grubu için mevcut bir kaynak grubunu seçmek için **Varolanı kullan** ' ı seçin (veya) kaynak grubu için bir ad girmek üzere **Yeni oluştur** ' u seçin.
    4. Veri fabrikası için bir **konum** seçin.
    5. Dikey pencerenin alt kısmındaki **Panoya sabitle** onay kutusunu seçin.
    6. **Oluştur**'a tıklayın.
3. Oluşturma işlemi tamamlandıktan sonra, aşağıdaki görüntüde gösterildiği gibi **Data Factory** dikey penceresini görürsünüz: ![Data Factory giriş sayfası](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Kopyalama Sihirbazı
1. Data Factory giriş sayfasında, **veri kopyalama Sihirbazı 'nı** ayrı bir sekmede başlatmak Için **veri Kopyala** kutucuğuna tıklayın.  
    
    > [!NOTE]
    > Web tarayıcısının "yetkilendiriliyor..." konumunda takılı olduğunu görürseniz, **üçüncü taraf tanımlama bilgilerini ve site verilerini engellemeyi** devre dışı bırakın/işaretini kaldırın ve **login.microsoftonline.com** için bir özel durum oluşturun ve ardından Sihirbazı yeniden başlatmayı deneyin.
2. **Özellikler** sayfasında:
    1. **Görev adı**Için **copypipeline** girin. Görev adı, veri fabrikanızdaki işlem hattının adıdır.
    2. Görev için bir **Açıklama** girin (isteğe bağlı).
    3. **Görev temposunda veya görev zamanlaması**için **zamanlamaya göre Çalıştır** seçeneğini saklayın. Bu görevi bir zamanlamaya göre tekrar tekrar çalıştırmak yerine yalnızca bir kez çalıştırmak istiyorsanız, **Şimdi Çalıştır**' ı seçin. **Şimdi Çalıştır** seçeneğini belirlerseniz, bir [kerelik işlem hattı](data-factory-create-pipelines.md#onetime-pipeline) oluşturulur.
    4. **Yinelenen model**ayarlarını tutun. Bu görev, bir sonraki adımda belirttiğiniz başlangıç ve bitiş zamanları arasında günlük olarak çalışır.
    5. **Başlangıç tarihi saatini** **04/21/2017**olarak değiştirin.
    6. **Bitiş tarih saatini** **04/25/2017**olarak değiştirin. Takvime göz atmak yerine tarihi yazmak isteyebilirsiniz.
    8. **İleri**’ye tıklayın.
        ![kopyalama aracı-Özellikler sayfası](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. **Kaynak veri deposu** sayfasında **Azure Blob Storage** kutucuğuna tıklayın. Kopyalama görevine yönelik kaynak veri deposunu belirtmek için bu sayfayı kullanın. Yeni bir veri deposu belirtmek için mevcut bir veri deposu bağlı hizmetini kullanabilirsiniz (veya) yeni bir veri deposu belirtebilirsiniz. Mevcut bir bağlı hizmeti kullanmak için **mevcut bağlı hizmetlerden** seçim yapın ve doğru bağlı hizmeti seçin.
    ![kopyalama aracı-kaynak veri deposu sayfası](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. **Azure Blob depolama hesabı belirtin** sayfasında:
    1. **Bağlantı adı**için otomatik olarak oluşturulan adı koruyun. Bağlantı adı, şu türde bağlı hizmetin adıdır: Azure Storage.
    2. **Hesap seçme yöntemi** için **Azure aboneliklerinden** seçeneğinin belirlendiğini onaylayın.
    3. Azure aboneliğiniz ' ı seçin veya **Tüm** **Azure abonelikleri**' ni seçin.
    4. Seçili abonelikte bulunan Azure depolama hesapları listesinden bir **Azure depolama hesabı** seçin. Ayrıca, **hesap seçme yöntemi**için **el ile gir** seçeneğini belirleyerek depolama hesabı ayarlarını el ile girmeyi de tercih edebilirsiniz.
    5. **İleri**’ye tıklayın.  
        ![kopyalama aracı-Azure Blob Depolama hesabını belirtin](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. **Girdi dosyası veya klasörü seçin** sayfasında:
    1. **Adfblobcontainer**öğesine çift tıklayın.
    2. **Giriş**' i seçin ve **Seç**' e tıklayın. Bu izlenecek yolda giriş klasörünü seçersiniz. Bunun yerine, klasöründeki ". txt" dosyasını da seçebilirsiniz.
        ![kopyalama aracı-giriş dosyasını veya klasörünü seçin](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. **Girdi dosyasını veya klasörünü seçin** sayfasında:
    1. **Dosya veya klasörün** **adfblobconnector/Input**olarak ayarlandığını onaylayın. Dosyalar alt klasörlerdir (örneğin, 2017/04/01, 2017/04/02 vb.), dosya veya klasör için adfblobconnector/Input/{year}/{month}/{Day} girin. Metin kutusunu basılı bıraktığınızda, yıl (yyyy), ay (MM) ve gün (gg) için biçim seçmek üzere üç açılan liste görürsünüz.
    2. **Kopya dosyasını yinelemeli**olarak ayarlamayın. Hedefe kopyalanan dosyalar için klasörler arasında yinelemeli geçiş yapmak için bu seçeneği belirleyin.
    3. **İkili kopya** seçeneğini kullanmayın. Kaynak dosyanın hedefe ikili bir kopyasını gerçekleştirmek için bu seçeneği belirleyin. Sonraki sayfalarda daha fazla seçenek görebilmeniz için bu izlenecek yol için seçim yapın.
    4. **Sıkıştırma türünün** **none**olarak ayarlandığını doğrulayın. Kaynak dosyalarınız desteklenen biçimlerden birinde sıkıştırılmışsa Bu seçenek için bir değer seçin.
    5. **İleri**’ye tıklayın.
    ![kopyalama aracı-giriş dosyasını veya klasörünü seçin](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. **Dosya biçimi ayarları** sayfasında sınırlayıcıları ve sihirbaz tarafından dosya ayrıştırılarak otomatik olarak algılanan düzeni görürsünüz.
    1. Aşağıdaki seçenekleri onaylayın:  
        a. **Dosya biçimi** **metin biçimine**ayarlanır. Desteklenen tüm biçimleri aşağı açılan listede görebilirsiniz. Örneğin: JSON, avro, ORC, Parquet.
       b. **Sütun sınırlayıcısı** `Comma (,)`olarak ayarlanır. Aşağı açılan listede Data Factory tarafından desteklenen diğer sütun sınırlayıcılarını görebilirsiniz. Ayrıca, özel bir sınırlayıcı de belirtebilirsiniz.
       c. **Satır sınırlayıcısı** `Carriage Return + Line feed (\r\n)`olarak ayarlanır. Data Factory tarafından desteklenen diğer satır sınırlayıcılarını aşağı açılan listede görebilirsiniz. Ayrıca, özel bir sınırlayıcı de belirtebilirsiniz.
       d. **Atlama satırı sayısı** **0**olarak ayarlanır. Dosyanın en üstünde birkaç satır atlanmasını istiyorsanız, numarayı buraya girin.
       e. **İlk veri satırı sütun adlarını içerir** olarak ayarlanamaz. Kaynak dosyalar ilk satırda sütun adları içeriyorsa, bu seçeneği seçin.
       f. **Boş sütun değerini null olarak değerlendir** seçeneği ayarlandı.
    2. Gelişmiş seçenekleri görmek için Gelişmiş **Ayarlar** ' ı genişletin.
    3. Sayfanın alt kısmında, izleme. txt dosyasındaki verilerin **önizlemesine** bakın.
    4. Kopyalama sihirbazının, kaynak dosyadaki verilere bakarak çıkartılan şemayı görmek için en alttaki **şema** sekmesi ' ne tıklayın.
    5. Sınırlayıcıları gözden geçirin verilerin önizlemesini gördükten sonra **İleri**’ye tıklayın.
    ![kopyalama aracı-dosya biçimi ayarları](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. **Hedef veri deposu sayfasında** **Azure Blob depolama**' yı seçin ve **İleri**' ye tıklayın. Bu izlenecek yolda hem kaynak hem de hedef veri deposu olarak Azure Blob depolamayı kullanıyorsunuz.  
    ![kopyalama aracı-hedef veri deposu seçin](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. **Azure Blob depolama hesabı belirtin** sayfasında:  
    1. **Bağlantı adı** alanı için **AzureStorageLinkedService** girin.
    2. **Hesap seçme yöntemi** için **Azure aboneliklerinden** seçeneğinin belirlendiğini onaylayın.
    3. Azure **aboneliğinizi** seçin.
    4. Azure depolama hesabınızı seçin.
    5. **İleri**’ye tıklayın.
10. **Çıktı dosyasını veya klasörünü seçin** sayfasında:  
    1. **klasör yolunu** **adfblobconnector/output/{year}/{month}/{Day}** olarak belirtin. **Sekme**girin.
    1. **Yıl**için **yyyy**' ı seçin.
    1. **Ay**Için, **mm**olarak ayarlandığını onaylayın.
    1. **Gün**için, **dd**olarak ayarlandığını onaylayın.
    1. **Sıkıştırma türünün** **none**olarak ayarlandığını doğrulayın.
    1. **Kopyalama davranışının** **Dosyaları Birleştir**olarak ayarlandığını doğrulayın. Aynı ada sahip çıkış dosyası zaten varsa, yeni içerik sonundaki aynı dosyaya eklenir.
    1. **İleri**’ye tıklayın.
       ![kopyalama aracı-çıkış dosyası veya klasör seçin](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. **Dosya biçimi ayarları** sayfasında, ayarları gözden geçirin ve **İleri**' ye tıklayın. Buradaki ek seçeneklerden biri, çıkış dosyasına bir üst bilgi eklemektir. Bu seçeneği belirlerseniz, kaynağın şemasından sütunların adlarıyla bir başlık satırı eklenir. Kaynak için şemayı görüntülerken varsayılan sütun adlarını yeniden adlandırabilirsiniz. Örneğin, ilk sütunu Ilk adı ve ikinci sütunu soyadı olacak şekilde değiştirebilirsiniz. Ardından, çıkış dosyası bu adlara sahip bir üstbilgiyle birlikte sütun adı olarak oluşturulur.
    hedef](media/data-factory-azure-blob-connector/file-format-destination.png) için ![kopyalama aracı-dosya biçimi ayarları
12. **Performans ayarları** sayfasında, **bulut birimlerinin** ve **paralel kopyaların** **Auto**olarak ayarlandığını doğrulayın ve ileri ' ye tıklayın. Bu ayarlar hakkında daha fazla bilgi için bkz. [kopyalama etkinliği performansı ve ayarlama Kılavuzu](data-factory-copy-activity-performance.md#parallel-copy).
    ![kopyalama aracı-performans ayarları](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. **Özet** sayfasında tüm ayarları gözden geçirin (görev özellikleri, kaynak ve hedef ayarları ve kopyalama ayarları) ve **İleri**' ye tıklayın.
    ![kopyalama aracı-Özet sayfası](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. **Özet** sayfasındaki bilgileri gözden geçirin ve **Son**’a tıklayın. Sihirbaz, veri fabrikasında (Kopyalama Sihirbazı’nı başlattığınız yer) iki bağlı hizmet, iki veri kümesi (girdi ve çıktı) ve bir işlem hattı oluşturur.
    ![kopyalama aracı-dağıtım sayfası](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>İşlem hattını izleme (görevi Kopyala)

1. **Dağıtım** sayfasında bağlantıyı `Click here to monitor copy pipeline` tıklatın.
2. **İzlemeyi ve yönetmeyi uygulamayı** ayrı bir sekmede görmeniz gerekir.  ![uygulama](media/data-factory-azure-blob-connector/monitor-manage-app.png) Izleme ve yönetme
3. En üstteki **Başlangıç** saatini `04/19/2017` ve **bitiş** zamanı `04/27/2017`olacak şekilde değiştirin ve **Uygula**' ya tıklayın.
4. **Etkınlık Windows** listesinde beş etkinlik penceresi görmeniz gerekir. **Windowstart** süreleri, işlem hattı başlangıcından ardışık düzen bitiş zamanına kadar olan tüm günleri kapsamalıdır.
5. Etkinlik pencerelerinin tümünün durumunu görene kadar birkaç kez **etkinlik Windows** listesi için **Yenile** düğmesine tıklayın.
6. Şimdi, çıkış dosyalarının adfblobconnector kapsayıcısının çıkış klasöründe oluşturulduğunu doğrulayın. Çıkış klasöründe aşağıdaki klasör yapısını görmeniz gerekir:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Veri fabrikalarını izleme ve yönetme hakkında ayrıntılı bilgi için Data Factory işlem hattını [izleme ve yönetme](data-factory-monitor-manage-app.md) makalesine bakın.

### <a name="data-factory-entities"></a>Data Factory varlıkları
Şimdi Data Factory giriş sayfası ile sekmeye geri dönün. Data Factory 'de Şu anda iki bağlı hizmet, iki veri kümesi ve bir işlem hattı olduğuna dikkat edin.

![Varlıklarla Data Factory giriş sayfası](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Data Factory Düzenleyiciyi başlatmak için **Yazar ve dağıt** ' a tıklayın.

![Data Factory Düzenleyicisi](media/data-factory-azure-blob-connector/data-factory-editor.png)

Data Factory 'de aşağıdaki Data Factory varlıkları görmeniz gerekir:

- İki bağlı hizmet. Biri kaynak ve diğeri için bir tane. Her iki bağlı hizmet da bu kılavuzda aynı Azure depolama hesabına başvurur.
- İki veri kümesi. Giriş veri kümesi ve çıkış veri kümesi. Bu kılavuzda, her ikisi de aynı blob kapsayıcısını kullanır, ancak farklı klasörlere (giriş ve çıkış) bakın.
- İşlem hattı. İşlem hattı, bir Azure Blob konumundan başka bir Azure Blob konumuna veri kopyalamak için bir blob kaynağı ve bir blob havuzu kullanan bir kopyalama etkinliği içerir.

Aşağıdaki bölümlerde bu varlıklar hakkında daha fazla bilgi sağlanmaktadır.

#### <a name="linked-services"></a>Bağlı hizmetler
İki bağlı hizmet görmeniz gerekir. Biri kaynak ve diğeri için bir tane. Bu kılavuzda, her iki tanım de adlar hariç aynı şekilde görünür. Bağlı hizmetin **türü** **azurestorage**olarak ayarlanır. Bağlı hizmet tanımının en önemli özelliği, çalışma zamanında Azure depolama hesabınıza bağlanmak için Data Factory tarafından kullanılan **ConnectionString**'dir. Tanımda hubName özelliğini yoksayın.

##### <a name="source-blob-storage-linked-service"></a>Kaynak BLOB depolama bağlı hizmeti
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Hedef BLOB depolama bağlı hizmeti

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Azure depolama bağlı hizmeti hakkında daha fazla bilgi için bkz. [bağlı hizmet özellikleri](#linked-service-properties) bölümü.

#### <a name="datasets"></a>Veri kümeleri
İki veri kümesi vardır: giriş veri kümesi ve çıkış veri kümesi. Veri kümesinin türü her ikisi için **AzureBlob** olarak ayarlanır.

Giriş veri kümesi, **adfblobconnector** blob kapsayıcısının **giriş** klasörünü işaret eder. Veriler, bu veri kümesini giriş olarak alan kopyalama etkinliği ile işlem hattı tarafından üretilmediğinden, bu veri kümesi için **External** özelliği **true** olarak ayarlanır.

Çıktı veri kümesi aynı blob kapsayıcısının **Çıkış** klasörünü işaret eder. Çıktı veri kümesi aynı zamanda, çıktı dosyasının yolunu dinamik olarak değerlendirmek için, aynı zamanda, **Festart** sistem değişkeninin yılını, ayını ve gününü kullanır. Data Factory tarafından desteklenen işlevlerin ve sistem değişkenlerinin listesi için bkz. [Data Factory işlevleri ve sistem değişkenleri](data-factory-functions-variables.md). Bu veri kümesi ardışık düzen tarafından üretildiği için **dış** özellik **false** (varsayılan değer) olarak ayarlanır.

Azure blob veri kümesi tarafından desteklenen özellikler hakkında daha fazla bilgi için bkz. [veri kümesi özellikleri](#dataset-properties) bölümü.

##### <a name="input-dataset"></a>Giriş veri kümesi

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Çıkış veri kümesi

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>İşlem hattı
İşlem hattının tek bir etkinliği vardır. Etkinliğin **türü** **kopya**olarak ayarlanır. Etkinliğin tür özelliklerinde, biri kaynak ve diğeri havuz için olmak üzere iki bölüm vardır. Etkinlik bir blob depolamadan veri kopyalarken, kaynak türü **Blobsource** olarak ayarlanır. BLOB depolama alanına veri kopyalarken, havuz türü **Blobsink** olarak ayarlanır. Kopyalama etkinliği, giriş ve OutputDataset-z4y olarak ınputdataset-z4y alır.

BlobSource ve BlobSink tarafından desteklenen özellikler hakkında daha fazla bilgi için bkz. [kopyalama etkinliği özellikleri](#copy-activity-properties) bölümü.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>BLOB depolama alanına ve BLOB depolamadan veri kopyalamaya yönelik JSON örnekleri
Aşağıdaki örnekler, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Azure Blob depolama ve Azure SQL veritabanı 'na veri kopyalamayı gösterir. Ancak, veriler, Azure Data Factory ' deki kopyalama etkinliği kullanılarak, [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir kaynaktan herhangi bir kaynağa **doğrudan** kopyalanabilir.

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON örneği: blob depolamadan SQL veritabanına veri kopyalama
Aşağıdaki örnek şunu gösterir:

1. [Azuressqldatabase](data-factory-azure-sql-connector.md#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](#linked-service-properties)türünde bağlı bir hizmet.
3. [AzureBlob](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [Azuressqltable](data-factory-azure-sql-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [Blobsource](#copy-activity-properties) ve [Sqlsink](data-factory-azure-sql-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, saat serisi verilerini bir Azure blobundan saatlik bir Azure SQL tablosuna kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure SQL bağlı hizmeti:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure depolama bağlı hizmeti:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory, iki tür Azure Storage bağlı hizmetini destekler: **Azurestorage** ve **Azurestorampasas**. Birincisi için, hesap anahtarını ve sonraki bir sürümü içeren bağlantı dizesini belirtirsiniz, paylaşılan erişim Imzası (SAS) URI 'sini belirtirsiniz. Ayrıntılar için [bağlı hizmetler](#linked-service-properties) bölümüne bakın.

**Azure Blob giriş veri kümesi:**

Veriler her saat yeni bir bloba alınır (sıklık: saat, Aralık: 1). Blob için klasör yolu ve dosya adı, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay ve gün bölümünü ve dosya adını kullanır başlangıç zamanının saat kısmını kullanır. "External": "true" ayarı, tablonun veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory bildirir.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL çıkış veri kümesi:**

Örnek, verileri bir Azure SQL veritabanında "MyTable" adlı bir tabloya kopyalar. Blob CSV dosyasının içermesini istediğiniz sütun sayısıyla aynı sayıda sütunla Azure SQL Veritabanınızda tablo oluşturun. Yeni satırlar tabloya her saat eklenir.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Blob kaynağı ve SQL havuzu içeren bir işlem hattındaki kopyalama etkinliği:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **blobsource** olarak ayarlanır ve **Havuz** türü **sqlsink**olarak ayarlanır.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON örneği: Azure SQL 'den Azure Blob 'a veri kopyalama
Aşağıdaki örnek şunu gösterir:

1. [Azuressqldatabase](data-factory-azure-sql-connector.md#linked-service-properties)türünde bağlı bir hizmet.
2. [Azurestorage](#linked-service-properties)türünde bağlı bir hizmet.
3. [Azuressqltable](data-factory-azure-sql-connector.md#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
4. [AzureBlob](#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
5. [SQLSource](data-factory-azure-sql-connector.md#copy-activity-properties) ve [Blobsink](#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

Örnek, saat serisi verilerini bir Azure SQL tablosundan Azure blobuna saatlik olarak kopyalar. Bu örneklerde kullanılan JSON özellikleri, örnekleri takip eden bölümlerde açıklanmıştır.

**Azure SQL bağlı hizmeti:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure depolama bağlı hizmeti:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory, iki tür Azure Storage bağlı hizmetini destekler: **Azurestorage** ve **Azurestorampasas**. Birincisi için, hesap anahtarını ve sonraki bir sürümü içeren bağlantı dizesini belirtirsiniz, paylaşılan erişim Imzası (SAS) URI 'sini belirtirsiniz. Ayrıntılar için [bağlı hizmetler](#linked-service-properties) bölümüne bakın.

**Azure SQL giriş veri kümesi:**

Örnek, Azure SQL 'de bir "MyTable" tablosu oluşturduğunuzu ve zaman serisi verileri için "timestampcolumn" adlı bir sütun içerdiğini varsayar.

"External": "true" ayarı, tablonun veri fabrikasında dış olduğunu ve veri fabrikasında bir etkinlik tarafından üretilmediğini Data Factory.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure Blob çıktı veri kümesi:**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1). Blob 'un klasör yolu, işlenmekte olan dilimin başlangıç zamanına göre dinamik olarak değerlendirilir. Klasör yolu başlangıç zamanının yıl, ay, gün ve saat kısımlarını kullanır.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**SQL kaynağı ve BLOB havuzu içeren bir işlem hattındaki kopyalama etkinliği:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. İşlem hattı JSON tanımında **kaynak** türü **SQLSource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır. **Sqlreaderquery** özelliği IÇIN belirtilen SQL sorgusu, kopyalamanın Son saatteki verilerini seçer.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
