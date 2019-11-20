---
title: TDE koruyucuyu döndürme-PowerShell
description: Azure SQL Server için Saydam Veri Şifrelemesi (TDE) koruyucusunu döndürme hakkında bilgi edinin.
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
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824676"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>PowerShell kullanarak Saydam Veri Şifrelemesi (TDE) koruyucusunu döndürme

Bu makalede, Azure Key Vault 'den bir TDE koruyucusu kullanılarak Azure SQL Server için anahtar döndürme açıklanmaktadır. Azure SQL Server 'ın TDE koruyucusunu döndürme, sunucudaki veritabanlarını koruyan yeni bir asimetrik anahtara geçiş anlamına gelir. Anahtar döndürme bir çevrimiçi işlemdir ve yalnızca birkaç saniye sürer, çünkü bu yalnızca veritabanının veri şifreleme anahtarını çözer ve veritabanının tamamını değil, yeniden şifreler.

Bu kılavuzda, sunucuda TDE koruyucusunu döndürmek için iki seçenek ele alınmaktadır.

> [!NOTE]
> Duraklatılmış bir SQL veri ambarı, anahtar döndürmelerinde devam etmelidir.
>

> [!IMPORTANT]
> Bir geçişten sonra anahtarın önceki sürümlerini **silmeyin** .  Anahtarlar devralındığında, bazı veriler, daha eski veritabanı yedeklemeleri gibi önceki anahtarlarla de şifrelenir. 
>

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modülü Azure SQL veritabanı tarafından hala desteklenmektedir, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır.

- Bu nasıl yapılır kılavuzunda, bir Azure SQL veritabanı veya veri ambarı için TDE koruyucusu olarak Azure Key Vault bir anahtar kullandığınızı varsaymış olursunuz. Bkz. [BYOK desteği ile saydam veri şifrelemesi](transparent-data-encryption-byok-azure-sql.md).
- Azure PowerShell yüklü ve çalışıyor olmanız gerekir.
- [Önerilir, ancak isteğe bağlı] Önce bir donanım güvenlik modülünde (HSM) veya yerel anahtar deposunda TDE koruyucusu için anahtar malzemesi oluşturun ve Azure Key Vault için anahtar malzemesini içeri aktarın. Daha fazla bilgi edinmek için [donanım güvenlik modülü (HSM) kullanma ve Key Vault yönergelerini](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) izleyin.

## <a name="manual-key-rotation"></a>El ile anahtar döndürme

El ile anahtar döndürme, [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)ve [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'lerini kullanarak yeni bir anahtar adı veya hatta başka bir anahtar altında olabilecek tamamen yeni bir anahtar ekler ka. Bu yaklaşımın kullanılması, yüksek kullanılabilirlik ve coğrafi Dr senaryolarını desteklemek üzere farklı anahtar kasalarına aynı anahtarı eklemeyi destekler.

>[!NOTE]
>Anahtar Kasası adı ve anahtar adının birleşik uzunluğu 94 karakteri aşamaz.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Diğer yararlı PowerShell cmdlet 'leri

- TDE, Microsoft tarafından yönetilen ve BYOK moduna geçiş yapmak için [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- TDE koruyucusunu BYOK modundan Microsoft tarafından yönetilen olarak değiştirmek için [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet 'ini kullanın.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Sonraki adımlar

- Güvenlik riskine karşı, riskli olabilecek bir TDE koruyucuyu kaldırma hakkında bilgi edinin: [riskli olabilecek bir anahtarı kaldırma](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Azure Key Vault tümleştirme ile çalışmaya başlayın ve TDE için Kendi Anahtarını Getir desteğini kullanın: [PowerShell kullanarak Key Vault kendi anahtarınızı kullanarak TDE açma](transparent-data-encryption-byok-azure-sql-configure.md)
