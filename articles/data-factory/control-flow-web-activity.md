---
title: Azure Data Factory Web etkinliği
description: Bir işlem hattından REST uç noktasını çağırmak için Data Factory tarafından desteklenen denetim akışı etkinliklerinin biri olan Web etkinliğini nasıl kullanabileceğinizi öğrenin.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: e4578b41e5cbb62c8a1bfa0c48d4fd60d042a506
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361528"
---
# <a name="web-activity-in-azure-data-factory"></a>Azure Data Factory Web etkinliği
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Web Etkinliği bir Data Factory işlem hattından özel bir REST uç noktasını çağırmak için kullanılabilir. Etkinlik tarafından kullanılacak ve erişilecek veri kümelerini ve bağlı hizmetleri geçirebilirsiniz.

> [!NOTE]
> Web Etkinliği hem özel sanal ağda barındırılan URL'leri çağırmak için hem de şirket içinde barındırılan tümleştirme çalışma zamanı tarafından desteklenir. Tümleştirme çalışma zamanının URL uç noktasında bir görüş alanı olmalıdır. 

> [!NOTE]
> Desteklenen en büyük çıkış yanıtı yükü boyutu 4 MB 'tır.  

## <a name="syntax"></a>Syntax

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "connectVia": {
          "referenceName": "<integrationRuntimeName>",
          "type": "IntegrationRuntimeReference"
      }
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>Tür özellikleri

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | --------
name | Web etkinliğinin adı | Dize | Yes
tür | **Webactivity** olarak ayarlanmalıdır. | Dize | Yes
method | Hedef uç nokta için REST API yöntemi. | Dize. <br/><br/>Desteklenen türler: "GET", "POST", "PUT" | Yes
url | Hedef uç nokta ve yol | Dize (veya dize resultType 'ı olan ifade). Etkinlik uç noktadan yanıt almazsa, 1 dakikanın sonunda zaman aşımına uğrar ve hata verir. | Yes
bilgisinde | İsteğe gönderilen üst bilgiler. Örneğin, bir istek için dili ve türü ayarlamak için: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` . | Dize (veya dize resultType 'ı olan ifade) | Evet, Content-Type üst bilgisi gereklidir. `"headers":{ "Content-Type":"application/json"}`
body | Uç noktaya gönderilen yükü temsil eder.  | Dize (veya dize resultType 'ı olan ifade). <br/><br/>İstek [yükü şeması](#request-payload-schema) 'nda istek yükü şeması bölümüne bakın. | POST/PUT yöntemleri için gereklidir.
kimlik doğrulaması | Uç noktayı çağırmak için kullanılan kimlik doğrulama yöntemi. Desteklenen türler "Basic, or ClientCertificate" dir. Daha fazla bilgi için bkz. [kimlik doğrulama](#authentication) bölümü. Kimlik doğrulaması gerekmiyorsa, bu özelliği dışlayın. | Dize (veya dize resultType 'ı olan ifade) | No
veri kümeleri | Uç noktaya geçirilen veri kümelerinin listesi. | Veri kümesi başvuruları dizisi. Boş bir dizi olabilir. | Yes
linkedServices | Uç noktaya geçirilen bağlı hizmetlerin listesi. | Bağlı hizmet başvuruları dizisi. Boş bir dizi olabilir. | Yes
connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](./concepts-integration-runtime.md) . Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını (veri depolduğunuz özel bir ağda olması halinde) kullanabilirsiniz. Bu özellik belirtilmezse, hizmet varsayılan Azure tümleştirme çalışma zamanını kullanır. | Integration Runtime başvurusu. | No 

> [!NOTE]
> Web etkinliğinin çağırdığı REST uç noktaları JSON türünde bir yanıt döndürmelidir. Etkinlik uç noktadan yanıt almazsa, 1 dakikanın sonunda zaman aşımına uğrar ve hata verir.

Aşağıdaki tabloda JSON içeriği için gereksinimler gösterilmektedir:

| Değer türü | İstek gövdesi | Yanıt gövdesi |
|---|---|---|
|JSON nesnesi | Desteklenir | Desteklenir |
|JSON dizisi | Desteklenir <br/>(Mevcut olduğunda, JSON dizileri bir hata sonucu olarak çalışmaz. Bir çözüm devam ediyor.) | Desteklenmeyen |
| JSON değeri | Desteklenir | Desteklenmeyen |
| JSON olmayan tür | Desteklenmeyen | Desteklenmeyen |
||||

## <a name="authentication"></a>Kimlik Doğrulaması

Aşağıda, Web etkinliğinde desteklenen kimlik doğrulama türleri verilmiştir.

### <a name="none"></a>Yok

Kimlik doğrulaması gerekmiyorsa, "Authentication" özelliğini eklemeyin.

### <a name="basic"></a>Temel

Temel kimlik doğrulamasıyla kullanılacak kullanıcı adını ve parolayı belirtin.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>İstemci sertifikası

PFX dosyası ve parolanın Base64 ile kodlanmış içeriğini belirtin.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>Yönetilen Kimlik

Veri Fabrikası için yönetilen kimlik kullanılarak erişim belirtecinin istendiği Kaynak URI 'sini belirtin. Azure Kaynak yönetimi API 'sini çağırmak için kullanın `https://management.azure.com/` . Yönetilen kimliklerin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure kaynaklarına ilişkin Yönetilen kimlikler genel bakış sayfası](../active-directory/managed-identities-azure-resources/overview.md).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> Data Factory 'niz bir git deposu ile yapılandırıldıysa, temel veya istemci sertifikası kimlik doğrulamasını kullanmak için kimlik bilgilerinizi Azure Key Vault depolamanız gerekir. Azure Data Factory, parolaları git 'te depolamaz.

## <a name="request-payload-schema"></a>İstek yük şeması
POST/PUT yöntemini kullandığınızda Body özelliği uç noktaya gönderilen yükü temsil eder. Bağlı hizmetleri ve veri kümelerini yükün bir parçası olarak geçirebilirsiniz. Yükün şeması aşağıda verilmiştir:

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>Örnek
Bu örnekte, işlem hattının Web etkinliği REST uç noktasını çağırır. Bir Azure SQL bağlı hizmetini ve bir Azure SQL veri kümesini uç noktaya geçirir. REST uç noktası, mantıksal SQL Server 'a bağlanmak için Azure SQL bağlantı dizesini kullanır ve SQL Server örneğinin adını döndürür.

### <a name="pipeline-definition"></a>İşlem hattı tanımı

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>İşlem hattı parametre değerleri

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Web hizmeti uç noktası kodu

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>Sonraki adımlar
Data Factory tarafından desteklenen diğer denetim akışı etkinliklerini görün:

- [İşlem hattı yürütme etkinliği](control-flow-execute-pipeline-activity.md)
- [Her Bir Etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
