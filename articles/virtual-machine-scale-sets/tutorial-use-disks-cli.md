---
title: Öğretici - Azure CLI ile ölçek kümeleri için diskler oluşturma ve kullanma | Microsoft Docs
description: Disk ekleme, hazırlama, listeleme ve ayırma gibi, Azure CLI kullanılarak sanal makine ölçek kümesi ile Yönetilen Diskler oluşturma ve kullanma işlemlerinin nasıl yapılacağını öğrenin.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 58090e860b79d59021d467fcf73596271c91c7f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60329465"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Öğretici: Diskler oluşturma ve sanal makine ölçek kümesi Azure CLI ile kullanma
Sanal makine ölçek kümeleri, sanal makine örneğinin işletim sistemini, uygulamalarını ve verilerini depolamak için diskleri kullanır. Bir ölçek kümesi oluştururken ve yönetirken, beklenen iş yüküne uygun disk boyutu ve yapılandırmasını seçmek önemlidir. Bu öğretici, sanal makine disklerinin oluşturulmasını ve yönetilmesini kapsar. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * İşletim sistemi diskleri ve geçici diskler
> * Veri diskleri
> * Standart ve Premium diskler
> * Disk performansı
> * Veri disklerini ekleme ve hazırlama

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.29 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).


## <a name="default-azure-disks"></a>Varsayılan Azure diskleri
Bir ölçek kümesi oluşturulduğunda veya ölçeklendirildiğinde, her bir sanal makine örneğine otomatik olarak iki disk eklenir.

**İşletim sistemi diski** - İşletim sistemi diskleri 2 TB’a kadar boyutlandırılabilir ve sanal makinenin işletim sistemini barındırır. İşletim sistemi diski varsayılan olarak */dev/sda* etiketine sahiptir. İşletim sistemi diskinin yapılandırmasını önbelleğe alan disk, işletim sistemi performansı için iyileştirilir. Bu yapılandırma nedeniyle işletim sistemi diski uygulama veya veri **barındırmamalıdır**. Uygulamalar ve veriler için, bu makalede daha sonra ayrıntılı olarak açıklanan veri disklerini kullanın.

**Geçici disk** - Geçici diskler, sanal makine örneğiyle aynı Azure ana bilgisayarında bulunan bir katı hal sürücüsü kullanır. Bunlar yüksek performansa sahiptir ve geçici veri işleme gibi işlemler için kullanılabilir. Ancak sanal makine örneği yeni bir ana bilgisayara taşınırsa, geçici diskte depolanan tüm veriler kaldırılır. Geçici diskin boyutu, sanal makine örneği tarafından belirlenir. Geçici diskler */dev/sdb* etiketine ve */mnt* bağlama noktasına sahiptir.

### <a name="temporary-disk-sizes"></a>Geçici disk boyutları
| Tür | Ortak boyutlar | En yüksek geçici disk boyutu (GiB) |
|----|----|----|
| [Genel amaçlı](../virtual-machines/linux/sizes-general.md) | A, B ve D serisi | 1600 |
| [İşlem için iyileştirilmiş](../virtual-machines/linux/sizes-compute.md) | F serisi | 576 |
| [Bellek için iyileştirilmiş](../virtual-machines/linux/sizes-memory.md) | D, E, G ve M serisi | 6144 |
| [Depolama için iyileştirilmiş](../virtual-machines/linux/sizes-storage.md) | L serisi | 5630 |
| [GPU](../virtual-machines/linux/sizes-gpu.md) | N serisi | 1440 |
| [Yüksek performans](../virtual-machines/linux/sizes-hpc.md) | A ve H serisi | 2000 |


## <a name="azure-data-disks"></a>Azure veri diskleri
Uygulamalar yüklemeniz ve veri depolamanız gerekirse ek veri diskleri eklenebilir. Dayanıklı ve duyarlı veri depolama gerektiren her koşulda veri diskleri kullanılmalıdır. Her veri diski maksimum 4 TB kapasiteye sahiptir. Sanal makine örneğinin boyutu, kaç veri diskinin eklenebileceğini belirler. Her VM vCPU için iki veri diski eklenebilir.

### <a name="max-data-disks-per-vm"></a>VM başına en fazla veri diski
| Tür | Ortak boyutlar | VM başına en fazla veri diski |
|----|----|----|
| [Genel amaçlı](../virtual-machines/linux/sizes-general.md) | A, B ve D serisi | 64 |
| [İşlem için iyileştirilmiş](../virtual-machines/linux/sizes-compute.md) | F serisi | 64 |
| [Bellek için iyileştirilmiş](../virtual-machines/linux/sizes-memory.md) | D, E, G ve M serisi | 64 |
| [Depolama için iyileştirilmiş](../virtual-machines/linux/sizes-storage.md) | L serisi | 64 |
| [GPU](../virtual-machines/linux/sizes-gpu.md) | N serisi | 64 |
| [Yüksek performans](../virtual-machines/linux/sizes-hpc.md) | A ve H serisi | 64 |


## <a name="vm-disk-types"></a>VM disk türleri
Azure iki disk türü sunar.

### <a name="standard-disk"></a>Standart disk
Standart Depolama, HDD’ler ile desteklenir ve uygun maliyetli depolama ve yüksek performans sağlar. Standart diskler, uygun maliyetli bir geliştirme ve iş yükü testi için idealdir.

### <a name="premium-disk"></a>Premium disk
Premium diskler SSD tabanlı, yüksek performanslı ve düşük gecikme süreli disk ile desteklenir. Üretim iş yüklerini çalıştıran sanal makineler için bu diskler önerilir. Premium Depolama, DS serisi, DSv2 serisi, GS serisi ve FS serisi VM'lerini destekler. Disk boyutu seçilirken boyutun değeri sonraki türe yuvarlanır. Örneğin disk boyutu 128 GB’den azsa disk türü P10’dur. Disk boyutu 129 GB ile 512 GB arasında ise boyut P20’dir. 512 GB’ın üstündeki diskler P30 boyutundadır.

### <a name="premium-disk-performance"></a>Premium disk performansı
|Premium depolama diski türü | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Disk boyutu (yuvarlanmış değer) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2\.048 GB (2 TB) | 4\.095 GB (4 TB) |
| Disk başına en fazla IOPS | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Disk başına aktarım hızı | 25 MB/sn | 50 MB/sn | 100 MB/s | 150 MB/s | 200 MB/sn | 250 MB/sn | 250 MB/sn |

Yukarıdaki tabloda, disk başına maksimum IOPS tanımlanmış olsa da birden çok veri diski bölümlenerek daha yüksek performansa ulaşılabilir. Örneğin bir Standard_GS5 VM’si en fazla 80.000 IOPS’ye ulaşabilir. VM başına IOPS üst sınırı hakkında ayrıntılı bilgi için bkz. [Linux VM türleri](../virtual-machines/linux/sizes.md).


## <a name="create-and-attach-disks"></a>Disk oluşturma ve ekleme
Bir ölçek kümesi oluştururken veya mevcut bir ölçek kümesi ile diskler oluşturabilir ve ekleyebilirsiniz.

### <a name="attach-disks-at-scale-set-creation"></a>Ölçek kümesi oluşturulurken diskler ekleme
Öncelikle, [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Bu örnekte, *eastus* bölgesinde *myResourceGroup* adlı bir kaynak grubu oluşturulur.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vmss create](/cli/azure/vmss) komutuyla bir sanal makine ölçek kümesi oluşturun. Aşağıdaki örnek, *myScaleSet* adlı bir ölçek kümesini ve yoksa SSH anahtarlarını oluşturur. `--data-disk-sizes-gb` parametresiyle iki disk oluşturulur. İlk diskin boyutu *64* GB, ikinci diskin boyutuysa *128* GB’tır:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

Tüm ölçek kümesi kaynaklarının ve sanal makine örneklerinin oluşturulup yapılandırılması birkaç dakika sürer.

### <a name="attach-a-disk-to-existing-scale-set"></a>Mevcut ölçek kümesine bir disk ekleme
Mevcut bir ölçek kümesine de diskler ekleyebilirsiniz. [az vmss disk attach](/cli/azure/vmss/disk) komutunu kullanarak başka bir disk eklemek için önceki adımda oluşturulan ölçek kümesini kullanın. Aşağıdaki örnekte ek bir *128* GB disk eklenmektedir:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Veri disklerini hazırlama
Oluşturulan ve ölçek kümesi sanal makine örneklerinize eklenen diskler, ham disklerdir. Bunları veri ve uygulamalarınızla kullanabilmeniz için disklerin hazırlanması gerekir. Diskleri hazırlamak için bir bölüm oluşturun, bir dosya sistemi oluşturun ve bunları bağlayın.

Bir ölçek kümesindeki birden çok sanal makine örneğinde işlemi otomatikleştirmek için Azure Özel Betik Uzantısı’nı kullanabilirsiniz. Bu uzantı, örneğin, eklenen veri disklerini hazırlamak için her bir sanal makine örneğinde betikleri yerel olarak yürütebilir. Daha fazla bilgi için bkz. [Özel Betik Uzantısı'na genel bakış](../virtual-machines/linux/extensions-customscript.md).

Aşağıdaki örnek, tüm eklenen ham veri disklerini hazırlayan [az vmss extension set](/cli/azure/vmss/extension) komutuyla her bir sanal makine örneğindeki GitHub örnek deposundan bir betik yürütür:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Disklerin düzgün şekilde hazırlandığını onaylamak için, sanal makine örneklerinden birinde SSH oturumu açın. [az vmss list-instance-connection-info](/cli/azure/vmss) komutuyla ölçek kümeniz için bağlantı bilgilerini listeleyin:

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Aşağıdaki örnekte gösterildiği gibi birinci sanal makine örneğinize bağlanmak için kendi genel IP adresinizi ve bağlantı noktası numaranızı kullanın:

```azurecli-interactive
ssh azureuser@52.226.67.166 -p 50001
```

Aşağıdaki gibi, sanal makine örneğindeki bölümleri inceleyin:

```bash
sudo fdisk -l
```

Aşağıdaki örnek çıktı, sanal makine örneğine üç diskin eklendiğini gösterir: */dev/sdc*, */dev/sdd* ve */dev/sde*. Bu disklerin her birinde, tüm kullanılabilir alanı kullanan tek bir bölüm vardır:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Aşağıdaki gibi, sanal makine örneğindeki dosya sistemlerini ve bağlama noktalarını inceleyin:

```bash
sudo df -h
```

Aşağıdaki örnek çıktı, üç diskin dosya sistemlerinin */datadisks* konumuna düzgün şekilde bağlandığını gösterir:

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

Ölçeğinizdeki her sanal makine örneğindeki diskler otomatik olarak aynı şekilde hazırlanır. Ölçek kümeniz yukarı ölçeklendirilirken, yeni sanal makine örneklerine gerekli veri diskleri eklenir. Diskleri otomatik olarak hazırlamak için Özel Betik Uzantısı da çalıştırılır.

Sanal makine örneğinize SSH bağlantısını kapatın:

```bash
exit
```


## <a name="list-attached-disks"></a>Eklenen diskleri listeleme
Bir ölçek kümesine eklenen disklerle ilgili bilgileri görüntülemek için [az vmss show](/cli/azure/vmss) komutunu kullanın ve *virtualMachineProfile.storageProfile.dataDisks* üzerinde sorgulama yapın:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

Disk boyutu, depolama katmanı ve LUN (Mantıksal Birim Numarası) ile ilgili bilgiler gösterilir. Aşağıdaki örnek çıktıda, ölçek kümesine eklenen üç veri diski açıklanmaktadır:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Disk ayırma
Belirli bir disk artık gerekli olmadığında o diski ölçek kümesinden ayırabilirsiniz. Disk, ölçek kümesindeki tüm sanal makine örneklerinden kaldırılır. Ölçek kümesinden bir diski ayırmak için [az vmss disk detach](/cli/azure/vmss/disk) komutunu kullanın ve diskin LUN’unu belirtin. Önceki bölümde [az vmss show](/cli/azure/vmss) komutundan elde edilen çıktıda LUN’lar gösterilir. Aşağıdaki örnek, ölçek kümesinden LUN *2*’yi ayırır:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Kaynakları temizleme
Ölçek kümenizi ve disklerinizi kaldırmak için [az group delete](/cli/azure/group) komutunu kullanarak kaynak grubunu ve bu kaynak grubunun tüm kaynaklarını silin. `--no-wait` parametresi işlemin tamamlanmasını beklemeden denetimi komut istemine döndürür. `--yes` parametresi kaynakları ek bir komut istemi olmadan silmek istediğinizi onaylar.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, Azure CLI ile ölçek kümeleri içeren diskler oluşturma ve kullanma işleminin nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * İşletim sistemi diskleri ve geçici diskler
> * Veri diskleri
> * Standart ve Premium diskler
> * Disk performansı
> * Veri disklerini ekleme ve hazırlama

Ölçek kümesi sanal makine örnekleriniz için özel görüntünün nasıl kullanılacağını öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Ölçek kümesi sanal makine örnekleri için özel görüntü kullanma](tutorial-use-custom-image-cli.md)
