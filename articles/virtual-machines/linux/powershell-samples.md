---
title: Azure sanal makine PowerShell örnekleri
description: Azure sanal makine PowerShell örnekleri
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: cynthn
ms.openlocfilehash: 903860dfd1e30f7941770efd759227a1349d41ad
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035243"
---
# <a name="azure-virtual-machine-powershell-samples"></a>Azure sanal makine PowerShell örnekleri

Aşağıdaki tabloda, Linux sanal makineleri oluşturup yöneten PowerShell betikleri örneklerinin bağlantıları yer almaktadır.

| | |
|---|---|
|**Sanal makineler oluşturma**||
| [Tam olarak yapılandırılmış bir sanal makine oluşturma](./../scripts/virtual-machines-linux-powershell-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir kaynak grubu, sanal makine ve tüm ilgili kaynakları oluşturur.|
| [Docker etkin bir VM oluşturma](./../scripts/virtual-machines-linux-powershell-sample-create-docker-host.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir sanal makine oluşturur, bu VM 'yi bir Docker Konağı olarak yapılandırır ve NGıNX kapsayıcısını çalıştırır. |
| [VM oluşturma ve yapılandırma betiği çalıştırma](./../scripts/virtual-machines-linux-powershell-sample-create-vm-nginx.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir sanal makine oluşturur ve NGıNX yüklemek için Azure Özel Betik uzantısı 'nı kullanır. |
| [WordPress yüklü bir VM oluşturma](./../scripts/virtual-machines-linux-powershell-sample-create-vm-wordpress.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir sanal makine oluşturur ve WordPress 'yi yüklemek için Azure Özel Betik uzantısı 'nı kullanır. |
| [Yönetilen işletim sistemi diskinden VM oluşturma](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Mevcut bir yönetilen diski işletim sistemi diski olarak ekleyerek bir sanal makine oluşturur. |
| [Anlık görüntüden VM oluşturma](./../scripts/virtual-machines-linux-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Önce anlık görüntüden yönetilen bir disk oluşturup sonra yeni yönetilen diski işletim sistemi diski olarak ekleyerek bir anlık görüntüden sanal makine oluşturur. |
|**Depolamayı yönetme**||
| [Aynı veya farklı bir abonelikte bulunan bir VHD 'den yönetilen disk oluşturma](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Özelleştirilmiş bir VHD 'den işletim sistemi diski olarak veya bir veri VHD 'sinden, aynı veya farklı bir abonelikte bir veri diski olarak yönetilen bir disk oluşturur.  |
| [Anlık görüntüden yönetilen disk oluşturma](../scripts/virtual-machines-linux-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir anlık görüntüden yönetilen disk oluşturur. |
| [Bir anlık görüntüyü bir depolama hesabına VHD olarak dışarı aktarma](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Yönetilen bir anlık görüntüyü, farklı bir bölgedeki depolama hesabına VHD olarak dışa aktarır. |
| [Yönetilen bir diskin VHD 'sini bir depolama hesabına dışarı aktarma](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Yönetilen bir diskin temel VHD 'sini farklı bir bölgedeki depolama hesabına dışarı aktarır. |
| [Bir VHD 'den anlık görüntü oluşturma](../scripts/virtual-machines-linux-powershell-sample-create-snapshot-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir VHD 'den anlık görüntü oluşturur ve daha sonra bu anlık görüntüyü kullanarak birden fazla özdeş yönetilen disk oluşturur.  |
| [Bir anlık görüntüyü aynı veya farklı bir aboneliğe kopyalama](../scripts/virtual-machines-linux-powershell-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Anlık görüntüyü, üst anlık görüntüyle aynı bölgedeki aynı veya farklı bir aboneliğe kopyalar. |
|**Sanal makineleri izleme**||
| [Azure Izleyici günlükleri ile VM izleme](./../scripts/virtual-machines-linux-powershell-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Bir sanal makine oluşturur, Log Analytics aracısını yükleyip VM 'yi bir Log Analytics çalışma alanına kaydeder.  |
| [Yönetilen bir diski aynı veya farklı bir aboneliğe kopyalama](../scripts/virtual-machines-linux-powershell-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Yönetilen bir diski, ana yönetilen diskle aynı bölgedeki aynı veya farklı bir aboneliğe kopyalar.
| [PowerShell ile bir abonelikteki tüm VM 'Lerle ilgili ayrıntıları toplama](../scripts/virtual-machines-powershell-sample-collect-vm-details.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | Belirtilen abonelikte VM adı, kaynak grubu adı, bölge, sanal ağ, alt ağ, özel IP adresi, işletim sistemi türü ve VM 'lerin genel IP adresini içeren bir CSV oluşturur.
| | |
