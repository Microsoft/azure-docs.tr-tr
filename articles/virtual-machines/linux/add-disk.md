---
title: Azure CLı kullanarak Linux sanal makinesine veri diski ekleme
description: Azure CLı ile Linux VM 'nize kalıcı bir veri diski eklemeyi öğrenin
services: virtual-machines-linux
documentationcenter: ''
author: roygara
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 06/13/2018
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.subservice: disks
ms.openlocfilehash: 5d7ec2cbbc5cc1bf8bdc87d7f82a965b3bc8c267
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037099"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM'ye disk ekleme
Bu makalede, VM 'niz bakım veya yeniden boyutlandırma nedeniyle yeniden sağlansa bile verilerinizi koruyabilmeniz için sanal makinenize kalıcı bir disk nasıl iliştirilebileceğiniz gösterilmektedir.


## <a name="attach-a-new-disk-to-a-vm"></a>VM 'ye yeni bir disk iliştirme

VM 'nize yeni, boş bir veri diski eklemek istiyorsanız, `--new` parametresiyle [az VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) komutunu kullanın. VM 'niz bir kullanılabilirlik Bölgeindeyse, disk VM ile aynı bölgede otomatik olarak oluşturulur. Daha fazla bilgi için bkz. [kullanılabilirlik alanları genel bakış](../../availability-zones/az-overview.md). Aşağıdaki örnek, 50 GB boyutundaki *mydatadisk* adlı bir disk oluşturur:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Var olan bir diski ekleme

Var olan bir diski eklemek için disk KIMLIĞINI bulup KIMLIĞI [az VM disk Attach](/cli/azure/vm/disk?view=azure-cli-latest) komutuna geçirin. Aşağıdaki örnek, *Myresourcegroup*Içinde *mydatadisk* adlı bir disk Için sorgular ve *myvm*adlı VM 'ye iliştirir:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Yeni diski bağlamak için Linux VM 'ye bağlanma

Linux sanal makinenizin kullanabilmesi için yeni diskinizi bölümlemek, biçimlendirmek ve bağlamak üzere sanal makinenize SSH. Daha fazla bilgi için bkz. [Azure’da Linux ile SSH kullanma](mac-create-ssh-keys.md). Aşağıdaki örnek, *mypublicdns.westus.cloudapp.Azure.com* Kullanıcı adı Ile genel DNS girişi olan bir VM 'ye bağlanır *azureuser*:

```bash
ssh azureuser@mypublicdns.westus.cloudapp.azure.com
```

Sanal makinenize bağlandıktan sonra bir disk eklemeye hazırsınız demektir. İlk olarak, `dmesg` kullanarak diski bulun (yeni diskinizi bulmak için kullandığınız yöntem farklılık gösterebilir). Aşağıdaki örnek, *SCSI* disklerinde filtrelemek için dmesg kullanır:

```bash
dmesg | grep SCSI
```

Çıktı aşağıdaki örneğe benzer:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

> [!NOTE]
> Kendi oluşturduğunuz en son Fdisk sürümlerini kullanmanız veya uygulamanız için uygun olması önerilir.

Burada, *SDC* , istediğimiz disktir. Disk boyutu 2 tebibayt (Tib) veya daha büyükse diski `parted`olarak bölümlemek, ardından GPT bölümlendirme kullanmanız gerekir, bu durumda MBR veya GPT bölümlemesini kullanabilirsiniz. MBR bölümlemeyi kullanıyorsanız, `fdisk`kullanabilirsiniz. Bölüm 1 ' de bir birincil disk oluşturun ve diğer varsayılanları kabul edin. Aşağıdaki örnek */dev/SDC*üzerinde `fdisk` işlemini başlatır:

```bash
sudo fdisk /dev/sdc
```

Yeni bir bölüm eklemek için `n` komutunu kullanın. Bu örnekte, birincil bölüm için `p` ve varsayılan değerlerin geri kalanını kabul ediyoruz. Çıktı aşağıdaki örneğe benzer olacaktır:

```bash
Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
Building a new DOS disklabel with disk identifier 0x2a59b123.
Changes will remain in memory only, until you decide to write them.
After that, of course, the previous content won't be recoverable.

Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1): 1
First sector (2048-10485759, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
Using default value 10485759
```

Bölüm tablosunu `p` yazarak yazdırın ve sonra `w` kullanarak tabloyu diske yazın ve çıkın. Çıktı aşağıdaki örneğe benzer şekilde görünmelidir:

```bash
Command (m for help): p

Disk /dev/sdc: 5368 MB, 5368709120 bytes
255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x2a59b123

   Device Boot      Start         End      Blocks   Id  System
/dev/sdc1            2048    10485759     5241856   83  Linux

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Çekirdeği güncelleştirmek için aşağıdaki komutu kullanın:
```
partprobe 
```

Şimdi, `mkfs` komutuyla bölüme bir dosya sistemi yazın. Dosya sistemi türünü ve cihaz adını belirtin. Aşağıdaki örnek, önceki adımlarda oluşturulan */dev/sdc1* bölümünde bir *ext4* FileSystem oluşturur:

```bash
sudo mkfs -t ext4 /dev/sdc1
```

Çıktı aşağıdaki örneğe benzer:

```bash
mke2fs 1.42.9 (4-Feb-2014)
Discarding device blocks: done
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
327680 inodes, 1310464 blocks
65523 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=1342177280
40 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
    32768, 98304, 163840, 229376, 294912, 819200, 884736
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
```

Şimdi, `mkdir`kullanarak dosya sistemini bağlamak için bir dizin oluşturun. Aşağıdaki örnek, */datadrive*dizininde bir dizin oluşturur:

```bash
sudo mkdir /datadrive
```

Dosya sistemini bağlamak için `mount` kullanın. Aşağıdaki örnek */dev/sdc1* bölümünü */datadrive* bağlama noktasına bağlar:

```bash
sudo mount /dev/sdc1 /datadrive
```

Sürücünün yeniden başlatma işleminden sonra otomatik olarak yeniden takıldığından emin olmak için, */etc/fstab* dosyasına eklenmesi gerekir. Ayrıca, */etc/fstab* içinde, yalnızca cihaz adı (örneğin, */dev/sdc1*) yerine, sürücüye başvurmak Için UUID 'Nin (evrensel olarak benzersiz tanımlayıcı) kullanılması önemle tavsiye edilir. İşletim sistemi önyükleme sırasında bir disk hatası algılarsa, UUID 'nin kullanılması, belirli bir konuma bağlanan hatalı diski önler. Daha sonra, kalan veri disklerine aynı cihaz kimlikleri atanır. Yeni sürücünün UUID 'sini bulmak için `blkid` yardımcı programını kullanın:

```bash
sudo blkid
```

Çıktı aşağıdaki örneğe benzer şekilde görünür:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```

> [!NOTE]
> **/Etc/fstab** dosyasının yanlış düzenlenmesiyle, önyüklenemeyen bir sistemle sonuçlanabilir. Emin değilseniz, bu dosyayı doğru şekilde düzenleme hakkında bilgi edinmek için dağıtımın belgelerine bakın. Ayrıca,/etc/fstab dosyasının bir yedeğinin düzenlenmeden önce oluşturulması önerilir.

Sonra, */etc/fstab* dosyasını bir metin düzenleyicisinde şu şekilde açın:

```bash
sudo vi /etc/fstab
```

Bu örnekte, önceki adımlarda oluşturulan */dev/sdc1* cihazının UUID değerini ve */datadrive*bağlama noktasını kullanın. */Etc/fstab* dosyasının sonuna aşağıdaki satırı ekleyin:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

> [!NOTE]
> Daha sonra fstab düzenlemeden bir veri diskinin kaldırılması, sanal makinenin önyüklenememesine neden olabilir. Çoğu dağıtım, *nofail* ve/veya *nobootwaıt* fstab seçeneklerini sağlar. Bu seçenekler, disk önyükleme zamanında takılamazsa bile sistemin önyüklenmesine izin verir. Bu parametrelerle ilgili daha fazla bilgi için, dağıtım belgelerine bakın.
>
> *NOFAIL* seçeneği, dosya sistemi bozuk olsa da veya önyükleme zamanında disk mevcut olmasa bile VM 'nin başlamasını sağlar. Bu seçenek olmadan, [FSTAB hataları nedeniyle LINUX sanal MAKINESINE SSH yapılamıyor](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) bölümünde açıklandığı gibi davranışla karşılaşabilirsiniz
>
> Azure VM seri konsolu, fstab değiştirme bir önyükleme hatası ile sonuçlanmışsa sanal makinenize konsol erişimi için kullanılabilir. [Seri konsol belgelerinde](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)daha fazla ayrıntı bulunabilir.

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 'da Linux için KıRPMA/eşlemeyi kaldır
Bazı Linux çekirdekler, diskteki kullanılmayan blokları atmak için kesme/eşlemeyi Kaldır işlemlerini destekler. Bu özellik, Azure 'un silinen sayfaların artık geçerli olmadığını ve atılamayacağını, daha sonra da büyük dosyalar oluşturup bunları silerek paradan tasarruf etmesini bildirmek için standart depolamada yararlıdır.

Linux sanal makinenizde KıRPMA desteğini etkinleştirmenin iki yolu vardır. Her zamanki gibi, önerilen yaklaşım için dağıtıma başvurun:

* */Etc/fstab*' de `discard` bağlama seçeneğini kullanın, örneğin:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,discard   1   2
    ```
* Bazı durumlarda `discard` seçeneğinde performans etkileri olabilir. Alternatif olarak, komut satırından `fstrim` komutunu el ile çalıştırabilir veya bunları düzenli olarak çalıştırmak için crontab 'ize ekleyebilirsiniz:

    **Ubuntu**

    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```

    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="troubleshooting"></a>Sorun giderme

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Linux sanal makinenizin doğru bir şekilde yapılandırıldığından emin olmak için [Linux makinenizin performans önerilerinizi iyileştirin](optimization.md) ' i gözden geçirin.
* Ek diskler ekleyerek depolama kapasitenizi genişletin ve ek performans için [RAID 'i yapılandırın](configure-raid.md) .
