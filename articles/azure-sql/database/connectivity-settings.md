---
title: Azure SQL veritabanı ve Azure SYNAPSE Analytics için bağlantı ayarları
description: Bu makalede, Azure SQL veritabanı ve Azure SYNAPSE Analytics için Aktarım Katmanı Güvenliği (TLS) sürüm seçimi ve proxy ve yeniden yönlendirme ayarları açıklanmaktadır.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 0c45a48e6cafa722945400554f2f81916da13775
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627617"
---
# <a name="azure-sql-connectivity-settings"></a>Azure SQL bağlantı ayarları
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Bu makalede, Azure SQL veritabanı ve Azure SYNAPSE Analytics 'te [ADANMıŞ SQL Havuzu (eski ADıYLA SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) için sunucuya bağlantıyı denetleyen ayarlar açıklanır. Bu ayarlar, sunucu ile ilişkili tüm SQL veritabanı ve adanmış SQL Havuzu (eski adıyla SQL DW) veritabanları için geçerlidir.

> [!IMPORTANT]
> Bu makale, Azure SQL yönetilen örneği için geçerlidir.

Bağlantı ayarlarına aşağıdaki ekran görüntüsünde gösterildiği gibi **güvenlik duvarları ve sanal ağlar** ekranından erişilebilir:

 ![Bağlantı ayarları penceresinin ekran görüntüsü.][1]

> [!NOTE]
> Bu ayarlar, uygulandıktan sonra hemen etkili olur. Müşterileriniz, her bir ayar için gereksinimleri karşılamıyorsa bağlantı kaybına neden olabilir.

## <a name="deny-public-network-access"></a>Genel ağ erişimini engelleme

**Ortak ağ erişimini reddet** **Evet** olarak ayarlandığında yalnızca özel uç noktalar aracılığıyla bağlantılara izin verilir. Bu ayar **Hayır** (varsayılan) olmadığında, müşteriler [ağ erişimine genel bakış](network-access-controls-overview.md)bölümünde açıklandığı gibi genel uç noktaları (IP tabanlı güvenlik duvarı kuralları veya sanal ağ tabanlı güvenlik duvarı kuralları ile) veya özel uç noktalar (Azure özel bağlantısı kullanarak) kullanarak bağlanabilir.

 ![Ortak ağ erişimini reddet seçeneği Hayır olarak ayarlandığında bağlantıyı gösteren diyagram Evet olarak ayarlanır.][2]

Mantıksal sunucuda var olan özel uç noktaları olmadan **ortak ağ erişimini reddetme** **girişimi,** şuna benzer bir hata iletisiyle başarısız olur:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Özel uç noktalarıyla yapılandırılmış bir mantıksal sunucuda sanal ağ güvenlik duvarı kuralları **tanımlamak için,** **ortak ağ erişimini reddet** olarak ayarlayın.

**Ortak ağ erişimini reddet** **Evet** olarak ayarlandığında yalnızca özel uç noktalar aracılığıyla bağlantılara izin verilir. Ortak uç noktalar aracılığıyla tüm bağlantılar, şuna benzer bir hata iletisiyle reddedilir:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

**Ortak ağ erişimini reddet** **Evet** olarak ayarlandığında, güvenlik duvarı kuralları ekleme veya güncelleştirme girişimleri şuna benzer bir hata iletisiyle reddedilir:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>PowerShell aracılığıyla genel ağ erişimini değiştirme

> [!IMPORTANT]
> Azure SQL veritabanı, PowerShell Azure Resource Manager modülünü hala destekliyor, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Aşağıdaki betik [Azure PowerShell modülünü](/powershell/azure/install-az-ps)gerektiriyor.

Aşağıdaki PowerShell betiği, `Get` sunucu düzeyinde nasıl ve `Set` **genel ağ erişim** özelliğinin nasıl yapılacağını gösterir:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>CLı aracılığıyla ortak ağ erişimini değiştirme

> [!IMPORTANT]
> Bu bölümdeki tüm betikler [Azure CLI](/cli/azure/install-azure-cli)gerektirir.

### <a name="azure-cli-in-a-bash-shell"></a>Bash kabuğu 'nda Azure CLı

Aşağıdaki CLı betiği, bir bash kabuğu 'ndaki **genel ağ erişim** ayarının nasıl değiştirileceğini göstermektedir:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>En düşük TLS sürümü 

Minimum [Aktarım Katmanı Güvenliği (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) sürümü ayarı, müşterilerin SQL veritabanı 'NıN hangi TLS sürümünü kullandığını seçmesine olanak sağlar.

Şu anda TLS 1.0, 1.1 ve 1.2’yi destekliyoruz. En düşük TLS sürümünü ayarlamak, daha yeni TLS sürümlerinin desteklendiğinden emin olmanızı sağlar. Örneğin, TLS sürüm 1,1 seçilmesi yalnızca TLS 1,1 ve 1,2 ile bağlantı kabul edilir ve TLS 1,0 ile kurulan bağlantılar reddedilir. Uygulamalarınızın desteklediğini test edip onayladıktan sonra, en düşük TLS sürümü olarak 1.2’yi ayarlamanızı öneririz. Bu sürüm önceki sürümlerdeki güvenlik açıklarının düzeltmelerini içerir ve Azure SQL Veritabanı’nda desteklenen en yüksek TLS sürümüdür.

> [!IMPORTANT]
> En az TLS sürümü için varsayılan değer tüm sürümlere izin verdir. Bir TLS sürümünü zorladıktan sonra, varsayılana dönmek mümkün değildir.

TLS’nin daha eski sürümlerine bağımlı olan uygulamalara sahip müşteriler için, en düşük TLS sürümünü uygulamalarınızın gereksinimlerine göre ayarlamanızı öneririz. Şifrelenmemiş bir bağlantı kullanarak bağlanacak uygulamalara bağımlı olan müşteriler için, en düşük TLS sürümünü ayarlamamanızı öneririz.

Daha fazla bilgi için bkz. [SQL veritabanı bağlantısı Için TLS konuları](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

En düşük TLS sürümünü ayarladıktan sonra, sunucunun en düşük TLS sürümünü kullanan müşterilerden gelen oturum açma girişimleri aşağıdaki hatayla başarısız olur:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>PowerShell ile en düşük TLS sürümünü ayarlama

> [!IMPORTANT]
> Azure SQL veritabanı, PowerShell Azure Resource Manager modülünü hala destekliyor, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Aşağıdaki betik [Azure PowerShell modülünü](/powershell/azure/install-az-ps)gerektiriyor.

Aşağıdaki PowerShell betiği, `Get` `Set` mantıksal sunucu düzeyinde nasıl ve **en az TLS sürümü** özelliğinin olduğunu gösterir:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Azure CLı ile en düşük TLS sürümünü ayarlama

> [!IMPORTANT]
> Bu bölümdeki tüm betikler [Azure CLI](/cli/azure/install-azure-cli)gerektirir.

### <a name="azure-cli-in-a-bash-shell"></a>Bash kabuğu 'nda Azure CLı

Aşağıdaki CLı betiği, bir bash kabuğunda **En düşük TLS sürümü** ayarının nasıl değiştirileceğini göstermektedir:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Bağlantı ilkesini değiştirme

[Bağlantı ilkesi](connectivity-architecture.md#connection-policy) , MÜŞTERILERIN Azure SQL veritabanına nasıl bağlanacağını belirler.

## <a name="change-the-connection-policy-via-powershell"></a>Bağlantı ilkesini PowerShell aracılığıyla değiştirme

> [!IMPORTANT]
> Azure SQL veritabanı, PowerShell Azure Resource Manager modülünü hala destekliyor, ancak gelecekteki tüm geliştirmeler az. SQL modülüne yöneliktir. Bu cmdlet 'ler için bkz. [Azurerd. SQL](/powershell/module/AzureRM.Sql/). Az Module ve Azurerd modüllerinde komutların bağımsız değişkenleri önemli ölçüde aynıdır. Aşağıdaki betik [Azure PowerShell modülünü](/powershell/azure/install-az-ps)gerektiriyor.

Aşağıdaki PowerShell betiği, PowerShell kullanarak bağlantı ilkesinin nasıl değiştirileceğini göstermektedir:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Azure CLı aracılığıyla bağlantı ilkesini değiştirme

> [!IMPORTANT]
> Bu bölümdeki tüm betikler [Azure CLI](/cli/azure/install-azure-cli)gerektirir.

### <a name="azure-cli-in-a-bash-shell"></a>Bash kabuğu 'nda Azure CLı

Aşağıdaki CLı betiği, bir bash kabuğu 'nda bağlantı ilkesinin nasıl değiştirileceğini göstermektedir:

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Windows komut isteminden Azure CLı

Aşağıdaki CLı betiği, bir Windows komut isteminden (Azure CLı yüklü) bağlantı ilkesinin nasıl değiştirileceğini göstermektedir:

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure SQL veritabanı 'nda bağlantının nasıl çalıştığına ilişkin genel bilgiler için, [bağlantı mimarisi](connectivity-architecture.md)' ne bakın.
- Sunucu için bağlantı ilkesini değiştirme hakkında daha fazla bilgi için bkz. [Conn-Policy](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
