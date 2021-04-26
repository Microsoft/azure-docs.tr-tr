---
title: Azure Site Recovery ile Mobility hizmeti gönderme yükleme sorunlarını giderme
description: Azure Site Recovery ile olağanüstü durum kurtarma için çoğaltmayı etkinleştirirken Mobility hizmeti yükleme hatalarıyla ilgili sorunları giderin.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98127881"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Mobility hizmeti anında yükleme sorunlarını giderme

Mobility hizmeti yüklemesi, çoğaltmayı etkinleştirmek için önemli bir adımdır. Bu adımın başarısı, Toplantı önkoşullarını ve desteklenen yapılandırmalarda çalışmayı gösterir. Mobility hizmeti yüklemesi sırasında yüz yüze olabilecek en yaygın sorunlar şunlardır:

* [Kimlik bilgisi/ayrıcalık hataları](#credentials-check-errorid-95107--95108)
* [Oturum açma sorunları](#login-failures-errorid-95519-95520-95521-95522)
* [Bağlantı hataları](#connectivity-failure-errorid-95117--97118)
* [Dosya ve yazıcı paylaşım hataları](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Yönetim Araçları (WMI) sorunları](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Desteklenmeyen işletim sistemleri](#unsupported-operating-systems)
* [Desteklenmeyen önyükleme yapılandırması](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Birim gölge kopyası hizmeti (VSS) yükleme sorunları](#vss-installation-failures)
* [Cihaz UUID 'SI yerine GRUB yapılandırmasında cihaz adı](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [Mantıksal birim Yöneticisi (LVM) birimi](#lvm-support-from-920-version)
* [Yeniden başlatma uyarıları](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

Çoğaltmayı etkinleştirdiğinizde, Azure Site Recovery Mobility hizmeti aracısını sanal makinenize (VM) yüklemeye çalışır. Bu işlemin bir parçası olarak yapılandırma sunucusu sanal makineyle bağlantı kurmaya çalışır ve aracıyı kopyalayabilir. Başarılı yüklemeyi etkinleştirmek için adım adım sorun giderme kılavuzunu izleyin.

## <a name="credentials-check-errorid-95107--95108"></a>Kimlik bilgileri denetimi (errorID: 95107 & 95108)

Çoğaltmayı etkinleştir sırasında seçilen kullanıcı hesabının geçerli ve doğru olduğunu doğrulayın. Azure Site Recovery, bir gönderme yüklemesi gerçekleştirmek için **kök** hesabın veya Kullanıcı hesabının **yönetici ayrıcalıklarına** sahip olmasını gerektirir. Aksi halde, gönderme yüklemesi kaynak makinede engellenir.

Windows için (**hata 95107**), Kullanıcı hesabının kaynak bilgisayarda bir yerel hesap veya etki alanı hesabıyla yönetim erişimi olduğunu doğrulayın. Bir etki alanı hesabı kullanmıyorsanız, yerel bilgisayarda Uzak Kullanıcı erişim denetimini devre dışı bırakmanız gerekir.

* Uzaktan Kullanıcı erişimi denetimini devre dışı bırakan bir kayıt defteri anahtarını el ile eklemek için:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Yeni bir Ekle `DWORD` : `LocalAccountTokenFilterPolicy`
  * Değeri olarak ayarlayın `1`

* Kayıt defteri anahtarını eklemek için, bir komut isteminden aşağıdaki komutu çalıştırın:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

Linux için (**hata 95108**), Mobility hizmeti aracısının başarıyla yüklenmesi için **kök** hesabı seçmeniz gerekir. Ayrıca, SSH Dosya Aktarım Protokolü (SFTP) hizmetlerinin çalışıyor olması gerekir. _Sshd_config_ dosyasında SFTP alt sistemi ve parola kimlik doğrulamasını etkinleştirmek için:

1. **Kök** kullanıcı olarak oturum açın.
1. _/Etc/ssh/sshd_config dosyasına_ gidin, ile başlayan satırı bulun `PasswordAuthentication` .
1. Satırın açıklamasını kaldırın ve değerini olarak değiştirin `yes` .
1. İle başlayan satırı bulun `Subsystem` ve satırın açıklamasını kaldırın.
1. Hizmeti yeniden başlatın `sshd` .

Seçilen Kullanıcı hesabının kimlik bilgilerini değiştirmek istiyorsanız, [Bu yönergeleri](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)izleyin.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Yetersiz ayrıcalık hatası (errorID: 95517)

Mobility Aracısı 'nı yüklemeye seçtiği kullanıcının yönetici ayrıcalıkları yoksa, yapılandırma sunucusu/genişleme işlem sunucusu 'nun Mobility Aracısı yazılımını kaynak makineye kopyalamasına izin verilmez. Bu hata, erişim reddedildi hatasının bir sonucudur. Kullanıcı hesabının yönetici ayrıcalıklarına sahip olduğundan emin olun.

Seçilen Kullanıcı hesabının kimlik bilgilerini değiştirmek istiyorsanız, [Bu yönergeleri](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)izleyin.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Yetersiz ayrıcalık hatası (errorID: 95518)

Kaynak makinede oturum açmaya çalışırken birincil etki alanı ve iş istasyonu arasında etki alanı güven ilişkisi kurulumu başarısız olduğunda, Mobility Aracısı yüklemesi 95518 hata KIMLIĞIYLE başarısız olur. Mobility Aracısı 'nı yüklemek için kullanılan Kullanıcı hesabının, kaynak makinenin birincil etki alanında oturum açmak için yönetim ayrıcalıklarına sahip olduğundan emin olun.

Seçilen Kullanıcı hesabının kimlik bilgilerini değiştirmek istiyorsanız, [Bu yönergeleri](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)izleyin.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Oturum açma hataları (errorID: 95519, 95520, 95521, 95522)

Bu bölümde kimlik bilgisi ve oturum açma hata iletileri açıklanmaktadır.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Kullanıcı hesabının kimlik bilgileri devre dışı bırakıldı (errorID: 95519)

Çoğaltmayı etkinleştirme sırasında seçilen kullanıcı hesabı devre dışı bırakıldı. Kullanıcı hesabını etkinleştirmek için, [Bu makaleye](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) başvurun veya metin _Kullanıcı_ adı ' nı gerçek kullanıcı adıyla değiştirerek aşağıdaki komutu çalıştırın.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Birden çok başarısız oturum açma denemesi nedeniyle kimlik bilgileri kilitlendi (errorID: 95520)

Bir makineye erişmek için birden çok başarısız yeniden deneme işlemi kullanıcı hesabını kilitleyecek. Hatanın nedeni şu olabilir:

* Yapılandırma kurulumu sırasında belirtilen kimlik bilgileri yanlış.
* Çoğaltmayı etkinleştirme sırasında seçilen kullanıcı hesabı yanlış.

[Bu yönergeleri](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) izleyerek seçilen kimlik bilgilerini değiştirin ve işlemi yeniden deneyin.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Kaynak makinede oturum açma sunucuları yok (errorID: 95521)

Bu hata, oturum açma sunucuları kaynak makinede kullanılabilir olmadığında oluşur. Oturum açma sunucuları kullanılamıyorsa, oturum açma istekleri başarısız olur ve Mobility Aracısı yüklenemez. Başarılı bir oturum açma işlemi için, kaynak makinede oturum açma sunucularının kullanılabilir olduğundan emin olun ve Netlogon hizmetini başlatın. Daha fazla bilgi için bkz. [Windows oturum açma senaryoları](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Oturum açma hizmeti kaynak makinede çalışmıyor (errorID: 95522)

Oturum açma hizmeti, kaynak makinenizde çalışmıyor ve oturum açma isteğinde başarısız oldu. Mobility Aracısı yüklenemiyor. Hatayı gidermek için aşağıdaki yöntemlerden birini kullanarak `Netlogon` hizmeti kaynak makinede başlatın:

* `Netlogon`Hizmeti bir komut isteminden başlatmak için komutunu çalıştırın `net start Netlogon` .
* Görev Yöneticisi 'nde `Netlogon` hizmeti başlatın.

## <a name="connectivity-failure-errorid-95117--97118"></a>Bağlantı hatası (errorID: 95117 & 97118)

Yapılandırma sunucusu/genişleme işlem sunucusu, Mobility Aracısı 'nı yüklemek için kaynak VM 'ye bağlanmaya çalışır. Bu hata, ağ bağlantısı sorunları olduğundan kaynak makineye erişilemediğinde oluşur.

Hatayı gidermek için:

* Kullanıcı hesabının, kaynak bilgisayarda, yerel bir hesap veya etki alanı hesabıyla yönetim erişimi olduğunu doğrulayın. Bir etki alanı hesabı kullanmıyorsanız, yerel bilgisayarda Uzak Kullanıcı erişim denetimini devre dışı bırakmanız gerekir.
  * Uzaktan Kullanıcı erişimi denetimini devre dışı bırakan bir kayıt defteri anahtarını el ile eklemek için:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Yeni bir Ekle `DWORD` : `LocalAccountTokenFilterPolicy`
    * Değeri olarak ayarlayın `1`
  * Kayıt defteri anahtarını eklemek için, bir komut isteminden aşağıdaki komutu çalıştırın:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Kaynak makinenize yapılandırma sunucusundan ping atabiliyor olduğunuzdan emin olun. Çoğaltmayı etkinleştir sırasında genişleme işlem sunucusu ' nu seçtiyseniz, kaynak makinenize işlem sunucusundan ping atadığınızı doğrulayın.

* Sanal makinenizde dosya ve yazıcı paylaşım hizmeti 'nin etkinleştirildiğinden emin olun. [Buradaki](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106)adımları kontrol edin.

* Sanal makinenizde WMI hizmetinin etkinleştirildiğinden emin olun. [Buradaki](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103)adımları kontrol edin.

* Sanal makinenizde bulunan ağ paylaşılan klasörlerinin işlem sunucusundan erişilebilir olduğundan emin olun. [Buradaki](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523)adımları kontrol edin.

* Aşağıdaki komutta gösterildiği gibi, kaynak sunucu makinesi komut satırından, `Telnet` HTTPS bağlantı noktası 135 üzerinde yapılandırma sunucusuna veya genişleme işlem sunucusuna ping çekmek için öğesini kullanın. Bu komut, ağ bağlantısı sorunları veya güvenlik duvarı bağlantı noktası engelleme sorunları olup olmadığını denetler.

  `telnet <CS/ scale-out PS IP address> <135>`

* Ayrıca, bir Linux sanal makinesi için:
  * En son OpenSSH, OpenSSH Server ve OpenSSL paketlerinin yüklü olup olmadığını denetleyin.
  * Secure Shell (SSH) özelliğinin etkinleştirildiğinden ve 22 numaralı bağlantı noktasında çalıştığından emin olun.
  * SFTP hizmetlerinin çalışıyor olması gerekir. _Sshd_config_ dosyasında SFTP alt sistemi ve parola kimlik doğrulamasını etkinleştirmek için:

    1. **Kök** kullanıcı olarak oturum açın.
    1. _/Etc/ssh/sshd_config_ dosyasına gidin, ile başlayan satırı bulun `PasswordAuthentication` .
    1. Satırın açıklamasını kaldırın ve değerini olarak değiştirin `yes` .
    1. İle başlayan satırı bulun `Subsystem` ve satırın açıklamasını kaldırın
    1. Hizmeti yeniden başlatın `sshd` .

* Bir süre sonra uygun yanıt yoksa bir bağlantı girişimi başarısız olmuş olabilir veya bağlı bir konak yanıt veremediği için kurulan bir bağlantı başarısız olabilir.
* Bağlantı/ağ/etki alanı ile ilgili bir sorun olabilir. Bunun nedeni, DNS adı sorunu çözme veya TCP bağlantı noktası tükenmesi sorunu da olabilir. Etki alanında bilinen herhangi bir sorun olup olmadığını denetleyin.

## <a name="connectivity-failure-errorid-95523"></a>Bağlantı hatası (errorID: 95523)

Bu hata, kaynak makinenin bulunduğu ağ bulunamadığında, silinmiş veya artık kullanılamadığı durumlarda oluşur. Hatayı çözmek için tek yol, ağın mevcut olduğundan emin olmak içindir.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Kaynak makinedeki ağ paylaşılan klasörlerine erişimi denetle (errorID: 95105, 95523)

Sanal makinenizde ağ paylaşılan klasörlerinin, belirtilen kimlik bilgileri kullanılarak Işlem sunucusu 'ndan (PS) uzaktan erişilebilir olup olmadığını doğrulayın. Erişimi onaylamak için: 

1. Işlem sunucusu makinenizde oturum açın.
2. Dosya Gezgini'ni açın. Adres çubuğunda yazın `\\<SOURCE-MACHINE-IP>\C$` ve ENTER ' a tıklayın.

    ![Klasörü PS 'de aç](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. Dosya Gezgini, kimlik bilgilerini ister. Kullanıcı adını ve parolayı girip Tamam ' a tıklayın. <br><br/>

    ![Kimlik bilgilerini belirtin](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Kaynak makine etki alanına katılmış ise, Kullanıcı adıyla birlikte etki alanı adını belirtin `<domainName>\<username>` . Kaynak makine iş grubsunsam yalnızca Kullanıcı adını sağlayın.

4. Bağlantı başarılı olursa, kaynak makinenin klasörleri Işlem sunucusundan uzaktan görünür olur.

    ![Kaynak makineden görünür klasörler](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Bağlantı başarısız olursa, lütfen tüm önkoşulların karşılanıp karşılanmadığını kontrol edin.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Dosya ve yazıcı paylaşım hizmetleri denetimi (errorID: 95105 & 95106)

Bir bağlantı denetiminden sonra, sanal makinenizde dosya ve yazıcı paylaşım hizmetinin etkin olduğunu doğrulayın. Bu ayarlar, Mobility aracısını kaynak makineye kopyalamak için gereklidir.

**Windows 2008 R2 ve önceki sürümler** için:

* Windows Güvenlik Duvarı aracılığıyla dosya ve yazdırma paylaşımını etkinleştirmek için
  1. **Denetim Masası**  >  **sistem ve güvenlik**  >  **Windows Güvenlik Duvarı**'nı açın. Sol bölmede, konsol ağacındaki **Gelişmiş ayarlar**  >  **gelen kuralları** ' nı seçin.
  1. Kurallar dosya ve yazıcı paylaşımı (NB-oturum-ın) ve dosya ve yazıcı paylaşımı (SMB-ın) konumunu bulun.
  1. Her kural için kurala sağ tıklayın ve **kuralı etkinleştir**' e tıklayın.

* Grup ilkesi ile dosya paylaşımını etkinleştirmek için:
  1. **Başlat**' a gidin, yazın `gpmc.msc` ve arama yapın.
  1. Gezinti bölmesinde, aşağıdaki klasörleri açın:   >    >    >  **Windows bileşenleri**  >  **ağ paylaşımı** Yönetim Şablonları yerel bilgisayar ilkesi Kullanıcı Yapılandırması.
  1. Ayrıntılar bölmesinde, **kullanıcıların profilleri içinde dosya paylaşmasını engelle**' ye çift tıklayın.

     Grup ilkesi ayarı devre dışı bırakmak ve kullanıcının dosya paylaşma özelliğini etkinleştirmek için **devre dışı**' yı seçin.

  1. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

  Daha fazla bilgi için bkz. [Grup İlkesi dosya paylaşımını etkinleştirme veya devre dışı bırakma](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Windows veya Linux 'un sonraki sürümleri için dosya ve yazıcı paylaşımını etkinleştirmek üzere, [VMware VM 'leri ve fiziksel sunucuları olağanüstü durum kurtarma Için Mobility hizmetini Install](vmware-azure-install-mobility-service.md) konusundaki yönergeleri izleyin.

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Yönetim Araçları (WMI) yapılandırma denetimi (hata kodu: 95103)

Dosya ve Yazıcı Hizmetleri denetimini tamamladıktan sonra, güvenlik duvarı üzerinden özel, genel ve etki alanı profilleri için WMI hizmetini etkinleştirin. Bu ayarlar, kaynak makinede uzaktan yürütmeyi tamamlayacak şekilde gereklidir.

WMI 'yi etkinleştirmek için:

1. **Denetim Masası**  >  **güvenliği** ' ne gidin ve **Windows Güvenlik Duvarı**' nı seçin.
1. **Ayarları Değiştir** ' i seçin ve ardından **özel durumlar** sekmesini seçin.
1. **Özel durumlar** penceresinde, güvenlik DUVARı üzerinden WMI trafiğini etkinleştirmek için WINDOWS YÖNETIM araçları (WMI) onay kutusunu seçin.

Aşağıdaki komutla, komut isteminden güvenlik duvarı üzerinden WMI trafiğini de etkinleştirebilirsiniz:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Diğer WMI sorun giderme makaleleri aşağıdaki makalelerde bulunabilir.

* [Temel WMI testi](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI sorunlarını giderme](/windows/win32/wmisdk/wmi-troubleshooting)
* [WMI betikleri ve WMI hizmetleriyle ilgili sorunları giderme](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Desteklenmeyen işletim sistemleri

Hatanın yaygın bir nedeni, desteklenmeyen bir işletim sistemi olabilir. Mobility hizmetinin başarılı bir şekilde yüklenmesi için desteklenen bir işletim sistemi ve çekirdek sürümü kullanın. Özel düzeltme eklerinin kullanılmasını önleyin.

Azure Site Recovery tarafından desteklenen işletim sistemlerinin ve çekirdek sürümlerinin listesini görüntülemek için bkz. [destek matrisi belgesi](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Desteklenmeyen önyükleme disk konfigürasyonları (errorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Önyükleme ve sistem bölümleri/birimleri aynı disk değil (errorID: 95309)

9,20 sürümünden önce, farklı disklerdeki önyükleme ve sistem bölümleri/birimleri desteklenmeyen bir yapılandırmadır. [9,20 sürümünden](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)itibaren bu yapılandırma desteklenir.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Önyükleme diski kullanılamıyor (errorID: 95310)

Önyükleme diski olmayan bir sanal makine korunamaz. Önyükleme diski, yük devretme işlemi sırasında bir sanal makinenin düzgün kurtarılmasını sağlar. Önyükleme diskinin yokluğu, yük devretmeden sonra makinenin önyüklenememesi hatasıyla sonuçlanır. Sanal makinenin bir önyükleme diski içerdiğinden emin olup işlemi yeniden deneyin. Ayrıca, aynı makinede birden fazla önyükleme diski desteklenmez.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Kaynak makinede birden fazla önyükleme diski var (errorID: 95311)

Birden çok önyükleme diskine sahip bir sanal makine [desteklenen bir yapılandırma](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage)değildir.

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Birden çok diskte sistem bölümü (errorID: 95313)

9,20 sürümünden önce, birden çok disk üzerindeki bir kök bölüm veya birim kurulumu desteklenmeyen bir yapılandırmadır. [9,20 sürümünden](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)itibaren bu yapılandırma desteklenir.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Koruma etkinleştirme, eş adı UUID (errorID: 95320) yerine GRUB yapılandırmasında belirtilen cihaz adı olarak başarısız oldu.

### <a name="possible-cause"></a>Olası nedeni

Genel Birleşik önyükleme yükleyicisi (GRUB) yapılandırma dosyaları (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/GRUB2/grub.cfg_ veya _/etc/default/grub_), parametre **kökünün** değerini içerebilir ve evrensel benzersiz tanımlayıcı (UUID) yerine gerçek cihaz adları olarak **sürdürülür** . VM 'nin yeniden başlatılması sırasında cihaz adlarının değiştirebilmeleri için UUID 'nin yaklaşımını Site Recovery. Örneğin, VM, yük devretme sırasında aynı ada sahip ve sorunlara neden olan bir ad ile çevrimiçi olamıyor olabilir.

Örnek:

- Aşağıdaki satır, GRUB dosyası _/Boot/GRUB2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Aşağıdaki satır, GRUB dosyası _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB hatları, parametre **kökü** için gerçek cihaz ADLARıNı ve UUID yerine **özgeçmişi** içerir.

### <a name="how-to-fix"></a>Nasıl düzeltileceğini

Cihaz adları karşılık gelen UUID ile değiştirilmelidir.

1. Komutu yürüterek cihazın UUID 'sini bulun `blkid \<device name>` .

   Örnek:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Şimdi, cihaz adını, benzer biçimde UUID ile değiştirin `root=UUID=\<UUID>` . Örneğin, _/Boot/GRUB2/grub.cfg_, _/Boot/GRUB2/grub.cfg_ veya _/etc/default/grub_ dosyalarında belirtilen kök ve yenıden başlatma parametresi için cihaz adlarını UUID ile değiştirmek, dosyalardaki satırlar aşağıdaki satıra benzer şekilde görünür:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Korumayı yeniden başlatın.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Mobility hizmetini yükleme yeniden başlatma uyarısıyla tamamlandı (errorID: 95265 & 95266)

Site Recovery Mobility hizmetinde, biri filtre sürücüsü olarak adlandırılan birçok bileşen vardır. Filtre sürücüsü yalnızca sistemin yeniden başlatılması sırasında sistem belleğine yüklenir. Filtre sürücüsü düzeltmeleri yalnızca, sistemin yeniden başlatılması sırasında yeni bir filtre sürücüsü yüklendiğinde gerçekleştirilebilir.

> [!IMPORTANT]
> Bu bir uyarıdır ve yeni aracı güncelleştirmesinden sonra bile mevcut çoğaltma çalışacaktır. Yeni filtre sürücüsünün avantajlarından yararlanmak istediğiniz zaman yeniden başlatmayı seçebilirsiniz, ancak yeniden başlatma durumunda eski filtre sürücüsü çalışmaya devam eder. Bu nedenle, bir yeniden başlatma olmadan bir güncelleştirme sonrasında, filtre sürücüsü dışında, **Mobility hizmetindeki diğer geliştirmelerin ve düzeltmelerin avantajları gerçekleştirilmiş olarak gerçekleştirilir**. Önerilse de, her yükseltmeden sonra yeniden başlatılması zorunlu değildir. Yeniden başlatmanın ne zaman zorunlu olduğu hakkında bilgi için, Azure Site Recovery ' deki hizmet güncelleştirmeleri 'ndeki [Mobility hizmeti yükseltmesinin ardından yeniden Başlat](service-updates-how-to.md#reboot-after-mobility-service-upgrade) bölümünü ayarlayın.

> [!TIP]
>Bakım pencereniz sırasında yükseltmeleri zamanlamaya yönelik en iyi yöntemler için Azure Site Recovery içindeki hizmet güncelleştirmelerinde [en son işletim sistemi/çekirdek desteğine](service-updates-how-to.md#support-for-latest-operating-systemskernels) bakın.

## <a name="lvm-support-from-920-version"></a>9,20 sürümünden LVM desteği

9,20 sürümünden önce mantıksal birim Yöneticisi (LVM) yalnızca veri diskleri için desteklenmelidir. `/boot`Bölüm BIR LVM birimi değil disk bölümünde olmalıdır.

[9,20 sürümünden](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)başlayarak, [LVM 'deki işletim sistemi diski](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) desteklenir.

## <a name="insufficient-space-errorid-95524"></a>Yetersiz alan (errorID: 95524)

Mobility Aracısı kaynak makineye kopyalandığında, en az 100 MB boş alan gerekir. Kaynak makinenizin gerekli miktarda boş alana sahip olduğundan emin olun ve işlemi yeniden deneyin.

## <a name="low-system-resources"></a>Düşük sistem kaynakları

Bu sorun için görülen olası hata kimlikleri 95572 ve 95573 ' dir. Bu sorun, sistem kullanılabilir belleğin düşük olması durumunda oluşur ve Mobility hizmeti yüklemesi için bellek ayıramayabilir. Yüklemenin devam edebilmesi ve başarıyla tamamlanabilmesi için yeterli bellek boşaldığınızdan emin olun.

## <a name="vss-installation-failures"></a>VSS yükleme sorunları

Birim gölge kopyası hizmeti (VSS) yüklemesi, Mobility Aracısı yüklemesinin bir parçasıdır. Bu hizmet, uygulamayla tutarlı kurtarma noktaları oluşturmak için sürecinde kullanılır. VSS yüklemesi sırasında oluşan başarısızlıklar, birden çok nedenden dolayı oluşabilir. Hataları tam olarak belirlemek için _C:\programdata\asrsetuplogs\asrunifiedagentınstaller.log_ dosyasına bakın. Yaygın hatalardan bazıları ve çözüm adımları aşağıdaki bölümde vurgulanır.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS hatası-2147023170 [0X800706in]-çıkış kodu 511

Virüsten koruma yazılımı Azure Site Recovery hizmetleri işlemlerini engellemede bu sorun genellikle görülür.

Bu sorunu çözmek için:

1. [Virüsten koruma programından klasör dışlamaları](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)listesini gözden geçirin.
1. Windows 'da DLL kaydının engelini kaldırmak için virüsten koruma sağlayıcınız tarafından yayımlanan yönergeleri izleyin.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS hatası 7 [0x7]-çıkış kodu 511

Bu hata, VSS 'yi yüklemek için yeterli bellek olmadığından oluşan bir çalışma zamanı hatasıdır. Bu işlemin başarılı bir şekilde tamamlanması için disk alanını artırın.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS hatası-2147023824 [0x80070430]-çıkış kodu 517

Bu hata, Azure Site Recovery VSS sağlayıcısı hizmeti [silinmek üzere işaretlendiğinde](/previous-versions/ms838153(v=msdn.10))oluşur. Aşağıdaki komutu çalıştırarak VSS 'yi kaynak makineye el ile yüklemeyi deneyin:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS hatası-2147023841 [0x8007041F]-çıkış kodu 512

Bu hata, Azure Site Recovery VSS sağlayıcısı hizmet veritabanı [kilitlendiğinde](/previous-versions/ms833798(v=msdn.10))oluşur. Bir komut isteminden aşağıdaki komutu çalıştırarak VSS 'yi kaynak makineye el ile yüklemeyi deneyin:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Bir hata olduğunda, bir virüsten koruma programının veya diğer hizmetlerin **Başlangıç** durumunda takılı olup olmadığını denetleyin. **Başlangıç** durumundaki bir işlem, veritabanı Hizmetleri üzerindeki kilidi koruyabilir. VSS sağlayıcısı yükleme sırasında hatalara yol açacaktır. Hiçbir hizmetin **Başlangıç** durumunda olmadığından emin olun ve ardından yukarıdaki işlemi yeniden deneyin.

### <a name="vss-exit-code-806"></a>VSS çıkış kodu 806

Bu hata, yükleme için kullanılan Kullanıcı hesabının komutunu yürütmek için izinlere sahip olmadığı durumlarda oluşur `CSScript` . Betiği yürütmek için Kullanıcı hesabına gerekli izinleri sağlayın ve işlemi yeniden deneyin.

### <a name="other-vss-errors"></a>Diğer VSS hataları

Bir komut isteminden aşağıdaki komutu çalıştırarak VSS sağlayıcısı hizmetini kaynak makineye el ile yüklemeyi deneyin:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS hatası-0x8004E00F

Bu hata genellikle, içindeki sorunlar nedeniyle `DCOM` ve kritik bir durumda olduğundan Mobility aracısının yüklenmesi sırasında oluşur `DCOM` .

Hatanın nedenini öğrenmek için aşağıdaki yordamı kullanın.

### <a name="examine-the-installation-logs"></a>Yükleme günlüklerini inceleyin

1. _C:\programdata\asrsetuplogs\asrunifiedagentınstaller.log_ konumunda bulunan yükleme günlüğünü açın.
2. Aşağıdaki hatanın varlığı bu sorunu gösterir:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Bu sorunu çözmek için:

DCOM sorununu çözme hakkında yardım almak için [Microsoft Windows platformu ekibine](https://aka.ms/Windows_Support) başvurun.

DCOM sorunu çözüldüğünde, bir komut isteminden aşağıdaki komutu kullanarak Azure Site Recovery VSS sağlayıcısını el ile yeniden yükleyin:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Olağanüstü durum kurtarma gereksinimleriniz için uygulama tutarlılığı önemli değilse VSS sağlayıcısı yüklemesini atlayabilirsiniz.

Azure Site Recovery VSS sağlayıcısı yüklemesini atlamak ve Azure Site Recovery VSS sağlayıcısı yükleme sonrası yüklemeyi el ile yüklemek için:

1. Mobility hizmetini yükler. Yükleme adım: **yükleme sonrası yapılandırma** sırasında başarısız olur.
1. VSS yüklemesini atlamak için:
   1. Şu adreste bulunan Azure Site Recovery Mobility hizmeti yükleme dizinini açın:

      _C:\Program Files (x86) \Microsoft Azure Site Recovery\agent_

   1. Azure Site Recovery VSS sağlayıcısı yükleme betikleri _InMageVSSProvider_Install_ ve _InMageVSSProvider_Uninstall. cmd_ ' ye aşağıdaki satırları ekleyerek her zaman başarılı olacak şekilde değiştirin:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Mobility Aracısı 'nı el ile yükleme.
1. Yükleme başarılı olur ve sonraki adıma geçerse, ' yi **yapılandırın**, eklediğiniz satırları kaldırın.
1. VSS sağlayıcısını yüklemek için yönetici olarak bir komut istemi açın ve aşağıdaki komutu çalıştırın:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Azure Site Recovery VSS sağlayıcısının Windows hizmetlerinde bir hizmet olarak yüklendiğini doğrulayın. VSS sağlayıcısının listelendiğini doğrulamak için bileşen hizmeti MMC 'YI açın.
1. VSS sağlayıcısı yüklemesi başarısız olmaya devam ederse, şifreleme uygulama programlama arabirimindeki (CAPı2) izin hatalarını çözümlemek için teknik destekle çalışın.

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Küme hizmeti küme dışı makinede etkinleştirildiğinden VSS sağlayıcısı yüklemesi başarısız oluyor

Bu sorun, Azure Site Recovery VSS sağlayıcısı yüklemesi sırasında Azure Site Recovery Mobility Aracısı yüklemesinin başarısız olmasına neden olur. Hata, `COM+` VSS sağlayıcısı yüklemesinin önlediği bir sorun nedeniyle oluşur.

### <a name="to-identify-the-issue"></a>Sorunu belirlemek için

Configuration Server 'da _C:\programdata\asrsetuplogs\uploadedlogs yolunda bulunan günlükte \<date-time> UA_InstallLogFile. log dosyasına_ aşağıdaki özel durumu bulacaksınız:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Bu sorunu çözmek için:

1. Bu makinenin küme dışı bir makine olduğunu ve küme bileşenlerinin kullanılmadığını doğrulayın.
1. Bileşenler kullanılmıyorsa, küme bileşenlerini makineden kaldırın.

## <a name="drivers-are-missing-on-the-source-server"></a>Kaynak sunucuda sürücüler eksik

Mobility Aracısı yüklemesi başarısız olursa, bazı denetim kümelerinde gerekli sürücülerden bazılarının eksik olup olmadığını öğrenmek için _C:\programdata\asrsetuplogs_ altındaki günlükleri inceleyin.

Bu sorunu çözmek için:

1. Gibi bir kayıt defteri Düzenleyicisi kullanarak `regedit.msc` kayıt defterini açın.
1. Düğümünü açın `HKEY_LOCAL_MACHINE\SYSTEM` .
1. `SYSTEM`Düğümünde denetim kümelerini bulun.
1. Her bir denetim kümesini açın ve aşağıdaki Windows sürücülerinin mevcut olduğunu doğrulayın:

   * ATAPI
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Eksik sürücüleri yeniden yükleyin.

## <a name="next-steps"></a>Sonraki adımlar

VMware VM 'Leri için olağanüstü durum kurtarmayı ayarlama hakkında [daha fazla bilgi edinin](vmware-azure-tutorial.md) .
