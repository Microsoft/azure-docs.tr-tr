---
title: Genelleştirilmiş şirket içi bir VHD 'den yönetilen bir Azure VM oluşturma
description: Genelleştirilmiş bir VHD 'yi Azure 'a yükleyin ve Kaynak Yöneticisi dağıtım modelinde yeni VM 'Ler oluşturmak için kullanın.
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
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: d0995fed61d169cc173ca01767c2e48f4f798b0d
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74067429"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Genelleştirilmiş bir VHD 'YI karşıya yükleyin ve Azure 'da yeni VM 'Ler oluşturmak için kullanın

Bu makalede, PowerShell kullanarak genelleştirilmiş bir sanal makinenin VHD 'sini Azure 'a yükleme, VHD 'den bir görüntü oluşturma ve bu görüntüden yeni bir VM oluşturma işlemleri gösterilmektedir. Şirket içi bir sanallaştırma aracından veya başka bir buluttan aktarılmış bir VHD 'yi karşıya yükleyebilirsiniz. Yeni VM için [yönetilen disklerin](managed-disks-overview.md) kullanılması VM yönetimini BASITLEŞTIRIR ve VM bir kullanılabilirlik kümesine yerleştirildiğinde daha iyi kullanılabilirlik sağlar. 

Örnek betik için bkz. [Azure 'a BIR VHD yüklemek ve yeni BIR VM oluşturmak Için örnek betik](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Başlamadan önce

- Herhangi bir VHD 'yi Azure 'a yüklemeden önce, [Azure 'a yüklemek için bir WINDOWS VHD veya vhdx hazırlamanızı](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)izlemelisiniz.
- [Yönetilen disklere](managed-disks-overview.md)geçişinizi başlatmadan önce [yönetilen disklere geçiş planını](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) gözden geçirin.

 


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Sysprep kullanarak kaynak VM 'yi Genelleştirme

Sysprep diğer öğelerin yanı sıra tüm kişisel hesap bilgilerinizi kaldırır ve makineyi bir görüntü olarak kullanılacak şekilde hazırlar. Sysprep hakkında daha fazla bilgi için bkz. [Sysprep genel bakış](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Makinede çalışan sunucu rollerinin Sysprep tarafından desteklendiğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> VHD 'nizi Azure 'a ilk kez yüklemeden önce Sysprep 'i çalıştırmayı planlıyorsanız, [VM 'nizi hazırladığınızdan](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)emin olun. 
> 
> 

1. Windows sanal makinesinde oturum açın.
2. Yönetici olarak Komut İstemi penceresini açın. Dizini%windir%\system32\sysprep olarak değiştirip `sysprep.exe`çalıştırın.
3. **Sistem Hazırlama Aracı** iletişim kutusunda, **sistem kutudan çıkar deneyimi (OOBE)** seçeneğini belirleyin ve **Genelleştir** onay kutusunun etkinleştirildiğinden emin olun.
4. **Kapalı seçenekleri**Için, **kapanıyor**' ı seçin.
5. **Tamam**’ı seçin.
   
    ![Sysprep 'ı Başlat](./media/upload-generalized-managed/sysprepgeneral.png)
6. Sysprep tamamlandığında, sanal makineyi kapatır. VM 'yi yeniden başlatmayın.


## <a name="upload-the-vhd-to-your-storage-account"></a>VHD 'YI depolama hesabınıza yükleyin

Artık bir VHD 'YI bir yönetilen diske doğrudan yükleyebilirsiniz. Yönergeler için bkz. [Azure PowerShell kullanarak BIR VHD 'Yi Azure 'A yükleme](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Karşıya yüklenen VHD 'den yönetilen bir görüntü oluşturma 

Genelleştirilmiş işletim sistemi tarafından yönetilen diskinizden yönetilen bir görüntü oluşturun. Aşağıdaki değerleri kendi bilgileriniz ile değiştirin.


İlk olarak, bazı parametreleri ayarlayın:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Genelleştirilmiş işletim sistemi VHD 'nizi kullanarak görüntü oluşturun.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Sanal makine oluşturma

Artık bir görüntünüz olduğuna göre, görüntüden bir veya daha fazla yeni VM oluşturabilirsiniz. Bu örnek *Myresourcegroup*Içindeki *myvm* adlı birVM oluşturur.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Sonraki adımlar

Yeni sanal makinenizde oturum açın. Daha fazla bilgi için bkz. [Windows çalıştıran bir Azure sanal makinesine bağlanma ve oturum](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)açma. 

