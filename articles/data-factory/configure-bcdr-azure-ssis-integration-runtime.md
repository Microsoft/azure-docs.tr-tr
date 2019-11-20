---
title: SQL veritabanı yük devretmesi için Azure-SSIS Integration Runtime yapılandırma
description: Bu makalede, SSıSDB veritabanı için Azure SQL veritabanı coğrafi çoğaltma ve yük devretme ile Azure-SSIS Integration Runtime nasıl yapılandırılacağı açıklanmaktadır
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6e709a25c6c33a1fc80a110435035b1473d92681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681383"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Azure-SSIS Integration Runtime Azure SQL veritabanı coğrafi çoğaltma ve yük devretme ile yapılandırma

Bu makalede, SSıSDB veritabanı için Azure SQL veritabanı coğrafi çoğaltmasıyla Azure-SSIS Integration Runtime nasıl yapılandırılacağı açıklanır. Yük devretme gerçekleştiğinde, Azure-SSIS IR ikincil veritabanıyla çalışmayı sürdürdüğünden emin olabilirsiniz.

SQL veritabanı için coğrafi çoğaltma ve yük devretme hakkında daha fazla bilgi için bkz. [genel bakış: etkin coğrafi çoğaltma ve otomatik yük devretme grupları](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Senaryo 1-Azure-SSIS IR okuma-yazma dinleyicisi uç noktasını işaret ediyor

### <a name="conditions"></a>Koşullar

Bu bölüm aşağıdaki koşullar doğru olduğunda geçerlidir:

- Azure-SSIS IR, yük devretme grubunun okuma/yazma dinleyicisi uç noktasını işaret eder.

  '

- SQL veritabanı sunucusu, sanal ağ hizmeti uç noktası *kuralıyla yapılandırılmadı.*

### <a name="solution"></a>Çözüm

Yük devretme gerçekleştiğinde Azure-SSIS IR saydamdır. Azure-SSIS IR, yük devretme grubunun yeni birincili otomatik olarak bağlanır.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Senaryo 2-Azure-SSIS IR birincil sunucu uç noktasını gösteriyor

### <a name="conditions"></a>Koşullar

Bu bölüm aşağıdaki koşullardan biri doğru olduğunda geçerlidir:

- Azure-SSIS IR, yük devretme grubunun birincil sunucu uç noktasını işaret eder. Bu uç nokta, yük devretme gerçekleştiğinde değişir.

  OR

- Azure SQL veritabanı sunucusu, sanal ağ hizmeti uç noktası kuralıyla yapılandırılır.

  OR

- Veritabanı sunucusu, bir sanal ağ ile yapılandırılmış bir SQL veritabanı yönetilen örneğidir.

### <a name="solution"></a>Çözüm

Yük devretme gerçekleştiğinde, aşağıdaki işlemleri yapmanız gerekir:

1. Azure-SSIS IR durdurun.

2. IR 'yi yeni birincil uç noktaya ve yeni bölgedeki bir sanal ağa işaret edecek şekilde yeniden yapılandırın.

3. IR 'yi yeniden başlatın.

Aşağıdaki bölümlerde bu adımlar daha ayrıntılı olarak açıklanır.

### <a name="prerequisites"></a>Ön koşullar

- Sunucunun aynı anda bir kesinti olması durumunda Azure SQL veritabanı sunucunuz için olağanüstü durum kurtarmayı etkinleştirdiğinizden emin olun. Daha fazla bilgi için bkz. [Azure SQL veritabanı ile iş sürekliliği 'Ne genel bakış](../sql-database/sql-database-business-continuity.md).

- Geçerli bölgede bir sanal ağ kullanıyorsanız, Azure-SSIS tümleştirme çalışma zamanını bağlamak için yeni bölgede başka bir sanal ağ kullanmanız gerekir. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanını bir sanal ağa ekleme](join-azure-ssis-integration-runtime-virtual-network.md).

- Özel bir kurulum kullanıyorsanız, özel kurulum komut dosyanızı ve ilişkili dosyalarınızı depolayan blob kapsayıcısı için başka bir SAS URI 'SI hazırlamanız gerekebilir, bu nedenle bir kesinti sırasında erişilebilir olmaya devam eder. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanı üzerinde özel bir kurulum yapılandırma](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Adımlar

Azure-SSIS IR durdurmak için şu adımları izleyin, IR 'yi yeni bir bölgeye geçirin ve yeniden başlatın.

1. Özgün bölgedeki IR 'yi durdurun.

2. IR 'yi yeni ayarlarla güncelleştirmek için PowerShell 'de aşağıdaki komutu çağırın.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Bu PowerShell komutu hakkında daha fazla bilgi için bkz [. Azure Data Factory Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md)

3. IR 'yi yeniden başlatın.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Senaryo 3-var olan bir SSıSDB (SSIS Kataloğu) yeni bir Azure-SSIS IR Iliştirme

Geçerli bölgede bir ADF veya Azure-SSIS IR olağanüstü durum oluştuğunda, SSıSDB 'nizin yeni bir bölgede yeni bir Azure-SSIS IR çalışmasını sağlayabilirsiniz.

### <a name="prerequisites"></a>Ön koşullar

- Geçerli bölgede bir sanal ağ kullanıyorsanız, Azure-SSIS tümleştirme çalışma zamanını bağlamak için yeni bölgede başka bir sanal ağ kullanmanız gerekir. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanını bir sanal ağa ekleme](join-azure-ssis-integration-runtime-virtual-network.md).

- Özel bir kurulum kullanıyorsanız, özel kurulum komut dosyanızı ve ilişkili dosyalarınızı depolayan blob kapsayıcısı için başka bir SAS URI 'SI hazırlamanız gerekebilir, bu nedenle bir kesinti sırasında erişilebilir olmaya devam eder. Daha fazla bilgi için bkz. [Azure-SSIS tümleştirme çalışma zamanı üzerinde özel bir kurulum yapılandırma](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Adımlar

Azure-SSIS IR durdurmak için şu adımları izleyin, IR 'yi yeni bir bölgeye geçirin ve yeniden başlatın.

1. SSıSDB 'yi **\<new_data_factory_name\>** veya **\<new_integration_runtime_name\>** 'e bağlı hale getirmek için saklı yordamı yürütün.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Yeni bölgede **\<new_data_factory_name\>** adlı yeni bir veri fabrikası oluşturun. Daha fazla bilgi için bkz. Veri Fabrikası oluşturma.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Bu PowerShell komutu hakkında daha fazla bilgi için bkz. [PowerShell kullanarak Azure Veri Fabrikası oluşturma](quickstart-create-data-factory-powershell.md)

3. Yeni bölgede Azure PowerShell kullanarak **\<new_integration_runtime_name\>** adlı yeni bir Azure-SSIS IR oluşturun.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Bu PowerShell komutu hakkında daha fazla bilgi için bkz [. Azure Data Factory Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md)

4. IR 'yi yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

Azure-SSIS IR için diğer yapılandırma seçeneklerini göz önünde bulundurun:

- [Yüksek performans için Azure-SSIS Integration Runtime yapılandırma](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS Integration Runtime için kurulumu özelleştirme](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS Integration Runtime için Enterprise Edition sağlama](how-to-configure-azure-ssis-ir-enterprise-edition.md)
