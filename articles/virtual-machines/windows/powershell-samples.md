---
title: Azure sanal makine PowerShell örnekleri
description: Azure sanal makine PowerShell örnekleri
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f068b79f1b1eaa9a11df70052619c8e3993101cb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033007"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure sanal makine PowerShell örnekleri

Aşağıdaki tabloda, Windows sanal makinelerini (VM 'Ler) oluşturan ve yöneten PowerShell betiği örneklerinin bağlantıları verilmiştir.

| | |
|---|---|
|**Sanal makineler oluşturma**||
| [Hızlı bir şekilde sanal makine oluşturun](./../scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | En az istem içeren bir kaynak grubu, bir sanal makine ve tüm ilgili kaynakları oluşturur.|
| [Tam olarak yapılandırılmış bir sanal makine oluşturma](./../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir kaynak grubu, bir sanal makine ve tüm ilgili kaynakları oluşturur.|
| [Yüksek oranda kullanılabilir sanal makineler oluşturma](./../scripts/virtual-machines-windows-powershell-sample-create-nlb-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Yüksek oranda kullanılabilir ve yük dengeli bir yapılandırmada birkaç sanal makine oluşturur.|
| [VM oluşturma ve yapılandırma betiği çalıştırma](./../scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir sanal makine oluşturur ve IIS yüklemek için Azure Özel Betik uzantısı 'nı kullanır. |
| [VM oluşturma ve DSC yapılandırması çalıştırma](./../scripts/virtual-machines-windows-powershell-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir sanal makine oluşturur ve IIS yüklemek için Azure Istenen durum yapılandırması (DSC) uzantısını kullanır. |
| [Bir VHD 'YI karşıya yükleme ve VM oluşturma](./../scripts/virtual-machines-windows-powershell-upload-generalized-script.md) | Yerel bir VHD dosyasını Azure 'a yükler, VHD 'den bir görüntü oluşturur ve sonra bu görüntüden bir VM oluşturur. |
| [Yönetilen işletim sistemi diskinden VM oluşturma](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Mevcut bir yönetilen diski işletim sistemi diski olarak ekleyerek bir sanal makine oluşturur. |
| [Anlık görüntüden VM oluşturma](./../scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Önce anlık görüntüden yönetilen bir disk oluşturup sonra yeni yönetilen diski işletim sistemi diski olarak ekleyerek bir anlık görüntüden sanal makine oluşturur. |
|**Depolamayı yönetme**||
| [Aynı veya farklı bir abonelikte bulunan bir VHD 'den yönetilen disk oluşturma](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Özelleştirilmiş bir VHD 'den işletim sistemi diski olarak veya bir veri VHD 'sinden, aynı veya farklı bir abonelikte bir veri diski olarak yönetilen bir disk oluşturur.  |
| [Anlık görüntüden yönetilen disk oluşturma](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir anlık görüntüden yönetilen disk oluşturur. |
| [Yönetilen bir diski aynı veya farklı bir aboneliğe kopyalama](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fcli%2fmodule%2ftoc.json) | Yönetilen bir diski, ana yönetilen diskle aynı bölgedeki aynı veya farklı bir aboneliğe kopyalar.
| [Bir anlık görüntüyü bir depolama hesabına VHD olarak dışarı aktarma](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Yönetilen bir anlık görüntüyü, farklı bir bölgedeki depolama hesabına VHD olarak dışa aktarır. |
| [Yönetilen bir diskin VHD 'sini bir depolama hesabına dışarı aktarma](../scripts/virtual-machines-windows-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Yönetilen bir diskin temel VHD 'sini farklı bir bölgedeki depolama hesabına dışarı aktarır. |
| [Bir VHD 'den anlık görüntü oluşturma](../scripts/virtual-machines-windows-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir VHD 'den anlık görüntü oluşturur ve daha sonra bu anlık görüntüyü kullanarak birden fazla özdeş yönetilen disk oluşturur.  |
| [Bir anlık görüntüyü aynı veya farklı bir aboneliğe kopyalama](../scripts/virtual-machines-windows-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Anlık görüntüyü, üst anlık görüntüyle aynı bölgedeki aynı veya farklı bir aboneliğe kopyalar. |
|**Güvenli sanal makineler**||
| [VM 'yi ve veri disklerini şifreleyin](./../scripts/virtual-machines-windows-powershell-sample-encrypt-vm.md?toc=%2fpowershell%2fazure%2ftoc.json) | Bir Azure Anahtar Kasası, bir şifreleme anahtarı ve bir hizmet sorumlusu oluşturur ve ardından bir VM 'yi şifreler. |
|**Sanal makineleri izleme**||
| [Azure Izleyici ile VM izleme](./../scripts/virtual-machines-windows-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir sanal makine oluşturur, Azure Log Analytics aracısını yükleyip VM 'yi bir Log Analytics çalışma alanına kaydeder.  |
| [PowerShell ile bir abonelikteki tüm VM 'Lerle ilgili ayrıntıları toplama](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Belirtilen abonelikte VM adı, kaynak grubu adı, bölge, sanal ağ, alt ağ, özel IP adresi, işletim sistemi türü ve VM 'lerin genel IP adresini içeren bir CSV oluşturur.
| | |
