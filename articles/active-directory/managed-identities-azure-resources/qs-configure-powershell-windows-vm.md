---
title: PowerShell kullanarak bir Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma
description: PowerShell kullanarak bir Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ba8ce6fb8147736c8265148a9f3576390dcccc6
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71309771"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>PowerShell kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen bir kimlikle Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz. 

Bu makalede, PowerShell 'i kullanarak Azure VM 'de Azure kaynakları işlemleri için aşağıdaki yönetilen kimlikleri nasıl gerçekleştireceğinizi öğreneceksiniz.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için yönetilen kimliklerle bilmiyorsanız kullanıma [genel bakış bölümünde](overview.md). **Gözden geçirmeyi unutmayın [sistem tarafından atanan ve kullanıcı tarafından atanan bir yönetilen kimlik arasındaki farkı](overview.md#how-does-it-work)** .
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Yükleme [Azure PowerShell'in en son sürümünü](/powershell/azure/install-az-ps) henüz yapmadıysanız.

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure PowerShell kullanarak sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştirme

Sistem tarafından atanan yönetilen kimlik etkin bir Azure VM 'si oluşturmak için hesabınızın [sanal makine katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Yalnızca gerekli bölümleri ("Azure 'da oturum aç", "kaynak grubu oluşturma", "ağ oluşturma grubu oluştur", "VM oluşturma") tamamlanırken aşağıdaki Azure VM hızlı başlangıçlarından birine bakın.
    
    "VM oluşturma" bölümüne geldiğinizde, [New-AzVMConfig](/powershell/module/az.compute/new-azvm) cmdlet sözdiziminde küçük bir değişiklik yapın. VM 'yi, sistem tarafından `-AssignIdentity:$SystemAssigned` atanan kimlik etkin olacak şekilde sağlamak için bir parametre eklediğinizden emin olun; örneğin:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [PowerShell kullanarak Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell kullanarak Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Mevcut bir Azure VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirme

Başlangıçta sağlanan bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Kullanarak `Connect-AzAccount`Azure 'da oturum açın. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesap kullanın.

   ```powershell
   Connect-AzAccount
   ```

2. İlk olarak `Get-AzVM` cmdlet 'ini kullanarak VM özelliklerini alın. Ardından, sistem tarafından atanan bir yönetilen kimliği etkinleştirmek için `-AssignIdentity` [Update-azvm](/powershell/module/az.compute/update-azvm) cmdlet 'inde anahtarını kullanın:

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```



### <a name="add-vm-system-assigned-identity-to-a-group"></a>VM sistem tarafından atanan kimliği bir gruba ekle

Bir VM 'de sistem tarafından atanan kimliği etkinleştirdikten sonra, bir gruba ekleyebilirsiniz.  Aşağıdaki yordam bir gruba bir sanal makinenin sistem tarafından atanmış kimliğini ekler.

1. Kullanarak `Connect-AzAccount`Azure 'da oturum açın. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesap kullanın.

   ```powershell
   Connect-AzAccount
   ```

2. VM 'nin hizmet sorumlusunun `ObjectID` (döndürülen değerlerin `Id` alanında belirtildiği gibi) öğesini alın ve aklınızda edin:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Grubunun `ObjectID` (döndürülen değerlerin `Id` alanında belirtildiği gibi) ' i alın ve bu gruba göz önünde edin:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. VM 'nin hizmet sorumlusunu gruba ekleyin:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM 'den sistem tarafından atanan yönetilen kimliği devre dışı bırakma

Bir VM 'de sistem tarafından atanan yönetilen kimliği devre dışı bırakmak için hesabınızın [sanal makine katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

Sistem tarafından atanan yönetilen kimliğe, ancak hala Kullanıcı tarafından atanan yönetilen kimliklere ihtiyaç duyulmayan bir sanal makineniz varsa, aşağıdaki cmdlet 'i kullanın:

1. Kullanarak `Connect-AzAccount`Azure 'da oturum açın. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesap kullanın.

   ```powershell
   Connect-AzAccount
   ```

2. `Get-AzVM` Cmdlet 'ini kullanarak VM özelliklerini alın ve `-IdentityType` parametresini şu şekilde `UserAssigned`ayarlayın:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Artık sistem tarafından atanan yönetilen kimliğe ihtiyacı olmayan ve Kullanıcı tarafından atanan yönetilen kimliklere sahip olmayan bir sanal makineniz varsa, aşağıdaki komutları kullanın:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```



## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure PowerShell kullanarak bir VM 'den Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma hakkında bilgi edineceksiniz.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Oluşturma sırasında bir VM 'ye Kullanıcı tarafından atanan yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) ve [yönetilen kimlik işleci](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Yalnızca gerekli bölümleri ("Azure 'da oturum aç", "kaynak grubu oluşturma", "ağ oluşturma grubu oluştur", "VM oluşturma") tamamlanırken aşağıdaki Azure VM hızlı başlangıçlarından birine bakın. 
  
    "VM oluşturma" bölümüne geldiğinizde [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) cmdlet sözdiziminde küçük bir değişiklik yapın. VM 'yi Kullanıcı `-IdentityID` tarafından atanan bir kimlikle sağlamak için veparametreleriniekleyin.`-IdentityType UserAssigned`  ,,, Ve `<VM NAME>` değerlerinikendideğerlerinizledeğiştirin.`<USER ASSIGNED IDENTITY NAME>` `<SUBSCRIPTION ID>` `<RESROURCE GROUP>`  Örneğin:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [PowerShell kullanarak Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell kullanarak Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-powershell.md)



### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Mevcut bir Azure VM 'sine Kullanıcı tarafından atanan bir yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) ve [yönetilen kimlik işleci](/azure/role-based-access-control/built-in-roles#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Kullanarak `Connect-AzAccount`Azure 'da oturum açın. VM 'yi içeren Azure aboneliğiyle ilişkili bir hesap kullanın.

   ```powershell
   Connect-AzAccount
   ```

2. [New-Azuseratandidentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) cmdlet 'ini kullanarak Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun.  Sonraki adımda `Id` bunun gerekli olacağı için çıktıda öğesine göz önünde bulabilirsiniz.

   > [!IMPORTANT]
   > Kullanıcı tarafından atanan yönetilen kimliklerin oluşturulması yalnızca alfasayısal, alt çizgi ve kısa çizgi (0-9 veya a-z veya a-z \_ veya-) karakterlerini destekler. Ayrıca, VM/VMSS atamasının düzgün çalışması için ad 3 ile 128 karakter uzunluğunda olmalıdır. Daha fazla bilgi için bkz. [SSS ve bilinen sorunlar](known-issues.md)

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. `Get-AzVM` Cmdlet 'ini kullanarak VM özelliklerini alın. Ardından, Azure VM 'ye Kullanıcı tarafından atanan bir yönetilen kimlik atamak için `-IdentityType` [Update-azvm](/powershell/module/az.compute/update-azvm) cmdlet 'inde ve `-IdentityID` anahtarını kullanın.  `-IdentityId` Parametresinin`Id` değeri, önceki adımda not ettiğiniz değerdir.  ,,, Ve `<VM NAME>` değerlerinikendideğerlerinizledeğiştirin.`<USER ASSIGNED IDENTITY NAME>` `<SUBSCRIPTION ID>` `<RESROURCE GROUP>`

   > [!WARNING]
   > VM 'ye atanan önceden Kullanıcı tarafından atanan yönetilen kimlikleri tutmak için VM nesnesinin `Identity` özelliğini sorgulayın (örneğin, `$vm.Identity`).  Yönetilen kimlikler atanmış herhangi bir Kullanıcı döndürülürse, sanal makineye atamak istediğiniz yeni kullanıcı tarafından atanmış yönetilen kimlik ile birlikte bunları aşağıdaki komuta dahil edin.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```



### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure VM 'den kaldırma

Kullanıcı tarafından atanan kimliği bir VM 'ye kaldırmak için hesabınızın [sanal makine katılımcısı](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) rolü ataması gerekir.

SANAL makinenizde birden çok kullanıcı tarafından atanan yönetilen kimlik varsa, aşağıdaki komutları kullanarak en son biri hariç tümünü kaldırabilirsiniz. `<RESOURCE GROUP>` ve `<VM NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY NAME>` Kullanıcı tarafından atanan yönetilen kimliğin ad özelliğidir ve bu, VM 'de kalmalıdır. Bu bilgiler, VM nesnesinin `Identity` özelliği sorgulanarak bulunabilir.  Örneğin `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
SANAL makinenizde sistem tarafından atanan yönetilen bir kimlik yoksa ve bundan sonra Kullanıcı tarafından atanan tüm yönetilen kimlikleri kaldırmak istiyorsanız aşağıdaki komutu kullanın:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
SANAL makinenizde hem sistem tarafından atanan hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, yalnızca sistem tarafından atanan yönetilen kimlikleri kullanmak üzere geçiş yaparak Kullanıcı tarafından atanan tüm yönetilen kimlikleri kaldırabilirsiniz.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış için yönetilen kimlik](overview.md)
- Azure VM oluşturma hızlı başlangıçlarını tam olarak görmek için bkz.:
  
  - [PowerShell ile Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-powershell.md) 
  - [PowerShell ile Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-powershell.md) 
