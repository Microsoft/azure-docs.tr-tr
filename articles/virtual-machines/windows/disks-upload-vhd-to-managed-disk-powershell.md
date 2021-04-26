---
title: Bir VHD 'yi Azure 'a yükleyin veya bir diski bölgeler arasında kopyalayın-Azure PowerShell
description: Bir VHD 'yi Azure yönetilen diskine yüklemeyi ve Azure PowerShell kullanarak doğrudan karşıya yükleme yoluyla bir yönetilen diski bölgelere kopyalamayı öğrenin.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 675e99797a507cdcf96ad33ab13c4f386f6f372c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169037"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Bir VHD 'yi Azure 'a yükleme veya yönetilen bir diski başka bir bölgeye kopyalama-Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Önkoşullar

- [AzCopy ile v10 arasındaki 'ın](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)en son sürümünü indirin.
- [Azure PowerShell modülünü yükler](/powershell/azure/install-Az-ps).
- Şirket içinden bir VHD yüklemeyi planlıyorsanız: [Azure için hazırlanan](prepare-for-upload-vhd-image.md)sabıt boyutlu VHD, yerel olarak depolanır.
- Ya da bir kopyalama eylemi gerçekleştirmek istiyorsanız Azure 'da yönetilen bir disk.

## <a name="getting-started"></a>Kullanmaya başlama

Bir GUI aracılığıyla disk yüklemeyi tercih ediyorsanız, Azure Depolama Gezgini kullanarak bunu yapabilirsiniz. Ayrıntılar için bkz. [Azure yönetilen diskleri yönetmek için Azure Depolama Gezgini kullanma](../disks-use-storage-explorer-managed-disks.md)

VHD 'nizi Azure 'a yüklemek için, bu karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturmanız gerekir. Bir tane oluşturmadan önce, bu diskler hakkında bilmeniz gereken bazı ek bilgiler vardır.

Bu tür yönetilen disklerin iki benzersiz durumu vardır:

- ReadyToUpload, diskin karşıya yükleme almaya hazır olduğu ancak [güvenli erişim imzası](../../storage/common/storage-sas-overview.md) (SAS) üretilmediği anlamına gelir.
- ActiveUpload, bu, diskin karşıya yükleme almaya ve SAS üretilmeye hazırlanmasıdır.

> [!NOTE]
> Bu durumlardan birinde, yönetilen disk, gerçek disk türünden bağımsız olarak [Standart HDD fiyatlandırmasına](https://azure.microsoft.com/pricing/details/managed-disks/)göre faturalandırılır. Örneğin, bir P10 S10 olarak faturalandırılacaktır. Bu, `revoke-access` diski BIR VM 'ye eklemek için gerekli olan yönetilen diskte çağrılana kadar doğru olacaktır.

## <a name="create-an-empty-managed-disk"></a>Boş bir yönetilen disk oluşturma

Karşıya yüklemek üzere boş bir standart HDD oluşturabilmeniz için önce, yüklemek istediğiniz VHD 'nin bayt cinsinden dosya boyutu gerekir. Örnek kod, sizin tarafınızdan sizin için bunu sizin için kullanabilirsiniz `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Bu değer **-uploadsizeınbytes** parametresini belirtirken kullanılır.

Şimdi, yerel kabuğunuzun içinde, **-createoption** parametresindeki **karşıya yükleme** ayarını ve [New-azdiskconfig](/powershell/module/az.compute/new-azdiskconfig) cmdlet 'inde **-uploadsizeınbytes** PARAMETRESINI belirterek karşıya yüklemek üzere boş bir standart HDD oluşturun. Ardından, diski oluşturmak için [New-AzDisk](/powershell/module/az.compute/new-azdisk) ' i çağırın.

`<yourdiskname>`, `<yourresourcegroupname>` Ve `<yourregion>` ardından aşağıdaki komutları çalıştırın:

> [!TIP]
> Bir işletim sistemi diski oluşturuyorsanız, `-HyperVGeneration '<yourGeneration>'` öğesine ekleyin `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Premium SSD veya standart SSD yüklemek isterseniz, **Standard_LRS** **Premium_LRS** veya **StandardSSD_LRS** ile değiştirin. Ultra diskler henüz desteklenmiyor.

Karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturduğunuza göre, buna bir VHD yükleyebilirsiniz. Bir VHD 'yi diske yüklemek için, bir yazılabilir SAS gerekir, bu sayede karşıya yüklemenizin hedefi olarak başvurabilirsiniz.

Boş yönetilen diskinizin yazılabilir bir SAS oluşturmak için, `<yourdiskname>` ve `<yourresourcegroupname>` ' ı değiştirin ve ardından aşağıdaki komutları kullanın:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>VHD’yi karşıya yükleme

Boş yönetilen diskiniz için bir SAS olduğuna göre, bunu kullanarak, yükleme Komutunuz için yönetilen diskinizi hedef olarak ayarlayabilirsiniz.

Yerel VHD dosyanızı oluşturduğunuz SAS URI 'sini belirterek yönetilen bir diske yüklemek için AzCopy ile v10 arasındaki kullanın.

Bu karşıya yükleme, eşdeğer [Standart HDD](../disks-types.md#standard-hdd)ile aynı aktarım hızına sahiptir. Örneğin, S4 'e karşılık gelen bir boyutunuz varsa, 60 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz. Ancak, S70 'e karşılık gelen bir boyutunuz varsa, 500 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Karşıya yükleme tamamlandıktan sonra ve diske daha fazla veri yazmanıza gerek kalmadığında, SAS 'yi iptal edin. SAS iptal edildiğinde, yönetilen diskin durumu değişir ve diski bir VM 'ye eklemenize olanak tanır.

`<yourdiskname>`Ve öğesini değiştirin ve `<yourresourcegroupname>` ardından aşağıdaki komutu çalıştırın:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Yönetilen diski çoğaltma

Doğrudan karşıya yükleme, yönetilen bir disk kopyalama işlemini de basitleştirir. Aynı bölge içinde kopyalayabilir veya yönetilen diskinizi başka bir bölgeye kopyalayabilirsiniz.

İzleme betiği bunu sizin için yapacaktır, bu işlem, mevcut bir diskle çalıştığınızdan, bazı farklılıklarla daha önce açıklanan adımlara benzerdir.

> [!IMPORTANT]
> Azure 'dan yönetilen bir diskin bayt cinsinden disk boyutunu sağlarken 512 sapmasını eklemeniz gerekir. Bunun nedeni, Azure 'un disk boyutunu döndürürken alt bilgiyi atatmesinden kaynaklanır. Bunu yapmazsanız kopya başarısız olur. Aşağıdaki komut dosyası sizin için zaten bunu yapar.

,, `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` , `<yourOSTypeHere>` Ve (bir `<yourTargetLocationHere>` konum değeri örneği uswest2) değerlerini değerleriyle değiştirin, ardından yönetilen bir diski kopyalamak için aşağıdaki betiği çalıştırın.

> [!TIP]
> Bir işletim sistemi diski oluşturuyorsanız, ' <yourGeneration> ' öğesine ' ' ekleyin `New-AzDiskConfig` .

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
