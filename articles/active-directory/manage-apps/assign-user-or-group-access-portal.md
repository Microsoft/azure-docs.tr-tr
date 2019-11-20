---
title: Azure Active Directory 'de bir kurumsal uygulamaya Kullanıcı veya Grup atama | Microsoft Docs
description: Bir kurumsal uygulamayı seçerek bu gruba bir kullanıcı veya Grup atama Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d28b9c31b8fbad8a565ff8cbdf717bfb3bc1309
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72896464"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory ' de bir kurumsal uygulamaya Kullanıcı veya Grup atama

Bir kullanıcı veya grubu kurumsal bir uygulamaya atamak için şu yönetici rollerinden herhangi birini atamış olmanız gerekir: genel yönetici, uygulama Yöneticisi, bulut uygulama Yöneticisi veya kurumsal uygulamanın sahibi olarak atanmış.  Microsoft uygulamaları (örneğin, Office 365 uygulamaları) için, PowerShell kullanarak kullanıcıları Kurumsal bir uygulamaya atayın.

> [!NOTE]
> Bu makalede ele alınan özelliklerle ilgili lisans gereksinimleri için [Azure Active Directory fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/active-directory)bakın.

## <a name="assign-a-user-to-an-app---portal"></a>Bir uygulamaya Kullanıcı atama-Portal

1. Dizin için genel yönetici olan bir hesapla [Azure portalda](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin, metin kutusuna Azure Active Directory girin ve ardından **ENTER**' u seçin.
1. **Kurumsal uygulamalar**' ı seçin.
1. **Kurumsal uygulamalar-tüm uygulamalar** bölmesinde, yönetebileceğiniz uygulamaların bir listesini görürsünüz. Bir uygulama seçin.
1. ***Appname*** bölmesinde (diğer bir deyişle, başlığında Seçili uygulamanın adını taşıyan bölme), **Kullanıcılar & Gruplar**' ı seçin.
1. ***Appname*** **-User ve groups** bölmesinde **Kullanıcı Ekle**' yi seçin.
1. **Atama Ekle** bölmesinde **Kullanıcılar ve gruplar**' ı seçin.

   ![Uygulamaya Kullanıcı veya Grup atama](./media/assign-user-or-group-access-portal/assign-users.png)

1. **Kullanıcılar ve gruplar** bölmesinde, listeden bir veya daha fazla Kullanıcı veya grup seçin ve ardından bölmenin altındaki **Seç** düğmesini seçin.
1. **Atama Ekle** bölmesinde **rol**' i seçin. Ardından, **Rol Seç** bölmesinde, seçili kullanıcılara veya gruplara uygulanacak bir rol seçin ve ardından bölmenin altındaki **Tamam** ' ı seçin.
1. **Atama Ekle** bölmesinde, bölmenin altındaki **ata** düğmesini seçin. Atanan kullanıcılar veya gruplar, bu kurumsal uygulama için seçilen rol tarafından tanımlanan izinlere sahiptir.

## <a name="allow-all-users-to-access-an-app---portal"></a>Tüm kullanıcıların bir uygulamaya erişmesine izin ver-Portal

1. Dizin için genel yönetici olan bir hesapla [Azure portalda](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin, metin kutusuna Azure Active Directory girin ve ardından **ENTER**' u seçin.
1. **Kurumsal uygulamalar**' ı seçin.
1. **Kurumsal uygulamalar** bölmesinde **tüm uygulamalar**' ı seçin. Böylece, yönetebileceğiniz uygulamalar listelenir.
1. **Kurumsal uygulamalar-tüm uygulamalar** bölmesinde bir uygulama seçin.
1. ***Appname*** bölmesinde **Özellikler**' i seçin.
1. ***Appname* -Properties** bölmesinde, **Kullanıcı Ataması gerekli mi?** ayarını **Hayır**olarak ayarlayın.

**Kullanıcı Ataması gerekli mi?** seçenek:

- Bu seçenek Evet olarak ayarlanırsa, kullanıcılardan erişebilmek için önce bu uygulamaya atanması gerekir.
- Bu seçenek Hayır olarak ayarlanırsa, uygulama derin bağlantı URL 'sine veya uygulama URL 'sine doğrudan erişen kullanıcılara erişim verilir
- Uygulama erişim panelinde bir uygulamanın görünüp başlatılmayacağını etkilemez. Uygulamayı erişim panelinde göstermek için, uygulamaya uygun bir kullanıcı veya grup atamanız gerekir.
- Yalnızca SAML çoklu oturum açma için yapılandırılmış bulut uygulamalarına sahip işlevler, Azure Active Directory ön kimlik doğrulama veya uygulamaları kullanan uygulama proxy uygulamaları, OAuth 2,0/kullanan Azure AD uygulama platformunda doğrudan oluşturulan uygulamalar. Bir kullanıcı veya yönetici bu uygulamaya alındıktan sonra OpenID Connect kimlik doğrulaması. Bkz. [uygulamalar Için çoklu oturum açma](what-is-single-sign-on.md). Bkz. [bir uygulamaya son kullanıcıların onayını yapılandırma](configure-user-consent.md).
- Bu seçeneğin, bir uygulama diğer çoklu oturum açma modlarında herhangi biri için yapılandırıldığında hiçbir etkisi yoktur.

## <a name="assign-a-user-to-an-app---powershell"></a>Bir uygulamaya Kullanıcı atama-PowerShell

1. Yükseltilmiş bir Windows PowerShell komut istemi açın.

   > [!NOTE]
   > AzureAD modülünü yüklemeniz gerekir (`Install-Module -Name AzureAD`komutunu kullanın). NuGet modülünü veya yeni Azure Active Directory v2 PowerShell modülünü yüklemek isteyip istemediğiniz sorulursa, Y yazın ve ENTER tuşuna basın.

1. `Connect-AzureAD` çalıştırın ve bir genel yönetici kullanıcı hesabıyla oturum açın.
1. Bir uygulamaya Kullanıcı ve rol atamak için aşağıdaki betiği kullanın:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

Bir kullanıcıyı bir uygulama rolüne atama hakkında daha fazla bilgi için, [New-Azureaduserapprotaatama](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) belgelerini ziyaret edin

Bir kurumsal uygulamaya bir grup atamak için `Get-AzureADUser` `Get-AzureADGroup`ile değiştirmeniz gerekir.

### <a name="example"></a>Örnek

Bu örnek, PowerShell kullanarak [Microsoft çalışma alanı analizi](https://products.office.com/business/workplace-analytics) uygulamasına Britta Simon kullanıcısını atar.

1. PowerShell 'de, $app _Adı ve $app _role_adı $username değişkenlere karşılık gelen değerleri atayın.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Bu örnekte, Britta Simon 'a atamak istediğimiz uygulama rolünün tam adı ne olduğunu bilmedik. Kullanıcı UPN 'sini ve hizmet sorumlusu görünen adlarını kullanarak kullanıcıyı ($user) ve hizmet sorumlusunu ($sp) almak için aşağıdaki komutları çalıştırın.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Çalışma alanı analizi uygulamasının kullanabileceği rolleri göstermek için `$sp.AppRoles` komutunu çalıştırın. Bu örnekte, çözümleyici (sınırlı erişim) rolüne Britta Simı atamak istiyoruz.

   ![Çalışma alanı analizi rolü kullanan bir kullanıcının kullanabileceği rolleri gösterir](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Rol adını `$app_role_name` değişkenine atayın.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Kullanıcıyı uygulama rolüne atamak için aşağıdaki komutu çalıştırın:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Tüm gruplarımı gör](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Bir kurumsal uygulamadan Kullanıcı veya grup atamasını kaldırma](remove-user-or-group-access-portal.md)
- [Kurumsal uygulama için Kullanıcı oturum açma işlemlerini devre dışı bırakma](disable-user-sign-in-portal.md)
- [Kurumsal uygulamanın adını veya logosunu değiştirme](change-name-or-logo-portal.md)
