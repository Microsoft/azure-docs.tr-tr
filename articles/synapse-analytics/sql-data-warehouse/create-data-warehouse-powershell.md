---
title: 'Hızlı başlangıç: Azure PowerShell ile adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturma'
description: Azure PowerShell kullanarak sunucu düzeyinde güvenlik duvarı kuralına sahip adanmış bir SQL havuzunu (eski adıyla SQL DW) hızlıca oluşturun.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537448"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell ile adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturma

Azure PowerShell kullanarak Azure SYNAPSE Analytics 'te adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!IMPORTANT]
> Adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturmak, yeni bir faturalanabilir hizmetle sonuçlanabilir.  Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) komutunu kullanarak Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

Kullanmakta olduğunuz aboneliği görmek için [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)komutunu çalıştırın.

```powershell
Get-AzSubscription
```

Varsayılandan farklı bir abonelik kullanmanız gerekiyorsa, [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)komutunu çalıştırın.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Değişken oluşturma

Bu hızlı başlangıçtaki betiklerde kullanılacak değişkenleri tanımlayacaksınız.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) komutunu kullanarak bir [Azure Kaynak grubu](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir kapsayıcıdır. Aşağıdaki örnek `westeurope` konumunda `myResourceGroup` adlı bir kaynak grubu oluşturur.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Sunucu oluşturma

[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) komutunu kullanarak [mantıksal bir SQL Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oluşturun. Sunucu, grup olarak yönetilen bir veritabanı grubu içerir. Aşağıdaki örnek, kaynak grubunuzda, adlı Yönetici Kullanıcı ve parolası ile rastgele olarak adlandırılmış bir sunucu oluşturur `ServerAdmin` `ChangeYourAdminPassword1` . Bu önceden tanımlı değerleri istediğiniz gibi değiştirin.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Sunucu düzeyinde güvenlik duvarı kuralı oluşturma

[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) komutunu kullanarak [sunucu düzeyinde bir güvenlik duvarı kuralı](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) oluşturun. Sunucu düzeyinde bir güvenlik duvarı kuralı, SQL Server Management Studio veya SQLCMD yardımcı programının adanmış SQL havuzu hizmet güvenlik duvarı aracılığıyla adanmış bir SQL havuzuna (eski adıyla SQL DW) bağlanmasını sağlar.

Aşağıdaki örnekte, güvenlik duvarı yalnızca diğer Azure kaynakları için açılır. Dışarıdan bağlantı kurulabilmesi için IP adresini ortamınız için uygun bir adres olarak değiştirin. Tüm IP adreslerini açmak için başlangıç IP adresi olarak 0.0.0.0’ı, bitiş adresi olaraksa 255.255.255.255’i kullanın.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL uç noktaları 1433 bağlantı noktası üzerinden iletişim kurar. Bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı müddetçe sunucunuza bağlanamazsınız.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Adanmış bir SQL havuzu oluştur (eski adıyla SQL DW)

Aşağıdaki örnek, önceden tanımlanmış değişkenleri kullanarak adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturur.  Adanmış SQL havuzunuz (eski adıyla SQL DW) için daha düşük maliyetli bir başlangıç noktası olan DW100c olarak hizmet hedefini belirtir.

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Gerekli Parametreler şunlardır:

* **Requestedserviceobjectivename**: istediğiniz [veri ambarı birimlerinin](what-is-a-data-warehouse-unit-dwu-cdwu.md) miktarı. Bu miktarı artırmak, işlem maliyetini artırır. Desteklenen değerlerin bir listesi için bkz. [bellek ve eşzamanlılık sınırları](memory-concurrency-limits.md).
* **DatabaseName**: oluşturmakta olduğunuz adanmış SQL havuzunun adı (eskı ADıYLA SQL DW).
* **ServerName**: oluşturma için kullanmakta olduğunuz sunucunun adı.
* **Resourcegroupname**: kullandığınız kaynak grubu. Aboneliğinizdeki kullanılabilir kaynak gruplarını bulmak için Get-AzureResource komutunu kullanın.
* **Sürüm**: ADANMıŞ bir SQL Havuzu (eskı ADıYLA SQL DW) oluşturmak Için "DataWarehouse" olmalıdır.

İsteğe Bağlı Parametreler şunlardır:

* **CollationName**: Belirtilmezse varsayılan harmanlama SQL_Latin1_General_CP1_CI_AS şeklindedir. Harmanlama bir veritabanında değiştirilemez.
* **Maxsizebytes**: bir veritabanının varsayılan en büyük boyutu 240tb 'tır. En büyük boyut, rowstore verilerini sınırlandırır. Sütunlu veriler için sınırsız depolama vardır.

Parametre seçenekleri hakkında daha fazla bilgi için, bkz. [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıç öğreticileri, bu hızlı başlangıcı temel alır.

> [!TIP]
> Sonraki hızlı başlangıç öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, Azure portal bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Sonraki adımlar

Artık adanmış bir SQL Havuzu (eski adıyla SQL DW) oluşturdunuz, bir güvenlik duvarı kuralı oluşturdunuz ve adanmış SQL havuzunuza bağlı. Daha fazla bilgi edinmek için [verileri özel BIR SQL havuzunda yükleme](./load-data-from-azure-blob-storage-using-copy.md) makalesine geçin.
