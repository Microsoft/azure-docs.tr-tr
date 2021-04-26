---
title: Bir VHD 'yi Azure 'a yükleyin veya bir diski bölgeler arasında kopyalayın-Azure CLı
description: Azure yönetilen diskine bir VHD yüklemeyi ve doğrudan karşıya yükleme yoluyla Azure CLı kullanarak bölgeler arasında yönetilen bir disk kopyalamayı öğrenin.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0f48856f085737040ca16afcca1e56be1da4843e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816755"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Bir VHD 'yi Azure 'a yükleme veya yönetilen bir diski başka bir bölgeye kopyalama-Azure CLı

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Önkoşullar

- [AzCopy ile v10 arasındaki 'ın](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)en son sürümünü indirin.
- [Azure CLI 'Yı yükler](/cli/azure/install-azure-cli).
- Şirket içinden bir VHD yüklemeyi planlıyorsanız: [Azure için hazırlanan](../windows/prepare-for-upload-vhd-image.md)sabıt boyutlu VHD, yerel olarak depolanır.
- Ya da bir kopyalama eylemi gerçekleştirmek istiyorsanız Azure 'da yönetilen bir disk.

## <a name="getting-started"></a>Kullanmaya başlama

Bir GUI aracılığıyla disk yüklemeyi tercih ediyorsanız, Azure Depolama Gezgini kullanarak bunu yapabilirsiniz. Ayrıntılar için bkz. [Azure yönetilen diskleri yönetmek için Azure Depolama Gezgini kullanma](../disks-use-storage-explorer-managed-disks.md)

VHD 'nizi Azure 'a yüklemek için, bu karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturmanız gerekir. Bir tane oluşturmadan önce, bu diskler hakkında bilmeniz gereken bazı ek bilgiler vardır.

Bu tür yönetilen disklerin iki benzersiz durumu vardır:

- ReadToUpload, diskin karşıya yüklemeyi almaya çalıştığı ancak [güvenli erişim imzası](../../storage/common/storage-sas-overview.md) (SAS) üretilmediği anlamına gelir.
- ActiveUpload, bu, diskin karşıya yükleme almaya ve SAS üretilmeye hazırlanmasıdır.

> [!NOTE]
> Bu durumlardan birinde, yönetilen disk, gerçek disk türünden bağımsız olarak [Standart HDD fiyatlandırmasına](https://azure.microsoft.com/pricing/details/managed-disks/)göre faturalandırılır. Örneğin, bir P10 S10 olarak faturalandırılacaktır. Bu, `revoke-access` diski BIR VM 'ye eklemek için gerekli olan yönetilen diskte çağrılana kadar doğru olacaktır.

## <a name="create-an-empty-managed-disk"></a>Boş bir yönetilen disk oluşturma

Karşıya yüklemek üzere boş bir standart HDD oluşturabilmeniz için önce, yüklemek istediğiniz VHD 'nin bayt cinsinden dosya boyutu gerekir. Bunu sağlamak için ya da kullanabilirsiniz `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd` . Bu değer, **--karşıya yükleme-boyut-bayt** parametresi belirtildiğinde kullanılır.

Bir [disk oluşturma](/cli/azure/disk#az_disk_create) cmdlet 'inde-- **for-upload** parametresini ve **--upload-size-bytes** parametresini belirterek KARŞıYA yüklemek için boş bir standart HDD oluşturun:

Öğesini `<yourdiskname>` `<yourresourcegroupname>` `<yourregion>` seçtiğiniz değerlerle değiştirin. `--upload-size-bytes`Parametresi örnek bir değeri içerir `34359738880` , bunu sizin için uygun bir değerle değiştirin.

> [!TIP]
> Bir işletim sistemi diski oluşturuyorsanız,--Hyper-v oluşturma 'yı <yourGeneration> öğesine ekleyin `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Premium SSD veya standart SSD yüklemek isterseniz, **standard_lrs** **premium_LRS** veya **standardssd_lrs** ile değiştirin. Günümüzde Ultra diskler desteklenmez.

Karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturduğunuza göre, buna bir VHD yükleyebilirsiniz. Bir VHD 'yi diske yüklemek için, bir yazılabilir SAS gerekir, bu sayede karşıya yüklemenizin hedefi olarak başvurabilirsiniz.

Boş yönetilen diskinizin yazılabilir bir SAS oluşturmak için, `<yourdiskname>` ve öğesini değiştirin ve `<yourresourcegroupname>` ardından aşağıdaki komutu kullanın:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Örnek döndürülen değer:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD’yi karşıya yükleme

Boş yönetilen diskiniz için bir SAS olduğuna göre, bunu kullanarak, yükleme Komutunuz için yönetilen diskinizi hedef olarak ayarlayabilirsiniz.

Yerel VHD dosyanızı oluşturduğunuz SAS URI 'sini belirterek yönetilen bir diske yüklemek için AzCopy ile v10 arasındaki kullanın.

Bu karşıya yükleme, eşdeğer [Standart HDD](../disks-types.md#standard-hdd)ile aynı aktarım hızına sahiptir. Örneğin, S4 'e karşılık gelen bir boyutunuz varsa, 60 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz. Ancak, S70 'e karşılık gelen bir boyutunuz varsa, 500 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Karşıya yükleme tamamlandıktan sonra ve diske daha fazla veri yazmanıza gerek kalmadığında, SAS 'yi iptal edin. SAS iptal edildiğinde, yönetilen diskin durumu değişir ve diski bir VM 'ye eklemenize olanak tanır.

`<yourdiskname>`Ve öğesini değiştirin ve `<yourresourcegroupname>` ardından diski kullanılabilir hale getirmek için aşağıdaki komutu kullanın:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Yönetilen diski çoğaltma

Doğrudan karşıya yükleme, yönetilen bir disk kopyalama işlemini de basitleştirir. Aynı bölge veya çapraz bölge içinde (başka bir bölgeye) kopyalayabilirsiniz.

İzleme betiği bunu sizin için işler, mevcut bir diskle çalıştığınızdan daha önce açıklanan adımlara benzer.

> [!IMPORTANT]
> Azure 'dan yönetilen bir diskin bayt cinsinden disk boyutunu sağlarken 512 sapmasını eklemeniz gerekir. Bunun nedeni, Azure 'un disk boyutunu döndürürken alt bilgiyi atatmesinden kaynaklanır. Bunu yapmazsanız kopya başarısız olur. Aşağıdaki komut dosyası sizin için zaten bunu yapar.

,, `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` , Ve (bir `<yourTargetLocationHere>` konum değeri örneği uswest2) değerlerini değerleriyle değiştirin, ardından yönetilen bir diski kopyalamak için aşağıdaki betiği çalıştırın.

> [!TIP]
> Bir işletim sistemi diski oluşturuyorsanız,--Hyper-v oluşturma 'yı <yourGeneration> öğesine ekleyin `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Sonraki adımlar

Bir VHD 'yi yönetilen diske başarıyla yüklediğinize göre, diski mevcut bir sanal makineye bir [veri diski](add-disk.md) olarak ekleyebilir veya [diski bir VM 'ye bir sanal makineye iliştirebilirsiniz](upload-vhd.md#create-the-vm), yeni bir VM oluşturabilirsiniz.
