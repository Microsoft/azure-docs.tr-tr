---
title: DNS diğer adı (PowerShell & Azure CLı)
description: PowerShell ve Azure CLı cmdlet 'leri, istemci yapılandırmasına dokunmanıza gerek kalmadan yeni istemci bağlantılarını Azure 'daki farklı bir SQL Server 'a yönlendirmenizi sağlar.
keywords: DNS SQL veritabanı
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: 02cfd839ed1b75fd85553f2e5a5150cadc29ff8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790466"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>DNS diğer adı için PowerShell Azure SQL veritabanı
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Bu makalede, Azure SQL veritabanınızı barındıran [SQL Server](logical-servers.md) IÇIN bir DNS diğer adını nasıl yönetebileceğinizi gösteren bir PowerShell betiği sağlanmaktadır.

> [!NOTE]
> Bu makale, Azure PowerShell az Module veya Azure CLı 'yi kullanacak şekilde güncelleştirilmiştir. En azından Aralık 2020'ye kadar hata düzeltmeleri almaya devam edecek olan AzureRM modülünü de kullanmaya devam edebilirsiniz.
>
> Az Module ve Azurerd uyumluluğu hakkında daha fazla bilgi edinmek için bkz. [Azure PowerShell az Module tanıtımı](/powershell/azure/new-azureps-module-az). Yükleme yönergeleri için bkz. [yükleme Azure PowerShell](/powershell/azure/install-az-ps) veya [Azure CLI 'yi yükleme](/cli/azure/install-azure-cli).

## <a name="dns-alias-in-connection-string"></a>Bağlantı dizesinde DNS diğer adı

[Mantıksal BIR SQL Server](logical-servers.md)'a bağlanmak için, SQL Server Management Studio (SSMS) gibi bir istemci, doğru sunucu adı yerine DNS diğer adı sağlayabilir. Aşağıdaki örnek sunucu dizesinde, *Any-UNIQUE-Alias-name* diğer adı dört düğümlü sunucu dizesindeki ilk noktayla ayrılmış düğümü değiştirir:

   `<yourServer>.database.windows.net`

## <a name="prerequisites"></a>Önkoşullar

Bu makalede verilen demo PowerShell betiğini çalıştırmak istiyorsanız aşağıdaki önkoşullar geçerlidir:

- Azure aboneliği ve hesabı, ücretsiz deneme için bkz. [Azure denemeleri](https://azure.microsoft.com/free/)
- İki sunucu

## <a name="example"></a>Örnek

Aşağıdaki kod örneği, birkaç değişkene değişmez değerler atayarak başlar.

Kodu çalıştırmak için, yer tutucu değerlerini sisteminizdeki gerçek değerlerle eşleşecek şekilde düzenleyin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Kullanılan cmdlet 'ler şunlardır:

- [New-Azsqlserverdnsalıas](/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL veritabanı hizmet SISTEMINDE bir DNS diğer adı oluşturur. Diğer ad sunucu 1 ' e başvurur.
- [Get-Azsqlserverdnsalıas](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): sunucu 1 ' e atanan tüm diğer adları alın ve listeleyin.
- [Set-Azsqlserverdnsalıas](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): diğer adın, sunucu 1 ' den sunucu 2 ' ye başvuracak şekilde yapılandırıldığı sunucu adını değiştirir.
- [Remove-Azsqlserverdnsalıas](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): diğer adı kullanarak sunucu 2 ' den diğer adı kaldırın.

Yüklemek veya yükseltmek için bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps).

`Get-Module -ListAvailable Az`Sürümü bulmak için *PowerShell \_ise.exe* içinde kullanın.

```powershell
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
Connect-AzAccount -SubscriptionName $subscriptionName;
$subscriptionId = Get-AzSubscription -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
New-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName `
    -Name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName -ServerName $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
Set-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -TargetServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName `
    -SourceServerResourceGroup $resourceGroupName -SourceServerName $sqlServerName `
    -SourceServerSubscriptionId $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
Get-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
Remove-AzSqlServerDnsAlias –ResourceGroupName $resourceGroupName2 -ServerName $sqlServerName2 `
    -Name $sqlServerDnsAliasName;
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Kullanılan komutlar şunlardır:

- [az SQL Server DNS-Alias Create](/powershell/module/az.Sql/New-azSqlServerDnsAlias): sunucu IÇIN bir DNS diğer adı oluşturur. Diğer ad sunucu 1 ' e başvurur.
- [az SQL Server DNS-Alias Show](/powershell/module/az.Sql/Get-azSqlServerDnsAlias): Server 1 ' e atanan tüm diğer adları alın ve listeleyin.
- [az SQL Server DNS-Alias set](/powershell/module/az.Sql/Set-azSqlServerDnsAlias): sunucu 1 ' den sunucu 2 ' ye başvurmak için diğer adın yapılandırıldığı sunucu adını değiştirir.
- [az SQL Server DNS-alias delete](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): diğer adı kullanarak sunucu 2 ' den diğer adı kaldırın.

Yüklemek veya yükseltmek için bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

```azurecli-interactive
$subscriptionName = '<subscriptionName>';
$sqlServerDnsAliasName = '<aliasName>';
$resourceGroupName = '<resourceGroupName>';  
$sqlServerName = '<sqlServerName>';
$resourceGroupName2 = '<resourceGroupNameTwo>'; # can be same or different than $resourceGroupName
$sqlServerName2 = '<sqlServerNameTwo>'; # must be different from $sqlServerName.

# login to Azure
az login -SubscriptionName $subscriptionName;
$subscriptionId = az account list[0].i -SubscriptionName $subscriptionName;

Write-Host 'Assign an alias to server 1...';
az sql server dns-alias create –-resource-group $resourceGroupName --server $sqlServerName `
    --name $sqlServerDnsAliasName;

Write-Host 'Get the aliases assigned to server 1...';
az sql server dns-alias show –-resource-group $resourceGroupName --server $sqlServerName;

Write-Host 'Move the alias from server 1 to server 2...';
az sql server dns-alias set –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName `
    --original-resource-group $resourceGroupName --original-server $sqlServerName `
    --original-subscription-id $subscriptionId.Id;

Write-Host 'Get the aliases assigned to server 2...';
az sql server dns-alias show –-resource-group $resourceGroupName2 --server $sqlServerName2;

Write-Host 'Remove the alias from server 2...';
az sql server dns-alias delete –-resource-group $resourceGroupName2 --server $sqlServerName2 `
    --name $sqlServerDnsAliasName;
```

* * *

## <a name="next-steps"></a>Sonraki adımlar

SQL veritabanı için DNS diğer adı özelliğinin tam açıklaması için bkz. [Azure SQL veritabanı Için DNS diğer adı](./dns-alias-overview.md).