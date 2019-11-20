---
title: Azure 'da birden çok VM oluşturmak için bir generalize VHD yükleyin
description: Kaynak Yöneticisi dağıtım modeliyle kullanmak üzere bir Windows sanal makinesi oluşturmak için bir Azure depolama hesabına genelleştirilmiş bir VHD yükleyin.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/18/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: 933b648f15418c4838d3da1ea8379267765c784b
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073339"
---
# <a name="upload-a-generalized-vhd-to-azure-to-create-a-new-vm"></a>Yeni bir VM oluşturmak için genelleştirilmiş bir VHD 'yi Azure 'a yükleyin

Bu konu, bir depolama hesabına genelleştirilmiş bir yönetilmeyen disk yüklemeyi ve ardından karşıya yüklenen diski kullanarak yeni bir VM oluşturmayı içerir. Genelleştirilmiş bir VHD görüntüsünde tüm kişisel hesap bilgileriniz Sysprep kullanılarak kaldırılmıştır. 

Bir depolama hesabındaki özelleştirilmiş bir VHD 'den VM oluşturmak istiyorsanız bkz. [özelleştirilmiş BIR VHD 'den VM oluşturma](sa-create-vm-specialized.md).

Bu konuda, depolama hesaplarının kullanımı ele alınmaktadır, ancak müşteriler bunun yerine yönetilen diskleri kullanmaya geçiş yapmanızı öneririz. Yönetilen diskleri kullanarak yeni bir VM hazırlama, karşıya yükleme ve oluşturma hakkında tam bir adım adım için bkz. [yönetilen diskler kullanılarak Azure 'a yüklenen Genelleştirilmiş BIR VHD 'den yeni bir sanal makine oluşturma](upload-generalized-managed.md).

 

## <a name="prepare-the-vm"></a>VM’yi hazırlama

Genelleştirilmiş bir VHD, tüm kişisel hesap bilgilerinizin Sysprep kullanılarak kaldırıldığını içeriyordu. VHD 'yi ' den yeni VM 'Ler oluşturmak için bir görüntü olarak kullanmayı düşünüyorsanız şunları yapmalısınız:
  
  * [Bir WINDOWS VHD 'Yi Azure 'a yüklemek Için hazırlayın](prepare-for-upload-vhd-image.md). 
  * Sysprep kullanarak sanal makineyi genelleştirin

### <a name="generalize-a-windows-virtual-machine-using-sysprep"></a>Sysprep kullanarak bir Windows sanal makinesini genelleştirin
Bu bölümde, Windows sanal makinenizi görüntü olarak kullanılmak üzere genelleştirirsiniz. Sysprep diğer öğelerin yanı sıra tüm kişisel hesap bilgilerinizi kaldırır ve makineyi bir görüntü olarak kullanılacak şekilde hazırlar. Sysprep hakkındaki ayrıntılar için bkz. [Sysprep İşlemini Kullanma: Giriş](https://technet.microsoft.com/library/bb457073.aspx).

Makinede çalışan sunucu rollerinin Sysprep tarafından desteklendiğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> VHD 'nizi Azure 'a ilk kez yüklemeden önce Sysprep çalıştırıyorsanız, Sysprep 'ı çalıştırmadan önce [VM 'nizi hazırladığınızdan](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) emin olun. 
> 
> 

1. Windows sanal makinesinde oturum açın.
2. Yönetici olarak Komut İstemi penceresini açın. Dizini **%windir%\system32\sysprep**olarak değiştirip `sysprep.exe`çalıştırın.
3. **Sistem Hazırlama Aracı** iletişim kutusunda  **Sistem İlk Çalıştırma Deneyimi (OOBE) Moduna Gir**'i seçin ve **Genelleştir** onay kutusunun seçili olduğundan emin olun.
4. **Kapalı seçenekleri**' nde, **kapatır**' ı seçin.
5. **OK (Tamam)** düğmesine tıklayın.
   
    ![Sysprep 'ı Başlat](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep tamamlandığında, sanal makineyi kapatır. 

> [!IMPORTANT]
> VHD 'yi Azure 'a yüklemeyi veya VM 'den bir görüntü oluşturmayı tamamlayana kadar VM 'yi yeniden başlatmayın. VM yanlışlıkla yeniden başlatılırsa, yeniden genelleştirmek için Sysprep komutunu çalıştırın.
> 
> 


## <a name="upload-the-vhd"></a>VHD 'YI karşıya yükleme

VHD 'YI bir Azure depolama hesabına yükleyin.

### <a name="log-in-to-azure"></a>Azure'da oturum açma
PowerShell sürüm 1,4 veya sonraki bir sürümü yüklü değilse [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/overview)makalesini okuyun.

1. Azure PowerShell açın ve Azure hesabınızda oturum açın. Azure hesabı kimlik bilgilerinizi girmeniz için bir açılır pencere açılır.
   
    ```powershell
    Connect-AzAccount
    ```
2. Kullanılabilir abonelikleriniz için abonelik kimliklerini alın.
   
    ```powershell
    Get-AzSubscription
    ```
3. Abonelik KIMLIĞINI kullanarak doğru aboneliği ayarlayın. `<subscriptionID>`, doğru aboneliğin KIMLIĞIYLE değiştirin.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

### <a name="get-the-storage-account"></a>Depolama hesabını al
Karşıya yüklenen VM görüntüsünü depolamak için Azure 'da bir depolama hesabı gerekir. Var olan bir depolama hesabı kullanabilir veya yeni bir tane oluşturabilirsiniz. 

Kullanılabilir depolama hesaplarını göstermek için şunu yazın:

```powershell
Get-AzStorageAccount
```

Mevcut bir depolama hesabını kullanmak istiyorsanız, VM görüntüsünü karşıya yükleme bölümüne ilerleyin.

Bir depolama hesabı oluşturmanız gerekiyorsa, şu adımları izleyin:

1. Depolama hesabının oluşturulması gereken kaynak grubunun adının olması gerekir. Aboneliğinizdeki tüm kaynak gruplarını bulmak için şunu yazın:
   
    ```powershell
    Get-AzResourceGroup
    ```

    **Batı ABD** bölgesinde **myresourcegroup** adlı bir kaynak grubu oluşturmak için şunu yazın:

    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet 'ini kullanarak bu kaynak grubunda **mystorageaccount** adlı bir depolama hesabı oluşturun:
   
    ```powershell
    New-AzStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
 
### <a name="start-the-upload"></a>Karşıya yüklemeyi Başlat 

Görüntüyü Depolama hesabınızdaki bir kapsayıcıya yüklemek için [Add-AzVhd](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd) cmdlet 'ini kullanın. Bu örnekte, **Myvhd. vhd** dosyasını, **myresourcegroup** kaynak grubundaki **mystorageaccount** adlı bir depolama hesabına `"C:\Users\Public\Documents\Virtual hard disks\"` yükler. Dosya **myContainer** adlı kapsayıcıya yerleştirilecek ve yeni dosya adı **Myuploadedvhd. vhd**olacaktır.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Başarılı olursa şuna benzer bir yanıt alırsınız:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Ağ bağlantınıza ve VHD dosyanızın boyutuna bağlı olarak, bu komutun tamamlanması biraz zaman alabilir.


## <a name="create-a-new-vm"></a>Yeni bir VM oluşturun 

Artık yeni bir VM oluşturmak için karşıya yüklenen VHD 'YI kullanabilirsiniz. 

### <a name="set-the-uri-of-the-vhd"></a>VHD URI 'sini ayarlama

Kullanılacak VHD için URI şu biçimdedir: https://**mystorageaccount**. blob.Core.Windows.net/**myContainer**/**myvhdname**. vhd. Bu örnekte, **myvhd** adlı VHD, kapsayıcıda Container **mystorageaccount** depolama **hesabıdır.**

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma
[Sanal ağın](../../virtual-network/virtual-networks-overview.md)vNet ve alt ağını oluşturun.

1. Alt ağı oluşturun. Aşağıdaki örnek, **Myresourcegroup** kaynak grubunda **10.0.0.0/24**adres ön eki ile **mysubnet** adlı bir alt ağ oluşturur.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Sanal ağı oluşturun. Aşağıdaki örnek, **10.0.0.0/16**adres ön ekine sahip **Batı ABD** konumunda **myvnet** adlı bir sanal ağ oluşturur.  
   
    ```powershell
    $location = "WestUS"
    $vnetName = "myVnet"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Genel IP adresi ve ağ arabirimi oluşturma
Sanal makinenin sanal ağda iletişimini etkinleştirmeniz için, [genel IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) ve ağ arabirimi gereklidir.

1. Genel bir IP adresi oluşturun. Bu örnek **Mypıp**adlı BIR genel IP adresi oluşturur. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 'yi oluşturun. Bu örnek, **MYNIC**ADLı bir NIC oluşturur. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Ağ güvenlik grubu ve RDP kuralı oluşturma
RDP kullanarak sanal makinenizde oturum açabiliyor olması için 3389 numaralı bağlantı noktasında RDP erişimine izin veren bir güvenlik kuralına sahip olmanız gerekir. 

Bu örnekte, 3389 numaralı bağlantı noktası üzerinden RDP trafiğine izin veren **Myrdprule** adlı bir kural Içeren **mynsg** adlı bir NSG oluşturulur. NSG 'ler hakkında daha fazla bilgi için bkz. [PowerShell kullanarak Azure 'DA VM 'ye bağlantı noktaları açma](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Sanal ağ için bir değişken oluşturun
Tamamlanan sanal ağ için bir değişken oluşturun. 

```powershell
$vnet = Get-AzVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

### <a name="create-the-vm"></a>Sanal makine oluşturma
Aşağıdaki PowerShell betiği, sanal makine yapılandırmalarının nasıl ayarlanacağını ve yeni yüklemenin kaynağı olarak karşıya yüklenen VM görüntüsünü nasıl kullanacağınızı gösterir.



```powershell
# Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>VM 'nin oluşturulduğunu doğrulama
Tamamlandığında, yeni oluşturulan VM 'yi, > **sanal makinelere** **gözatarak** [Azure Portal](https://portal.azure.com) veya aşağıdaki PowerShell komutlarını kullanarak görmeniz gerekir:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Sonraki adımlar
Yeni sanal makinenizi Azure PowerShell yönetmek için bkz. [Azure Resource Manager ve PowerShell kullanarak sanal makineleri yönetme](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


