---
title: Öğretici - Azure PowerShell ile Azure disklerini yönetme
description: Bu öğreticide, Azure CLI PowerShell kullanarak sanal makineler için Azure diskleri oluşturup yönetmeyi öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: a61d7425a7a907230008ab1d4f15a836150e1518
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549065"
---
# <a name="tutorial---manage-azure-disks-with-azure-powershell"></a>Öğretici - Azure PowerShell ile Azure disklerini yönetme

Azure sanal makineleri, VM’lerin işletim sistemini, uygulamalarını ve verilerini depolamak için diskleri kullanır. VM oluştururken, beklenen iş yüküne uygun disk boyutu ve yapılandırmasını seçmek önemlidir. Bu öğreticide, VM disklerini dağıtma ve yönetme gösterilmektedir. Şunları öğreneceksiniz:

> [!div class="checklist"]
> * İşletim sistemi diskleri ve geçici diskler
> * Veri diskleri
> * Standart ve Premium diskler
> * Disk performansı
> * Veri disklerini ekleme ve hazırlama

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="default-azure-disks"></a>Varsayılan Azure diskleri

Azure sanal makinesi oluşturulduğunda, sanal makineye otomatik olarak iki disk eklenir. 

**İşletim sistemi diski** - İşletim sistemi diskleri 4 terabayta kadar boyutlandırılabilir ve VM'nin işletim sistemini barındırır. Bir [Azure Marketi](https://azure.microsoft.com/marketplace/) görüntüsünden yeni bir sanal makıne (VM) oluşturursanız, genellıkle 127 GB (ancak bazı görüntülerin daha küçük işletim sistemi disk boyutları vardır). İşletim sistemi diskine, varsayılan olarak *C:* sürücü harfi atanır. İşletim sistemi diskinin yapılandırmasını önbelleğe alan disk, işletim sistemi performansı için iyileştirilir. İşletim sistemi diski, uygulama veya veri **barındırmamalıdır**. Uygulamalar ve veriler için, bu makalede daha sonra ayrıntılı olarak açıklanan bir veri diski kullanın.

**Geçici disk** - Geçici diskler, VM ile aynı Azure ana bilgisayarında bulunan bir katı hal sürücüsü kullanır. Geçici diskler yüksek performansa sahiptir ve geçici veri işleme gibi işlemler için kullanılabilir. Ancak VM yeni bir konağa taşındığında, geçici diskte depolanan tüm veriler kaldırılır. Geçici diskin boyutu, [VM boyutu](../sizes.md)tarafından belirlenir. Geçici disklere varsayılan olarak *D:* sürücü harfi atanır.

## <a name="azure-data-disks"></a>Azure veri diskleri

Uygulama yüklemek ve veri depolamak için ek veri diskleri eklenebilir. Dayanıklı ve duyarlı veri depolama gerektiren her koşulda veri diskleri kullanılmalıdır. Sanal makinenin boyutu, bir VM’ye kaç veri diskinin eklenebileceğini belirler.

## <a name="vm-disk-types"></a>VM disk türleri

Azure iki disk türü sağlar.

**Standart diskler** - HDD’ler ile desteklenir ve uygun maliyetli bir depolama sağlarken iyi bir performans da sunar. Standart diskler, uygun maliyetli bir geliştirme ve iş yükü testi için idealdir.

**Premium diskler** -SSD tabanlı, yüksek performanslı ve düşük gecikmeli disk tarafından desteklenir. Üretim iş yükü çalıştıran VM'ler için son derece uygundur. [Boyut adında](../vm-naming-conventions.md) **S** olan VM boyutları genellikle Premium depolamayı destekler. Örneğin, DS serisi, DSv2 serisi, GS serisi ve FS Serisi VM 'Ler Premium depolamayı destekler. Disk boyutu seçilirken boyutun değeri sonraki türe yuvarlanır. Örneğin, disk boyutu 64 GB 'den büyükse ancak 128 GB 'den küçükse, disk türü P10 olur. 
<br>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Standart depolamanın aksine bir Premium Depolama diski sağladığınızda, bu diskin kapasitesi, ıOPS ve aktarım hızı garanti edilir. Örneğin, bir P50 diski oluşturursanız, Azure bu disk için 4.095 GB depolama kapasitesi, 7.500 ıOPS ve 250 MB/sn aktarım hızı sağlar. Uygulamanız, kapasite ve performansın tümünü veya bir bölümünü kullanabilir. Premium SSD diskler, önceki tabloda% 99,9 ' de açıklanan düşük tek basamaklı milisaniyelik gecikme süreleri ve hedef ıOPS ve aktarım hızı sağlamak üzere tasarlanmıştır.

Yukarıdaki tablo, disk başına maksimum IOPS tanımlamış olsa da, daha yüksek düzeyde performansa birden çok veri diskini bölümleyerek ulaşılabilir. Örneğin, Standard_GS5 VM’ye 64 veri diski eklenebilir. Bu disklerin her biri P30 olarak boyutlandırılırsa, en fazla 80.000 IOPS’ye ulaşılabilir. VM başına maksimum IOPS hakkında ayrıntılı bilgi için bkz. [VM türleri ve boyutları](../sizes.md).

## <a name="create-and-attach-disks"></a>Disk oluşturma ve ekleme

Bu öğreticideki örneği tamamlamak için, mevcut bir sanal makinenizin olması gerekir. Gerekirse, aşağıdaki komutlarla bir sanal makine oluşturun.

[Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) ile sanal makinede yönetici hesabı için gereken kullanıcı adı ve parolasını ayarlayın:


[New-AzVM](/powershell/module/az.compute/new-azvm)ile sanal makineyi oluşturun. VM’nin yönetici hesabı için bir kullanıcı adı ve parola girmeniz istenir.

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupDisk" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" 
```


[Yeni-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig)ile ilk yapılandırmayı oluşturun. Aşağıdaki örnek boyutu 128 gigabayt olan bir diski yapılandırır.

```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
    -Location "EastUS" `
    -CreateOption Empty `
    -DiskSizeGB 128
```

[New-AzDisk](/powershell/module/az.compute/new-azdisk) komutuyla veri diskini oluşturun.

```azurepowershell-interactive
$dataDisk = New-AzDisk `
    -ResourceGroupName "myResourceGroupDisk" `
    -DiskName "myDataDisk" `
    -Disk $diskConfig
```

[Get-AzVM](/powershell/module/az.compute/get-azvm) komutuyla veri diski eklemek istediğiniz sanal makineyi alın.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName "myResourceGroupDisk" -Name "myVM"
```

Veri diskini, [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk) komutuyla sanal makine yapılandırmasına ekleyin.

```azurepowershell-interactive
$vm = Add-AzVMDataDisk `
    -VM $vm `
    -Name "myDataDisk" `
    -CreateOption Attach `
    -ManagedDiskId $dataDisk.Id `
    -Lun 1
```

Sanal makineyi [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk) komutuyla güncelleştirin.

```azurepowershell-interactive
Update-AzVM -ResourceGroupName "myResourceGroupDisk" -VM $vm
```

## <a name="prepare-data-disks"></a>Veri disklerini hazırlama

Bir disk sanal makineye eklendikten sonra, diskin kullanılması için işletim sisteminin yapılandırılması gerekir. Aşağıdaki örnek, VM’ye eklenen ilk diski el ile yapılandırmayı gösterir. Bu işlem [özel betik uzantısı](./tutorial-automate-vm-deployment.md) kullanılarak otomatik olarak da yapılabilir.

### <a name="manual-configuration"></a>El ile yapılandırma

Sanal makine ile bir RDP bağlantısı oluşturun. PowerShell'i açın ve bu betiği çalıştırın.

```azurepowershell
Get-Disk | Where partitionstyle -eq 'raw' |
    Initialize-Disk -PartitionStyle MBR -PassThru |
    New-Partition -AssignDriveLetter -UseMaximumSize |
    Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="verify-the-data-disk"></a>Veri diskini doğrulama

Veri diskinin eklendiğini doğrulamak amacıyla, eklenen `DataDisks` için `StorageProfile` öğesini görüntüleyin.

```azurepowershell-interactive
$vm.StorageProfile.DataDisks
```

Çıkış şu örneğe benzer olmalıdır:

```
Name            : myDataDisk
DiskSizeGB      : 128
Lun             : 1
Caching         : None
CreateOption    : Attach
SourceImage     :
VirtualHardDisk :
```


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunun gibi VM disk konularını öğrendiniz:

> [!div class="checklist"]
> * İşletim sistemi diskleri ve geçici diskler
> * Veri diskleri
> * Standart ve Premium diskler
> * Disk performansı
> * Veri disklerini ekleme ve hazırlama

VM yapılandırmasını otomatikleştirme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [VM yapılandırmasını otomatikleştirme](./tutorial-automate-vm-deployment.md)
