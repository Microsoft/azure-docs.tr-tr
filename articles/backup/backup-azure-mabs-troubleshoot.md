---
title: Azure Backup Sunucusu sorunlarını giderme
description: Yükleme, Azure Backup Sunucusu kaydı ve uygulama iş yüklerinin yedeklenmesi ve geri yüklenmesi sorunlarını giderin.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: eed90cd6b684891efe1996e22bbdd7c3ead2a83f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172961"
---
# <a name="troubleshoot-azure-backup-server"></a>Azure Backup Sunucusu sorunlarını giderme

Azure Backup Sunucusu kullanırken karşılaştığınız hataların sorunlarını gidermek için aşağıdaki tablolardaki bilgileri kullanın.

## <a name="basic-troubleshooting"></a>Temel sorun giderme

Microsoft Azure Backup Server (MABS) sorun gidermeye başlamadan önce aşağıdaki doğrulamayı gerçekleştirmenizi öneririz:

- [Microsoft Azure Kurtarma Hizmetleri (MARS) aracısının güncel olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS aracısı ile Azure arasında ağ bağlantısı sağlandığından emin olun](https://aka.ms/AB-A4dp50)
- Microsoft Azure Kurtarma Hizmetleri'nin çalıştığından emin olun (Hizmet konsolunda). Gerekirse yeniden başlatın ve işlemi yeniden deneyin
- [Boş klasör konumunda %5-10 oranında kullanılabilir alan olduğundan emin olun](https://aka.ms/AB-AA4dwtt)
- Kayıt başarısız olursa, yüklemeye çalıştığınız sunucunun Azure Backup Sunucusu başka bir kasada zaten kayıtlı olmadığından emin olun
- Göndermeli yükleme başarısız olursa DPM aracısının zaten mevcut olup olmadığını kontrol edin. Varsa aracıyı kaldırıp tekrar yüklemeyi deneyin
- [Azure Backup ile çakışan başka bir işlem veya virüsten koruma yazılımı olmadığından emin olun](https://aka.ms/AA4nyr4)<br>
- MAB sunucusunda SQL Aracısı hizmetinin çalıştığından ve otomatik olarak ayarlandığından emin olun<br>

## <a name="invalid-vault-credentials-provided"></a>Belirtilen kasa kimlik bilgileri geçersiz

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Kasaya kaydolma | Sağlanan kasa kimlik bilgileri geçersiz. Dosya bozuk veya kurtarma hizmetiyle ilişkili en son kimlik bilgilerine sahip değil. | Önerilen eylem: <br> <ul><li> Kasadan en son kimlik bilgileri dosyasını indirin ve yeniden deneyin. <br>VEYA</li> <li> Önceki eylem işe yaramazsa, kimlik bilgilerini farklı bir yerel dizine indirmeyi veya yeni bir kasa oluşturmayı deneyin. <br>VEYA</li> <li> Tarih ve saat ayarlarını [Bu blogda](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/)anlatıldığı şekilde güncelleştirmeyi deneyin. <br>VEYA</li> <li> C:\Windows\Temp 'in 65000 'den fazla dosya olup olmadığını denetleyin. Eski dosyaları başka bir konuma taşıyın veya Temp klasöründeki öğeleri silin. <br>VEYA</li> <li> Sertifikaların durumunu denetleyin. <br> a. **Bilgisayar sertifikalarını Yönet** ' i açın (Denetim Masası 'nda). <br> b. **Kişisel** düğümünü ve onun alt düğüm **sertifikalarını**genişletin.<br> c.  Sertifikayı kaldırın **Windows Azure Araçları**. <br> d. Azure Backup istemcisinde kaydı yeniden deneyin. <br> VEYA </li> <li> Herhangi bir grup ilkesinin yerinde olup olmadığını kontrol edin. </li></ul> |

## <a name="replica-is-inconsistent"></a>Çoğaltma tutarsız

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | Çoğaltma tutarsız | Koruma Grubu Sihirbazı 'ndaki otomatik tutarlılık denetimi seçeneğinin açık olduğunu doğrulayın. Çoğaltma tutarsızlığına ve ilgili önerilere yönelik nedenler hakkında daha fazla bilgi için bkz. [çoğaltma tutarsız](https://technet.microsoft.com/library/cc161593.aspx).<br> <ol><li> Sistem durumu/BMR yedeklemesi durumunda, Windows Server Yedekleme korunan sunucuda yüklü olduğunu doğrulayın.</li><li> DPM/Microsoft Azure Backup sunucusundaki DPM depolama havuzunda yer alan sorunları denetleyin ve gerekli olduğu gibi depolama alanını ayırın.</li><li> Korunan sunucudaki Birim Gölge Kopyası Hizmeti durumunu denetleyin. Devre dışı durumdaysa, el ile başlayacak şekilde ayarlayın. Hizmeti sunucuda başlatın. Ardından DPM/Microsoft Azure Backup sunucu konsoluna geri dönün ve tutarlılık denetimi işiyle eşitlemeyi başlatın.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Çevrimiçi kurtarma noktası oluşturma başarısız oldu

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | Çevrimiçi kurtarma noktası oluşturma başarısız oldu | **Hata iletisi**: Windows Azure Backup Aracısı seçili birimin anlık görüntüsünü oluşturamadı. <br> **Geçici çözüm**: çoğaltma ve kurtarma noktası birimindeki alanı artırmayı deneyin.<br> <br> **Hata iletisi**: Windows Azure Backup Aracısı obengine hizmetine bağlanamıyor <br> **Geçici çözüm**: obengine 'in bilgisayardaki çalışan hizmetler listesinde var olduğunu doğrulayın. OBEngine hizmeti çalışmıyorsa, OBEngine hizmetini başlatmak için "net start OBEngine" komutunu kullanın. <br> <br> **Hata iletisi**: Bu sunucunun şifreleme parolası ayarlanmadı. Lütfen bir şifreleme parolası yapılandırın. <br> **Geçici çözüm**: bir şifreleme parolası yapılandırmayı deneyin. Başarısız olursa, aşağıdaki adımları uygulayın: <br> <ol><li>Karalama konumunun mevcut olduğunu doğrulayın. Bu, **HKEY_LOCAL_MACHINE \Software\Microsoft\Windows Azure Backup\Config**kayıt defterinde belirtilen konumdur ve bu ad, **scratchlocation** adında olmalıdır.</li><li> Karalama konumu varsa, eski parolayı kullanarak yeniden kaydetmeyi deneyin. *Şifreleme parolasını yapılandırdığınızda, güvenli bir konuma kaydedin.*</li><ol>|

## <a name="the-vault-credentials-provided-are-different-from-the-vault-the-server-is-registered"></a>Belirtilen kasa kimlik bilgileri, sunucunun kayıtlı olduğu kasadan farklı

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Geri Yükleme | **Hata kodu**: CBPServerRegisteredVaultDontMatchWithCurrent/kasa kimlik bilgileri hatası: 100110 <br/> <br/>**Hata iletisi**: belirtilen kasa kimlik bilgileri, sunucunun kayıtlı olduğu kasadan farklı | **Neden**: Bu sorun, dış DPM kurtarma seçeneğini kullanarak dosyaları özgün sunucudan alternatif sunucuya geri yüklemeye çalışırken ve Kurtarılmakta olan sunucu ve verilerin yedeklendiği orijinal sunucu aynı kurtarma hizmeti kasasıyla ilişkilendirilmediğinde oluşur.<br/> <br/>**Geçici çözüm** Bu sorunu çözmek için hem özgün hem de alternatif sunucunun aynı kasaya kayıtlı olduğundan emin olun.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>VMware VM için çevrimiçi kurtarma noktası oluşturma işleri başarısız oluyor

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | VMware VM için çevrimiçi kurtarma noktası oluşturma işleri başarısız oluyor. DPM, şifre izleme bilgilerini almaya çalışırken VMware 'den bir hatayla karşılaştı. HataKodu-FileFaultFault (KIMLIK 33621) |  <ol><li> Etkilenen VM 'Ler için VMware üzerinde CTK 'yi sıfırlayın.</li> <li>Bağımsız diskin VMware üzerinde yerinde olmadığından emin olun.</li> <li>Etkilenen VM 'Ler için korumayı durdurun ve **Yenile** düğmesini kullanarak yeniden koruyun. </li><li>Etkilenen VM 'Ler için bir CC çalıştırın.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Sunucuda DPM aracı Düzenleyicisi hizmetindeki bir iletişim hatası nedeniyle Aracı işlemi başarısız oldu

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Aracıları korumalı sunuculara iletme | Aracı işlemi, \<ServerName > DPM Aracı Düzenleyicisi hizmetindeki bir iletişim hatası nedeniyle başarısız oldu. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları gerçekleştirin**: <ul><li> Güvenilmeyen bir etki alanından bilgisayar iliştirirken, [Bu adımları](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx)izleyin. <br> VEYA </li><li> Güvenilen bir etki alanından bir bilgisayar iliştiriyorsanız, [Bu blogda](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/)özetlenen adımları kullanarak sorun giderin. <br>VEYA</li><li> Sorun giderme adımı olarak virüsten koruma 'yı devre dışı bırakmayı deneyin. Sorunu giderirse, virüsten koruma ayarlarını [Bu makalede](https://technet.microsoft.com/library/hh757911.aspx)önerildiği şekilde değiştirin.</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Kurulum, kayıt defteri meta verilerini güncelleştiremedi

| İşlem | Hata Ayrıntıları | Geçici çözüm |
|-----------|---------------|------------|
|Yükleme | Kurulum, kayıt defteri meta verilerini güncelleştiremedi. Bu güncelleştirme hatası, depolama tüketiminin aşırı kullanılmasına yol açabilir. Bu güncelleştirmeyi önlemek için ReFS kırpma kayıt defteri girişini güncelleştirin. | **System\currentcontrolset\control\filesystem\refsenableınlinetrım**kayıt defteri anahtarını ayarlayın. DWORD değerini 1 olarak ayarlayın. |
|Yükleme | Kurulum, kayıt defteri meta verilerini güncelleştiremedi. Bu güncelleştirme hatası, depolama tüketiminin aşırı kullanılmasına yol açabilir. Bunu önlemek için, birim Snapoptımleştirme kayıt defteri girişini güncelleştirin. | **Software\microsoft Data Protection Manager\configuration\volsnapoptimization\writeıds** kayıt defteri anahtarını boş bir dize değeri ile oluşturun. |

## <a name="registration-and-agent-related-issues"></a>Kayıt ve aracıyla ilgili sorunlar

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Aracıları korumalı sunuculara iletme | Sunucu için belirtilen kimlik bilgileri geçersiz. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları uygulayın**: <br> Koruma aracısını üretim sunucusuna [Bu makalede](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual)belirtilen şekilde el ile yüklemeyi deneyin.|
| Azure Backup Aracısı Azure Backup hizmetine bağlanamadı (KIMLIK: 100050) | Azure Backup Aracısı Azure Backup hizmetine bağlanamadı. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları uygulayın**: <br>1. yükseltilmiş bir komut isteminde şu komutu çalıştırın: **PsExec-i-s "C:\Program Files\Explorer\iexplore.exe**. Bu, Internet Explorer penceresini açar. <br/> 2. **araçlar** > **Internet seçenekleri** > **bağlantı** > **LAN ayarları**' na gidin. <br/> 3. bir proxy sunucu kullanacak şekilde ayarları değiştirin. Ardından proxy sunucusu ayrıntılarını sağlayın.<br/> 4. makinenizin internet erişimi sınırlı ise, makinedeki veya proxy 'deki güvenlik duvarı ayarlarının bu [URL 'ler](backup-configure-vault.md#verify-internet-access) ve [IP adreslerine](backup-configure-vault.md#verify-internet-access)izin verildiğinden emin olun.|
| Aracı yüklemesi Azure Backup başarısız oldu | Microsoft Azure Kurtarma Hizmetleri yüklemesi başarısız oldu. Microsoft Azure Kurtarma Hizmetleri yüklemesi tarafından sisteme yapılan tüm değişiklikler geri alındı. (KIMLIK: 4024) | Azure aracısını el ile yükleyebilirsiniz.

## <a name="configuring-protection-group"></a>Koruma grubu yapılandırılıyor

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Koruma gruplarını yapılandırma | DPM, korumalı bilgisayardaki (korunan bilgisayar adı) uygulama bileşenini numaralandıramadı. | İlgili veri kaynağı/bileşen düzeyindeki koruma grubunu yapılandırma kullanıcı arabirimi ekranında **Yenile** ' yi seçin. |
| Koruma gruplarını yapılandırma | Koruma yapılandırılamıyor | Korumalı sunucu bir SQL Server ise, [Bu makalede](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx)açıklandığı gibi korunan bilgisayardaki sistem hesabına (NTAuthority\System adlı) sysadmin rolü izinlerinin sağlandığını doğrulayın.
| Koruma gruplarını yapılandırma | Bu koruma grubu için depolama havuzunda yeterli boş alan yok. | Depolama havuzuna eklenen diskler [bir bölüm içermemelidir](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Disklerde var olan tüm birimleri silin. Ardından bunları depolama havuzuna ekleyin.|
| İlke değişikliği |Yedekleme ilkesi değiştirilemedi. Hata: geçerli işlem, [0x29834] iç hizmet hatası nedeniyle başarısız oldu. Lütfen bir süre geçtikten sonra işlemi yeniden deneyin. Sorun devam ederse, Microsoft desteği 'ne başvurun. | **Sağlamak**<br/>Bu hata üç koşulda oluşur: güvenlik ayarları etkinleştirildiğinde, daha önce belirtilen minimum değerlerin altındaki saklama aralığını azaltmaya çalıştığınızda ve desteklenmeyen bir sürümdayken. (Desteklenmeyen sürümler Microsoft Azure Backup Server Version 2.0.9052 ve Azure Backup Sunucusu Update 1 ' dir.) <br/>**Önerilen eylem:**<br/> İlkeyle ilgili güncelleştirmelerle devam etmek için, belirtilen en düşük bekletme döneminin üzerindeki saklama süresini ayarlayın. (En düşük saklama süresi günlük için yedi gün, haftalık dört hafta, aylık veya yıllık yılda bir yıl için üç hafta) <br><br>İsteğe bağlı olarak, başka bir tercih edilen yaklaşım yedekleme aracısını güncelleştirmek ve tüm güvenlik güncelleştirmelerinden yararlanmak için Azure Backup Sunucusu. |

## <a name="backup"></a>Backup

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | İş çalışırken beklenmeyen bir hata oluştu. Cihaz kullanılamıyor. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları uygulayın:** <br> <ul><li>Koruma grubundaki öğelerde gölge kopya depolama alanını sınırsız olarak ayarlayın ve ardından tutarlılık denetimini çalıştırın.<br></li> VEYA <li>Mevcut koruma grubunu silip birden çok yeni grup oluşturmayı deneyin. Her yeni koruma grubunun içinde ayrı bir öğesi olmalıdır.</li></ul> |
| Backup | Yalnızca sistem durumunu yedekliyorsanız, korunan bilgisayarda sistem durumu yedeklemesini depolamak için yeterli boş alan olduğunu doğrulayın. | <ol><li>Windows Server Yedekleme korunan makinede yüklü olduğunu doğrulayın.</li><li>Korunan bilgisayarda sistem durumu için yeterli alan olduğunu doğrulayın. Bunu doğrulamak için en kolay yol, korunan bilgisayara gidip Windows Server Yedekleme açın, seçimlere tıklayarak, sonra BMR 'yi seçer. Kullanıcı arabirimi daha sonra ne kadar alan gerektiğini söyler. **WSB** > **yerel yedekleme** > yedekleme **zamanlaması** ' nı açın > **yedekleme yapılandırması** > **tam sunucu** ' yı seçin (boyut görüntülenir). Doğrulama için bu boyutu kullanın.</li></ol>
| Backup | BMR için yedekleme hatası | BMR boyutu büyükse, bazı uygulama dosyalarını işletim sistemi sürücüsüne taşıyın ve yeniden deneyin. |
| Backup | Yeni bir Microsoft Azure Backup sunucusundaki bir VMware VM 'yi yeniden koruma seçeneği, eklemek için kullanılabilir olarak gösterilmez. | VMware özellikleri, Microsoft Azure Backup sunucusunun eski, Kullanımdan kaldırılmış bir örneğine işaret edilir. Bu sorunu çözmek için:<br><ol><li>VCenter 'da (SC-VMM eşdeğeri), **Özet** sekmesine ve ardından **özel özniteliklere**gidin.</li>  <li>**DPMServer** değerinden eski Microsoft Azure Backup sunucu adını silin.</li>  <li>Yeni Microsoft Azure Backup sunucusuna dönün ve PG 'ı değiştirin.  **Yenile** düğmesini seçtikten sonra, sanal makine, korumaya eklemek için kullanılabilir bir onay kutusuyla birlikte görüntülenir.</li></ol> |
| Backup | Dosyalara/paylaşılan klasörlere erişirken hata oluştu | Virüsten koruma ayarlarını, [DPM sunucusunda virüsten koruma yazılımı çalıştırma](https://technet.microsoft.com/library/hh757911.aspx)başlıklı TechNet makalesinde önerildiği şekilde değiştirmeyi deneyin.|

## <a name="change-passphrase"></a>Parolayı Değiştir

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Parolayı Değiştir |Girilen güvenlik PIN 'ı hatalı. Bu işlemi gerçekleştirmek için doğru güvenlik PIN 'ini girin. |**Sağlamak**<br/> Bu hata, kritik bir işlem gerçekleştirirken (örneğin, bir parolayı değiştirme gibi) geçersiz veya süre dolduktan sonra bir güvenlik PIN kodu girdiğinizde oluşur. <br/>**Önerilen eylem:**<br/> İşlemi gerçekleştirmek için geçerli bir güvenlik PIN kodu girmelisiniz. PIN 'i almak için Azure portal oturum açın ve kurtarma hizmetleri kasasına gidin. Ardından **ayarlar** > **Özellikler** ' e gıdın ve **güvenlik PIN 'i oluşturun** > . Parolayı değiştirmek için bu PIN 'ı kullanın. |
| Parolayı Değiştir |İşlem başarısız oldu. KIMLIK: 120002 |**Sağlamak**<br/>Bu hata, güvenlik ayarları etkinleştirildiğinde veya desteklenmeyen bir sürüm kullanırken parolayı değiştirmeye çalıştığınızda oluşur.<br/>**Önerilen eylem:**<br/> Parolayı değiştirmek için, önce yedekleme aracısını 2.0.9052 olan en düşük sürüme güncelleştirmeniz gerekir. Ayrıca, güncelleştirme 1 ' in en düşük Azure Backup Sunucusu güncelleştirmeniz ve ardından geçerli bir güvenlik PIN 'i girmeniz gerekir. PIN 'i almak için Azure portal oturum açın ve kurtarma hizmetleri kasasına gidin. Ardından **ayarlar** > **Özellikler** ' e gıdın ve **güvenlik PIN 'i oluşturun** > . Parolayı değiştirmek için bu PIN 'ı kullanın. |

## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

| İşlem | Hata Ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Office 365 hesabı kullanarak e-posta bildirimleri ayarlama |Hata KIMLIĞI: 2013| **Sağlamak**<br> Office 365 hesabı kullanılmaya çalışılıyor <br>**Önerilen eylem:**<ol><li> Emin olunması gereken ilk şey, DPM sunucunuz için "alma bağlayıcısında anonim geçişe Izin ver" ayarı Exchange 'de ayarlanmıştır. Bunun nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz. TechNet 'teki [alma bağlayıcısında anonim geçişe Izin verme](https://technet.microsoft.com/library/bb232021.aspx) .</li> <li> İç SMTP geçişi kullanamaz ve Office 365 sunucunuzu kullanarak ayarlamanız gerekiyorsa, IIS 'yi geçiş olarak ayarlayabilirsiniz. DPM sunucusunu, [IIS kullanarak, SMTP 'Yi O365 'e geçirecek](https://technet.microsoft.com/library/aa995718(v=exchg.65).aspx)şekilde yapılandırın.<br><br> **Önemli:** *Etkialanı \* kullanıcı\@Domain.com biçimini kullandığınızdan emin olun.<br><br><li>DPM 'yi yerel sunucu adını SMTP sunucusu, bağlantı noktası 587 olarak kullanmak için işaretleyin. Sonra, e-postaların gelmesi gereken kullanıcı e-postasına işaret edin.<li> DPM SMTP kurulumu sayfasındaki Kullanıcı adı ve parola, DPM 'nin açık olduğu etki alanındaki bir etki alanı hesabı için olmalıdır. </li><br> **Note**: SMTP sunucu adresini değiştirirken, yeni ayarlarda değişiklik yapın, ayarlar kutusunu kapatın ve yeni değeri yansıttığından emin olmak için yeniden açın.  Yalnızca değiştirme ve test etme her zaman yeni ayarların etkili olmasına neden olabilir, bu nedenle bu şekilde test etmek en iyi uygulamadır.<br><br>Bu işlem sırasında dilediğiniz zaman DPM konsolunu kapatarak ve aşağıdaki kayıt defteri anahtarlarını düzenleyerek bu ayarları temizleyebilirsiniz: **Hklm\software\microsoft\microsoft Data Protection Manager\Notification\ <br/> SMTPPassword ve SMTPUserName anahtarlarını silin**. Yeniden başlattığınızda onları Kullanıcı arabirimine geri ekleyebilirsiniz.

## <a name="common-issues"></a>Genel sorunlar

Bu bölümde Azure Backup Sunucusu kullanırken karşılaşabileceğiniz yaygın hatalar ele alınmaktadır.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Hata iletisi | Önerilen eylem |
-- | --
Disk yedekleme kopyası geçersiz ya da eksik olduğundan yedekleme başarısız oldu. | Bu sorunu çözmek için aşağıdaki adımları doğrulayın ve işlemi yeniden deneyin: <br/> 1. bir disk kurtarma noktası oluşturun<br/> 2. veri kaynağı üzerinde tutarlılık denetimi Çalıştır <br/> 3. DataSource 'un korumasını durdurun ve ardından bu veri kaynağının korumasını yeniden yapılandırın

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Hata iletisi | Önerilen eylem |
-- | --
Çoğaltmadaki meta veriler geçersiz olduğundan kaynak birim anlık görüntüsü başarısız oldu. | Bu veri kaynağı için bir disk kurtarma noktası oluşturun ve çevrimiçi yedeklemeyi yeniden deneyin

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Hata iletisi | Önerilen eylem |
-- | --
Kaynak birim anlık görüntüsü, tutarsız veri kaynağı çoğaltması nedeniyle başarısız oldu. | Bu veri kaynağı üzerinde bir tutarlılık denetimi çalıştırın ve yeniden deneyin

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Hata iletisi | Önerilen eylem |
-- | --
Disk yedekleme çoğaltması kopyalanamadığı için yedekleme başarısız oldu.| Önceki tüm disk yedekleme çoğaltma dosyalarının (. vhdx) çıkarıldığından ve çevrimiçi yedeklemeler sırasında devam eden disk yedeklemesi olmadığından emin olun
