---
title: Veritabanı geçiş hizmeti ve PowerShell ile Azure SQL veritabanı yönetilen örneği 'ne SQL Server geçirin | Microsoft Docs
description: Azure PowerShell kullanarak şirket içi SQL Server Azure SQL VERITABANı yönetilen örneği 'ne geçirmeyi öğrenin.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 426285340a9401aa6c84a7ee07f172eee6791d9e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163956"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>Azure PowerShell kullanarak şirket içi SQL Server Azure SQL veritabanı yönetilen örneği 'ne geçirme
Bu makalede, Microsoft Azure PowerShell kullanarak bir Azure SQL veritabanı yönetilen örneğine SQL Server 2005 veya üzeri bir şirket içi örneğine geri yüklenen **Adventureworks2016** veritabanını geçirmiş olursunuz. Bir şirket içi SQL Server örneğinden veritabanlarını, Microsoft Azure PowerShell `Az.DataMigration` modülünü kullanarak bir Azure SQL veritabanı yönetilen örneğine geçirebilirsiniz.

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
>
> * Bir kaynak grubu oluşturun.
> * Azure veritabanı geçiş hizmeti 'nin bir örneğini oluşturun.
> * Azure veritabanı geçiş hizmeti örneğinde bir geçiş projesi oluşturun.
> * Geçişi çalıştırma.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Bu makale, hem çevrimiçi hem de çevrimdışı geçişleri gerçekleştirme hakkında ayrıntılı bilgi içerir.

## <a name="prerequisites"></a>Önkoşullar

Bu adımları tamamlayabilmeniz için şunlar gerekir:

* [SQL Server 2016 veya üzeri](https://www.microsoft.com/sql-server/sql-server-downloads) (herhangi bir sürüm).
* [Burada](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)indirmek üzere kullanılabilen **AdventureWorks2016** veritabanının yerel bir kopyası.
* SQL Server Express yükleme ile varsayılan olarak devre dışı bırakılan TCP/IP protokolünü etkinleştirmek için. [Sunucu ağ protokolünü etkinleştirme veya devre dışı bırakma](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)MAKALESINI izleyerek TCP/IP protokolünü etkinleştirin.
* [Windows Güvenlik duvarınızı veritabanı altyapısı erişimi için](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)yapılandırmak için.
* Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
* Azure SQL veritabanı yönetilen örneği. Azure SQL [veritabanı yönetilen örneği oluşturma](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)makalesindeki ayrıntıyı IZLEYEREK Azure SQL veritabanı yönetilen örneği oluşturabilirsiniz.
* [Data Migration Yardımcısı](https://www.microsoft.com/download/details.aspx?id=53595) v 3.3 veya sonraki bir sürümünü indirip yükleyin.
* [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) veya VPN kullanarak şirket içi kaynak sunucularınız için siteden siteye bağlantı Ile Azure veritabanı geçiş hizmeti sağlayan Azure Resource Manager dağıtım modeli kullanılarak oluşturulan bir Azure sanal ağı (VNet). [ ](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
* [SQL Server geçiş değerlendirmesi gerçekleştirme](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)makalesinde açıklandığı gibi Data Migration Yardımcısı kullanarak şirket içi veritabanınızın ve şema geçişinin tamamlanmış değerlendirmesi.
* `Az.DataMigration` modülünü (sürüm 0.7.2 veya üzeri), [install-Module PowerShell cmdlet 'ini](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)kullanarak PowerShell Galerisi indirip yükleyin.
* Kaynak SQL Server örneğine bağlanmak için kullanılan kimlik bilgilerinin [DENETIM sunucusu](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) iznine sahip olduğundan emin olmak için.
* Hedef Azure SQL veritabanı yönetilen örneğine bağlanmak için kullanılan kimlik bilgilerinin hedef Azure SQL veritabanı yönetilen örnek veritabanlarında DENETIM VERITABANı iznine sahip olduğundan emin olmak için.

    > [!IMPORTANT]
    > Çevrimiçi geçişler için Azure Active Directory kimlik bilgilerinizi ayarlamış olmanız gerekir. Daha fazla bilgi için, [kaynaklara erişebilen bir Azure AD uygulaması ve hizmet sorumlusu oluşturmak için portalı kullanma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)makalesine bakın.

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure aboneliğinizde oturum açın

PowerShell kullanarak Azure aboneliğinizde oturum açın. Daha fazla bilgi için [Azure PowerShell oturum açma](https://docs.microsoft.com/powershell/azure/authenticate-azureps)makalesine bakın.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure Kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir kaynak grubu oluşturun.

Aşağıdaki örnek, *Doğu ABD* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur.

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti örneği oluşturma

`New-AzDataMigrationService` cmdlet 'ini kullanarak Azure veritabanı geçiş hizmeti 'nin yeni bir örneğini oluşturabilirsiniz.
Bu cmdlet aşağıdaki gerekli parametreleri bekliyor:

* *Azure Kaynak grubu adı*. Daha önce gösterildiği gibi bir Azure Kaynak grubu oluşturmak ve adını bir parametre olarak sağlamak için [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) komutunu kullanabilirsiniz.
* *Hizmet adı*. Azure veritabanı geçiş hizmeti için istenen benzersiz hizmet adına karşılık gelen dize.
* *Konum*. Hizmetin konumunu belirtir. Batı ABD veya Güneydoğu Asya gibi bir Azure veri merkezi konumu belirtin.
* *SKU 'su*. Bu parametre, DMS SKU adına karşılık gelir. Şu anda desteklenen SKU adları *Basic_1vCore*, *Basic_2vCores*, *GeneralPurpose_4vCores*.
* *Sanal alt ağ tanımlayıcısı*. Bir alt ağ oluşturmak için [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) cmdlet 'ini kullanabilirsiniz.

Aşağıdaki örnek, *Myvnet* adlı bir sanal ağ ve *mysubnet*adlı bir alt ağ kullanarak *Doğu ABD* bölgesinde bulunan *Mydmsresourcegroup* kaynak grubunda *mydms* adlı bir hizmet oluşturur.

> [!IMPORTANT]
> Aşağıdaki kod parçacığı, Premium SKU 'ya dayalı bir Azure veritabanı geçiş hizmeti örneği gerektirmeyen çevrimdışı geçiş içindir. Çevrimiçi geçiş için-SKU parametresinin değeri bir Premium SKU içermelidir.

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

Aşağıdaki parametreleri bekleyen `New-AzDmsConnInfo` cmdlet 'ini kullanarak bir veritabanı bağlantı bilgisi nesnesi oluşturabilirsiniz:

* *SunucuTürü*. İstenen veritabanı bağlantısı türü (örneğin, SQL, Oracle veya MySQL). SQL Server ve Azure SQL için SQL kullanın.
* *Veri kaynağı*. Bir SQL Server örneğinin veya Azure SQL veritabanı örneğinin adı veya IP 'si.
* *AuthType*. SqlAuthentication veya WindowsAuthentication olabilen bağlantı için kimlik doğrulaması türü.
* *TrustServerCertificate*. Bu parametre, güveni doğrulamak üzere sertifika zincirini atlayarak kanalın şifrelenip şifrelenmeyeceğini belirten bir değer ayarlar. Değer `$true` veya `$false`olabilir.

Aşağıdaki örnek, SQL kimlik doğrulaması kullanarak *Mysourcessqlserver* adlı bir kaynak SQL Server Için bir bağlantı bilgileri nesnesi oluşturur:

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

Sonraki örnekte, SQL kimlik doğrulaması kullanarak ' targetmanagedinstance.database.windows.net ' adlı bir Azure SQL veritabanı yönetilen örnek sunucusu için bağlantı bilgilerinin oluşturulması gösterilmektedir:

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>Geçiş projesi için veritabanları sağlama

Azure veritabanı geçiş hizmeti projesi 'nin bir parçası olarak veritabanlarını belirten `AzDataMigrationDatabaseInfo` nesnelerinin bir listesini oluşturun ve bu proje oluşturmak için parametre olarak temin edilebilir. `AzDataMigrationDatabaseInfo`oluşturmak için `New-AzDataMigrationDatabaseInfo` cmdlet 'ini kullanabilirsiniz.

Aşağıdaki örnek, **AdventureWorks2016** veritabanı için `AzDataMigrationDatabaseInfo` projesi oluşturur ve proje oluşturmak için parametre olarak sağlanacak listeye ekler.

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>Proje nesnesi oluştur

Son olarak, `New-AzDataMigrationProject` kullanarak *Doğu ABD* bulunan *Mydmsproject* adlı bir Azure veritabanı geçiş hizmeti projesi oluşturabilir ve daha önce oluşturulan kaynak ve hedef bağlantıları ve geçirilecek veritabanlarının listesini ekleyebilirsiniz.

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

Bağlantı güvenliği kimlik bilgilerini [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) nesnesi olarak oluşturun.

Aşağıdaki örnek, hem kaynak hem de hedef bağlantılar için *PSCredential* nesnelerinin oluşturulmasını gösterir ve bu da *$sourcePassword* dize değişkenleri olarak parolalar sağlar *$targetPassword*.

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>Yedek FileShare nesnesi oluştur

Şimdi, Azure veritabanı geçiş hizmeti 'nin `New-AzDmsFileShare` cmdlet 'ini kullanarak kaynak veritabanı yedeklemelerini atabileceğiniz yerel SMB ağ paylaşımını temsil eden bir FileShare nesnesi oluşturun.

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>Seçili veritabanı nesnesi oluştur

Sonraki adım `New-AzDmsSelectedDB` cmdlet 'ini kullanarak kaynak ve hedef veritabanlarını seçkullanmaktır.

Aşağıdaki örnek, SQL Server bir Azure SQL veritabanı yönetilen örneğine tek bir veritabanının geçirilmesi içindir:

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

Bir SQL Server örneğinin tamamı Azure SQL veritabanı yönetilen örneği için bir yükseltme ve kaydırma işlemi gerekiyorsa, kaynaktan tüm veritabanlarını alma döngüsü aşağıda verilmiştir. Aşağıdaki örnekte, $Server, $SourceUserName ve $SourcePassword için kaynak SQL Server ayrıntılarınızı sağlayın.

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

### <a name="additional-configuration-requirements"></a>Ek yapılandırma gereksinimleri

Bilmeniz gereken birkaç ek gereksinim vardır, ancak çevrimdışı veya çevrimiçi geçiş gerçekleştirdiğinize bağlı olarak farklılık gösterir.

#### <a name="offline-migrations"></a>Çevrimdışı geçişler

Yalnızca çevrimdışı geçişler için aşağıdaki ek yapılandırma görevlerini gerçekleştirin.

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

#### <a name="online-migrations"></a>Çevrimiçi geçişler

Yalnızca çevrimiçi geçişler için aşağıdaki ek yapılandırma görevlerini gerçekleştirin.

* **Azure Active Directory uygulamasını yapılandırma**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **Depolama kaynağını yapılandırma**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>Geçiş görevi oluşturma ve başlatma

Bir geçiş görevi oluşturmak ve başlatmak için `New-AzDataMigrationTask` cmdlet 'ini kullanın.

#### <a name="specify-parameters"></a>Parametreleri belirtin

##### <a name="common-parameters"></a>Ortak Parametreler

Çevrimdışı veya çevrimiçi geçiş gerçekleştirmekten bağımsız olarak `New-AzDataMigrationTask` cmdlet 'i aşağıdaki parametreleri bekler:

* *TaskType*. SQL Server Azure SQL veritabanı yönetilen örneği geçiş türü için oluşturulacak geçiş görevinin türü *Migratesqlserversqldbmı* bekleniyor. 
* *Kaynak grubu adı*. Görevin oluşturulacağı Azure Kaynak grubunun adı.
* *ServiceName*. Görevin oluşturulacağı Azure veritabanı geçiş hizmeti örneği.
* *ProjectName*. Görevin oluşturulacağı Azure veritabanı geçiş hizmeti projesinin adı. 
* *Görevadı*. Oluşturulacak görevin adı. 
* *SourceConnection*. Kaynak SQL Server bağlantısını temsil eden Azdmsconnınfo nesnesi.
* *TargetConnection*. Hedef Azure SQL veritabanı yönetilen örnek bağlantısını temsil eden Azdmsconnınfo nesnesi.
* *Sourcecred*. Kaynak sunucuya bağlanmak için [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) nesnesi.
* *Targetkimlik bilgileri*. Hedef sunucuya bağlanmak için [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) nesnesi.
* *Selecteddatabase*. Kaynak ve hedef veritabanı eşlemesini temsil eden AzDataMigrationSelectedDB nesnesi.
* *Backupfileshare*. Azure veritabanı geçiş hizmetinin kaynak veritabanı yedeklemelerini atabileceğiniz yerel ağ paylaşımını temsil eden FileShare nesnesi.
* *Backupblobsasurı*. Azure veritabanı geçiş hizmeti 'ni, hizmetin yedekleme dosyalarını karşıya yükleyen depolama hesabı kapsayıcısına erişim sağlayan SAS URI 'SI. Blob kapsayıcısı için SAS URI 'sini almayı öğrenin.
* *SelectedLogins*. Geçirilecek seçili oturum açma işlemleri listesi.
* *SelectedAgentJobs*. Geçirilecek seçili aracı işlerinin listesi.

##### <a name="additional-parameters"></a>Ek parametreler

`New-AzDataMigrationTask` cmdlet 'i, çevrimdışı veya çevrimiçi olarak geçiş türüne özgü parametreleri de bekliyor.

* **Çevrimdışı geçişler**. Çevrimdışı geçişler için `New-AzDataMigrationTask` cmdlet 'i aşağıdaki parametreleri de bekler:

  * *SelectedLogins*. Geçirilecek seçili oturum açma işlemleri listesi.
  * *SelectedAgentJobs*. Geçirilecek seçili aracı işlerinin listesi.

* **Çevrimiçi geçişler**. Çevrimiçi geçişler için `New-AzDataMigrationTask` cmdlet 'i de aşağıdaki parametreleri bekler.

* *AzureActiveDirectoryApp*. Active Directory uygulama.
* *Storageresourceıd*. Depolama hesabının kaynak KIMLIĞI.

#### <a name="create-and-start-an-offline-migration-task"></a>Çevrimdışı geçiş görevi oluşturma ve başlatma

Aşağıdaki örnek, **myDMSTask**adlı bir çevrimdışı geçiş görevi oluşturur ve başlatır:

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

#### <a name="create-and-start-an-online-migration-task"></a>Çevrimiçi geçiş görevi oluşturma ve başlatma

Aşağıdaki örnek, **myDMSTask**adlı bir çevrimiçi geçiş görevi oluşturur ve başlatır:

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
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
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
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

2. Geçiş görevinin geçerli durumunu almak için `$CheckTask` değişkenini kullanın.

    Geçiş görevinin geçerli durumunu almak üzere `$CheckTask` değişkenini kullanmak için, aşağıdaki örnekte gösterildiği gibi görevin durum özelliğini sorgulayarak çalışan geçiş görevini izleyebilirsiniz:

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

## <a name="performing-the-cutover-online-migrations-only"></a>Tam geçişi gerçekleştirme (yalnızca çevrimiçi geçişler)

Çevrimiçi bir geçişle, veritabanlarının tam yedeklemesi ve geri yüklenmesi gerçekleştirilir ve sonra iş, BackupFileShare içinde depolanan Işlem günlüklerini geri yüklemeye devam eder.

Azure SQL veritabanı yönetilen örneğindeki veritabanı en son verilerle güncelleştirildiği ve kaynak veritabanıyla eşitlenmiş olduğunda, bir cutover gerçekleştirebilirsiniz.

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

Azure veritabanı geçiş hizmeti nedir [?](https://docs.microsoft.com/azure/dms/dms-overview)makalesinde Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi edinin.
