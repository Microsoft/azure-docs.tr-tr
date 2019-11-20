---
title: Linux için Azure seri konsolu | Microsoft Docs
description: Azure sanal makineleri ve sanal makine ölçek kümeleri için iki yönlü seri konsol.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 15e0b8a5b3ea64148eb78cb376500adac2410a71
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949664"
---
# <a name="azure-serial-console-for-linux"></a>Linux için Azure seri konsolu

Azure portal seri konsol, Linux sanal makineleri (VM 'Ler) ve sanal makine ölçek kümesi örnekleri için metin tabanlı bir konsola erişim sağlar. Bu seri bağlantı, ağ veya işletim sistemi durumundan bağımsız olarak erişim sağlayan VM veya sanal makine ölçek kümesi örneğinin ttyS0 seri bağlantı noktasına bağlanır. Seri konsoluna yalnızca Azure portal kullanılarak erişilebilir ve yalnızca VM veya sanal makine ölçek kümesine katkıda bulunan veya daha yüksek bir erişim rolüne sahip olan kullanıcılar için izin verilir.

Seri konsol, VM 'Ler ve sanal makine ölçek kümesi örnekleri için aynı şekilde çalışmaktadır. Bu belgede, aksi belirtilmediği takdirde VM 'lerdeki tüm bahsetmeler, sanal makine ölçek kümesi örneklerini örtülü olarak içerir.

Windows için seri konsol belgeleri için bkz. [Windows Için seri konsol](../windows/serial-console.md).

> [!NOTE]
> Seri konsol Genel Azure bölgelerinde genel kullanıma sunulmuştur. Henüz Azure Kamu veya Azure Çin bulutlarında kullanılabilir değildir.


## <a name="prerequisites"></a>Prerequisites

- VM 'niz veya sanal makine ölçek kümesi örneğinizin kaynak yönetimi dağıtım modelini kullanması gerekir. Klasik dağıtımlar desteklenmez.

- Seri konsol kullanan hesabınızda VM ve [önyükleme tanılama](boot-diagnostics.md) depolama hesabı Için [sanal makine katılımcısı rolü](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) olmalıdır

- VM 'niz veya sanal makine ölçek kümesi örneğinizin parola tabanlı bir kullanıcısı olmalıdır. VM erişimi uzantısının [parola sıfırlama](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) işleviyle bir tane oluşturabilirsiniz. **Destek + sorun giderme** bölümünde **Parolayı Sıfırla** ' yı seçin.

- VM 'niz veya sanal makine ölçek kümesi örneğinizin [önyükleme tanılaması](boot-diagnostics.md) etkin olmalıdır.

    ![Önyükleme Tanılama ayarları](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Linux dağıtımlarına özgü ayarlar için bkz. [Linux dağıtım kullanılabilirliği seri konsol](#serial-console-linux-distribution-availability).

- VM 'niz veya sanal makine ölçek kümesi örneğinizin `ttys0` ' da seri çıkış için yapılandırılması gerekir. Bu, Azure görüntüleri için varsayılandır, ancak bunu özel görüntülerde çift denetlemek isteyeceksiniz. Ayrıntılar [aşağıda](#custom-linux-images)verilmiştir.


> [!NOTE]
> Seri konsol, yapılandırılmış bir parolası olan yerel bir kullanıcı gerektirir. Yalnızca bir SSH ortak anahtarı ile yapılandırılan VM 'Ler veya sanal makine ölçek kümeleri seri konsolunda oturum açamaz. Parolası olan bir yerel kullanıcı oluşturmak için, Azure portal **Parolayı Sıfırla** ' yı seçerek portalda bulunan [VMAccess uzantısını](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension)kullanın ve parolası olan bir yerel kullanıcı oluşturun.
> Ayrıca, [tek kullanıcı modunda önyüklemek IÇIN GRUB 'yi kullanarak](./serial-console-grub-single-user-mode.md)hesabınızdaki yönetici parolasını sıfırlayabilirsiniz.

## <a name="serial-console-linux-distribution-availability"></a>Seri konsol Linux dağıtım kullanılabilirliği
Seri konsolunun düzgün çalışması için, Konuk işletim sisteminin konsol iletilerini seri bağlantı noktasına okuyup yazacak şekilde yapılandırılması gerekir. En çok onaylanan [Azure Linux dağıtımları](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) varsayılan olarak yapılandırılmış seri konsoluna sahiptir. Azure portal **destek + sorun giderme** bölümünde **seri konsol** seçeneğinin belirlenmesi, seri konsoluna erişim sağlar.

> [!NOTE]
> Seri konsolunda herhangi bir şey görmüyorsanız, VM 'niz üzerinde önyükleme tanılaması 'nın etkinleştirildiğinden emin olun. **ENTER** tuşuna basmak, genellikle seri konsolda hiçbir şeyin gösterilmediğini ortaya çıkan sorunları düzeltir.

Dağılı      | Seri konsol erişim
:-----------|:---------------------
Red Hat Enterprise Linux    | Seri konsol erişimi varsayılan olarak etkinleştirilmiştir.
CentOS      | Seri konsol erişimi varsayılan olarak etkinleştirilmiştir.
Ubuntu      | Seri konsol erişimi varsayılan olarak etkinleştirilmiştir.
CoreOS      | Seri konsol erişimi varsayılan olarak etkinleştirilmiştir.
SUSE        | Azure 'da kullanılabilen yeni SLES görüntülerine, varsayılan olarak seri konsol erişimi etkinleştirilmiştir. Azure üzerinde SLES 'nin eski sürümlerini (10 veya daha eski sürümler) kullanıyorsanız, seri konsolu 'nu etkinleştirmek için [KB makalesine](https://www.novell.com/support/kb/doc.php?id=3456486) bakın.
Oracle Linux        | Seri konsol erişimi varsayılan olarak etkinleştirilmiştir.

### <a name="custom-linux-images"></a>Özel Linux görüntüleri
Özel Linux sanal makinenizin seri konsolunu etkinleştirmek için, */etc/ınittab* dosyasında konsol erişimini etkinleştirin `ttyS0` ' de bir Terminal çalıştırın. Örneğin: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Ayrıca ttyS0 üzerinde bir Getty oluşturmanız gerekebilir. Bu, `systemctl start serial-getty@ttyS0.service` ile yapılabilir.

Ayrıca, seri çıkış için hedef olarak ttyS0 eklemek isteyeceksiniz. Özel bir görüntünün seri konsoluyla çalışacak şekilde yapılandırılması hakkında daha fazla bilgi için bkz. [Azure 'da bir LINUX VHD oluşturma ve yükleme](https://aka.ms/createuploadvhd#general-linux-system-requirements)konusunda genel sistem gereksinimleri.

Özel bir çekirdek oluşturuyorsanız, bu çekirdek bayraklarını etkinleştirmeyi düşünün: `CONFIG_SERIAL_8250=y` ve `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Yapılandırma dosyası genellikle */Boot/* Path içinde bulunur.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Seri konsoluna erişmek için yaygın senaryolar

Senaryo          | Seri konsolundaki eylemler
:------------------|:-----------------------------------------
Bozuk *FSTAB* dosyası | Devam etmek için **ENTER** tuşuna basın ve *FSTAB* dosyasını onarmak için bir metin düzenleyicisi kullanın. Bunu yapmak için tek kullanıcı modunda olmanız gerekebilir. Daha fazla bilgi için bkz. [fstab sorunlarını giderme](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) ve [seri konsol kullanarak grub ve tek kullanıcı moduna erişme](serial-console-grub-single-user-mode.md).
Yanlış güvenlik duvarı kuralları |  SSH bağlantısını engellemek için Iptables yapılandırdıysanız, SSH gerekmeden VM 'niz ile etkileşim kurmak için seri konsolu 'nu kullanabilirsiniz. Daha fazla ayrıntı için [Iptables man sayfasında](https://linux.die.net/man/8/iptables)bulabilirsiniz.<br>Benzer şekilde, firewalld 'niz SSH erişimini engelliyorsa, VM 'ye seri konsol aracılığıyla erişebilir ve firewalld 'leri yeniden yapılandırabilirsiniz. Daha fazla ayrıntı, [firewalld belgelerinde](https://firewalld.org/documentation/)bulunabilir.
Dosya sistemi Bozulması/denetimi | Seri konsol kullanarak bozuk dosya sistemleri sorunlarını giderme hakkında daha fazla ayrıntı için lütfen [Azure LINUX VM](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck) 'nin seri konsol bölümüne bakın.
SSH yapılandırma sorunları | Seri konsoluna erişin ve ayarları değiştirin. Seri konsol, VM 'nin ağ bağlantısının çalışması için gerekli olmadığından VM 'nin SSH yapılandırmasından bağımsız olarak kullanılabilir. Sorun giderme kılavuzu, hata [veren veya reddedilen bir Azure LINUX VM 'sine YÖNELIK SSH bağlantılarında sorun gidermeye](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection)yönelik olarak kullanılabilir. [Azure 'da bir LINUX VM 'ye bağlanma sorunları Için AYRıNTıLı SSH sorun giderme adımlarında](./detailed-troubleshoot-ssh-connection.md) daha fazla ayrıntı mevcuttur
Önyükleme yükleyicisine etkileşim | Linux sanal makinenizin içindeki GRUB 'ye erişmek için VM 'nizi seri konsol dikey penceresinden yeniden başlatın. Daha fazla ayrıntı ve belirli bilgiler için bkz. çevirmeli [ve tek kullanıcı moduna erişmek için seri konsol kullanma](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Seri konsolunu devre dışı bırakma

Varsayılan olarak, tüm aboneliklerde seri konsol erişimi etkindir. Seri konsolunu abonelik düzeyinde ya da VM/sanal makine ölçek kümesi düzeyinde devre dışı bırakabilirsiniz. Ayrıntılı yönergeler için [Azure seri konsolunu etkinleştir ve devre dışı bırak](./serial-console-enable-disable.md)' ı ziyaret edin.

## <a name="serial-console-security"></a>Seri konsol güvenliği

### <a name="access-security"></a>Erişim güvenliği
Seri konsol erişimi, sanal makine [katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) veya daha yüksek bir erişim rolüne sahip kullanıcılarla sınırlıdır. Azure Active Directory kiracınız Multi-Factor Authentication (MFA) gerektiriyorsa, seri konsolunun erişimi [Azure Portal](https://portal.azure.com)üzerinden olduğu için seri konsoluna ERIŞIME de MFA gerekir.

### <a name="channel-security"></a>Kanal güvenliği
İleri ve geri gönderilen tüm veriler kabloda şifrelenir.

### <a name="audit-logs"></a>Denetim günlükleri
Seri konsoluna tüm erişim şu anda sanal makinenin [önyükleme tanılama](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) günlüklerinde oturum açtı. Bu günlüklere erişim, Azure sanal makine yöneticisi tarafından sahiplenilir ve denetlenir.

> [!CAUTION]
> Konsolun hiçbir erişim parolası günlüğe kaydedilmez. Ancak, konsolda çalıştırılan komutlar, parola, gizli dizileri, Kullanıcı adlarını veya herhangi bir kişisel olarak tanımlanabilir bilgi (PII) gibi bir diğer biçimi içeriyorsa, bunlar VM önyüklemesi tanılama günlüklerine yazılır. Seri konsolunun geri Kaydır işlevi uygulamasının bir parçası olarak, diğer tüm görünür metinle birlikte yazılır. Bu Günlükler dairesel ve yalnızca tanılama depolama hesabı için okuma iznine sahip olan bireyler bunlara erişebilir. Ancak, gizli dizi ve/veya PII içerebilen herhangi bir şey için Uzak Masaüstü kullanmanın en iyi yöntemini takip etmenizi öneririz.

### <a name="concurrent-usage"></a>Eşzamanlı Kullanım
Bir Kullanıcı seri konsoluna bağlandıysa ve başka bir kullanıcı aynı sanal makineye erişim isteğinde bulunursa, ilk kullanıcının bağlantısı kesilir ve ikinci Kullanıcı aynı oturuma bağlanır.

> [!CAUTION]
> Bu, bağlantısı kesilen bir kullanıcının oturum açmayacağı anlamına gelir. Bağlantı kesildikten sonra (SıGHUP veya benzer mekanizmayı kullanarak) bir oturum açma zorlaması yapma yeteneği, hala yol haritası üzerinde. Windows için özel yönetim konsolunda (SAC) etkin bir otomatik zaman aşımı vardır; Ancak, Linux için Terminal zaman aşımı ayarını yapılandırabilirsiniz. Bunu yapmak için, konsolunda oturum açmak üzere kullandığınız kullanıcının *. bash_profile* veya *. Profile* dosyasında `export TMOUT=600` ' ı ekleyin. Bu ayar 10 dakika sonra oturumu zaman aşımına uğrar.

## <a name="accessibility"></a>Erişilebilirlik
Erişilebilirlik, Azure seri konsolu için önemli bir odadır. Bu uçta, seri konsolunun tam olarak erişilebilir olduğunu umuyoruz.

### <a name="keyboard-navigation"></a>Klavye gezintisi
Azure portal seri konsol arabiriminde gezinmek için klavyenizde **Tab** tuşunu kullanın. Konumunuz ekranda vurgulanacaktır. Seri konsol penceresinin odağını bırakmak için klavyenizde **Ctrl**+**F6** tuşuna basın.

### <a name="use-serial-console-with-a-screen-reader"></a>Bir ekran okuyucuyla seri konsol kullanma
Seri konsolunda yerleşik ekran okuyucusu desteği vardır. Bir ekran okuyucusu açıkken dolaşma, ekran okuyucu tarafından şu anda seçili olan düğme için alternatif metnin yüksek sesle okunmasıyla izin verir.

## <a name="known-issues"></a>Bilinen sorunlar
Seri konsol ve VM 'nin işletim sistemi ile ilgili bazı sorunları fark ediyoruz. Bu sorunların ve Linux VM 'Leri hafifletme adımlarının bir listesi aşağıda verilmiştir. Bu sorunlar ve azaltmaları, hem VM 'Ler hem de sanal makine ölçek kümesi örnekleri için geçerlidir. Bunlar gördüğünüz hatayla eşleşmiyorsa, yaygın [seri konsol hatalarıyla](./serial-console-errors.md)ortak seri konsol hizmeti hatalarına bakın.

Sorun                           |   Azaltma
:---------------------------------|:--------------------------------------------|
Bağlantı başlığından sonra **ENTER** tuşuna basmak, oturum açma isteminin görüntülenmesine neden olmaz. | Daha fazla bilgi için bkz. ENTER tuşuna basarak [hiçbir şey yapılmıyor](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Bu sorun, Linux 'un seri bağlantı noktasına bağlanamamasına neden olan özel bir VM, sağlamlaştırılmış gereç veya GRUB yapılandırması çalıştırıyorsanız oluşabilir.
Seri konsol metin yalnızca ekran boyutunun bir kısmını kaplar (genellikle bir metin düzenleyicisi kullanarak). | Seri konsolları, pencere boyutu ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)) üzerinde anlaşmaya varılanır, yani güncelleştirme ekranı boyutuna gönderilen SIGWINCH sinyali yoktur ve VM 'nin terminalin boyutuyla ilgili hiçbir bilgisi olmayacaktır. @No__t-0 komutu sağlamak için xterm veya benzer bir yardımcı program yükleyip `resize` ' i çalıştırın.
Uzun dizeleri yapıştırma işe yaramıyor. | Seri konsol, seri bağlantı noktası bant genişliğinin aşırı yüklenmesini engellemek için, terminale yapıştırılan dizelerin uzunluğunu 2048 karakter olarak sınırlandırır.
SLES BYOS görüntülerinde klavye girişi Erratic. Klavye girişi yalnızca sporadolarak tanınıyor. | Bu, Plymouth paketiyle ilgili bir sorundur. Plymouth, Azure 'da çalıştırılmamalıdır ve bir giriş ekranına gerek kalmaz ve Plymouth 'in seri konsolu kullanma yeteneğini kesintiye uğratır. @No__t-0 ile Plymouth kaldırın ve ardından yeniden başlatın. Alternatif olarak, `plymouth.enable=0` ' ı satırın sonuna ekleyerek, GRUB yapılandırmasının çekirdek satırını değiştirebilirsiniz. Bunu, önyükleme [sırasında önyükleme girdisini düzenleyerek](https://aka.ms/serialconsolegrub#single-user-mode-in-suse-sles)veya `/etc/default/grub` ' deki GRUB_CMDLINE_LINUX satırını düzenleyerek, grub 'yi `grub2-mkconfig -o /boot/grub2/grub.cfg` ile yeniden başlatarak yapabilirsiniz.


## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S. nasıl geri bildirim gönderebilirim?**

A. @No__t-0 ' da bir GitHub sorunu oluşturarak geri bildirim sağlayın. Alternatif olarak (daha az tercih edilen), azserialhelp@microsoft.com ile veya https://feedback.azure.com ' in sanal makine kategorisinde geri bildirim gönderebilirsiniz.

**S. seri konsol kopyalamayı/yapıştırmayı destekliyor mu?**

A. Evet. Terminale kopyalayıp yapıştırmak için **ctrl**+**SHIFT**+**C** ve **CTRL**+**SHIFT**+**V** kullanın.

**Soru-cevap, SSH bağlantısı yerine seri konsol kullanabilir miyim?**

A. Bu kullanım Teknik olarak mümkün görünse de, seri konsolun Öncelikle SSH aracılığıyla bağlantının mümkün olmadığı durumlarda bir sorun giderme aracı olarak kullanılması amaçlanmıştır. Aşağıdaki nedenlerden dolayı seri konsolunun SSH değiştirme olarak kullanılması önerilir:

- Seri konsol SSH ile çok bant genişliğine sahip değildir. Yalnızca bir salt metin bağlantısı olduğundan, GUI ağır etkileşimler daha zordur.
- Seri konsol erişim şu anda yalnızca bir Kullanıcı adı ve parola kullanılarak yapılabilir. SSH anahtarları Kullanıcı adı/parola birleşimlerinden çok daha güvenli olduğundan, oturum açma güvenlik perspektifinden, seri konsol üzerinden SSH önerilir.

**S. Aboneliğimin seri konsolunu kim etkinleştirebilir veya devre dışı bırakabilirim?**

A. Seri konsolunu abonelik genelinde bir düzeyde etkinleştirmek veya devre dışı bırakmak için abonelik için yazma izinleriniz olmalıdır. Yazma izni olan roller yönetici veya sahip rolleri içerir. Özel rollerin de yazma izinleri olabilir.

**S. VM/sanal makine ölçek kümesi için seri konsoluna kimler erişebilir?**

A. Seri konsoluna erişmek için bir VM veya sanal makine ölçek kümesi için sanal makine katılımcısı rolü veya daha yüksek bir sürüme sahip olmanız gerekir.

**S. seri konsolum hiçbir şeyi görüntülüyor, ne yapmam gerekir?**

A. Resminiz, seri konsol erişimi için hatalı yapılandırılmış olabilir. Görüntünüzü seri konsolunu etkinleştirecek şekilde yapılandırma hakkında daha fazla bilgi için bkz. [seri konsol Linux dağıtım kullanılabilirliği](#serial-console-linux-distribution-availability).

**S. sanal makine ölçek kümeleri için seri konsol kullanılabilir mi?**

A. Evet öyle! Bkz. [Sanal Makine Ölçek Kümeleri Için seri konsol](serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

**S. VM 'yi veya sanal makine ölçek kümesini yalnızca SSH anahtarı kimlik doğrulaması kullanarak ayarladım, VM/sanal makine ölçek kümesi örneğine bağlanmak için hala seri konsolunu kullanabilir miyim?**

A. Evet. Seri konsol SSH anahtarları gerektirmediğinden, yalnızca bir Kullanıcı adı/parola bileşimi ayarlamanız gerekir. Bunu, Azure portal **Parolayı Sıfırla** ' yı seçerek ve seri konsolunda oturum açmak için bu kimlik bilgilerini kullanarak yapabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [GRUB ve tek kullanıcı moduna erişmek](serial-console-grub-single-user-mode.md)için seri konsolu 'nu kullanın.
* [NMI ve SysRq çağrıları](serial-console-nmi-sysrq.md)için seri konsolunu kullanın.
* [Çeşitli detros 'de GRUB 'yi etkinleştirmek](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)için seri konsolunu nasıl kullanacağınızı öğrenin.
* Seri konsol, [Windows VM 'leri](../windows/serial-console.md)için de kullanılabilir.
* [Önyükleme tanılaması](boot-diagnostics.md)hakkında daha fazla bilgi edinin.

