---
title: TDE koruyucuyu kaldırma (Azure CLı & PowerShell)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Kendi Anahtarını Getir (BYOK) desteğiyle TDE kullanarak Azure SQL veritabanı veya Azure SYNAPSE Analytics için riskli olabilecek bir TDE için nasıl yanıt verileceğini öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: f98dcdd9c1a479703c82c01b4fd240507ea355de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784478"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell kullanarak Saydam Veri Şifrelemesi (TDE) koruyucusunu kaldırma
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Bu konuda, Azure Key Vault-Kendi Anahtarını Getir (BYOK) desteği 'nde müşteri tarafından yönetilen anahtarlarla TDE kullanan Azure SQL veritabanı veya Azure SYNAPSE Analytics 'e yönelik olası riskli bir TDE korumasına nasıl yanıt verileceğini açıklanmaktadır. TDE için BYOK desteği hakkında daha fazla bilgi edinmek için [genel bakış sayfasına](transparent-data-encryption-byok-overview.md)bakın.

> [!CAUTION]
> Bu makalede özetlenen yordamlar yalnızca olağanüstü durumlarda veya test ortamlarında yapılmalıdır. Azure Key Vault ' den etkin olarak kullanılan TDE koruyucuları silmek **veritabanının kullanılamaz hale gelmesine** neden olacak şekilde adımları dikkatle gözden geçirin.

Bir anahtarın tehlikeye atılmaya şüphelenildiği durumlarda, bir hizmet veya kullanıcının anahtara yetkisiz erişimi olması gibi, anahtarın silinmesi en iyisidir.

Key Vault ' de TDE koruyucusu silindikten sonra, en fazla 10 dakika içinde tüm şifreli veritabanlarının karşılık gelen hata iletisiyle tüm bağlantıları reddetmesinin ve durumunu [erişilemez](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)olarak değiştirmesinin başlayacağı göz önünde bulundurun.

Bu nasıl yapılır Kılavuzu, güvenliği aşılmış bir olay yanıtından sonra istenen sonuca bağlı olarak iki yaklaşımdan geçer:

- Azure SQL veritabanı/Azure SYNAPSE Analytics 'teki veritabanlarını **erişilemez** hale getirmek için.
- Azure SQL veritabanı/Azure Azure SYNAPSE Analytics 'te veritabanlarını **erişilemez** hale getirmek için.

## <a name="prerequisites"></a>Önkoşullar

- Bu abonelikte bir Azure aboneliğinizin olması ve bir yönetici olmanız gerekir
- Azure PowerShell yüklü ve çalışıyor olmanız gerekir.
- Bu nasıl yapılır kılavuzunda, bir Azure SQL veritabanı veya Azure SYNAPSE için TDE koruyucusu olarak Azure Key Vault bir anahtar kullandığınızı varsaymış olursunuz. Daha fazla bilgi için bkz. [BYOK desteği ile saydam veri şifrelemesi](transparent-data-encryption-byok-overview.md) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Az modülü yükleme yönergeleri için bkz. [Azure PowerShell'i yükleme](/powershell/azure/install-az-ps). Belirli cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. AzureRM modülü, en az Aralık 2020 ' e kadar hata düzeltmeleri almaya devam edecektir.  Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Uyumluluklarını hakkında daha fazla bilgi için bkz. [new Azure PowerShell konusuna giriş az Module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Yükleme için bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>TDE koruyucu parmak izlerini denetle

Aşağıdaki adımlarda, belirli bir veritabanının sanal günlük dosyaları (VLF) tarafından hala kullanımda olan TDE Protector parmak izlerinin nasıl denetinin yapılacağı ana hatlarıyla gösterilmiştir.
Veritabanının geçerli TDE koruyucunun parmak izi ve veritabanı KIMLIĞI şu şekilde çalıştırılarak bulunabilir:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

Aşağıdaki sorgu, kullanılan parmak izlerinin VLFs ve TDE koruyucusunu döndürür. Her farklı parmak izi Azure Key Vault (AKV) içinde farklı bir anahtara başvurur:

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Alternatif olarak, PowerShell veya Azure CLı 'yi de kullanabilirsiniz:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

**Get-AzureRmSqlServerKeyVaultKey** PowerShell komutu,   sorguda kullanılan TDE koruyucunun parmak izini sağlar, böylece saklanacak anahtarları ve Akv 'de hangi anahtarların silineceğini görebilirsiniz. Yalnızca, veritabanı tarafından artık kullanılmayan anahtarlar Azure Key Vault güvenle silinebilir.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

**Az SQL Server Key Show** PowerShell komutu,   sorguda kullanılan TDE koruyucunun parmak izini sağlar, böylece saklanacak anahtarları ve Akv 'de hangi anahtarların silineceğini görebilirsiniz. Yalnızca, veritabanı tarafından artık kullanılmayan anahtarlar Azure Key Vault güvenle silinebilir.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Şifrelenmiş kaynakları erişilebilir durumda tut

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Key Vault yeni bir anahtar](/powershell/module/az.keyvault/add-azkeyvaultkey)oluşturun. Bu yeni anahtarın, çok riskli bir TDE koruyucudan farklı bir anahtar kasasında oluşturulduğundan emin olun, çünkü erişim denetimi bir kasa düzeyinde sağlanır.

2. [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) ve [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'lerini kullanarak yeni anahtarı sunucuya ekleyin ve sunucunun yeni TDE koruyucusu olarak güncelleştirin.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Sunucu ve çoğaltmaların, [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanarak yeni TDE koruyucuya güncelleştirildiğinden emin olun.

   > [!NOTE]
   > Yeni TDE koruyucunun sunucu altındaki tüm veritabanlarına ve ikincil veritabanlarına yayılması birkaç dakika sürebilir.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Key Vault [Yeni anahtarın bir yedeğini](/powershell/module/az.keyvault/backup-azkeyvaultkey) alın.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) cmdlet 'ini kullanarak Key Vault güvenliği aşılmış anahtarı silin.

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) cmdlet 'ini kullanarak gelecekte Key Vault bir anahtarı geri yüklemek için:

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Komut başvurusu için bkz. [Azure CLI Anahtar Kasası](/cli/azure/keyvault/key).

1. [Key Vault yeni bir anahtar](/cli/azure/keyvault/key#az_keyvault_key_create)oluşturun. Bu yeni anahtarın, çok riskli bir TDE koruyucudan farklı bir anahtar kasasında oluşturulduğundan emin olun, çünkü erişim denetimi bir kasa düzeyinde sağlanır.

2. Yeni anahtarı sunucuya ekleyin ve sunucunun yeni TDE koruyucusu olarak güncelleştirin.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Sunucunun ve çoğaltmaların yeni TDE koruyucuya güncelleştirildiğinden emin olun.

   > [!NOTE]
   > Yeni TDE koruyucunun sunucu altındaki tüm veritabanlarına ve ikincil veritabanlarına yayılması birkaç dakika sürebilir.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Key Vault yeni anahtarın bir yedeğini alın.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Güvenliği aşılan anahtarı Key Vault silin.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Daha sonra Key Vault bir anahtarı geri yüklemek için.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Şifrelenmiş kaynakları erişilemez yapma

1. Güvenliği aşılmış olabilecek anahtarla şifrelenen veritabanlarını bırakın.

   Veritabanı ve günlük dosyaları otomatik olarak yedeklenir, bu nedenle veritabanının bir noktadan sonra geri yüklenmesi herhangi bir noktada yapılabilir (anahtarı sağladığınız sürece). En son işlemlerin 10 dakikaya kadar olan olası veri kaybını engellemek için etkin bir TDE koruyucusu silinmeden önce veritabanlarının bırakılması gerekir.

2. Key Vault içindeki TDE koruyucunun önemli malzemesini yedekleyin.
3. Key Vault karşı riskli anahtarı kaldırın

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik gereksinimleriyle uyum sağlamak için bir sunucunun TDE koruyucusunu döndürme hakkında bilgi edinin: [PowerShell kullanarak saydam veri şifrelemesi koruyucusunu döndürme](transparent-data-encryption-byok-key-rotation.md)
- TDE için Kendi Anahtarını Getir desteği ile çalışmaya başlama: [PowerShell kullanarak Key Vault kendi anahtarınızı kullanarak TDE açma](transparent-data-encryption-byok-configure.md)