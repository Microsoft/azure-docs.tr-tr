---
title: 'PowerShell: SQL Server SQL yönetilen örneğine çevrimiçi geçirme'
titleSuffix: Azure Database Migration Service
description: Azure PowerShell ve Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server Azure SQL yönetilen örneğine çevrimiçi geçiş yapmayı öğrenin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: ''
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 7f09db2e1f98d48e91dfea2642969ff4ca360967
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697832"
---
# <a name="migrate-sql-server-to-sql-managed-instance-online-with-powershell--azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti & PowerShell ile SQL Server SQL yönetilen örneğine çevrimiçi olarak geçirme

Bu makalede, Microsoft Azure PowerShell kullanarak bir Azure SQL SQL yönetilen örneğine SQL Server 2005 veya üzeri bir şirket içi örneğine geri yüklenen **Adventureworks2016** veritabanını çevrimiçi olarak geçirmiş olursunuz. Microsoft Azure PowerShell modülünü kullanarak bir SQL Server örneğinden bir SQL yönetilen örneğine veritabanları geçirebilirsiniz `Az.DataMigration` .

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
>
> * Bir kaynak grubu oluşturun.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti örneğinde bir geçiş projesi oluşturun.
> * Geçişi çevrimiçi olarak çalıştırın.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makale, çevrimiçi geçiş için adımlar sağlar, ancak [çevrimdışı](howto-sql-server-to-azure-sql-managed-instance-powershell-offline.md)geçiş için de mümkündür.


## <a name="prerequisites"></a>Önkoşullar

Bu adımları tamamlayabilmeniz için şunlar gerekir:

* [SQL Server 2016 veya üzeri](https://www.microsoft.com/sql-server/sql-server-downloads) (herhangi bir sürüm).
* [Burada](/sql/samples/adventureworks-install-configure)indirmek üzere kullanılabilen **AdventureWorks2016** veritabanının yerel bir kopyası.
* SQL Server Express yükleme ile varsayılan olarak devre dışı bırakılan TCP/IP protokolünü etkinleştirmek için. [Sunucu ağ protokolünü etkinleştirme veya devre dışı bırakma](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)MAKALESINI izleyerek TCP/IP protokolünü etkinleştirin.
* [Windows Güvenlik duvarınızı veritabanı altyapısı erişimi için](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)yapılandırmak için.
* Azure aboneliği. Bir tane yoksa, başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .
* SQL yönetilen örneği. [BIR ASQL yönetilen örneği oluşturma](../azure-sql/managed-instance/instance-create-quickstart.md)makalesindeki ayrıntıyı IZLEYEREK bir SQL yönetilen örneği oluşturabilirsiniz.
* [Data Migration Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 veya sonraki bir sürümünü indirip yükleyin.
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak Azure veritabanı geçiş hizmeti 'ni şirket içi kaynak sunucularınız için siteden siteye bağlantı ile sağlayan Azure Resource Manager dağıtım modeli kullanılarak oluşturulan Microsoft Azure sanal ağ.
* [SQL Server geçiş değerlendirmesi gerçekleştirme](/sql/dma/dma-assesssqlonprem)makalesinde açıklandığı gibi Data Migration Yardımcısı kullanarak şirket içi veritabanınızın ve şema geçişinin tamamlanmış değerlendirmesi.
* `Az.DataMigration`PowerShell Galerisi modülünü (sürüm 0.7.2 veya üzeri), [Install-Module PowerShell cmdlet 'ini](/powershell/module/powershellget/Install-Module)kullanarak indirip yükleyin.
* Kaynak SQL Server örneğine bağlanmak için kullanılan kimlik bilgilerinin [DENETIM sunucusu](/sql/t-sql/statements/grant-server-permissions-transact-sql) iznine sahip olduğundan emin olmak için.
* Hedef SQL yönetilen örneğine bağlanmak için kullanılan kimlik bilgilerinin hedef SQL yönetilen örnek veritabanlarında DENETIM VERITABANı iznine sahip olduğundan emin olmak için.

    > [!IMPORTANT]
    > Çevrimiçi geçişler için Azure Active Directory kimlik bilgilerinizi ayarlamış olmanız gerekir. Daha fazla bilgi için, [kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md)makalesine bakın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Komutunu kullanarak bir kaynak grubu oluşturun [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) .

Aşağıdaki örnek, *Doğu ABD* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-dms"></a>DMS örneği oluşturma

Cmdlet 'ini kullanarak Azure veritabanı geçiş hizmeti 'nin yeni bir örneğini oluşturabilirsiniz `New-AzDataMigrationService` .
Bu cmdlet aşağıdaki gerekli parametreleri bekliyor:

* *Azure Kaynak grubu adı*. [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup)Komutunu, daha önce gösterildiği gibi bir Azure Kaynak grubu oluşturmak ve adını bir parametre olarak sağlamak için kullanabilirsiniz.
* *Hizmet adı*. Azure veritabanı geçiş hizmeti için istenen benzersiz hizmet adına karşılık gelen dize.
* *Konum*. Hizmetin konumunu belirtir. Batı ABD veya Güneydoğu Asya gibi bir Azure veri merkezi konumu belirtin.
* *SKU 'su*. Bu parametre, DMS SKU adına karşılık gelir. Şu anda desteklenen SKU adları *Basic_1vCore*, *Basic_2vCores* *GeneralPurpose_4vCores*.
* *Sanal alt ağ tanımlayıcısı*. [`New-AzVirtualNetworkSubnetConfig`](/powershell/module/az.network/new-azvirtualnetworksubnetconfig)Bir alt ağ oluşturmak için cmdlet 'ini kullanabilirsiniz.

Aşağıdaki örnek, *Myvnet* adlı bir sanal ağ ve *mysubnet* adlı bir alt ağ kullanarak *Doğu ABD* bölgesinde bulunan *Mydmsresourcegroup* kaynak grubunda *mydms* adlı bir hizmet oluşturur.


```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Azure veritabanı geçiş hizmeti örneği oluşturduktan sonra bir geçiş projesi oluşturun. Azure veritabanı geçiş hizmeti projesi hem kaynak hem de hedef örneklerin yanı sıra projenin bir parçası olarak geçirmek istediğiniz veritabanlarının listesini gerektirir.
Kaynak ve hedef bağlantısı bağlantı dizelerini tanımlayın.

Aşağıdaki betik, kaynak SQL Server bağlantı ayrıntılarını tanımlar: 

```powershell
# Source connection properties
$sourceDataSource = "<mysqlserver.domain.com/privateIP of source SQL>"
$sourceUserName = "domain\user"
$sourcePassword = "mypassword"
```

Aşağıdaki betik, hedef SQL yönetilen örnek bağlantı ayrıntılarını tanımlar: 

```powershell
# Target MI connection properties
$targetMIResourceId = "/subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Sql/managedInstances/<myMI>"
$targetUserName = "<user>"
$targetPassword = "<password>"
```



### <a name="define-source-and-target-database-mapping"></a>Kaynak ve hedef veritabanı eşlemesini tanımlama

Bu geçiş projesinde geçirilecek veritabanlarını sağlayın

Aşağıdaki betik, kaynak veritabanını hedef SQL yönetilen örneği üzerindeki ilgili yeni veritabanıyla birlikte belirtilen adla eşler.

```powershell
# Selected databases (Source database name to target database name mapping)
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name>", "<target database name> ")
```

Birden çok veritabanı için aşağıdaki biçimi kullanarak veritabanlarının listesini yukarıdaki komut dosyasına ekleyin:

```powershell
$selectedDatabasesMap = New-Object System.Collections.Generic.Dictionary"[String,String]" 
$selectedDatabasesMap.Add("<source  database name1>", "<target database name1> ")
$selectedDatabasesMap.Add("<source  database name2>", "<target database name2> ")
```

### <a name="create-dms-project"></a>DMS projesi oluşturma

DMS örneği içinde bir Azure veritabanı geçiş hizmeti projesi oluşturabilirsiniz.

```powershell
# Create DMS project
$project = New-AzDataMigrationProject `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -Location $dmsLocation `
  -SourceType SQL `
  -TargetType SQLMI `

# Create selected databases object
$selectedDatabases = @();
foreach ($sourceDbName in $selectedDatabasesMap.Keys){
    $targetDbName = $($selectedDatabasesMap[$sourceDbName])
    $selectedDatabases += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
      -Name $sourceDbName `
      -TargetDatabaseName $targetDbName `
      -BackupFileShare $backupFileShare `
}
```



### <a name="create-a-backup-fileshare-object"></a>Yedek FileShare nesnesi oluştur

Şimdi, Azure veritabanı geçiş hizmeti 'nin New-AzDmsFileShare cmdlet 'ini kullanarak kaynak veritabanı yedeklemelerini atabileceğiniz yerel SMB ağ paylaşımını temsil eden bir FileShare nesnesi oluşturun.

```powershell
# SMB Backup share properties
$smbBackupSharePath = "\\shareserver.domain.com\mybackup"
$smbBackupShareUserName = "domain\user"
$smbBackupSharePassword = "<password>"

# Create backup file share object
$smbBackupSharePasswordSecure = ConvertTo-SecureString -String $smbBackupSharePassword -AsPlainText -Force
$smbBackupShareCredentials = New-Object System.Management.Automation.PSCredential ($smbBackupShareUserName, $smbBackupSharePasswordSecure)
$backupFileShare = New-AzDmsFileShare -Path $smbBackupSharePath -Credential $smbBackupShareCredentials
```

### <a name="define-the-azure-storage"></a>Azure Storage 'ı tanımlama 

Geçiş için kullanılacak Azure depolama kapsayıcısını seçin: 

```powershell
# Storage resource id
$storageAccountResourceId = "/subscriptions/<subscriptionname>/resourceGroups/<rg>/providers/Microsoft.Storage/storageAccounts/<mystorage>"
```


### <a name="configure-azure-active-directory-app"></a>Azure Active Directory uygulamasını yapılandırma

Çevrimiçi SQL yönetilen örnek geçişi için Azure Active Directory için gerekli ayrıntıları sağlayın: 

```powershell
# AAD properties
$AADAppId = "<appid-guid>"
$AADAppKey = "<app-key>"

# Create AAD object
$AADAppKeySecure = ConvertTo-SecureString $AADAppKey -AsPlainText -Force
$AADApp = New-AzDmsAadApp -ApplicationId $AADAppId -AppKey $AADAppKeySecure
```


## <a name="create-and-start-a-migration-task"></a>Geçiş görevi oluşturma ve başlatma

Ardından, bir Azure veritabanı geçiş hizmeti görevi oluşturun ve başlatın. Değişkenleri kullanarak kaynak ve hedefi çağırın ve geçirilecek veritabanı tablolarını listeleyin: 


```powershell
# Managed Instance online migration properties
$dmsTaskName = "testmigration1"

# Create source connection info
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource $sourceDataSource `
  -AuthType WindowsAuthentication `
  -TrustServerCertificate:$true
$sourcePasswordSecure = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCredentials = New-Object System.Management.Automation.PSCredential ($sourceUserName, $sourcePasswordSecure)

# Create target connection info
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI `
    -MiResourceId $targetMIResourceId
$targetPasswordSecure = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCredentials = New-Object System.Management.Automation.PSCredential ($targetUserName, $targetPasswordSecure)
```

Aşağıdaki örnek bir çevrimiçi geçiş görevi oluşturur ve başlatır: 

```powershell
# Create DMS migration task
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName $dmsResourceGroupName `
  -ServiceName $dmsServiceName `
  -ProjectName $dmsProjectName `
  -TaskName $dmsTaskName `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCredentials `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCredentials `
  -SelectedDatabase  $selectedDatabases `
  -BackupFileShare $backupFileShare `
  -AzureActiveDirectoryApp $AADApp `
  -StorageResourceId $storageAccountResourceId
```

Daha fazla bilgi için bkz. [New-AzDataMigrationTask](/powershell/module/az.datamigration/new-azdatamigrationtask).

## <a name="monitor-the-migration"></a>Geçişi izleme

Geçişi izlemek için aşağıdaki görevleri gerçekleştirin.

### <a name="check-the-status-of-task"></a>Görevin durumunu denetleme

```powershell
# Get migration task status details
$migTask = Get-AzDataMigrationTask `
                    -ResourceGroupName $dmsResourceGroupName `
                    -ServiceName $dmsServiceName `
                    -ProjectName $dmsProjectName `
                    -Name $dmsTaskName `
                    -ResultType DatabaseLevelOutput `
                    -Expand

# Task state will be either of 'Queued', 'Running', 'Succeeded', 'Failed', 'FailedInputValidation' or 'Faulted'
$taskState = $migTask.ProjectTask.Properties.State

# Display task state
$taskState | Format-List
```

Hata listesini almak için aşağıdakileri kullanın:-

```powershell
# Get task errors
$taskErrors = $migTask.ProjectTask.Properties.Errors

# Display task errors
foreach($taskError in $taskErrors){
    $taskError |  Format-List
}


# Get database level details
$databaseLevelOutputs = $migTask.ProjectTask.Properties.Output

# Display database level details
foreach($databaseLevelOutput in $databaseLevelOutputs){

    # This is the source database name.
    $databaseName = $databaseLevelOutput.SourceDatabaseName;

    Write-Host "=========="
    Write-Host "Start migration details for database " $databaseName
    # This is the status for that database - It will be either of:
    # INITIAL, FULL_BACKUP_UPLOADING, FULL_BACKUP_UPLOADED, LOG_FILES_UPLOADING,
    # CUTOVER_IN_PROGRESS, CUTOVER_INITIATED, CUTOVER_COMPLETED, COMPLETED, CANCELLED, FAILED
    $databaseMigrationState = $databaseLevelOutput.MigrationState;

    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo
        
    # Details about last restored backup. This contains file names, LSN, backup date, etc 
    $databaseLastRestoredBackup = $databaseLevelOutput.LastRestoredBackupSetInfo

    # Details about last Currently active/most recent backups. This contains file names, LSN, backup date, etc 
    $databaseActiveBackpusets = $databaseLevelOutput.ActiveBackupSets

    # Display info
    $databaseLevelOutput | Format-List

    Write-Host "Currently active/most recent backupset details:"
    $databaseActiveBackpusets  | select BackupStartDate, BackupFinishedDate, FirstLsn, LastLsn -ExpandProperty ListOfBackupFiles | Format-List

    Write-Host "Last restored backupset details:"
    $databaseLastRestoredBackupFiles  | Format-List

    Write-Host "End migration details for database " $databaseName
    Write-Host "=========="
}
```

## <a name="performing-the-cutover"></a>Tam geçişi gerçekleştiriliyor 

Çevrimiçi bir geçişle, veritabanlarının tam yedeklemesi ve geri yüklenmesi gerçekleştirilir ve sonra iş, BackupFileShare içinde depolanan Işlem günlüklerini geri yüklemeye devam eder.

Azure SQL yönetilen örneğindeki veritabanı en son verilerle güncellendiğinde ve kaynak veritabanıyla eşitleniyorsa cutover gerçekleştirebilirsiniz.

Aşağıdaki örnek cutover\migrationtamamlanacaktır. Kullanıcılar bu komutu kendi takdirine göre çağırır.

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti örneğini silme

Geçiş işlemi tamamlandıktan sonra Azure veritabanı geçiş hizmeti örneğini silebilirsiniz:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>Ek kaynaklar

Diğer geçiş senaryoları (kaynak/hedef çiftleri) hakkında daha fazla bilgi için bkz. Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/).

## <a name="next-steps"></a>Sonraki adımlar

Azure veritabanı geçiş hizmeti nedir [?](./dms-overview.md)makalesinde Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi edinin.