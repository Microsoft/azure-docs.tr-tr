---
title: Azure Backup Sunucusu sorunlarını giderme
description: Yükleme, Azure Backup Sunucusu kaydı ve uygulama iş yüklerinin yedeklenmesi ve geri yüklenmesi sorunlarını giderin.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 644946ca90c2893ba3d87f9d2ff8bfd8325f4715
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514759"
---
# <a name="troubleshoot-azure-backup-server"></a>Azure Backup Sunucusu sorunlarını giderme

Azure Backup Sunucusu kullanırken karşılaştığınız hataların sorunlarını gidermek için aşağıdaki tablolardaki bilgileri kullanın.

## <a name="basic-troubleshooting"></a>Temel sorun giderme

Microsoft Azure Backup Server (MABS) sorun gidermeye başlamadan önce aşağıdaki doğrulamayı gerçekleştirmenizi öneririz:

- [Microsoft Azure Kurtarma Hizmetleri (MARS) aracısının güncel olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS Aracısı ve Azure arasında ağ bağlantısı olduğundan emin olun](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Microsoft Azure Kurtarma Hizmetleri'nin çalıştığından emin olun (Hizmet konsolunda). Gerekirse yeniden başlatın ve işlemi yeniden deneyin
- [Boş klasör konumunda %5-10 oranında kullanılabilir alan olduğundan emin olun](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- Kayıt başarısız olursa, yüklemeye çalıştığınız sunucunun Azure Backup Sunucusu başka bir kasada zaten kayıtlı olmadığından emin olun
- Göndermeli yükleme başarısız olursa DPM aracısının zaten mevcut olup olmadığını kontrol edin. Varsa aracıyı kaldırıp tekrar yüklemeyi deneyin
- [Azure Backup ile çakışan başka bir işlem veya virüsten koruma yazılımı olmadığından emin olun](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- SQL Aracısı hizmetinin çalıştığından ve MABS sunucusunda otomatik olarak ayarlandığından emin olun<br>

## <a name="configure-antivirus-for-mabs-server"></a>MABS sunucusu için virüsten koruma yapılandırma

MABS, en popüler virüsten koruma yazılımı ürünleri ile uyumludur. Çakışmaları önlemek için aşağıdaki adımları öneririz:

1. **Gerçek zamanlı Izlemeyi devre dışı bırak** -aşağıdaki için virüsten koruma yazılımı tarafından gerçek zamanlı izlemeyi devre dışı bırakın:
    - `C:\Program Files<MABS Installation path>\XSD` klasörde
    - `C:\Program Files<MABS Installation path>\Temp` klasörde
    - Modern Yedekleme Alanı biriminin sürücü harfi
    - Çoğaltma ve aktarım günlükleri: bunu yapmak Için, klasöründe bulunan **dpmra.exe** gerçek zamanlı izlemesini devre dışı bırakın `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Gerçek zamanlı izleme performansı düşürür çünkü virüsten koruma yazılımı, korumalı sunucu ile her eşitlediğinde çoğaltmaları tarar ve MABS çoğaltmaları çoğaltmalar üzerinde her değişiklik uygularsa etkilenen tüm dosyaları tarar.
    - Yönetici Konsolu: performans üzerindeki etkileri önlemek Için **csc.exe** işleminin gerçek zamanlı izlemesini devre dışı bırakın. **csc.exe** süreci C \# derleyicisidir ve virüsten koruma yazılımı, **csc.exe** işleminin xml iletileri oluştururken gösterdiği dosyaları taradığından gerçek zamanlı izleme performansı düşürebilir. **CSC.exe** aşağıdaki yollarda bulunur:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - MABS sunucusunda yüklü olan MARS Aracısı için aşağıdaki dosyaları ve konumları dışlanmasını öneririz:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` işlem olarak
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Karalama konumu (Standart konumu kullanmıyorsanız)
2. **Korunan sunucuda gerçek zamanlı Izlemeyi devre dışı bırak**: klasörde bulunan **dpmra.exe** gerçek zamanlı izlemeyi korumalı sunucuda devre dışı bırakın `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` .
3. Virüsten **koruma yazılımını korunan sunuculardaki ve MABS sunucusundaki virüslü dosyaları silecek şekilde yapılandırın**: çoğaltmalarda ve kurtarma noktalarında veri bozulmasını engellemek için, virüsten koruma yazılımını, virüslü dosyaları otomatik olarak temizlemek veya karantinaya almak yerine silmek üzere yapılandırın. Otomatik Temizleme ve karantinaya alma, virüsten koruma yazılımının dosyaları değiştirmesine ve MABS 'lerin tespit edememesine neden olabilir.

Bir tutarlılık ile el ile eşitleme çalıştırmalısınız. Çoğaltma tutarsız olarak işaretlenmiş olsa da, virüsten koruma yazılımının çoğaltmadan bir dosya sildiği her seferinde işi denetleyin.

### <a name="mabs-installation-folders"></a>MABS yükleme klasörleri

DPM için varsayılan yükleme klasörleri aşağıdaki gibidir:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Ayrıca, Install Folder yolunu bulmak için aşağıdaki komutu çalıştırabilirsiniz:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Sağlanan kasa kimlik bilgileri geçersiz

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Kasaya kaydolma | Sağlanan kasa kimlik bilgileri geçersiz. Dosya bozuk veya kurtarma hizmetiyle ilişkili en son kimlik bilgilerine sahip değil. | Önerilen eylem: <br> <ul><li> Kasadan en son kimlik bilgileri dosyasını indirin ve yeniden deneyin. <br>VEYA</li> <li> Önceki eylem işe yaramazsa, kimlik bilgilerini farklı bir yerel dizine indirmeyi veya yeni bir kasa oluşturmayı deneyin. <br>VEYA</li> <li> Tarih ve saat ayarlarını [Bu makalede](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided)açıklandığı gibi güncelleştirmeyi deneyin. <br>VEYA</li> <li> C:\Windows\Temp 'in 65000 'den fazla dosya olup olmadığını denetleyin. Eski dosyaları başka bir konuma taşıyın veya Temp klasöründeki öğeleri silin. <br>VEYA</li> <li> Sertifikaların durumunu denetleyin. <br> a. **Bilgisayar sertifikalarını Yönet** ' i açın (Denetim Masası 'nda). <br> b. **Kişisel** düğümünü ve onun alt düğüm **sertifikalarını** genişletin.<br> c.  Sertifikayı kaldırın **Windows Azure Araçları**. <br> d. Azure Backup istemcisinde kaydı yeniden deneyin. <br> VEYA </li> <li> Herhangi bir grup ilkesinin yerinde olup olmadığını kontrol edin. </li></ul> |

## <a name="replica-is-inconsistent"></a>Çoğaltma tutarsız

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | Çoğaltma tutarsız | Koruma Grubu Sihirbazı 'ndaki otomatik tutarlılık denetimi seçeneğinin açık olduğunu doğrulayın. Çoğaltma seçenekleri ve tutarlılık denetimleri hakkında daha fazla bilgi için [Bu makaleye](/system-center/dpm/create-dpm-protection-groups) bakın.<br> <ol><li> Sistem durumu/BMR yedeklemesi durumunda, Windows Server Yedekleme korunan sunucuda yüklü olduğunu doğrulayın.</li><li> DPM/Microsoft Azure Backup sunucusundaki DPM depolama havuzunda yer alan sorunları denetleyin ve gerekli olduğu gibi depolama alanını ayırın.</li><li> Korunan sunucudaki Birim Gölge Kopyası Hizmeti durumunu denetleyin. Devre dışı durumdaysa, el ile başlayacak şekilde ayarlayın. Hizmeti sunucuda başlatın. Ardından DPM/Microsoft Azure Backup sunucu konsoluna geri dönün ve tutarlılık denetimi işiyle eşitlemeyi başlatın.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Çevrimiçi kurtarma noktası oluşturma başarısız oldu

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | Çevrimiçi kurtarma noktası oluşturma başarısız oldu | **Hata iletisi**: Windows Azure Backup Aracısı seçili birimin anlık görüntüsünü oluşturamadı. <br> **Geçici çözüm**: çoğaltma ve kurtarma noktası birimindeki alanı artırmayı deneyin.<br> <br> **Hata iletisi**: Windows Azure Backup Aracısı obengine hizmetine bağlanamıyor <br> **Geçici çözüm**: obengine 'in bilgisayardaki çalışan hizmetler listesinde var olduğunu doğrulayın. OBEngine hizmeti çalışmıyorsa, OBEngine hizmetini başlatmak için "net start OBEngine" komutunu kullanın. <br> <br> **Hata iletisi**: Bu sunucunun şifreleme parolası ayarlanmadı. Lütfen bir şifreleme parolası yapılandırın. <br> **Geçici çözüm**: bir şifreleme parolası yapılandırmayı deneyin. Başarısız olursa, aşağıdaki adımları uygulayın: <br> <ol><li>Karalama konumunun mevcut olduğunu doğrulayın. Bu, kayıt defteri **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config** bahsedilen, **scratchlocation** adında bir konum olmalıdır.</li><li> Karalama konumu varsa, eski parolayı kullanarak yeniden kaydetmeyi deneyin. *Şifreleme parolasını yapılandırdığınızda, güvenli bir konuma kaydedin.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>Orijinal ve dış DPM sunucularının aynı kasaya kayıtlı olması gerekir

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Geri Yükleme | **Hata kodu**: CBPServerRegisteredVaultDontMatchWithCurrent/kasa kimlik bilgileri hatası: 100110 <br/> <br/>**Hata iletisi**: özgün ve dış DPM sunucularının aynı kasaya kayıtlı olması gerekir | **Neden**: Bu sorun, dış DPM kurtarma seçeneğini kullanarak dosyaları özgün sunucudan alternatif sunucuya geri yüklemeye çalışırken ve Kurtarılmakta olan sunucu ve verilerin yedeklendiği orijinal sunucu aynı kurtarma hizmetleri kasasıyla ilişkilendirilmediğinde oluşur.<br/> <br/>**Geçici çözüm** Bu sorunu çözmek için hem özgün hem de alternatif sunucunun aynı kasaya kayıtlı olduğundan emin olun.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>VMware VM için çevrimiçi kurtarma noktası oluşturma işleri başarısız oluyor

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | VMware VM için çevrimiçi kurtarma noktası oluşturma işleri başarısız oluyor. DPM, şifre izleme bilgilerini almaya çalışırken VMware 'den bir hatayla karşılaştı. HataKodu-FileFaultFault (KIMLIK 33621) |  <ol><li> Etkilenen VM 'Ler için VMware üzerinde CTK 'yi sıfırlayın.</li> <li>Bağımsız diskin VMware üzerinde yerinde olmadığından emin olun.</li> <li>Etkilenen VM 'Ler için korumayı durdurun ve **Yenile** düğmesini kullanarak yeniden koruyun. </li><li>Etkilenen VM 'Ler için bir CC çalıştırın.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Sunucuda DPM aracı Düzenleyicisi hizmetindeki bir iletişim hatası nedeniyle Aracı işlemi başarısız oldu

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Aracıları korumalı sunuculara iletme | Üzerinde DPM Aracı Düzenleyicisi hizmetindeki bir iletişim hatası nedeniyle Aracı işlemi başarısız oldu \<ServerName> . | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları gerçekleştirin**: <ul><li> Güvenilmeyen bir etki alanından bir bilgisayar iliştiriyorsanız, [Bu adımları](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains)izleyin. <br> VEYA </li><li> Güvenilen bir etki alanından bir bilgisayar iliştirdiyseniz, [Bu blogda](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726)açıklanan adımları kullanarak sorun giderin. <br>VEYA</li><li> Sorun giderme adımı olarak virüsten koruma 'yı devre dışı bırakmayı deneyin. Sorunu giderirse, virüsten koruma ayarlarını [Bu makalede](/system-center/dpm/run-antivirus-server)önerildiği şekilde değiştirin.</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Kurulum, kayıt defteri meta verilerini güncelleştiremedi

| İşlem | Hata ayrıntıları | Geçici çözüm |
|-----------|---------------|------------|
|Yükleme | Kurulum, kayıt defteri meta verilerini güncelleştiremedi. Bu güncelleştirme hatası, depolama tüketiminin aşırı kullanılmasına yol açabilir. Bu güncelleştirmeyi önlemek için ReFS kırpma kayıt defteri girişini güncelleştirin. | **System\currentcontrolset\control\filesystem\refsenableınlinetrım** kayıt defteri anahtarını ayarlayın. DWORD değerini 1 olarak ayarlayın. |
|Yükleme | Kurulum, kayıt defteri meta verilerini güncelleştiremedi. Bu güncelleştirme hatası, depolama tüketiminin aşırı kullanılmasına yol açabilir. Bunu önlemek için, birim Snapoptımleştirme kayıt defteri girişini güncelleştirin. | **Software\microsoft Data Protection Manager\configuration\volsnapoptimization\writeıds** kayıt defteri anahtarını boş bir dize değeri ile oluşturun. |

## <a name="registration-and-agent-related-issues"></a>Kayıt ve aracıyla ilgili sorunlar

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Aracıları korumalı sunuculara iletme | Sunucu için belirtilen kimlik bilgileri geçersiz. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları uygulayın**: <br> Koruma aracısını üretim sunucusuna [Bu makalede](/system-center/dpm/deploy-dpm-protection-agent)belirtilen şekilde el ile yüklemeyi deneyin.|
| Azure Backup Aracısı Azure Backup hizmetine bağlanamadı (KIMLIK: 100050) | Azure Backup Aracısı Azure Backup hizmetine bağlanamadı. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları uygulayın**: <br>1. yükseltilmiş bir komut isteminde şu komutu çalıştırın: **PsExec-i-s "C:\Program Files\ınternet Explorer\iexplore.exe**. Bu, Internet Explorer penceresini açar. <br/> 2. **Araçlar**  >  **Internet seçenekleri**  >  **Bağlantılar**  >  **LAN ayarları**' na gidin. <br/> 3. bir proxy sunucu kullanacak şekilde ayarları değiştirin. Ardından proxy sunucusu ayrıntılarını sağlayın.<br/> 4. makinenizin internet erişimi sınırlı ise, makinedeki veya proxy 'deki güvenlik duvarı ayarlarının bu [URL 'ler](install-mars-agent.md#verify-internet-access) ve [IP adreslerine](install-mars-agent.md#verify-internet-access)izin verildiğinden emin olun.|
| Aracı yüklemesi Azure Backup başarısız oldu | Microsoft Azure Kurtarma Hizmetleri yüklemesi başarısız oldu. Microsoft Azure Kurtarma Hizmetleri yüklemesi tarafından sisteme yapılan tüm değişiklikler geri alındı. (KIMLIK: 4024) | Azure aracısını el ile yükleyebilirsiniz.

## <a name="configuring-protection-group"></a>Koruma grubu yapılandırılıyor

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Koruma gruplarını yapılandırma | DPM, korumalı bilgisayardaki (korunan bilgisayar adı) uygulama bileşenini numaralandıramadı. | İlgili veri kaynağı/bileşen düzeyindeki koruma grubunu yapılandırma kullanıcı arabirimi ekranında **Yenile** ' yi seçin. |
| Koruma gruplarını yapılandırma | Koruma yapılandırılamıyor | Korumalı sunucu bir SQL Server ise, [Bu makalede](/system-center/dpm/back-up-sql-server)açıklandığı gibi korunan bilgisayardaki sistem hesabına (NTAuthority\System adlı) sysadmin rolü izinlerinin sağlandığını doğrulayın.
| Koruma gruplarını yapılandırma | Bu koruma grubu için depolama havuzunda yeterli boş alan yok. | Depolama havuzuna eklenen diskler [bir bölüm içermemelidir](/system-center/dpm/create-dpm-protection-groups). Disklerde var olan tüm birimleri silin. Ardından bunları depolama havuzuna ekleyin.|
| İlke değişikliği |Yedekleme ilkesi değiştirilemedi. Hata: geçerli işlem, [0x29834] iç hizmet hatası nedeniyle başarısız oldu. Lütfen bir süre geçtikten sonra işlemi yeniden deneyin. Sorun devam ederse, Microsoft desteğine başvurun. | **Neden:**<br/>Bu hata üç koşulda oluşur: güvenlik ayarları etkinleştirildiğinde, daha önce belirtilen minimum değerlerin altındaki saklama aralığını azaltmaya çalıştığınızda ve desteklenmeyen bir sürümdayken. (Desteklenmeyen sürümler Microsoft Azure Backup Server Version 2.0.9052 ve Azure Backup Sunucusu Update 1 ' den daha düşüktür.) <br/>**Önerilen eylem:**<br/> İlkeyle ilgili güncelleştirmelerle devam etmek için, belirtilen en düşük bekletme döneminin üzerindeki saklama süresini ayarlayın. (En düşük saklama süresi günlük için yedi gün, haftalık dört hafta, aylık veya yıllık yılda bir yıl için üç hafta) <br><br>İsteğe bağlı olarak, başka bir tercih edilen yaklaşım yedekleme aracısını güncelleştirmek ve tüm güvenlik güncelleştirmelerinden yararlanmak için Azure Backup Sunucusu. |

## <a name="backup"></a>Backup

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Backup | İş çalışırken beklenmeyen bir hata oluştu. Cihaz kullanılamıyor. | **Üründe gösterilen önerilen eylem işe yaramazsa aşağıdaki adımları uygulayın:** <br> <ul><li>Koruma grubundaki öğelerde gölge kopya depolama alanını sınırsız olarak ayarlayın ve ardından tutarlılık denetimini çalıştırın.<br></li> VEYA <li>Mevcut koruma grubunu silip birden çok yeni grup oluşturmayı deneyin. Her yeni koruma grubunun içinde ayrı bir öğesi olmalıdır.</li></ul> |
| Backup | Yalnızca sistem durumunu yedekliyorsanız, korunan bilgisayarda sistem durumu yedeklemesini depolamak için yeterli boş alan olduğunu doğrulayın. | <ol><li>Windows Server Yedekleme korunan makinede yüklü olduğunu doğrulayın.</li><li>Korunan bilgisayarda sistem durumu için yeterli alan olduğunu doğrulayın. Bunu doğrulamak için en kolay yol, korunan bilgisayara gidip Windows Server Yedekleme açın, seçimlere tıklayarak, sonra BMR 'yi seçer. Kullanıcı arabirimi daha sonra ne kadar alan gerektiğini söyler. **WSB**  >  **yerel yedekleme**  >  **Yedekleme zamanlamasını** açın  >  **yedekleme yapılandırması**  >  **tam sunucu** seçin (boyut görüntülenir). Doğrulama için bu boyutu kullanın.</li></ol>
| Backup | BMR için yedekleme hatası | BMR boyutu büyükse, bazı uygulama dosyalarını işletim sistemi sürücüsüne taşıyın ve yeniden deneyin. |
| Backup | Yeni bir Microsoft Azure Backup sunucusundaki bir VMware VM 'yi yeniden koruma seçeneği, eklemek için kullanılabilir olarak gösterilmez. | VMware özellikleri, Microsoft Azure Backup sunucusunun eski, Kullanımdan kaldırılmış bir örneğine işaret edilir. Bu sorunu çözmek için:<br><ol><li>VCenter 'da (SC-VMM eşdeğeri), **Özet** sekmesine ve ardından **özel özniteliklere** gidin.</li>  <li>**DPMServer** değerinden eski Microsoft Azure Backup sunucu adını silin.</li>  <li>Yeni Microsoft Azure Backup sunucusuna dönün ve PG 'ı değiştirin.  **Yenile** düğmesini seçtikten sonra, sanal makine, korumaya eklemek için kullanılabilir bir onay kutusuyla birlikte görüntülenir.</li></ol> |
| Backup | Dosyalara/paylaşılan klasörlere erişirken hata oluştu | Virüsten koruma ayarlarını bu makalede önerildiği şekilde değiştirmeyi deneyin [DPM sunucusunda virüsten koruma yazılımı çalıştırın](/system-center/dpm/run-antivirus-server).|

## <a name="change-passphrase"></a>Parolayı Değiştir

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| Parolayı Değiştir |Girilen güvenlik PIN 'ı hatalı. Bu işlemi gerçekleştirmek için doğru güvenlik PIN 'ini girin. |**Neden:**<br/> Bu hata, kritik bir işlem gerçekleştirirken (örneğin, bir parolayı değiştirme gibi) geçersiz veya süre dolduktan sonra bir güvenlik PIN kodu girdiğinizde oluşur. <br/>**Önerilen eylem:**<br/> İşlemi gerçekleştirmek için geçerli bir güvenlik PIN kodu girmelisiniz. PIN 'i almak için Azure portal oturum açın ve kurtarma hizmetleri kasasına gidin. Ardından **Ayarlar**  >  **Özellikler**  >  **güvenlik PIN 'i oluştur**'a gidin. Parolayı değiştirmek için bu PIN 'ı kullanın. |
| Parolayı Değiştir |İşlem başarısız oldu. KIMLIK: 120002 |**Neden:**<br/>Bu hata, güvenlik ayarları etkinleştirildiğinde veya desteklenmeyen bir sürüm kullanırken parolayı değiştirmeye çalıştığınızda oluşur.<br/>**Önerilen eylem:**<br/> Parolayı değiştirmek için, önce yedekleme aracısını 2.0.9052 olan en düşük sürüme güncelleştirmeniz gerekir. Ayrıca, güncelleştirme 1 ' in en düşük Azure Backup Sunucusu güncelleştirmeniz ve ardından geçerli bir güvenlik PIN 'i girmeniz gerekir. PIN 'i almak için Azure portal oturum açın ve kurtarma hizmetleri kasasına gidin. Ardından **Ayarlar**  >  **Özellikler**  >  **güvenlik PIN 'i oluştur**'a gidin. Parolayı değiştirmek için bu PIN 'ı kullanın. |

## <a name="configure-email-notifications"></a>E-posta bildirimlerini yapılandırma

| İşlem | Hata ayrıntıları | Geçici çözüm |
| --- | --- | --- |
| İş veya okul hesabı kullanarak e-posta bildirimleri ayarlama |Hata KIMLIĞI: 2013| **Neden:**<br> İş veya okul hesabı kullanılmaya çalışılıyor <br>**Önerilen eylem:**<ol><li> Emin olunması gereken ilk şey, DPM sunucunuz için "alma bağlayıcısında anonim geçişe Izin ver" ayarı Exchange 'de ayarlanmıştır. Bunun nasıl yapılandırılacağı hakkında daha fazla bilgi için bkz. [alma bağlayıcısında anonim geçişe Izin verme](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> İç SMTP geçişi kullanamaz ve Office 365 sunucunuzu kullanarak ayarlamanız gerekiyorsa, IIS 'yi geçiş olarak ayarlayabilirsiniz. IIS kullanarak, DPM sunucusunu, [SMTP 'Yi Office 365 'e geçirecek](/exchange/mail-flow/test-smtp-with-telnet)şekilde yapılandırın.<br><br>  Etkialanı \ Kullanıcı domain.com biçimini kullandığınızdan emin olun \@ . <br><br><li>DPM 'yi yerel sunucu adını SMTP sunucusu, bağlantı noktası 587 olarak kullanmak için işaretleyin. Sonra, e-postaların gelmesi gereken kullanıcı e-postasına işaret edin.<li> DPM SMTP kurulumu sayfasındaki Kullanıcı adı ve parola, DPM 'nin açık olduğu etki alanındaki bir etki alanı hesabı için olmalıdır. </li><br> SMTP sunucu adresini değiştirirken, yeni ayarlarda değişiklik yapın, ayarlar kutusunu kapatın ve yeni değeri yansıttığından emin olmak için yeniden açın.  Yalnızca değiştirme ve test etme her zaman yeni ayarların etkili olmasına neden olabilir, bu nedenle bu şekilde test etmek en iyi uygulamadır.<br><br>Bu işlem sırasında dilediğiniz zaman, DPM konsolunu kapatarak ve aşağıdaki kayıt defteri anahtarlarını düzenleyerek bu ayarları temizleyebilirsiniz: **Hklm\software\microsoft\microsoft Data Protection Manager\Notification\ <br/> Password ve smtpusername anahtarlarını silin**. Yeniden başlattığınızda onları Kullanıcı arabirimine geri ekleyebilirsiniz.

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
