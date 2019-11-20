---
title: GRUB ve tek kullanıcılı mod için Azure seri konsolu | Microsoft Docs
description: Bu makalede, Azure sanal makinelerinde GRUB için seri konsolunun nasıl kullanılacağı açıklanır.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 06cb3fe5d551ddfc95fcbd37cd9620adebd825c5
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883935"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>GRUB ve tek kullanıcı moduna erişmek için seri konsol kullanma
Bir sanal makineyi (VM) önyüklerken gördüğünüz ilk şey, genel Birleşik önyükleme yükleyicisinden (GRUB) büyük olasılıkla karşılaşırsınız. İşletim sistemi başlatılmadan önce görüntülendiğinden, GRUB SSH aracılığıyla erişilebilir değildir. GRUB 'de, önyükleme yapılandırmanızı, diğer şeyler arasında tek kullanıcılı modda önyüklenecek şekilde değiştirebilirsiniz.

Tek kullanıcılı mod, en az işlevselliğe sahip minimal bir ortamdır. Önyükleme sorunlarını, dosya sistemi sorunlarını veya ağ sorunlarını araştırmak için yararlı olabilir. Arka planda daha az hizmet çalıştırılabilir ve Runlevel 'a bağlı olarak, bir dosya sistemi de otomatik olarak takılmayabilir.

Tek kullanıcılı mod, sanal makinenizin yalnızca oturum açma için SSH anahtarlarını kabul edecek şekilde yapılandırıldığı durumlarda da yararlıdır. Bu durumda, parola kimlik doğrulamasıyla bir hesap oluşturmak için tek kullanıcılı modu kullanabilirsiniz. 

> [!NOTE]
> Seri konsol hizmeti, yalnızca *katkıda bulunan* düzeyindeki veya daha yüksek izinlere sahip KULLANıCıLARıN bir VM 'nin seri konsoluna erişmesine izin verir.

Tek kullanıcılı modu girmek için, VM 'niz önyüklenirken, GRUB girin ve GRUB 'de önyükleme yapılandırmasını değiştirin. Sonraki bölümde GRUB girmeye yönelik ayrıntılı yönergelere bakın. Genel olarak, VM 'niz bir GRUB 'yi görüntüleyecek şekilde yapılandırıldıysa, VM 'yi yeniden başlatmak ve GRUB 'yi göstermek için sanal makinenizin seri konsolundaki yeniden Başlat düğmesini kullanabilirsiniz.

![Linux seri konsol yeniden başlatma düğmesi](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Genel GRUB erişimi
GRUB 'ye erişmek için seri konsol bölmesi açıkken VM 'nizi yeniden başlatın. Bazı dağıtımlarca, GRUB 'yi göstermek için klavye girişi gerekir ve diğerleri, Kullanıcı klavye girişinin zaman aşımını iptal edebilmesini sağlamak için birkaç saniye sonra otomatik olarak GRUB 'yi gösterir.

Tek kullanıcı moduna erişebilmek için, sanal makinenizde GRUB 'nin etkinleştirildiğinden emin olmak istersiniz. Dağıtıma bağlı olarak, bazı kurulum işleri, GRUB 'nin etkinleştirildiğinden emin olmak için gerekli olabilir. Dağıtıma özgü bilgiler için bkz. [Azure 'Da Linux Için destek sayfasında yer](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) alan bir sonraki bölüm.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Seri konsolundaki GRUB 'ye erişmek için sanal makineyi yeniden başlatın
**Yeniden başlatma** düğmesini ve sonra **VM 'yi yeniden Başlat**' ı seçerek VM 'nizi seri konsol içinde yeniden başlatabilirsiniz. Yeniden başlatma hakkında bir bildirim bölmenin en altında görüntülenir.

Ayrıca, [SySRq](./serial-console-nmi-sysrq.md) etkinse bir SySRq "b" komutu çalıştırarak VM 'nizi yeniden başlatabilirsiniz. Yeniden başlatma sırasında GRUB 'den beklediklerinizi öğrenmek için, sonraki bölümlerde dağıtıma özgü yönergelere bakın.

![Linux seri konsol yeniden başlatması](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Genel tek kullanıcı modu erişimi
Parola kimlik doğrulamasıyla bir hesap yapılandırmadıysanız, tek kullanıcı moduna el ile erişmeniz gerekebilir. Tek kullanıcılı modu el ile girmek için GRUB yapılandırmasını değiştirin. Bunu yaptıktan sonra, daha fazla yönerge için "bir parolayı sıfırlamak veya parolayı eklemek için tek kullanıcılı modu kullanma" bölümüne bakın.

VM önyüklemesi yapamıyor, dağıtımlar genellikle sizi otomatik olarak tek kullanıcı moduna veya acil durum moduna bırakamaz. Bununla birlikte, diğer dağıtımlar, sizi tek kullanıcı veya Acil moduna otomatik olarak bırakmadan önce, kök parola ayarlama gibi ek kurulum gerektirir.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Bir parolayı sıfırlamak veya eklemek için tek kullanıcılı modu kullanma
Tek Kullanıcı modundayken, aşağıdakileri yaparak sudo ayrıcalıklarına sahip yeni bir kullanıcı ekleyin:
1. Bir `useradd <username>` Kullanıcı eklemek için ' i çalıştırın.
1. Yeni `sudo usermod -a -G sudo <username>` Kullanıcı kök ayrıcalıklarına izin vermek için ' i çalıştırın.
1. Yeni `passwd <username>` kullanıcının parolasını ayarlamak için kullanın. Daha sonra yeni kullanıcı olarak oturum açabilirsiniz.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Red Hat Enterprise Linux erişimi (RHEL)
RHEL normal şekilde önyükleme yapamıyor, sizi otomatik olarak tek kullanıcı moduna bırakır. Ancak, tek kullanıcılı mod için kök erişimi ayarlamadıysanız, kök parolanız yoktur ve oturum açamazsınız. Geçici bir çözüm vardır ("RHEL 'de tek kullanıcı moduna el Ile girme" bölümüne bakın), ancak başlangıçta kök erişimi ayarlamanızı öneririz.

### <a name="grub-access-in-rhel"></a>RHEL 'de GRUB erişimi
RHEL, kutudan çıkar seçeneğiyle birlikte gelir. Grub girmek için çalıştırarak `sudo reboot`sanal makinenizi yeniden başlatın ve ardından herhangi bir tuşa basın. GRUB bölmesi görüntülenmelidir. Aksi takdirde, aşağıdaki satırların GRUB dosyanızda (`/etc/default/grub`) bulunduğundan emin olun:

**RHEL 8 için**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**RHEL 7 için**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> Red Hat ayrıca kurtarma moduna, acil durum moduna veya hata ayıklama moduna önyükleme için ve kök parolayı sıfırlamaya yönelik belgeler sağlar. Yönergeler için bkz. [önyükleme sırasında Terminal menüsü düzenlemesi](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>RHEL 'de tek kullanıcılı mod için kök erişimi ayarlama
Kök kullanıcı varsayılan olarak devre dışıdır. RHEL 'de tek kullanıcılı mod, kök kullanıcının etkinleştirilmesini gerektirir. Tek kullanıcılı modu etkinleştirmeniz gerekiyorsa aşağıdaki yönergeleri kullanın:

1. SSH aracılığıyla Red Hat sisteminde oturum açın.
1. Köke geçiş yapın.
1. Aşağıdaki işlemleri gerçekleştirerek kök kullanıcı için parolayı etkinleştirin:
    * Çalıştır `passwd root` (güçlü bir kök parolası ayarlayın).
1. Kök kullanıcının aşağıdakileri yaparak yalnızca ttyS0 aracılığıyla oturum açabildiğinden emin olun:  
    a. Öğesini `edit /etc/ssh/sshd_config`çalıştırın ve permitrootlogın 'in olarak `no`ayarlandığından emin olun.  
    b. Yalnızca `edit /etc/securetty file` ttyS0 aracılığıyla oturum açmaya izin vermek için çalıştırın.

Artık sistem tek kullanıcı modunda önyükleniyorsa, kök parolasıyla oturum açabilirsiniz.

Alternatif olarak, RHEL 7.4 + ya da 6.9 + için, GRUB istemlerinde tek kullanıcılı modu etkinleştirmek için bkz. [tek kullanıcı modunda önyükleme](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>RHEL 'de tek kullanıcılı modu el ile girin
Yukarıdaki yönergeleri kullanarak GRUB ve kök erişim ayarladıysanız, aşağıdakileri yaparak tek kullanıcılı mod girebilirsiniz:

1. GRUB girmek için VM 'yi yeniden başlattıktan sonra ESC tuşuna basın.
1. GRUB 'de, önyüklemek istediğiniz işletim sistemini düzenlemek için E tuşuna basın. İşletim sistemi genellikle ilk satırda listelenir.
1. Çekirdek satırını bulun. Azure 'da *linux16*ile başlar.
1. Satırın sonuna gitmek için CTRL + E tuşlarına basın.
1. Satırın sonunda *systemd. Unit = kurtarma. Target*' ı ekleyin.
    
    Bu eylem sizi tek kullanıcılı modda önyükler. Acil durum modunu kullanmak istiyorsanız, satırın sonuna *systemd. Unit = acil durum. Target* ekleyin ( *systemd. Unit = kurtarma. Target*yerine).

1. Çıkmak için CTRL + X tuşlarına basın ve uygulanan ayarlarla yeniden başlatın.

   Tek kullanıcı moduna girebilmeniz için önce yönetici parolasını girmeniz istenir. Bu parola, önceki yönergelerde oluşturduğunuz bir paroladır.

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>RHEL 'de kök hesabı etkin olmadan tek kullanıcılı mod girin
Önceki yönergeleri izleyerek kök kullanıcıyı etkinleştirmediyseniz, aşağıdaki işlemleri yaparak kök parolanızı yine de sıfırlamayı seçebilirsiniz:

> [!NOTE]
> SELinux kullanıyorsanız, kök parolayı sıfırlarken, [Red Hat belgelerinde](https://aka.ms/rhel7grubterminal)açıklanan ek adımları izlediğinizden emin olun.

1. GRUB girmek için VM 'yi yeniden başlattıktan sonra ESC tuşuna basın.

1. GRUB 'de, önyüklemek istediğiniz işletim sistemini düzenlemek için E tuşuna basın. İşletim sistemi genellikle ilk satırda listelenir.
1. Çekirdek satırını bulun. Azure 'da *linux16*ile başlar.
1. Satırın sonunda, satırın sonuna *RD. Break* ekleyin. Çekirdek çizgi ve *RD. Break*arasında bir boşluk bırakın.

    Bu eylem, [Red Hat belgelerinde](https://aka.ms/rhel7rootpassword)açıklandığı gibi, denetim öğesinden `initramfs` öğesine `systemd`geçirilmeden önce önyükleme işlemini keser.
1. Çıkmak için CTRL + X tuşlarına basın ve uygulanan ayarlarla yeniden başlatın.

   ' Yi yeniden başlattıktan sonra, salt bir dosya sistemi ile acil durum moduna bırakılmışız. 
   
1. Kabukta kök dosya sistemini `mount -o remount,rw /sysroot` okuma/yazma izinleriyle yeniden bağlamak için girin.
1. Tek kullanıcılı modda önyükleme yaptıktan sonra, `chroot /sysroot` `sysroot` Jail 'e geçiş yapmak için girin.
1. Artık kökte olursunuz. Önceki yönergeleri girerek `passwd` ve ardından tek kullanıcılı mod girmek için kök parolanızı sıfırlayabilirsiniz. 
1. İşiniz bittiğinde yeniden başlatmak için girin `reboot -f` .

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Önceki yönergelerden çalıştırılması sizi acil durum kabuğuna bırakır, böylece, Düzenle `fstab`gibi görevleri de gerçekleştirebilirsiniz. Bununla birlikte, genellikle kök parolanızı sıfırlamanıza ve tek kullanıcılı mod girmek için kullanmanızı öneririz.

## <a name="access-for-centos"></a>CentOS erişimi
Red Hat Enterprise Linux benzer şekilde, CentOS 'daki tek kullanıcılı mod, GRUB ve kök kullanıcının etkinleştirilmesini gerektirir.

### <a name="grub-access-in-centos"></a>CentOS 'da GRUB erişimi
CentOS, kutudan çıkan GRUB ile birlikte gelir. Grub girmek için, girerek `sudo reboot`sanal makinenizi yeniden başlatın ve ardından herhangi bir tuşa basın. Bu eylem, GRUB bölmesini görüntüler.

### <a name="single-user-mode-in-centos"></a>CentOS 'da tek kullanıcılı mod
CentOS 'da tek kullanıcılı modu etkinleştirmek için, önceki RHEL yönergelerini izleyin.

## <a name="access-for-ubuntu"></a>Ubuntu erişimi
Ubuntu görüntüleri kök parolası gerektirmez. Sistem tek kullanıcı modunda önyükleniyorsa, ek kimlik bilgileri olmadan kullanabilirsiniz.

### <a name="grub-access-in-ubuntu"></a>Ubuntu 'da GRUB erişimi
GRUB 'ye erişmek için VM 'yi önyüklerken ESC tuşuna basın ve basılı tutun.

Varsayılan olarak, Ubuntu görüntüleri, GRUB bölmesini otomatik olarak görüntülemeyebilir. Aşağıdakileri yaparak ayarı değiştirebilirsiniz:
1. Bir metin düzenleyicisinde */etc/default/grub.d/50-cloudimg-Settings.cfg* dosyasını açın.

1. `GRUB_TIMEOUT` Değeri sıfır olmayan bir değerle değiştirin.
1. Bir metin düzenleyicisinde */etc/default/grub*öğesini açın.
1. `GRUB_HIDDEN_TIMEOUT=1` Satırı açıklama.
1. Bir `GRUB_TIMEOUT_STYLE=menu` satır olduğundan emin olun.
1. `sudo update-grub` öğesini çalıştırın.

### <a name="single-user-mode-in-ubuntu"></a>Ubuntu 'da tek kullanıcılı mod
Ubuntu normal şekilde önyükleme yapamıyor, sizi otomatik olarak tek kullanıcı moduna bırakır. Tek kullanıcılı modu el ile girmek için aşağıdakileri yapın:

1. GRUB 'de, önyükleme girdinizi (Ubuntu girişi) düzenlemek için E tuşuna basın.
1. *Linux*ile başlayan satırı bulun ve ardından *ro*' ı arayın.
1. Single *'dan* önce ve sonra bir boşluk olduğundan emin olmak için, bir sonraki *ro* *ekleyin.*
1. Bu ayarlarla yeniden başlatmak için CTRL + X tuşlarına basın ve tek kullanıcı modunu girin.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Ubuntu 'da Bash 'i çağırmak için GRUB kullanma
Yukarıdaki yönergeleri tamamladıktan sonra, Ubuntu sanal makinenizde tek kullanıcılı moda erişemediği bir durum (unutulmuş root parolası gibi) olabilir. Çekirdekten sistem init yerine init olarak çalışacağını `/bin/bash` de söyleyebilirsiniz. Bu eylem size bir bash kabuğu sağlar ve sistem bakımını sağlar. Aşağıdaki yönergeleri kullanın:

1. GRUB 'de, önyükleme girdinizi (Ubuntu girişi) düzenlemek için E tuşuna basın.

1. *Linux*ile başlayan satırı bulun ve ardından *ro*' ı arayın.
1. *Ro* 'yi *RW init =/bin/Bash*ile değiştirin.

    Bu eylem, dosya sisteminizi okuma-yazma olarak takar ve init `/bin/bash` işlemi olarak kullanır.
1. Bu ayarlarla yeniden başlatmak için CTRL + X tuşlarına basın.

## <a name="access-for-coreos"></a>CoreOS erişimi
CoreOS 'daki tek kullanıcılı mod, GRUB 'nin etkinleştirilmesini gerektirir.

### <a name="grub-access-in-coreos"></a>CoreOS 'ta GRUB erişimi
GRUB 'ye erişmek için sanal makinenizin önyüklemesi sırasında herhangi bir tuşa basın.

### <a name="single-user-mode-in-coreos"></a>CoreOS 'de tek kullanıcılı mod
CoreOS normal şekilde önyükleme yapamıyor, sizi otomatik olarak tek kullanıcı moduna bırakır. Tek kullanıcılı modu el ile girmek için aşağıdakileri yapın:

1. GRUB 'de, önyükleme girdinizi düzenlemek için E tuşuna basın.

1. *Linux $* ile başlayan satırı bulun. Satırın iki örneği olması gerekir, her biri farklı bir if... şeklinde kapsüllenir *. Else* tümcesi.
1. Her *Linux $* hattının sonuna *CoreOS. oto Login = ttyS0* ekleyin.
1. Bu ayarlarla yeniden başlatmak için CTRL + X tuşlarına basın ve tek kullanıcı modunu girin.

## <a name="access-for-suse-sles"></a>SUSE SLES için erişim
SLES 12 SP3 + ' un daha yeni görüntüleri, sistem acil durum modunda önyükleniyorsa seri konsol aracılığıyla erişime izin verir.

### <a name="grub-access-in-suse-sles"></a>SUSE SLES 'de GRUB erişimi
SLES 'deki GRUB erişimi, YaST aracılığıyla bir önyükleme yükleyicisi yapılandırması gerektirir. Yapılandırmayı oluşturmak için aşağıdakileri yapın:

1. SLES sanal makinenizde oturum açmak ve ardından çalıştırmak `sudo yast bootloader`için SSH kullanın. Tab tuşuna basın, ENTER tuşuna basın ve ardından menüde gezinmek için ok tuşlarını kullanın.

1. **Çekirdek parametreleri**' ne gidin ve ardından **seri konsolunu kullan** onay kutusunu seçin.
1. Konsol `serial --unit=0 --speed=9600 --parity=no` bağımsız değişkenlerine ekleyin.
1. Ayarlarınızı kaydetmek ve çıkmak için F10 tuşuna basın.
1. GRUB girmek için sanal makinenizi yeniden başlatın ve önyükleme sırası sırasında, GRUB bölmesini görüntülenmesini sağlamak için herhangi bir tuşa basın.

    GRUB için varsayılan zaman aşımı **1**' dir. Bu ayarı, `GRUB_TIMEOUT` */etc/default/grub* dosyasındaki değişkeni değiştirerek değiştirebilirsiniz.

![Önyükleme yükleyicisi yapılandırması başlatılıyor](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>SUSE SLES 'de tek kullanıcılı mod
SLES normal önyükleme yapamıyor, otomatik olarak acil durum kabuğu 'na bırakılır. Acil durum kabuğunu el ile girmek için aşağıdakileri yapın:

1. GRUB 'de, önyükleme girdinizi (SLES girişi) düzenlemek için E tuşuna basın.

1. *Linux*ile başlayan çekirdek hattını arayın.
1. Çekirdek satırının sonuna *systemd. Unit = acil durum. Target* ekleyin.
1. Bu ayarlarla yeniden başlatmak için CTRL + X tuşlarına basın ve acil durum kabuğunu girin.

   > [!NOTE]
   > Bu eylem, bir salt okuma dosya sistemiyle sizi acil durum kabuğu 'na bırakır. Herhangi bir dosyayı düzenlemek için dosya sistemini okuma-yazma izinleriyle yeniden bağlayın. Bunu yapmak için, kabuğa girin `mount -o remount,rw /` .

## <a name="access-for-oracle-linux"></a>Oracle Linux için erişim
Red Hat Enterprise Linux benzer şekilde, Oracle Linux tek kullanıcılı mod, GRUB ve kök kullanıcının etkinleştirilmesini gerektirir.

### <a name="grub-access-in-oracle-linux"></a>Oracle Linux erişim
Oracle Linux, kutudan çıkan GRUB ile birlikte gelir. Grub girmek için çalıştırarak `sudo reboot`sanal makinenizi yeniden başlatın ve ardından ESC tuşuna basın. Bu eylem, GRUB bölmesini görüntüler. Grub bölmesi görüntülenmiyorsa, `GRUB_TERMINAL` satır değerinin *seri konsol* (yani, `GRUB_TERMINAL="serial console"`) içerdiğinden emin olun. GRUB ile `grub2-mkconfig -o /boot/grub/grub.cfg`yeniden derleyin.

### <a name="single-user-mode-in-oracle-linux"></a>Oracle Linux tek kullanıcılı mod
Oracle Linux çoklu Kullanıcı modunu etkinleştirmek için, önceki RHEL yönergelerini izleyin.

## <a name="next-steps"></a>Sonraki adımlar
Seri konsol hakkında daha fazla bilgi için bkz.
* [Linux seri konsol belgeleri](serial-console-linux.md)
* [Çeşitli dağıtımlardan GRUB 'yi etkinleştirmek için seri konsol kullanma](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [NMI ve SysRq çağrıları için seri konsol kullan](serial-console-nmi-sysrq.md)
* [Windows VM 'Leri için seri konsol](serial-console-windows.md)
* [Önyükleme tanılamaları](boot-diagnostics.md)
