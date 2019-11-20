---
title: Azure-SSIS Integration Runtime için performansı yapılandırın
description: Yüksek performans için Azure-SSIS Integration Runtime özelliklerinin nasıl yapılandırılacağını öğrenin
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 518da092f690108111ca4456eaca66e4f3153c54
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681448"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Yüksek performans için Azure-SSIS Integration Runtime yapılandırma

Bu makalede, yüksek performans için bir Azure-SSIS Integration Runtime (IR) yapılandırma açıklanmaktadır. Azure-SSIS IR, Azure 'da SQL Server Integration Services (SSIS) paketlerini dağıtmanıza ve çalıştırmanıza olanak tanır. Azure-SSIS IR hakkında daha fazla bilgi için bkz. [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) makalesi. Azure 'da SSIS paketlerini dağıtma ve çalıştırma hakkında daha fazla bilgi için bkz. [SQL Server Integration Services iş yüklerini buluta kaldırma ve kaydırma](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Bu makale, SSIS geliştirme ekibinin üyeleri tarafından gerçekleştirilen şirket içi testlerin performans sonuçlarını ve gözlemlerini içerir. Sonuçlarınız farklılık gösterebilir. Hem maliyeti hem de performansı etkileyen yapılandırma ayarlarınızı sonuçlandırmak için kendi testinizi yapın.

## <a name="properties-to-configure"></a>Yapılandırılacak Özellikler

Bir yapılandırma betiğinin aşağıdaki kısmı, bir Azure-SSIS Integration Runtime oluşturduğunuzda yapılandırabileceğiniz özellikleri gösterir. PowerShell betiği ve açıklaması hakkında daha fazla bilgi için bkz. [Azure 'a SQL Server Integration Services paketleri dağıtma](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
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
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**Azuressislocation** , Integration Runtime çalışan düğümünün konumudur. Çalışan düğümü, bir Azure SQL veritabanında SSIS Katalog veritabanına (SSSıSDB) sabit bir bağlantı sağlar. **Azuressislocation** 'ı, SSıSDB barındıran SQL veritabanı sunucusuyla aynı konuma ayarlayın. Bu, tümleştirme çalışma zamanının mümkün olduğunca verimli bir şekilde çalışmasını sağlar.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Azure-SSIS IR dahil Data Factory, aşağıdaki seçenekleri destekler:
-   Standart\_a4\_v2
-   Standart\_A8\_v2
-   Standart\_D1\_v2
-   Standart\_D2\_v2
-   Standart\_D3\_v2
-   Standart\_D4\_v2
-   Standart\_D2\_v3
-   Standart\_D4\_v3
-   Standart\_D8\_v3
-   Standart\_D16\_v3
-   Standart\_D32\_v3
-   Standart\_D64\_v3
-   Standart\_E2\_v3
-   Standart\_E4\_v3
-   Standart\_E8\_v3
-   Standart\_E16\_v3
-   Standart\_E32\_v3
-   Standart\_E64\_v3

SSIS mühendislik ekibi tarafından resmi olmayan şirket içi sınamada, D serisi SSIS paketi yürütmesi için bir serinin daha uygun olduğu görülüyor.

-   D serisinin performans/fiyat oranı bir serinin yüksektir ve v3 serisinin performans/fiyat oranı v2 serisinden daha yüksektir.
-   D serisi için üretilen işlem, aynı fiyata ait bir seriden daha yüksektir ve v3 serisi için üretilen iş, aynı fiyata v2 serisinden daha yüksektir.
-   Azure-SSIS IR v2 serisi düğümleri özel kurulum için uygun değildir, bu nedenle lütfen bunun yerine v3 serisi düğümlerini kullanın. V2 serisi düğümlerini zaten kullanıyorsanız, lütfen v3 serisi düğümlerini mümkün olan en kısa sürede kullanmaya geçin.
-   E serisi, diğer makinelerden daha yüksek bellekten CPU oranı sağlayan bellek için iyileştirilmiş VM boyutlarıdır. Paketiniz çok miktarda bellek gerektiriyorsa, E Serisi VM 'yi seçmeyi düşünebilirsiniz.

### <a name="configure-for-execution-speed"></a>Yürütme hızı için yapılandırma
Çalıştırmak için çok fazla paketleriniz yoksa ve paketlerin hızlı bir şekilde çalışmasını istiyorsanız, senaryonuza uygun bir sanal makine türü seçmek için aşağıdaki grafikteki bilgileri kullanın.

Bu veriler tek bir çalışan düğümünde tek bir paket yürütmesini temsil eder. Paket, Azure Blob depolama alanından ad ve Soyadı sütunları olan 3.000.000 kaydı yükler, tam ad sütunu oluşturur ve tam adı olan kayıtları 20 karakterden uzun Azure Blob depolama alanına yazar.

![SSIS Integration Runtime paketi yürütme hızı](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Genel üretilen iş için yapılandırma

Çalıştırmak için çok sayıda paketiniz varsa ve genel aktarım hızını en iyi şekilde düşünüyorsanız, senaryonuza uygun bir sanal makine türü seçmek için aşağıdaki grafikteki bilgileri kullanın.

![SSIS Integration Runtime en fazla genel işleme](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** , tümleştirme çalışma zamanının ölçeklenebilirliğini ayarlar. Tümleştirme çalışma zamanının üretilen işi **AzureSSISNodeNumber**ile orantılıdır. Önce **AzureSSISNodeNumber** değerini küçük bir değere ayarlayın, tümleştirme çalışma zamanının verimini izleyin ve sonra senaryonuz için değeri ayarlayın. Çalışan düğümü sayısını yeniden yapılandırmak için bkz. [Azure-SSIS tümleştirme çalışma zamanını yönetme](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Paketleri çalıştırmak için zaten güçlü bir çalışan düğümü kullanıyorsanız, **Azuressismaxparallelexecutionspernode** öğesinin artırılması, tümleştirme çalışma zamanının genel verimini artırabilir. Standard_D1_v2 düğümleri için, düğüm başına 1-4 paralel yürütmeler desteklenir. Diğer tüm düğüm türleri için 1-en fazla (2 x çekirdek sayısı, 8) düğüm başına paralel yürütmeler desteklenir. **Azuressımaxparallelexecutionspernode** ' u desteklediğimiz maksimum değerin ötesinde isterseniz, bir destek bileti açabilir ve sizin için en büyük değeri artırabilir ve Azure PowerShell 'ı kullanarak **Azuressismaxparallelexecutionspernode 'u güncelleştirmeniz gerekir** .
Paketinizin maliyetine ve çalışan düğümlerine yönelik aşağıdaki yapılandırmalara göre uygun değeri tahmin edebilirsiniz. Daha fazla bilgi için bkz. [genel amaçlı sanal makine boyutları](../virtual-machines/windows/sizes-general.md).

| Boyut             | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum veri diski/aktarım hızı: IOPS | Maks NIC / Beklenen ağ performansı (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standart\_D1\_v2 | 1    | 3,5         | 50                     | 3000/46/23                                             | 2/2x500                         | 2 / 750                                        |
| Standart\_D2\_v2 | 2    | 7           | 100                    | 6000/93/46                                             | 4/4x500                         | 2 / 1500                                       |
| Standart\_D3\_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 8/8x500                         | 4 / 3000                                       |
| Standart\_D4\_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 16/16x500                       | 8 / 6000                                       |
| Standart\_a4\_v2 | 4    | 8           | 40                     | 4000/80/40                                             | 8/8x500                         | 4 / 1000                                       |
| Standart\_A8\_v2 | 8    | 16          | 80                     | 8000/160/80                                            | 16/16x500                       | 8 / 2000                                       |
| Standart\_D2\_v3 | 2    | 8           | 50                     | 3000/46/23                                             | 4/6x500                         | 2 / 1000                                       |
| Standart\_D4\_v3 | 4    | 16          | 100                    | 6000/93/46                                             | 8/12x500                        | 2 / 2000                                       |
| Standart\_D8\_v3 | 8    | 32          | 200                    | 12000/187/93                                           | 16/24x500                       | 4 / 4000                                       |
| Standart\_D16\_v3| 16   | 64          | 400                    | 24000/375/187                                          | 32/48x500                        | 8 / 8000                                       |
| Standart\_D32\_v3| 32   | 128         | 800                    | 48000/750/375                                          | 32/96x500                       | 8 / 16000                                      |
| Standart\_D64\_v3| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standart\_E2\_v3 | 2    | 16          | 50                     | 3000/46/23                                             | 4/6x500                         | 2 / 1000                                       |
| Standart\_E4\_v3 | 4    | 32          | 100                    | 6000/93/46                                             | 8/12x500                        | 2 / 2000                                       |
| Standart\_E8\_v3 | 8    | 64          | 200                    | 12000/187/93                                           | 16/24x500                       | 4 / 4000                                       |
| Standart\_E16\_v3| 16   | 128         | 400                    | 24000/375/187                                          | 32/48x500                       | 8 / 8000                                       |
| Standart\_E32\_v3| 32   | 256         | 800                    | 48000/750/375                                          | 32/96x500                       | 8 / 16000                                      |
| Standart\_E64\_v3| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

**Azuressismaxparallelexecutionspernode** özelliği için doğru değeri ayarlamaya yönelik yönergeler aşağıda verilmiştir: 

1. İlk olarak bunu küçük bir değere ayarlayın.
2. Genel üretilen iş hızının iyileştirilip geliştirilmediğini denetlemek için bunu küçük bir miktarda artırın.
3. Genel verimlilik en büyük değere ulaştığında değeri artırmayı durdurun.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**Ssisdbpricingtier** , BIR Azure SQL veritabanında SSIS Katalog veritabanı 'NıN (sssısdb) fiyatlandırma katmandır. Bu ayar IR örneğindeki en fazla çalışan sayısını, bir paket yürütmesini sıraya alma hızını ve yürütme günlüğünü yükleme hızını etkiler.

-   Paket yürütmeyi sıraya alma ve yürütme günlüğünü yükleme hızlarından endişelenmezseniz, en düşük veritabanı fiyatlandırma katmanını seçebilirsiniz. Temel fiyatlandırmayla Azure SQL veritabanı, bir tümleştirme çalışma zamanı örneğinde 8 çalışanı destekler.

-   Çalışan sayısı 8 ' den büyükse, temel değerinden daha güçlü bir veritabanı seçin veya çekirdek sayısı 50 ' den fazla olur. Aksi halde veritabanı Integration Runtime örneğinin performans sorununa dönüşmiş olur ve genel performans olumsuz etkilenir.

-   Günlüğe kaydetme düzeyi Verbose olarak ayarlandıysa S3 gibi daha güçlü bir veritabanı seçin. S3 fiyatlandırma katmanı, resmi olmayan şirket içi testimize göre SSIS paketi yürütmeyi 2 düğüm, 128 paralel sayımlar ve ayrıntılı günlük düzeyi ile destekleyebilir.

Ayrıca, veritabanı [işlem birimi](../sql-database/sql-database-what-is-a-dtu.md) (DTU) kullanım bilgilerine göre veritabanı fiyatlandırma katmanını Azure Portal de ayarlayabilirsiniz.

## <a name="design-for-high-performance"></a>Yüksek performans tasarımı
Azure 'da çalışmak üzere bir SSIS paketi tasarlamak, şirket içi yürütmeye yönelik bir paket tasarlamaktan farklıdır. Aynı pakette birden fazla bağımsız görevi birleştirmek yerine, Azure-SSIS IR daha verimli yürütme için bunları birkaç pakete ayırın. Her paket için bir paket yürütmesi oluşturun, böylece birbirlerinin tamamlanmasını beklemek zorunda kalmaz. Bu yaklaşım, Azure-SSIS tümleştirme çalışma zamanının ölçeklenebilirliğinden faydalanır ve genel üretilen işi geliştirir.

## <a name="next-steps"></a>Sonraki adımlar
Azure-SSIS Integration Runtime hakkında daha fazla bilgi edinin. Bkz. [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
