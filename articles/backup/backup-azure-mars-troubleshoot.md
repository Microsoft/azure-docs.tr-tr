---
title: Azure Backup aracısında sorun giderme
description: Bu makalede Azure Backup aracısının yüklenmesi ve kaydettirilmesinde nasıl sorun giderileceği hakkında bilgi edinebilirsiniz.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: 4f1d93a6ba36821832c945db2e5cc19bf7e9d232
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172944"
---
# <a name="troubleshoot-the-microsoft-azure-recovery-services-mars-agent"></a>Microsoft Azure Kurtarma Hizmetleri (MARS) Aracısı sorunlarını giderme

Bu makalede yapılandırma, kayıt, yedekleme ve geri yükleme sırasında görebileceğiniz hataların nasıl çözümleneceği açıklanır.

## <a name="basic-troubleshooting"></a>Temel sorun giderme

Microsoft Azure kurtarma hizmetleri (MARS) Aracısı ile ilgili sorun gidermeye başlamadan önce aşağıdakileri kontrol etmenizi öneririz:

- [Mars aracısının güncel olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409).
- [Mars Aracısı ve Azure arasında ağ bağlantısına sahip olduğunuzdan emin olun](https://aka.ms/AB-A4dp50).
- MARS 'ın çalıştığından emin olun (hizmet konsolunda). Gerekirse, yeniden başlatın ve işlemi yeniden deneyin.
- [Karalama klasörü konumunda %5 ila %10 boş birim alanı kullanılabilir olduğundan emin olun](https://aka.ms/AB-AA4dwtt).
- [Azure Backup başka bir işlem veya virüsten koruma yazılımı](https://aka.ms/AB-AA4dwtk)olup olmadığını denetleyin.
- Zamanlanan yedekleme başarısız olursa, ancak el ile yedekleme çalışırsa, bkz. [yedeklemeler zamanlamaya göre Çalıştırılmadı](https://aka.ms/ScheduledBackupFailManualWorks).
- İşletim sisteminin en son güncelleştirmelere sahip olduğundan emin olun.
- Desteklenmeyen [özniteliklerin ve desteklenmeyen özniteliklere sahip dosyaların yedeklemeden dışlandığından emin olun](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup).
- Korunan sistemdeki saatin doğru saat dilimine göre yapılandırıldığından emin olun.
- [Sunucuda .NET Framework 4.5.2 veya üzeri sürümünün yüklü olduğundan emin olun](https://www.microsoft.com/download/details.aspx?id=30653).
- Sunucunuzu bir kasaya yeniden kaydetmeye çalışıyorsanız:
  - Aracının sunucuda kaldırıldığından ve portaldan silindiğinden emin olun.
  - Başlangıçta sunucuyu kaydetmek için kullanılan parolayı kullanın.
- Çevrimdışı yedeklemeler için, yedeklemeye başlamadan önce Azure PowerShell 3.7.0 hem kaynak hem de kopya bilgisayarda yüklü olduğundan emin olun.
- Yedekleme aracısı bir Azure sanal makinesinde çalışıyorsa, [Bu makaleye](https://aka.ms/AB-AA4dwtr)bakın.

## <a name="invalid-vault-credentials-provided"></a>Belirtilen kasa kimlik bilgileri geçersiz

**Hata iletisi**: geçersiz kasa kimlik bilgileri belirtildi. Dosya bozuk veya kurtarma hizmeti ile ilişkili en son kimlik bilgilerine sahip değil. (KIMLIK: 34513)

| Nedeni | Önerilen Eylemler |
| ---     | ---    |
| **Kasa kimlik bilgileri geçerli değil** <br/> <br/> Kasa kimlik bilgileri dosyaları bozulmuş olabilir veya zaman aşımına uğradı. (Örneğin, kayıt zamanından önce 48 saatten daha fazla indirilmiş olabilir.)| Azure portal kurtarma hizmetleri kasasından yeni kimlik bilgilerini indirin. ( [Mars aracısını indirme](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) bölümünde 6. adıma bakın.) Ardından aşağıdaki adımları uygulayın: <ul><li> MARS 'ı zaten yüklediyseniz ve daha önce kaydolduysanız, yeni kimlik bilgileriyle kaydı gerçekleştirmek için Microsoft Azure Backup Aracısı MMC konsolunu açın ve **Eylemler** bölmesinde **sunucuyu kaydet** ' i seçin. <br/> <li> Yeni yükleme başarısız olursa yeni kimlik bilgileriyle yeniden yüklemeyi deneyin.</ul> **Note**: birden fazla kasa kimlik bilgileri dosyası indirildiyse, sonraki 48 saat için yalnızca en son dosya geçerlidir. Yeni bir kasa kimlik bilgileri dosyası indirmenizi öneririz.
| **Proxy sunucusu/güvenlik duvarı kaydı engelliyor** <br/>veya <br/>**İnternet bağlantısı yok** <br/><br/> Makinenizin veya proxy sunucunuzun sınırlı internet bağlantısı varsa ve gerekli URL 'Ler için erişim sağlanmıyorsanız, kayıt başarısız olur.| Aşağıdaki adımları gerçekleştirin:<br/> <ul><li> Sistemin internet bağlantısı olduğundan emin olmak için BT ekibinizle birlikte çalışın.<li> Proxy sunucunuz yoksa, aracıyı kaydettiğinizde ara sunucu seçeneğinin seçili olmadığından emin olun. [Proxy ayarlarınızı denetleyin](#verifying-proxy-settings-for-windows).<li> Bir güvenlik duvarı/proxy sunucunuz varsa, bu URL 'Lerin ve IP adreslerinin erişime sahip olduğundan emin olmak için ağ ekibinizle birlikte çalışın:<br/> <br> **Adresleri**<br> `www.msftncsi.com` <br> .Microsoft.com <br> .WindowsAzure.com <br> .microsoftonline.com <br> .windows.net <br>**IP adresleri**<br>  20.190.128.0/18 <br>  40.126.0.0/18 <br/></ul></ul>Yukarıdaki sorun giderme adımlarını tamamladıktan sonra kaydetmeyi yeniden deneyin.
| **Virüsten koruma yazılımı kaydı engelliyor** | Sunucusunda virüsten koruma yazılımı yüklüyse, bu dosya ve klasörler için virüsten koruma taramasına gereken dışlama kurallarını ekleyin: <br/><ul> <li> CBengine. exe <li> CSC. exe<li> Karalama klasörü. Varsayılan konum C:\Program Files\Microsoft Azure Kurtarma Hizmetleri ' dir. <li> C:\Program Files\Microsoft Azure kurtarma hizmetleri \ bin klasöründe bin klasörü.

### <a name="additional-recommendations"></a>Ek öneriler

- C:/Windows/Temp adresine gidin ve. tmp uzantısıyla 60.000 ' den fazla veya 65.000 dosya olup olmadığını denetleyin. Varsa, bu dosyaları silin.
- Makinenin tarih ve saatinin yerel saat dilimiyle eşleştiğinden emin olun.
- Internet Explorer 'da [Bu sitelerin](backup-configure-vault.md#verify-internet-access) güvenilen sitelerinize eklendiğinden emin olun.

### <a name="verifying-proxy-settings-for-windows"></a>Windows için proxy ayarları doğrulanıyor

1. [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) sayfasından PsExec 'yi indirin.
1. Yükseltilmiş bir komut isteminden `psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"` çalıştırın.

   Bu komut, Internet Explorer 'ı açar.
1. **Araçlar** > **Internet seçenekleri** > **bağlantı** > **LAN ayarları**' na gidin.
1. Sistem hesabının ara sunucu ayarlarını kontrol edin.
1. Yapılandırılmış bir proxy yoksa ve proxy ayrıntıları sağlanmışsa, ayrıntıları kaldırın.
1. Bir ara sunucu yapılandırıldıysa ve proxy ayrıntıları yanlışsa, **Proxy IP** ve **bağlantı noktası** ayrıntılarının doğru olduğundan emin olun.
1. Internet Explorer 'ı kapatın.

## <a name="unable-to-download-vault-credential-file"></a>Kasa kimlik bilgileri dosyası karşıdan yüklenemiyor

| Hata   | Önerilen Eylemler |
| ---     | ---    |
|Kasa kimlik bilgileri dosyası indirilemedi. (KIMLIK: 403) | <ul><li> Farklı bir tarayıcı kullanarak kasa kimlik bilgilerini indirmeyi deneyin veya şu adımları uygulayın: <ul><li> Internet Explorer 'ı başlatın. F12 ' i seçin. </li><li> **Ağ** sekmesine gidin ve önbelleği ve tanımlama bilgilerini temizleyin. </li> <li> Sayfayı yenileyin.<br></li></ul> <li> Aboneliğin devre dışı bırakılıp bırakılmadığını kontrol edin.<br></li> <li> Bir güvenlik duvarı kuralının indirmeyi engelleyip engellemediğini denetleyin. <br></li> <li> Kasadaki limiti tüketmemiş olduğunuzdan emin olun (kasa başına 50 makine).<br></li>  <li> Kullanıcının kasa kimlik bilgilerini indirmek ve kasaya bir sunucu kaydetmek için gereken Azure Backup izinlere sahip olduğundan emin olun. Bkz. [Azure Backup kurtarma noktalarını yönetmek Için rol tabanlı Access Control kullanma](backup-rbac-rs-vault.md).</li></ul> |

## <a name="the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup"></a>Microsoft Azure Kurtarma Hizmeti Aracısı, Microsoft Azure Backup'a bağlanamadı

| Hata  | Olası neden | Önerilen Eylemler |
| ---     | ---     | ---    |
| <br /><ul><li>Microsoft Azure kurtarma hizmeti Aracısı Microsoft Azure Backup bağlantı kuramadı. (KIMLIK: 100050) Ağ ayarlarınızı denetleyin ve internet 'e bağlanabildiğinizden emin olun.<li>(407) proxy kimlik doğrulaması gerekiyor. |Ara sunucu bağlantıyı engelliyor. |  <ul><li>Internet Explorer 'da **araçlar** > **ınternet seçenekleri** ** >  > ** **İnternet**' e gidin. **Özel düzey** ' i seçin ve **dosya indirme** bölümüne gidin. **Etkinleştir**’i seçin.<p>Ayrıca, Internet Explorer 'daki güvenilen sitelerinize [URL 'ler ve IP adresleri](backup-configure-vault.md#verify-internet-access) eklemeniz gerekebilir.<li>Ayarları bir proxy sunucu kullanacak şekilde değiştirin. Ardından proxy sunucusu ayrıntılarını sağlayın.<li> Makinenizin internet erişimi sınırlı ise, makinedeki veya proxy 'deki güvenlik duvarı ayarlarının bu [URL 'lere ve IP adreslerine](backup-configure-vault.md#verify-internet-access)izin verildiğinden emin olun. <li>Sunucuda yüklü bir virüsten koruma yazılımınız varsa, bu dosyaları virüsten koruma taramasından çıkarın: <ul><li>CBEngine. exe (DPMRA. exe yerine).<li>CSC. exe (.NET Framework ile ilgili). Sunucuda yüklü her .NET Framework sürümü için bir CSC. exe vardır. Etkilenen sunucuda .NET Framework tüm sürümleri için CSC. exe dosyalarını hariç tutun. <li>Karalama klasörü veya önbellek konumu. <br>Karalama klasörü veya önbellek yolu için varsayılan konum C:\Program Files\Microsoft Azure Recovery Services.<li>C:\Program Files\Microsoft Azure kurtarma hizmetleri \ bin klasöründe bin klasörü.

## <a name="failed-to-set-the-encryption-key-for-secure-backups"></a>Güvenli yedeklemeler için şifreleme anahtarı ayarlama başarısız oldu

| Hata | Olası nedenler | Önerilen Eylemler |
| ---     | ---     | ---    |
| <br />Güvenli yedeklemeler için şifreleme anahtarı ayarlanamadı. Etkinleştirme tamamen başarılı olmadı, ancak şifreleme parolası aşağıdaki dosyaya kaydedildi. |<li>Sunucu zaten başka bir kasaya kayıtlı.<li>Yapılandırma sırasında parola bozulmuş.| Sunucunun kasasından kaydını kaldırın ve yeni bir parola ile yeniden kaydedin.

## <a name="the-activation-did-not-complete-successfully"></a>Etkinleştirme başarıyla tamamlanmadı

| Hata  | Olası nedenler | Önerilen Eylemler |
|---------|---------|---------|
|<br />Etkinleştirme başarıyla tamamlanmadı. Geçerli işlem, [0x1FC07] iç hizmet hatası nedeniyle başarısız oldu. Bir süre sonra işlemi yeniden deneyin. Sorun devam ederse, lütfen Microsoft desteğine başvurun.     | <li> Karalama klasörü, yeterli alana sahip olmayan bir birimde bulunur. <li> Karalama klasörü yanlış bir şekilde taşındı. <li> OnlineBackup. KEK dosyası eksik.         | <li>MARS aracısının [en son sürümüne](https://aka.ms/azurebackup_agent) yükseltin.<li>Karalama klasörünü veya önbellek konumunu, yedekleme verilerinin toplam boyutunun %5 ' i ile %10 ' a kadar boş alana sahip bir birime taşıyın. Önbellek konumunu doğru bir şekilde taşımak için, [dosya ve klasörleri yedekleme hakkında sık sorulan soruların](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)adımlarına bakın.<li> OnlineBackup. KEK dosyasının mevcut olduğundan emin olun. <br>*Karalama klasörü veya önbellek yolu için varsayılan konum C:\Program Files\Microsoft Azure Kurtarma Hizmetleri hizmeti \ çalışma klasörüdür*.        |

## <a name="encryption-passphrase-not-correctly-configured"></a>Şifreleme parolası doğru yapılandırılmadı

| Hata  | Olası nedenler | Önerilen Eylemler |
|---------|---------|---------|
| <br />Hata 34506. Bu bilgisayarda depolanan şifreleme parolası düzgün yapılandırılmamış.    | <li> Karalama klasörü, yeterli alana sahip olmayan bir birimde bulunur. <li> Karalama klasörü yanlış bir şekilde taşındı. <li> OnlineBackup. KEK dosyası eksik.        | <li>MARS aracısının [en son sürümüne](https://aka.ms/azurebackup_agent) yükseltin.<li>Karalama klasörünü veya önbellek konumunu, yedekleme verilerinin toplam boyutunun %5 ' i ile %10 ' a kadar boş alana sahip bir birime taşıyın. Önbellek konumunu doğru bir şekilde taşımak için, [dosya ve klasörleri yedekleme hakkında sık sorulan soruların](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq#manage-the-backup-cache-folder)adımlarına bakın.<li> OnlineBackup. KEK dosyasının mevcut olduğundan emin olun. <br>*Karalama klasörü veya önbellek yolu için varsayılan konum C:\Program Files\Microsoft Azure Kurtarma Hizmetleri hizmeti \ çalışma klasörüdür*.         |

## <a name="backups-dont-run-according-to-schedule"></a>Yedeklemeler zamanlamaya göre Çalıştırılmadı

Zamanlanmış yedeklemeler otomatik olarak tetiklenmez ancak el ile yedeklemeler doğru şekilde çalışmamışsa, aşağıdaki eylemleri deneyin:

- Windows Server yedekleme zamanlamasının Azure dosyaları ve klasörleri yedekleme zamanlaması ile çakışmadığından emin olun.

- Çevrimiçi yedekleme durumunun **Etkinleştir**olarak ayarlandığından emin olun. Durumu doğrulamak için şu adımları uygulayın:

  1. Görev Zamanlayıcı, **Microsoft** ' u genişletin ve **çevrimiçi yedekleme**' yi seçin.
  1. **Microsoft-OnlineBackup** ' a çift tıklayın ve **Tetikleyiciler** sekmesine gidin.
  1. Durumun **etkin**olarak ayarlandığından emin olun. Değilse, **Düzenle**' yi seçin, **etkin**' i seçin ve ardından **Tamam**' ı seçin.

- Görevi çalıştırmak için seçilen kullanıcı hesabının, sunucuda **sistem** veya **yerel Yöneticiler ' grubu** olduğundan emin olun. Kullanıcı hesabını doğrulamak için **genel** sekmesine gidin ve **güvenlik** seçeneklerini kontrol edin.

- PowerShell 3,0 veya sonraki bir sürümünün sunucuda yüklü olduğundan emin olun. PowerShell sürümünü denetlemek için, bu komutu çalıştırın ve `Major` sürüm numarasının 3 veya üzeri olduğunu doğrulayın:

  `$PSVersionTable.PSVersion`

- Bu yolun `PSMODULEPATH` ortam değişkeninin bir parçası olduğundan emin olun:

  `<MARS agent installation path>\Microsoft Azure Recovery Services Agent\bin\Modules\MSOnlineBackup`

- `LocalMachine` için PowerShell yürütme ilkesi `restricted`olarak ayarlanırsa, yedekleme görevini tetikleyen PowerShell cmdlet 'i başarısız olabilir. Yürütme ilkesini `Unrestricted` ya da `RemoteSigned`olarak denetlemek ve ayarlamak için bu komutları yükseltilmiş modda çalıştırın:

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

## <a name="troubleshoot-restore-problems"></a>Geri yükleme sorunlarını giderme

Azure Backup, birkaç dakika sonra bile kurtarma birimini başarıyla bağlayamayabilir. Ve işlem sırasında hata iletileri alabilirsiniz. Normal olarak kurtarmaya başlamak için şu adımları uygulayın:

1. Birkaç dakika boyunca çalışıyorsa bağlama işlemini iptal edin.

2. Yedekleme aracısının en son sürümüne sahip olup olmadığınızı denetleyin. Sürümü denetlemek için MARS konsolunun **Eylemler** bölmesinde **Microsoft Azure kurtarma hizmetleri Aracısı**' nı seçin. **Sürüm** numarasının [Bu makalede](https://go.microsoft.com/fwlink/?linkid=229525)bahsedilen sürümden veya daha yüksek olduğunu doğrulayın. [En son sürümü indirmek](https://go.microsoft.com/fwLink/?LinkID=288905)için bu bağlantıyı seçin.

3. **Aygıt Yöneticisi** > **depolama denetleyicilerine** gidin ve **Microsoft iSCSI başlatıcısı**'nı bulun. Bunu buldıysanız, doğrudan 7. adıma gidin.

4. Microsoft Iscsı Başlatıcısı hizmetini bulamıyorsanız, **Aygıt Yöneticisi** > **depolama denetleyicilerinin** altında, donanım kimliği **Root\ıbir** **cihaz olan bilinmeyen cihaz** adlı bir giriş bulmayı deneyin.

5. **Bilinmeyen cihaza** sağ tıklayıp **sürücü yazılımını Güncelleştir**' i seçin.

6. **Güncelleştirilmiş sürücü yazılımını otomatik olarak ara**seçeneğini belirleyerek sürücüyü güncelleştirin. Bu güncelleştirme **Bilinmeyen cihazı** **Microsoft iSCSI başlatıcısı**'na değiştirmeli:

    ![Depolama denetleyicileri vurgulanmış şekilde Azure Backup Aygıt Yöneticisi ekran görüntüsü](./media/backup-azure-restore-windows-server/UnknowniSCSIDevice.png)

7. **Görev yöneticisi** > **Hizmetler (yerel)**  > **Microsoft iSCSI Başlatıcısı hizmeti**' ne gidin:

    ![Hizmetler (yerel) vurgulanmış Azure Backup Görev Yöneticisi ekran görüntüsü](./media/backup-azure-restore-windows-server/MicrosoftInitiatorServiceRunning.png)

8. Microsoft Iscsı Başlatıcısı hizmetini yeniden başlatın. Bunu yapmak için hizmete sağ tıklayın ve **Durdur**' u seçin. Sonra tekrar sağ tıklayıp **Başlat**' ı seçin.

9. Kurtarmayı [anında geri yükleme](backup-instant-restore-capability.md)kullanarak yeniden deneyin.

Kurtarma hala başarısız olursa, sunucunuzu veya istemcinizi yeniden başlatın. Yeniden başlatmak istemiyorsanız veya sunucu yeniden başlatıldıktan sonra bile kurtarma hala başarısız olursa, [başka bir makineden kurtarmayı](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)deneyin.

## <a name="troubleshoot-cache-problems"></a>Önbellek sorunlarını giderme

Önbellek klasörü (karalama klasörü olarak da bilinir) yanlış yapılandırılmışsa, eksik Önkoşullar veya kısıtlı erişim varsa yedekleme işlemi başarısız olabilir.

### <a name="prerequisites"></a>Önkoşullar

MARS Aracısı işlemlerinin başarılı olabilmesi için önbellek klasörünün aşağıdaki gereksinimlere uyması gerekir:

- [Karalama klasörü konumunda %5 ila %10 boş birim alanı kullanılabildiğinden emin olun](backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- [Karalama klasörü konumunun geçerli ve erişilebilir olduğundan emin olun](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)
- [Önbellek klasöründeki dosya özniteliklerinin desteklendiğinden emin olun](backup-azure-file-folder-backup-faq.md#are-there-any-attributes-of-the-cache-folder-that-arent-supported)
- [Ayrılan gölge kopya depolama alanının yedekleme işlemi için yeterli olduğundan emin olun](#increase-shadow-copy-storage)
- [Önbellek klasörüne erişimi kısıtlayan başka işlem olmadığından emin olun (örn. virüsten koruma yazılımı)](#another-process-or-antivirus-software-blocking-access-to-cache-folder)

### <a name="increase-shadow-copy-storage"></a>Gölge kopya depolama alanını artırma

Veri kaynağını korumak için yeterli gölge kopya depolama alanı yoksa yedekleme işlemleri başarısız olabilir. Bu sorunu çözmek için, aşağıda gösterildiği gibi, aşağıdaki gibi, Korumalı birimdeki gölge kopya depolama alanını yükseltin:

- Yükseltilmiş komut isteminden geçerli gölge depolama alanını kontrol edin:<br/>
  `vssadmin List ShadowStorage /For=[Volume letter]:`
- Aşağıdaki komutu kullanarak gölge depolama alanını yükseltin:<br/>
  `vssadmin Resize ShadowStorage /On=[Volume letter]: /For=[Volume letter]: /Maxsize=[size]`

### <a name="another-process-or-antivirus-software-blocking-access-to-cache-folder"></a>Önbellek klasörüne erişimi engelleyen başka bir işlem veya virüsten koruma yazılımı

Sunucusunda virüsten koruma yazılımı yüklüyse, bu dosya ve klasörler için virüsten koruma taramasına gereken dışlama kurallarını ekleyin:  

- Karalama klasörü. Varsayılan konum C:\Program Files\Microsoft Azure Recovery Services Hizmet\boş
- C:\Program Files\Microsoft Azure Kurtarma Hizmetleri hizmeti \ bin konumundaki bin klasörü
- CBengine. exe
- CSC. exe

## <a name="common-issues"></a>Genel sorunlar

Bu bölümde, MARS Aracısı kullanılırken karşılaştığınız yaygın hatalar ele alınmaktadır.

### <a name="salchecksumstoreinitializationfailed"></a>SalChecksumStoreInitializationFailed

Hata iletisi | Önerilen eylem |
-- | --
Microsoft Azure Kurtarma Hizmetleri Aracısı, karalama konumunda depolanmış yedekleme sağlama toplamına erişemedi | Bu sorunu çözmek için, aşağıdaki işlemleri gerçekleştirin ve sunucuyu yeniden başlatın <br/> - [, karalama konumu dosyalarını kilitleyen bir virüsten koruma veya başka bir işlem](#another-process-or-antivirus-software-blocking-access-to-cache-folder) olup olmadığını denetleyin<br/> - , [karalama konumunun geçerli ve Mars Aracısı tarafından erişilebilir olup olmadığını kontrol edin.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="salvhdinitializationerror"></a>SalVhdInitializationError

Hata iletisi | Önerilen eylem |
-- | --
Microsoft Azure Kurtarma Hizmetleri Aracısı, VHD’yi başlatmak için karalama konumuna erişemedi | Bu sorunu çözmek için, aşağıdaki işlemleri gerçekleştirin ve sunucuyu yeniden başlatın <br/> - [, karalama konumu dosyalarını kilitleyen bir virüsten koruma veya başka bir işlem](#another-process-or-antivirus-software-blocking-access-to-cache-folder) olup olmadığını denetleyin<br/> - , [karalama konumunun geçerli ve Mars Aracısı tarafından erişilebilir olup olmadığını kontrol edin.](backup-azure-file-folder-backup-faq.md#how-to-check-if-scratch-folder-is-valid-and-accessible)

### <a name="sallowdiskspace"></a>SalLowDiskSpace

Hata iletisi | Önerilen eylem |
-- | --
Karalama klasörünün bulunduğu birimde yetersiz depolama nedeniyle yedekleme başarısız oldu | Bu sorunu çözmek için aşağıdaki adımları doğrulayın ve işlemi yeniden deneyin:<br/>- [Mars aracısının en son sürümü olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)<br/> [yedekleme karalama alanını etkileyen depolama sorunlarını doğrulamak ve çözümlemek](#prerequisites) - 

### <a name="salbitmaperror"></a>SalBitmapError

Hata iletisi | Önerilen eylem |
-- | --
Dosyadaki değişiklikler bulunamadı. Bunun çeşitli nedenleri olabilir. Lütfen işlemi yeniden deneyin | Bu sorunu çözmek için aşağıdaki adımları doğrulayın ve işlemi yeniden deneyin:<br/> - [Mars aracısının en son sürümü olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409) <br/> [yedekleme karalama alanını etkileyen depolama sorunlarını doğrulamak ve çözümlemek](#prerequisites) - 

## <a name="next-steps"></a>Sonraki adımlar

- [Windows Server 'ı Azure Backup aracısıyla nasıl yedekleyeceğiniz](tutorial-backup-windows-server-to-azure.md)hakkında daha fazla bilgi alın.
- Bir yedeklemeyi geri yüklemeniz gerekiyorsa bkz. [dosyaları bir Windows makinesine geri yükleme](backup-azure-restore-windows-server.md).
