---
title: Azure VM 'Leri ile yedekleme hatalarını giderme
description: Bu makalede, Azure sanal makinelerini yedekleme ve geri yükleme ile karşılaşılan hatalarla ilgili sorunları nasıl giderebileceğiniz hakkında bilgi edinin.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 2d09081533cdb2de5ee97cb000e9844b41a85ac3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559375"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Azure sanal makinelerinde yedekleme hatalarının sorunlarını giderme

Aşağıda listelenen bilgilerle Azure Backup kullanırken karşılaşılan hatalarla ilgili sorunları çözebilirsiniz:

## <a name="backup"></a>Backup

Bu bölümde, Azure sanal makinesinin yedekleme işlemi hatası ele alınmaktadır.

### <a name="basic-troubleshooting"></a>Temel sorun giderme

* VM Aracısı 'nın (WA Aracısı) [en son sürüm](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)olduğundan emin olun.
* Windows veya Linux VM işletim sistemi sürümünün desteklendiğinden emin olun, [IaaS VM yedekleme desteği matrisine](./backup-support-matrix-iaas.md)bakın.
* Başka bir yedekleme hizmetinin çalışmadığını doğrulayın.
  * Anlık görüntü uzantısı sorunları olmadığından emin olmak için, [yeniden yüklemeyi zorlamak üzere uzantıları kaldırın ve sonra yedeklemeyi yeniden deneyin](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* VM 'nin internet bağlantısı olduğunu doğrulayın.
  * Başka bir yedekleme hizmetinin çalışmadığından emin olun.
* `Services.msc` **Windows Azure Konuk Aracısı** hizmetinin **çalıştığından** emin olun. **Windows Azure Konuk Aracısı** hizmeti eksikse, [bir kurtarma hizmetleri kasasındaki Azure VM 'lerinden yedekleme](./backup-azure-arm-vms-prepare.md#install-the-vm-agent)konumundan yüklemesi yapın.
* **Olay günlüğü** , diğer yedekleme ürünlerinden (örneğin, Windows Server Yedekleme) olan yedekleme başarısızlıklarını gösterebilir ve Azure Backup nedeni değildir. Sorunun Azure Backup olup olmadığını anlamak için aşağıdaki adımları kullanın:
  * Olay kaynağında veya iletisinde giriş **yedeğiyle** ilgili bir hata varsa, Azure ıAAS VM yedeklemesi yedeklerinin başarılı olup olmadığını ve istenen anlık görüntü türüyle bir geri yükleme noktası oluşturulup oluşturulmayacağını denetleyin.
  * Azure Backup çalışıyorsa, sorun büyük olasılıkla başka bir yedekleme çözümüyle birlikte olur.
  * Burada Azure Backup düzgün çalıştığı ancak "Windows Server Yedekleme" başarısız olduğu Olay Görüntüleyicisi hatası 517 bir örneğidir: ![ başarısız Windows Server yedekleme](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Azure Backup başarısız olursa, bu makaledeki ortak VM yedekleme hataları bölümünde karşılık gelen hata kodunu arayın.

## <a name="common-issues"></a>Genel sorunlar

Azure sanal makinelerinde yedekleme hatalarıyla ilgili yaygın sorunlar aşağıda verilmiştir.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>Vmrestorepointınternalerror-VM 'de yapılandırılan virüsten koruma, yedekleme uzantısının yürütülmesini kısıtlıyor

Hata kodu: Vmrestorepointınternalerror

Yedekleme sırasında **Olay Görüntüleyicisi uygulama günlüklerinde** **Hatalı uygulama adı** iletisi görüntülenir. IaaSBcdrExtension.exedaha sonra, VM 'de yapılandırılan virüsten koruma, yedekleme uzantısının yürütülmesini kısıtlamaktadır.
Bu sorunu çözmek için, virüsten koruma yapılandırmasındaki aşağıdaki dizinleri dışlayın ve yedekleme işlemini yeniden deneyin.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>CopyingVHDsFromBackUpVaultTakingLongTime-yedeklenen verileri kasadan kopyalama zaman aşımına uğradı

Hata kodu: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Hata iletisi: yedeklenen verileri kasadan kopyalama zaman aşımına uğradı

Bu durum, geçici depolama hatalarından veya yedekleme hizmeti için yetersiz depolama hesabı ıOPS 'nin zaman aşımı süresi içinde kasaya veri aktarmasına neden olmuş olabilir. Bu [en iyi yöntemleri](backup-azure-vms-introduction.md#best-practices) kullanarak VM yedeklemesini yapılandırın ve yedekleme işlemini yeniden deneyin.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState - VM yedeklemeye izin veren bir durumda değil

Hata kodu: Usererrorvmnotındesıralaması <br/>
Hata iletisi: VM, yedeklemelere izin veren bir durumda değil.<br/>

VM başarısız durumda olduğu için yedekleme işlemi başarısız oldu. Başarılı bir yedekleme için sanal makinenin durumu Çalışıyor, Durduruldu veya Durduruldu (serbest bırakıldı) olmalıdır.

* VM, **çalıştırma** ve **kapatma** arasında geçici bir durumdaysa, durumun değiştirilmesini bekleyin. Ardından yedekleme işini tetikleyin.
* VM bir Linux sanal makinesi ise ve Security-Enhanced Linux çekirdek modülünü kullanıyorsa, Azure Linux Aracısı yolu **/var/lib/waagent** öğesini güvenlik ilkesinden dışlayın ve yedekleme uzantısının yüklü olduğundan emin olun.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed-dosya sistemiyle tutarlı bir anlık görüntü almak için VM 'nin bir veya daha fazla bağlama noktası dondurulamadı

Hata kodu: UserErrorFsFreezeFailed <br/>
Hata iletisi: dosya sistemiyle tutarlı bir anlık görüntü almak için VM 'nin bir veya daha fazla bağlama noktası dondurulamıyor.

* Dosya sistemi durumunun temizlenmediği cihazların, **umorekıt** komutunu kullanarak bağlantısını çıkarın.
* **Fsck** komutunu kullanarak bu cihazlarda bir dosya sistemi tutarlılık denetimi çalıştırın.
* Cihazları yeniden bağlayın ve yedekleme işlemini yeniden deneyin.</ol>

Cihazların atamasını kaldırmak için, VM yedekleme yapılandırmasını belirli bağlama noktalarını yoksayacak şekilde güncelleştirebilirsiniz. Örneğin, '/mnt/Resource ' bağlama noktası bağlanamaz ve VM yedekleme hatalarının olmasına neden oluyorsa, VM yedekleme yapılandırma dosyalarını ```MountsToSkip``` özelliği ile aşağıdaki şekilde güncelleştirebilirsiniz.

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```


### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/Extensionınstalssiledcom/Extensionınstalssiledmdtc-Uzantı yükleme/işlem bir COM+ hatası nedeniyle başarısız oldu

Hata kodu: ExtensionSnapshotFailedCOM <br/>
Hata iletisi: COM+ hatası nedeniyle anlık görüntü işlemi başarısız oldu

Hata kodu: Extensionınstalssiledcom  <br/>
Hata iletisi: bir COM+ hatası nedeniyle Uzantı yükleme/işlem başarısız oldu

Hata kodu: Extensionınstalssiledmdtc <br/>
Hata iletisi: uzantı yüklemesi, "COM+ Microsoft Dağıtılmış İşlem Düzenleyicisi konuşamadı hatasıyla başarısız oldu <br/>

Windows hizmeti **com+ sistem** uygulamasındaki bir sorun nedeniyle yedekleme işlemi başarısız oldu.  Bu sorunu çözmek için şu adımları izleyin:

* Windows hizmeti **com+ sistem uygulamasını** başlatmayı/yeniden başlatmayı deneyin (yükseltilmiş bir komut isteminden **-net start COMSysApp**).
* **Dağıtılmış işlem Düzenleyicisi** hizmetinin **ağ hizmeti** hesabı olarak çalıştığından emin olun. Aksi takdirde, **ağ hizmeti** hesabı olarak Çalıştır ' ı değiştirip **com+ sistem uygulamasını** yeniden başlatın.
* Hizmeti yeniden başlatmazsanız, aşağıdaki adımları izleyerek **Dağıtılmış işlem Düzenleyicisi** hizmeti 'ni yeniden yükleyin:
  * MSDTC hizmetini durdurun
  * Komut istemini (cmd) açın
  * `msdtc -uninstall` komutunu çalıştırın
  * `msdtc -install` komutunu çalıştırın
  * MSDTC hizmetini başlatın
* Windows hizmeti **com+ sistem uygulamasını** başlatın. **Com+ sistem uygulaması** başladıktan sonra Azure Portal bir yedekleme işi tetikleyin.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState - Anlık görüntü işlemi VSS yazıcıları hatalı durumda olduğu için başarısız oldu

Hata kodu: Extensionfailedvsswriterınbadstate <br/>
Hata iletisi: VSS yazarları hatalı durumda olduğundan anlık görüntü işlemi başarısız oldu.

VSS yazıcılarının hatalı durumda olması nedeniyle bu hata oluşur. Azure Backup uzantıları, disklerin anlık görüntülerini almak için VSS yazıcılarında etkileşim kurar. Bu sorunu çözmek için şu adımları izleyin:

1\. Adım: Hatalı durumda olan VSS yazıcılarını yeniden başlatın.

* Yükseltilmiş bir komut isteminden komutunu çalıştırın ```vssadmin list writers``` .
* Çıkış, tüm VSS yazıcılarını ve onların durumunu içerir. Durumu **[1] Kararlı** olmayan her VSS yazıcısı için, ilgili VSS yazıcısının hizmetini yeniden başlatın.
* Hizmeti yeniden başlatmak için yükseltilmiş komut isteminden aşağıdaki komutları çalıştırın: 

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Bazı hizmetlerin yeniden başlatılmasına yönelik üretim ortamınız üzerinde bir etkisi olabilir. Onay işleminin izlendiğinden ve hizmetin zamanlanan kesinti sırasında yeniden başlatıldığından emin olun.

2. Adım: VSS yazıcılarını yeniden başlatmak sorunu çözmezse, iş parçacıklarının blob anlık görüntüleri için oluşturulmasını engellemek üzere yükseltilmiş bir komut isteminden (yönetici olarak) aşağıdaki komutu çalıştırın.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

3. Adım: 1 ve 2. adımlar sorunu çözmezse, bu, sınırlı ıOPS nedeniyle VSS yazıcılarının zaman aşımına uğramasından kaynaklanıyor olabilir.<br>

Doğrulamak için, ***sistem ve Olay Görüntüleyicisi uygulama günlükleri*** ' ne gidin ve aşağıdaki hata iletisini kontrol edin:<br>
*Gölge kopya sağlayıcısı, gölge kopya oluşturulan birime yazma işlemlerini tutarken zaman aşımına uğradı. Bu, büyük olasılıkla bir uygulama veya sistem hizmeti tarafından birimdeki aşırı etkinlikten kaynaklanıyor olabilir. Birimdeki etkinlik azaltıldığında daha sonra yeniden deneyin.*<br>

Çözüm:

* Yükü VM disklerinde dağıtmak için olanaklar olup olmadığını denetleyin. Bu, tek disklerdeki yükü azaltır. [Depolama düzeyinde tanılama ölçümlerini etkinleştirerek IOPS azaltmasını kontrol](/troubleshoot/azure/virtual-machines/performance-diagnostics#install-and-run-performance-diagnostics-on-your-vm)edebilirsiniz.
* VM üzerindeki yük en düşük düzeyde olduğunda, yedekleme ilkesini yoğun saatlerde yedeklemeler gerçekleştirmek üzere değiştirin.
* Azure disklerini, daha yüksek IOPS 'yi destekleyecek şekilde yükseltin. [Daha fazla bilgi edinin](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState - VSS (Birim Gölge Kopyası) hizmeti hatalı durumda olduğundan anlık görüntü işlemi başarısız oldu

Hata kodu: Extensionfailedvssserviceınbadstate <br/>
Hata iletisi: VSS (birim gölge kopyası) hizmeti hatalı durumda olduğundan anlık görüntü işlemi başarısız oldu.

VSS hizmeti hatalı durumda olduğundan bu hata oluşur. Azure Backup uzantıları, disklerin anlık görüntülerini almak için VSS hizmetiyle etkileşime geçer. Bu sorunu çözmek için şu adımları izleyin:

VSS (Birim Gölge Kopyası) hizmetini yeniden başlatın.

* Services.msc'ye gidin ve 'Birim Gölge Kopyası hizmetini' yeniden başlatın.<br>
(veya)<br>
* Yükseltilmiş bir komut isteminden aşağıdaki komutları çalıştırın:

 ```net stop VSS``` <br>
 ```net start VSS```

Sorun devam ederse zamanlanmış kapalı kalma süresi içinde VM'yi yeniden başlatın.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable-VM boyutu seçili olmadığından VM oluşturma başarısız oldu

Hata kodu: UserErrorSkuNotAvailable hata iletisi: seçili VM boyutu kullanılabilir olmadığından VM oluşturma başarısız oldu.

Bu hata, geri yükleme işlemi sırasında seçilen VM boyutu desteklenmeyen bir boyut olduğu için oluşur. <br>

Bu sorunu çözmek için geri yükleme işlemi sırasında [diskleri geri yükle](./backup-azure-arm-restore-vms.md#restore-disks) seçeneğini kullanın. [PowerShell cmdlet 'lerini](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks)kullanarak [kullanılabilir desteklenen VM BOYUTLARı](./backup-support-matrix-iaas.md#vm-compute-support) listesinden bir VM oluşturmak için bu diskleri kullanın.

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported-VM oluşturma, Pazar yeri satın alma isteği olmadığı için başarısız oldu

Hata kodu: UserErrorMarketPlaceVMNotSupported hata iletisi: Pazar yeri satın alma isteği mevcut olmadığından VM oluşturma başarısız oldu.

Azure Backup, Azure Marketi 'nde bulunan VM 'lerin yedeklenmesini ve geri yüklenmesini destekler. Bu hata, Azure Marketi 'nde artık kullanılamayan bir VM 'yi (belirli bir plan/yayımcı ayarı ile) geri yüklemeye çalıştığınızda oluşur. [daha fazla bilgi edinin](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Bu sorunu çözmek için geri yükleme işlemi sırasında [diskleri geri yükle](./backup-azure-arm-restore-vms.md#restore-disks) seçeneğini kullanın ve ardından sanal makineye karşılık gelen en son Market BILGILERINI kullanarak VM oluşturmak için [POWERSHELL](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) veya [Azure CLI](./tutorial-restore-disk.md) cmdlet 'lerini kullanın.
* Yayımcının Market bilgisi yoksa, verilerinizi almak için veri disklerini kullanabilir ve bunları mevcut bir VM 'ye ekleyebilirsiniz.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure-yedekleme uzantısı için yapılandırma ayrıştırılırken hata oluştu

Hata kodu: ExtensionConfigParsingFailure<br/>
Hata iletisi: yedekleme uzantısı için yapılandırma ayrıştırılırken hata oluştu.

Bu hata, **MachineKeys** dizininde değiştirilen izinler nedeniyle oluşur: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Aşağıdaki komutu çalıştırın ve **MachineKeys** dizinindeki izinlerin varsayılan olanları doğrulayın: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

Varsayılan izinler aşağıdaki gibidir:

* Herkes: (R, W)
* BUILTIN\Administrators: (F)

**MachineKeys** dizininde varsayılandan farklı izinler görürseniz, izinleri düzeltmek, sertifikayı silmek ve yedeklemeyi tetiklemek için aşağıdaki adımları izleyin:

1. **MachineKeys** dizinindeki izinleri onarın. Dizindeki gezgin güvenlik özelliklerini ve gelişmiş güvenlik ayarlarını kullanarak, izinleri varsayılan değerlere sıfırlayın. Dizinden varsayılanlar dışında tüm kullanıcı nesnelerini kaldırın ve **Herkes** izninin şu şekilde özel erişime sahip olduğundan emin olun:

   * Klasörü listeleme/verileri okuma
   * Öznitelikleri oku
   * Genişletilmiş öznitelikleri oku
   * Dosya oluştur/veri yaz
   * Klasör oluştur/veri Ekle
   * Yazma öznitelikleri
   * Genişletilmiş öznitelikleri Yaz
   * Okuma izinleri
2. ' A **verilen** tüm sertifikaları, klasik dağıtım modeli veya **Microsoft Azure CRP sertifika Oluşturucu** olarak silin:

   * [Sertifikaları yerel bir bilgisayar konsolunda açın](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * **Kişisel**  >  **Sertifikalar**' ın altında, ' a **verilen** tüm sertifikaları, klasik dağıtım modeli veya **Microsoft Azure CRP sertifika Oluşturucu** olarak silin.
3. Bir VM yedekleme işi tetikleyin.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-uzantı durumu yedekleme işlemi için destekmedi

Hata kodu: ExtensionStuckInDeletionState <br/>
Hata iletisi: uzantı durumu yedekleme işlemine destekmedi

Yedekleme uzantısının tutarsız durumu nedeniyle yedekleme işlemi başarısız oldu. Bu sorunu çözmek için şu adımları izleyin:

* Konuk Aracı'nın yüklü olduğundan ve yanıt verdiğinden emin olun
* Azure Portal, **sanal makine**  >  **Tüm ayarlar**  >  **uzantıları** ' na git
* VmSnapshot veya VmSnapshotLinux yedekleme uzantısını seçin ve **Kaldır**' ı seçin.
* Yedekleme uzantısını sildikten sonra yedekleme işlemini yeniden deneyin
* Sonraki yedekleme işleminde yeni uzantı istenen durumda yüklenecektir

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-anlık görüntü işlemi, ekli disklerden bazıları için anlık görüntü sınırı aşıldığından başarısız oldu

Hata kodu: ExtensionFailedSnapshotLimitReachedError  <br/>
Hata iletisi: eklenen bazı diskler için anlık görüntü sınırı aşıldığından anlık görüntü işlemi başarısız oldu

Anlık görüntü sınırı, eklenen bazı diskler için aşıldığı için başarısız oldu. Aşağıdaki sorun giderme adımlarını tamamlayıp işlemi yeniden deneyin.

* Disk blobu silme-gerekli olmayan anlık görüntüler. Disk bloblarını silmemeye dikkat edin. Yalnızca anlık görüntü Blobları silinmelidir.
* VM disk depolaması hesaplarında geçici silme etkinse, mevcut anlık görüntülerin herhangi bir zamanda izin verilen en yüksek sayıdan küçük olması için geçici silme bekletmesini yapılandırın.
* Azure Site Recovery, yedeklenen VM 'de etkinleştirilmişse aşağıdaki adımları gerçekleştirin:

  * **İsanysnapshotfailed** değerinin/etc/Azure/vmbackup.conf içinde false olarak ayarlandığından emin olun
  * Farklı bir zamanda Azure Site Recovery zamanlayın, bu nedenle yedekleme işlemini çakışmaz.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>Extensionfailedtimeoutvmnetworktimeout-yetersiz VM kaynakları nedeniyle anlık görüntü işlemi başarısız oldu

Hata kodu: Extensionfailedtimeoutvmnetworkyanıt vermiyor<br/>
Hata iletisi: yetersiz VM kaynakları nedeniyle anlık görüntü işlemi başarısız oldu.

Anlık görüntü işlemi gerçekleştirilirken ağ çağrılarında gecikme nedeniyle VM 'deki yedekleme işlemi başarısız oldu. Bu sorunu çözmek için 1. adımı uygulayın. Sorun devam ederse 2. ve 3. adımları deneyin.

**1. adım**: Ana bilgisayar aracılığıyla anlık görüntü oluşturma

Yükseltilmiş (yönetici) komut isteminden aşağıdaki komutu çalıştırın:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Bu, anlık görüntünün Konuk yerine konak üzerinden alınmasını sağlar. Yedekleme işlemini yeniden deneyin.

**2. adım**: Yedekleme zamanlamasını VM 'nin daha az yük altında olduğu bir saate değiştirmeyi deneyin (daha az CPU veya IOPS gibi)

**3. adım**: [VM 'nin boyutunu artırmayı](../virtual-machines/windows/resize-vm.md) deneyin ve işlemi yeniden deneyin

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound-VM artık mevcut olmadığından işlem gerçekleştirilemedi/400094, BCMV2VMNotFound-sanal makine yok/bir Azure sanal makinesi bulunamadı

Hata kodu: 320001, ResourceNotFound <br/> Hata iletisi: VM artık mevcut olmadığından işlem gerçekleştirilemedi. <br/> <br/> Hata kodu: 400094, BCMV2VMNotFound <br/> Hata iletisi: sanal makine yok <br/>
Azure sanal makinesi bulunamadı.

Birincil VM silindiğinde bu hata oluşur, ancak yedekleme ilkesi hala bir VM 'yi yedekleyecek şekilde arar. Bu hatayı onarmak için aşağıdaki adımları uygulayın:

* Aynı ada ve aynı kaynak grubu adına sahip sanal makineyi yeniden oluşturun, **bulut hizmeti adı**,<br>veya
* Yedekleme verilerini silmeden sanal makineyi korumayı durdurun. Daha fazla bilgi için bkz. [sanal makineleri korumayı durdurma](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>Usererrorbcmpremıumstoragequotaerror-depolama hesabında yeterli boş alan olmadığından sanal makinenin anlık görüntüsü kopyalanamadı

Hata kodu: Usererrorbcmpremıumstoragequotaerror<br/> Hata iletisi: depolama hesabında yeterli boş alan olmadığından sanal makinenin anlık görüntüsü kopyalanamadı

 VM yedekleme yığını v1 'deki Premium VM 'Ler için, anlık görüntüyü depolama hesabına kopyalayacağız. Bu adım, anlık görüntüde kullanılan yedekleme yönetimi trafiğinin Premium diskler kullanılarak uygulama için kullanılabilir ıOPS sayısını sınırlandırmaz olmasını sağlar. <br><br>Toplam depolama hesabı alanını yalnızca yüzde 50, 17,5 TB olarak ayırmanız önerilir. Ardından Azure Backup hizmeti, anlık görüntüyü depolama hesabına kopyalayabilir ve depolama hesabındaki bu kopyalanmış konumdan kasaya veri aktarabilir.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline-sanal makine çalışmadığı için Microsoft Kurtarma Hizmetleri Uzantısı yüklenemedi

Hata kodu: 380008, AzureVmOffline <br/> Hata iletisi: sanal makine çalışmadığı için Microsoft Kurtarma Hizmetleri Uzantısı yüklenemedi

VM Aracısı, Azure Kurtarma Hizmetleri uzantısı için bir önkoşuldur. Azure sanal makine aracısını yükleyip kayıt işlemini yeniden başlatın. <br> <ol> <li>VM aracısının doğru yüklenip yüklenmediğini denetleyin. <li>VM yapılandırması üzerindeki bayrağın doğru ayarlandığından emin olun.</ol> VM aracısını yükleme ve VM Aracısı yüklemesinin nasıl doğrulanacağı hakkında daha fazla bilgi edinin.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError-anlık görüntü işlemi Birim Gölge Kopyası Hizmeti (VSS) işlem hatasıyla başarısız oldu

Hata kodu: ExtensionSnapshotBitlockerError <br/> Hata iletisi: anlık görüntü işlemi Birim Gölge Kopyası Hizmeti (VSS) işlem hatasıyla başarısız oldu, **Bu sürücü BitLocker Sürücü Şifrelemesi tarafından kilitlenmiş. Bu sürücünün kilidini, Denetim Masası 'ndan açmanız gerekir.**

VM 'deki tüm sürücüler için BitLocker 'ı kapatın ve VSS sorununun çözümlenip çözümlenmediğini denetleyin.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>Vmnotındesıralaması-VM, yedeklemelere izin veren bir durumda değil

Hata kodu: Vmnotındesıralaması <br/> Hata iletisi: VM, yedeklemelere izin veren bir durumda değil.

* VM, **çalıştırma** ve **kapatma** arasında geçici bir durumdaysa, durumun değiştirilmesini bekleyin. Ardından yedekleme işini tetikleyin.
* VM bir Linux sanal makinesi ise ve Security-Enhanced Linux çekirdek modülünü kullanıyorsa, Azure Linux Aracısı yolu **/var/lib/waagent** öğesini güvenlik ilkesinden dışlayın ve yedekleme uzantısının yüklü olduğundan emin olun.

* VM Aracısı sanal makinede yok: <br>Herhangi bir önkoşulu ve VM aracısını yükler. Sonra işlemi yeniden başlatın. | [VM Aracısı yüklemesi ve VM Aracısı yüklemesinin nasıl doğrulanacağı](#vm-agent)hakkında daha fazla bilgi edinin.

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork-bir güvenli ağ iletişim kanalı oluşturma hatası nedeniyle anlık görüntü işlemi başarısız oldu

Hata kodu: ExtensionSnapshotFailedNoSecureNetwork <br/> Hata iletisi: güvenli ağ iletişim kanalı oluşturma hatası nedeniyle anlık görüntü işlemi başarısız oldu.

* **regedit.exe** , yükseltilmiş modda çalıştırarak kayıt defteri düzenleyicisini açın.
* Sisteminizde mevcut olan tüm .NET Framework sürümlerini belirler. Bunlar **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft** kayıt defteri anahtarı hiyerarşisi altında mevcuttur.
* Kayıt defteri anahtarında bulunan her bir .NET Framework için aşağıdaki anahtarı ekleyin: <br> **Schusestrongşifre "= DWORD: 00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure-anlık görüntü işlemi başarısız oldu Visual Studio için Visual C++ Yeniden Dağıtılabilir 2012

Hata kodu: ExtensionVCRedistInstallationFailure <br/> Hata iletisi: 2012 Visual Studio için Visual C++ Yeniden Dağıtılabilir yüklenemediğinden anlık görüntü işlemi başarısız oldu.

* Vcredist2013_x64 gidin `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` ve bu yüklemeye erişin.<br/>Hizmet yüklemeye izin veren kayıt defteri anahtarı değerinin doğru değere ayarlandığından emin olun. Diğer bir deyişle, **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** **Başlangıç** değerini **4** değil **3** olarak ayarlayın. <br><br>Yükleme ile ilgili sorun yaşıyorsanız, **msiexec/Unregister** ' yi ve ardından yükseltilmiş bir komut isteminden **msiexec/Register** ' i çalıştırarak yükleme hizmetini yeniden başlatın.
* İlgili sorunlar yaşıyorsanız emin olup olmadığınızı doğrulamak için olay günlüğünü kontrol edin. Örneğin: *Ürün: Microsoft Visual C++ 2013 x64 en düşük çalışma zamanı-12.0.21005--hata 1401. Anahtar oluşturulamadı: Software\Classes.  Sistem hatası 5.  Bu anahtara yeterli erişiminizin olduğunu doğrulayın veya destek personelinize başvurun.* <br><br> Yönetici veya Kullanıcı hesabının **HKEY_LOCAL_MACHINE\SOFTWARE\Classes** kayıt defteri anahtarını güncelleştirmek için yeterli izinlere sahip olduğundan emin olun. Yeterli izinleri sağlayın ve Windows Azure Konuk Aracısı 'nı yeniden başlatın.<br><br> <li> Virüsten koruma ürünleri varsa, yüklemeye izin vermek için doğru dışlama kurallarına sahip olduklarından emin olun.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy - Sanal makinede Anlık Görüntü işlemini engelleyen geçersiz bir ilke yapılandırıldı

Hata kodu: UserErrorRequestDisallowedByPolicy <BR> Hata iletisi: VM 'de anlık görüntü işlemini önleyecek geçersiz bir ilke yapılandırıldı.

[Ortamınızdaki etiketleri yöneten](../governance/policy/tutorials/govern-tags.md)bir Azure ilkeniz varsa, Ilkeyi bir [reddetme etkisine](../governance/policy/concepts/effects.md#deny) değiştirme [efektiyle](../governance/policy/concepts/effects.md#modify)değiştirmeyi düşünün veya [Azure Backup için gereken adlandırma şemasına](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)göre kaynak grubunu el ile oluşturun.

## <a name="jobs"></a>İşler

| Hata ayrıntıları | Geçici çözüm |
| --- | --- |
| Bu iş türü için iptal desteklenmez: <br>İş bitene kadar bekleyin. |Yok |
| İş iptal edilebilen durumunda değil: <br>İş bitene kadar bekleyin. <br>**veya**<br> Seçilen iş iptal edilebilen bir durumda değil: <br>İşin bitmesini bekleyin. |İşin neredeyse tamamlanmış olması olasıdır. İş tamamlanana kadar bekleyin.|
| Yedekleme, devam ettiğinden işi iptal edemiyor: <br>İptal etme işlemi yalnızca devam eden işler için desteklenir. Devam eden bir işi iptal etmeyi deneyin. |Bu hata, geçici bir durum nedeniyle oluşur. Bir dakika bekleyip iptal işlemini yeniden deneyin. |
| Yedekleme işi iptal edemedi: <br>İş bitene kadar bekleyin. |Yok |

## <a name="restore"></a>Geri Yükleme

### <a name="disks-appear-offline-after-file-restore"></a>Dosya geri yüklemeden sonra diskler çevrimdışı görünüyor

Geri yükleme sonrasında, disklerin çevrimdışı olduğunu fark edersiniz:

* Betiğin yürütüldüğü makinenin işletim sistemi gereksinimlerini karşıladığını doğrulayın. [Daha fazla bilgi edinin](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Aynı kaynağa geri yüklemediğinizden emin olun, [daha fazla bilgi edinin](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>Usererrorınstantrpnotfound-VM 'nin anlık görüntüsü bulunamadığı için geri yükleme başarısız oldu

Hata kodu: Usererrorınstantrpnotfound <br>
Hata iletisi: VM 'nin anlık görüntüsü bulunamadığı için geri yükleme başarısız oldu. Anlık görüntü silinmiş olabilir, lütfen kontrol edin.<br>

Bu hata, kasaya aktarılmayan ve anlık görüntü aşamasında silinen bir kurtarma noktasından geri yükleme yapmaya çalıştığınızda oluşur. 
<br>
Bu sorunu çözmek için, VM 'yi farklı bir geri yükleme noktasından geri yüklemeyi deneyin.<br>

#### <a name="common-errors"></a>Sık karşılaşılan hatalar 
| Hata ayrıntıları | Geçici çözüm |
| --- | --- |
| Geri yükleme, bir bulut iç hatasıyla başarısız oldu. |<ol><li>Geri yüklemeye çalıştığınız bulut hizmeti DNS ayarları ile yapılandırılmış. Şunları kontrol edebilirsiniz: <br>**$Deployment = Get-AzureDeployment-ServiceName "HizmetAdı"-yuva "üretim" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>**Adres** yapılandırıldıysa DNS ayarları yapılandırılır.<br> <li>Geri yüklemeye çalıştığınız bulut hizmeti **ReservedIP** ile yapılandırılmış ve bulut hizmetindeki mevcut VM 'ler durdurulmuş durumda. Aşağıdaki PowerShell cmdlet 'lerini kullanarak bir bulut hizmeti 'nin bir IP 'yi ayırmış olduğunu kontrol edebilirsiniz: **$Deployment = Get-AzureDeployment-ServiceName "HizmetAdı"-slot "üretim" $DEP. Rezervedipname**. <br><li>Aşağıdaki özel ağ yapılandırmalarına sahip bir sanal makineyi aynı bulut hizmetine geri yüklemeye çalışıyorsunuz: <ul><li>Yük dengeleyici yapılandırması, iç ve dış kapsamındaki sanal makineler.<li>Birden çok ayrılmış IP 'ye sahip sanal makineler. <li>Birden çok NIC içeren sanal makineler. </ul><li>Kullanıcı arabiriminde yeni bir bulut hizmeti seçin veya özel ağ yapılandırmalarına sahip VM 'Ler için [geri yükleme konularına](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) bakın.</ol> |
| Seçilen DNS adı zaten alınmış: <br>Farklı bir DNS adı belirtip yeniden deneyin. |Bu DNS adı, genellikle **. cloudapp.net** ile biten bulut hizmeti adına başvurur. Bu adın benzersiz olması gerekir. Bu hatayı alırsanız geri yükleme sırasında farklı bir VM adı seçmeniz gerekir. <br><br> Bu hata yalnızca Azure portal kullanıcılarına gösterilir. PowerShell aracılığıyla geri yükleme işlemi, yalnızca diskleri geri yüklediği ve VM 'yi oluşturmadığından başarılı olur. VM, disk geri yükleme işleminden sonra sizin tarafınızdan açıkça oluşturulduğunda, hata alınacaktır. |
| Belirtilen sanal ağ yapılandırması doğru değil: <br>Farklı bir sanal ağ yapılandırması belirtip yeniden deneyin. |Yok |
| Belirtilen bulut hizmeti, geri yüklenmekte olan sanal makine yapılandırmasıyla eşleşmeyen bir ayrılmış IP kullanıyor: <br>Ayrılmış IP kullanmayan farklı bir bulut hizmeti belirtin. Ya da geri yüklemek için başka bir kurtarma noktası seçin. |Yok |
| Bulut hizmeti, giriş uç noktası sayısının sınırına ulaştı: <br>Farklı bir bulut hizmeti belirterek veya var olan bir uç noktayı kullanarak işlemi yeniden deneyin. |Yok |
| Kurtarma Hizmetleri kasası ve hedef depolama hesabı iki farklı bölgede bulunur: <br>Geri yükleme işleminde belirtilen depolama hesabının, kurtarma hizmetleri kasasıyla aynı Azure bölgesinde olduğundan emin olun. |Yok |
| Geri yükleme işlemi için belirtilen depolama hesabı desteklenmiyor: <br>Yalnızca yerel olarak yedekli veya coğrafi olarak yedekli çoğaltma ayarlarına sahip temel veya standart depolama hesapları desteklenir. Desteklenen bir depolama hesabı seçin. |Yok |
| Geri yükleme işlemi için belirtilen depolama hesabı türü çevrimiçi değil: <br>Geri yükleme işleminde belirtilen depolama hesabının çevrimiçi olduğundan emin olun. |Bu hata, Azure depolama 'daki geçici bir hatadan veya bir kesinti nedeniyle oluşabilir. Başka bir depolama hesabı seçin. |
| Kaynak grubu kotasına ulaşıldı: <br>Azure portal bazı kaynak gruplarını silin veya limitleri artırmak için Azure desteğine başvurun. |Yok |
| Seçilen alt ağ yok: <br>Var olan bir alt ağ seçin. |Yok |
| Yedekleme hizmetinin aboneliğinizdeki kaynaklara erişme yetkisi yok. |Bu hatayı çözmek için, önce [yedeklenen diskleri geri yükleme](backup-azure-arm-restore-vms.md#restore-disks)bölümündeki adımları kullanarak diskleri geri yükleyin. Ardından, [geri yüklenen disklerden BIR VM oluşturma](backup-azure-vms-automation.md#restore-an-azure-vm)içindeki PowerShell adımlarını kullanın. |

## <a name="backup-or-restore-takes-time"></a>Yedekleme veya geri yükleme zaman alır

Yedeklemeniz 12 saatten fazla sürerse veya geri yükleme 6 saatten fazla sürerse, [en iyi uygulamaları](backup-azure-vms-introduction.md#best-practices)gözden geçirin ve [performans değerlendirmeleri](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM Aracısı

### <a name="set-up-the-vm-agent"></a>VM aracısını ayarlama

Genellikle, VM Aracısı Azure galerisinden oluşturulan VM 'lerde zaten mevcuttur. Ancak şirket içi veri merkezlerinden geçirilen sanal makinelerin VM Aracısı yüklü olmayacaktır. Bu VM 'Ler için VM aracısının açıkça yüklenmesi gerekir.

#### <a name="windows-vms---set-up-the-agent"></a>Windows VM 'Leri-aracıyı ayarlama

* [Aracı MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) dosyasını indirip yükleyin. Yüklemeyi bitirebilmeniz için yönetici ayrıcalıklarına sahip olmanız gerekir.
* Klasik dağıtım modeli kullanılarak oluşturulan sanal makineler için, aracının yüklü olduğunu belirtmek üzere [VM özelliğini güncelleştirin](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) . Bu adım Azure Resource Manager sanal makineler için gerekli değildir.

#### <a name="linux-vms---set-up-the-agent"></a>Linux VM 'Leri-aracıyı ayarlama

* Dağıtım deposundan aracının en son sürümünü yükler. Paket adı hakkında daha fazla bilgi için bkz. [Linux aracı deposu](https://github.com/Azure/WALinuxAgent).
* Klasik dağıtım modeli kullanılarak oluşturulan VM 'Ler için [VM özelliğini güncelleştirin](/troubleshoot/azure/virtual-machines/install-vm-agent-offline#use-the-provisionguestagent-property-for-classic-vms) ve aracının yüklü olduğunu doğrulayın. Bu adım Kaynak Yöneticisi sanal makineler için gerekli değildir.

### <a name="update-the-vm-agent"></a>VM aracısını güncelleştirme

#### <a name="windows-vms---update-the-agent"></a>Windows VM 'Leri-aracıyı güncelleştirme

* VM aracısını güncelleştirmek için [VM Aracısı ikili dosyalarını](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)yeniden yükleyin. Aracıyı güncelleştirmeden önce, VM Aracısı güncelleştirmesi sırasında yedekleme işlemlerinin gerçekleşmeyecek olduğundan emin olun.

#### <a name="linux-vms---update-the-agent"></a>Linux VM 'Leri-aracıyı güncelleştirme

* Linux VM aracısını güncelleştirmek için, [LINUX VM aracısını güncelleştirme](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json)makalesindeki yönergeleri izleyin.

    > [!NOTE]
    > Aracıyı güncelleştirmek için her zaman dağıtım deposunu kullanın.

    Aracı kodunu GitHub 'dan indirmeyin. Dağıtım için en son aracı kullanılamazsa, en son aracıyı alma yönergeleri için dağıtım desteğiyle iletişim kurun. GitHub deposundaki en son [Windows Azure Linux Aracısı](https://github.com/Azure/WALinuxAgent/releases) bilgilerini de denetleyebilirsiniz.

### <a name="validate-vm-agent-installation"></a>VM Aracısı yüklemesini doğrula

Windows VM 'lerinde VM Aracısı sürümünü doğrulama:

1. Azure sanal makinesinde oturum açın ve **C:\windowsazure\packages** klasörüne gidin. **WaAppAgent.exe** dosyasını bulmanız gerekir.
2. Dosyaya sağ tıklayın ve **Özellikler**' e gidin. Ardından **Ayrıntılar** sekmesini seçin. **Ürün sürümü** alanı 2.6.1198.718 veya üzeri olmalıdır.

## <a name="troubleshoot-vm-snapshot-issues"></a>VM anlık görüntüsü sorunlarını giderme

VM yedeklemesi, temel depolama alanına anlık görüntü komutları vermeyi kullanır. Bir anlık görüntü görevindeki depolama veya gecikmelerin erişimine sahip olmaması yedekleme işinin başarısız olmasına neden olabilir. Aşağıdaki koşullar anlık görüntü görevi hatasına neden olabilir:

* **SQL Server yedeğine sahip VM 'ler anlık görüntü görevi gecikmesine neden olabilir**. Varsayılan olarak, VM yedekleme Windows VM 'lerde bir VSS tam yedekleme oluşturur. SQL Server yedekleme yapılandırılmış SQL Server çalıştıran VM 'Ler anlık görüntü gecikmelerine neden olabilir. Anlık görüntü gecikmeleri yedekleme hatalarıyla karşılaşırsanız, aşağıdaki kayıt defteri anahtarını ayarlayın:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **VM, RDP 'de kapatıldığından, VM durumu yanlış bildirilir**. Sanal makineyi kapatmak için Uzak Masaüstü 'nü kullandıysanız, portaldaki VM durumunun doğru olduğundan emin olun. Durum doğru değilse, VM 'yi kapatmak için Portal VM panosundaki **kapatma** seçeneğini kullanın.
* **Aynı bulut hizmetini dörtten fazla VM paylaşıyorsa, VM 'leri birden çok yedekleme ilkesine yayın**. Yedekleme sürelerini şaşırtan, aynı anda dört taneden fazla VM yedeklemesi başlamamasını sağlayın. İlkelerdeki başlangıç zamanlarını en az bir saat ayırarak ayırmaya çalışın.
* **VM yüksek CPU veya bellek üzerinde çalışır**. Sanal makine yüksek bellek veya CPU kullanımı yüzde 90 ' den fazla çalışıyorsa, anlık görüntü göreviniz sıraya alınır ve gecikir. Sonuç olarak zaman aşımına uğrar. Bu sorun oluşursa, isteğe bağlı yedekleme yapmayı deneyin.

## <a name="networking"></a>Ağ

IaaS VM yedeklemesinin çalışması için, DHCP 'nin Konuk içinde etkin olması gerekir. Statik bir özel IP gerekiyorsa, Azure portal veya PowerShell aracılığıyla yapılandırın. VM içindeki DHCP seçeneğinin etkinleştirildiğinden emin olun.
PowerShell aracılığıyla statik IP ayarlama hakkında daha fazla bilgi alın:

* [Var olan bir VM 'ye statik bir iç IP ekleme](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Bir ağ arabirimine atanan özel IP adresi için ayırma yöntemini değiştirme](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)