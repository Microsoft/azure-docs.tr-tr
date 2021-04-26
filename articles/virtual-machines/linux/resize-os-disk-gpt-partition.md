---
title: GPT bölümü olan bir işletim sistemi diskini yeniden boyutlandırma
description: Bu makalede, Linux 'ta GUID bölümleme tablosu (GPT) bölümünün bulunduğu bir işletim sistemi diskini yeniden boyutlandırmayla ilgili yönergeler sağlanır.
services: virtual-machines
ms.topic: article
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 0db79728bbb963aa360743afc70aecc213bfb7bc
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011690"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>GPT bölümü olan bir işletim sistemi diskini yeniden boyutlandırma

> [!NOTE]
> Bu makale yalnızca bir GUID bölümleme tablosu (GPT) bölümü olan işletim sistemi diskleri için geçerlidir.

Bu makalede, Linux 'ta GPT bölümü olan bir işletim sistemi diskinin boyutunu artırma işlemi açıklanır. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>İşletim sistemi diskinde MBR veya GPT bölümü olup olmadığını belirler

`parted`Disk bölümünün ana önyükleme kaydı (MBR) bölümüyle veya BIR GPT bölümüyle oluşturulmuş olup olmadığını belirlemek için komutunu kullanın.

### <a name="mbr-partition"></a>MBR bölümü

Aşağıdaki çıktıda, **bölüm tablosunda** **Msdos** değeri gösterilmektedir. Bu değer bir MBR bölümünü tanımlar.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT bölümü

Aşağıdaki çıktıda, **bölüm tablosu** **GPT** değerini gösterir. Bu değer bir GPT bölümünü tanımlar.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Sanal makinenizde (VM) işletim sistemi diskinizde bir GPT bölümü varsa, işletim sistemi diskinin boyutunu artırın.

## <a name="increase-the-size-of-the-os-disk"></a>İşletim sistemi diskinin boyutunu artırın

Aşağıdaki yönergeler, Linux onaylı dağıtımlar için geçerlidir.

> [!NOTE]
> Devam etmeden önce, VM 'nizin yedek bir kopyasını oluşturun veya işletim sistemi diskinizin bir anlık görüntüsünü alın.

### <a name="ubuntu"></a>Ubuntu

Ubuntu 16 ' da işletim sistemi diskinin boyutunu artırmak için. *x* ve 18. *x*:

1. VM'yi durdurun.
1. Portaldan işletim sistemi diskinin boyutunu artırın.
1. VM 'yi yeniden başlatın ve ardından VM 'de bir **kök** Kullanıcı olarak oturum açın.
1. İşletim sistemi diskinin artık daha fazla dosya sistemi boyutunu görüntülediğini doğrulayın.

Aşağıdaki örnekte, işletim sistemi diski portaldan 100 GB 'a yeniden boyutlandırıldı. **/Dev/sda1** dosya sistemi **/** artık 97 GB görüntülüyor.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

SUSE 12 SP4'TE işletim sistemi diskinin boyutunu artırmak için, SAP için SUSE SLES 12, SUSE SLES 15 ve SAP için SUSE SLES 15:

1. VM'yi durdurun.
1. Portaldan işletim sistemi diskinin boyutunu artırın.
1. VM’yi yeniden başlatın.

VM yeniden başlatıldığında şu adımları uygulayın:

1. Şu komutu kullanarak sanal makinenize **kök** Kullanıcı olarak erişin:

   ```
   # sudo -i
   ```

1. Bölümü yeniden boyutlandırmak için kullanacağınız **growpart** paketini yüklemek için aşağıdaki komutu kullanın:

   ```
   # zypper install growpart
   ```

1. `lsblk`Dosya sisteminin köküne takılan bölümü bulmak için komutunu kullanın ( **/** ). Bu durumda, **sda** cihaz Bölüm 4 ' ün bağlı olduğunu görüyoruz **/** :

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. `growpart`Önceki adımda belirlenen komutu ve bölüm numarasını kullanarak gerekli bölümü yeniden boyutlandırın:

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. `lsblk`Bölümün arttırılmadığını denetlemek için komutu yeniden çalıştırın.

   Aşağıdaki çıktıda **/dev/sda4** bölümünün 46,5 GB olarak yeniden boyutlandırıldığı gösterilmektedir:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Komutunu bayrağıyla kullanarak işletim sistemi diskindeki dosya sisteminin türünü belirler `lsblk` `-f` :

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. Dosya sistemi türüne göre, dosya sistemini yeniden boyutlandırmak için uygun komutları kullanın.
   
   **XFS** için şu komutu kullanın:
   
   ```
   #xfs_growfs /
   ```
   
   Örnek çıktı:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   **Ext4** için şu komutu kullanın:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Bu komutu kullanarak **df** için daha fazla dosya sistemi boyutunu doğrulayın:
   
   ```
   #df -Thl
   ```
   
   Örnek çıktı:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   Yukarıdaki örnekte, işletim sistemi diski için dosya sistemi boyutunun arttığını görebiliriz.

### <a name="rhel-with-lvm"></a>LVM ile RHEL

1. Şu komutu kullanarak sanal makinenize **kök** Kullanıcı olarak erişin:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. `lsblk`Dosya sisteminin köküne hangi mantıksal birimin (LV) bağlı olduğunu () öğrenmek için komutunu kullanın **/** . Bu durumda, **rootvg-rootlv** 'nin bağlı olduğunu görüyoruz **/** . Başka bir dosya sistemi istiyorsanız, bu makale boyunca LV ve bağlama noktasını yerine koyun.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Kök bölümü içeren LVM birim grubunda (VG) boş alan olup olmadığını denetleyin. Boş alan varsa, 12. adıma atlayın.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   Bu örnekte, satır **ÜCRETSIZ PE/boyut** , birim grubunda 38,02 GB boş alan olduğunu gösterir. Birim grubuna alan eklemeden önce diski yeniden boyutlandırmanıza gerek yoktur.

1. RHEL 7 ' de işletim sistemi diskinin boyutunu artırmak için. LVM ile *x* :

   1. VM'yi durdurun.
   1. Portaldan işletim sistemi diskinin boyutunu artırın.
   1. VM’yi başlatın.

1. VM yeniden başlatıldığında, aşağıdaki adımı izleyin:

   - OS diskinin boyutunu ve GPT disk düzenleri için GDisk işleyicisini artırmak için gereken **growpart** komutunu sağlamak üzere **Cloud-utils-growpart** paketini yüklemek için Bu paketler çoğu Market görüntülerine önceden yüklenir.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Komutunu kullanarak, hangi disk ve bölümün, **rootvg** adlı birim grubunda LVM fiziksel birimini veya BIRIMLERINI (BD) bulundurduğunu saptayın `pvscan` . Köşeli ayraçlar (**[** ve **]**) arasında listelenen boyut ve boş alanı aklınızda bırakın.

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Kullanarak bölümün boyutunu doğrulayın `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Bu BD içeren bölümü `growpart` , cihaz adını ve bölüm numarasını kullanarak genişletin. Bu işlem, cihazdaki tüm boş alanı kullanmak için belirtilen bölümü genişletir.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Komutu yeniden kullanarak, bölümün beklenen boyuta yeniden boyutlandırıldığından emin olun `lsblk` . Örnek **sda4** , 63 gb 'den 95 GB 'ye değiştiği konusunda dikkat edin.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Yeni genişletilen bölümün geri kalanını kullanmak için BD ' i genişletin:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. BD 'in yeni boyutunun beklenen boyut olduğunu ve özgün **[boyut/serbest]** değerleriyle karşılaştırarak emin olun:

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. İstenen mantıksal birimi (LV) istediğiniz miktara göre genişletin. Miktarın birim grubundaki tüm boş alan olması gerekmez. Aşağıdaki örnekte, **/dev/mapper/rootvg-rootlv** 2 GB Ila 12 GB (10 GB 'lık artış) olarak yeniden boyutlandırılır. Bu komut ayrıca dosya sistemini yeniden boyutlandıracaktır.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Örnek çıktı:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. `lvresize`Komut, LV içindeki dosya sistemi için uygun yeniden boyutlandırma komutunu otomatik olarak çağırır. Bu komutu kullanarak, bağlı olan **/dev/mapper/rootvg-rootlv**'nin, **/** Artırılmış bir dosya sistemi boyutuna sahip olup olmadığını denetleyin:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Örnek çıktı:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Diğer herhangi bir mantıksal birimi yeniden boyutlandırmak için aynı yordamı kullanmak için, adım 12 ' de LV adı ' nı değiştirin.


### <a name="rhel-raw"></a>RHEL RAW

Bir RHEL RAW bölümünde işletim sistemi diskinin boyutunu artırmak için:

1. VM'yi durdurun.
1. Portaldan işletim sistemi diskinin boyutunu artırın.
1. VM’yi başlatın.

VM yeniden başlatıldığında şu adımları uygulayın:

1. Aşağıdaki komutu kullanarak sanal makinenize **kök** Kullanıcı olarak erişin:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. VM yeniden başlatıldığında, aşağıdaki adımı izleyin:

   - OS diskinin boyutunu ve GPT disk düzenleri için GDisk işleyicisini artırmak için gereken **growpart** komutunu sağlamak üzere **Cloud-utils-growpart** paketini yüklemek için Bu paket, Market görüntülerinin çoğunda önceden yüklenir.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Kök () bölümünü tutan bölüm ve dosya sistemi türünü doğrulamak için **lsblk-f** komutunu kullanın **/** :

   ```bash
   [root@vm-dd-cent7 ~]# lsblk -f
   NAME    FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1  xfs          2a7bb59d-6a71-4841-a3c6-cba23413a5d2 /boot
   ├─sda2  xfs          148be922-e3ec-43b5-8705-69786b522b05 /
   ├─sda14
   └─sda15 vfat         788D-DC65                            /boot/efi
   sdb
   └─sdb1  ext4         923f51ff-acbd-4b91-b01b-c56140920098 /mnt/resource
   ```

1. Doğrulama için, SDA diskinin bölüm tablosunu **GDisk** ile listeleyerek başlatın. Bu örnekte, 29,0 GiB 'de Bölüm 2 ile 48 GB bir disk görüyoruz. Disk, Azure portal 30 GB ile 48 GB arasında genişletilmiştir.

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 62914526
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 6076 sectors (3.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048        62912511   29.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. **Growpart** komutunu kullanarak, bu örnekte sda2 for root bölümünü genişletin. Bu komutun kullanılması, disk üzerindeki tüm bitişik alanı kullanmak için bölümü genişletir.

   ```bash
   [root@vm-dd-cent7 ~]# growpart /dev/sda 2
   CHANGED: partition=2 start=2050048 old: size=60862464 end=62912512 new: size=98613214 end=100663262
   ```

1. Şimdi yeni bölüm tablosunu **GDisk** ile yeniden yazdırın.  Bölüm 2 ' nin 47,0 GiB 'ye genişlediğine dikkat edin:

   ```bash
   [root@vm-dd-cent7 ~]# gdisk -l /dev/sda
   GPT fdisk (gdisk) version 0.8.10

   Partition table scan:
   MBR: protective
   BSD: not present
   APM: not present
   GPT: present

   Found valid GPT with protective MBR; using GPT.
   Disk /dev/sda: 100663296 sectors, 48.0 GiB
   Logical sector size: 512 bytes
   Disk identifier (GUID): 78CDF84D-9C8E-4B9F-8978-8C496A1BEC83
   Partition table holds up to 128 entries
   First usable sector is 34, last usable sector is 100663262
   Partitions will be aligned on 2048-sector boundaries
   Total free space is 4062 sectors (2.0 MiB)

   Number  Start (sector)    End (sector)  Size       Code  Name
      1         1026048         2050047   500.0 MiB   0700
      2         2050048       100663261   47.0 GiB    0700
   14            2048           10239   4.0 MiB     EF02
   15           10240         1024000   495.0 MiB   EF00  EFI System Partition
   ```

1. Bölümdeki FileSystem 'ı, standart Market tarafından oluşturulan bir RedHat sistemine uygun olan **xfs_growfs** ile genişletin:

   ```bash
   [root@vm-dd-cent7 ~]# xfs_growfs /
   meta-data=/dev/sda2              isize=512    agcount=4, agsize=1901952 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=7607808, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3714, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7607808 to 12326651
   ```

1. Yeni boyutun **df** komutu kullanılarak yansıtıldığını doğrulayın:

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
