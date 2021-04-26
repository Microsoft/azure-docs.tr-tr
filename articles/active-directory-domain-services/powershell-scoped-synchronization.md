---
title: Azure AD Domain Services için PowerShell kullanarak kapsamlı eşitleme | Microsoft Docs
description: Azure AD PowerShell 'i kullanarak Azure AD 'den Azure Active Directory Domain Services yönetilen bir etki alanına kapsamlı eşitleme yapılandırma hakkında bilgi edinin
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: justinha
ms.openlocfilehash: f877a631fd3c89d74b9e3b47cf205bbcf173ebc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453419"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Azure AD PowerShell 'i kullanarak Azure AD 'den kapsamlı eşitlemeyi Azure Active Directory Domain Services yapılandırma

Azure Active Directory Domain Services (Azure AD DS), kimlik doğrulama hizmetleri sağlamak için kullanıcıları ve grupları Azure AD 'den eşitler. Karma bir ortamda, şirket içi Active Directory Domain Services (AD DS) ortamından kullanıcılar ve gruplar önce Azure AD Connect kullanılarak Azure AD ile eşitlenebilir ve sonra Azure AD DS ile eşitlenebilir.

Varsayılan olarak, bir Azure AD dizininden tüm kullanıcılar ve gruplar Azure AD DS yönetilen bir etki alanıyla eşitlenir. Belirli gereksinimleriniz varsa, bunun yerine yalnızca tanımlı bir kullanıcı kümesini eşitlemeyi tercih edebilirsiniz.

Bu makalede, kapsamlı eşitleme kullanan bir yönetilen etki alanı oluşturma ve ardından Azure AD PowerShell kullanarak kapsamlı kullanıcı kümesini değiştirme veya devre dışı bırakma işlemlerinin nasıl yapılacağı gösterilir. Ayrıca [, Azure Portal kullanarak bu adımları tamamlayabilirsiniz][scoped-sync].

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturmak ve yapılandırmak][tutorial-create-instance]için öğreticiyi doldurun.
* Azure AD DS eşitleme kapsamını değiştirmek için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.

## <a name="scoped-synchronization-overview"></a>Kapsamlı eşitlemeye genel bakış

Varsayılan olarak, bir Azure AD dizininden tüm kullanıcılar ve gruplar yönetilen bir etki alanıyla eşitlenir. Yönetilen etki alanına yalnızca birkaç kullanıcının erişmesi gerekiyorsa yalnızca bu kullanıcı hesaplarını eşitlemeniz yeterlidir. Bu kapsamlı eşitleme grup tabanlıdır. Grup tabanlı kapsamlı eşitleme yapılandırdığınızda, yalnızca belirttiğiniz gruplara ait olan kullanıcı hesapları yönetilen etki alanına eşitlenir. İç içe gruplar yalnızca seçtiğiniz belirli gruplar ile eşitlenmez.

Yönetilen etki alanını oluşturmadan önce veya sonra eşitleme kapsamını değiştirebilirsiniz. Eşitleme kapsamı, 2565bd9d-dad50-47d4-8B85-4c97f669dc36 uygulama tanımlayıcısına sahip bir hizmet sorumlusu tarafından tanımlanır. Kapsam kaybını engellemek için hizmet sorumlusunu silmeyin veya değiştirmeyin. Yanlışlıkla silinirse, eşitleme kapsamı kurtarılamaz. 

Eşitleme kapsamını değiştirirseniz aşağıdaki uyarıları aklınızda bulundurun:

- Tam eşitleme gerçekleşir.
- Yönetilen etki alanında artık gerekli olmayan nesneler silinir. Yönetilen etki alanında yeni nesneler oluşturulur.

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [Azure AD Domain Services eşitlemeyi anlama][concepts-sync].

## <a name="powershell-script-for-scoped-synchronization"></a>Kapsamlı eşitleme için PowerShell betiği

PowerShell kullanarak kapsamlı eşitlemeyi yapılandırmak için, önce aşağıdaki betiği adlı bir dosyaya kaydedin `Select-GroupsToSync.ps1` .

Bu betik, Azure AD DS seçili grupları Azure AD 'den eşitleyecek şekilde yapılandırır. Belirtilen grupların parçası olan tüm Kullanıcı hesapları yönetilen etki alanıyla eşitlenir.

Bu komut dosyası, bu makaledeki ek adımlarda kullanılır.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Kapsamlı eşitlemeyi etkinleştir

Yönetilen bir etki alanı için grup tabanlı kapsamlı eşitlemeyi etkinleştirmek üzere aşağıdaki adımları izleyin:

1. Önce Azure AD DS kaynağında *"Filteredsync" = "Enabled"* olarak ayarlayın ve ardından yönetilen etki alanını güncelleştirin.

    İstendiğinde, [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açmak için bir *genel yöneticiye* ait kimlik bilgilerini belirtin:

    ```powershell
    # Connect to your Azure AD tenant
    Connect-AzureAD

    # Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    # Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    # Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Şimdi, kullanıcıların yönetilen etki alanına eşitlenmesi gereken grupların listesini belirtin.

    Betiği çalıştırın `Select-GroupsToSync.ps1` ve eşitlenecek grupların listesini belirtin. Aşağıdaki örnekte, *GroupName1* ve *GroupName2* eşitleyeceğiniz gruplar.

    > [!WARNING]
    > Kapsamlı eşitleme için grup listesine *AAD DC Administrators* grubunu dahil etmeniz gerekir. Bu grubu eklemezseniz, yönetilen etki alanı kullanılamaz olur.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Eşitleme kapsamını değiştirmek, yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitlemenin tamamlanması uzun zaman alabilir.

## <a name="modify-scoped-synchronization"></a>Kapsamlı eşitlemeyi değiştirme

Kullanıcıları yönetilen etki alanıyla eşitlenmesi gereken grupların listesini değiştirmek için, `Select-GroupsToSync.ps1` betiği çalıştırın ve eşitlenecek grupların yeni listesini belirtin.

Aşağıdaki örnekte, eşitlenmesi yapılacak gruplar artık *GroupName2* içermez ve artık *GroupName3*' yi içerir.

> [!WARNING]
> Kapsamlı eşitleme için grup listesine *AAD DC Administrators* grubunu dahil etmeniz gerekir. Bu grubu eklemezseniz, yönetilen etki alanı kullanılamaz olur.

İstendiğinde, [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açmak için bir *genel yöneticiye* ait kimlik bilgilerini belirtin:

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Eşitleme kapsamını değiştirmek, yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitlemenin tamamlanması uzun zaman alabilir.

## <a name="disable-scoped-synchronization"></a>Kapsamlı eşitlemeyi devre dışı bırak

Yönetilen bir etki alanı için grup tabanlı kapsamlı eşitlemeyi devre dışı bırakmak için, Azure AD DS kaynağında *"Filteredsync" = "Disabled"* seçeneğini belirleyin ve ardından yönetilen etki alanını güncelleştirin. Bu tamamlandığında, tüm kullanıcılar ve gruplar Azure AD 'den eşitlenmek üzere ayarlanır.

İstendiğinde, [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açmak için bir *genel yöneticiye* ait kimlik bilgilerini belirtin:

```powershell
# Connect to your Azure AD tenant
Connect-AzureAD

# Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

# Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

# Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Eşitleme kapsamını değiştirmek, yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitlemenin tamamlanması uzun zaman alabilir.

## <a name="next-steps"></a>Sonraki adımlar

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [Azure AD Domain Services eşitlemeyi anlama](synchronization.md).

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
