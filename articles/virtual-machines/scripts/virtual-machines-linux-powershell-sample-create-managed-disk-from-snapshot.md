---
title: Azure PowerShell Betiği Örneği - Anlık görüntüden yönetilen disk oluşturma
description: Azure PowerShell Betiği Örneği - Anlık görüntüden yönetilen disk oluşturma
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
ms.openlocfilehash: 93f2e24a2a32af1fb4bfa6560b27ce6c113ad7a1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040150"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>PowerShell ile anlık görüntüden yönetilen disk oluşturma

Bu betik bir anlık görüntüden yönetilen disk oluşturur. İşletim sistemi anlık görüntülerinden ve veri disklerinden bir sanal makineyi geri yüklemek için bu betiği kullanın. İlgili anlık görüntülerden işletim sistemi ve veri diskleri oluşturun ve sonra yönetilen diskleri ekleyerek yeni bir sanal makine oluşturun. Ayrıca, anlık görüntülerden oluşturulan veri disklerini ekleyerek mevcut bir VM'nin veri disklerini geri yükleyebilirsiniz.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Örnek betik

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]

## <a name="script-explanation"></a>Betik açıklaması

Bu betik bir anlık görüntüden yönetilen disk oluşturmak için aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [Get-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/Get-AzSnapshot) | Anlık görüntü özelliklerini alır.  |
| [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzDiskConfig) | Disk oluşturmak için kullanılan disk yapılandırmasını oluşturur. Üst anlık görüntünün kaynak kimliğini, üst anlık görüntünün konumuyla aynı olan konumu ve depolama türünü içerir.  |
| [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | Parametre olarak geçirilen disk yapılandırmasını, disk adını ve kaynak grubu adını kullanarak bir disk oluşturur. |

## <a name="next-steps"></a>Sonraki adımlar


[Yönetilen diskten sanal makine oluşturma](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Azure PowerShell modülü hakkında daha fazla bilgi için bkz. [Azure PowerShell belgeleri](/powershell/azure/overview).

Ek sanal makine PowerShell betiği örnekleri, [Azure Linux VM belgeleri](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) içinde bulunabilir.
