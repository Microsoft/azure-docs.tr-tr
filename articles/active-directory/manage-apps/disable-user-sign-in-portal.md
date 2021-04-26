---
title: Azure AD 'de kurumsal uygulama için Kullanıcı oturum açma işlemlerini devre dışı bırakma
description: Bir kurumsal uygulamayı devre dışı bırakma, bir kullanıcının Azure Active Directory oturum açmasını sağlamak için
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/12/2019
ms.author: iangithinji
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9af580ef8b3691a5f188ac15069dda00a5f5802
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374259"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory ' de bir kurumsal uygulama için Kullanıcı oturum açma işlemlerini devre dışı bırakma

Bir kurumsal uygulamayı devre dışı bırakmak kolaydır, böylece hiçbir Kullanıcı Azure Active Directory (Azure AD) içinde oturum açabilirler. Kurumsal uygulamayı yönetmek için uygun izinlere sahip olmanız gerekir. Ve, dizin için genel yönetici olmanız gerekir.

## <a name="how-do-i-disable-user-sign-ins"></a>Kullanıcı Nasıl yaparım? oturum açma işlemlerini devre dışı bırakmak istiyor musunuz?

1. Dizin için genel yönetici olan bir hesapla [Azure portalda](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin, metin kutusuna **Azure Active Directory** girin ve ardından **ENTER**' u seçin.
1. **Azure Active Directory**  -   **_DirectoryName_*_ bölmesinde (diğer bir deyişle, yönettiğiniz dizin için Azure AD bölmesi) _ kurumsal uygulamalar ' ı seçin***.
1. **Kurumsal uygulamalar-tüm uygulamalar** bölmesinde, yönetebileceğiniz uygulamaların bir listesini görürsünüz. Bir uygulama seçin.
1. ***Appname** _ bölmesinde (diğer bir deyişle, başlığında Seçili uygulamanın adını taşıyan bölme) _ * Özellikler * * öğesini seçin.
1. ***Appname** _-_ *Properties** bölmesinde, **kullanıcıların oturum açmasını etkinleştirmek** için **Hayır** ' ı seçin.
1. **Kaydet** komutunu seçin.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Listelenmemiş bir uygulamayı devre dışı bırakmak için Azure AD PowerShell kullanma

Kurumsal uygulamalar listesinde görünmeyen bir uygulamanın uygulama kimliği ' ni biliyorsanız (örneğin, uygulamayı sildiğiniz veya hizmet sorumlusu Microsoft tarafından önceden yetkilendirildiği için henüz oluşturulmadığından), uygulama için hizmet sorumlusunu el ile oluşturabilir ve sonra [Azuread PowerShell cmdlet 'ini](/powershell/module/azuread/New-AzureADServicePrincipal)kullanarak devre dışı bırakabilirsiniz.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Tüm gruplarımı gör](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Kurumsal uygulamaya Kullanıcı veya Grup atama](assign-user-or-group-access-portal.md)
* [Bir kurumsal uygulamadan Kullanıcı veya grup atamasını kaldırma](./assign-user-or-group-access-portal.md)
* [Kurumsal uygulamanın adını veya logosunu değiştirme](./add-application-portal-configure.md)
