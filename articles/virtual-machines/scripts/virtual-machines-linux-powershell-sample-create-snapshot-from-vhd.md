---
title: Azure PowerShell Betiği Örneği - Kısa sürede birden fazla aynı yönetilen disk oluşturmak için VHD’den anlık görüntü oluşturma
description: Azure PowerShell Betiği Örneği - Kısa sürede birden fazla aynı yönetilen disk oluşturmak için VHD’den anlık görüntü oluşturma
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 2cbb50ee7dcea75b1fb215a1cf32a75bd7caeb55
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033777"
---
# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>PowerShell ile kısa sürede birden fazla aynı yönetilen disk oluşturmak için VHD’den anlık görüntü oluşturma

Bu betik, aynı veya farklı abonelikteki bir depolama hesabında VHD dosyasından bir anlık görüntü oluşturur. Özelleştirilmiş (genelleştirilmiş/sysprepped uygulanmış olmayan) bir VHD’yi bir anlık görüntüye içeri aktarmak ve sonra anlık görüntüyü kullanarak kısa sürede birden fazla aynı yönetilen diskler oluşturmak için bu betiği kullanın. Bir veri VHD’sini anlık görüntüye içeri aktarmak ve sonra anlık görüntüyü kullanarak kısa sürede birden fazla yönetilen disk oluşturmak için de bunu kullanın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Create snapshot from VHD")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, farklı abonelikteki bir VHD’den yönetilen disk oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Disk oluşturmak için kullanılan disk yapılandırmasını oluşturur. Üst VHD’nin depolandığı depolama hesabının depolama türünü, konumunu, kaynak kimliğini ve üst VHD’nin VHD URI’sini içerir. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Parametre olarak geçirilen disk yapılandırmasını, disk adını ve kaynak grubu adını kullanarak bir disk oluşturur. |

## <a name="next-steps"></a>Sonraki adımlar

[Anlık görüntüden yönetilen disk oluşturma](virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek sanal makine PowerShell betiği örnekleri, [Azure Linux VM belgeleri](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) içinde bulunabilir.
