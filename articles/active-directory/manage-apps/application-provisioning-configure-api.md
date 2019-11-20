---
title: Sağlamayı yapılandırmak için MS Graph API 'Lerini kullanma-Azure Active Directory | Microsoft Docs
description: Bir uygulamanın birden çok örneği için sağlama ayarlanması mı gerekiyor? Otomatik sağlama yapılandırmasını otomatik hale getirmek için MS Graph API 'Lerini kullanarak zamandan tasarruf yapmayı öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00b3e6f84051aec97691d2bedda6665d3c9c0ceb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125080"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Microsoft Graph API 'Leri kullanarak sağlamayı yapılandırma

Azure portal, bireysel uygulamalar için tek seferde sağlamayı yapılandırmanın kolay bir yoludur. Ancak, bir uygulamanın birkaç veya hatta yüzlerce — örneğini oluşturuyorsanız, uygulama oluşturma ve yapılandırmayı Microsoft Graph API 'lerle otomatik hale getirmek daha kolay olabilir. Bu makalede API 'Ler aracılığıyla sağlama yapılandırmasının nasıl otomatikleştirilmesi özetlenmektedir. Bu yöntem, genellikle [Amazon Web Services](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-azure-ad-single-sign-on)gibi uygulamalar için kullanılır.

**Sağlama yapılandırmasını otomatikleştirmek için Microsoft Graph API 'Leri kullanmaya yönelik adımlara genel bakış**


|Adım  |Ayrıntılar  |
|---------|---------|
|[1. adım. Galeri uygulaması oluşturma](#step-1-create-the-gallery-application)     |API istemcisinde oturum açma <br> Galeri uygulaması şablonunu alma <br> Galeri uygulaması oluşturma         |
|[2. adım. Şablon temelinde sağlama işi oluştur](#step-2-create-the-provisioning-job-based-on-the-template)     |Sağlama bağlayıcısının şablonunu alma <br> Sağlama işini oluşturma         |
|[3. adım. Erişim yetkisi verme](#step-3-authorize-access)     |Uygulamayla bağlantıyı test etme <br> Kimlik bilgilerini kaydedin         |
|[4. adım. Sağlama işini Başlat](#step-4-start-the-provisioning-job)     |İşi başlatma         |
|[5. adım. İzleme sağlama](#step-5-monitor-provisioning)     |Sağlama işinin durumunu denetleyin <br> Sağlama günlüklerini alma         |

> [!NOTE]
> Bu makalede gösterilen yanıt nesneleri okunabilirlik için kısaltılamayabilir. Tüm özellikler gerçek bir çağrıdan döndürülecek.

## <a name="step-1-create-the-gallery-application"></a>1\. Adım: Galeri uygulamasını oluşturma

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Microsoft Graph Explorer (önerilen), Postman veya kullandığınız başka bir API istemcisinde oturum açın

1. [Microsoft Graph Gezginini](https://developer.microsoft.com/graph/graph-explorer) Başlat
1. "Microsoft ile oturum aç" düğmesini seçin ve Azure AD Genel Yöneticisi veya uygulama Yöneticisi kimlik bilgilerini kullanarak oturum açın.

    ![Graph oturum açma](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Oturum açma başarılı olduğunda, Kullanıcı hesabı ayrıntılarını sol bölmede görürsünüz.

### <a name="retrieve-the-gallery-application-template-identifier"></a>Galeri uygulaması şablon tanımlayıcısını alma
Azure AD uygulama galerisindeki uygulamaların her biri, uygulamanın meta verilerini açıklayan bir [uygulama şablonuna](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) sahiptir. Bu şablonu kullanarak, yönetim için kiracınızda uygulama ve hizmet sorumlusu örneği oluşturabilirsiniz.

#### <a name="request"></a>*İstek*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Yanıt*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>Galeri uygulaması oluşturma

Kiracınızda uygulamanın ve hizmet sorumlusunun [bir örneğini oluşturmak](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) için son adımdaki uygulamanız için ALıNAN şablon kimliğini kullanın.

#### <a name="request"></a>*İstek*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Yanıt*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>2\. Adım: şablonu temel alarak sağlama işini oluşturma

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>Sağlama bağlayıcısının şablonunu alma

Galerisindeki sağlama için etkinleştirilen uygulamaların, yapılandırmayı kolaylaştırmak için şablonları vardır. [Sağlama yapılandırması şablonunu almak](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http)için aşağıdaki isteği kullanın.

#### <a name="request"></a>*İstek*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>Sağlama işini oluşturma
Sağlamayı etkinleştirmek için öncelikle [bir iş oluşturmanız](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)gerekir. Bir sağlama işi oluşturmak için aşağıdaki isteği kullanın. İş için kullanılacak şablonu belirtirken önceki adımdaki TemplateId 'yi kullanın.

#### <a name="request"></a>*İstek*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>3\. Adım: erişimi yetkilendirme

### <a name="test-the-connection-to-the-application"></a>Uygulamayla bağlantıyı test etme

Üçüncü taraf uygulamayla bağlantıyı test edin. Aşağıdaki örnek, clientSecret ve secretToken gerektiren bir uygulama içindir. Her uygulamanın gereksinimleri vardır. Kullanılabilir seçenekleri görmek için [API belgelerini](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) gözden geçirin. 

#### <a name="request"></a>*İstek*
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Kimlik bilgilerinizi kaydedin

Sağlamayı yapılandırmak için Azure AD ile uygulama arasında bir güven oluşturulması gerekir. Üçüncü taraf uygulamasına erişimi yetkilendirin. Aşağıdaki örnek, clientSecret ve secretToken gerektiren bir uygulama içindir. Her uygulamanın gereksinimleri vardır. Kullanılabilir seçenekleri görmek için [API belgelerini](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) gözden geçirin. 

#### <a name="request"></a>*İstek*
```json
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>4\. Adım: sağlama işini başlatma
Sağlama işi yapılandırıldığına göre, [işi başlatmak](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)için aşağıdaki komutu kullanın. 


#### <a name="request"></a>*İstek*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>5\. Adım: Izleme sağlama

### <a name="monitor-the-provisioning-job-status"></a>Sağlama işi durumunu izleme

Sağlama işi çalışıyor olduğuna göre, geçerli sağlama döngüsünün ilerlemesini izlemek için aşağıdaki komutu ve hedef sistemde oluşturulan kullanıcı ve grup sayısı gibi tarihe göre istatistikleri izlemek için kullanın. 

#### <a name="request"></a>*İstek*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Sağlama günlüklerini kullanarak sağlama olaylarını izleme
Sağlama işinin durumunu izlemeye ek olarak, gerçekleşen tüm olayları sorgulamak için [sağlama günlüklerini](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) kullanabilirsiniz (örneğin, belirli bir kullanıcı için sorgu ve başarıyla sağlanıp sağlanmadığını belirleme).

#### <a name="request"></a>*İstek*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Yanıt*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>İlgili makaleler

- [Eşitleme Microsoft Graph belgelerini gözden geçirin](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Özel bir SCıM uygulamasını Azure AD ile tümleştirme](use-scim-to-provision-users-and-groups.md)
