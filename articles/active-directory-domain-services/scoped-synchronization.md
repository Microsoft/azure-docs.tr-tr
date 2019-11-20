---
title: Azure AD Domain Services için kapsamlı eşitleme | Microsoft Docs
description: Azure AD 'den Azure Active Directory Domain Services yönetilen bir etki alanına kapsamlı eşitlemeyi yapılandırmayı öğrenin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 09/06/2019
ms.author: iainfou
ms.openlocfilehash: 5fe19d3800883782187ae15c0a6fc0cd9709f0e9
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842673"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services"></a>Azure AD 'den kapsamlı eşitlemeyi Azure Active Directory Domain Services olarak yapılandırma

Azure Active Directory Domain Services (Azure AD DS), kimlik doğrulama hizmetleri sağlamak için kullanıcıları ve grupları Azure AD 'den eşitler. Karma bir ortamda, şirket içi Active Directory Domain Services (AD DS) ortamından kullanıcılar ve gruplar önce Azure AD Connect kullanılarak Azure AD ile eşitlenebilir ve sonra Azure AD DS ile eşitlenebilir. Varsayılan olarak, bir Azure AD dizininden tüm kullanıcılar ve gruplar Azure AD DS yönetilen bir etki alanıyla eşitlenir. Belirli gereksinimleriniz varsa, bunun yerine yalnızca tanımlı bir kullanıcı kümesini eşitlemeyi tercih edebilirsiniz.

Bu makalede, kapsamlı eşitleme kullanan bir Azure AD DS yönetilen etki alanı oluşturma ve ardından kapsamlı kullanıcı kümesini değiştirme veya devre dışı bırakma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="scoped-synchronization-overview"></a>Kapsamlı eşitlemeye genel bakış

Varsayılan olarak, bir Azure AD dizininden tüm kullanıcılar ve gruplar Azure AD DS yönetilen bir etki alanıyla eşitlenir. Yönetilen etki alanına yalnızca birkaç kullanıcının erişmesi gerekiyorsa yalnızca bu kullanıcı hesaplarını eşitlemeniz yeterlidir. Bu kapsamlı eşitleme grup tabanlıdır. Grup tabanlı kapsamlı eşitleme yapılandırdığınızda, yalnızca belirttiğiniz gruplara ait olan kullanıcı hesapları Azure AD DS tarafından yönetilen etki alanına eşitlenir.

Aşağıdaki tabloda kapsamlı eşitlemenin nasıl kullanılacağı özetlenmektedir:

| Geçerli durum | İstenen durum | Gerekli yapılandırma |
| --- | --- | --- |
| Mevcut bir yönetilen etki alanı, tüm Kullanıcı hesaplarını ve grupları eşitleyecek şekilde yapılandırılır. | Yalnızca belirli gruplara ait olan kullanıcı hesaplarını eşleştirmek istiyorsunuz. | Tüm kullanıcıları, kapsamlı eşitleme kullanarak eşitlemeye geçiş yapamazsınız. [Mevcut yönetilen etki alanını silin](delete-aadds.md)ve ardından bu makaledeki adımları izleyerek Azure AD DS yönetilen bir etki alanını kapsamlı eşitleme yapılandırılmış bir şekilde yeniden oluşturun. |
| Mevcut bir yönetilen etki alanı yok. | Yeni bir yönetilen etki alanı oluşturmak ve yalnızca belirli gruplara ait kullanıcı hesaplarını eşleştirmek istiyorsunuz. | Kapsamlı eşitleme yapılandırılmış bir Azure AD DS yönetilen etki alanı oluşturmak için bu makaledeki adımları izleyin. |
| Mevcut bir yönetilen etki alanı, yalnızca belirli gruplara ait olan hesapları eşitleyecek şekilde yapılandırılır. | Kullanıcıları Azure AD DS tarafından yönetilen etki alanına eşitlenmesi gereken grupların listesini değiştirmek istiyorsunuz. | Kapsamlı eşitlemeyi değiştirmek için bu makaledeki adımları izleyin. |

Kapsamlı eşitleme ayarlarını yapılandırmak için Azure portal veya PowerShell kullanın:

| Action | | |
|--|--|--|
| Azure AD DS yönetilen etki alanı oluşturma ve kapsamlı eşitlemeyi yapılandırma | [Azure portal](#enable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#enable-scoped-synchronization-using-powershell) |
| Kapsamlı eşitlemeyi değiştirme | [Azure portal](#modify-scoped-synchronization-using-the-azure-portal) | [PowerShell](#modify-scoped-synchronization-using-powershell) |
| Kapsamlı eşitlemeyi devre dışı bırak | [Azure portal](#disable-scoped-synchronization-using-the-azure-portal) | [PowerShell](#disable-scoped-synchronization-using-powershell) |

> [!WARNING]
> Eşitleme kapsamını değiştirmek, Azure AD DS yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur.
> 
>  * Azure AD DS yönetilen bir etki alanının eşitleme kapsamını değiştirdiğinizde, tam bir yeniden eşitleme gerçekleşir.
>  * Azure AD DS yönetilen etki alanında artık gerekli olmayan nesneler silinir. Yönetilen etki alanında yeni nesneler oluşturulur.
>  * Yeniden eşitlemenin tamamlanması uzun zaman alabilir. Eşitleme süresi, Azure AD DS yönetilen etki alanındaki ve Azure AD dizinindeki kullanıcılar, gruplar ve grup üyelikleri gibi nesne sayısına bağlıdır. Yüzlerce binlerce nesne içeren büyük dizinler için yeniden eşitleme işlemi birkaç gün sürebilir.

## <a name="enable-scoped-synchronization-using-the-azure-portal"></a>Azure portal kullanarak kapsamlı eşitlemeyi etkinleştirme

1. [Azure AD DS örneği oluşturmak ve yapılandırmak için öğreticiyi](tutorial-create-instance.md)izleyin. Eşitleme kapsamı dışında tüm önkoşulları ve dağıtım adımlarını doldurun.
1. Eşitleme adımında **kapsam** ' ı seçin, ardından Azure AD DS örneğiyle EŞITLENECEK Azure AD gruplarını seçin.

Azure AD DS yönetilen etki alanının, dağıtımın tamamlanması bir saate kadar sürebilir. Azure portal Azure AD DS yönetilen etki alanınız için **genel bakış** sayfasında, bu dağıtım aşamasının tamamında geçerli durum gösterilir.

Azure portal Azure AD DS yönetilen etki alanının sağlamayı bitirmiş olduğunu gösteriyorsa, aşağıdaki görevlerin tamamlanması gerekir:

* Sanal makinelerin, etki alanına katılması veya kimlik doğrulaması için yönetilen etki alanını bulabileceği şekilde sanal ağ için DNS ayarlarını güncelleştirin.
    * DNS 'yi yapılandırmak için portalda Azure AD DS yönetilen etki alanınızı seçin. **Genel bakış** penceresinde, bu DNS ayarlarını otomatik olarak yapılandırmanız istenir.
* Son kullanıcıların şirket kimlik bilgilerini kullanarak yönetilen etki alanında oturum açmasını sağlamak [için parola eşitlemesini Azure AD Domain Services etkinleştirin](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) .

## <a name="modify-scoped-synchronization-using-the-azure-portal"></a>Azure portal kullanarak kapsamlı eşitlemeyi değiştirme

Kullanıcıları Azure AD DS yönetilen etki alanı ile eşitlenmesi gereken grupların listesini değiştirmek için aşağıdaki adımları izleyin:

1. Azure portal, *contoso.com*gibi Azure AD DS örneğinizi seçin.
1. Sol taraftaki menüden **eşitleme** ' yi seçin.
1. Bir grup eklemek için üstteki **grupları + seç** ' i seçin ve ardından eklenecek grupları seçin.
1. Bir grubu eşitleme kapsamından kaldırmak için, şu anda eşitlenmiş gruplar listesinden seçin ve **grupları kaldır**' ı seçin.
1. Tüm değişiklikler yapıldığında, **eşitleme kapsamını kaydet**' i seçin.

Eşitleme kapsamını değiştirmek, Azure AD DS yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Azure AD DS tarafından yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması uzun sürebilir.

## <a name="disable-scoped-synchronization-using-the-azure-portal"></a>Azure portal kullanarak kapsamlı eşitlemeyi devre dışı bırakma

Azure AD DS yönetilen bir etki alanı için grup tabanlı kapsamlı eşitlemeyi devre dışı bırakmak için aşağıdaki adımları izleyin:

1. Azure portal, *contoso.com*gibi Azure AD DS örneğinizi seçin.
1. Sol taraftaki menüden **eşitleme** ' yi seçin.
1. Eşitleme kapsamını kapsam dışında tümü **olarak** ayarlayınve ardından **eşitleme kapsamını kaydet**' i seçin.

Eşitleme kapsamını değiştirmek, Azure AD DS yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Azure AD DS tarafından yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması uzun sürebilir.

## <a name="powershell-script-for-scoped-synchronization"></a>Kapsamlı eşitleme için PowerShell betiği

PowerShell kullanarak kapsamlı eşitlemeyi yapılandırmak için, önce aşağıdaki betiği adlı `Select-GroupsToSync.ps1`bir dosyaya kaydedin. Bu betik, Azure AD DS seçili grupları Azure AD 'den eşitleyecek şekilde yapılandırır. Belirtilen grupların parçası olan tüm Kullanıcı hesapları Azure AD DS yönetilen etki alanına eşitlenir.

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

## <a name="enable-scoped-synchronization-using-powershell"></a>PowerShell kullanarak kapsamlı eşitlemeyi etkinleştirme

Bu adım kümesini gerçekleştirmek için PowerShell 'i kullanın. [PowerShell kullanarak Azure Active Directory Domain Services etkinleştirme](powershell-create-instance.md)yönergelerine bakın. Bu makaledeki birkaç adım kapsamlı eşitlemeyi yapılandırmak için biraz değiştirilmiştir.

1. Azure AD DS PowerShell kullanarak etkinleştirmek için aşağıdaki görevleri makalesini doldurun. Yönetilen etki alanını gerçekten oluşturmak için bu adımı durdurun. Kapsamlı eşitlemeyi, Azure AD DS yönetilen etki alanını oluşturduğunuz şekilde yapılandırırsınız.

   * [Gerekli PowerShell modüllerini yükler](powershell-create-instance.md#prerequisites).
   * [Yönetici erişimi için gerekli hizmet sorumlusu ve Azure AD grubunu oluşturun](powershell-create-instance.md#create-required-azure-ad-resources).
   * [Sanal ağ ve alt ağlar gibi destekleyici Azure kaynakları oluşturun](powershell-create-instance.md#create-supporting-azure-resources).

1. Azure AD 'den eşitlenmesini istediğiniz grupları ve kullanıcıları saptayın. Azure AD DS eşitlenmek üzere grupların görünen adlarının listesini oluşturun.

1. [Önceki bölümden betiği](#powershell-script-for-scoped-synchronization) çalıştırın ve *-groupstoadd* parametresini kullanarak, eşitlenmek üzere grupların listesini geçirin.

   > [!WARNING]
   > Kapsamlı eşitleme için grup listesine *AAD DC Administrators* grubunu dahil etmeniz gerekir. Bu grubu eklemezseniz, Azure AD DS yönetilen etki alanı kullanılamaz.

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

1. Şimdi Azure AD DS yönetilen etki alanını oluşturun ve grup tabanlı kapsamlı eşitlemeyi etkinleştirin. *-Properties* parametresinde *"filteredsync" = "Enabled"* ekleyin.

    Azure abonelik KIMLIĞINIZI ayarlayın ve ardından yönetilen etki alanı için *contoso.com*gibi bir ad sağlayın. [Get-AzSubscription][Get-AzSubscription] cmdlet 'ini kullanarak abonelik kimliğinizi alabilirsiniz. Destekleyici Azure kaynaklarını oluşturmak için kaynak grubu adı, sanal ağ adı ve bölgeyi önceki adımlarda kullanılan değerlere ayarlayın:

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso.com"
   $ResourceGroupName = "myResourceGroup"
   $VnetName = "myVnet"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -Force -Verbose
   ```

Kaynağın oluşturulması ve denetimin PowerShell istemine döndürülmesi birkaç dakika sürer. Azure AD DS yönetilen etki alanı arka planda sağlanmaya devam eder ve dağıtımın tamamlanması bir saate kadar sürebilir. Azure portal Azure AD DS yönetilen etki alanınız için **genel bakış** sayfasında, bu dağıtım aşamasının tamamında geçerli durum gösterilir.

Azure portal Azure AD DS yönetilen etki alanının sağlamayı bitirmiş olduğunu gösteriyorsa, aşağıdaki görevlerin tamamlanması gerekir:

* Sanal makinelerin, etki alanına katılması veya kimlik doğrulaması için yönetilen etki alanını bulabileceği şekilde sanal ağ için DNS ayarlarını güncelleştirin.
    * DNS 'yi yapılandırmak için portalda Azure AD DS yönetilen etki alanınızı seçin. **Genel bakış** penceresinde, bu DNS ayarlarını otomatik olarak yapılandırmanız istenir.
* Son kullanıcıların şirket kimlik bilgilerini kullanarak yönetilen etki alanında oturum açmasını sağlamak [için parola eşitlemesini Azure AD Domain Services etkinleştirin](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) .

## <a name="modify-scoped-synchronization-using-powershell"></a>PowerShell kullanarak kapsamlı eşitlemeyi değiştirme

Kullanıcıları Azure AD DS yönetilen etki alanı ile eşitlenmesi gereken grupların listesini değiştirmek için, [PowerShell betiğini](#powershell-script-for-scoped-synchronization) yeniden çalıştırın ve yeni grup listesini belirtin. Aşağıdaki örnekte, eşitlenmesi yapılacak gruplar artık *GroupName2*içermez ve artık *GroupName3*' yi içerir.

> [!WARNING]
> Kapsamlı eşitleme için grup listesine *AAD DC Administrators* grubunu dahil etmeniz gerekir. Bu grubu eklemezseniz, Azure AD DS yönetilen etki alanı kullanılamaz.

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Eşitleme kapsamını değiştirmek, Azure AD DS yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Azure AD DS tarafından yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması uzun sürebilir.

## <a name="disable-scoped-synchronization-using-powershell"></a>PowerShell kullanarak kapsamlı eşitlemeyi devre dışı bırakma

Azure AD DS yönetilen bir etki alanı için grup tabanlı kapsamlı eşitlemeyi devre dışı bırakmak için, Azure AD DS kaynağında *"Filteredsync" = "Disabled"* seçeneğini belirleyin ve ardından yönetilen etki alanını güncelleştirin. Bu tamamlandığında, tüm kullanıcılar ve gruplar Azure AD 'den eşitlenmek üzere ayarlanır.

```powershell
// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Eşitleme kapsamını değiştirmek, Azure AD DS yönetilen etki alanının tüm verileri yeniden eşitlemesine neden olur. Azure AD DS tarafından yönetilen etki alanında artık gerekli olmayan nesneler silinir ve yeniden eşitleme işleminin tamamlanması uzun sürebilir.

## <a name="next-steps"></a>Sonraki adımlar

Eşitleme işlemi hakkında daha fazla bilgi edinmek için bkz. [Azure AD Domain Services eşitlemeyi anlama](synchronization.md).

<!-- EXTERNAL LINKS -->
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
