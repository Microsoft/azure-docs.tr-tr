---
title: 'Öğretici: bir yük devretme grubuna tek bir veritabanı ekleme'
description: Azure portal, PowerShell veya Azure CLı kullanarak bir yük devretme grubuna Azure SQL veritabanı tek veritabanı ekleme.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 6e3b4be836699cc200d30168c14462f81136646b
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821089"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Öğretici: bir yük devretme grubuna Azure SQL veritabanı tek veritabanı ekleme

Azure SQL veritabanı tek veritabanı için bir yük devretme grubu yapılandırın ve Azure portal, PowerShell veya Azure CLı kullanarak yük devretme testi yapın.  Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> - Azure SQL veritabanı tek veritabanı oluşturun.
> - İki mantıksal SQL Server arasında tek bir veritabanı için [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturun.
> - Yük devretme testi.

## <a name="prerequisites"></a>Ön koşullar

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun: 

- Azure aboneliği. Henüz bir [hesabınız yoksa ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Öğreticiyi tamamlayabilmeniz için aşağıdaki öğelerin bulunduğundan emin olun:

- Azure aboneliği. Henüz bir [hesabınız yoksa ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Öğreticiyi tamamlayabilmeniz için aşağıdaki öğelerin bulunduğundan emin olun:

- Azure aboneliği. Henüz bir [hesabınız yoksa ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)'nın en son sürümü. 

---

## <a name="1---create-a-single-database"></a>1-tek veritabanı oluşturma 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-yük devretme grubu oluşturma 
Bu adımda, mevcut bir Azure SQL sunucusu ile başka bir bölgedeki yeni bir Azure SQL sunucusu arasında bir [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturacaksınız. Ardından örnek veritabanını yük devretme grubuna ekleyin. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Yük devretme grubunuzu oluşturun ve Azure portal kullanarak tek veritabanınızı veritabanına ekleyin. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. `mySampleDatabase`gibi, Bölüm 1 ' de oluşturulan tek veritabanını seçin. 
1. Sunucu ayarlarını açmak için sunucu **adı** altında sunucunun adını seçin.

   ![Tek veritabanı için açık sunucu](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve sonra yeni bir yük devretme grubu oluşturmak için **Grup Ekle** ' yi seçin. 

    ![Yeni Yük devretme grubu Ekle](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. **Yük devretme grubu** sayfasında, aşağıdaki değerleri girin veya seçin ve ardından **Oluştur**' u seçin:
    - **Yük devretme grubu adı**: `failovergrouptutorial`gibi benzersiz bir yük devretme grubu adı yazın. 
    - **İkincil sunucu**: *gerekli ayarları yapılandırma* seçeneğini belirleyin ve ardından **Yeni bir sunucu oluşturmayı**seçin. Alternatif olarak, zaten var olan bir sunucuyu ikincil sunucu olarak seçebilirsiniz. Aşağıdaki değerleri girdikten sonra **Seç**' i seçin. 
        - **Sunucu adı**: ikincil sunucu için `mysqlsecondary`gibi benzersiz bir ad yazın. 
        - **Sunucu Yöneticisi oturum açma**: tür `azureuser`
        - **Parola**: parola gereksinimlerini karşılayan karmaşık bir parola yazın.
        - **Konum**: açılır listeden `East US`gibi bir konum seçin. Bu konum, birincil sunucunuz ile aynı konumda olamaz.

    > [!NOTE]
    > Sunucu oturum açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir. 
    
      ![Yük devretme grubu için ikincil sunucu oluşturma](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Grup Içindeki veritabanları**: ikincil bir sunucu seçildikten sonra, bu seçenek kilidi açılmış olur. **Eklenecek veritabanlarını seçmek** ve sonra Bölüm 1 ' de oluşturduğunuz veritabanını seçmek için bu seçeneği belirleyin. Veritabanını yük devretme grubuna eklemek, coğrafi çoğaltma işlemini otomatik olarak başlatır. 
        
    ![SQL DB 'yi yük devretme grubuna ekle](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Yük devretme grubunuzu oluşturun ve PowerShell kullanarak tek veritabanınızı buna ekleyin. 

   > [!NOTE]
   > Sunucu oturum açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir. 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule   
   
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

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Mantıksal sunucu için bir güvenlik duvarı kuralı oluşturur. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Yeni bir Azure SQL veritabanı tek veritabanı oluşturur. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Yeni bir yük devretme grubu oluşturur. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Bir veya daha fazla SQL veritabanını alır. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Bir yük devretme grubuna bir veya daha fazla Azure SQL veritabanı ekler. |

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
Yük devretme grubunuzu oluşturun ve AZ CLı kullanarak tek veritabanınızı ona ekleyin. 

   > [!NOTE]
   > Sunucu oturum açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir. 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password

   # Configure a firewall rule for the server
   echo "Configuring firewall..."
   az sql server firewall-rule create \
      --resource-group $resourceGroupName \
      --server $drServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```

Öğreticinin bu bölümü aşağıdaki az CLı cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Sunucunun güvenlik duvarı kurallarını oluşturur. | 
| [az SQL yük devretme-Grup oluşturma](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Bir yük devretme grubu oluşturur. | 

---

## <a name="3---test-failover"></a>3-yük devretme testi 
Bu adımda, yük devretme grubunuzu ikincil sunucuya devreder ve sonra Azure portal kullanarak yeniden başarısız olursunuz. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal kullanarak yük devretmeyi test edin. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Bölüm 2 ' de oluşturulan tek veritabanını seçin, örneğin `mySampleDatbase`. 
1. Sunucu ayarlarını açmak için sunucu **adı** altında sunucunun adını seçin.

   ![Tek veritabanı için açık sunucu](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve ardından Bölüm 2 ' de oluşturduğunuz yük devretme grubunu seçin. 
  
   ![Portaldan yük devretme grubunu seçin](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. 
1. Örnek tek veritabanınızı içeren yük devretme grubunuzun yükünü devretmek için görev bölmesinden **Yük devretmeyi** seçin. 
1. TDS oturumlarının kesileceğini bildiren uyarıda **Evet** ' i seçin. 

   ![SQL veritabanınızı içeren yük devretme grubunuzun yükünü devreder](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Hangi sunucunun artık birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa iki sunucu, bulunan rolleri değiştirmiş olmalıdır. 
1. Sunucuları ilk rollerine geri dönmek için **Yük devretmeyi** yeniden seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell kullanarak yük devretmeyi test etme. 


İkincil çoğaltmanın rolünü kontrol edin: 

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
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
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName 
   ```

Yük devretme grubunu birincil sunucuya geri çevir:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL veritabanı yük devretme gruplarını alır veya listeler. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Bir Azure SQL veritabanı yük devretme grubunun yük devretmesini yürütür. |



# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
AZ CLı kullanarak yük devretmeyi test edin. 

Hangi sunucunun ikincil olduğunu doğrulayın:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

İkincil sunucuya Yük devret: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Yük devretme grubunu birincil sunucuya geri çevir:

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

Öğreticinin bu bölümü aşağıdaki az CLı cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [az SQL yük devretme-Grup listesi](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Bir sunucudaki yük devretme gruplarını listeler. |
| [az SQL yük devretme-grup kümesi-birincil](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Geçerli birincil sunucudaki tüm veritabanlarının yükünü devretmek için yük devretme grubunun birincil kısmını ayarlayın. | 

---

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Kaynak grubunu silerek kaynakları temizleyin. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal kullanarak kaynak grubunu silin. 

1. [Azure Portal](https://portal.azure.com)kaynak grubunuza gidin.
1. Gruptaki tüm kaynakların yanı sıra kaynak grubunun kendisini silmek için **kaynak grubunu sil** ' i seçin. 
1. Kaynak grubunun adını `myResourceGroup`, metin kutusuna yazın ve ardından **Sil** ' i seçerek kaynak grubunu silin.  

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak kaynak grubunu silin. 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

AZ CLı kullanarak kaynak grubunu silin. 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

Öğreticinin bu bölümü aşağıdaki az CLı cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

---


## <a name="full-scripts"></a>Tam betikler

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Mantıksal sunucu için bir güvenlik duvarı kuralı oluşturur. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Yeni bir Azure SQL veritabanı tek veritabanı oluşturur. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Yeni bir yük devretme grubu oluşturur. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Bir veya daha fazla SQL veritabanını alır. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Bir yük devretme grubuna bir veya daha fazla Azure SQL veritabanı ekler. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL veritabanı yük devretme gruplarını alır veya listeler. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Bir Azure SQL veritabanı yük devretme grubunun yük devretmesini yürütür. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır | 

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [az Account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Aboneliği geçerli etkin abonelik olacak şekilde ayarlar. | 
| [az group create](/cli/azure/group#az-group-create) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [az SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) | Sunucunun güvenlik duvarı kurallarını oluşturur. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Bir veritabanı oluşturur. | 
| [az SQL yük devretme-Grup oluşturma](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Bir yük devretme grubu oluşturur. | 
| [az SQL yük devretme-Grup listesi](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Bir sunucudaki yük devretme gruplarını listeler. |
| [az SQL yük devretme-grup kümesi-birincil](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Geçerli birincil sunucudaki tüm veritabanlarının yükünü devretmek için yük devretme grubunun birincil kısmını ayarlayın. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Bir kaynak grubunu tüm iç içe geçmiş kaynaklar dahil siler. |

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal için kullanılabilir komut yok. 
 
---

Diğer Azure SQL veritabanı betiklerini buradan bulabilirsiniz: [Azure PowerShell](sql-database-powershell-samples.md) ve [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir yük devretme grubuna Azure SQL veritabanı tek veritabanı eklediniz ve yük devretme işlemi test edildi. Şunları öğrendiniz: 

> [!div class="checklist"]
> - Azure SQL veritabanı tek veritabanı oluşturun. 
> - İki mantıksal SQL Server arasında tek bir veritabanı için [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturun.
> - Yük devretme testi.

Esnek havuzunuzu bir yük devretme grubuna ekleme hakkında sonraki öğreticiye ilerleyin. 

> [!div class="nextstepaction"]
> [Öğretici: bir yük devretme grubuna Azure SQL veritabanı elastik havuzu ekleme](sql-database-elastic-pool-failover-group-tutorial.md)
