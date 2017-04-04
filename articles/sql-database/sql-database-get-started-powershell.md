---
title: "Azure PowerShell: SQL veritabanı oluşturma | Microsoft Docs"
description: "Azure portalında SQL Veritabanı mantıksal sunucusu, sunucu düzeyi güvenlik duvarı kuralı ve veritabanı oluşturmayı öğrenin."
keywords: "sql veritabanı öğreticisi, sql veritabanı oluşturma"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 70cf89a5832aee2d0c303e0d40e104d84837b50c
ms.lasthandoff: 03/25/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>PowerShell kullanarak tek Azure SQL veritabanı oluşturma

PowerShell komut satırından veya betik içindeki Azure kaynaklarını oluşturmak ve yönetmek için kullanılır. Bu kılavuzda bir [Azure SQL Veritabanı mantıksal sunucusundaki](sql-database-features.md) [Azure kaynak grubuna](../azure-resource-manager/resource-group-overview.md) Azure SQL veritabanı dağıtmak için PowerShell kullanma işlemi ayrıntılı olarak açıklanmaktadır.

Başlamadan önce en yeni PowerShell sürümünün yüklü olduğundan emin olun. Ayrıntılı bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azureps-cmdlets-docs). 

## <a name="log-in-to-azure"></a>Azure'da oturum açma

[Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) komutu ile yeni bir [Azure kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek `westeurope` konumunda `myResourceGroup` adlı bir kaynak grubu oluşturur.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "westeurope"
```
## <a name="create-a-logical-server"></a>Mantıksal sunucu oluşturma

[New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) komutuyla bir [Azure SQL Veritabanı mantıksal sunucusu](sql-database-features.md) oluşturun. Mantıksal sunucu, grup olarak yönetilen bir veritabanı grubu içerir. Aşağıdaki örnek, kaynak grubunuzda `ServerAdmin` yönetici oturum açma bilgileri ve `ChangeYourAdminPassword1` parolası ile rastgele olarak adlandırılmış bir sunucu oluşturur. Bu önceden tanımlı değerleri istediğiniz gibi değiştirin.

```powershell
$servername = "server-$(Get-Random)"
New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -Location "westeurope" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList "ServerAdmin", $(ConvertTo-SecureString -String "ChangeYourAdminPassword1" -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Sunucu güvenlik duvarı kurallarını yapılandırma

[New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) komutu ile bir [Azure SQL Veritabanı sunucu düzeyi güvenlik duvarı kuralı](sql-database-firewall-configure.md) oluşturun. Bir sunucu düzeyi güvenlik duvarı kuralı SQL Server Management Studio veya SQLCMD yardımcı programı gibi bir dış uygulamanın SQL Veritabanı hizmet güvenlik duvarı üzerinden bir SQL veritabanına bağlanmasına olanak sağlar. Aşağıdaki örnekte önceden tanımlanmış bir adres aralığı için bir güvenlik duvarı kuralı oluşturulmaktadır. Bu örnek için aralık, olası tüm IP adresleri aralığıdır. Bu önceden tanımlı değerleri kendi dış IP adresiniz veya IP adresi aralığınız için değerlerle değiştirin. 

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress "0.0.0.0" -EndIpAddress "255.255.255.255"
```

## <a name="create-a-blank-database"></a>Boş veritabanı oluşturma

Sunucuda [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) komutu ile [S0 performans](sql-database-service-tiers.md) düzeyinde boş bir SQL veritabanı oluşturun. Aşağıdaki örnek `mySampleDatabase` adlı bir veritabanı oluşturur. Bu önceden tanımlı değeri istediğiniz gibi değiştirin.

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MySampleDatabase" `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıçlar, bu hızlı başlangıca göre belirlenir. Sonraki hızlı başlangıçlar veya öğreticilerle devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, bu hızlı başlangıç ile oluşturulan tüm kaynakları silmek için aşağıdaki komutu kullanın.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

- SQL Server Management Studio kullanarak bağlanmak ve sorgu yürütmek için, bkz. [SSMS ile bağlanma ve sorgu yürütme](sql-database-connect-query-ssms.md)
- Visual Studio kullanarak bağlanmak için bkz. [Visual Studio ile bağlanma ve sorgu yürütme](sql-database-connect-query.md).
* SQL Veritabanı’na teknik bir genel bakış için, bkz. [SQL Veritabanı hizmeti hakkında](sql-database-technical-overview.md).

