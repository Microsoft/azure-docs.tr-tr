---
title: Müşteri tarafından yönetilen saydam veri şifrelemesi (TDE)
description: Kendi Anahtarını Getir (BYOK) SQL veritabanı ve Azure SYNAPSE Analytics için Azure Key Vault ile Saydam Veri Şifrelemesi (TDE) desteği. BYOK 'a genel bakış, avantajlar, nasıl çalıştığı, önemli noktalar ve öneriler ile TDE.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019, azure-synapse
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/01/2021
ms.openlocfilehash: b3403558cbc07d152bbae7e901464a8aa4a8e4d2
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812777"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Müşteri tarafından yönetilen anahtarla Azure SQL Saydam Veri Şifrelemesi
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Müşteri tarafından yönetilen anahtarla Azure SQL [Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) , bekleyen veri koruması için kendi anahtarını getir (bYok) senaryosunu sağlar ve kuruluşların anahtar ve veri yönetiminde görevlerin ayrılmasını sağlar. Müşteri tarafından yönetilen saydam veri şifrelemesi ile, müşteri, anahtar yaşam döngüsü yönetimi (anahtar oluşturma, karşıya yükleme, döndürme, silme), anahtar kullanımı izinleri ve anahtarların üzerinde işlemler denetimi hakkında tam bir denetim üzerinden ve bunların sorumluluğundadır.

Bu senaryoda, TDE koruyucusu olarak adlandırılan veritabanı şifreleme anahtarı (DEK) şifrelemesi için kullanılan anahtar, bulut tabanlı bir dış anahtar yönetim sistemi olan müşterinin sahip olduğu ve müşteri tarafından yönetilen [Azure Key Vault (AKV)](../../key-vault/general/security-features.md)içinde depolanan, müşteri tarafından yönetilen bir asimetrik anahtardır. Key Vault, isteğe bağlı olarak FIPS 140-2 düzey 2 tarafından doğrulanan donanım güvenlik modülleri (HSM 'ler) tarafından desteklenen, RSA şifreleme anahtarları için yüksek düzeyde kullanılabilir ve ölçeklenebilir güvenli depolama alanı. Depolanan bir anahtara doğrudan erişime izin vermez, ancak yetkili varlıkların anahtarını kullanarak şifreleme/şifre çözme hizmetleri sağlar. Anahtar, Anahtar Kasası tarafından oluşturulabilir, içeri aktarılabilir veya [bir şirket ıçı HSM cihazından anahtar kasasına aktarılabilir](../../key-vault/keys/hsm-protected-keys.md).

Azure SQL veritabanı ve Azure SYNAPSE Analytics için, TDE koruyucusu sunucu düzeyinde ayarlanır ve bu sunucuyla ilişkili olan tüm şifreli veritabanları tarafından devralınır. Azure SQL yönetilen örneği için, TDE koruyucusu örnek düzeyinde ayarlanır ve bu örnekteki tüm şifreli veritabanları tarafından devralınır. *Sunucu* TERIMI hem SQL veritabanı hem de Azure SYNAPSE ' deki bir sunucuya ve farklı belirtilmedikçe, bu belge boyunca SQL yönetilen örneğindeki yönetilen bir örneğe başvurur.

> [!IMPORTANT]
> Müşteri tarafından yönetilen TDE kullanmaya başlamak isteyen hizmet tarafından yönetilen TDE kullanan bu işlemler için veriler, geçiş işlemi sırasında şifrelenmiş olarak kalır ve veritabanı dosyalarının kapalı kalması veya yeniden şifrelenmesi gerekmez. Hizmet tarafından yönetilen anahtardan müşterinin yönettiği bir anahtara geçiş yapmak, hızlı ve çevrimiçi bir işlem olan DEK ' ın yeniden şifrelenmesini gerektirir.

> [!NOTE]
> <a id="doubleencryption"></a> Azure SQL müşterilerinin bekleyen verilerin iki katmanını kullanmasını sağlamak için platform tarafından yönetilen anahtarlarla altyapı şifrelemesi (AES-256 şifreleme algoritması kullanılarak) kullanıma alınıyor. Bu, zaten mevcut olan, müşteri tarafından yönetilen anahtarlarla birlikte, bekleyen bir şifreleme katmanını de sağlar. Azure SQL veritabanı ve yönetilen örnek için, altyapı şifrelemesi açık olduğunda, ana veritabanı ve diğer sistem veritabanları dahil olmak üzere tüm veritabanları şifrelenir. Şu anda müşterilerin bu özelliğe erişim istemesi gerekir. Bu özellik ile ilgileniyorsanız, iletişim kurun AzureSQLDoubleEncryptionAtRest@service.microsoft.com .

## <a name="benefits-of-the-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE 'nın avantajları

Müşteri tarafından yönetilen TDE müşteri için aşağıdaki avantajları sağlar:

- TDE koruyucunun kullanımı ve yönetimi üzerinde tam ve ayrıntılı denetim;

- TDE koruyucusu kullanımının saydamlığı;

- Kuruluş içindeki anahtar ve verilerin yönetiminde görev ayrımı uygulama yeteneği;

- Key Vault Yöneticisi şifrelenmiş veritabanını erişilemez hale getirmek için anahtar erişim izinlerini iptal edebilir;

- AKV; içindeki anahtarların Merkezi Yönetimi

- Microsoft 'un şifreleme anahtarlarını göremediği ve ayıklayamayacağı gibi, bu nedenle son müşterilerinizin sunduğu daha fazla güven;

## <a name="how-customer-managed-tde-works"></a>Müşteri tarafından yönetilen TDE nasıl çalıştığı

![Müşteri tarafından yönetilen TDE 'nın kurulumu ve çalışması](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-roles.PNG)

Sunucunun, 1. & lt; 1} içinde depolanan TDE koruyucusunu kullanabilmesi için, Anahtar Kasası yöneticisinin benzersiz Azure Active Directory (Azure AD) kimliğini kullanarak sunucuya aşağıdaki erişim haklarını vermesi gerekir:

- Key Vault, anahtarın genel bölümünü ve **özelliklerini alma**

- **wrapKey** -koruyabilmek için (ŞIFRELEME) dek

- **unwrapKey** -korumayı kaldırmak için (şifre çözme) dek

Anahtar Kasası Yöneticisi ayrıca [Anahtar Kasası denetim olaylarının günlüğe kaydedilmesini etkinleştirerek](../../azure-monitor/insights/key-vault-insights-overview.md)daha sonra denetlenebilir.

Sunucu, AKV 'den bir TDE koruyucusu kullanacak şekilde yapılandırıldığında, sunucu her bir TDE etkin veritabanının DEK i şifreleme için anahtar kasasına gönderir. Anahtar Kasası, daha sonra Kullanıcı veritabanında depolanan şifreli DEK ' i döndürür.

Gerektiğinde, sunucu şifre çözme için anahtar kasasına korumalı DEK gönderir.

Denetçiler, günlük etkinleştirildiyse Anahtar Kasası AuditEvent günlüklerini gözden geçirmek için Azure Izleyici 'yi kullanabilir.

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE yapılandırma gereksinimleri

### <a name="requirements-for-configuring-akv"></a>AKV 'yi yapılandırma gereksinimleri

- Anahtar Kasası ve SQL veritabanı/yönetilen örnek aynı Azure Active Directory kiracısına ait olmalıdır. Platformlar arası anahtar kasası ve sunucu etkileşimleri desteklenmez. Kaynakları daha sonra taşımak için, AKV ile TDE yeniden yapılandırılması gerekir. [Kaynakları taşıma](../../azure-resource-manager/management/move-resource-group-and-subscription.md)hakkında daha fazla bilgi edinin.

- Anahtar kasasında [geçici silme](../../key-vault/general/soft-delete-overview.md) özelliğinin etkinleştirilmesi, veri kaybını korumak için yanlışlıkla anahtar (veya Anahtar Kasası) silme gerçekleşmelidir. Geçici olarak silinen kaynaklar, bu sırada müşteri tarafından kurtarılmaz veya temizlenmediği takdirde 90 gün boyunca tutulur. *Kurtarma* ve *Temizleme* eylemlerinin bir Anahtar Kasası erişim ilkesiyle ilişkili kendi izinleri vardır. Geçici silme özelliği varsayılan olarak kapalıdır ve [PowerShell](../../key-vault/general/key-vault-recovery.md?tabs=azure-powershell) veya [CLI](../../key-vault/general/key-vault-recovery.md?tabs=azure-cli)aracılığıyla etkinleştirilebilir. Azure portal aracılığıyla etkinleştirilemez.  

- Azure Active Directory kimliğini kullanarak sunucuya veya yönetilen örnek için anahtar kasasına (Get, wrapKey, unwrapKey) erişim izni verin. Azure portal kullanılırken, Azure AD kimliği otomatik olarak oluşturulur. PowerShell veya CLı kullanırken, Azure AD kimliğinin açık bir şekilde oluşturulması ve tamamlanmasının doğrulanması gerekir. PowerShell kullanırken ayrıntılı adım adım yönergeler için bkz. [bYok Ile TDE yapılandırma](transparent-data-encryption-byok-configure.md) ve [SQL yönetilen örneği için bYok Ile TDE yapılandırma](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md) .

- AKV ile güvenlik duvarı kullanırken, *Güvenilen Microsoft hizmetlerinin güvenlik duvarını atlamasına Izin ver* seçeneğini etkinleştirmeniz gerekir.

### <a name="requirements-for-configuring-tde-protector"></a>TDE koruyucusunu yapılandırma gereksinimleri

- TDE koruyucusu yalnızca asimetrik, RSA veya RSA HSM anahtarı olabilir. Desteklenen anahtar uzunlukları 2048 bayt ve 3072 bayttır.

- Anahtar etkinleştirme tarihi (ayarlandıysa), geçmişteki bir tarih ve saat olmalıdır. Sona erme tarihi (ayarlandıysa) gelecekteki bir tarih ve saat olmalıdır.

- Anahtar *etkin* durumda olmalıdır.

- Anahtar kasasında mevcut anahtarı içeri aktarıyorsanız, bunu desteklenen dosya biçimlerinde ( `.pfx` , `.byok` , veya) sağladığınızdan emin olun `.backup` .

> [!NOTE]
> Azure SQL artık, yönetilen bir HSM 'de TDE koruyucusu olarak depolanan bir RSA anahtarının kullanılmasını desteklemektedir. Bu özellik **genel önizlemede**. Azure Key Vault yönetilen HSM, FIPS 140-2 düzey 3 tarafından doğrulanan HSM 'leri kullanarak bulut uygulamalarınızın şifreleme anahtarlarını korumanıza olanak sağlayan, tam olarak yönetilen, yüksek oranda kullanılabilir, tek kiracılı ve standartlara uygun bir bulut hizmetidir. [Yönetilen HSM](../../key-vault/managed-hsm/index.yml)'ler hakkında daha fazla bilgi edinin.


## <a name="recommendations-when-configuring-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE yapılandırılırken öneriler

### <a name="recommendations-when-configuring-akv"></a>AKV yapılandırılırken öneriler

- Sunucu, anahtar kasasındaki TDE koruyucuya eriştiğinde yüksek kullanılabilirlik sağlamak için, tek bir abonelikteki en fazla 500 Genel Amaçlı veya 200 İş Açısından Kritik veritabanlarını toplam bir anahtar kasası ile ilişkilendirin. Bu rakamlar deneyimi temel alır ve [Anahtar Kasası hizmeti sınırları](../../key-vault/general/service-limits.md)bölümünde belgelenmiştir. Buradaki amaç, sunucuda veritabanları olduğundan kasada çok sayıda anahtar işlem tetikleyeceği için sunucu yük devretmesinin ardından oluşan sorunları önlemektir.

- Bu kritik kaynağı kimlerin silebilen ve yanlışlıkla veya yetkisiz silme işleminin önlenmesi için anahtar kasasında bir kaynak kilidi ayarlayın. [Kaynak kilitleri](../../azure-resource-manager/management/lock-resources.md)hakkında daha fazla bilgi edinin.

- Tüm şifreleme anahtarlarında denetim ve raporlamayı etkinleştir: Anahtar Kasası, diğer güvenlik bilgilerine ve olay yönetim araçlarına eklemek kolay olan Günlükler sağlar. Operations Management Suite [Log Analytics](../../azure-monitor/insights/key-vault-insights-overview.md) , zaten tümleştirilmiş bir hizmetin bir örneğidir.

- Her bir sunucuyu farklı bölgelerde bulunan iki anahtar kasası ile bağlayın ve şifrelenmiş veritabanlarının yüksek oranda kullanılabilirliğini sağlamak için aynı anahtar malzemesini tutun. Yalnızca aynı bölgedeki anahtar kasasındaki anahtarı TDE koruyucusu ile işaretleyin. Aynı bölgedeki anahtar kasasını etkileyen bir kesinti varsa, sistem otomatik olarak uzak bölgedeki anahtar kasasına geçiş yapar.

### <a name="recommendations-when-configuring-tde-protector"></a>TDE koruyucusunu yapılandırırken öneriler

- TDE koruyucunun bir kopyasını güvenli bir yerde tutun veya Emanet hizmetine sağlayın.

- Anahtar, Anahtar Kasası 'nda oluşturulduysa, ilk kez AKV 'de anahtarı kullanmadan önce anahtar yedeklemesi oluşturun. Yedekleme, yalnızca bir Azure Key Vault geri yüklenebilir. [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey) komutu hakkında daha fazla bilgi edinin.

- Anahtara her değişiklik yapıldığında (örneğin, anahtar öznitelikleri, Etiketler, ACL 'Ler) yeni bir yedekleme oluşturun.

- Anahtarları döndürürken anahtar kasasında anahtarın **önceki sürümlerini saklayın** , bu nedenle eski veritabanı yedeklemeleri geri yüklenebilir. Bir veritabanı için TDE koruyucusu değiştirildiğinde, veritabanının eski yedekleri en son TDE koruyucuyu kullanacak şekilde **güncellenmez** . Geri yükleme sırasında her bir yedeklemenin, oluşturma zamanında şifrelendiğinden TDE koruyucusu olması gerekir. Anahtar döndürmeler [, PowerShell kullanarak saydam veri şifrelemesi koruyucusunu döndürme](transparent-data-encryption-byok-key-rotation.md)bölümündeki yönergeleri izleyerek gerçekleştirilebilir.

- Hizmet tarafından yönetilen anahtarlara geçiş yapıldıktan sonra bile, daha önce kullanılan tüm anahtarları AKV 'de saklayın. Veritabanı yedeklemelerinin, AKV 'de depolanan TDE koruyucuları ile geri yüklenebilmesini sağlar.  Azure Key Vault ile oluşturulan TDE koruyucuları, kalan tüm depolanan yedeklemeler hizmet tarafından yönetilen anahtarlarla oluşturuluncaya kadar korunmalıdır. [Backup-AzKeyVaultKey](/powershell/module/az.keyvault/backup-azkeyvaultkey)kullanarak bu anahtarların kurtarılabilir yedek kopyalarını oluşturun.

- Bir güvenlik olayı sırasında, veri kaybı riski olmadan riskli olabilecek bir anahtarı kaldırmak için, riskli [olabilecek anahtar kaldırma anahtarındaki](transparent-data-encryption-byok-remove-tde-protector.md)adımları izleyin.

## <a name="inaccessible-tde-protector"></a>Erişilemeyen TDE koruyucusu

Saydam veri şifrelemesi, müşteri tarafından yönetilen anahtar kullanacak şekilde yapılandırıldığında, veritabanının çevrimiçi kalması için TDE koruyucusuna sürekli erişim gerekir. Sunucu, AKV 'de müşteri tarafından yönetilen TDE koruyucusuna erişimi kaybederse, 10 dakikalık bir veritabanı, ilgili hata iletisiyle tüm bağlantıları reddetmeye başlar ve durumunu *erişilemez* olarak değiştirir. Erişilemeyen durumdaki bir veritabanında izin verilen tek eylem, onu siliyor.

> [!NOTE]
> Zaman aralıklı bir ağ kesintisi nedeniyle veritabanına erişilemezse, hiçbir eylem gerekmez ve veritabanları otomatik olarak yeniden çevrimiçi olur.

Anahtar erişimi geri yüklendikten sonra, veritabanı yeniden çevrimiçi duruma getirilene kadar ek saat ve adımlar gerekir, bu, anahtara erişim olmadan geçen süreye ve veritabanındaki verilerin boyutuna göre farklılık gösterebilir:

- Anahtar erişimi 8 saat içinde geri yüklenirse, veritabanı bir sonraki saat içinde geri döndürülür.

- Anahtar erişimi 8 saatten uzun bir süre sonra geri yüklenirse, oto Heal mümkün değildir ve veritabanının geri getirilmesi portala ek adımlar gerektirir ve veritabanının boyutuna bağlı olarak önemli miktarda zaman alabilir. Veritabanı yeniden çevrimiçi olduktan sonra, önceden [Yük devretme grubu](auto-failover-group-overview.md) yapılandırması, zaman içinde geri yükleme geçmişi ve Etiketler gibi sunucu düzeyi ayarları daha önce **yapılandırılmış olur.** Bu nedenle, temel alınan anahtar erişim sorunlarını 8 saat içinde tanımlamanızı ve adresetmenizi sağlayan bir bildirim sistemi uygulanması önerilir.

Aşağıda, erişilemeyen bir veritabanını yeniden çevrimiçi hale getirmek için portalda gereken ek adımların bir görünümü yer alır.

![TDE BYOK erişilemeyen veritabanı](./media/transparent-data-encryption-byok-overview/customer-managed-tde-inaccessible-database.jpg)


### <a name="accidental-tde-protector-access-revocation"></a>Yanlışlıkla TDE koruyucu erişimi iptali

Anahtar Kasası 'na yeterli erişim haklarına sahip birinin yanlışlıkla bu anahtara sunucu erişimini devre dışı bırakmasından kaynaklanabilir:

- anahtar kasasının *Get*, *wrapKey*, *unwrapKey* izinleri sunucudan iptal ediliyor

- anahtar siliniyor

- anahtar kasasını silme

- anahtar kasasının güvenlik duvarı kurallarını değiştirme

- Azure Active Directory içindeki sunucunun yönetilen kimliğini silme

[Veritabanının erişilemez hale gelmesi için yaygın nedenler](/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current&preserve-view=true#common-errors-causing-databases-to-become-inaccessible)hakkında daha fazla bilgi edinin.

## <a name="monitoring-of-the-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE 'nın izlenmesi

Veritabanı durumunu izlemek ve TDE koruyucu erişimi kaybı nedeniyle uyarı etkinleştirmek için aşağıdaki Azure özelliklerini yapılandırın:

- [Azure Kaynak durumu](../../service-health/resource-health-overview.md). Veritabanına ilk bağlantı reddedildikten sonra TDE koruyucusu erişimi kayıp olan erişilemeyen bir veritabanı "kullanılamaz" olarak gösterilir.
- Müşteri tarafından yönetilen anahtar kasasındaki TDE koruyucuya erişim başarısız olduğunda [etkinlik günlüğü](../../service-health/alerts-activity-log-service-notifications-portal.md) etkinlik günlüğüne eklenir.  Bu olaylar için uyarı oluşturulması mümkün olan en kısa sürede erişimi yeniden etkinleştirmenizi sağlar.
- [Eylem grupları](../../azure-monitor/alerts/action-groups.md) , tercihlerinize göre size bildirim ve uyarı göndermek için tanımlanabilir; Örneğin, e-posta/SMS/Push/Voice, Logic App, Web KANCASı, ISM veya Otomasyon Runbook 'u.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE ile veritabanı yedekleme ve geri yükleme

Bir veritabanı TDE Key Vault 'ten anahtar kullanılarak şifrelendikten sonra, yeni oluşturulan tüm yedeklemeler aynı TDE koruyucusuyla de şifrelenir. TDE koruyucusu değiştirildiğinde, veritabanının eski yedekleri en son TDE koruyucuyu kullanacak şekilde **güncellenmez** .

Key Vault bir TDE koruyucusu ile şifrelenmiş bir yedeklemeyi geri yüklemek için, anahtar malzemenin hedef sunucu için kullanılabilir olduğundan emin olun. Bu nedenle TDE koruyucusunun tüm eski sürümlerini anahtar kasasında tutmanızı öneririz; böylelikle veritabanı yedeklemeleri geri yüklenebilir.

> [!IMPORTANT]
> Herhangi bir anda, bir sunucu için birden fazla TDE koruyucu kümesi olamaz. Bu, Azure portal dikey penceresindeki "anahtarı varsayılan TDE koruyucuyu yap" ile işaretlenen anahtardır. Ancak, birden fazla ek anahtar, bir TDE koruyucusu olarak işaretlenmeksizin bir sunucuya bağlanabilir. Bu anahtarlar DEK koruma için kullanılmaz, ancak yedekleme dosyası karşılık gelen parmak izine sahip anahtarla şifrelendiyse, yedekten geri yükleme sırasında kullanılabilir.

Bir yedeklemeyi geri yüklemek için gerekli olan anahtar artık hedef sunucu için kullanılabilir değilse, geri yükleme denemeniz: "hedef sunucunun `<Servername>` ve arasında oluşturulan tüm AKV URI 'lere erişimi yok \<Timestamp #1> \<Timestamp #2> . Tüm AKV URI 'Lerini geri yükledikten sonra işlemi yeniden deneyin. "

Bunu azaltmak için, hedef sunucu için [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 'ini veya hedef yönetilen örnek için [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) ' i çalıştırarak kullanılabilir anahtarların listesini döndürün ve eksik olanları tespit edin. Tüm yedeklemelerin geri yüklenebildiğinden emin olmak için, geri yükleme için hedef sunucunun gerekli tüm anahtarlara erişebildiğinden emin olun. Bu anahtarların TDE koruyucusu olarak işaretlenmesi gerekmez.

SQL veritabanı için yedekleme kurtarması hakkında daha fazla bilgi edinmek için bkz. [SQL veritabanı 'nda veritabanını kurtarma](recovery-using-backups.md). Azure SYNAPSE Analytics 'te adanmış SQL havuzu için yedekleme kurtarması hakkında daha fazla bilgi edinmek için bkz. [ayrılmış BIR SQL havuzunu kurtarma](../../synapse-analytics/sql-data-warehouse/backup-and-restore.md). SQL yönetilen örneği ile SQL Server yerel yedekleme/geri yükleme için bkz [. hızlı başlangıç: VERITABANıNı SQL yönetilen örneğine geri yükleme](../managed-instance/restore-sample-database-quickstart.md)

Günlük dosyaları için ek dikkat: yedeklenen günlük dosyaları, döndürülse bile özgün TDE koruyucusu ile şifrelenmeye devam eder ve veritabanı şimdi yeni bir TDE koruyucu kullanıyor.  Geri yükleme sırasında, veritabanını geri yüklemek için her iki anahtar de gerekecektir.  Günlük dosyası Azure Key Vault depolanan bir TDE koruyucu kullanıyorsa, veritabanı hizmet tarafından yönetilen TDE kullanacak şekilde değiştirilse bile, bu anahtar geri yükleme zamanında gerekecektir.

## <a name="high-availability-with-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE ile yüksek kullanılabilirlik

Sunucu için yapılandırılmış coğrafi yedeklilik olmadığında bile, sunucuyu aynı anahtar malzemesine sahip iki farklı bölgede iki farklı Anahtar Kasası kullanacak şekilde yapılandırmak kesinlikle önerilir. Diğer bölgedeki ikincil anahtar kasasındaki anahtar, TDE koruyucusu olarak işaretlenmemelidir ve buna izin verilmemiştir. Birincil anahtar kasasını etkileyen bir kesinti varsa ve bu durumda, sistem varsa ikincil anahtar kasasında aynı parmak izine sahip diğer bağlantılı anahtara otomatik olarak geçiş yapar. Bu anahtar, iptal edilen erişim haklarından dolayı TDE koruyucusu erişilemediğinde veya anahtar ya da Anahtar Kasası silindiği için, müşterinin bir sunucunun anahtara erişimini kasıtlı olarak kısıtlayabileceğinden bu anahtarın gerçekleşmeyeceğini unutmayın. Farklı bölgelerde iki Anahtar Kasası için aynı anahtar malzemesini sağlamak, anahtar kasasının dışında anahtar oluşturarak ve bunları her iki Anahtar Kasası içine aktararak yapılabilir. 

Alternatif olarak, sunucu ile aynı bölgede bulunan Birincil Anahtar Kasası kullanılarak anahtar oluşturarak ve anahtarı farklı bir Azure bölgesindeki anahtar kasasında klonlayarak elde edilebilir. Anahtarı birincil anahtar kasasından şifrelenmiş biçimde almak için [Backup-azkeyvaultkey](/powershell/module/az.keyvault/Backup-AzKeyVaultKey) cmdlet 'ini kullanın ve ardından [restore-azkeyvaultkey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet 'ini kullanın ve anahtarı klonlamak için ikinci bölgede bir Anahtar Kasası belirtin. Alternatif olarak, anahtarı yedeklemek ve geri yüklemek için Azure portal kullanın. Anahtar yedekleme/geri yükleme işlemine yalnızca aynı Azure aboneliği ve [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/)içindeki anahtar kasaları arasında izin verilir.  

![Single-Server HA](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Coğrafi-DR ve müşteri tarafından yönetilen TDE

Hem [etkin coğrafi çoğaltma](active-geo-replication-overview.md) hem de [Yük devretme grupları](auto-failover-group-overview.md) senaryolarında, dahil edilen her sunucu ayrı bir Anahtar Kasası gerektirir ve bu, aynı Azure bölgesindeki sunucu ile birlikte bulunmalıdır. Müşteri, anahtar kasalarının önemli kasaların tamamında korunmasından sorumludur. böylece, coğrafi ikincil değer eşitlenmiş olur ve birincil anahtar kasasından, bölgedeki bir kesinti nedeniyle, bir yük devretme işlemi tetiklendiği için aynı anahtarı kullanmaya devam edebilir. En fazla dört ikincil değer yapılandırılabilir ve zincirleme (ikinconun ikincilleri) desteklenmez.

Eksik anahtar malzemesi nedeniyle coğrafi çoğaltma sırasında veya sırasında sorunları önlemek için müşteri tarafından yönetilen TDE yapılandırılırken bu kuralları izlemeniz önemlidir:

- Dahil edilen tüm anahtar kasaları, ilgili sunucular için aynı özelliklere ve aynı erişim haklarına sahip olmalıdır.

- Dahil olan tüm anahtar kasaları aynı anahtar malzemesini içermelidir. Yalnızca geçerli bir TDE koruyucu için değil, yedekleme dosyalarında kullanılabilecek tüm önceki TDE koruyucuları için geçerlidir.

- İkisin ilk kurulumu ve döndürme işlemi, önce ikincil üzerinde, sonra birincilde yapılmalıdır.

![Yük devretme grupları ve coğrafi-Dr](./media/transparent-data-encryption-byok-overview/customer-managed-tde-with-bcdr.png)

Yük devretmeyi test etmek için [etkin coğrafi Çoğaltmaya genel bakış](active-geo-replication-overview.md)' daki adımları izleyin. SQL veritabanının her iki Anahtar Kasası için de erişim izni olduğunu doğrulamak için yük devretme testi düzenli olarak yapılmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, müşteri tarafından yönetilen TDE ile yaygın işlemler için aşağıdaki PowerShell örnek betiklerini denetlemek isteyebilirsiniz:

- [PowerShell kullanarak SQL veritabanı için Saydam Veri Şifrelemesi koruyucusunu döndürme](transparent-data-encryption-byok-key-rotation.md)

- [PowerShell kullanarak SQL veritabanı için Saydam Veri Şifrelemesi (TDE) koruyucusunu kaldırma](transparent-data-encryption-byok-remove-tde-protector.md)

- [PowerShell kullanarak kendi anahtarınızla SQL yönetilen örneğindeki Saydam Veri Şifrelemesi yönetme](../managed-instance/scripts/transparent-data-encryption-byok-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)