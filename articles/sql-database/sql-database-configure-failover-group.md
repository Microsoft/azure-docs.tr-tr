---
title: Yük devretme grubu yapılandırma
description: Azure portal, az CLı ve PowerShell kullanarak Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek için otomatik yük devretme grubu yapılandırmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
ms.openlocfilehash: fb9ee2378679c420a7675856ec95e60f6ae1d14f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827147"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Azure SQL veritabanı için bir yük devretme grubu yapılandırma

Bu konu, bir Azure SQL veritabanı için bir [otomatik yük devretme grubu](sql-database-auto-failover-group.md) , Azure Portal veya PowerShell kullanarak tek veritabanı, elastik havuz ve yönetilen örnek için nasıl yapılandırılacağını öğretir. 

## <a name="single-database"></a>Tek veritabanı
Yük devretme grubunu oluşturun ve Azure portal veya PowerShell kullanarak buna tek bir veritabanı ekleyin.

### <a name="prerequisites"></a>Ön koşullar

Aşağıdaki önkoşulları göz önünde bulundurun:

- İkincil sunucu için sunucu oturumu açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir. 

### <a name="create-failover-group"></a>Yük devretme grubu oluştur

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Yük devretme grubunuzu oluşturun ve Azure portal kullanarak tek veritabanınızı veritabanına ekleyin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Yük devretme grubuna eklemek istediğiniz tek veritabanını seçin. 
1. Sunucu ayarlarını açmak için sunucu **adı** altında sunucunun adını seçin.

   ![Tek veritabanı için açık sunucu](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve sonra yeni bir yük devretme grubu oluşturmak için **Grup Ekle** ' yi seçin. 

    ![Yeni Yük devretme grubu Ekle](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **Yük devretme grubu** sayfasında, gerekli değerleri girin veya seçin ve ardından **Oluştur**' u seçin.

   - **Grup Içindeki veritabanları**: yük devretme grubunuza eklemek istediğiniz veritabanını seçin. Veritabanını yük devretme grubuna eklemek, coğrafi çoğaltma işlemini otomatik olarak başlatır. 
        
    ![SQL DB 'yi yük devretme grubuna ekle](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Yük devretme grubunuzu oluşturun ve PowerShell kullanarak tek veritabanınızı buna ekleyin. 

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
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yük devretme grubunuzun yük devretmesini test edin. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Yük devretme grubuna eklemek istediğiniz tek veritabanını seçin. 

   ![Tek veritabanı için açık sunucu](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve ardından yeni oluşturduğunuz yük devretme grubunu seçin. 
  
   ![Portaldan yük devretme grubunu seçin](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. 
1. Tek veritabanınızı içeren yük devretme grubunuzun yükünü devretmek için görev bölmesinden **Yük devretmeyi** seçin. 
1. TDS oturumlarının kesileceğini bildiren uyarıda **Evet** ' i seçin. 

   ![SQL veritabanınızı içeren yük devretme grubunuzun yükünü devreder](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Hangi sunucunun artık birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa iki sunucu, bulunan rolleri değiştirmiş olmalıdır. 
1. Sunucuları ilk rollerine geri dönmek için **Yük devretmeyi** yeniden seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

## <a name="elastic-pool"></a>Elastik havuz
Yük devretme grubunu oluşturun ve Azure portal veya PowerShell kullanarak buna bir elastik havuz ekleyin.  

### <a name="prerequisites"></a>Ön koşullar

Aşağıdaki önkoşulları göz önünde bulundurun:

- İkincil sunucu için sunucu oturumu açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir. 

### <a name="create-the-failover-group"></a>Yük devretme grubu oluşturma 

Azure portal veya PowerShell kullanarak elastik havuzunuz için yük devretme grubu oluşturun. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Yük devretme grubunuzu oluşturun ve Azure portal kullanarak elastik havuzunuzu ekleyin.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Yük devretme grubuna eklemek istediğiniz elastik havuzu seçin. 
1. **Genel bakış** bölmesinde sunucu **adı** bölümünde sunucu adı ' nı seçerek sunucu ayarlarını açın.
  
    ![Elastik havuz için sunucu açma](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve sonra yeni bir yük devretme grubu oluşturmak için **Grup Ekle** ' yi seçin. 

    ![Yeni Yük devretme grubu Ekle](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **Yük devretme grubu** sayfasında, gerekli değerleri girin veya seçin ve ardından **Oluştur**' u seçin. Yeni bir ikincil sunucu oluşturun ya da var olan bir ikincil sunucuyu seçin. 

1. **Grup Içindeki veritabanları** ' nı seçin ve ardından yük devretme grubuna eklemek istediğiniz elastik havuzu seçin. İkincil sunucuda zaten elastik bir havuz yoksa, ikincil sunucuda esnek havuz oluşturmanızı isteyen bir uyarı görüntülenir. Uyarıyı seçin ve ardından ikincil sunucuda elastik havuzu oluşturmak için **Tamam** ' ı seçin. 
        
    ![Esnek havuzu yük devretme grubuna ekle](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Elastik havuz ayarlarınızı yük devretme grubuna uygulamak için **Seç** ' i seçin ve ardından yük devretme grubunuzu oluşturmak için **Oluştur** ' u seçin. Elastik havuzun yük devretme grubuna eklenmesi, coğrafi çoğaltma işlemini otomatik olarak başlatır. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
       –ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -PartnerServerName $drServerName  `
       –FailoverGroupName $failoverGroupName `
       –FailoverPolicy Automatic `
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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Yük devretme grubunuzu ikincil sunucuya devreder ve Azure portal kullanarak yeniden devreder. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Yük devretme grubuna eklemek istediğiniz elastik havuzu seçin. 
1. **Genel bakış** bölmesinde sunucu **adı** bölümünde sunucu adı ' nı seçerek sunucu ayarlarını açın.
  
    ![Elastik havuz için sunucu açma](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)
1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve ardından Bölüm 2 ' de oluşturduğunuz yük devretme grubunu seçin. 
  
   ![Portaldan yük devretme grubunu seçin](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. 
1. Elastik havuzunuzu içeren yük devretme grubunuzun yükünü devretmek için görev bölmesinden **Yük devretmeyi** seçin. 
1. TDS oturumlarının kesileceğini bildiren uyarıda **Evet** ' i seçin. 

   ![SQL veritabanınızı içeren yük devretme grubunuzun yükünü devreder](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa iki sunucu, bulunan rolleri değiştirmiş olmalıdır. 
1. Yük devretme grubundan özgün ayarlara geri dönmek için **Yük devretmeyi** yeniden seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

## <a name="managed-instance"></a>Yönetilen örnek

Azure portal veya PowerShell kullanarak iki yönetilen örnek arasında bir yük devretme grubu oluşturun. 

Her bir yönetilen örneğin sanal ağı için bir ağ geçidi oluşturmanız, iki ağ geçidini bağlamanız ve ardından yük devretme grubu oluşturmanız gerekir.

### <a name="prerequisites"></a>Ön koşullar
Aşağıdaki önkoşulları göz önünde bulundurun:

- İkincil yönetilen örnek boş olmalıdır.
- İkincil sanal ağın alt ağ aralığı, birincil sanal ağın alt ağ aralığıyla çakışmamalıdır. 
- İkincil örneğin harmanlama ve saat dilimi, birincil örnekteki ile aynı olmalıdır. 
- İki ağ geçidini bağlarken, **paylaşılan anahtar** her iki bağlantı için de aynı olmalıdır. 

### <a name="create-primary-virtual-network-gateway"></a>Birincil sanal ağ geçidi oluştur 

Azure portal veya PowerShell ile birincil sanal ağ geçidini oluşturun. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak birincil sanal ağ geçidini oluşturun. 

1. [Azure Portal](https://portal.azure.com), kaynak grubunuza gidin ve birincil yönetilen örneğiniz için **sanal ağ** kaynağını seçin. 
1. **Ayarlar** altında **alt ağlar** ' ı seçin ve ardından yeni bir **ağ geçidi alt ağı**eklemeyi seçin. Varsayılan değerleri bırakın. 

   ![Birincil yönetilen örnek için ağ geçidi ekle](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Alt ağ geçidi oluşturulduktan sonra sol gezinti bölmesinden **kaynak oluştur** ' u seçin ve arama kutusuna `Virtual network gateway` yazın. **Microsoft**tarafından yayınlanan **sanal ağ geçidi** kaynağını seçin. 

   ![Yeni bir sanal ağ geçidi oluştur](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Birincil yönetilen örneğinizin ağ geçidini yapılandırmak için gerekli alanları doldurun. 

   Aşağıdaki tabloda, birincil yönetilen örnek için ağ geçidi için gereken değerler gösterilmektedir:
 
    | **Alan** | Değer |
    | --- | --- |
    | **Abonelik** |  Birincil yönetilen örneğinizin bulunduğu abonelik. |
    | **Ad** | Sanal ağ geçidinizin adı. | 
    | **Bölge** | İkincil yönetilen örneğinizin bulunduğu bölge. |
    | **Ağ Geçidi türü** | **VPN**' yi seçin. |
    | **VPN türü** | **Rota tabanlı** seçin |
    | **SKU**| `VpnGw1`varsayılan olarak bırakın. |
    | **Konum**| İkincil yönetilen örneğinizin ve ikincil sanal ağınızın bulunduğu konum.   |
    | **Sanal ağ**| İkincil yönetilen örneğiniz için sanal ağı seçin. |
    | **Genel IP adresi**| **Yeni oluştur**’u seçin. |
    | **Genel IP adresi adı**| IP adresiniz için bir ad girin. |
    | &nbsp; | &nbsp; |

1. Diğer değerleri varsayılan olarak bırakın ve sonra sanal ağ geçidinizin ayarlarını gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

   ![Birincil ağ geçidi ayarları](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Yeni sanal ağ geçidinizi oluşturmak için **Oluştur** ' u seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
İkinci yönetilen örnek için sanal ağ alt ağını ve ağ geçidini oluşturmak üzere önceki bölümdeki adımları yineleyin. İkincil yönetilen örneğiniz için ağ geçidini yapılandırmak üzere gerekli alanları doldurun. 

   Aşağıdaki tabloda, ikincil yönetilen örnek için ağ geçidi için gereken değerler gösterilmektedir:

   | **Alan** | Değer |
   | --- | --- |
   | **Abonelik** |  İkincil yönetilen örneğinizin olduğu abonelik. |
   | **Ad** | `secondary-mi-gateway`gibi sanal ağ geçidinizin adı. | 
   | **Bölge** | İkincil yönetilen örneğinizin bulunduğu bölge. |
   | **Ağ Geçidi türü** | **VPN**' yi seçin. |
   | **VPN türü** | **Rota tabanlı** seçin |
   | **SKU**| `VpnGw1`varsayılan olarak bırakın. |
   | **Konum**| İkincil yönetilen örneğinizin ve ikincil sanal ağınızın bulunduğu konum.   |
   | **Sanal ağ**| Bölüm 2 ' de oluşturulan sanal ağı seçin, örneğin `vnet-sql-mi-secondary`. |
   | **Genel IP adresi**| **Yeni oluştur**’u seçin. |
   | **Genel IP adresi adı**| IP adresiniz için `secondary-gateway-IP`gibi bir ad girin. |
   | &nbsp; | &nbsp; |

   ![İkincil ağ geçidi ayarları](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal kullanarak iki ağ geçidi arasında bağlantı oluşturun. 

1. Azure portal **kaynak oluştur** ' u seçin [](https://portal.azure.com).
1. Arama kutusuna `connection` yazın ve ardından arama yapmak için ENTER tuşuna basın ve Microsoft tarafından yayımlanan **bağlantı** kaynağına gidersiniz.
1. Bağlantınızı oluşturmak için **Oluştur** ' u seçin. 
1. **Temel bilgiler** sekmesinde, aşağıdaki değerleri seçip **Tamam**' ı seçin. 
    1. **Bağlantı türü**için `VNet-to-VNet` seçin. 
    1. Açılan listeden aboneliğinizi seçin. 
    1. Açılan kutuda yönetilen örneğiniz için kaynak grubunu seçin. 
    1. Açılan listeden birincil yönetilen örneğinizin konumunu seçin 
1. **Ayarlar** sekmesinde, aşağıdaki değerleri seçin veya girin ve sonra **Tamam**' ı seçin:
    1. **İlk sanal ağ geçidi**için `Primary-Gateway`gibi birincil ağ geçidini seçin.  
    1. **İkinci sanal ağ geçidi**için `Secondary-Gateway`gibi ikincil ağ geçidini seçin. 
    1. **Çift yönlü bağlantı oluştur**' un yanındaki onay kutusunu işaretleyin. 
    1. Varsayılan birincil bağlantı adını bırakın ya da seçtiğiniz bir değerle yeniden adlandırın. 
    1. Bağlantı için `mi1m2psk`gibi bir **paylaşılan anahtar (PSK)** sağlayın. 

   ![Ağ Geçidi bağlantısı oluştur](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. **Özet** sekmesinde, çift yönlü bağlantınızın ayarlarını gözden geçirin ve ardından bağlantıyı oluşturmak için **Tamam** ' ı seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yönetilen örneklerinizin yük devretme grubunu oluşturun. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Yük devretme grubuna eklemek istediğiniz birincil yönetilen örneği seçin.  
1. **Ayarlar**altında, **örnek yük devretme grupları** ' na gidin ve sonra **örnek yük devretme grubu** sayfasını açmak için **Grup Ekle** ' yi seçin. 

   ![Yük devretme grubu ekleme](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **Örnek yük devretme grubu** sayfasında, yük devretme grubunuzun adını yazın ve ardından açılan listeden ikincil yönetilen örneği seçin. Yük devretme grubunuzu oluşturmak için **Oluştur** ' u seçin. 

   ![Yük devretme grubu oluştur](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Yük devretme grubu dağıtımı tamamlandıktan sonra, **Yük devretme grubu** sayfasına geri yönlendirilirsiniz. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yük devretme grubunuzun yük devretmesini test edin. 

1. [Azure Portal](https://portal.azure.com) içinde yönetilen örneğinize gidin ve ayarlar altında **örnek yük devretme grupları** ' nı seçin. 
1. Hangi yönetilen örnek birincil olduğunu ve hangi yönetilen örnek ikincil olduğunu gözden geçirin. 
1. **Yük devretme** ' yı seçin ve sonra kesilmekte olan tds oturumlarının uyarısında **Evet** ' i seçin. 

   ![Yük devretme grubu yükünü devreder](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Hangi manşlı örneğin birincil olduğunu ve hangi örneğin ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa, iki örnek anahtarlamalı rollere sahip olmalıdır. 

   ![Yönetilen örnekler, yük devretmeden sonra rolleri değiştirdi](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Birincil örnek birincil role geri dönmek için **Yük devretmeyi** bir kez daha seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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

## <a name="locate-listener-endpoint"></a>Dinleyici uç noktasını bul

Yük devretme grubunuz yapılandırıldıktan sonra, uygulamanız için bağlantı dizesini dinleyici uç noktasına güncelleştirin. Bu işlem, uygulamanızı birincil veritabanı, elastik havuz veya yönetilen örnek yerine yük devretme grubu dinleyicisine bağlı tutar. Bu şekilde, Azure SQL veritabanı varlığınızın her seferinde bağlantı dizesini el ile güncelleştirmeniz gerekmez ve trafik şu anda birincil olan varlığa yönlendirilir. 

Dinleyici uç noktası `fog-name.database.windows.net`biçimindedir ve Azure portal, yük devretme grubu görüntülenirken görünür:

![Yük devretme grubu bağlantı dizesi](media/sql-database-configure-failover-group/find-failover-group-connection-string.png)

## <a name="remarks"></a>Açıklamalar

- Tek veya havuza alınmış bir veritabanının yük devretme grubunu kaldırmak çoğaltmayı durdurmaz ve çoğaltılan veritabanını silmez. Bir yük devretme grubuna kaldırıldıktan sonra tek veya havuza alınmış bir veritabanı eklemek istiyorsanız Coğrafi çoğaltmayı el ile durdurmanız ve veritabanını ikincil sunucudan silmeniz gerekir. Bunun yapılması, veritabanının yük devretme grubuna eklenmeye çalışıldığında `The operation cannot be performed due to multiple errors` benzer bir hata oluşmasına neden olabilir. 


## <a name="next-steps"></a>Sonraki adımlar

Yük devretme grubunu yapılandırma hakkında ayrıntılı adımlar için aşağıdaki öğreticilere bakın:
- [Yük devretme grubuna tek bir veritabanı ekleme](sql-database-single-database-failover-group-tutorial.md)
- [Bir yük devretme grubuna elastik havuz ekleme](sql-database-elastic-pool-failover-group-tutorial.md)
- [Yönetilen örnekleri bir yük devretme grubuna ekle](sql-database-managed-instance-failover-group-tutorial.md)
 
Azure SQL veritabanı yüksek kullanılabilirlik seçeneklerine genel bakış için bkz. [coğrafi çoğaltma](sql-database-active-geo-replication.md) ve [otomatik yük devretme grupları](sql-database-auto-failover-group.md). 
