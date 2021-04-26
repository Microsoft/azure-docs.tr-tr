---
title: Azure Active Directory’de uygulama için kullanıcı atamasını yönetme
description: Kimlik yönetimi için Azure Active Directory kullanarak bir uygulama için kullanıcılar ve gruplar atamayı ve atamayı öğrenin.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: iangithinji
ms.reviewer: luleon
ms.openlocfilehash: 097bf55cc7e5372749240c19afb09ba374d437af
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377981"
---
# <a name="manage-user-assignment-for-an-app-in-azure-active-directory"></a>Azure Active Directory’de uygulama için kullanıcı atamasını yönetme

Bu makalede, kullanıcıları ve grupları Azure Active Directory (Azure AD) içindeki kurumsal uygulamalara Azure portal veya PowerShell kullanarak nasıl atayacağınız gösterilmektedir. Bir uygulamayı bir kullanıcıya atadığınızda, uygulama kullanıcının [uygulamamda](https://myapps.microsoft.com/) kolay erişim için görüntülenir. Uygulama rolleri kullanıma sunarsa, kullanıcıya belirli bir rol de atayabilirsiniz.

Daha fazla denetim için, bazı kurumsal uygulama türleri [Kullanıcı Ataması gerektirecek](#configure-an-application-to-require-user-assignment)şekilde yapılandırılabilir. 

> [!IMPORTANT]
> Bir uygulamaya bir grup atadığınızda, yalnızca gruptaki kullanıcıların erişimi olur. Atama, iç içe gruplar olarak basamaklanmıyor.

> [!NOTE]
> Grup tabanlı atama Azure Active Directory Premium P1 veya P2 Edition gerektirir. Grup tabanlı atama yalnızca güvenlik grupları için desteklenir. İç içe grup üyelikleri ve Microsoft 365 grupları Şu anda desteklenmemektedir. Bu makalede ele alınan özelliklerin daha fazla lisanslama gereksinimi için [Azure Active Directory fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/active-directory)bakın. 

## <a name="configure-an-application-to-require-user-assignment"></a>Bir uygulamayı kullanıcı ataması gerektirecek şekilde yapılandırma

Aşağıdaki uygulama türleri ile, kullanıcıların uygulamaya erişebilmesi için kullanıcılara atanmasını isteme seçeneğiniz vardır:

- SAML tabanlı kimlik doğrulaması ile federe çoklu oturum açma (SSO) için yapılandırılmış uygulamalar
- Azure Active Directory ön kimlik doğrulaması kullanan uygulama proxy uygulamaları
- Bir kullanıcı veya yönetici bu uygulamaya alındıktan sonra OAuth 2,0/OpenID Connect kimlik doğrulaması kullanan Azure AD uygulama platformunda oluşturulan uygulamalar.

Kullanıcı Ataması gerekli olduğunda, yalnızca uygulamaya açıkça atadığınız kullanıcılar (doğrudan kullanıcı ataması veya grup üyeliğine göre) oturum açabiliyor. Uygulamalar uygulamalarım sayfasında veya doğrudan bağlantı kullanarak uygulamalara erişebilirler. 

Atama *gerekli* olmadığında, bu seçeneği **Hayır** olarak ayarlamış olmanız ya da uygulamanın başka bir SSO modu kullanması nedeniyle, uygulamanın doğrudan bir bağlantısı veya uygulamanın **Özellikler** sayfasında **Kullanıcı erişimi URL 'si** varsa, herhangi bir Kullanıcı uygulamaya erişebilecektir. 

Bu ayar, uygulamamda bir uygulamanın görüntülenip görüntülenmeyeceğini etkilemez. Uygulamaya bir kullanıcı veya grup atadıktan sonra, uygulamalar kullanıcıların uygulama erişim panellerinde görüntülenir. Arka plan için bkz. [uygulamalara erişimi yönetme](what-is-access-management.md).

Bir uygulama için Kullanıcı Ataması gerektirmek için:
1. [Azure Portal](https://portal.azure.com) bir yönetici hesabıyla veya uygulamanın sahibi olarak oturum açın.
2. **Azure Active Directory** seçin. Sol gezinti menüsünde **Kurumsal uygulamalar**' ı seçin.
3. Listeden uygulamayı seçin. Uygulamayı görmüyorsanız, arama kutusuna adını yazmaya başlayın. Ya da uygulama türünü, durumunu veya görünürlüğünü seçmek için filtre denetimlerini kullanın ve ardından **Uygula**' yı seçin.
4. Sol gezinti menüsünde **Özellikler**' i seçin.
5. **Kullanıcı atamasının gerekli** olduğundan emin olun. iki durumlu düğme **Evet** olarak ayarlanmıştır.
   > [!NOTE]
   > **Kullanıcı Ataması gerekli değil mi?** geçiş kullanılamıyor, hizmet sorumlusu üzerinde Approleatamamentrequired özelliğini ayarlamak için PowerShell kullanabilirsiniz.
6. Ekranın üst kısmındaki **Kaydet** düğmesini seçin.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-azure-portal"></a>Azure portal kullanarak bir uygulama için kullanıcılar ve gruplar atama veya atamasını kaldırma
Azure portal kullanarak bir kullanıcı veya grup atamayı veya atamayı öğrenmek için, [uygulama yönetiminde hızlı başlangıç serisi](add-application-portal-assign-users.md)' ne bakın.

## <a name="assign-or-unassign-users-and-groups-for-an-app-using-the-graph-api"></a>Graph API kullanarak bir uygulama için kullanıcılar ve gruplar atama veya atamasını kaldırma
Bir uygulama için kullanıcıları ve grupları atamak veya atamasını kaldırmak için Graph API kullanabilirsiniz. Daha fazla bilgi için bkz. [uygulama rolü atamaları](/graph/api/resources/approleassignment).

## <a name="assign-users-and-groups-to-an-app-using-powershell"></a>PowerShell kullanarak bir uygulamaya kullanıcılar ve gruplar atama
1. Yükseltilmiş bir Windows PowerShell komut istemi açın.
   > [!NOTE]
   > AzureAD modülünü yüklemeniz gerekir (komutunu kullanın `Install-Module -Name AzureAD` ). NuGet modülünü veya yeni Azure Active Directory v2 PowerShell modülünü yüklemek isteyip istemediğiniz sorulursa, Y yazın ve ENTER tuşuna basın.
2. `Connect-AzureAD`' İ çalıştırın ve bir genel yönetici kullanıcı hesabıyla oturum açın.
3. Bir uygulamaya Kullanıcı ve rol atamak için aşağıdaki betiği kullanın:

    ```powershell
    # Assign the values to the variables
    $username = "<Your user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"

    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }

    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```
Bir kullanıcıyı bir uygulama rolüne atama hakkında daha fazla bilgi için, [New-Azureaduserapprotaatama](/powershell/module/azuread/new-azureaduserapproleassignment)belgelerine bakın.

Bir kurumsal uygulamaya bir grup atamak için `Get-AzureADUser` ile değiştirmeniz ve ile değiştirmeniz gerekir `Get-AzureADGroup` `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

Bir uygulama rolüne Grup atama hakkında daha fazla bilgi için, [New-AzureADGroupAppRoleAssignment](/powershell/module/azuread/new-azureadgroupapproleassignment)belgelerine bakın.

### <a name="example"></a>Örnek

Bu örnek, PowerShell kullanarak [Microsoft çalışma alanı analizi](https://products.office.com/business/workplace-analytics) uygulamasına Britta Simon kullanıcısını atar.

1. PowerShell 'de, karşılık gelen değerleri $username değişkenine atayın $app _name ve $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```
2. Bu örnekte, Britta Simon 'a atamak istediğimiz uygulama rolünün tam adı ne olduğunu bilmedik. Kullanıcı UPN 'sini ve hizmet sorumlusu görünen adlarını kullanarak kullanıcıyı ($user) ve hizmet sorumlusunu ($sp) almak için aşağıdaki komutları çalıştırın.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
3. `$sp.AppRoles`Çalışma alanı analizi uygulaması için kullanılabilen rolleri göstermek için komutunu çalıştırın. Bu örnekte, çözümleyici (sınırlı erişim) rolüne Britta Simı atamak istiyoruz.
   ![Çalışma alanı analizi rolü kullanan bir kullanıcının kullanabileceği rolleri gösterir](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)
4. Rol adını `$app_role_name` değişkenine atayın.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```
5. Kullanıcıyı uygulama rolüne atamak için aşağıdaki komutu çalıştırın:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="unassign-users-and-groups-from-an-app-using-powershell"></a>PowerShell kullanarak bir uygulamadan kullanıcıların ve grupların atamasını kaldırma

1. Yükseltilmiş bir Windows PowerShell komut istemi açın.
   > [!NOTE]
   > AzureAD modülünü yüklemeniz gerekir (komutunu kullanın `Install-Module -Name AzureAD` ). NuGet modülünü veya yeni Azure Active Directory v2 PowerShell modülünü yüklemek isteyip istemediğiniz sorulursa, Y yazın ve ENTER tuşuna basın.
2. `Connect-AzureAD`' İ çalıştırın ve bir genel yönetici kullanıcı hesabıyla oturum açın.
3. Bir uygulamayı bir Kullanıcı ve rolü kaldırmak için aşağıdaki betiği kullanın:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```


## <a name="related-articles"></a>İlgili makaleler:

- [Uygulamalara son kullanıcı erişimi hakkında daha fazla bilgi edinin](end-user-experiences.md)
- [Azure AD My Apps dağıtımını planlayın](my-apps-deployment-plan.md)
- [Uygulamalara erişimi yönetme](what-is-access-management.md)
 
## <a name="next-steps"></a>Sonraki adımlar

- [Tüm gruplarımı gör](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Bir kurumsal uygulamadan Kullanıcı veya grup atamasını kaldırma]()
- [Kurumsal uygulama için kullanıcı oturum açma işlemlerini devre dışı bırakma](disable-user-sign-in-portal.md)
- [Kurumsal uygulamanın adını veya logosunu değiştirme](./add-application-portal-configure.md)
