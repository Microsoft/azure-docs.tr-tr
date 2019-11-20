---
title: TDE koruyucusunu kaldır-PowerShell
description: Kendi anahtarını getir (BYOK) desteği ile TDE ' i kullanarak bir Azure SQL veritabanı veya veri ambarı için olası riskli bir TDE için nasıl yapılır Kılavuzu.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: df1bf5a53cd5c49465acbe363c71a4a316cd2cc9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820787"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell kullanarak Saydam Veri Şifrelemesi (TDE) koruyucusunu kaldırma

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

- Bu abonelikte bir Azure aboneliğinizin olması ve bir yönetici olmanız gerekir
- Azure PowerShell yüklü ve çalışıyor olmanız gerekir. 
- Bu nasıl yapılır kılavuzunda, bir Azure SQL veritabanı veya veri ambarı için TDE koruyucusu olarak Azure Key Vault bir anahtar kullandığınızı varsaymış olursunuz. Daha fazla bilgi için bkz. [BYOK desteği ile saydam veri şifrelemesi](transparent-data-encryption-byok-azure-sql.md) .

## <a name="overview"></a>Genel Bakış

Bu nasıl yapılır kılavuzunda, Azure Key Vault-Kendi Anahtarını Getir (BYOK) desteği 'nde müşteri tarafından yönetilen anahtarlarla TDE kullanan bir Azure SQL veritabanı veya veri ambarı için olası riskli bir TDE için nasıl yanıt verileceğini açıklanmaktadır. TDE için BYOK desteği hakkında daha fazla bilgi edinmek için [genel bakış sayfasına](transparent-data-encryption-byok-azure-sql.md)bakın. 

Aşağıdaki yordamlar yalnızca olağanüstü durumlarda veya test ortamlarında yapılmalıdır. Azure Key Vault ' den etkin olarak kullanılan TDE koruyucuları silme, **veri kaybına**neden olabilir. 

Bir anahtarın tehlikeye atılmaya şüphelenildiği durumlarda, bir hizmet veya kullanıcının anahtara yetkisiz erişimi olması gibi, anahtarın silinmesi en iyisidir.

Key Vault ' de TDE koruyucusu silindikten sonra, **sunucu altındaki şifreli veritabanlarına yapılan tüm bağlantıların engellendiğini ve bu veritabanlarının çevrimdışı olduğunu ve 24 saat içinde bırakılacağını**unutmayın. Güvenliği aşılmış anahtarla şifrelenen eski yedeklemeler artık erişilebilir değil.

Aşağıdaki adımlarda, belirli bir veritabanının sanal günlük dosyaları (VLF) tarafından hala kullanımda olan TDE Protector parmak izlerinin nasıl denetinin yapılacağı ana hatlarıyla gösterilmiştir. Veritabanının geçerli TDE koruyucunun parmak izi ve veritabanı KIMLIĞI şu şekilde çalıştırılarak bulunabilir: SELECT [database_id],       [encryption_state], [encryptor_type],/*asimetrik anahtar, AKV anlamına gelir, sertifika hizmet tarafından yönetilen anahtarlar*/[ encryptor_thumbprint], [sys] ÖĞESINDEN. [dm_database_encryption_keys] 
 
Aşağıdaki sorgu VLFs 'yi ve kullanılan parmak izlerini kullanan Şifreleyici 'yi geri döndürür. Her farklı parmak izi Azure Key Vault (AKV) içinde farklı bir anahtara başvurur: SELECT * FROM sys. dm_db_log_info (database_id) 

Get-AzureRmSqlServerKeyVaultKey PowerShell komutu, sorguda kullanılan TDE koruyucunun parmak izini sağlar, böylece saklanacak anahtarları ve AKV 'de hangi anahtarların silineceğini görebilirsiniz. Yalnızca, veritabanı tarafından artık kullanılmayan anahtarlar Azure Key Vault güvenle silinebilir.

Bu nasıl yapılır Kılavuzu, olay yanıtından sonra istenen sonuca bağlı olarak iki yaklaşımdan geçer:

- Azure SQL veritabanlarını/veri ambarlarını **erişilebilir** tutmak için
- Azure SQL veritabanlarını/veri ambarlarını **erişilemez** hale getirmek için

## <a name="to-keep-the-encrypted-resources-accessible"></a>Şifrelenmiş kaynakları erişilebilir tutmak için

1. [Key Vault yeni bir anahtar](/powershell/module/az.keyvault/add-azkeyvaultkey)oluşturun. Bu yeni anahtarın, çok riskli bir TDE koruyucudan farklı bir anahtar kasasında oluşturulduğundan emin olun, çünkü erişim denetimi bir kasa düzeyinde sağlanır.
2. [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) ve [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'lerini kullanarak yeni anahtarı sunucuya ekleyin ve sunucunun yeni TDE koruyucusu olarak güncelleştirin.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Sunucu ve çoğaltmaların, [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanarak yeni TDE koruyucuya güncelleştirildiğinden emin olun. 

   >[!NOTE]
   > Yeni TDE koruyucunun sunucu altındaki tüm veritabanlarına ve ikincil veritabanlarına yayılması birkaç dakika sürebilir.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault [Yeni anahtarın bir yedeğini](/powershell/module/az.keyvault/backup-azkeyvaultkey) alın.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet 'ini kullanarak Key Vault güvenliği aşılmış anahtarı silin. 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet 'ini kullanarak gelecekte Key Vault bir anahtarı geri yüklemek için:
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Şifrelenmiş kaynakları erişilemez hale getirmek için

1. Güvenliği aşılmış olabilecek anahtarla şifrelenen veritabanlarını bırakın.

   Veritabanı ve günlük dosyaları otomatik olarak yedeklenir, bu nedenle veritabanının bir noktadan sonra geri yüklenmesi herhangi bir noktada yapılabilir (anahtarı sağladığınız sürece). En son işlemlerin 10 dakikaya kadar olan olası veri kaybını engellemek için etkin bir TDE koruyucusu silinmeden önce veritabanlarının bırakılması gerekir. 
2. Key Vault içindeki TDE koruyucunun önemli malzemesini yedekleyin.
3. Key Vault karşı riskli anahtarı kaldırın

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik gereksinimleriyle uyum sağlamak için bir sunucunun TDE koruyucusunu döndürme hakkında bilgi edinin: [PowerShell kullanarak saydam veri şifrelemesi koruyucusunu döndürme](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- TDE için Kendi Anahtarını Getir desteği ile çalışmaya başlama: [PowerShell kullanarak Key Vault kendi anahtarınızı kullanarak TDE açma](transparent-data-encryption-byok-azure-sql-configure.md)
