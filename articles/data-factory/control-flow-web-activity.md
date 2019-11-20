---
title: Azure Data Factory Web etkinliği
description: Bir işlem hattından REST uç noktasını çağırmak için Data Factory tarafından desteklenen denetim akışı etkinliklerinin biri olan Web etkinliğini nasıl kullanabileceğinizi öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 5929d4edac53b2be87e168b527034c5a473f154f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73678181"
---
# <a name="web-activity-in-azure-data-factory"></a>Azure Data Factory Web etkinliği
Web Etkinliği bir Data Factory işlem hattından özel bir REST uç noktasını çağırmak için kullanılabilir. Etkinlik tarafından kullanılacak ve erişilecek veri kümelerini ve bağlı hizmetleri geçirebilirsiniz.

> [!NOTE]
> Web etkinliği, yalnızca genel olarak gösterilen URL 'Leri çağırabilir. Özel bir sanal ağda barındırılan URL 'Ler için desteklenmez.

## <a name="syntax"></a>Sözdizimi

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
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
ad | Web etkinliğinin adı | Dize | Evet
type | **Webactivity**olarak ayarlanmalıdır. | Dize | Evet
method | Hedef uç nokta için REST API yöntemi. | dizisinde. <br/><br/>Desteklenen türler: "GET", "POST", "PUT" | Evet
url | Hedef uç nokta ve yol | Dize (veya dize resultType 'ı olan ifade). Bitiş noktasından yanıt almadıysanız etkinlik, bir hatayla 1 dakika sonra zaman aşımına uğrayacaktır. | Evet
Bilgisinde | İsteğe gönderilen üst bilgiler. Örneğin, bir istek için dili ve türü ayarlamak için: `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`. | Dize (veya dize resultType 'ı olan ifade) | Evet, Content-Type üst bilgisi gereklidir. `"headers":{ "Content-Type":"application/json"}`
bölümü | Uç noktaya gönderilen yükü temsil eder.  | Dize (veya dize resultType 'ı olan ifade). <br/><br/>İstek [yükü şeması](#request-payload-schema) 'nda istek yükü şeması bölümüne bakın. | POST/PUT yöntemleri için gereklidir.
kimlik doğrulaması | Uç noktayı çağırmak için kullanılan kimlik doğrulama yöntemi. Desteklenen türler "Basic, or ClientCertificate" dir. Daha fazla bilgi için bkz. [kimlik doğrulama](#authentication) bölümü. Kimlik doğrulaması gerekmiyorsa, bu özelliği dışlayın. | Dize (veya dize resultType 'ı olan ifade) | Hayır
kümelerinin | Uç noktaya geçirilen veri kümelerinin listesi. | Veri kümesi başvuruları dizisi. Boş bir dizi olabilir. | Evet
LinkedServices | Uç noktaya geçirilen bağlı hizmetlerin listesi. | Bağlı hizmet başvuruları dizisi. Boş bir dizi olabilir. | Evet

> [!NOTE]
> Web etkinliğinin çağırdığı REST uç noktaları JSON türünde bir yanıt döndürmelidir. Bitiş noktasından yanıt almadıysanız etkinlik, bir hatayla 1 dakika sonra zaman aşımına uğrayacaktır.

Aşağıdaki tabloda JSON içeriği için gereksinimler gösterilmektedir:

| Değer türü | İstek gövdesi | Yanıt gövdesi |
|---|---|---|
|JSON nesnesi | Destekleniyor | Destekleniyor |
|JSON dizisi | Destekleniyor <br/>(Mevcut olduğunda, JSON dizileri bir hata sonucu olarak çalışmaz. Bir çözüm devam ediyor.) | Desteklenmeyen |
| JSON değeri | Destekleniyor | Desteklenmeyen |
| JSON olmayan tür | Desteklenmeyen | Desteklenmeyen |
||||

## <a name="authentication"></a>Kimlik Doğrulaması

### <a name="none"></a>None
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

Veri Fabrikası için yönetilen kimlik kullanılarak erişim belirtecinin istendiği Kaynak URI 'sini belirtin. Azure Kaynak yönetimi API 'sini çağırmak için `https://management.azure.com/`kullanın. Yönetilen kimliklerin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure kaynaklarına ilişkin Yönetilen kimlikler genel bakış sayfası](/azure/active-directory/managed-identities-azure-resources/overview).

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

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
Bu örnekte, işlem hattının Web etkinliği REST uç noktasını çağırır. Bir Azure SQL bağlı hizmetini ve bir Azure SQL veri kümesini uç noktaya geçirir. REST uç noktası Azure SQL Server 'a bağlanmak için Azure SQL bağlantı dizesini kullanır ve SQL Server örneğinin adını döndürür.

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

- [İşlem Hattı Yürütme Etkinliği](control-flow-execute-pipeline-activity.md)
- [Her etkinlik için](control-flow-for-each-activity.md)
- [Meta Veri Alma Etkinliği](control-flow-get-metadata-activity.md)
- [Arama Etkinliği](control-flow-lookup-activity.md)
