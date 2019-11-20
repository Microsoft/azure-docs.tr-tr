---
title: Müşteri tarafından yönetilen saydam veri şifrelemesi (TDE)
description: Kendi Anahtarını Getir (BYOK) SQL veritabanı ve veri ambarı için Azure Key Vault ile Saydam Veri Şifrelemesi (TDE) desteği. BYOK 'a genel bakış, avantajlar, nasıl çalıştığı, önemli noktalar ve öneriler ile TDE.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 11/04/2019
ms.openlocfilehash: 3ae5403c2313bc1d2f271aeba9d4a99d9a0c5db7
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822276"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>Müşteri tarafından yönetilen anahtarla Azure SQL Saydam Veri Şifrelemesi

Müşteri tarafından yönetilen anahtarla Azure SQL [Saydam veri şifrelemesi (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) , bekleyen veri koruması için kendi anahtarını getir (bYok) senaryosunu sağlar ve kuruluşların anahtar ve veri yönetiminde görevlerin ayrılmasını sağlar. Müşteri tarafından yönetilen saydam veri şifrelemesi ile, müşteri, anahtar yaşam döngüsü yönetimi (anahtar oluşturma, karşıya yükleme, döndürme, silme), anahtar kullanımı izinleri ve anahtarların üzerinde işlemler denetimi hakkında tam bir denetim üzerinden ve bunların sorumluluğundadır.

Bu senaryoda, TDE koruyucusu olarak adlandırılan veritabanı şifreleme anahtarı (DEK) şifrelemesi için kullanılan anahtar, bulut tabanlı bir dış anahtar yönetimi olan, müşterinin sahip olduğu ve müşteri tarafından yönetilen [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)içinde depolanan, müşteri tarafından yönetilen bir asimetrik anahtardır sistemin. Key Vault, FIPS 140-2 düzey 2 tarafından desteklenen donanım güvenlik modülleri (HSM 'ler) tarafından desteklenen, RSA şifreleme anahtarları için yüksek düzeyde kullanılabilir ve ölçeklenebilir güvenli depolama alanı. Depolanan bir anahtara doğrudan erişime izin vermez, ancak yetkili varlıkların anahtarını kullanarak şifreleme/şifre çözme hizmetleri sağlar. Anahtar, Anahtar Kasası tarafından oluşturulabilir, içeri aktarılabilir veya [bir şirket ıçı HSM cihazından anahtar kasasına aktarılabilir](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys).

Azure SQL veritabanı ve Azure SQL veri ambarı için, TDE koruyucusu mantıksal sunucu düzeyinde ayarlanır ve bu sunucuyla ilişkili olan tüm şifreli veritabanları tarafından devralınır. Azure SQL yönetilen örneği için, TDE koruyucusu örnek düzeyinde ayarlanır ve bu örnekteki tüm şifreli veritabanları tarafından devralınır. *Sunucu* terimi, farklı belirtilmedikçe bu belge boyunca hem SQL veritabanı mantıksal sunucusu hem de yönetilen örnek anlamına gelir. 

> [!IMPORTANT]
> Müşteri tarafından yönetilen TDE kullanmaya başlamak isteyen hizmet tarafından yönetilen TDE kullanan bu işlemler için veriler, geçiş işlemi sırasında şifrelenmiş olarak kalır ve veritabanı dosyalarının kapalı kalması veya yeniden şifrelenmesi gerekmez. Hizmet tarafından yönetilen anahtardan müşterinin yönettiği bir anahtara geçiş yapmak, hızlı ve çevrimiçi bir işlem olan DEK ' ın yeniden şifrelenmesini gerektirir.

## <a name="benefits-of-the-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE 'nın avantajları

Müşteri tarafından yönetilen TDE müşteri için aşağıdaki avantajları sağlar:

- TDE koruyucunun kullanımı ve yönetimi üzerinde tam ve ayrıntılı denetim;

- TDE koruyucusu kullanımının saydamlığı;

- Kuruluş içindeki anahtar ve verilerin yönetiminde görev ayrımı uygulama yeteneği;

- Key Vault Yöneticisi şifrelenmiş veritabanını erişilemez hale getirmek için anahtar erişim izinlerini iptal edebilir;

- AKV; içindeki anahtarların Merkezi Yönetimi

- Microsoft 'un şifreleme anahtarlarını göremediği ve ayıklayamayacağı gibi, bu nedenle son müşterilerinizin sunduğu daha fazla güven;

## <a name="how-customer-managed-tde-works"></a>Müşteri tarafından yönetilen TDE nasıl çalıştığı

![Müşteri tarafından yönetilen TDE 'nın kurulumu ve çalışması](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

Sunucunun, 1. & lt; 1} içinde depolanan TDE koruyucusunu kullanabilmesi için, Anahtar Kasası yöneticisinin benzersiz AAD kimliğini kullanarak sunucuya aşağıdaki erişim haklarını vermesi gerekir:

- Key Vault, anahtarın genel bölümünü ve **özelliklerini alma**

- **wrapKey** -koruyabilmek için (ŞIFRELEME) dek

- **unwrapKey** -korumayı kaldırmak için (şifre çözme) dek

Anahtar Kasası Yöneticisi ayrıca [Anahtar Kasası denetim olaylarının günlüğe kaydedilmesini etkinleştirerek](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)daha sonra denetlenebilir.

Sunucu, AKV 'den bir TDE koruyucusu kullanacak şekilde yapılandırıldığında, sunucu her bir TDE etkin veritabanının DEK i şifreleme için anahtar kasasına gönderir. Anahtar Kasası, daha sonra Kullanıcı veritabanında depolanan şifreli DEK ' i döndürür.

Gerektiğinde, sunucu şifre çözme için anahtar kasasına korumalı DEK gönderir.

Denetçiler, günlük etkinleştirildiyse Anahtar Kasası AuditEvent günlüklerini gözden geçirmek için Azure Izleyici 'yi kullanabilir.


## <a name="requirements-for-configuring-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE yapılandırma gereksinimleri

### <a name="requirements-for-configuring-akv"></a>AKV 'yi yapılandırma gereksinimleri

- Anahtar Kasası ve SQL veritabanı/yönetilen örnek aynı Azure Active Directory kiracısına ait olmalıdır. Platformlar arası anahtar kasası ve sunucu etkileşimleri desteklenmez. Kaynakları daha sonra taşımak için, AKV ile TDE yeniden yapılandırılması gerekir. [Kaynakları taşıma](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)hakkında daha fazla bilgi edinin.

- Anahtar kasasında [geçici silme](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) özelliğinin etkinleştirilmesi, veri kaybını korumak için yanlışlıkla anahtar (veya Anahtar Kasası) silme gerçekleşmelidir. Geçici olarak silinen kaynaklar, bu sırada müşteri tarafından kurtarılmaz veya temizlenmediği takdirde 90 gün boyunca tutulur. *Kurtarma* ve *Temizleme* eylemlerinin bir Anahtar Kasası erişim ilkesiyle ilişkili kendi izinleri vardır. Geçici silme özelliği varsayılan olarak kapalıdır ve [PowerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) veya [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)aracılığıyla etkinleştirilebilir. Azure portal aracılığıyla etkinleştirilemez.  

- Azure Active Directory kimliğini kullanarak SQL veritabanı sunucusuna veya yönetilen örnek için Anahtar Kasası 'na (Get, wrapKey, unwrapKey) erişim izni verin. Azure portal kullanırken, Azure AD kimliği otomatik olarak oluşturulur. PowerShell veya CLı kullanırken, Azure AD kimliğinin açık bir şekilde oluşturulması ve tamamlanmasının doğrulanması gerekir. PowerShell kullanırken ayrıntılı adım adım yönergeler için bkz. [bYok Ile TDE yapılandırma](transparent-data-encryption-byok-azure-sql-configure.md) ve [yönetilen örnek için bYok Ile TDE yapılandırma](https://aka.ms/sqlmibyoktdepowershell) .

- AKV ile güvenlik duvarı kullanırken, *Güvenilen Microsoft hizmetlerinin güvenlik duvarını atlamasına Izin ver*seçeneğini etkinleştirmeniz gerekir.

### <a name="requirements-for-configuring-tde-protector"></a>TDE koruyucusunu yapılandırma gereksinimleri

- TDE koruyucusu yalnızca asimetrik, RSA 2048 veya RSA HSM 2048 anahtarı olabilir.

- Anahtar etkinleştirme veya sona erme tarihi kümesi içeremez.

- Anahtar, anahtar kasasındaki etkin durumda olmalıdır.

- Anahtar kasasında mevcut anahtarı içeri aktarıyorsanız, bu dosyayı desteklenen dosya biçimlerinde (. pfx,. bYok veya. Backup) sağladığınızdan emin olun.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE yapılandırılırken öneriler

### <a name="recommendations-when-configuring-akv"></a>AKV yapılandırılırken öneriler

- Sunucu, anahtar kasasındaki TDE koruyucuya eriştiğinde yüksek kullanılabilirlik sağlamak için, tek bir abonelikteki en fazla 500 Genel Amaçlı veya 200 İş Açısından Kritik veritabanlarını toplam bir anahtar kasası ile ilişkilendirin. Bu rakamlar deneyimi temel alır ve [Anahtar Kasası hizmeti sınırları](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)bölümünde belgelenmiştir. Buradaki amaç, sunucuda veritabanları olduğundan kasada çok sayıda anahtar işlem tetikleyeceği için sunucu yük devretmesinin ardından oluşan sorunları önlemektir. 

- Bu kritik kaynağı kimlerin silebilen ve yanlışlıkla veya yetkisiz silme işleminin önlenmesi için anahtar kasasında bir kaynak kilidi ayarlayın. [Kaynak kilitleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)hakkında daha fazla bilgi edinin.

- Tüm şifreleme anahtarlarında denetim ve raporlamayı etkinleştir: Anahtar Kasası, diğer güvenlik bilgilerine ve olay yönetim araçlarına eklemek kolay olan Günlükler sağlar. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) , zaten tümleştirilmiş bir hizmetin bir örneğidir.

- Her bir sunucuyu farklı bölgelerde bulunan iki anahtar kasası ile bağlayın ve şifrelenmiş veritabanlarının yüksek oranda kullanılabilirliğini sağlamak için aynı anahtar malzemesini tutun. Yalnızca aynı bölgedeki anahtar kasasındaki anahtarı TDE koruyucusu ile işaretleyin. Sistem kullanacak

### <a name="recommendations-when-configuring-tde-protector"></a>TDE koruyucusunu yapılandırırken öneriler
- TDE koruyucunun bir kopyasını güvenli bir yerde tutun veya Emanet hizmetine sağlayın. 

- Anahtar, Anahtar Kasası 'nda oluşturulduysa, ilk kez AKV 'de anahtarı kullanmadan önce anahtar yedeklemesi oluşturun. Yedekleme, yalnızca bir Azure Key Vault geri yüklenebilir. [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) komutu hakkında daha fazla bilgi edinin.

- Anahtarda her değişiklik yapıldığında yeni bir yedekleme oluşturun (ör. anahtar öznitelikleri, Etiketler, ACL 'Ler).

- Anahtarları döndürürken anahtar kasasında anahtarın **önceki sürümlerini saklayın** , bu nedenle eski veritabanı yedeklemeleri geri yüklenebilir. Bir veritabanı için TDE koruyucusu değiştirildiğinde, veritabanının eski yedekleri en son TDE koruyucuyu kullanacak şekilde **güncellenmez** . Geri yükleme sırasında her bir yedeklemenin, oluşturma zamanında şifrelendiğinden TDE koruyucusu olması gerekir. Anahtar döndürmeler [, PowerShell kullanarak saydam veri şifrelemesi koruyucusunu döndürme](transparent-data-encryption-byok-azure-sql-key-rotation.md)bölümündeki yönergeleri izleyerek gerçekleştirilebilir.

- Hizmet tarafından yönetilen anahtarlara geçiş yapıldıktan sonra bile, daha önce kullanılan tüm anahtarları AKV 'de saklayın. Veritabanı yedeklemelerinin, AKV 'de depolanan TDE koruyucuları ile geri yüklenebilmesini sağlar.  Azure Key Vault ile oluşturulan TDE koruyucuları, kalan tüm depolanan yedeklemeler hizmet tarafından yönetilen anahtarlarla oluşturuluncaya kadar korunmalıdır. [Backup-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)kullanarak bu anahtarların kurtarılabilir yedek kopyalarını oluşturun.

- Bir güvenlik olayı sırasında, veri kaybı riski olmadan riskli olabilecek bir anahtarı kaldırmak için, riskli [olabilecek anahtar kaldırma anahtarındaki](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)adımları izleyin.

## <a name="inaccessible-tde-protector"></a>Erişilemeyen TDE koruyucusu

Saydam veri şifrelemesi, müşteri tarafından yönetilen anahtar kullanacak şekilde yapılandırıldığında, veritabanının çevrimiçi kalması için TDE koruyucusuna sürekli erişim gerekir. Sunucu, AKV 'de müşteri tarafından yönetilen TDE koruyucusuna erişimi kaybederse, 10 dakikalık bir veritabanı, ilgili hata iletisiyle tüm bağlantıları reddetmeye başlar ve durumunu *erişilemez*olarak değiştirir. Erişilemeyen durumdaki bir veritabanında izin verilen tek eylem, onu siliyor.

> [!NOTE]
> Zaman aralıklı bir ağ kesintisi nedeniyle veritabanına erişilemezse, hiçbir eylem gerekmez ve veritabanları otomatik olarak yeniden çevrimiçi olur.

Anahtar erişimi geri yüklendikten sonra, veritabanı yeniden çevrimiçi duruma getirilene kadar ek saat ve adımlar gerekir, bu, anahtara erişim olmadan geçen süreye ve veritabanındaki verilerin boyutuna göre farklılık gösterebilir:

- Anahtar erişimi 8 saat içinde geri yüklenirse, veritabanı bir sonraki saat içinde otomatik olarak alınır.

- Anahtar erişimi 8 saatten uzun bir süre geçtikten sonra geri yüklenirse, otomatik heveme mümkün olmaz ve veritabanının boyutuna bağlı olarak, bir destek bileti açılmasını gerektiren önemli miktarda zaman alabilir. Veritabanı yeniden çevrimiçi olduktan sonra, önceden [Yük devretme grubu](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) yapılandırması, zaman içinde geri yükleme geçmişi ve Etiketler gibi sunucu düzeyi ayarları daha önce yapılandırılmış olur. Bu nedenle, temel alınan anahtar erişim sorunlarını 8 saat içinde tanımlamanızı ve adresetmenizi sağlayan bir bildirim sistemi uygulanması önerilir.

### <a name="accidental-tde-protector-access-revocation"></a>Yanlışlıkla TDE koruyucu erişimi iptali

Anahtar Kasası 'na yeterli erişim haklarına sahip birinin yanlışlıkla bu anahtara sunucu erişimini devre dışı bırakmasından kaynaklanabilir:

- anahtar kasasının *Get*, *wrapKey*, *unwrapKey* izinleri sunucudan iptal ediliyor

- anahtar siliniyor

- anahtar kasasını silme

- anahtar kasasının güvenlik duvarı kurallarını değiştirme

- Azure Active Directory içindeki sunucunun yönetilen kimliğini silme

[Veritabanının erişilemez hale gelmesi için yaygın nedenler](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)hakkında daha fazla bilgi edinin.

## <a name="monitoring-of-the-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE 'nın izlenmesi

Veritabanı durumunu izlemek ve TDE koruyucu erişimi kaybı nedeniyle uyarı etkinleştirmek için aşağıdaki Azure özelliklerini yapılandırın:
- [Azure Kaynak durumu](https://docs.microsoft.com/azure/service-health/resource-health-overview). Veritabanına ilk bağlantı reddedildikten sonra TDE koruyucusu erişimi kayıp olan erişilemeyen bir veritabanı "kullanılamaz" olarak gösterilir.
- Müşteri tarafından yönetilen anahtar kasasındaki TDE koruyucuya erişim başarısız olduğunda [etkinlik günlüğü](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) etkinlik günlüğüne eklenir.  Bu olaylar için uyarı oluşturulması mümkün olan en kısa sürede erişimi yeniden etkinleştirmenizi sağlar.
- [Eylem grupları](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) , tercihlerinize, örn. e-posta/SMS/Push/Voice, Logic App, Web KANCASı, ISM veya Otomasyon Runbook 'una göre bildirim ve uyarı göndermek için tanımlanabilir.

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE ile veritabanı yedekleme ve geri yükleme

Bir veritabanı TDE Key Vault 'ten anahtar kullanılarak şifrelendikten sonra, yeni oluşturulan tüm yedeklemeler aynı TDE koruyucusuyla de şifrelenir. TDE koruyucusu değiştirildiğinde, veritabanının eski yedekleri en son TDE koruyucuyu kullanacak şekilde **güncellenmez** .

Key Vault bir TDE koruyucusu ile şifrelenmiş bir yedeklemeyi geri yüklemek için, anahtar malzemenin hedef sunucu için kullanılabilir olduğundan emin olun. Bu nedenle, temel kasadaki TDE koruyucunun tüm eski sürümlerini tutmanız önerilir, böylelikle veritabanı yedeklemeleri geri yüklenebilir. 

> [!IMPORTANT]
> Herhangi bir anda, bir sunucu için birden fazla TDE koruyucu kümesi olamaz. Bu, Azure portal dikey penceresindeki "anahtarı varsayılan TDE koruyucuyu yap" ile işaretlenen anahtardır. Ancak, birden fazla ek anahtar, bir TDE koruyucusu olarak işaretlenmeksizin bir sunucuya bağlanabilir. Bu anahtarlar DEK koruma için kullanılmaz, ancak yedekleme dosyası karşılık gelen parmak izine sahip anahtarla şifrelendiyse, yedekten geri yükleme sırasında kullanılabilir.

Bir yedeklemeyi geri yüklemek için gerekli olan anahtar artık hedef sunucuda yoksa, geri yükleme denemeniz: "hedef sunucu `<Servername>`, \<zaman damgası #1 > ve @no__t_ arasında oluşturulan tüm AKV URI 'Lerinin erişimine sahip değildir. 2_ zaman damgası #2 >.\< Lütfen tüm AKV URI 'Lerini geri yükledikten sonra işlemi yeniden deneyin. "

Bunu azaltmak için, hedef SQL veritabanı mantıksal sunucusu için [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 'ini veya hedef yönetilen örnek için [Get-AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) ' i çalıştırarak kullanılabilir anahtarların listesini döndürün ve eksik olanları tespit edin. Tüm yedeklemelerin geri yüklenebildiğinden emin olmak için, geri yükleme için hedef sunucunun gerekli tüm anahtarlara erişebildiğinden emin olun. Bu anahtarların TDE koruyucusu olarak işaretlenmesi gerekmez.

SQL veritabanı için yedekleme kurtarması hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veritabanını kurtarma](sql-database-recovery-using-backups.md). SQL veri ambarı için yedekleme kurtarması hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veri ambarını kurtarma](../sql-data-warehouse/backup-and-restore.md). Yönetilen örnekle SQL Server yerel yedekleme/geri yükleme için bkz [. hızlı başlangıç: veritabanını yönetilen örneğe geri yükleme](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) 

Günlük dosyaları için ek dikkat: yedeklenen günlük dosyaları, döndürülse bile özgün TDE koruyucusu ile şifrelenmeye devam eder ve veritabanı şimdi yeni bir TDE koruyucu kullanıyor.  Geri yükleme sırasında, veritabanını geri yüklemek için her iki anahtar de gerekecektir.  Günlük dosyası Azure Key Vault depolanan bir TDE koruyucu kullanıyorsa, veritabanı hizmet tarafından yönetilen TDE kullanacak şekilde değiştirilse bile, bu anahtar geri yükleme zamanında gerekecektir.

## <a name="high-availability-with-customer-managed-tde"></a>Müşteri tarafından yönetilen TDE ile yüksek kullanılabilirlik

Sunucu için yapılandırılmış coğrafi yedeklilik olmadığında bile, sunucuyu aynı anahtar malzemesine sahip iki farklı bölgede iki farklı Anahtar Kasası kullanacak şekilde yapılandırmak kesinlikle önerilir. Sunucu ile aynı bölgede yer alan birincil anahtar kasası kullanılarak bir TDE koruyucu oluşturularak ve anahtarı farklı bir Azure bölgesindeki bir anahtar kasasında kopyalayarak, sunucunun birincil anahtar kasası ile ilgili olması gereken ikinci bir anahtar kasasına erişimi olması için Veritabanı çalışır duruma ience bir kesinti olur. 

Anahtarı birincil anahtar kasasından şifrelenmiş biçimde almak için Backup-AzKeyVaultKey cmdlet 'ini kullanın ve ardından restore-AzKeyVaultKey cmdlet 'ini kullanın ve anahtarı klonlamak için ikinci bölgede bir Anahtar Kasası belirtin. Alternatif olarak, anahtarı yedeklemek ve geri yüklemek için Azure portal kullanın. Diğer bölgedeki ikincil anahtar kasasındaki anahtar, TDE koruyucusu olarak işaretlenmemelidir ve buna izin verilmez.

 Birincil anahtar kasasını etkileyen bir kesinti varsa ve bu durumda, sistem varsa ikincil anahtar kasasında aynı parmak izine sahip diğer bağlantılı anahtara otomatik olarak geçiş yapar. Bu anahtar, iptal edilen erişim haklarından dolayı TDE koruyucusu erişilemediğinde veya anahtar ya da Anahtar Kasası silindiği için, müşterinin bir sunucunun anahtara erişimini kasıtlı olarak kısıtlayabileceğinden bu anahtarın gerçekleşmeyeceğini unutmayın.

![Tek sunuculu HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>Coğrafi-DR ve müşteri tarafından yönetilen TDE

Hem [etkin coğrafi çoğaltma](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) hem de [Yük devretme grupları](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) senaryolarında, dahil edilen her sunucu, aynı Azure bölgesindeki sunucu ile birlikte bulunması gereken ayrı bir Anahtar Kasası gerektirir. Müşteri, anahtar kasalarının önemli kasaların tamamında korunmasından sorumludur. böylece, coğrafi ikincil değer eşitlenmiş olur ve birincil anahtar kasasından, bölgedeki bir kesinti nedeniyle, bir yük devretme tetiklendiği için bu anahtarın aynı anahtarı kullanmaya devam edebilir . En fazla dört ikincil değer yapılandırılabilir ve zincirleme (ikinconun ikincilleri) desteklenmez.

Eksik anahtar malzemesi nedeniyle coğrafi çoğaltma sırasında veya sırasında sorunları önlemek için müşteri tarafından yönetilen TDE yapılandırılırken bu kuralları izlemeniz önemlidir:

- Dahil edilen tüm anahtar kasaları, ilgili sunucular için aynı özelliklere ve aynı erişim haklarına sahip olmalıdır.

- Dahil olan tüm anahtar kasaları aynı anahtar malzemesini içermelidir. Yalnızca geçerli bir TDE koruyucu için değil, yedekleme dosyalarında kullanılabilecek tüm önceki TDE koruyucuları için geçerlidir.

- İkisin ilk kurulumu ve döndürme işlemi, önce ikincil üzerinde, sonra birincilde yapılmalıdır.

![Yük devretme grupları ve coğrafi-Dr](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

Yük devretmeyi test etmek için [etkin coğrafi Çoğaltmaya genel bakış](sql-database-geo-replication-overview.md)' daki adımları izleyin. Her iki anahtar kasası da korunmak üzere SQL 'e yönelik erişim izinlerini onaylamak için düzenli olarak yapılmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Ayrıca, müşteri tarafından yönetilen TDE ile yaygın işlemler için aşağıdaki PowerShell örnek betiklerini denetlemek isteyebilirsiniz:

- [PowerShell kullanarak SQL veritabanı için Saydam Veri Şifrelemesi koruyucusunu döndürme](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [PowerShell kullanarak SQL veritabanı için Saydam Veri Şifrelemesi (TDE) koruyucusunu kaldırma](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [Yönetilen bir örnekteki Saydam Veri Şifrelemesi PowerShell kullanarak kendi anahtarınızla yönetme](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
