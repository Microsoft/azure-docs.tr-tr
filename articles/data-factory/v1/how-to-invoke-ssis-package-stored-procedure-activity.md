---
title: Azure Data Factory saklı yordam etkinliğini kullanarak SSIS paketini çağırma
description: Bu makalede, saklı yordam etkinliğini kullanarak bir Azure Data Factory işlem hattından bir SQL Server Integration Services (SSIS) paketinin nasıl çağırılacağını açıklanmaktadır.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: f0a63db95d0948951ec98159af381e0a04ac91ff
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666390"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory saklı yordam etkinliğini kullanarak bir SSIS paketini çağırma
Bu makalede, bir saklı yordam etkinliği kullanarak bir Azure Data Factory işlem hattından bir SSIS paketinin nasıl çağırılacağını açıklanmaktadır. 

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [içindeki saklı yordam kullanarak SSIS paketlerini çağırma](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Ön koşullar

### <a name="azure-sql-database"></a>Azure SQL Veritabanı 
Bu makaledeki izlenecek yol, SSIS kataloğunu barındıran bir Azure SQL veritabanı kullanır. Azure SQL veritabanı yönetilen örneği de kullanabilirsiniz.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS tümleştirme çalışma zamanı oluşturma
Öğreticideki Adım adım yönergeleri izleyerek bir Azure-SSIS tümleştirme çalışma zamanı oluşturun [: SSIS paketlerini dağıtma](../tutorial-create-azure-ssis-runtime-portal.md). Azure-SSIS tümleştirme çalışma zamanı oluşturmak için Data Factory sürüm 1 ' i kullanamazsınız. 

## <a name="azure-powershell"></a>Azure PowerShell
Bu bölümde, bir SSIS paketini çağıran saklı yordam etkinliği ile Data Factory işlem hattı oluşturmak için Azure PowerShell kullanırsınız.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-az-ps) konusundaki yönergeleri izleyerek en güncel Azure PowerShell modüllerini yükleyin.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Aşağıdaki yordam bir veri fabrikası oluşturmak için adımlar sağlar. Bu veri fabrikasında saklı yordam etkinliği ile bir işlem hattı oluşturursunuz. Saklı yordam etkinliği, SSIS paketinizi çalıştırmak için SSSıSDB veritabanında bir saklı yordam yürütür.

1. Daha sonra PowerShell komutlarında kullanacağınız kaynak grubu adı için bir değişken tanımlayın. Aşağıdaki komut metnini PowerShell'e kopyalayın [Azure kaynak grubu](../../azure-resource-manager/resource-group-overview.md) için çift tırnak içinde bir ad belirtin ve ardından komutu çalıştırın. Örneğin: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$ResourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın
2. Azure kaynak grubunu oluşturmak için aşağıdaki komutu çalıştırın: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Kaynak grubu zaten varsa, üzerine yazılmasını istemeyebilirsiniz. `$ResourceGroupName` değişkenine farklı bir değer atayın ve komutu yeniden çalıştırın. 
3. Veri fabrikasının adı için bir değişken tanımlayın. 

    > [!IMPORTANT]
    >  Veri fabrikasının adını genel olarak benzersiz olacak şekilde güncelleştirin. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Veri Fabrikası oluşturmak için, $ResGrp değişkeninden location ve ResourceGroupName özelliğini kullanarak aşağıdaki **New-AzDataFactory** cmdlet 'ini çalıştırın: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Aşağıdaki noktalara dikkat edin:

* Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, **katkıda bulunan** veya **sahip** rollerinin üyesi ya da bir Azure aboneliğinin **yöneticisi** olmalıdır.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti oluşturma
SSIS kataloğunu barındıran Azure SQL veritabanınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturun. Data Factory, SSıSDB veritabanına bağlanmak için bu bağlantılı hizmette bilgileri kullanır ve bir SSIS paketini çalıştırmak için bir saklı yordam yürütür. 

1. **C:\adf\runssispackage** klasöründe aşağıdaki Içeriğe sahip **Azuressındatabaselinkedservice. JSON** adlı bir JSON dosyası oluşturun: 

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;ServerName&gt;, &lt;Kullanıcı adı&gt;@&lt;ServerName&gt; ve &lt;Password&gt; değerlerini Azure SQL veritabanınızın değerleriyle değiştirin.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. **Azure PowerShell**, **C:\adf\runssispackage** klasörüne geçin.
3. **New-AzDataFactoryLinkedService** cmdlet 'ini çalıştırarak bağlı hizmeti oluşturun: **Azuressqldatabaselinkedservice**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Çıktı veri kümesi oluşturma
Bu çıktı veri kümesi, işlem hattının zamanlamasını yönlendiren bir kukla veri kümesidir. Sıklık 'in Hour ve Interval değeri 1 olarak ayarlandığını unutmayın. Bu nedenle işlem hattı, işlem hattı başlangıç ve bitiş zamanları içinde bir saat sonra çalışır. 

1. Aşağıdaki içeriğe sahip bir OutputDataset. JSON dosyası oluşturun: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Bir veri kümesi oluşturmak için **New-AzDataFactoryDataset** cmdlet 'ini çalıştırın. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Saklı yordam etkinliği ile işlem hattı oluşturma 
Bu adımda, saklı yordam etkinliği ile bir işlem hattı oluşturacaksınız. Etkinlik, SSIS paketinizi çalıştırmak için sp_executesql saklı yordamını çağırır. 

1. **C:\adf\runssispackage** klasöründe aşağıdaki Içeriğe sahip **Mypipeline. JSON** adlı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;klasör adı&gt;, &lt;proje adı&gt;, &lt;paket adı&gt;, SSIS kataloğunda klasör, proje ve paket adlarıyla değiştirin.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. **Runssispackagepipeline**işlem hattını oluşturmak için **New-AzDataFactoryPipeline** cmdlet 'ini çalıştırın.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. İşlem hattının çıkış tablosu olan * * çıkış veri kümesinin tüm dilimlerinin ayrıntılarını almak için **Get-AzDataFactorySlice** ' i çalıştırın.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Burada belirttiğiniz StartDateTime ile JSON işlem hattında belirtilen başlangıç zamanıyla aynı olmasına özen gösterin. 
1. Belirli bir dilim için etkinlik çalıştırmalarının ayrıntılarını almak için **Get-AzDataFactoryRun** komutunu çalıştırın.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Dilimi **Hazır** durumunda veya **Başarısız** durumunda görene kadar bu cmdlet’i çalışır halde tutun. 

    Paketin yürütüldüğünü doğrulamak için, Azure SQL sunucunuzdaki SSıSDB veritabanında aşağıdaki sorguyu çalıştırabilirsiniz. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Sonraki adımlar
Saklı yordam etkinliği hakkında ayrıntılı bilgi için [saklı yordam etkinliği](data-factory-stored-proc-activity.md) makalesine bakın.

