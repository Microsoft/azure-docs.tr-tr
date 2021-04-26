---
title: Azure Data Factory kullanarak OData kaynaklarından veri kopyalama
description: Azure Data Factory işlem hattındaki kopyalama etkinliğini kullanarak OData kaynaklarından desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: jingwang
ms.openlocfilehash: 9dd86b4982edf5d206e64431a5e1458c4b848e9e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968544"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Azure Data Factory kullanarak OData kaynağından veri kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-odata-connector.md)
> * [Güncel sürüm](connector-odata.md)

Bu makalede, bir OData kaynağından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Makale, kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory kopyalama etkinliği](copy-activity-overview.md)üzerinde oluşturulur.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu OData Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

OData kaynağından desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının bir listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).

Özellikle, bu OData Bağlayıcısı şunları destekler:

- OData sürüm 3,0 ve 4,0.
- Şu kimlik doğrulamalarından birini kullanarak verileri kopyalama: **anonim**, **temel**, **Windows** ve **AAD hizmet sorumlusu**.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, bir OData bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

OData bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **OData** olarak ayarlanmalıdır. |Yes |
| url | OData hizmetinin kök URL 'SI. |Yes |
| authenticationType | OData kaynağına bağlanmak için kullanılan kimlik doğrulaması türü. İzin verilen değerler **anonim**, **temel**, **Windows** ve **aadserviceprincipal**. Kullanıcı tabanlı OAuth desteklenmez. Ayrıca, özelliğindeki kimlik doğrulama üstbilgilerini yapılandırabilirsiniz `authHeader` .| Yes |
| authHeaders | Kimlik doğrulaması için ek HTTP isteği üst bilgileri.<br/> Örneğin, API anahtarı kimlik doğrulamasını kullanmak için, kimlik doğrulama türü ' nü "anonim" olarak seçebilir ve üst bilgide API anahtarını belirtebilirsiniz. | No |
| userName | Temel veya Windows kimlik doğrulamasını kullanıyorsanız **Kullanıcı adını** belirtin. | No |
| password | Kullanıcı **adı** için belirttiğiniz kullanıcı hesabı için **parola** belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** türü olarak işaretleyin. Ayrıca, [Azure Key Vault depolanan bir gizli](store-credentials-in-key-vault.md)dizi için de başvurabilirsiniz. | No |
| Serviceprincipalıd | Azure Active Directory uygulamasının istemci KIMLIĞINI belirtin. | No |
| aadServicePrincipalCredentialType | Hizmet sorumlusu kimlik doğrulaması için kullanılacak kimlik bilgisi türünü belirtin. İzin verilen değerler: `ServicePrincipalKey` veya `ServicePrincipalCert` . | No |
| Servicesprincipalkey | Azure Active Directory uygulamasının anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| Serviceprincıpalimon Beddedcert | Azure Active Directory kayıtlı olan uygulamanızın Base64 olarak kodlanmış sertifikasını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | No |
| Serviceprincıpalimon Beddedcertpassword | Sertifikanızın bir parolayla güvenliği varsa sertifikanızın parolasını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın.  | No|
| Kiracı | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | No |
| Aadresourceıd | Yetkilendirme için istediğiniz AAD kaynağını belirtin.| No |
| Azurecses türü | Hizmet sorumlusu kimlik doğrulaması için AAD uygulamanızın kaydedildiği Azure bulut ortamının türünü belirtin. <br/> İzin verilen değerler **Azucumhuriyeti**, **AzureChina**, **AzureUsGovernment** ve **AzureGermany**. Varsayılan olarak, Data Factory 'nin bulut ortamı kullanılır. | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, varsayılan Azure Integration Runtime kullanılır. |No |

**Örnek 1: anonim kimlik doğrulaması kullanma**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 2: temel kimlik doğrulaması kullanma**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 3: Windows kimlik doğrulamasını kullanma**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 4: hizmet sorumlusu anahtar kimlik doğrulamasını kullanma**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Örnek 5: hizmet sorumlusu sertifika kimlik doğrulamasını kullanma**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**Örnek 6: API anahtarı kimlik doğrulamasını kullanma**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "APIKey": {
                    "type": "SecureString",
                    "value": "<API key>"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bu bölüm, OData veri kümesinin desteklediği özelliklerin bir listesini sağlar.

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md). 

OData 'ten veri kopyalamak için, veri kümesinin **Type** özelliğini **ODataResource** olarak ayarlayın. Aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin **Type** özelliği **ODataResource** olarak ayarlanmalıdır. | Yes |
| path | OData kaynağının yolu. | Yes |

**Örnek**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Bu bölüm, OData kaynağının desteklediği özelliklerin bir listesini sağlar.

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). 

### <a name="odata-as-source"></a>Kaynak olarak OData

OData 'ten veri kopyalamak için aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **odatasource** olarak ayarlanmalıdır. | Yes |
| sorgu | Verileri filtrelemek için OData sorgu seçenekleri. Örnek: `"$select=Name,Description&$top=5"`.<br/><br/>**Note**: OData BAĞLAYıCıSı Birleşik URL 'den verileri kopyalar: `[URL specified in linked service]/[path specified in dataset]?[query specified in copy activity source]` . Daha fazla bilgi için bkz. [OData URL bileşenleri](https://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, yanıt verilerinin okunması için zaman aşımı değil, yanıt almaya yönelik zaman aşımı değeridir. Belirtilmemişse, varsayılan değer **00:30:00** ' dir (30 dakika). | No |

**Örnek**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "ODataSource",
                "query": "$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Yazılan kaynağı kullanıyorsanız, `RelationalSource` hala olduğu gibi desteklenmektedir, ileri ' yi kullanmaya devam etmeniz önerilir.

## <a name="data-type-mapping-for-odata"></a>OData için veri türü eşlemesi

OData 'ten veri kopyaladığınızda, OData veri türleri ve Azure Data Factory geçici veri türleri arasında aşağıdaki eşlemeler kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| OData veri türü | Data Factory geçici veri türü |
|:--- |:--- |
| EDM. Binary | Byte [] |
| Edm.Boolean | Bool |
| EDM. Byte | Byte [] |
| EDM. DateTime | DateTime |
| EDM. Decimal | Ondalık |
| Edm.Double | Çift |
| EDM. Single | Tek |
| EDM. Guid | Guid |
| EDM. Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| EDM. SByte | Int16 |
| Edm.String | Dize |
| EDM. Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData karmaşık veri türleri (örneğin, **nesne**) desteklenmez.

## <a name="copy-data-from-project-online"></a>Project Online 'dan veri kopyalama

Project Online 'dan veri kopyalamak için OData bağlayıcısını ve Postman gibi araçlardan alınan bir erişim belirtecini kullanabilirsiniz.

> [!CAUTION]
> Erişim belirtecinin süresi, varsayılan olarak 1 saat içinde dolar. süresi dolmuşsa yeni bir erişim belirteci almanız gerekir.

1. Erişim belirtecini almak için **Postman** kullanın:

   1. Postman Web sitesinde **Yetkilendirme** sekmesine gidin.
   1. **Tür** kutusunda, **OAuth 2,0**' ı seçin ve **yetkilendirme verileri ekle** kutusunda **istek üst bilgileri**' ni seçin.
   1. Yeni bir erişim belirteci almak için **yeni belirteci Yapılandır** sayfasında aşağıdaki bilgileri girin: 
      - **Verme türü**: **yetkilendirme kodu** seçin.
      - **Geri arama URL 'si**: girin `https://www.localhost.com/` . 
      - **Kimlik doğrulama URL 'si**: girin `https://login.microsoftonline.com/common/oauth2/authorize?resource=https://<your tenant name>.sharepoint.com` . `<your tenant name>`Kendi kiracı adınızla değiştirin. 
      - **Erişim belirteci URL 'si**: girin `https://login.microsoftonline.com/common/oauth2/token` .
      - **ISTEMCI kimliği**: AAD HIZMET sorumlusu kimliğinizi girin.
      - **Istemci parolası**: hizmet sorumlusu gizli anahtarını girin.
      - **Istemci kimlik doğrulaması**: **Basit kimlik doğrulama üst bilgisi gönder**' i seçin.
     
   1. Kullanıcı adınız ve parolanızla oturum açmanız istenir.
   1. Erişim belirtecinizi aldıktan sonra lütfen bir sonraki adım için kopyalayın ve kaydedin.
   
    [![Erişim belirtecini almak için Postman kullanın](./media/connector-odata/odata-project-online-postman-access-token-inline.png)](./media/connector-odata/odata-project-online-postman-access-token-expanded.png#lightbox)

1. OData bağlı hizmetini oluşturun:
    - **Hizmet URL 'si**: girin `https://<your tenant name>.sharepoint.com/sites/pwa/_api/Projectdata` . `<your tenant name>`Kendi kiracı adınızla değiştirin. 
    - **Kimlik doğrulama türü**: **anonim**' i seçin.
    - **Kimlik doğrulama üstbilgileri**:
        - **Özellik adı**: **Yetkilendirmeyi** seçin.
        - **Değer**: 1. adımdan kopyalanmış **erişim belirtecini** girin.
    - Bağlı hizmeti test edin.

    ![OData bağlı hizmeti oluştur](./media/connector-odata/odata-project-online-linked-service.png)

1. OData veri kümesini oluşturun:
    1. 2. adımda oluşturulan OData bağlı hizmeti ile veri kümesini oluşturun.
    1. Verileri önizleyin.
 
    ![Veri önizleme](./media/connector-odata/odata-project-online-preview-data.png)
 


## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Kopyalama etkinliğinin Azure Data Factory kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).