---
title: Yönetilen kaynak grubunu al & VM 'Leri yeniden boyutlandır-Azure PowerShell
description: Azure yönetilen uygulaması için yönetilen bir kaynak grubunu alan Azure PowerShell örnek betik sağlar. Betik, VM 'Leri yeniden boyutlandırır.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f11f9b56a8a17330fac3fb50bfd5f560357463e7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529407"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Yönetilen bir kaynak grubundaki kaynakları edinme ve VM 'Leri PowerShell ile yeniden boyutlandırma

Bu betik bir yönetilen kaynak grubundan kaynakları alır ve bu kaynak grubundaki VM’leri yeniden boyutlandırır.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Örnek betik

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Betik açıklaması

Bu betik, yönetilen uygulamayı dağıtmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-Azmanagedadpplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Yönetilen uygulamaları listeler. Sonuçları odaklamak için kaynak grubu adı belirtin. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Kaynakları listeler. Sonuca odaklanmak için bir kaynak grubu ve kaynak türü sağlayın. |
| [Güncelleştirme-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Sanal makinenin boyutunu güncelleştirir. |


## <a name="next-steps"></a>Sonraki adımlar

* Yönetilen uygulamalara giriş için [Azure Yönetilen Uygulamalara genel bakış](../overview.md) konusunu inceleyin.
* PowerShell hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](https://docs.microsoft.com/powershell/azure/get-started-azureps).
