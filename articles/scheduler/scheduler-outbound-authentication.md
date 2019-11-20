---
title: Giden kimlik doğrulama-Azure Zamanlayıcı
description: Azure Scheduler için giden kimlik doğrulamasını ayarlama veya kaldırma hakkında bilgi edinin
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 2ea09330fb8d3d97da5fbc197dba9668f1a4f685
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300857"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Azure Scheduler için giden kimlik doğrulaması

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) , [devre dışı bırakılmakta](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)olan Azure Scheduler 'ı değiştiriyor. Zamanlayıcı 'da ayarladığınız işlerle çalışmaya devam etmek için lütfen en kısa sürede [Azure Logic Apps geçirin](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

Azure Scheduler işlerinin, diğer Azure Hizmetleri, Salesforce.com, Facebook ve güvenli özel Web siteleri gibi kimlik doğrulaması gerektiren hizmetleri çağırması gerekebilir. Çağrılan hizmet, Scheduler işinin istenen kaynaklara erişip erişemeyeceğini tespit edebilir. 

Zamanlayıcı bu kimlik doğrulama modellerini destekler: 

* SSL/TLS istemci sertifikaları kullanırken *istemci sertifikası* kimlik doğrulaması
* *Temel* kimlik doğrulaması
* *OAuth* kimlik doğrulamasını Active Directory

## <a name="add-or-remove-authentication"></a>Kimlik doğrulaması ekleme veya kaldırma

* Bir Zamanlayıcı işine kimlik doğrulaması eklemek için, işi oluşturduğunuzda veya güncelleştirdiğinizde `authentication` JavaScript nesne gösterimi (JSON) alt öğesini `request` öğesine ekleyin. 

  Yanıtlar, `authentication` nesne içindeki bir put, Patch veya post isteği aracılığıyla Zamanlayıcı hizmetine geçirilen gizli dizileri hiçbir şekilde döndürmez. 
  Yanıtlar gizli bilgileri null olarak ayarlar veya kimliği doğrulanmış varlığı temsil eden bir ortak belirteç kullanabilir. 

* Bir Zamanlayıcı işinden kimlik doğrulamasını kaldırmak için, iş üzerinde açıkça bir put veya Patch isteği çalıştırın ve `authentication` nesneyi null olarak ayarlayın. Yanıt herhangi bir kimlik doğrulama özelliği içermemelidir.

## <a name="client-certificate"></a>İstemci sertifikası

### <a name="request-body---client-certificate"></a>İstek gövdesi-Istemci sertifikası

`ClientCertificate` Modeli kullanarak kimlik doğrulaması eklerken, istek gövdesinde bu ek öğeleri belirtin.  

| Öğe | Gerekli | Açıklama |
|---------|----------|-------------|
| **kimlik doğrulaması** (üst öğe) | SSL istemci sertifikası kullanmaya yönelik kimlik doğrulama nesnesi |
| **type** | Evet | Kimlik doğrulama türü. SSL istemci sertifikaları için değer `ClientCertificate`. |
| **Türk** | Evet | PFX dosyasının Base64 ile kodlanmış içeriği |
| **Parola** | Evet | PFX dosyasına erişim parolası |
||| 

### <a name="response-body---client-certificate"></a>Yanıt gövdesi-Istemci sertifikası 

Kimlik doğrulama bilgileriyle bir istek gönderildiğinde, yanıt bu kimlik doğrulama öğelerini içerir.

| Öğe | Açıklama | 
|---------|-------------| 
| **kimlik doğrulaması** (üst öğe) | SSL istemci sertifikası kullanmaya yönelik kimlik doğrulama nesnesi |
| **type** | Kimlik doğrulama türü. SSL istemci sertifikaları için değer `ClientCertificate`. |
| **certificateThumbprint** |Sertifikanın parmak izi |
| **certificateSubjectName** |Sertifika konusu ayırt edici adı |
| **certificateExpiration** | Sertifikanın sona erme tarihi |
||| 

### <a name="sample-rest-request---client-certificate"></a>Örnek REST isteği-Istemci sertifikası

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Örnek REST yanıtı-Istemci sertifikası

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Temel

### <a name="request-body---basic"></a>İstek gövdesi-temel

`Basic` Modeli kullanarak kimlik doğrulaması eklerken, istek gövdesinde bu ek öğeleri belirtin.

| Öğe | Gerekli | Açıklama |
|---------|----------|-------------|
| **kimlik doğrulaması** (üst öğe) | Temel kimlik doğrulaması kullanmak için kimlik doğrulama nesnesi | 
| **type** | Evet | Kimlik doğrulama türü. Temel kimlik doğrulaması için değer `Basic`. | 
| **Kullanıcı adı** | Evet | Kimlik doğrulama için Kullanıcı adı | 
| **Parola** | Evet | Kimlik doğrulaması için parola |
|||| 

### <a name="response-body---basic"></a>Yanıt gövdesi-temel

Kimlik doğrulama bilgileriyle bir istek gönderildiğinde, yanıt bu kimlik doğrulama öğelerini içerir.

| Öğe | Açıklama | 
|---------|-------------|
| **kimlik doğrulaması** (üst öğe) | Temel kimlik doğrulaması kullanmak için kimlik doğrulama nesnesi |
| **type** | Kimlik doğrulama türü. Temel kimlik doğrulaması için değer `Basic`. |
| **Kullanıcı adı** | Kimliği doğrulanmış Kullanıcı adı |
||| 

### <a name="sample-rest-request---basic"></a>Örnek REST isteği-temel

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Örnek REST yanıtı-temel

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>İstek gövdesi-Active Directory OAuth 

`ActiveDirectoryOAuth` Modeli kullanarak kimlik doğrulaması eklerken, istek gövdesinde bu ek öğeleri belirtin.

| Öğe | Gerekli | Açıklama |
|---------|----------|-------------|
| **kimlik doğrulaması** (üst öğe) | Evet | ActiveDirectoryOAuth kimlik doğrulamasını kullanmaya yönelik kimlik doğrulama nesnesi |
| **type** | Evet | Kimlik doğrulama türü. ActiveDirectoryOAuth kimlik doğrulaması için değer `ActiveDirectoryOAuth`. |
| **tenant** | Evet | Azure AD kiracısı için kiracı tanımlayıcısı. Azure AD kiracının kiracı tanımlayıcısını bulmak için Azure PowerShell ' de çalıştırın `Get-AzureAccount` . |
| **grubu** | Evet | Bu değer olarak `https://management.core.windows.net/`ayarlanır. | 
| **ClientID** | Evet | Azure AD uygulaması için istemci tanımlayıcısı | 
| **gizlilikle** | Evet | Belirteci isteyen istemcinin parolası | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Yanıt gövdesi-Active Directory OAuth

Kimlik doğrulama bilgileriyle bir istek gönderildiğinde, yanıt bu kimlik doğrulama öğelerini içerir.

| Öğe | Açıklama |
|---------|-------------|
| **kimlik doğrulaması** (üst öğe) | ActiveDirectoryOAuth kimlik doğrulamasını kullanmaya yönelik kimlik doğrulama nesnesi |
| **type** | Kimlik doğrulama türü. ActiveDirectoryOAuth kimlik doğrulaması için değer `ActiveDirectoryOAuth`. | 
| **tenant** | Azure AD kiracısı için kiracı tanımlayıcısı |
| **grubu** | Bu değer olarak `https://management.core.windows.net/`ayarlanır. |
| **ClientID** | Azure AD uygulaması için istemci tanımlayıcısı |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Örnek REST isteği-Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Örnek REST yanıtı-Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="see-also"></a>Ayrıca bkz.

* [Azure Scheduler nedir?](scheduler-intro.md)
* [Azure Scheduler kavramları, terminolojisi ve varlık hiyerarşisi](scheduler-concepts-terms.md)
* [Azure Scheduler sınırları, varsayılanları ve hata kodları](scheduler-limits-defaults-errors.md)
* [Azure Scheduler REST API](https://msdn.microsoft.com/library/mt629143)
* [Azure Scheduler PowerShell cmdlet’leri başvurusu](scheduler-powershell-reference.md)
