---
title: SQL Server veritabanı yedeklemesi sorunlarını giderme
description: Azure Backup ile Azure VM 'lerde çalışan SQL Server veritabanlarının yedeklenmesi için sorun giderme bilgileri.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 2cf0ed0200de9b2787f5d9f38bd343f93648bc78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99557746"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure Backup kullanarak SQL Server veritabanı yedeklemesi sorunlarını giderme

Bu makalede, Azure sanal makinelerinde çalışan SQL Server veritabanları için sorun giderme bilgileri sağlanmaktadır.

Yedekleme işlemi ve sınırlamaları hakkında daha fazla bilgi için bkz. [Azure VM 'lerde SQL Server yedekleme hakkında](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server izinleri

Bir sanal makinede SQL Server veritabanının korumasını yapılandırmak için, **AzureBackupWindowsWorkload** uzantısını bu sanal makineye yüklemelisiniz. **Usererrorsqlnosysadminmembership** hatasını alırsanız, SQL Server Örneğiniz gereken yedekleme izinlerine sahip olmadığı anlamına gelir. Bu hatayı onarmak için [VM Izinlerini ayarlama](backup-azure-sql-database.md#set-vm-permissions)bölümündeki adımları izleyin.

## <a name="troubleshoot-discover-and-configure-issues"></a>Bulma ve yapılandırma sorunlarını giderme

Bir kurtarma hizmetleri Kasası oluşturup yapılandırdıktan sonra, veritabanlarının keşfedilmesinden ve yedeklemenin yapılandırılması iki adımlı bir işlemdir.<br>

![Yedekleme hedefi-Azure VM 'de SQL Server](./media/backup-azure-sql-database/sql.png)

Yedekleme yapılandırması sırasında, SQL sanal makinesi ve örnekleri **VM 'Lerde bulma** ve **yedekleme yapılandırma** (yukarıdaki görüntüye bakın) ' in içinde görünmediğine dikkat edin:

### <a name="step-1-discovery-dbs-in-vms"></a>1. Adım: VM 'lerde keşif veritabanları

- VM, bulunan VM listesinde listelenmemişse ve aynı zamanda başka bir kasada SQL yedeklemesi için kayıtlı değilse, [bulma SQL Server yedekleme](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) adımlarını izleyin.

### <a name="step-2-configure-backup"></a>2. Adım: yedeklemeyi yapılandırma

- SQL VM 'nin veritabanlarını korumak için kullanılan kasada kayıtlı olduğu kasasında [yedekleme yapılandırma](./backup-sql-server-database-azure-vms.md#configure-backup) adımlarını izleyin.

SQL VM 'nin yeni kasada kayıtlı olması gerekiyorsa, eski kasadaki kaydı kaldırılmalıdır.  Kasadan bir SQL VM kaydının silinmesi, korunan tüm veri kaynaklarının korunmasını gerektirir ve sonra yedeklenen verileri silebilirsiniz. Yedeklenen verileri silme, bozucu bir işlemdir.  SQL VM kaydını silmek için tüm önlemleri inceledikten ve aldıktan sonra, bu VM 'yi yeni bir kasa ile kaydedin ve yedekleme işlemini yeniden deneyin.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Yedekleme ve kurtarma sorunlarını giderme  

Her zaman, yedekleme ve geri yükleme işlemlerinde rastgele sorunlar oluşabilir veya bu işlemler takılmış olabilir. Bunun nedeni, sanal makinenizde virüsten koruma programlarının olması olabilir. En iyi uygulama olarak, aşağıdaki adımları öneririz:

1. Aşağıdaki klasörleri virüsten koruma taramayla hariç tutun:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    `C:\` *Systemdrive*'larınızın harfiyle değiştirin.

1. Bir VM içinde çalışan aşağıdaki üç işlemi virüsten koruma taramayla hariç tutun:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. SQL, virüsten koruma programlarıyla çalışmak üzere bazı yönergeler de sunmaktadır. Ayrıntılar için [Bu makaleye](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) bakın.

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Birden çok SQL Server örneğine sahip bir VM 'de hatalı örnek

Yalnızca VM içinde çalışan tüm SQL örnekleri sağlıklı olarak raporlandıysanız bir SQL VM 'ye geri yükleyebilirsiniz. Bir veya daha fazla örnek "hatalı" ise, VM geri yükleme hedefi olarak görünmez. Bu nedenle, geri yükleme işlemi sırasında "sunucu" açılan menüsünde çok örnekli bir VM 'nin neden görünmemesinin olası bir nedeni olabilir.

VM 'deki tüm SQL örneklerinin "yedekleme hazırlığı" ni, **yedeklemeyi Yapılandır** altında doğrulayabilirsiniz.

![Yedeklemenin hazır olduğunu doğrulama](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Sağlıklı SQL örneklerinde geri yükleme tetiklemek isterseniz, aşağıdaki adımları uygulayın:

1. SQL VM 'de oturum açın ve adresine gidin `C:\Program Files\Azure Workload Backup\bin` .
1. Adlı bir JSON dosyası oluşturun `ExtensionSettingsOverrides.json` (zaten mevcut değilse). Bu dosya VM 'de zaten mevcutsa, uygulamayı kullanmaya devam edin.
1. Aşağıdaki içeriği JSON dosyasına ekleyin ve dosyayı kaydedin:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Etkilenen sunucuda bulunan **DB 'Leri yeniden bul** işlemini Azure Portal tetikleyin (yedekleme hazırlığında görünebileceği konum). VM, geri yükleme işlemleri için hedef olarak görünmeye başlayacaktır.

    ![DB 'Leri yeniden keşfet](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. Geri yükleme işlemi tamamlandıktan sonra, bu dosyadaki ExtensionSettingsOverrides.js *beyaz Listedınstancesforsorgulama* girişini kaldırın.

## <a name="error-messages"></a>Hata iletileri

### <a name="backup-type-unsupported"></a>Yedekleme türü desteklenmiyor

| Önem Derecesi | Açıklama | Olası nedenler | Önerilen eylem |
|---|---|---|---|
| Uyarı | Bu veritabanının geçerli ayarları, ilişkili ilkede mevcut olan bazı yedekleme türlerini desteklemiyor. | <li>Ana veritabanında yalnızca tam bir veritabanı yedekleme işlemi yapılabilir. Değişiklik yedekleme ve işlem günlüğü yedeklemesi mümkün değildir. </li> <li>Basit kurtarma modelindeki tüm veritabanları, işlem günlüklerinin yedeklenme izin vermez.</li> | İlkedeki tüm yedekleme türlerinin desteklenmesi için veritabanı ayarlarını değiştirin. Ya da geçerli ilkeyi yalnızca desteklenen Yedekleme türlerini içerecek şekilde değiştirin. Aksi takdirde, desteklenmeyen yedekleme türleri zamanlanmış yedekleme sırasında atlanır veya isteğe bağlı yedekleme için yedekleme işi başarısız olur.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Bu SQL veritabanı, istenen yedekleme türünü desteklemiyor. | Veritabanı kurtarma modeli istenen yedekleme türüne izin vermediği zaman gerçekleşir. Hata aşağıdaki durumlarda oluşabilir: <br/><ul><li>Basit kurtarma modeli kullanan bir veritabanı, günlük yedeklemeye izin vermez.</li><li>Ana veritabanı için fark ve günlük yedeklerine izin verilmez.</li></ul>Daha ayrıntılı bilgi için bkz. [Kurtarma modellerini SQL Server](/sql/relational-databases/backup-restore/recovery-models-sql-server) belgeleri. | Basit kurtarma modelinde veritabanı için günlük yedeklemesi başarısız olursa, şu seçeneklerden birini deneyin:<ul><li>Veritabanı basit kurtarma modundaysa, günlük yedeklemelerini devre dışı bırakın.</li><li>Veritabanı kurtarma modelini tam veya toplu günlüğe kaydedilecek şekilde değiştirmek için [SQL Server belgelerini](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) kullanın. </li><li> Kurtarma modelini değiştirmek istemiyorsanız ve değiştirilemeyen birden çok veritabanını yedeklemek için standart bir ilkeniz varsa, hatayı yoksayın. Tam ve değişiklik yedeklemeleriniz zamanlama başına çalışacaktır. Bu durumda beklenen günlük yedeklemeleri atlanacak.</li></ul>Bu bir ana veritabanıdır ve değişiklik veya günlük yedeklemesi yapılandırdıysanız aşağıdaki adımlardan birini kullanın:<ul><li>Ana veritabanının yedekleme ilkesi zamanlamasını tam olarak değiştirmek için portalını kullanın.</li><li>Değiştirilemeyen birden çok veritabanını yedeklemek için standart bir ilkeniz varsa, hatayı yoksayın. Tam yedekleme, zamanlama başına çalışacaktır. Bu durumda beklenen değişiklik veya günlük yedeklemeleri gerçekleşmez.</li></ul> |
| Aynı veritabanında çakışan bir işlem zaten çalışmakta olduğundan işlem iptal edildi. | Aynı anda çalışan [yedekleme ve geri yükleme sınırlamaları hakkında blog girişine](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) bakın.| [Yedekleme işlerini izlemek için SQL Server Management Studio (SSMS) kullanın](manage-monitor-sql-database-backup.md). Çakışan işlem başarısız olduktan sonra işlemi yeniden başlatın.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| SQL veritabanı yok. | Veritabanı silinmiş ya da yeniden adlandırıldı. | Veritabanının yanlışlıkla silinip silinmediğini veya yeniden adlandırılmadığını denetleyin.<br/><br/> Veritabanı yanlışlıkla silinmişse, yedeklemelere devam etmek için veritabanını özgün konuma geri yükleyin.<br/><br/> Veritabanını silmiş ve gelecekteki yedeklemelere ihtiyacınız yoksa, kurtarma hizmetleri kasasında yedekleme **verilerini koruma** veya **yedekleme verilerini silme** ile **Yedeklemeyi Durdur** ' u seçin. Daha fazla bilgi için bkz. [yedeklenen SQL Server veritabanlarını yönetme ve izleme](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Günlük zinciri bozuk. | Veritabanı veya VM, günlük zincirini kesen başka bir yedekleme çözümüyle yedeklenir.|<ul><li>Başka bir yedekleme çözümünün veya betiğin kullanımda olup olmadığını denetleyin. Bu durumda, diğer yedekleme çözümünü durdurun. </li><li>Yedekleme isteğe bağlı bir günlük yedeğiydi, yeni bir günlük zinciri başlatmak için tam yedekleme tetikleyin. Zamanlanan günlük yedeklemeleri için, Azure Backup hizmeti bu sorunu giderecek bir tam yedeklemeyi otomatik olarak tetikleyeceği için herhangi bir eylem gerekmez.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Azure Backup SQL örneğine bağlanamıyor. | Azure Backup SQL Server örneğine bağlanamaz. | Ana nedenleri daraltmak için Azure portal hata menüsündeki ek ayrıntıları kullanın. Hatayı gidermek için [SQL Backup sorun giderme](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) bölümüne bakın.<br/><ul><li>Varsayılan SQL ayarları uzak bağlantılara izin vermezseniz ayarları değiştirin. Ayarları değiştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Oturum açma sorunları varsa, bunları gidermek için bu bağlantıları kullanın:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Bu veri kaynağı için ilk tam yedekleme eksik. | Veritabanı için tam yedekleme eksik. Günlük ve değişiklik yedeklemeleri üst bir tam yedekleme için, fark veya günlük yedeklemelerini tetiklemeden önce tam yedeklemeler aldığınızdan emin olun. | İsteğe bağlı bir tam yedekleme tetikleyin.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Veri kaynağı için işlem günlüğü dolu olduğundan yedek alınamıyor. | Veritabanı işlem günlüğü alanı dolu. | Bu sorunu onarmak için [SQL Server belgelerine](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)bakın. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Hedef konumda aynı ada sahip bir veritabanı zaten var | Hedef geri yükleme hedefinin aynı ada sahip bir veritabanı zaten var.  | <ul><li>Hedef veritabanı adını değiştirin.</li><li>Ya da geri yükleme sayfasındaki üzerine yazmayı zorla seçeneğini kullanın.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Veritabanı çevrimdışına alınamadığından geri yükleme başarısız oldu. | Geri yükleme yaparken hedef veritabanının çevrimdışı hale getirilmesi gerekir. Azure Backup bu verileri çevrimdışına getiremedi. | Ana nedenleri daraltmak için Azure portal hata menüsündeki ek ayrıntıları kullanın. Daha fazla bilgi için bkz. [SQL Server belgeleri](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Hata İletisi |Olası nedenler  |Önerilen Eylem  |
|---------|---------|---------|
|İşlem sırasında bir giriş/çıkış hatası oluştu. Lütfen sanal makinede ortak GÇ hatalarını kontrol edin.   |   Hedefte erişim izinleri veya boşluk kısıtlamaları.       |  Sanal makinede ortak GÇ hatalarını kontrol edin. Makinedeki hedef sürücünün/ağın paylaşımının olduğundan emin olun: <li> makinedeki NT AUTHORITY\SYSTEM hesabı için okuma/yazma izni vardır. <li> işlemin başarıyla tamamlanabilmesi için yeterli alan yok.<br> Daha fazla bilgi için bkz. [dosya olarak geri yükleme](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Hedefte parmak izine sahip sunucu sertifikası bulunamıyor. | Hedef örnekteki ana veritabanının geçerli bir şifreleme parmak izi yok. | Kaynak örneğinde kullanılan geçerli sertifika parmak izini hedef örneğe içeri aktarın. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Kurtarma için kullanılan günlük yedeklemesi toplu olarak günlüğe kaydedilen değişiklikler içeriyor. SQL yönergelerine göre rastgele bir zaman noktasında durmak için kullanılamaz. | Bir veritabanı toplu olarak günlüğe kaydedilmiş kurtarma modundayken, toplu günlüğe kaydedilen bir işlem ve sonraki günlük işlemi arasındaki veriler kurtarılamaz. | Kurtarma için farklı bir zaman noktası seçin. [Daha fazla bilgi edinin](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Kullanılabilirlik Grubunun bazı düğümleri kaydedilmediğinden SQL AlwaysOn Kullanılabilirlik Grubu yedekleme tercihi uygulanamadı. | Yedeklemeleri gerçekleştirmek için gereken düğümler kayıtlı değil veya ulaşılamaz durumda. | <ul><li>Bu veritabanının yedeklerini gerçekleştirmek için gereken tüm düğümlerin kayıtlı ve sağlıklı olduğundan emin olup işlemi yeniden deneyin.</li><li>SQL Server Always on kullanılabilirlik grubu için yedekleme tercihini değiştirin.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| SQL Server sanal makinesi kapalı ve Azure Backup hizmeti için erişilebilir değil. | VM kapatıldı. | SQL Server örneğinin çalıştığından emin olun. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Azure Backup hizmeti yedekleme yapmak için Azure VM Konuk aracısını kullanır, ancak Konuk aracı hedef sunucuda kullanılamıyor. | Konuk Aracısı etkin değil veya sağlam değil. | [VM Konuk aracısını](../virtual-machines/extensions/agent-windows.md) el ile yükleyebilirsiniz. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Otomatik koruma amacı kaldırılmış veya başka bir geçerli değil. | SQL Server örneğinde otomatik korumayı etkinleştirdiğinizde, bu örnekteki tüm veritabanları için **yedekleme Işlerini yapılandırın** . İşler çalışırken otomatik korumayı devre dışı bırakırsanız, bu hata kodu ile **devam eden** işler iptal edilir. | Kalan tüm veritabanlarının korunmasına yardımcı olmak için otomatik korumayı bir kez daha etkinleştirin. |

### <a name="clouddosabsolutelimitreached"></a>Clouddosabsolutelimitulaşıldı

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
24 saat içinde izin verilen işlem sayısı sınırına ulaştığınızdan işlem engellendi. | 24 saatin bir sınırında bir işlem için izin verilen en yüksek sınıra ulaştıysanız, bu hata görüntülenir. <br> Örneğin: günlük tetiklenebilecek yedekleme yapılandırma işi sayısı sınırına ulaşılırsa ve yeni bir öğede yedeklemeyi yapılandırmaya çalışırsanız, bu hatayı görürsünüz. | Genellikle, 24 saat sonra işlemi yeniden denemek bu sorunu çözer. Ancak sorun devam ederse, yardım için Microsoft desteğine başvurabilirsiniz.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
Kasa, 24 saatlik bir yayılmaya izin verilen bu işlemler için maksimum sınırına ulaştığından, işlem engellendi. | 24 saatin bir sınırında bir işlem için izin verilen en yüksek sınıra ulaştıysanız, bu hata görüntülenir. Bu hata genellikle değiştirme ilkesi veya otomatik koruma gibi ölçekli işlemler olduğunda görüntülenir. Clouddosabsolutelimitto 'ın aksine bu durumu çözmek için pek çok şey yoktur. Aslında Azure Backup hizmet, söz konusu tüm öğeler için işlemleri dahili olarak yeniden dener.<br> Örneğin: bir ilkeyle korunan çok sayıda veri kaynağı varsa ve bu ilkeyi değiştirmeye çalışırsanız, korumalı öğelerin her biri için koruma işlerini yapılandırma tetikleyecektir ve bazen bu gibi işlemler için izin verilen en yüksek sınıra ulaşabiliriz.| Azure Backup hizmeti, 24 saat sonra bu işlemi otomatik olarak yeniden deneyecek.

### <a name="workloadextensionnotreachable"></a>WorkloadExtensionNotReachable

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
AzureBackup iş yükü uzantısı işlemi başarısız oldu. | VM kapatılmış veya internet bağlantısı sorunları nedeniyle VM Azure Backup hizmetiyle iletişim kuramıyor.| <li> VM 'nin çalışır olduğundan ve internet bağlantısına sahip olduğundan emin olun.<li> [SQL Server VM uzantıyı yeniden kaydedin](manage-monitor-sql-database-backup.md#re-register-extension-on-the-sql-server-vm).


### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
VM, internet bağlantısı sorunları nedeniyle Azure Backup hizmetine başvuramayacak. | VM, Azure Backup hizmeti, Azure depolama veya Azure Active Directory hizmetlerine giden bağlantı gerektirir.| <li> Bağlantıyı kısıtlamak için NSG kullanıyorsanız, Azure Backup hizmetine giden erişime izin vermek ve Azure AD (*AzureActiveDirectory*) ve Azure depolama (*depolama*) Hizmetleri için benzer şekilde *AzureBackup* Service etiketini kullanmanız gerekir. Erişim vermek için bu [adımları](./backup-sql-server-database-azure-vms.md#nsg-tags) izleyin. <li> DNS 'in Azure uç noktalarını çözümlediğinden emin olun. <li> VM 'nin internet erişimini engelleyen bir yük dengeleyicinin arkasında olup olmadığını denetleyin. VM 'lere ortak IP atayarak bulma işlemi çalışacaktır. <li> Yukarıdaki üç hedef hizmete yapılan çağrıları engelleyen güvenlik duvarı/virüsten koruma/proxy bulunmadığından emin olun.

## <a name="re-registration-failures"></a>Yeniden kayıt sorunları

Yeniden kaydetme işlemini tetiklemeniz için aşağıdaki belirtilerden bir veya daha fazlasını denetleyin:

- Tüm işlemler (yedekleme, geri yükleme ve yapılandırma gibi) VM 'de şu hata kodlarından biriyle başarısız oluyor: **[Workloadextensionnoterişilebilen](#workloadextensionnotreachable)**, **usererrorworkloadextensionnotyüklü**, **workloadextensionnotsun,** **workloadextensiondidntdequeuemsg**.
- Yedekleme öğesi için **yedekleme durum** alanı **erişilebilir değil** olarak gösteriyorsa, aynı durum oluşmasına neden olabilecek diğer tüm nedenleri inceleyin:

  - VM 'de yedeklemeyle ilgili işlemler gerçekleştirme izninin bulunmaması.
  - Sanal makineyi kapatıp yedeklemeler gerçekleşmiyor.
  - [Ağ sorunları](#usererrorvminternetconnectivityissue)

   ![VM 'yi yeniden kaydetme](./media/backup-azure-sql-database/re-register-vm.png)

- Her zaman açık kullanılabilirlik grubu söz konusu olduğunda, yedekleme tercihini değiştirdikten veya bir yük devretmeden sonra yedeklemeler başarısız olarak başlatılır.

Bu belirtiler aşağıdaki nedenlerden biri veya birkaçı olabilir:

- Bir uzantı silinmiş veya portaldan kaldırılmış.
- Bir uzantıyı **kaldırma veya program değiştirme** altındaki VM 'Deki **denetim masasından** bir uzantı kaldırıldı.
- VM, yerinde disk geri yüklemesi sırasında geri yüklendi.
- VM, genişletilmiş bir süre için kapatıldı, bu nedenle uzantı yapılandırması süresi dolmadı.
- VM silindi ve silinen VM ile aynı kaynak grubunda ve aynı ada sahip başka bir VM oluşturuldu.
- Kullanılabilirlik grubu düğümlerinden biri, tüm yedekleme yapılandırmasını almadı. Kullanılabilirlik grubu kasaya kaydedildiğinde veya yeni bir düğüm eklendiğinde bu durum oluşabilir.

Yukarıdaki senaryolarda, VM 'de bir yeniden kaydetme işlemi tetiklemeniz önerilir. Bu görevi PowerShell 'de gerçekleştirme yönergeleri için [buraya](./backup-azure-sql-automation.md#enable-backup) bakın.

## <a name="size-limit-for-files"></a>Dosyalar için boyut sınırı

Dosyaların toplam dize boyutu yalnızca dosya sayısına ve ayrıca adlarına ve yollarına göre değişir. Her veritabanı dosyası için, mantıksal dosya adını ve fiziksel yolu alın. Bu SQL sorgusunu kullanabilirsiniz:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Şimdi aşağıdaki biçimde düzenleyin:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Aşağıda bir örnek verilmiştir:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

İçeriğin dize boyutu 20.000 baytı aşarsa, veritabanı dosyaları farklı şekilde depolanır. Kurtarma sırasında, geri yükleme için hedef dosya yolunu ayarlayamayacağız. Dosyalar, SQL Server tarafından belirtilen varsayılan SQL yoluna geri yüklenecek.

### <a name="override-the-default-target-restore-file-path"></a>Varsayılan hedef geri yükleme dosya yolunu geçersiz kıl

Geri yükleme işlemi sırasında hedef geri yükleme dosya yolunu, veritabanı dosyasının hedef geri yükleme yoluna eşlemesini içeren bir JSON dosyası yerleştirerek geçersiz kılabilirsiniz. Bir `database_name.json` dosya oluşturun ve konuma yerleştirin `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` .

Dosyanın içeriği şu biçimde olmalıdır:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Aşağıda bir örnek verilmiştir:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

Önceki içerikte, aşağıdaki SQL sorgusunu kullanarak veritabanı dosyasının mantıksal adını alabilirsiniz:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Geri yükleme işlemini tetiklemeniz için bu dosyanın yerleştirilmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

SQL Server VM 'Ler için Azure Backup (Genel Önizleme) hakkında daha fazla bilgi için bkz. [SQL VM 'leri Azure Backup](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).
