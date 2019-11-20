---
title: Azure 'da yönetilen bir görüntü oluşturma
description: Azure 'da genelleştirilmiş bir VM veya VHD 'nin yönetilen bir görüntüsünü oluşturun. Görüntüler, yönetilen diskler kullanan birden çok VM oluşturmak için kullanılabilir.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 5557028304d0e2bd5940dd9b01dddf525806d0c6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033670"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Azure 'da genelleştirilmiş bir VM 'nin yönetilen bir görüntüsünü oluşturma

Yönetilen bir görüntü kaynağı, yönetilen bir disk veya depolama hesabında yönetilmeyen bir disk olarak depolanan genelleştirilmiş bir sanal makineden (VM) oluşturulabilir. Görüntü daha sonra birden çok VM oluşturmak için kullanılabilir. Yönetilen görüntülerin faturalandırılması hakkında daha fazla bilgi için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/). 

 

## <a name="generalize-the-windows-vm-using-sysprep"></a>Sysprep kullanarak Windows VM'sini genelleştirme

Sysprep tüm kişisel hesabınızı ve güvenlik bilgilerini kaldırır ve ardından makineyi bir görüntü olarak kullanılacak şekilde hazırlar. Sysprep hakkında daha fazla bilgi için bkz. [Sysprep genel bakış](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Makinede çalışan sunucu rollerinin Sysprep tarafından desteklendiğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) ve [desteklenmeyen senaryolar](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios).

> [!IMPORTANT]
> Bir VM 'de Sysprep 'ı çalıştırdıktan sonra, bu VM *Genelleştirilmiş* olarak değerlendirilir ve yeniden başlatılamaz. VM’yi genelleştirme işlemi geri döndürülemez. Özgün VM 'yi çalışır durumda tutmanız gerekiyorsa, [sanal makinenin bir kopyasını](create-vm-specialized.md#option-3-copy-an-existing-azure-vm) oluşturmanız ve kopyasını genelleştirmelisiniz. 
>
> Sanal sabit diskinizi (VHD) Azure 'a ilk kez yüklemeden önce Sysprep 'i çalıştırmayı planlıyorsanız, [VM 'nizi hazırladığınızdan](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)emin olun.  
> 
> 

Windows VM 'nizi genelleştirmek için aşağıdaki adımları izleyin:

1. Windows sanal makinenizde oturum açın.
   
2. Yönetici olarak bir komut Istemi penceresi açın. Dizini%windir%\system32\sysprep olarak değiştirip `sysprep.exe`çalıştırın.
   
3. **Sistem Hazırlama Aracı** iletişim kutusunda, **sistem kutudan çıkar deneyimini (OOBE) girin** ve **generalize** onay kutusunu seçin.
   
4. **Kapalı seçenekleri**Için, **kapanıyor**' ı seçin.
   
5. **Tamam**’ı seçin.
   
    ![Sysprep 'ı Başlat](./media/upload-generalized-managed/sysprepgeneral.png)

6. Sysprep tamamlandığında, sanal makineyi kapatır. VM 'yi yeniden başlatmayın.

> [!TIP]
> **Isteğe bağlı** Görüntünüzü iyileştirmek ve sanal makinenizin ilk önyükleme süresini azaltmak için [DISM 'yi](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options) kullanın.
>
> Görüntünüzü iyileştirmek için, Windows Gezgini 'nde çift tıklayarak VHD 'nizi bağlayın ve ardından `/optimize-image` parametresiyle DıSM 'yi çalıştırın.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Burada D: bağlı VHD 'nin yoludur.
>
> `DISM /optimize-image` çalıştırmak, VHD 'niz üzerinde yaptığınız son değişiklik olmalıdır. Dağıtımdan önce VHD 'niz üzerinde herhangi bir değişiklik yaparsanız, `DISM /optimize-image` yeniden çalıştırmanız gerekir.

## <a name="create-a-managed-image-in-the-portal"></a>Portalda yönetilen bir görüntü oluşturma 

1. [Azure portalı](https://portal.azure.com) açın.

2. Soldaki menüden **sanal makineler** ' i seçin ve ardından listeden VM 'yi seçin.

3. VM 'nin **sanal makine** sayfasında, üstteki menüden **yakala**' yı seçin.

   **Görüntü oluştur** sayfası görüntülenir.

4. **Ad**için önceden doldurulmuş adı kabul edin ya da görüntü için kullanmak istediğiniz bir ad girin.

5. **Kaynak grubu**için, **Yeni oluştur** ' u seçin ve bir ad girin veya **var olanı kullan** ' ı seçin ve açılan listeden kullanılacak bir kaynak grubu seçin.

6. Görüntü oluşturulduktan sonra kaynak VM 'yi silmek istiyorsanız, **görüntüyü oluşturduktan sonra bu sanal makineyi otomatik olarak sil**' i seçin.

7. Görüntüyü herhangi bir [kullanılabilirlik](../../availability-zones/az-overview.md)alanında kullanmak istiyorsanız **bölge dayanıklılığı**için **Açık** ' ı seçin.

8. Görüntüyü oluşturmak için **Oluştur** ' u seçin.

9. Görüntü oluşturulduktan sonra, kaynak grubundaki kaynaklar listesinde **görüntü** kaynağı olarak bulabilirsiniz.



## <a name="create-an-image-of-a-vm-using-powershell"></a>PowerShell kullanarak VM 'nin bir görüntüsünü oluşturma

 

Doğrudan VM 'den bir görüntü oluşturmak, görüntünün işletim sistemi diski ve veri diskleri dahil olmak üzere VM ile ilişkili tüm diskleri de kapsamasını sağlar. Bu örnek, yönetilen diskleri kullanan bir VM 'den yönetilen bir görüntünün nasıl oluşturulacağını gösterir.

Başlamadan önce, Azure PowerShell modülünün en son sürümüne sahip olduğunuzdan emin olun. Sürümü bulmak için PowerShell 'de `Get-Module -ListAvailable Az` çalıştırın. Yükseltmeniz gerekiyorsa, bkz. [PowerShellGet Ile Windows üzerinde Azure PowerShell yüklemesi](/powershell/azure/install-az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız Azure ile bağlantı oluşturmak için `Connect-AzAccount` çalıştırın.


> [!NOTE]
> Görüntünüzü bölgesel olarak yedekli depolamada depolamak isterseniz, bunu [kullanılabilirlik bölgelerini](../../availability-zones/az-overview.md) destekleyen bir bölgede oluşturmanız ve görüntü yapılandırmasına `-ZoneResilient` parametresini eklemeniz gerekir (`New-AzImageConfig` komutu).

Bir VM görüntüsü oluşturmak için aşağıdaki adımları izleyin:

1. Bazı değişkenler oluşturun.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. VM 'nin serbest bırakılmış olduğundan emin olun.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Sanal makinenin durumunu **Genelleştirilmiş**olarak ayarlayın. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Sanal makineyi alın. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Görüntü yapılandırması oluşturun.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Görüntü oluşturun.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>PowerShell kullanarak yönetilen diskten görüntü oluşturma

Yalnızca işletim sistemi diskinin bir görüntüsünü oluşturmak istiyorsanız, işletim sistemi diski olarak yönetilen disk KIMLIĞI ' ni belirtin:

    
1. Bazı değişkenler oluşturun. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. VM 'yi alın.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Yönetilen diskin KIMLIĞINI alın.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Görüntü yapılandırması oluşturun.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Görüntü oluşturun.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>PowerShell kullanarak anlık görüntüden görüntü oluşturma

Aşağıdaki adımları izleyerek genelleştirilmiş bir VM 'nin anlık görüntüsünden yönetilen bir görüntü oluşturabilirsiniz:

    
1. Bazı değişkenler oluşturun. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Anlık görüntüyü alın.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Görüntü yapılandırması oluşturun.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Görüntü oluşturun.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Depolama hesabı kullanan bir VM 'den görüntü oluşturma

Yönetilen diskler kullanmayan bir VM 'den yönetilen bir görüntü oluşturmak için, depolama hesabındaki işletim sistemi VHD 'sinin URI 'si şu biçimde olmalıdır: https://*mystorageaccount*. blob.Core.Windows.net/*vhdcontainer*/*vhdfilename. vhd* . Bu örnekte, VHD, *vhdcontainer*adlı bir kapsayıcıda *mystorageaccount*içinde ve VHD dosya adı, *vhdfilename. vhd*' dir.


1.  Bazı değişkenler oluşturun.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. VM 'yi durdurun/serbest bırakın.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. VM 'yi Genelleştirilmiş olarak işaretleyin.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Genelleştirilmiş işletim sistemi VHD 'nizi kullanarak görüntü oluşturun.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Sonraki adımlar
- [Yönetilen görüntüden BIR VM oluşturun](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    

