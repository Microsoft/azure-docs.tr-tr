---
title: Aracı ve uzantı sorunlarını giderme
description: Belirtiler, nedenler ve Aracı, uzantısı ve disklerle ilgili Azure Backup hatalarının çözümleri.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.service: backup
ms.openlocfilehash: 0313394ad149460f82c98c63cab95b922b4a3da2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519614"
---
# <a name="troubleshoot-azure-backup-failure-issues-with-the-agent-or-extension"></a>Sorun giderme Azure Backup hatası: aracıdaki veya uzantıdaki sorunlar

Bu makalede, VM Aracısı ve uzantısıyla iletişim ile ilgili Azure Backup hatalarını çözmenize yardımcı olabilecek sorun giderme adımları sunulmaktadır.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="step-by-step-guide-to-troubleshoot-backup-failures"></a>Yedekleme hatalarıyla ilgili sorunları gidermek için adım adım kılavuz

En yaygın yedekleme hatalarının çoğu, aşağıda listelenen sorun giderme adımları izleyerek kendiliğinden çözülebilir:

### <a name="step-1-check-azure-vm-health"></a>1. Adım: Azure VM sistem durumunu denetleme

- **Azure VM sağlama durumunun ' çalışıyor ' olduğundan emin olun**: [VM sağlama durumu](../virtual-machines/states-billing.md) **durdurulmuş/serbest bırakıldı/güncelleştirme** durumundaysa, yedekleme işlemini kesintiye uğratacaktır. *Azure portal > vm > genel bakış >* açın ve **çalışır** durumda olduğundan emin olmak için VM durumunu denetleyin ve yedekleme işlemini yeniden deneyin.
- **Bekleyen işletim sistemi güncelleştirmelerini gözden geçirme veya yeniden başlatmalar**: VM 'de bekleyen bir işletim sistemi güncelleştirmesi veya bekleyen yeniden başlatmalar olmadığından emin olun.

### <a name="step-2-check-azure-vm-guest-agent-service-health"></a>2. Adım: Azure VM Konuk Aracısı hizmet durumunu denetleme

- **Azure VM Konuk Aracısı hizmetinin başlatıldığından ve güncel olduğundan emin olun**:
  - Bir Windows VM 'de:
    - **Services. msc** ' ye gidin ve **Windows Azure VM Konuk Aracısı hizmeti** 'nin çalışır ve çalışıyor olduğundan emin olun. Ayrıca, [en son sürümün](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [WINDOWS VM Konuk Aracısı sorunları](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms).
    - Azure VM Aracısı, Portal, PowerShell, komut satırı arabirimi veya Azure Resource Manager şablonundan Azure Marketi görüntüsünden dağıtılan herhangi bir Windows sanal makinesinde varsayılan olarak yüklenir. Azure 'a dağıtılan özel bir VM görüntüsü oluşturduğunuzda [, aracının el ile yüklenmesi](../virtual-machines/extensions/agent-windows.md#manual-installation) gerekebilir.
    - [Desteklenen Windows işletim sisteminde](backup-support-matrix-iaas.md#operating-system-support-windows)VM 'nin çalışıp çalışmamasından emin olmak için destek matrisini gözden geçirin.
  - Linux VM 'de,
    - Komutunu yürüterek Azure VM Konuk Aracısı hizmetinin çalıştığından emin olun `ps-e` . Ayrıca, [en son sürümün](../virtual-machines/extensions/update-linux-agent.md) yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [LINUX VM Konuk Aracısı sorunları](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms).
    - [Sistem paketlerinde LINUX VM Aracısı bağımlılıklarının](../virtual-machines/extensions/agent-linux.md#requirements) desteklenen yapılandırmaya sahip olduğundan emin olun. Örneğin: desteklenen Python sürümü 2,6 ve üzeri.
    - [Desteklenen Linux işletim sisteminde](backup-support-matrix-iaas.md#operating-system-support-linux) VM 'nin çalışıp çalışmamasından emin olmak için destek matrisini gözden geçirin.

### <a name="step-3-check-azure-vm-extension-health"></a>3. Adım: Azure VM Uzantısı sistem durumunu denetleme

- **Tüm Azure VM uzantılarının ' sağlama başarılı ' durumunda olduğundan emin olun**: herhangi bir uzantı başarısız durumdaysa yedeklemeyi kesintiye uğratabilirler.
- *Azure portal > VM > ayarları > uzantıları > uzantıları durumunu açın* ve tüm uzantıların **sağlama başarılı** durumunda olup olmadığını denetleyin.
- Tüm [uzantı sorunlarının](../virtual-machines/extensions/overview.md#troubleshoot-extensions) çözümlendiğinden emin olun ve yedekleme işlemini yeniden deneyin.
- **Com+ sistem uygulamasının çalışır ve çalışıyor olduğundan emin olun** . Ayrıca, **Dağıtılmış işlem Düzenleyicisi hizmetin** **ağ hizmeti hesabı** olarak çalışıyor olması gerekir. [Com+ ve MSDTC sorunlarını gidermek](backup-azure-vms-troubleshoot.md#extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error)için bu makaledeki adımları izleyin.

### <a name="step-4-check-azure-backup-vm-extension-health"></a>4. Adım: Azure Backup VM Uzantısı sistem durumunu denetleme

Azure Backup, Azure sanal makinesinin uygulamayla tutarlı bir yedeğini almak için VM anlık görüntüsü uzantısını kullanır. Azure Backup, yedekleme etkinleştirildikten sonra tetiklenen ilk zamanlanan yedeklemenin bir parçası olarak uzantıyı yükleyecek.

- **VMSnapshot uzantısının başarısız durumda olmadığından emin olun**: Azure Backup uzantısının sağlıklı olduğundan emin olmak için bu [bölümde](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) listelenen adımları izleyin.

- **Virüsten koruma 'nın uzantıyı engelleyip engellemediğini denetle**: bazı virüsten koruma yazılımları, uzantıların yürütülmesini engelleyebilir.
  
  Yedekleme hatası sırasında, ***Olay Görüntüleyicisi uygulama günlükleri** _ ' de _ *_Hatalı uygulama adı: IaaSBcdrExtension.exe_* * olan günlük girişleri olup olmadığını doğrulayın. Girişleri görürseniz, VM 'de yapılandırılmış bir virüsten koruma, yedekleme uzantısının yürütülmesini kısıtlıyor olabilir. Virüsten koruma yapılandırmasındaki aşağıdaki dizinleri çıkararak test edin ve yedekleme işlemini yeniden deneyin.
  - `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
  - `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

- **Ağ erişiminin gerekli** olup olmadığını denetleyin: uzantı paketleri Azure depolama uzantısı deposundan indirilir ve uzantı durumu karşıya yüklemeleri Azure depolama 'ya gönderilir. [Daha fazla bilgi edinin](../virtual-machines/extensions/features-windows.md#network-access).
  - Aracının desteklenmeyen bir sürümü kullanıyorsanız, VM 'den o bölgedeki Azure depolama 'ya giden erişime izin vermeniz gerekir.
  - `168.63.129.16`Konuk güvenlik duvarını veya bir ara sunucu kullanarak erişimi engellediyseniz, uzantılar yukarıdakilerden bağımsız olarak başarısız olur. 80, 443 ve 32526 bağlantı noktaları gereklidir, [daha fazla bilgi edinin](../virtual-machines/extensions/features-windows.md#network-access).

- **Konuk VM IÇINDE DHCP 'nin etkinleştirildiğinden emin olun**: Bu, IaaS VM yedeklemesinin ÇALıŞMASı için DHCP 'den ana bilgisayar veya doku adresi almak için gereklidir. Statik bir özel IP gerekiyorsa, Azure portal veya PowerShell aracılığıyla yapılandırmanız ve VM 'nin içindeki DHCP seçeneğinin etkinleştirildiğinden emin olmanız gerekir, [daha fazla bilgi edinin](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken).

- **VSS Yazıcı hizmetinin çalışır olduğundan emin olun**: [VSS yazıcı sorunlarını gidermek](backup-azure-vms-troubleshoot.md#extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state)için aşağıdaki adımları izleyin.
- **Yedekleme en iyi yöntem kılavuzlarını izleyin**: [Azure VM yedeklemesini etkinleştirmek için en iyi uygulamaları](backup-azure-vms-introduction.md#best-practices)gözden geçirin.
- **Şifrelenmiş diskler için yönergeleri gözden geçirin**: şifrelenmiş disk Içeren VM 'ler için yedeklemeyi etkinleştirirseniz, tüm gerekli izinleri sağladığınızdan emin olun. Daha fazla bilgi için bkz. [şifrelenmiş Azure VM 'Yi yedekleme ve geri yükleme](backup-azure-vms-encryption.md).

## <a name="usererrorguestagentstatusunavailable---vm-agent-unable-to-communicate-with-azure-backup"></a><a name="UserErrorGuestAgentStatusUnavailable-vm-agent-unable-to-communicate-with-azure-backup"></a>UserErrorGuestAgentStatusUnavailable - VM aracısı Azure Backup ile iletişim kuramıyor

**Hata kodu**: usererrorguestagentstatusunavailable <br>
**Hata iletisi**: VM Aracısı Azure Backup ile iletişim kuramadı<br>

Azure VM Aracısı durdurulmuş, süresi geçmiş, tutarsız durumda veya yüklü olmayabilir. Bu durumlar Azure Backup hizmetinin anlık görüntüleri tetiklemesini önler.

- VM **durumunun** **çalıştığından** ve **aracı durumunun** **Azure portal > VM > ayarları > Özellikler > bölmesini açın** **.** VM Aracısı durdurulmuşsa veya tutarsız bir durumdaysa aracıyı yeniden başlatın<br>
  - Windows VM 'Leri için, Konuk aracısını yeniden başlatmak için aşağıdaki [adımları](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms) izleyin.<br>
  - Linux VM 'Ler için, Konuk aracısını yeniden başlatmak için aşağıdaki [adımları](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) izleyin.
- Tüm uzantıların **sağlama başarılı** durumunda olduğundan emin olmak için **Azure portal > VM > ayarları > > uzantıları** ' nı açın. Aksi takdirde, bu sorunu çözmek için aşağıdaki [adımları](#usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state) izleyin.

## <a name="guestagentsnapshottaskstatuserror---could-not-communicate-with-the-vm-agent-for-snapshot-status"></a>GuestAgentSnapshotTaskStatusError - Anlık görüntü durumu için VM aracısı ile iletişim kurulamadı

**Hata kodu**: guestagentsnapshottaskstatuserror<br>
**Hata iletisi**: anlık görüntü durumu için VM aracısıyla iletişim kurulamadı <br>

Azure Backup hizmeti için bir VM 'yi kaydettikten ve zamanladıktan sonra, yedekleme işlemi, bir noktadan noktaya anlık görüntü almak için VM yedekleme uzantısıyla iletişim kurarak işi başlatır. Aşağıdaki koşullardan herhangi biri, anlık görüntünün tetiklenmesi önlenebilir. Anlık görüntü tetiklenmemişse bir yedekleme hatası oluşabilir. Aşağıdaki sorun giderme adımlarını listelenen sırayla doldurun ve ardından işleminizi yeniden deneyin:  

**Neden 1: [Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'leri için)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  

**Neden 2: [VM 'de yüklü olan aracı güncel değil (Linux VM 'ler için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

**Neden 3: [anlık görüntü durumu alınamaz veya bir anlık görüntü](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) alınamaz**

**Neden 4: [VM Aracısı yapılandırma seçenekleri ayarlanmamış (Linux sanal makineleri için)](#vm-agent-configuration-options-are-not-set-for-linux-vms)**

**Neden 5: [uygulama denetimi çözümü engelliyor IaaSBcdrExtension.exe](#application-control-solution-is-blocking-iaasbcdrextensionexe)**

## <a name="usererrorvmprovisioningstatefailed---the-vm-is-in-failed-provisioning-state"></a>UserErrorVmProvisioningStateFailed - VM başarısız sağlama durumunda

**Hata kodu**: usererrorvmprovisioningstatefailed<br>
**Hata iletisi**: VM başarısız sağlama durumunda<br>

Bu hata, uzantı hatalarından biri VM 'yi sağlama başarısız durumuna koyarken oluşur.<br>**Azure portal > VM > ayarları > uzantıları > uzantıları durumunu açın** ve tüm uzantıların **sağlama başarılı** durumunda olup olmadığını denetleyin. Daha fazla bilgi için bkz. [sağlama durumları](../virtual-machines/states-billing.md).

- Herhangi bir uzantı başarısız durumdaysa, yedeklemeyi kesintiye uğratabilirler. Uzantı sorunlarının çözümlendiğinden emin olun ve yedekleme işlemini yeniden deneyin.
- VM sağlama durumu bir güncelleştirme durumundaysa, yedeklemeyi kesintiye uğratabilirler. Sağlıklı olduğundan emin olun ve yedekleme işlemini yeniden deneyin.

## <a name="usererrorrpcollectionlimitreached---the-restore-point-collection-max-limit-has-reached"></a>UserErrorRpCollectionLimitReached - Geri Yükleme Noktası koleksiyonu maksimum sınırına ulaşıldı

**Hata kodu**: usererrorrpcollectionlimitulaşıldı <br>
**Hata iletisi**: geri yükleme noktası koleksiyonu en yüksek sınırına ulaşıldı. <br>

- Kurtarma noktası kaynak grubunda, kurtarma noktalarının otomatik temizlenmesini engelleyen bir kilit olduğunda bu sorun oluşabilir.
- Bir günde birden çok yedeklemenin tetiklenmesi durumunda da bu sorun oluşabilir. Şu anda günde yalnızca bir yedekleme öneriyoruz çünkü anlık geri yükleme noktaları yapılandırılmış anlık görüntü saklaması başına 1-5 gün arası saklanıyor ve verili bir zamanda sanal makineyle yalnızca 18 anlık RP ilişkilendirilebiliyor. <br>
- Bir VM için geri yükleme noktası koleksiyonları ve kaynak grupları genelinde geri yükleme noktalarının sayısı 18 ' i aşamaz. Yeni bir geri yükleme noktası oluşturmak için mevcut geri yükleme noktalarını silin.

Önerilen Eylem:<br>
Bu sorunu çözmek için VM 'nin kaynak grubundaki kilidi kaldırın ve temizleme işlemini tetiklemek için işlemi yeniden deneyin.
> [!NOTE]
> Yedekleme hizmeti, geri yükleme noktası koleksiyonunu depolamak için VM 'nin kaynak grubundan farklı bir kaynak grubu oluşturur. Yedekleme hizmeti tarafından kullanılmak üzere oluşturulan kaynak grubunu kilitlemeniz önerilir. Yedekleme hizmeti tarafından oluşturulan kaynak grubunun adlandırma biçimi: AzureBackupRG_ `<Geo>` _ `<number>` . Örneğin: *AzureBackupRG_northeurope_1*

**1. Adım: [kilidi geri yükleme noktası kaynak grubundan kaldırma](#remove_lock_from_the_recovery_point_resource_group)** <br>
**2. Adım: [geri yükleme noktası koleksiyonunu Temizleme](#clean_up_restore_point_collection)**<br>

## <a name="usererrorkeyvaultpermissionsnotconfigured---backup-doesnt-have-sufficient-permissions-to-the-key-vault-for-backup-of-encrypted-vms"></a>UserErrorKeyvaultPermissionsNotConfigured-yedekleme, şifrelenmiş VM 'lerin yedeklenmesi için Anahtar Kasası üzerinde yeterli izinlere sahip değil

**Hata kodu**: usererrorkeyvaultpermissionsnotconfigured <br>
**Hata iletisi**: yedekleme, şifrelenmiş VM 'lerin yedeklenmesi için Anahtar Kasası üzerinde yeterli izinlere sahip değil. <br>

Bir yedekleme işleminin şifreli VM 'lerde başarılı olması için anahtar kasasına erişmek için gereken izinlere sahip olması gerekir. İzinler [Azure Portal](./backup-azure-vms-encryption.md) veya [PowerShell](./backup-azure-vms-automation.md#enable-protection)aracılığıyla ayarlanabilir.

## <a name="extensionsnapshotfailednonetwork---snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a><a name="ExtensionSnapshotFailedNoNetwork-snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine"></a>ExtensionSnapshotFailedNoNetwork - Sanal makinede ağ bağlantısı olmaması nedeniyle Anlık Görüntü işlemi başarısız oldu

**Hata kodu**: extensionsnapshotfailednonetwork<br>
**Hata iletisi**: sanal makinede ağ bağlantısı olmaması nedeniyle anlık görüntü işlemi başarısız oldu<br>

Azure Backup hizmeti için bir VM 'yi kaydettikten ve zamanladıktan sonra, yedekleme işlemi, bir noktadan noktaya anlık görüntü almak için VM yedekleme uzantısıyla iletişim kurarak işi başlatır. Aşağıdaki koşullardan herhangi biri, anlık görüntünün tetiklenmesi önlenebilir. Anlık görüntü tetiklenmemişse bir yedekleme hatası oluşabilir. Aşağıdaki sorun giderme adımını tamamlayıp işleminizi yeniden deneyin:

**[Anlık görüntü durumu alınamaz veya bir anlık görüntü alınamaz](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken)**  

## <a name="extensionoperationfailedformanageddisks---vmsnapshot-extension-operation-failed"></a><a name="ExtensionOperationFailed-vmsnapshot-extension-operation-failed"></a>ExtensionOperationFailedForManagedDisks - VMSnapshot uzantısı işlemi başarısız oldu

**Hata kodu**: extensionoperationfailedformanageddisks <br>
**Hata iletisi**: VMSnapshot genişletme işlemi başarısız oldu<br>

Azure Backup hizmeti için bir VM 'yi kaydettikten ve zamanladıktan sonra, yedekleme işlemi, bir noktadan noktaya anlık görüntü almak için VM yedekleme uzantısıyla iletişim kurarak işi başlatır. Aşağıdaki koşullardan herhangi biri, anlık görüntünün tetiklenmesi önlenebilir. Anlık görüntü tetiklenmemişse bir yedekleme hatası oluşabilir. Aşağıdaki sorun giderme adımlarını listelenen sırayla doldurun ve ardından işleminizi yeniden deneyin:  
**Neden 1: [anlık görüntü durumu alınamaz veya bir anlık görüntü](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) alınamaz**  
**Neden 2: [Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'leri için)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Neden 3: [VM 'de yüklü olan aracı güncel değil (Linux VM 'ler için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**

## <a name="backupoperationfailed--backupoperationfailedv2---backup-fails-with-an-internal-error"></a>BackUpOperationFailed/BackUpOperationFailedV2 - Yedekleme başarısız, iç hata

**Hata kodu**: backupoperationfailed/BackUpOperationFailedV2 <br>
**Hata iletisi**: yedekleme bir iç hata ile başarısız oldu-lütfen işlemi birkaç dakika içinde yeniden deneyin <br>

Azure Backup hizmeti için bir VM 'yi kaydettikten ve zamanladıktan sonra, yedekleme işlemi bir zaman noktaya anlık görüntü almak için VM yedekleme uzantısıyla iletişim kurarak işi başlatır. Aşağıdaki koşullardan herhangi biri, anlık görüntünün tetiklenmesi önlenebilir. Anlık görüntü tetiklenmemişse bir yedekleme hatası oluşabilir. Aşağıdaki sorun giderme adımlarını listelenen sırayla doldurun ve ardından işleminizi yeniden deneyin:  
**Neden 1: [Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'leri için)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**  
**Neden 2: [VM 'de yüklü olan aracı güncel değil (Linux VM 'ler için)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Neden 3: [anlık görüntü durumu alınamaz veya bir anlık görüntü](#the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken) alınamaz**  
**Neden 4: [yedekleme hizmeti 'nin, bir kaynak grubu kilidi nedeniyle eski geri yükleme noktalarını silme izni](#remove_lock_from_the_recovery_point_resource_group) yok**<br>

## <a name="usererrorunsupporteddisksize---the-configured-disk-sizes-is-currently-not-supported-by-azure-backup"></a>UserErrorUnsupportedDiskSize-yapılandırılan disk boyutu (ler) Şu anda Azure Backup tarafından desteklenmiyor

**Hata kodu**: usererrorunsupporteddisksize <br>
**Hata iletisi**: yapılandırılan disk boyutu (ler) şu anda Azure Backup tarafından desteklenmiyor. <br>

Disk boyutu 32 TB 'den büyük olan bir VM yedeklenirken yedekleme işlemi başarısız olabilir. Ayrıca, boyutu 4 TB 'tan büyük şifrelenmiş disklerin yedeklenmesi Şu anda desteklenmemektedir. Diskleri bölerek disk boyutlarının desteklenen sınıra eşit veya ondan daha az olduğundan emin olun.

## <a name="usererrorbackupoperationinprogress---unable-to-initiate-backup-as-another-backup-operation-is-currently-in-progress"></a>Usererrorbackupoperationınprogress-Şu anda başka bir yedekleme işlemi sürdüğünden yedekleme başlatılamıyor

**Hata kodu**: usererrorbackupoperationınprogress <br>
**Hata iletisi**: Şu anda başka bir yedekleme işlemi devam ettiğinden yedekleme başlatılamadı<br>

Sürmekte olan bir yedekleme işi olduğundan son yedekleme işiniz başarısız oldu. Geçerli iş bitene kadar yeni bir yedekleme işi başlatamazsınız. Başka bir yedekleme işlemini tetiklemeden veya planlamadan önce sürmekte olan yedekleme işleminin tamamlandığından emin olun. Yedekleme işlerinin durumunu denetlemek için aşağıdaki adımları uygulayın:

1. Azure portal oturum açın, **tüm hizmetler**' i seçin. Kurtarma Hizmetleri yazın ve **Kurtarma Hizmetleri kasaları**' nı seçin. Kurtarma Hizmetleri kasalarının listesi görünür.
2. Kurtarma Hizmetleri kasaları listesinden yedeklemenin yapılandırıldığı bir kasa seçin.
3. Kasa panosu menüsünde **yedekleme işleri** ' ni seçin, tüm yedekleme işlerini görüntüler.
   - Devam eden bir yedekleme işi varsa işlemin tamamlanmasını bekleyin veya yedekleme işini iptal edin.
     - Yedekleme işini iptal etmek için yedekleme işine sağ tıklayın ve Iptal ' i seçin veya [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) **'i** kullanın.
   - Yedeği farklı bir kasada yeniden yapılandırdıysanız, eski kasada çalışan bir yedekleme işi olmadığından emin olun. Varsa, yedekleme işini iptal edin.
     - Yedekleme işini iptal etmek için yedekleme işine sağ tıklayın ve **iptal** ' i veya [PowerShell](/powershell/module/az.recoveryservices/stop-azrecoveryservicesbackupjob) kullan ' ı seçin.
4. Yedekleme işlemini yeniden deneyin.

Zamanlanmış yedekleme işlemi daha uzun sürüyorsa, sonraki yedekleme yapılandırması ile çakışıyor ve [En Iyi uygulamaları](backup-azure-vms-introduction.md#best-practices), [yedekleme performansını](backup-azure-vms-introduction.md#backup-performance)ve [geri yükleme](backup-azure-vms-introduction.md#backup-and-restore-considerations)konusunu gözden geçirin.

## <a name="usererrorcrpreportedusererror---backup-failed-due-to-an-error-for-details-see-job-error-message-details"></a>UserErrorCrpReportedUserError - Yedekleme bir hatadan dolayı başarısız oldu. Ayrıntılar için İş Hata İletisi Ayrıntıları’na bakın

**Hata kodu**: usererrorcrpreportedusererror <br>
**Hata iletisi**: bir hata nedeniyle yedekleme başarısız oldu. Ayrıntılar için bkz. Iş hata Iletisi ayrıntıları.

Bu hata IaaS VM 'den raporlanır. Sorunun kök nedenini belirlemek için kurtarma hizmetleri Kasası ayarları ' na gidin. **İzleme** bölümünde, filtre uygulamak ve durumu görüntülemek için **yedekleme işleri** ' ni seçin. Temel alınan hata iletisi ayrıntılarını gözden geçirmek için **hatalar** ' ı seçin. Hata ayrıntıları sayfasındaki önerilere göre daha fazla eylem gerçekleştirin.

## <a name="usererrorbcmdatasourcenotpresent---backup-failed-this-virtual-machine-is-not-actively-protected-by-azure-backup"></a>Usererrorbcmdatasourcenotsun-yedekleme başarısız oldu: Bu sanal makine, Azure Backup tarafından korunmuyor (etkin değil)

**Hata kodu**: usererrorbcmdatasourcenotvar <br>
**Hata iletisi**: yedekleme başarısız oldu: Bu sanal makine, Azure Backup tarafından korunmuyor (etkin değil).

Belirtilen sanal makinenin Azure Backup tarafından korunan etkin değil (duraklatma durumunda değil) olup olmadığını denetleyin. Bu sorunu aşmak için, sanal makinenin etkin olduğundan emin olun ve işlemi yeniden deneyin.

## <a name="causes-and-solutions"></a>Nedenler ve çözümler

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Aracı VM 'ye yüklendi, ancak yanıt vermiyor (Windows VM 'Leri için)

#### <a name="solution-for-this-error"></a>Bu hata için çözüm

VM Aracısı bozulmuş olabilir veya hizmet durdurulmuş olabilir. VM Aracısı 'nı yeniden yüklemek en son sürümü almaya yardımcı olur. Hizmet ile iletişimin yeniden başlatılmasına de yardımcı olur.

1. Windows Azure Konuk Aracısı hizmetinin VM hizmetlerinde çalışıp çalışmadığını belirleme (Services. msc). Windows Azure Konuk Aracısı hizmetini yeniden başlatmayı deneyin ve yedeklemeyi başlatın.
2. Windows Azure Konuk Aracısı hizmeti Hizmetler 'de görülemiyorsa, Denetim Masası 'nda, Windows Azure Konuk Aracısı hizmetinin yüklenip yüklenmediğini öğrenmek için **Programlar ve Özellikler** ' e gidin.
3. Windows Azure Konuk Aracısı **Programlar ve Özellikler**' de görünürse, Microsoft Azure Konuk aracısını kaldırın.
4. [Aracı MSI ' nın en son sürümünü](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)indirip yükleyin. Yüklemeyi gerçekleştirmek için yönetici haklarına sahip olmanız gerekir.
5. Windows Azure Konuk Aracısı hizmetlerinin hizmetler 'de göründüğünü doğrulayın.
6. İsteğe bağlı yedekleme çalıştırın:
   - Portalda **Şimdi Yedekle**' yi seçin.

Ayrıca, VM 'de [Microsoft .NET 4,5 ' nin yüklü](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) olduğunu doğrulayın. VM aracısının hizmetle iletişim kurması için .NET 4,5 gerekir.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>VM 'de yüklü olan aracı güncel değil (Linux VM 'Leri için)

#### <a name="solution"></a>Çözüm

Linux VM 'Leri için aracıyla ilgili veya uzantı ile ilgili çoğu başarısızlık, süresi geçmiş bir VM aracısını etkileyen sorunlardan kaynaklanır. Bu sorunu gidermek için aşağıdaki genel yönergeleri izleyin:

1. [LINUX VM aracısını güncelleştirme](../virtual-machines/extensions/update-linux-agent.md)yönergelerini izleyin.

   > [!NOTE]
   > Aracıyı yalnızca bir dağıtım deposu aracılığıyla güncelleştirmenizi *önemle tavsiye* ederiz. Doğrudan GitHub 'dan aracı kodunu indirmenizi ve güncelleştirmeyi önermiyoruz. Dağıtım için en son aracı kullanılamıyorsa, nasıl yükleneceğine ilişkin yönergeler için dağıtım desteğiyle iletişim kurun. En son aracıyı denetlemek için GitHub deposundaki [Windows Azure Linux Aracısı](https://github.com/Azure/WALinuxAgent/releases) sayfasına gidin.

2. Aşağıdaki komutu çalıştırarak Azure aracısının VM üzerinde çalıştığından emin olun: `ps -e`

   İşlem çalışmıyorsa, aşağıdaki komutları kullanarak yeniden başlatın:

   - Ubuntu için: `service walinuxagent start`
   - Diğer dağıtımlar için: `service waagent start`

3. [Otomatik yeniden başlatma aracısını yapılandırın](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Yeni bir test yedeklemesi çalıştırın. Hata devam ederse, VM 'den aşağıdaki günlükleri toplayın:

   - /var/lib/waagent/*. xml
   - /var/log/waagent.log
   - /var/log/azure/*

Waagent için ayrıntılı günlük kaydı gerekiyorsa, şu adımları izleyin:

1. /Etc/waagent.exe dosyasında şu satırı bulun: **ayrıntılı günlüğü etkinleştir (y | n)**
2. **Logs. Verbose** değerini *n* ile *y* arasında değiştirin.
3. Değişikliği kaydedin ve ardından bu bölümde daha önce açıklanan adımları tamamlayarak waagent 'ı yeniden başlatın.

### <a name="vm-agent-configuration-options-are-not-set-for-linux-vms"></a>VM-Agent yapılandırma seçenekleri ayarlanmadı (Linux sanal makineleri için)

Yapılandırma dosyası (/etc/waagent.exe) waagent 'un eylemlerini denetler. Yapılandırma dosyası seçenekleri **uzantıları. Enable** , **y** ve sağlama olarak ayarlanmalıdır **.** yedeklemenin çalışması için aracının **Auto** olarak ayarlanması gerekir.
VM-Agent yapılandırma dosyası seçeneklerinin tam listesi için bkz. <https://github.com/Azure/WALinuxAgent#configuration-file-options>

### <a name="application-control-solution-is-blocking-iaasbcdrextensionexe"></a>Uygulama denetim çözümü IaaSBcdrExtension.exe engelliyor

[AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/what-is-applocker) (veya başka bir uygulama denetimi çözümü) çalıştırıyorsanız ve kurallar yayımcı veya yol tabanlı ise, **IaaSBcdrExtension.exe** yürütülebilir dosyasının çalıştırılmasını engelleyebilirler.

#### <a name="solution-to-this-issue"></a>Bu soruna çözüm

`/var/lib`Yolu veya **IaaSBcdrExtension.exe** yürütülebiliri AppLocker 'dan (veya diğer uygulama denetimi yazılımlarından hariç tutun.)

### <a name="the-snapshot-status-cant-be-retrieved-or-a-snapshot-cant-be-taken"></a><a name="the-snapshot-status-cannot-be-retrieved-or-a-snapshot-cannot-be-taken"></a>Anlık görüntü durumu alınamaz veya bir anlık görüntü alınamaz

VM yedeklemesi, temel alınan depolama hesabına anlık görüntü komutu vermeyi kullanır. Yedekleme, depolama hesabına erişimi bulunmadığından ya da anlık görüntü görevinin yürütülmesi geciktiğinden başarısız olabilir.

#### <a name="solution-for-this-issue"></a>Bu sorun için çözüm

Aşağıdaki koşullar anlık görüntü görevinin başarısız olmasına neden olabilir:

| Nedeni | Çözüm |
| --- | --- |
| VM, Uzak Masaüstü Protokolü (RDP) ' de kapatıldığından VM durumu yanlış bildirilir. | VM 'yi RDP 'de kapatırsanız, VM 'nin durumunun doğru olup olmadığını öğrenmek için portalı denetleyin. Doğru değilse, sanal makine panosundaki **kapatma** seçeneğini kullanarak portalda VM 'yi kapatın. |
| VM, DHCP 'den konak veya doku adresini alamıyor. | IaaS VM yedeklemesinin çalışması için konuğun içinde DHCP etkinleştirilmelidir. VM, DHCP yanıt 245 ' den ana bilgisayar veya doku adresini alamıyor, hiçbir uzantıyı indiremez veya çalıştıramıyorum. Statik bir özel IP gerekiyorsa, **Azure Portal** veya **PowerShell** aracılığıyla yapılandırmanız gerekır ve VM içindeki DHCP seçeneğinin etkin olduğundan emin olun. PowerShell ile statik IP adresi ayarlama hakkında [daha fazla bilgi edinin](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) .

### <a name="remove-lock-from-the-recovery-point-resource-group"></a><a name="remove_lock_from_the_recovery_point_resource_group"></a>Kurtarma noktası kaynak grubundan kilidi kaldır

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Tüm kaynaklar seçeneğine** gidin, aşağıdaki biçimdeki geri yükleme noktası koleksiyonu kaynak grubunu seçin AzureBackupRG_ `<Geo>` _ `<number>` .
3. **Ayarlar** bölümünde, kilitleri göstermek için **kilitler** ' ı seçin.
4. Kilidi kaldırmak için üç noktayı seçin ve **Sil**' i seçin.

    ![Kilidi Sil](./media/backup-azure-arm-vms-prepare/delete-lock.png)

### <a name="clean-up-restore-point-collection"></a><a name="clean_up_restore_point_collection"></a> Geri yükleme noktası koleksiyonunu temizle

Kilidi kaldırdıktan sonra geri yükleme noktalarının temizlenmesi gerekir.

VM 'nin veya VM 'nin kaynak grubunu silerseniz, yönetilen disklerin anlık geri yükleme anlık görüntüleri etkin kalır ve bekletme kümesine göre sona erer. Geri yükleme noktası koleksiyonunda depolanan anlık geri yükleme anlık görüntülerini silmek için (artık ihtiyacınız yoksa), aşağıda verilen adımlara göre geri yükleme noktası koleksiyonunu temizleyin.

Geri yükleme noktalarını temizlemek için yöntemlerden birini izleyin:<br>

- [İsteğe bağlı yedekleme çalıştırarak geri yükleme noktası koleksiyonunu Temizleme](#clean-up-restore-point-collection-by-running-on-demand-backup)<br>
- [Geri yükleme noktası koleksiyonunu Azure portal temizle](#clean-up-restore-point-collection-from-azure-portal)<br>

#### <a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a><a name="clean-up-restore-point-collection-by-running-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırarak geri yükleme noktası koleksiyonunu Temizleme

Kilidi kaldırdıktan sonra isteğe bağlı yedekleme tetikleyin. Bu eylem geri yükleme noktalarının otomatik olarak temizlendiğinden emin olur. Bu isteğe bağlı işlemin ilk kez başarısız olmasını bekler. Ancak, geri yükleme noktalarını el ile silmek yerine otomatik temizlemeyi sağlayacaktır. Temizleme işleminden sonra, bir sonraki zamanlanmış yedeklemeniz başarılı olmalıdır.

> [!NOTE]
> İsteğe bağlı yedeklemeyi tetiklemenin birkaç saati sonrasında otomatik temizleme gerçekleşecektir. Zamanlanmış yedeğiniz hala başarısız olursa, [burada](#clean-up-restore-point-collection-from-azure-portal)listelenen adımları kullanarak geri yükleme noktası koleksiyonunu el ile silmeyi deneyin.

#### <a name="clean-up-restore-point-collection-from-azure-portal-br"></a><a name="clean-up-restore-point-collection-from-azure-portal"></a>Geri yükleme noktası koleksiyonunu Azure portal temizle <br>

Kaynak grubundaki kilit nedeniyle temizlenmediği geri yükleme noktaları koleksiyonunu el ile temizlemek için aşağıdaki adımları deneyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. **Hub** menüsünde **tüm kaynaklar**' ı seçin, ardından `<Geo>` sanal makinenizin bulunduğu kaynak grubunu aşağıdaki biçimde AzureBackupRG_ _ ' i seçin `<number>` .

    ![Kaynak grubunu seçin](./media/backup-azure-arm-vms-prepare/resource-group.png)

3. Kaynak grubu ' nu seçin, **genel bakış** bölmesi görüntülenir.
4. Tüm gizli kaynakları görüntülemek için **gizli türleri göster** seçeneğini belirleyin. Aşağıdaki biçime sahip geri yükleme noktası koleksiyonlarını seçin AzureBackupRG_ `<VMName>` _ `<number>` .

    ![Geri yükleme noktası koleksiyonunu seçin](./media/backup-azure-arm-vms-prepare/restore-point-collection.png)

5. Geri yükleme noktası koleksiyonunu temizlemek için **Sil** ' i seçin.
6. Yedekleme işlemini yeniden deneyin.

> [!NOTE]
 >Kaynağın (RP koleksiyonu) çok sayıda geri yükleme noktası varsa, bunları portaldan silmek zaman aşımına uğrayabilir ve başarısız olabilir. Bu, tüm geri yükleme noktalarının, belirlenen sürede silinmediği ve işlemin zaman aşımına uğraydığı bilinen bir CRP sorunudur. Ancak, silme işlemi genellikle iki veya üç yeniden denemeden sonra başarılı olur.
