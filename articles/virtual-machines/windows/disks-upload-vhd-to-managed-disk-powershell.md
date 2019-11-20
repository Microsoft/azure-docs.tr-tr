---
title: Azure PowerShell kullanarak bir VHD 'yi Azure 'a yükleme
description: Bir VHD 'yi Azure yönetilen diskine yüklemeyi ve Azure PowerShell kullanarak bir yönetilen diski bölgeler arasında kopyalamayı öğrenin.
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: d193dcd0c0539c2daa7220d915fdc3e02c8ea798
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512430"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Azure PowerShell kullanarak bir VHD 'yi Azure 'a yükleme

Bu makalede, yerel makinenizden bir Azure yönetilen diskine bir VHD yükleme işleminin nasıl yapılacağı açıklanır. Daha önce, verilerinizi bir depolama hesabına hazırlama ve bu depolama hesabını yönetme dahil olmak üzere daha fazla ilgili bir işlemi izlemeniz gerekiyordu. Artık bir depolama hesabını yönetmeniz veya bir VHD 'yi karşıya yüklemek için içindeki verileri aşamalandırmanız gerekmez. Bunun yerine, boş bir yönetilen disk oluşturur ve doğrudan buna bir VHD yüklersiniz. Bu, şirket içi VM 'Leri Azure 'a yüklemeyi basitleştirir ve bir VHD 'yi 32 TiB 'ye kadar doğrudan büyük bir yönetilen diske yüklemenizi sağlar.

Azure 'da IaaS sanal makineleri için bir yedekleme çözümü sağlıyorsanız, müşteri yedeklemelerini yönetilen disklere geri yüklemek için doğrudan karşıya yükleme kullanmanızı öneririz. Bir VHD 'yi Azure 'a harici bir makineden yüklüyorsanız, hızları yerel bant genişliğine göre değişir. Azure VM kullanıyorsanız, bant genişliğiniz standart HDD 'Ler ile aynı olacaktır.

Şu anda, standart HDD, standart SSD ve Premium SSD tarafından yönetilen diskler için doğrudan karşıya yükleme desteklenir. Henüz Ultra SSD 'Ler için desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

- [AzCopy ile v10 arasındaki 'ın](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)en son sürümünü indirin.
- [Azure PowerShell modülünü yükler](/powershell/azure/install-Az-ps).
- Şirket içi bir VHD 'yi karşıya yüklemeyi planlıyorsanız: [Azure için hazırlanan](prepare-for-upload-vhd-image.md)bir VHD, yerel olarak depolanır.
- Ya da bir kopyalama eylemi gerçekleştirmek istiyorsanız Azure 'da yönetilen bir disk.

## <a name="create-an-empty-managed-disk"></a>Boş bir yönetilen disk oluşturma

VHD 'nizi Azure 'a yüklemek için, bu karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturmanız gerekir. Bir tane oluşturmadan önce, bu diskler hakkında bilmeniz gereken bazı ek bilgiler vardır.

Bu tür yönetilen disklerin iki benzersiz durumu vardır:

- ReadToUpload, diskin karşıya yüklemeyi almaya çalıştığı ancak [güvenli erişim imzası](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) üretilmediği anlamına gelir.
- ActiveUpload, bu, diskin karşıya yükleme almaya ve SAS üretilmeye hazırlanmasıdır.

Bu durumlardan birinde, yönetilen disk, gerçek disk türünden bağımsız olarak [Standart HDD fiyatlandırmasına](https://azure.microsoft.com/pricing/details/managed-disks/)göre faturalandırılır. Örneğin, bir P10 S10 olarak faturalandırılacaktır. Bu, diskin bir VM 'ye eklenmesi için gerekli olan yönetilen diskte `revoke-access` çağrılana kadar doğru olacaktır.

Karşıya yüklemek üzere boş bir standart HDD oluşturmadan önce, karşıya yüklemek istediğiniz VHD 'nin bayt cinsinden dosya boyutu gerekir. Örnek kod, sizin tarafınızdan sizin için bunu sizin için kullanabilirsiniz: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`. Bu değer **-uploadsizeınbytes** parametresini belirtirken kullanılır.

Şimdi, yerel kabuğunuzun içinde, **-createoption** parametresindeki **karşıya yükleme** ayarını ve [New-azdiskconfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) cmdlet 'inde **-uploadsizeınbytes** PARAMETRESINI belirterek karşıya yüklemek üzere boş bir standart HDD oluşturun. Ardından, diski oluşturmak için [New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) ' i çağırın:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Premium SSD veya standart SSD yüklemek isterseniz, **Standard_LRS** değerini **Premium_LRS** veya **StandardSSD_LRS**ile değiştirin. Ultra SSD henüz desteklenmiyor.

Şimdi karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturdunuz. Bir VHD 'yi diske yüklemek için, bir yazılabilir SAS gerekir, bu sayede karşıya yüklemenizin hedefi olarak başvurabilirsiniz.

Boş yönetilen diskinizin yazılabilir bir SAS oluşturmak için aşağıdaki komutu kullanın:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>VHD 'yi karşıya yükle

Boş yönetilen diskiniz için bir SAS olduğuna göre, bunu kullanarak, yükleme Komutunuz için yönetilen diskinizi hedef olarak ayarlayabilirsiniz.

Yerel VHD dosyanızı oluşturduğunuz SAS URI 'sini belirterek yönetilen bir diske yüklemek için AzCopy ile v10 arasındaki kullanın.

Bu karşıya yükleme, eşdeğer [Standart HDD](disks-types.md#standard-hdd)ile aynı aktarım hızına sahiptir. Örneğin, S4 'e karşılık gelen bir boyutunuz varsa, 60 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz. Ancak, S70 'e karşılık gelen bir boyutunuz varsa, 500 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Yükleme sırasında SAS süresi dolarsa ve `revoke-access` henüz çağırmadıysanız, `grant-access` kullanarak karşıya yüklemeye devam etmek için yeni bir SAS alabilirsiniz.

Karşıya yükleme tamamlandıktan sonra ve diske daha fazla veri yazmanıza gerek kalmadığında, SAS 'yi iptal edin. SAS iptal edildiğinde, yönetilen diskin durumu değişir ve diski bir VM 'ye eklemenize olanak tanır.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Yönetilen diski çoğaltma

Doğrudan karşıya yükleme, yönetilen bir disk kopyalama işlemini de basitleştirir. Aynı bölge veya çapraz bölge içinde (başka bir bölgeye) kopyalayabilirsiniz.

İzleme betiği bunu sizin için işler, mevcut bir diskle çalıştığınızdan daha önce açıklanan adımlara benzer.

> [!IMPORTANT]
> Azure 'dan yönetilen bir diskin bayt cinsinden disk boyutunu sağlarken 512 sapmasını eklemeniz gerekir. Bunun nedeni, Azure 'un disk boyutunu döndürürken alt bilgiyi atatmesinden kaynaklanır. Bunu yapmazsanız kopya başarısız olur. Aşağıdaki komut dosyası sizin için zaten bunu yapar.

@No__t_0, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>` ve `<yourTargetLocationHere>` (bir konum değerinin bir örneği uswest2) değerini değerleriyle değiştirin, ardından yönetilen bir diski kopyalamak için aşağıdaki betiği çalıştırın.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Sonraki adımlar

Bir VHD 'yi yönetilen diske başarıyla yüklediğinize göre, diski bir sanal makineye iliştirebilir ve kullanmaya başlayabilirsiniz.

Bir sanal makineye bir veri diski eklemeyi öğrenmek için, ilgili makalemize bakın: [PowerShell ile bir WINDOWS sanal makinesine veri diski iliştirme](attach-disk-ps.md). Diski işletim sistemi diski olarak kullanmak için bkz. [özel bir diskten WINDOWS VM oluşturma](create-vm-specialized.md#create-the-new-vm).
