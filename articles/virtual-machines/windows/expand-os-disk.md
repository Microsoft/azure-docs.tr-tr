---
title: Azure 'da Windows VM 'nin işletim sistemi sürücüsünü genişletme
description: Kaynak Yöneticisi dağıtım modelinde Azure PowerShell kullanarak bir sanal makinenin işletim sistemi sürücüsünün boyutunu genişletin.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199454"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Bir sanal makinenin işletim sistemi sürücüsünü genişletme

[Azure Marketi](https://azure.microsoft.com/marketplace/)'nden bir görüntü dağıtarak bir kaynak grubunda yeni bir sanal makıne (VM) oluşturduğunuzda, varsayılan işletim sistemi sürücüsü genellıkle 127 GB olur (bazı görüntülerde varsayılan olarak daha küçük işletim sistemi disk boyutları bulunur). SANAL makineye veri diskleri eklemek mümkün olsa da (numara seçtiğiniz SKU 'ya bağlıdır) ve bu ek disklere uygulamalar ve CPU yoğunluklu iş yükleri yüklemeniz önerilir, genellikle müşterilerin belirli senaryoları desteklemesi için işletim sistemi sürücüsünü genişlemeleri gerekir:

- İşletim sistemi sürücüsüne bileşen yükleyen eski uygulamaları desteklemek için.
- Fiziksel bir BILGISAYARı veya VM 'yi Şirket içinden daha büyük bir işletim sistemi sürücüsüyle geçirmek için.

> [!IMPORTANT]
> Bir Azure sanal makinesinin işletim sistemini veya veri diskini yeniden boyutlandırmak için sanal makinenin serbest bırakılmış olması gerekir.
>
> Mevcut bir diskin küçültülmesi desteklenmez ve muhtemelen veri kaybına neden olabilir.
> 
> Diskleri genişlettikten sonra, daha büyük diskten yararlanmak için [işletim sistemi içindeki birimi genişletmeniz](#expand-the-volume-within-the-os) gerekir.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Azure portal yönetilen bir diski yeniden boyutlandırma

1. [Azure Portal](https://portal.azure.com), diski genişletmek istediğiniz sanal makineye gidin. VM 'yi serbest bırakmak için **Durdur** ' u seçin.
2. VM durdurulduğunda, sol taraftaki menüde, **Ayarlar**' ın altında, **diskler**' i seçin.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Menünün ayarlar bölümünde seçilen diskler seçeneğini gösteren ekran görüntüsü.":::

 
3. **Disk adı** bölümünde, yeniden boyutlandırmak istediğiniz diski seçin.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Bir disk adı seçili olan diskler bölmesini gösteren ekran görüntüsü.":::

4. **Ayarlar** altındaki sol menüde **Boyut + performans**' ı seçin.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Menünün ayarlar bölümünde seçilen boyut ve performans seçeneğinin gösterildiği ekran görüntüsü.":::

5. **Boyut ve performans**' da istediğiniz disk boyutunu seçin.
   
   > [!WARNING]
   > Yeni boyut mevcut disk boyutundan büyük olmalıdır. İşletim sistemi diskleri için izin verilen en yüksek 2.048 GB 'dir. (VHD blobunun boyutunu bu boyuttan büyük bir şekilde genişletmek mümkündür, ancak işletim sistemi yalnızca ilk 2.048 GB alan ile çalışabilir.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Boyut ve performans bölmesini seçilen disk boyutuyla gösteren ekran görüntüsü.":::

6. Sayfanın alt kısmındaki **yeniden boyutlandır** ' ı seçin.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Boyutlandır düğmesinin seçili olduğu boyut ve performans bölmesini gösteren ekran görüntüsü.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>PowerShell kullanarak yönetilen bir diski yeniden boyutlandırma

PowerShell ıSE veya PowerShell pencerenizi yönetim modunda açın ve aşağıdaki adımları izleyin:

1. Kaynak yönetimi modunda Microsoft Azure hesabınızda oturum açın ve aboneliğinizi seçin:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Kaynak grubu adınızı ve sanal makine adınızı ayarlayın:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. VM 'nize bir başvuru alın:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Diski yeniden boyutlandırmadan önce VM 'yi durdurun:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Yönetilen işletim sistemi diskine bir başvuru alın. Yönetilen işletim sistemi diskinin boyutunu istenen değere ayarlayın ve diski güncelleştirin:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Yeni boyut mevcut disk boyutundan büyük olmalıdır. İşletim sistemi diskleri için izin verilen en yüksek 2.048 GB 'dir. (VHD blobunun boyutunu bu boyuttan daha büyük bir şekilde genişletmek mümkündür, ancak işletim sistemi yalnızca ilk 2.048 GB alan ile çalışabilir.)
    > 
         
6. VM 'nin güncelleştirilmesi birkaç saniye sürebilir. Komutun yürütülmesi tamamlandığında, VM 'yi yeniden başlatın:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

İşte bu kadar! Şimdi RDP ile sanal makinenize girin, Bilgisayar Yönetimi’ni (veya Disk Yönetimi) açın ve yeni ayrılan alanı kullanarak sürücüyü genişletin.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>PowerShell kullanarak yönetilmeyen bir diski yeniden boyutlandırma

PowerShell ıSE veya PowerShell pencerenizi yönetim modunda açın ve aşağıdaki adımları izleyin:

1. Kaynak yönetimi modunda Microsoft Azure hesabınızda oturum açın ve aboneliğinizi seçin:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Kaynak grubu adınızı ve sanal makine adlarınızı ayarlayın:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. VM 'nize bir başvuru alın:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Diski yeniden boyutlandırmadan önce VM 'yi durdurun:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Yönetilmeyen işletim sistemi diskinin boyutunu istenen değere ayarlayın ve VM 'yi güncelleştirin:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Yeni boyut mevcut disk boyutundan büyük olmalıdır. İşletim sistemi diskleri için izin verilen en yüksek 2.048 GB 'dir. (VHD Blobun boyutunu bu boyuttan daha büyük bir şekilde genişletmek mümkündür, ancak işletim sistemi yalnızca ilk 2.048 GB alan ile çalışabilir.)
    > 
    > 
   
6. VM 'nin güncelleştirilmesi birkaç saniye sürebilir. Komutun yürütülmesi tamamlandığında, VM 'yi yeniden başlatın:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>İşletim sistemi diski için betikler

Hem yönetilen hem de yönetilmeyen diskler için başvurağınız için tüm komut dosyası aşağıda verilmiştir:


**Yönetilen diskler**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Yönetilmeyen diskler**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Veri disklerini yeniden boyutlandırma

Bu makale, birincil olarak VM 'nin işletim sistemi diskini genişletmeden odaklanır, ancak betik, VM 'ye bağlı veri disklerini genişletmek için de kullanılabilir. Örneğin, VM’ye bağlı ilk veri diskini genişletmek için `StorageProfile` öğesinin `OSDisk` nesnesini `DataDisks` dizisi ile değiştirin ve aşağıda gösterildiği gibi sayısal bir dizin kullanarak ilk bağlanan veri diskinin başvurusunu edinin:

**Yönetilen disk**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Yönetilmeyen disk**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Benzer şekilde, yukarıda gösterildiği gibi bir dizin kullanarak VM 'ye bağlı diğer veri disklerine ya da diskin **Name** özelliğine başvurabilirsiniz:


**Yönetilen disk**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Yönetilmeyen disk**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>İşletim sistemi içindeki birimi genişletme

VM için diski genişlettikten sonra işletim sistemine gitmeniz ve yeni alanı kapsayacak şekilde birimi genişletmeniz gerekir. Bölüm genişletmek için birkaç yöntem vardır. Bu bölümde, **DiskPart** kullanarak bölümü genişletmek IÇIN bir RDP BAĞLANTıSı kullanarak VM 'nin bağlanması ele alınmaktadır.

1. VM 'nize bir RDP bağlantısı açın.

2. Bir komut istemi açın ve **DiskPart** yazın.

3. **DISKPART** isteminde yazın `list volume` . Genişletmek istediğiniz birimi unutmayın.

4. **DISKPART** isteminde yazın `select volume <volumenumber>` . Bu, aynı diskteki bitişik boş alana genişletmek istediğiniz birim *volumenumber* seçer.

5. **DISKPART** isteminde yazın `extend [size=<size>]` . Bu, seçilen birimi megabayt (MB) cinsinden *boyuta* genişletir.


## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal](attach-managed-disk-portal.md)kullanarak da disk ekleyebilirsiniz.
