---
title: Azure PowerShell-Azure AD kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme & silme
description: Azure PowerShell kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme ve silme hakkında adım adım yönergeler.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: af76affd9f4034401225e82de4e25e8b0a51125a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784848"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Azure PowerShell kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme

Azure kaynakları için Yönetilen kimlikler, Azure Active Directory yönetilen bir kimlik ile Azure hizmetleri sağlar. Kodunuzda kimlik bilgileri gerekmeden Azure AD kimlik doğrulamasını destekleyen hizmetlere kimlik doğrulaması yapmak için bu kimliği kullanabilirsiniz. 

Bu makalede, Azure PowerShell kullanarak Kullanıcı tarafından atanan yönetilen kimlik oluşturmayı, listeleyeceğinizi ve silmeyi öğreneceksiniz.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Örnek betikleri çalıştırmak için iki seçeneğiniz vardır:
    - Kod bloklarının sağ üst köşesindeki **It TRY** düğmesini kullanarak açabileceğiniz [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - Komut dosyalarını, sonraki bölümde açıklandığı gibi Azure PowerShell yerel olarak çalıştırın.

### <a name="configure-azure-powershell-locally"></a>Azure PowerShell yerel olarak yapılandırma

Bu makale için Azure PowerShell yerel olarak kullanmak için (Cloud Shell kullanmak yerine), aşağıdaki adımları izleyin:

1. Henüz yapmadıysanız [Azure PowerShell en son sürümünü](/powershell/azure/install-az-ps) yükleyebilirsiniz.

1. Azure 'da oturum açın:

    ```azurepowershell
    Connect-AzAccount
    ```

1. [PowerShellGet'in en son sürümünü](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget) yükleyin.

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    `Exit`Sonraki adımda bu komutu çalıştırdıktan sonra geçerli PowerShell oturumunun olması gerekebilir.

1. `Az.ManagedServiceIdentity`Bu makalede Kullanıcı tarafından atanan yönetilen kimlik işlemlerini gerçekleştirmek için modülün ön sürümü sürümünü yükler:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimlik oluşturma

Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

Kullanıcı tarafından atanan yönetilen kimlik oluşturmak için `New-AzUserAssignedIdentity` komutunu kullanın. `ResourceGroupName`Parametresi, Kullanıcı tarafından atanan yönetilen kimliğin oluşturulacağı kaynak grubunu belirtir ve `-Name` parametresi adını belirtir. `<RESOURCE GROUP>`Ve `<USER ASSIGNED IDENTITY NAME>` parametre değerlerini kendi değerlerinizle değiştirin:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Kullanıcı tarafından atanan yönetilen kimlikleri listeleme

Kullanıcı tarafından atanan yönetilen kimliği listelemek/okumak için hesabınızın [yönetilen kimlik işleci](../../role-based-access-control/built-in-roles.md#managed-identity-operator) veya [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rolü ataması gerekir.

Kullanıcı tarafından atanan yönetilen kimlikleri listelemek için, [Get-Azuseratandı] komutunu kullanın.  `-ResourceGroupName`Parametresi, Kullanıcı tarafından atanan yönetilen kimliğin oluşturulduğu kaynak grubunu belirtir. `<RESOURCE GROUP>`Değerini kendi değeri ile değiştirin:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
Yanıtta, Kullanıcı tarafından atanan yönetilen kimliklerin `"Microsoft.ManagedIdentity/userAssignedIdentities"` anahtar için döndürülen değeri vardır `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Kullanıcı tarafından atanan yönetilen kimliği silme

Kullanıcı tarafından atanan bir yönetilen kimliği silmek için hesabınıza [yönetilen kimlik katılımcısı](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) rol ataması gerekir.

Kullanıcı tarafından atanan bir yönetilen kimliği silmek için `Remove-AzUserAssignedIdentity` komutunu kullanın.  `-ResourceGroupName`Parametresi, Kullanıcı tarafından atanan kimliğin oluşturulduğu kaynak grubunu belirtir ve `-Name` parametresi adını belirtir. `<RESOURCE GROUP>`Ve `<USER ASSIGNED IDENTITY NAME>` parametrelerinin değerlerini kendi değerlerinizle değiştirin:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> Kullanıcı tarafından atanan bir yönetilen kimliğin silinmesi başvuruyu, atandığı herhangi bir kaynaktan kaldırmaz. Kimlik atamalarının ayrı ayrı kaldırılması gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure kaynakları komutları için Azure PowerShell yönetilen kimliklerin tam listesi ve daha ayrıntılı bilgi için bkz. [az. Managedserviceıdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).
