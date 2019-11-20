---
title: Azure Data Factory kullanarak REST kaynağından veri kopyalama
description: Azure Data Factory bir işlem hattındaki kopyalama etkinliği kullanarak bir bulut veya şirket içi REST kaynağından desteklenen havuz veri depolarına veri kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 6bb597ab49050c2bb365379cfac44f4b4d176af1
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680402"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory kullanarak REST uç noktasından veri kopyalama

Bu makalede, bir REST uç noktasından veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Makale, kopyalama etkinliğine genel bir bakış sunan [Azure Data Factory kopyalama etkinliği](copy-activity-overview.md)üzerinde oluşturulur.

Bu REST Bağlayıcısı, [http Bağlayıcısı](connector-http.md) ve [Web tablosu Bağlayıcısı](connector-web-table.md) arasındaki fark şudur:

- **Rest Bağlayıcısı** , verilerin yeniden oluşturulmuş API 'lerden kopyalanmasını özellikle destekler; 
- **Http Bağlayıcısı** , örneğin dosyayı indirmek için HERHANGI bir HTTP uç noktasından veri almak için geneldir. Bu REST Bağlayıcısı kullanılabilir hale gelmeden önce, desteklenen ancak REST bağlayıcısıyla daha az işlevsel bir işlem olan API 'den veri kopyalamak için HTTP bağlayıcısını kullanabilirsiniz.
- **Web tablosu Bağlayıcısı** , tablo IÇERIĞINI bir HTML Web sayfasından ayıklar.

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bir REST kaynağından, desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliğinin kaynak ve havuz olarak desteklediği veri depolarının bir listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).

Özellikle, bu genel REST Bağlayıcısı şunları destekler:

- **Get** veya **Post** yöntemlerini kullanarak bir REST uç noktasından veri alma.
- Aşağıdaki kimlik doğrulamalarından birini kullanarak verileri alma: **anonim**, **temel**, **AAD hizmet sorumlusu**ve **Azure kaynakları için Yönetilen kimlikler**.
- REST API 'Lerinde **[sayfalandırma](#pagination-support)** .
- REST JSON yanıtını [olduğu gibi](#export-json-response-as-is) kopyalama veya [şema eşleme](copy-activity-schema-and-type-mapping.md#schema-mapping)kullanarak ayrıştırma. Yalnızca **JSON** 'daki yanıt yükü desteklenir.

> [!TIP]
> Data Factory ' de REST bağlayıcısını yapılandırmadan önce veri alımı isteğini test etmek için, üst bilgi ve gövde gereksinimlerine yönelik API belirtimi hakkında bilgi edinin. Doğrulamak için Postman veya bir Web tarayıcısı gibi araçları kullanabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, REST bağlayıcısına özgü Data Factory varlıkları tanımlamak için kullanabileceğiniz özelliklerle ilgili ayrıntıları sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

REST bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | **Type** özelliği **RESTService**olarak ayarlanmalıdır. | Evet |
| url | REST hizmetinin temel URL 'SI. | Evet |
| enableServerCertificateValidation | Uç noktaya bağlanılırken sunucu tarafı SSL sertifikasının doğrulanması gerekip gerekmediğini belirtir. | Hayır<br /> (varsayılan değer **true**'dur) |
| authenticationType | REST hizmetine bağlanmak için kullanılan kimlik doğrulaması türü. İzin verilen değerler **anonim**, **temel**, **Aadserviceprincipal** ve **managedserviceıdentity**. Daha fazla özellik ve örnekte sırasıyla aşağıdaki ilgili bölümlere bakın. | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [Integration Runtime](concepts-integration-runtime.md) . [Önkoşullar](#prerequisites) bölümünden daha fazla bilgi edinin. Belirtilmemişse, bu özellik varsayılan Azure Integration Runtime kullanır. |Hayır |

### <a name="use-basic-authentication"></a>Temel kimlik doğrulaması kullan

**AuthenticationType** özelliğini **temel**olarak ayarlayın. Önceki bölümde açıklanan genel özelliklere ek olarak, aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| Nitelen | REST uç noktasına erişmek için kullanılacak Kullanıcı adı. | Evet |
| password | Kullanıcı için parola ( **Kullanıcı adı** değeri). Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** türü olarak işaretleyin. Ayrıca, [Azure Key Vault depolanan bir gizli](store-credentials-in-key-vault.md)dizi için de başvurabilirsiniz. | Evet |

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

**AuthenticationType** özelliğini **Aadserviceprincipal**olarak ayarlayın. Önceki bölümde açıklanan genel özelliklere ek olarak, aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| Serviceprincipalıd | Azure Active Directory uygulamasının istemci KIMLIĞINI belirtin. | Evet |
| Servicesprincipalkey | Azure Active Directory uygulamasının anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı **SecureString** olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet |
| Kiracı | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Evet |
| Aadresourceıd | Yetkilendirme için istediğiniz AAD kaynağını belirtin, örn. `https://management.core.windows.net`.| Evet |

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

### <a name="managed-identity"></a>Azure kaynakları kimlik doğrulaması için Yönetilen kimlikler kullanma

**AuthenticationType** özelliğini **managedserviceıdentity**olarak ayarlayın. Önceki bölümde açıklanan genel özelliklere ek olarak, aşağıdaki özellikleri belirtin:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| Aadresourceıd | Yetkilendirme için istediğiniz AAD kaynağını belirtin, örn. `https://management.core.windows.net`.| Evet |

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

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bu bölüm, REST veri kümesinin desteklediği özelliklerin bir listesini sağlar. 

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri ve bağlı hizmetler](concepts-datasets-linked-services.md). 

REST 'ten veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | DataSet 'in **Type** özelliği **restresource**olarak ayarlanmalıdır. | Evet |
| relativeUrl 'Si | Verileri içeren kaynağın göreli URL 'SI. Bu özellik belirtilmediğinde, yalnızca bağlı hizmet tanımında belirtilen URL kullanılır. | Hayır |

Veri kümesinde `requestMethod`, `additionalHeaders`, `requestBody` ve `paginationRules` ayarlıyorsanız, hala olduğu gibi desteklenirken, etkinlik kaynağı ' nda yeni modeli kullanmanız önerilir.

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

Bu bölüm, REST kaynağının desteklediği özelliklerin bir listesini sağlar.

Etkinlikleri tanımlamak için kullanılabilen bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>Kaynak olarak REST

Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının **Type** özelliği **restsource**olarak ayarlanmalıdır. | Evet |
| requestMethod | HTTP yöntemi. İzin verilen değerler **Al** (varsayılan) ve **Post**. | Hayır |
| additionalHeaders | Ek HTTP istek üstbilgileri. | Hayır |
| Istek gövdesi | HTTP isteğinin gövdesi. | Hayır |
| Sayfaationrules | Sonraki sayfa isteklerini oluşturmak için sayfalandırma kuralları. Ayrıntılar için [sayfalandırma desteği](#pagination-support) bölümüne bakın. | Hayır |
| httpRequestTimeout | HTTP isteğinin yanıt almak için zaman aşımı ( **TimeSpan** değeri). Bu değer, yanıt verilerinin okunması için zaman aşımı değil, yanıt almaya yönelik zaman aşımı değeridir. Varsayılan değer **00:01:40**' dir.  | Hayır |
| Requestınterval | Sonraki sayfa için istek gönderilmeden önce beklenecek süre. Varsayılan değer **00:00:01** ' dir |  Hayır |

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

## <a name="pagination-support"></a>Sayfalandırma desteği

Genellikle REST API, tek bir isteğin yanıt yük boyutunu makul bir sayı altında sınırlandırır. büyük miktarda veri döndürülirken, sonucu birden çok sayfaya böler ve çağıranların sonraki sayfasına ulaşmak için birbirini izleyen istekler göndermesini gerektirir. Genellikle, bir sayfa için istek dinamik ve önceki sayfanın yanıtından döndürülen bilgiler tarafından oluşur.

Bu genel REST Bağlayıcısı aşağıdaki sayfalandırma düzenlerini destekler: 

* Sonraki isteğin mutlak veya göreli URL = geçerli yanıt gövdesindeki Özellik değeri
* Sonraki isteğin mutlak veya göreli URL = geçerli yanıt başlıklarındaki üst bilgi değeri
* Sonraki isteğin sorgu parametresi = geçerli yanıt gövdesinde Özellik değeri
* Sonraki isteğin sorgu parametresi = geçerli yanıt başlıklarındaki üst bilgi değeri
* Sonraki isteğin üst bilgisi = geçerli yanıt gövdesinde Özellik değeri
* Sonraki isteğin üst bilgisi = geçerli yanıt başlıklarındaki üst bilgi değeri

**Sayfalandırma kuralları** , bir veya daha fazla büyük/küçük harf duyarlı anahtar-değer çifti içeren veri kümesinde sözlük olarak tanımlanır. Bu yapılandırma, ikinci sayfadan başlayarak isteği oluşturmak için kullanılacaktır. Bağlayıcı, HTTP durum kodu 204 (Içerik yok) aldığında veya "Sayfaationrules" içindeki bir JSONPath ifadelerinden herhangi biri null döndürürse yineleme durdurulur.

Sayfalandırma kurallarında **desteklenen anahtarlar** :

| Anahtar | Açıklama |
|:--- |:--- |
| AbsoluteUrl | Sonraki isteği vermek için URL 'YI gösterir. **Mutlak URL ya da GÖRELI URL**olabilir. |
| QueryParameters. *request_query_parameter* OR QueryParameters [' request_query_parameter '] | "request_query_parameter", bir sonraki HTTP istek URL 'sinde bir sorgu parametresi adına başvuran Kullanıcı tanımlı ' dır. |
| Bilgisinde. *request_header* VEYA üstbilgiler [' request_header '] | "request_header", bir sonraki HTTP isteğindeki bir üst bilgi adına başvuran Kullanıcı tanımlı ' dır. |

Sayfalandırma kurallarında **desteklenen değerler** :

| Değer | Açıklama |
|:--- |:--- |
| Bilgisinde. *response_header* VEYA üstbilgiler [' response_header '] | "response_header", geçerli HTTP yanıtında bir üst bilgi adına başvuran, bir sonraki isteği vermek için kullanılacak olan Kullanıcı tanımlı ' dır. |
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

## <a name="export-json-response-as-is"></a>JSON yanıtını olduğu gibi dışarı aktar

Bu REST bağlayıcısını, REST API JSON yanıtını farklı dosya tabanlı depolara dışarı aktarmak için kullanabilirsiniz. Bu tür şemadan bağımsız bir kopya elde etmek için, kopyalama etkinliğinde veri kümesi ve şema eşleme ' de "yapı" (kısaca *şema*) bölümünü atlayın.

## <a name="schema-mapping"></a>Şema eşleme

REST uç noktasından tablo havuzuna veri kopyalamak için [Şema eşlemesi](copy-activity-schema-and-type-mapping.md#schema-mapping)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Kopyalama etkinliğinin Azure Data Factory kaynak ve havuz olarak desteklediği veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
