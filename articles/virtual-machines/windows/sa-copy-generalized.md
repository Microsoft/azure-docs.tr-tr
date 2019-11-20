---
title: Azure 'da genelleştirilmiş bir VM 'nin yönetilmeyen görüntüsünü oluşturma
description: Azure 'da bir VM 'nin birden çok kopyasını oluşturmak için kullanılan genelleştirilmiş bir Windows sanal makinesinin yönetilmeyen bir görüntüsünü oluşturun.
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
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: f25968fb74f0f10b1d498866c036dd04d4d5d134
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073387"
---
# <a name="how-to-create-an-unmanaged-vm-image-from-an-azure-vm"></a>Azure VM 'den yönetilmeyen VM görüntüsü oluşturma

Bu makalede, depolama hesaplarının kullanımı ele alınmaktadır. Depolama hesabı yerine yönetilen diskleri ve yönetilen görüntüleri kullanmanızı öneririz. Daha fazla bilgi için bkz. [Azure 'da Genelleştirilmiş BIR VM 'nin yönetilen görüntüsünü yakalama](capture-image-resource.md).

Bu makalede, bir depolama hesabı kullanarak genelleştirilmiş bir Azure VM 'nin görüntüsünü oluşturmak için Azure PowerShell nasıl kullanılacağı gösterilmektedir. Daha sonra başka bir VM oluşturmak için görüntüsünü kullanabilirsiniz. Görüntü, işletim sistemi diskini ve sanal makineye bağlı veri disklerini içerir. Görüntüde sanal ağ kaynakları dahil değildir, bu nedenle yeni VM 'yi oluştururken bu kaynakları ayarlamanız gerekir. 

 

## <a name="generalize-the-vm"></a>VM 'yi Genelleştirme 
Bu bölümde, Windows sanal makinenizi görüntü olarak kullanılmak üzere genelleştirirsiniz. Bir VM 'nin genelleştirilmesi, tüm kişisel hesap bilgilerinizi diğer şeyler arasında kaldırır ve makineyi bir görüntü olarak kullanılmak üzere hazırlar. Sysprep hakkındaki ayrıntılar için bkz. [Sysprep İşlemini Kullanma: Giriş](https://technet.microsoft.com/library/bb457073.aspx).

Makinede çalışan sunucu rollerinin Sysprep tarafından desteklendiğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> VHD 'nizi ilk kez Azure 'a yüklüyorsanız, Sysprep 'ı çalıştırmadan önce [VM 'nizi hazırladığınızdan](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) emin olun. 
> 
> 

Ayrıca, `sudo waagent -deprovision+user` kullanarak bir Linux sanal makinesini genelleştirebilirsiniz ve ardından VM 'yi yakalamak için PowerShell 'i kullanabilirsiniz. Bir VM yakalamak için CLı kullanma hakkında daha fazla bilgi için bkz. [Azure CLI kullanarak Linux sanal makinesi Genelleştirme ve yakalama](../linux/capture-image.md).


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

## <a name="log-in-to-azure-powershell"></a>Azure PowerShell'de oturum açma
1. Azure PowerShell açın ve Azure hesabınızda oturum açın.
   
    ```powershell
    Connect-AzAccount
    ```
   
    Azure hesabı kimlik bilgilerinizi girmeniz için bir açılır pencere açılır.
2. Kullanılabilir abonelikleriniz için abonelik kimliklerini alın.
   
    ```powershell
    Get-AzSubscription
    ```
3. Abonelik KIMLIĞINI kullanarak doğru aboneliği ayarlayın.
   
    ```powershell
    Select-AzSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>VM 'yi serbest bırakma ve durumu Genelleştirilmiş olarak ayarlama

> [!IMPORTANT] 
> Genelleştirilmiş olarak işaretlendikten sonra bir VM 'ye etiket ekleyemez, düzenleyemez veya kaldıramazsınız. VM 'ye bir etiket eklemek istiyorsanız, etiketleri Genelleştirilmiş olarak işaretlemeden önce eklediğinizden emin olun.
> 

1. VM kaynaklarını serbest bırakın.
   
    ```powershell
    Stop-AzVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    Azure portal sanal makinenin *durumu* durduruldu durumundan **durduruldu (serbest bırakıldı)** **olarak değişir** .
2. Sanal makinenin durumunu **Genelleştirilmiş**olarak ayarlayın. 
   
    ```powershell
    Set-AzVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. VM 'nin durumunu denetleyin. VM 'nin **Osstate/Genelleştirilmiş** bölümünde **displaystatus** , **VM Genelleştirilmiş**olarak ayarlanmalıdır.  
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Görüntü oluşturma

Bu komutu kullanarak hedef depolama kapsayıcısında bir yönetilmeyen sanal makine görüntüsü oluşturun. Görüntü, özgün sanal makine ile aynı depolama hesabında oluşturulur. `-Path` parametresi, kaynak VM için JSON şablonunun bir kopyasını yerel bilgisayarınıza kaydeder. `-DestinationContainerName` parametresi, görüntülerinizi tutmak istediğiniz kapsayıcının adıdır. Kapsayıcı yoksa, sizin için oluşturulur.
   
```powershell
Save-AzVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
    -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
    -Path <C:\local\Filepath\Filename.json>
```
   
Resminizin URL 'sini JSON dosya şablonundan alabilirsiniz. Görüntünüzün tüm yolu için **Storageprofile** > **osdisk** > **Image** > **Uri** bölümünü > **kaynaklara** gidin. Görüntünün URL 'SI şöyle görünür: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
Ayrıca, portalda URI 'yi doğrulayabilirsiniz. Görüntü, depolama hesabınızda **sistem** adlı bir kapsayıcıya kopyalanır. 

## <a name="create-a-vm-from-the-image"></a>Görüntüden bir VM oluşturun

Artık yönetilmeyen görüntüden bir veya daha fazla sanal makine oluşturabilirsiniz.

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
    $location = "West US"
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
Aşağıdaki PowerShell sanal makine yapılandırmasını tamamlar ve yeni yüklemenin kaynağı olarak yönetilmeyen görüntü kullanır.

</br>

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

### <a name="verify-that-the-vm-was-created"></a>VM 'nin oluşturulduğunu doğrulama
Tamamlandığında, yeni oluşturulan VM 'yi, > **sanal makinelere** **gözatarak** [Azure Portal](https://portal.azure.com) veya aşağıdaki PowerShell komutlarını kullanarak görmeniz gerekir:

```powershell
    $vmList = Get-AzVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Sonraki adımlar
Yeni sanal makinenizi Azure PowerShell yönetmek için bkz. [Azure Resource Manager ve PowerShell kullanarak sanal makineleri yönetme](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


