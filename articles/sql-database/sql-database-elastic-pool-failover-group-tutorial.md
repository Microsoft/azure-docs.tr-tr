---
title: 'Öğretici: bir yük devretme grubuna elastik havuz ekleme'
description: Azure portal, PowerShell veya Azure CLı kullanarak bir yük devretme grubuna Azure SQL veritabanı elastik havuzu ekleme.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: e2ae9afaf7c1dcc1794b90d4851fdd60298b5ad6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823884"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Öğretici: bir yük devretme grubuna Azure SQL veritabanı elastik havuzu ekleme

Azure SQL veritabanı elastik havuzu için bir yük devretme grubu yapılandırın ve Azure portal kullanarak yük devretme testi yapın.  Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> - Azure SQL veritabanı tek veritabanı oluşturun.
> - Tek veritabanını elastik bir havuza ekleyin. 
> - İki mantıksal SQL Server arasında iki elastik havuz için bir [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturun.
> - Yük devretme testi.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun: 

- Azure aboneliği. Henüz bir [hesabınız yoksa ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .


## <a name="1---create-a-single-database"></a>1-tek veritabanı oluşturma 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2-elastik havuza tek veritabanı ekleme
Bu adımda, bir elastik havuz oluşturacak ve tek veritabanınızı bu veritabanına eklersiniz. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak elastik havuzunuzu oluşturun. 


1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. Veritabanları kutucuğunda ayrıntıları göster ' i seçerek farklı veritabanları hakkındaki ek bilgileri görüntüleyebilirsiniz.
1. **SQL veritabanları** kutucuğunda **kaynak türü** açılır listesinden **elastik havuz** ' ı seçin. Elastik havuzunuzu oluşturmak için **Oluştur** ' u seçin. 

    ![Elastik havuz Seç](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Elastik havuzunuzu aşağıdaki değerlerle yapılandırın:
   - **Ad**: `myElasticPool`gibi elastik havuzunuz için benzersiz bir ad sağlayın. 
   - **Abonelik**: açılır listeden aboneliğinizi seçin.
   - **ResourceGroup**: 1. bölümde oluşturduğunuz kaynak grubundan açılan listeden `myResourceGroup` seçin. 
   - **Sunucu**: açılan listeden, Bölüm 1 ' de oluşturduğunuz sunucuyu seçin.  

       ![Elastik havuz için yeni sunucu oluştur](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **İşlem + depolama**: işlem, depolama alanınızı yapılandırmak ve esnek havuzunuza tek veritabanınızı eklemek için **elastik havuzu Yapılandır** ' ı seçin. **Havuz ayarları** sekmesinde, 2 sanal çekirdek ve 32 Ile varsayılan 5. nesil bırakın. 

1. **Yapılandır** sayfasında **veritabanları** sekmesini seçin ve ardından **veritabanı Ekle**' yi seçin. Bölüm 1 ' de oluşturduğunuz veritabanını seçin ve ardından, elastik havuzunuza eklemek için **Uygula** ' yı seçin. Esnek havuz ayarlarınızı uygulamak ve **Yapılandır** sayfasını kapatmak Için yeniden **Uygula** ' yı seçin. 

    ![Elastik havuza SQL DB ekleme](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Elastik havuz ayarlarınızı gözden geçirmek için **gözden geçir + oluştur** seçeneğini belirleyin ve ardından esnek havuzunuzu oluşturmak için **Oluştur** ' u seçin 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell kullanarak elastik havuzlarınızı ve ikincil sunucunuzu oluşturun. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL veritabanı için elastik veritabanı havuzu oluşturur.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Bir veritabanının özelliklerini ayarlar veya var olan bir veritabanını esnek bir havuza taşıanlar. | 

---

## <a name="3---create-the-failover-group"></a>3-yük devretme grubu oluşturma 
Bu adımda, mevcut bir Azure SQL sunucusu ile başka bir bölgedeki yeni bir Azure SQL sunucusu arasında bir [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturacaksınız. Ardından, elastik havuzu yük devretme grubuna ekleyin. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yük devretme grubunuz oluşturun. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Önceki bölümde oluşturulan elastik havuzu seçin, örneğin `myElasticPool`. 
1. **Genel bakış** bölmesinde sunucu **adı** bölümünde sunucu adı ' nı seçerek sunucu ayarlarını açın.
  
    ![Elastik havuz için sunucu açma](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve sonra yeni bir yük devretme grubu oluşturmak için **Grup Ekle** ' yi seçin. 

    ![Yeni Yük devretme grubu Ekle](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. **Yük devretme grubu** sayfasında, aşağıdaki değerleri girin veya seçin ve ardından **Oluştur**' u seçin:
    - **Yük devretme grubu adı**: `failovergrouptutorial`gibi benzersiz bir yük devretme grubu adı yazın. 
    - **İkincil sunucu**: *gerekli ayarları yapılandırma* seçeneğini belirleyin ve ardından **Yeni bir sunucu oluşturmayı**seçin. Alternatif olarak, zaten var olan bir sunucuyu ikincil sunucu olarak seçebilirsiniz. Yeni ikincil sunucunuz için aşağıdaki değerleri girdikten sonra **Seç**' i seçin. 
        - **Sunucu adı**: ikincil sunucu için `mysqlsecondary`gibi benzersiz bir ad yazın. 
        - **Sunucu Yöneticisi oturum açma**: tür `azureuser`
        - **Parola**: parola gereksinimlerini karşılayan karmaşık bir parola yazın.
        - **Konum**: açılır listeden `East US`gibi bir konum seçin. Bu konum, birincil sunucunuz ile aynı konumda olamaz.

       > [!NOTE]
       > Sunucu oturum açma ve güvenlik duvarı ayarları, birincil sunucunuzun bilgileriyle eşleşmelidir. 
    
       ![Yük devretme grubu için ikincil sunucu oluşturma](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. **Grup Içindeki veritabanları** ' nı seçin ve ardından Bölüm 2 ' de oluşturduğunuz elastik havuzu seçin. İkincil sunucuda bir elastik havuz oluşturmanızı isteyen bir uyarı görüntülenmelidir. Uyarıyı seçin ve ardından ikincil sunucuda elastik havuzu oluşturmak için **Tamam** ' ı seçin. 
        
    ![Esnek havuzu yük devretme grubuna ekle](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Elastik havuz ayarlarınızı yük devretme grubuna uygulamak için **Seç** ' i seçin ve ardından yük devretme grubunuzu oluşturmak için **Oluştur** ' u seçin. Elastik havuzun yük devretme grubuna eklenmesi, coğrafi çoğaltma işlemini otomatik olarak başlatır.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak yük devretme grubunuz oluşturun. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
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
   $failoverGroup
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Mantıksal sunucu için bir güvenlik duvarı kuralı oluşturur. | 
| [New-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL veritabanı için elastik veritabanı havuzu oluşturur.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Yeni bir yük devretme grubu oluşturur. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Bir yük devretme grubuna bir veya daha fazla Azure SQL veritabanı ekler. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL veritabanı yük devretme gruplarını alır veya listeler. |

---


## <a name="4---test-failover"></a>4-yük devretme testi 
Bu adımda, yük devretme grubunuzu ikincil sunucuya devreder ve sonra Azure portal kullanarak yeniden başarısız olursunuz. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak yük devretme grubunuzun yük devretmesini test edin. 

1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. Önceki bölümde oluşturulan elastik havuzu seçin, örneğin `myElasticPool`. 
1. Sunucu ayarlarını açmak için sunucu **adı** altında sunucunun adını seçin.

    ![Elastik havuz için sunucu açma](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. **Ayarlar** bölmesinde **Yük devretme grupları** ' nı seçin ve ardından Bölüm 2 ' de oluşturduğunuz yük devretme grubunu seçin. 
  
   ![Portaldan yük devretme grubunu seçin](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. 
1. Elastik havuzunuzu içeren yük devretme grubunuzun yükünü devretmek için görev bölmesinden **Yük devretmeyi** seçin. 
1. TDS oturumlarının kesileceğini bildiren uyarıda **Evet** ' i seçin. 

   ![SQL veritabanınızı içeren yük devretme grubunuzun yükünü devreder](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Hangi sunucunun birincil olduğunu ve hangi sunucunun ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa iki sunucu, bulunan rolleri değiştirmiş olmalıdır. 
1. Yük devretme grubundan özgün ayarlara geri dönmek için **Yük devretmeyi** yeniden seçin. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak yük devretme grubunuzun yük devretmesini test edin. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Yük devretme grubunuzu ikincil sunucuya devreder ve sonra PowerShell kullanarak yeniden devreder. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL veritabanı yük devretme gruplarını alır veya listeler. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Bir Azure SQL veritabanı yük devretme grubunun yük devretmesini yürütür. |


---

## <a name="clean-up-resources"></a>Kaynakları temizleme 

Kaynak grubunu silerek kaynakları temizleyin. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)


1. [Azure Portal](https://portal.azure.com)kaynak grubunuza gidin.
1. Gruptaki tüm kaynakların yanı sıra kaynak grubunun kendisini silmek için **kaynak grubunu sil** ' i seçin. 
1. Kaynak grubunun adını `myResourceGroup`, metin kutusuna yazın ve ardından **Sil** ' i seçerek kaynak grubunu silin. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak kaynaklarınızı temizleyin. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```
---

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'ini kullanır:

| Komut | Notlar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır | 

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

## <a name="full-script"></a>Tam betik

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Tüm kaynakların depolandığı bir kaynak grubu oluşturur. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Tek veritabanları ve elastik havuzlar barındıran bir SQL veritabanı sunucusu oluşturur. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Mantıksal sunucu için bir güvenlik duvarı kuralı oluşturur. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Yeni bir Azure SQL veritabanı tek veritabanı oluşturur. | 
| [New-Azsqtalaçıkartma havuzu](/powershell/module/az.sql/new-azsqlelasticpool) | Azure SQL veritabanı için elastik veritabanı havuzu oluşturur.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Bir veritabanının özelliklerini ayarlar veya var olan bir veritabanını esnek bir havuza taşıanlar. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Yeni bir yük devretme grubu oluşturur. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Bir veya daha fazla SQL veritabanını alır. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Bir yük devretme grubuna bir veya daha fazla Azure SQL veritabanı ekler. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Azure SQL veritabanı yük devretme gruplarını alır veya listeler. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Bir Azure SQL veritabanı yük devretme grubunun yük devretmesini yürütür. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır | 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal için kullanılabilir komut yok.

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir yük devretme grubuna Azure SQL veritabanı elastik havuzu eklediniz ve yük devretmeyi test edersiniz. Şunları öğrendiniz:

> [!div class="checklist"]
> - Azure SQL veritabanı tek veritabanı oluşturun.
> - Tek veritabanını elastik bir havuza ekleyin. 
> - İki mantıksal SQL Server arasında iki elastik havuz için bir [Yük devretme grubu](sql-database-auto-failover-group.md) oluşturun.
> - Yük devretme testi.

DMS kullanarak geçiş yapmak için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Öğretici: DMS kullanarak SQL Server havuza alınmış bir veritabanına geçirme](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
