---
title: PowerShell-Azure DevTest Labs bir laboratuvarda özel rol oluşturma
description: Bu makalede, Azure DevTest Labs bir laboratuvarda özel bir rol oluşturan bir Azure PowerShell betiği sunulmaktadır.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3757d6c16a762a0f76f4718f117da93322af074c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021206"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs 'de laboratuarda özel bir rol oluşturmak için PowerShell kullanın

Bu örnek PowerShell betiği, Azure DevTest Labs laboratuvarında kullanmak üzere özel bir rol oluşturur. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Önkoşullar
* **Laboratuvar**. Betik, mevcut bir laboratuvarınızın olmasını gerektirir. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: 

| Komut | Notlar |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Azure RBAC kullanılarak güvenliği sağlanabilir bir Azure Kaynak sağlayıcısı için işlemleri alır. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Atama için kullanılabilen tüm Azure rollerini listeler. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Özel bir rol oluşturur. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Lab Services PowerShell betiği örnekleri [Azure Lab Services PowerShell örneklerinde](../samples-powershell.md)bulunabilir.
