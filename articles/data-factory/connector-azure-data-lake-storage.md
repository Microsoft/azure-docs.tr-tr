---
title: Data Factory kullanarak Azure Data Lake Storage 2. verileri kopyalama ve dönüştürme
description: Azure Data Lake Storage 2. veri kopyalamayı ve Azure Data Factory kullanarak Azure Data Lake Storage 2. verileri dönüştürmeyi öğrenin.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: fb21dbbe087f4dd1c210af1afbba19ba9df1242a
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076787"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Data Lake Storage 2. verileri kopyalama ve dönüştürme

Azure Data Lake Storage 2. (ADLS 2.), [Azure Blob depolamada](../storage/blobs/storage-blobs-introduction.md)yerleşik olan büyük veri analizlerine adanmış bir dizi özellik kümesidir. Hem dosya sistemi hem de nesne depolama paradigmalarına kullanarak verilerinize arabirim atamak için kullanabilirsiniz.

Bu makalede, verileri Azure Data Lake Storage 2. kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir ve Azure Data Lake Storage 2. verileri dönüştürmek için veri akışı kullanılır. Azure Data Factory hakkında bilgi edinmek için [giriş makalesi](introduction.md).

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure Data Lake Storage 2. Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)
- [Etkinliği sil](delete-activity.md)

Kopyalama etkinliği için, bu bağlayıcıyla şunları yapabilirsiniz:

- Azure kaynakları kimlik doğrulamaları için hesap anahtarı, hizmet sorumlusu veya yönetilen kimlikler kullanarak verileri/-Azure Data Lake Storage 2. kopyalayın.
- Dosyaları olduğu gibi kopyalayın veya [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs.md)ile dosyaları ayrıştırır veya oluşturun.

>[!IMPORTANT]
>Azure Storage güvenlik duvarı ayarlarında **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver** seçeneğini etkinleştirip Data Lake Storage 2. bağlanmak için Azure tümleştirme çalışma zamanı 'nı kullanmak istiyorsanız, [yönetilen kimlik kullanmanız gerekir ADLS 2. için kimlik doğrulaması](#managed-identity) .

>[!TIP]
>Hiyerarşik ad alanını etkinleştirirseniz, şu anda blob ve Data Lake Storage 2. API 'Ler arasındaki işlemlerden birlikte çalışabilirlik özelliği yoktur. "ErrorCode = FilesystemNotFound" hatasını "belirtilen dosya sistemi yok" iletisiyle karşılaşırsanız, bunun nedeni API 'nin başka bir yerinde Data Lake Storage 2., blob API 'SI aracılığıyla oluşturulmuş belirtilen havuz dosya sistemidir. Sorunu onarmak için, bir blob kapsayıcısının adı olarak mevcut olmayan yeni bir dosya sistemi belirtin. Data Factory, veri kopyalama sırasında otomatik olarak bu dosya sistemini oluşturur.

## <a name="get-started"></a>Başlarken

>[!TIP]
>Data Lake Storage 2. bağlayıcısını nasıl kullanacağınızı öğrenmek için bkz. [Azure Data Lake Storage 2. verileri yükleme](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler Data Lake Storage 2. özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında bilgiler sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri

Azure Data Lake Storage 2. Bağlayıcısı aşağıdaki kimlik doğrulama türlerini destekler. Ayrıntılar için ilgili bölümlere bakın:

- [Hesap anahtarı kimlik doğrulaması](#account-key-authentication)
- [Hizmet sorumlusu kimlik doğrulaması](#service-principal-authentication)
- [Azure kaynaklarında kimlik doğrulaması için yönetilen kimlik](#managed-identity)

>[!NOTE]
>SQL veri ambarı 'na veri yüklemek için PolyBase kullanılırken, kaynak Data Lake Storage 2. sanal ağ uç noktası ile yapılandırıldıysa, PolyBase için gereken yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. Daha fazla yapılandırma önkoşulları ile [yönetilen kimlik doğrulama](#managed-identity) bölümüne bakın.

### <a name="account-key-authentication"></a>Hesap anahtarı kimlik doğrulaması

Depolama hesabı anahtarı kimlik doğrulaması kullanmak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği ayarlanmalıdır **AzureBlobFS**. |Yes |
| url | `https://<accountname>.dfs.core.windows.net`düzenine sahip Data Lake Storage 2. için uç nokta. | Yes |
| accountKey | Data Lake Storage 2. için hesap anahtarı. Data Factory'de güvenle depolamak için bir SecureString olarak bu alanı işaretleyin veya [Azure Key Vault'ta depolanan bir gizli dizi başvuru](store-credentials-in-key-vault.md). |Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) veri deposuna bağlanmak için kullanılacak. Veri depolubir özel ağda ise Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Bu özellik belirtilmezse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |Hayır |

>[!NOTE]
>Hesap anahtarı kimlik doğrulaması kullanılırken ikincil ADLS dosya sistemi uç noktası desteklenmez. Diğer kimlik doğrulama türlerini kullanabilirsiniz.

**Örnek:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması

Hizmet sorumlusu kimlik doğrulamasını kullanmak için aşağıdaki adımları izleyin.

1. Uygulamanızı [bir Azure AD kiracısıyla kaydetme](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)bölümündeki adımları izleyerek Azure Active Directory (Azure AD) uygulamasına bir uygulama varlığı kaydedin. Bağlı hizmetini tanımlamak için kullandığınız şu değerleri not edin:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

2. Hizmet sorumlusu uygun iznini verin. [Dosya ve dizinlerdeki erişim denetim listelerinden](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) Data Lake Storage 2. izin nasıl çalıştığına ilişkin örneklere bakın

    - **Kaynak olarak**: Depolama Gezgini ' de, tüm yukarı akış klasörleri ve dosya sistemi için en az **yürütme** iznini, kopyalanacak dosyalar için de **Oku** iznini verin. Alternatif olarak, erişim denetimi 'nde (ıAM) en azından **Depolama Blobu veri okuyucu** rolü verin.
    - **Havuz olarak**: Depolama Gezgini IÇINDE, tüm yukarı akış klasörleri ve dosya sistemi için, havuz klasörü için **yazma** Izniyle birlikte en az **yürütme** izni verin. Alternatif olarak, erişim denetimi 'nde (ıAM), en azından **Depolama Blobu veri katılımcısı** rolüne izin verin.

>[!NOTE]
>Yazmak için Data Factory Kullanıcı arabirimini kullanırsanız ve hizmet sorumlusu, ıAM 'de "Depolama Blobu veri okuyucusu/katkıda bulunan" rolüyle ayarlanmamışsa, test bağlantısı veya tarama/gezinme sırasında "dosya yoluna Bağlantıyı Sına" veya "belirtilen yoldan göz at" seçeneğini belirleyin ve devam etmek için yürütme iznine sahip bir dosya sistemi veya yol belirtin.

Bu özellikler bağlı hizmet için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği ayarlanmalıdır **AzureBlobFS**. |Yes |
| url | `https://<accountname>.dfs.core.windows.net`düzenine sahip Data Lake Storage 2. için uç nokta. | Yes |
| servicePrincipalId | Uygulamanın istemci kimliği belirtin. | Yes |
| servicePrincipalKey | Uygulama anahtarını belirtin. Bu alanı, Data Factory güvenli bir şekilde depolamak için `SecureString` olarak işaretleyin. Veya [Azure Key Vault depolanan bir gizli](store-credentials-in-key-vault.md)dizi ile başvurabilirsiniz. | Yes |
| tenant | Kiracı bilgileri (etki alanı adı veya Kiracı kimliği), uygulamanızın bulunduğu altında belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) veri deposuna bağlanmak için kullanılacak. Veri depolubir özel ağda ise Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |Hayır |

**Örnek:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure kaynaklarında kimlik doğrulaması için yönetilen kimlik

Veri Fabrikası ile ilişkilendirilebilen bir [yönetilen Azure kaynakları için kimliği](data-factory-service-identity.md), bu belirli veri fabrikası temsil eder. Bu yönetilen kimliği, kendi hizmet sorumlunuzu kullanmaya benzer şekilde Data Lake Storage 2. kimlik doğrulaması için doğrudan kullanabilirsiniz. Bu belirlenen fabrikasının Data Lake Storage 2. erişmesine ve bu verilere veri kopyalamasına izin verir.

Azure Kaynak kimlik doğrulaması için yönetilen kimlikleri kullanmak için aşağıdaki adımları izleyin.

1. Fabrikalarınızla birlikte oluşturulan **hizmet kimliği uygulama kimliği** değerini kopyalayarak [Data Factory yönetilen kimlik bilgilerini alın](data-factory-service-identity.md#retrieve-managed-identity) .

2. Yönetilen kimliğe uygun izni verin. [Dosya ve dizinlerdeki erişim denetim listelerinden](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)Data Lake Storage 2. iznin nasıl çalıştığına ilişkin örneklere bakın.

    - **Kaynak olarak**: Depolama Gezgini ' de, tüm yukarı akış klasörleri ve dosya sistemi için en az **yürütme** iznini, kopyalanacak dosyalar için de **Oku** iznini verin. Alternatif olarak, erişim denetimi 'nde (ıAM) en azından **Depolama Blobu veri okuyucu** rolü verin.
    - **Havuz olarak**: Depolama Gezgini IÇINDE, tüm yukarı akış klasörleri ve dosya sistemi için, havuz klasörü için **yazma** Izniyle birlikte en az **yürütme** izni verin. Alternatif olarak, erişim denetimi 'nde (ıAM), en azından **Depolama Blobu veri katılımcısı** rolüne izin verin.

>[!NOTE]
>Yazmak için Data Factory Kullanıcı arabirimini kullanıyorsanız ve yönetilen kimlik, ıAM 'de "Depolama Blobu veri okuyucusu/katkıda bulunan" rolü ile ayarlanmamışsa, test bağlantısı veya tarama/gezinme sırasında "dosya yoluna Bağlantıyı Sına" veya "belirtilen yoldan göz at" seçeneğini belirleyin ve devam etmek için yürütme iznine sahip bir dosya sistemi veya yol belirtin.

>[!IMPORTANT]
>Data Lake Storage 2. verileri SQL veri ambarı 'na yüklemek için PolyBase kullanırsanız, [Data Lake Storage 2. için yönetilen](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) kimlik kimlik doğrulamasını kullanırken, 1. ve 2. adımları izleyin. Azure Active Directory (Azure AD) ve 2) SQL veritabanı sunucunuza Depolama Blobu veri katılımcısı rolünü atayın; Rest Data Factory tarafından işlenir. Data Lake Storage 2. Azure sanal ağ uç noktası ile yapılandırıldıysa, verileri dosyadan yüklemek için PolyBase kullanmak istiyorsanız, PolyBase 'in gerektirdiği şekilde yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir.

Bu özellikler bağlı hizmet için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği ayarlanmalıdır **AzureBlobFS**. |Yes |
| url | `https://<accountname>.dfs.core.windows.net`düzenine sahip Data Lake Storage 2. için uç nokta. | Yes |
| connectVia | [Integration runtime](concepts-integration-runtime.md) veri deposuna bağlanmak için kullanılacak. Veri depolubir özel ağda ise Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |Hayır |

**Örnek:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md).

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Biçim tabanlı veri kümesindeki `location` ayarları altında Data Lake Storage 2. için aşağıdaki özellikler desteklenir:

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Veri kümesindeki `location` seçeneğinin altında bulunan tür özelliği **AzureBlobFSLocation**olarak ayarlanmalıdır. | Yes      |
| fileSystem | Data Lake Storage 2. dosya sistemi adı.                              | Hayır       |
| folderPath | Verilen dosya sistemi altındaki bir klasörün yolu. Klasörleri filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları 'nda belirtin. | Hayır       |
| fileName   | Verilen fileSystem + folderPath altındaki dosya adı. Dosyaları filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları 'nda belirtin. | Hayır       |

**Örnek:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="legacy-dataset-model"></a>Eski veri kümesi modeli

>[!NOTE]
>Aşağıdaki veri kümesi modeli, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. Yukarıdaki bölümde bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Dataset öğesinin type özelliği ayarlanmalıdır **AzureBlobFSFile**. |Yes |
| folderPath | Data Lake Storage 2. klasörünün yolu. Belirtilmezse, kök dizinine işaret eder. <br/><br/>Joker karakter filtresi desteklenir. İzin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). Asıl klasör adınızın bir joker karakter varsa veya bu kaçış karakteri içindeyse çıkış için `^` kullanın. <br/><br/>Örnekler: FileSystem/Folder/. [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. |Hayır |
| fileName | Belirtilen "folderPath" altındaki dosyalar için ad veya joker karakter filtresi. Bu özellik için bir değer belirtmezseniz, klasördeki tüm dosyaları için veri kümesini işaret eder. <br/><br/>Filtre için, izin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir).<br/>-Örnek 1: `"fileName": "*.csv"`<br/>-Örnek 2: `"fileName": "???20180427.txt"`<br/>Gerçek dosya adınızın bir joker karakter varsa veya bu kaçış karakteri içindeyse çıkış için `^` kullanın.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde ve bir etkinlik havuzunda **Preservehierarchy** belirtilmemişse, Copy etkinliği dosya adını aşağıdaki Düzenle otomatik olarak oluşturur: "*Data. [ Etkinlik çalıştırma KIMLIĞI GUID 'SI]. [DÜZEDEN hiyerarşi varsa GUID]. [yapılandırıldıysa Biçimlendir]. [yapılandırıldıysa sıkıştırma]* "Örneğin," Data. 0a405f8a-93ff-4c6f-B3BE-f69616f1df7a. txt. gz ". Sorgu yerine tablo adı kullanarak tablosal kaynaktan kopyalama yaparsanız, ad deseninin " *[tablo adı]" olması gerekir. [ Biçim]. [yapılandırıldıysa sıkıştırma]* "Örneğin," MyTable. csv ". |Hayır |
| modifiedDatetimeStart | Son değiştirilen özniteliğe göre dosya filtresi. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Veri hareketinin genel performansı, çok büyük miktarlarda dosya filtresi yapmak istediğinizde bu ayarın etkinleştirilmesiyle etkilenir. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir. `modifiedDatetimeStart` bir tarih saat değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden büyük veya buna eşit olan dosyalar seçilir. `modifiedDatetimeEnd` bir tarih saat değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| Hayır |
| modifiedDatetimeEnd | Son değiştirilen özniteliğe göre dosya filtresi. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Veri hareketinin genel performansı, çok büyük miktarlarda dosya filtresi yapmak istediğinizde bu ayarın etkinleştirilmesiyle etkilenir. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir. `modifiedDatetimeStart` bir tarih saat değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden büyük veya buna eşit olan dosyalar seçilir. `modifiedDatetimeEnd` bir tarih saat değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| Hayır |
| format | (İkili kopya) depoları arasında dosya tabanlı olduğu gibi dosyaları kopyalamak girdi ve çıktı veri kümesi tanımları biçimi bölümüne atlayın.<br/><br/>Ayrıştırma veya belirli bir biçime sahip dosyaları oluşturmak istiyorsanız, aşağıdaki dosya biçimi türleri desteklenir: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, ve **ParquetFormat**. Ayarlama **türü** özelliği altında **biçimi** şu değerlerden biri olarak. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs.md#json-format), [avro Format](supported-file-formats-and-compression-codecs.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| compression | Veri sıkıştırma düzeyi ve türünü belirtin. Daha fazla bilgi için [desteklenen dosya biçimleri ve codec sıkıştırma](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Desteklenen türler **GZip**, **Deflate**, **Bzıp2**, ve **ZipDeflate**.<br/>Desteklenen düzeyleri **Optimal** ve **en hızlı**. |Hayır |

>[!TIP]
>Bir klasör altındaki tüm dosyaları kopyalamak için belirtin **folderPath** yalnızca.<br>Belirli bir ada sahip tek bir dosyayı kopyalamak için, klasör **bölüm ve dosya** adı ile bir dosya adı ile **FolderPath** öğesini belirtin.<br>Bir klasör altındaki bir dosya alt kümesini kopyalamak için, bir klasör bölümü ve **dosya adı** ile bir joker karakter filtresi içeren **FolderPath** öğesini belirtin. 

**Örnek:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [kopyalama etkinliği yapılandırması](copy-activity-overview.md#configuration) ve işlem [hatları ve etkinlikleri](concepts-pipelines-activities.md). Bu bölümde, Data Lake depolama 2. nesil kaynak ve havuz desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Kaynak türü olarak Azure Data Lake Storage 2.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Biçim tabanlı kopyalama kaynağında `storeSettings` ayarları altında Data Lake Storage 2. için aşağıdaki özellikler desteklenir:

| Özellik                 | Açıklama                                                  | Gerekli                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` altındaki tür özelliği **AzureBlobFSReadSetting**olarak ayarlanmalıdır. | Yes                                           |
| recursive                | Belirtilen klasörün alt klasörleri ya da yalnızca veri yinelemeli olarak okunur olup olmadığını belirtir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir deposa, havuzda boş bir klasör veya alt klasör kopyalanmaz veya oluşturulmaz. İzin verilen değerler **true** (varsayılan) ve **false**. | Hayır                                            |
| Yavaya Cardfolderpath       | Kaynak klasörleri filtrelemek için veri kümesinde yapılandırılan belirtilen dosya sistemi altındaki joker karakterlerle klasör yolu. <br>İzin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). Asıl klasör adınızın bir joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. <br>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Hayır                                            |
| Yavaya Cardfilename         | Kaynak dosyaları filtrelemek için, belirtilen dosya sistemi + folderPath/, Cardfolderpath altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). Asıl klasör adınızın bir joker karakter veya içinde bu kaçış karakteri varsa çıkmak için `^` kullanın. [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Veri kümesinde `fileName` belirtilmemişse Evet |
| modifiedDatetimeStart    | Son değiştirilen özniteliğe göre dosya filtresi. Son değiştirilme zamanı `modifiedDatetimeStart` ve `modifiedDatetimeEnd`arasındaki zaman aralığı içinde ise dosyalar seçilir. Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir. `modifiedDatetimeStart` bir tarih saat değeri olduğunda ancak `modifiedDatetimeEnd` NULL olduğunda, son değiştirilen özniteliği DateTime değerinden büyük veya buna eşit olan dosyaların seçildiği anlamına gelir. `modifiedDatetimeEnd` bir tarih saat değeri olduğunda ancak `modifiedDatetimeStart` NULL olduğunda, son değiştirilen özniteliği tarih saat değerinden küçük olan dosyaların seçildiği anlamına gelir. | Hayır                                            |
| modifiedDatetimeEnd      | Yukarıdaki gibi.                                               | Hayır                                            |
| maxConcurrentConnections | Depolama deposuna aynı anda bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır                                            |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="legacy-source-model"></a>Eski kaynak modeli

>[!NOTE]
>Aşağıdaki kopya kaynak modeli, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. Yukarıdaki ileri giderek bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağı öğesinin type özelliği ayarlanmalıdır **AzureBlobFSSource**. |Yes |
| recursive | Belirtilen klasörün alt klasörleri ya da yalnızca veri yinelemeli olarak okunur olup olmadığını belirtir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir deposa, havuzda boş bir klasör veya alt klasör kopyalanmaz veya oluşturulmaz.<br/>İzin verilen değerler **true** (varsayılan) ve **false**. | Hayır |
| maxConcurrentConnections | Aynı anda veri deposuna bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Bir havuz türü olarak Azure Data Lake depolama Gen2

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Biçim tabanlı kopya havuzunda `storeSettings` ayarları altında Data Lake Storage 2. için aşağıdaki özellikler desteklenir:

| Özellik                 | Açıklama                                                  | Gerekli |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` altındaki tür özelliği **AzureBlobFSWriteSetting**olarak ayarlanmalıdır. | Yes      |
| copyBehavior             | Kaynak dosyaları bir dosya tabanlı veri deposundan olduğunda kopyalama davranışını tanımlar.<br/><br/>İzin verilen değerler şunlardır:<br/><b>-(Varsayılan) PreserveHierarchy</b>: hedef klasördeki ise dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu, hedef dosyanın göreli yoluyla hedef klasöre aynıdır.<br/><b>-FlattenHierarchy</b>: kaynak klasördeki tüm dosyaları ilk hedef klasörün içinde düzeyindedir. Hedef dosyalar otomatik olarak oluşturulan adlarına sahip. <br/><b>-MergeFiles</b>: tüm dosyaları kaynak klasörden bir dosya birleştirir. Dosya adı belirtilirse, birleştirilmiş dosya adı belirtilen adıdır. Aksi takdirde, bir otomatik olarak oluşturulan dosya adı değil. | Hayır       |
| maxConcurrentConnections | Aynı anda veri deposuna bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır       |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="legacy-sink-model"></a>Eski havuz modeli

>[!NOTE]
>Aşağıdaki kopya havuzu modeli, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. Yukarıdaki ileri giderek bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuz öğesinin type özelliği ayarlanmalıdır **AzureBlobFSSink**. |Yes |
| copyBehavior | Kaynak dosyaları bir dosya tabanlı veri deposundan olduğunda kopyalama davranışını tanımlar.<br/><br/>İzin verilen değerler şunlardır:<br/><b>-(Varsayılan) PreserveHierarchy</b>: hedef klasördeki ise dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu, hedef dosyanın göreli yoluyla hedef klasöre aynıdır.<br/><b>-FlattenHierarchy</b>: kaynak klasördeki tüm dosyaları ilk hedef klasörün içinde düzeyindedir. Hedef dosyalar otomatik olarak oluşturulan adlarına sahip. <br/><b>-MergeFiles</b>: tüm dosyaları kaynak klasörden bir dosya birleştirir. Dosya adı belirtilirse, birleştirilmiş dosya adı belirtilen adıdır. Aksi takdirde, bir otomatik olarak oluşturulan dosya adı değil. | Hayır |
| maxConcurrentConnections | Aynı anda veri deposuna bağlanacak bağlantı sayısı. Yalnızca veri deposuyla eşzamanlı bağlantıyı sınırlandırmak istediğinizde belirtin. | Hayır |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Klasör ve dosya filtresi örnekleri

Bu bölümde, klasör yolu ve dosya adının joker karakter filtreleriyle elde edilen davranışı açıklanmaktadır.

| folderPath | fileName | recursive | Kaynak klasör yapısı ve filtre sonucu ( **kalın** olan dosyalar alınır)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Boş, Varsayılanı kullan) | yanlış | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**dosya2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | (Boş, Varsayılanı kullan) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**dosya2. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4. JSON**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | yanlış | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3. csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**FILE1. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3. csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4. JSON<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5. csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6. csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Özyinelemeli ve copyBehavior örnekler

Bu bölümde, özyinelemeli ve copyBehavior değerlerinin farklı birleşimleri için kopyalama işleminin ortaya çıkan davranışı açıklanmaktadır.

| recursive | copyBehavior | Kaynak klasör yapısı | Sonuçta elde edilen hedef |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1, kaynak ile aynı yapıyla oluşturulur:<br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapısı ile oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fıle1'de otomatik olarak oluşturulan adı<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya3 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 için otomatik olarak oluşturulan ad |
| true |mergeFiles | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapısı ile oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de dosya2 + dosya3 + File4 + File5 içeriği otomatik olarak oluşturulan dosya adıyla bir dosya halinde birleştirilir. |
| yanlış |preserveHierarchy | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapısı ile oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte çekilmez. |
| yanlış |flattenHierarchy | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapısı ile oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;fıle1'de otomatik olarak oluşturulan adı<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2 için otomatik olarak oluşturulan ad<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte çekilmez. |
| yanlış |mergeFiles | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Dosya3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapısı ile oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1'de + dosya2 içeriği otomatik olarak oluşturulan dosya adıyla bir dosya halinde birleştirilir. fıle1'de otomatik olarak oluşturulan adı<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte çekilmez. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Data Lake Storage 1. ACL 'Leri koru

>[!TIP]
>Azure Data Lake Storage 1. verileri genel olarak Gen2 'e kopyalamak için bkz. bir adım adım ve en iyi uygulamalar için [Azure Data Factory ile Azure Data Lake Storage 1. verileri Gen2 'e kopyalama](load-azure-data-lake-storage-gen2-from-gen1.md) .

Dosyaları Azure Data Lake Storage 1. 'den Gen2 'e kopyaladığınızda, POSIX erişim denetim listelerini (ACL 'Ler) verilerle birlikte korumayı seçebilirsiniz. Access Control hakkında daha fazla bilgi için Azure Data Lake Storage 2. Azure Data Lake Storage 1. ve [erişim denetimi](../storage/blobs/data-lake-storage-access-control.md) [' nde erişim denetimi](../data-lake-store/data-lake-store-access-control.md) ' ne bakın.

Aşağıdaki ACL türleri Azure Data Factory kopyalama etkinliği kullanılarak korunabilir. Bir veya daha fazla tür seçebilirsiniz:

- **ACL**: dosya ve dizinlerde POSIX erişim denetim listelerini kopyalayın ve koruyun. Var olan tüm ACL 'Leri kaynaktan havuza kopyalar. 
- **Sahip**: dosya ve dizinlerin sahip olduğu kullanıcıyı kopyalayın ve koruyun. Data Lake Storage 2. havuza Süper Kullanıcı erişimi gerekiyor.
- **Grup**: sahip olan dosya ve dizinlerin grubunu kopyalayın ve koruyun. Havuz Data Lake Storage 2. veya sahip olan kullanıcıya yönelik süper kullanıcı erişimi (sahip olan kullanıcı aynı zamanda hedef grubun üyesiyse) gerekli.

Bir klasörden kopyalamayı belirtirseniz Data Factory, bu klasör için ACL 'Leri ve `recursive` doğru olarak ayarlandıysa, altındaki dosyaları ve dizinleri çoğaltır. Tek bir dosyadan kopyalamayı belirtirseniz, bu dosyadaki ACL 'Ler kopyalanır.

>[!IMPORTANT]
>ACL 'Leri korumayı seçtiğinizde, Data Factory havuz Data Lake Storage 2. hesabınızda çalışması için yeterince yüksek izinler verdiğinizden emin olun. Örneğin, hesap anahtarı kimlik doğrulamasını kullanın veya Depolama Blobu veri sahibi rolünü hizmet sorumlusu veya yönetilen kimliğe atayın.

Kaynağı ikili kopyalama seçeneği veya ikili biçimi ve havuz Data Lake Storage 2. ikili kopya seçeneği veya ikili biçimle Data Lake Storage 1. olarak yapılandırdığınızda, **veri kopyalama aracı ayarları** sayfasında veya üzerinde **koru** seçeneğini bulabilirsiniz. Etkinlik yazma için **kopyalama etkinliği** > **ayarları** sekmesi.

![ACL 'yi korumak için Data Lake Storage 1. Gen2](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

JSON yapılandırmasına bir örnek aşağıda verilmiştir (bkz. `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışı eşleme özelliğinde [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüştürme](data-flow-sink.md) hakkında daha fazla bilgi edinin.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="delete-activity-properties"></a>Etkinlik özelliklerini Sil

Özelliklerle ilgili ayrıntıları öğrenmek için [silme etkinliği](delete-activity.md) onay
## <a name="next-steps"></a>Sonraki adımlar

Veri fabrikasında kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md##supported-data-stores-and-formats).
