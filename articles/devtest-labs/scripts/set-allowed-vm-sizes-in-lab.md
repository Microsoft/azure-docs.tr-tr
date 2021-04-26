---
title: 'PowerShell betiği: Azure Lab Services izin verilen VM boyutlarını ayarlama | Microsoft Docs'
description: Bu makale, Azure Lab Services izin verilen sanal makine (VM) boyutlarını ayarlayan bir örnek PowerShell betiği içerir.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998150"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Azure Lab Services izin verilen VM boyutlarını ayarlamak için PowerShell 'i kullanma

Bu örnek PowerShell betiği, Azure Lab Services izin verilen sanal makine (VM) boyutlarını ayarlar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Önkoşullar
* **Laboratuvar**. Betik, mevcut bir laboratuvarınızın olmasını gerektirir. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: 

| Komut | Notlar |
|---|---|
| Find-AzResource | Belirtilen parametrelere göre kaynak arar. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Kaynakları alır. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Bir kaynağı değiştirir. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Kaynak oluşturma. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Lab Services PowerShell betiği örnekleri [Azure Lab Services PowerShell örneklerinde](../samples-powershell.md)bulunabilir.
