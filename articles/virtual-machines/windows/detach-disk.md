---
title: Bir Windows VM 'den veri diski ayırma-Azure
description: Kaynak Yöneticisi dağıtım modelini kullanarak Azure 'daki bir sanal makineden bir veri diskini ayırın.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 93db2935fdc41787bb1820d1f8ce85ac05ef0863
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033354"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Bir Windows sanal makinesinden veri diski ayırma

Sanal makineye bağlı bir veri diskine ihtiyacınız olmadığında bunu kolayca ayırabilirsiniz. Bu, diski sanal makineden kaldırır, ancak depolama alanından kaldırmaz.

> [!WARNING]
> Bir diski ayırdıysanız otomatik olarak silinmez. Premium depolamaya abone olduysa, disk için depolama ücretleri uygulanmaya devam edersiniz. Daha fazla bilgi için [Premium Depolama kullanılırken fiyatlandırma ve faturalandırma](disks-types.md#billing)bölümüne bakın.

Disk üzerinde var olan verileri yeniden kullanmak isterseniz bu verileri aynı sanal makineye veya başka birine yeniden ekleyebilirsiniz.

 

## <a name="detach-a-data-disk-using-powershell"></a>PowerShell kullanarak veri diskini ayırma

PowerShell kullanarak bir veri diskini *etkin* bir şekilde kaldırabilirsiniz, ancak diskin VM 'den kullanımdan çıkarmadan önce hiçbir şeyin etkin bir şekilde kullanıldığından emin olun.

Bu örnekte, **Myresourcegroup** kaynak grubundaki **myvm** VM 'sinden **mydisk** adlı diski kaldırdık. Önce [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) cmdlet 'ini kullanarak diski kaldırırsınız. Ardından, veri diskini kaldırma işlemini gerçekleştirmek için [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) cmdlet 'ini kullanarak sanal makinenin durumunu güncelleştirin.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Disk depolamada kalır, ancak artık bir sanal makineye bağlı değildir.

## <a name="detach-a-data-disk-using-the-portal"></a>Portalı kullanarak veri diski çıkarma

1. Sol taraftaki menüden **sanal makineler**' i seçin.
2. Ayırmak istediğiniz veri diskine sahip sanal makineyi seçin ve VM 'yi serbest bırakmak için **Durdur** ' a tıklayın.
3. Sanal makine bölmesinde, **diskler**' i seçin.
4. **Diskler** bölmesinin üst kısmında **Düzenle**' yi seçin.
5. **Diskler** bölmesinde, ayırmak istediğiniz veri diskinin en sağında yer alan ![ayır düğme görüntüsünü](./media/detach-disk/detach.png) ayır düğmesine tıklayın.
5. Disk kaldırıldıktan sonra bölmenin en üstünde yer alan **Kaydet** ' e tıklayın.
6. Sanal makine bölmesinde **genel bakış** ' a tıklayın ve ardından sanal makineyi yeniden başlatmak için bölmenin üst kısmındaki **Başlat** düğmesine tıklayın.

Disk depolamada kalır, ancak artık bir sanal makineye bağlı değildir.

## <a name="next-steps"></a>Sonraki adımlar

Veri diskini yeniden kullanmak istiyorsanız, [bunu başka BIR VM 'ye](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ekleyebilirsiniz