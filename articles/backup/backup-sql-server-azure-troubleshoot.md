---
title: SQL Server veritabanı yedeklemesi sorunlarını giderme
description: Azure Backup ile Azure VM 'lerde çalışan SQL Server veritabanlarının yedeklenmesi için sorun giderme bilgileri.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: 95f7966fa59f0a1f6f6a3c9c6832cc573f89e05c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172119"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Azure Backup kullanarak SQL Server veritabanı yedeklemesi sorunlarını giderme

Bu makalede, Azure sanal makinelerinde çalışan SQL Server veritabanları için sorun giderme bilgileri sağlanmaktadır.

Yedekleme işlemi ve sınırlamaları hakkında daha fazla bilgi için bkz. [Azure VM 'lerde SQL Server yedekleme hakkında](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server izinleri

Bir sanal makinede SQL Server veritabanının korumasını yapılandırmak için, **AzureBackupWindowsWorkload** uzantısını bu sanal makineye yüklemelisiniz. **Usererrorsqlnosysadminmembership**hatasını alırsanız, SQL Server Örneğiniz gereken yedekleme izinlerine sahip olmadığı anlamına gelir. Bu hatayı onarmak için [VM Izinlerini ayarlama](backup-azure-sql-database.md#set-vm-permissions)bölümündeki adımları izleyin.

## <a name="error-messages"></a>Hata iletileri

### <a name="backup-type-unsupported"></a>Yedekleme türü desteklenmiyor

| Severity | Açıklama | Olası nedenler | Önerilen eylem |
|---|---|---|---|
| Uyarı | Bu veritabanının geçerli ayarları, ilişkili ilkede mevcut olan bazı yedekleme türlerini desteklemiyor. | <li>Ana veritabanında yalnızca tam bir veritabanı yedekleme işlemi yapılabilir. Değişiklik yedeklemesi veya işlem günlüğü yedeklemesi de mümkün değildir. </li> <li>Basit kurtarma modelindeki tüm veritabanları, işlem günlüklerinin yedeklenme izin vermez.</li> | İlkedeki tüm yedekleme türlerinin desteklendiği şekilde veritabanı ayarlarını değiştirin. Ya da, geçerli ilkeyi yalnızca desteklenen Yedekleme türlerini içerecek şekilde değiştirin. Aksi takdirde, desteklenmeyen yedekleme türleri zamanlanmış yedekleme sırasında atlanır veya isteğe bağlı yedekleme için yedekleme işi başarısız olur.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Bu SQL veritabanı, istenen yedekleme türünü desteklemiyor. | Veritabanı kurtarma modeli istenen yedekleme türüne izin vermediği zaman gerçekleşir. Hata aşağıdaki durumlarda oluşabilir: <br/><ul><li>Basit kurtarma modeli kullanan bir veritabanı, günlük yedeklemeye izin vermez.</li><li>Ana veritabanı için fark ve günlük yedeklerine izin verilmez.</li></ul>Daha ayrıntılı bilgi için bkz. [Kurtarma modellerini SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) belgeleri. | Basit kurtarma modelinde veritabanı için günlük yedeklemesi başarısız olursa, şu seçeneklerden birini deneyin:<ul><li>Veritabanı basit kurtarma modundaysa, günlük yedeklemelerini devre dışı bırakın.</li><li>Veritabanı kurtarma modelini tam veya toplu günlüğe kaydedilecek şekilde değiştirmek için [SQL Server belgelerini](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) kullanın. </li><li> Kurtarma modelini değiştirmek istemiyorsanız ve değiştirilemeyen birden çok veritabanını yedeklemek için standart bir ilkeniz varsa, hatayı yoksayın. Tam ve değişiklik yedeklemeleriniz zamanlama başına çalışacaktır. Bu durumda beklenen günlük yedeklemeleri atlanacak.</li></ul>Bu bir ana veritabanıdır ve değişiklik veya günlük yedeklemesi yapılandırdıysanız aşağıdaki adımlardan birini kullanın:<ul><li>Ana veritabanının yedekleme ilkesi zamanlamasını tam olarak değiştirmek için portalını kullanın.</li><li>Değiştirilemeyen birden çok veritabanını yedeklemek için standart bir ilkeniz varsa, hatayı yoksayın. Tam yedekleme, zamanlama başına çalışacaktır. Bu durumda beklenen değişiklik veya günlük yedeklemeleri gerçekleşmez.</li></ul> |
| Aynı veritabanında çakışan bir işlem zaten çalışmakta olduğundan işlem iptal edildi. | Aynı anda çalışan [yedekleme ve geri yükleme sınırlamaları hakkında blog girişine](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) bakın.| [Yedekleme işlerini izlemek için SQL Server Management Studio (SSMS) kullanın](manage-monitor-sql-database-backup.md). Çakışan işlem başarısız olduktan sonra işlemi yeniden başlatın.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| SQL veritabanı yok. | Veritabanı silinmiş ya da yeniden adlandırıldı. | Veritabanının yanlışlıkla silinip silinmediğini veya yeniden adlandırılmadığını denetleyin.<br/><br/> Veritabanı yanlışlıkla silinmişse, yedeklemelere devam etmek için veritabanını özgün konuma geri yükleyin.<br/><br/> Veritabanını silmiş ve gelecekteki yedeklemelere ihtiyacınız yoksa, kurtarma hizmetleri kasasında yedekleme **verilerini koruma** veya **yedekleme verilerini silme**ile **Yedeklemeyi Durdur** ' u seçin. Daha fazla bilgi için bkz. [yedeklenen SQL Server veritabanlarını yönetme ve izleme](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Günlük zinciri bozuk. | Veritabanı veya VM, günlük zincirini kesen başka bir yedekleme çözümüyle yedeklenir.|<ul><li>Başka bir yedekleme çözümünün veya betiğin kullanımda olup olmadığını denetleyin. Bu durumda, diğer yedekleme çözümünü durdurun. </li><li>Yedekleme isteğe bağlı bir günlük yedeğiydi, yeni bir günlük zinciri başlatmak için tam yedekleme tetikleyin. Zamanlanan günlük yedeklemeleri için, Azure Backup hizmeti bu sorunu giderecek bir tam yedeklemeyi otomatik olarak tetikleyeceği için herhangi bir eylem gerekmez.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Azure Backup SQL örneğine bağlanamıyor. | Azure Backup SQL Server örneğine bağlanamaz. | Ana nedenleri daraltmak için Azure portal hata menüsündeki ek ayrıntıları kullanın. Hatayı gidermek için [SQL Backup sorun giderme](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) bölümüne bakın.<br/><ul><li>Varsayılan SQL ayarları uzak bağlantılara izin vermezseniz ayarları değiştirin. Ayarları değiştirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Oturum açma sorunları varsa, bunları gidermek için bu bağlantıları kullanın:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Bu veri kaynağı için ilk tam yedekleme eksik. | Veritabanı için tam yedekleme eksik. Günlük ve değişiklik yedeklemeleri üst bir tam yedekleme için, fark veya günlük yedeklemelerini tetiklemeden önce tam yedeklemeler aldığınızdan emin olun. | İsteğe bağlı bir tam yedekleme tetikleyin.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Veri kaynağı için işlem günlüğü dolu olduğundan yedek alınamıyor. | Veritabanı işlem günlüğü alanı dolu. | Bu sorunu onarmak için [SQL Server belgelerine](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error)bakın. |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Hedef konumda aynı ada sahip bir veritabanı zaten var | Hedef geri yükleme hedefinin aynı ada sahip bir veritabanı zaten var.  | <ul><li>Hedef veritabanı adını değiştirin.</li><li>Ya da geri yükleme sayfasındaki üzerine yazmayı zorla seçeneğini kullanın.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Veritabanı çevrimdışına alınamadığından geri yükleme başarısız oldu. | Geri yükleme yaparken hedef veritabanının çevrimdışı hale getirilmesi gerekir. Azure Backup bu verileri çevrimdışına getiremedi. | Ana nedenleri daraltmak için Azure portal hata menüsündeki ek ayrıntıları kullanın. Daha fazla bilgi için bkz. [SQL Server belgeleri](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Hedefte parmak izine sahip sunucu sertifikası bulunamıyor. | Hedef örnekteki ana veritabanının geçerli bir şifreleme parmak izi yok. | Kaynak örneğinde kullanılan geçerli sertifika parmak izini hedef örneğe içeri aktarın. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
| Kurtarma için kullanılan günlük yedeklemesi toplu olarak günlüğe kaydedilen değişiklikler içeriyor. SQL yönergelerine göre zamanın rastgele bir noktasında durdurmak için kullanılamaz. | Bir veritabanı toplu olarak günlüğe kaydedilmiş kurtarma modundayken, toplu günlüğe kaydedilen bir işlem ve sonraki günlük işlemi arasındaki veriler kurtarılamaz. | Kurtarma için farklı bir zaman noktası seçin. [Daha fazla bilgi edinin](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105)).

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
24 saat içinde izin verilen işlem sayısı sınırına ulaştığınızdan işlem engellendi. | 24 saatin bir sınırında bir işlem için izin verilen en yüksek sınıra ulaştıysanız, bu hata gelir. <br> Örneğin: günlük tetiklenebilecek yedekleme yapılandırma işi sayısı sınırına ulaşılırsa ve yeni bir öğede yedeklemeyi yapılandırmaya çalışırsanız, bu hatayı görürsünüz. | Genellikle, 24 saat sonra işlemi yeniden denemek bu sorunu çözer. Ancak sorun devam ederse, yardım için Microsoft desteğine başvurabilirsiniz.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Hata iletisi | Olası nedenler | Önerilen eylem |
|---|---|---|
Kasa, 24 saatlik bir yayılmaya izin verilen bu işlemler için maksimum sınırına ulaştığından, işlem engellendi. | 24 saatin bir sınırında bir işlem için izin verilen en yüksek sınıra ulaştıysanız, bu hata gelir. Bu hata genellikle değiştirme ilkesi veya otomatik koruma gibi ölçekli işlemler olduğunda gelir. Clouddosabsolutelimitulaştığı durumdan farklı olarak, bu durumu çözmek için pek çok şey yapabilirsiniz, aslında Azure Backup hizmet, söz konusu tüm öğeler için işlemleri dahili olarak yeniden dener.<br> Örneğin: bir ilkeyle korunan çok sayıda veri kaynağı varsa ve bu ilkeyi değiştirmeye çalışırsanız, korumalı öğelerin her biri için koruma işlerini yapılandırma tetikleyecektir ve bazen bu gibi işlemler için izin verilen en yüksek sınıra ulaşabiliriz.| Azure Backup hizmeti, 24 saat sonra bu işlemi otomatik olarak yeniden deneyecek.

## <a name="re-registration-failures"></a>Yeniden kayıt sorunları

Yeniden kaydetme işlemini tetiklemeniz için aşağıdaki belirtilerden bir veya daha fazlasını denetleyin:

* VM 'de şu hata kodlarından birine sahip tüm işlemler (yedekleme, geri yükleme ve yapılandırma) başarısız oluyor: **Workloadextensionnoterişilebilen**, **usererrorworkloadextensionnotyüklü**, **workloadextensionnotsun** , **Workloadextensiondidntdequeuemsg**.
* Yedekleme öğesi için **Yedekleme durumu** alanı **erişilebilir değil**olarak gösteriliyor. Aynı durumla sonuçlanmasına neden olabilecek tüm diğer nedenler arasında bir kural yapın:

  * VM 'de yedeklemeyle ilgili işlemler gerçekleştirme izninin bulunmaması  
  * Sanal makineyi kapatıp yedeklemeler gerçekleşmiyor
  * Ağ sorunları  

  ![Bir VM 'yi yeniden kaydettirme içindeki "erişilebilir değil" durumu](./media/backup-azure-sql-database/re-register-vm.png)

* Her zaman açık kullanılabilirlik grubu söz konusu olduğunda, yedekleme tercihini değiştirdikten veya bir yük devretmeden sonra yedeklemeler başarısız olarak başlatılır.

Bu belirtiler aşağıdaki nedenlerden biri veya birkaçı olabilir:

* Bir uzantı silinmiş veya portaldan kaldırılmış.
* Bir uzantıyı **kaldırma veya program değiştirme**altındaki VM 'Deki **denetim masasından** bir uzantı kaldırıldı.
* VM, yerinde disk geri yüklemesi sırasında geri yüklendi.
* VM, genişletilmiş bir süre için kapatıldı, bu nedenle uzantı yapılandırması süresi dolmadı.
* VM silindi ve silinen VM ile aynı kaynak grubunda ve aynı ada sahip başka bir VM oluşturuldu.
* Kullanılabilirlik grubu düğümlerinden biri, tüm yedekleme yapılandırmasını almadı. Kullanılabilirlik grubu kasaya kaydedildiğinde veya yeni bir düğüm eklendiğinde bu durum oluşabilir.

Yukarıdaki senaryolarda, VM 'de bir yeniden kaydetme işlemi tetiklemeniz önerilir. Bu görevi PowerShell 'de gerçekleştirme yönergeleri için [buraya](https://docs.microsoft.com/azure/backup/backup-azure-sql-automation#enable-backup) bakın.

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

Bir örneği aşağıda verilmiştir:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

İçeriğin dize boyutu 20.000 baytı aşarsa, veritabanı dosyaları farklı şekilde depolanır. Kurtarma sırasında, geri yükleme için hedef dosya yolunu ayarlayamayacağız. Dosyalar, SQL Server tarafından belirtilen varsayılan SQL yoluna geri yüklenecek.

### <a name="override-the-default-target-restore-file-path"></a>Varsayılan hedef geri yükleme dosya yolunu geçersiz kıl

Geri yükleme işlemi sırasında hedef geri yükleme dosya yolunu, veritabanı dosyasının hedef geri yükleme yoluna eşlemesini içeren bir JSON dosyası yerleştirerek geçersiz kılabilirsiniz. Bir `database_name.json` dosyası oluşturun ve *C:\Program Files\Azure Iş yükü Backup\bin\plugins\SQL*konumuna yerleştirin.

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

Bir örneği aşağıda verilmiştir:

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

SQL Server VM 'Ler için Azure Backup (Genel Önizleme) hakkında daha fazla bilgi için bkz. [SQL VM 'leri Azure Backup](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
