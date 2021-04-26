---
title: Azure Data Factory kullanarak ve bir REST uç noktasına veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliği kullanarak bir bulut veya şirket içi REST kaynağından desteklenen havuz veri depolarına veya desteklenen kaynak veri deposundan bir REST havuzuna veri kopyalamayı öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: jingwang
ms.openlocfilehash: 779a8745688e6a1fb8a15bc9119c6fbc1803ca2c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078936"
---
# <a name="copy-data-from-and-to-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory kullanarak ve bir REST uç noktasına veri kopyalama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, bir REST uç noktasına ve arasında veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Makale, kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory kopyalama etkinliği](copy-activity-overview.md)üzerinde oluşturulur.

Bu REST Bağlayıcısı, [http Bağlayıcısı](connector-http.md)ve [Web tablosu Bağlayıcısı](connector-web-table.md) arasındaki fark şudur:

- **Rest Bağlayıcısı** , verilerin yeniden oluşturulmuş API 'lerden kopyalanmasını özellikle destekler; 
- **Http Bağlayıcısı** , örneğin, dosyayı indirmek için HERHANGI bir HTTP uç noktasından veri almak için geneldir. Bu REST bağlayıcısından önce, desteklenen ancak REST bağlayıcısıyla daha az işlevsel bir işlevselliğe sahip olan, yeniden gerçekleşen API 'den veri kopyalamak için HTTP bağlayıcısını kullanabilirsiniz.
- **Web tablosu Bağlayıcısı** , tablo IÇERIĞINI bir HTML Web sayfasından ayıklar.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bir REST kaynağından, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Ayrıca, desteklenen herhangi bir kaynak veri deposundan verileri bir REST havuzuna kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının bir listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).

Özellikle, bu genel REST Bağlayıcısı şunları destekler:

- **Get** veya **Post** yöntemlerini kullanarak bir REST uç noktasından veri kopyalama ve **Post**, **PUT** veya **Patch** yöntemlerini kullanarak verileri REST uç noktasına kopyalama.
- Aşağıdaki kimlik doğrulamalarından birini kullanarak verileri kopyalama: **anonim**, **temel**, **AAD hizmet sorumlusu** ve **Azure kaynakları için Yönetilen kimlikler**.
- REST API 'Lerinde **[sayfalandırma](#pagination-support)** .
- Geri kalan kaynak olarak, REST JSON yanıtını [olduğu gibi](#export-json-response-as-is) kopyalayarak veya [şema eşleme](copy-activity-schema-and-type-mapping.md#schema-mapping)kullanarak ayrıştırarak. Yalnızca **JSON** 'daki yanıt yükü desteklenir.

> [!TIP]
> Data Factory ' de REST bağlayıcısını yapılandırmadan önce veri alımı isteğini test etmek için, üst bilgi ve gövde gereksinimlerine yönelik API belirtimi hakkında bilgi edinin. Doğrulamak için Postman veya bir Web tarayıcısı gibi araçları kullanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, REST bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

REST bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **RESTService** olarak ayarlanmalıdır. | Yes |
| url | REST hizmetinin temel URL 'SI. | Yes |
| enableServerCertificateValidation | Uç noktaya bağlanılırken sunucu tarafı TLS/SSL sertifikası doğrulanıp doğrulanmayacağı. | No<br /> (varsayılan değer **true**'dur) |
| authenticationType | REST hizmetine bağlanmak için kullanılan kimlik doğrulaması türü. İzin verilen değerler **anonim**, **temel**, **Aadserviceprincipal** ve **managedserviceıdentity**. Kullanıcı tabanlı OAuth desteklenmez. Ayrıca, özelliğindeki kimlik doğrulama üstbilgilerini yapılandırabilirsiniz `authHeader` . Daha fazla özellik ve örnekte sırasıyla aşağıdaki ilgili bölümlere bakın.| Yes |
| authHeaders | Kimlik doğrulaması için ek HTTP isteği üst bilgileri.<br/> Örneğin, API anahtarı kimlik doğrulamasını kullanmak için, kimlik doğrulama türü ' nü "anonim" olarak seçebilir ve üst bilgide API anahtarını belirtebilirsiniz. | No |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, bu özellik varsayılan Azure Integration Runtime kullanır. |No |

### <a name="use-basic-authentication"></a>Temel kimlik doğrulaması kullan

**AuthenticationType** özelliğini **temel** olarak ayarlayın. Önceki bölümde açıklanan genel özelliklere ek olarak, aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| userName | REST uç noktasına erişmek için kullanılacak Kullanıcı adı. | Yes |
| password | Kullanıcı için parola ( **Kullanıcı adı** değeri). Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** türü olarak işaretleyin. Ayrıca, [Azure Key Vault depolanan bir gizli](store-credentials-in-key-vault.md)dizi için de başvurabilirsiniz. | Yes |

**Örnek**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
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

### <a name="use-aad-service-principal-authentication"></a>AAD hizmet sorumlusu kimlik doğrulamasını kullanma

**AuthenticationType** özelliğini **Aadserviceprincipal** olarak ayarlayın. Önceki bölümde açıklanan genel özelliklere ek olarak, aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| Serviceprincipalıd | Azure Active Directory uygulamasının istemci KIMLIĞINI belirtin. | Yes |
| Servicesprincipalkey | Azure Active Directory uygulamasının anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Yes |
| Kiracı | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Yes |
| Aadresourceıd | Yetkilendirme için istediğiniz AAD kaynağını (örneğin,) belirtin `https://management.core.windows.net` .| Yes |
| Azurecses türü | Hizmet sorumlusu kimlik doğrulaması için AAD uygulamanızın kaydedildiği Azure bulut ortamının türünü belirtin. <br/> İzin verilen değerler **Azucumhuriyeti**, **AzureChina**, **AzureUsGovernment** ve **AzureGermany**. Varsayılan olarak, Data Factory 'nin bulut ortamı kullanılır. | No |

**Örnek**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure kaynakları kimlik doğrulaması için Yönetilen kimlikler kullanma

**AuthenticationType** özelliğini **managedserviceıdentity** olarak ayarlayın. Önceki bölümde açıklanan genel özelliklere ek olarak, aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| Aadresourceıd | Yetkilendirme için istediğiniz AAD kaynağını (örneğin,) belirtin `https://management.core.windows.net` .| Yes |

**Örnek**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-authentication-headers"></a>Kimlik doğrulama üstbilgilerini kullanma

Ayrıca, kimlik doğrulama için istek üst bilgilerini yerleşik kimlik doğrulama türleriyle birlikte yapılandırabilirsiniz.

**Örnek: API anahtarı kimlik doğrulamasını kullanma**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint>",
            "authenticationType": "Anonymous",
            "authHeader": {
                "x-api-key": {
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

Bu bölüm, REST veri kümesinin desteklediği özelliklerin bir listesini sağlar. 

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md). 

REST 'ten veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in **Type** özelliği **restresource** olarak ayarlanmalıdır. | Yes |
| relativeUrl 'Si | Verileri içeren kaynağın göreli URL 'SI. Bu özellik belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. HTTP Bağlayıcısı Birleşik URL 'den verileri kopyalar: `[URL specified in linked service]/[relative URL specified in dataset]` . | No |

,, `requestMethod` `additionalHeaders` Ve veri kümesinde ayarlarsanız, ve ' de `requestBody` `paginationRules` olduğu gibi yeni modeli kullanmaya önermeniz durumunda olduğu gibi hala desteklenmektedir.

**Örnek:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Bu bölüm REST kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>Kaynak olarak REST

Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **restsource** olarak ayarlanmalıdır. | Yes |
| requestMethod | HTTP yöntemi. İzin verilen değerler **Al** (varsayılan) ve **Post**. | No |
| additionalHeaders | Ek HTTP istek üstbilgileri. | No |
| Istek gövdesi | HTTP isteğinin gövdesi. | No |
| Sayfaationrules | Sonraki sayfa isteklerini oluşturmak için sayfalandırma kuralları. Ayrıntılar için [sayfalandırma desteği](#pagination-support) bölümüne bakın. | No |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, yanıt verilerinin okunması için zaman aşımı değil, yanıt almaya yönelik zaman aşımı değeridir. Varsayılan değer **00:01:40**' dir.  | No |
| Requestınterval | Sonraki sayfa için istek gönderilmeden önce beklenecek süre. Varsayılan değer **00:00:01** ' dir |  No |

>[!NOTE]
>REST Bağlayıcısı içinde belirtilen "kabul etme" üst bilgisini yoksayar `additionalHeaders` . REST Bağlayıcısı yalnızca JSON 'daki yanıtı destekliyorsa, otomatik olarak bir üst bilgisi oluşturur `Accept: application/json` .

**Örnek 1: sayfalama ile get yöntemini kullanma**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Örnek 2: Post yöntemini kullanma**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="rest-as-sink"></a>Havuz olarak REST

Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun **Type** özelliği **restsink** olarak ayarlanmalıdır. | Yes |
| requestMethod | HTTP yöntemi. İzin verilen değerler **gönderi** (varsayılan), **PUT** ve **Patch**. | No |
| additionalHeaders | Ek HTTP istek üstbilgileri. | No |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, verileri yazmak için zaman aşımı değil, yanıt almak için zaman aşımı değeridir. Varsayılan değer **00:01:40**' dir.  | No |
| Requestınterval | Milisaniye cinsinden farklı istekler arasındaki Aralık süresi. İstek aralığı değeri [10, 60000] arasında bir sayı olmalıdır. |  No |
| httpCompressionType | En Iyi sıkıştırma düzeyiyle veri gönderilirken kullanılacak HTTP sıkıştırma türü. İzin verilen değerler **none** ve **gzip**'tur. | No |
| writeBatchSize | Yığın başına REST havuzuna yazılacak kayıt sayısı. Varsayılan değer 10000 ' dir. | No |

Havuz olarak REST Bağlayıcısı, JSON kabul eden REST API 'Leri ile birlikte çalışmaktadır. Veriler JSON 'da aşağıdaki Düzenle gönderilir. Gerektiğinde, kaynak verileri REST API beklenen yüküyle uyumlu olacak şekilde yeniden şekillendirmek için kopyalama etkinliği [şema eşlemesini](copy-activity-schema-and-type-mapping.md#schema-mapping) kullanabilirsiniz.

```json
[
    { <data object> },
    { <data object> },
    ...
]
```

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<REST output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "RestSink",
                "requestMethod": "POST",
                "httpRequestTimeout": "00:01:40",
                "requestInterval": 10,
                "writeBatchSize": 10000,
                "httpCompressionType": "none",
            },
        }
    }
]
```

## <a name="pagination-support"></a>Sayfalandırma desteği

Normal olarak, REST API 'lerden veri kopyalarken, REST API tek bir isteğin yanıt yükü boyutunu makul bir sayı altında sınırlandırır. büyük miktarda veri döndürülirken, sonucu birden çok sayfaya böler ve çağıranların sonraki sayfasına ulaşmak için birbirini izleyen istekler göndermesini gerektirir. Genellikle, bir sayfa için istek dinamik ve önceki sayfanın yanıtından döndürülen bilgiler tarafından oluşur.

Bu genel REST Bağlayıcısı aşağıdaki sayfalandırma düzenlerini destekler: 

* Sonraki isteğin mutlak veya göreli URL = geçerli yanıt gövdesindeki Özellik değeri
* Sonraki isteğin mutlak veya göreli URL = geçerli yanıt başlıklarındaki üst bilgi değeri
* Sonraki isteğin sorgu parametresi = geçerli yanıt gövdesinde Özellik değeri
* Sonraki isteğin sorgu parametresi = geçerli yanıt başlıklarındaki üst bilgi değeri
* Sonraki isteğin üst bilgisi = geçerli yanıt gövdesinde Özellik değeri
* Sonraki isteğin üst bilgisi = geçerli yanıt başlıklarındaki üst bilgi değeri

**Sayfalandırma kuralları** , bir veya daha fazla büyük/küçük harf duyarlı anahtar-değer çifti içeren, veri kümesinde sözlük olarak tanımlanır. Bu yapılandırma, ikinci sayfadan başlayarak isteği oluşturmak için kullanılacaktır. Bağlayıcı, HTTP durum kodu 204 (Içerik yok) aldığında veya "Sayfaationrules" içindeki bir JSONPath ifadelerinden herhangi biri null döndürürse yineleme durdurulur.

Sayfalandırma kurallarında **desteklenen anahtarlar** :

| Anahtar | Description |
|:--- |:--- |
| AbsoluteUrl | Sonraki isteği vermek için URL 'YI gösterir. **Mutlak URL ya da GÖRELI URL** olabilir. |
| QueryParameters. *request_query_parameter* OR QueryParameters [' request_query_parameter '] | "request_query_parameter", bir sonraki HTTP istek URL 'sinde bir sorgu parametresi adına başvuran Kullanıcı tanımlı. |
| Bilgisinde. *request_header* VEYA üst bilgiler [' request_header '] | "request_header", bir sonraki HTTP isteğindeki bir üst bilgi adına başvuran Kullanıcı tanımlı. |

Sayfalandırma kurallarında **desteklenen değerler** :

| Değer | Açıklama |
|:--- |:--- |
| Bilgisinde. *response_header* VEYA üst bilgiler [' response_header '] | "response_header", geçerli HTTP yanıtında bir üst bilgi adına başvuran Kullanıcı tanımlı, sonraki istek vermek için kullanılacak değerdir. |
| "$" (Yanıt gövdesinin kökünü temsil eden) ile başlayan bir JSONPath ifadesi | Yanıt gövdesi yalnızca bir JSON nesnesi içermelidir. JSONPath ifadesi bir sonraki isteği vermek için kullanılacak tek bir temel değer döndürmelidir. |

**Örnek:**

Facebook Graph API aşağıdaki yapıda yanıtı döndürür ve bu durumda sonraki sayfanın URL 'SI disk belleğine gösterilir ***. ileri***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

Karşılık gelen REST kopyalama etkinliği kaynak yapılandırması özellikle `paginationRules` aşağıdaki gibidir:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>OAuth kullanma
Bu bölümde, OAuth kullanarak JSON biçimindeki Azure Data Lake Storage verileri REST bağlayıcısından kopyalamak için bir çözüm şablonunun nasıl kullanılacağı açıklanmaktadır. 

### <a name="about-the-solution-template"></a>Çözüm şablonu hakkında

Şablon iki etkinlik içerir:
- **Web** etkinliği, taşıyıcı belirtecini alır ve ardından onu yetkilendirme olarak sonraki kopyalama etkinliğine iletir.
- **Kopyalama** etkinliği, verileri REST 'ten Azure Data Lake Storage kopyalar.

Şablon iki parametre tanımlar:
- **Sinkcontainer** , verilerin Azure Data Lake Storage kopyalandığı kök klasör yoludur. 
- **Sinkdirectory** , verilerin Azure Data Lake Storage kopyalandığı kök altındaki Dizin yoludur. 

### <a name="how-to-use-this-solution-template"></a>Bu çözüm şablonunu kullanma

1. **OAuth şablonu kullanarak REST veya http 'Den kopyalamaya** gidin. Kaynak bağlantısı için yeni bir bağlantı oluşturun. 
    ![Yeni bağlantı oluştur](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Aşağıda yeni bağlı hizmet (REST) ayarları için önemli adımlar verilmiştir:
    
     1. **Taban URL 'si** altında kendı kaynak Rest hizmetiniz için URL parametresini belirtin. 
     2. **Kimlik doğrulama türü** için *anonim*' i seçin.
        ![Yeni REST bağlantısı](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Hedef bağlantı için yeni bir bağlantı oluşturun.  
    ![Yeni Gen2 bağlantısı](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. **Bu şablonu kullan**' ı seçin.
    ![Bu şablonu kullan](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Aşağıdaki örnekte gösterildiği gibi oluşturulan işlem hattını görürsünüz:  ![ ekran görüntüsü şablondan oluşturulan işlem hattını gösterir.](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. **Web** etkinliği ' ni seçin. **Ayarlar**' da, verileri kopyalamak istediğiniz hizmetin oturum açma API 'sinden OAuth taşıyıcı belirtecini almak için karşılık gelen **URL**'yi, **yöntemi**, **üstbilgileri** ve **metni** belirtin. Şablondaki yer tutucu, Azure Active Directory (AAD) OAuth örneğine örnek olarak gösterilebilir. Bkz. AAD kimlik doğrulaması REST Bağlayıcısı tarafından yerel olarak desteklenir, burada yalnızca OAuth Flow için bir örnektir. 

    | Özellik | Açıklama |
    |:--- |:--- |
    | URL |OAuth taşıyıcı belirtecini almak için URL 'yi belirtin. Örneğin, buradaki örnekte https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |
    | Yöntem | HTTP yöntemi. İzin verilen değerler **gönderi** ve **Get**. | 
    | Üst Bilgiler | Üst bilgi, HTTP isteğindeki bir üst bilgi adına başvuran Kullanıcı tanımlı ' dır. | 
    | Gövde | HTTP isteğinin gövdesi. | 

    ![İşlem Hattı](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. **Verileri Kopyala** etkinliği ' nde, *kaynak* sekmesini seçin, önceki adımdan alınan taşıyıcı belirtecinin (access_token), ek üstbilgiler altında **Yetkilendirme** olarak veri etkinliğini kopyalamak için geçirilebileceğini görebilirsiniz. İşlem hattı çalıştırmasını başlatmadan önce aşağıdaki özellikler için ayarları onaylayın.

    | Özellik | Açıklama |
    |:--- |:--- |
    | İstek yöntemi | HTTP yöntemi. İzin verilen değerler **Al** (varsayılan) ve **Post**. | 
    | Ek üstbilgiler | Ek HTTP istek üstbilgileri.| 

   ![Kaynak kimlik doğrulamasını Kopyala](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. **Hata Ayıkla**' yı seçin, **parametreleri** girin ve ardından **son**' u seçin.
   ![İşlem hattı çalıştırması](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. İşlem hattı çalıştırması başarıyla tamamlandığında, aşağıdaki örneğe benzer bir sonuç görürsünüz: işlem ![ hattı çalıştırma sonucu](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. **Eylemler** sütununda webactivity 'Nin "çıkış" simgesine tıklayın, hizmet tarafından döndürülen access_token görürsünüz.

   ![Belirteç çıkışı](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. **Eylemler** sütununda copyactivity ' nin "giriş" simgesine tıklayın, webactivity tarafından alınan access_token kimlik doğrulaması Için copyactivity 'ye geçtiğini görürsünüz. 

    ![Belirteç girişi](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Simgenin düz metin olarak günlüğe kaydedilmesini önlemek için, Web etkinliğinde "güvenli çıkış" ve kopyalama etkinliğinde "güvenli giriş" seçeneğini etkinleştirin.


## <a name="export-json-response-as-is"></a>JSON yanıtını olduğu gibi dışarı aktar

Bu REST bağlayıcısını, REST API JSON yanıtını farklı dosya tabanlı depolara dışarı aktarmak için kullanabilirsiniz. Bu tür şemadan bağımsız bir kopya elde etmek için, kopyalama etkinliğinde veri kümesi ve şema eşleme ' de "yapı" (kısaca *şema*) bölümünü atlayın.

## <a name="schema-mapping"></a>Şema eşleme

REST uç noktasından tablo havuzuna veri kopyalamak için [Şema eşlemesi](copy-activity-schema-and-type-mapping.md#schema-mapping)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Kopyalama etkinliğinin Azure Data Factory kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
