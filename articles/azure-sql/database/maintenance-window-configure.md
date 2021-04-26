---
title: Bakım penceresini yapılandırma (Önizleme)
description: Azure SQL veritabanlarınızda, elastik Havuzlarınızda ve yönetilen örnek veritabanlarında planlı bakımın ne zaman yapılacağını nasıl ayarlayabileceğinizi öğrenin.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 9771c68dda6f457586f27ea45fbc52aa118e8006
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874792"
---
# <a name="configure-maintenance-window-preview"></a>Bakım penceresini yapılandırma (Önizleme)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Kaynak oluşturma sırasında veya bir kaynak oluşturulduktan sonra herhangi bir zamanda Azure SQL veritabanı, elastik havuz veya Azure SQL yönetilen örnek veritabanı için [Bakım penceresini (Önizleme)](maintenance-window.md) yapılandırın. 

*Sistem varsayılan* bakım penceresi, yoğun iş saatleri kesintilerinin önüne geçmek için 5 pm Ile 8:00:00 (kaynağın bulunduğu Azure bölgesinin yerel saati). *Sistem varsayılan* bakım penceresi en iyi zaman değilse, kullanılabilir diğer bakım pencerelerinin birini seçin.

Farklı bir bakım penceresine geçiş özelliği, her hizmet düzeyi veya her bölge için kullanılamaz. Kullanılabilirlik hakkında daha fazla bilgi için bkz. [bakım penceresi kullanılabilirliği](maintenance-window.md#availability).

> [!Important]
> Bakım penceresini yapılandırmak, Azure SQL kaynağının hizmet katmanını değiştirmeye benzer şekilde, uzun süren bir zaman uyumsuz işlemdir. İşlem sırasında, işlemin sonunda gerçekleşen kısa bir yeniden yapılandırma haricinde ve genellikle uzun süreli işlem durumunda bile 8 saniyeye kadar süren kaynak kullanılabilir. Yeniden yapılandırma işleminin etkisini en aza indirmek için işlemi yoğun saatlerin dışında gerçekleştirmeniz gerekir.

## <a name="configure-maintenance-window-during-database-creation"></a>Veritabanı oluşturma sırasında bakım penceresini yapılandırma 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Bir veritabanı, elastik havuz veya yönetilen örnek oluştururken bakım penceresini yapılandırmak için, **ek ayarlar** sayfasında istenen **Bakım penceresini** ayarlayın. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Tek bir veritabanı veya elastik havuz oluştururken bakım penceresini ayarlama

Yeni bir veritabanı veya havuz oluşturma hakkında adım adım bilgiler için bkz. [Azure SQL veritabanı tek veritabanı oluşturma](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Veritabanı ek ayarlar sekmesi oluştur":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Yönetilen örnek oluştururken bakım penceresini ayarla

Yeni bir yönetilen örnek oluşturma hakkında adım adım bilgiler için bkz. [Azure SQL yönetilen örneği oluşturma](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Yönetilen örnek ek ayarlar sekmesi oluştur":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Aşağıdaki örneklerde Azure PowerShell kullanarak bakım penceresinin nasıl yapılandırılacağı gösterilmektedir. [Azure PowerShell yükleyebilir](/powershell/azure/install-az-ps)veya Azure Cloud Shell kullanabilirsiniz.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.


## <a name="discover-available-maintenance-windows"></a>Kullanılabilir bakım pencerelerini bul

Bakım penceresini ayarlarken, her bölgenin, veritabanının veya havuzun bulunduğu bölge için saat dilimine karşılık gelen kendi bakım penceresi seçenekleri vardır. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>SQL veritabanı ve elastik havuz bakım pencerelerini bulma 

Aşağıdaki örnek, [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet 'ini kullanarak *eastus2* bölgesi için kullanılabilir bakım pencerelerini döndürür. Veritabanları ve elastik havuzlar için `MaintenanceScope` olarak ayarlayın `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL yönetilen örnek bakım pencerelerini bul 

Aşağıdaki örnek, [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) cmdlet 'ini kullanarak *eastus2* bölgesi için kullanılabilir bakım pencerelerini döndürür. Yönetilen örnekler için olarak ayarlayın `MaintenanceScope` `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Tek bir veritabanı oluştururken bakım penceresini ayarla

Aşağıdaki örnek yeni bir veritabanı oluşturur ve [Yeni-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) cmdlet 'ini kullanarak bakım penceresini ayarlar. `-MaintenanceConfigurationId`Veritabanınızın bölgesi için geçerli bir değer olarak ayarlanmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Elastik havuz oluştururken bakım penceresini ayarlama

Aşağıdaki örnek yeni bir elastik havuz oluşturur ve [New-Azsqtalakpool](/powershell/module/az.sql/new-azsqlelasticpool) cmdlet 'ini kullanarak bakım penceresini ayarlar. Bakım penceresi elastik havuzda ayarlanır, bu nedenle havuzdaki tüm veritabanlarının havuzun bakım penceresi zamanlaması vardır. , `-MaintenanceConfigurationId` Havuzunuzun bölgesi için geçerli bir değer olarak ayarlanmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Yönetilen örnek oluştururken bakım penceresini ayarla

Aşağıdaki örnek yeni bir yönetilen örnek oluşturur ve [Yeni-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance) cmdlet 'ini kullanarak bakım penceresini ayarlar. Bakım penceresi örneğinde ayarlanır, böylece örnekteki tüm veritabanları örneğin bakım penceresi zamanlaması olur. İçin `-MaintenanceConfigurationId` *MaintenanceConfigName* , örneğinizin bölgesi için geçerli bir değer olmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Aşağıdaki örneklerde Azure CLı kullanarak bakım penceresinin nasıl yapılandırılacağı gösterilmektedir. [Azure CLI 'yı yükleyebilir](/cli/azure/install-azure-cli)veya Azure Cloud Shell kullanabilirsiniz. 

Azure CLı ile bakım penceresini yapılandırma yalnızca SQL yönetilen örneği için kullanılabilir.

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell’i başlatma

Azure Cloud Shell, bu makaledeki adımları çalıştırmak için kullanabileceğiniz ücretsiz bir etkileşimli kabuktur. Yaygın Azure araçları, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. 

Cloud Shell'i açmak için kod bloğunun sağ üst köşesinden **Deneyin**'i seçmeniz yeterlidir. Ayrıca, ' a giderek ayrı bir tarayıcı sekmesinde Cloud Shell de başlatabilirsiniz [https://shell.azure.com/cli](https://shell.azure.com/cli) . **Kopyala**’yı seçerek kod bloğunu kopyalayın, Cloud Shell’e yapıştırın ve Enter tuşuna basarak çalıştırın.

## <a name="discover-available-maintenance-windows"></a>Kullanılabilir bakım pencerelerini bul

Bakım penceresini ayarlarken, her bölgenin, veritabanının veya havuzun bulunduğu bölge için saat dilimine karşılık gelen kendi bakım penceresi seçenekleri vardır.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>SQL veritabanı ve elastik havuz bakım pencerelerini bulma

Aşağıdaki örnek, [az Maintenance public-Configuration List](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) komutunu kullanarak *eastus2* bölgesi için kullanılabilir bakım pencerelerini döndürür. Veritabanları ve elastik havuzlar için `maintenanceScope` olarak ayarlayın `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>SQL yönetilen örnek bakım pencerelerini bul

Aşağıdaki örnek, [az Maintenance public-Configuration List](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) komutunu kullanarak *eastus2* bölgesi için kullanılabilir bakım pencerelerini döndürür. Yönetilen örnekler için olarak ayarlayın `maintenanceScope` `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Tek bir veritabanı oluştururken bakım penceresini ayarla

Aşağıdaki örnek, yeni bir veritabanı oluşturur ve [az SQL DB Create](/cli/azure/sql/db#az_sql_db_create) komutunu kullanarak bakım penceresini ayarlar. `--maint-config-id`(Veya `-m` ) veritabanınızın bölgesi için geçerli bir değer olarak ayarlanmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Elastik havuz oluştururken bakım penceresini ayarlama

Aşağıdaki örnek yeni bir elastik havuz oluşturur ve [az SQL elastik havuz Create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) cmdlet 'ini kullanarak bakım penceresini ayarlar. Bakım penceresi elastik havuzda ayarlanır, bu nedenle havuzdaki tüm veritabanlarının havuzun bakım penceresi zamanlaması vardır. `--maint-config-id`(Veya `-m` ), havuzunuzun bölgesi için geçerli bir değer olarak ayarlanmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Yönetilen örnek oluştururken bakım penceresini ayarla

Aşağıdaki örnek, yeni bir yönetilen örnek oluşturur ve [az SQL mı Create](/cli/azure/sql/mi#az_sql_mi_create)kullanarak bakım penceresini ayarlar. Bakım penceresi örneğinde ayarlanır, böylece örnekteki tüm veritabanları örneğin bakım penceresi zamanlaması olur. *MaintenanceConfigName* , örneğinizin bölgesi için geçerli bir değer olmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Mevcut veritabanları için bakım penceresini yapılandırma


Bir veritabanına bakım pencere seçimi uygulanırken, Azure 'un gerekli değişiklikleri uyguladığı bazı durumlarda kısa bir yeniden yapılandırma (birkaç saniye) yaşanmış olabilir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aşağıdaki adımlar, Azure portal kullanarak mevcut bir veritabanı, elastik havuzda veya yönetilen örnekteki bakım penceresini ayarlar:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Mevcut bir veritabanı veya elastik havuz için bakım penceresini ayarla

1. Bakım penceresini ayarlamak istediğiniz SQL veritabanına veya elastik havuza gidin.
1. **Ayarlar** menüsünde **bakım**' ı seçin ve ardından istenen bakım penceresini seçin.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="SQL veritabanı bakım sayfası":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Mevcut bir yönetilen örnek için bakım penceresini ayarla

1. Bakım penceresini ayarlamak istediğiniz yönetilen örneğe gidin.
1. **Ayarlar** menüsünde **bakım**' ı seçin ve ardından istenen bakım penceresini seçin.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="SQL yönetilen örnek bakım sayfası":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Mevcut bir veritabanı için bakım penceresini ayarla

Aşağıdaki örnek, [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet 'ini kullanarak var olan bir veritabanında bakım penceresini ayarlar. `-MaintenanceConfigurationId`Veritabanınızın bölgesi için geçerli bir değer olarak ayarlanmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Mevcut bir elastik havuzda bakım penceresini ayarla

Aşağıdaki örnek, [set-Azsqtalakpool](/powershell/module/az.sql/set-azsqlelasticpool) cmdlet 'ini kullanarak mevcut bir elastik havuzda bakım penceresini ayarlar. `$maintenanceConfig`Değerin havuzunuzun bölgesi için geçerli bir değer olduğundan emin olmak önemlidir.  Bir bölgenin geçerli değerlerini almak için bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Mevcut bir yönetilen örnek üzerinde bakım penceresini ayarla

Aşağıdaki örnek, [set-Azsqlınstance](/powershell/module/az.sql/set-azsqlinstance) cmdlet 'ini kullanarak var olan bir yönetilen örnekteki bakım penceresini ayarlar. `$maintenanceConfig`Değerin örneğinizin bölgesi için geçerli bir değer olduğundan emin olmak önemlidir.  Bir bölgenin geçerli değerlerini almak için bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

Aşağıdaki örneklerde Azure CLı kullanarak bakım penceresinin nasıl yapılandırılacağı gösterilmektedir. [Azure CLI 'yı yükleyebilir](/cli/azure/install-azure-cli)veya Azure Cloud Shell kullanabilirsiniz.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Mevcut bir veritabanı için bakım penceresini ayarla

Aşağıdaki örnek, [az SQL DB Update](/cli/azure/sql/db#az_sql_db_update) komutunu kullanarak var olan bir veritabanının bakım penceresini ayarlar. `--maint-config-id`(Veya `-m` ) veritabanınızın bölgesi için geçerli bir değer olarak ayarlanmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Mevcut bir elastik havuzda bakım penceresini ayarla

Aşağıdaki örnek, [az SQL elastik havuz Update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) komutunu kullanarak mevcut bir elastik havuzda bakım penceresini ayarlar. `maintenanceConfig`Değerin havuzunuzun bölgesi için geçerli bir değer olduğundan emin olmak önemlidir.  Bir bölgenin geçerli değerlerini almak için bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Mevcut bir yönetilen örnek üzerinde bakım penceresini ayarla

Aşağıdaki örnek, [az SQL mı Update](/cli/azure/sql/mi#az_sql_mi_update)kullanarak bakım penceresini ayarlar. Bakım penceresi örneğinde ayarlanır, böylece örnekteki tüm veritabanları örneğin bakım penceresi zamanlaması olur. İçin `-MaintenanceConfigurationId` *MaintenanceConfigName* , örneğinizin bölgesi için geçerli bir değer olmalıdır. Bölgeniz için geçerli değerler almak üzere bkz. [kullanılabilir bakım pencerelerini bulma](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Kaynakları temizleme

Gereksiz ücretlerden kaçınmak için, bunları tamamladıktan sonra gereksiz kaynakları sildiğinizden emin olun.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Artık ihtiyacınız olmayan SQL veritabanına veya elastik havuza gidin.
1. **Genel bakış** menüsünde, kaynağı silme seçeneğini belirleyin.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Sonraki adımlar

- Bakım penceresi hakkında daha fazla bilgi için bkz. [bakım penceresi (Önizleme)](maintenance-window.md).
- Daha fazla bilgi için bkz. [bakım penceresi hakkında SSS](maintenance-window-faq.yml).
- Performansı iyileştirme hakkında daha fazla bilgi edinmek için bkz. [Azure SQL veritabanı ve Azure SQL yönetilen örneği 'Nde izleme ve performans ayarları](monitor-tune-overview.md).
