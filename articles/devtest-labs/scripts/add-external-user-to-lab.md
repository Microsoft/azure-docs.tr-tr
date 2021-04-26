---
title: PowerShell-Azure DevTest Labs ' de bir laboratuvara dış Kullanıcı ekleme
description: Bu makalede, Azure DevTest Labs bir laboratuvara dış Kullanıcı ekleyen bir Azure PowerShell betiği sunulmaktadır.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 76fbb5e4d7f5db39a0aadba9098ebf1064b3cda5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022403"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>PowerShell kullanarak Azure DevTest Labs bir laboratuvara dış Kullanıcı ekleme

Bu örnek PowerShell betiği, Azure DevTest Labs bir laboratuvarına dış Kullanıcı ekler. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Önkoşullar
* **Laboratuvar**. Betik, mevcut bir laboratuvarınızın olmasını gerektirir. 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik şu komutları kullanır: 

| Komut | Notlar |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Azure Active Directory 'den Kullanıcı nesnesini yeniden dener. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Belirtilen kapsamdaki belirtilen Role belirtilen rolü atar. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/).

Ek Azure Lab Services PowerShell betiği örnekleri [Azure Lab Services PowerShell örneklerinde](../samples-powershell.md)bulunabilir.
