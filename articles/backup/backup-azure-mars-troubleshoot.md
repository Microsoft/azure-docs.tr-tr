---
title: Azure Backup aracısında sorun giderme
description: Bu makalede Azure Backup aracısının yüklenmesi ve kaydettirilmesinde nasıl sorun giderileceği hakkında bilgi edinebilirsiniz.
ms.topic: troubleshooting
ms.date: 07/15/2019
ms.openlocfilehash: c662bf8c8d9490691f45254bef01618f17bd6e2a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518193"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı sorunlarını giderme

Bu makalede yapılandırma, kayıt, yedekleme ve geri yükleme sırasında görebileceğiniz hataların nasıl çözümleneceği açıklanır.

## <a name="basic-troubleshooting"></a>Temel sorun giderme

Microsoft Azure kurtarma hizmetleri (MARS) Aracısı ile ilgili sorun gidermeye başlamadan önce aşağıdakileri kontrol etmenizi öneririz:

- [Mars aracısının güncel olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Mars Aracısı ve Azure arasında ağ bağlantısına sahip olduğunuzdan emin olun](#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup).
- MARS 'ın çalıştığından emin olun (hizmet konsolunda). Gerekirse, yeniden başlatın ve işlemi yeniden deneyin.
- [Karalama klasörü konumunda %5 ila %10 boş birim alanı kullanılabilir olduğundan emin olun](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-).
- [Azure Backup ile çakışan başka bir işlem veya virüsten koruma yazılımı olup olmadığını kontrol edin](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup).
- Yedekleme işi uyarılarla tamamlanırsa, bkz. [yedekleme Işleri uyarı Ile tamamlandı](#backup-jobs-completed-with-warning)
- Zamanlanan yedekleme başarısız olursa, ancak el ile yedekleme çalışırsa, bkz. [yedeklemeler zamanlamaya göre Çalıştırılmadı](#backups-dont-run-according-to-schedule).
- İşletim sisteminin en son güncelleştirmelere sahip olduğundan emin olun.
- Desteklenmeyen [özniteliklerin ve desteklenmeyen özniteliklere sahip dosyaların yedeklemeden dışlandığından emin olun](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Korunan sistemdeki saatin doğru saat dilimine göre yapılandırıldığından emin olun.
- [Sunucuda .NET Framework 4.5.2 veya üzeri sürümünün yüklü olduğundan emin olun](https://www.microsoft.com/download/details.aspx?id=30653).
- Sunucunuzu bir kasaya yeniden kaydetmeye çalışıyorsanız:
  - Aracının sunucuda kaldırıldığından ve portaldan silindiğinden emin olun.
  - Başlangıçta sunucuyu kaydetmek için kullanılan parolayı kullanın.
- Çevrimdışı yedeklemeler için, yedeklemeye başlamadan önce Azure PowerShell 3.7.0 hem kaynak hem de kopya bilgisayarda yüklü olduğundan emin olun.
- Yedekleme aracısı bir Azure sanal makinesinde çalışıyorsa, [Bu makaleye](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)bakın.

## <a name="invalid-vault-credentials-provided"></a>Sağlanan kasa kimlik bilgileri geçersiz

**Hata iletisi**: geçersiz kasa kimlik bilgileri belirtildi. Dosya bozuk veya kurtarma hizmeti ile ilişkili en son kimlik bilgilerine sahip değil. (KIMLIK: 34513)

| Nedeni | Önerilen Eylemler |
| ---     | ---    |
| **Kasa kimlik bilgileri geçerli değil** <br/> <br/> Kasa kimlik bilgileri dosyaları bozulmuş olabilir, zaman aşımına uğradı veya *. vaultcredentials*'dan farklı bir dosya uzantısına sahip olabilir. (Örneğin, kayıt zamanından 10 günden daha uzun bir süre önce indirilmiş olabilir.)| Azure portal kurtarma hizmetleri kasasından [yeni kimlik bilgilerini indirin](backup-azure-file-folder-backup-faq.yml#where-can-i-download-the-vault-credentials-file-) . Ardından aşağıdaki adımları uygulayın: <ul><li> MARS 'ı zaten yüklediyseniz ve daha önce kaydolduysanız, Microsoft Azure Backup Agent MMC konsolunu açın. Ardından, yeni kimlik bilgileriyle kaydı gerçekleştirmek için **Eylemler** bölmesinde **sunucuyu kaydet** ' i seçin. <br/> <li> Yeni yükleme başarısız olursa yeni kimlik bilgileriyle yeniden yüklemeyi deneyin.</ul> **Note**: birden fazla kasa kimlik bilgileri dosyası indirildiyse, sonraki 10 gün için yalnızca en son dosya geçerlidir. Yeni bir kasa kimlik bilgileri dosyası indirmenizi öneririz.
| **Proxy sunucusu/güvenlik duvarı kaydı engelliyor** <br/>veya <br/>**İnternet bağlantısı yok** <br/><br/> Makinenizin veya proxy sunucunuzun sınırlı internet bağlantısı varsa ve gerekli URL 'Ler için erişim sağlanmıyorsanız, kayıt başarısız olur.| Şu adımları uygulayın:<br/> <ul><li> Sistemin internet bağlantısı olduğundan emin olmak için BT ekibinizle birlikte çalışın.<li> Proxy sunucunuz yoksa, aracıyı kaydettiğinizde ara sunucu seçeneğinin seçili olmadığından emin olun. [Proxy ayarlarınızı denetleyin](#verifying-proxy-settings-for-windows).<li> Bir güvenlik duvarı/proxy sunucunuz varsa, bu URL 'Lerin ve IP adreslerinin erişime sahip olduğundan emin olmak için ağ ekibinizle birlikte çalışın:<br/> <br> **URL’ler**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>`www.msftconnecttest.com`<br><br>**IP adresleri**<br>  20.190.128.0/18 <br>  40.126.0.0/18<br> <br/></ul></ul>Yukarıdaki sorun giderme adımlarını tamamladıktan sonra kaydetmeyi yeniden deneyin.<br></br> Bağlantınız Azure ExpressRoute ile çalışıyorsa, ayarların [Azure ExpressRoute desteği](backup-support-matrix-mars-agent.md#azure-expressroute-support)' nde açıklandığı şekilde yapılandırıldığından emin olun.
| **Virüsten koruma yazılımı kaydı engelliyor** | Sunucusunda virüsten koruma yazılımı yüklüyse, bu dosya ve klasörler için virüsten koruma taramasına gereken dışlama kurallarını ekleyin: <br/><ul> <li> CBengine.exe <li> CSC.exe<li> Karalama klasörü. Varsayılan konum C:\Program Files\Microsoft Azure Kurtarma Hizmetleri ' dir. <li> C:\Program Files\Microsoft Azure kurtarma hizmetleri \ bin klasöründe bin klasörü.

### <a name="additional-recommendations"></a>Ek öneriler

- C:/Windows/Temp adresine gidin ve. tmp uzantısıyla 60.000 ' den fazla veya 65.000 dosya olup olmadığını denetleyin. Varsa, bu dosyaları silin.
- Makinenin tarih ve saatinin yerel saat dilimiyle eşleştiğinden emin olun.
- Internet Explorer 'da [Bu sitelerin](install-mars-agent.md#verify-internet-access) güvenilen sitelerinize eklendiğinden emin olun.

### <a name="verifying-proxy-settings-for-windows"></a>Windows için proxy ayarları doğrulanıyor

1. [Sysinternals](/sysinternals/downloads/psexec) sayfasından PsExec 'yi indirin.
1. `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"`Yükseltilmiş bir komut isteminden çalıştırın.

   Bu komut, Internet Explorer 'ı açar.
1. **Araçlar**  >  **Internet seçenekleri**  >  **Bağlantılar**  >  **LAN ayarları**' na gidin.
1. Sistem hesabının ara sunucu ayarlarını kontrol edin.
1. Yapılandırılmış bir proxy yoksa ve proxy ayrıntıları sağlanmışsa, ayrıntıları kaldırın.
1. Bir ara sunucu yapılandırıldıysa ve proxy ayrıntıları yanlışsa, **Proxy IP** ve **bağlantı noktası** ayrıntılarının doğru olduğundan emin olun.
1. Internet Explorer'ı kapatın.

## <a name="unable-to-download-vault-credential-file"></a>Kasa kimlik bilgileri dosyası karşıdan yüklenemiyor

| Hata   | Önerilen Eylemler |
| ---     | ---    |
|Kasa kimlik bilgileri dosyası indirilemedi. (KIMLIK: 403) | <ul><li> Farklı bir tarayıcı kullanarak kasa kimlik bilgilerini indirmeyi deneyin veya şu adımları uygulayın: <ul><li> Internet Explorer 'ı başlatın. F12 ' i seçin. </li><li> **Ağ** sekmesine gidin ve önbelleği ve tanımlama bilgilerini temizleyin. </li> <li> Sayfayı yenileyin.<br></li></ul> <li> Aboneliğin devre dışı bırakılıp bırakılmadığını kontrol edin.<br></li> <li> Bir güvenlik duvarı kuralının indirmeyi engelleyip engellemediğini denetleyin. <br></li> <li> Kasadaki limiti tüketmemiş olduğunuzdan emin olun (kasa başına 50 makine).<br></li>  <li> Kullanıcının kasa kimlik bilgilerini indirmek ve kasaya bir sunucu kaydetmek için gereken Azure Backup izinlere sahip olduğundan emin olun. Bkz. [Azure Backup kurtarma noktalarını yönetmek Için Azure rol tabanlı erişim denetimi kullanma](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Kurtarma Hizmeti Aracısı, Microsoft Azure Backup'a bağlanamadı

| Hata  | Olası nedeni | Önerilen Eylemler |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure kurtarma hizmeti Aracısı Microsoft Azure Backup bağlantı kuramadı. (KIMLIK: 100050) Ağ ayarlarınızı denetleyin ve internet 'e bağlanabildiğinizden emin olun.<li>(407) proxy kimlik doğrulaması gerekiyor. |Ara sunucu bağlantıyı engelliyor. |  <ul><li>Internet Explorer 'da **Araçlar**  >  **Internet seçenekleri**  >  **güvenlik**  >  **İnternet**'e gidin. **Özel düzey** ' i seçin ve **dosya indirme** bölümüne gidin. **Etkinleştir**’i seçin.<p>Ayrıca, Internet Explorer 'daki güvenilen sitelerinize [URL 'ler ve IP adresleri](install-mars-agent.md#verify-internet-access) eklemeniz gerekebilir.<li>Ayarları bir proxy sunucu kullanacak şekilde değiştirin. Ardından proxy sunucusu ayrıntılarını sağlayın.<li> Makinenizin internet erişimi sınırlı ise, makinedeki veya proxy 'deki güvenlik duvarı ayarlarının bu [URL 'lere ve IP adreslerine](install-mars-agent.md#verify-internet-access)izin verildiğinden emin olun. <li>Sunucuda yüklü bir virüsten koruma yazılımınız varsa, bu dosyaları virüsten koruma taramasından çıkarın: <ul><li>CBEngine.exe (dpmra.exe yerine).<li>CSC.exe (.NET Framework ile ilgili). Sunucuda yüklü her .NET Framework sürümü için bir CSC.exe vardır. Etkilenen sunucudaki tüm .NET Framework sürümleri için CSC.exe dosyalarını dışlayın. <li>Karalama klasörü veya önbellek konumu. <br>Karalama klasörü veya önbellek yolu için varsayılan konum C:\Program Files\Microsoft Azure Recovery Services.<li>C:\Program Files\Microsoft Azure kurtarma hizmetleri \ bin klasöründe bin klasörü.

## <a name="the-specified-vault-credential-file-cannot-be-used-as-it-is-not-downloaded-from-the-vault-associated-with-this-server"></a>Belirtilen kasa kimlik bilgileri dosyası, bu sunucuyla ilişkili kasadan indirilmediğinden kullanılamıyor

| Hata  | Olası nedeni | Önerilen Eylemler |
| ---     | ---     | ---    |
| Belirtilen kasa kimlik bilgileri dosyası, bu sunucuyla ilişkili kasadan indirilmediğinden kullanılamıyor. (KIMLIK: 100110) Lütfen uygun kasa kimlik bilgilerini belirtin. | Kasa kimlik bilgileri dosyası, bu sunucunun zaten kayıtlı olduğu bir kasadan farklı. | Hedef makinenin ve kaynak makinenin aynı kurtarma hizmetleri kasasında kayıtlı olduğundan emin olun. Hedef sunucu zaten farklı bir kasaya kaydedilmişse, doğru kasaya kaydolmak için **sunucuyu kaydet** seçeneğini kullanın.  

## <a name="backup-jobs-completed-with-warning"></a>Yedekleme işleri uyarıyla tamamlandı

- MARS Aracısı yedekleme sırasında dosyalar ve klasörler üzerinde yineleme yaparken, yedeklemenin uyarılarla tamamlandı olarak işaretlenmesine neden olabilecek çeşitli koşullara karşılaşabilir. Bu koşullar sırasında, bir iş uyarılarla tamamlandı olarak gösterilir. Bu kadar iyidir, ancak en az bir dosyanın yedeklenmemesi anlamına gelir. Böylece iş bu dosyayı atladı, ancak veri kaynağında söz konusu tüm dosyaları yedekledi.

  ![Yedekleme işi uyarılarla tamamlandı](./media/backup-azure-mars-troubleshoot/backup-completed-with-warning.png)

- Yedeklemelerin dosyaları atlamasına neden olabilecek koşullar şunlardır:
  - Desteklenmeyen dosya öznitelikleri (örneğin: OneDrive klasöründe, sıkıştırılmış akış, yeniden ayrıştırma noktaları). Tüm liste için [destek matrisine](./backup-support-matrix-mars-agent.md#supported-file-types-for-backup)bakın.
  - Bir dosya sistemi sorunu
  - Başka bir işlem kesintiye uğramasını engelliyor (örneğin, dosyalar üzerinde tutamaçları tutan virüsten koruma yazılımı, MARS aracısının dosyalara erişmesini engelleyebilir)
  - Bir uygulama tarafından kilitlenen dosyalar  

- Yedekleme hizmeti, bu dosyaları günlük dosyasında şu adlandırma kuralına sahip başarısız olarak işaretler: *C:\Program Files\Microsoft Azure Recovery Service Agent \ temp* klasörü altında *LastBackupFailedFilesxxxx.txt* .
- Sorunu çözmek için, sorunun doğasını anlamak için günlük dosyasını gözden geçirin:

  | Hata kodu             | Olası                                             | Öneriler                                              |
  | ---------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
  | 0x80070570             | Dosya veya dizin bozuk ve okunamaz durumda. | Kaynak birimde **Chkdsk komutunu** çalıştırın.                             |
  | 0x80070002, 0x80070003 | Sistem belirtilen dosyayı bulamıyor.         | [Karalama klasörünün dolu olmadığından emin olun](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)  <br><br>  Boş alanın yapılandırıldığı birimin var olup olmadığını denetle (silinmedi)  <br><br>   [MARS aracısının makinede yüklü olan antivirüs dışında dışlandığından emin olun](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)  |
  | 0x80070005             | Erişim reddedildi                                    | [Virüsten koruma veya başka bir üçüncü taraf yazılımının erişimi engelleyip engellemediğini denetleyin](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)     |
  | 0x8007018b             | Bulut dosyasına erişim reddedildi.                | OneDrive dosyaları, git dosyaları veya makinede çevrimdışı durumda olabilecek diğer dosyalar |

- Başarılı yedeklemeler sağlamak için yedekleme ilkenizde desteklenmeyen, eksik veya silinen dosyaları dışlamak için [dışlama kuralları Ekle](./backup-azure-manage-mars.md#add-exclusion-rules-to-existing-policy) ' yi kullanabilirsiniz.

- Üst düzey klasörde aynı ada sahip korumalı klasörleri silip yeniden oluşturmayı önleyin. Bunun yapılması, kritik bir tutarsızlığın algılanmasıyla ilgili uyarılarla birlikte yedeklemenin tamamlanmasına neden olabilir *, bu nedenle değişiklikler çoğaltılamaz.*  Klasörleri silip yeniden oluşturmanız gerekiyorsa, bunu korumalı en üst düzey klasörün altındaki alt klasörlerde yapmayı göz önünde bulundurun.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Güvenli yedekleme işlemleri için şifreleme anahtarı ayarlanamadı

| Hata | Olası nedenler | Önerilen Eylemler |
| ---     | ---     | ---    |
| <br />Güvenli yedeklemeler için şifreleme anahtarı ayarlanamadı. Etkinleştirme tamamen başarılı olmadı, ancak şifreleme parolası aşağıdaki dosyaya kaydedildi. |<li>Sunucu zaten başka bir kasaya kayıtlı.<li>Yapılandırma sırasında parola bozulmuş.| Sunucunun kasasından kaydını kaldırın ve yeni bir parola ile yeniden kaydedin.

## <a name="the-activation-did-not-complete-successfully"></a>Etkinleştirme başarıyla tamamlanmadı

| Hata  | Olası nedenler | Önerilen Eylemler |
|---------|---------|---------|
|<br />Etkinleştirme başarıyla tamamlanmadı. Geçerli işlem, [0x1FC07] iç hizmet hatası nedeniyle başarısız oldu. Bir süre sonra işlemi yeniden deneyin. Sorun devam ederse, lütfen Microsoft Desteği'ne başvurun.     | <li> Karalama klasörü, yeterli alana sahip olmayan bir birimde bulunur. <li> Karalama klasörü yanlış bir şekilde taşındı. <li> OnlineBackup. KEK dosyası eksik.         | <li>MARS aracısının [en son sürümüne](https://aka.ms/azurebackup_agent) yükseltin.<li>Karalama klasörünü veya önbellek konumunu, yedekleme verilerinin toplam boyutunun %5 ' i ile %10 ' a kadar boş alana sahip bir birime taşıyın. Önbellek konumunu doğru bir şekilde taşımak için, [dosya ve klasörleri yedekleme hakkında sık sorulan soruların](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)adımlarına bakın.<li> OnlineBackup. KEK dosyasının mevcut olduğundan emin olun. <br>*Karalama klasörü veya önbellek yolu için varsayılan konum C:\Program Files\Microsoft Azure Kurtarma Hizmetleri hizmeti \ çalışma klasörüdür*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Şifreleme parolası düzgün yapılandırılmadı

| Hata  | Olası nedenler | Önerilen Eylemler |
|---------|---------|---------|
| <br />Hata 34506. Bu bilgisayarda depolanan şifreleme parolası düzgün yapılandırılmamış.    | <li> Karalama klasörü, yeterli alana sahip olmayan bir birimde bulunur. <li> Karalama klasörü yanlış bir şekilde taşındı. <li> OnlineBackup. KEK dosyası eksik.        | <li>MARS aracısının [en son sürümüne](https://aka.ms/azurebackup_agent) yükseltin.<li>Karalama klasörünü veya önbellek konumunu, yedekleme verilerinin toplam boyutunun %5 ' i ile %10 ' a kadar boş alana sahip bir birime taşıyın. Önbellek konumunu doğru bir şekilde taşımak için, [dosya ve klasörleri yedekleme hakkında sık sorulan soruların](/backup-azure-file-folder-backup-faq.yml#manage-the-backup-cache-folder)adımlarına bakın.<li> OnlineBackup. KEK dosyasının mevcut olduğundan emin olun. <br>*Karalama klasörü veya önbellek yolu için varsayılan konum C:\Program Files\Microsoft Azure Kurtarma Hizmetleri hizmeti \ çalışma klasörüdür*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Yedeklemeler zamanlamaya göre Çalıştırılmadı

Zamanlanmış yedeklemeler otomatik olarak tetiklenmez ancak el ile yedeklemeler doğru şekilde çalışmamışsa, aşağıdaki eylemleri deneyin:

- Windows Server yedekleme zamanlamasının Azure dosyaları ve klasörleri yedekleme zamanlaması ile çakışmadığından emin olun.

- Çevrimiçi yedekleme durumunun **Etkinleştir** olarak ayarlandığından emin olun. Durumu doğrulamak için şu adımları uygulayın:

  1. Görev Zamanlayıcı, **Microsoft** ' u genişletin ve **çevrimiçi yedekleme**' yi seçin.
  1. **Microsoft-OnlineBackup** ' a çift tıklayın ve **Tetikleyiciler** sekmesine gidin.
  1. Durumun **etkin** olarak ayarlandığından emin olun. Değilse, **Düzenle**' yi seçin, **etkin**' i seçin ve ardından **Tamam**' ı seçin.

- Görevi çalıştırmak için seçilen kullanıcı hesabının, sunucuda **sistem** veya **yerel Yöneticiler ' grubu** olduğundan emin olun. Kullanıcı hesabını doğrulamak için **genel** sekmesine gidin ve **güvenlik** seçeneklerini kontrol edin.

- PowerShell 3,0 veya sonraki bir sürümünün sunucuda yüklü olduğundan emin olun. PowerShell sürümünü denetlemek için, bu komutu çalıştırın ve `Major` sürüm numarasının 3 veya üzeri olduğunu doğrulayın:

  `$PSVersionTable.PSVersion`

- Bu yolun ortam değişkeninin bir parçası olduğundan emin olun `PSMODULEPATH` :

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- İçin PowerShell yürütme ilkesi `LocalMachine` olarak ayarlandıysa `restricted` , yedekleme görevini tetikleyen PowerShell cmdlet 'i başarısız olabilir. Yürütme ilkesini denetlemek ve ayarlamak için bu komutları yükseltilmiş modda çalıştırın `Unrestricted` `RemoteSigned` :

 ```PowerShell
 Get-ExecutionPolicy -List

Set-ExecutionPolicy Unrestricted
 ```

- Eksik veya bozuk PowerShell modülünün MSOnlineBackup dosyalarını bulunmadığından emin olun. Eksik veya bozuk dosyalar varsa, şu adımları uygulayın:

  1. Doğru şekilde çalışan bir MARS aracısına sahip olan herhangi bir makineden MSOnlineBackup klasörünü C:\Program Files\Microsoft Azure Recovery Services 'dan kopyalayın Agent\bin\Modules.
  1. Sorunlu makinede, kopyalanmış dosyaları aynı klasör konumuna (C:\Program Files\Microsoft Azure Recovery Services \Bin\modules) yapıştırın.

     Makinede zaten bir MSOnlineBackup klasörü varsa, dosyaları bu klasöre yapıştırın veya var olan dosyaları değiştirin.

> [!TIP]
> Değişikliklerin tutarlı bir şekilde uygulandığından emin olmak için, önceki adımları gerçekleştirdikten sonra sunucuyu yeniden başlatın.

## <a name="resource-not-provisioned-in-service-stamp"></a>Kaynak hizmet damgasında sağlanmadı

Hata | Olası nedenler | Önerilen Eylemler
--- | --- | ---
Geçerli işlem, bir iç hizmet hatası nedeniyle başarısız oldu "kaynak hizmet damgasında sağlanmadı". Lütfen bir süre sonra işlemi yeniden deneyin. (KIMLIK: 230006) | Korunan sunucu yeniden adlandırıldı. | <li> Sunucuyu kasayla birlikte kaydedilen özgün adla yeniden adlandırın. <br> <li> Sunucuyu yeni adla kasaya yeniden kaydedin.

## <a name="job-could-not-be-started-as-another-job-was-in-progress"></a>Başka bir iş sürdüğünden iş başlatılamadı

**Mars konsolu**  >  **iş geçmişinde** bir uyarı iletisi görürseniz, "iş devam eden başka bir iş olarak başlatılamadı" diyerek, bu durum Görev Zamanlayıcı tarafından tetiklenen bir işin yinelenen örneği olabilir.

![Başka bir iş sürdüğünden iş başlatılamadı](./media/backup-azure-mars-troubleshoot/job-could-not-be-started.png)

Bu sorunu çözmek için:

1. Çalıştır penceresinde *taskschd. msc* yazarak Görev Zamanlayıcı ek bileşenini başlatın
1. Sol bölmede **Görev Zamanlayıcı Kitaplığı**  ->  **Microsoft**  ->  **onlinebackup**' a gidin.
1. Bu kitaplıktaki her görev için, özellikleri açmak üzere göreve çift tıklayın ve aşağıdaki adımları gerçekleştirin:
    1. **Ayarlar** sekmesine geçin.

         ![Ayarlar sekmesi](./media/backup-azure-mars-troubleshoot/settings-tab.png)

    1. **Görev zaten çalışıyorsa, aşağıdaki kural geçerli** olur. **Yeni bir örnek başlatma**' yı seçin.

         ![Yeni örnek başlatmayın kuralını değiştirme](./media/backup-azure-mars-troubleshoot/change-rule.png)

## <a name="troubleshoot-restore-problems"></a>Geri yükleme sorunlarını giderme

Azure Backup, birkaç dakika sonra bile kurtarma birimini başarıyla bağlayamayabilir. Ve işlem sırasında hata iletileri alabilirsiniz. Normal olarak kurtarmaya başlamak için şu adımları uygulayın:

1. Birkaç dakika boyunca çalışıyorsa bağlama işlemini iptal edin.

2. Yedekleme aracısının en son sürümüne sahip olup olmadığınızı denetleyin. Sürümü denetlemek için MARS konsolunun **Eylemler** bölmesinde **Microsoft Azure kurtarma hizmetleri Aracısı**' nı seçin. **Sürüm** numarasının [Bu makalede](https://go.microsoft.com/fwlink/?linkid=229525)bahsedilen sürümden veya daha yüksek olduğunu doğrulayın. [En son sürümü indirmek](https://go.microsoft.com/fwLink/?LinkID=288905)için bu bağlantıyı seçin.

3. **Device Manager**  >  **depolama denetleyicileri** ' ne gidin ve **Microsoft iSCSI başlatıcısı**' nı bulun. Bunu buldıysanız, doğrudan 7. adıma gidin.

4. Microsoft iSCSI başlatıcısı hizmetini bulamıyorsanız, **Device Manager**  >  **depolama denetleyicileri** altında, donanım kimliği **root\ıscsı** olan **bilinmeyen cihaz** adlı bir giriş bulmayı deneyin.

5. **Bilinmeyen cihaza** sağ tıklayıp **sürücü yazılımını Güncelleştir**' i seçin.

6. **Güncelleştirilmiş sürücü yazılımını otomatik olarak ara** seçeneğini belirleyerek sürücüyü güncelleştirin. Bu güncelleştirme **Bilinmeyen cihazı** **Microsoft iSCSI başlatıcısı**'na değiştirmeli:

    ![Depolama denetleyicileri vurgulanmış şekilde Azure Backup Device Manager ekran görüntüsü](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. **Görev Yöneticisi**  >  **Hizmetleri (yerel)**  >  **Microsoft iSCSI Başlatıcısı hizmeti**'ne gidin:

    ![Hizmetler (yerel) vurgulanmış Azure Backup Görev Yöneticisi ekran görüntüsü](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Microsoft Iscsı Başlatıcısı hizmetini yeniden başlatın. Bunu yapmak için hizmete sağ tıklayın ve **Durdur**' u seçin. Sonra tekrar sağ tıklayıp **Başlat**' ı seçin.

9. Kurtarmayı [anında geri yükleme](backup-instant-restore-capability.md)kullanarak yeniden deneyin.

Kurtarma hala başarısız olursa, sunucunuzu veya istemcinizi yeniden başlatın. Yeniden başlatmak istemiyorsanız veya sunucu yeniden başlatıldıktan sonra bile kurtarma hala başarısız olursa, [başka bir makineden kurtarmayı](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)deneyin.

## <a name="troubleshoot-cache-problems"></a>Önbellek sorunlarını giderme

Önbellek klasörü (karalama klasörü olarak da bilinir) yanlış yapılandırılmışsa, eksik Önkoşullar veya kısıtlı erişim varsa yedekleme işlemi başarısız olabilir.

### <a name="prerequisites"></a>Önkoşullar

MARS Aracısı işlemlerinin başarılı olabilmesi için önbellek klasörünün aşağıdaki gereksinimlere uyması gerekir:

- [Karalama klasörü konumunda %5 ila %10 boş birim alanı kullanılabildiğinden emin olun](backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Karalama klasörü konumunun geçerli ve erişilebilir olduğundan emin olun](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)
- [Önbellek klasöründeki dosya özniteliklerinin desteklendiğinden emin olun](backup-azure-file-folder-backup-faq.yml#are-there-any-attributes-of-the-cache-folder-that-aren-t-supported-)
- [Ayrılan gölge kopya depolama alanının yedekleme işlemi için yeterli olduğundan emin olun](#increase-shadow-copy-storage)
- [Önbellek klasörüne erişimi kısıtlayan başka işlem olmadığından emin olun (örn. virüsten koruma yazılımı)](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Gölge kopya depolama alanını artırma

Veri kaynağını korumak için gereken yeterli gölge kopya depolama alanı yoksa yedekleme işlemleri başarısız olabilir. Bu sorunu çözmek için, aşağıda **gösterildiği gibi,** aşağıdaki gibi, Korumalı birimdeki gölge kopya depolama alanını yükseltin:

- Yükseltilmiş komut isteminden geçerli gölge depolama alanını kontrol edin:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aşağıdaki komutu kullanarak gölge depolama alanını arttırın:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Önbellek klasörüne erişimi engelleyen başka bir işlem veya virüsten koruma yazılımı

Sunucusunda virüsten koruma yazılımı yüklüyse, bu dosya ve klasörler için virüsten koruma taramasına gereken dışlama kurallarını ekleyin:  

- Karalama klasörü. Varsayılan konumu `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
- Konumundaki bin klasörü `C:\Program Files\Microsoft Azure Recovery Services Agent\Bin`
- CBengine.exe
- CSC.exe

## <a name="common-issues"></a>Genel sorunlar

Bu bölümde, MARS Aracısı kullanılırken karşılaştığınız yaygın hatalar ele alınmaktadır.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Hata iletisi | Önerilen eylem
--|--
Microsoft Azure Kurtarma Hizmetleri Aracısı, karalama konumunda depolanmış yedekleme sağlama toplamına erişemedi | Bu sorunu çözmek için, aşağıdaki adımları uygulayın ve sunucuyu yeniden başlatın <br/> - [Karalama konumu dosyalarını kilitleyen bir virüsten koruma veya başka bir işlem olup olmadığını denetleyin](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Karalama konumunun geçerli ve MARS Aracısı tarafından erişilebilir olup olmadığını denetleyin.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Hata iletisi | Önerilen eylem
--|--
Microsoft Azure Kurtarma Hizmetleri Aracısı, VHD’yi başlatmak için karalama konumuna erişemedi | Bu sorunu çözmek için, aşağıdaki adımları uygulayın ve sunucuyu yeniden başlatın <br/> - [Virüsten koruma veya diğer işlemlerin karalama konumu dosyalarını kilitlemesinin olup olmadığını denetleyin](#another-process-or-antivirus-software-blocking-access-to-cache-folder)<br/> - [Karalama konumunun geçerli ve MARS Aracısı tarafından erişilebilir olup olmadığını denetleyin.](backup-azure-file-folder-backup-faq.yml#how-to-check-if-scratch-folder-is-valid-and-accessible-)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Hata iletisi | Önerilen eylem
--|--
Karalama klasörünün bulunduğu birimde yetersiz depolama nedeniyle yedekleme başarısız oldu | Bu sorunu çözmek için aşağıdaki adımları doğrulayıp işlemi yeniden deneyin:<br/>- [MARS aracısının en son sürümü olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> - [Yedekleme karalama alanını etkileyen depolama sorunlarını doğrulama ve çözme](#prerequisites)

### <a name="salbitmaperror"></a>SalBitmapError

Hata iletisi | Önerilen eylem
--|--
Dosyadaki değişiklikler bulunamadı. Bunun çeşitli nedenleri olabilir. Lütfen işlemi yeniden deneyin | Bu sorunu çözmek için aşağıdaki adımları doğrulayıp işlemi yeniden deneyin:<br/> - [MARS aracısının en son sürümü olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> - [Yedekleme karalama alanını etkileyen depolama sorunlarını doğrulama ve çözme](#prerequisites)

## <a name="next-steps"></a>Sonraki adımlar

- [Windows Server 'ı Azure Backup aracısıyla nasıl yedekleyeceğiniz](tutorial-backup-windows-server-to-azure.md)hakkında daha fazla bilgi alın.
- Bir yedeklemeyi geri yüklemeniz gerekiyorsa bkz. [dosyaları bir Windows makinesine geri yükleme](backup-azure-restore-windows-server.md).
