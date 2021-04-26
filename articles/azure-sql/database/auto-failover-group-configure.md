---
title: Yük devretme grubu yapılandırma
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure portal, Azure CLı ve PowerShell kullanarak bir Azure SQL veritabanı (hem tek hem de havuza alınmış) ve SQL yönetilen örneği için otomatik yük devretme grubu yapılandırmayı öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: a2f0cb683669aa092493c8080d5e4646cf9706c3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477946"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL veritabanı için bir yük devretme grubu yapılandırma
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Bu konu, Azure SQL veritabanı ve Azure SQL yönetilen örneği için bir [otomatik yük devretme grubu](auto-failover-group-overview.md) yapılandırmayı öğretir.

## <a name="single-database"></a>Tek veritabanı

Yük devretme grubunu oluşturun ve Azure portal veya PowerShell kullanarak buna tek bir veritabanı ekleyin.

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulları göz önünde bulundurun:

- İkincil sunucu için sunucu oturumu açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir.

### <a name="create-failover-group"></a>Yük devretme grubu oluştur

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yük devretme grubunuzu oluşturun ve Azure portal kullanarak tek veritabanınızı veritabanına ekleyin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin.
1. Yük devretme grubuna eklemek istediğiniz veritabanını seçin.
1. Sunucu ayarlarını açmak için sunucu **adı** altında sunucunun adını seçin.

   ![Tek veritabanı için açık sunucu](./media/auto-failover-group-configure/open-sql-db-server.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve sonra yeni bir yük devretme grubu oluşturmak için **Grup Ekle** ' yi seçin.

   ![Yeni Yük devretme grubu Ekle](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. **Yük devretme grubu** sayfasında, gerekli değerleri girin veya seçin ve ardından **Oluştur**' u seçin.

   - **Grup Içindeki veritabanları**: yük devretme grubunuza eklemek istediğiniz veritabanını seçin. Veritabanını yük devretme grubuna eklemek, coğrafi çoğaltma işlemini otomatik olarak başlatır.

   ![Yük devretme grubuna SQL veritabanı Ekle](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Yük devretme grubunuzu oluşturun ve PowerShell 'i kullanarak veritabanınızı veritabanına ekleyin.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

---

### <a name="test-failover"></a>Yük devretme testi

Azure portal veya PowerShell kullanarak yük devretme grubunuzun yük devretmesini test edin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yük devretme grubunuzun yük devretmesini test edin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna "Azure SQL" yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin.
1. Yük devretme grubuna eklemek istediğiniz veritabanını seçin.

   ![Tek veritabanı için açık sunucu](./media/auto-failover-group-configure/open-sql-db-server.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve ardından yeni oluşturduğunuz yük devretme grubunu seçin.
  
   ![Portaldan yük devretme grubunu seçin](./media/auto-failover-group-configure/select-failover-group.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin.
1. Veritabanınızı içeren yük devretme grubunuzun yükünü devretmek için görev bölmesinden **Yük devretmeyi** seçin.
1. TDS oturumlarının kesileceğini bildiren uyarıda **Evet** ' i seçin.

   ![Veritabanınızı içeren yük devretme grubunuzun yükünü devreder](./media/auto-failover-group-configure/failover-sql-db.png)

1. Hangi sunucunun artık birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa iki sunucu, bulunan rolleri değiştirmiş olmalıdır.
1. Sunucuları özgün rollerine geri dönmek için **Yük devretmeyi** yeniden seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak yük devretme grubunuzun yük devretmesini test edin.  

İkincil çoğaltmanın rolünü kontrol edin:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

İkincil sunucuya Yük devret:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

Yük devretme grubunu birincil sunucuya geri çevir:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> İkincil veritabanını silmeniz gerekiyorsa, silmeden önce yük devretme grubundan kaldırın. İkincil bir veritabanının yük devretme grubundan kaldırılmadan önce silinmesi öngörülemeyen davranışlara neden olabilir.

## <a name="elastic-pool"></a>Elastik havuz

Yük devretme grubunu oluşturun ve Azure portal veya PowerShell kullanarak buna bir elastik havuz ekleyin.  

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulları göz önünde bulundurun:

- İkincil sunucu için sunucu oturumu açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir.

### <a name="create-the-failover-group"></a>Yük devretme grubu oluşturma

Azure portal veya PowerShell kullanarak elastik havuzunuz için yük devretme grubu oluşturun.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yük devretme grubunuzu oluşturun ve Azure portal kullanarak elastik havuzunuzu ekleyin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna "Azure SQL" yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin.
1. Yük devretme grubuna eklemek istediğiniz elastik havuzu seçin.
1. **Genel bakış** bölmesinde sunucu **adı** bölümünde sunucu adı ' nı seçerek sunucu ayarlarını açın.
  
   ![Elastik havuz için sunucu açma](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve sonra yeni bir yük devretme grubu oluşturmak için **Grup Ekle** ' yi seçin.

   ![Yeni Yük devretme grubu Ekle](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. **Yük devretme grubu** sayfasında, gerekli değerleri girin veya seçin ve ardından **Oluştur**' u seçin. Yeni bir ikincil sunucu oluşturun ya da var olan bir ikincil sunucuyu seçin.

1. **Grup Içindeki veritabanları** ' nı seçin ve ardından yük devretme grubuna eklemek istediğiniz elastik havuzu seçin. İkincil sunucuda zaten elastik bir havuz yoksa, ikincil sunucuda esnek havuz oluşturmanızı isteyen bir uyarı görüntülenir. Uyarıyı seçin ve ardından ikincil sunucuda elastik havuzu oluşturmak için **Tamam** ' ı seçin.

   ![Esnek havuzu yük devretme grubuna ekle](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Elastik havuz ayarlarınızı yük devretme grubuna uygulamak için **Seç** ' i seçin ve ardından yük devretme grubunuzu oluşturmak için **Oluştur** ' u seçin. Elastik havuzun yük devretme grubuna eklenmesi, coğrafi çoğaltma işlemini otomatik olarak başlatır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Yük devretme grubunuzu oluşturun ve PowerShell kullanarak elastik havuzunuzu ekleyin.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
       â€“ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       â€“FailoverGroupName $failoverGroupName `
       â€“FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
               -ResourceGroupName $resourceGroupName `
               -ServerName $serverName `
               -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Yük devretme testi

Azure portal veya PowerShell kullanarak elastik havuzunuzun yük devretmesini test edin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Yük devretme grubunuzu ikincil sunucuya devreder ve Azure portal kullanarak yeniden devreder.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna "Azure SQL" yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin.
1. Yük devretme grubuna eklemek istediğiniz elastik havuzu seçin.
1. **Genel bakış** bölmesinde sunucu **adı** bölümünde sunucu adı ' nı seçerek sunucu ayarlarını açın.

   ![Elastik havuz için sunucu açma](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve ardından Bölüm 2 ' de oluşturduğunuz yük devretme grubunu seçin.
  
   ![Portaldan yük devretme grubunu seçin](./media/auto-failover-group-configure/select-failover-group.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin.
1. Elastik havuzunuzu içeren yük devretme grubunuzun yükünü devretmek için görev bölmesinden **Yük devretmeyi** seçin.
1. TDS oturumlarının kesileceğini bildiren uyarıda **Evet** ' i seçin.

   ![Veritabanınızı içeren yük devretme grubunuzun yükünü devreder](./media/auto-failover-group-configure/failover-sql-db.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa iki sunucu, bulunan rolleri değiştirmiş olmalıdır.
1. Yük devretme grubundan özgün ayarlara geri dönmek için **Yük devretmeyi** yeniden seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak yük devretme grubunuzun yük devretmesini test edin.

İkincil çoğaltmanın rolünü kontrol edin:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

İkincil sunucuya Yük devret:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> İkincil veritabanını silmeniz gerekiyorsa, silmeden önce yük devretme grubundan kaldırın. İkincil bir veritabanının yük devretme grubundan kaldırılmadan önce silinmesi öngörülemeyen davranışlara neden olabilir.

## <a name="sql-managed-instance"></a>SQL Yönetilen Örnek

Azure portal veya PowerShell 'i kullanarak Azure SQL yönetilen örneği 'nde iki yönetilen örnek arasında bir yük devretme grubu oluşturun.

[ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) 'u yapılandırmanız veya her bir SQL yönetilen örneğinin sanal ağı için bir ağ geçidi oluşturmanız, iki ağ geçidini bağlamanız ve ardından yük devretme grubu oluşturmanız gerekir. 

Performans nedenleriyle, [eşleştirilmiş bölgelere](../../best-practices-availability-paired-regions.md) her iki yönetilen örneği de dağıtın. Coğrafi olarak eşleştirilmiş bölgelerde bulunan yönetilen örneklerin eşleştirildiği bölgelere kıyasla çok daha iyi bir performansı vardır. 

### <a name="prerequisites"></a>Önkoşullar

Aşağıdaki önkoşulları göz önünde bulundurun:

- İkincil yönetilen örnek boş olmalıdır.
- İkincil sanal ağın alt ağ aralığı, birincil sanal ağın alt ağ aralığıyla çakışmamalıdır.
- İkincil yönetilen örneğin harmanlama ve saat dilimi, birincil yönetilen örnekten eşleşmelidir.
- İki ağ geçidini bağlarken, **paylaşılan anahtar** her iki bağlantı için de aynı olmalıdır.

### <a name="create-primary-virtual-network-gateway"></a>Birincil sanal ağ geçidi oluştur

[ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)'u yapılandırmadıysanız Azure Portal veya PowerShell ile birincil sanal ağ geçidini oluşturabilirsiniz.

> [!NOTE]
> Ağ geçidinin SKU 'SU verimlilik performansını etkiler. Bu makalede en temel SKU () ile bir ağ geçidi dağıtılır `HwGw1` . Daha yüksek performans elde etmek için daha yüksek bir SKU (örnek: `VpnGw3` ) dağıtın. Tüm kullanılabilir seçenekler için bkz. [ağ geçidi SKU 'ları](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak birincil sanal ağ geçidini oluşturun.

1. [Azure Portal](https://portal.azure.com), kaynak grubunuza gidin ve birincil yönetilen örneğiniz için **sanal ağ** kaynağını seçin.
1. **Ayarlar** altında **alt ağlar** ' ı seçin ve ardından yeni bir **ağ geçidi alt ağı** eklemeyi seçin. Varsayılan değerleri bırakın.

   ![Birincil yönetilen örnek için ağ geçidi ekle](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. Alt ağ geçidi oluşturulduktan sonra sol gezinti bölmesinden **kaynak oluştur** ' u seçin ve `Virtual network gateway` Arama kutusuna yazın. **Microsoft** tarafından yayınlanan **sanal ağ geçidi** kaynağını seçin.

   ![Yeni bir sanal ağ geçidi oluştur](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Birincil yönetilen örneğinizin ağ geçidini yapılandırmak için gerekli alanları doldurun.

   Aşağıdaki tabloda, birincil yönetilen örnek için ağ geçidi için gereken değerler gösterilmektedir:

    | **Alan** | Değer |
    | --- | --- |
    | **Abonelik** |  Birincil yönetilen örneğinizin bulunduğu abonelik. |
    | **Ad** | Sanal ağ geçidinizin adı. |
    | **Bölge** | Birincil yönetilen örneğinizin bulunduğu bölge. |
    | **Ağ geçidi türü** | **VPN**' yi seçin. |
    | **VPN türü** | **Rota tabanlı** seçin |
    | **SKU**| Varsayılan bırakın `VpnGw1` . |
    | **Konum**| İkincil yönetilen örneğinizin ve ikincil sanal ağınızın bulunduğu konum.   |
    | **Sanal ağ**| İkincil yönetilen örneğiniz için sanal ağı seçin. |
    | **Genel IP adresi**| **Yeni oluştur**’u seçin. |
    | **Genel IP adresi adı**| IP adresiniz için bir ad girin. |
    | &nbsp; | &nbsp; |

1. Diğer değerleri varsayılan olarak bırakın ve sonra sanal ağ geçidinizin ayarlarını gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

   ![Birincil ağ geçidi ayarları](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Yeni sanal ağ geçidinizi oluşturmak için **Oluştur** ' u seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak birincil sanal ağ geçidini oluşturun.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>İkincil sanal ağ geçidi oluştur

Azure portal veya PowerShell kullanarak ikincil sanal ağ geçidini oluşturun.

# <a name="portal"></a>[Portal](#tab/azure-portal)

İkinci yönetilen örnek için sanal ağ alt ağını ve ağ geçidini oluşturmak üzere önceki bölümdeki adımları yineleyin. İkincil yönetilen örneğiniz için ağ geçidini yapılandırmak üzere gerekli alanları doldurun.

Aşağıdaki tabloda, ikincil yönetilen örnek için ağ geçidi için gereken değerler gösterilmektedir:

   | **Alan** | Değer |
   | --- | --- |
   | **Abonelik** |  İkincil yönetilen örneğinizin olduğu abonelik. |
   | **Ad** | Sanal ağ geçidinizin adı, örneğin `secondary-mi-gateway` . |
   | **Bölge** | İkincil yönetilen örneğinizin bulunduğu bölge. |
   | **Ağ geçidi türü** | **VPN**' yi seçin. |
   | **VPN türü** | **Rota tabanlı** seçin |
   | **SKU**| Varsayılan bırakın `VpnGw1` . |
   | **Konum**| İkincil yönetilen örneğinizin ve ikincil sanal ağınızın bulunduğu konum.   |
   | **Sanal ağ**| 2. bölümde oluşturulan sanal ağı seçin `vnet-sql-mi-secondary` . |
   | **Genel IP adresi**| **Yeni oluştur**’u seçin. |
   | **Genel IP adresi adı**| IP adresiniz için gibi bir ad girin `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![İkincil ağ geçidi ayarları](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak ikincil sanal ağ geçidini oluşturun.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Ağ geçitlerini bağlama

Azure portal veya PowerShell kullanarak iki ağ geçidi arasında bağlantı oluşturun.

İki bağlantı oluşturulması gerekir-birincil ağ geçidinden ikincil ağ geçidine bağlantı ve sonra ikincil ağ geçidinden birincil ağ geçidine bağlantı.

Her iki bağlantı için de kullanılan paylaşılan anahtar her bağlantı için aynı olmalıdır.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak iki ağ geçidi arasında bağlantı oluşturun.

1. Azure portal **kaynak oluştur** ' u seçin [](https://portal.azure.com).
1. Arama `connection` kutusuna yazın ve ardından arama yapmak için ENTER tuşuna basın. Bu, sizi Microsoft tarafından yayımlanan **bağlantı** kaynağına götürür.
1. Bağlantınızı oluşturmak için **Oluştur** ' u seçin.
1. **Temel bilgiler** sekmesinde, aşağıdaki değerleri seçip **Tamam**' ı seçin.
    1. `VNet-to-VNet` **Bağlantı türü** için seçin.
    1. Açılan listeden aboneliğinizi seçin.
    1. Açılan kutuda yönetilen örneğiniz için kaynak grubunu seçin.
    1. Açılan listeden birincil yönetilen örneğinizin konumunu seçin.
1. **Ayarlar** sekmesinde, aşağıdaki değerleri seçin veya girin ve sonra **Tamam**' ı seçin:
    1. **İlk sanal ağ geçidi** için, gibi birincil ağ geçidini seçin `Primary-Gateway` .  
    1. **İkinci sanal ağ geçidi** için ikincil ağ geçidini (gibi) seçin `Secondary-Gateway` .
    1. **Çift yönlü bağlantı oluştur**' un yanındaki onay kutusunu işaretleyin.
    1. Varsayılan birincil bağlantı adını bırakın ya da seçtiğiniz bir değerle yeniden adlandırın.
    1. Bağlantı için, gibi bir **paylaşılan anahtar (PSK)** sağlayın `mi1m2psk` .

   ![Ağ Geçidi bağlantısı oluştur](./media/auto-failover-group-configure/create-gateway-connection.png)

1. **Özet** sekmesinde, çift yönlü bağlantınızın ayarlarını gözden geçirin ve ardından bağlantıyı oluşturmak için **Tamam** ' ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak iki ağ geçidi arasında bağlantı oluşturun.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Yük devretme grubu oluşturma

Azure portal veya PowerShell 'i kullanarak yönetilen örneklerinizin yük devretme grubunu oluşturun.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak, SQL yönetilen örneklerinizin yük devretme grubunu oluşturun.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin.
1. Yük devretme grubuna eklemek istediğiniz birincil yönetilen örneği seçin.  
1. **Ayarlar** altında, **örnek yük devretme grupları** ' na gidin ve sonra **örnek yük devretme grubu** sayfasını açmak için **Grup Ekle** ' yi seçin.

   ![Yük devretme grubu ekleme](./media/auto-failover-group-configure/add-failover-group.png)

1. **Örnek yük devretme grubu** sayfasında, yük devretme grubunuzun adını yazın ve ardından açılan listeden ikincil yönetilen örneği seçin. Yük devretme grubunuzu oluşturmak için **Oluştur** ' u seçin.

   ![Yük devretme grubu oluştur](./media/auto-failover-group-configure/create-failover-group.png)

1. Yük devretme grubu dağıtımı tamamlandıktan sonra, **Yük devretme grubu** sayfasına geri yönlendirilirsiniz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak yönetilen örneklerinizin yük devretme grubunu oluşturun.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Yük devretme testi

Azure portal veya PowerShell kullanarak yük devretme grubunuzun yük devretmesini test edin.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yük devretme grubunuzun yük devretmesini test edin.

1. [Azure Portal](https://portal.azure.com) içinde _İkincil_ yönetilen örneğinize gidin ve ayarlar altında **örnek yük devretme grupları** ' nı seçin.
1. Hangi yönetilen örnek birincil olduğunu ve hangi yönetilen örnek ikincil olduğunu gözden geçirin.
1. **Yük devretme** ' yı seçin ve sonra kesilmekte olan tds oturumlarının uyarısında **Evet** ' i seçin.

   ![Yük devretme grubu yükünü devreder](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Hangi manşlı örneğin birincil olduğunu ve hangi örneğin ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa, iki örnek anahtarlamalı rollere sahip olmalıdır.

   ![Yönetilen örnekler, yük devretmeden sonra rolleri değiştirdi](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Yeni _İkincil_ yönetilen örneğe gidin ve birincil örnek birincil role geri dönmek Için **Yük devretmeyi** bir kez daha seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak yük devretme grubunuzun yük devretmesini test edin.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Özel Bağlantı kullanma

Özel bir bağlantı kullanmak, mantıksal bir sunucuyu sanal ağ ve alt ağ içindeki belirli bir özel IP adresiyle ilişkilendirmenizi sağlar. 

Yük devretme grubunuza özel bir bağlantı kullanmak için aşağıdakileri yapın:

1. Birincil ve ikincil sunucularınızın [eşleştirilmiş bir bölgede](../../best-practices-availability-paired-regions.md)bulunduğundan emin olun. 
1. Birincil ve ikincil sunucular için özel uç noktaları, çakışmayan IP adresi alanları olacak şekilde barındırmak için her bölgede sanal ağ ve alt ağ oluşturun. Örneğin, 10.0.0.0/16 birincil sanal ağ adresi aralığı ve 10.0.0.1/16 için ikincil sanal ağ adresi aralığı çakışıyor. Sanal ağ adres aralıkları hakkında daha fazla bilgi için bkz. [Azure sanal ağlarını tasarlama](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)blogu.
1. [Birincil sunucu için özel bir uç nokta ve Azure özel DNS bölgesi](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint)oluşturun. 
1. İkincil sunucu için de özel bir uç nokta oluşturun, ancak bu kez birincil sunucu için oluşturulan Özel DNS bölgeyi yeniden kullanmayı tercih edin. 
1. Özel bağlantı kurulduktan sonra, bu makalede daha önce özetlenen adımları izleyerek yük devretme grubunu oluşturabilirsiniz. 


## <a name="locate-listener-endpoint"></a>Dinleyici uç noktasını bul

Yük devretme grubunuz yapılandırıldıktan sonra, uygulamanız için bağlantı dizesini dinleyici uç noktasına güncelleştirin. Bu işlem, uygulamanızı birincil veritabanı, elastik havuz veya örnek veritabanı yerine yük devretme grubu dinleyicisine bağlı tutar. Bu şekilde, veritabanı varlığınızın her seferinde bağlantı dizesini el ile güncelleştirmeniz gerekmez ve trafik şu anda birincil olan varlığa yönlendirilir.

Dinleyici uç noktası `fog-name.database.windows.net` ,,, yük devretme grubu görüntülenirken Azure Portal görünür.

![Yük devretme grubu bağlantı dizesi](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Açıklamalar

- Tek veya havuza alınmış bir veritabanının yük devretme grubunu kaldırmak çoğaltmayı durdurmaz ve çoğaltılan veritabanını silmez. Bir yük devretme grubuna kaldırıldıktan sonra tek veya havuza alınmış bir veritabanı eklemek istiyorsanız Coğrafi çoğaltmayı el ile durdurmanız ve veritabanını ikincil sunucudan silmeniz gerekir. Bunun başarısız olması `The operation cannot be performed due to multiple errors` , veritabanının yük devretme grubuna eklenmeye çalışıldığında aşağıdakine benzer bir hata oluşmasına neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar

Yük devretme grubunu yapılandırma hakkında ayrıntılı adımlar için aşağıdaki öğreticilere bakın:

- [Yük devretme grubuna tek bir veritabanı ekleme](failover-group-add-single-database-tutorial.md)
- [Yük devretme grubuna bir elastik havuz ekleme](failover-group-add-elastic-pool-tutorial.md)
- [Bir yük devretme grubuna yönetilen örnek ekleme](../managed-instance/failover-group-add-instance-tutorial.md)

Azure SQL veritabanı yüksek kullanılabilirlik seçeneklerine genel bakış için bkz. [coğrafi çoğaltma](active-geo-replication-overview.md) ve [otomatik yük devretme grupları](auto-failover-group-overview.md).
