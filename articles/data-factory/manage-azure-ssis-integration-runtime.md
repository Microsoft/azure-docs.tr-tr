---
title: Azure-SSIS tümleştirme çalışma zamanını yeniden yapılandırma
description: Azure Data Factory bir Azure-SSIS tümleştirme çalışma zamanını zaten sağladıktan sonra yeniden yapılandırma hakkında bilgi edinin.
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/03/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 724411dc12654aec1614230c943923062b334cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100370691"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanını yeniden yapılandırma

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu makalede, mevcut bir Azure-SSIS tümleştirme çalışma zamanının nasıl yeniden yapılandırılacağı açıklanır. Azure Data Factory içinde bir Azure-SSIS tümleştirme çalışma zamanı (IR) oluşturmak için bkz. [Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI) 
Data Factory Kullanıcı arabirimini kullanarak bir Azure-SSIS IR durdurabilir, düzenleyebilir/yeniden yapılandırabilir veya silebilirsiniz. 

1. Veri fabrikanızın giriş sayfasında **yazar & izleyici** kutucuğunu seçerek Data Factory Kullanıcı arabirimini açın.
2. **Bağlantılar** bölmesini göstermek Için, **ana**, **düzenleme** ve hub 'ları **izlemek** üzere hub 'ı **Yönet** ' i seçin.

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Bir Azure-SSIS IR yeniden yapılandırmak için
Hub 'ı **Yönet** ' in **Bağlantılar** bölmesinde, **tümleştirme çalışma zamanları** sayfasına geçin ve **Yenile**' yi seçin. 

   ![Bağlantılar bölmesi](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR adını seçerek düzenleyebilir/yeniden yapılandırabilirsiniz. Ayrıca, Azure-SSIS IR izlemek/başlatmak/durdurmak/silmek için ilgili düğmeleri seçebilir, Azure-SSIS IR üzerinde çalışacak SSIS paketi yürütme etkinliğinin bulunduğu bir ADF işlem hattını otomatik olarak oluşturabilir ve Azure-SSIS IR JSON kodunu/yükünü görüntüleyebilirsiniz.  Azure-SSIS IR düzenlemeniz/silmeniz, yalnızca durdurulduğunda yapılabilir.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS Integration Runtime 'ın bir örneğini sağlayıp başlattıktan sonra, bir dizi `Stop`  -  `Set`  -  `Start` PowerShell cmdlet 'ini art arda çalıştırarak yeniden yapılandırabilirsiniz. Örneğin, aşağıdaki PowerShell betiği, Azure-SSIS tümleştirme çalışma zamanı örneği için ayrılan düğüm sayısını beş olarak değiştirir.

### <a name="reconfigure-an-azure-ssis-ir"></a>Azure-SSIS IR yeniden yapılandırın

1. İlk olarak, [stop-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet 'Ini kullanarak Azure-SSIS tümleştirme çalışma zamanını durdurun. Bu komut tüm düğümlerini serbest bırakır ve faturalandırmayı durduruyor.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
   ```
2. Sonra, [set-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet 'ini kullanarak Azure-SSIS IR yeniden yapılandırın. Aşağıdaki örnek komut, Azure-SSIS tümleştirme çalışma zamanını beş düğüme ölçeklendirir.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
   ```  
3. Ardından, [Start-AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet 'Ini kullanarak Azure-SSIS tümleştirme çalışma zamanını başlatın. Bu komut, SSIS paketlerini çalıştırmak için tüm düğümlerini ayırır.   

   ```powershell
   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
   ```

### <a name="delete-an-azure-ssis-ir"></a>Azure-SSIS IR silme
1. İlk olarak, veri fabrikanızın altındaki tüm mevcut Azure SSIS IRS ' nı listeleyin.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
   ```
2. Ardından, veri Fabrikanızdaki tüm mevcut Azure SSIS IRS 'yi durdurun.

   ```powershell
   Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
3. Ardından, veri Fabrikanızdaki tüm mevcut Azure SSIS IRS 'yi tek tek kaldırın.

   ```powershell
   Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
   ```
4. Son olarak, veri fabrikanızı kaldırın.

   ```powershell
   Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
   ```
5. Yeni bir kaynak grubu oluşturduysanız, kaynak grubunu kaldırın.

   ```powershell
   Remove-AzResourceGroup -Name $ResourceGroupName -Force 
   ```

## <a name="next-steps"></a>Sonraki adımlar
Azure-SSIS çalışma zamanı hakkında daha fazla bilgi için aşağıdaki konulara bakın: 

- [Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede, Azure-SSIS IR dahil olmak üzere genel olarak tümleştirme çalışma zamanları hakkında kavramsal bilgiler verilmektedir. 
- [Öğretici: SSIS paketlerini Azure’a dağıtma](./tutorial-deploy-ssis-packages-azure.md). Bu makalede, Azure-SSIS IR oluşturmak ve SSIS kataloğunu barındırmak için Azure SQL veritabanı 'nı kullanması için adım adım yönergeler sağlanmaktadır. 
- [Nasıl yapılır: Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticide genişletilir ve Azure SQL yönetilen örneği kullanımı ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar. 
- [Azure-SSIS IR’yi bir sanal ağa ekleyin](join-azure-ssis-integration-runtime-virtual-network.md). Bu makale Azure-SSIS IR’yi bir Azure sanal ağına ekleme hakkında kavramsal bilgiler sağlar. Ayrıca, Azure portalını kullanarak Azure-SSIS IR’nin sanal ağa katılmasını sağlayacak şekilde sanal ağı yapılandırma adımlarını da sunar. 
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede bir Azure-SSIS IR ile ilgili bilgileri ve döndürülen bilgilerdeki durumların açıklamalarını alma işlemi gösterilmektedir.