---
title: Azure-SSIS tümleştirme çalışma zamanını yeniden yapılandırın
description: Azure Data Factory bir Azure-SSIS tümleştirme çalışma zamanını zaten sağladıktan sonra yeniden yapılandırma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 09ef8560edc47a6bb12fad782ae3d34987471127
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672547"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Azure-SSIS tümleştirme çalışma zamanını yeniden yapılandırın
Bu makalede, mevcut bir Azure-SSIS tümleştirme çalışma zamanının nasıl yeniden yapılandırılacağı açıklanır. Azure Data Factory içinde bir Azure-SSIS tümleştirme çalışma zamanı (IR) oluşturmak için bkz. [Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Data Factory Kullanıcı Arabirimi (UI) 
Data Factory Kullanıcı arabirimini kullanarak bir Azure-SSIS IR durdurabilir, düzenleyebilir/yeniden yapılandırabilir veya silebilirsiniz. 

1. **Data Factory Kullanıcı arabiriminde**, **Düzenle** sekmesine geçin. Data Factory Kullanıcı arabirimini başlatmak için veri fabrikanızın giriş sayfasında **yazar & İzleyici** ' ye tıklayın.
2. Sol bölmede **Bağlantılar**' a tıklayın.
3. Sağ bölmede **tümleştirme çalışma zamanları**' na geçin. 
4. Tümleştirme çalışma zamanını **durdurmak**, **düzenlemek**veya **silmek** için Eylemler sütunundaki düğmeleri kullanabilirsiniz. **Eylemler** sütunundaki **kod** düğmesi ıNTEGRATION Runtime ile ilişkili JSON tanımını görüntülemenize olanak sağlar.  
    
    ![Azure SSIS IR için Eylemler](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Bir Azure-SSIS IR yeniden yapılandırmak için
1. **Eylemler** sütununda **Durdur** ' a tıklayarak tümleştirme çalışma zamanını durdurun. Liste görünümünü yenilemek için araç çubuğunda **Yenile** ' ye tıklayın. IR durdurulduktan sonra ilk eylemin IR 'yi başlatabilmenizi sağlar. 

    ![Azure SSIS IR için Eylemler-durdurulduktan sonra](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. **Eylemler** sütunundaki **Düzenle** düğmesine tıklayarak IR 'yi düzenleyin/yeniden yapılandırın. **Integration Runtime kurulum** penceresinde ayarları (örneğin, düğüm boyutu, düğüm sayısı veya düğüm başına en fazla paralel yürütme) değiştirin. 
3. IR 'yi yeniden başlatmak için **Eylemler** sütunundaki **Başlat** düğmesine tıklayın.     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS tümleştirme çalışma zamanının bir örneğini sağlayıp başlattıktan sonra, `Start` PowerShell cmdlet 'lerini art arda `Set` - bir dizi `Stop` - çalıştırarak yeniden yapılandırabilirsiniz. Örneğin, aşağıdaki PowerShell betiği, Azure-SSIS tümleştirme çalışma zamanı örneği için ayrılan düğüm sayısını beş olarak değiştirir.

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
- [Öğretici: SSIS paketlerini Azure’a dağıtma](tutorial-create-azure-ssis-runtime-portal.md). Bu makale bir Azure-SSIS IR oluşturmaya ilişkin adım adım yönergeler sağlar ve SSIS kataloğunu barındırmak için bir Azure SQL veritabanı kullanır. 
- [Nasıl yapılır: Azure-SSIS tümleştirme çalışma zamanı oluşturma](create-azure-ssis-integration-runtime.md). Bu makale öğreticiye genişleterek Azure SQL veritabanı yönetilen örneği kullanımı ve IR 'yi bir sanal ağa katma hakkında yönergeler sağlar. 
- [Azure-SSIS IR’yi bir sanal ağa ekleyin](join-azure-ssis-integration-runtime-virtual-network.md). Bu makale Azure-SSIS IR’yi bir Azure sanal ağına ekleme hakkında kavramsal bilgiler sağlar. Ayrıca, Azure portalını kullanarak Azure-SSIS IR’nin sanal ağa katılmasını sağlayacak şekilde sanal ağı yapılandırma adımlarını da sunar. 
- [Azure-SSIS IR’yi izleme](monitor-integration-runtime.md#azure-ssis-integration-runtime). Bu makalede bir Azure-SSIS IR ile ilgili bilgileri ve döndürülen bilgilerdeki durumların açıklamalarını alma işlemi gösterilmektedir. 
 
