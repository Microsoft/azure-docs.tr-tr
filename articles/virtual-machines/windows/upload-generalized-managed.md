---
title: Karşıya yüklenen genelleştirilmiş bir VHD 'den VM oluşturma
description: Genelleştirilmiş bir VHD 'yi Azure 'a yükleyin ve Kaynak Yöneticisi dağıtım modelinde yeni VM 'Ler oluşturmak için kullanın.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/12/2019
ms.author: cynthn
ms.openlocfilehash: 793bc5518664761a2a9b0cfd46e616d2fb72c3e6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562104"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Genelleştirilmiş bir VHD'yi karşıya yükleme ve Azure’da yeni VM’ler oluştururken kullanma

Bu makalede, PowerShell kullanarak genelleştirilmiş bir sanal makinenin VHD 'sini Azure 'a yükleme, VHD 'den bir görüntü oluşturma ve bu görüntüden yeni bir VM oluşturma işlemleri gösterilmektedir. Şirket içi bir sanallaştırma aracından veya başka bir buluttan aktarılmış bir VHD 'yi karşıya yükleyebilirsiniz. Yeni VM için [yönetilen disklerin](../managed-disks-overview.md) kullanılması VM yönetimini BASITLEŞTIRIR ve VM bir kullanılabilirlik kümesine yerleştirildiğinde daha iyi kullanılabilirlik sağlar. 

Örnek betik için bkz. [Azure 'a BIR VHD yüklemek ve yeni BIR VM oluşturmak Için örnek betik](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script).

## <a name="before-you-begin"></a>Başlamadan önce

- Herhangi bir VHD 'yi Azure 'a yüklemeden önce, [Azure 'a yüklemek için bir WINDOWS VHD veya vhdx hazırlamanızı](prepare-for-upload-vhd-image.md)izlemelisiniz.
- [Yönetilen disklere](../managed-disks-overview.md)geçişinizi başlatmadan önce [yönetilen disklere geçiş planını](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) gözden geçirin.

 
## <a name="generalize-the-source-vm-by-using-sysprep"></a>Sysprep kullanarak kaynak VM 'yi Genelleştirme

Henüz yapmadıysanız, VHD 'yi Azure 'a yüklemeden önce VM 'yi Sysprep yapmanız gerekir. Sysprep diğer öğelerin yanı sıra tüm kişisel hesap bilgilerinizi kaldırır ve makineyi bir görüntü olarak kullanılacak şekilde hazırlar. Sysprep hakkında daha fazla bilgi için bkz. [Sysprep genel bakış](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Makinede çalışan sunucu rollerinin Sysprep tarafından desteklendiğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> VHD 'nizi Azure 'a ilk kez yüklemeden önce Sysprep 'i çalıştırmayı planlıyorsanız, [VM 'nizi hazırladığınızdan](prepare-for-upload-vhd-image.md)emin olun. 
> 
> 

1. Windows sanal makinesinde oturum açın.
1. Yönetici olarak Komut İstemi penceresini açın. 
1. Panther dizinini (C:\Windows\Panther) silin.
1. Dizini%windir%\system32\sysprep olarak değiştirip komutunu çalıştırın `sysprep.exe` .
1. **Sistem Hazırlama Aracı** iletişim kutusunda, **sistem kutudan çıkar deneyimi (OOBE)** seçeneğini belirleyin ve **Genelleştir** onay kutusunun etkinleştirildiğinden emin olun.
1. **Kapalı seçenekleri** Için, **kapanıyor**' ı seçin.
1. **Tamam**’ı seçin.
   
    ![Sysprep 'ı Başlat](./media/upload-generalized-managed/sysprepgeneral.png)
1. Sysprep tamamlandığında, sanal makineyi kapatır. VM'yi yeniden başlatmayın.


## <a name="upload-the-vhd"></a>VHD 'YI karşıya yükleme 

Artık bir VHD 'YI bir yönetilen diske doğrudan yükleyebilirsiniz. Yönergeler için bkz. [Azure PowerShell kullanarak BIR VHD 'Yi Azure 'A yükleme](disks-upload-vhd-to-managed-disk-powershell.md).



VHD yönetilen diske yüklendikten sonra, yönetilen diski almak için [Get-AzDisk](/powershell/module/az.compute/get-azdisk) ' i kullanmanız gerekir.

```azurepowershell-interactive
$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="create-the-image"></a>Görüntü oluşturma
Genelleştirilmiş işletim sistemi tarafından yönetilen diskinizden yönetilen bir görüntü oluşturun. Aşağıdaki değerleri kendi bilgileriniz ile değiştirin.

İlk olarak, bazı değişkenleri ayarlayın:

```powershell
$location = 'East US'
$imageName = 'myImage'
$rgName = 'myResourceGroup'
```

Yönetilen diskinizi kullanarak görüntü oluşturun.

```azurepowershell-interactive
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsState Generalized `
   -OsType Windows `
   -ManagedDiskId $disk.Id
```

Görüntü oluşturun.

```azurepowershell-interactive
$image = New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```

## <a name="create-the-vm"></a>Sanal makineyi oluşturma

Artık bir görüntünüz olduğuna göre, görüntüden bir veya daha fazla yeni VM oluşturabilirsiniz. Bu örnek *Myresourcegroup* Içindeki *myvm* adlı bir VM oluşturur.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Image $image.Id `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Sonraki adımlar

Yeni sanal makinenizde oturum açın. Daha fazla bilgi için bkz. [Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum](connect-logon.md)açma.