---
title: PowerShell ve Graph kullanarak alt etki alanı kimlik doğrulaması türünü değiştirme-Azure Active Directory | Microsoft Docs
description: Azure Active Directory ' deki kök etki alanı ayarlarından devralınan varsayılan alt etki alanı kimlik doğrulama ayarlarını değiştirin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 04/18/2021
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4acf01a6672d17e62b6ebf5c6f43c8d6145f95a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739323"
---
# <a name="change-subdomain-authentication-type-in-azure-active-directory"></a>Azure Active Directory alt etki alanı kimlik doğrulaması türünü değiştir

Azure Active Directory (Azure AD) ' a bir kök etki alanı eklendikten sonra, Azure AD kuruluşunuzda o köke eklenen tüm sonraki alt etki alanı, kimlik doğrulama ayarını otomatik olarak kök etki alanından alırlar. Ancak, etki alanı kimlik doğrulama ayarlarını kök etki alanı ayarlarından bağımsız olarak yönetmek istiyorsanız artık Microsoft Graph API 'siyle birlikte olabilirsiniz. Örneğin, contoso.com gibi bir Federasyon kök etki alanınız varsa, bu makale, child.contoso.com gibi bir alt etki alanının Federasyon yerine yönetildiğini doğrulamanıza yardımcı olabilir.

Azure AD portalında, üst etki alanı federe olduğunda ve yönetici **özel etki alanı adları** sayfasında yönetilen bir alt etki alanını doğrulamaya çalıştığında, "bir veya daha fazla özellik geçersiz değerler içeriyor" nedenine sahip bir ' etki alanı eklenemedi ' hatası alırsınız. Bu alt etki alanını Microsoft 365 Yönetim merkezinden eklemeye çalışırsanız, benzer bir hata alırsınız. Hata hakkında daha fazla bilgi için bkz. [bir alt etki alanı Office 365, Azure veya Intune 'da üst etki alanı değişikliklerini almıyor](/office365/troubleshoot/administration/child-domain-fails-inherit-parent-domain-changes).

## <a name="how-to-verify-a-custom-subdomain"></a>Özel bir alt etki alanını doğrulama

Alt etki alanı kök etki alanının kimlik doğrulama türünü varsayılan olarak devraldığı için, kimlik doğrulama türünü istediğiniz türden ayarlayabilmeniz için Microsoft Graph kullanarak alt etki alanını Azure AD 'de bir kök etki alanına yükseltmeniz gerekir.

### <a name="add-the-subdomain-and-view-its-authentication-type"></a>Alt etki alanını ekleme ve kimlik doğrulama türünü görüntüleme

1. Kök etki alanının varsayılan kimlik doğrulama türüne sahip yeni alt etki alanını eklemek için PowerShell 'i kullanın. Azure AD ve Microsoft 365 yönetim merkezleri bu işlemi henüz desteklememektedir.

   ```powershell
   New-MsolDomain -Name "child.mydomain.com" -Authentication Federated
   ```

1. Etki alanını almak için [Azure AD Graph Explorer 'ı](https://graphexplorer.azurewebsites.net) kullanın. Etki alanı bir kök etki alanı olmadığından, kök etki alanı kimlik doğrulama türünü devralır. Komutunuz ve sonuçlarınız, kendi kiracı KIMLIĞINIZI kullanarak aşağıdaki gibi görünebilir:

   ```http
   GET https://graph.windows.net/{tenant_id}/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Federated",
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": false,          <---------------- Not a root domain, so it inherits parent domain's authentication type (federated)
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null
     },
   ```

### <a name="use-azure-ad-graph-explorer-api-to-make-this-a-root-domain"></a>Bu kök etki alanı yapmak için Azure AD Graph Explorer API 'sini kullanma

Alt etki alanını yükseltmek için aşağıdaki komutu kullanın:

```http
POST https://graph.windows.net/{tenant_id}/domains/child.mydomain.com/promote?api-version=1.6
```

### <a name="change-the-subdomain-authentication-type"></a>Alt etki alanı kimlik doğrulaması türünü değiştirme

1. Alt etki alanı kimlik doğrulaması türünü değiştirmek için aşağıdaki komutu kullanın:

   ```powershell
   Set-MsolDomainAuthentication -DomainName child.mydomain.com -Authentication Managed
   ```

1. Azure AD Graph Explorer 'da Al ile bu alt etki alanı kimlik doğrulama türünün yönetildiğini doğrulayın:

   ```http
   GET https://graph.windows.net/{{tenant_id} }/domains?api-version=1.6
   
   Return:
     {
         "authenticationType": "Managed",   <---------- Now this domain is successfully added as Managed and not inheriting Federated status
         "availabilityStatus": null,
         "isAdminManaged": true,
         "isDefault": false,
         "isDefaultForCloudRedirections": false,
         "isInitial": false,
         "isRoot": true,   <------------------------------ Also a root domain, so not inheriting from parent domain any longer
         "isVerified": true,
         "name": "child.mydomain.com",
         "supportedServices": [
             "Email",
             "OfficeCommunicationsOnline",
             "Intune"
         ],
         "forceDeleteState": null,
         "state": null,
         "passwordValidityPeriodInDays": null,
         "passwordNotificationWindowInDays": null }
   ```

## <a name="next-steps"></a>Sonraki adımlar

- [Özel etki alanı adı ekleme](../fundamentals/add-custom-domain.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Etki alanı adlarını yönetme](domains-manage.md)
- [ForceMicrosoft Graph API ile özel bir etki alanı adını silme](/graph/api/domain-forcedelete?view=graph-rest-beta&preserve-view=true)