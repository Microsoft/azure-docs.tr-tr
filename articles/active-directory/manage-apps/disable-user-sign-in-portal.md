---
title: Azure Active Directory 'de kurumsal uygulama için Kullanıcı oturum açma işlemlerini devre dışı bırakma | Microsoft Docs
description: Bir kurumsal uygulamayı devre dışı bırakma, bir kullanıcının Azure Active Directory oturum açmasını sağlamak için
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a08779d171367d982392ae4e987fb46e019e61f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720271"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory ' de bir kurumsal uygulama için Kullanıcı oturum açma işlemlerini devre dışı bırakma

Bir kurumsal uygulamayı devre dışı bırakmak kolaydır, böylece hiçbir Kullanıcı Azure Active Directory (Azure AD) içinde oturum açabilirler. Kurumsal uygulamayı yönetmek için uygun izinlere sahip olmanız gerekir. Ve, dizin için genel yönetici olmanız gerekir.

## <a name="how-do-i-disable-user-sign-ins"></a>Kullanıcı Nasıl yaparım? oturum açma işlemlerini devre dışı bırakmak istiyor musunuz?

1. Dizin için genel yönetici olan bir hesapla [Azure portalda](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin, metin kutusuna **Azure Active Directory** girin ve ardından **ENTER**' u seçin.
1. **Azure Active Directory** -  ***DirectoryName*** bölmesinde (diğer bir deyişle, YÖNETTIĞINIZ dizin için Azure AD bölmesi) **Kurumsal uygulamalar**' ı seçin.
1. **Kurumsal uygulamalar-tüm uygulamalar** bölmesinde, yönetebileceğiniz uygulamaların bir listesini görürsünüz. Bir uygulama seçin.
1. ***Appname*** bölmesinde (yani, başlığında Seçili uygulamanın adını taşıyan bölme) **Özellikler**' i seçin.
1. ***Appname*** - **Özellikler** bölmesinde, **kullanıcıların oturum açması**için **Hayır** ' ı seçin.
1. **Kaydet** komutunu seçin.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Listelenmemiş bir uygulamayı devre dışı bırakmak için Azure AD PowerShell kullanma

Kurumsal uygulamalar listesinde görünmeyen bir uygulamanın uygulama kimliği ' ni biliyorsanız (örneğin, uygulamayı sildiğiniz veya hizmet sorumlusu Microsoft tarafından önceden yetkilendirildiği için henüz oluşturulmadığından), için hizmet sorumlusunu el ile oluşturabilirsiniz. ardından, [Azuread PowerShell cmdlet 'ini](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0)kullanarak uygulamayı devre dışı bırakın.

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
* [Bir kurumsal uygulamadan Kullanıcı veya grup atamasını kaldırma](remove-user-or-group-access-portal.md)
* [Kurumsal uygulamanın adını veya logosunu değiştirme](change-name-or-logo-portal.md)
