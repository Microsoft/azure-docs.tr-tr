---
title: PowerShell kullanarak Azure VM 'de Yönetilen kimlikler Yapılandırma-Azure AD
description: PowerShell kullanarak bir Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırmaya yönelik adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1eca17106067d964ba2f280d358b2973b41459a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783278"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>PowerShell kullanarak Azure VM 'de Azure kaynakları için Yönetilen kimlikler yapılandırma

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory ' de otomatik olarak yönetilen kimlik ile Azure hizmetleri sağlar. Bu kimliği, kodunuzda kimlik bilgileri olmadan Azure AD kimlik doğrulamasını destekleyen herhangi bir hizmette kimlik doğrulaması yapmak için kullanabilirsiniz.

Bu makalede, PowerShell 'i kullanarak Azure VM 'de Azure kaynakları işlemleri için aşağıdaki yönetilen kimlikleri nasıl gerçekleştireceğinizi öğreneceksiniz.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Örnek betikleri çalıştırmak için iki seçeneğiniz vardır:
    - Kod bloklarının sağ üst köşesindeki **It TRY** düğmesini kullanarak açabileceğiniz [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - [Azure PowerShell](/powershell/azure/install-az-ps)en son sürümünü yükleyerek betikleri yerel olarak çalıştırın ve ardından kullanarak Azure 'da oturum açın `Connect-AzAccount` . 

## <a name="system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimlik

Bu bölümde, Azure PowerShell kullanarak sistem tarafından atanan yönetilen kimliği etkinleştirmeyi ve devre dışı bırakmayı öğreneceksiniz.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Azure VM oluşturma sırasında sistem tarafından atanan yönetilen kimliği etkinleştirme

Sistem tarafından atanan yönetilen kimlik etkin bir Azure VM 'si oluşturmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Yalnızca gerekli bölümleri ("Azure 'da oturum aç", "kaynak grubu oluşturma", "ağ oluşturma grubu oluştur", "VM oluşturma") tamamlanırken aşağıdaki Azure VM hızlı başlangıçlarından birine bakın.

    "VM oluşturma" bölümüne geldiğinizde, [New-AzVMConfig](/powershell/module/az.compute/new-azvm) cmdlet sözdiziminde küçük bir değişiklik yapın. `-IdentityType SystemAssigned`VM 'yi, sistem tarafından atanan kimlik etkin olacak şekilde sağlamak için bir parametre eklediğinizden emin olun; örneğin:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [PowerShell kullanarak Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-powershell.md)
   - [PowerShell kullanarak Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Mevcut bir Azure VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirme

Başlangıçta sağlanan bir VM 'de sistem tarafından atanan yönetilen kimliği etkinleştirmek için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

1. Cmdlet 'ini kullanarak VM özelliklerini alın `Get-AzVM` . Ardından, sistem tarafından atanan bir yönetilen kimliği etkinleştirmek için `-IdentityType` [Update-azvm](/powershell/module/az.compute/update-azvm) cmdlet 'inde anahtarını kullanın:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>VM sistem tarafından atanan kimliği bir gruba ekle

Bir VM 'de sistem tarafından atanan kimliği etkinleştirdikten sonra, bir gruba ekleyebilirsiniz.  Aşağıdaki yordam bir gruba bir sanal makinenin sistem tarafından atanmış kimliğini ekler.

1. `ObjectID` `Id` VM 'nin hizmet sorumlusunun (döndürülen değerlerin alanında belirtildiği gibi) öğesini alın ve aklınızda edin:

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. `ObjectID` `Id` Grubunun (döndürülen değerlerin alanında belirtildiği gibi) ' i alın ve bu gruba göz önünde edin:

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. VM 'nin hizmet sorumlusunu gruba ekleyin:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Azure VM 'den sistem tarafından atanan yönetilen kimliği devre dışı bırakma

Bir VM 'de sistem tarafından atanan yönetilen kimliği devre dışı bırakmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.  Ek Azure AD dizin rolü ataması gerekli değildir.

Sistem tarafından atanan yönetilen kimliğe, ancak hala Kullanıcı tarafından atanan yönetilen kimliklere ihtiyaç duyulmayan bir sanal makineniz varsa, aşağıdaki cmdlet 'i kullanın:

1. Cmdlet 'ini kullanarak VM özelliklerini alın `Get-AzVM` ve `-IdentityType` parametresini şu şekilde ayarlayın `UserAssigned` :

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Artık sistem tarafından atanan yönetilen kimliğe ihtiyacı olmayan ve Kullanıcı tarafından atanan yönetilen kimliklere sahip olmayan bir sanal makineniz varsa, aşağıdaki komutları kullanın:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik

Bu bölümde, Azure PowerShell kullanarak bir VM 'den Kullanıcı tarafından atanan yönetilen kimlik ekleme ve kaldırma hakkında bilgi edineceksiniz.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Oluşturma sırasında bir VM 'ye Kullanıcı tarafından atanan yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. Yalnızca gerekli bölümleri ("Azure 'da oturum aç", "kaynak grubu oluşturma", "ağ oluşturma grubu oluştur", "VM oluşturma") tamamlanırken aşağıdaki Azure VM hızlı başlangıçlarından birine bakın.

    "VM oluşturma" bölümüne geldiğinizde cmdlet sözdiziminde küçük bir değişiklik yapın [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) . VM 'yi `-IdentityType UserAssigned` `-IdentityID` Kullanıcı tarafından atanan bir kimlikle sağlamak için ve parametrelerini ekleyin.  ,,, `<VM NAME>` `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` Ve `<USER ASSIGNED IDENTITY NAME>` değerlerini kendi değerlerinizle değiştirin.  Örnek:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [PowerShell kullanarak Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-powershell.md)
    - [PowerShell kullanarak Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Mevcut bir Azure VM 'sine Kullanıcı tarafından atanan bir yönetilen kimlik atama

Bir VM 'ye Kullanıcı tarafından atanan bir kimlik atamak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) ve [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) rol atamalarına ihtiyacı vardır. Ek Azure AD dizin rolü ataması gerekli değildir.

1. [New-Azuseratandidentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) cmdlet 'ini kullanarak Kullanıcı tarafından atanan bir yönetilen kimlik oluşturun.  `Id`Sonraki adımda bunun gerekli olacağı için çıktıda öğesine göz önünde bulabilirsiniz.

   > [!IMPORTANT]
   > Kullanıcı tarafından atanan yönetilen kimliklerin oluşturulması yalnızca alfasayısal, alt çizgi ve kısa çizgi (0-9 veya a-z veya A-Z veya \_ -) karakterlerini destekler. Ayrıca, VM/VMSS atamasının düzgün çalışması için ad 3 ile 128 karakter uzunluğunda olmalıdır. Daha fazla bilgi için bkz. [SSS ve bilinen sorunlar](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. Cmdlet 'ini kullanarak VM özelliklerini alın `Get-AzVM` . Ardından, Azure VM 'ye Kullanıcı tarafından atanan bir yönetilen kimlik atamak için `-IdentityType` `-IdentityID` [Update-azvm](/powershell/module/az.compute/update-azvm) cmdlet 'inde ve anahtarını kullanın.  Parametresinin değeri, `-IdentityId` `Id` önceki adımda not ettiğiniz değerdir.  ,,, `<VM NAME>` `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` Ve `<USER ASSIGNED IDENTITY NAME>` değerlerini kendi değerlerinizle değiştirin.

   > [!WARNING]
   > VM 'ye atanan önceden Kullanıcı tarafından atanan yönetilen kimlikleri tutmak için `Identity` VM nesnesinin özelliğini sorgulayın (örneğin, `$vm.Identity` ).  Yönetilen kimlikler atanmış herhangi bir Kullanıcı döndürülürse, sanal makineye atamak istediğiniz yeni kullanıcı tarafından atanmış yönetilen kimlik ile birlikte bunları aşağıdaki komuta dahil edin.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Kullanıcı tarafından atanan yönetilen kimliği bir Azure VM 'den kaldırma

Kullanıcı tarafından atanan kimliği bir VM 'ye kaldırmak için hesabınızın [sanal makine katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) rolü ataması gerekir.

SANAL makinenizde birden çok kullanıcı tarafından atanan yönetilen kimlik varsa, aşağıdaki komutları kullanarak en son biri hariç tümünü kaldırabilirsiniz. `<RESOURCE GROUP>` ve `<VM NAME>` parametre değerlerini kendi değerlerinizle değiştirmeyi unutmayın. , `<USER ASSIGNED IDENTITY NAME>` Kullanıcı tarafından atanan yönetilen kimliğin ad özelliğidir ve bu, VM 'de kalmalıdır. Bu bilgiler, `Identity` VM nesnesinin özelliği sorgulanarak bulunabilir.  Örneğin `$vm.Identity` :

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
SANAL makinenizde sistem tarafından atanan yönetilen bir kimlik yoksa ve bundan sonra Kullanıcı tarafından atanan tüm yönetilen kimlikleri kaldırmak istiyorsanız aşağıdaki komutu kullanın:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
SANAL makinenizde hem sistem tarafından atanan hem de Kullanıcı tarafından atanan Yönetilen kimlikler varsa, yalnızca sistem tarafından atanan yönetilen kimlikleri kullanmak üzere geçiş yaparak Kullanıcı tarafından atanan tüm yönetilen kimlikleri kaldırabilirsiniz.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış için Yönetilen kimlikler](overview.md)
- Azure VM oluşturma hızlı başlangıçlarını tam olarak görmek için bkz.:

  - [PowerShell ile Windows sanal makinesi oluşturma](../../virtual-machines/windows/quick-create-powershell.md)
  - [PowerShell ile Linux sanal makinesi oluşturma](../../virtual-machines/linux/quick-create-powershell.md)
