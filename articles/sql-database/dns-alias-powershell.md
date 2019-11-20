---
title: DNS diğer adı için PowerShell
description: New-Azsqlserverdnsalıas gibi PowerShell cmdlet 'leri, herhangi bir istemci yapılandırmasına dokunmanıza gerek kalmadan yeni istemci bağlantılarını farklı bir Azure SQL veritabanı sunucusuna yönlendirmenizi sağlar.
keywords: DNS SQL veritabanı
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.devlang: PowerShell
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, amagarwa, maboja, jrasnick, vanto
ms.date: 05/14/2019
ms.openlocfilehash: cb1854c27a3722bc9c3c682c4787395c680d6241
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808462"
---
# <a name="powershell-for-dns-alias-to-azure-sql-database"></a>DNS diğer adı için PowerShell Azure SQL veritabanı

Bu makalede, Azure SQL veritabanı için bir DNS diğer adını nasıl yönetebileceğinizi gösteren bir PowerShell betiği sağlanmaktadır. Betik aşağıdaki eylemleri alan aşağıdaki cmdlet 'leri çalıştırır:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Kod örneğinde kullanılan cmdlet 'ler şunlardır:

- [New-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/New-azSqlServerDnsAlias): Azure SQL veritabanı hizmet sisteminde yenı bir DNS diğer adı oluşturur. Diğer ad, Azure SQL veritabanı sunucusu 1 ' e başvurur.
- [Get-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/Get-azSqlServerDnsAlias): SQL DB Server 1 ' e atanan tüm DNS diğer adlarını alın ve listeleyin.
- [Set-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/Set-azSqlServerDnsAlias): diğer adın, Server 1 ' den SQL DB Server 2 ' ye başvuracak şekilde yapılandırıldığı sunucu adını değiştirir.
- [Remove-Azsqlserverdnsalıas](https://docs.microsoft.com/powershell/module/az.Sql/Remove-azSqlServerDnsAlias): DNS DIĞER adını SQL DB sunucu 2 ' den diğer ad adını kullanarak kaldırın.

## <a name="dns-alias-in-connection-string"></a>Bağlantı dizesinde DNS diğer adı

Belirli bir Azure SQL veritabanı sunucusuna bağlanmak için, SQL Server Management Studio (SSMS) gibi bir istemci, doğru sunucu adı yerine DNS diğer adı sağlayabilir. Aşağıdaki örnek sunucu dizesinde, *Any-UNIQUE-Alias-name* diğer adı dört düğümlü sunucu dizesindeki ilk noktayla ayrılmış düğümü değiştirir:

- Örnek sunucu dizesi: `any-unique-alias-name.database.windows.net`.

## <a name="prerequisites"></a>Ön koşullar

Bu makalede verilen demo PowerShell betiğini çalıştırmak istiyorsanız aşağıdaki önkoşullar geçerlidir:

- Bir Azure aboneliği ve hesabı. Ücretsiz deneme için [https://azure.microsoft.com/free/][https://azure.microsoft.com/free/]' ye tıklayın.
- **New-Azsqlserverdnsalıas**cmdlet 'i ile Azure PowerShell modülü.
  - Yüklemek veya yükseltmek için bkz. [Azure PowerShell Modülü yükleme][install-Az-ps-84p].
  - Sürümü bulmak için PowerShell\_Ise. exe ' de `Get-Module -ListAvailable Az;` çalıştırın.
- İki Azure SQL veritabanı sunucusu.

## <a name="code-example"></a>Kod örneği

Aşağıdaki PowerShell kod örneği, birkaç değişkene değişmez değerler atayarak başlar. Kodu çalıştırmak için, önce tüm yer tutucu değerlerini sisteminizdeki gerçek değerlerle eşleşecek şekilde düzenlemeniz gerekir. Ya da yalnızca kodu denetleyebilirsiniz. Ayrıca, kodun konsol çıktısı da sağlanır.

```powershell
################################################################
###    Assign prerequisites.                                 ###
################################################################
cls;

$SubscriptionName             = '<EDIT-your-subscription-name>';
[string]$SubscriptionGuid_Get = '?'; # The script assigns this value, not you.

$SqlServerDnsAliasName = '<EDIT-any-unique-alias-name>';

$1ResourceGroupName = '<EDIT-rg-1>';  # Can be same or different than $2ResourceGroupName.
$1SqlServerName     = '<EDIT-sql-1>'; # Must differ from $2SqlServerName.

$2ResourceGroupName = '<EDIT-rg-2>';
$2SqlServerName     = '<EDIT-sql-2>';

# Login to your Azure subscription, first time per session.
Write-Host "You must log into Azure once per powershell_ise.exe session,";
Write-Host "  thus type 'yes' only the first time.";
Write-Host " ";
$yesno = Read-Host '[yes/no]  Do you need to log into Azure now?';
if ('yes' -eq $yesno)
{
    Connect-AzAccount -SubscriptionName $SubscriptionName;
}

$SubscriptionGuid_Get = Get-AzSubscription `
    -SubscriptionName $SubscriptionName;

################################################################
###    Working with DNS aliasing for Azure SQL DB server.    ###
################################################################

Write-Host '[1] Assign a DNS alias to SQL DB server 1.';
New-AzSqlServerDNSAlias `
    –ResourceGroupName  $1ResourceGroupName `
    -ServerName         $1SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;

Write-Host '[2] Get and list all the DNS aliases that are assigned to SQL DB server 1.';
Get-AzSqlServerDNSAlias `
    –ResourceGroupName $1ResourceGroupName `
    -ServerName        $1SqlServerName;

Write-Host '[3] Move the DNS alias from 1 to SQL DB server 2.';
Set-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -NewServerName      $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName `
    -OldServerResourceGroup  $1ResourceGroupName `
    -OldServerName           $1SqlServerName `
    -OldServerSubscriptionId $SubscriptionGuid_Get;

# Here your client, such as SSMS, can connect to your "$2SqlServerName"
# by using "$SqlServerDnsAliasName" in the server name.
# For example, server:  "any-unique-alias-name.database.windows.net".

# Remove-AzSqlServerDNSAlias  - would fail here for SQL DB server 1.

Write-Host '[4] Remove the DNS alias from SQL DB server 2.';
Remove-AzSqlServerDNSAlias `
    –ResourceGroupName  $2ResourceGroupName `
    -ServerName         $2SqlServerName `
    -ServerDNSAliasName $SqlServerDnsAliasName;
```

### <a name="actual-console-output-from-the-powershell-example"></a>PowerShell örneğinde gerçek konsol çıkışı

Aşağıdaki konsol çıkışı kopyalanmış ve gerçek bir çalıştırmadan yapıştırıldı.

```powershell
You must log into Azure once per powershell_ise.exe session,
  thus type 'yes' only the first time.

[yes/no]  Do you need to log into Azure now?: yes


Environment           : AzureCloud
Account               : gm@acorporation.com
TenantId              : 72f988bf-1111-1111-1111-111111111111
SubscriptionId        : 45651c69-2222-2222-2222-222222222222
SubscriptionName      : mysubscriptionname
CurrentStorageAccount :

[1] Assign a DNS alias to SQL DB server 1.
[2] Get the DNS alias that is assigned to SQL DB server 1.
[3] Move the DNS alias from 1 to SQL DB server 2.
[4] Remove the DNS alias from SQL DB server 2.
ResourceGroupName ServerName         ServerDNSAliasName
----------------- ----------         ------------------
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-1       gm-sqldb-dns-1     unique-alias-name-food
gm-rg-dns-2       gm-sqldb-dns-2     unique-alias-name-food


[C:\windows\system32\]
>>
```

## <a name="next-steps"></a>Sonraki adımlar

SQL veritabanı için DNS diğer adı özelliğinin tam açıklaması için bkz. [Azure SQL veritabanı Için DNS diğer adı][dns-alias-overview-37v].

<!-- Article links. -->

[https://azure.microsoft.com/free/]: https://azure.microsoft.com/free/

[install-Az-ps-84p]: https://docs.microsoft.com/powershell/azure/install-az-ps

[dns-alias-overview-37v]: dns-alias-overview.md
