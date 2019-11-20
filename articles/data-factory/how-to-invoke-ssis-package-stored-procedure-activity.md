---
title: Saklı yordam etkinliği ile SSIS paketini çalıştırma-Azure
description: Bu makalede, saklı yordam etkinliği kullanılarak bir Azure Data Factory işlem hattında SQL Server Integration Services (SSIS) paketinin nasıl çalıştırılacağı açıklanmaktadır.
services: data-factory
documentationcenter: ''
author: swinarko
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 3bfef0d787d8289055ab80e2ac30408dd7a13fb4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73673758"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Azure Data Factory saklı yordam etkinliği ile bir SSIS paketi çalıştırın
Bu makalede, bir Azure Data Factory Işlem hattındaki bir SSIS paketinin saklı yordam etkinliği kullanılarak nasıl çalıştırılacağı açıklanmaktadır. 

## <a name="prerequisites"></a>Ön koşullar

### <a name="azure-sql-database"></a>Azure SQL Veritabanı 
Bu makaledeki izlenecek yol, SSIS kataloğunu barındıran bir Azure SQL veritabanı kullanır. Azure SQL veritabanı yönetilen örneği de kullanabilirsiniz.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS tümleştirme çalışma zamanı oluşturma
Öğreticideki Adım adım yönergeleri izleyerek bir Azure-SSIS tümleştirme çalışma zamanı oluşturun [: SSIS paketlerini dağıtma](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory Kullanıcı arabirimi (Azure portal)
Bu bölümde, bir SSIS paketini çağıran saklı yordam etkinliği ile Data Factory işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini kullanırsınız.

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
İlk adım Azure portal kullanarak bir veri fabrikası oluşturmaktır. 

1. **Microsoft Edge** veya **Google Chrome** web tarayıcısını açın. Şu anda Data Factory kullanıcı arabirimi yalnızca Microsoft Edge ve Google Chrome web tarayıcılarında desteklenmektedir.
2. [Azure portalına](https://portal.azure.com) gidin. 
3. Soldaki menüde **Yeni**, **Veri + Analiz** ve **Data Factory** öğesine tıklayın. 
   
   ![Yeni->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. **Yeni veri fabrikası** sayfasında **ad** için **ADFTutorialDataFactory** girin. 
      
     ![Yeni veri fabrikası sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Azure data factory adı **küresel olarak benzersiz** olmalıdır. Ad alanı için aşağıdaki hatayı görürseniz veri fabrikasının adını değiştirin (örneğin, adınızADFTutorialDataFactory). Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](naming-rules.md) makalesine bakın.
  
     ![Ad yok - hata](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Veri fabrikasını oluşturmak istediğiniz Azure **aboneliğini** seçin. 
4. **Kaynak Grubu** için aşağıdaki adımlardan birini uygulayın:
     
   - **Var olanı kullan**’ı seçin ve ardından açılır listeden var olan bir kaynak grubu belirleyin. 
   - **Yeni oluştur**’u seçin ve bir kaynak grubunun adını girin.   
         
     Kaynak grupları hakkında daha fazla bilgi için bkz. [Azure kaynaklarınızı yönetmek için kaynak gruplarını kullanma](../azure-resource-manager/resource-group-overview.md).  
4. **Sürüm** için **V2**'yi seçin.
5. Data factory için **konum** seçin. Açılan listede yalnızca Data Factory tarafından desteklenen konumlar görüntülenir. Veri fabrikası tarafından kullanılan veri depoları (Azure Depolama, Azure SQL Veritabanı, vb.) ve işlemler (HDInsight, vb.) başka konumlarda olabilir.
6. **Panoya sabitle**’yi seçin.     
7. **Oluştur**'a tıklayın.
8. Panoda şu kutucuğu ve üzerinde şu durumu görürsünüz: **Veri fabrikası dağıtılıyor**. 

     ![veri fabrikası dağıtılıyor kutucuğu](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Oluşturma işlemi tamamlandıktan sonra, resimde gösterildiği gibi **Data Factory** sayfasını görürsünüz.
   
     ![Data factory giriş sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Azure Data Factory kullanıcı arabirimi (UI) uygulamasını ayrı bir sekmede açmak için **Yazar ve İzleyici** kutucuğuna tıklayın. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Saklı yordam etkinliği ile işlem hattı oluşturma
Bu adımda, bir işlem hattı oluşturmak için Data Factory Kullanıcı arabirimini kullanırsınız. İşlem hattına saklı yordam etkinliği ekleyin ve sp_executesql saklı yordamını kullanarak SSIS paketini çalıştıracak şekilde yapılandırın. 

1. Başlarken sayfasında işlem **hattı oluştur**' a tıklayın: 

    ![Başlarken sayfası](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. **Etkinlikler** araç kutusunda **genel**' i genişletin ve **saklı yordam** etkinliğini sürükleyerek işlem hattı tasarımcısının yüzeyine bırakın. 

    ![Saklı yordam etkinliğini sürükleyip bırakma](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. Saklı yordam etkinliğinin Özellikler penceresinde **SQL hesabı** sekmesine geçin ve **+ Yeni**' ye tıklayın. SSIS kataloğunu (SSıDB veritabanı) barındıran Azure SQL veritabanı ile bir bağlantı oluşturursunuz. 
   
    ![Yeni bağlı hizmet düğmesi](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. **Yeni Bağlı Hizmet** penceresinde aşağıdaki adımları izleyin: 

    1. **Tür**IÇIN **Azure SQL veritabanı** ' nı seçin.
    2. `SSISDB` veritabanını barındıran Azure SQL veritabanı 'na bağlanmak için **varsayılan** Azure Integration Runtime seçin.
    3. **Sunucu adı** alanı IÇIN SSISDB veritabanını BARıNDıRAN Azure SQL veritabanını seçin.
    4. **Veritabanı adı**Için **SSISDB** 'yi seçin.
    5. **Kullanıcı adı**için, veritabanına erişimi olan kullanıcının adını girin.
    6. **Parola**için kullanıcının parolasını girin. 
    7. **Bağlantıyı Sına** düğmesine tıklayarak veritabanına bağlantıyı test edin.
    8. Bağlı hizmeti **Kaydet** düğmesine tıklayarak kaydedin. 

        ![Azure SQL Veritabanı bağlı hizmeti](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. Özellikler penceresinde, **SQL hesabı** sekmesinden **saklı yordam** sekmesine geçin ve aşağıdaki adımları uygulayın: 

    1. **Düzenle**’yi seçin. 
    2. **Saklı yordam adı** alanı Için `sp_executesql`girin. 
    3. **Saklı yordam parametreleri** bölümünde **+ Yeni** ' ye tıklayın. 
    4. Parametrenin **adı** için **stmt**girin. 
    5. Parametre **türü** için **dize**girin. 
    6. Parametresinin **değeri** IÇIN aşağıdaki SQL sorgusunu girin:

        SQL sorgusunda, **klasör_adı**, **Project_Name**ve **package_name** parametrelerinin doğru değerlerini belirtin. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Azure SQL Veritabanı bağlı hizmeti](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. İşlem hattı yapılandırmasını doğrulamak için araç çubuğunda **Doğrula** ' ya tıklayın. **İşlem Hattı Doğrulama Raporu**'nu kapatmak için **>>** seçeneğine tıklayın.

    ![İşlem hattını doğrulama](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Data Factory **Tümünü** Yayımla düğmesine tıklayarak ardışık düzeni yayımlayın. 

    ![Yayımlama](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>İşlem hattını çalıştırma ve izleme
Bu bölümde bir işlem hattı çalıştırması tetiklersiniz ve sonra bunu izleyebilirsiniz. 

1. Bir işlem hattı çalıştırması tetiklemek için araç çubuğunda **Tetikle** ' e tıklayın ve **Şimdi Tetikle**' ye tıklayın. 

    ![Şimdi tetikle](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. **İşlem Hattı Çalıştırma** penceresinde **Son**’u seçin. 
3. Soldaki **İzleyici** sekmesine geçin. İşlem hattı çalıştırmasını ve durumunu diğer bilgilerle birlikte (çalıştırma başlangıç saati gibi) görürsünüz. Görünümü yenilemek için **Yenile**’ye tıklayın.

    ![İşlem hattı çalıştırmaları](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. **Eylemler** sütunundaki **Etkinlik Çalıştırmalarını Görüntüle** bağlantısına tıklayın. İşlem hattının yalnızca bir etkinliği (saklı yordam etkinliği) olduğu için yalnızca bir etkinlik çalıştırması görürsünüz.

    ![Etkinlik çalıştırmaları](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Paketin yürütüldüğünü doğrulamak için, Azure SQL sunucunuzdaki SSıSDB veritabanında aşağıdaki **sorguyu** çalıştırabilirsiniz. 

    ```sql
    select * from catalog.executions
    ```

    ![Paket yürütmelerini doğrulama](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> İşlem hattının zamanlamaya göre (saatlik, günlük, vb.) çalışması için zamanlanmış bir tetikleyici de oluşturabilirsiniz. Bir örnek için bkz. [Data Factory-Data Factory Kullanıcı arabirimi oluşturma](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu bölümde, bir SSIS paketini çağıran saklı yordam etkinliği ile Data Factory işlem hattı oluşturmak için Azure PowerShell kullanırsınız. 

[Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/install-az-ps) konusundaki yönergeleri izleyerek en güncel Azure PowerShell modüllerini yükleyin. 

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Azure-SSIS IR sahip olan aynı veri fabrikasını kullanabilir veya ayrı bir veri fabrikası oluşturabilirsiniz. Aşağıdaki yordam bir veri fabrikası oluşturmak için adımlar sağlar. Bu veri fabrikasında saklı yordam etkinliği ile bir işlem hattı oluşturursunuz. Saklı yordam etkinliği, SSIS paketinizi çalıştırmak için SSSıSDB veritabanında bir saklı yordam yürütür. 

1. Daha sonra PowerShell komutlarında kullanacağınız kaynak grubu adı için bir değişken tanımlayın. Aşağıdaki komut metnini PowerShell'e kopyalayın [Azure kaynak grubu](../azure-resource-manager/resource-group-overview.md) için çift tırnak içinde bir ad belirtin ve ardından komutu çalıştırın. Örneğin: `"adfrg"`. 
   
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

5. Veri Fabrikası oluşturmak için, $ResGrp değişkeninden location ve ResourceGroupName özelliğini kullanarak aşağıdaki **set-AzDataFactoryV2** cmdlet 'ini çalıştırın: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Aşağıdaki noktalara dikkat edin:

* Azure veri fabrikasının adı genel olarak benzersiz olmalıdır. Aşağıdaki hata iletisini alırsanız adı değiştirip yeniden deneyin.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory örnekleri oluşturmak için, Azure’da oturum açarken kullandığınız kullanıcı hesabı, **katkıda bulunan** veya **sahip** rollerinin üyesi ya da bir Azure aboneliğinin **yöneticisi** olmalıdır.
* Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Veritabanı bağlı hizmeti oluşturma
SSIS kataloğunu barındıran Azure SQL veritabanınızı veri fabrikasına bağlamak için bağlı bir hizmet oluşturun. Data Factory, SSıSDB veritabanına bağlanmak için bu bağlantılı hizmette bilgileri kullanır ve bir SSIS paketini çalıştırmak için bir saklı yordam yürütür. 

1. **C:\adf\runssispackage** klasöründe aşağıdaki Içeriğe sahip **Azuressındatabaselinkedservice. JSON** adlı bir JSON dosyası oluşturun: 

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;ServerName&gt;, &lt;Kullanıcı adı&gt;ve &lt;Password&gt; değerlerini Azure SQL veritabanınızın değerleriyle değiştirin.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. **Azure PowerShell**, **C:\adf\runssispackage** klasörüne geçin.

3. Şu bağlı hizmeti oluşturmak için **set-AzDataFactoryV2LinkedService** cmdlet 'ini çalıştırın: **Azuressqldatabaselinkedservice**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Saklı yordam etkinliği ile işlem hattı oluşturma 
Bu adımda, saklı yordam etkinliği ile bir işlem hattı oluşturacaksınız. Etkinlik, SSIS paketinizi çalıştırmak için sp_executesql saklı yordamını çağırır. 

1. **C:\adf\runssispackage** klasöründe aşağıdaki Içeriğe sahip **Runssıspackagepipeline. JSON** adlı bir JSON dosyası oluşturun:

    > [!IMPORTANT]
    > Dosyayı kaydetmeden önce &lt;klasör adı&gt;, &lt;proje adı&gt;, &lt;paket adı&gt;, SSIS kataloğunda klasör, proje ve paket adlarıyla değiştirin. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. **Runssispackagepipeline**işlem hattını oluşturmak için **set-AzDataFactoryV2Pipeline** cmdlet 'ini çalıştırın.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Örnek çıktı aşağıdaki gibidir:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>İşlem hattı çalıştırması oluşturma
İşlem hattını çalıştırmak için **Invoke-AzDataFactoryV2Pipeline** cmdlet 'ini kullanın. Cmdlet, gelecekte izlemek üzere işlem hattı çalıştırma kimliğini döndürür.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

İşlem hattı çalıştırma durumunu, verileri kopyalama işlemi tamamlanıncaya kadar sürekli olarak denetlemek için aşağıdaki PowerShell betiğini çalıştırın. Aşağıdaki betiği kopyalayıp PowerShell penceresine yapıştırın ve ENTER tuşuna basın. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Tetikleyici oluşturma
Önceki adımda, işlem hattını isteğe bağlı olarak çağırdınız. İşlem hattını bir zamanlamaya göre (saatlik, günlük, vb.) çalıştırmak için bir zamanlama tetikleyicisi de oluşturabilirsiniz.

1. **C:\adf\runssispackage** klasöründe aşağıdaki Içeriğe sahip **mytrigger. JSON** adlı bir JSON dosyası oluşturun: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. **Azure PowerShell**, **C:\adf\runssispackage** klasörüne geçin.
3. Tetikleyiciyi oluşturan **set-AzDataFactoryV2Trigger** cmdlet 'ini çalıştırın. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Varsayılan olarak, tetikleyici durdurulmuş durumda olur. **Start-AzDataFactoryV2Trigger** cmdlet 'ini çalıştırarak tetikleyiciyi başlatın. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Tetikleyici, **Get-AzDataFactoryV2Trigger** cmdlet 'ini çalıştırarak başlatıldığını onaylayın. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Sonraki saatten sonra aşağıdaki komutu çalıştırın. Örneğin, geçerli saat 3:25 PM UTC ise, komutu 4 PM UTC 'de çalıştırın. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Paketin yürütüldüğünü doğrulamak için, Azure SQL sunucunuzdaki SSıSDB veritabanında aşağıdaki sorguyu çalıştırabilirsiniz. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Sonraki adımlar
Ardışık düzeni Azure portal kullanarak da izleyebilirsiniz. Adım adım yönergeler için bkz. işlem hattını [izleme](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
