---
title: Azure Data Lake Storage 2. verileri kopyalama ve dönüştürme
description: Azure Data Lake Storage 2. veri kopyalamayı ve Azure Data Factory kullanarak Azure Data Lake Storage 2. verileri dönüştürmeyi öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 7a501a86f979bb508052c8957627ebfa7950fd63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597615"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Data Lake Storage 2. verileri kopyalama ve dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage 2. (ADLS 2.), [Azure Blob depolamada](../storage/blobs/storage-blobs-introduction.md)yerleşik olan büyük veri analizlerine adanmış bir dizi özellik kümesidir. Bu hizmet sayesinde hem dosya sistemi hem de nesne depolama alanı yaklaşımlarını kullanarak verilerinize arabirim oluşturabilirsiniz.

Bu makalede Azure Data Lake Storage 2. Nesil'e ve oradan veri kopyalamak için Azure Data Factory’nin Kopya Etkinliğini kullanma ve Azure Data Lake Storage 2. Nesil'deki verileri dönüştürmek için Veri Akışı’nı kullanma adımları açıklanır. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

>[!TIP]
>Data Lake veya veri ambarı geçiş senaryosunda, veri [Gölü veya veri Ambarınızdan Azure 'a veri geçirmek için Azure Data Factory kullanma](data-migration-guidance-overview.md)hakkında daha fazla bilgi edinin.

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
- [Kopyalama sırasında dosya meta verilerini koru](#preserve-metadata-during-copy).
- Azure Data Lake Storage 1./Gen2 'tan kopyalarken [ACL 'Leri koru](#preserve-acls) .

## <a name="get-started"></a>başlarken

>[!TIP]
>Data Lake Storage 2. bağlayıcısını nasıl kullanacağınızı öğrenmek için bkz. [Azure Data Lake Storage 2. verileri yükleme](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler Data Lake Storage 2. özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında bilgiler sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Azure Data Lake Storage 2. Bağlayıcısı aşağıdaki kimlik doğrulama türlerini destekler. Ayrıntılar için ilgili bölümlere bakın:

- [Hesap anahtarı kimlik doğrulaması](#account-key-authentication)
- [Hizmet sorumlusu kimlik doğrulaması](#service-principal-authentication)
- [Azure kaynakları kimlik doğrulaması için Yönetilen kimlikler](#managed-identity)

>[!NOTE]
>- Azure Storage güvenlik duvarında etkin **Microsoft hizmetlerinin bu depolama hesabına erişmesine Izin ver** seçeneğini kullanarak Data Lake Storage 2. bağlanmak Için Genel Azure tümleştirme çalışma zamanını kullanmak istiyorsanız, [yönetilen kimlik kimlik doğrulamasını](#managed-identity)kullanmanız gerekir.
>- Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase veya COPY ifadesini kullandığınızda, kaynak veya hazırlama Data Lake Storage 2. bir Azure sanal ağ uç noktasıyla yapılandırılmışsa, SYNAPSE tarafından gereken yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. Daha fazla yapılandırma önkoşulları ile [yönetilen kimlik doğrulama](#managed-identity) bölümüne bakın.

### <a name="account-key-authentication"></a>Hesap anahtarı kimlik doğrulaması

Depolama hesabı anahtarı kimlik doğrulamasını kullanmak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **AzureBlobFS** olarak ayarlanmalıdır. |Yes |
| url | Düzenine sahip Data Lake Storage 2. için uç nokta `https://<accountname>.dfs.core.windows.net` . | Yes |
| accountKey | Data Lake Storage 2. için hesap anahtarı. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. |Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Veri depolubir özel ağda ise Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Bu özellik belirtilmezse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |No |

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

1. Uygulamanızı [bir Azure AD kiracısıyla kaydetme](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)bölümündeki adımları izleyerek Azure Active Directory (Azure AD) uygulamasına bir uygulama varlığı kaydedin. Bağlı hizmeti tanımlamak için kullandığınız aşağıdaki değerleri unutmayın:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

2. Hizmet sorumlusu uygun iznini verin. [Dosya ve dizinlerdeki erişim denetim listelerinden](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories) Data Lake Storage 2. izin nasıl çalıştığına ilişkin örneklere bakın

    - **Kaynak olarak**: Depolama Gezgini ' de, tüm yukarı akış klasörleri ve dosya sistemi için en az **yürütme** iznini, kopyalanacak dosyalar için de **Oku** iznini verin. Alternatif olarak, erişim denetimi 'nde (ıAM) en azından **Depolama Blobu veri okuyucu** rolü verin.
    - **Havuz olarak**: Depolama Gezgini IÇINDE, tüm yukarı akış klasörleri ve dosya sistemi için, havuz klasörü için **yazma** Izniyle birlikte en az **yürütme** izni verin. Alternatif olarak, erişim denetimi 'nde (ıAM), en azından **Depolama Blobu veri katılımcısı** rolüne izin verin.

>[!NOTE]
>Yazmak için Data Factory Kullanıcı arabirimi kullanırsanız ve hizmet sorumlusu, ıAM 'de "Depolama Blobu veri okuyucusu/katkıda bulunan" rolüyle ayarlanmamışsa, test bağlantısı veya tarama/gezinme sırasında "dosya yoluna Bağlantıyı Sına" veya "belirtilen yoldan göz at" seçeneğini belirleyin ve devam etmek için **okuma + yürütme** iznine sahip bir yol belirtin.

Bu özellikler bağlı hizmet için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **AzureBlobFS** olarak ayarlanmalıdır. |Yes |
| url | Düzenine sahip Data Lake Storage 2. için uç nokta `https://<accountname>.dfs.core.windows.net` . | Yes |
| Serviceprincipalıd | Uygulamanın istemci KIMLIĞINI belirtin. | Yes |
| servicePrincipalCredentialType | Hizmet sorumlusu kimlik doğrulaması için kullanılacak kimlik bilgisi türü. İzin verilen değerler **Servicesprincipalkey** ve **serviceprincipalcert**' dir. | Yes |
| servicePrincipalCredential | Hizmet sorumlusu kimlik bilgileri. <br/> Kimlik bilgisi türü olarak **Serviceprincipalkey** kullandığınızda, uygulamanın anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. <br/> Kimlik bilgisi olarak **Serviceprincipalcert** kullandığınızda Azure Key Vault bir sertifikaya başvurun. | Yes |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. <br/> Bu özellik için olduğu gibi hala desteklenir `servicePrincipalId`  +  `servicePrincipalKey` . ADF yeni hizmet sorumlusu sertifikası kimlik doğrulaması eklediğinden, hizmet sorumlusu kimlik doğrulaması için yeni model `servicePrincipalId`  +  `servicePrincipalCredentialType`  +  `servicePrincipalCredential` . | No |
| Kiracı | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Yes |
| Azurecses türü | Hizmet sorumlusu kimlik doğrulaması için Azure Active Directory uygulamanızın kaydedildiği Azure bulut ortamının türünü belirtin. <br/> İzin verilen değerler **Azucumhuriyeti**, **AzureChina**, **AzureUsGovernment** ve **AzureGermany**. Varsayılan olarak, Data Factory 'nin bulut ortamı kullanılır. | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Veri depolubir özel ağda ise Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |No |

**Örnek: hizmet sorumlusu anahtar kimlik doğrulamasını kullanma**

Ayrıca, hizmet sorumlusu anahtarını Azure Key Vault de saklayabilirsiniz.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**Örnek: hizmet sorumlusu sertifika kimlik doğrulamasını kullanma**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure kaynakları kimlik doğrulaması için Yönetilen kimlikler

Veri Fabrikası, bu belirli veri fabrikasını temsil eden [Azure kaynakları için yönetilen bir kimlikle](data-factory-service-identity.md)ilişkilendirilebilir. Bu yönetilen kimliği, kendi hizmet sorumlunuzu kullanmaya benzer şekilde Data Lake Storage 2. kimlik doğrulaması için doğrudan kullanabilirsiniz. Bu belirlenen fabrikasının Data Lake Storage 2. erişmesine ve bu verilere veri kopyalamasına izin verir.

Azure Kaynak kimlik doğrulaması için yönetilen kimlikleri kullanmak için aşağıdaki adımları izleyin.

1. Fabrikalarınızla birlikte oluşturulan **yönetilen kimlik nesne kimliğinin** değerini kopyalayarak [Data Factory yönetilen kimlik bilgilerini alın](data-factory-service-identity.md#retrieve-managed-identity) .

2. Yönetilen kimliğe uygun izni verin. [Dosya ve dizinlerdeki erişim denetim listelerinden](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)Data Lake Storage 2. iznin nasıl çalıştığına ilişkin örneklere bakın.

    - **Kaynak olarak**: Depolama Gezgini ' de, tüm yukarı akış klasörleri ve dosya sistemi için en az **yürütme** iznini, kopyalanacak dosyalar için de **Oku** iznini verin. Alternatif olarak, erişim denetimi 'nde (ıAM) en azından **Depolama Blobu veri okuyucu** rolü verin.
    - **Havuz olarak**: Depolama Gezgini IÇINDE, tüm yukarı akış klasörleri ve dosya sistemi için, havuz klasörü için **yazma** Izniyle birlikte en az **yürütme** izni verin. Alternatif olarak, erişim denetimi 'nde (ıAM), en azından **Depolama Blobu veri katılımcısı** rolüne izin verin.

>[!NOTE]
>Yazmak için Data Factory UI kullanırsanız ve yönetilen kimlik, ıAM 'de "Depolama Blobu veri okuyucusu/katkıda bulunan" rolü ile ayarlanmamışsa, test bağlantısı veya tarama/gezinme sırasında "dosya yoluna Bağlantıyı Sına" veya "belirtilen yoldan göz at" seçeneğini belirleyin ve devam etmek için **okuma + yürütme** iznine sahip bir yol belirtin.

>[!IMPORTANT]
>Data Lake Storage 2. verileri Azure SYNAPSE Analytics 'e yüklemek için PolyBase veya COPY ifadesini kullanıyorsanız, Data Lake Storage 2. için yönetilen kimlik kimlik doğrulaması kullandığınızda, [Bu](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)kılavuzda 1 ile 3 arasındaki adımları da izlediğinizden emin olun. Bu adımlar, sunucunuzu Azure AD 'ye kaydeder ve Depolama Blobu veri katılımcısı rolünü sunucunuza atar. Data Factory Rest 'i işler. Blob depolamayı bir Azure sanal ağ uç noktasıyla yapılandırırsanız, **Güvenilen Microsoft hizmetlerinin bu depolama hesabına** Azure depolama hesabı **güvenlik duvarları ve sanal ağlar** ayarları menüsü altında, SYNAPSE Için gereken şekilde erişmesine izin ver ' i de yüklemeniz gerekir.

Bu özellikler bağlı hizmet için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **AzureBlobFS** olarak ayarlanmalıdır. |Yes |
| url | Düzenine sahip Data Lake Storage 2. için uç nokta `https://<accountname>.dfs.core.windows.net` . | Yes |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Veri depolubir özel ağda ise Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |No |

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

Biçim tabanlı veri kümesindeki ayarlar altında Data Lake Storage 2. için aşağıdaki özellikler desteklenir `location` :

| Özellik   | Açıklama                                                  | Gerekli |
| ---------- | ------------------------------------------------------------ | -------- |
| tür       | `location`Veri kümesinde bulunan tür özelliğinin **AzureBlobFSLocation** olarak ayarlanması gerekir. | Yes      |
| Biçimlendiri | Data Lake Storage 2. dosya sistemi adı.                              | No       |
| folderPath | Verilen dosya sistemi altındaki bir klasörün yolu. Klasörleri filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları 'nda belirtin. | No       |
| fileName   | Verilen fileSystem + folderPath altındaki dosya adı. Dosyaları filtrelemek için bir joker karakter kullanmak istiyorsanız, bu ayarı atlayın ve etkinlik kaynağı ayarları 'nda belirtin. | No       |

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

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [kopyalama etkinliği yapılandırması](copy-activity-overview.md#configuration) ve işlem [hatları ve etkinlikleri](concepts-pipelines-activities.md). Bu bölüm Data Lake Storage 2. kaynak ve havuz tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Kaynak türü olarak Azure Data Lake Storage 2.

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

ADLS 2. verileri kopyalamak için çeşitli seçenekleriniz vardır:

- Veri kümesinde belirtilen verilen yoldan Kopyala.
- Klasör yolu veya dosya adına karşılık gelen joker karakter filtresi, bkz `wildcardFolderPath` `wildcardFileName` . ve.
- Verilen metin dosyasında tanımlanan dosyaları dosya kümesi olarak kopyalayın, bkz `fileListPath` ..

Aşağıdaki özellikler, `storeSettings` Biçim tabanlı kopyalama kaynağı ayarları altındaki Data Lake Storage 2. için desteklenir:

| Özellik                 | Açıklama                                                  | Gerekli                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| tür                     | İçindeki tür özelliği `storeSettings` **AzureBlobFSReadSettings** olarak ayarlanmalıdır. | Yes                                           |
| ***Kopyalanacak dosyaları bulun:*** |  |  |
| SEÇENEK 1: statik yol<br> | Veri kümesinde belirtilen verilen dosya sistemi veya klasör/dosya yolundan kopyalama. Dosya sistemi/klasöründen tüm dosyaları kopyalamak istiyorsanız, ayrıca olarak öğesini belirtin `wildcardFileName` `*` . |  |
| Seçenek 2: joker karakter<br>-Yavaya Cardfolderpath | Kaynak klasörleri filtrelemek için veri kümesinde yapılandırılan belirtilen dosya sistemi altındaki joker karakterlerle klasör yolu. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek klasör adınızın joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın. <br>[Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | No                                            |
| Seçenek 2: joker karakter<br>-Yavaya Cardfilename | Kaynak dosyaları filtrelemek için, belirtilen dosya sistemi + folderPath/, Cardfolderpath altındaki joker karakterlerle dosya adı. <br>İzin verilen joker karakterler: `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir); `^` gerçek dosya adınızın joker karakter veya içinde bu kaçış karakteri varsa kaçış için kullanın.  [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. | Yes |
| Seçenek 3: dosya listesi<br>-fileListPath | Belirli bir dosya kümesinin kopyalanıp ayrılmadığını gösterir. Veri kümesinde yapılandırılan yolun göreli yolu olan, kopyalamak istediğiniz dosyaların listesini içeren bir metin dosyası üzerine gelin.<br/>Bu seçeneği kullanırken, veri kümesinde dosya adı belirtmeyin. [Dosya listesi örneklerinde](#file-list-examples)daha fazla örneğe bakın. |No |
| ***Ek ayarlar:*** |  | |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir depo olduğunda, havuzda boş bir klasör veya alt klasör kopyalanmadığını veya oluşturulamadığına unutmayın. <br>İzin verilen değerler **true** (varsayılan) ve **false** şeklindedir.<br>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . |No |
| deleteFilesAfterCompletion | Hedef depoya başarıyla taşıdıktan sonra, ikili dosyaların kaynak depodan silinip silinmeyeceğini gösterir. Dosya silme dosya başına yapılır, bu nedenle kopyalama etkinliği başarısız olduğunda, bazı dosyaların hedefe zaten kopyalanmış ve kaynaktan silindiği görürsünüz, diğerleri ise kaynak deposunda hala kalır. <br/>Bu özellik yalnızca ikili dosyalar kopyalama senaryosunda geçerlidir. Varsayılan değer: false. |No |
| modifiedDatetimeStart    | Öznitelikleri temel alan dosya filtresi: son değiştirme. <br>Son değiştirilme zamanı ve arasındaki zaman aralığı içinde ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmayacağı anlamına gelir.  `modifiedDatetimeStart`Tarih saat değeri olduğunda ancak `modifiedDatetimeEnd` null olduğunda, son değiştirilen özniteliği DateTime değeri ile eşit veya daha büyük olan dosyalar seçilir.  `modifiedDatetimeEnd`Tarih saat değeri olduğunda ancak `modifiedDatetimeStart` null olduğunda, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.<br/>Bu özellik, yapılandırdığınızda uygulanmaz `fileListPath` . | No                                            |
| modifiedDatetimeEnd      | Yukarıdakiyle aynıdır.                                               | No                                            |
| enablePartitionDiscovery | Bölümlenmiş dosyalar için, dosya yolundan bölümlerin ayrıştırıp ayrıştırmayacağını belirtin ve bunları ek kaynak sütunları olarak ekleyin.<br/>İzin verilen değerler **false** (varsayılan) ve **true** şeklindedir. | No                                            |
| Partitionrootyolu | Bölüm bulma etkin olduğunda, bölümlenmiş klasörleri veri sütunları olarak okumak için mutlak kök yolunu belirtin.<br/><br/>Belirtilmemişse, varsayılan olarak<br/>-Veri kümesinde dosya yolunu veya kaynaktaki dosya listesini kullandığınızda, bölüm kök yolu, veri kümesinde yapılandırılan yoldur.<br/>-Joker karakter klasörü filtresi kullandığınızda, bölüm kök yolu ilk joker karakterin öncesindeki alt yoldur.<br/><br/>Örneğin, veri kümesindeki yolu "root/Folder/Year = 2020/ay = 08/gün = 27" olarak yapılandırdığınız varsayılarak:<br/>-Bölüm kök yolunu "root/Folder/Year = 2020" olarak belirtirseniz, kopyalama etkinliği `month` `day` dosyaların içindeki sütunlara ek olarak, sırasıyla "08" ve "27" değeriyle birlikte iki sütun oluşturur.<br/>-Bölüm kök yolu belirtilmemişse, ek sütun oluşturulmaz. | No                                            |
| maxConcurrentConnections | Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Havuz türü olarak Azure Data Lake Storage 2.

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Biçim tabanlı kopya havuzunda ayarlar altında Data Lake Storage 2. için aşağıdaki özellikler desteklenir `storeSettings` :

| Özellik                 | Açıklama                                                  | Gerekli |
| ------------------------ | ------------------------------------------------------------ | -------- |
| tür                     | İçindeki tür özelliği `storeSettings` **AzureBlobFSWriteSettings** olarak ayarlanmalıdır. | Yes      |
| copyBehavior             | Kaynak dosya tabanlı bir veri deposundan dosyalar olduğunda kopyalama davranışını tanımlar.<br/><br/>İzin verilen değerler şunlardır:<br/><b>-Preservehierarchy (varsayılan)</b>: Hedef klasördeki dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu, hedef dosyanın göreli yoluyla hedef klasöre aynıdır.<br/><b>-DÜZEDEN hiyerarşi</b>: kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyindedir. Hedef dosyalar otomatik olarak oluşturulan adlara sahiptir. <br/><b>-Mergefiles</b>: kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya adı belirtilmişse, birleştirilmiş dosya adı belirtilen addır. Aksi takdirde, otomatik olarak oluşturulan bir dosya adıdır. | No       |
| Blocksizeınmb | ADLS 2. veri yazmak için kullanılan blok boyutunu MB olarak belirleyin. [Blok Blobları hakkında](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs)daha fazla bilgi edinin. <br/>İzin verilen değer **4 MB ile 100 MB arasındadır**. <br/>Varsayılan olarak, ADF, kaynak depolama türü ve verilerinize göre blok boyutunu otomatik olarak belirler. İkili olmayan kopya ADLS 2. ' ye kadar, varsayılan blok boyutu 100 MB 'tır, bu nedenle en fazla 4,95 TB veriye uyum sağlar. Verileriniz büyük olmadığında en iyi durumda olmayabilir, özellikle de kötü ağ ile şirket içinde barındırılan Integration Runtime, işlem zaman aşımı veya performans sorunu ile sonuçlanır. Açıkça bir blok boyutu belirtebilirsiniz, ancak Blocksizeınmb * 50000, verileri depolamak için yeterince büyük olduğundan, kopyalama etkinliği çalıştırması başarısız olur. | No |
| maxConcurrentConnections | Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No       |

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
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Klasör ve dosya filtresi örnekleri

Bu bölümde, klasör yolu ve dosya adının joker karakter filtreleriyle elde edilen davranışı açıklanmaktadır.

| folderPath | fileName | öz | Kaynak klasör yapısı ve filtre sonucu ( **kalın** olan dosyalar alınır)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Boş, Varsayılanı kullan) | yanlış | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (Boş, Varsayılanı kullan) | true | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile2.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**ÜzerindeFile4.js**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | yanlış | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>Dosya listesi örnekleri

Bu bölümde, kopyalama etkinlik kaynağında dosya listesi yolu kullanmanın ortaya çıkan davranışı açıklanmaktadır.

Aşağıdaki kaynak klasör yapısına sahip olduğunuz ve dosyaları kalın yazı tipinde kopyalamak istediğiniz varsayılarak:

| Örnek kaynak yapısı                                      | FileListToCopy.txt içerik                             | ADF yapılandırması                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Biçimlendiri<br/>&nbsp;&nbsp;&nbsp;&nbsp;Klasör a<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile2.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ÜzerindeFile4.js<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Veriyi<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **Veri kümesinde:**<br>-Dosya sistemi: `filesystem`<br>-Klasör yolu: `FolderA`<br><br>**Kopyalama etkinliği kaynağı:**<br>-Dosya listesi yolu: `filesystem/Metadata/FileListToCopy.txt` <br><br>Dosya listesi yolu, aynı veri deposunda, kopyalamak istediğiniz dosyaların listesini içeren bir metin dosyasını işaret eder, veri kümesinde yapılandırılan yolun göreli yolu ile her satıra bir dosya. |


### <a name="some-recursive-and-copybehavior-examples"></a>Bazı özyinelemeli ve copyBehavior örnekleri

Bu bölümde, özyinelemeli ve copyBehavior değerlerinin farklı birleşimleri için kopyalama işleminin ortaya çıkan davranışı açıklanmaktadır.

| öz | copyBehavior | Kaynak klasör yapısı | Sonuç hedefi |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1, kaynak ile aynı yapıyla oluşturulur:<br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |DÜZEDEN hiyerarşisi | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;file3 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5 için otomatik olarak oluşturulan ad |
| true |mergeFiles | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1 + dosya2 + File3 + File4 + File5 içerikler, otomatik olarak oluşturulan bir dosya adına sahip tek bir dosyada birleştirilir. |
| yanlış |preserveHierarchy | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte çekilmez. |
| yanlış |DÜZEDEN hiyerarşisi | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fıle1 için otomatik olarak oluşturulan ad<br/>&nbsp;&nbsp;&nbsp;&nbsp;dosya2 için otomatik olarak oluşturulan ad<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte çekilmez. |
| yanlış |mergeFiles | Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Dosya2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Hedef Klasör1 aşağıdaki yapıyla oluşturulur: <br/><br/>Klasör1<br/>&nbsp;&nbsp;&nbsp;&nbsp;FILE1 + dosya2 içerikleri, otomatik olarak oluşturulan dosya adına sahip bir dosyada birleştirilir. Fıle1 için otomatik olarak oluşturulan ad<br/><br/>Subfolder1, File3, File4 ve File5 ile birlikte çekilmez. |

## <a name="preserve-metadata-during-copy"></a>Kopya sırasında meta verileri koru

Dosyaları Amazon S3/Azure Blob/Azure Data Lake Storage 2. Azure Data Lake Storage 2./Azure Blob 'a kopyaladığınızda, dosya meta verilerini verilerle birlikte korumayı seçebilirsiniz. [Meta verileri koru](copy-activity-preserve-metadata.md#preserve-metadata)'dan daha fazla bilgi edinin.

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> ACL 'Leri Data Lake Storage 1./Gen2 koruma

Azure Data Lake Storage 1./Gen2 'den Gen2 'e dosya kopyaladığınızda, POSIX erişim denetim listelerini (ACL 'Ler) verilerle birlikte korumayı seçebilirsiniz. [Data Lake Storage 1./Gen2 Ile Gen2 arasında ACL 'Leri koruyun](copy-activity-preserve-metadata.md#preserve-acls)'ten daha fazla bilgi edinin.

>[!TIP]
>Azure Data Lake Storage 1. verileri genel olarak Gen2 'e kopyalamak için bkz. bir adım adım ve en iyi uygulamalar için [Azure Data Factory ile Azure Data Lake Storage 1. verileri Gen2 'e kopyalama](load-azure-data-lake-storage-gen2-from-gen1.md) .

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Veri akışlarındaki verileri dönüştürürken, aşağıdaki biçimlerde Azure Data Lake Storage 2. dosyaları okuyabilir ve yazabilirsiniz:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Ortak veri modeli (Önizleme)](format-common-data-model.md#mapping-data-flow-properties)
* [Sınırlandırılmış metin](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Biçimlendirme özel ayarları, bu biçimdeki belgelerde bulunur. Daha fazla bilgi için bkz. eşleme veri akışı ve [Havuz dönüşümünde](data-flow-sink.md) [kaynak dönüştürme](data-flow-source.md) veri akışında.

### <a name="source-transformation"></a>Kaynak dönüştürme

Kaynak dönüşümünde, Azure Data Lake Storage 2. bir kapsayıcı, klasör veya tek dosyadan okuyabilirsiniz. **Kaynak seçenekleri** sekmesi, dosyaların nasıl okunduğunu yönetmenizi sağlar. 

![Kaynak seçenekleri](media/data-flow/sourceOptions1.png "Kaynak seçenekleri")

**Joker karakter yolu:** Bir joker karakter deseninin kullanılması, ADF 'nin eşleşen her klasör ve dosyada tek bir kaynak dönüşümünde döngüye girmesine neden olur. Bu, tek bir akış içinde birden çok dosyayı işlemek için etkili bir yoldur. Mevcut joker karakter deseniniz üzerine getirildiğinde görüntülenen + işaretiyle birden çok joker karakter eşleştirme deseni ekleyin.

Kaynak kapsayıcısından bir düzeniyle eşleşen bir dosya serisi seçin. Yalnızca kapsayıcı, veri kümesinde belirtilebilir. Bu nedenle, joker karakter yolunuzda Ayrıca, kök klasörden klasör yolunuzdan de yer verilmelidir.

Joker karakter örnekleri:

* ```*``` Herhangi bir karakter kümesini temsil eder
* ```**``` Özyinelemeli dizin iç içe geçirmeyi temsil eder
* ```?``` Bir karakteri değiştirir
* ```[]``` Köşeli parantezdeki daha fazla karakterden biriyle eşleşir

* ```/data/sales/**/*.csv``` /Data/Sales altındaki tüm CSV dosyalarını alır
* ```/data/sales/20??/**/``` 20. yüzdeki tüm dosyaları alır
* ```/data/sales/*/*/*.csv``` /Data/Sales altındaki CSV dosyalarını iki düzey alır
* ```/data/sales/2004/*/12/[XY]1?.csv``` İki basamaklı bir sayı tarafından önekli X veya Y ile başlayan Aralık içinde 2004 içindeki tüm CSV dosyalarını alır

**Bölüm kök yolu:** Dosya kaynağınızda bir biçimde bölümlenmiş klasörler varsa ```key=value``` (örneğin, Year = 2019), bu bölüm klasör ağacının en üst düzeyini veri akışı veri akışınızdaki bir sütun adına atayabilirsiniz.

İlk olarak, bölümlenmiş klasörler ve okumak istediğiniz yaprak dosyaları olan tüm yolları içerecek şekilde bir joker karakter ayarlayın.

![Bölüm kaynak dosyası ayarları](media/data-flow/partfile2.png "Bölüm dosyası ayarı")

Klasör yapısının en üst düzeyinin ne olduğunu tanımlamak için bölüm kök yolu ayarını kullanın. Veri önizleme aracılığıyla verilerinizin içeriğini görüntülediğinizde, ADF 'nin klasör düzeylerinizde bulunan çözümlenmiş bölümleri eklemesini görürsünüz.

![Bölüm kök yolu](media/data-flow/partfile1.png "Bölüm kök yolu önizlemesi")

**Dosya listesi:** Bu bir dosya kümesidir. İşlemek için göreli yol dosyalarının bir listesini içeren bir metin dosyası oluşturun. Bu metin dosyasına işaret edin.

**Depolanacak sütun dosya adı:** Kaynak dosyanın adını verilerinizin bir sütununda depolayın. Dosya adı dizesini depolamak için buraya yeni bir sütun adı girin.

**Tamamlandıktan sonra:** Veri akışı çalıştıktan sonra kaynak dosyayla ilgili hiçbir şey yapma seçeneğini belirleyin, kaynak dosyayı silin veya kaynak dosyayı taşıyın. Taşımanın yolları görelidir.

Kaynak dosyalarını başka bir konuma işleme sonrası taşımak için, önce dosya işlemi için "taşı" yı seçin. Sonra, "Kimden" dizinini ayarlayın. Yolunuzda herhangi bir joker karakter kullanmıyorsanız, "Kimden" ayarı kaynak klasörünüzün bulunduğu klasör olacaktır.

Joker karakter içeren bir kaynak yolunuz varsa söz dizinizin aşağıdaki gibi görünmesi gerekir:

```/data/sales/20??/**/*.csv```

"Kimden" olarak belirtebilirsiniz

```/data/sales```

Ve "to" as

```/backup/priorSales```

Bu durumda,/Data/Sales altında kaynağı bulunan tüm dosyalar/Backup/priorsales' a taşınır.

> [!NOTE]
> Dosya işlemleri, bir işlem hattındaki veri akışını Yürüt etkinliğini kullanan bir işlem hattı çalıştırmasıyla (bir işlem hattı hata ayıklaması veya yürütme çalıştırması) veri akışını başlattığınızda çalışır. Dosya işlemleri veri akışı hata ayıklama *modunda çalışmaz.*

**Son değiştirme ölçütü:** Son değiştirilme tarihi için bir tarih aralığı belirterek hangi dosyaları işleyeistediğinizi filtreleyebilirsiniz. Tüm tarih zamanları UTC olarak. 

### <a name="sink-properties"></a>Havuz özellikleri

Havuz dönüşümünde, Azure Data Lake Storage 2. bir kapsayıcı veya klasöre yazabilirsiniz. **Ayarlar** sekmesi, dosyaların nasıl yazıldığını yönetmenizi sağlar.

![havuz seçenekleri](media/data-flow/file-sink-settings.png "havuz seçenekleri")

**Klasörü temizle:** Hedef klasörün, veriler yazılmadan önce temizlenip temizlenmeyeceğini belirler.

**Dosya adı seçeneği:** Hedef klasördeki hedef dosyaların nasıl adlandırıldığını belirler. Dosya adı seçenekleri şunlardır:
   * **Varsayılan**: Spark 'ın bölüm varsayılanlarına göre dosya adına erişmesine izin verin.
   * **Model**: bölüm başına çıktı dosyalarınızı numaralandırır bir model girin. Örneğin, **krediler [n]. csv** loans1.csv, loans2.csv vb. oluşturacak.
   * **Bölüm başına**: bölüm başına bir dosya adı girin.
   * **Sütunda veri olarak**: çıkış dosyasını bir sütunun değeri olarak ayarlayın. Yol, hedef klasöre değil, veri kümesi kapsayıcısına göredir. Veri kümenizde bir klasör yolunuz varsa, bu geçersiz kılınır.
   * **Tek bir dosyaya çıkış**: bölümlenmiş çıktı dosyalarını tek bir adlandırılmış dosyada birleştirin. Yol, veri kümesi klasörüne görelidir. Lütfen birleştirme işleminin düğüm boyutuna bağlı olarak başarısız olabileceğini unutmayın. Bu seçenek, büyük veri kümeleri için önerilmez.

**Tüm teklif:** Tüm değerlerin tırnak içine alınmış olup olmayacağını belirler

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="delete-activity-properties"></a>Etkinlik özelliklerini Sil

Özelliklerle ilgili ayrıntıları öğrenmek için [silme etkinliği](delete-activity.md) onay

## <a name="legacy-models"></a>Eski modeller

>[!NOTE]
>Geriye dönük uyumluluk için aşağıdaki modeller hala desteklenmektedir. Yukarıdaki bölümlerde bahsedilen yeni modeli kullanmanız önerilir ve ADF yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

### <a name="legacy-dataset-model"></a>Eski veri kümesi modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği **AzureBlobFSFile** olarak ayarlanmalıdır. |Yes |
| folderPath | Data Lake Storage 2. klasörünün yolu. Belirtilmemişse, köke işaret eder. <br/><br/>Joker karakter filtresi desteklenir. İzin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir). `^`Gerçek klasör adınızın bir joker karakter varsa veya bu kaçış karakteri içindeyse kaçış için kullanın. <br/><br/>Örnekler: FileSystem/Folder/. [Klasör ve dosya filtresi örneklerinde](#folder-and-file-filter-examples)daha fazla örnek görüntüleyin. |No |
| fileName | Belirtilen "folderPath" altındaki dosyalar için ad veya joker karakter filtresi. Bu özellik için bir değer belirtmezseniz, veri kümesi klasördeki tüm dosyaları işaret eder. <br/><br/>Filtre için, izin verilen joker karakterler `*` (sıfır veya daha fazla karakterle eşleşir) ve `?` (sıfır veya tek karakterle eşleşir).<br/>-Örnek 1: `"fileName": "*.csv"`<br/>-Örnek 2: `"fileName": "???20180427.txt"`<br/>`^`Gerçek dosya adınızın bir joker karakter varsa veya bu kaçış karakteri içindeyse kaçış için kullanın.<br/><br/>Bir çıkış veri kümesi için dosya adı belirtilmediğinde ve bir etkinlik havuzunda **Preservehierarchy** belirtilmemişse, Copy etkinliği dosya adını aşağıdaki Düzenle otomatik olarak oluşturur: "*Data. [ Etkinlik çalıştırma KIMLIĞI GUID 'SI]. [DÜZEDEN hiyerarşi varsa GUID]. [yapılandırıldıysa Biçimlendir]. [yapılandırıldıysa sıkıştırma]*"Örneğin," Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. gz ". Sorgu yerine tablo adı kullanarak tablosal kaynaktan kopyalama yaparsanız, ad deseninin "*[tablo adı]" olması gerekir. [ Biçim]. [yapılandırıldıysa sıkıştırma]*"Örneğin," MyTable.csv ". |No |
| modifiedDatetimeStart | Son değiştirilen özniteliğe göre dosya filtresi. Son değiştirilme zamanı, ve arasındaki zaman aralığı içinde ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Veri hareketinin genel performansı, çok büyük miktarlarda dosya filtresi yapmak istediğinizde bu ayarın etkinleştirilmesiyle etkilenir. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir. Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak null ise, son değiştirilen özniteliği DateTime değerinden büyük veya ona eşit olan dosyalar seçilir. Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak null ise, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| No |
| modifiedDatetimeEnd | Son değiştirilen özniteliğe göre dosya filtresi. Son değiştirilme zamanı, ve arasındaki zaman aralığı içinde ise dosyalar seçilir `modifiedDatetimeStart` `modifiedDatetimeEnd` . Saat, UTC saat dilimine "2018-12-01T05:00:00Z" biçiminde uygulanır. <br/><br/> Veri hareketinin genel performansı, çok büyük miktarlarda dosya filtresi yapmak istediğinizde bu ayarın etkinleştirilmesiyle etkilenir. <br/><br/> Özellikler NULL olabilir, bu da veri kümesine hiçbir dosya özniteliği filtresinin uygulanmadığı anlamına gelir. Ne zaman `modifiedDatetimeStart` bir tarih saat değeri olduğunda `modifiedDatetimeEnd` , ancak null ise, son değiştirilen özniteliği DateTime değerinden büyük veya ona eşit olan dosyalar seçilir. Ne zaman `modifiedDatetimeEnd` bir tarih saat değeri olduğunda `modifiedDatetimeStart` , ancak null ise, son değiştirilen özniteliği DateTime değerinden küçük olan dosyalar seçilir.| No |
| biçim | Dosyaları dosya tabanlı depolarla (ikili kopya) olduğu gibi kopyalamak istiyorsanız, hem giriş hem de çıkış veri kümesi tanımlarının biçim bölümünü atlayın.<br/><br/>Belirli bir biçimdeki dosyaları ayrıştırmak veya oluşturmak isterseniz, aşağıdaki dosya biçimi türleri desteklenir: **TextFormat**, **jsonformat**, **avroformat**, **Orcformat** ve **parquetformat**. **Biçim** ' in altındaki **Type** özelliğini bu değerlerden birine ayarlayın. Daha fazla bilgi için bkz. [metin biçimi](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON biçimi](supported-file-formats-and-compression-codecs-legacy.md#json-format), [avro Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [orc biçimi](supported-file-formats-and-compression-codecs-legacy.md#orc-format)ve [Parquet biçim](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) bölümleri. |Hayır (yalnızca ikili kopya senaryosu için) |
| sıkıştırma | Verilerin türünü ve sıkıştırma düzeyini belirtin. Daha fazla bilgi için bkz. [Desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Desteklenen türler **gzip**, **söndür**, **bzip2** ve **zipsöndür**.<br/>Desteklenen düzeyler **en iyi** ve **en hızlardır**. |No |

>[!TIP]
>Bir klasörün altındaki tüm dosyaları kopyalamak için, yalnızca **FolderPath** ' i belirtin.<br>Belirli bir ada sahip tek bir dosyayı kopyalamak için, klasör **bölüm ve dosya** adı ile bir dosya adı ile **FolderPath** öğesini belirtin.<br>Bir klasör altındaki bir dosya alt kümesini kopyalamak için, bir klasör bölümü ve **dosya adı** ile bir joker karakter filtresi içeren **FolderPath** öğesini belirtin. 

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

### <a name="legacy-copy-activity-source-model"></a>Eski kopyalama etkinliği kaynak modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği **AzureBlobFSSource** olarak ayarlanmalıdır. |Yes |
| öz | Verilerin alt klasörlerden veya yalnızca belirtilen klasörden özyinelemeli olarak okunup okunmadığını gösterir. Özyinelemeli değeri true olarak ayarlandığında ve havuz dosya tabanlı bir deposa, havuzda boş bir klasör veya alt klasör kopyalanmaz veya oluşturulmaz.<br/>İzin verilen değerler **true** (varsayılan) ve **false** şeklindedir. | No |
| maxConcurrentConnections | Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

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

### <a name="legacy-copy-activity-sink-model"></a>Eski kopyalama etkinliği havuz modeli

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun Type özelliği **AzureBlobFSSink** olarak ayarlanmalıdır. |Yes |
| copyBehavior | Kaynak dosya tabanlı bir veri deposundan dosyalar olduğunda kopyalama davranışını tanımlar.<br/><br/>İzin verilen değerler şunlardır:<br/><b>-Preservehierarchy (varsayılan)</b>: Hedef klasördeki dosya hiyerarşisini korur. Kaynak dosyanın kaynak klasöre göreli yolu, hedef dosyanın göreli yoluyla hedef klasöre aynıdır.<br/><b>-DÜZEDEN hiyerarşi</b>: kaynak klasördeki tüm dosyalar hedef klasörün ilk düzeyindedir. Hedef dosyalar otomatik olarak oluşturulan adlara sahiptir. <br/><b>-Mergefiles</b>: kaynak klasördeki tüm dosyaları tek bir dosya ile birleştirir. Dosya adı belirtilmişse, birleştirilmiş dosya adı belirtilen addır. Aksi takdirde, otomatik olarak oluşturulan bir dosya adıdır. | No |
| maxConcurrentConnections | Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

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

## <a name="next-steps"></a>Sonraki adımlar

Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).