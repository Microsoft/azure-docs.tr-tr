---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 11/04/2019
ms.author: mathoma
ms.openlocfilehash: 0fad326107fa101cbba869311724710bd3f5307b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496128"
---
Bu adımda, bir Azure SQL veritabanı tek veritabanı oluşturacaksınız. 

> [!IMPORTANT]
> Bu makaleyi tamamladıktan sonra, kullanmakta olduğunuz bilgisayarın genel IP adresini kullanmak için güvenlik duvarı kuralları ayarladığınızdan emin olun.
>
> Bilgi için, bkz. [veritabanı düzeyinde güvenlik duvarı kuralı oluşturma](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) veya bilgisayarınızın sunucu düzeyinde güvenlik duvarı kuralı IÇIN kullanılan IP adresini belirleme bkz. [sunucu düzeyinde güvenlik duvarı oluşturma](../sql-database-server-level-firewall-rule.md).  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak kaynak grubunuzu ve tek veritabanınızı oluşturun.

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna *Azure SQL* yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
2. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. **Veritabanları** kutucuğunda **Ayrıntıları göster** ' i seçerek farklı veritabanları hakkındaki ek bilgileri görüntüleyebilirsiniz.
3. **Oluştur**' u seçin.

   ![Tek veritabanı oluşturma](../media/sql-database-get-started-portal/create-single-database.png)

3. **Temel bilgiler** sekmesinde, **proje ayrıntıları** bölümünde aşağıdaki değerleri yazın veya seçin:

   - **Abonelik**: açılır ve görünmüyorsa doğru aboneliği seçin.
   - **Kaynak grubu**: **Yeni oluştur**' u seçin, `myResourceGroup`yazın ve **Tamam**' ı seçin.

     ![Yeni SQL veritabanı-temel sekmesi](../media/sql-database-get-started-portal/new-sql-database-basics.png)

4. **Veritabanı ayrıntıları** bölümünde, aşağıdaki değerleri yazın veya seçin:

   - **Veritabanı adı**: `mySampleDatabase`girin.
   - **Sunucu**: **Yeni oluştur**' u seçin, aşağıdaki değerleri girin ve ardından **Seç**' i seçin.
       - **Sunucu adı**: tür `mysqlserver`; Benzersizlik için bazı sayılarla birlikte.
       - **Sunucu Yöneticisi oturum açma**: tür `azureuser`.
       - **Parola**: parola gereksinimlerini karşılayan karmaşık bir parola yazın.
       - **Konum**: açılır listeden `West US`gibi bir konum seçin.

         ![Yeni sunucu](../media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > Bu ve diğer hızlı başlangıçlara yönelik sunucu ve veritabanlarında oturum açabilmek için Sunucu Yöneticisi oturum açma bilgilerini ve parolayı kaydetmeyi unutmayın. Oturum açma veya parolayı unutursanız **SQL Server** sayfasında oturum açma adını alabilir veya parolayı sıfırlayabilirsiniz. **SQL Server** sayfasını açmak için veritabanı oluşturulduktan sonra veritabanına **genel bakış** sayfasında sunucu adını seçin.

   - **SQL elastik havuzu kullanmak Istiyor musunuz**: **Hayır** seçeneğini belirleyin.
   - **İşlem + depolama**: **Veritabanını yapılandır**' ı seçin. 

     ![SQL veritabanı ayrıntıları](../media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **Sağlanan**' ı seçin.

     ![Sağlanan 4. nesil](../media/sql-database-get-started-portal/create-database-provisioned.png)

   - **Sanal çekirdekler**için ayarları gözden geçirin ve **veri boyutu üst sınırı**. Bunları istediğiniz şekilde değiştirin. 
     - İsteğe bağlı olarak, donanım oluşturma 'yı değiştirmek için **yapılandırmayı Değiştir** ' i de seçebilirsiniz.
   - **Uygula**’yı seçin.

5. **Ek ayarlar** sekmesini seçin. 
6. **Veri kaynağı** bölümünde, **mevcut verileri kullan**altında `Sample`' yi seçin.

   ![Ek SQL VERITABANı ayarları](../media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > Bu verileri kullanan kolayca bu ve diğer Azure SQL veritabanı hızlı başlangıçlarını izleyebilmeniz için **örnek (AdventureWorksLT)** verilerini seçtiğinizden emin olun.

7. Kalan değerleri varsayılan olarak bırakın ve formun altındaki **gözden geçir + oluştur** ' u seçin.
8. Son ayarları gözden geçirin ve **Oluştur**' u seçin.

9. Kaynak grubunu, sunucuyu ve veritabanını dağıtmak ve sağlamak için **SQL veritabanı** formunda **Oluştur** ' u seçin.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell kullanarak kaynak grubunuzu ve tek bir veritabanınızı oluşturun.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "PWD27!"+(New-Guid).Guid
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName 
   Write-host "Password is" $password  
   Write-host "Server name is" $serverName 

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary logical server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

Makalenin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Mantıksal sunucu için bir güvenlik duvarı kuralı oluşturur. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Yeni bir Azure SQL veritabanı tek veritabanı oluşturur. | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

AZ CLı kullanarak kaynak grubunuzu ve tek veritabanınızı oluşturun.

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=myResourceGroup-$RANDOM
   location=SouthCentralUS
   adminLogin=azureuser
   password="PWD27!"+`openssl rand -base64 18`
   serverName=mysqlserver-$RANDOM
   databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # The ip address range that you want to allow to access your DB. 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB
   startip=0.0.0.0
   endip=0.0.0.0
  
   # Connect to Azure
   az login

   # Set the subscription context for the Azure account
   az account set -s $subscriptionID

   # Create a resource group
   echo "Creating resource group..."
   az group create \
      --name $resourceGroupName \
      --location $location \
      --tags Owner[=SQLDB-Samples]

   # Create a logical server in the resource group
   echo "Creating primary logical server..."
   az sql server create \
      --name $serverName \
      --resource-group $resourceGroupName \
      --location $location  \
      --admin-user $adminLogin \
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $serverName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip

   # Create a gen5 1vCore database in the server 
   echo "Creating a gen5 2 vCore database..."
   az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2
   ```

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Aboneliği geçerli etkin abonelik olacak şekilde ayarlar. | 
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Sunucunun güvenlik duvarı kurallarını oluşturur. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Bir veritabanı oluşturur. | 


---
