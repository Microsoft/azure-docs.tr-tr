---
title: İlk Data Factory 'nizi derleme (PowerShell)
description: Bu öğreticide Azure PowerShell kullanarak örnek bir Azure Data Factory işlem hattı oluşturursunuz.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: 0209b004ba54417a26cd41716687d328ec1431cf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682988"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Öğretici: Azure PowerShell kullanarak ilk Azure data factory’nizi derleme
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager Şablonu](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory'nin geçerli sürümünü kullanıyorsanız [Hızlı Başlangıç: Azure Data Factory'yi kullanarak veri fabrikası oluşturma](../quickstart-create-data-factory-powershell.md) konusunu inceleyin.

Bu makalede, ilk Azure data factory’nizi oluşturmak için Azure PowerShell kullanırsınız. Diğer araçları/SDK’ları kullanarak öğreticiyi uygulamak için açılır listedeki seçeneklerden birini belirleyin.

Bu öğreticideki işlem hattı bir etkinlik içerir: **HDInsight Hive etkinliği**. Bu etkinlik, Azure HDInsight kümesi üzerinde çıkış verileri üretmek üzere giriş verilerini dönüştüren bir hive betiği çalıştırır. İşlem hattı, belirtilen başlangıç ve bitiş saatleri arasında ayda bir kez çalışacak şekilde zamanlanmıştır. 

> [!NOTE]
> Bu öğreticideki veri işlem hattı, çıkış verileri üretmek üzere giriş verilerini dönüştürür. Bir kaynak veri deposundan hedef veri deposuna verileri kopyalamaz. Azure Data Factory kullanarak verileri kopyalama öğreticisi için bkz. [Öğretici: Blob Depolama’dan SQL Veritabanı’na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Data Factory'de zamanlama ve yürütme](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [Öğreticiye Genel Bakış](data-factory-build-your-first-pipeline.md) makalesinin tamamını okuyun ve **ön koşul** adımlarını tamamlayın.
* Bilgisayarınıza Azure PowerShell’in en son sürümünü yüklemek için [Azure PowerShell’i yükleme ve yapılandırma](/powershell/azure/overview) makalesindeki yönergeleri izleyin.
* (isteğe bağlı) Bu makalede, tüm Data Factory cmdlet'lerini kapsamaz. Data Factory cmdlet’leri hakkında kapsamlı bilgi için bkz. [Data Factory Cmdlet Başvurusu](/powershell/module/az.datafactory).

## <a name="create-data-factory"></a>Veri fabrikası oluşturma
Bu adımda **FirstDataFactoryPSH** adlı bir Azure Data Factory oluşturmak için Azure PowerShell’i kullanırsınız. Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Örneğin, bir kaynaktan hedef veri deposuna veri kopyalama amaçlı bir Kopyalama Etkinliği ve giriş verilerini dönüştürmek üzere Hive betiği çalıştırma amaçlı bir HDInsight Hive etkinliği. Bu adımda data factory oluşturmayla başlayalım.

1. Azure PowerShell’i başlatın ve aşağıdaki komutu çalıştırın. Bu öğreticide sonuna kadar Azure PowerShell’i açık tutun. Kapatıp yeniden açarsanız, bu komutları yeniden çalıştırmanız gerekir.
   * Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin.
     ```PowerShell
     Connect-AzAccount
     ```    
   * Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın.
     ```PowerShell
     Get-AzSubscription  
     ```
   * Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. Bu abonelik Azure portalında kullanılanla aynı olmalıdır.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Aşağıdaki komutu kullanarak **ADFTutorialResourceGroup** adlı bir Azure kaynak grubu oluşturun:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Bu öğreticideki adımlardan bazıları ADFTutorialResourceGroup adlı kaynak grubunu kullandığınızı varsayar. Farklı bir kaynak grubu kullanıyorsanız, bu öğreticide ADFTutorialResourceGroup yerine onu kullanmanız gerekir.
3. **Firstdatafactorypsh**adlı bir veri fabrikası oluşturan **New-azdatafactory** cmdlet 'ini çalıştırın.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Aşağıdaki noktalara dikkat edin:

* Azure Data Factory adı küresel olarak benzersiz olmalıdır. Şu hatayı alırsanız: **“FirstDataFactoryPSH” veri fabrikası adı yok**, adı değiştirin (örneğin, yournameFirstDataFactoryPSH). Bu öğreticide adımları uygularken ADFTutorialFactoryPSH yerine bu adı kullanın. Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.
* Data Factory örnekleri oluşturmak için, Azure aboneliğinde katılımcı/yönetici rolünüz olmalıdır
* Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş ve herkese görünür hale gelmiş olabilir.
* Şu hatayı alırsanız: "**Abonelik, Microsoft.DataFactory ad alanını kullanacak şekilde kaydedilmemiş**", aşağıdakilerden birini yapın ve yeniden yayımlamayı deneyin:

  * Azure PowerShell’de Data Factory sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Data Factory sağlayıcısının kayıtlı olduğunu onaylamak için aşağıdaki komutu çalıştırabilirsiniz:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Azure aboneliğini kullanarak [Azure portalında](https://portal.azure.com) oturum açın ve Data Factory dikey penceresine gidin (ya da) Azure portalında bir data factory oluşturun. Bu eylem sağlayıcıyı sizin için otomatik olarak kaydeder.

İşlem hattı oluşturmadan önce, öncelikle birkaç Data Factory varlığı oluşturmanız gerekir. Önce veri depolarını/işlemleri veri deponuza bağlamak için bağlı hizmetler oluşturun, bağlı veri depolarında giriş/çıkış verilerini temsil etmek üzere giriş ve çıkış veri kümeleri tanımlayın, sonra da bu veri kümelerini kullanan bir etkinlikle işlem hattını oluşturun.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu adımda, Azure Depolama hesabınızı ve isteğe bağlı Azure HDInsight kümesini data factory’nize bağlarsınız. Azure Depolama hesabı, bu örnekteki işlem hattı için girdi ve çıktı verilerini tutar. HDInsight bağlı hizmeti, bu örnekteki işlem hattının etkinliğinde belirtilen Hive betiğini çalıştırmak için kullanılır. Senaryonuzda hangi veri deposu/işlem hizmetlerinin kullanılacağını belirleyin ve bağlı hizmetler oluşturarak bu hizmetleri data factory’ye bağlayın.

### <a name="create-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma
Bu adımda, Azure Depolama hesabınızı veri fabrikanıza bağlarsınız. Giriş/çıkış verilerini ve HQL betik dosyasını depolamak için aynı Azure Depolama hesabını kullanırsınız.

1. C:\ADFGetStarted klasöründe aşağıdaki içeriğe sahip StorageLinkedService.json adlı bir JSON dosyası oluşturun: Henüz yoksa ADFGetStarted klasörünü oluşturun.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    **accountname** sözcüğünü Azure depolama hesabınızın adıyla, **accountkey** sözcüğünü de Azure depolama hesabının erişim anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alabileceğinizi öğrenmek için [Depolama hesabınızı yönetme](../../storage/common/storage-account-manage.md#access-keys) sayfasındaki depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma bilgilerine bakın.
2. Azure PowerShell’de ADFGetStarted klasörüne geçin.
3. Bağlı bir hizmet oluşturan **New-AzDataFactoryLinkedService** cmdlet 'ini kullanabilirsiniz. Bu öğreticide kullandığınız bu cmdlet ve diğer Data Factory cmdlet’lerini *ResourceGroupName* ve *DataFactoryName* parametreleri için değerleri geçirmeniz gerekir. Alternatif olarak, bir **DataFactory** nesnesini almak ve bir cmdlet 'i her çalıştırdığınızda *Resourcegroupname* ve *datafactoryname* yazmadan nesneyi geçirmek için **Get-azdatafactory** ' yi kullanabilirsiniz. **Get-AzDataFactory** cmdlet 'inin çıkışını bir **$df** değişkenine atamak için aşağıdaki komutu çalıştırın.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Şimdi, bağlantılı **StorageLinkedService** hizmetini oluşturan **New-AzDataFactoryLinkedService** cmdlet 'ini çalıştırın.

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    **Get-AzDataFactory** cmdlet 'ini çalıştırıp **$df** değişkenine çıkış atadıysanız, *Resourcegroupname* ve *datafactoryname* parametrelerinin değerlerini aşağıdaki gibi belirtmeniz gerekir.

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Öğreticinin ortasında Azure PowerShell kapatırsanız, Öğreticiyi tamamladıktan sonra Azure PowerShell başlattığınızda **Get-AzDataFactory** cmdlet 'ini çalıştırmanız gerekir.

### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight bağlı hizmeti oluşturma
Bu adımda, isteğe bağlı HDInsight kümesini data factory’nize bağlarsınız. HDInsight kümesi çalışma zamanında otomatik olarak oluşturulur ve işlenmesi bittiğinde ve belirtilen sürede boşta kalırsa silinir. İsteğe bağlı HDInsight kümesi yerine kendi HDInsight kümenizi kullanabilirsiniz. Ayrıntılar için bkz. [İşlem Bağlı Hizmetleri](data-factory-compute-linked-services.md).

1. **C:\ADFGetStarted** klasöründe aşağıdaki içeriğe sahip **HDInsightOnDemandLinkedService**.json adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

   | Özellik | Açıklama |
   |:--- |:--- |
   | clusterSize |HDInsight kümesi boyutunu belirtir. |
   | timeToLive |Silinmeden önce HDInsight kümesinin boşta kalma süresini belirtir. |
   | linkedServiceName |HDInsight tarafından oluşturulan günlükleri depolamak için kullanılan depolama hesabını belirtir |

    Aşağıdaki noktalara dikkat edin:

   * Data Factory, sizin için JSON ile **Linux tabanlı** bir HDInsight kümesi oluşturur. Ayrıntılar için bkz. [İsteğe Bağlı HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
   * İsteğe bağlı HDInsight kümesi yerine **kendi HDInsight kümenizi** kullanabilirsiniz. Ayrıntılar için bkz. [HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
   * HDInsight kümesi JSON’da belirttiğiniz blob depolamada (**linkedServiceName**) bir **varsayılan kapsayıcı** oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmeti kullanıldığında, mevcut canlı bir küme olmadığı sürece bir dilim her işlendiğinde bir HDInsight kümesi oluşturulur (**timeToLive**). Küme, işlem tamamlandığında otomatik olarak silinir.

       Daha fazla dilim işlendikçe, Azure blob depolamanızda çok sayıda kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcıların adları şu deseni izler: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp". Azure blob depolamada kapsayıcı silmek için [Microsoft Storage Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

     Ayrıntılar için bkz. [İsteğe Bağlı HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
2. Hdınsightondemandlinkedservice adlı bağlı hizmeti oluşturan **New-AzDataFactoryLinkedService** cmdlet 'ini çalıştırın.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, Hive işlenmesi için girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturursunuz. Bu veri kümeleri, bu öğreticide daha önce oluşturduğunuz **StorageLinkedService** öğesine başvurur. Bağlı hizmet Azure Storage hesabını belirtirken, veri kümeleri de girdi ve çıktı verilerini tutan depolama biriminde kapsayıcı, klasör, dosya adı belirtir.

### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma
1. **C:\ADFGetStarted** klasöründe aşağıdaki içeriğe sahip **InputTable.json** adlı bir JSON dosyası oluşturun:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Bu JSON, işlem hattındaki bir etkinliğin girdi verilerini temsil eden **AzureBlobInput** adlı veri kümesini tanımlamaktadır. Ek olarak, girdi verilerinin **adfgetstarted** adlı blob kapsayıcısında ve **inputdata** adlı klasörde bulunduğunu belirtir.

    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

   | Özellik | Açıklama |
   |:--- |:--- |
   | type |Veriler Azure blob depolamada yer aldığından type özelliği AzureBlob olarak ayarlanmıştır. |
   | linkedServiceName |daha önce oluşturduğunuz StorageLinkedService’e başvurur. |
   | fileName |Bu özellik isteğe bağlıdır. Bu özelliği atarsanız, tüm folderPath dosyaları alınır. Bu durumda, yalnızca input.log işlenir. |
   | type |Günlük dosyaları metin biçiminde olduğundan TextFormat kullanacağız. |
   | columnDelimiter |Günlük dosyalarındaki sütunlar virgül (,) ile ayrılmıştır. |
   | frequency/interval |frequency Ay, interval de 1 olarak ayarlanmıştır; girdi dilimlerinin aylık olarak kullanılabileceğini belirtir. |
   | external |bu özellik, girdi verileri Data Factory hizmetiyle oluşturulmadıysa true olarak ayarlanır. |
2. Data Factory veri kümesi oluşturmak için Azure PowerShell’de şu komutu çalıştırın:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Çıktı veri kümesi oluşturma
Şimdi, Azure Blob depolamada depolanan çıktı verilerini göstermek için çıktı veri kümesi oluşturursunuz.

1. **C:\ADFGetStarted** klasöründe aşağıdaki içeriğe sahip **OutputTable.json** adlı bir JSON dosyası oluşturun:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Bu JSON, işlem hattındaki bir etkinliğin çıktı verilerini temsil eden **AzureBlobOutput** adlı veri kümesini tanımlamaktadır. Ek olarak, sonuçların **adfgetstarted** adlı blob kapsayıcısında ve **partitioneddata** adlı klasörde depolandığını belirtir. Burada, **availability** bölümü çıktı veri kümesinin aylık tabanda oluşturulduğunu belirtiyor.
2. Data Factory veri kümesi oluşturmak için Azure PowerShell’de şu komutu çalıştırın:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>İşlem hattı oluşturma
Bu adımda, **HDInsightHive** etkinliğiyle ilk işlem hattınızı oluşturursunuz. Girdi diliminin ayda bir (frequency: Month, interval: 1) kullanılabilir, çıktı dilimi ayda bir oluşturulur ve etkinlik zamanlayıcı özelliği de ayda bir olacak şekilde ayarlanır. Çıktı veri kümesi ve etkinlik zamanlayıcı ayarlarının eşleşmesi gerekir. Şu anda, çıktı veri kümesi zamanlamayı yönetendir; bu nedenle etkinlik hiçbir çıktı oluşturmasa bile sizin bir çıktı veri kümesi oluşturmanız gerekir. Etkinlik herhangi bir girdi almazsa, girdi veri kümesi oluşturma işlemini atlayabilirsiniz. Aşağıdaki JSON’da kullanılan özellikler bu bölümün sonunda anlatılmaktadır.

1. C:\ADFGetStarted klasöründe aşağıdaki içeriğe sahip MyFirstPipelinePSH.json adlı bir JSON dosyası oluşturun:

   > [!IMPORTANT]
   > **storageaccountname**’i JSON’daki depolama adınızla değiştirin.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    JSON parçacığında, HDInsight kümesinde Veri işleyecek Hive’ı kullanan etkinlikten oluşmuş bir işlem hattı oluşturuyorsunuz.

    **partitionweblogs.hql** Hive betik dosyası Azure depolama hesabında (scriptLinkedService tarafından belirtilen **StorageLinkedService** adıyla) ve **adfgetstarted** kapsayıcısındaki **betik** klasöründe depolanır.

    Burada, **defines** bölümü hive betiğine Hive yapılandırma değerleri olarak (örn., ${hiveconf:inputtable}, ${hiveconf:partitionedtable}) geçirilecek çalışma zamanı ayarlarını belirtmek için kullanılır.

    İşlem hattının **start** ve **end** özellikleri işlem hattının etkin dönemini belirtir.

    JSON etkinliğinde, Hive betiğinin **linkedServiceName** – **HDInsightOnDemandLinkedService** tarafından belirtilen işlemde çalışacağını belirtirsiniz.

   > [!NOTE]
   > Örnekte kullanılan JSON özellikleri hakkında ayrıntılı bilgi için [Azure Data Factory'deki işlem hatları ve etkinlikler](data-factory-create-pipelines.md) sayfasındaki "JSON İşlem Hatları" bölümüne bakın.

2. Azure blob depolamada **adfgetstarted/inputdata** klasöründeki **input.log** dosyasını gördüğünüzü doğrulayın ve işlem hattına dağıtmak için aşağıdaki komutu çalıştırın. **start** ve **end** zamanları geçmişe ayarlanmış ve **isPaused** yanlış olarak ayarlanmış olduğundan işlem hattı (işlem hattında etkinlik) dağıtıldıktan hemen sonra çalışır.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Tebrikler, Azure PowerShell kullanarak ilk işlem hattınızı başarıyla oluşturdunuz.

## <a name="monitor-pipeline"></a>İşlem hattını izleme
Bu adımda, Azure data factory’de neler olduğunu izlemek için Azure PowerShell kullanırsınız.

1. **Get-AzDataFactory** komutunu çalıştırın ve çıktıyı bir **$df** değişkenine atayın.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. İşlem hattının çıkış tablosu olan **Empsqltable**' ın tüm dilimlerinin ayrıntılarını almak için **Get-AzDataFactorySlice** komutunu çalıştırın.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Burada belirttiğiniz StartDateTime ile JSON işlem hattında belirtilen başlangıç zamanıyla aynı olmasına özen gösterin. Örnek çıktı aşağıdaki gibidir:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Belirli bir dilim için etkinlik çalıştırmalarının ayrıntılarını almak için **Get-AzDataFactoryRun** komutunu çalıştırın.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Örnek çıktı aşağıdaki gibidir: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Dilimi **Hazır** durumunda veya **Başarısız** durumunda görene kadar bu cmdlet’i çalışır halde tutun. Dilim Hazır durumunda olduğunda, çıktı verileri için blob depolamanızın **adfgetstarted** klasöründe **partitioneddata** klasörünü denetleyin.  İsteğe bağlı HDInsight kümesinin oluşturulması genellikle biraz zaman alır.

    ![çıktı verileri](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> İsteğe bağlı HDInsight kümesinin oluşturulması genellikle biraz zaman alır (yaklaşık 20 dakika). Bu nedenle, işlem hattının dilimi işlemesi için **yaklaşık 30 dakika** bekleyin.
>
> Dilim başarıyla işlendiğinde girdi dosyası silinir. Bu nedenle, dilimi yeniden çalıştırmak veya öğreticiyi yeniden uygulamak isterseniz girdi dosyasını (input.log) adfgetstarted kapsayıcısının inputdata klasörüne yükleyin.
>
>

## <a name="summary"></a>Özet
Bu öğreticide, HDInsight hadoop kümesindeki Hive betiği çalıştırılarak verileri işlemek için bir Azure data factory oluşturdunuz. Aşağıdaki adımları uygulamak için Azure Portal’da Data Factory Düzenleyici’yi kullandınız:

1. Oluşturulan Azure **data factory**.
2. Oluşturulan iki **bağlı hizmet**:
   1. Girdi/çıktı dosyalarını tutan Azure blob depolamanızı data factory’ye bağlamak için **Azure Storage** bağlı hizmeti.
   2. İsteğe bağlı HDInsight Hadoop kümesini data factory’ye bağlamak için isteğe bağlı **Azure HDInsight** bağlı hizmeti. Azure Data Factory, girdi verilerini işlemek, çıktı verilerini de oluşturmak için tam zamanında HDInsight Hadoop kümesi oluşturur.
3. İşlem hattındaki HDInsight Hive etkinliğiyle ilgili girdi ve çıktı verilerini açıklayan oluşturulan iki **veri kümesi**.
4. **HDInsight Hive** etkinliğine sahip oluşturulan bir **işlem hattı**.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, isteğe bağlı Azure HDInsight kümesinde bir Hive betiği çalıştıran dönüştürme etkinliğine (HDInsight Etkinliği) sahip işlem hattı oluşturdunuz. Verileri Azure Blob’tan Azure SQL’e kopyalamak için Kopyalama Etkinliği’nin kullanılması hakkında bilgi için bkz. [Öğretici: Verileri Azure Blob’tan Azure SQL’e kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Ayrıca Bkz.

| Konu | Açıklama |
|:--- |:--- |
| [Data Factory Cmdlet Başvurusu](/powershell/module/az.datafactory) |Data Factory cmdlet'leri hakkında kapsamlı belgelere bakma |
| [İşlem hatları](data-factory-create-pipelines.md) |Bu makale, Azure Data Factory’de işlem hatlarının ve etkinliklerini anlamanıza ve senaryonuz ya da işletmeniz için uçtan uca veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı anlamanıza yardımcı olur. |
| [Veri kümeleri](data-factory-create-datasets.md) |Bu makale, Azure Data Factory’deki veri kümelerini anlamanıza yardımcı olur. |
| [Zamanlama ve Yürütme](data-factory-scheduling-and-execution.md) |Bu makalede Azure Data Factory uygulama modelinin zamanlama ve yürütme yönleri açıklanmaktadır. |
| [İzleme Uygulaması kullanılarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) |Bu makalede İzleme ve Yönetim Uygulaması kullanılarak işlem hatlarını izleme, yönetme ve hatalarını ayıklama işlemleri açıklanmaktadır. |
