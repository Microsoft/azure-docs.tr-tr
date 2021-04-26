---
title: "PowerShell: DMS kullanarak MySQL veritabanından MySQL için Azure veritabanı 'na çevrimdışı geçiş çalıştırma"
titleSuffix: Azure Database Migration Service
description: PowerShell betiği aracılığıyla Azure veritabanı geçiş hizmeti 'ni kullanarak şirket içi bir MySQL veritabanını MySQL için Azure veritabanı 'na geçirmeyi öğrenin.
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: 424443288dddcb09d15b7d00ffe9a2840f602959
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819783"
---
# <a name="migrate-mysql-to-azure-database-for-mysql-offline-with-powershell--azure-database-migration-service"></a>Azure veritabanı geçiş hizmeti & PowerShell ile MySQL için MySQL için Azure veritabanı 'na çevrimiçi geçiş yapma

Bu makalede, Microsoft Azure PowerShell aracılığıyla Azure veritabanı geçiş hizmeti 'nin çevrimdışı geçiş özelliğini kullanarak, şirket içi bir örneğe geri yüklenen bir MySQL veritabanını MySQL için Azure veritabanı 'na geçireceğiniz. Makale, MySQL veritabanının Azure 'a çevrimdışı geçişini gerçekleştirmek için sırayla yürütülebilecek bir PowerShell betikleri koleksiyonu belgeler.

> [!NOTE]
> Şu anda az. DataMigration modülünü kullanarak tüm veritabanı geçişini çalıştırmak mümkün değildir. Bu sırada, örnek PowerShell betiği, [DMS REST API](https://docs.microsoft.com/rest/api/datamigration/tasks/get) 'sini kullanan ve geçişi otomatikleştirmenizi sağlayan "olduğu gibi" sağlanır. Bu betik, az. DataMigration modülüne ve Azure CLı 'ye resmi destek eklendikten sonra değiştirilecek veya kullanım dışı bırakılacak. 

> [!IMPORTANT]
> "MySQL için Azure veritabanı" çevrimiçi geçiş senaryosu, 1 Haziran 2021 ' den paralellik, yüksek performanslı bir çevrimdışı geçiş senaryosu ile değiştiriliyor. Çevrimiçi geçişler için, bu yeni teklifi, [veri ile çoğaltma](https://docs.microsoft.com/azure/mysql/concepts-data-in-replication)ile birlikte kullanabilirsiniz. Alternatif olarak, çevrimiçi geçişler için veri-çoğaltma ile [Mydumper/MyLoader](https://centminmod.com/mydumper.html) gibi açık kaynaklı araçları kullanın. 

Bu makale, kaynak ve hedef veritabanı adlarının aynı veya farklı olduğu ve geçişin bir parçası olarak hedef veritabanındaki tabloların tümünün veya birkaçının aynı ada ve tablo yapısına sahip geçirilmesi gereken bir senaryoyu otomatikleştirmenize yardımcı olur. Makaleler kaynağı bir MySQL veritabanı örneği ve hedefi MySQL için Azure veritabanı olacak şekilde varsaysa da, kaynak sunucu adını ve kimlik bilgilerini değiştirerek MySQL için bir Azure veritabanından diğerine geçiş yapmak için kullanılabilir. Ayrıca, daha düşük sürüm MySQL sunucularından (v 5.6 ve üzeri) daha yüksek sürümlere geçiş de desteklenir.

[!INCLUDE [preview features callout](../../includes/dms-boilerplate-preview.md)]

Bu makalede şunları öğreneceksiniz:
> [!div class="checklist"]
>
> * Veritabanı şemasını geçir.
> * Bir kaynak grubu oluşturun.
> * Azure Veritabanı Geçiş Hizmeti örneği oluşturma.
> * Azure veritabanı geçiş hizmeti örneğinde bir geçiş projesi oluşturun.
> * Geçiş projesini MySQL için çevrimdışı geçiş özelliğini kullanacak şekilde yapılandırın.
> * Geçişi çalıştırma.

## <a name="prerequisites"></a>Önkoşullar

Bu adımları tamamlayabilmeniz için şunlar gerekir:

* Etkin aboneliği olan bir Azure hesabına sahip olmanız gerekir. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free).
* Sürüm 5,6 veya üzerini içeren bir şirket içi MySQL veritabanına sahip olmanız gerekir. Aksi takdirde, [MySQL Community edition](https://dev.mysql.com/downloads/mysql/) 5,6 veya üstünü indirip yükleyin.
* [MySQL için Azure Veritabanı örneği oluşturma](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Çalışma ekranı uygulamasını kullanarak bir veritabanını bağlama ve oluşturma hakkındaki ayrıntıları öğrenmek için [MySQL çalışma ekranı kullanma makalesine başvurun ve verileri sorgulayın](../mysql/connect-workbench.md) . MySQL için Azure veritabanı sürümü, şirket içi MySQL sürümüne eşit veya ondan daha yüksek olmalıdır. Örneğin, MySQL 5,7, MySQL için Azure veritabanı 5,7 ' e geçirebilir veya 8 ' e yükseltilir.  
* [ExpressRoute](../expressroute/expressroute-introduction.md) veya [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)kullanarak şirket içi kaynak sunucularınıza siteden siteye bağlantı sağlayan Azure Resource Manager dağıtım modelini kullanarak Azure veritabanı geçiş hizmeti için bir Microsoft Azure sanal ağ oluşturun. Sanal ağ oluşturma hakkında daha fazla bilgi için [sanal ağ belgelerine](../virtual-network/index.yml)ve özellikle adım adım ayrıntılarla birlikte hızlı başlangıç makalelerine bakın.

    > [!NOTE]
    > Sanal networkNet kurulumu sırasında, ExpressRoute 'u Microsoft 'a ağ eşlemesi ile kullanıyorsanız, *Microsoft. SQL* hizmet [uç noktasını](../virtual-network/virtual-network-service-endpoints-overview.md) hizmetin sağlanacağı alt ağa ekleyin. Azure veritabanı geçiş hizmeti internet bağlantısı olmadığından bu yapılandırma gereklidir.

* Sanal ağ ağ güvenlik grubu kurallarınızın, depolama ve AzureMonitor için ServiceTag giden bağlantı noktası 443 ' i engellemediğinden emin olun. Sanal ağ NSG trafik filtrelemesi hakkında daha fazla bilgi için ağ [güvenlik grupları ile ağ trafiğini filtreleme](../virtual-network/virtual-network-vnet-plan-design-arm.md)makalesine bakın.
* Azure veritabanı geçiş hizmeti için sanal ağ bağlantılarının kaynak MySQL sunucusuna erişmesine izin vermek için Windows Güvenlik duvarınızı açın, varsayılan olarak TCP bağlantı noktası 3306 ' dir.
* Kaynak veritabanınızın önünde bir güvenlik duvarı gereci kullanırken, Azure veritabanı geçiş hizmeti için sanal ağdan gelen bağlantıların geçiş için kaynak veritabanına erişmesine izin vermek üzere güvenlik duvarı kuralları eklemeniz gerekebilir.
* Hedef veritabanlarına Azure veritabanı geçiş hizmeti erişimi için sanal ağa izin vermek amacıyla, sunucu düzeyinde bir [güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) oluşturun veya MySQL Için hedef Azure VERITABANı için [VNET hizmet uç noktalarını yapılandırın](../mysql/howto-manage-vnet-using-portal.md) .
* Kaynak MySQL'in desteklenen MySQL Community Edition sürümünde olması gerekir. MySQL örneğinin sürümünü belirlemek için, MySQL yardımcı programında veya MySQL Workbench'te şu komutu çalıştırın:

    ```
    SELECT @@version;
    ```

* MySQL için Azure Veritabanı yalnızca InnoDB tablolarını destekler. MyISAM tablolarını InnoDB'ye dönüştürmek için [Converting Tables from MyISAM to InnoDB (Tabloları MyISAM'dan InnoDB'ye Dönüştürme)](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) başlıklı makaleye bakın
* Kullanıcının, kaynak veritabanındaki verileri okuma ayrıcalıklarına sahip olması gerekir.
* Kılavuz, [Yükleme Kılavuzu 'na](/powershell/scripting/install/installing-powershell?view=powershell-7.1&preserve-view=true) göre yüklenebilen, PSEdition Core ile PowerShell v 7.1 kullanır
* Aşağıdaki modülleri [Install-Module PowerShell cmdlet 'ini](/powershell/module/powershellget/Install-Module)kullanarak PowerShell Galerisi indirip yükleyin; Yönetici olarak çalıştır 'ı kullanarak PowerShell komut penceresini açmayı unutmayın:
    * Az.Resources
    * Az.Network
    * Az. DataMigration

```powershell
Install-Module Az.Resources
Install-Module Az.Network
Install-Module Az.DataMigration
Import-Module Az.Resources
Import-Module Az.Network
Import-Module Az.DataMigration
```

## <a name="migrate-database-schema"></a>Veritabanı şemasını geçir

Tablo şemaları, dizinler ve saklı yordamlar gibi tüm veritabanı nesnelerini aktarmak için, kaynak veritabanından şemayı ayıklamız ve hedef veritabanına uygulamanız gerekir. Şemayı ayıklamak için `--no-data` parametresiyle mysqldump yardımcı programını kullanabilirsiniz. Bunun için hem kaynak MySQL veritabanına hem de MySQL için Azure veritabanı 'na bağlanabilecek bir makineye ihtiyacınız vardır.

Şemayı mysqldump kullanarak dışarı aktarmak için aşağıdaki komutu çalıştırın:

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

Örnek:

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

Şemayı, MySQL için Azure veritabanı hedef olarak içeri aktarmak için aşağıdaki komutu çalıştırın:

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

Örnek:

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

Şemanızda yabancı anahtarlarınız varsa, geçiş sırasında paralel veri yükü geçiş görevi tarafından işlenir. Şema geçişi sırasında yabancı anahtarları bırakmaya gerek yoktur.

Veritabanında Tetikleyiciniz varsa, kaynaktan tam veri geçişinin önüne veri bütünlüğünü zorlayacaktır. Bu öneri, geçiş sırasında hedefteki tüm tablolardaki Tetikleyicileri devre dışı bırakıp, geçiş yapıldıktan sonra Tetikleyicileri etkinleştirir.

Bırakma tetikleyicisi betiğini ayıklamak ve tetikleyici betiği eklemek için hedef veritabanındaki MySQL çalışma sayfasında aşağıdaki betiği yürütün.

```sql
SELECT
    SchemaName,
    GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery,
    Concat('DELIMITER $$ \n\n', GROUP_CONCAT(AddQuery SEPARATOR '$$\n'), '$$\n\nDELIMITER ;') as AddQuery
FROM
(
SELECT 
    TRIGGER_SCHEMA as SchemaName,
    Concat('DROP TRIGGER `', TRIGGER_NAME, "`") as DropQuery,
    Concat('CREATE TRIGGER `', TRIGGER_NAME, '` ', ACTION_TIMING, ' ', EVENT_MANIPULATION, 
            '\nON `', EVENT_OBJECT_TABLE, '`\n' , 'FOR EACH ', ACTION_ORIENTATION, ' ',
            ACTION_STATEMENT) as AddQuery
FROM  
    INFORMATION_SCHEMA.TRIGGERS
ORDER BY EVENT_OBJECT_SCHEMA, EVENT_OBJECT_TABLE, ACTION_TIMING, EVENT_MANIPULATION, ACTION_ORDER ASC
) AS Queries
GROUP BY SchemaName
```

Tetikleyicileri hedef veritabanında bırakmak için sonuç içinde oluşturulan bırakma tetikleyicisi sorgusunu (DropQuery sütunu) çalıştırın. Ekleme tetikleyicisi sorgusu, veri geçişi sonrası işleminin tamamlanmasını kullanılmak üzere kaydedilebilir.

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Microsoft Azure aboneliğinizde oturum açın

[Azure PowerShell Ile oturum](/powershell/azure/authenticate-azureps)açma makalesindeki yönergelere göre, PowerShell kullanarak Azure aboneliğinizde oturum açmak için [Connect-azaccount PowerShell komutunu](/powershell/module/az.accounts/connect-azaccount) kullanın.

Aşağıdaki betik, PowerShell oturumu gönderi için varsayılan aboneliği ayarlar ve biçimlendirilen konsol günlükleri için bir yardımcı günlük işlevi oluşturur.

```powershell
[string] $SubscriptionName = "mySubscription"
$ErrorActionPreference = "Stop";

Connect-AzAccount
Set-AzContext -Subscription $SubscriptionName
$global:currentSubscriptionId = (Get-AzContext).Subscription.Id;

function LogMessage([string] $Message, [bool] $IsProcessing = $false) {
    if ($IsProcessing) {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Yellow
    }
    else {
        Write-Host "$(Get-Date -Format "yyyy-MM-dd HH:mm:ss"): $Message" -ForegroundColor Green
    }    
}
```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration kaynak sağlayıcısını kaydetme

Kaynak sağlayıcısı kaydının her bir Azure aboneliğinde yalnızca bir kez yapılması gerekir. Kayıt olmadan, **Azure veritabanı geçiş hizmeti**'nin bir örneğini oluşturabileceksiniz.

[Kayıt-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) komutunu kullanarak kaynak sağlayıcısını kaydedin. Aşağıdaki betik, **Azure veritabanı geçiş hizmeti** için gereken kaynak sağlayıcısını kaydeder

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataMigration
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Herhangi bir DMS kaynağı oluşturmadan önce bir kaynak grubu oluşturun.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir kaynak grubu oluşturun.

Aşağıdaki örnek, *Myresourcegroup* adlı varsayılan abonelik aboneliğindeki *Batı ABD 2* bölgesinde *myresourcegroup* adlı bir kaynak grubu oluşturur.

```powershell
# Get the details of resource group
[string] $Location = "westus2"
[string] $ResourceGroupName = "myResourceGroup"

$resourceGroup = Get-AzResourceGroup -Name $ResourceGroupName
if (-not($resourceGroup)) {
    LogMessage -Message "Creating resource group $ResourceGroupName..." -IsProcessing $true
    $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
    LogMessage -Message "Created resource group - $($resourceGroup.ResourceId)."
}
else { LogMessage -Message "Resource group $ResourceGroupName exists." }
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>Azure Veritabanı Geçiş Hizmeti'nin örneğini oluşturma

[New-AzDataMigrationService](/powershell/module/az.datamigration/new-azdatamigrationservice) komutunu kullanarak Azure veritabanı geçiş hizmeti 'nin yeni bir örneğini oluşturabilirsiniz. Bu komut aşağıdaki gerekli parametreleri bekler:
* *Azure Kaynak grubu adı*. [Yeni-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu, daha önce gösterildiği gibi Azure Kaynak grubu oluşturmak ve adını bir parametre olarak sağlamak için kullanabilirsiniz.
* *Hizmet adı*. Azure veritabanı geçiş hizmeti için istenen benzersiz hizmet adına karşılık gelen dize 
* *Konum*. Hizmetin konumunu belirtir. Batı ABD veya Güneydoğu Asya gibi bir Azure veri merkezi konumu belirtin
* *SKU 'su*. Bu parametre, DMS SKU adına karşılık gelir. Şu anda desteklenen SKU adı *Standard_1vCore*, *Standard_2vCores*, *Standard_4vCores* *Premium_4vCores*.
* *Sanal alt ağ tanımlayıcısı*. Bir alt ağın bilgilerini almak için [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) komutunu kullanabilirsiniz. 

Aşağıdaki betik, *myVirtualNetwork* sanal ağının *varsayılan* adlı bir alt ağda var olduğunu ve sonra **Adım 3** ' te ve aynı bölgede oluşturulan kaynak grubu altında *mydmservice* adlı bir veritabanı geçiş hizmeti oluşturduğunu bekliyor.

```powershell
# Get a reference to the DMS service - Create if not exists
[string] $VirtualNetworkName = "myVirtualNetwork"
[string] $SubnetName = "default"
[string] $ServiceName = "myDmService"

$dmsServiceResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$ResourceGroupName/providers/Microsoft.DataMigration/services/$ServiceName"
$dmsService = Get-AzResource -ResourceId $dmsServiceResourceId -ErrorAction SilentlyContinue

# Create Azure DMS service if not existing
# Possible values for SKU currently are Standard_1vCore,Standard_2vCores,Standard_4vCores,Premium_4vCores
if (-not($dmsService)) {   
    $virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Name $VirtualNetworkName
    if (-not ($virtualNetwork)) { throw "ERROR: Virtual Network $VirtualNetworkName does not exists" }

    $subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $virtualNetwork -Name $SubnetName
    if (-not ($subnet)) { throw "ERROR: Virtual Network $VirtualNetworkName does not contains Subnet $SubnetName" }

    LogMessage -Message "Creating Azure Data Migration Service $ServiceName..." -IsProcessing $true
    $dmsService = New-AzDataMigrationService `
        -ResourceGroupName $ResourceGroupName `
        -Name $ServiceName `
        -Location $resourceGroup.Location `
        -Sku Premium_4vCores `
        -VirtualSubnetId $Subnet.Id
    
    $dmsService = Get-AzResource -ResourceId $dmsServiceResourceId
    LogMessage -Message "Created Azure Data Migration Service - $($dmsService.ResourceId)."
}
else { LogMessage -Message "Azure Data Migration Service $ServiceName exists." }
```

## <a name="create-a-migration-project"></a>Geçiş projesi oluşturma

Azure veritabanı geçiş hizmeti örneği oluşturduktan sonra bir geçiş projesi oluşturacaksınız. Bir geçiş projesi, gerçekleştirilmesi gereken geçişin türünü belirtir.

Aşağıdaki betik, **Adım 4** ' te ve aynı bölgede oluşturulan veritabanı geçiş hizmeti örneği altında MySQL Için Azure veritabanı 'na *myfirstmysqlofflineproject* adlı bir geçiş projesi oluşturur.

```powershell
# Get a reference to the DMS project - Create if not exists
[string] $ProjectName = "myfirstmysqlofflineproject"

$dmsProjectResourceId = "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($dmsService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($dmsService.Name)/projects/$projectName"
$dmsProject = Get-AzResource -ResourceId $dmsProjectResourceId -ErrorAction SilentlyContinue

# Create Azure DMS Project if not existing
if (-not($dmsProject)) {
    LogMessage -Message "Creating Azure DMS project $projectName for MySQL migration ..." -IsProcessing $true

    $newProjectProperties = @{"sourcePlatform" = "MySQL"; "targetPlatform" = "AzureDbForMySQL" }
    $dmsProject = New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $dmsService.Location `
        -ResourceId $dmsProjectResourceId `
        -Properties $newProjectProperties `
        -Force

    LogMessage -Message "Created Azure DMS project $projectName - $($dmsProject.ResourceId)."
}
else { LogMessage -Message "Azure DMS project $projectName exists." }
```

## <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>Kaynak ve hedef bağlantıları için bir veritabanı bağlantı bilgileri nesnesi oluşturma

Geçiş projesini oluşturduktan sonra, veritabanı bağlantı bilgilerini oluşturacaksınız. Bu bağlantı bilgileri, geçiş işlemi sırasında kaynak ve hedef sunuculara bağlanmak için kullanılacaktır.

Aşağıdaki betik, kaynak ve hedef MySQL örnekleri için sunucu adını, Kullanıcı adını ve parolayı alır ve bağlantı bilgisi nesnelerini oluşturur. Betik, kullanıcıdan kaynak ve hedef MySQL örnekleri için parola girmesini ister. Sessiz betikler için Azure Key Vault, kimlik bilgileri getirilebilir. 

```powershell
# Initialize the source and target database server connections
[string] $SourceServerName = "13.66.136.192"
[string] $SourceUserName = "docadmin@mysqlserver"
[securestring] $SourcePassword = Read-Host "Enter MySQL Source Server Password" -AsSecureString

[string] $TargetServerName = "migdocdevwus2mysqlsstrgt.mysql.database.azure.com"
[string] $TargetUserName = "docadmin@migdocdevwus2mysqlsstrgt"
[securestring] $TargetPassword = Read-Host "Enter MySQL Target Server Password" -AsSecureString

function InitConnection(
    [string] $ServerName,
    [string] $UserName,
    [securestring] $Password) {
    $connectionInfo = @{
        "dataSource"             = "";
        "serverName"             = "";
        "port"                   = 3306;
        "userName"               = "";
        "password"               = "";
        "authentication"         = "SqlAuthentication";
        "encryptConnection"      = $true;
        "trustServerCertificate" = $true;
        "additionalSettings"     = "";
        "type"                   = "MySqlConnectionInfo" 
    }

    $connectionInfo.dataSource = $ServerName;
    $connectionInfo.serverName = $ServerName;
    $connectionInfo.userName = $UserName;
    $connectionInfo.password = (ConvertFrom-SecureString -AsPlainText $password).ToString();
    $connectionInfo;
}

# Initialize the source and target connections
LogMessage -Message "Initializing source and target connection objects ..." -IsProcessing $true
$sourceConnInfo = InitConnection `
    $SourceServerName `
    $SourceUserName `
    $SourcePassword;

$targetConnInfo = InitConnection `
    $TargetServerName `
    $TargetUserName `
    $TargetPassword;

LogMessage -Message "Source and target connection object initialization complete."
```

## <a name="extract-the-list-of-table-names-from-the-target-database"></a>Hedef veritabanından tablo adları listesini Ayıkla

Veritabanı tablosu listesi, bir geçiş görevi ve bağlantı bilgileri kullanılarak ayıklanamaz. Tablo listesi, doğru eşleme ve doğrulamanın yapılabilmesi için hem kaynak veritabanından hem de hedef veritabanından ayıklanacaktır. 

Aşağıdaki betik, kaynak ve hedef veritabanlarının adlarını alır ve ardından *Getusertablesmyısql* geçiş görevini kullanarak tablo listesini veritabanlarından ayıklar. 

```powershell
# Run scenario to get the tables from the target database to build
# the migration table mapping
[string] $TargetDatabaseName = "migtargetdb"
[string] $SourceDatabaseName = "migsourcedb"

function RunScenario([object] $MigrationService, 
    [object] $MigrationProject, 
    [string] $ScenarioTaskName, 
    [object] $TaskProperties, 
    [bool] $WaitForScenario = $true) {
    # Check if the scenario task already exists, if so remove it
    LogMessage -Message "Removing scenario if already exists..." -IsProcessing $true
    Remove-AzDataMigrationTask `
        -ResourceGroupName $MigrationService.ResourceGroupName `
        -ServiceName $MigrationService.Name `
        -ProjectName $MigrationProject.Name `
        -TaskName $ScenarioTaskName `
        -Force;

    # Start the new scenario task using the provided properties
    LogMessage -Message "Initializing scenario..." -IsProcessing $true
    New-AzResource `
        -ApiVersion 2018-03-31-preview `
        -Location $MigrationService.Location `
        -ResourceId "/subscriptions/$($global:currentSubscriptionId)/resourceGroups/$($MigrationService.ResourceGroupName)/providers/Microsoft.DataMigration/services/$($MigrationService.Name)/projects/$($MigrationProject.Name)/tasks/$($ScenarioTaskName)" `
        -Properties $TaskProperties `
        -Force | Out-Null;
    
    LogMessage -Message "Waiting for $ScenarioTaskName scenario to complete..." -IsProcessing $true
    if ($WaitForScenario) {
        $progressCounter = 0;
        do {
            if ($null -ne $scenarioTask) {
                Start-Sleep 10;
            }

            # Get calls can time out and will return a cancellation exception in that case
            $scenarioTask = Get-AzDataMigrationTask `
                -ResourceGroupName $MigrationService.ResourceGroupName `
                -ServiceName $MigrationService.Name `
                -ProjectName $MigrationProject.Name `
                -TaskName $ScenarioTaskName `
                -Expand `
                -ErrorAction Ignore;

            Write-Progress -Activity "Scenario Run $ScenarioTaskName  (Marquee Progress Bar)" `
                -Status $scenarioTask.ProjectTask.Properties.State `
                -PercentComplete $progressCounter
            
            $progressCounter += 10;
            if ($progressCounter -gt 100) { $progressCounter = 10 }
        }
        while (($null -eq $scenarioTask) -or ($scenarioTask.ProjectTask.Properties.State -eq "Running") -or ($scenarioTask.ProjectTask.Properties.State -eq "Queued"))
    }
    Write-Progress -Activity "Scenario Run $ScenarioTaskName" `
        -Status $scenarioTask.ProjectTask.Properties.State `
        -Completed
                 
    # Now get it using REST APIs so we can expand the output  
    LogMessage -Message "Getting expanded task results ..." -IsProcessing $true  
    $psToken = (Get-AzAccessToken -ResourceUrl https://management.azure.com).Token;
    $token = ConvertTo-SecureString -String $psToken -AsPlainText -Force;
    $taskResource = Invoke-RestMethod `
        -Method GET `
        -Uri "https://management.azure.com$($scenarioTask.ProjectTask.Id)?api-version=2018-03-31-preview&`$expand=output" `
        -ContentType "application/json" `
        -Authentication Bearer `
        -Token $token;
    
    $taskResource.properties;
}

# create the get table task properties by initializing the connection and 
# database name
$getTablesTaskProperties = @{
    "input"    = @{
        "connectionInfo"    = $null;
        "selectedDatabases" = $null;
    };
    "taskType" = "GetUserTablesMySql";
};

LogMessage -Message "Running scenario to get the list of tables from the target database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $targetConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($TargetDatabaseName);
# Create a name for the task
$getTableTaskName = "$($TargetDatabaseName)GetUserTables"
# Get the list of tables from the source
$getTargetTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getTargetTablesTask)) { throw "ERROR: Could not get target database $TargetDatabaseName table information." }
LogMessage -Message "List of tables from the target database acquired."

LogMessage -Message "Running scenario to get the list of tables from the source database..." -IsProcessing $true
$getTablesTaskProperties.input.connectionInfo = $sourceConnInfo;
$getTablesTaskProperties.input.selectedDatabases = @($SourceDatabaseName);
# Create a name for the task
$getTableTaskName = "$($SourceDatabaseName)GetUserTables"
# Get the list of tables from the source
$getSourceTablesTask = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $getTableTaskName `
    -TaskProperties $getTablesTaskProperties;

if (-not ($getSourceTablesTask)) { throw "ERROR: Could not get source database $SourceDatabaseName table information." }
LogMessage -Message "List of tables from the source database acquired."

```

## <a name="build-table-mapping-based-on-user-configuration"></a>Kullanıcı yapılandırmasına göre tablo eşlemesi oluştur

Geçiş görevini yapılandırmanın bir parçası olarak, kaynak ve hedef tablolar arasında bir eşleme oluşturacaksınız. Eşleme, tablo adı düzeyindedir, ancak eşlenen tabloların tablo yapısının (sütun sayısı, sütun adları, veri türleri vb.) tamamen aynı olduğu varsayılır.

Aşağıdaki betik, **adım 7**' de ayıklanan hedef ve kaynak tablo listesini temel alan bir eşleme oluşturur. Kısmi veri yükü için Kullanıcı, tabloların filtreleneceği Tablo listesini sağlayabilir. Kullanıcı girişi sağlanmazsa, tüm hedef tablolar eşleştirilir. Bu komut dosyası, kaynakta aynı ada sahip bir tablonun mevcut olup olmadığını da denetler. Tablo adı kaynakta yoksa, geçiş için hedef tablo yok sayılır. 

```powershell
# Create the source to target table map
# Optional table settings
# DEFAULT: $IncludeTables = $null => include all tables for migration
# DEFAULT: $ExcludeTables = $null => exclude no tables from migration
# Exclude list has higher priority than include list
# Array of qualified source table names which should be migrated
[string[]] $IncludeTables = @("migsourcedb.coupons", "migsourcedb.daily_cash_sheets");
[string[]] $ExcludeTables = $null;

LogMessage -Message "Creating the table map based on the user input and database table information ..." `
    -IsProcessing $true

$targetTables = $getTargetTablesTask.Output.DatabasesToTables."$TargetDatabaseName";
$sourceTables = $getSourceTablesTask.Output.DatabasesToTables."$SourceDatabaseName";
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]';

$schemaPrefixLength = $($SourceDatabaseName + ".").Length;
$tableMappingError = $false
foreach ($srcTable in $sourceTables) {
    # Removing the database name prefix from the table name so that comparison
    # can be done in cases where database name given are different
    $tableName = $srcTable.Name.Substring($schemaPrefixLength, `
            $srcTable.Name.Length - $schemaPrefixLength)

    # In case the table is part of exclusion list then ignore the table
    if ($null -ne $ExcludeTables -and $ExcludeTables -contains $srcTable.Name) {
        continue;
    }

    # Either the include list is null or the table is part of the include list then add it in the mapping
    if ($null -eq $IncludeTables -or $IncludeTables -contains $srcTable.Name) {
        # Check if the table exists in the target. If not then log TABLE MAPPING ERROR
        if (-not ($targetTables | Where-Object { $_.name -ieq "$($TargetDatabaseName).$tableName" })) {
            $tableMappingError = $true
            Write-Host "TABLE MAPPING ERROR: $($targetTables.name) does not exists in target." -ForegroundColor Red
            continue;
        }  

        $tableMap.Add("$($SourceDatabaseName).$tableName", "$($TargetDatabaseName).$tableName");
    }     
}

# In case of any table mapping errors identified, throw an error and stop the process
if ($tableMappingError) { throw "ERROR: One or more table mapping errors were identified. Please see previous messages." }
# In case no tables are in the mapping then throw error
if ($tableMap.Count -le 0) { throw "ERROR: Could not create table mapping." }
LogMessage -Message "Migration table mapping created for $($tableMap.Count) tables."
```

## <a name="create-and-configure-the-migration-task-inputs"></a>Geçiş görevi girdilerini oluşturma ve yapılandırma

Tablo eşlemesini oluşturduktan sonra, *migrate. MySQL. AzureDbForMySql* türünde geçiş görevi için girişler oluşturacak ve özellikleri yapılandıracaksınız.

Aşağıdaki betik geçiş görevini oluşturur ve bağlantıları, veritabanı adlarını ve tablo eşlemesini ayarlar.

```powershell
# Create and configure the migration scenario based on the connections
# and the table mapping
$offlineMigTaskProperties = @{
    "input"    = @{
        "sourceConnectionInfo"  = $null;
        "targetConnectionInfo"  = $null;
        "selectedDatabases"     = $null;
        "optionalAgentSettings" = @{
            "EnableCacheBatchesInMemory"         = $true;
            "DisableIncrementalRowStatusUpdates" = $true;
        };
        "startedOn"             = $null;
    };
    "taskType" = "Migrate.MySql.AzureDbForMySql";
};
$offlineSelectedDatabase = @{
    "name"               = $null;
    "targetDatabaseName" = $null;
    "tableMap"           = $null;
};

LogMessage -Message "Preparing migration scenario configuration ..." -IsProcessing $true

# Select the database to be migrated
$offlineSelectedDatabase.name = $SourceDatabaseName;
$offlineSelectedDatabase.tableMap = New-Object PSObject -Property $tableMap;
$offlineSelectedDatabase.targetDatabaseName = $TargetDatabaseName;

# Set connection info and the database mapping
$offlineMigTaskProperties.input.sourceConnectionInfo = $sourceConnInfo;
$offlineMigTaskProperties.input.targetConnectionInfo = $targetConnInfo;
$offlineMigTaskProperties.input.selectedDatabases = @($offlineSelectedDatabase);
$offlineMigTaskProperties.input.startedOn = [System.DateTimeOffset]::UtcNow.ToString("O");
```

## <a name="configure-performance-tuning-parameters"></a>Performans ayarlama parametrelerini Yapılandır

PowerShell modülünün PERT olarak, ortama göre ayarlanabilir, kullanılabilir birkaç isteğe bağlı parametre vardır. Bu parametreler, geçiş görevinin performansını geliştirmek için kullanılabilir. Tüm bu parametreler isteğe bağlıdır ve varsayılan değerleri NULL olur.

> [!NOTE]
> Aşağıdaki performans yapılandırmalarının Premium SKU 'ya geçiş sırasında artan verimlilik gösteriliyor.
> * WriteDataRangeBatchTaskCount = 12
> * DelayProgressUpdatesInStorageInterval = 30 saniye
> * ThrottleQueryTableDataRangeTaskAtBatchCount = 36

Aşağıdaki betik, parametrelerin Kullanıcı değerlerini alır ve geçiş görevi özelliklerindeki parametreleri ayarlar.

```powershell
# Setting optional parameters from fine tuning the data transfer rate during migration
# DEFAULT values for all the configurations is $null
LogMessage -Message "Adding optional migration performance tuning configuration ..." -IsProcessing $true
# Partitioning settings
# Optional setting that configures the maximum number of parallel reads on tables located on the source database.
[object] $DesiredRangesCount = 4
# Optional setting that configures that size of the largest batch that will be committed to the target server.
[object] $MaxBatchSizeKb = 4096
# Optional setting that configures the minimum number of rows in each batch written to the target.
[object] $MinBatchRows = $null
# Task count settings
# Optional setting that configures the number of databases that will be prepared for migration in parallel.
[object] $PrepareDatabaseForBulkImportTaskCount = $null
# Optional setting that configures the number of tables that will be prepared for migration in parallel.
[object] $PrepareTableForBulkImportTaskCount = $null
# Optional setting that configures the number of threads available to read ranges on the source.
[object] $QueryTableDataRangeTaskCount = 8
# Optional setting that configures the number of threads available to write batches to the target.
[object] $WriteDataRangeBatchTaskCount = 12
# Batch cache settings
# Optional setting that configures how much memory will be used to cache batches in memory before reads on the source are throttled.
[object] $MaxBatchCacheSizeMb = $null
# Optional setting that configures the amount of available memory at which point reads on the source will be throttled.
[object] $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb = $null
# Optional setting that configures the number of batches cached in memory that will trigger read throttling on the source.
[object] $ThrottleQueryTableDataRangeTaskAtBatchCount = 36
# Performance settings
# Optional setting that configures the delay between updates of result objects in Azure Table Storage.
[object] $DelayProgressUpdatesInStorageInterval = "00:00:30"

function AddOptionalSetting($optionalAgentSettings, $settingName, $settingValue) {
    # If no value specified for the setting, don't bother adding it to the input
    if ($null -eq $settingValue) {
        return;
    }

    # Add a new property to the JSON object to capture the setting which will be customized
    $optionalAgentSettings | add-member -MemberType NoteProperty -Name $settingName -Value $settingValue
}

# Set any optional settings in the input based on parameters to this cmdlet
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DesiredRangesCount" $DesiredRangesCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchSizeKb" $MaxBatchSizeKb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MinBatchRows" $MinBatchRows;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareDatabaseForBulkImportTaskCount" $PrepareDatabaseForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "PrepareTableForBulkImportTaskCount" $PrepareTableForBulkImportTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "QueryTableDataRangeTaskCount" $QueryTableDataRangeTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "WriteDataRangeBatchTaskCount" $WriteDataRangeBatchTaskCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "MaxBatchCacheSizeMb" $MaxBatchCacheSizeMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb" $ThrottleQueryTableDataRangeTaskAtAvailableMemoryMb;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "ThrottleQueryTableDataRangeTaskAtBatchCount" $ThrottleQueryTableDataRangeTaskAtBatchCount;
AddOptionalSetting $offlineMigTaskProperties.input.optionalAgentSettings "DelayProgressUpdatesInStorageInterval" $DelayProgressUpdatesInStorageInterval;
```

## <a name="creating-and-running-the-migration-task"></a>Geçiş görevi oluşturma ve çalıştırma

Görevin girişi yapılandırıldıktan sonra, görev artık aracıda oluşturulur ve yürütülür. Betik, görev yürütmeyi tetikler ve geçişin tamamlanmasını bekler.

Aşağıdaki betik yapılandırılmış geçiş görevini çağırır ve tamamlanmasını bekler.

```powershell
# Running the migration scenario
[string] $TaskName = "mysqlofflinemigrate"

LogMessage -Message "Running data migration scenario ..." -IsProcessing $true
$summary = @{
    "SourceServer"   = $SourceServerName;
    "SourceDatabase" = $SourceDatabaseName;
    "TargetServer"   = $TargetServerName;
    "TargetDatabase" = $TargetDatabaseName;
    "TableCount"     = $tableMap.Count;
    "StartedOn"      = $offlineMigTaskProperties.input.startedOn;
}

Write-Host "Job Summary:" -ForegroundColor Yellow
Write-Host $(ConvertTo-Json $summary) -ForegroundColor Yellow

$migrationResult = RunScenario -MigrationService $dmsService `
    -MigrationProject $dmsProject `
    -ScenarioTaskName $TaskName `
    -TaskProperties $offlineMigTaskProperties

LogMessage -Message "Migration completed with status - $($migrationResult.state)"
#Checking for any errors or warnings captured by the task during migration
$dbLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "DatabaseLevelOutput" } 
$migrationLevelResult = $migrationResult.output | Where-Object { $_.resultType -eq "MigrationLevelOutput" }
if ($dbLevelResult.exceptionsAndWarnings) {
    Write-Host "Following database errors were captured: $($dbLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationLevelResult.exceptionsAndWarnings) {
    Write-Host "Following migration errors were captured: $($migrationLevelResult.exceptionsAndWarnings)" -ForegroundColor Red
}
if ($migrationResult.errors.details) {
    Write-Host "Following task level migration errors were captured: $($migrationResult.errors.details)" -ForegroundColor Red
}
```

## <a name="deleting-the-database-migration-service"></a>Veritabanı geçiş hizmeti siliniyor

Aynı veritabanı geçiş hizmeti birden çok geçiş için kullanılabilir, böylece örnek oluşturulduktan sonra yeniden kullanılabilir. Veritabanı geçiş hizmetini kullanmaya devam edemeyecekiniz, [Remove-AzDataMigrationService](/powershell/module/az.datamigration/remove-azdatamigrationservice?) komutunu kullanarak hizmeti silebilirsiniz.

Aşağıdaki betik, Azure veritabanı geçiş hizmeti örneğini ve ilişkili projelerini siler.

```powershell
Remove-AzDataMigrationService -ResourceId $($dmsService.ResourceId)
```

## <a name="next-steps"></a>Sonraki adımlar

* DMS kullanarak geçişler gerçekleştirirken oluşan bilinen sorunlar ve sınırlamalar hakkında bilgi için, [yaygın sorunlar-Azure veritabanı geçiş hizmeti](./known-issues-troubleshooting-dms.md)makalesine bakın.
* DMS kullanırken kaynak veritabanı bağlantı sorunlarını giderme hakkında bilgi için bkz. [kaynak veritabanlarını bağlama sorunları](./known-issues-troubleshooting-dms-source-connectivity.md).
* Azure veritabanı geçiş hizmeti hakkında daha fazla bilgi için [Azure veritabanı geçiş hizmeti nedir?](./dms-overview.md)makalesine bakın.
* MySQL için Azure veritabanı hakkında daha fazla bilgi için, [MySQL Için Azure veritabanı nedir?](../mysql/overview.md)makalesine bakın.
* Portal aracılığıyla DMS kullanma hakkında öğretici için, bkz. [Örneğin, DMS kullanarak MySQL Için MySQL Azure veritabanına](./tutorial-mysql-azure-mysql-offline-portal.md)