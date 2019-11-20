---
title: Azure Site Recovery f ile Mobility hizmeti anında yükleme sorunlarını giderme
description: Azure Site Recovery ile olağanüstü durum kurtarma için çoğaltmayı etkinleştirirken Mobility hizmeti yükleme hatalarıyla ilgili sorunları giderin.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 09/11/2019
ms.openlocfilehash: 3646499ad2104566cb82f3f26c6b55d05f84dc7d
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953788"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Mobility hizmeti anında yükleme sorunlarını giderme 

Mobility hizmetinin yüklenmesi, çoğaltmayı etkinleştirme sırasında önemli bir adımdır. Bu adımın başarısı yalnızca Toplantı önkoşullarını ve desteklenen yapılandırmalara göre çalışır. Mobility hizmeti yüklemesi sırasında en sık kullandığınız sorunlar şunlardır:

* [Kimlik bilgisi/ayrıcalık hataları](#credentials-check-errorid-95107--95108)
* [Oturum açma sorunları](#login-failures-errorid-95519-95520-95521-95522)
* [Bağlantı hataları](#connectivity-failure-errorid-95117--97118)
* [Dosya ve yazıcı paylaşım hataları](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [WMI sorunları](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Desteklenmeyen Işletim sistemleri](#unsupported-operating-systems)
* [Desteklenmeyen Önyükleme yapılandırmaları](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [VSS yükleme hataları](#vss-installation-failures)
* [Cihaz UUID 'SI yerine GRUB yapılandırmasında cihaz adı](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM birimi](#lvm-support-from-920-version)
* [Yeniden başlatma uyarıları](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Çoğaltmayı etkinleştirdiğinizde Azure Site Recovery, sanal makinenize Mobility hizmeti Aracısı 'nı yüklemeye çalışır. Bunun bir parçası olarak, yapılandırma sunucusu sanal makineyle bağlantı kurmaya çalışır ve aracıyı kopyalayabilir. Başarılı yüklemeyi etkinleştirmek için aşağıda verilen adım adım sorun giderme kılavuzunu izleyin.

## <a name="credentials-check-errorid-95107--95108"></a>Kimlik bilgileri denetimi (errorID: 95107 & 95108)

* Çoğaltmayı etkinleştir sırasında seçilen kullanıcı hesabının **geçerli, doğru**olduğunu doğrulayın.
* Azure Site Recovery, istek temelli yüklemeyi gerçekleştirmek için **yönetici ayrıcalıklarına** sahip **kök** hesap veya Kullanıcı hesabı gerektirir. Aksi takdirde, gönderme yüklemesi kaynak makinede engellenir.
  * Windows için (**hata 95107**), Kullanıcı hesabının kaynak makinede yerel veya etki alanı yönetim erişiminin olduğunu doğrulayın.
  * Bir etki alanı hesabı kullanmıyorsanız, yerel bilgisayarda Uzak Kullanıcı erişim denetimini devre dışı bırakmanız gerekir.
    * Uzak Kullanıcı erişim denetimini devre dışı bırakmak için HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System kayıt defteri anahtarı altında yeni bir DWORD: LocalAccountTokenFilterPolicy ekleyin. Değeri 1 olarak ayarlayın. Bu adımı yürütmek için komut isteminden aşağıdaki komutu çalıştırın:

         `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
  * Linux için (**hata 95108**), Mobility aracısının başarıyla yüklenmesi için kök hesabı seçmeniz gerekir. Ayrıca, SFTP hizmetlerinin çalışıyor olması gerekir. Sshd_config dosyasında SFTP alt sistemi ve parola kimlik doğrulamasını etkinleştirmek için:
    1. Kök olarak oturum açın.
    2. /Etc/ssh/sshd_config dosyasına gidin, Passwordaduthentication ile başlayan satırı bulun.
    3. Satırın açıklamasını kaldırın ve değeri Evet olarak değiştirin.
    4. Alt sistem ile başlayan satırı bulun ve satırın açıklamasını kaldırın.
    5. SSHD hizmetini yeniden başlatın.

Seçilen Kullanıcı hesabının kimlik bilgilerini değiştirmek isterseniz, [burada](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)verilen yönergeleri izleyin.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Yetersiz ayrıcalık hatası (errorID: 95517)

Mobility Aracısı 'nı yüklemeye seçtiği kullanıcının yönetici ayrıcalıkları yoksa, yapılandırma sunucusu/genişleme işlem sunucusu 'nun, Mobility Aracısı yazılımını kaynak makineye kopyalamasına izin verilmez. Bu nedenle, bu hata erişim reddedildi hatasının bir sonucudur. Kullanıcı hesabının yönetici ayrıcalıklarına sahip olduğundan emin olun.

Seçilen Kullanıcı hesabının kimlik bilgilerini değiştirmek isterseniz, [burada](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)verilen yönergeleri izleyin.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Yetersiz ayrıcalık hatası (errorID: 95518)

Kaynak makinede oturum açmaya çalışırken birincil etki alanı ve iş istasyonu arasında etki alanı güven ilişkisi kurulumu başarısız olduğunda, Mobility Aracısı yüklemesi 95518 hata KIMLIĞIYLE başarısız olur. Bu nedenle, Mobility Aracısı 'nı yüklemek için kullanılan Kullanıcı hesabının, kaynak makinenin birincil etki alanında oturum açmak için yönetim ayrıcalıklarına sahip olduğundan emin olun.

Seçilen Kullanıcı hesabının kimlik bilgilerini değiştirmek isterseniz, [burada](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)verilen yönergeleri izleyin.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Oturum açma hataları (errorID: 95519, 95520, 95521, 95522)

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Kullanıcı hesabının kimlik bilgileri devre dışı bırakıldı (errorID: 95519)

Çoğaltmayı etkinleştirme sırasında seçilen kullanıcı hesabı devre dışı bırakıldı. Kullanıcı hesabını etkinleştirmek için [buradaki](https://aka.ms/enable_login_user) makaleye başvurun veya metin *Kullanıcı* adı ' nı gerçek kullanıcı adıyla değiştirerek aşağıdaki komutu çalıştırın.
`net user 'username' /active:yes`

### <a name="credentials-locked-out-due-to-multiple-failed-login-attempts-errorid-95520"></a>Birden çok başarısız oturum açma denemesi nedeniyle kimlik bilgileri kilitlendi (errorID: 95520)

Bir makineye erişmek için birden çok başarısız yeniden deneme işlemi kullanıcı hesabını kilitleyecek. Hatanın nedeni şu olabilir:

* Yapılandırma kurulumu sırasında belirtilen kimlik bilgileri yanlış veya
* Çoğaltmayı etkinleştirme sırasında seçilen kullanıcı hesabı yanlış

Bu nedenle, [burada](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) verilen yönergeleri izleyerek seçilen kimlik bilgilerini değiştirin ve bir süre sonra işlemi yeniden deneyin.

### <a name="logon-servers-are-not-available-on-the-source-machine-errorid-95521"></a>Kaynak makinede oturum açma sunucuları yok (errorID: 95521)

Bu hata, oturum açma sunucuları kaynak makinede kullanılabilir olmadığında oluşur. Oturum açma sunucularının kullanım dışı kalması, oturum açma isteğinin başarısız olmasına neden olur ve bu nedenle Mobility Aracısı yüklenemez. Başarılı oturum açma için, kaynak makinede oturum açma sunucularının kullanılabilir olduğundan emin olun ve oturum açma hizmetini başlatın. Ayrıntılı yönergeler için bkz. KB [139410](https://support.microsoft.com/en-in/help/139410/err-msg-there-are-currently-no-logon-servers-available) hata Iletisi: Şu anda kullanılabilir oturum açma sunucusu yok.

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Oturum açma hizmeti kaynak makinede çalışmıyor (errorID: 95522)

Oturum açma hizmeti, kaynak makinenizde çalışmıyor ve oturum açma isteğinde başarısız oldu. Bu nedenle, Mobility Aracısı yüklenemez. Sorunu gidermek için, başarılı oturum açma için kaynak makinede oturum açma hizmetinin çalıştığından emin olun. Oturum açma hizmetini başlatmak için komut isteminden "net start Logon" komutunu çalıştırın veya Görev Yöneticisi 'nden "NetLogon" hizmetini başlatın.

## <a name="connectivity-failure-errorid-95117--97118"></a>**Bağlantı hatası (errorID: 95117 & 97118)**

Yapılandırma sunucusu/genişleme işlem sunucusu, Mobility Aracısı 'nı yüklemek için kaynak VM 'ye bağlanmaya çalışır. Bu hata, ağ bağlantısı sorunları nedeniyle kaynak makineye ulaşılamadığınızda oluşur. Çözümlemek için

* Kaynak makinenize yapılandırma sunucusundan ping atabildiğinizden emin olun. Çoğaltmayı etkinleştir sırasında genişleme işlem sunucusu ' nu seçtiyseniz, kaynak makinenize işlem sunucusundan ping gönderebildiğinizden emin olun.
  * Kaynak sunucu makinesi komut satırından, ağ bağlantısı sorunları veya güvenlik duvarı bağlantı noktası engelleme sorunları olup olmadığını görmek için aşağıda gösterildiği gibi, yapılandırma sunucusu/genişleme işlem sunucusuna HTTPS bağlantı noktası (135) ile ping göndermek için Telnet kullanın.

     `telnet <CS/ scale-out PS IP address> <135>`
* Ayrıca, **LINUX VM**için
  * En son OpenSSH, OpenSSH-Server ve OpenSSL paketlerinin yüklü olup olmadığını denetleyin.
  * Secure Shell (SSH) özelliğinin etkinleştirildiğinden ve 22 numaralı bağlantı noktasında çalıştığından emin olun.
  * SFTP hizmetlerinin çalışıyor olması gerekir. Sshd_config dosyasında SFTP alt sistemi ve parola kimlik doğrulamasını etkinleştirmek için
    * Kök olarak oturum açın.
    * /Etc/ssh/sshd_config dosyasına gidin, Passwordaduthentication ile başlayan satırı bulun.
    * Satırın açıklamasını kaldırın ve değeri Evet olarak değiştirin
    * Alt sistem ile başlayan satırı bulun ve satırın açıklamasını kaldırın
    * SSHD hizmetini yeniden başlatın.
* Bir süre sonra doğru yanıt yoksa bağlantı girişimi başarısız olabilir veya bağlı konak yanıt veremediği için bağlantı kurulamadı.
* Bağlantı/ağ/etki alanı ile ilgili bir sorun olabilir. Bunun nedeni, sorun çözümleme veya TCP bağlantı noktası tükenmesi sorunu da DNS adıdır. Etki alanında bilinen herhangi bir sorun olup olmadığını denetleyin.

## <a name="connectivity-failure-errorid-95523"></a>Bağlantı hatası (errorID: 95523)

Bu hata, kaynak makinenin bulunduğu ağ bulunamadığında veya silinmiş ya da artık kullanılamadığı durumlarda oluşur. Hatayı çözmek için tek yol, ağın mevcut olduğundan emin olmanızı sağlar.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Dosya ve yazıcı paylaşım hizmetleri denetimi (errorID: 95105 & 95106)

Bağlantı denetimi sonrasında, sanal makinenizde dosya ve yazıcı paylaşım hizmeti 'nin etkin olduğunu doğrulayın. Bu ayarlar, Mobility aracısını kaynak makineye kopyalamak için gereklidir.

**Windows 2008 R2 ve önceki sürümleri**için

* Windows Güvenlik Duvarı aracılığıyla dosya ve yazdırma paylaşımını etkinleştirmek için
  * Denetim Masası 'nı açın > sistem ve güvenlik-> Windows Güvenlik Duvarı-> sol bölmede, Gelişmiş ayarlar ' a tıklayın > konsol ağacındaki gelen kurallar ' a tıklayın.
  * Kurallar dosya ve yazıcı paylaşımı (NB-oturum-ın) ve dosya ve yazıcı paylaşımı (SMB-ın) konumunu bulun. Her kural için kurala sağ tıklayın ve **kuralı etkinleştir**' e tıklayın.
* grup ilkesi ile dosya paylaşımını etkinleştirmek için
  * Başlat ' a gidin, GPMC. msc yazın ve arama yapın.
  * Gezinti bölmesinde şu klasörleri açın: yerel bilgisayar Ilkesi, Kullanıcı Yapılandırması, Yönetim Şablonları, Windows bileşenleri ve ağ paylaşımı.
  * Ayrıntılar bölmesinde, **kullanıcıların profilleri içinde dosya paylaşmasını engelle**' ye çift tıklayın. Grup ilkesi ayarı devre dışı bırakmak ve kullanıcının dosya paylaşma özelliğini etkinleştirmek için devre dışı ' ya tıklayın. Değişikliklerinizi kaydetmek için Tamam ' ı tıklatın. Daha fazla bilgi için bkz. [Grup İlkesi dosya paylaşımını etkinleştirme veya devre dışı bırakma](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

**Daha sonraki sürümler**için, dosya ve yazıcı paylaşımını etkinleştirmek üzere [VMware VM 'leri ve fiziksel sunucuları olağanüstü durum kurtarma Için Mobility hizmetini yüklerken](vmware-azure-install-mobility-service.md) sunulan yönergeleri izleyin.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Yönetim Araçları (WMI) yapılandırma denetimi (hata kodu: 95103)

Dosya ve Yazıcı Hizmetleri denetimini tamamladıktan sonra, güvenlik duvarı aracılığıyla özel, genel ve etki alanı profilleri için WMI hizmetini etkinleştirin. Bu ayarlar, kaynak makinede uzaktan yürütmeyi tamamlayacak şekilde gereklidir. Etkinleştirmek için

* Denetim Masası ' na gidin, güvenlik ' e ve ardından Windows Güvenlik Duvarı ' na tıklayın.
* Ayarları Değiştir ' e tıklayın ve ardından özel durumlar sekmesine tıklayın.
* Özel durumlar penceresinde, güvenlik duvarı üzerinden WMI trafiğini etkinleştirmek için Windows Yönetim Araçları (WMI) onay kutusunu seçin. 

WMI trafiğini komut isteminde güvenlik duvarı üzerinden de etkinleştirebilirsiniz. Aşağıdaki komutu kullanın `netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`
Diğer WMI sorun giderme makaleleri aşağıdaki makalelerde bulunabilir.

* [Temel WMI testi](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI sorunlarını giderme](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI betikleri ve WMI hizmetleriyle ilgili sorunları giderme](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="unsupported-operating-systems"></a>Desteklenmeyen Işletim sistemleri

Hatanın en yaygın bir nedeni, desteklenmeyen işletim sisteminden kaynaklanabilir. Mobility hizmetinin başarıyla yüklenmesi için desteklenen Işletim sistemi/çekirdek sürümünde olduğunuzdan emin olun. Özel düzeltme ekinin kullanımından kaçının.
Azure Site Recovery tarafından desteklenen işletim sistemlerinin ve çekirdek sürümlerinin listesini görüntülemek için [destek matrisi belgemizi](vmware-physical-azure-support-matrix.md#replicated-machines)inceleyin.

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Desteklenmeyen önyükleme disk konfigürasyonları (errorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-are-not-the-same-disk-errorid-95309"></a>Önyükleme ve sistem bölümleri/birimleri aynı disk değil (errorID: 95309)

9,20 sürümünden önce, farklı disklerdeki önyükleme ve sistem bölümleri/birimleri desteklenmeyen bir yapılandırmadır. [9,20 sürümünden](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)bu yapılandırma desteklenir. Bu destek için en son sürümü kullanın.

### <a name="the-boot-disk-is-not-available-errorid-95310"></a>Önyükleme diski kullanılamıyor (errorID: 95310)

Önyükleme diski olmayan bir sanal makine korunamaz. Bu, yük devretme işlemi sırasında sanal makinenin düzgün kurtarılmasını sağlamaktır. Önyükleme diskinin yokluğu, yük devretmeden sonra makinenin önyüklenememesi nedeniyle oluşur. Sanal makinenin önyükleme diski içerdiğinden emin olup işlemi yeniden deneyin. Ayrıca, aynı makinede birden fazla önyükleme diskinin desteklenmediğini unutmayın.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Kaynak makinede birden fazla önyükleme diski var (errorID: 95311)

Birden çok önyükleme diskine sahip bir sanal makine desteklenen bir [yapılandırma](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)değildir.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Birden çok diskte sistem bölümü (errorID: 95313)

9,20 sürümünden önce, birden fazla diskte bulunan kök bölüm veya birim desteklenmeyen bir yapılandırmadır. [9,20 sürümünden](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)bu yapılandırma desteklenir. Bu destek için en son sürümü kullanın.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Koruma etkinleştirme, eş adı UUID (errorID: 95320) yerine GRUB yapılandırmasında belirtilen cihaz adı olarak başarısız oldu.

**Olası neden:** </br>
GRUB yapılandırma dosyaları ("/boot/grub/menu.lst", "/boot/grub/grub.cfg", "/Boot/GRUB2/grub.cfg" veya "/etc/default/grub"), parametre **kökünün** değerini IÇEREBILIR ve UUID yerine gerçek cihaz adları olarak **sürdürülür** . VM 'nin yeniden başlatılması sırasında cihaz adı değişebilir, çünkü yük devretme sırasında sorun ortaya çıkan aynı ada sahip sanal makine aynı ada sahip olmayabilir. Site Recovery Örneğin: </br>


- Aşağıdaki satır, GRUB dosyası **/Boot/GRUB2/grub.cfg**. <br>
  *Linux/Boot/vmlinuz-3.12.49-11-Default **root =/dev/sda2** $ {extra_cmdline} **sürdürülmesi =/dev/sda1** Splash = sessiz sessiz showopts*


- GRUB dosyasıdır aşağıdaki satırı **/boot/grub/menu.lst**
  *çekirdek /boot/vmlinuz-3.0.101-63-default **kök = / dev/sda2** **= / dev/sda1 Sürdür** splash sessiz crashkernel = 256M-:128M showopts vga = 0x314 =*

Yukarıdaki kalın dizeyi gözlemlerseniz, GRUB 'nin UUID yerine "root" ve "özgeçmişi" parametrelerinin gerçek cihaz adları vardır.
 
**Nasıl düzeltilir:**<br>
Cihaz adları karşılık gelen UUID ile değiştirilmelidir.<br>


1. "Blkıd \<cihaz adı >" komutunu yürüterek cihazın UUID 'sini bulun. Örneğin:<br>
   ```
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2 
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3" 
   ```

2. Şimdi, cihaz adını "root = UUID =\<UUID >" biçimindeki biçimde UUID ile değiştirin. Örneğin, "/Boot/GRUB2/grub.cfg", "/Boot/GRUB2/grub.cfg" veya "/etc/default/grub" dosyalarında yukarıda bahsedilen root ve özgeçmişi parametresi için, cihaz adlarını UUID ile değiştirdiğinizden, dosyalardaki satırlar benzer şekilde görünür. <br>
   *çekirdek/Boot/vmlinuz-3.0.101-63-Default **root = UUID = 62927e85-f7ba-40bc-9993-cc1feeb191e4** **özgeçmişi = UUID = 6f614b44-433b-431b-9ca1-4dd2f6f74f6b** tanıtım = sessiz crashkernel = 256D-: 128mshowopts VGA = 0x314*
3. Korumayı yeniden başlatın

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Mobility hizmetini yükleme yeniden başlatma uyarısıyla tamamlandı (errorID: 95265 & 95266)

Site Recovery Mobility hizmetinde, biri filtre sürücüsü olarak adlandırılan birçok bileşen vardır. Filtre sürücüsü sistem belleğine yalnızca sistem yeniden başlatma sırasında yüklenir. Filtre sürücüsü düzeltmelerinin yalnızca yeni bir filtre sürücüsü yüklendiğinde gerçekleştirilbileceği anlamına gelir. Bu, yalnızca sistem yeniden başlatma sırasında gerçekleşebilir.

Bu bir uyarı olduğunu ve yeni aracı güncelleştirmesinden sonra bile mevcut çoğaltmanın çalıştığını **lütfen unutmayın** . Yeni filtre sürücüsünün avantajlarından yararlanmak istediğiniz zaman yeniden başlatmayı seçebilirsiniz, ancak eski filtre sürücüsünü yeniden başlatmanız durumunda çalışmaya devam eder. Bu nedenle, filtre sürücüsünden ayrı olarak yeniden başlatma olmadan bir güncelleştirmeden sonra, **Mobility hizmetindeki diğer geliştirmelerin ve düzeltmelerin avantajları gerçekleştirilmiş**olur. Bu nedenle, önerilir, her yükseltmeden sonra yeniden başlatma zorunlu değildir. Yeniden başlatmanın zorunlu olduğu zaman hakkında daha fazla bilgi için, Azure Site Recovery ' deki hizmet güncelleştirmelerinde [Mobility Aracısı yükseltme bölümünden sonra kaynak makinenin yeniden başlatılmasını](https://aka.ms/v2a_asr_reboot) ayarlayın.

> [!TIP]
>Bakım pencereniz sırasında yükseltmeleri zamanlamaya yönelik en iyi yöntemler için, Azure Site Recovery ' deki hizmet güncelleştirmelerinde [en son işletim sistemi/çekirdek sürümleri Için destek](https://aka.ms/v2a_asr_upgrade_practice) bölümüne bakın.

## <a name="lvm-support-from-920-version"></a>9,20 sürümünden LVM desteği

9,20 sürümünden önce, LVM yalnızca veri diskleri için desteklendi. /Boot bir disk bölümünde olmalıdır ve LVM birimi olmamalıdır.

[9,20 sürümünden](https://support.microsoft.com/en-in/help/4478871/update-rollup-31-for-azure-site-recovery)itibaren, [LVM 'deki işletim sistemi diski](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) desteklenir. Bu destek için en son sürümü kullanın.

## <a name="insufficient-space-errorid-95524"></a>Yetersiz alan (errorID: 95524)

Mobility Aracısı kaynak makineye kopyalandığında, en az 100 MB boş alan gerekir. Bu nedenle, kaynak makinenizin gerekli boş alana sahip olduğundan emin olun ve işlemi yeniden deneyin.

## <a name="vss-installation-failures"></a>VSS yükleme sorunları

VSS yüklemesi, Mobility Aracısı yüklemesinin bir parçasıdır. Bu hizmet uygulamayla tutarlı kurtarma noktaları oluşturma işleminde kullanılır. VSS yüklemesi sırasında oluşan başarısızlıklar, birden çok nedenden dolayı oluşabilir. Hataları tam olarak belirlemek için **C:\programdata\asrsetuplogs\asrunifiedagentınstaller.log**dosyasına bakın. Aşağıdaki bölümde bazı yaygın hatalar ve çözüm adımları vurgulanır.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS hatası-2147023170 [0X800706in]-çıkış kodu 511

Virüsten koruma yazılımı Azure Site Recovery hizmetleri işlemlerini engellemede bu sorun genellikle görülür. Bu sorunu çözmek için:

1. [Burada](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)bahsedilen tüm klasörleri hariç tutun.
2. Windows 'da DLL kaydının engelini kaldırmak için virüsten koruma sağlayıcınız tarafından yayımlanan yönergeleri izleyin.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS hatası 7 [0x7]-çıkış kodu 511

Bu bir çalışma zamanı hatasıdır ve VSS 'yi yüklemek için yetersiz bellek nedeniyle oluşur. Bu işlemin başarılı bir şekilde tamamlanması için disk alanını artırtığınızdan emin olun.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS hatası-2147023824 [0x80070430]-çıkış kodu 517

Bu hata, Azure Site Recovery VSS sağlayıcısı hizmeti [silinmek üzere işaretlendiğinde](https://msdn.microsoft.com/library/ms838153.aspx)oluşur. Aşağıdaki komut satırını çalıştırarak VSS 'yi kaynak makineye el ile yüklemeyi deneyin

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS hatası-2147023841 [0x8007041F]-çıkış kodu 512

Bu hata, Azure Site Recovery VSS sağlayıcısı hizmet veritabanı [kilitlendiğinde](https://msdn.microsoft.com/library/ms833798.aspx)oluşur. Aşağıdaki komut satırını çalıştırarak VSS 'yi kaynak makineye el ile yüklemeyi deneyin

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Hata durumunda, herhangi bir virüsten koruma programının veya diğer hizmetlerin "başlangıç" durumunda takılı olup olmadığını denetleyin. Bu, veritabanı Hizmetleri üzerindeki kilidi koruyabilir. VSS sağlayıcısı yükleme sırasında hatalara yol açacaktır. Hiçbir hizmetin "başlangıç" durumunda olmadığından emin olun ve ardından yukarıdaki işlemi yeniden deneyin.

### <a name="vss-exit-code-806"></a>VSS çıkış kodu 806

Bu hata, yükleme için kullanılan Kullanıcı hesabının CSScript komutunu yürütmek için gereken izinlere sahip olmadığında oluşur. Betiği yürütmek için Kullanıcı hesabına gerekli izinleri sağlayın ve işlemi yeniden deneyin.

### <a name="other-vss-errors"></a>Diğer VSS hataları

Aşağıdaki komut satırını çalıştırarak VSS sağlayıcısı hizmetini kaynak makineye el ile yüklemeyi deneyin

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent>"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`



## <a name="vss-error---0x8004e00f"></a>VSS hatası-0x8004E00F

Bu hatayla genellikle, DCOM aracısının, DCOM 'daki sorunlar ve DCOM 'un kritik bir durumda olması nedeniyle Mobility Aracısı yüklenirken karşılaşıldı.

Hatanın nedenini öğrenmek için aşağıdaki yordamı kullanın.

**Yükleme günlüklerini inceleyin**

1. C:\programdata\asrsetuplogs\asrunifiedagentınstaller.log konumunda bulunan yükleme günlüğünü açın.
2. Aşağıdaki hatanın varlığı bu sorunu gösterir:

    Mevcut uygulamanın kaydı siliniyor...  Uygulama koleksiyonunu al Catalogue nesnesini oluşturma 

    HATAYLA

    - Hata kodu:-2147164145 [0x8004E00F]
    - Çıkış kodu: 802

Sorunu çözmek için:

DCOM sorununu çözme hakkında yardım almak için [Microsoft Windows platformu ekibine](https://aka.ms/Windows_Support) başvurun.

DCOM sorunu çözüldüğünde, aşağıdaki komutu kullanarak Azure Site Recovery VSS sağlayıcısını el ile yeniden yükleyin:
 
**C:\Program Files (x86) \Microsoft Azure Site Recovery\agent > "C:\Program Files (x86) \Microsoft Azure Site Recovery\agent\ InMageVSSProvider_Install. cmd**
  
Olağanüstü durum kurtarma gereksinimleriniz için uygulama tutarlılığı önemli değilse VSS sağlayıcısı yüklemesini atlayabilirsiniz. 

Azure Site Recovery VSS sağlayıcısı yüklemesini atlamak ve Azure Site Recovery VSS sağlayıcısı yükleme sonrası yüklemeyi el ile yüklemek için:

1. Mobility hizmetini yükler. 
   > [!Note]
   > 
   > Yükleme, ' yapılandırma yükleme sonrası ' adımında başarısız olur. 
2. VSS yüklemesini atlamak için:
   1. Şu adreste bulunan Azure Site Recovery Mobility hizmeti yükleme dizinini açın:
   
      C:\Program Files (x86) \Microsoft Azure Site Recovery\agent
   2. Azure Site Recovery VSS sağlayıcısı yükleme betikleri **nMageVSSProvider_Install** ve **InMageVSSProvider_Uninstall. cmd** ' ye aşağıdaki satırları ekleyerek her zaman başarılı olacak şekilde değiştirin:
    
      ```     
      rem @echo off
      setlocal
      exit /B 0
      ```

3. Mobility Aracısı yüklemesini el ile yeniden çalıştırın. 
4. Yükleme başarılı olur ve sonraki adıma geçerse, ' yi **yapılandırın**, eklediğiniz satırları kaldırın.
5. VSS sağlayıcısını yüklemek için yönetici olarak bir komut istemi açın ve aşağıdaki komutu çalıştırın:
   
    **C:\Program Files (x86) \Microsoft Azure Site Recovery\agent >. \ InMageVSSProvider_Install. cmd**

9. ASR VSS sağlayıcısının Windows hizmetlerinde bir hizmet olarak yüklendiğini doğrulayın ve ASR VSS sağlayıcısının listelendiğini doğrulamak için bileşen hizmeti MMC 'YI açın.
10. VSS sağlayıcısı yüklemesi başarısız olmaya devam ederse, CAPı2 içindeki izin hatalarını çözümlemek için CX ile çalışın.

## <a name="vss-provider-installation-fails-due-to-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Küme dışı makinede küme hizmetinin etkinleştirilmesi nedeniyle VSS sağlayıcısı yüklemesi başarısız oluyor

Bu sorun, Azure Site Recovery Mobility Aracısı yüklemesinin, bu site RecoveryR VSS sağlayıcısı yükleme adımı sırasında başarısız olmasına neden olur ve bu da VSS sağlayıcısı 'nın yüklenmesini engelleyen bir COM+ sorunu nedeniyle.
 
### <a name="to-identify-the-issue"></a>Sorunu belirlemek için

C:\ProgramData\ASRSetupLogs\UploadedLogs konumundaki yapılandırma sunucusunda bulunan günlükte, tarih-saat > UA_InstallLogFile. log ' u\<, aşağıdaki özel durumu görürsünüz:

COM+, Microsoft Dağıtılmış İşlem Düzenleyicisi ile iletişim kuramadı (HRESULT 'ten özel durum: 0x8004E00F)

Sorunu çözmek için:

1.  Bu makinenin küme dışı bir makine olduğunu ve küme bileşenlerinin kullanılmadığını doğrulayın.
3.  Bileşenler kullanılmıyorsa, küme bileşenlerini makineden kaldırın.

## <a name="drivers-are-missing-on-the-source-server"></a>Kaynak sunucuda sürücüler eksik

Mobility Aracısı yüklemesi başarısız olursa, bazı denetim kümelerinde gerekli sürücülerden bazılarının eksik olup olmadığını öğrenmek için C:\ProgramData\ASRSetupLogs altındaki günlükleri inceleyin.
 
Sorunu çözmek için:
  
1. Regedit. msc gibi bir kayıt defteri Düzenleyicisi kullanarak kayıt defterini açın.
2. HKEY_LOCAL_MACHINE \SYSTEM düğümünü açın.
3. SISTEM düğümünde denetim kümelerini bulun.
4. Her bir denetim kümesini açın ve aşağıdaki Windows sürücülerinin mevcut olduğunu doğrulayın:

   - ATAPI
   - VMBus
   - storflt
   - storvsc
   - intelide
 
Eksik sürücüleri yeniden yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

VMware VM 'Leri için olağanüstü durum kurtarmayı ayarlamayı [öğrenin](vmware-azure-tutorial.md) .
