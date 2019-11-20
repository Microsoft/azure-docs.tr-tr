---
title: Linux VM 'ye veri diski iliştirme
description: Bir Linux sanal makinesine yeni veya mevcut veri diski eklemek için portalını kullanın.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 78604a4f6fd5a6bcd21d0adc80c1c60278068836
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037058"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Bir Linux VM 'sine veri diski eklemek için portalı kullanma 
Bu makalede, Azure portal aracılığıyla bir Linux sanal makinesine hem yeni hem de mevcut diskleri nasıl ekleyebileceğiniz gösterilmektedir. Ayrıca [, Azure Portal bir WINDOWS sanal makinesine veri diski ekleyebilirsiniz](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

VM 'nize disk iliştirmadan önce şu ipuçlarını gözden geçirin:

* Sanal makinenin boyutu, ekleyebileceğiniz veri disklerinin sayısını denetler. Ayrıntılar için bkz. [sanal makineler Için boyutlar](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Sanal makinelere eklenen diskler aslında. vhd dosyaları Azure 'da depolanır. Ayrıntılar için bkz. [yönetilen disklere giriş](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Diski iliştirdikten sonra, [Yeni diski bağlamak Için LINUX VM 'ye bağlanmanız](#connect-to-the-linux-vm-to-mount-the-new-disk)gerekir.


## <a name="find-the-virtual-machine"></a>Sanal makineyi bul
1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Sol taraftaki menüden **sanal makineler**' e tıklayın.
3. Listeden sanal makineyi seçin.
4. Sanal makineler sayfasında, **temel**bileşenler ' de **diskler**' e tıklayın.
   
    ![Disk ayarlarını aç](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Yeni Disk Ekle

1. **Diskler** bölmesinde **+ veri diski Ekle**' ye tıklayın.
2. **Ad** ' ın açılan menüsüne tıklayın ve **disk oluştur**' u seçin:

    ![Azure yönetilen diski oluştur](./media/attach-disk-portal/create-new-md.png)

3. Yönetilen diskiniz için bir ad girin. Varsayılan ayarları gözden geçirin, gerekirse güncelleştirin ve ardından **Oluştur**' a tıklayın.
   
   ![Disk ayarlarını gözden geçirme](./media/attach-disk-portal/create-new-md-settings.png)

4. Yönetilen diski oluşturmak ve VM yapılandırmasını güncelleştirmek için **Kaydet** ' e tıklayın:

   ![Yeni Azure yönetilen diskini Kaydet](./media/attach-disk-portal/confirm-create-new-md.png)

5. Azure diski oluşturduktan ve sanal makineye iliştirdikten sonra, yeni disk, **veri diskleri**altındaki sanal makinenin disk ayarlarında listelenir. Yönetilen diskler üst düzey bir kaynak olduğundan, disk kaynak grubunun kökünde görünür:

   ![Kaynak grubunda Azure yönetilen disk](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Var olan bir diski ekleme
1. **Diskler** bölmesinde **+ veri diski Ekle**' ye tıklayın.
2. Azure aboneliğiniz tarafından erişilebilen mevcut yönetilen disklerin bir listesini görüntülemek için **ad** ' ın açılan menüsüne tıklayın. İliştirilecek yönetilen diski seçin:

   ![Mevcut Azure yönetilen diski Ekle](./media/attach-disk-portal/select-existing-md.png)

3. Mevcut yönetilen diski eklemek ve VM yapılandırmasını güncelleştirmek için **Kaydet** ' e tıklayın:
   
   ![Azure yönetilen disk güncelleştirmelerini Kaydet](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Azure, diski sanal makineye iliştirdikten sonra, sanal makinenin disk ayarlarında **veri diskleri**altında listelenir.

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

Burada, *SDC* , istediğimiz disktir. 

### <a name="partition-a-new-disk"></a>Yeni bir disk bölümleme
Veri içeren mevcut bir disk kullanıyorsanız, diski bağlamak için atlayın. Yeni bir disk iliştirmeye çalışıyorsanız, diski bölümlemek gerekir.

> [!NOTE]
> Kendi oluşturduğunuz en son Fdisk sürümlerini kullanmanız veya uygulamanız için uygun olması önerilir.

`fdisk` ile diski bölümlendirin. Disk boyutu 2 tebibayt (Tib) veya daha büyükse GPT bölümlendirme kullanmanız gerekir, GPT bölümlendirme gerçekleştirmek için `parted` kullanabilirsiniz. Disk boyutu 2TiB altındaysa, MBR veya GPT bölümlemesini kullanabilirsiniz. Bölüm 1 ' de bir birincil disk oluşturun ve diğer varsayılanları kabul edin. Aşağıdaki örnek */dev/SDC*üzerinde `fdisk` işlemini başlatır:

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
### <a name="mount-the-disk"></a>Diski bağlama
`mkdir`kullanarak dosya sistemini bağlamak için bir dizin oluşturun. Aşağıdaki örnek, */datadrive*dizininde bir dizin oluşturur:

```bash
sudo mkdir /datadrive
```

Dosya sistemini bağlamak için `mount` kullanın. Aşağıdaki örnek */dev/sdc1* bölümünü */datadrive* bağlama noktasına bağlar:

```bash
sudo mount /dev/sdc1 /datadrive
```

Sürücünün yeniden başlatma işleminden sonra otomatik olarak yeniden takıldığından emin olmak için, */etc/fstab* dosyasına eklenmesi gerekir. Ayrıca, */etc/fstab* içinde, yalnızca cihaz adı (örneğin, */dev/sdc1*) yerine, sürücüye başvurmak Için UUID 'Nin (evrensel olarak benzersiz tanımlayıcı) kullanılması önemle tavsiye edilir. İşletim sistemi önyükleme sırasında bir disk hatası algılarsa, UUID 'nin kullanılması, belirli bir konuma bağlanan hatalı diski önler. Daha sonra, kalan veri disklerine aynı cihaz kimlikleri atanır. Yeni sürücünün UUID 'sini bulmak için `blkid` yardımcı programını kullanın:

```bash
sudo -i blkid
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
İşiniz bittiğinde, */etc/fstab* dosyasını kaydedin ve sistemi yeniden başlatın.
> [!NOTE]
> Daha sonra fstab düzenlemeden bir veri diskinin kaldırılması, sanal makinenin önyüklenememesine neden olabilir. Çoğu dağıtım, *nofail* ve/veya *nobootwaıt* fstab seçeneklerini sağlar. Bu seçenekler, disk önyükleme zamanında takılamazsa bile sistemin önyüklenmesine izin verir. Bu parametrelerle ilgili daha fazla bilgi için, dağıtım belgelerine bakın.
> 
> *NOFAIL* seçeneği, dosya sistemi bozuk olsa da veya önyükleme zamanında disk mevcut olmasa bile VM 'nin başlamasını sağlar. Bu seçenek olmadan, [FSTAB hataları nedeniyle LINUX sanal MAKINESINE SSH yapılamıyor](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting/) bölümünde açıklandığı gibi davranışla karşılaşabilirsiniz

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

## <a name="next-steps"></a>Sonraki adımlar
Ayrıca, Azure CLı kullanarak [bir veri diski](add-disk.md) ekleyebilirsiniz.
