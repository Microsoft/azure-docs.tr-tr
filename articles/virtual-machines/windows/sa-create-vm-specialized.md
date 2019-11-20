---
title: Azure 'da özel bir diskten VM oluşturma
description: Kaynak Yöneticisi dağıtım modelinde özelleştirilmiş bir yönetilmeyen disk ekleyerek yeni bir VM oluşturun.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ROBOTS: NOINDEX
ms.openlocfilehash: d887ef2ef74bb433d6e8ae7f53cd0b77f5948303
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073343"
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Depolama hesabındaki özelleştirilmiş bir VHD 'den VM oluşturma

PowerShell kullanarak özel bir yönetilmeyen diski işletim sistemi diski olarak ekleyerek yeni bir VM oluşturun. Özel bir disk, mevcut bir VM 'den, özgün VM 'nizden Kullanıcı hesaplarını, uygulamaları ve diğer durum verilerini tutan bir kopyasıdır. 

İki seçeneğiniz vardır:
* [Bir VHD’yi karşıya yükleme](sa-create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Mevcut bir Azure VM 'nin VHD 'sini kopyalama](sa-create-vm-specialized.md#option-2-copy-the-vhd-from-an-existing-azure-vm)

 


## <a name="option-1-upload-a-specialized-vhd"></a>Seçenek 1: özelleştirilmiş bir VHD 'YI karşıya yükleme

VHD 'yi Hyper-V gibi şirket içi bir sanallaştırma aracıyla oluşturulan özel bir VM 'den veya başka bir buluttan aktarılmış bir VM 'den karşıya yükleyebilirsiniz.

### <a name="prepare-the-vm"></a>VM’yi hazırlama
Şirket içi bir VM veya başka bir buluttan aktarılmış bir VHD kullanılarak oluşturulmuş özel bir VHD 'YI karşıya yükleyebilirsiniz. Özelleştirilmiş bir VHD, özgün VM 'nizden Kullanıcı hesaplarını, uygulamaları ve diğer durum verilerini korur. Yeni bir VM oluşturmak için VHD 'YI kullanmak istiyorsanız, aşağıdaki adımların tamamlandığından emin olun. 
  
  * [Bir WINDOWS VHD 'Yi Azure 'a yüklemek Için hazırlayın](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Sysprep 'ı kullanarak VM 'yi **genelleştirmeyin** .
  * VM 'de yüklü olan tüm konuk sanallaştırma araçlarını ve aracılarını (örneğin, VMware araçları) kaldırın.
  * VM 'nin IP adresini ve DNS ayarlarını DHCP aracılığıyla çekmek üzere yapılandırıldığından emin olun. Bu, sunucu başlatıldığında sanal ağ içinde bir IP adresi elde edilmesini sağlar. 


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
   
### <a name="upload-the-vhd-to-your-storage-account"></a>VHD 'YI depolama hesabınıza yükleyin
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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>2\. seçenek: VHD 'yi mevcut bir Azure VM 'den kopyalama

Yeni, yinelenen bir VM oluştururken kullanmak üzere bir VHD 'yi başka bir depolama hesabına kopyalayabilirsiniz.

### <a name="before-you-begin"></a>Başlamadan önce
Şunları yaptığınızdan emin olun:

* **Kaynak ve hedef depolama hesapları**hakkında bilgi sahibi. Kaynak VM için, depolama hesabı ve kapsayıcı adlarına sahip olmanız gerekir. Genellikle kapsayıcı adı **VHD**'ler olur. Ayrıca bir hedef depolama hesabınız olması gerekir. Henüz bir hesabınız yoksa, portalı (**tüm hizmetler** > depolama hesapları > Ekle) veya [New-azstorageaccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) cmdlet 'ini kullanarak bir tane oluşturabilirsiniz. 
* [AzCopy aracını](../../storage/common/storage-use-azcopy.md)indirmiş ve yükledi. 

### <a name="deallocate-the-vm"></a>VM 'yi serbest bırakma
Kopyalanacak VHD 'yi serbest bırakma VM 'yi serbest bırakın. 

* **Portal**: **sanal makineler** > **myvm** > Durdur ' a tıklayın
* **PowerShell**: **myresourcegroup**kaynak grubundaki **myvm** adlı VM 'yi durdurmak (serbest bırakmak) için [stop-azvm](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) kullanın.

```powershell
Stop-AzVM -ResourceGroupName myResourceGroup -Name myVM
```

Azure portal sanal makinenin **durumu** durduruldu durumundan **durduruldu (serbest bırakıldı)** **olarak değişir** .

### <a name="get-the-storage-account-urls"></a>Depolama hesabı URL 'Lerini al
Kaynak ve hedef depolama hesaplarının URL 'Lerine ihtiyacınız vardır. URL 'Ler şöyle görünür: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Depolama hesabı ve kapsayıcı adını zaten biliyorsanız, URL 'nizi oluşturmak için köşeli ayraçları arasındaki bilgileri değiştirebilirsiniz. 

URL 'YI almak için Azure portal veya Azure PowerShell kullanabilirsiniz:

* **Portal**: **tüm hizmetler** > **depolama hesapları** > *depolama hesabı* > **Blobları** ve kaynak VHD dosyanız büyük olasılıkla **VHD** kapsayıcısında **>** tıklayın. Kapsayıcının **Özellikler** ' e tıklayın ve **URL**etiketli metni kopyalayın. Kaynak ve hedef kapsayıcıların URL 'Lerine ihtiyacınız olacaktır. 
* **PowerShell**: **myresourcegroup**kaynak GRUBUNDAKI **myvm** adlı VM 'Nin bilgilerini almak için [Get-azvm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) ' i kullanın. Sonuçlarda, **VHD URI 'si**için **depolama profili** bölümüne bakın. URI 'nin ilk bölümü kapsayıcının URL 'sidir ve son parça VM 'nin işletim sistemi VHD adıdır.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Depolama erişim anahtarlarını al
Kaynak ve hedef depolama hesapları için erişim anahtarlarını bulun. Erişim anahtarları hakkında daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../../storage/common/storage-create-storage-account.md).

* **Portal**: **tüm hizmetler** > depolama **hesapları** > *depolama hesabı* > **erişim anahtarları**' na tıklayın. **KEY1**olarak etiketlenen anahtarı kopyalayın.
* **PowerShell**: **myresourcegroup**kaynak grubundaki depolama hesabı **Mystorageaccount** depolama anahtarını almak Için [Get-azstorageaccountkey](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) komutunu kullanın. **KEY1**etiketli anahtarı kopyalayın.

```powershell
Get-AzStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>VHD 'YI kopyalama
AzCopy kullanarak, depolama hesapları arasında dosya kopyalayabilirsiniz. Hedef kapsayıcı için, belirtilen kapsayıcı yoksa, sizin için oluşturulur. 

AzCopy kullanmak için yerel makinenizde bir komut istemi açın ve AzCopy 'in yüklü olduğu klasöre gidin. Bu, *C:\Program Files (x86) \Microsoft SDKs\Azure\AzCopy*ile benzerdir. 

Bir kapsayıcı içindeki tüm dosyaları kopyalamak için **/s** anahtarını kullanın. Bu, işletim sistemi VHD 'sini ve aynı kapsayıcıda olmaları durumunda tüm veri disklerini kopyalamak için kullanılabilir. Bu örnek, **mysourcestorampaaccount** depolama hesabındaki **mysourcecontainer** kapsayıcısındaki tüm dosyaların **mydestinationstorageaccount** depolama hesabındaki Container **mydestinationcontainer** öğesine nasıl kopyalanacağını gösterir . Depolama hesaplarının ve kapsayıcıların adlarını kendi ile değiştirin. `<sourceStorageAccountKey1>` ve `<destinationStorageAccountKey1>` kendi anahtarlarınız ile değiştirin.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Yalnızca birden çok dosya içeren bir kapsayıcıda belirli bir VHD 'yi kopyalamak istiyorsanız,/model anahtarını kullanarak da dosya adını belirtebilirsiniz. Bu örnekte, yalnızca **Myfilename. vhd** adlı dosya kopyalanacaktır.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


İşlem tamamlandığında, şöyle bir ileti alırsınız:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Sorun giderme
* AZCopy kullandığınızda, "sunucu istek kimlik doğrulaması başarısız oldu" hatasını görürseniz, yetkilendirme üst bilgisinin değerinin imza dahil doğru biçimlendirildiğinden emin olun. Anahtar 2 veya ikincil depolama anahtarı kullanıyorsanız, birincil veya 1. depolama anahtarını kullanmayı deneyin.

## <a name="create-the-new-vm"></a>Yeni VM oluşturma 

Yeni VM tarafından kullanılmak üzere ağ ve diğer VM kaynakları oluşturmanız gerekir.

### <a name="create-the-subnet-and-vnet"></a>Alt ağ ve vNet oluşturma

[Sanal ağın](../../virtual-network/virtual-networks-overview.md)VNET ve alt ağını oluşturun.

1. Alt ağı oluşturun. Bu örnek **Myresourcegroup**kaynak grubu Içinde **mysubnet**adlı bir alt ağ oluşturur ve alt ağ adres önekini **10.0.0.0/24**olarak ayarlar.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. VNet oluşturun. Bu örnek, sanal ağ adını **Myvağadı**, **Batı ABD**konumu ve sanal ağın adres önekini **10.0.0.0/16**olarak ayarlar. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
   ### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Ağ güvenlik grubu ve RDP kuralı oluşturma
   RDP kullanarak sanal makinenizde oturum açabiliyor olması için 3389 numaralı bağlantı noktasında RDP erişimine izin veren bir güvenlik kuralına sahip olmanız gerekir. Yeni VM için VHD mevcut bir özelleştirilmiş VM 'den oluşturulduğundan, VM oluşturulduktan sonra, RDP kullanarak oturum açma izni olan kaynak sanal makineden mevcut bir hesabı kullanabilirsiniz.
   Bu işlem, ile ilişkilendirilecek ağ arabirimi oluşturulmadan önce tamamlanmalıdır.  
   Bu örnek NSG adını **Mynsg** olarak ve RDP kuralı adını **Myrdprule**olarak ayarlar.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Uç noktalar ve NSG kuralları hakkında daha fazla bilgi için bkz. [PowerShell kullanarak Azure 'DA VM 'ye bağlantı noktalarını açma](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Genel IP adresi ve NIC oluşturma
Sanal makinenin sanal ağda iletişimini etkinleştirmeniz için, [genel IP adresi](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) ve ağ arabirimi gereklidir.

1. Genel IP 'yi oluşturun. Bu örnekte, genel IP adresi adı **MYIP**olarak ayarlanır.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. NIC 'yi oluşturun. Bu örnekte, NIC adı **Mynicname**olarak ayarlanır. Bu adım Ayrıca, daha önce oluşturulan ağ güvenlik grubunu bu NIC ile ilişkilendirir.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
    ```

### <a name="set-the-vm-name-and-size"></a>VM adı ve boyutunu ayarla

Bu örnek, VM adını "myVM" ve VM boyutu olarak "Standard_A2" olarak ayarlar.
```powershell
$vmName = "myVM"
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>NIC 'yi ekleme
    
```powershell
$vm = Add-AzVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>İşletim sistemi diskini yapılandırma

1. Karşıya yüklediğiniz veya kopyaladığınız VHD için URI 'yi ayarlayın. Bu örnekte, **Myosdisk. vhd** adlı VHD dosyası **myContainer**adlı kapsayıcıda **mystorageaccount** adlı bir depolama hesabında tutulur.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. İşletim sistemi diskini ekleyin. Bu örnekte, işletim sistemi diski oluşturulduğunda "osDisk" terimi, işletim sistemi diski adını oluşturmak için VM adına eklenir. Bu örnek ayrıca, Windows tabanlı VHD 'nin VM 'ye işletim sistemi diski olarak eklenmesi gerektiğini belirtir.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

İsteğe bağlı: VM 'ye bağlanması gereken veri disklerinizi varsa veri VHD 'Leri ve uygun mantıksal birim numarası (LUN) kullanarak veri disklerini ekleyin.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Bir depolama hesabı kullanırken, veri ve işletim sistemi disk URL 'Leri şuna benzer: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Bunu portalda, hedef depolama kapsayıcısına giderek, kopyalanan işletim sistemine veya veri VHD 'sine tıklayarak ve ardından URL 'nin içeriğini kopyalayarak bulabilirsiniz.


### <a name="complete-the-vm"></a>VM 'yi doldurun 

Yeni oluşturduğumuz konfigürasyonları kullanarak VM 'yi oluşturun.

```powershell
#Create the new VM
New-AzVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Bu komut başarılı olduysa, şunun gibi bir çıktı görürsünüz:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>VM 'nin oluşturulduğunu doğrulama
Yeni oluşturulan VM 'yi [Azure Portal](https://portal.azure.com), **tüm hizmetler** > **sanal makineler**altında ya da aşağıdaki PowerShell komutlarını kullanarak görmeniz gerekir:

```powershell
$vmList = Get-AzVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Sonraki adımlar
Yeni sanal makinenizde oturum açın. Daha fazla bilgi için bkz. [Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum](connect-logon.md)açma.

