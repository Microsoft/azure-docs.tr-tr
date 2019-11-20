---
title: 'Hızlı başlangıç: bir ambar oluşturma-Azure PowerShell'
description: Azure PowerShell ile bir SQL veritabanı mantıksal sunucusu, sunucu düzeyi güvenlik duvarı kuralı ve veri ambarını hızlıca oluşturun.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cfc427b11944cb81d8bc3d12d13668d53be698b7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693105"
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell bir Azure SQL veri ambarı oluşturma ve sorgulama

Azure PowerShell kullanarak hızlı bir şekilde Azure SQL veri ambarı oluşturun.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun.

> [!NOTE]
> Bir SQL Veri Ambarı'nın oluşturulması ek hizmet ücretlerinin alınmasına neden olabilir.  Ayrıntılı bilgi için bkz. [SQL Veri Ambarı fiyatlandırması](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) komutunu kullanarak Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

```powershell
Connect-AzAccount
```

Kullanmakta olduğunuz aboneliği görmek için [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)komutunu çalıştırın.

```powershell
Get-AzSubscription
```

Varsayılandan farklı bir abonelik kullanmanız gerekiyorsa, [set-AzContext](/powershell/module/az.accounts/set-azcontext)komutunu çalıştırın.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```


## <a name="create-variables"></a>Değişken oluşturma

Bu hızlı başlangıçtaki betiklerde kullanılacak değişkenleri tanımlayacaksınız.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek `myResourceGroup` konumunda `westeurope` adlı bir kaynak grubu oluşturur.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>Mantıksal sunucu oluşturma

[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) komutunu kullanarak BIR [Azure SQL mantıksal sunucusu](../sql-database/sql-database-logical-servers.md) oluşturun. Mantıksal sunucu, grup olarak yönetilen bir veritabanı grubu içerir. Aşağıdaki örnek, kaynak grubunuzda `ServerAdmin` adlı Yönetici Kullanıcı ve `ChangeYourAdminPassword1`parolası ile rastgele olarak adlandırılmış bir sunucu oluşturur. Bu önceden tanımlı değerleri istediğiniz gibi değiştirin.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>Sunucu güvenlik duvarı kurallarını yapılandırma

[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) komutunu kullanarak [Azure SQL Server düzeyinde bir güvenlik duvarı kuralı](../sql-database/sql-database-firewall-configure.md) oluşturun. Sunucu düzeyinde bir güvenlik duvarı kuralı, SQL veri ambarı hizmeti güvenlik duvarı aracılığıyla SQL Server Management Studio veya SQLCMD yardımcı programı gibi bir SQL veri ambarına bağlanmak için bir dış uygulamaya izin verir. Aşağıdaki örnekte, güvenlik duvarı yalnızca diğer Azure kaynakları için açılır. Dışarıdan bağlantı kurulabilmesi için IP adresini ortamınız için uygun bir adres olarak değiştirin. Tüm IP adreslerini açmak için başlangıç IP adresi olarak 0.0.0.0’ı, bitiş adresi olaraksa 255.255.255.255’i kullanın.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL veritabanı ve SQL veri ambarı 1433 bağlantı noktası üzerinden iletişim kurar. Bir kurumsal ağ içinden bağlanmaya çalışıyorsanız, ağınızın güvenlik duvarı tarafından 1433 numaralı bağlantı noktası üzerinden giden trafiğe izin verilmiyor olabilir. Bu durumda, BT departmanınız 1433 numaralı bağlantı noktasını açmadığı takdirde Azure SQL sunucunuza bağlanamazsınız.
>


## <a name="create-a-data-warehouse"></a>Veri ambarı oluşturma
Bu örnek, daha önce tanımlanmış değişkenleri kullanarak bir veri ambarı oluşturur.  Hizmet hedefini, veri ambarınız için daha düşük maliyetli bir başlangıç noktası olan DW100c olarak belirtir. 

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

* **Requestedserviceobjectivename**: istediğiniz [veri ambarı birimlerinin](what-is-a-data-warehouse-unit-dwu-cdwu.md) miktarı. Bu miktarı artırmak, işlem maliyetini artırır. Desteklenen değerlerin listesi için bkz. [bellek ve eşzamanlılık limitleri] bellek-eşzamanlılık-limits.md).
* **DatabaseName**: oluşturmakta olduğunuz SQL veri ambarı 'nın adı.
* **ServerName**: oluşturma için kullanmakta olduğunuz sunucunun adı.
* **Resourcegroupname**: kullandığınız kaynak grubu. Aboneliğinizdeki kullanılabilir kaynak gruplarını bulmak için Get-AzureResource komutunu kullanın.
* **Edition**: SQL Veri Ambarı oluşturmak için "DataWarehouse" olmalıdır.

İsteğe Bağlı Parametreler şunlardır:

- **CollationName**: Belirtilmezse varsayılan harmanlama SQL_Latin1_General_CP1_CI_AS şeklindedir. Harmanlama bir veritabanında değiştirilemez.
- **Maxsizebytes**: bir veritabanının varsayılan en büyük boyutu 240tb 'tır. En büyük boyut, rowstore verilerini sınırlandırır. Sütunlu veriler için sınırsız depolama vardır.

Parametre seçenekleri hakkında daha fazla bilgi için, bkz. [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase).


## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıç öğreticileri, bu hızlı başlangıcı temel alır. 

> [!TIP]
> Sonraki hızlı başlangıç öğreticilerle çalışmaya devam etmeyi planlıyorsanız, bu hızlı başlangıçta oluşturulan kaynakları temizlemeyin. Devam etmeyi planlamıyorsanız, Azure portal bu hızlı başlangıç tarafından oluşturulan tüm kaynakları silmek için aşağıdaki adımları kullanın.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Sonraki adımlar

Artık bir veri ambarı oluşturdunuz, bir güvenlik duvarı kuralı oluşturdunuz, veri ambarınıza bağlanmış ve birkaç sorgu çalıştırdınız. Azure SQL Veri Ambarı hakkında daha fazla bilgi edinmek için, veri yükleme öğreticisiyle devam edin.
> [!div class="nextstepaction"]
>[Verileri bir SQL veri ambarına yükleme](load-data-from-azure-blob-storage-using-polybase.md)
