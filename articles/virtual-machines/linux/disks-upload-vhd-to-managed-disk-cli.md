---
title: Azure CLı kullanarak bir VHD 'yi Azure 'a yükleme
description: Azure yönetilen diskine bir VHD yükleme ve Azure CLı kullanarak bölgeler arasında yönetilen bir disk kopyalama hakkında bilgi edinin.
services: virtual-machines-linux,storage
author: roygara
ms.author: rogarana
ms.date: 09/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 5215a7d899af15dc028189aee5760a6ec5b6577d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803995"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Azure CLı kullanarak bir VHD 'yi Azure 'a yükleme

Bu makalede, yerel makinenizden bir Azure yönetilen diskine bir VHD yükleme işleminin nasıl yapılacağı açıklanır. Daha önce, verilerinizi bir depolama hesabına hazırlama ve bu depolama hesabını yönetme dahil olmak üzere daha fazla ilgili bir işlemi izlemeniz gerekiyordu. Artık bir depolama hesabını yönetmeniz veya bir VHD 'yi karşıya yüklemek için içindeki verileri aşamalandırmanız gerekmez. Bunun yerine, boş bir yönetilen disk oluşturur ve doğrudan buna bir VHD yüklersiniz. Bu, şirket içi VM 'Leri Azure 'a yüklemeyi basitleştirir ve bir VHD 'yi 32 TiB 'ye kadar doğrudan büyük bir yönetilen diske yüklemenizi sağlar.

Azure 'da IaaS sanal makineleri için bir yedekleme çözümü sağlıyorsanız, müşteri yedeklemelerini yönetilen disklere geri yüklemek için doğrudan karşıya yükleme kullanmanızı öneririz. Bir VHD 'yi Azure 'a harici bir makineden yüklüyorsanız, hızlarınızın yerel bant genişliğinizin olması gerekir. Azure VM kullanıyorsanız, bant genişliğiniz standart HDD 'Ler ile aynı olacaktır.

Şu anda, standart HDD, standart SSD ve Premium SSD tarafından yönetilen diskler için doğrudan karşıya yükleme desteklenir. Henüz Ultra SSD 'Ler için desteklenmez.

## <a name="prerequisites"></a>Önkoşullar

- [AzCopy ile v10 arasındaki 'ın](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)en son sürümünü indirin.
- [Azure CLI 'Yı yükler](/cli/azure/install-azure-cli).
- Yerel olarak depolanan bir VHD dosyası
- Şirket içinden bir VHD yüklemeyi planlıyorsanız: [Azure için hazırlanan](../windows/prepare-for-upload-vhd-image.md)bir VHD, yerel olarak depolanır.
- Ya da bir kopyalama eylemi gerçekleştirmek istiyorsanız Azure 'da yönetilen bir disk.

## <a name="create-an-empty-managed-disk"></a>Boş bir yönetilen disk oluşturma

VHD 'nizi Azure 'a yüklemek için, bu karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturmanız gerekir. Bir tane oluşturmadan önce, bu diskler hakkında bilmeniz gereken bazı ek bilgiler vardır.

Bu tür yönetilen disklerin iki benzersiz durumu vardır:

- ReadToUpload, diskin karşıya yüklemeyi almaya çalıştığı ancak [güvenli erişim imzası](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) üretilmediği anlamına gelir.
- ActiveUpload, bu, diskin karşıya yükleme almaya ve SAS üretilmeye hazırlanmasıdır.

Bu durumlardan birinde, yönetilen disk, gerçek disk türünden bağımsız olarak [Standart HDD fiyatlandırmasına](https://azure.microsoft.com/pricing/details/managed-disks/)göre faturalandırılır. Örneğin, bir P10 S10 olarak faturalandırılacaktır. Bu, diskin bir VM 'ye eklenmesi için gerekli olan yönetilen diskte `revoke-access` çağrılana kadar doğru olacaktır.

Karşıya yüklemek üzere boş bir standart HDD oluşturabilmeniz için önce, karşıya yüklemek istediğiniz VHD 'nin dosya boyutunu bayt cinsinden yapmanız gerekir. Bunu sağlamak için `wc -c <yourFileName>.vhd` veya `ls -al <yourFileName>.vhd` kullanabilirsiniz. Bu değer, **--karşıya yükleme-boyut-bayt** parametresi belirtildiğinde kullanılır.

Bir [disk oluşturma](/cli/azure/disk#az-disk-create) cmdlet 'inde-- **for-upload** parametresini ve **--upload-size-bytes** parametresini belirterek KARŞıYA yüklemek için boş bir standart HDD oluşturun:

```bash
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Premium SSD veya standart SSD yüklemek isterseniz, **standard_lrs** değerini **premium_LRS** veya **standardssd_lrs**ile değiştirin. Ultra SSD henüz desteklenmiyor.

Şimdi karşıya yükleme işlemi için yapılandırılmış boş bir yönetilen disk oluşturdunuz. Bir VHD 'yi diske yüklemek için, bir yazılabilir SAS gerekir, bu sayede karşıya yüklemenizin hedefi olarak başvurabilirsiniz.

Boş yönetilen diskinizin yazılabilir bir SAS oluşturmak için aşağıdaki komutu kullanın:

```bash
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Örnek döndürülen değer:

```
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>VHD 'yi karşıya yükle

Boş yönetilen diskiniz için bir SAS olduğuna göre, bunu kullanarak, yükleme Komutunuz için yönetilen diskinizi hedef olarak ayarlayabilirsiniz.

Yerel VHD dosyanızı oluşturduğunuz SAS URI 'sini belirterek yönetilen bir diske yüklemek için AzCopy ile v10 arasındaki kullanın.

Bu karşıya yükleme, eşdeğer [Standart HDD](disks-types.md#standard-hdd)ile aynı aktarım hızına sahiptir. Örneğin, S4 'e karşılık gelen bir boyutunuz varsa, 60 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz. Ancak, S70 'e karşılık gelen bir boyutunuz varsa, 500 MIB/sn 'ye kadar bir aktarım hızına sahip olursunuz.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Yükleme sırasında SAS süresi dolarsa ve `revoke-access` henüz çağırmadıysanız, `grant-access` kullanarak karşıya yüklemeye devam etmek için yeni bir SAS alabilirsiniz.

Karşıya yükleme tamamlandıktan sonra ve diske daha fazla veri yazmanıza gerek kalmadığında, SAS 'yi iptal edin. SAS iptal edildiğinde, yönetilen diskin durumu değişir ve diski bir VM 'ye eklemenize olanak tanır.

```bash
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Yönetilen diski çoğaltma

Doğrudan karşıya yükleme, yönetilen bir disk kopyalama işlemini de basitleştirir. Aynı bölge veya çapraz bölge içinde (başka bir bölgeye) kopyalayabilirsiniz.

İzleme betiği bunu sizin için işler, mevcut bir diskle çalıştığınızdan daha önce açıklanan adımlara benzer.

> [!IMPORTANT]
> Azure 'dan yönetilen bir diskin bayt cinsinden disk boyutunu sağlarken 512 sapmasını eklemeniz gerekir. Bunun nedeni, Azure 'un disk boyutunu döndürürken alt bilgiyi atatmesinden kaynaklanır. Bunu yapmazsanız kopya başarısız olur. Aşağıdaki komut dosyası sizin için zaten bunu yapar.

@No__t_0, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>` ve `<yourTargetLocationHere>` (bir konum değerinin bir örneği uswest2) değerini değerleriyle değiştirin, ardından yönetilen bir diski kopyalamak için aşağıdaki betiği çalıştırın.

```bash
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Sonraki adımlar

Bir VHD 'yi yönetilen diske başarıyla yüklediğinize göre, diski mevcut bir sanal makineye bir [veri diski](add-disk.md) olarak ekleyebilir veya [diski bir VM 'ye bir sanal makineye iliştirebilirsiniz](upload-vhd.md#create-the-vm), yeni bir VM oluşturabilirsiniz. 

