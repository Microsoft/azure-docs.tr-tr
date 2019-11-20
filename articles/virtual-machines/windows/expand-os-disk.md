---
title: Azure 'da Windows VM 'nin işletim sistemi sürücüsünü genişletme
description: Kaynak Yöneticisi dağıtım modelinde Azure PowerShell kullanarak bir sanal makinenin işletim sistemi sürücüsünün boyutunu genişletin.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: c76f57d15cd4cbdad5ded3b7545aab4d57272a50
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033492"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Bir sanal makinenin işletim sistemi sürücüsünü genişletme

[Azure Marketi](https://azure.microsoft.com/marketplace/)'nden bir görüntü dağıtarak bir kaynak grubunda yeni bir sanal makıne (VM) oluşturduğunuzda, varsayılan işletim sistemi sürücüsü genellıkle 127 GB olur (bazı görüntülerde varsayılan olarak daha küçük işletim sistemi disk boyutları bulunur). VM’ye veri diskleri eklemek (kaç tane ekleyebileceğiniz seçtiğiniz SKU’ya bağlıdır) mümkün olmasına, hatta uygulamaları ve yoğun CPU kullanımlı iş yüklerini bu ek disklere yüklemeniz önerilmesine rağmen, sıklıkla müşterilerin aşağıdaki gibi belirli senaryoları etkinleştirmesi için işletim sistemi sürücüsünü genişletmesi gerekir:

- İşletim sistemi sürücüsüne bileşen yükleyen eski uygulamaları destekleme.
- Fiziksel bir bilgisayarı veya sanal makineyi daha büyük bir işletim sistemi sürücüsüne sahip şirket içi kaynaktan geçirme.


> [!IMPORTANT]
> Bir Azure sanal makinesinin işletim sistemi diskinin yeniden boyutlandırılması için sanal makinenin serbest bırakılmış olması gerekir.
>
> Diskleri genişlettikten sonra, daha büyük diskten yararlanmak için [işletim sistemi içindeki birimi genişletmeniz](#expand-the-volume-within-the-os) gerekir.
> 


 


## <a name="resize-a-managed-disk"></a>Yönetilen diski yeniden boyutlandırma

Powershell ISE veya Powershell pencerenizi yönetim modunda açın ve aşağıdaki adımları izleyin:

1. Kaynak yönetimi modunda Microsoft Azure hesabınızda oturum açın ve aboneliğinizi aşağıdaki gibi seçin:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Aşağıdakileri yaparak kaynak grubunuzun adını ve VM adını ayarlayın:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Aşağıdakileri yaparak sanal makineniz için bir başvuru edinin:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Aşağıdakileri yaparak diski yeniden boyutlandırmadan önce VM’yi durdurun:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Yönetilen işletim sistemi diskine bir başvuru alın. Yönetilen işletim sistemi diskinin boyutunu istenen değere ayarlayın ve diski aşağıdaki gibi güncelleştirin:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Yeni boyut mevcut disk boyutundan büyük olmalıdır. İşletim sistemi diskleri için izin verilen en yüksek 2048 GB 'dir. (VHD blobunun boyutunu bu boyuttan daha büyük bir şekilde genişletmek mümkündür, ancak işletim sistemi yalnızca ilk 2048 GB alan ile çalışabilir.)
   > 
   > 
6. VM güncelleştirmesi biraz zaman alabilir. Komutun yürütülmesi tamamlandığında aşağıdakileri yaparak VM’yi yeniden başlatın:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Hepsi bu! Şimdi RDP ile sanal makinenize girin, Bilgisayar Yönetimi’ni (veya Disk Yönetimi) açın ve yeni ayrılan alanı kullanarak sürücüyü genişletin.

## <a name="resize-an-unmanaged-disk"></a>Yönetilmeyen bir diski yeniden boyutlandırma

Powershell ISE veya Powershell pencerenizi yönetim modunda açın ve aşağıdaki adımları izleyin:

1. Kaynak yönetimi modunda Microsoft Azure hesabınızda oturum açın ve aboneliğinizi aşağıdaki gibi seçin:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Aşağıdakileri yaparak kaynak grubunuzun adını ve VM adını ayarlayın:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Aşağıdakileri yaparak sanal makineniz için bir başvuru edinin:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. Aşağıdakileri yaparak diski yeniden boyutlandırmadan önce VM’yi durdurun:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Yönetilmeyen işletim sistemi diskinin boyutunu istenen değere ayarlayın ve VM 'yi aşağıdaki şekilde güncelleştirin:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Yeni boyut mevcut disk boyutundan büyük olmalıdır. İşletim sistemi diskleri için izin verilen en yüksek 2048 GB 'dir. (VHD blobunun boyutunu bu boyuttan daha büyük bir şekilde genişletmek mümkündür, ancak işletim sistemi yalnızca ilk 2048 GB alan ile çalışabilir.)
   > 
   > 
   
6. VM güncelleştirmesi biraz zaman alabilir. Komutun yürütülmesi tamamlandığında aşağıdakileri yaparak VM’yi yeniden başlatın:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>İşletim sistemi diski için betikler

Hem yönetilen hem de yönetilmeyen diskler için başvurağınız için tüm komut dosyası aşağıda verilmiştir:


**Yönetilen diskler**

```Powershell
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

Bu makale, birincil olarak VM 'nin işletim sistemi diskini genişletmeden odaklanır, ancak betik, VM 'ye bağlı veri disklerini genişletmek için de kullanılabilir. Örneğin, VM’ye bağlı ilk veri diskini genişletmek için `OSDisk` öğesinin `StorageProfile` nesnesini `DataDisks` dizisi ile değiştirin ve aşağıda gösterildiği gibi sayısal bir dizin kullanarak ilk bağlanan veri diskinin başvurusunu edinin:

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
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>İşletim sistemi içindeki birimi genişletme

VM 'nin diskini genişlettikten sonra, işletim sistemine gitmeniz ve yeni alanı kapsayacak şekilde birimi genişletmeniz gerekir. Bölüm genişletmek için birkaç yöntem vardır. Bu bölümde, **DiskPart**kullanarak bölümü genişletmek IÇIN bir RDP BAĞLANTıSı kullanarak VM 'nin bağlanması ele alınmaktadır.

1. VM 'nize bir RDP bağlantısı açın.

2.  Bir komut istemi açın ve **DiskPart**yazın.

2.  **DISKPART** isteminde `list volume`yazın. Genişletmek istediğiniz birimi unutmayın.

3.  **DISKPART** isteminde `select volume <volumenumber>`yazın. Bu, aynı diskteki bitişik boş alana genişletmek istediğiniz birim *volumenumber* seçer.

4.  **DISKPART** isteminde `extend [size=<size>]`yazın. Bu, seçilen birimi megabayt (MB) cinsinden *boyuta* genişletir.


## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal](attach-managed-disk-portal.md)kullanarak da disk ekleyebilirsiniz.
