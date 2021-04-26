---
title: PowerShell kullanarak elastik Iş Aracısı oluşturma (Önizleme)
description: PowerShell kullanarak Elastik İş aracısı oluşturmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 10/21/2020
ms.openlocfilehash: 95e9ef340328bb4c1835e966cc9c3019bca88c09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100368838"
---
# <a name="create-an-elastic-job-agent-using-powershell-preview"></a>PowerShell kullanarak elastik Iş Aracısı oluşturma (Önizleme)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Elastik işler (Önizleme)](job-automation-overview.md) bir veya daha fazla Transact-SQL (T-SQL) komut dosyasının çok sayıda veritabanı arasında paralel olarak çalıştırılmasını sağlar.

Bu öğreticide, birden çok veritabanında bir sorgu çalıştırmak için gereken adımları öğreneceksiniz:

> [!div class="checklist"]
> * Elastik İş aracısı oluşturma
> * İşlerin hedeflerinde betik yürütebilmesi için iş kimlik bilgileri oluşturma
> * İşi çalıştırmak istediğiniz hedefleri (sunucular, elastik havuzlar, parça eşlemeleri) tanımlama
> * Aracının işlere bağlanıp yürütebilmesi için hedef veritabanlarında veritabanlı kapsamlı kimlik bilgileri oluşturma
> * Bir iş oluşturma
> * Bir işe iş adımları ekleme
> * Bir işin yürütülmesini başlatma
> * Bir işi izleme

## <a name="prerequisites"></a>Önkoşullar

Elastik Veritabanı işleri yükseltilen sürümünde, geçiş sırasında kullanılmak üzere yeni bir PowerShell cmdlet 'leri kümesi vardır. Bu yeni cmdlet 'ler, tüm mevcut iş kimlik bilgilerinizi, hedefleri (veritabanları, sunucular, özel koleksiyonlar dahil), iş Tetikleyicileri, iş zamanlamaları, iş içerikleri ve işleri yeni bir elastik Iş aracısına aktarır.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>En son elastik Işler cmdlet 'lerini yükler

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

En son elastik Iş cmdlet 'lerini almak için **az. SQL** modülünü yükler. PowerShell 'de Yönetim erişimiyle aşağıdaki komutları çalıştırın.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Bu öğretici, **az. SQL** modülüne ek olarak *SqlServer* PowerShell modülünü de gerektirir. Ayrıntılar için bkz. [SQL Server PowerShell modülünü Install](/sql/powershell/download-sql-server-ps-module).

## <a name="create-required-resources"></a>Gerekli kaynakları oluşturma

Elastik İş aracısı oluşturmak için [İş veritabanı](job-automation-overview.md#elastic-job-database) olarak kullanılacak bir veritabanı (S0 veya üzeri) gerekir.

Aşağıdaki betik yeni bir kaynak grubu ve sunucunun yanı sıra İş veritabanı olarak kullanılacak bir veritabanı oluşturur. İkinci komut, işleri yürütmek için iki boş veritabanı ile ikinci bir sunucu oluşturur.

Elastik İşlere özel adlandırma gereksinimleri olmadığından [Azure gereksinimlerine](/azure/architecture/best-practices/resource-naming) uygun olduğu sürece istediğiniz adlandırma kuralını kullanabilirsiniz.

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

### <a name="create-the-elastic-job-agent"></a>Elastik İş aracısını oluşturma

Elastik İş aracısı; işlerin oluşturulması, çalıştırılması ve yönetilmesi için kullanılan bir Azure kaynağıdır. Aracı işleri belirli bir plana göre veya tek seferlik iş olarak yürütür.

**New-Azsqtalakjobagent** cmdlet 'ı, Azure SQL veritabanında zaten var olan bir veritabanı gerektirir, bu nedenle *resourcegroupname*, *ServerName* ve *DatabaseName* parametrelerinin hepsi mevcut kaynaklara işaret etmelidir.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>İş kimlik bilgilerini oluşturma

İşler, yürütme ve yürütme betikleri üzerinde hedef grup tarafından belirtilen hedef veritabanlarına bağlanmak için veritabanı kapsamlı kimlik bilgilerini kullanır. Bu veritabanı kapsamlı kimlik bilgileri aynı zamanda hedef grup üyesi türü olarak kullanıldığında bir sunucu veya elastik havuzdaki tüm veritabanlarını numaralandırma amacıyla asıl veritabanına bağlanmak için kullanılır.

Veritabanı kapsamlı kimlik bilgileri iş veritabanında oluşturulmalıdır. İşin başarıyla tamamlanması için hedef veritabanlarının tümünde yeterli izinlere sahip bir oturum açma adı olmalıdır.

![Elastik İşler kimlik bilgileri](./media/elastic-jobs-powershell-create/job-credentials.png)

Aşağıdaki betikte görüntüdeki kimlik bilgilerine ek olarak **GRANT** komutlarının kullanıldığına dikkat edin. Bu izinler, bu örnek işte seçtiğimiz betik için geçerlidir. Örnek, hedeflenen veritabanlarında yeni bir tablo oluşturduğundan, her hedef DB 'nin başarıyla çalışması için uygun izinlere ihtiyacı vardır.

Gereken iş kimlik bilgilerini (iş veritabanında) oluşturmak için aşağıdaki betiği çalıştırın:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = 'CREATE LOGIN masteruser WITH PASSWORD=''password!123'''
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = 'CREATE LOGIN jobuser WITH PASSWORD=''password!123'''
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String 'password!123' -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>İşi çalıştırmak için hedef veritabanlarını tanımlayın

[Hedef grup](job-automation-overview.md#target-group), işin üzerinde çalışacağı veritabanı bir veya daha fazla veritabanından oluşan kümeyi tanımlar.

Aşağıdaki kod parçacığı iki hedef grubu oluşturur: *ServerGroup* ve *serverGroupExcludingDb2*. *ServerGroup* , yürütme sırasında sunucuda var olan tüm veritabanlarını hedefler ve *serverGroupExcludingDb2* , *targetDb2* dışında, sunucudaki tüm veritabanlarını hedefler.

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Bir iş ve adım oluşturun

Bu örnek, işin çalışması için bir işi ve iki iş adımını tanımlar. Birinci iş adımı (*step1*), hedef *ServerGroup* grubundaki her veritabanında yeni bir tablo (*Step1Table*) oluşturur. İkinci iş adımı (*step2*), *TargetDb2* haricindeki tüm veritabanlarında yeni bir tablo (*Step2Table*) oluşturur. Bunun nedeni hedef grubun önceden bu veritabanını hariç tutacak şekilde tanımlanmış olmasıdır.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>İşi çalıştırma

İşi hemen başlatmak için aşağıdaki komutu çalıştırın:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

İşlem başarıyla tamamlandıktan sonra TargetDb1 içinde iki yeni tablo, TargetDb2 içinde ise yalnızca bir yeni tablo görmeniz gerekir:

   ![SSMS'de yeni tablo doğrulaması](./media/elastic-jobs-powershell-create/job-execution-verification.png)

İşi daha sonra çalışacak şekilde de zamanlayabilirsiniz. Bir işi belirli bir zamanda çalışacak şekilde zamanlamak için aşağıdaki komutu çalıştırın:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>İş yürütme durumunu izleme

Aşağıdaki kod parçacıkları, iş yürütme ayrıntılarını almaktadır:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

Aşağıdaki tabloda olası iş yürütme durumları listelenmektedir:

|Durum|Açıklama|
|:---|:---|
|**Oluşturulan** | İş yürütmesi yeni oluşturulmuştur ve henüz devam etmiyor.|
|**Ediyor** | İş yürütmesi Şu anda devam ediyor.|
|**WaitingForRetry** | İş yürütmesi, işlemini tamamlayamadı ve yeniden denenmayı bekliyor.|
|**Başarılı** | İşin yürütülmesi başarıyla tamamlandı.|
|**SucceededWithSkipped** | İş yürütmesi başarıyla tamamlandı, ancak bazı alt öğeleri atlandı.|
|**Başarısız** | İş yürütmesi başarısız oldu ve yeniden deneme süresi bitti.|
|**Zaman aşımına uğradı** | İş yürütme zaman aşımına uğradı.|
|**İptal edildi** | İş yürütmesi iptal edildi.|
|**Atlandı** | Aynı iş adımının başka bir yürütmesi aynı hedefte zaten çalıştığı için iş yürütmesi atlandı.|
|**Waitingforchildjobyürütmeler** | İş yürütmesi, alt yürütmelerinin tamamlanmasını bekliyor.|

## <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu silerek bu öğreticide oluşturulan Azure kaynaklarını silebilirsiniz.

> [!TIP]
> Bu işlerle çalışmaya devam etmeyi planlıyorsanız, bu makalede oluşturulan kaynakları temizlemeyin.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide bir veritabanı kümesinde Transact-SQL betiği çalıştırdınız. Aşağıdaki görevleri nasıl gerçekleştireceğinizi öğrendiniz:

> [!div class="checklist"]
> * Elastik İş aracısı oluşturma
> * İşlerin hedeflerinde betik yürütebilmesi için iş kimlik bilgileri oluşturma
> * İşi çalıştırmak istediğiniz hedefleri (sunucular, elastik havuzlar, parça eşlemeleri) tanımlama
> * Aracının işlere bağlanıp yürütebilmesi için hedef veritabanlarında veritabanlı kapsamlı kimlik bilgileri oluşturma
> * Bir iş oluşturma
> * İşe bir iş adımı ekleme
> * Bir işi yürütmeye başlatma
> * İş izleme

> [!div class="nextstepaction"]
> [Elastik İşleri Transact-SQL kullanarak yönetme](elastic-jobs-tsql-create-manage.md)
