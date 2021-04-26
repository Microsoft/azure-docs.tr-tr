---
title: PowerShell kullanarak bir kaynağa yönetilen kimlik erişimi atama-Azure AD
description: Bir kaynakta yönetilen kimlik atamaya, başka bir kaynağa erişim için PowerShell kullanarak adım adım yönergeler.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc5df641f27f8d604f7b5647736128856a3ecd51
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764378"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>PowerShell kullanarak bir kaynağa yönetilen kimlik erişimi atama

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Yönetilen kimliğe sahip bir Azure kaynağı yapılandırdıktan sonra, herhangi bir güvenlik sorumlusu gibi, yönetilen kimliğe başka bir kaynağa erişim izni verebilirsiniz. Bu örnekte, PowerShell kullanarak bir Azure depolama hesabına Azure sanal makinesinin yönetilen kimlik erişimi verme yöntemi gösterilmektedir.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

- Azure kaynakları için Yönetilen kimlikler hakkında bilginiz varsa [genel bakış bölümüne](overview.md)bakın. **[Sistem tarafından atanan ve Kullanıcı tarafından atanan yönetilen kimlik arasındaki farkı](overview.md#managed-identity-types)gözden geçirdiğinizden emin** olun.
- Henüz bir Azure hesabınız yoksa, devam etmeden önce [ücretsiz bir hesaba kaydolun](https://azure.microsoft.com/free/).
- Örnek betikleri çalıştırmak için iki seçeneğiniz vardır:
    - Kod bloklarının sağ üst köşesindeki **It TRY** düğmesini kullanarak açabileceğiniz [Azure Cloud Shell](../../cloud-shell/overview.md)kullanın.
    - [Azure PowerShell](/powershell/azure/install-az-ps)en son sürümünü yükleyerek betikleri yerel olarak çalıştırın ve ardından kullanarak Azure 'da oturum açın `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Başka bir kaynağa yönetilen kimlik erişimi atamak için Azure RBAC kullanma

1. [Azure VM](qs-configure-powershell-windows-vm.md)gibi bir Azure kaynağında yönetilen kimliği etkinleştirin.

1. Bu örnekte, bir depolama hesabına Azure VM erişimi veriyoruz. İlk olarak, yönetilen kimliği etkinleştirdiğimiz sırada oluşturulan adlı VM 'ye ait hizmet sorumlusunu almak için [Get-AzVM](/powershell/module/az.compute/get-azvm) `myVM` ' i kullanıyoruz. Ardından, [Yeni-Azroleatama](/powershell/module/Az.Resources/New-AzRoleAssignment) kullanarak VM **okuyucusu** için adlı bir depolama hesabına erişim sağlayın `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure kaynaklarına genel bakış için yönetilen kimlik](overview.md)
- Azure VM 'de yönetilen kimliği etkinleştirmek için bkz. [PowerShell kullanarak Azure VM 'de Azure kaynakları için yönetilen kimlikleri yapılandırma](qs-configure-powershell-windows-vm.md).
