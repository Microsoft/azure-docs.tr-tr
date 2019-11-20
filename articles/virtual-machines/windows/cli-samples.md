---
title: Azure CLı örnekleri Windows
description: Azure CLı örnekleri Windows
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
ms.openlocfilehash: 8d57220f79f1349937a279e57a17d51a76bcccb3
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033636"
---
# <a name="azure-cli-samples-for-windows-virtual-machines"></a>Windows sanal makineleri için Azure CLı örnekleri

Aşağıdaki tabloda, Windows sanal makinelerini dağıtan Azure CLı kullanılarak oluşturulan Bash betiklerinin bağlantıları yer almaktadır.

| | |
|---|---|
|**Sanal makineler oluşturma**||
| [Sanal makine oluşturun](./../scripts/virtual-machines-windows-cli-sample-create-vm-quick-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | En az yapılandırmaya sahip bir Windows sanal makinesi oluşturur. |
| [Tam olarak yapılandırılmış bir sanal makine oluşturma](./../scripts/virtual-machines-windows-cli-sample-create-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir kaynak grubu, sanal makine ve tüm ilgili kaynakları oluşturur.|
| [Yüksek oranda kullanılabilir sanal makineler oluşturma](./../scripts/virtual-machines-windows-cli-sample-nlb.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Birden çok yüksek oranda kullanılabilir sanal makineler ve yük dengeli küme yapılandırması oluşturur. |
| [VM oluşturma ve yapılandırma betiği çalıştırma](./../scripts/virtual-machines-windows-cli-sample-create-vm-iis.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir sanal makine oluşturur ve IIS yüklemek için Azure Özel Betik uzantısı 'nı kullanır. |
| [VM oluşturma ve DSC yapılandırması çalıştırma](./../scripts/virtual-machines-windows-cli-sample-create-iis-using-dsc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir sanal makine oluşturur ve IIS yüklemek için Azure Istenen durum yapılandırması (DSC) uzantısını kullanır. |
|**Depolamayı yönetme**||
| [Bir VHD 'den yönetilen disk oluşturma](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Özelleştirilmiş bir VHD 'den işletim sistemi diski olarak ya da veri diski olarak bir veri VHD 'sinden yönetilen bir disk oluşturur.  |
| [Anlık görüntüden yönetilen disk oluşturma](../scripts/virtual-machines-windows-cli-sample-create-managed-disk-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir anlık görüntüden yönetilen disk oluşturur. |
| [Yönetilen diski aynı veya farklı aboneliğe kopyalama](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Yönetilen diski aynı veya farklı aboneliğe, ancak üst yönetilen diskle aynı bölgeye kopyalar. 
| [Bir anlık görüntüyü bir depolama hesabına VHD olarak dışarı aktarma](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-storage-account.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Yönetilen bir anlık görüntüyü, farklı bölgedeki bir depolama hesabına VHD olarak dışa aktarır. |
| [Yönetilen bir diskin VHD 'sini bir depolama hesabına dışarı aktarma](../scripts/virtual-machines-windows-cli-sample-copy-managed-disks-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Yönetilen bir diskin temel VHD 'sini farklı bölgedeki bir depolama hesabına dışarı aktarır. |
| [Anlık görüntüyü aynı veya farklı aboneliğe kopyalama](../scripts/virtual-machines-windows-cli-sample-copy-snapshot-to-same-or-different-subscription.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Anlık görüntüyü aynı veya farklı aboneliğe, ancak üst anlık görüntüyle aynı bölgeye kopyalar. |
|**Ağ sanal makineleri**||
| [Sanal makineler arasında güvenli ağ trafiği](./../scripts/virtual-machines-windows-cli-sample-create-vm-nsg.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | İki sanal makine, tüm ilgili kaynakları ve bir iç ve dış ağ güvenlik grupları (NSG) oluşturur. |
|**Güvenli sanal makineler**||
| [VM ve veri disklerini şifreleme](./../scripts/virtual-machines-windows-cli-sample-encrypt-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir Azure Key Vault, şifreleme anahtarı ve hizmet sorumlusu oluşturur, ardından bir VM 'yi şifreler. |
|**Sanal makineleri izleme**||
| [Azure Izleyici ile VM izleme](./../scripts/virtual-machines-windows-cli-sample-create-vm-oms.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) | Bir sanal makine oluşturur, Log Analytics aracısını yükleyip VM 'yi bir Log Analytics çalışma alanına kaydeder.  |
| | |
