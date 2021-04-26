---
title: TDE koruyucuyu döndürme (Azure CLı & PowerShell)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: PowerShell ve Azure CLı kullanarak Azure SQL veritabanı ve Azure SYNAPSE Analytics tarafından kullanılan Azure 'da bir sunucu için Saydam Veri Şifrelemesi (TDE) koruyucusunu döndürme hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 67bcd8597314530f26481ef840644ffbc056b033
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777572"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Saydam Veri Şifrelemesi (TDE) koruyucusunu döndürme
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Bu makalede, Azure Key Vault 'den bir TDE koruyucusu kullanan bir [sunucu](logical-servers.md) için anahtar döndürme açıklanmaktadır. Bir sunucu için mantıksal TDE koruyucuyu döndürme, sunucudaki veritabanlarını koruyan yeni bir asimetrik anahtara geçme anlamına gelir. Anahtar döndürme bir çevrimiçi işlemdir ve yalnızca birkaç saniye sürer, çünkü bu yalnızca veritabanının veri şifreleme anahtarını çözer ve veritabanının tamamını değil, yeniden şifreler.

Bu kılavuzda, sunucuda TDE koruyucusunu döndürmek için iki seçenek ele alınmaktadır.

> [!NOTE]
> Azure SYNAPSE Analytics 'te duraklatılan adanmış bir SQL havuzu, anahtar döndürmelerinde devam etmelidir.

> [!IMPORTANT]
> Bir geçişten sonra anahtarın önceki sürümlerini silmeyin. Anahtarlar devralındığında, bazı veriler, daha eski veritabanı yedeklemeleri gibi önceki anahtarlarla de şifrelenir.

## <a name="prerequisites"></a>Önkoşullar

- Bu nasıl yapılır kılavuzunda, Azure SQL veritabanı veya Azure SYNAPSE Analytics için TDE koruyucusu olarak Azure Key Vault bir anahtar kullandığınızı varsaymış olursunuz. Bkz. [BYOK desteği ile saydam veri şifrelemesi](transparent-data-encryption-byok-overview.md).
- Azure PowerShell yüklü ve çalışıyor olmanız gerekir.
- [Önerilir, ancak isteğe bağlı] Önce bir donanım güvenlik modülünde (HSM) veya yerel anahtar deposunda TDE koruyucusu için anahtar malzemesi oluşturun ve Azure Key Vault için anahtar malzemesini içeri aktarın. Daha fazla bilgi edinmek için [donanım güvenlik modülü (HSM) kullanma ve Key Vault yönergelerini](../../key-vault/general/overview.md) izleyin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az modülü yükleme yönergeleri için bkz. [Azure PowerShell'i yükleme](/powershell/azure/install-az-ps). Belirli cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> PowerShell Azure Resource Manager (RM) modülü hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. AzureRM modülü, en az Aralık 2020 ' e kadar hata düzeltmeleri almaya devam edecektir.  Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Uyumluluklarını hakkında daha fazla bilgi için bkz. [new Azure PowerShell konusuna giriş az Module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Yükleme için bkz. [Azure CLI 'Yı yükleme](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>El ile anahtar döndürme

El ile anahtar döndürme, yeni bir anahtar adı ya da başka bir Anahtar Kasası altında olabilecek tamamen yeni bir anahtar eklemek için aşağıdaki komutları kullanır. Bu yaklaşımın kullanılması, yüksek kullanılabilirlik ve coğrafi Dr senaryolarını desteklemek üzere farklı anahtar kasalarına aynı anahtarı eklemeyi destekler.

> [!NOTE]
> Anahtar Kasası adı ve anahtar adının birleşik uzunluğu 94 karakteri aşamaz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)ve [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'lerini kullanın.

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

[Az keykasa anahtarı oluşturma](/cli/azure/keyvault/key#az_keyvault_key_create), [az SQL Server Key Create](/cli/azure/sql/server/key#az_sql_server_key_create)ve [az SQL Server TDE anahtar kümesi](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set) komutları kullanın.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE koruyucu modunu değiştir

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- TDE, Microsoft tarafından yönetilen ve BYOK moduna geçiş yapmak için [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE koruyucusunu BYOK modundan Microsoft tarafından yönetilen olarak değiştirmek için [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Aşağıdaki örneklerde [az SQL Server TDE anahtar kümesi](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)kullanılır.

- TDE, Microsoft tarafından yönetilen ve BYOK moduna geçiş yapmak için

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- TDE koruyucusunu BYOK modundan Microsoft tarafından yönetilen olarak değiştirmek için

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik riskine karşı, riskli olabilecek bir TDE koruyucuyu kaldırmayı öğrenin: [riskli olabilecek bir anahtarı kaldırın](transparent-data-encryption-byok-remove-tde-protector.md).

- Azure Key Vault tümleştirme ile çalışmaya başlayın ve TDE için Kendi Anahtarını Getir desteğini kullanın: [PowerShell kullanarak Key Vault kendi anahtarınızı kullanarak TDE açın](transparent-data-encryption-byok-configure.md).