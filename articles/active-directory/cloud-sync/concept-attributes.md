---
title: Azure AD şemasını ve özel ifadeleri anlayın
description: Bu makalede, Azure AD şeması, sağlama aracısının akan öznitelikleri ve özel ifadeler açıklanmaktadır.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 750bc2f4f535238dbb757afc8b615fa71063d74d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98614368"
---
# <a name="understand-the-azure-ad-schema"></a>Azure AD şemasını anlama
Tüm Dizin gibi Azure Active Directory (Azure AD) bir nesne, kullanıcılar, gruplar ve kişiler gibi şeyleri temsil eden bir programlama üst düzey veri yapısıdır. Azure AD 'de yeni bir kullanıcı veya ilgili kişi oluşturduğunuzda, bu nesnenin yeni bir örneğini oluşturuyorsunuz. Bu örnekler özelliklerine göre farklılaştırılabilir.

Azure AD 'deki özellikler, Azure AD 'de bir nesnenin örneğiyle ilgili bilgileri saklamaktan sorumlu olan öğelerdir.

Azure AD şeması, bir girişte özelliklerin kullanılabileceği kuralları, bu özelliklerin sahip olabileceği değer türlerini ve kullanıcıların bu değerlerle nasıl etkileşime girebileceğini tanımlar. 

Azure AD 'nin iki tür özelliği vardır:
- **Yerleşik özellikler**: Azure AD şeması tarafından önceden tanımlanan özellikler. Bu özellikler farklı kullanımlar sağlar ve erişilebilir olmayabilir veya olmayabilir.
- **Dizin uzantıları**: Azure AD 'yi kendi kullanım için özelleştirebilmeniz için sunulan özellikler. Örneğin, şirket içi Active Directory belirli bir öznitelikle genişlettiyseniz ve bu özniteliği Flow istiyorsanız, belirtilen özel özelliklerden birini kullanabilirsiniz. 

## <a name="attributes-and-expressions"></a>Öznitelikler ve ifadeler
Kullanıcı gibi bir nesne Azure AD 'ye sağlandığında, Kullanıcı nesnesinin yeni bir örneği oluşturulur. Bu oluşturma, bu nesnenin öznitelikleri olarak da bilinen özelliklerini içerir. Başlangıçta, yeni oluşturulan nesnenin öznitelikleri, eşitleme kuralları tarafından belirlenen değerler olarak ayarlanır. Bu öznitelikler daha sonra bulut sağlama Aracısı aracılığıyla güncel tutulur.

![Nesne sağlama](media/concept-attributes/attribute-1.png)

Örneğin, bir Kullanıcı bir pazarlama bölümünün parçası olabilir. Azure AD departmanı özniteliği, başlangıçta sağlandığında oluşturulur ve değer pazarlama olarak ayarlanır. Altı ay sonra satışa değiştirildiklerinde şirket içi Active Directory departmanı özniteliği Sales olarak değiştirilir. Bu değişiklik Azure AD 'ye eşitlenir ve Azure AD kullanıcı nesnesine yansıtılır.

Öznitelik eşitlemesi doğrudan, Azure AD 'deki değerin şirket içi özniteliğinin değerine doğrudan ayarlandığı bir biçimde olabilir. Ya da bir programlama ifadesi eşitlemeyi işleyebilir. Bir programlı ifade, değeri doldurmak için bazı Logic veya bir belirleme yapılması gereken durumlarda gereklidir.

Örneğin, "" posta özniteliğine sahip olsaydıysanız john.smith@contoso.com ve " @contoso.com John. Smith" yalnızca "John. Smith" değerini bırakmak için gerekliyse, şöyle bir şey kullanırsınız:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Örnek giriş/çıkış:** <br>

* **Giriş** (posta): " john.smith@contoso.com "
* **Çıkış**: "John. Smith"

Özel ifadeler ve söz dizimi yazma hakkında daha fazla bilgi için, bkz. [Azure Active Directory öznitelik eşlemeleri için Ifadeler yazma](../app-provisioning/functions-for-customizing-application-data.md).

Aşağıdaki tabloda, ortak öznitelikler ve bunların Azure AD ile nasıl eşitlendikleri listelenmektedir.


|Şirket içi Active Directory|Eşleme türü|Azure AD|
|-----|-----|-----|
|,|Direct|commonName
|countryCode|Direct|countryCode|
|displayName|Direct|displayName|
|givenName|Expression|givenName|
|Objectguıd 'dir|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="view-the-schema"></a>Şemayı görüntüleme
> [!WARNING]
> Bulut eşitleme yapılandırması bir hizmet sorumlusu oluşturur. Hizmet sorumlusu Azure portal görünür. Azure portal hizmet sorumlusu deneyimini kullanarak öznitelik eşlemelerini değiştirmemelisiniz.  Bu özellik desteklenmez.

Şemayı görüntülemek ve doğrulamak için aşağıdaki adımları izleyin.

1.  [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)'a gidin.
1.  Genel Yönetici hesabınızla oturum açın.
1.  Sol tarafta **izinleri değiştir** ' i seçin ve **Directory. ReadWrite. All** *' ın onaylı olduğundan emin* olun.
1.  Sorguyu çalıştırın `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')` . Bu sorgu, hizmet sorumlularının filtrelenmiş bir listesini döndürür.
1.  `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"`Değerini bulup aklınızda bulun `"id"` .
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. `{Service Principal id}`Değerini ile değiştirin ve sorguyu çalıştırın `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/` .
1. `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"`Değerini bulup aklınızda bulun `"id"` .
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Şimdi sorguyu çalıştırın `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema` .
 
    Örnek: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   `{Service Principal Id}`Ve `{AD2ADD Provisioning Id}` değerlerini değerlerinizle değiştirin.

1. Bu sorgu şemayı döndürür.

   ![Döndürülen şema](media/concept-attributes/schema-1.png)
 
## <a name="next-steps"></a>Sonraki adımlar

- [Sağlama nedir?](what-is-provisioning.md)
- [Azure AD Connect bulut eşitlemesi nedir?](what-is-cloud-sync.md)