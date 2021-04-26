---
title: 'PowerShell: SQL Server SQL yönetilen örneğine çevrimdışı geçirme'
titleSuffix: Azure Database Migration Service
description: Azure PowerShell ve Azure veritabanı geçiş hizmeti 'ni kullanarak SQL Server 'ten Azure SQL yönetilen örneğine çevrimdışı geçiş yapmayı öğrenin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit, devx-track-azurepowershell
ms.topic: how-to
ms.date: 12/16/2020
ms.openlocfilehash: 90663b6beb4f1e3f7ade32e603a53c8b9d9158f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697856"
---
# <a name="migrate-sql-server-to-sql-managed-instance-offline-with-powershell--azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti & PowerShell ile SQL Server SQL yönetilen örneğine çevrimdışı geçirme

Bu makalede, Microsoft Azure PowerShell kullanarak bir Azure SQL SQL yönetilen örneğine SQL Server 2005 veya üzeri bir şirket içi örneğine geri yüklenen **Adventureworks2016** veritabanını çevrimdışı olarak geçirmiş olursunuz. Microsoft Azure PowerShell modülünü kullanarak bir SQL Server örneğinden bir SQL yönetilen örneğine veritabanları geçirebilirsiniz `Az.DataMigration` .

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
>
> * Bir kaynak grubu oluşturun.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturun.
> * Azure veritabanı geçiş hizmeti örneğinde bir geçiş projesi oluşturun.
> * Geçişi çevrimdışı olarak çalıştırın.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makale, çevrimdışı geçiş için adımlar sağlar, ancak [çevrimiçi](howto-sql-server-to-azure-sql-managed-instance-powershell-online.md)geçiş için de mümkündür.


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


## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure aboneliğinizde oturum açın

PowerShell kullanarak Azure aboneliğinizde oturum açın. Daha fazla bilgi için [Azure PowerShell oturum açma](/powershell/azure/authenticate-azureps)makalesine bakın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Komutunu kullanarak bir kaynak grubu oluşturun [`New-AzResourceGroup`](/powershell/module/az.resources/new-azresourcegroup) .

Aşağıdaki örnek, *Doğu ABD* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Veritabanı Geçiş Hizmeti'nin örneğini oluşturma

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

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Kaynak ve hedef bağlantıları için bir veritabanı bağlantı bilgileri nesnesi oluşturma

Aşağıdaki parametreleri bekleyen cmdlet 'ini kullanarak bir veritabanı bağlantı bilgisi nesnesi oluşturabilirsiniz `New-AzDmsConnInfo` :

* *SunucuTürü*. İstenen veritabanı bağlantısı türü (örneğin, SQL, Oracle veya MySQL). SQL Server ve Azure SQL için SQL kullanın.
* *Veri kaynağı*. Bir SQL Server örneğinin veya Azure SQL veritabanı örneğinin adı veya IP 'si.
* *AuthType*. SqlAuthentication veya WindowsAuthentication olabilen bağlantı için kimlik doğrulaması türü.
* *TrustServerCertificate*. Bu parametre, güveni doğrulamak üzere sertifika zincirini atlayarak kanalın şifrelenip şifrelenmeyeceğini belirten bir değer ayarlar. Değer `$true` veya olabilir `$false` .

Aşağıdaki örnek, SQL kimlik doğrulaması kullanarak *Mysourcessqlserver* adlı bir kaynak SQL Server Için bir bağlantı bilgileri nesnesi oluşturur:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Sonraki örnekte, ' targetmanagedınstance ' adlı bir Azure SQL yönetilen örneği için bağlantı bilgilerinin oluşturulması gösterilmektedir:

```powershell
$targetResourceId = (Get-AzSqlInstance -Name "targetmanagedinstance").Id
$targetConnInfo = New-AzDmsConnInfo -ServerType SQLMI -MiResourceId $targetResourceId
```

### <a name="provide-databases-for-the-migration-project"></a>Geçiş projesi için veritabanları sağlama

`AzDataMigrationDatabaseInfo`Azure veritabanı geçiş hizmeti projesi 'nin bir parçası olarak veritabanlarını belirten nesnelerin bir listesini oluşturun ve bu proje oluşturmak için parametre olarak temin edilebilir. Oluşturmak için cmdlet 'ini kullanabilirsiniz `New-AzDataMigrationDatabaseInfo` `AzDataMigrationDatabaseInfo` .

Aşağıdaki örnek, `AzDataMigrationDatabaseInfo` **AdventureWorks2016** veritabanı için projeyi oluşturur ve proje oluşturma için parametre olarak sağlanacak listeye ekler.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Proje nesnesi oluştur

Son olarak, kullanarak *Doğu ABD* bulunan *Mydmsproject* adlı bir Azure veritabanı geçiş hizmeti projesi oluşturabilir `New-AzDataMigrationProject` ve önceden oluşturulan kaynak ve hedef bağlantıları ve geçirilecek veritabanlarının listesini ekleyebilirsiniz.

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>Geçiş görevi oluşturma ve başlatma

Ardından, bir Azure veritabanı geçiş hizmeti görevi oluşturun ve başlatın. Bu görev hem kaynak hem de hedef için bağlantı kimlik bilgilerinin yanı sıra geçirilecek veritabanı tablolarının listesini ve bir önkoşul olarak oluşturulan projeyle zaten sağlanmış olan bilgileri gerektirir.

### <a name="create-credential-parameters-for-source-and-target"></a>Kaynak ve hedef için kimlik bilgisi parametreleri oluşturma

Bağlantı güvenliği kimlik bilgilerini [PSCredential](/dotnet/api/system.management.automation.pscredential) nesnesi olarak oluşturun.

Aşağıdaki örnek, hem kaynak hem de hedef bağlantılar için *PSCredential* nesnelerinin oluşturulmasını gösterir ve bu da *$sourcePassword* dize değişkenleri olarak parolalar sağlar *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Yedek FileShare nesnesi oluştur

Şimdi, Azure veritabanı geçiş hizmeti 'nin cmdlet 'i kullanarak kaynak veritabanı yedeklemelerini atabileceğiniz yerel SMB ağ paylaşımını temsil eden bir FileShare nesnesi oluşturun `New-AzDmsFileShare` .

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Seçili veritabanı nesnesi oluştur

Sonraki adım cmdlet 'ini kullanarak kaynak ve hedef veritabanlarını seçtir `New-AzDmsSelectedDB` .

Aşağıdaki örnek, SQL Server bir Azure SQL yönetilen örneğine tek bir veritabanının geçirilmesi içindir:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Bir SQL Server örneğinin tamamı Azure SQL yönetilen örneği için bir yükseltme ve kaydırma işlemi gerekiyorsa, kaynaktan tüm veritabanlarını alma döngüsü aşağıda verilmiştir. Aşağıdaki örnekte, $Server, $SourceUserName ve $SourcePassword için kaynak SQL Server ayrıntılarınızı sağlayın.

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure depolama kapsayıcısı için SAS URI 'SI

Azure veritabanı geçiş hizmeti 'ni, hizmetin yedekleme dosyalarını karşıya yükleyen depolama hesabı kapsayıcısına erişimi sağlayan SAS URI 'sini içeren bir değişken oluşturun.

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

> [!NOTE]
> Azure veritabanı geçiş hizmeti, hesap düzeyinde SAS belirteci kullanmayı desteklemez. Depolama hesabı kapsayıcısı için bir SAS URI 'SI kullanmanız gerekir. [Blob kapsayıcısı için SAS URI değerini almayı öğrenin](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

### <a name="additional-configuration-requirements"></a>Ek yapılandırma gereksinimleri

Bilmeniz gereken birkaç ek gereksinim vardır:


* **Oturum açmalar**' ı seçin. Aşağıdaki örnekte gösterildiği gibi geçirilecek bir oturum açma listesi oluşturun:

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > Şu anda Azure veritabanı geçiş hizmeti yalnızca SQL oturum açmaları geçirmeyi destekliyor.

* **Aracı Işlerini seçin**. Aşağıdaki örnekte gösterildiği gibi geçirilecek aracı işlerinin listesini oluşturun:

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > Azure veritabanı geçiş hizmeti şu anda yalnızca T-SQL alt sistemi iş adımları olan işleri destekliyor.



### <a name="create-and-start-the-migration-task"></a>Geçiş görevi oluşturma ve başlatma

`New-AzDataMigrationTask`Bir geçiş görevi oluşturmak ve başlatmak için cmdlet 'ini kullanın.

#### <a name="specify-parameters"></a>Parametreleri belirtin

`New-AzDataMigrationTask`Cmdlet 'i aşağıdaki parametreleri bekler:

* *TaskType*. SQL Server Azure SQL yönetilen örneği geçiş türü için oluşturulacak geçiş görevinin türü *Migratesqlserversqldbmı* bekleniyor. 
* *Kaynak grubu adı*. Görevin oluşturulacağı Azure Kaynak grubunun adı.
* *ServiceName*. Görevin oluşturulacağı Azure veritabanı geçiş hizmeti örneği.
* *ProjectName*. Görevin oluşturulacağı Azure veritabanı geçiş hizmeti projesinin adı. 
* *Görevadı*. Oluşturulacak görevin adı. 
* *SourceConnection*. Kaynak SQL Server bağlantısını temsil eden Azdmsconnınfo nesnesi.
* *TargetConnection*. Hedef Azure SQL yönetilen örnek bağlantısını temsil eden Azdmsconnınfo nesnesi.
* *Sourcecred*. Kaynak sunucuya bağlanmak için [PSCredential](/dotnet/api/system.management.automation.pscredential) nesnesi.
* *Targetkimlik bilgileri*. Hedef sunucuya bağlanmak için [PSCredential](/dotnet/api/system.management.automation.pscredential) nesnesi.
* *Selecteddatabase*. Kaynak ve hedef veritabanı eşlemesini temsil eden AzDataMigrationSelectedDB nesnesi.
* *Backupfileshare*. Azure veritabanı geçiş hizmetinin kaynak veritabanı yedeklemelerini atabileceğiniz yerel ağ paylaşımını temsil eden FileShare nesnesi.
* *Backupblobsasurı*. Azure veritabanı geçiş hizmeti 'ni, hizmetin yedekleme dosyalarını karşıya yükleyen depolama hesabı kapsayıcısına erişim sağlayan SAS URI 'SI. Blob kapsayıcısı için SAS URI değerini almayı öğrenin.
* *SelectedLogins*. Geçirilecek seçili oturum açma işlemleri listesi.
* *SelectedAgentJobs*. Geçirilecek seçili aracı işlerinin listesi.
* *SelectedLogins*. Geçirilecek seçili oturum açma işlemleri listesi.
* *SelectedAgentJobs*. Geçirilecek seçili aracı işlerinin listesi.



#### <a name="create-and-start-a-migration-task"></a>Geçiş görevi oluşturma ve başlatma

Aşağıdaki örnek, **myDMSTask** adlı bir çevrimdışı geçiş görevi oluşturur ve başlatır:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```


## <a name="monitor-the-migration"></a>Geçişi izleme

Geçişi izlemek için aşağıdaki görevleri gerçekleştirin.

1. Tüm geçiş ayrıntılarını $CheckTask adlı bir değişkende birleştirin.

    Geçişle ilişkili özellikler, durum ve veritabanı bilgileri gibi geçiş ayrıntılarını birleştirmek için aşağıdaki kod parçacığını kullanın:

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. `$CheckTask`Geçiş görevinin geçerli durumunu almak için değişkenini kullanın.

    `$CheckTask`Geçiş görevinin geçerli durumunu almak üzere değişkenini kullanmak için, aşağıdaki örnekte gösterildiği gibi görevin durum özelliğini sorgulayarak çalışan geçiş görevini izleyebilirsiniz:

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```


## <a name="delete-the-instance-of-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti örneğini silme

Geçiş işlemi tamamlandıktan sonra Azure veritabanı geçiş hizmeti örneğini silebilirsiniz:

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```


## <a name="next-steps"></a>Sonraki adımlar

Azure veritabanı geçiş hizmeti nedir [?](./dms-overview.md)makalesinde Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi edinin.

Diğer geçiş senaryoları (kaynak/hedef çiftleri) hakkında daha fazla bilgi için bkz. Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/).