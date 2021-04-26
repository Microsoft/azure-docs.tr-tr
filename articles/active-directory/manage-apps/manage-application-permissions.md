---
title: Kullanıcı ve yönetici izinlerini yönetme-Azure Active Directory | Microsoft Docs
description: Azure AD 'de uygulama için izinleri incelemeyi ve yönetmeyi öğrenin. Örneğin, bir uygulamaya verilen tüm izinleri iptal edin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/10/2020
ms.author: iangithinji
ms.reviewer: luleonpla
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd137030e4e1f3e88f47ec5ba78b3bde08fe068
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373989"
---
# <a name="take-action-on-overprivileged-or-suspicious-applications-in-azure-active-directory"></a>Azure Active Directory fazla ayrıcalıklı veya şüpheli uygulamalarda işlem yapın

Uygulama izinlerini incelemeyi ve yönetmeyi öğrenin. Bu makalede, senaryonuza göre uygulamanızı güvenli hale getirmek için uygulayabileceğiniz farklı eylemler sunulmaktadır. Bu eylemler, Kullanıcı veya yönetici izni aracılığıyla Azure Active Directory (Azure AD) kiracısına eklenen tüm uygulamalar için geçerlidir.

Uygulamalara yönelik yarışmaya yönelik daha fazla bilgi için bkz. [Azure Active Directory izin çerçevesi](../develop/consent-framework.md).

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki işlemleri yapmak için, genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açmanız gerekir.

Uygulamalara erişimi kısıtlamak için Kullanıcı ataması yapmanız ve ardından kullanıcılara veya grupları uygulamaya atamanız gerekir.  Daha fazla bilgi için bkz. [kullanıcıları ve grupları atamaya yönelik yöntemler](./assign-user-or-group-access-portal.md).

Eylemleri gerçekleştirmek üzere bağlamsal PowerShell betikleri almak için Azure AD portalına erişebilirsiniz.
 
1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**' ı seçin.
3. Erişimi kısıtlamak istediğiniz uygulamayı seçin.
4. **İzinler**'i seçin. Komut çubuğunda, **Izinleri gözden geçir**' i seçin.

![İzinleri gözden geçir penceresinin ekran görüntüsü.](./media/manage-application-permissions/review-permissions.png)


## <a name="control-access-to-an-application"></a>Bir uygulamaya erişimi denetleme

**Kullanıcı atama** ayarını açarak uygulamaya erişimi kısıtlamanız önerilir.

1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**' ı seçin.
3. Erişimi kısıtlamak istediğiniz uygulamayı seçin.
4. **Özellikler**' i seçin ve **gerekli Kullanıcı gereksinimini** **Evet** olarak ayarlayın.
5. **Kullanıcı ve gruplar**' ı seçin ve ardından uygulamaya atanan istenmeyen kullanıcıları kaldırın.
6. Uygulamaya Kullanıcı veya Grup atama.

İsteğe bağlı olarak, PowerShell kullanarak uygulamaya atanan tüm kullanıcıları kaldırabilirsiniz.

## <a name="revoke-all-permissions-for-an-application"></a>Bir uygulama için tüm izinleri iptal et

PowerShell betiğini kullanmak, bu uygulamaya verilen tüm izinleri iptal eder.

> [!NOTE]
> Verilen geçerli izin iptal etmek, kullanıcıların uygulamaya yeniden onay duymasına gerek kalmaz. Kullanıcıların uygulamayı engellenmesini engellemek isterseniz, [kullanıcıların uygulamaları nasıl kabul](configure-user-consent.md)ettıklarını okuyun.

İsteğe bağlı olarak, kullanıcıların uygulamaya erişmesini ve uygulamanın verilerinize erişimini önlemek için uygulamayı devre dışı bırakabilirsiniz.

1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**' ı seçin.
3. Erişimi kısıtlamak istediğiniz uygulamayı seçin.
4. **Özellikler**' i seçin ve ardından **kullanıcıların oturum açması için etkin** ' i **ayarlayın.**

## <a name="investigate-a-suspicious-application"></a>Şüpheli bir uygulamayı araştırın

**Kullanıcı atama** ayarını açarak uygulamaya erişimi kısıtlamanız önerilir. Ardından, kullanıcıların ve yöneticilerin uygulamaya verdiği izinleri gözden geçirin.

1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
3. **Azure Active Directory**  >  **Kurumsal uygulamalar**' ı seçin.
5. Erişimi kısıtlamak istediğiniz uygulamayı seçin.
6. **Özellikler**' i seçin ve **gerekli Kullanıcı gereksinimini** **Evet** olarak ayarlayın.
7. **İzinler**' i seçin ve yönetici ve Kullanıcı tarafından onaylanan izinleri gözden geçirin.

İsteğe bağlı olarak, PowerShell kullanarak şunları yapabilirsiniz:

- Uygulamanın oturum açmasını engellemek için atanan tüm kullanıcıları kaldırın.
- Uygulamaya erişimi olan kullanıcılar için yenileme belirteçlerini geçersiz kılar.
- Uygulamanın tüm izinlerini iptal edin.

Ya da kullanıcının erişimini engellemek ve uygulamanın verilerinize erişimini durdurmak için uygulamayı devre dışı bırakabilirsiniz.


## <a name="disable-a-malicious-application"></a>Kötü amaçlı uygulamayı devre dışı bırakma 

Kullanıcıların erişimini engellemek ve uygulamanın verilerinize erişmesini önlemek için uygulamayı devre dışı bırakmanızı öneririz. Bunun yerine uygulamayı silerseniz, kullanıcılar uygulamayı yeniden onaylamasına ve verilerinize erişim izni verebilir.

1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**' ı seçin.
3. Erişimi kısıtlamak istediğiniz uygulamayı seçin.
4. **Özellikler**' i seçin ve ardından nesne kimliğini kopyalayın.

### <a name="powershell-commands"></a>PowerShell komutları


Hizmet sorumlusu nesne KIMLIĞINI alın.

1. [Azure Portal](https://portal.azure.com) genel yönetici, uygulama Yöneticisi veya bulut uygulama Yöneticisi olarak oturum açın.
2. **Azure Active Directory**  >  **Kurumsal uygulamalar**' ı seçin.
3. Erişimi kısıtlamak istediğiniz uygulamayı seçin.
4. **Özellikler**' i seçin ve ardından nesne kimliğini kopyalayın.

```powershell
$sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
$sp.ObjectId
```
Uygulamaya atanan tüm kullanıcıları kaldırın.
 ```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectId of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true

# Remove all users and groups assigned to the application
$assignments | ForEach-Object {
    if ($_.PrincipalType -eq "User") {
        Remove-AzureADUserAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    } elseif ($_.PrincipalType -eq "Group") {
        Remove-AzureADGroupAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.ObjectId
    }
}
 ```

Uygulamaya verilen izinleri iptal et.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```
Yenileme belirteçlerini geçersiz kılar.
```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```
## <a name="next-steps"></a>Sonraki adımlar
- [Uygulamalara onayı yönetin ve onay isteğini değerlendirin](manage-consent-requests.md)
- [Kullanıcı onayını yapılandırma](configure-user-consent.md)
- [Yönetici onayı iş akışını yapılandırma](configure-admin-consent-workflow.md)
