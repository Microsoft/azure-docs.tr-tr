---
title: MS Graph API kullanarak bulut eşitlemesini programlı olarak yapılandırma
description: Bu konuda yalnızca Graph API kullanarak gelen eşitlemenin nasıl etkinleştirileceği açıklanmaktadır
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fe220cf7b5cb8b67e5ab7ded221494e89a28aa5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530257"
---
# <a name="how-to-programmatically-configure-cloud-sync-using-ms-graph-api"></a>MS Graph API kullanarak bulut eşitlemesini programlı olarak yapılandırma

Aşağıdaki belge, yalnızca MSGraph API 'Leri kullanarak bir eşitleme profilinin sıfırdan nasıl çoğaltılacağını açıklamaktadır.  
Bunun nasıl yapılacağını gösteren yapı aşağıdaki adımlardan oluşur.  Bunlar:

- [MS Graph API kullanarak bulut eşitlemesini programlı olarak yapılandırma](#how-to-programmatically-configure-cloud-sync-using-ms-graph-api)
  - [Temel kurulum](#basic-setup)
    - [Kiracı bayraklarını etkinleştir](#enable-tenant-flags)
  - [Hizmet sorumlusu oluşturma](#create-service-principals)
  - [Eşitleme işi oluştur](#create-sync-job)
  - [Hedeflenen etki alanını güncelleştir](#update-targeted-domain)
  - [Yapılandırma dikey penceresinde eşitleme parolası karmalarını etkinleştir](#enable-sync-password-hashes-on-configuration-blade)
  - [Yanlışlıkla silme](#accidental-deletes)
    - [Eşiği etkinleştirme ve ayarlama](#enabling-and-setting-the-threshold)
    - [Silmeleri izin verme](#allowing-deletes)
  - [Eşitleme işini Başlat](#start-sync-job)
  - [İnceleme durumu](#review-status)
  - [Sonraki adımlar](#next-steps)

Bu [Windows PowerShell için Microsoft Azure Active Directory modülü](/powershell/module/msonline/) komutlarını, bu kiracı Için Yönetim Web hizmetini çağırabilmek üzere bir üretim kiracısı için eşitlemeyi etkinleştirmek üzere kullanın.

## <a name="basic-setup"></a>Temel kurulum

### <a name="enable-tenant-flags"></a>Kiracı bayraklarını etkinleştir

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
Bu iki komutun ilki Azure Active Directory kimlik bilgilerini gerektirir. Bu commandde kiracıyı örtülü olarak tanımlayıp eşitleme için etkinleştirebilmenizi sağlar.

## <a name="create-service-principals"></a>Hizmet sorumlusu oluşturma
Sonra, [AD2AAD uygulaması/hizmet sorumlusunu](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http&preserve-view=true) oluşturuyoruz

Bu uygulama KIMLIĞI 1a4721b3-e57f-4451-ae87-ef078703ec94 kullanmanız gerekir. DisplayName, portalda kullanılıyorsa (örneğin, contoso.com) AD etki alanı URL 'sidir, ancak başka bir şey adlandırılmış olabilir.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Eşitleme işi oluştur
Yukarıdaki komutun çıktısı, oluşturulan hizmet sorumlusunun ObjectID değerini döndürür. Bu örnekte, ObjectID 614ac0e9-a59b-481f-bd8f-79a73d167e1c olur.  Bu hizmet sorumlusuna bir synchronizationJob eklemek için Microsoft Graph kullanın.  

Eşitleme işi oluşturmaya yönelik belgeler [burada](/graph/api/synchronization-synchronizationjob-post?tabs=http&view=graph-rest-beta&preserve-view=true)bulunabilir.

Yukarıdaki KIMLIĞI kaydetmediyseniz, aşağıdaki MS Graf çağrısını çalıştırarak hizmet sorumlusunu bulabilirsiniz. Dizin. Read. bu çağrıyı yapmak için tüm izinlere sahip olmanız gerekir:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Ardından çıktıda Uygulama adınızı arayın.

İki iş oluşturmak için aşağıdaki iki komutu çalıştırın: biri Kullanıcı/Grup sağlama için ve diğeri Parola karması eşitlemesi için. Aynı istek iki kez aynı ancak farklı şablon kimlikleriyle aynı.


Aşağıdaki iki isteği çağırın:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Her ikisini de oluşturmak istiyorsanız iki çağrıya ihtiyaç duyarsınız.

Örnek dönüş değeri (sağlama için):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Hedeflenen etki alanını güncelleştir
Bu kiracı için, hizmet sorumlusunun nesne tanımlayıcısı ve uygulama tanımlayıcısı aşağıdaki gibidir:

ObjectID: 8895955e-2e6c-4d79-8943-4d72ca36878f AppID: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Bu yapılandırmanın hedeflediği etki alanını güncelleştirmemiz gerekiyor, bu nedenle bu etki alanı için gizli dizileri güncelleştirin.

Kullandığınız etki alanı adının, şirket içi etki alanı denetleyiciniz için ayarladığınız URL ile aynı olduğundan emin olun

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```
 Aşağıdaki değer dizisine, yapmaya çalıştığınız değere göre aşağıdaki anahtar/değer çiftini ekleyin:
 - Hem PHS hem de Sync kiracı bayraklarını etkinleştir {anahtar: "AppKey", değer: "{" appKeyScenario ":" AD2AADPasswordHash "}"}
 
 - Yalnızca eşitleme kiracı bayrağını etkinleştir (PHS 'yi açma) {Key: "AppKey", değer: "{" appKeyScenario ":" AD2AADProvisioning "}"}
 ```
 Request body –
 {
    "value": [
               {
                 "key": "Domain",
                 "value": "{\"domain\":\"ad2aadTest.com\"}"
               }
             ]
  }
```

Beklenen yanıt... HTTP 204/içerik yok

Burada, vurgulanan "etki alanı" değeri, girişlerin Azure Active Directory sağlanacak olan şirket içi Active Directory etki alanının adıdır.

## <a name="enable-sync-password-hashes-on-configuration-blade"></a>Yapılandırma dikey penceresinde eşitleme parolası karmalarını etkinleştir

 Bu bölüm, belirli bir yapılandırma için parola karmalarının eşitlenmesini etkinleştirmeyi kapsar. Bu, yalnızca tek bir etki alanı/yapılandırma için olan kiracı düzeyinde özellik bayrağını sağlayan AppKey gizlilikten farklıdır. Bunun uçtan uca olması için uygulama anahtarını PHS 'ye ayarlamanız gerekir.

1. Şemayı alın (uyarı bu oldukça büyük) 
 ```
 GET –https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
 ```
2. Bu CredentialData öznitelik eşlemesini al:
 ``` 
 {
 "defaultValue": null,
 "exportMissingReferences": false,
 "flowBehavior": "FlowWhenChanged",
 "flowType": "Always",
 "matchingPriority": 0,
 "targetAttributeName": "CredentialData",
 "source": {
 "expression": "[PasswordHash]",
 "name": "PasswordHash",
 "type": "Attribute",
 "parameters": []
 }
 ```
3. Şemada aşağıdaki adlarla aşağıdaki nesne eşlemelerini bul
 - Active Directory Kullanıcıları sağlama
 - İnetOrgPersons Active Directory sağlama

 Nesne eşlemeleri Schema. synchronizationRules [0]. objectMappings içinde (Şu anda yalnızca 1 eşitleme kuralı olduğunu varsayabilirsiniz)

4. (2) adımından CredentialData eşlemesini alın ve adımdaki nesne eşlemelerine ekleyin (3)

 Nesne eşleme şuna benzer şekilde görünür:
 ```
 {
 "enabled": true,
 "flowTypes": "Add,Update,Delete",
 "name": "Provision Active Directory users",
 "sourceObjectName": "user",
 "targetObjectName": "User",
 "attributeMappings": [
 ...
 } 
 ```
 Yukarıdaki **Create AD2AADProvisioning ve AD2AADPasswordHash Jobs** adımındaki eşlemeyi attributemappings dizisine kopyalayın/yapıştırın. 

 Bu dizideki öğelerin sırası önemi yoktur (arka uçta sizin için sıralama yapılır). Ad zaten dizide varsa, bu öznitelik eşlemesini ekleme konusunda dikkatli olun (örn. targetAttributeName CredentialData özniteliği olan attributeMappings 'de zaten bir öğe varsa)-çakışma hataları alabilir veya önceden var olan ve yeni eşlemeler birlikte birleştirilebilir (genellikle istenen sonuç değildir). Arka uç, sizin için kaldırma işlemi yapmaz. 

 Bunu hem kullanıcılar hem de inetOrgpersons için bunu unutmayın

5. Oluşturduğunuz şemayı kaydetme 
 ```
 PUT –
 https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ [AD2AADProvisioningJobId]/schema
```

 Şemayı istek gövdesine ekleyin. 

## <a name="accidental-deletes"></a>Yanlışlıkla silme
Bu bölümde program aracılığıyla [yanlışlıkla silme](how-to-accidental-deletes.md) /devre dışı bırakma ve programlı silme işlemlerinin nasıl yapılacağı ele alınacaktır.


### <a name="enabling-and-setting-the-threshold"></a>Eşiği etkinleştirme ve ayarlama
Kullanabileceğiniz her iş ayarı için iki işlem vardır:

 - DeleteThresholdEnabled-' true ' olarak ayarlandığında iş için yanlışlıkla silme engellemesini sağlar. Varsayılan olarak ' true ' olarak ayarlayın.
 - DeleteThresholdValue-yanlışlıkla silme engellemesi etkinken, işin her yürütmesinde izin verilecek en fazla silme sayısını tanımlar. Değer varsayılan olarak 500 olarak ayarlanır.  Bu nedenle, değer 500 olarak ayarlandıysa, izin verilen en fazla silme sayısı her yürütmede 499 olacaktır.

Silme eşiği ayarları öğesinin bir parçasıdır `SyncNotificationSettings` ve Graf aracılığıyla değiştirilebilir. 

Bu yapılandırmanın hedeflediği SyncNotificationSettings öğesini güncelleştirmemiz gerekiyor, bu nedenle gizli dizileri güncelleştirin.

 ```
 PUT – https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 ```

 Aşağıdaki değer dizisine, yapmaya çalıştığınız değere göre aşağıdaki anahtar/değer çiftini ekleyin:

```
 Request body -
 {
   "value":[
             {
               "key":"SyncNotificationSettings",
               "value": "{\"Enabled\":true,\"Recipients\":\"foobar@xyz.com\",\"DeleteThresholdEnabled\":true,\"DeleteThresholdValue\":50}"
              }
            ]
  }


```

Yukarıdaki örnekteki "etkin" ayarı, iş karantinaya alındığında bildirim e-postalarını etkinleştirmek/devre dışı bırakmak içindir.


Şu anda gizli dizileri için düzeltme eki isteklerini desteklememiz, bu nedenle diğer değerleri korumak için PUT isteğinin gövdesindeki tüm değerleri (Yukarıdaki örnekte olduğu gibi) eklemeniz gerekir.

Tüm gizli dizileri için varolan değerler kullanılarak alınabilir 

```
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
```

### <a name="allowing-deletes"></a>Silmeleri izin verme
İş karantinasına alındıktan sonra silmelerde akışa izin vermek için, kapsam olarak yalnızca "Forcesilmeleri" ile yeniden başlatma yapmanız gerekir. 

```
Request:
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

```
Request Body:
{
  "criteria": {"resetScope": "ForceDeletes"}
}
```






## <a name="start-sync-job"></a>Eşitleme işini Başlat
İş aşağıdaki komutla yeniden alınabilir:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

İşlerin alınmasına ilişkin belgeler [burada](/graph/api/synchronization-synchronizationjob-list?tabs=http&view=graph-rest-beta&preserve-view=true)bulunabilir. 
 
İşi başlatmak için, ilk adımda oluşturulan hizmet sorumlusu ObjectID 'yi ve işi oluşturan istekten döndürülen iş tanımlayıcısını kullanarak bu isteği verin.

Bir işi başlatmaya ilişkin belgeler [burada](/graph/api/synchronization-synchronizationjob-start?tabs=http&view=graph-rest-beta&preserve-view=true)bulunabilir. 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

Beklenen yanıt... HTTP 204/içerik yok.

İşi denetlemeye yönelik diğer komutlar [burada](/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta&preserve-view=true)belgelenmiştir.
 
Bir işi yeniden başlatmak için, bunlardan biri kullanılır...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>İnceleme durumu
İş durumlarını aracılığıyla alın...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

İlgili ayrıntılar için dönüş nesnesinin ' durum ' bölümüne bakın

## <a name="next-steps"></a>Sonraki adımlar 

- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)
- [Dönüşümler](how-to-transformation.md)
- [Azure AD eşitleme API 'SI](/graph/api/resources/synchronization-overview?view=graph-rest-beta&preserve-view=true)
