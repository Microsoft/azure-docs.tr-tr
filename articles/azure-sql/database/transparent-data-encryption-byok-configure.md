---
title: SQL TDE Azure Key Vault etkinleştir
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Azure SQL veritabanı ve Azure SYNAPSE analizlerini, PowerShell veya Azure CLı kullanarak bekleyen şifreleme için Saydam Veri Şifrelemesi (TDE) kullanmaya başlamak üzere nasıl yapılandıracağınızı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: b82c42d27d05cd84cf7fc59b711c24794a8e2f17
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314023"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell ve Azure CLı: Saydam Veri Şifrelemesi Azure Key Vault müşteri tarafından yönetilen anahtarla etkinleştirin
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Bu makalede, Azure SQL veritabanı veya Azure SYNAPSE Analytics 'te Saydam Veri Şifrelemesi (TDE) için Azure Key Vault bir anahtarın nasıl kullanılacağı açıklanmaktadır. TDE Azure Key Vault tümleştirme-Kendi Anahtarını Getir (BYOK) desteği hakkında daha fazla bilgi edinmek için, [Azure Key Vault 'de müşteri tarafından yönetilen anahtarlarla TDE](transparent-data-encryption-byok-overview.md)sayfasını ziyaret edin.

> [!NOTE] 
> Azure SQL artık, yönetilen bir HSM 'de TDE koruyucusu olarak depolanan bir RSA anahtarının kullanılmasını desteklemektedir. Bu özellik **genel önizlemede**. Azure Key Vault yönetilen HSM, FIPS 140-2 düzey 3 tarafından doğrulanan HSM 'leri kullanarak bulut uygulamalarınızın şifreleme anahtarlarını korumanıza olanak sağlayan, tam olarak yönetilen, yüksek oranda kullanılabilir, tek kiracılı ve standartlara uygun bir bulut hizmetidir. [Yönetilen HSM](../../key-vault/managed-hsm/index.yml)'ler hakkında daha fazla bilgi edinin.

## <a name="prerequisites-for-powershell"></a>PowerShell önkoşulları

- Bu abonelikte bir Azure aboneliğiniz olması ve bir yönetici olmanız gerekir.
- [Önerilir, ancak Isteğe bağlı] TDE koruyucusu anahtar malzemesinin yerel bir kopyasını oluşturmak için bir donanım güvenlik modülü (HSM) veya yerel anahtar deposu vardır.
- Azure PowerShell yüklü ve çalışıyor olmanız gerekir.
- TDE için kullanmak üzere bir Azure Key Vault ve anahtar oluşturun.
  - [Donanım güvenlik modülü (HSM) ve Key Vault kullanımı için yönergeler](../../key-vault/keys/hsm-protected-keys.md)
    - Anahtar Kasası, TDE için aşağıdaki özelliğe sahip olmalıdır:
  - [geçici-silme](../../key-vault/general/soft-delete-overview.md) ve Temizleme koruması
- Anahtar, TDE için kullanılacak aşağıdaki özniteliklere sahip olmalıdır:
  - Son kullanma tarihi yok
  - Devre dışı bırakılmadı
  - Al, *sarmalama tuşu*, *anahtar sarmalama işlemini geri* *alabilir*
- **(Önizlemede)** Yönetilen bir HSM anahtarı kullanmak için [Azure CLI kullanarak yönetilen BIR HSM oluşturma ve etkinleştirme](../../key-vault/managed-hsm/quick-create-cli.md) yönergelerini izleyin

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az modülü yükleme yönergeleri için bkz. [Azure PowerShell'i yükleme](/powershell/azure/install-az-ps). Belirli cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/).

Key Vault hakkında daha fazla bilgi için bkz. [Key Vault PowerShell yönergeleri](../../key-vault/secrets/quick-create-powershell.md) ve [PowerShell ile geçici silme Key Vault kullanma](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. AzureRM modülü, en az Aralık 2020 ' e kadar hata düzeltmeleri almaya devam edecektir.  Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Uyumluluklarını hakkında daha fazla bilgi için bkz. [new Azure PowerShell konusuna giriş az Module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Sunucunuza Azure Active Directory (Azure AD) kimliği atama

Var olan bir [sunucunuz](logical-servers.md)varsa, sunucunuza Azure Active Directory (Azure AD) kimliği eklemek için aşağıdakileri kullanın:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Sunucu oluşturuyorsanız, sunucu oluşturma sırasında bir Azure AD kimliği eklemek için [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet 'ini Tag-Identity ile birlikte kullanın:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Sunucunuza Key Vault izinleri verin

TDE için bir anahtar kullanmadan önce sunucunuza anahtar kasasına erişim izni vermek üzere [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 'ini kullanın.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
Yönetilen bir HSM 'de sunucunuza izinler eklemek için, ' yönetilen HSM şifre hizmeti şifrelemesi ' yerel RBAC rolünü sunucuya ekleyin. Bu, sunucunun yönetilen HSM 'deki anahtarlar üzerinde al, sarmalama ve sarmalama işlemlerini kaldırma işlemini gerçekleştirmesini sağlayacaktır.
[Yönetilen HSM 'de sunucu erişimi sağlama yönergeleri](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault anahtarını sunucuya ekleme ve TDE koruyucuyu ayarlama

- Anahtar kasasından anahtar KIMLIĞI almak için [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) cmdlet 'ini kullanın
- Anahtarı Key Vault sunucuya eklemek için [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet 'ini kullanın.
- Anahtarı tüm sunucu kaynaklarının TDE koruyucusu olarak ayarlamak için [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.
- TDE koruyucunun amaçlanan olarak yapılandırıldığını doğrulamak için [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.

> [!NOTE]
> **(Önizlemede)** Yönetilen HSM anahtarları için, PowerShell 'in az. SQL 2.11.1 sürümünü kullanın.

> [!NOTE]
> Anahtar Kasası adı ve anahtar adının birleşik uzunluğu 94 karakteri aşamaz.

> [!TIP]
> Key Vault örnek bir keyId:<br/>https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
> Yönetilen HSM 'den örnek bir keyId:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>TDE açma

TDE ' i açmak için [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 'ini kullanın.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Artık veritabanı veya veri ambarı Key Vault içindeki bir şifreleme anahtarıyla TDE etkinleştirilmiştir.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Şifreleme durumunu ve şifreleme etkinliğini denetleme

Bir veritabanı veya veri ambarı için şifreleme ilerleme durumunu denetlemek için, Get [-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) ve [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) ' y i kullanın.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı 'nın gerekli sürümünü (sürüm 2,0 veya üzeri) yüklemek ve Azure aboneliğinize bağlanmak için bkz. [Azure platformlar arası Command-Line arabirimi 2,0 ' ni yükleyip yapılandırma](/cli/azure/install-azure-cli).

Key Vault hakkında daha fazla bilgi için bkz. [clı 2,0 kullanarak Key Vault yönetme](../../key-vault/general/manage-with-cli2.md) ve [CLI ile geçici silme Key Vault kullanma](../../key-vault/general/key-vault-recovery.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Sunucunuza bir Azure AD kimliği atama

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> "PrincipalId" sunucusunu sunucu oluşturmaktan tutun, bir sonraki adımda Anahtar Kasası izinleri atamak için kullanılan nesne kimliğidir

## <a name="grant-key-vault-permissions-to-your-server"></a>Sunucunuza Key Vault izinleri verin

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Yeni anahtarın anahtar URI 'sini veya keyId değerini bir sonraki adım için tutun, örneğin: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Key Vault anahtarını sunucuya ekleme ve TDE koruyucuyu ayarlama

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Anahtar Kasası adı ve anahtar adının birleşik uzunluğu 94 karakteri aşamaz.

## <a name="turn-on-tde"></a>TDE açma

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Artık veritabanı veya veri ambarı Azure Key Vault içinde müşteri tarafından yönetilen bir şifreleme anahtarıyla TDE etkinleştirilmiştir.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Şifreleme durumunu ve şifreleme etkinliğini denetleme

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Yararlı PowerShell cmdlet 'leri

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE 'yi devre dışı bırakmak için [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Sunucuya eklenen Key Vault anahtarlarının listesini döndürmek için [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet 'ini kullanın.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Sunucudan bir Key Vault anahtarı kaldırmak için [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) komutunu kullanın.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Genel veritabanı ayarları için bkz. [az SQL](/cli/azure/sql).

- Kasa anahtarı ayarları için bkz. [az SQL Server Key](/cli/azure/sql/server/key).

- TDE ayarları için, bkz. [az SQL Server TDE-Key](/cli/azure/sql/server/tde-key) ve [az SQL DB TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Sorun giderme

Bir sorun oluşursa, aşağıdakileri denetleyin:

- Anahtar Kasası bulunamazsa, doğru abonelikte olduğunuzdan emin olun.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Yeni anahtar sunucuya eklenemezse veya yeni anahtar TDE koruyucusu olarak güncelleştirilemez, aşağıdakileri denetleyin:
   - Anahtar, bir sona erme tarihi içermemelidir
   - Anahtar, *Al*, *sarmalama tuşu* ve *sarmalama anahtar* işlemlerini etkin olmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik gereksinimleriyle uyum sağlamak için bir sunucunun TDE koruyucusunu döndürme hakkında bilgi edinin: [PowerShell kullanarak saydam veri şifrelemesi koruyucusunu döndürün](transparent-data-encryption-byok-key-rotation.md).
- Güvenlik riskine karşı, riskli olabilecek bir TDE koruyucuyu kaldırmayı öğrenin: [riskli olabilecek bir anahtarı kaldırın](transparent-data-encryption-byok-remove-tde-protector.md).
