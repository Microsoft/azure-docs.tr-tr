---
title: SQL TDE Azure Key Vault etkinleştir
description: Azure SQL veritabanı ve veri ambarını, PowerShell veya CLı kullanarak bekleyen şifreleme için Saydam Veri Şifrelemesi (TDE) kullanmaya başlamak üzere nasıl yapılandıracağınızı öğrenin.
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
ms.openlocfilehash: 8668718f33cff1ebc0c8e159c0edcae41de3ad75
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821682"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell ve CLı: Saydam Veri Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarla etkinleştirin

Bu makalede, bir SQL veritabanı veya veri ambarı üzerindeki Saydam Veri Şifrelemesi (TDE) için Azure Key Vault bir anahtarın nasıl kullanılacağı açıklanmaktadır. TDE Azure Key Vault tümleştirme-Kendi Anahtarını Getir (BYOK) desteği hakkında daha fazla bilgi edinmek için, [Azure Key Vault 'de müşteri tarafından yönetilen anahtarlarla TDE](transparent-data-encryption-byok-azure-sql.md)sayfasını ziyaret edin. 

## <a name="prerequisites-for-powershell"></a>PowerShell önkoşulları

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

- Bu abonelikte bir Azure aboneliğiniz olması ve bir yönetici olmanız gerekir.
- [Önerilir, ancak Isteğe bağlı] TDE koruyucusu anahtar malzemesinin yerel bir kopyasını oluşturmak için bir donanım güvenlik modülü (HSM) veya yerel anahtar deposu vardır.
- Azure PowerShell yüklü ve çalışıyor olmanız gerekir. 
- TDE için kullanmak üzere bir Azure Key Vault ve anahtar oluşturun.
  - [Key Vault PowerShell yönergeleri](../key-vault/quick-create-powershell.md)
  - [Donanım güvenlik modülü (HSM) ve Key Vault kullanımı için yönergeler](../key-vault/key-vault-hsm-protected-keys.md)
    - Anahtar Kasası, TDE için aşağıdaki özelliğe sahip olmalıdır:
  - [geçici-silme](../key-vault/key-vault-ovw-soft-delete.md) ve Temizleme koruması
  - [Key Vault geçici silmeyi PowerShell ile kullanma](../key-vault/key-vault-soft-delete-powershell.md) 
- Anahtar, TDE için kullanılacak aşağıdaki özniteliklere sahip olmalıdır:
   - Sona erme tarihi yok
   - Devre dışı değil
   - Al, *sarmalama tuşu*, *anahtar sarmalama işlemini geri* *alabilir*

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>1\. Adım Sunucunuza bir Azure AD kimliği atama 

Var olan bir sunucunuz varsa, sunucunuza bir Azure AD kimliği eklemek için aşağıdakileri kullanın:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Sunucu oluşturuyorsanız, sunucu oluşturma sırasında bir Azure AD kimliği eklemek için [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 'ini Tag-Identity ile birlikte kullanın:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>2\. Adım. Sunucunuza Key Vault izinleri verin

TDE için bir anahtar kullanmadan önce sunucunuza anahtar kasasına erişim izni vermek üzere [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 'ini kullanın.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3\. Adım. Key Vault anahtarını sunucuya ekleme ve TDE koruyucuyu ayarlama


- Anahtar kasasından anahtar KIMLIĞI almak için [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) cmdlet 'ini kullanın
- Anahtarı Key Vault sunucuya eklemek için [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet 'ini kullanın.
- Anahtarı tüm sunucu kaynaklarının TDE koruyucusu olarak ayarlamak için [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.
- TDE koruyucunun amaçlanan olarak yapılandırıldığını doğrulamak için [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.

> [!Note]
> Anahtar Kasası adı ve anahtar adının birleşik uzunluğu 94 karakteri aşamaz.
> 

>[!Tip]
>Key Vault örnek bir keyId: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>4\. Adım. TDE açma 

TDE ' i açmak için [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Artık veritabanı veya veri ambarı Key Vault içindeki bir şifreleme anahtarıyla TDE etkinleştirilmiştir.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5\. Adım. Şifreleme durumunu ve şifreleme etkinliğini denetleme

Bir veritabanı veya veri ambarı için şifreleme ilerleme durumunu denetlemek için, Get [-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) ve [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) ' y i kullanın.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Diğer yararlı PowerShell cmdlet 'leri

- TDE 'yi devre dışı bırakmak için [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled"
   ```
 
- Sunucuya eklenen Key Vault anahtarlarının listesini döndürmek için [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 'ini kullanın.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Sunucudan bir Key Vault anahtarı kaldırmak için [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) komutunu kullanın.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Sorun giderme

Bir sorun oluşursa, aşağıdakileri denetleyin:
- Anahtar Kasası bulunamazsa, [Get-azsubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet 'ini kullanarak doğru abonelikte olduğunuzdan emin olun.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Yeni anahtar sunucuya eklenemezse veya yeni anahtar TDE koruyucusu olarak güncelleştirilemez, aşağıdakileri denetleyin:
   - Anahtar, bir sona erme tarihi içermemelidir
   - Anahtar, *Al*, *sarmalama tuşu*ve *sarmalama anahtar* işlemlerini etkin olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik gereksinimleriyle uyum sağlamak için bir sunucunun TDE koruyucusunu döndürme hakkında bilgi edinin: [PowerShell kullanarak saydam veri şifrelemesi koruyucusunu döndürün](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Güvenlik riskine karşı, riskli olabilecek bir TDE koruyucuyu kaldırmayı öğrenin: [riskli olabilecek bir anahtarı kaldırın](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>CLı önkoşulları

- Bu abonelikte bir Azure aboneliğiniz olması ve bir yönetici olmanız gerekir.
- [Önerilir, ancak Isteğe bağlı] TDE koruyucusu anahtar malzemesinin yerel bir kopyasını oluşturmak için bir donanım güvenlik modülü (HSM) veya yerel anahtar deposu vardır.
- Komut satırı arabirimi sürüm 2,0 veya üzeri. En son sürümü yüklemek ve Azure aboneliğinize bağlanmak için bkz. [Azure platformlar arası komut satırı arabirimi 2,0 'ı yüklemek ve yapılandırmak](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- TDE için kullanmak üzere bir Azure Key Vault ve anahtar oluşturun.
  - [CLı 2,0 kullanarak Key Vault yönetme](../key-vault/key-vault-manage-with-cli2.md)
  - [Donanım güvenlik modülü (HSM) ve Key Vault kullanımı için yönergeler](../key-vault/key-vault-hsm-protected-keys.md)
    - Anahtar Kasası, TDE için aşağıdaki özelliğe sahip olmalıdır:
  - [geçici silme](../key-vault/key-vault-ovw-soft-delete.md)
  - [Key Vault geçici silmeyi CLI ile kullanma](../key-vault/key-vault-soft-delete-cli.md) 
- Anahtar, TDE için kullanılacak aşağıdaki özniteliklere sahip olmalıdır:
   - Sona erme tarihi yok
   - Devre dışı değil
   - Al, *sarmalama tuşu*, *anahtar sarmalama işlemini geri* *alabilir*
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>1\. Adım Azure AD kimliğiyle sunucu oluşturma
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>"PrincipalId" sunucusunu sunucu oluşturmaktan tutun, bir sonraki adımda Anahtar Kasası izinleri atamak için kullanılan nesne kimliğidir
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>2\. Adım. Mantıksal SQL Server 'a Key Vault izinleri verme
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Yeni anahtarın anahtar URI 'sini veya keyId değerini bir sonraki adım için tutun, örneğin: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>3\. Adım. Key Vault anahtarını sunucuya ekleme ve TDE koruyucuyu ayarlama
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Anahtar Kasası adı ve anahtar adının birleşik uzunluğu 94 karakteri aşamaz.
> 

  
## <a name="step-4-turn-on-tde"></a>4\. Adım. TDE açma 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Artık veritabanı veya veri ambarı Azure Key Vault içinde müşteri tarafından yönetilen bir şifreleme anahtarıyla TDE etkinleştirilmiştir.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>5\. Adım. Şifreleme durumunu ve şifreleme etkinliğini denetleme

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL CLı başvuruları

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

