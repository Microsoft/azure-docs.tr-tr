---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: sstein
ms.reviewer: vanto
ms.openlocfilehash: 0568860c387aa1239ec56005e404606272ae8275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800095"
---
Bu adımda, bir [MANTıKSAL SQL Server](../database/logical-servers.md) ve AdventureWorksLT örnek verisi kullanan [tek bir veritabanı](../database/single-database-overview.md) oluşturursunuz. Azure portal menüleri ve ekranları kullanarak veya Azure Cloud Shell bir Azure CLı veya PowerShell betiği kullanarak veritabanı oluşturabilirsiniz.

Tüm Yöntemler, sunucu düzeyinde bir güvenlik duvarı kuralını, kullanmakta olduğunuz bilgisayarın genel IP adresinin sunucuya erişmek için ayarlanmasını sağlar. Sunucu düzeyinde güvenlik duvarı kuralları oluşturma hakkında daha fazla bilgi için bkz. [sunucu düzeyinde güvenlik duvarı oluşturma](../database/firewall-create-server-level-portal-quickstart.md). Ayrıca, veritabanı düzeyinde güvenlik duvarı kuralları da ayarlayabilirsiniz. Bkz. [veritabanı düzeyinde güvenlik duvarı kuralı oluşturma](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal bir kaynak grubu, sunucu ve tek veritabanı oluşturmak için:

1. [Portalda](https://portal.azure.com)oturum açın.
1. Arama çubuğundan **Azure SQL** araması yapın ve seçin.
1. **Azure SQL** sayfasında **Ekle**' yi seçin.

   ![Azure SQL 'e Ekle](./media/sql-database-create-single-database/sqldbportal.png)

1. **SQL dağıtım seçeneğini belirleyin** sayfasında, **kaynak türü** altında **tek bir veritabanı** ile **SQL veritabanları** kutucuğunu seçin. **Ayrıntıları göster**' i seçerek farklı veritabanları hakkında daha fazla bilgi görüntüleyebilirsiniz.
1. **Oluştur**’u seçin.

   ![Tek veritabanı oluşturma](./media/sql-database-create-single-database/create-single-database.png)

1. **SQL veritabanı oluştur** formunun **temel bilgiler** sekmesinde, **proje ayrıntıları**' nın altında, henüz seçili değilse doğru Azure **aboneliğini** seçin.
1. **Kaynak grubu** altında **Yeni oluştur**' u seçin, *myresourcegroup* girin ve **Tamam**' ı seçin.
1. Veritabanı **ayrıntıları**' nın altında, **veritabanı adı** için *mysampledatabase* yazın.
1. **Sunucu** Için **Yeni oluştur**' u seçin ve **Yeni sunucu** formunu aşağıdaki gibi doldurun:
   - **Sunucu adı**: *te yapılandırılmış MySQLServer* ve benzersizlik için bazı karakterler girin.
   - **Sunucu Yöneticisi oturum açma**: *azureuser* girin.
   - **Parola**: gereksinimleri karşılayan bir parola girin ve **Parolayı Onayla** alanına yeniden girin.
   - **Konum**: açılır ve **(US) Doğu ABD** gibi bir konum seçin.

   **Tamam**’ı seçin.

   ![Yeni sunucu](./media/sql-database-create-single-database/new-server.png)

   Sunucuda ve veritabanlarında oturum açabilmek için Sunucu Yöneticisi oturum açma bilgilerini ve parolasını kaydedin. Oturum açma veya parolayı unutursanız, veritabanı oluşturulduktan sonra **SQL Server** sayfasında oturum açma adını alabilir veya parolayı sıfırlayabilirsiniz. **SQL Server** sayfasını açmak Için veritabanına **genel bakış** sayfasında sunucu adını seçin.

1. **İşlem + depolama** altında, Varsayılanları yeniden yapılandırmak Istiyorsanız **Veritabanını yapılandır**' ı seçin.

   **Yapılandır** sayfasında, isteğe bağlı olarak şunları yapabilirsiniz:
   - **İşlem katmanını** **sağlanmasından** **sunucusuz** olarak değiştirin.
   - **Sanal çekirdekler** ve **en fazla veri boyutu** ayarlarını gözden geçirin ve değiştirin.
   - Donanım oluşturmayı değiştirmek için **yapılandırmayı Değiştir** ' i seçin.

   Herhangi bir değişiklik yaptıktan sonra **Uygula**' yı seçin.

1. **İleri ' yi** seçin. sayfanın en altındaki Ağ.

   ![Yeni SQL veritabanı-temel sekmesi](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. **Ağ** sekmesinde, **bağlantı yöntemi** altında **ortak uç nokta**' ı seçin.
1. **Güvenlik duvarı kuralları** altında, **geçerli Istemci IP adresini ekle** ' yi **Evet** olarak ayarlayın.
1. Ileri ' yi seçin: sayfanın en altındaki **ek ayarlar** .

   ![Ağ sekmesi](./media/sql-database-create-single-database/networking.png)
  
   Güvenlik Duvarı ayarları hakkında daha fazla bilgi için bkz. [Azure hizmetlerinin ve kaynaklarının bu sunucuya erişmesine Izin verme](../database/network-access-controls-overview.md) ve [Özel uç nokta ekleme](../database/private-endpoint-overview.md).

1. **Ek ayarlar** sekmesinde, **veri kaynağı** bölümünde **var olan verileri kullan** için **örnek**' i seçin.
1. İsteğe bağlı olarak, [SQL Için Azure Defender 'ı](../database/azure-defender-for-sql.md)etkinleştirin.
1. İsteğe bağlı olarak, [Bakım penceresini](../database/maintenance-window.md) , planlı bakımın veritabanınızın en iyi saatinde gerçekleştirilmesi için ayarlayın.
1. Sayfanın alt kısmındaki **gözden geçir + oluştur** ' u seçin.

   ![Ek ayarlar sekmesi](./media/sql-database-create-single-database/additional-settings.png)

1. Ayarları inceledikten sonra **Oluştur**' u seçin.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure komut satırı arabirimi (Azure CLı) kullanarak bir Azure Kaynak grubu, sunucu ve tek veritabanı oluşturabilirsiniz. Azure Cloud Shell kullanmak istemiyorsanız, bilgisayarınıza [Azure CLI 'yı yükleyebilirsiniz](/cli/azure/install-azure-cli) .

Aşağıdaki kod örneğini Azure Cloud Shell çalıştırmak için kod örneği başlık çubuğunda **deneyin** ' i seçin. Cloud Shell açıldığında, kod örneği başlık çubuğunda **Kopyala** ' yı seçin ve kod örneğini Cloud Shell penceresine yapıştırın. Kodda, `<Subscription ID>` Azure ABONELIK Kimliğiniz ile değiştirin ve `$startip` ve için `$endip` , `0.0.0.0` kullanmakta olduğunuz bilgisayarın genel IP adresiyle değiştirin.

Azure 'da oturum açmak için ekrandaki istemleri izleyin ve kodu çalıştırın.

Ayrıca, üstteki çubukta Cloud Shell simgesini seçerek Azure portal Azure Cloud Shell de kullanabilirsiniz.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Portalda Cloud Shell ilk kez kullandığınızda, **hoş geldiniz** Iletişim kutusunda **Bash** ' i seçin. Sonraki oturumlar, bir bash ortamında Azure CLı 'yı kullanacaktır veya Cloud Shell denetim çubuğundan **Bash** seçeneğini belirleyebilirsiniz.

Aşağıdaki Azure CLı kodu, sunucuya erişmek için bir kaynak grubu, sunucu, tek veritabanı ve sunucu düzeyi IP güvenlik duvarı kuralı oluşturur. Oluşturulan kaynak grubunu ve sunucu adlarını kaydettiğinizden emin olun, böylece bu kaynakları daha sonra yönetebilirsiniz.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a server-level firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

Yukarıdaki kod şu Azure CLı komutlarını kullanır:

| Komut | Açıklama |
|---|---|
| [az Account set](/cli/azure/account#az_account_set) | Aboneliği geçerli etkin abonelik olacak şekilde ayarlar. |
| [az group create](/cli/azure/group#az_group_create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Veritabanları ve elastik havuzlar barındıran bir sunucu oluşturur. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_create) | Sunucu düzeyinde bir güvenlik duvarı kuralı oluşturur. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Bir veritabanı oluşturur. |

Azure SQL veritabanı Azure CLı örnekleri hakkında daha fazla bilgi için bkz. [Azure CLI örnekleri](../database/az-cli-script-samples-content-guide.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Windows PowerShell kullanarak bir kaynak grubu, sunucu ve tek veritabanı oluşturabilirsiniz. Azure Cloud Shell kullanmak istemiyorsanız [Azure PowerShell modülünü yükleyebilirsiniz](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aşağıdaki kod örneğini Azure Cloud Shell çalıştırmak için kod başlık çubuğunda **deneyin** ' i seçin. Cloud Shell açıldığında, kod örneği başlık çubuğunda **Kopyala** ' yı seçin ve kod örneğini Cloud Shell penceresine yapıştırın. Kodda, `<Subscription ID>` Azure ABONELIK Kimliğiniz ile değiştirin ve `$startIp` ve için `$endIp` , `0.0.0.0` kullanmakta olduğunuz bilgisayarın genel IP adresiyle değiştirin.

Azure 'da oturum açmak için ekrandaki istemleri izleyin ve kodu çalıştırın.

Ayrıca, üstteki çubukta Cloud Shell simgesini seçerek Azure portal Azure Cloud Shell de kullanabilirsiniz.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Portaldan Cloud Shell ilk kez kullandığınızda, **hoş geldiniz** Iletişim kutusunda **PowerShell** ' i seçin. Sonraki oturumlar PowerShell kullanır veya Cloud Shell denetim çubuğundan seçebilirsiniz.

Aşağıdaki PowerShell kodu, sunucuya erişmek için bir Azure Kaynak grubu, sunucu, tek veritabanı ve güvenlik duvarı kuralı oluşturur. Oluşturulan kaynak grubunu ve sunucu adlarını kaydettiğinizden emin olun, böylece bu kaynakları daha sonra yönetebilirsiniz.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
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
   Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary server..."
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

Yukarıdaki kod bu PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Veritabanları ve elastik havuzlar barındıran bir sunucu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Sunucu için sunucu düzeyinde bir güvenlik duvarı kuralı oluşturur. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Bir veritabanı oluşturur. |

Daha fazla Azure SQL veritabanı PowerShell örneği için bkz. [Azure PowerShell örnekleri](../database/powershell-script-content-guide.md).

---
