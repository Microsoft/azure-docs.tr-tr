---
title: Yenilikler
titleSuffix: Azure SQL Database & SQL Managed Instance
description: SQL yönetilen örneği & Azure SQL veritabanı için yeni özellikler ve belge iyileştirmeleri hakkında bilgi edinin.
services: sql-database
author: stevestein
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/17/2021
ms.author: sstein
ms.openlocfilehash: d0522fe2c8b6d6b623903a720e6c8e760bd6aa92
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602082"
---
# <a name="whats-new-in-azure-sql-database--sql-managed-instance"></a>SQL yönetilen örneği & Azure SQL veritabanı 'ndaki yenilikler nelerdir?
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu makalede, şu anda genel önizleme aşamasında olan Azure SQL veritabanı ve Azure SQL yönetilen örnek özellikleri listelenmektedir. SQL veritabanı ve SQL yönetilen örnek güncelleştirmeleri ve geliştirmeleri için bkz. SQL [veritabanı & SQL yönetilen örnek hizmeti güncelleştirmeleri](https://azure.microsoft.com/updates/?product=sql-database). Diğer Azure hizmetlerine yönelik güncelleştirmeler ve geliştirmeler için bkz. [hizmet güncelleştirmeleri](https://azure.microsoft.com/updates).

## <a name="whats-new"></a>Yenilikler

Azure SQL veritabanı ve Azure SQL yönetilen örneği belgeleri ayrı bölümlere bölündü. *Azure SQL veritabanı yönetilen örneği* Ile *Azure SQL yönetilen örneği* arasında yönetilen bir örneğe nasıl başvurduğumuz de güncelleştiriyoruz.

Bazı özellikler ve işlevler büyük ölçüde tek bir veritabanı ve yönetilen bir örnek arasında farklılık gösterdiğinden ve bireysel paylaşılan makalelerdeki Azure SQL veritabanı ile Azure SQL yönetilen örneği arasındaki karmaşık nuslikleri açıklamak daha zor hale geldiği için bunu yaptık.

Farklı Azure SQL ürünleri arasında bu açıklama, Azure SQL veritabanı 'nda tek bir yönetilen veritabanı, Azure SQL veritabanı 'nda birden çok veritabanını barındıran tam olarak güvenilir bir yönetilen örnek veya Azure 'da bir sanal makinede barındırılan tanıdık şirket içi SQL Server ürünü olan Azure 'da SQL Server Veritabanı altyapısıyla çalışma sürecini basitleştirmeye ve kolaylaştırmalıdır.

Bunun devam eden bir iş olduğunu ve her makale henüz güncelleştirilmemiş olduğunu göz önünde bulundurun. Örneğin Transact-SQL (T-SQL) deyimleri, saklı yordamlar ve Azure SQL veritabanı ile Azure SQL yönetilen örneği arasında paylaşılan birçok özellik henüz tamamlanmadığından, içeriği açıklığa kavuşturmaya devam ettiğimiz için size teşekkür ederiz. 

Bu tablo, terminoloji değişikliği için hızlı bir karşılaştırma sağlar: 


|**Yeni terim**  | **Önceki dönem**  |**Açıklama** |
|---------|---------|---------|
|**Azure SQL Yönetilen Örnek** | Azure SQL veritabanı *yönetilen örneği*| Azure SQL yönetilen örneği, Azure SQL veritabanı 'nda yalnızca bir dağıtım seçeneği yerine Azure SQL ailesi içinde kendi ürünüdür. | 
|**Azure SQL Veritabanı**|Azure SQL veritabanı *tek veritabanı*| Açıkça belirtilmediği takdirde, Azure SQL veritabanı ürün adı, elastik bir havuza dağıtılan tek veritabanlarını ve veritabanlarını içerir. |
|**Azure SQL Veritabanı**|Azure SQL veritabanı *elastik havuzu*| Açıkça belirtilmediği takdirde, Azure SQL veritabanı ürün adı, elastik bir havuza dağıtılan tek veritabanlarını ve veritabanlarını içerir.  |
|**Azure SQL Veritabanı** |Azure SQL Veritabanı | Terim aynı kalır, ancak artık tek veritabanı ve elastik havuz dağıtımları için geçerli olur ve yönetilen örnek içermez. |
| **Azure SQL**| Yok | Bu, Azure 'da kullanılabilen SQL Server veritabanı motoru ürünlerinin ailesini ifade eder: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure VM 'lerinde SQL Server. | 

## <a name="features-in-public-preview"></a>Genel önizlemede Özellikler

### <a name="azure-sql-database"></a>[Azure SQL Veritabanı](#tab/single-database)

| Özellik | Ayrıntılar |
| ---| --- |
| Elastik veritabanı işleri (Önizleme) | Bilgi için bkz. [elastik Işler oluşturma, yapılandırma ve yönetme](elastic-jobs-overview.md). |
| Esnek sorgular | Daha fazla bilgi için bkz. [elastik sorguya genel bakış](elastic-query-overview.md). |
| Elastik işlemler | [Bulut veritabanları arasında dağıtılmış işlemler](elastic-transactions-overview.md). |
| Azure portal sorgu Düzenleyicisi |Bilgi için bkz. [Azure Portal SQL sorgu düzenleyicisini kullanarak bağlanma ve veri sorgulama](connect-query-portal.md).|
|SQL Analytics|Bilgi için bkz. [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md).|
| &nbsp; |

### <a name="azure-sql-managed-instance"></a>[Azure SQL Yönetilen Örnek](#tab/managed-instance)

| Özellik | Ayrıntılar |
| ---| --- |
| [Dağıtılmış işlemler](/azure/azure-sql/database/elastic-transactions-overview) | Yönetilen örnekler arasında dağıtılmış işlemler. |
| [Örnek havuzları](/azure/sql-database/sql-database-instance-pools) | Daha küçük SQL örneklerini buluta geçirmek için kullanışlı ve uygun maliyetli bir yol. |
| [Örnek düzeyi Azure AD sunucu sorumluları (oturum açmalar)](/sql/t-sql/statements/create-login-transact-sql) | [Dış sağlayıcıdan BIR oturum açma oluştur](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) ifadesiyle örnek düzeyinde oturumlar oluşturun. |
| [İşlem Çoğaltması](../managed-instance/replication-transactional-overview.md) | Tablolardaki değişiklikleri SQL yönetilen örneği, SQL veritabanı veya SQL Server diğer veritabanlarına çoğaltın. Ya da diğer SQL yönetilen örneği veya SQL Server diğer örneklerinde bazı satırlar değiştirildiğinde tablolarınızı güncelleştirin. Daha fazla bilgi için bkz. [Azure SQL yönetilen örneği 'nde çoğaltmayı yapılandırma](../managed-instance/replication-between-two-instances-configure-tutorial.md). |
| Tehdit algılama |Bilgi için bkz. [Azure SQL yönetilen örneği 'nde tehdit algılamayı yapılandırma](../managed-instance/threat-detection-configure.md).|
| Uzun süreli yedek saklama | Daha fazla bilgi için, şu anda sınırlı genel önizleme aşamasında olan [Azure SQL yönetilen örneği 'nde uzun süreli yedek saklama süresini yapılandırma](../managed-instance/long-term-backup-retention-configure.md)konusuna bakın. |

---

## <a name="new-features"></a>Yeni özellikler

### <a name="sql-managed-instance-h2-2019-updates"></a>SQL yönetilen örnek H2 2019 güncelleştirmeleri

- [Hizmet destekli alt ağ yapılandırması](https://azure.microsoft.com/updates/service-aided-subnet-configuration-for-managed-instance-in-azure-sql-database-available/) , SQL yönetilen örneği, yönetim trafiğinin kesintisiz akışını sağlarken, veri trafiğini denetlediğiniz alt ağ yapılandırmasını yönetmenin güvenli ve uygun bir yoludur.
- [Kendi anahtarını getir (BYOK) ile saydam veri şifrelemesi (TDE),](https://azure.microsoft.com/updates/general-avilability-transparent-data-encryption-with-customer-managed-keys-for-azure-sql-database-managed-instance/) bekleyen veri koruması için kendi anahtarını getır (bYok) senaryosunu sağlar ve kuruluşların anahtarlar ve veriler için yönetim görevlerini ayırmasını sağlar.
- [Otomatik yük devretme grupları](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) , birincil örnekten diğer bir bölgedeki ikincil örneğe tüm veritabanlarını çoğaltmanıza olanak sağlar.
- [Genel izleme bayrakları](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/) , SQL yönetilen örnek davranışını yapılandırmanızı sağlar.

### <a name="sql-managed-instance-h1-2019-updates"></a>SQL yönetilen örnek H1 2019 güncelleştirmeleri

Aşağıdaki özellikler, H1 2019 ' de SQL yönetilen örnek dağıtım modelinde etkinleştirilmiştir:
  - <a href="/azure/azure-sql/managed-instance/resource-limits">Visual Studio aboneleri için aylık Azure kredisi</a> ve artan [bölge sınırları](../managed-instance/resource-limits.md#regional-resource-limitations)ile abonelikler için destek.
  - <a href="/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019">Sharepoint 2016 ve sharepoint 2019</a> ve <a href="/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance">Dynamics 365 Business Central</a> için destek.
  - <a href="/azure/azure-sql/managed-instance/scripts/create-powershell-azure-resource-manager-template">Örnek düzeyi harmanlama</a> ve tercih ettiğiniz bir <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">saat dilimi</a> ile yönetilen bir örnek oluşturun.
  - Yönetilen örnekler artık [yerleşik güvenlik duvarıyla](../managed-instance/management-endpoint-verify-built-in-firewall.md)korunuyor.
  - SQL yönetilen örneğini [genel uç noktaları](../managed-instance/public-endpoint-configure.md)kullanacak şekilde yapılandırın, <a href="https://aka.ms/four-cores-sql-mi-update">5. nesil donanım oluşturma konusunda</a> daha iyi ağ performansı elde etmek için [proxy geçersiz kılma](connectivity-architecture.md#connection-policy) bağlantısı yapın veya en son nokta geri yükleme için <a href="/azure/azure-sql/database/automated-backups-overview">35 güne kadar yedekleme tutma 'yı yapılandırın</a> . [Uzun süreli yedek saklama](long-term-retention-overview.md) (10 yıla kadar) Şu anda genel önizleme aşamasındadır.  
  - Yeni işlevler, <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">PowerShell kullanarak veritabanınızı başka bir veri merkezine coğrafi olarak geri yükleme</a>, [veritabanını yeniden adlandırma](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [sanal kümeyi silme](../managed-instance/virtual-cluster-delete.md)olanağı sağlar.
  - Yeni yerleşik [örnek katılımcısı rolü](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor) , güvenlik ilkelerine ve kurumsal standartlarla uyumluluğa sahip vergi (SOD) uyumluluğuna izin verebilir.
  - SQL yönetilen örneği, aşağıdaki Azure Kamu bölgelerinde GA (US Gov Teksas, US Gov Arizona) ve Çin Kuzey 2 ve Çin Doğu 2 ' de kullanılabilir. Ayrıca, şu ortak bölgelerde de mevcuttur: Avustralya Orta, Avustralya Orta 2, Brezilya Güney, Fransa Güney, BAE Orta, BAE Kuzey, Güney Afrika Kuzey, Güney Afrika Batı.

## <a name="known-issues"></a>Bilinen sorunlar

|Sorun  |Keşfedilen Tarih  |Durum  |Çözümlenme tarihi  |
|---------|---------|---------|---------|
|[Bağlantı türünü değiştirmek, yük devretme grubu uç noktası üzerinden bağlantıları etkilemez](#changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint)|Ocak 2021|Geçici çözüm vardır||
|[Parametre kullanıldığında yordam sp_send_dbmail geçişli olarak başarısız olabilir @query](#procedure-sp_send_dbmail-may-transiently-fail-when--parameter-is-used)|Ocak 2021|Geçici çözüm vardır||
|[Dağıtılmış işlemler, sunucu güven grubundan yönetilen örnek kaldırıldıktan sonra yürütülebilir](#distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group)|Eki 2020|Geçici çözüm vardır||
|[Dağıtılmış işlemler, yönetilen örnek ölçeklendirme işleminden sonra yürütülemez](#distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation)|Eki 2020|Geçici çözüm vardır||
|[bulk INSERT](/sql/t-sql/statements/bulk-insert-transact-sql) / [](/sql/t-sql/functions/openrowset-transact-sql) Azure SQL 'de ve `BACKUP` / `RESTORE` yönetilen örnekteki deyimdeki OPENROWSET, Azure depolama 'da kimlik doğrulaması yapmak için Azure AD Yönetim kimliğini kullanamaz|Eyl 2020|Geçici çözüm vardır||
|[Hizmet sorumlusu Azure AD 'ye ve AKV 'ye erişemiyor](#service-principal-cannot-access-azure-ad-and-akv)|Ağu 2020|Geçici çözüm vardır||
|[SAĞLAMA TOPLAMı olmadan el ile yedeklemenin geri yüklenmesi başarısız olabilir](#restoring-manual-backup-without-checksum-might-fail)|Mayıs 2020|Çözümlendi|Haziran 2020|
|[Aracı, mevcut işleri değiştirme, devre dışı bırakma veya etkinleştirme sırasında yanıt vermemeye başladı](#agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs)|Mayıs 2020|Çözümlendi|Haziran 2020|
|[Kaynak grubundaki izinler SQL yönetilen örneği 'ne uygulanmadı](#permissions-on-resource-group-not-applied-to-sql-managed-instance)|Şub 2020|Çözümlendi|Kas 2020|
|[Yük devretme grupları için Portal aracılığıyla el ile yük devretme sınırlaması](#limitation-of-manual-failover-via-portal-for-failover-groups)|Ocak 2020|Geçici çözüm vardır||
|[SQL Server Agent rolleri, sysadmin dışındaki oturum açma bilgileri için açık EXECUTE izinlerine ihtiyaç duyar](#in-memory-oltp-memory-limits-are-not-applied)|Dec 2019|Geçici çözüm vardır||
|[SQL Aracısı işleri, aracı işleminin yeniden başlatılmasına göre kesintiye uğrar](#sql-agent-jobs-can-be-interrupted-by-agent-process-restart)|Dec 2019|Çözümlendi|Mar 2020|
|[SSDT 'de Azure AD oturum açmaları ve kullanıcılar desteklenmez](#azure-ad-logins-and-users-are-not-supported-in-ssdt)|Kas 2019|Geçici çözüm yok||
|[Bellek içi OLTP bellek sınırları uygulanmadı](#in-memory-oltp-memory-limits-are-not-applied)|Eki 2019|Geçici çözüm vardır||
|[Boş olmayan bir dosya kaldırılmaya çalışılırken hatalı hata döndürüldü](#wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty)|Eki 2019|Geçici çözüm vardır||
|[Hizmet katmanını değiştirme ve örnek oluşturma işlemleri sürekli veritabanı geri yükleme tarafından engelleniyor](#change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore)|Eyl 2019|Geçici çözüm vardır||
|[İş Açısından Kritik hizmet katmanındaki Resource Governor yük devretmeden sonra yeniden yapılandırılması gerekebilir](#resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover)|Eyl 2019|Geçici çözüm vardır||
|[Çapraz veritabanı Hizmet Aracısı iletişim kutuları, hizmet katmanı yükseltmesinden sonra yeniden başlatılmalıdır](#cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade)|Ağu 2019|Geçici çözüm vardır||
|[Azure AD oturum açma türleri için kimliğe bürünme desteklenmiyor](#impersonation-of-azure-ad-login-types-is-not-supported)|2019 Tem|Geçici çözüm yok||
|[@query sp_send_db_mail içinde parametre desteklenmiyor](#-parameter-not-supported-in-sp_send_db_mail)|2019 Nis|Çözümlendi|Ocak 2021|
|[İşlemsel çoğaltmanın coğrafi Yük devretme sonrasında yeniden yapılandırılması gerekir](#transactional-replication-must-be-reconfigured-after-geo-failover)|Mar 2019|Geçici çözüm yok||
|[GERI yükleme işlemi sırasında geçici veritabanı kullanılıyor](#temporary-database-is-used-during-restore-operation)||Geçici çözüm vardır||
|[TEMPDB yapısı ve içerik yeniden oluşturuluyor](#tempdb-structure-and-content-is-re-created)||Geçici çözüm yok||
|[Küçük veritabanı dosyalarıyla depolama alanını aşma](#exceeding-storage-space-with-small-database-files)||Geçici çözüm vardır||
|[Veritabanı adları yerine gösterilen GUID değerleri](#guid-values-shown-instead-of-database-names)||Geçici çözüm vardır||
|[Hata günlükleri kalıcı değil](#error-logs-arent-persisted)||Geçici çözüm yok||
|[Aynı örnek içindeki iki veritabanında işlem kapsamı desteklenmiyor](#transaction-scope-on-two-databases-within-the-same-instance-isnt-supported)||Geçici çözüm vardır|Mar 2020|
|[CLR modülleri ve bağlı sunucular bazen yerel bir IP adresine başvuramaz](#clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address)||Geçici çözüm vardır||
|Veritabanı tutarlılığı, Azure Blob depolamadan geri yükleme sonrasında DBCC CHECKDB kullanılarak doğrulanmadı.||Çözümlendi|Kas 2019|
|Kaynak veritabanında bellek içi OLTP nesneleri varsa, İş Açısından Kritik katmanından Genel Amaçlı katmana geri yükleme işlemi başarılı olmaz.||Çözümlendi|Eki 2019|
|Güvenli bağlantı kullanan harici (Azure dışı) posta sunucularıyla veritabanı posta özelliği||Çözümlendi|Eki 2019|
|Kapsanan veritabanları SQL yönetilen örneği 'nde desteklenmiyor||Çözümlendi|Ağu 2019|

### <a name="changing-the-connection-type-does-not-affect-connections-through-the-failover-group-endpoint"></a>Bağlantı türünü değiştirmek, yük devretme grubu uç noktası üzerinden bağlantıları etkilemez

Bir örnek [otomatik yük devretme grubuna](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview)katılıyorsa, örneğin [bağlantı türünü](https://docs.microsoft.com/azure/azure-sql/managed-instance/connection-types-overview) değiştirmek, yük devretme grubu dinleyicisi uç noktası aracılığıyla kurulan bağlantılar için geçerli olmaz.

**Geçici çözüm**: bağlantı türünü değiştirerek otomatik yük devretme grubunu bırakın ve yeniden oluşturun.

### <a name="procedure-sp_send_dbmail-may-transiently-fail-when-query-parameter-is-used"></a>Parametre kullanıldığında yordam sp_send_dbmail geçişli olarak başarısız olabilir @query

Parametre kullanıldığında yordam sp_send_dbmail geçişli hale gelebilir `@query` . Bu sorun oluştuğunda, yordam sp_send_dbmail her ikinci yürütmesi hata ve iletiyle başarısız olur `Msg 22050, Level 16, State 1` `Failed to initialize sqlcmd library with error number -2147467259` . Bu hatayı doğru bir şekilde görebilmek için, yordam parametre için varsayılan 0 değeri ile çağrılmalıdır `@exclude_query_output` , aksi halde hata yayılmaz.
Bu sorun, sp_send_dbmail kimliğe bürünme ve bağlantı havuzu kullanma ile ilgili bilinen bir hata nedeniyle oluşur.
Bu sorunu geçici olarak çözmek için, çıkış parametresine dayanan bir yeniden deneme mantığına e-posta göndermek için sarmalama kodu `@mailitem_id` . Yürütme başarısız olursa parametre değeri NULL olur ve bir e-postayı başarıyla göndermek için sp_send_dbmail bir kez daha çağrılmalıdır. Bu yeniden deneme mantığı örneği aşağıda verilmiştir.
```sql
CREATE PROCEDURE send_dbmail_with_retry AS
BEGIN
    DECLARE @miid INT
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
        @mailitem_id = @miid OUTPUT

    -- If sp_send_dbmail returned NULL @mailidem_id then retry sending email.
    --
    IF (@miid is NULL)
    EXEC msdb.dbo.sp_send_dbmail
        @recipients = 'name@mail.com', @subject = 'Subject', @query = 'select * from dbo.test_table',
        @profile_name ='AzureManagedInstance_dbmail_profile', @execute_query_database = 'testdb',
END
```

### <a name="distributed-transactions-can-be-executed-after-removing-managed-instance-from-server-trust-group"></a>Dağıtılmış işlemler, sunucu güven grubundan yönetilen örnek kaldırıldıktan sonra yürütülebilir

[Sunucu güven grupları](../managed-instance/server-trust-group-overview.md) , [dağıtılmış işlemleri](./elastic-transactions-overview.md)yürütmek Için önkoşul olan yönetilen örnekler arasında güven oluşturmak için kullanılır. Yönetilen örneği sunucu güven grubundan kaldırdıktan veya grubu sildikten sonra, dağıtılmış işlemleri yürütebiliyor olabilirsiniz. Dağıtılmış işlemlerin devre dışı bırakıldığından ve yönetilen örnek üzerinde [Kullanıcı tarafından başlatılan el ile yük devretme](../managed-instance/user-initiated-failover.md) olduğundan emin olmak için uygulayabileceğiniz bir geçici çözüm vardır.

### <a name="distributed-transactions-cannot-be-executed-after-managed-instance-scaling-operation"></a>Dağıtılmış işlemler, yönetilen örnek ölçeklendirme işleminden sonra yürütülemez

Hizmet katmanını veya sanal çekirdek sayısını değiştiren yönetilen örnek ölçekleme işlemleri arka uçta sunucu güven grubu ayarlarını sıfırlar ve [dağıtılmış işlemleri](./elastic-transactions-overview.md)çalıştırmayı devre dışı bırakır. Geçici bir çözüm olarak, Azure portal yeni [sunucu güveni grubunu](../managed-instance/server-trust-group-overview.md) silin ve oluşturun.

### <a name="bulk-insert-and-backuprestore-statements-cannot-use-managed-identity-to-access-azure-storage"></a>BULK INSERT ve yedekleme/GERI yükleme deyimleri, Azure depolama 'ya erişmek için yönetilen kimlik kullanamaz

Toplu ekleme, yedekleme ve GERI yükleme deyimleri ve OPENROWSET işlevi, `DATABASE SCOPED CREDENTIAL` Azure depolama 'da kimlik doğrulaması yapmak Için yönetilen kimlikle birlikte kullanılamaz. Geçici bir çözüm olarak, PAYLAŞıLAN ERIŞIM IMZASı kimlik doğrulaması ' na geçin. Aşağıdaki örnek Azure SQL 'de (hem veritabanı hem de yönetilen örnek) çalışmaz:

```sql
CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Identity';
GO
CREATE EXTERNAL DATA SOURCE MyAzureBlobStorage
  WITH ( TYPE = BLOB_STORAGE, LOCATION = 'https://****************.blob.core.windows.net/curriculum', CREDENTIAL= msi_cred );
GO
BULK INSERT Sales.Invoices FROM 'inv-2017-12-08.csv' WITH (DATA_SOURCE = 'MyAzureBlobStorage');
```

**Geçici çözüm**: [depolama için kimlik doğrulaması yapmak üzere paylaşılan erişim imzasını](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage)kullanın.

### <a name="service-principal-cannot-access-azure-ad-and-akv"></a>Hizmet sorumlusu Azure AD 'ye ve AKV 'ye erişemiyor

Bazı durumlarda, Azure AD ve Azure Key Vault (AKV) hizmetlerine erişmek için kullanılan hizmet sorumlusu ile ilgili bir sorun var olabilir. Sonuç olarak, bu sorun SQL yönetilen örneği ile birlikte Azure AD kimlik doğrulaması ve saydam veritabanı şifrelemesi (TDE) kullanımını etkiler. Bu durum aralıklı bir bağlantı sorunu veya dış SAĞLAYıCıDAN oturum açma/Kullanıcı oluşturma ya da oturum açma/kullanıcı olarak yürütme gibi deyimleri çalıştırabilmeyebilir. Yeni bir Azure SQL yönetilen örneği üzerinde, müşteri tarafından yönetilen anahtarla TDE ayarlama, bazı durumlarda da çalışmayabilir.

**Geçici çözüm**: herhangi bir güncelleştirme komutunu YÜRÜTMEDEN önce SQL yönetilen Örneğinizde bu sorunun oluşmasını önlemek için veya güncelleştirme komutlarından sonra bu sorunla karşılaştıysanız, Azure Portal gıdın, SQL yönetilen örneği [Active Directory Yönetim dikey penceresine](./authentication-aad-configure.md?tabs=azure-powershell#azure-portal)erişin. "Yönetilen örnek 'in Azure Active Directory erişmek için bir hizmet sorumlusu olması gerekir" hata iletisini görebildiğinizi doğrulayın. Hizmet sorumlusu oluşturmak için buraya tıklayın. Bu hata iletisiyle karşılaştıysanız, üzerine tıklayın ve bu hata çözümlenene kadar sunulan adım adım yönergeleri izleyin.

### <a name="restoring-manual-backup-without-checksum-might-fail"></a>SAĞLAMA TOPLAMı olmadan el ile yedeklemenin geri yüklenmesi başarısız olabilir

Bazı durumlarda, yönetilen bir örnek üzerinde sağlama TOPLAMı olmadan gerçekleştirilen veritabanlarının el ile yedeklenmesi geri yüklenemeyebilir. Bu gibi durumlarda, başarılı olana kadar yedeklemeyi geri yüklemeyi yeniden deneyin.

**Geçici çözüm**: sağlama toplamı etkinken yönetilen örneklerde veritabanlarının el ile yedeklerini alın.

### <a name="agent-becomes-unresponsive-upon-modifying-disabling-or-enabling-existing-jobs"></a>Aracı, mevcut işleri değiştirme, devre dışı bırakma veya etkinleştirme sırasında yanıt vermemeye başladı

Belirli durumlarda, var olan bir işi değiştirmek, devre dışı bırakmak veya etkinleştirmek, aracının yanıt vermemesine neden olabilir. Sorun algılama sonrasında otomatik olarak azaltıldığında, aracı işleminin yeniden başlatılmasına neden olur.

### <a name="permissions-on-resource-group-not-applied-to-sql-managed-instance"></a>Kaynak grubundaki izinler SQL yönetilen örneği 'ne uygulanmadı

SQL yönetilen örneği katkıda bulunan Azure rolü bir kaynak grubuna (RG) uygulandığında, SQL yönetilen örneğine uygulanmaz ve hiçbir etkisi olmaz.

**Geçici çözüm**: kullanıcılar için abonelik DÜZEYINDE bir SQL yönetilen örnek katılımcısı rolü ayarlayın.

### <a name="limitation-of-manual-failover-via-portal-for-failover-groups"></a>Yük devretme grupları için Portal aracılığıyla el ile yük devretme sınırlaması

Yük devretme grubu farklı Azure aboneliklerindeki veya kaynak gruplarındaki örneklere yayılmışsa, yük devretme grubundaki birincil örnekten el ile yük devretme başlatılamaz.

**Geçici çözüm**: coğrafi ikincil örnekten Portal aracılığıyla yük devretmeyi başlatın.

### <a name="sql-agent-roles-need-explicit-execute-permissions-for-non-sysadmin-logins"></a>SQL Server Agent rolleri, sysadmin dışındaki oturum açma bilgileri için açık EXECUTE izinlerine ihtiyaç duyar

Sysadmin olmayan oturumlar herhangi bir [SQL Agent sabit veritabanı rolüne](/sql/ssms/agent/sql-server-agent-fixed-database-roles)eklenirse, bu oturumların çalışması için ana saklı YORDAMLARA açık yürütme izinlerinin verilmesi gereken bir sorun vardır. Bu sorunla karşılaşılırsa, "nesne üzerinde yürütme izni reddedildi <object_name> (Microsoft SQL Server, hata: 229)" hata mesajını görürsünüz.

**Geçici çözüm**: bir SQL Aracısı sabit veritabanı rolüne (SQLAgentUserRole, SQLAgentReaderRole veya da SQLAgentOperatorRole rolünün) oturum açma işlemleri yaptıktan sonra, bu rollere eklenen her oturum açma için, listelenen saklı YORDAMLARA açıkça yürütme izinleri vermek üzere aşağıdaki T-SQL betiğini yürütün.

```tsql
USE [master]
GO
CREATE USER [login_name] FOR LOGIN [login_name]
GO
GRANT EXECUTE ON master.dbo.xp_sqlagent_enum_jobs TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_is_starting TO [login_name]
GRANT EXECUTE ON master.dbo.xp_sqlagent_notify TO [login_name]
```

### <a name="sql-agent-jobs-can-be-interrupted-by-agent-process-restart"></a>SQL Aracısı işleri, aracı işleminin yeniden başlatılmasına göre kesintiye uğrar

**(Mart 2020 ' de çözümlendi)** SQL Aracısı, her iş başlatıldığında yeni bir oturum oluşturur ve bellek tüketimini kademeli olarak artırır. Zamanlanan işlerin yürütülmesini engelleyebilen iç bellek sınırına ulaşmaktan kaçınmak için, bellek tüketimi eşiğe ulaştığında Aracı işlemi yeniden başlatılır. Yeniden başlatma sırasında çalışan işlerin yürütülmesini kesintiye neden olabilir.

### <a name="in-memory-oltp-memory-limits-are-not-applied"></a>Bellek içi OLTP bellek sınırları uygulanmadı

İş Açısından Kritik hizmet katmanı, bazı durumlarda [bellek için iyileştirilmiş nesneler için maksimum bellek sınırlarını](../managed-instance/resource-limits.md#in-memory-oltp-available-space) doğru bir şekilde uygulamacaktır. SQL yönetilen örneği, iş yükünün bellek içi OLTP işlemleri için daha fazla bellek kullanmasını sağlayabilir ve bu da örneğin kullanılabilirliğini ve kararlılığını etkileyebilir. Sınırlara ulaşan bellek içi OLTP sorguları hemen başarısız olmayabilir. Bu sorun yakında düzeltilecektir. Daha fazla bellek içi OLTP belleği kullanan sorgular [sınırlara](../managed-instance/resource-limits.md#in-memory-oltp-available-space)ulaştıklarında daha önce başarısız olur.

**Geçici çözüm**: iş yükünün kullanılabilir bellekten daha fazlasını kullandığından emin olmak için [SQL Server Management Studio](/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage#bkmk_Monitoring) kullanarak [bellek içi OLTP depolama kullanımını izleyin](../in-memory-oltp-monitor-space.md) . Sanal çekirdek sayısına bağlı olan bellek sınırlarını artırın veya daha az bellek kullanmak için iş yükünüzü iyileştirin.
 
### <a name="wrong-error-returned-while-trying-to-remove-a-file-that-is-not-empty"></a>Boş olmayan bir dosya kaldırılmaya çalışılırken hatalı hata döndürüldü

SQL Server ve SQL yönetilen örneği [, kullanıcının boş olmayan bir dosyayı bırakmaya izin vermez](/sql/relational-databases/databases/delete-data-or-log-files-from-a-database#Prerequisites). Bir deyimleri kullanarak boş olmayan bir veri dosyasını kaldırmaya çalışırsanız `ALTER DATABASE REMOVE FILE` , hata `Msg 5042 – The file '<file_name>' cannot be removed because it is not empty` hemen döndürülmeyecektir. SQL yönetilen örneği dosyayı bırakmaya devam eder ve 30 dakika sonra işlem başarısız olur `Internal server error` .

**Geçici çözüm**: komutunu kullanarak dosyanın içeriğini kaldırın `DBCC SHRINKFILE (N'<file_name>', EMPTYFILE)` . Dosya grubundaki tek dosya ise, dosyayı daralmadan önce bu dosya grubuyla ilişkili tablodan veya bölümden verileri silmeniz ve isteğe bağlı olarak bu verileri başka bir tabloya/bölüme yüklemeniz gerekir.

### <a name="change-service-tier-and-create-instance-operations-are-blocked-by-ongoing-database-restore"></a>Hizmet katmanını değiştirme ve örnek oluşturma işlemleri sürekli veritabanı geri yükleme tarafından engelleniyor

Devam eden `RESTORE` bildirim, veri geçiş hizmeti geçiş işlemi ve yerleşik bir noktadan sonra geri yükleme, bir hizmet katmanını güncelleştirmeyi veya mevcut örneği yeniden boyutlandırmayı ve geri yükleme işlemi tamamlanana kadar yeni örnekler oluşturmayı engeller. 

Geri yükleme işlemi, bu işlemleri, geri yükleme işleminin çalıştırıldığı aynı alt ağdaki yönetilen örneklerde ve örnek havuzlarda engeller. Örnek havuzlardaki örnekler etkilenmez. Hizmet katmanı işlemleri oluşturma veya değiştirme işlemi başarısız olmayacak veya zaman aşımına uğrar. Geri yükleme işlemi tamamlandıktan veya iptal edildikten sonra devam eder.

**Geçici çözüm**: geri yükleme işlemi tamamlanana kadar bekleyin veya oluşturma veya güncelleştirme-hizmet katmanı işlemi daha yüksek önceliğe sahipse geri yükleme işlemini iptal edin.

### <a name="resource-governor-on-business-critical-service-tier-might-need-to-be-reconfigured-after-failover"></a>İş Açısından Kritik hizmet katmanındaki Resource Governor yük devretmeden sonra yeniden yapılandırılması gerekebilir

Kullanıcı iş yüküne atanan kaynakları sınırlandırmanızı sağlayan [Resource Governor](/sql/relational-databases/resource-governor/resource-governor) özelliği, yük devretmeden sonra bazı Kullanıcı iş yükünü veya hizmet katmanının Kullanıcı tarafından başlatılan değişikliğini yanlış sınıflandırabilir (örneğin, en büyük Vcore veya en büyük örnek depolama boyutu değişikliği).

**Geçici çözüm**: `ALTER RESOURCE GOVERNOR RECONFIGURE` [Resource Governor](/sql/relational-databases/resource-governor/resource-governor)kullanıyorsanız, örnek başlatıldığında SQL görevini yürüten SQL Aracısı işinin BIR parçası olarak veya bir SQL Agent işinin parçası olarak çalıştırın.

### <a name="cross-database-service-broker-dialogs-must-be-reinitialized-after-service-tier-upgrade"></a>Çapraz veritabanı Hizmet Aracısı iletişim kutuları, hizmet katmanı yükseltmesinden sonra yeniden başlatılmalıdır

Çapraz veritabanı Hizmet Aracısı iletişim kutuları, hizmet katmanı işlemini değiştirdikten sonra iletileri diğer veritabanlarındaki hizmetlere teslim eder. İletiler *kaybolmaz* ve gönderici kuyruğunda bulunabilir. SQL yönetilen örneğindeki sanal çekirdekler veya örnek depolama boyutu değişiklikleri `service_broke_guid` [sys. databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql) görünümündeki bir değerin tüm veritabanları için değiştirilmesine neden olur. `DIALOG`Diğer veritabanındaki hizmet aracılarına başvuran [BEGIN iletişim kutusu](/sql/t-sql/statements/begin-dialog-conversation-transact-sql) kullanılarak oluşturulan her türlü, hedef hizmete ileti teslimi durdurulur.

**Geçici çözüm**: hizmet katmanını güncelleştirmeden önce veritabanları arası hizmet Aracısı iletişim kutusu konuşmaları kullanan tüm etkinlikleri durdurun ve daha sonra yeniden başlatın. Bir hizmet katmanı değişikliğinden sonra teslim edilmemiş kalan iletiler varsa, kaynak kuyruktaki iletileri okuyun ve hedef sıraya yeniden gönderin.

### <a name="impersonation-of-azure-ad-login-types-is-not-supported"></a>Azure AD oturum açma türleri için kimliğe bürünme desteklenmiyor

`EXECUTE AS USER` `EXECUTE AS LOGIN` Şu Azure Active Directory (Azure AD) sorumlularını veya kullanarak kimliğe bürünme desteklenmez:
-   Diğer ad Azure AD kullanıcıları. Bu durumda şu hata döndürülür: `15517` .
- Azure AD oturum açmaları ve Azure AD uygulamalarına veya hizmet sorumlularına göre kullanıcılar. Bu durumda aşağıdaki hatalar döndürülür: `15517` ve `15406` .

### <a name="query-parameter-not-supported-in-sp_send_db_mail"></a>@query sp_send_db_mail içinde parametre desteklenmiyor

`@query` [Sp_send_db_mail](/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) yordamındaki parametresi çalışmıyor.

### <a name="transactional-replication-must-be-reconfigured-after-geo-failover"></a>İşlemsel çoğaltmanın coğrafi Yük devretme sonrasında yeniden yapılandırılması gerekir

Bir otomatik yük devretme grubundaki bir veritabanında Işlem çoğaltması etkinleştirilmişse, SQL yönetilen örnek Yöneticisi eski birincil üzerindeki tüm yayınları temizlemeli ve başka bir bölgeye yük devretme gerçekleştikten sonra yeni birincil üzerinde yeniden yapılandırmalıdır. Daha fazla bilgi için bkz. [çoğaltma](../managed-instance/transact-sql-tsql-differences-sql-server.md#replication).

### <a name="azure-ad-logins-and-users-are-not-supported-in-ssdt"></a>SSDT 'de Azure AD oturum açmaları ve kullanıcılar desteklenmez

SQL Server Veri Araçları Azure AD oturum açmaları ve kullanıcılarını tam olarak desteklemez.

### <a name="temporary-database-is-used-during-restore-operation"></a>GERI yükleme işlemi sırasında geçici veritabanı kullanılıyor

Bir veritabanı SQL yönetilen örneği 'ne geri yüklerken, geri yükleme hizmeti öncelikle adı örnek üzerinde ayırmak için istenen ada sahip boş bir veritabanı oluşturur. Bir süre sonra, bu veritabanı bırakılır ve gerçek veritabanının geri yüklenmesi başlatılır. 

*Geri yükleme* durumundaki veritabanının adı yerine geçici olarak rastgele bir GUID değeri olur. Geri yükleme işlemi tamamlandıktan sonra geçici ad, bildiriminde belirtilen istenen adla değiştirilecektir `RESTORE` . 

İlk aşamada, bir kullanıcı boş veritabanına erişebilir ve hatta tablo oluşturabilir veya bu veritabanında veri yükleyebilir. Bu geçici veritabanı, geri yükleme hizmeti ikinci aşamayı başlattığında bırakılacak.

**Geçici çözüm**: geri yükleme işleminin tamamlandığını görene kadar geri yüklemekte olduğunuz veritabanına erişmeyin.

### <a name="tempdb-structure-and-content-is-re-created"></a>TEMPDB yapısı ve içerik yeniden oluşturuluyor

`tempdb`Veritabanı her zaman 12 veri dosyasına bölünür ve dosya yapısı değiştirilemez. Dosya başına en büyük boyut değiştirilemez ve yeni dosyalar ' a eklenemez `tempdb` . `Tempdb` örnek başlatıldığında veya başarısız olduğunda her zaman boş bir veritabanı olarak yeniden oluşturulur ve ' de yapılan tüm değişiklikler `tempdb` korunmaz.

### <a name="exceeding-storage-space-with-small-database-files"></a>Küçük veritabanı dosyalarıyla depolama alanını aşma

`CREATE DATABASE``ALTER DATABASE ADD FILE` `RESTORE DATABASE` örnek, Azure depolama sınırına ulaşabileceğinden,, ve deyimleri başarısız olabilir.

SQL yönetilen örneğinin her bir Genel Amaçlı örneği, Azure Premium disk alanı için ayrılmış 35 TB 'a kadar depolama alanı içerir. Her veritabanı dosyası ayrı bir fiziksel diske yerleştirilir. Disk boyutları 128 GB, 256 GB, 512 GB, 1 TB veya 4 TB olabilir. Diskteki kullanılmayan alan ücretlendirilmez, ancak Azure Premium disk boyutlarının toplam toplamı 35 TB 'yi aşamaz. Bazı durumlarda, toplam olarak 8 TB 'lık bir yönetilen örnek, iç parçalanma nedeniyle depolama boyutu 35 TB Azure sınırını aşabilir.

Örneğin, bir SQL yönetilen örneğinin Genel Amaçlı örneğinde, 4 TB 'lik bir diske yerleştirilmiş Boyut 1,2 TB olan bir büyük dosya olabilir. Ayrıca, her biri 1 GB olan ve ayrı 128 GB disklere yerleştirilmiş 248 dosya olabilir. Bu örnekte:

- Ayrılan toplam disk depolama boyutu 1 x 4 TB + 248 x 128 GB = 35 TB 'tır.
- Örnekteki veritabanları için ayrılan toplam alan 1 x 1,2 TB + 248 x 1 GB = 1,4 TB 'tır.

Bu örnekte, belirli bir dosya dağıtımı nedeniyle, bir SQL yönetilen örneği örneğinin, bağlı bir Azure Premium diski için ayrılan 35 TB sınırına ulaşamamanıza neden olduğunu gösteren bir SQL yönetilen örnek örneği gösterilmektedir.

Bu örnekte, mevcut veritabanları çalışmaya devam eder ve yeni dosyalar eklenmedikçe herhangi bir sorun olmadan büyüyebilir. Tüm veritabanlarının toplam boyutu örnek boyut sınırına ulaşmasa bile yeni disk sürücüleri için yeterli alan olmadığından yeni veritabanları oluşturulamaz veya geri yüklenemez. Bu durumda döndürülen hata net değildir.

[Kalan dosyaların sayısını](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) sistem görünümlerini kullanarak belirleyebilirsiniz. Bu sınıra ulaştıysanız, [DBCC SHRINKFILE ifadesini kullanarak daha küçük bir dosyayı boş ve silmeyi](/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) deneyin veya [Bu sınıra sahip olmayan iş açısından kritik katmanına](../managed-instance/resource-limits.md#service-tier-characteristics)geçin.

### <a name="guid-values-shown-instead-of-database-names"></a>Veritabanı adları yerine gösterilen GUID değerleri

Çeşitli sistem görünümleri, performans sayaçları, hata iletileri, XEvents ve hata günlüğü girdileri, gerçek veritabanı adları yerine GUID veritabanı tanımlayıcılarını görüntüler. Gelecekte gerçek veritabanı adlarıyla değiştirildiklerinden, bu GUID tanımlayıcılarına güvenmeyin.

**Geçici çözüm**: GUID veritabanı tanımlayıcıları biçiminde belirtilen fiziksel veritabanı adından gerçek veritabanı adını çözümlemek için sys. databases görünümünü kullanın:

```tsql
SELECT name as ActualDatabaseName, physical_database_name as GUIDDatabaseIdentifier 
FROM sys.databases
WHERE database_id > 4
```

### <a name="error-logs-arent-persisted"></a>Hata günlükleri kalıcı değil

SQL yönetilen örneği 'nde kullanılabilen hata günlükleri kalıcı değil ve boyutu en fazla depolama sınırına dahil değildir. Yük devretme gerçekleşirse hata günlükleri otomatik olarak silinebilir. SQL yönetilen örnek birkaç sanal makinede birkaç kez taşındığından, hata günlüğü geçmişinde boşluklar olabilir.

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>Aynı örnek içindeki iki veritabanında işlem kapsamı desteklenmiyor

**(Mart 2020 ' de çözümlendi)** `TransactionScope` Aynı işlem kapsamında aynı örnek içindeki iki veritabanına iki sorgu gönderilirse .net 'teki sınıfı çalışmaz:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

**Geçici çözüm (2020 Mart 'tan beri gerekmez)**: iki bağlantı kullanmak yerine başka bir veritabanını bir bağlantı bağlamında kullanmak için [SqlConnection. ChangeDatabase (dize)](/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) kullanın.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>CLR modülleri ve bağlı sunucular bazen yerel bir IP adresine başvuramaz

SQL yönetilen örneği ve bağlı sunuculardaki CLR modülleri veya geçerli örneğe başvuran dağıtılmış sorgular bazen yerel bir örneğin IP 'sini çözemez. Bu hata, geçici bir sorundur.

**Geçici çözüm**: mümkünse bir clr modülünde bağlam bağlantılarını kullanın.

## <a name="updates"></a>Güncelleştirmeler

SQL veritabanı güncelleştirmelerinin ve geliştirmelerin bir listesi için bkz. [SQL Database Service Updates](https://azure.microsoft.com/updates/?product=sql-database).

Tüm Azure hizmetleri için güncelleştirmeler ve geliştirmeler için bkz. [hizmet güncelleştirmeleri](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>İçeriğe katkıda bulunma

Azure SQL belgelerine katkıda bulunmak için [docs katılımcısı Kılavuzu](/contribute/)' na bakın.
