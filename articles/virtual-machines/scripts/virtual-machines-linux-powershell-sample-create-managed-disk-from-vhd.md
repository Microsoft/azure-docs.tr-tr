---
title: Azure PowerShell Betiği Örneği - Aynı veya farklı abonelikteki bir depolama hesabında VHD dosyasından yönetilen disk oluşturma
description: Azure PowerShell Betiği Örneği - Aynı veya farklı abonelikteki bir depolama hesabında VHD dosyasından yönetilen disk oluşturma
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
ms.openlocfilehash: 941fd7e4fc883317b6968e87fc52048523b396d4
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74031500"
---
# <a name="create-a-managed-disk-from-a-vhd-file-in-a-storage-account-in-same-or-different-subscription-with-powershell"></a>PowerShell kullanarak aynı veya farklı abonelikteki bir depolama hesabında VHD dosyasından yönetilen disk oluşturma

Bu betik, aynı veya farklı abonelikteki bir depolama hesabında VHD dosyasından bir yönetilen disk oluşturur. Bir sanal makine oluşturmak üzere yönetilen işletim sistemi diskine özelleştirilmiş (genelleştirilmemiş/sysprep uygulanmamış) bir VHD’yi almak için bu betiği kullanın. Veya yönetilen veri diskine bir veri VHD’si içeri aktarmak için bunu kullanın.

Kısa sürede bir VHD dosyasından birden fazla aynı yönetilen disk oluşturmayın. Bir vhd dosyasından yönetilen diskler oluşturmak için vhd dosyasının blob anlık görüntüsü oluşturulur ve sonra yönetilen diskler oluşturmak için kullanılır. Bir dakika içinde, azaltma nedeniyle disk oluşturma hatalarına neden olan yalnızca bir blob anlık görüntüsü oluşturulabilir. Bu azaltmayı önlemek için, bir [vhd dosyasındaki yönetilen anlık görüntü](virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oluşturun ve sonra kısa sürede birden fazla yönetilen disk oluşturmak için yönetilen anlık görüntüyü kullanın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disks-from-vhd-in-different-subscription/create-managed-disks-from-vhd-in-different-subscription.ps1 "Create managed disk from VHD")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik, farklı abonelikteki bir VHD’den yönetilen disk oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Disk oluşturmak için kullanılan disk yapılandırmasını oluşturur. Üst VHD’nin depolandığı depolama hesabının depolama türünü, konumunu, kaynak kimliğini ve üst VHD’nin VHD URI’sini içerir. |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Parametre olarak geçirilen disk yapılandırmasını, disk adını ve kaynak grubu adını kullanarak bir disk oluşturur. |

## <a name="next-steps"></a>Sonraki adımlar

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek sanal makine PowerShell betiği örnekleri, [Azure Linux VM belgeleri](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) içinde bulunabilir.
