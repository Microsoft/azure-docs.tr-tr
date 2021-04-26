---
title: Öğretici - Azure CLI ile Azure disklerini yönetme
description: Bu öğreticide, Azure CLI kullanarak sanal makineler için Azure diskleri oluşturup yönetmeyi öğrenirsiniz
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: 56e804bc0d479f09ef2900c42361fbd24eed1d98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765962"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>Öğretici - Azure CLI ile Azure disklerini yönetme

Azure sanal makineleri (VM) işletim sistemini, uygulamalarını ve verilerini depolamak için diskleri kullanır. Bir VM oluşturduğunuzda, beklenen iş yüküne uygun bir disk boyutu ve yapılandırma seçmek önemlidir. Bu öğreticide, VM disklerini dağıtma ve yönetme işlemleri gösterilir. Şunları öğreneceksiniz:

> [!div class="checklist"]
> * İşletim sistemi diskleri ve geçici diskler
> * Veri diskleri
> * Standart ve Premium diskler
> * Disk performansı
> * Veri disklerini ekleme ve hazırlama
> * Disk anlık görüntüleri


## <a name="default-azure-disks"></a>Varsayılan Azure diskleri

Azure sanal makinesi oluşturulduğunda, sanal makineye otomatik olarak iki disk eklenir.

**İşletim sistemi diski** - İşletim sistemi diskleri 2 TB'a kadar boyutlandırılabilir ve VM'nin işletim sistemini barındırır. İşletim sistemi diski varsayılan olarak */dev/sda* etiketine sahiptir. İşletim sistemi diskinin yapılandırmasını önbelleğe alan disk, işletim sistemi performansı için iyileştirilir. Bu yapılandırma nedeniyle işletim sistemi diski uygulamalar veya veriler için **kullanılmamalıdır**. Uygulamalar ve veriler için, bu öğreticide daha sonra ayrıntılı olarak açıklanan veri disklerini kullanın.

**Geçici disk** - Geçici diskler, VM ile aynı Azure ana bilgisayarında bulunan bir katı hal sürücüsü kullanır. Geçici diskler yüksek performansa sahiptir ve geçici veri işleme gibi işlemler için kullanılabilir. Ancak VM yeni bir konağa taşındığında, geçici diskte depolanan tüm veriler kaldırılır. Geçici diskin boyutu, VM boyutu tarafından belirlenilir. Geçici diskler */dev/sdb* etiketine ve */mnt* bağlama noktasına sahiptir.

## <a name="azure-data-disks"></a>Azure veri diskleri

Uygulamaları yüklemek ve verileri depolamak için başka veri diskleri eklenebilir. Dayanıklı ve duyarlı veri depolamanın istenildiği her koşulda veri diskleri kullanılmalıdır. Sanal makinenin boyutu, bir VM’ye kaç veri diskinin eklenebileceğini belirler.

## <a name="vm-disk-types"></a>VM disk türleri

Azure iki disk türü sağlar.

**Standart diskler** - HDD’ler ile desteklenir ve uygun maliyetli bir depolama sağlarken iyi bir performans da sunar. Standart diskler, uygun maliyetli bir geliştirme ve iş yükü testi için idealdir.

**Premium diskler** -SSD tabanlı, yüksek performanslı ve düşük gecikmeli disk tarafından desteklenir. Üretim iş yükü çalıştıran VM'ler için son derece uygundur. [Boyut adında](../vm-naming-conventions.md) **S** olan VM boyutları genellikle Premium depolamayı destekler. Örneğin, DS serisi, DSv2 serisi, GS serisi ve FS Serisi VM 'Ler Premium depolamayı destekler. Disk boyutu seçilirken boyutun değeri sonraki türe yuvarlanır. Örneğin, disk boyutu 64 GB 'den büyükse ancak 128 GB 'den küçükse, disk türü P10 olur. 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Standart depolamanın aksine bir Premium Depolama diski sağladığınızda, bu diskin kapasitesi, ıOPS ve aktarım hızı garanti edilir. Örneğin, bir P50 diski oluşturursanız, Azure bu disk için 4.095 GB depolama kapasitesi, 7.500 ıOPS ve 250 MB/sn aktarım hızı sağlar. Uygulamanız, kapasite ve performansın tümünü veya bir bölümünü kullanabilir. Premium SSD diskler, önceki tabloda% 99,9 ' de açıklanan düşük tek basamaklı milisaniyelik gecikme süreleri ve hedef ıOPS ve aktarım hızı sağlamak üzere tasarlanmıştır.

Yukarıdaki tablo, disk başına maksimum IOPS tanımlamış olsa da, daha yüksek düzeyde performansa birden çok veri diskini bölümleyerek ulaşılabilir. Örneğin, Standard_GS5 VM’ye 64 veri diski eklenebilir. Bu disklerin her biri P30 olarak boyutlandırılırsa, en fazla 80.000 IOPS’ye ulaşılabilir. VM başına maksimum IOPS hakkında ayrıntılı bilgi için bkz. [VM türleri ve boyutları](../sizes.md).

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz, ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır.

Cloud Shell açmak için, bir kod bloğunun sağ üst köşesinden **dene** ' yi seçin. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/bash) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="create-and-attach-disks"></a>Disk oluşturma ve ekleme

Veri diskleri oluşturulabilir ve VM oluşturulduğunda veya varolan bir VM’ye eklenebilir.

### <a name="attach-disk-at-vm-creation"></a>VM oluşturulurken disk ekleme

[az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun.

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

[az vm create](/cli/azure/vm#az_vm_create) komutuyla bir VM oluşturun. Aşağıdaki örnek, *myVM* adlı bir VM oluşturur, *azureuser* adlı bir kullanıcı hesabı ekler ve yoksa SSH anahtarlarını oluşturur. `--datadisk-sizes-gb` bağımsız değişkeni, ek bir disk oluşturulması ve sanal makineye eklenmesi gerektiğini belirtmek için kullanılır. Birden fazla disk oluşturmak ve eklemek için disk boyutu değerlerinin boşlukla ayrılmış bir listesini kullanın. Aşağıdaki örnekte her iki veri diskinin boyutu 128 GB olan bir VM oluşturulur. Disk boyutları 128 GB olduğundan her disk de P10 (disk başına en fazla 500 IOPS sağlar) olarak yapılandırılabilir.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>Varolan VM’ye disk ekleme

Yeni bir veri diski oluşturmak ve bunu varolan bir sanal makineye eklemek için [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach) komutunu kullanın. Aşağıdaki örnek 128 gigabayt boyutunda bir premium disk oluşturur ve bunu son adımda oluşturulan VM’ye ekler.

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>Veri disklerini hazırlama

Bir disk sanal makineye eklendikten sonra, diskin kullanılması için işletim sisteminin yapılandırılması gerekir. Aşağıdaki örnekte bir diskin el ile nasıl yapılandırılacağı gösterilmektedir. Bu işlem aynı zamanda cloud-init kullanılarak otomatikleştirilebilir. Bu konu [sonraki öğreticide](./tutorial-automate-vm-deployment.md) açıklanacak.


Sanal makine ile bir SSH bağlantısı oluşturun. Örnek IP adresini, sanal makinenin genel IP adresiyle değiştirin.

```console
ssh 10.101.10.10
```

`parted` ile diski bölümlendirin.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

`mkfs` komutu kullanarak dosya sistemini bölüme yazın. `partprobe`İşletim sisteminin değişikliğin farkında olması için kullanın.

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

İşletim sisteminde erişilebilir olması için yeni diski bağlayın.

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

Diske artık, `/datadrive` komutu çalıştırılarak doğrulanamayan bağlama noktası üzerinden erişilebilir `df -h` .

```bash
df -h | grep -i "sd"
```

Çıktı, üzerine takılan yeni sürücüyü gösterir `/datadrive` .

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

Bir yeniden başlatma işleminden sonra sürücünün yeniden bağlanmasını sağlamak için sürücü, */etc/fstab* dosyasına eklenmelidir. Bunu yapmak için `blkid` yardımcı programıyla diskin UUID’sini alın.

```bash
sudo -i blkid
```

Bu durumda çıktı sürücünün UUID’sini, `/dev/sdc1`, görüntüler.

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> **/Etc/fstab** dosyasının yanlış düzenlenmesiyle, önyüklenemeyen bir sistemle sonuçlanabilir. Emin değilseniz, bu dosyayı doğru düzenleme hakkındaki bilgiler için dağıtımın belgelerine bakın. Ayrıca,/etc/fstab dosyasının bir yedeğinin düzenlenmeden önce oluşturulması önerilir.

`/etc/fstab`Dosyayı bir metin düzenleyicisinde şu şekilde açın:

```bash
sudo nano /etc/fstab
```

*/Etc/fstab* dosyasına benzer bir satır ekleyerek UUID değerini kendi değerlerinizle değiştirin.

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

Dosyayı düzenleme işiniz bittiğinde, `Ctrl+O` dosyayı yazmak ve düzenleyiciden çıkmak için öğesini kullanın `Ctrl+X` .

Artık disk yapılandırıldığında göre SSH oturumunu kapatabilirsiniz.

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>Disk anlık görüntüsü al

Bir disk anlık görüntüsü aldığınızda, Azure diskin belirli bir noktadaki salt okunur kopyasını oluşturur. Azure VM anlık görüntüleri, yapılandırma değişiklikleri yapmadan önce VM’nin durumunu hızla kaydetmenize yardımcı olur. Bir sorun veya hata durumunda VM, anlık görüntü kullanılarak geri yüklenebilir. VM birden fazla disk içeriyorsa her bir disk için diğerlerinden bağımsız olarak bir anlık görüntü alınır. Uygulamayla tutarlı yedekler almak için disk anlık görüntülerini almadan önce VM’yi durdurmayı göz önünde bulundurun. Bunun yerine VM çalışırken otomatik olarak yedeklemeyi sağlayan [Azure Backup hizmetini](../../backup/index.yml) kullanabilirsiniz.

### <a name="create-snapshot"></a>Anlık görüntü oluşturma

Anlık görüntü oluşturmadan önce diskin KIMLIĞI veya adı gerekir. Disk KIMLIĞINI [göstermek için az VM Show](/cli/azure/vm#az_vm_show) ' i kullanın. Bu örnekte sonraki adımda kullanılabilmesi için disk kimliği bir değişken içinde saklanır.

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

Artık KIMLIĞINIZ olduğuna göre, diskin anlık görüntüsünü oluşturmak için [az Snapshot Create](/cli/azure/snapshot#az_snapshot_create) kullanın.

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>Anlık görüntüden disk oluşturma

Bu anlık görüntü daha sonra, sanal makineyi yeniden oluşturmak için kullanılabilecek [az disk Create](/cli/azure/disk#az_disk_create)kullanılarak bir diske dönüştürülebilir.

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>Sanal makineyi anlık görüntüden geri yükleme

Sanal makine kurtarmayı göstermek için [az VM Delete](/cli/azure/vm#az_vm_delete)kullanarak var olan sanal makineyi silin.

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

Anlık görüntü diskinden yeni bir sanal makine oluşturun.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>Veri diskini yeniden ekleme

Tüm veri disklerinin sanal makineye yeniden eklenmesi gerekir.

[Az disk List](/cli/azure/disk#az_disk_list) komutunu kullanarak veri diski adını bulun. Bu örnek `datadisk` , bir sonraki adımda kullanılan adlı bir değişkende diskin adını koyar.

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

Diski eklemek için [az vm disk attach](/cli/azure/vm/disk#az_vm_disk_attach) komutunu kullanın.

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunun gibi VM disk konularını öğrendiniz:

> [!div class="checklist"]
> * İşletim sistemi diskleri ve geçici diskler
> * Veri diskleri
> * Standart ve Premium diskler
> * Disk performansı
> * Veri disklerini ekleme ve hazırlama
> * Disk anlık görüntüleri

VM yapılandırmasını otomatikleştirme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [VM yapılandırmasını otomatikleştirme](./tutorial-automate-vm-deployment.md)
