---
title: Fstab hataları nedeniyle Linux sanal makinesi sorunlarını giderme | Microsoft Docs
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
ms.openlocfilehash: 868a0238092786d0999a6a41de71d30011bbef7a
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245350"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Fstab hataları nedeniyle Linux sanal makinesi sorunlarını giderme

Bir Azure Linux sanal makinesine (VM) Secure Shell (SSH) bağlantısı kullanarak bağlanamazsınız. [Azure Portal](https://portal.azure.com/) [önyükleme tanılama](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) özelliğini çalıştırdığınızda aşağıdaki örneklere benzeyen günlük girdileri görürsünüz:

## <a name="examples"></a>Örnekler

Olası hataların örnekleri aşağıda verilmiştir.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Örnek 1: bir disk, evrensel benzersiz tanımlayıcı (UUID) yerine SCSI KIMLIĞI tarafından bağlanır

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type “journalctl -xb” to viewsystem logs, “systemctl reboot” to reboot, “systemctl default” to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Örnek 2: CentOS üzerinde eklenmemiş bir cihaz eksik

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sdd1: nonexistent device (“nofail” fstab option may be used to skip this device)
/dev/sde1: nonexistent device (“nofail” fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Örnek 3: bir fstab yanlış yapılandırma nedeniyle veya disk artık eklenmediğinden bir VM başlatılamaz

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Örnek 4: bir seri günlüğü girdisi yanlış bir UUID gösterir

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run ‘setenforce 1’ to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

Bu sorun, dosya sistemi tablosu (fstab) sözdizimi yanlışsa veya "/etc/fstab" dosyasındaki bir girdiyle eşlenen gerekli bir veri diski VM 'ye bağlı değilse oluşabilir.

## <a name="resolution"></a>Çözüm

Bu sorunu çözmek için, Azure sanal makineler için seri konsol kullanarak VM 'yi acil modda başlatın. Ardından, dosya sistemini onarmak için aracını kullanın. VM 'niz üzerinde seri konsol etkinleştirilmemişse, [VM çevrimdışına Onar](#repair-the-vm-offline) bölümüne gidin.

## <a name="use-the-serial-console"></a>Seri konsolunu kullanma

1. [Seri konsoluna](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)bağlanın.
2. Yerel bir Kullanıcı ve parola kullanarak sistemde oturum açın.

   > [!Note]
   > Seri konsolundaki sistemde oturum açmak için bir SSH anahtarı kullanamazsınız.

3. Diskin takılmadığını belirten hatayı arayın. Aşağıdaki örnekte, sistem artık mevcut olmayan bir diski eklemeye çalışıyor:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Kök parolayı (Red Hat tabanlı VM 'Ler) kullanarak VM 'ye bağlanın.

5. En sevdiğiniz metin düzenleyiciyi kullanarak fstab dosyasını açın. Disk bağlandıktan sonra nano için aşağıdaki komutu çalıştırın:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Listelenen dosya sistemlerini gözden geçirin. Fstab dosyasındaki her satır, VM başlatıldığında bağlanan bir dosya sistemini gösterir. Fstab dosyasının sözdizimi hakkında daha fazla bilgi için Man fstab komutunu çalıştırın. Bir başlatma başarısızlığının sorunlarını gidermek için her bir satırı gözden geçirerek her iki yapıda ve içerikte doğru olduğundan emin olun.

   > [!Note]
   > * Her satırdaki alanlar sekme veya boşluk ile ayrılır. Boş satırlar yok sayılır. İlk karakter olarak numara işareti (#) olan satırlar açıklamalardır. Açıklamalı çizgiler fstab dosyasında kalabilir, ancak işlenmeyecektir. Satırları kaldırmak yerine, emin olduğunuz fstab satırlarını açıklamanızı öneririz.
   > * VM 'nin kurtarılmasına ve başlaması için, dosya sistemi bölümlerinin yalnızca gerekli bölümler olması gerekir. VM, ek açıklamalı bölümler hakkında uygulama hatalarıyla karşılaşabilir. Ancak, VM ek bölümler olmadan başlamalıdır. Açıklamalı tüm çizgileri daha sonra açıklama ekleyebilirsiniz.
   > * Dosya sistemi bölümünün UUID 'sini kullanarak Azure VM 'lerine veri diskleri bağlamanız önerilir. Örneğin, şu komutu çalıştırın: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Dosya sisteminin UUID 'sini öğrenmek için blkıd komutunu çalıştırın. Sözdizimi hakkında daha fazla bilgi için Man blkıd komutunu çalıştırın.
   > * NOFAIL seçeneği, dosya sistemi bozuksa veya dosya sistemi başlangıçta mevcut olmasa bile VM 'nin başlamasını sağlar. Sanal makinenin başlaması gerekmeyen bölümlerde hata oluşması durumunda başlatma işleminin devam etmesini sağlamak için fstab dosyasında NOFAIL seçeneğini kullanmanızı öneririz.

7. VM 'nin doğru şekilde başlamasını sağlamak için fstab dosyasındaki yanlış veya gereksiz satırları değiştirin veya not edin.

8. Değişiklikleri fstab dosyasına kaydedin.

9. Sanal makineyi yeniden başlatın.

10. Giriş yorumu veya düzeltilmesi başarılı olduysa, sistem portalda bir bash istemine ulaşmalıdır. VM 'ye bağlanıp bağlanamaıp bağlanamayacağını denetleyin.

11. Bağlama – bir komut çalıştırarak herhangi bir fstab değişikliğini test ettiğinizde bağlama noktalarınızı kontrol edin. Hata yoksa bağlama noktalarınız iyi olmalıdır.

## <a name="repair-the-vm-offline"></a>VM 'yi çevrimdışı onarma

1. VM 'nin sistem diskini bir kurtarma VM 'sine (çalışmakta olan herhangi bir Linux VM) veri diski olarak ekleyin. Bunu yapmak için [CLI komutlarını](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) kullanabilir veya [VM onarım komutlarını](repair-linux-vm-using-azure-virtual-machine-repair-commands.md)kullanarak kurtarma VM 'sinin kurulumunu otomatik hale getirebilirsiniz.

2. Sistem diskini kurtarma sanal makinesine bir veri diski olarak bağladığınızda, değişiklik yapmadan önce fstab dosyasını yedekleyin ve ardından fstab dosyasını düzeltmek için sonraki adımları uygulayın.

3.  Diskin takılmadığını belirten hatayı arayın. Aşağıdaki örnekte, sistem artık mevcut olmayan bir diski eklemeye çalışıyor:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Kök parolayı (Red Hat tabanlı VM 'Ler) kullanarak VM 'ye bağlanın.

5. En sevdiğiniz metin düzenleyiciyi kullanarak fstab dosyasını açın. Disk bağlandıktan sonra nano için aşağıdaki komutu çalıştırın. Bağlı diskte bulunan fstab dosyasında, kurtarma sanal makinesinde bulunan fstab dosyasında çalıştığınızdan emin olun.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Listelenen dosya sistemlerini gözden geçirin. Fstab dosyasındaki her satır, VM başlatıldığında bağlanan bir dosya sistemini gösterir. Fstab dosyasının sözdizimi hakkında daha fazla bilgi için Man fstab komutunu çalıştırın. Bir başlatma başarısızlığının sorunlarını gidermek için her bir satırı gözden geçirerek her iki yapıda ve içerikte doğru olduğundan emin olun.

   > [!Note]
   > * Her satırdaki alanlar sekme veya boşluk ile ayrılır. Boş satırlar yok sayılır. İlk karakter olarak numara işareti (#) olan satırlar açıklamalardır. Açıklamalı çizgiler fstab dosyasında kalabilir, ancak işlenmeyecektir. Satırları kaldırmak yerine, emin olduğunuz fstab satırlarını açıklamanızı öneririz.
   > * VM 'nin kurtarılmasına ve başlaması için, dosya sistemi bölümlerinin yalnızca gerekli bölümler olması gerekir. VM, ek açıklamalı bölümler hakkında uygulama hatalarıyla karşılaşabilir. Ancak, VM ek bölümler olmadan başlamalıdır. Açıklamalı tüm çizgileri daha sonra açıklama ekleyebilirsiniz.
   > * Dosya sistemi bölümünün UUID 'sini kullanarak Azure VM 'lerine veri diskleri bağlamanız önerilir. Örneğin, şu komutu çalıştırın: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Dosya sisteminin UUID 'sini öğrenmek için blkıd komutunu çalıştırın. Sözdizimi hakkında daha fazla bilgi için Man blkıd komutunu çalıştırın. Kurtarmak istediğiniz diskin artık yeni bir VM 'ye bağlı olduğuna dikkat edin. UUID 'ler tutarlı olmalıdır, ancak cihaz bölüm kimlikleri (örneğin, "/dev/sda1") bu VM 'de farklı. Sistem dışı bir VHD 'de bulunan özgün başarısız VM 'nin dosya sistemi bölümleri, [CLI komutları kullanılarak](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)kurtarma sanal makinesi için kullanılamaz.
   > * NOFAIL seçeneği, dosya sistemi bozuksa veya dosya sistemi başlangıçta mevcut olmasa bile VM 'nin başlamasını sağlar. Sanal makinenin başlaması gerekmeyen bölümlerde hata oluşması durumunda başlatma işleminin devam etmesini sağlamak için fstab dosyasında NOFAIL seçeneğini kullanmanızı öneririz.

7. VM 'nin doğru şekilde başlamasını sağlamak için fstab dosyasındaki yanlış veya gereksiz satırları değiştirin veya not edin.

8. Değişiklikleri fstab dosyasına kaydedin.

9. Sanal makineyi yeniden başlatın veya orijinal VM 'yi yeniden derleyin.

10. Giriş yorumu veya düzeltilmesi başarılı olduysa, sistem portalda bir bash istemine ulaşmalıdır. VM 'ye bağlanıp bağlanamaıp bağlanamayacağını denetleyin.

11. Bağlama – bir komut çalıştırarak herhangi bir fstab değişikliğini test ettiğinizde bağlama noktalarınızı kontrol edin. Hata yoksa bağlama noktalarınız iyi olmalıdır.

12. Özgün sanal sabit diski çıkarın ve kullanımdan çıkarın ve ardından özgün sistem diskinden bir VM oluşturun. Bunu yapmak için, onları kurtarma VM 'sini oluşturmak üzere kullandıysanız [CLI komutlarını](troubleshoot-recovery-disks-linux.md) veya [VM onarım komutlarını](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) kullanabilirsiniz.

13. VM 'yi yeniden oluşturduktan sonra SSH aracılığıyla buna bağlanabilirsiniz, aşağıdaki işlemleri gerçekleştirin:
    * Kurtarma sırasında değiştirilen veya açıklama eklenen fstab satırlarından herhangi birini gözden geçirin.
    * UUID ve NOFAIL seçeneğini uygun şekilde kullandığınızdan emin olun.
    * VM 'yi yeniden başlatmadan önce fstab değişikliklerini test edin. Bunu yapmak için şu komutu kullanın: ``$ sudo mount -a``
    * Gelecekteki kurtarma senaryolarında kullanılmak üzere düzeltilmiş fstab dosyasının ek bir kopyasını oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure CLı 2,0 ile işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek bir Linux sanal makinesi sorunlarını giderme](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Azure portal kullanarak işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek bir Linux VM sorunlarını giderme](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

