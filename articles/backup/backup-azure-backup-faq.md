
---
title: Azure Backup ile ilgili SSS | Microsoft Belgeleri
description: "Şu konulardaki yaygın soruların yanıtları: hizmetin çalışması, Azure Backup aracısı, Kurtarma Hizmetleri kasası ve yedekleme ile bekletme sınırları."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "yedekleme ve olağanüstü durum kurtarma; backup hizmeti"
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 3/10/2017
ms.author: markgal;giridham;arunak;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 87d500d886feb2dbd61d15d5a980ab2c4018c2f2
ms.lasthandoff: 04/03/2017


---
# <a name="questions-about-the-azure-backup-service"></a>Azure Backup hizmetiyle ilgili sorular
Bu makalede Azure Backup bileşenlerini hızlıca anlamanıza yardımcı olacak yaygın soruların (yanıtlarıyla birlikte) bölümleri bulunur. Bazı yanıtlarda, kapsamlı bilgiler içeren makalelerin bağlantıları vardır. **Yorumlar**’a (sağda) tıklayarak Azure Backup hakkında soru sorabilirsiniz. Yorumlar bu makalenin altında görünür. Yorum yapmak için bir Livefyre hesabı gerekir. Ayrıca Azure Backup hizmeti ile ilgili sorularınızı [tartışma forumunda](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup) paylaşabilirsiniz.

Bu makaledeki bölümleri hızlı taramak için **bu makale altında** sağ taraftaki bağlantıları kullanın.


## <a name="recovery-services-vault"></a>Kurtarma hizmetleri kasası

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Her bir Azure aboneliği için oluşturulan kasaların sayısına yönelik herhangi bir sınır var mıdır? <br/>
Evet. Eylül 2016’dan itibaren abonelik başına 25 Kurtarma Hizmeti veya yedekleme kasası oluşturabilirsiniz. Her abonelikte, Azure Backup hizmetinin desteklenen her bir bölgesi için en fazla 25 Kurtarma Hizmetleri kasası oluşturabilirsiniz. Daha fazla kasaya ihtiyacınız varsa başka bir abonelik oluşturun.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Her bir kasa için kaydedilebilen sunucu/makine sayısına yönelik sınırlar var mıdır? <br/>
Evet, kasa başına en fazla 50 makine kaydedebilirsiniz. Azure IaaS sanal makineleri için sınır, kasa başına 200 VM'dir. Daha fazla makine kaydetmeniz gerekirse başka bir kasa oluşturun.

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Sunucumu başka bir veri merkezine nasıl kaydederim?<br/>
Yedekleme verileri, kasanın kayıtlı olduğu veri merkezine gönderilir. Veri merkezini değiştirmenin en kolay yolu, aracıyı kaldırmak ve aracıyı yeniden yükleyip istenilen veri merkezine ait yeni bir kasa kaydetmektir.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Kuruluşumun bir kasası varsa veri geri yükleme sırasında bir sunucunun verilerini başka bir sunucudan nasıl ayırabilirim?<br/>
Aynı kasaya kayıtlı tüm sunucular, *aynı parolayı kullanan* diğer sunucular tarafından yedeklenen verileri kurtarabilir. Yedekleme verilerini kuruluşunuzdaki diğer sunucularından ayırmak istediğiniz sunucularınız varsa bu sunucular için belirlenmiş bir parola kullanın. Örneğin, insan kaynakları sunucuları bir şifreleme parolası kullanırken, muhasebe sunucuları ve depolama sunucuları farklı birer şifreleme parolası kullanabilir.

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Önbellek klasörü için minimum boyut gereksinimini nedir? <br/>
Önbellek klasörünün boyutu, yedeklediğiniz veri miktarını belirler. Önbellek klasörü, veri depolama için gerekli olan alanın % 5'ini oluşturmalıdır.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>Yedekleme verilerimin veya kasamın abonelikler arasında "geçişini" sağlayabilir miyim? <br/>
Hayır. Kasa abonelik düzeyinde oluşturulur ve oluşturulduktan sonra başka bir aboneliğe yeniden atanamaz.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Kurtarma Hizmetleri kasaları Resource Manager tabanlıdır. Backup kasaları (Klasik mod) hala destekleniyor mu? <br/>
[Klasik portaldaki](https://manage.windowsazure.com) tüm mevcut Backup kasaları desteklenmeye devam etmektedir. Ancak, bundan böyle yeni Backup kasaları dağıtmak için klasik portalı kullanamazsınız. Gelecekteki iyileştirmeler yalnızca Kurtarma Hizmetleri kasaları için geçerli olacağından, Microsoft tüm dağıtımlar için Kurtarma Hizmetleri kasalarının kullanılmasını önerir. Klasik portalda bir Backup kasası oluşturmayı denerseniz, [Azure portalına](https://portal.azure.com) yönlendirilirsiniz.

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Bir Backup kasasının Kurtarma Hizmetleri kasasına geçişini sağlayabilir miyim? <br/>
Ne yazık ki hayır, bir Backup kasasının içeriğini Kurtarma Hizmetleri kasasına geçiremezsiniz. Bu işlevi eklemeye yönelik çalışmalarımız devam ediyor ancak işlev şu anda kullanılamıyor.

### <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Kurtarma Hizmetleri kasaları, klasik VM’leri mi Resource Manager tabanlı VM’leri mi destekler? <br/>
Kurtarma Hizmetleri kasaları iki modeli de destekler.  Klasik portalda oluşturulan bir klasik VM’yi ya da Azure portalında oluşturulan bir Resource Manager VM’sini bir Kurtarma Hizmetleri kasasına yedekleyebilirsiniz.

### <a name="i-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Klasik VM’lerimi bir Backup kasasına yedekledim. VM’lerimi klasik moddan Resource Manager moduna geçirip bunları bir Kurtarma Hizmetleri kasasında koruyabilir miyim?
Bir VM’yi klasikten Resource Manager moduna taşıdığınızda yedekleme kasasındaki klasik VM kurtarma noktaları, Kurtarma Hizmetleri kasasına otomatik olarak geçirilmez. VM yedeklerinizi aktarmak için bu adımları izleyin:

1. Backup kasasında **Korunan Öğeler** sekmesine gidin ve VM’yi seçin. [Korumayı Durdur](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines)’a tıklayın. *İlişkili yedekleme verilerini sil* seçeneğini **işaretlenmemiş** olarak bırakın.
2. Sanal makineyi, klasik moddan Resource Manager moduna geçirin. Sanal makineye karşılık gelen depolama ve ağ bilgilerinin de Resource Manager moduna geçirildiğinden emin olun.
3. Bir Kurtarma Hizmetleri kasası oluşturun ve kasa panosunun üstündeki **Yedekle** eylemini kullanarak, geçirilen sanal makinede yedeklemeyi yapılandırın. Bir VM’yi Kurtarma Hizmetleri kasasına yedekleme hakkında ayrıntılı bilgi için [Azure VM’leri bir Kurtarma Hizmetleri kasasıyla koruma](backup-azure-vms-first-look-arm.md) başlıklı makaleye bakın.



## <a name="azure-backup-agent"></a>Azure Backup aracısı

### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>En son Azure Backup aracısını nereden indirebilirim? <br/>
Windows Server, System Center DPM veya Windows istemcisini yedeklemeye yönelik en son aracıyı [buradan](http://aka.ms/azurebackup_agent) indirebilirsiniz. Bir sanal makineyi yedeklemek istiyorsanız VM Aracısı'nı (otomatik olarak uygun uzantıyı yükler) kullanın. VM Aracısı, Azure galerisinden oluşturulan sanal makineler üzerinde zaten mevcuttur.

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Azure Backup aracısını yapılandırırken kasa kimlik bilgilerini girmem isteniyor. Kasa kimlik bilgilerinin süresi dolar mı?
Evet, kasa kimlik bilgilerinin süresi 48 saat sonra dolar. Dosyanın süresi dolarsa Azure portalında oturum açın ve kasa kimlik bilgileri dosyalarını kasanızdan indirin.

### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Azure'a veri yedekleyen bir Windows sunucusunu yeniden adlandırırsam ne olur?<br/>
Bir sunucuyu yeniden adlandırdığınızda, geçerli olarak yapılandırılmış olan tüm yedeklemeler durdurulur.
Sunucunun yeni adını Backup kasasına kaydedin. Yeni adı kasaya kaydettiğinizde, ilk yedekleme işlemi *tam* yedekleme olur. Eski sunucu adıyla kasaya yedeklenen verileri kurtarmanız gerekiyorsa **Veri Kurtarma** sihirbazındaki [**Başka bir sunucu**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) seçeneğini kullanın.

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>Ne tür sürücülerden dosya ve klasör yedekleyebilirim? <br/>
Aşağıdaki sürücüleri/birimleri yedekleyemezsiniz:

* Çıkarılabilir Medya: Tüm yedekleme öğesi kaynaklarının sabit olarak bildirilmesi gerekir.
* Salt Okunur Birimler: Birimin çalışması için birim gölge kopyası hizmetine (VSS) yönelik olarak yazılabilir olması gerekir.
* Çevrimdışı Birimler: Birimin çalışması için VSS'ye yönelik olarak çevrimiçi olması gerekir.
* Ağ paylaşımı: Birimin çevrimiçi yedekleme kullanılarak yedeklenebilmesi için sunucuya yönelik olarak yerel olması gerekir.
* Bitlocker korumalı birimler: Yedeklemenin gerçekleşebilmesi için birimin kilidinin açık olması gerekir.
* Dosya Sistemi Tanımı: NTFS, desteklenen tek dosya sistemidir.

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>Sunucumdan hangi dosya ve klasör hangi türlerini yedekleyebilirim?<br/>
Aşağıdaki türler desteklenir:

* Şifreli
* Sıkıştırılmış
* Seyrek
* Sıkıştırılmış + Seyrek
* Sabit Bağlantılar: Desteklenmez, atlanır
* Yeniden Ayrıştırma Noktası: Desteklenmez, atlanır
* Şifreli + Seyrek: Desteklenmez, atlanır
* Sıkıştırılmış Akış: Desteklenmez, atlanır
* Seyrek Akış: Desteklenmez, atlanır

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>Yedekleme ilkesinde Azure Backup aracısını kullanarak belirtilebilecek dosya yolu uzunluğu için üst sınır nedir? <br/>
Azure Backup aracısı NTFS kullanır. [Dosya yolu uzunluğu belirtimi, Windows API ile sınırlıdır](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Korumak istediğiniz dosyalar Windows API tarafından izin verilenden daha uzun dosya yollarına sahipse, üst klasörü veya disk sürücüsünü yedekleyin.  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Azure Backup aracısını kullanan Azure Yedekleme ilkesinin dosya yolunda hangi karakterlere izin verilir? <br>
 Azure Backup aracısı NTFS kullanır. [NTFS destekli karakterleri](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) dosya belirtiminin bir parçası olarak etkinleştirir.  

### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Azure Backup aracısı için belirtilen önbellek konumunu nasıl değiştiririm?<br/>
Önbellek konumunu değiştirmek için aşağıdaki listeyi kullanın.

* Yükseltilmiş komut isteminde aşağıdaki komutu çalıştırarak Backup altyapısını durdurun:

```PS C:\> Net stop obengine```
* Dosyaları taşımayın. Bunun yerine, önbellek alanı klasörünü yeterli alana sahip farklı bir sürücüye kopyalayın. Yedeklemelerin yeni önbellek alanı ile çalıştığı onaylandıktan sonra özgün önbellek alanı kaldırılabilir.
* Aşağıdaki kayıt defteri girdilerini yeni önbellek alanı klasörünün yolu ile güncelleştirin.<br/>

| Kayıt defteri yolu | Kayıt Defteri Anahtarı | Değer |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Yeni önbellek klasörü konumu* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Yeni önbellek klasörü konumu* |

* Yükseltilmiş komut isteminde aşağıdaki komutu çalıştırarak Backup altyapısını yeniden başlatın:

```PS C:\> Net start obengine```

Yedekleme oluşturma yeni önbellek konumunda başarıyla tamamlandıktan sonra, özgün önbellek klasörünü kaldırabilirsiniz.

### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Azure Backup Aracısı'nın beklendiği şekilde çalışması için önbellek klasörünü nereye koyabilirim?<br/>
Önbellek klasörü için aşağıdaki konumlar önerilmez:

* Ağ paylaşımı veya Çıkarılabilir Medya: Önbellek klasörü, çevrimiçi yedekleme kullanılarak yedeklenmesi gereken sunucu için yerel olmalıdır. Ağ konumlarını veya USB sürücüleri gibi çıkarılabilir medyalar desteklenmez.
* Çevrimdışı Birimler: Önbellek klasörü, Azure Backup Aracısı kullanılarak gerçekleştirilecek beklenen yedekleme için çevrimiçi olmalıdır.

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Önbellek klasörünün desteklenmeyen herhangi bir özniteliği var mıdır?<br/>
Aşağıdaki öznitelikler veya bunların bileşimleri, önbellek klasörü için desteklenmez:

* Şifreli
* Yinelenenleri kaldırma işlemi uygulanmış
* Sıkıştırılmış
* Seyrek
* Yeniden Ayrıştırma Noktası

Önbellek klasörü ve meta veri VHD’si, Azure Backup aracısı için gerekli özniteliklere sahip değildir.


## <a name="virtual-machines"></a>Sanal makineler

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>Azure Backup aracısını, önceden VM uzantısı kullanılarak Azure Backup hizmeti tarafından yedeklenmiş olan bir Azure VM üzerine yükleyebilir miyim? <br/>
Kesinlikle. Azure Backup, VM uzantısını kullanan Azure VM'ler için VM düzeyinde yedekleme sağlar. Konuk Windows işletim sistemi üzerindeki dosya ve klasörleri korumak için Azure Backup aracısını konuk Windows işletim sistemine yükleyin.

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>Azure Backup aracısını bir Azure VM'ye yükleyerek mevcut dosya ve klasörleri Azure VM tarafından sağlanan geçici depolama alanına yedekleyebilir miyim? <br/>
Evet. Azure Backup aracısını Konuk Windows işletim sistemine yükleyin ve dosya ve klasörleri geçici depolama alanına yedekleyin. Geçici depolama verileri silindikten sonra yedeklemeler başarısız olur. Ayrıca, geçici depolama verilerinin silinmiş olması durumunda, yalnızca geçici olmayan depolama alanına geri yükleme gerçekleştirebilirsiniz.


## <a name="azure-backup-server-and-data-protection-manager"></a>Azure Backup Sunucusu ve Data Protection Manager

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Bir fiziksel sunucu için Tam Kurtarma (BMR) yedeklemesi oluşturmak üzere Azure Backup Sunucusu'nu kullanabilir miyim? <br/>
Evet.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>System Center Data Protection Manager’ın hangi sürümü desteklenir? <br/>
[En son](http://aka.ms/azurebackup_agent) Azure Backup aracısını, System Center Data Protection Manager'ın (DPM) en son güncelleştirme paketi (UR) üzerine yüklemeniz önerilir. Ağustos 2016 itibariyle en son güncelleştirme, Güncelleştirme Paketi 11’dir.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>Dosya ve klasörlerimi korumak için Azure Backup aracısını yükledim. Artık Azure'a yönelik şirket içi uygulama/VM iş yüklerini korumak için Azure Backup aracısıyla çalışmak üzere System Center DPM'yi yükleyebilir miyim? <br/>
Azure Backup’ı System Center Data Protection Manager (DPM) ile birlikte kullanmak için önce DPM’yi, ardından Azure Backup aracısını yükleyin. Azure Backup bileşenlerinin bu sırada yüklenmesi, Azure Backup aracısının DPM ile çalışmasını sağlar. Azure Backup aracısının DPM yüklenmeden önce yüklenmesi önerilmez veya desteklenmez.


## <a name="how-azure-backup-works"></a>Azure Backup nasıl çalışır?

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Azure Backup aracısı, Windows Server 2012 yinelenenleri kaldırma özelliğini kullanan bir sunucu üzerinde çalışır mı? <br/>
Evet. Aracı hizmeti, yedekleme işlemini hazırlarken yinelenenleri kaldırma işlemi uygulanmış verileri normal verilere dönüştürür. Ardından verileri yedekleme için en iyi duruma getirir, verileri şifreler ve daha sonra, şifreli verileri çevrimiçi yedekleme hizmetine gönderir.

### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Bir yedekleme işini başlatıldıktan sonra iptal edersem aktarılan yedekleme verileri silinir mi? <br/>
Hayır. Yedekleme işi iptal edilmeden önce kasaya aktarılan tüm veriler kasada kalır. Azure Backup, yedekleme işlemi sırasında yedekleme verilerine zaman zaman denetim noktaları eklemek üzere bir denetim noktası mekanizması kullanır. Yedekleme verilerinde denetim noktaları bulunduğundan, sonraki yedekleme işlemi dosyaların bütünlüğünü doğrulayabilir. Bir sonraki yedekleme işi, daha önce yedeklenen verilerin üzerine artımlı olarak gerçekleşir. Artımlı yedekleme işlemlerinin yalnızca yeni veya değiştirilmiş verileri aktarması, bant genişliğinin daha iyi kullanılması anlamına gelir.

Bir Azure VM’ye yönelik bir yedekleme işini iptal ederseniz aktarılan tüm veriler yoksayılır. Bir sonraki yedekleme işi, son başarılı yedekleme işinden artımlı verileri aktarır.

### <a name="if-a-backup-job-fails-can-i-configure-the-backup-service-to-send-e-mail-br"></a>Bir yedekleme işi başarısız olursa Backup hizmetini e-posta gönderecek şekilde yapılandırabilir miyim? <br/>
Evet, Backup hizmeti bir PowerShell betiği ile kullanılabilen çeşitli olay tabanlı uyarılara sahiptir. Tam açıklama için bkz. [Bildirimleri yapılandırma](backup-azure-monitor-vms.md#configure-notifications).

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Bir yedekleme işinin ne zaman veya kaç kez zamanlanabileceğine yönelik sınırlar var mıdır?<br/>
Evet. Yedekleme işlerini Windows Server veya Windows iş istasyonları üzerinde günde en fazla üç kez çalıştırabilirsiniz. Yedekleme işlerini System Center DPM üzerinde günde en fazla iki kez çalıştırabilirsiniz. Bir yedekleme işini IaaS VM'ler için günde bir kez çalıştırabilirsiniz. Windows Server veya Windows iş istasyonu için zamanlama ilkesini, günlük veya haftalık zamanlamalar belirtmek üzere kullanabilirsiniz. System Center DPM'yi kullanarak günlük, haftalık, aylık ve yıllık zamanlamalar belirtebilirsiniz.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Kurtarma Hizmetleri kasasına aktarılan verilerin büyüklüğü neden yedeklediğim verilerden daha küçük?<br/>
 Azure Backup Aracısı veya SCDPM ya da Azure Backup Sunucusundan yedeklenen tüm veriler aktarılmadan önce sıkıştırılır ve şifrelenir. Sıkıştırma ve şifreleme uygulandıktan sonra yedekleme kasasındaki veriler % 30-40 daha küçük hale gelir.

 ### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>Backup hizmeti tarafından kullanılan bant genişliği miktarını ayarlamanın bir yolu var mıdır?<br/>
  Evet, bant genişliğini ayarlamak için Backup Aracısı'ndaki **Özellikleri Değiştir** seçeneğini kullanın. Bant genişliği miktarını ve bu bant genişliğini kullanma zamanlarınızı ayarlayabilirsiniz. Adım adım yönergeler için bkz. **[Ağ kapasitesi azaltmayı etkinleştirme](backup-configure-vault.md#enable-network-throttling)**.



## <a name="what-can-i-back-up"></a>Neleri yedekleyebilirim?

### <a name="which-operating-systems-do-azure-backup-support-br"></a>Azure Backup hangi işletim sistemlerini destekler? <br/>
Azure Backup, Azure Backup Sunucusu ve System Center Data Protection Manager (DPM) kullanılarak korunan dosya ve klasörlerin yanı sıra iş yükü uygulamalarının yedeklenmesi için aşağıdaki listede yer alan işletim sistemlerini destekler.

| İşletim Sistemi | Platform | SKU |
|:--- | --- |:--- |
| Windows 8 ve en son SP'ler |64 bit |Enterprise, Pro |
| Windows 7 ve en son SP'ler |64 bit |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 ve en son SP'ler |64 bit |Enterprise, Pro |
| Windows 10 |64 bit |Enterprise, Pro, Home |
| Windows Server 2016 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 ve en son SP'ler |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 ve en son SP'ler |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 ve en son SP'ler |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 ve en son SP'ler |64 bit |Standard, Workgroup |
| Windows Server 2012 R2 ve en son SP'ler |64 bit |Essential |
| Windows Server 2008 R2 SP1 |64 bit |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bit |Standard, Enterprise, Datacenter, Foundation |

**Azure VM yedeklemesi için:**

* **Linux**: Azure Backup, [Azure tarafından onaylanan bir dağıtım listesini](../virtual-machines/linux/endorsed-distros.md) (CoreOS Linux hariç) destekler.  Sanal makinede VM aracısı kullanılabilir olduğu ve Python desteği bulunduğu sürece diğer Kendi Linux’unu Getir dağıtımları da çalışabilir.
* **Windows Server**:  Windows Server 2008 R2’den eski sürümler desteklenmez.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Yedeklenmekte olan her veri kaynağının boyutuna yönelik bir sınır var mıdır? <br/>
Bir kasaya yedekleyebileceğiniz veri miktarı konusunda sınır yoktur. Azure Backup, veri kaynağı için en fazla boyut kısıtlaması uygular ancak limitler yüksektir. Ağustos 2015 itibarıyla, desteklenen işletim sistemlerinde veri kaynağı için boyut üst sınırı şu şekildedir:

| S.No | İşletim sistemi | En büyük veri kaynağı boyutu |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 veya üzeri |54.400 GB |
| 2 |Windows 8 veya üzeri |54.400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

Aşağıdaki tabloda, her bir veri kaynağı boyutunun nasıl belirlendiği açıklanmaktadır.

| Veri kaynağı | Ayrıntılar |
|:---:|:--- |
| Birim |Bir sunucu veya istemci makinenin tek bir biriminden yedeklenmekte olan verilerin miktarı |
| Hyper-V sanal makine |Yedeklenmekte olan sanal makinenin tüm VHD'lerine ait verilerin toplamı |
| Microsoft SQL Server veritabanı |Yedeklenmekte olan tek bir SQL veritabanının boyutu |
| Microsoft SharePoint |Yedeklenmekte olan bir SharePoint grubu içinde içerik ve yapılandırma veritabanlarının toplamı |
| Microsoft Exchange |Yedeklenmekte olan bir Exchange sunucusundaki tüm Exchange veritabanlarının toplamı |
| BMR/Sistem Durumu |Yedeklenmekte olan makinenin BMR'sinin veya sistem durumunun her ayrı kopyası |



## <a name="retention-policy-and-recovery-points"></a>Bekletme ilkesi ve kurtarma noktaları

### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>DPM ve Windows Server'a/istemcisine yönelik bekletme ilkesi (DPM olmadan Windows Server'da) arasında bir fark var mıdır?<br/>
Hayır, hem DPM hem de Windows Server/istemcisi günlük, haftalık, aylık ve yıllık bekletme ilkelerine sahiptir.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>Bekletme ilkelerimi seçerek yapılandırabilir miyim? Başka bir deyişle, haftalık ve günlük yapılandırmaya olanak tanırken yıllık ve aylık yapılandırmayı engelleyebilir miyim?<br/>
Evet, Azure Backup bekletme yapısı gereksinimlerinizi karşılayan bekletme ilkesini tanımlama konusunda tam esnekliğe sahip olmanızı sağlar.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>Saat 18:00 için “bir yedekleme zamanlayıp” farklı bir saat için de "bekletme ilkeleri" belirtebilir miyim?<br/>
Hayır. Bekletme ilkeleri, yalnızca yedekleme noktalarında uygulanabilir. Aşağıdaki görüntüde, bekletme ilkesi 00:00 ve 18:00'de alınan yedeklemeler için belirtilmiştir. <br/>

![Yedekleme ve Bekletmeyi Zamanlama](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>Zamanlanmış bekletme ilkeleri için bir artımlı kopya aktarılır mı? <br/>
Hayır, artımlı kopya, yedekleme zamanlaması sayfasında belirtilen süreye göre gönderilir. Bekletilebilen noktalar, bekletme ilkesi temel alınarak belirlenir.

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Yedekleme uzun süre tutulursa daha eski bir veri noktasının kurtarılması daha uzun mu sürer? <br/>
Hayır, en eski veya en yeni noktanın kurtarılması için gereken süre aynıdır. Her kurtarma noktası bir tam nokta gibi davranır.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Her kurtarma noktası bir tam nokta gibiyse bu durum toplam faturalanabilir yedekleme alanını etkiler mi?<br/>
Genel uzun vadeli bekletme noktası ürünleri, yedekleme verilerini tam noktalar olarak depolar. Tam noktalar depolama açısından *verimsizdir* ancak daha kolay ve hızlı şekilde geri yüklenir. Artımlı kopyalar depolama açısından *verimlidir* ancak kurtarma süresini etkileyecek şekilde bir veri zincirini geri yüklemenizi gerektirir. Azure Backup alanı mimarisi, hızlı geri yükleme özelliğiyle optimum veri depolama olanağı sunarken aynı zamanda düşük depolama maliyetleri oluşturarak iki açıdan da avantaj sağlar. Bu veri depolama yaklaşımı, giriş ve çıkış bant genişliğinizin verimli şekilde kullanılmasını sağlar. Veri depolama alanı miktarı ve verileri kurtarmak için gereken süre minimum düzeyde tutulur. [Artımlı yedekleme](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) tasarrufunun ne kadar verimli olduğu hakkında daha fazla bilgi edinin.

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Oluşturulabilecek kurtarma noktalarının sayısına yönelik bir sınır var mıdır?<br/>
Korumalı bir örnek için en çok 9999 kurtarma noktası oluşturabilirsiniz. Korumalı örnek, verileri Azure’a yedeklemek için yapılandırılmış bir bilgisayar, sunucu (fiziksel veya sanal) veya iş yüküdür. Bir yedekleme kasası için korunan örnek sayısı sınırlı değildir. Daha fazla bilgi için [Yedekleme ve elde tutma](./backup-introduction-to-azure-backup.md#backup-and-retention) ile [Korunan örnek nedir](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance)? konularındaki açıklamalara bakın

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Azure'a yedeklenen veriler üzerinde kaç kurtarma işlemi yapabilirim?<br/>
Azure Backup ile gerçekleştirilen kurtarma işlemlerinin sayısına yönelik bir sınır yoktur.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Verileri geri yüklerken Azure'dan çıkış trafiği için ödeme yapacak mıyım? <br/>
Hayır. Kurtarma işlemleriniz ücretsizdir ve çıkış trafiği için sizden ücret tahsil edilmez.

### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>Bir yedekleme ilkesi zamanlamış olmama karşın "Azure Yedeklemeleri bu sunucu için yapılandırılmamış" uyarısını alıyorum <br/>
Bu uyarı, yerel sunucuda depolanan yedekleme zamanlaması ayarları, yedekleme kasasında depolanan ayarlarla aynı olmadığında oluşur. Sunucu ya da ayarlar bilinen bir iyi duruma getirilerek kurtarıldığında, yedekleme zamanlamaları eşitlemesini kaybedebilir. Bu uyarıyı alırsanız [yedekleme ilkesini yeniden yapılandırın](backup-azure-manage-windows-server.md) ve ardından yerel sunucuyu Azure ile yeniden eşitlemek için **Run Back Up Now (Yedeklemeyi Şimdi Çalıştır)** işlemini uygulayın.



## <a name="azure-backup-encryption"></a>Azure Backup şifrelemesi

### <a name="is-the-data-sent-to-azure-encrypted-br"></a>Veriler Azure'a şifreli olarak mı gönderilir? <br/>
Evet. Veriler AES256 kullanılarak şirket içi sunucu/istemci/SCDPM makinesi üzerinde şifrelenir ve güvenli bir HTTPS bağlantısı üzerinden gönderilir.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Azure üzerindeki yedekleme verileri de şifreli midir?<br/>
Evet. Azure'a gönderilen veriler (bekleyen) şifreli olarak kalır. Microsoft herhangi bir noktada yedekleme verilerinin şifresini çözmez. Azure Backup bir Azure VM’yi yedeklerken sanal makinenin şifrelemesini kullanır. Örneğin, VM’niz Azure Disk Şifrelemesi veya başka bir şifreleme teknolojisi kullanılarak şifrelendiyse, Azure Backup verilerinizi korumak için bu şifrelemeyi kullanır.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Yedekleme verilerini şifrelemek için kullanılan şifreleme anahtarının minimum uzunluğu nedir? <br/>
Şifreleme anahtarı en az 16 karakterden oluşmalıdır.

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Şifreleme anahtarını kaybedersem ne olur? Verileri kurtarabilir miyim?/Microsoft, verileri kurtarabilir mi? <br/>
Yedekleme verilerini şifrelemek için kullanılan anahtar yalnızca müşterinin şirketinde bulunur. Microsoft, Azure üzerinde anahtarın bir kopyasını tutmaz ve anahtara erişim sahibi değildir. Müşteri, anahtarı kaybederse Microsoft, yedekleme verilerini kurtaramaz.

