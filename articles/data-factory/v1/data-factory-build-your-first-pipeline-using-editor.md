---
title: İlk veri fabrikanızı derleme (Azure portal)
description: Bu öğreticide, Azure Portal'daki Data Factory Düzenleyiciyi kullanarak örnek bir Azure Data Factory işlem hattı oluşturursunuz.
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
ms.openlocfilehash: 012b13c440b8d0873e387c7d185803dc07852bf7
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683028"
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Öğretici: Azure portalını kullanarak ilk veri fabrikanızı oluşturma
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Bu makale, Azure Data Factory’nin genel kullanıma açık olan 1. sürümü için geçerlidir. Data Factory'nin geçerli sürümünü kullanıyorsanız [Hızlı Başlangıç: Data Factory'yi kullanarak veri fabrikası oluşturma](../quickstart-create-data-factory-dot-net.md) konusunu inceleyin.

> [!WARNING]
> ADF v1 işlem hatları dağıtmak & yazma için Azure portalındaki JSON Düzenleyicisi, 31 Temmuz 2019 tarihinde kapatılacak. 31 Temmuz 2019 ' den sonra, ADF v1 için ADF v1 .NET SDK, ADF v1 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.datafactories.models?view=azure-dotnet), [ADF v1 REST API](https://docs.microsoft.com/rest/api/datafactory/) 'leri kullanmaya devam edebilirsiniz & ADF v1 işlem hatlarınızı dağıtabilirsiniz. [](https://docs.microsoft.com/powershell/module/az.datafactory/?view=azps-2.4.0&viewFallbackFrom=azps-2.3.2)

Bu makalede, [Azure portalını](https://portal.azure.com/) kullanarak ilk veri fabrikanızı oluşturmayı öğrenirsiniz. Öğreticiyi diğer araçları/SDK’ları kullanarak uygulamak için açılır listedeki seçeneklerden birini belirleyin. 

Bu öğreticideki işlem hattı bir etkinlik içerir: Azure HDInsight Hive etkinliği. Bu etkinlik, HDInsight kümesi üzerinde çıktı verileri üretmek üzere girdi verilerini dönüştüren bir Hive betiği çalıştırır. İşlem hattı, belirtilen başlangıç ve bitiş saatleri arasında ayda bir kez çalışacak şekilde zamanlanmıştır. 

> [!NOTE]
> Bu öğreticideki veri işlem hattı, çıkış verileri üretmek üzere giriş verilerini dönüştürür. Data Factory kullanarak verileri kopyalama öğreticisi için bkz. [Öğretici: Azure Blob depolamadan Azure SQL Veritabanı’na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Data Factory'de zamanlama ve yürütme](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Ön koşullar
[Öğreticiye genel bakış](data-factory-build-your-first-pipeline.md) bölümünü okuyun ve "Önkoşullar" bölümündeki adımları izleyin.

Bu makale, Data Factory hizmetine kavramsal bir genel bakış sağlamaz. Hizmet hakkında daha fazla bilgi için [Azure Data Factory'ye giriş](data-factory-introduction.md) konusunu okuyun.  

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Bir kaynaktan hedef veri depolama alanına veri kopyalamaya yönelik bir Kopyalama etkinliği buna örnek olarak verilebilir. Girdi verilerini ürün çıktı verilerine dönüştürmek için bir Hive betiği çalıştıran bir HDInsight Hive etkinliği de başka bir örnektir. 

Veri fabrikası oluşturmak için bu adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Yeni** > **Veri ve Analiz** > **Data Factory**’yi seçin.

   ![Dikey pencere oluşturma](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

1. **Yeni veri fabrikası** dikey penceresinde, **Ad** bölümüne **GetStartedDF** adını girin.

   ![Yeni veri fabrikası dikey penceresi](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Veri fabrikasının adı genel olarak benzersiz olmalıdır. Veri fabrikası adı “GetStartedDF” kullanılamıyor hatasını alırsanız veri fabrikasının adını değiştirin. Örneğin, adınızGetStartedDF adını kullanarak veri fabrikasını yeniden oluşturun. Adlandırma kuralları hakkında daha fazla bilgi için bkz. [Data Factory: Adlandırma kuralları](data-factory-naming-rules.md).
   >
   > Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş olabilir ve herkese görünür hale gelebilir.
   >
   >
1. Abonelik bölümünde, veri fabrikasını oluşturmak istediğiniz **Azure aboneliğini** seçin.

1. Mevcut bir kaynak grubunu seçin ya da bir kaynak grubu oluşturun. Öğreticide kullanmak üzere şu ada sahip bir kaynak grubu oluşturun: **ADFGetStartedRG**.

1. **Konum** bölümünden veri fabrikası için bir konum seçin. Açılır listede yalnızca Data Factory hizmeti tarafından desteklenen bölgeler gösterilmektedir.

1. **Panoya sabitle** onay kutusunu seçin.

1. **Oluştur**'u seçin.

   > [!IMPORTANT]
   > Data Factory örnekleri oluşturmak için abonelik/kaynak grubu düzeyinde [Data Factory katılımcısı](../../role-based-access-control/built-in-roles.md#data-factory-contributor) rolünün üyesi olmanız gerekir.
   >
   >
1. Panoda, **Data Factory Dağıtılıyor** durumuna sahip aşağıdaki kutucuğu görürsünüz:    

   ![Veri Fabrikası Dağıtılıyor durumu](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

1. Veri fabrikası oluşturulduktan sonra veri fabrikasının içeriğinin gösterildiği **Veri fabrikası** sayfasını görürsünüz.     

    ![Veri fabrikası dikey penceresi](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Veri fabrikasında bir işlem hattı oluşturmadan önce birkaç veri fabrikası varlığı oluşturmanız gerekir. İlk olarak veri depolarını/işlemleri kendi veri deponuza bağlamak için bağlı hizmetler oluşturursunuz. Daha sonra, bağlı veri depolarındaki girdi/çıktıverilerini temsil eden girdi ve çıktı veri kümeleri tanımlarsınız. Son olarak bu veri kümelerini kullanan bir etkinlik ile işlem hattını oluşturursunuz.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu adımda, Azure Depolama hesabınızı ve isteğe bağlı HDInsight kümesini veri fabrikanıza bağlarsınız. Depolama hesabı, bu örnekteki işlem hattı için girdi ve çıktı verilerini tutar. HDInsight bağlı hizmeti, bu örnekteki işlem hattının etkinliğinde belirtilen Hive betiğini çalıştırmak için kullanılır. Senaryonuzda hangi [veri deposunun](data-factory-data-movement-activities.md)/[işlem hizmetlerinin](data-factory-compute-linked-services.md) kullanıldığını belirleyin. Sonra bağlı hizmetler oluşturarak bu hizmetleri veri fabrikasına bağlayın.  

### <a name="create-a-storage-linked-service"></a>Depolama bağlı hizmeti oluşturma
Bu adımda, depolama hesabınızı veri fabrikanıza bağlarsınız. Bu öğreticide, girdi/çıktı verilerini ve HQL betik dosyasını depolamak için aynı depolama hesabını kullanırsınız.

1. **GetStartedDF** için **Veri fabrikası** dikey penceresinde **Geliştir ve dağıt**’ı seçin. Data Factory Düzenleyicisi’ni görürsünüz.

   ![Geliştir ve dağıt kutucuğu](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

1. **Yeni veri deposu**’na tıklayın ve **Azure Depolama**’yı seçin.

   ![Yeni veri deposu dikey penceresi](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

1. Düzenleyicide bir Depolama bağlı hizmeti oluşturmaya yönelik JSON betiğini görürsünüz.

   ![Depolama bağlı hizmeti](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. **Hesap adı** değerini depolama hesabınızın adıyla değiştirin. **Hesap anahtarı** değerini depolama hesabının erişim anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alabileceğinizi öğrenmek için [Depolama hesabınızı yönetme](../../storage/common/storage-account-manage.md#access-keys) sayfasındaki depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma yönergelerine bakın.

1. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt**’ı seçin.

    ![Dağıt düğmesi](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Bağlı hizmet başarıyla dağıtıldıktan sonra Draft-1 penceresi kaybolur. Soldaki ağaç görünümünde **AzureStorageLinkedService** öğesini görürsünüz.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>HDInsight bağlı hizmeti oluşturma
Bu adımda, isteğe bağlı HDInsight kümesini data factory’nize bağlarsınız. HDInsight kümesi çalışma zamanında otomatik olarak oluşturulur. İşlem tamamlandıktan sonra küme belirtilen süre boyunca boşta kalırsa silinir.

1. Data Factory Düzenleyicisi’nde **Diğer** > **Yeni işlem** > **İsteğe bağlı HDInsight kümesi** seçeneğini belirleyin.

    ![Yeni işlem](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

1. Aşağıdaki kod parçacığını kopyalayıp Taslak-1 penceresine yapıştırın. JSON kod parçacığı, istek üzerine HDInsight kümesi oluşturmak için kullanılan özellikleri tanımlar.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmiştir.

   | Özellik | Açıklama |
   |:--- |:--- |
   | clusterSize |HDInsight kümesi boyutunu belirtir. |
   | timeToLive | HDInsight kümesinin silinmeden önce boşta kalma süresini belirtir. |
   | linkedServiceName | HDInsight tarafından oluşturulan günlükleri depolamak için kullanılan depolama hesabını belirtir. |

    Aşağıdaki noktalara dikkat edin:

     a. Veri fabrikası, sizin için JSON özelliklerine sahip Linux tabanlı bir HDInsight kümesi oluşturur. Daha fazla bilgi için bkz. [İsteğe bağlı HDInsight bağlı hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. İsteğe bağlı HDInsight kümesi yerine kendi HDInsight kümenizi kullanabilirsiniz. Daha fazla bilgi için bkz. [HDInsight bağlı hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. HDInsight kümesi JSON özelliğinde (**linkedServiceName**) belirttiğiniz blob depolamada bir varsayılan kapsayıcı oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmeti kullanıldığında, mevcut canlı bir küme olmadığı sürece bir dilim her işlendiğinde bir HDInsight kümesi oluşturulur (**timeToLive**). İşlem tamamlandığında küme otomatik olarak silinir.

     Daha fazla dilim işlendikçe, blob depolamanızda çok sayıda kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcıların adları şu deseni izler: "adf**verifabrikanızınadı**-**bağlıhizmeadı**-tarihsaatdamgası." Blob depolamanızdaki kapsayıcıları silmek için [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

     Daha fazla bilgi için bkz. [İsteğe bağlı HDInsight bağlı hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

1. Bağlı hizmeti dağıtmak için komut çubuğunda **Dağıt**’ı seçin.

    ![Dağıtım seçeneği](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

1. Hem **AzureStorageLinkedService**, hem de **HDInsightOnDemandLinkedService** öğesinin soldaki ağaç görünümünde olduğunu onaylayın.

    ![Bağlı hizmetlerin bulunduğu ağaç görünümü](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, Hive işlenmesi için girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturursunuz. Bu veri kümeleri, bu öğreticide daha önce oluşturduğunuz AzureStorageLinkedService öğesine başvurur. Bağlı hizmet bir depolama hesabını gösterir. Veri kümeleri, girdi ve çıktı verilerini barındıran depolama alanında kapsayıcı, klasör ve dosya adını belirtir.   

### <a name="create-the-input-dataset"></a>Girdi veri kümesini oluşturma
1. Data Factory Düzenleyicisi’nde **Diğer** > **Yeni veri kümesi** > **Azure Blob depolama**’yı seçin.

    ![Yeni veri kümesi](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

1. Aşağıdaki kod parçacığını kopyalayıp Taslak-1 penceresine yapıştırın. JSON parçacığında, işlem hattındaki etkinliğin girdi verilerini temsil eden **AzureBlobInput** adlı bir veri kümesi oluşturursunuz. Ek olarak, girdi verilerinin **adfgetstarted** adlı blob kapsayıcısında ve **inputdata** adlı klasörde olacağını belirtirsiniz.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
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
    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmiştir.

   | Özellik | İç içe geçmiş | Açıklama |
   |:--- |:--- |:--- |
   | type | properties |Veriler blob depolamada yer aldığından, type özelliği **AzureBlob** olarak ayarlanır. |
   | linkedServiceName | formatını |Daha önce oluşturduğunuz AzureStorageLinkedService hizmetine başvurur. |
   | folderPath | typeProperties | Blob kapsayıcısını ve giriş bloblarını içeren klasörü belirtir. | 
   | fileName | typeProperties |Bu özellik isteğe bağlıdır. Bu özelliği atarsanız, tüm folderPath dosyaları seçilir. Bu öğreticide yalnızca input.log dosyası işlenir. |
   | type | formatını |Günlük dosyaları metin biçiminde olduğundan **TextFormat** seçeneğini kullanın. |
   | columnDelimiter | formatını |Günlük dosyalarındaki sütunlar virgül karakteri (`,`) ile ayrılır. |
   | frequency/interval | availability |Sıklığın **Month**, aralığın **1** olarak ayarlanmış olması, girdi dilimlerinin aylık olarak kullanılabileceği anlamına gelir. |
   | external | properties | Bu özellik, giriş verileri bu işlem hattı tarafından oluşturulmadıysa **true** olarak ayarlanır. Bu öğreticide, input.log dosyası bu işlem hattı tarafından oluşturulmadığından, özelliği **true** olarak ayarlayacağız. |

    Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure Blob bağlayıcısı](data-factory-azure-blob-connector.md#dataset-properties).

1. Yeni oluşturulan veri kümesini dağıtmak için komut çubuğundan **Dağıt**’ı seçin. Soldaki ağaç görünümünde veri kümesini görürsünüz.

### <a name="create-the-output-dataset"></a>Çıktı veri kümesini oluşturma
Şimdi, blob depolamada depolanan çıktı verilerini temsil eden çıktı veri kümesini oluşturursunuz.

1. Data Factory Düzenleyicisi’nde **Diğer** > **Yeni veri kümesi** > **Azure Blob depolama**’yı seçin.

1. Aşağıdaki kod parçacığını kopyalayıp Taslak-1 penceresine yapıştırın. JSON kod parçacığında, **AzureBlobOutput** adlı bir veri kümesi oluşturarak Hive betiğinin oluşturacağı verilerin yapısını belirtirsiniz. Ayrıca, sonuçların **adfgetstarted** adlı blob kapsayıcısında ve **partitioneddata** adlı klasörde depolandığını belirtirsiniz. Burada, **availability** bölümü çıktı veri kümesinin aylık olarak oluşturulduğunu belirtir.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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
    Bu özelliklerin açıklamaları için “Girdi veri kümesi oluşturma” bölümüne bakın. Veri kümesi Data Factory hizmeti tarafından oluşturulduğundan, çıktı veri kümesinde dış özellik ayarlamazsınız.

1. Yeni oluşturulan veri kümesini dağıtmak için komut çubuğundan **Dağıt**’ı seçin.

1. Veri kümesinin başarıyla oluşturulduğunu doğrulayın.

    ![Bağlı hizmetlerin bulunduğu ağaç görünümü](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma
Bu adımda, bir HDInsightHive etkinliğiyle ilk işlem hattınızı oluşturursunuz. Girdi dilimi aylık olarak kullanılabilir (sıklık Month, aralık ise 1 değerine sahiptir). Çıktı dilimi aylık olarak oluşturulur. Etkinliğin zamanlayıcı özelliği de aylık olarak ayarlanır. Çıktı veri kümesi ve etkinlik zamanlayıcı ayarlarının eşleşmesi gerekir. Şu anda, zamanlama çıktı veri kümesi tarafından yönetildiğinden, etkinlik hiçbir çıktı oluşturmasa bile bir çıktı veri kümesi oluşturmanız gerekir. Etkinlik herhangi bir girdi almazsa, girdi veri kümesi oluşturma işlemini atlayabilirsiniz. Aşağıdaki JSON kod parçacığında kullanılan özellikler bu bölümün sonunda anlatılmaktadır.

1. Data Factory Düzenleyicisi’nde **Diğer** > **Yeni işlem hattı**’nı seçin.

    ![Yeni işlem hattı seçeneği](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

1. Aşağıdaki kod parçacığını kopyalayıp Taslak-1 penceresine yapıştırın.

   > [!IMPORTANT]
   > JSON kod parçacığında **storageaccountname**’i depolama hesabınızın adıyla değiştirin.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
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

    JSON kod parçacığında, HDInsight kümesinde veri işleyecek Hive’ı kullanan etkinlikten oluşturulmuş bir işlem hattı oluşturursunuz.

    **partitionweblogs.hql** adlı Hive betik dosyası, **AzureStorageLinkedService** adlı scriptLinkedService tarafından belirtilen depolama hesabında depolanır. Bunu **adfgetstarted** kapsayıcısının **script** klasöründe bulabilirsiniz.

    **Defines** bölümü, Hive betiğine Hive yapılandırma değerleri olarak geçirilen çalışma zamanı ayarlarını belirtmek için kullanılır. Buna örnek olarak ${hiveconf:inputtable} ve ${hiveconf:partitionedtable} verilebilir.

    İşlem hattının **start** ve **end** özellikleri işlem hattının etkin dönemini belirtir.

    Etkinlik JSON’unda, Hive betiğinin **linkedServiceName** – **HDInsightOnDemandLinkedService** tarafından belirtilen işlemde çalışacağını belirtirsiniz.

   > [!NOTE]
   > Örnekte kullanılan JSON özellikleri hakkında daha fazla bilgi için [Data Factory’deki işlem hatları ve etkinlikler](data-factory-create-pipelines.md) sayfasındaki “İşlem Hattı JSON’u” bölümüne bakın.
   >
   >
1. Şunları onaylayın:

   a. Blob depolamada **adfgetstarted** kapsayıcısının **inputdata** klasöründe **input.log** dosyasının olduğunu.

   b. Blob depolamada **adfgetstarted** kapsayıcısının **script** klasöründe **partitionweblogs.hql** dosyasının olduğunu. Bu dosyaları göremiyorsanız, [Öğreticiye genel bakış](data-factory-build-your-first-pipeline.md) konusunun "Önkoşullar" bölümündeki adımları izleyin.

   c. **storageaccountname**’i işlem hattı JSON’undaki adınızla değiştirdiğinizi.

1. İşlem hattını dağıtmak için komut çubuğundan **Dağıt**’ı seçin. **start** ve **end** zamanları geçmişe ayarlanmış ve **isPaused** değeri **false** olarak ayarlanmış olduğundan, işlem hattı (işlem hattında etkinlik) dağıtıldıktan hemen sonra çalışır.

1. İşlem hattını ağaç görünümünde gördüğünüzü onaylayın.

    ![İşlem hattının bulunduğu ağaç görünümü](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>İşlem hattını izleme
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Diyagram görünümünü kullanarak bir işlem hattını izleme
1. **Veri fabrikası** dikey penceresinde **Diyagram**’ı seçin.

    ![Diyagram kutucuğu](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

1. **Diyagram** görünümünde, işlem hatlarına ve bu öğreticide kullanılan veri kümelerine genel bir bakış görürsünüz.

    ![Diyagram görünümü](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

1. İşlem hattındaki tüm etkinlikleri görüntülemek için diyagramdaki işlem hattına sağ tıklayıp **İşlem hattını aç**’a tıklayın.

    ![İşlem hattı menüsünü açma](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

1. İşlem hattında **Hive Etkinliğini** gördüğünüzü onaylayın.

    ![İşlem hattı görünümünü açma](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Önceki görünüme dönmek için üstteki menüden **Veri fabrikası**’nı seçin.

1. **Diyagram** görünümünde **AzureBlobInput** veri kümesine çift tıklayın. Dilimin **Hazır** durumda olduğunu onaylayın. Dilimin **Hazır** olarak görünmesi birkaç dakika alabilir. Bir süre bekledikten sonra dilim görünmüyorsa, girdi dosyasının (**input.log**) doğru kapsayıcıda (**adfgetstarted**) ve klasörde (**inputdata**) olup olmadığına bakın.

   ![Girdi dilimi Hazır durumda](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

1. **AzureBlobInput** dikey penceresini kapatın.

1. **Diyagram** görünümünde **AzureBlobOutput** veri kümesine çift tıklayın. Dilimin işlenmekte olduğunu görürsünüz.

   ![Veri kümesinin işlenmesi sürüyor](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

1. İşleme tamamlandıktan sonra dilimi **Hazır** durumda görürsünüz.

   ![Veri kümesi Hazır durumda](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > İsteğe bağlı HDInsight kümesinin oluşturulması genellikle yaklaşık 20 dakika sürer. İşlem hattının dilimi işlemesi için yaklaşık 30 dakika bekleyin.
   >
   >

1. Dilim **Hazır** duruma geldiğinde, çıktı verileri için blob depolama alanınızın **adfgetstarted** kapsayıcısında **partitioneddata** klasörünü denetleyin.  

   ![Çıktı verileri](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

1. Bir **Veri dilimi** dikey penceresinde dilimle ilgili daha fazla bilgi görmek için dilimi seçin.

    ![Veri dilimi bilgileri](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

1. **Etkinlik çalıştırmaları** listesindeki bir etkinlik çalıştırmasıyla ilgili daha fazla bilgi görmek için çalıştırmayı seçin. (Bu senaryoda Hive etkinliğidir.) Bilgiler bir **etkinlik çalışma ayrıntıları** dikey penceresinde görünür.   

    ![Etkinlik çalıştırması ayrıntıları penceresi](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   Yürütülen Hive sorgusunu ve durum bilgilerini günlük dosyalarında görebilirsiniz. Bu günlükler her türlü sorunu gidermek için kullanışlıdır.
   Daha fazla bilgi için bkz. [Azure portal dikey pencerelerini kullanarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Dilim başarıyla işlendiğinde girdi dosyası silinir. Bu nedenle, dilimi yeniden çalıştırmak veya öğreticiyi yeniden uygulamak isterseniz girdi dosyasını (**input.log**) **adfgetstarted** kapsayıcısının **inputdata** klasörüne yükleyin.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>İzleme ve Yönetme uygulamasını kullanarak bir işlem hattını izleme
İşlem hatlarınızı izlemek için İzleme ve Yönetme uygulamasını da kullanabilirsiniz. Bu uygulamanın nasıl kullanılacağı hakkında daha fazla bilgi edinmek için bkz. [İzleme ve Yönetme uygulamasını kullanarak Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md).

1. Veri fabrikanızın giriş sayfasındaki **İzleme ve Yönetme** kutucuğunu seçin.

    ![İzleme ve Yönetme kutucuğu](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

1. İzleme ve Yönetme uygulamasında **Başlangıç saati** ve **Bitiş saati** değerlerini işlem hattınızın başlangıç ve bitiş saatleriyle eşleşecek şekilde değiştirin. **Uygula**’yı seçin.

    ![İzleme ve Yönetme uygulaması](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

1. Bir etkinlikle ilgili bilgileri görmek için **Etkinlik Pencereleri** listesinden bir etkinlik penceresini seçin.

    ![Etkinlik Pencereleri listesi](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Özet
Bu öğreticide, HDInsight Hadoop kümesindeki Hive betiği çalıştırılarak verileri işlemek için bir veri fabrikası oluşturdunuz. Aşağıdakileri uygulamak için Azure portalında Data Factory Düzenleyicisi’ni kullandınız:  

* Veri fabrikası oluşturma.
* İki bağlı hizmet oluşturma:
   * Girdi/çıktı dosyalarını tutan Azure blob depolamanızı veri fabrikasına bağlamak için bir Depolama bağlı hizmeti.
   * İsteğe bağlı HDInsight Hadoop kümesini veri fabrikasına bağlamak için isteğe bağlı bir HDInsight bağlı hizmeti. Data Factory, girdi verilerini işlemek, çıktı verilerini de oluşturmak için tam zamanında bir HDInsight Hadoop kümesi oluşturur.
* İşlem hattındaki bir HDInsight Hive etkinliğiyle ilgili girdi ve çıktı verilerini açıklayan iki veri kümesi oluşturun.
* Bir HDInsight Hive etkinliği içeren bir işlem hattı oluşturun.

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, isteğe bağlı HDInsight kümesinde bir Hive betiği çalıştıran dönüştürme etkinliğine (HDInsight etkinliği) sahip işlem hattı oluşturdunuz. Kopyalama etkinliği kullanarak blob depolamadaki verileri bir SQL veritabanına nasıl aktarabileceğinizi öğrenmek için bkz. [Öğretici: Blob depolamadan SQL Veritabanı’na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Ayrıca bkz.
| Konu | Açıklama |
|:--- |:--- |
| [İşlem hatları](data-factory-create-pipelines.md) |Bu makale, Data Factory’de işlem hatlarını ve etkinliklerini anlamanıza ve senaryonuz ya da işletmeniz için uçtan uca veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı anlamanıza yardımcı olur. |
| [Veri kümeleri](data-factory-create-datasets.md) |Bu makale, Data Factory’deki veri kümelerini anlamanıza yardımcı olur. |
| [Zamanlama ve yürütme](data-factory-scheduling-and-execution.md) |Bu makalede Data Factory uygulama modelinin zamanlama ve yürütme yönleri açıklanmaktadır. |
| [İzleme ve Yönetme uygulamasını kullanılarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) |Bu makalede, İzleme ve Yönetme uygulaması kullanılarak işlem hatlarını izleme, yönetme ve hatalarını ayıklama işlemleri açıklanmaktadır. |