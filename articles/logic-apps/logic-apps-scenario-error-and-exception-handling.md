---
title: Özel durum işleme & hata günlüğü senaryosu-Azure Logic Apps
description: Gelişmiş özel durum işleme ve hata günlüğü Azure Logic Apps için gerçek kullanım örneği ve senaryo
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: hedidin
ms.author: estfan
ms.reviewer: LADocs
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 7930d487d367ee19b869becae5017f80ea1df8cb
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680171"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Senaryo: Logic Apps için özel durum işleme ve hata günlüğü

Bu senaryo, özel durum işlemeyi daha iyi desteklemek için bir mantıksal uygulamayı nasıl genişletebileceğinizi açıklar. Soruyu yanıtlamak için gerçek yaşam bir kullanım durumu kullandık: "destek özel durum ve hata işleme Azure Logic Apps"

> [!NOTE]
> Geçerli Azure Logic Apps şeması, eylem yanıtları için standart bir şablon sağlar. Bu şablon, bir API uygulamasından döndürülen iç doğrulama ve hata yanıtlarını içerir.

## <a name="scenario-and-use-case-overview"></a>Senaryo ve kullanım örneğine genel bakış

Bu senaryo için kullanım örneği olarak hikaye aşağıda verilmiştir: 

İyi bilinen bir sağlık kurumunda, Microsoft Dynamics CRM Online 'ı kullanarak bir hasta portalı oluşturacak bir Azure çözümü geliştiriyorum. Dynamics CRM Online hasta portalı ve Salesforce arasında randevu kayıtları göndermeleri gerekir. Tüm hasta kayıtları için [HL7 FHıR](http://www.hl7.org/implement/standards/fhir/) standardını kullanmanız istendi.

Projede iki önemli gereksinim vardı:  

* Dynamics CRM Online portalından gönderilen kayıtları günlüğe kaydetmek için bir yöntem
* İş akışında oluşan hataları görüntülemenin bir yolu

> [!TIP]
> Bu proje hakkında üst düzey bir video için bkz. [tümleştirme Kullanıcı grubu](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Tümleştirme Kullanıcı grubu").

## <a name="how-we-solved-the-problem"></a>Sorunu nasıl çözeceğiz

Günlük ve hata kayıtları için depo olarak [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") seçtik (Cosmos DB kayıtları belgeler olarak ifade eder). Azure Logic Apps tüm yanıtlar için standart bir şablon içerdiğinden, özel bir şema oluşturmak zorunda olmamız gerekir. Hem hata hem de günlük kayıtlarını **eklemek** ve **sorgulamak** için bir API uygulaması oluşturacağız. Ayrıca API uygulaması içinde her biri için bir şema tanımlayabiliriz.  

Başka bir gereksinim, belirli bir tarihten sonra kayıtları temizlemelidir. Cosmos DB, [yaşam süresi](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Yaşam süresi") (TTL) adlı bir özelliğe sahiptir ve bu, her bir kayıt veya koleksiyon Için bir **yaşam** değeri ayarlayabileceğimizi sağlar. Bu özellik Cosmos DB kayıtları el ile silme gereksinimini ortadan kaldırmıştır.

> [!IMPORTANT]
> Bu öğreticiyi tamamlayabilmeniz için bir Cosmos DB veritabanı ve iki koleksiyon (günlüğe kaydetme ve hata) oluşturmanız gerekir.

## <a name="create-the-logic-app"></a>Mantıksal uygulamayı oluşturma

İlk adım mantıksal uygulama oluşturmak ve uygulamayı Logic App Designer 'da açmak. Bu örnekte, üst-alt mantıksal uygulamaları kullanıyoruz. Zaten üst öğeyi oluşturduğumuz ve bir alt mantıksal uygulama oluşturduğumuz varsayılalım.

Dynamics CRM Online 'dan gelen kaydı günlüğe kaydedebilmemiz için en üst kısımdaki bir başlangıç yapın. Üst mantıksal uygulama bu alt öğeyi tetiklediği için bir **istek** tetikleyicisi kullanmalıdır.

### <a name="logic-app-trigger"></a>Mantıksal uygulama tetikleyicisi

Aşağıdaki örnekte gösterildiği gibi bir **istek** tetikleyicisi kullanıyoruz:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Adımlar

Hasta kaydının kaynağını (istek) Dynamics CRM Online portalından günlüğe kaydetmeniz gerekir.

1. Dynamics CRM Online 'dan yeni bir randevu kaydı almalıdır.

   CRM 'den gelen tetikleyici, bize **CRM PatentId**, **kayıt türü**, **Yeni veya güncelleştirilmiş kayıt** (yeni veya güncelleştirme Boolean değeri) ve **salesforceıd**sağlar. **Salesforceıd** yalnızca bir güncelleştirme için kullanıldığından null olabilir.
   CRM **Hatıd** ve **kayıt türü**kullanılarak CRM kaydı alınır.

2. Daha sonra, mantıksal uygulama Tasarımcısı 'nda gösterildiği gibi Azure Cosmos DB SQL API uygulaması **ınsertlogentry** işleminizi eklememiz gerekiyor.

   **Günlük girdisi Ekle**

   ![Günlük girdisi Ekle](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Hata girişi Ekle**

   ![Günlük girdisi Ekle](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Kayıt oluşturma hatası olup olmadığını denetleyin**

   ![Koşul](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Mantıksal uygulama kaynak kodu

> [!NOTE]
> Aşağıdaki örnekler yalnızca örnektir. Bu öğretici üretimde bir uygulamaya bağlı olduğundan, **kaynak düğümün** değeri bir randevunun zamanlanması ile ilgili özellikleri görüntülemeyebilir. > 

### <a name="logging"></a>Günlüğe kaydetme

Aşağıdaki mantıksal uygulama kodu örneği, günlüğün nasıl işleneceğini gösterir.

#### <a name="log-entry"></a>Günlük girdisi

Günlük girdisi eklemek için mantıksal uygulama kaynak kodu aşağıda verilmiştir.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Günlük isteği

API uygulamasına gönderilen günlük isteği iletisi aşağıda verilmiştir.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Günlük yanıtı

API uygulamasından günlük yanıtı iletisi aşağıda verilmiştir.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Şimdi hata işleme adımlarına bakalım.

### <a name="error-handling"></a>Hata işleme

Aşağıdaki mantıksal uygulama kodu örneği, nasıl hata işleme uygulayabileceğinizi gösterir.

#### <a name="create-error-record"></a>Hata kaydı oluştur

Bir hata kaydı oluşturmak için mantıksal uygulama kaynak kodu aşağıda verilmiştir.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Cosmos DB--istek içine hata Ekle

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Cosmos DB--Response 'a hata ekleme

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce hata yanıtı

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Yanıtı üst mantıksal uygulamaya geri döndür

Yanıtı aldıktan sonra, yanıtı üst mantıksal uygulamaya geri geçirebilirsiniz.

#### <a name="return-success-response-to-parent-logic-app"></a>Üst mantıksal uygulamaya başarı yanıtı dön

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Üst mantıksal uygulamaya hata yanıtı döndürün

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB deposu ve Portal

Çözümümüzde [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db)özellikleri eklendi.

### <a name="error-management-portal"></a>Hata yönetimi portalı

Hataları görüntülemek için Cosmos DB hata kayıtlarını görüntülemek üzere bir MVC web uygulaması oluşturabilirsiniz. **Liste**, **Ayrıntılar**, **düzenleme**ve **silme** işlemleri geçerli sürüme dahildir.

> [!NOTE]
> İşlemi Düzenle: Cosmos DB tüm belgeyi değiştirir. **Liste** ve **ayrıntı** görünümlerinde gösterilen kayıtlar yalnızca örneklerdir. Bunlar gerçek hasta randevusu kayıtları değildir.

Aşağıda, daha önce açıklanan yaklaşımla oluşturulan MVC uygulama ayrıntılarımızın örnekleri verilmiştir.

#### <a name="error-management-list"></a>Hata yönetimi listesi
![Hata Listesi](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Hata yönetimi ayrıntı görünümü
![Hata Ayrıntıları](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Günlük yönetimi portalı

Günlükleri görüntülemek için de bir MVC web uygulaması oluşturduk. Aşağıda, daha önce açıklanan yaklaşımla oluşturulan MVC uygulama ayrıntılarımızın örnekleri verilmiştir.

#### <a name="sample-log-detail-view"></a>Örnek günlük ayrıntı görünümü
![Günlük ayrıntısı görünümü](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API uygulaması ayrıntıları

#### <a name="logic-apps-exception-management-api"></a>Logic Apps özel durum yönetim API 'SI

Açık kaynaklı Azure Logic Apps özel durum yönetim API 'SI uygulamanız burada açıklanan işlevselliği sağlar-iki denetleyici vardır:

* **Errorcontroller** bir Azure Cosmos DB koleksiyonuna bir hata kaydı (belge) ekler.
* **Günlüğe kaydetme denetleyicisi** Bir Azure Cosmos DB koleksiyonuna bir günlük kaydı (belge) ekler.

> [!TIP]
> Her iki denetleyici de `async Task<dynamic>` işlemler kullanır, bu nedenle işlem gövdesinde Azure Cosmos DB şeması oluşturuyoruz. 
> 

Azure Cosmos DB içindeki her belge benzersiz bir KIMLIĞE sahip olmalıdır. @No__t_0 kullanıyor ve bir Unix zaman damgası değerine (Double) dönüştürülen bir zaman damgası ekliyoruz. Kesirli değeri kaldırmak için değeri kestik.

[GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs)'dan hata denetleyicisi API 'sinin kaynak kodunu görüntüleyebilirsiniz.

Aşağıdaki sözdizimini kullanarak bir mantıksal uygulamadan API 'YI çağırıyoruz:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Önceki kod örneğindeki ifade, **başarısız**olup olmadığını kontrol *Create_NewPatientRecord* durumunu denetler.

## <a name="summary"></a>Özet

* Bir mantıksal uygulamada günlüğe kaydetme ve hata işlemeyi kolayca uygulayabilirsiniz.
* Günlük ve hata kayıtları (belgeler) için depo olarak Azure Cosmos DB kullanabilirsiniz.
* Günlük ve hata kayıtlarını göstermek için bir portal oluşturmak üzere MVC 'yi kullanabilirsiniz.

### <a name="source-code"></a>Kaynak kodu

Logic Apps özel durum yönetim API 'SI uygulamasının kaynak kodu bu [GitHub deposunda](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Mantıksal uygulama özel durum yönetim API 'SI")kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Daha fazla Logic App örneği ve senaryosu görüntüleyin](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Mantıksal uygulamaları izleme hakkında bilgi edinin](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [Mantıksal uygulama dağıtımını otomatikleştirme](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
