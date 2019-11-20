---
title: Dosya sistemi hataları nedeniyle Linux sanal makinesi sorunlarını giderme | Microsoft Docs
description: Linux VM 'nin neden başlayabileceğini ve sorunun nasıl çözüleceğini açıklar.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: a47dc1032115f8bcae0c7bdc37c84ab3b68ec4a8
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432299"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Dosya sistemi hataları nedeniyle Linux sanal makinesi sorunlarını giderme

Secure Shell (SSH) kullanarak bir Azure Linux sanal makinesine (VM) bağlanamazsınız. [Azure Portal](https://portal.azure.com/)' de önyükleme Tanılama özelliğini çalıştırdığınızda aşağıdaki örneklere benzeyen günlük girdileri görürsünüz.

## <a name="examples"></a>Örnekler

Olası hataların örnekleri aşağıda verilmiştir.

### <a name="example-1"></a>Örnek 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Örnek 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Örnek 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Örnek 4 

Bu örnek, temiz bir fsck nedeniyle oluşur. Bu durumda, VM 'ye bağlı ek veri diskleri de vardır (/dev/sdc1 ve/dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

Bu sorun, dosya sistemi sorunsuz veya depolamayla ilgili sorunları kapatmadığında ortaya çıkabilir. Sorunlar, donanım veya yazılım hatalarını, sürücü veya programlarla ilgili sorunları, yazma hatalarını vb. içerir. Kritik verilerin yedeklenmesi her zaman önemlidir. Bu makalede açıklanan araçlar dosya sistemlerinin kurtarılmasına yardımcı olabilir, ancak veri kaybı devam edebilir.

Linux 'ta kullanılabilir çeşitli dosya sistemi denetleyicileri vardır. Azure 'daki dağıtımlar için en yaygın olarak şunlardır: [fsck](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)ve [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Çözünürlük

Bu sorunu çözmek için, [seri konsolunu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) kullanarak VM 'yi acil modda önyükleyin ve dosya sistemini onarmak için bu aracı kullanın. Seri konsol VM 'niz üzerinde etkinleştirilmemişse veya işe yaramazsa, bu makaledeki [VM 'yi çevrimdışı ortamda onarma](#repair-the-vm-offline) bölümüne bakın.

## <a name="use-the-serial-console"></a>Seri konsolu kullanma

1. Seri konsoluna bağlanın.

   > [!Note]
   > Linux için seri konsol kullanma hakkında daha fazla bilgi için bkz.:
   > * [GRUB ve tek kullanıcı moduna erişmek için seri konsol kullanma](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [SysRq ve NMI çağrıları için seri konsol kullan](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Güç simgesi düğmesini seçin ve ardından VM 'yi yeniden Başlat ' ı seçin. (Seri konsol etkinleştirilmemişse veya başarıyla bağlanmayacaksa, düğmesini görmezsiniz.)

   ![GÖRÜNTÜYLE](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. VM 'yi acil modda önyükleyin.

4. Acil durum modunda oturum açmak için kök hesabınızın parolasını girin.

5. Dosya sistemindeki hataları algılamak için xfs_repair ile-n seçeneğini kullanın. Aşağıdaki örnekte, sistem bölümünün/dev/sda1. olduğunu varsaytık VM 'niz için uygun değer ile değiştirin:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Dosya sistemini onarmak için aşağıdaki komutu çalıştırın:

   ```
   xfs_repair /dev/sda1
   ```

7. Hata iletisini alırsanız "hata: FileSystem 'ın yeniden yürütülmesi gereken bir günlükte değerli meta veri değişiklikleri vardır", geçici bir dizin oluşturup dosya sistemini bağlama:

   ```
   mkdir /temp
   mount /dev/sda2 /temp
   ```

8. Disk takılamazsa,-L seçeneğiyle xfs_repair komutunu çalıştırın (günlük sıfırlama işlemi zorla):

   ```
   xfs_repair /dev/sda2 -L
   ```

9. Sonra, dosya sistemini bağlamaya çalışın. Disk başarıyla bağlanmışsa, şu çıktıyı alırsınız:
 
   ```
   XFS (sda2): Mounting V1 Filesystem
   XFS (sda2): Ending clean mount
   ```

10. VM 'yi yeniden başlatın ve sorunun çözümlenip çözümlenmediğini denetleyin.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>VM 'yi çevrimdışı onarma

1. VM 'nin sistem diskini bir kurtarma VM 'sine (çalışmakta olan herhangi bir Linux VM) veri diski olarak ekleyin. Bunu yapmak için [CLI komutlarını](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) kullanabilir veya [VM onarım komutlarını](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)kullanarak kurtarma VM 'sinin kurulumunu otomatik hale getirebilirsiniz.

2. Eklediğiniz sistem diskinin sürücü etiketini bulun. Bu durumda, eklediğiniz sistem diskinin etiketinin/dev/sdc1olduğunu varsaytık. VM 'niz için uygun değer ile değiştirin.

3. Dosya sistemindeki hataları algılamak için xfs_repair ile-n seçeneğini kullanın.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Dosya sistemini onarmak için aşağıdaki komutu çalıştırın:

   ```
   xfs_repair /dev/sdc1
   ```

5. Hata iletisini alırsanız "hata: FileSystem 'ın yeniden yürütülmesi gereken bir günlükte değerli meta veri değişiklikleri vardır", geçici bir dizin oluşturup dosya sistemini bağlama:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Disk takılamazsa,-L seçeneğiyle xfs_repair komutunu çalıştırın (günlük sıfırlama işlemi zorla):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Sonra, dosya sistemini bağlamaya çalışın. Disk başarıyla bağlanmışsa, şu çıktıyı alırsınız:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Özgün sanal sabit diski çıkarın ve kullanımdan çıkarın ve ardından özgün sistem diskinden bir VM oluşturun. Bunu yapmak için, onları kurtarma VM 'sini oluşturmak üzere kullandıysanız [CLI komutlarını](troubleshoot-recovery-disks-linux.md) veya [VM onarım komutlarını](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) kullanabilirsiniz.

8. Sorunun çözümlenip çözümlenmediğini denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLı 2,0 ile işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek bir Linux sanal makinesi sorunlarını giderme](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Bir Linux VM 'sine veri diski eklemek için portalı kullanma](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

