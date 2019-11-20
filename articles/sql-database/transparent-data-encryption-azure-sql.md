---
title: Saydam veri şifrelemesi
description: SQL veritabanı ve veri ambarı için saydam veri şifrelemeye genel bakış. Belge, hizmet tarafından yönetilen saydam veri şifrelemesini ve Kendi Anahtarını Getir dahil olmak üzere avantajlarının yanı sıra yapılandırma seçeneklerini de kapsar.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 11/01/2019
ms.openlocfilehash: b6af171eafbaf1f4d31bad649fcb0c69d8bdc24d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821700"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>SQL veritabanı ve veri ambarı için saydam veri şifrelemesi

Saydam veri şifrelemesi (TDE), bekleyen verileri şifreleyerek kötü niyetli çevrimdışı etkinlik tehditlerine karşı Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure veri ambarı ' nı korumanıza yardımcı olur. Uygulamada değişiklik gerektirmeden, bekleyen veritabanı, ilişkili yedeklemeler ve işlem günlüğü dosyaları için gerçek zamanlı şifreleme ve şifre çözme işlemleri gerçekleştirir. Varsayılan olarak, TDE, tüm yeni dağıtılan Azure SQL veritabanları için etkinleştirilmiştir. TDE, SQL veritabanında mantıksal **ana** veritabanını şifrelemek için kullanılamaz.  **Ana** veritabanı, Kullanıcı veritabanlarında TDE işlemleri gerçekleştirmek için gereken nesneleri içerir.

TDE 'nin Azure SQL veritabanı, Azure SQL yönetilen örneği veya Azure SQL veri ambarı 'nın eski veritabanları için el ile etkinleştirilmesi gerekiyor.
Restore ile oluşturulan yönetilen örnek veritabanları kaynak veritabanından şifreleme durumunu devralma.

Saydam veri şifrelemesi, veritabanı şifreleme anahtarı adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler. Bu veritabanı şifreleme anahtarı, saydam veri şifreleme koruyucusu tarafından korunuyor. Koruyucu, hizmet tarafından yönetilen bir sertifika (hizmet tarafından yönetilen saydam veri şifrelemesi) veya Azure Key Vault (Kendi Anahtarını Getir) içinde depolanan bir asimetrik anahtardır. Saydam veri şifreleme koruyucusunu Azure SQL veritabanı ve veri ambarı için sunucu düzeyinde ve Azure SQL yönetilen örneği için örnek düzeyinde ayarlarsınız. *Sunucu* terimi, farklı belirtilmedikçe, bu belge boyunca hem sunucu hem de örneğe başvurur.

Veritabanı başlangıcında, şifrelenmiş veritabanı şifreleme anahtarının şifresi çözülür ve veritabanı dosyalarının şifresinin çözülmesi ve SQL Server veritabanı altyapısı işleminde yeniden şifrelenmesi için kullanılır. Saydam veri şifrelemesi, verileri sayfa düzeyinde gerçek zamanlı g/ç şifrelemesi ve şifre çözme işlemleri gerçekleştirir. Her sayfa, belleğe okunmadan ve sonra diske yazılmadan önce şifrelendiğinde çözülür. Saydam veri şifrelemesi hakkında genel bir açıklama için bkz. [Saydam veri şifrelemesi](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

Azure sanal makinesinde çalışan SQL Server, Key Vault bir asimetrik anahtar da kullanabilir. Yapılandırma adımları SQL veritabanı ve SQL yönetilen örneği 'nde asimetrik anahtar kullanmaktan farklıdır. Daha fazla bilgi için [Azure Key Vault (SQL Server) kullanarak Genişletilebilir anahtar yönetimi](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)bölümüne bakın.

## <a name="service-managed-transparent-data-encryption"></a>Hizmet tarafından yönetilen saydam veri şifrelemesi

Azure 'da, saydam veri şifrelemesi için varsayılan ayar, veritabanı şifreleme anahtarının yerleşik bir sunucu sertifikasıyla korunansıdır. Yerleşik sunucu sertifikası her bir sunucu için benzersizdir ve kullanılan şifreleme algoritması AES 256 ' dir. Bir veritabanı coğrafi çoğaltma ilişkisinde ise, birincil ve coğrafi ikincil veritabanı birincil veritabanının üst sunucu anahtarı tarafından korunur. İki veritabanı aynı sunucuya bağlıysa aynı zamanda aynı yerleşik sertifikayı paylaşır.  Microsoft bu sertifikaları iç güvenlik ilkesiyle uyumlu olarak otomatik olarak döndürür ve kök anahtar Microsoft iç gizli dizisi tarafından korunur.  Müşteriler, [Microsoft Güven Merkezi](https://servicetrust.microsoft.com/)'nde bulunan bağımsız üçüncü taraf denetim raporlarında iç GÜVENLIK Ilkeleriyle SQL veritabanı uyumluluğunu doğrulayabilirler.

Microsoft ayrıca, coğrafi çoğaltma ve geri yükleme için gerektiğinde anahtarları sorunsuzca taşımalı ve yönetir.

> [!IMPORTANT]
> Yeni oluşturulan tüm SQL veritabanları ve yönetilen örnek veritabanları, hizmet tarafından yönetilen saydam veri şifrelemesi kullanılarak varsayılan olarak şifrelenir. 2017 Mayıs 'tan önce oluşturulan mevcut SQL veritabanları ve geri yükleme, coğrafi çoğaltma ve veritabanı kopyası kullanılarak oluşturulan SQL veritabanları varsayılan olarak şifrelenmez. 2019 Şubat 'tan önce oluşturulan mevcut yönetilen örnek veritabanları varsayılan olarak şifrelenmez. Restore ile oluşturulan yönetilen örnek veritabanları, kaynaktan şifreleme durumunu devralma.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Müşteri tarafından yönetilen saydam veri şifrelemesi-Kendi Anahtarını Getir

[Azure Key Vault içindeki müşteri tarafından yönetilen anahtarlarla birlikte](transparent-data-encryption-byok-azure-sql.md) , veritabanı şifreleme anahtarını (dek) TDE koruyucusu adlı, müşteri tarafından yönetilen bir asimetrik anahtarla şifrelemeyi sağlar.  Bu Ayrıca, Saydam Veri Şifrelemesi için genellikle Kendi Anahtarını Getir (BYOK) desteği olarak adlandırılır. BYOK senaryosunda, TDE koruyucusu, Azure 'un bulut tabanlı dış anahtar yönetim sistemi olan, müşterinin sahip olduğu ve yönetilen [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)depolanır. TDE koruyucusu, [Anahtar Kasası tarafından oluşturulabilir veya](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) ŞIRKET içi HSM cihazından anahtar kasasına aktarılabilir. Bir veritabanının önyükleme sayfasında depolanan TDE, Azure Key Vault ' de depolanan ve anahtar kasasını hiçbir şekilde bırakmayan TDE koruyucusu tarafından şifrelenir ve şifresi çözülür.  SQL veritabanı 'nın, DEK şifre çözme ve şifreleme için müşterinin sahip olduğu Anahtar Kasası 'na izin verilmesi gerekir. Mantıksal SQL Server 'ın Anahtar Kasası için izinleri iptal edildiğinde, bir veritabanına erişilemez ve tüm veriler şifrelenir. Azure SQL veritabanı için, TDE koruyucusu mantıksal SQL Server düzeyinde ayarlanır ve bu sunucuyla ilişkili tüm veritabanları tarafından devralınır. [Azure SQL yönetilen örneği](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)IÇIN, TDE koruyucusu örnek düzeyinde ayarlanır ve bu örnekteki tüm *şifreli* veritabanları tarafından devralınır. *Sunucu* terimi, farklı belirtilmedikçe, bu belge boyunca hem sunucu hem de örneğe başvurur.

TDE Azure Key Vault tümleştirmeyle, kullanıcılar anahtar döndürmeler, Anahtar Kasası izinleri, anahtar yedeklemeleri dahil olmak üzere önemli yönetim görevlerini denetleyebilir ve Azure Key Vault işlevselliğini kullanarak tüm TDE koruyucuda denetim/raporlamayı etkinleştirebilir. Key Vault, merkezi anahtar yönetimi sağlar, sıkı izlenen donanım güvenlik modüllerini (HSM 'ler) kullanır ve güvenlik ilkeleriyle uyumluluğu karşılamak için anahtar ve veri yönetimi arasında görev ayrımı sağlar.
Azure SQL veritabanı, SQL yönetilen örneği ve veri ambarı için Azure Key Vault Tümleştirme (Kendi Anahtarını Getir desteği) ile saydam veri şifrelemesi hakkında daha fazla bilgi edinmek için bkz. [Azure Key Vault tümleştirme Ile saydam veri şifrelemesi](transparent-data-encryption-byok-azure-sql.md).

Azure Key Vault Tümleştirme (Kendi Anahtarını Getir desteği) ile saydam veri şifrelemeyi kullanmaya başlamak için, [PowerShell kullanarak Key Vault kendi anahtarınızı kullanarak saydam veri şifrelemesini etkinleştirme](transparent-data-encryption-byok-azure-sql-configure.md)bölümüne bakın.

## <a name="move-a-transparent-data-encryption-protected-database"></a>Saydam veri şifrelemesi korumalı veritabanını taşıma

Azure 'daki işlemler için veritabanlarının şifresini çözmeniz gerekmez. Kaynak veritabanındaki veya birincil veritabanındaki saydam veri şifrelemesi ayarları, hedefte saydam olarak devralınır. Dahil edilen işlemler şunları içerir:

- Coğrafi Geri Yükleme
- Self servis zaman içinde geri yükleme
- Silinen bir veritabanının geri yüklenmesi
- Etkin coğrafi çoğaltma
- Veritabanı kopyası oluşturma
- Yedekleme dosyasını Azure SQL yönetilen örneğine geri yükleme

> [!IMPORTANT]
> Şifreleme için kullanılan sertifikaya erişilemediğinden, Azure SQL yönetilen örneği 'nde yalnızca hizmet tarafından yönetilen TDE tarafından şifrelenen bir veritabanının yedeğinin alınmasına izin verilmez. Bu tür bir veritabanını başka bir yönetilen örneğe taşımak için zaman noktası geri yükleme özelliğini kullanın.

Saydam bir veri şifreleme korumalı veritabanını dışarı aktardığınızda, veritabanının dışarı aktarılmış içeriği şifrelenmez. Bu içe aktarılmış içerik şifrelenmemiş BACPAC dosyalarında depolanır. BACPAC dosyalarını uygun şekilde koruduğunuzdan emin olun ve yeni veritabanını içeri aktarma işlemi tamamlandıktan sonra saydam veri şifrelemeyi etkinleştirin.

Örneğin, BACPAC dosyası şirket içi SQL Server örneğinden aktarılmışsa, yeni veritabanının içeri aktarılan içeriği otomatik olarak şifrelenmez. Benzer şekilde, BACPAC dosyası bir şirket içi SQL Server örneğine aktarılmışsa, yeni veritabanı da otomatik olarak şifrelenmez.

Bir SQL veritabanına veya sunucudan dışarı aktardığınızda tek bir istisna olur. Saydam veri şifrelemesi yeni veritabanında etkinleştirilmiştir, ancak BACPAC dosyası yine de şifrelenmemiştir.


## <a name="manage-transparent-data-encryption"></a>Saydam veri şifrelemesini yönetme
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal saydam veri şifrelemesini yönetin.

Saydam veri şifrelemesini Azure portal aracılığıyla yapılandırmak için Azure sahibi, katkıda bulunan veya SQL güvenlik yöneticisi olarak bağlı olmanız gerekir.

Saydam veri şifrelemesini veritabanı düzeyinde açıp kapatabilirsiniz. Bir veritabanında saydam veri şifrelemeyi etkinleştirmek için [Azure Portal](https://portal.azure.com) gidin ve Azure yöneticinize veya katkıda bulunan hesabınızla oturum açın. Kullanıcı veritabanınız altında saydam veri şifreleme ayarlarını bulun. Varsayılan olarak, hizmet tarafından yönetilen saydam veri şifrelemesi kullanılır. Bir saydam veri şifreleme sertifikası, veritabanını içeren sunucu için otomatik olarak oluşturulur. Azure SQL yönetilen örneği için T-SQL kullanarak bir veritabanında saydam veri şifrelemeyi açın ve kapatın.

![Hizmet tarafından yönetilen saydam veri şifrelemesi](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Saydam veri şifreleme koruyucusu olarak da bilinen saydam veri şifrelemesi ana anahtarını sunucu düzeyinde ayarlarsınız. Kendi Anahtarını Getir desteği ile saydam veri şifrelemeyi kullanmak ve veritabanlarınızı Key Vault bir anahtarla korumak için, sunucunuzun altındaki saydam veri şifreleme ayarlarını açın.

![Kendi Anahtarını Getir desteği ile saydam veri şifrelemesi](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell kullanarak saydam veri şifrelemesini yönetin.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

Saydam veri şifrelemesini PowerShell aracılığıyla yapılandırmak için Azure sahibi, katkıda bulunan veya SQL güvenlik yöneticisi olarak bağlı olmanız gerekir.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Azure SQL veritabanı ve veri ambarı için cmdlet 'ler

Azure SQL veritabanı ve veri ambarı için aşağıdaki cmdlet 'leri kullanın:

| Cmdlet | Açıklama |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Bir veritabanı için saydam veri şifrelemeyi etkinleştirilir veya devre dışı bırakır|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Bir veritabanı için saydam veri şifreleme durumunu alır |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Bir veritabanı için şifreleme ilerlemesini denetler |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Bir SQL Server örneğine Key Vault anahtarı ekler |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Azure SQL veritabanı sunucusu için Key Vault anahtarlarını alır  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |SQL Server örneği için saydam veri şifreleme koruyucusunu ayarlar |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Saydam veri şifreleme koruyucusunu alır |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Bir SQL Server örneğinden Key Vault anahtarı kaldırır |
|  | |

> [!IMPORTANT]
> Azure SQL yönetilen örneği için, bir veritabanı düzeyinde saydam veri şifrelemeyi açmak ve kapatmak için T-SQL [alter database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) komutunu kullanın ve örnek düzeyinde saydam veri şifrelemesini yönetmek Için [örnek PowerShell betiğini](transparent-data-encryption-byok-azure-sql-configure.md) kontrol edin.

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Transact-SQL ' i kullanarak saydam veri şifrelemesini yönetin.

Ana veritabanında bir yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak veritabanına bağlanın.

| Komut | Açıklama |
| --- | --- |
| [ALTER DATABASE (Azure SQL veritabanı)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | ŞIFRELEMEYI ayarlama/kapatma bir veritabanının şifresini şifreler veya şifresini çözer |
| [sys. dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Bir veritabanının şifreleme durumu ve ilişkili veritabanı şifreleme anahtarları hakkında bilgi döndürür |
| [sys. dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Her bir veri ambarı düğümünün ve ilişkili veritabanı şifreleme anahtarlarının şifreleme durumu hakkında bilgi döndürür |
|  | |

Transact-SQL ' y i kullanarak, saydam veri şifreleme koruyucusunu Key Vault bir anahtara geçirebilirsiniz. PowerShell veya Azure portal kullanın.

# <a name="rest-apitabazure-restapi"></a>[REST API](#tab/azure-RESTAPI)
REST API kullanarak saydam veri şifrelemesini yönetin.

Saydam veri şifrelemesini REST API aracılığıyla yapılandırmak için Azure sahibi, katkıda bulunan veya SQL güvenlik yöneticisi olarak bağlı olmanız gerekir.
Azure SQL veritabanı ve veri ambarı için aşağıdaki komut kümesini kullanın:

| Komut | Açıklama |
| --- | --- |
|[Sunucu oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Bir SQL Server örneğine Azure Active Directory kimliği ekler (Key Vault erişim vermek için kullanılır)|
|[Sunucu anahtarı oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Bir SQL Server örneğine Key Vault anahtarı ekler|
|[Sunucu anahtarını Sil](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Bir SQL Server örneğinden Key Vault anahtarı kaldırır|
|[Sunucu anahtarlarını al](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|SQL Server örneğinden belirli bir Key Vault anahtarını alır|
|[Sunucu anahtarlarını sunucuya göre Listele](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Bir SQL Server örneği için Key Vault anahtarlarını alır |
|[Şifreleme koruyucusu oluştur veya güncelleştir](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|SQL Server örneği için saydam veri şifreleme koruyucusunu ayarlar|
|[Şifreleme koruyucusunu al](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|SQL Server örneği için saydam veri şifreleme koruyucusunu alır|
|[Şifreleme koruyucularını sunucuya göre Listele](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|SQL Server örneği için saydam veri şifreleme koruyucularını alır |
|[Saydam Veri Şifrelemesi yapılandırma oluşturma veya güncelleştirme](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Bir veritabanı için saydam veri şifrelemeyi etkinleştirilir veya devre dışı bırakır|
|[Saydam Veri Şifrelemesi yapılandırmasını al](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Bir veritabanı için saydam veri şifrelemesi yapılandırmasını alır|
|[Saydam Veri Şifrelemesi yapılandırma sonuçlarını Listele](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Bir veritabanı için şifreleme sonucunu alır|

## <a name="next-steps"></a>Sonraki adımlar

- Saydam veri şifrelemesi hakkında genel bir açıklama için bkz. [Saydam veri şifrelemesi](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Azure SQL veritabanı, Azure SQL yönetilen örneği ve veri ambarı için Kendi Anahtarını Getir desteğiyle saydam veri şifrelemesi hakkında daha fazla bilgi için, bkz. [kendi anahtarını getir desteğiyle saydam veri şifrelemesi](transparent-data-encryption-byok-azure-sql.md).
- Kendi Anahtarını Getir desteğiyle saydam veri şifrelemeyi kullanmaya başlamak için, [PowerShell kullanarak Key Vault kendi anahtarınızı kullanarak saydam veri şifrelemesini etkinleştirme](transparent-data-encryption-byok-azure-sql-configure.md)bölümüne bakın.
- Key Vault hakkında daha fazla bilgi için [Key Vault belgeler sayfasına](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)bakın.
