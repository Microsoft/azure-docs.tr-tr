---
title: PowerShell ile Azure-SSIS Integration Runtime sağlama
description: Azure'da SSIS paketleri dağıtıp çalıştırabilmek için PowerShell ile Azure Data Factory'de Azure-SSIS tümleştirme çalışma zamanı sağlamayı öğrenin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: tutorial
ms.date: 06/26/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 594cdd848a3712d2164616e38f7b75a01a21b7f6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683575"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory-with-powershell"></a>Azure Data Factory'de PowerShell ile Azure-SSIS Tümleştirme Çalışma Zamanı Sağlama

Bu öğretici, Azure Data Factory (ADF) içinde bir Azure-SQL Server Integration Services (SSIS) Integration Runtime (IR) sağlamaya yönelik adımlar sağlar. Azure-SSIS IR, Azure SQL veritabanı sunucusu/yönetilen örnek (proje dağıtım modeli) tarafından barındırılan ve dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına (paket dağıtım modeli) dağıtılan SSIS kataloğuna (SSSıSDB) dağıtılmış paketlerin çalıştırılmasını destekler. Azure-SSIS IR sağlandıktan sonra, paketlerinizi dağıtmak ve çalıştırmak için SQL Server Veri Araçları (SSDT)/SQL Server Management Studio (SSMS) gibi tanıdık araçları ve `dtinstall`/`dtutil`/`dtexec`gibi komut satırı yardımcı programlarını kullanabilirsiniz. Mavisi. Bu öğreticide, aşağıdaki adımları gerçekleştireceksiniz:

> [!NOTE]
> Bu makale, bir Azure-SSIS IR sağlamak için Azure PowerShell kullanır. Azure-SSIS IR sağlamak üzere Azure portal/ADF uygulamasını kullanmak için bkz. [öğretici: sağlama Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md). 

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure SSIS tümleştirme çalışma zamanı oluşturma
> * Azure SSIS tümleştirme çalışma zamanını başlatma
> * Tam betiği gözden geçirme
> * SSIS paketlerini dağıtma

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure aboneliği**. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz](https://azure.microsoft.com/free/) bir hesap oluşturun. Azure-SSIS IR hakkında kavramsal bilgiler için bkz. [Azure SSIS tümleştirme çalışma zamanına genel bakış](concepts-integration-runtime.md#azure-ssis-integration-runtime).
- **Azure SQL veritabanı sunucusu (isteğe bağlı)** . Henüz bir veritabanı sunucunuz yoksa, başlamadan önce Azure portal bir tane oluşturun. ADF, bu veritabanı sunucusunda SSıSDB oluşturacak. Veritabanı sunucusunu tümleştirme çalışma zamanı ile aynı Azure bölgesinde oluşturmanız önerilir. Bu yapılandırma, tümleştirme çalışma zamanının yürütme günlüklerini Azure bölgeleriyle çıkmadan SSSıSDB 'ye yazmasını sağlar. 
    - Seçilen veritabanı sunucusuna göre SSISDB sizin adınıza tek veritabanı, elastik havuzun bir parçası veya Yönetilen Örnek biçiminde oluşturulabilir ve genel ağ üzerinden veya sanal ağa eklenerek erişilebilir. SSıSDB barındıracak veritabanı sunucusu türünü seçme konusunda rehberlik için bkz. [Azure SQL veritabanı tek veritabanı, elastik havuz ve yönetilen örnek karşılaştırması](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). SSıSDB barındırmak veya şirket içi verilere erişim gerektirmek için bir sanal ağda sanal ağ hizmet uç noktaları/yönetilen örnek ile Azure SQL veritabanı sunucusu kullanıyorsanız, Azure-SSIS IR sanal bir ağa eklemeniz gerekir, bkz. [Sanal ağda Azure-SSIS IR oluşturma Ağ](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Veritabanı sunucusunda **Azure hizmetlerine erişime izin ver** ayarının etkin olduğundan emin olun. Bu, SSıSDB 'yi barındırmak için sanal ağ hizmet uç noktaları/yönetilen örneği ile Azure SQL veritabanı sunucusu kullandığınızda geçerli değildir. Daha fazla bilgi için bkz. [Azure SQL veritabanınızın güvenliğini sağlama](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Bu ayarı PowerShell kullanarak etkinleştirmek için, bkz. [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Veritabanı sunucusunun güvenlik duvarı ayarlarındaki istemci IP adresi listesine istemci makinenin IP adresini veya istemci makinenin IP adresini içeren bir IP adresi aralığını ekleyin. Daha fazla bilgi için bkz. [Azure SQL Veritabanı'nda sunucu düzeyinde ve veritabanı düzeyinde güvenlik duvarı kuralları yapılandırma](../sql-database/sql-database-firewall-configure.md).
    - Sunucu Yöneticisi kimlik bilgileriniz veya Azure Active Directory (AAD) kimlik doğrulamasıyla SQL kimlik doğrulaması kullanarak ADF 'niz için yönetilen kimlikle veritabanı sunucusuna bağlanabilirsiniz.  İkinci seçenekte ADF için yönetilen kimliğinizi veritabanı sunucusuna erişim izni olan bir AAD grubuna eklemeniz gerekir, bkz. [AAD kimlik doğrulaması ile Azure-SSIS IR oluşturma](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
    - Veritabanı sunucunuzun zaten bir SSSıSDB içermediğinden emin olun. Azure-SSIS IR sağlanması, var olan bir SSıSDB kullanımını desteklemez.
- **Azure PowerShell**. Azure-SSIS IR sağlamak üzere bir PowerShell Betiği çalıştırmak istiyorsanız, [Azure PowerShell 'yi yüklemek ve yapılandırmak](/powershell/azure/install-Az-ps)için yönergeleri izleyin.

> [!NOTE]
> - ADF ve Azure-SSIS IR Şu anda kullanılabildiği Azure bölgelerinin listesi için, [bölgeye göre ADF + SSIS IR kullanılabilirliği](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all)bölümüne bakın. 

## <a name="launch-windows-powershell-ise"></a>Windows PowerShell ISE’yi başlatma

**Windows PowerShell ISE**’yi yönetici ayrıcalıklarıyla başlatın. 

## <a name="create-variables"></a>Değişken oluşturma

Aşağıdaki betiği kopyalayıp yapıştırın-değişkenler için değerler belirtin. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

## <a name="sign-in-and-select-subscription"></a>Oturum açın ve abonelik ' ı seçin

Aşağıdaki kodu betiğe ekleyerek oturum açın ve Azure aboneliğinizi seçin.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

## <a name="validate-the-connection-to-database-server"></a>Veritabanı sunucusuyla bağlantıyı doğrulama

Azure SQL veritabanı sunucunuzu doğrulamak için aşağıdaki betiği ekleyin. 

```powershell
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}
```

Betiğinin bir parçası olarak bir Azure SQL veritabanı oluşturmak için aşağıdaki örneğe bakın: 

Önceden tanımlanmamış değişkenlerin değerlerini belirleyin. Örneğin: SSISDBServerName, FirewallIPAddress. 

```powershell
New-AzSqlServer -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SSISDBServerAdminUserName, $(ConvertTo-SecureString -String $SSISDBServerAdminPassword -AsPlainText -Force))    

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SSISDBServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SSISDBServerName -AllowAllAzureIPs
```

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanarak bir [Azure Kaynak grubu](../azure-resource-manager/resource-group-overview.md) oluşturun. Kaynak grubu, Azure kaynaklarının grup olarak dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Kaynak grubunuz zaten varsa, bu kodu betiğe kopyalamayın. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Veri fabrikası oluşturmak için aşağıdaki komutu çalıştırın:

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>Tümleştirme çalışma zamanı oluşturma

Azure 'da SSIS paketlerini çalıştıran bir Azure-SSIS tümleştirme çalışma zamanı oluşturmak için aşağıdaki komutları çalıştırın.

SSıSDB kullanmıyorsanız, CatalogServerEndpoint, CatalogPricingTier ve CatalogAdminCredential parametrelerini atlayabilirsiniz.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

## <a name="start-integration-runtime"></a>Tümleştirme çalışma zamanını başlatma

Azure-SSIS tümleştirme çalışma zamanını başlatmak için aşağıdaki komutları çalıştırın.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

> [!NOTE]
> Özel kurulum süresini dışlayarak, bu işlem 5 dakika içinde tamamlanmalıdır.
>
> SSıSDB kullanırsanız, ADF hizmeti SSıSDB 'yi hazırlamak için veritabanı sunucunuza bağlanır. 
> 
> Bir Azure-SSIS IR sağladığınızda, SSIS için yeniden dağıtılabilir ve Azure Özellik paketi de yüklenir. Bu bileşenler, yerleşik bileşenler tarafından zaten desteklenen veri kaynaklarına ek olarak Excel/Access dosyalarına ve çeşitli Azure veri kaynaklarına bağlantı sağlar. Ayrıca, ek bileşenleri de yükleyebilirsiniz, bkz. [Azure-SSIS IR Için özel kurulum](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="full-script"></a>Tam betik

Bu bölümdeki PowerShell betiği, SSIS paketlerini çalıştıran bir Azure-SSIS IR örneğini yapılandırır. Bu betiği başarıyla çalıştırdıktan sonra, Azure 'da SSIS paketlerini dağıtabilir ve çalıştırabilirsiniz.

1. Windows PowerShell Tümleşik Komut Dosyası Ortamı’nı (ISE) başlatın.
2. ISE'de komut isteminden aşağıdaki komutu çalıştırın.  

    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```

3. Bu bölümde PowerShell betiğini kopyalayıp ISE’ye yapıştırın.
4. Betiğin başında tüm parametreler için uygun değerleri sağlayın.
5. Betiği çalıştırın. 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you want to use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf    
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
    $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
    Try
    {
        $sqlConnection.Open();
    }
    Catch [System.Data.SqlClient.SqlException]
    {
        Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
        Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
        $yn = Read-Host
        if(!($yn -ieq "Y"))
        {
            Return;
        } 
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -CatalogAdminCredential $serverCreds
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")   
```

## <a name="monitor-and-manage-azure-ssis-ir"></a>Azure-SSIS IR’yi izleme ve yönetme

Bir Azure-SSIS IR’yi izleme ve yönetme hakkında ayrıntılar için aşağıdaki makalelere bakın. 

- [İzleyici Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [Azure-SSIS IR Yönet](manage-azure-ssis-integration-runtime.md)

## <a name="deploy-ssis-packages"></a>SSIS paketlerini dağıtma

SSSıSDB kullanıyorsanız, paketlerinizi bu sunucuya dağıtabilir ve sunucu uç noktası aracılığıyla veritabanı sunucunuza bağlanan SSDT/SSMS araçlarını kullanarak Azure-SSIS IR çalıştırabilirsiniz. Genel bitiş noktası olan Azure SQL veritabanı sunucusu/yönetilen örneği için sunucu uç noktası biçimi sırasıyla `<server name>.database.windows.net`/`<server name>.public.<dns prefix>.database.windows.net,3342`. SSıSDB kullanmıyorsanız, paketlerinizi dosya sistemlerine/dosya paylaşımlarına/Azure dosyalarına dağıtabilir ve `dtinstall`/`dtutil`/`dtexec` komut satırı yardımcı programlarını kullanarak Azure-SSIS IR çalıştırabilirsiniz. Daha fazla bilgi için bkz. [SSIS paketlerini dağıtma](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). Her iki durumda da, ADF işlem hatlarında SSIS paketi yürütme etkinliğini kullanarak Azure-SSIS IR dağıtılan paketlerinizi çalıştırabilirsiniz, bkz. [birinci sınıf ADF etkinliği olarak SSIS paketi yürütmeyi çağırma](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Ayrıca, SSIS belgelerindeki aşağıdaki makalelere bakın: 

- [Azure 'da SSIS paketlerini dağıtma, çalıştırma ve izleme](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure 'da SSıSDB 'ye bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure 'da paket yürütmelerini zamanlama](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows kimlik doğrulaması ile şirket içi veri kaynaklarına bağlanma](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz: 

> [!div class="checklist"]
> * Veri fabrikası oluşturma.
> * Azure SSIS tümleştirme çalışma zamanı oluşturma
> * Azure SSIS tümleştirme çalışma zamanını başlatma
> * Tam betiği gözden geçirme
> * SSIS paketlerini dağıtma

Azure-SSIS tümleştirme çalışma zamanınızı özelleştirme hakkında bilgi edinmek için şu makaleye bakın:

> [!div class="nextstepaction"]
>[Azure-SSIS IR hizmetini özelleştirme](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)