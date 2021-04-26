---
title: Saydam veri şifrelemesi
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE Analytics için saydam veri şifrelemeye genel bakış. Belge, hizmet tarafından yönetilen saydam veri şifrelemesini ve Kendi Anahtarını Getir dahil olmak üzere avantajlarının yanı sıra yapılandırma seçeneklerini de kapsar.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 10/12/2020
ms.openlocfilehash: f93d65b4d10c1a8454a8e24b5cb081dae4d6943e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812813"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE Analytics için saydam veri şifrelemesi
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Saydam veri şifrelemesi (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) , bekleyen verileri şifreleyerek kötü amaçlı çevrimdışı etkinlik tehditlerine KARŞı Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE analizlerinin korunmasına yardımcı olur. Bu özellik bütün bir veritabanı, yedekleri ve işlem günlüğü dosyaları için gerçek zamanlı şifreleme ve şifre çözme işlemlerini gerçekleştirir ve uygulamada değişiklik yapmayı gerektirmez. Varsayılan olarak, TDE, tüm yeni dağıtılan SQL veritabanları için etkinleştirilmiştir ve Azure SQL veritabanı, Azure SQL yönetilen örneği 'nin eski veritabanları için el ile etkinleştirilmelidir. TDE, Azure SYNAPSE Analytics için el ile etkinleştirilmelidir.

TDE, verilerin sayfa düzeyindeki gerçek zamanlı g/ç şifrelemesini ve şifresini çözmeyi gerçekleştirir. Okunarak belleğe alınan her sayfanın şifresi çözülür ve sayfalar diske yazılmadan önce şifrelenir. TDE, veritabanı şifreleme anahtarı (DEK) adlı bir simetrik anahtar kullanarak veritabanının tamamının depolanmasını şifreler. Veritabanı başlangıcında, şifrelenen DEK şifresi çözülür ve veritabanı dosyalarının şifresini çözmek ve SQL Server veritabanı altyapısı işleminde yeniden şifrelemek için kullanılır. DEK, TDE koruyucusu tarafından korunur. TDE koruyucusu, hizmet tarafından yönetilen bir sertifika (hizmet tarafından yönetilen saydam veri şifrelemesi) veya [Azure Key Vault](../../key-vault/general/security-features.md) depolanan bir asimetrik anahtardır (müşteri tarafından yönetilen saydam veri şifrelemesi).

Azure SQL veritabanı ve Azure SYNAPSE için, TDE koruyucusu [sunucu](logical-servers.md) düzeyinde ayarlanır ve bu sunucuyla ilişkili tüm veritabanları tarafından devralınır. Azure SQL yönetilen örneği için, TDE koruyucusu örnek düzeyinde ayarlanır ve bu örnekteki tüm şifreli veritabanları tarafından devralınır. *Sunucu* terimi, farklı belirtilmedikçe, bu belge boyunca hem sunucu hem de örneğe başvurur.

> [!IMPORTANT]
> SQL veritabanı 'ndaki yeni oluşturulan tüm veritabanları, hizmet tarafından yönetilen saydam veri şifrelemesi kullanılarak varsayılan olarak şifrelenir. 2017 Mayıs 'tan önce oluşturulan mevcut SQL veritabanları ve geri yükleme, coğrafi çoğaltma ve veritabanı kopyası kullanılarak oluşturulan SQL veritabanları varsayılan olarak şifrelenmez. 2019 Şubat 'tan önce oluşturulan mevcut SQL yönetilen örnek veritabanları varsayılan olarak şifrelenmez. Restore aracılığıyla oluşturulan SQL yönetilen örnek veritabanları, kaynaktan şifreleme durumunu devralma.

> [!NOTE]
> TDE, Azure SQL veritabanı ve Azure SQL yönetilen örneğindeki **ana** veritabanı gibi sistem veritabanlarını şifrelemek için kullanılamaz. **Ana** veritabanı, Kullanıcı veritabanlarında TDE işlemleri gerçekleştirmek için gereken nesneleri içerir. Gizli verilerin sistem veritabanlarında depolanması önerilir. [Altyapı şifrelemesi](transparent-data-encryption-byok-overview.md#doubleencryption) , ana dahil sistem veritabanlarını şifreleyen bir şekilde kullanıma sunulmuştur. 

## <a name="service-managed-transparent-data-encryption"></a>Hizmet tarafından yönetilen saydam veri şifrelemesi

Azure 'da, TDE için varsayılan ayar, DEK 'ın yerleşik bir sunucu sertifikasıyla korunmakta olması. Yerleşik sunucu sertifikası her bir sunucu için benzersizdir ve kullanılan şifreleme algoritması AES 256 ' dir. Bir veritabanı coğrafi çoğaltma ilişkisinde ise, birincil ve coğrafi ikincil veritabanları birincil veritabanının üst sunucu anahtarı tarafından korunur. İki veritabanı aynı sunucuya bağlıysa aynı zamanda aynı yerleşik sertifikayı paylaşır. Microsoft bu sertifikaları iç güvenlik ilkesiyle uyumlu olarak otomatik olarak döndürür ve kök anahtar Microsoft iç gizli dizisi tarafından korunur. Müşteriler, [Microsoft Güven Merkezi](https://servicetrust.microsoft.com/)'nde bulunan bağımsız üçüncü taraf denetim raporlarında iç GÜVENLIK Ilkeleriyle SQL VERITABANı ve SQL yönetilen örnek uyumluluğunu doğrulayabilirler.

Microsoft ayrıca, coğrafi çoğaltma ve geri yükleme için gerektiğinde anahtarları sorunsuzca taşımalı ve yönetir.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Müşteri tarafından yönetilen saydam veri şifrelemesi-Kendi Anahtarını Getir

Ayrıca, müşteri tarafından yönetilen TDE, TDE için Kendi Anahtarını Getir (BYOK) desteği olarak adlandırılır. Bu senaryoda, 1 ' i şifreleyen TDE koruyucu, müşterinin sahip olduğu ve yönetilen bir Azure Key Vault (Azure 'un bulut tabanlı dış anahtar yönetim sistemi) depolanan ve anahtar kasasını hiçbir şekilde bırakmayan, müşteri tarafından yönetilen bir asimetrik anahtardır. TDE koruyucusu, [Anahtar Kasası tarafından oluşturulabilir veya](../../key-vault/keys/hsm-protected-keys.md) şirket içi donanım güvenlik modülü (HSM) cihazından anahtar kasasına aktarılabilir. SQL veritabanı, SQL yönetilen örneği ve Azure SYNAPSE 'in, DEK şifresini çözmek ve şifrelemek için müşterinin sahip olduğu anahtar kasasında izin verilmesi gerekir. Sunucu için Anahtar Kasası izinleri iptal edildiğinde, bir veritabanına erişilemez ve tüm veriler şifrelenir

TDE Azure Key Vault tümleştirmeyle, kullanıcılar anahtar döndürmeler, Anahtar Kasası izinleri, anahtar yedeklemeleri dahil olmak üzere önemli yönetim görevlerini denetleyebilir ve Azure Key Vault işlevselliğini kullanarak tüm TDE koruyucuda denetim/raporlamayı etkinleştirebilir. Key Vault merkezi anahtar yönetimi sağlar, tam olarak izlenen HSM 'leri kullanır ve güvenlik ilkeleriyle uyumluluğu karşılamak için anahtar ve veri yönetimi arasında görev ayrımı sağlar.
Azure SQL veritabanı ve Azure SYNAPSE için BYOK hakkında daha fazla bilgi için bkz. [Azure Key Vault tümleştirme Ile saydam veri şifreleme](transparent-data-encryption-byok-overview.md).

TDE Azure Key Vault tümleştirmeyle kullanmaya başlamak için Key Vault kendi anahtarınızı kullanarak, bkz. nasıl yapılır Kılavuzu, [Saydam veri şifrelemesini etkinleştirme](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Saydam veri şifrelemesi korumalı veritabanını taşıma

Azure 'daki işlemler için veritabanlarının şifresini çözmeniz gerekmez. Kaynak veritabanındaki veya birincil veritabanındaki TDE ayarları, hedefte saydam olarak devralınır. Dahil edilen işlemler şunları içerir:

- Coğrafi geri yükleme
- Self servis zaman içinde geri yükleme
- Silinen bir veritabanının geri yüklenmesi
- Etkin coğrafi çoğaltma
- Veritabanı kopyası oluşturma
- Yedekleme dosyasını Azure SQL yönetilen örneğine geri yükleme

> [!IMPORTANT]
> Şifreleme için kullanılan sertifikaya erişilemediğinden, el ile kopya alma, Azure SQL yönetilen örneği 'nde yalnızca hizmet tarafından yönetilen TDE tarafından şifrelenen bir veritabanının yedeklemesi desteklenmez. Bu tür bir veritabanını başka bir SQL yönetilen örneğine taşımak veya müşteri tarafından yönetilen anahtara geçiş yapmak için zaman içinde geri yükleme özelliğini kullanın.

Bir TDE korumalı veritabanını dışarı aktardığınızda, veritabanının dışarı aktarılmış içeriği şifrelenmez. Bu içe aktarılmış içerik şifrelenmemiş BACPAC dosyalarında depolanır. BACPAC dosyalarını uygun şekilde koruduğunuzdan emin olun ve yeni veritabanını içeri aktarma işlemi tamamlandıktan sonra TDE 'ı etkinleştirin.

Örneğin, BACPAC dosyası bir SQL Server örneğinden aktarılmışsa, yeni veritabanının içeri aktarılan içeriği otomatik olarak şifrelenmez. Benzer şekilde, BACPAC dosyası bir SQL Server örneğine aktarılmışsa, yeni veritabanı da otomatik olarak şifrelenmez.

Bir özel durum, SQL veritabanından ve bir veritabanını dışarı aktardığınızda olur. TDE, yeni veritabanında etkinleştirilmiştir, ancak BACPAC dosyası hala şifrelenmemiştir.

## <a name="manage-transparent-data-encryption"></a>Saydam veri şifrelemesini yönetme

# <a name="the-azure-portal"></a>[Azure portal](#tab/azure-portal)

Azure portal de TDE yönetin.

TDE Azure portal aracılığıyla yapılandırmak için Azure sahibi, katkıda bulunan veya SQL güvenlik yöneticisi olarak bağlı olmanız gerekir.

Veritabanı düzeyinde TDE 'ı etkinleştirin ve devre dışı bırakın. Azure SQL yönetilen örneği için Transact-SQL (T-SQL) kullanarak bir veritabanında TDE açın ve kapatın. Azure SQL veritabanı ve Azure SYNAPSE için, Azure yönetici veya katkıda bulunan hesabıyla oturum açtıktan sonra [Azure Portal](https://portal.azure.com) veritabanı için TDE yönetebilirsiniz. Kullanıcı veritabanınız altındaki TDE ayarlarını bulun. Varsayılan olarak, hizmet tarafından yönetilen saydam veri şifrelemesi kullanılır. Bir TDE sertifikası, veritabanını içeren sunucu için otomatik olarak oluşturulur.

![Hizmet tarafından yönetilen saydam veri şifrelemesi](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

TDE koruyucusu olarak bilinen TDE ana anahtarını sunucu veya örnek düzeyinde ayarlarsınız. BYOK desteği ile TDE kullanmak ve veritabanlarınızı Key Vault bir anahtarla korumak için, sunucunuzun altındaki TDE ayarlarını açın.

![Kendi Anahtarını Getir desteği ile saydam veri şifrelemesi](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 'i kullanarak TDE yönetin.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

TDE PowerShell 'i yapılandırmak için Azure sahibi, katkıda bulunan veya SQL güvenlik yöneticisi olarak bağlı olmanız gerekir.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Azure SQL veritabanı ve Azure SYNAPSE için cmdlet 'ler

Azure SQL veritabanı ve Azure SYNAPSE için aşağıdaki cmdlet 'leri kullanın:

| Cmdlet | Açıklama |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Bir veritabanı için saydam veri şifrelemeyi etkinleştirilir veya devre dışı bırakır.|
| [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Bir veritabanı için saydam veri şifreleme durumunu alır. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Bir veritabanı için şifreleme ilerleme durumunu denetler. |
| [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Bir sunucuya Key Vault anahtarı ekler. |
| [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Bir sunucu için Key Vault anahtarlarını alır  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Bir sunucu için saydam veri şifreleme koruyucusunu ayarlar. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Saydam veri şifreleme koruyucusunu alır |
| [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Bir Key Vault anahtarı sunucudan kaldırır. |
|  | |

> [!IMPORTANT]
> Azure SQL yönetilen örneği için T-SQL [alter database](/sql/t-sql/statements/alter-database-azure-sql-database) komutunu kullanarak bir veritabanı düzeyinde TDE açın ve kapatın ve örnek düzeyinde TDE 'yi yönetmek Için [örnek PowerShell betiğini](transparent-data-encryption-byok-configure.md) denetleyin.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Transact-SQL kullanarak TDE yönetin.

Ana veritabanında bir yönetici veya **DBManager** rolünün üyesi olan bir oturum kullanarak veritabanına bağlanın.

| Komut | Açıklama |
| --- | --- |
| [ALTER DATABASE (Azure SQL veritabanı)](/sql/t-sql/statements/alter-database-azure-sql-database) | ŞIFRELEMEYI ayarlama/kapatma bir veritabanının şifresini şifreler veya şifresini çözer |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Bir veritabanının şifreleme durumu ve ilişkili veritabanı şifreleme anahtarları hakkında bilgi döndürür |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Her bir Azure SYNAPSE düğümünün ve ilişkili veritabanı şifreleme anahtarlarının şifreleme durumu hakkında bilgi döndürür |
|  | |

Transact-SQL ' y i kullanarak, TDE koruyucusunu Key Vault bir anahtara geçirebilirsiniz. PowerShell veya Azure portal kullanın.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

REST API kullanarak TDE yönetin.

TDE REST API aracılığıyla yapılandırmak için Azure sahibi, katkıda bulunan veya SQL güvenlik yöneticisi olarak bağlı olmanız gerekir.
Azure SQL veritabanı ve Azure SYNAPSE için aşağıdaki komut kümesini kullanın:

| Komut | Açıklama |
| --- | --- |
|[Sunucu oluştur veya güncelleştir](/rest/api/sql/servers/createorupdate)|Bir sunucuya Azure Active Directory kimliği ekler. (Key Vault erişim vermek için kullanılır)|
|[Sunucu anahtarı oluştur veya güncelleştir](/rest/api/sql/serverkeys/createorupdate)|Bir sunucuya Key Vault anahtarı ekler.|
|[Sunucu anahtarını Sil](/rest/api/sql/serverkeys/delete)|Bir Key Vault anahtarı sunucudan kaldırır. |
|[Sunucu anahtarlarını al](/rest/api/sql/serverkeys/get)|Bir sunucudan belirli bir Key Vault anahtarını alır.|
|[Sunucu anahtarlarını sunucuya göre Listele](/rest/api/sql/serverkeys/listbyserver)|Bir sunucu için Key Vault anahtarlarını alır. |
|[Şifreleme koruyucusu oluştur veya güncelleştir](/rest/api/sql/encryptionprotectors/createorupdate)|Bir sunucu için TDE koruyucuyu ayarlar.|
|[Şifreleme koruyucusunu al](/rest/api/sql/encryptionprotectors/get)|Bir sunucu için TDE koruyucuyu alır.|
|[Şifreleme koruyucularını sunucuya göre Listele](/rest/api/sql/encryptionprotectors/listbyserver)|Bir sunucunun TDE koruyucularını alır. |
|[Saydam Veri Şifrelemesi yapılandırma oluşturma veya güncelleştirme](/rest/api/sql/transparentdataencryptions/createorupdate)|Bir veritabanı için TDE TDE devre dışı bırakır|
|[Saydam Veri Şifrelemesi yapılandırmasını al](/rest/api/sql/transparentdataencryptions/get)|Bir veritabanı için TDE yapılandırmasını alır.|
|[Saydam Veri Şifrelemesi yapılandırma sonuçlarını Listele](/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Bir veritabanı için şifreleme sonucunu alır.|

## <a name="see-also"></a>Ayrıca Bkz.

- Azure sanal makinesinde çalışan SQL Server, Key Vault bir asimetrik anahtar da kullanabilir. Yapılandırma adımları SQL veritabanı ve SQL yönetilen örneği 'nde asimetrik anahtar kullanmaktan farklıdır. Daha fazla bilgi için [Azure Key Vault (SQL Server) kullanarak Genişletilebilir anahtar yönetimi](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)bölümüne bakın.
- TDE genel bir açıklaması için bkz. [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Azure SQL veritabanı, Azure SQL yönetilen örneği ve Azure SYNAPSE için BYOK desteği ile TDE hakkında daha fazla bilgi edinmek için [kendi anahtarını getir desteğiyle saydam veri şifrelemesi](transparent-data-encryption-byok-overview.md)bölümüne bakın.
- Kendi Anahtarını Getir desteği ile TDE kullanmaya başlamak için, Key Vault ' de [kendi anahtarınızı kullanarak](transparent-data-encryption-byok-configure.md)nasıl yapılır Kılavuzu ' na bakın.
- Key Vault hakkında daha fazla bilgi için bkz. [bir anahtar kasasına güvenli erişim](../../key-vault/general/security-features.md).