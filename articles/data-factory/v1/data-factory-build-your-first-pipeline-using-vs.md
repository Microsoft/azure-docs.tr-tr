---
title: İlk Data Factory 'nizi derleme (Visual Studio)
description: Bu öğreticide Visual Studio kullanarak örnek bir Azure Data Factory işlem hattı oluşturursunuz.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: vs-azure
ms.date: 01/22/2018
ms.openlocfilehash: 888f3b1a53ba2e31195e3b9d577a475df441e972
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682999"
---
# <a name="tutorial-create-a-data-factory-by-using-visual-studio"></a>Öğretici: Visual Studio kullanarak veri fabrikası oluşturma
> [!div class="op_single_selector" title="Tools/SDKs"]
> * [Genel bakış ve önkoşullar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager Şablonu](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory'nin geçerli sürümünü kullanıyorsanız [Hızlı Başlangıç: Azure Data Factory'yi kullanarak veri fabrikası oluşturma](../quickstart-create-data-factory-dot-net.md) konusunu inceleyin.

Bu öğreticide Visual Studio kullanarak bir Azure veri fabrikası oluşturma ve izleme işlemi gösterilmektedir. Data Factory proje şablonunu kullanarak bir Visual Studio projesi oluşturacak, JSON biçiminde Data Factory varlıkları (bağlı hizmetler, veri kümeleri ve işlem hattı) tanımlayacak ve ardından bu varlıkları bulutta yayımlayacak/dağıtacaksınız. 

Bu öğreticideki işlem hattı bir etkinlik içerir: **HDInsight Hive etkinliği**. Bu etkinlik, Azure HDInsight kümesi üzerinde çıkış verileri üretmek üzere giriş verilerini dönüştüren bir hive betiği çalıştırır. İşlem hattı, belirtilen başlangıç ve bitiş saatleri arasında ayda bir kez çalışacak şekilde zamanlanmıştır. 

> [!NOTE]
> Bu öğreticide, Azure Data Factory kullanarak verilerin nasıl kopyalanacağı gösterilmemektedir. Azure Data Factory kullanarak verileri kopyalama öğreticisi için bkz. [Öğretici: Blob Depolama’dan SQL Veritabanı’na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Data Factory'de zamanlama ve yürütme](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="walkthrough-create-and-publish-data-factory-entities"></a>İzlenecek Yol: Data Factory varlıkları oluşturma ve yayımlama
Bu izlenecek yolun bir parçası olarak gerçekleştireceğiniz adımlar şunlardır:

1. İki bağlı hizmet oluşturun: **AzureStorageLinkedService1** ve **HDInsightOnDemandLinkedService1**. 
   
    Bu öğreticide, hive etkinliği için giriş ve çıkış verileri aynı Azure Blob Depolama içindedir. İsteğe bağlı HDInsight kümesini kullanarak, çıkış verileri üretmek üzere mevcut giriş verilerini işleyebilirsiniz. İsteğe bağlı HDInsight kümesi, giriş verileri işlenmeye hazır olduğunda Azure Data Factory tarafından çalışma zamanında otomatik olarak oluşturulur. Çalışma zamanında Data Factory hizmetinin veri depolarınıza veya işlemlerinize bağlanabilmesi için, bunları veri fabrikanıza bağlamanız gerekir. Bu nedenle, AzureStorageLinkedService1 kullanarak Azure Depolama Hesabınızı veri fabrikasına bağlarsınız ve HDInsightOnDemandLinkedService1 kullanarak isteğe bağlı HDInsight kümesini bağlarsınız. Yayımlama sırasında, oluşturulacak veri fabrikasının adını veya mevcut bir veri fabrikasını belirtirsiniz.  
2. Azure blob depolama alanında depolanan giriş/çıkış verilerini temsil eden iki veri kümesi oluşturun: **InputDataset** ve **OutputDataset**. 
   
    Bu veri kümesi tanımları, önceki adımda oluşturduğunuz bağlı Azure Depolama hizmetini ifade eder. InputDataset için blob kapsayıcısını (adfgetstarted) ve giriş verileriyle birlikte blob içeren klasörü (inptutdata) belirtin. OutputDataset için blob kapsayıcısını (adfgetstarted) ve çıkış verilerini içeren klasörü (partitioneddata) belirtin. Yapı, kullanılabilirlik ve ilke gibi diğer özellikleri de belirtirsiniz.
3. **MyFirstPipeline** adlı bir işlem hattı oluşturun. 
  
    Bu kılavuzdaki işlem hattı yalnızca bir etkinlik içerir: **HDInsight Hive Etkinliği**. Bu etkinlik, isteğe bağlı bir HDInsight kümesi üzerinde hive betiği çalıştırarak çıkış verileri üretmek üzere giriş verilerini dönüştürür. Hive etkinliği hakkında daha fazla bilgi edinmek için bkz. [Hive Etkinliği](data-factory-hive-activity.md) 
4. **DataFactoryUsingVS** adlı bir veri fabrikası oluşturun. Veri fabrikasını ve tüm Data Factory varlıklarını (bağlı hizmetler, tablolar ve işlem hattı) dağıtın.
5. Yayımladıktan sonra, işlem hattını izlemek için Azure portalı dikey pencereleri ile İzleme ve Yönetim Uygulamasını kullanabilirsiniz. 
  
### <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

1. [Öğreticiye Genel Bakış](data-factory-build-your-first-pipeline.md) makalesinin tamamını okuyun ve **ön koşul** adımlarını tamamlayın. Ayrıca, üst kısımdaki açılır listede bulunan **Genel bakış ve önkoşullar** seçeneğini belirleyerek makaleye geçiş yapabilirsiniz. Önkoşulları tamamladıktan sonra, açılır listeden **Visual Studio** seçeneğini belirleyerek bu makaleye geri dönün.
2. Data Factory örnekleri oluşturmak için abonelik/kaynak grubu düzeyinde [Data Factory Katılımcısı](../../role-based-access-control/built-in-roles.md#data-factory-contributor) rolünün üyesi olmanız gerekir.  
3. Bilgisayarınızda şunların yüklü olması gerekir:
   * Visual Studio 2013 veya Visual Studio 2015
   * Visual Studio 2013 veya Visual Studio 2015 için Azure SDK’sını indirin. [Azure İndirme Sayfası](https://azure.microsoft.com/downloads/)’na gidin ve **.NET** bölümündeki **VS 2013** veya **VS 2015**’e tıklayın.
   * Visual Studio için en son Azure Data Factory eklentisini indirin: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) veya [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). Aşağıdaki adımları uygulayarak eklentiyi güncelleştirebilirsiniz: Menüde **Araçlar** -> **Uzantılar ve Güncelleştirmeler** -> **Çevrimiçi** -> **Visual Studio Galerisi** -> **Visual Studio için Microsoft Azure Data Factory Araçları** -> **Güncelleştir**’e tıklayın.

Şimdi bir Azure data factory oluşturmak için Visual Studio kullanalım.

### <a name="create-visual-studio-project"></a>Visual Studio projesi oluşturma
1. **Visual Studio 2013** veya **Visual Studio 2015**’i başlatın. **Dosya**’ya tıklayın, **Yeni**’nin üzerine gelin ve **Proje**’ye tıklayın. **Yeni Proje** iletişim kutusu görmeniz gerekir.  
2. **Yeni Proje** iletişim kutusunda **DataFactory** şablonunu seçip **Boş Data Factory Projesi**’ne tıklayın.   

    ![Yeni proje iletişim kutusu](./media/data-factory-build-your-first-pipeline-using-vs/new-project-dialog.png)
3. Proje için bir **ad**, **konum** ve **çözüm** için ad girip **Tamam**’a tıklayın.

    ![Çözüm Gezgini](./media/data-factory-build-your-first-pipeline-using-vs/solution-explorer.png)

### <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu adımda iki bağlı hizmet oluşturursunuz: **Azure Depolama** ve **İsteğe bağlı HDInsight**. 

Azure Depolama bağlı hizmeti, bağlantı bilgilerini sağlayarak Azure Depolama hesabınızı veri fabrikasına bağlar. Data Factory hizmeti, çalışma zamanında Azure depolamaya bağlanmak için bağlı hizmet ayarındaki bağlantı dizesini kullanır. Bu depolama alanı, işlem hattının giriş ve çıkış verilerinin yanı sıra hive etkinliği tarafından kullanılan hive betik dosyasını içerir. 

İsteğe bağlı HDInsight bağlı hizmeti ile, giriş verileri işlenmeye hazır olduğunda HDInsight kümesi çalışma zamanında otomatik olarak oluşturulur. Kümenin işlenmesi tamamlandıktan sonra küme belirtilen süre boyunca boşta kalırsa, küme silinir. 

> [!NOTE]
> Data Factory çözümünüzü yayımlarken, ad ve ayarlarını belirterek bir veri fabrikası oluşturun.

#### <a name="create-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma
1. Çözüm gezgininde **Bağlı Hizmetler**’e sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.      
2. **Yeni Öğe Ekle** iletişim kutusunda **Azure Storage Bağlı Hizmeti**’ni listeden seçip **Ekle**’ye tıklayın.
    ![Azure Storage Bağlı Hizmeti](./media/data-factory-build-your-first-pipeline-using-vs/new-azure-storage-linked-service.png)
3. `<accountname>` ve `<accountkey>` değerlerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alabileceğinizi öğrenmek için [Depolama hesabınızı yönetme](../../storage/common/storage-account-manage.md#access-keys) sayfasındaki depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma bilgilerine bakın.
    ![Azure Storage Bağlı Hizmeti](./media/data-factory-build-your-first-pipeline-using-vs/azure-storage-linked-service.png)
4. **AzureStorageLinkedService1.json** dosyasını kaydedin.

#### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight bağlı hizmeti oluşturma
1. **Çözüm Gezgini**’nde **Bağlı Hizmetler**’e sağ tıklayın, **Ekle**’nin üzerine gelip Y**eni Öğe**’ye tıklayın.
2. **İsteğe Bağlı HDInsight Bağlı Hizmeti**’ni seçin ve **Ekle**’ye tıklayın.
3. **JSON** değerini aşağıdaki JSON ile değiştirin:

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
                "linkedServiceName": "AzureStorageLinkedService1"
            }
        }
    }
    ```

    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

    Özellik | Açıklama
    -------- | ----------- 
    ClusterSize | HDInsight Hadoop kümesinin boyutunu belirtir.
    TimeToLive | Silinmeden önce HDInsight kümesinin boşta kalma süresini belirtir.
    linkedServiceName | HDInsight Hadoop kümesi tarafından oluşturulan günlükleri depolamak için kullanılan depolama hesabını belirtir. 

    > [!IMPORTANT]
    > HDInsight kümesi JSON’da belirttiğiniz blob depolamada (linkedServiceName) bir **varsayılan kapsayıcı** oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmeti kullanıldığında, mevcut canlı bir küme olmadığı sürece bir dilim her işlendiğinde bir HDInsight kümesi oluşturulur (timeToLive). Küme, işlem tamamlandığında otomatik olarak silinir.
    > 
    > Daha fazla dilim işlendikçe, Azure blob depolamanızda çok sayıda kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcı adları bir düzene sahiptir: `adf<yourdatafactoryname>-<linkedservicename>-datetimestamp`. Azure blob depolamada kapsayıcı silmek için [Microsoft Storage Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

    JSON özellikleri hakkında daha fazla bilgi için [İşlem bağlı hizmetleri](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) makalesine bakın. 
4. **HDInsightOnDemandLinkedService1.json** dosyasını kaydedin.

### <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, Hive işlenmesi için girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturursunuz. Bu veri kümeleri, bu öğreticide daha önce oluşturduğunuz **AzureStorageLinkedService1** öğesine başvurur. Bağlı hizmet Azure Storage hesabını belirtirken, veri kümeleri de girdi ve çıktı verilerini tutan depolama biriminde kapsayıcı, klasör, dosya adı belirtir.   

#### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma
1. **Çözüm Gezgini**’nde **Tablolar**’a sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.
2. Listeden **Azure Blob**’u seçin, dosya adını **InputDataSet.json** olarak değiştirin ve **Ekle**’ye tıklayın.
3. Düzenleyicide **JSON** değerini aşağıdaki JSON kod parçacığıyla değiştirin:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    Bu JSON parçacığı, işlem hattındaki hive etkinliğinin giriş verilerini temsil eden **AzureBlobInput** adlı bir veri kümesi tanımlar. Giriş verilerinin `adfgetstarted` adlı blob kapsayıcısında ve `inputdata` adlı klasörde bulunduğunu belirtin.

    Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

    Özellik | Açıklama |
    -------- | ----------- |
    type |Veriler Azure Blob Depolamada yer aldığından type özelliği **AzureBlob** olarak ayarlanmıştır.
    linkedServiceName | Daha önce oluşturduğunuz AzureStorageLinkedService1’i ifade eder.
    fileName |Bu özellik isteğe bağlıdır. Bu özelliği atarsanız, tüm folderPath dosyaları alınır. Bu durumda, yalnızca input.log işlenir.
    type | Günlük dosyaları metin biçiminde olduğundan TextFormat kullanacağız. |
    columnDelimiter | Günlük dosyalarındaki sütunlar virgül (`,`) ile ayrılmıştır
    frequency/interval | frequency Ay, interval de 1 olarak ayarlanmıştır; girdi dilimlerinin aylık olarak kullanılabileceğini belirtir.
    external | Bu özellik, etkinliğin giriş verileri işlem hattı tarafından oluşturulmadıysa true olarak ayarlanır. Bu özellik yalnızca giriş veri kümelerinde belirtilir. Birinci etkinliğin giriş veri kümesi için her zaman true olarak ayarlayın.
4. **InputDataset.json** dosyasını kaydedin.

#### <a name="create-output-dataset"></a>Çıktı veri kümesi oluşturma
Şimdi, Azure Blob depolamada depolanan çıkış verilerini göstermek için çıkış veri kümesi oluşturun.

1. **Çözüm Gezgini**’nde **tablolar**’a sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.
2. Listeden **Azure Blob**’u seçin, dosya adını **OutputDataset.json** olarak değiştirin ve **Ekle**’ye tıklayın.
3. Düzenleyicide **JSON**’u aşağıdaki JSON ile değiştirin:
    
    ```json
    {
        "name": "AzureBlobOutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService1",
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
    JSON parçacığı, işlem hattındaki hive etkinliği tarafından oluşturulan çıkış verilerini temsil eden **AzureBlobOutput** adlı bir veri kümesi tanımlar. Hive etkinliği tarafından oluşturulan çıkış verilerinin `adfgetstarted` adlı blob kapsayıcısına ve `partitioneddata` adlı klasöre yerleştirileceğini belirtin. 
    
    Burada, **availability** bölümü çıktı veri kümesinin aylık tabanda oluşturulduğunu belirtiyor. Çıkış veri kümesi, işlem hattının zamanlamasını belirler. İşlem hattı, ayda bir kez başlangıç ve bitiş saatleri arasında çalışır. 

    Bu özelliklerin açıklamaları için **Girdi veri kümesi oluşturma** bölümüne bakın. Veri kümesi işlem hattı tarafından oluşturulduğundan çıkış veri kümesinde dış özellik ayarlamayın.
4. **OutputDataset.json** dosyasını kaydedin.

### <a name="create-pipeline"></a>İşlem hattı oluşturma
Şimdiye kadar, Azure Depolama bağlı hizmetinin yanı sıra giriş ve çıkış veri kümelerini oluşturdunuz. Şimdi, **HDInsightHive** etkinliğiyle bir işlem hattı oluşturacaksınız. Hive etkinliğinin **input** değeri **AzureBlobInput**, **output** değeri ise **AzureBlobOutput** olarak ayarlanır. Giriş veri kümesinin bir dilimi ayda bir kez kullanılabilir (sıklık: Ay, aralık: 1), çıkış dilimi de ayda bir kez üretilir. 

1. **Çözüm Gezgini**’nde **İşlem Hatları**’na sağ tıklayın, **Ekle**’nin üzerine gelip **Yeni Öğe**’ye tıklayın.
2. Listeden **Hive Dönüşüm İşlem Hattı**’nı seçin ve **Ekle**’ye tıklayın.
3. **JSON**’u aşağıdaki kod parçacığıyla değiştirin:

    > [!IMPORTANT]
    > `<storageaccountname>` değerini depolama hesabınızın adıyla değiştirin.

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
                        "scriptLinkedService": "AzureStorageLinkedService1",
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
            "start": "2016-04-01T00:00:00Z",
            "end": "2016-04-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    > [!IMPORTANT]
    > `<storageaccountname>` değerini depolama hesabınızın adıyla değiştirin.

    JSON parçacığı, tek etkinlikten (Hive Etkinliği) oluşan bir işlem hattı tanımlar. Bu etkinlik, isteğe bağlı bir HDInsight kümesi üzerinde çıkış verileri üretmek üzere giriş verilerini işleyen bir Hive betiği çalıştırır. İşlem hattı JSON etkinlikler bölümünde, dizi içinde türü **HDInsightHive** olarak ayarlanmış yalnızca bir etkinlik görürsünüz. 

    HDInsight Hive etkinliğine özel type özelliklerinde, hangi Azure Depolama bağlı hizmetinin hive betik dosyasına sahip olduğunu, betik dosyasının yolunu ve betik dosyasının parametrelerini belirtin. 

    **partitionweblogs.hql** Hive betik dosyası Azure depolama hesabında (scriptLinkedService tarafından belirtilir) ve `script` kapsayıcısındaki `adfgetstarted` klasöründe depolanır.

    `defines` bölümü, hive betiğine Hive yapılandırma değerleri olarak (örn `${hiveconf:inputtable}`, `${hiveconf:partitionedtable})`) geçirilen çalışma zamanı ayarlarını belirtmek için kullanılır.

    İşlem hattının **start** ve **end** özellikleri işlem hattının etkin dönemini belirtir. Aylık olarak üretilecek veri kümesini yapılandırdınız; bu nedenle, (başlangıç ve bitiş tarihleri aynı ayda olduğu için) işlem hattı tarafından yalnızca bir dilim üretilir.

    JSON etkinliğinde, Hive betiğinin **linkedServiceName** – **HDInsightOnDemandLinkedService** tarafından belirtilen işlemde çalışacağını belirtirsiniz.
4. **HiveActivity1.json** dosyasını kaydedin.

### <a name="add-partitionweblogshql-and-inputlog-as-a-dependency"></a>partitionweblogs.hql ve input.log dosyalarını bağımlılık olarak ekleme
1. **Çözüm Gezgini** penceresinde **Bağımlılıklar**’a sağ tıklayın, **Ekle**’nin üzerine gelip **Mevcut Öğe**’ye tıklayın.  
2. **C:\ADFGettingStarted** yoluna gidin ve **partitionweblogs.hql**, **input.log** dosyalarını seçip **Ekle**’ye tıklayın. Bu iki dosyayı [Öğreticiye Genel Bakış](data-factory-build-your-first-pipeline.md)’taki ön koşulların bir parçası olarak oluşturmuştunuz.

Sonraki adımda çözümü yayımladığınızda, **partitionweblogs.hql** dosyası **blob kapsayıcısındaki**script`adfgetstarted` klasörüne yüklenir.   

### <a name="publishdeploy-data-factory-entities"></a>Data Factory varlıklarını yayımlama/dağıtma
Bu adımda, projenizdeki Data Factory varlıklarını (bağlı hizmetler, veri kümeleri ve işlem hattı) Azure Data Factory hizmetinde yayımlayacaksınız. Yayımlama sürecinde, veri fabrikanızın adını belirtin. 

1. Çözüm Gezgini’nde projeye sağ tıklayın ve ardından **Yayımla**’ya tıklayın.
2. **Microsoft hesabınızda oturum açın** iletişim kutusunu görmezseniz, Azure aboneliğindeki kimlik bilgilerini hesap için girin ve **oturum aç**’a tıklayın.
3. Aşağıdaki iletişim kutusunu göreceksiniz:

   ![Yayımla iletişim kutusu](./media/data-factory-build-your-first-pipeline-using-vs/publish.png)
4. **Data Factory’yi yapılandırma** sayfasında aşağıdaki adımları uygulayın:

    ![Yayımlama - Yeni veri fabrikası ayarları](media/data-factory-build-your-first-pipeline-using-vs/publish-new-data-factory.png)

   1. **Yeni Data Factory Oluştur** seçeneğini seçin.
   2. Data factory için benzersiz bir **ad** girin. Örneğin: **DataFactoryUsingVS09152016**. Adın genel olarak benzersiz olması gerekir.
   3. **Abonelik** alanı için doğru abonelik seçin. 
        > [!IMPORTANT]
        > Herhangi bir abonelik görmüyorsanız aboneliğin yöneticisi veya ortak yöneticisi olan bir hesapla oturum açtığınızdan emin olun.
   4. oluşturulacak data factory için **kaynak grubu** seçin.
   5. Data factory için **bölge** seçin.
   6. **Öğeleri Yayımla** sayfasına geçmek için **İleri**’ye tıklayın. (Ad alanından çıkmak için, **İleri** düğmesi devre dışıysa **SEKME** tuşuna basın.)

      > [!IMPORTANT]
      > Yayımladığınızda **“DataFactoryUsingVS” data factory adı yok** hatasını alırsanız adı değiştirin (örneğin, yournameDataFactoryUsingVS). Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.   
1. **Öğeleri Yayımla** sayfasında tüm Data Factory varlıklarının işaretli olmasını sağlayın ve **Özet** sayfasına geçmek için **İleri**’ye tıklayın.

    ![Öğe yayımlama sayfası](media/data-factory-build-your-first-pipeline-using-vs/publish-items-page.png)     
2. Özeti gözden geçirin, dağıtım işlemini başlatmak ve **Dağıtım Durumu**’nu görüntülemek için **İleri**’ye tıklayın.

    ![Özet sayfası](media/data-factory-build-your-first-pipeline-using-vs/summary-page.png)
3. **Dağıtım Durumu** sayfasında dağıtım sürecinin durumunu görmelisiniz. Dağıtımını gerçekleştirdikten sonra Son'a tıklayın.

Dikkat edilmesi gereken önemli noktalar şunlardır:

- **Abonelik, Microsoft.DataFactory ad alanını kullanacak şekilde kaydedilmemiş** hatasını alırsanız, aşağıdakilerden birini yapın ve yeniden yayımlamayı deneyin:
    - Azure PowerShell’de Data Factory sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın.
        ```powershell   
        Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
        ```
        Data Factory sağlayıcısının kayıtlı olduğunu onaylamak için aşağıdaki komutu çalıştırabilirsiniz.

        ```powershell
        Get-AzResourceProvider
        ```
    - Azure aboneliğini kullanarak [Azure portalında](https://portal.azure.com) oturum açın ve Data Factory dikey penceresine gidin (ya da) Azure portalında bir data factory oluşturun. Bu eylem sağlayıcıyı sizin için otomatik olarak kaydeder.
- Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş ve herkese görünür hale gelmiş olabilir.
- Data Factory örnekleri oluşturmak için Azure aboneliğinde yönetici veya ortak yönetici olmanız gerekir

### <a name="monitor-pipeline"></a>İşlem hattını izleme
Bu adımda, veri fabrikasının Diyagram Görünümünü kullanarak işlem hattını izleyeceksiniz. 

#### <a name="monitor-pipeline-using-diagram-view"></a>Diyagram Görünümünü kullanarak işlem hattını izleme
1. [Azure portalında](https://portal.azure.com/) oturum açıp aşağıdaki adımları uygulayın:
   1. **Diğer hizmetler** ve **Data factory’ler** öğesine tıklayın.
       
        ![Data factory’lere göz atma](./media/data-factory-build-your-first-pipeline-using-vs/browse-datafactories.png)
   2. Veri fabrikası listesinden veri fabrikanızın adını seçin (örneğin: **DataFactoryUsingVS09152016**).
   
       ![Data factory’nizi seçme](./media/data-factory-build-your-first-pipeline-using-vs/select-first-data-factory.png)
2. Veri fabrikanızın giriş sayfasında penceresinde **Diyagram**’a tıklayın.

    ![Diyagram kutucuğu](./media/data-factory-build-your-first-pipeline-using-vs/diagram-tile.png)
3. Diyagram Görünümü’nde, işlem hatlarına ve bu öğreticide kullanılan veri kümelerine bir genel bakış görürsünüz.

    ![Diyagram Görünümü](./media/data-factory-build-your-first-pipeline-using-vs/diagram-view-2.png)
4. İşlem hattındaki tüm etkinlikleri görüntülemek için diyagramdaki işlem hattına sağ tıklayın ve Açık İşlem Hattı’na tıklayın.

    ![İşlem hattı menüsünü açma](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-menu.png)
5. İşlem hattında HDInsightHive etkinliğini gördüğünüzü onaylayın.

    ![İşlem hattı görünümünü açma](./media/data-factory-build-your-first-pipeline-using-vs/open-pipeline-view.png)

    Önceki görünüme dönmek için en üstteki içerik haritası menüsünde **Data factory**’ye tıklayın.
6. **Diyagram Görünümü**’nde **AzureBlobInput** veri kümesine çift tıklayın. Dilimin **Hazır** durumunda olduğunu onaylayın. Dilimin Hazır durumda gösterilmesi birkaç dakika alabilir. Bir süre bekledikten sonra bu gerçekleşmiyorsa, girdi dosyasının (input.log) doğru kapsayıcıda (`adfgetstarted`) ve klasörde (`inputdata`) olup olmadığına bakın. Ayrıca, giriş veri kümesindeki **external** özelliğinin **true** olarak ayarlandığından emin olun. 

   ![Girdi dilimi hazır durumda](./media/data-factory-build-your-first-pipeline-using-vs/input-slice-ready.png)
7. **AzureBlobInput** dikey penceresini kapatmak için **X** işaretine tıklayın.
8. **Diyagram Görünümü**’nde **AzureBlobOutput** veri kümesine çift tıklayın. Dilimin işlenmekte olduğunu görürsünüz.

   ![Veri kümesi](./media/data-factory-build-your-first-pipeline-using-vs/dataset-blade.png)
9. İşlem tamamlandığında dilimi **Hazır** durumunda görürsünüz.

   > [!IMPORTANT]
   > İsteğe bağlı HDInsight kümesinin oluşturulması genellikle biraz zaman alır (yaklaşık 20 dakika). Bu nedenle, işlem hattının dilimi işlemesi için **yaklaşık 30 dakika** bekleyin.  
   
    ![Veri kümesi](./media/data-factory-build-your-first-pipeline-using-vs/dataset-slice-ready.png)    
10. Dilim **Hazır** durumunda olduğunda çıktı verileri için blob depolama alanınızın `partitioneddata` kapsayıcısında `adfgetstarted` klasörünü denetleyin.  

    ![çıktı verileri](./media/data-factory-build-your-first-pipeline-using-vs/three-ouptut-files.png)
11. Dilimin ayrıntılarını bir **Veri dilimi** dikey penceresinde görmek için dilime tıklayın.

    ![Veri dilimi ayrıntıları](./media/data-factory-build-your-first-pipeline-using-vs/data-slice-details.png)  
12. Bir etkinlik çalışmasına ilişkin ayrıntıları (bu senaryoda Hive etkinliği) bir **Etkinlik çalışma ayrıntıları** penceresinde görmek için **Etkinlik çalışma listesi** içinden bir etkinlik çalışmasına tıklayın. 
  
    ![Etkinlik çalışma ayrıntıları](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-blade.png)    

    Yürütülen Hive sorgusunu ve durum bilgilerini günlük dosyalarında görebilirsiniz. Bu günlükler her türlü sorunu gidermek için kullanışlıdır.  

Bu öğreticide oluşturduğunuz işlem hattını ve veri kümelerini izlemek üzere Azure Portal’ın ilişkin yönergeler için bkz. [Veri kümelerini ve işlem hatlarını izleme](data-factory-monitor-manage-pipelines.md).

#### <a name="monitor-pipeline-using-monitor--manage-app"></a>İzleme ve Yönetme Uygulamasını kullanarak işlem hattını izleme
İşlem hatlarınızı izlemek için İzleme ve Yönetme uygulamasını da kullanabilirsiniz. Bu uygulamanın kullanımına ilişkin ayrıntılı bilgi için bkz. [İzleme ve Yönetme Uygulamasını kullanarak Azure Data Factory işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md).

1. İzleme ve Yönetme kutucuğuna tıklayın.

    ![İzleme ve Yönetme kutucuğu](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-tile.png)
2. İzleme ve Yönetme uygulamasını görmeniz gerekir. **Başlangıç saati** ve **Bitiş saati** değerlerini işlem hattınızın başlangıç (04-01-2016 12:00 AM) ve bitiş saatleri (04-02-2016 12:00 AM) ile eşleşecek şekilde değiştirin ve **Uygula**’ya tıklayın.

    ![İzleme ve Yönetme Uygulaması](./media/data-factory-build-your-first-pipeline-using-vs/monitor-and-manage-app.png)
3. Bir etkinlik penceresinin ayrıntılarını görmek için **Etkinlik Pencereleri listesinden** seçim yapın.
    ![Etkinlik penceresi ayrıntıları](./media/data-factory-build-your-first-pipeline-using-vs/activity-window-details.png)

> [!IMPORTANT]
> Dilim başarıyla işlendiğinde girdi dosyası silinir. Bu nedenle, dilimi yeniden çalıştırmak veya öğreticiyi yeniden uygulamak isterseniz girdi dosyasını (input.log) `inputdata` kapsayıcısının `adfgetstarted` klasörüne yükleyin.

### <a name="additional-notes"></a>Ek notlar
- Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Örneğin, bir kaynaktan hedef veri deposuna veri kopyalama amaçlı bir Kopyalama Etkinliği ve giriş verilerini dönüştürmek üzere Hive betiği çalıştırma amaçlı bir HDInsight Hive etkinliği. Kopyalama Etkinliği tarafından desteklenen tüm kaynaklar ve havuzlar için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory tarafından desteklenen işlem hizmetlerinin listesi için bkz. [bağlantılı işlem hizmetleri](data-factory-compute-linked-services.md).
- Bağlı hizmetler veri depolarını veya işlem hizmetlerini Azure data factory’ye bağlar. Kopyalama Etkinliği tarafından desteklenen tüm kaynaklar ve havuzlar için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory tarafından desteklenen işlem hizmetlerinin listesi ve bunlar üzerinde çalışabilecek [dönüşüm etkinlikleri](data-factory-compute-linked-services.md) için [işlem bağlı hizmetleri](data-factory-data-transformation-activities.md) bölümüne bakın.
- Azure Depolama bağlı hizmet tanımında kullanılan JSON özellikleri hakkında ayrıntılı bilgi için bkz. [Azure Blob’a/Blob’dan veri taşıma](data-factory-azure-blob-connector.md#azure-storage-linked-service).
- İsteğe bağlı HDInsight kümesi yerine kendi HDInsight kümenizi kullanabilirsiniz. Ayrıntılar için bkz. [İşlem Bağlı Hizmetleri](data-factory-compute-linked-services.md).
-  Data Factory, sizin için önceki JSON ile **Linux tabanlı** bir HDInsight kümesi oluşturur. Ayrıntılar için bkz. [İsteğe Bağlı HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
- HDInsight kümesi JSON’da belirttiğiniz blob depolamada (linkedServiceName) bir **varsayılan kapsayıcı** oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmeti kullanıldığında, mevcut canlı bir küme olmadığı sürece bir dilim her işlendiğinde bir HDInsight kümesi oluşturulur (timeToLive). Küme, işlem tamamlandığında otomatik olarak silinir.
    
    Daha fazla dilim işlendikçe, Azure blob depolamanızda çok sayıda kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcı adları bir düzene sahiptir: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Azure blob depolamada kapsayıcı silmek için [Microsoft Storage Gezgini](https://storageexplorer.com/) gibi araçları kullanın.
- Şu anda, çıktı veri kümesi zamanlamayı yönetendir; bu nedenle etkinlik hiçbir çıktı oluşturmasa bile sizin bir çıktı veri kümesi oluşturmanız gerekir. Etkinlik herhangi bir girdi almazsa, girdi veri kümesi oluşturma işlemini atlayabilirsiniz. 
- Bu öğreticide, Azure Data Factory kullanarak verilerin nasıl kopyalanacağı gösterilmemektedir. Azure Data Factory kullanarak verileri kopyalama öğreticisi için bkz. [Öğretici: Blob Depolama’dan SQL Veritabanı’na veri kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-server-explorer-to-view-data-factories"></a>Data factory’leri görüntülemek için Sunucu Gezgini’ni kullanın
1. **Visual Studio**’nun menüsünde **Görünüm**’e ve **Sunucu Gezgini**’ne tıklayın.
2. Sunucu Gezgini penceresinde, **Azure**’ü ve **Data Factory**’yi genişletin. **Visual Studio'da oturum açın**’ı görürseniz Azure aboneliğiyle ilişkili **hesabı** girin ve **Devam**’a tıklayın. **parola** girip **Oturum aç**’a tıklayın. Visual Studio, aboneliğinizdeki tüm Azure data factory’leri hakkında bilgi almaya çalışır. Bu işlemin durumunu **Data Factory Görev Listesi** penceresinde görürsünüz.

    ![Sunucu Gezgini](./media/data-factory-build-your-first-pipeline-using-vs/server-explorer.png)
3. İstediğiniz data factory’ye sağ tıklayıp, mevcut bir data factory’ye dayandırılan Visual Studio projesi oluşturmak için **Data Factory’yi Yeni Projeye Aktar**’ı seçin.

    ![Data factory’yi dışarı aktarma](./media/data-factory-build-your-first-pipeline-using-vs/export-data-factory-menu.png)

## <a name="update-data-factory-tools-for-visual-studio"></a>Visual Studio için Data Factory araçlarını güncelleştirme
Visual Studio için Azure Data Factory araçlarını güncelleştirmek üzere aşağıdaki adımları uygulayın:

1. Menüde **Araçlar**’a tıklayın ve **Uzantılar ve Güncelleştirmeler**’i seçin.
2. Sol bölmede **Güncelleştirmeler**’i, sonra da **Visual Studio Galerisi**’ni seçin.
3. **Visual Studio için Azure Data Factory araçları**’nı seçin ve **Güncelleştir**’e tıklayın. Bu girişi görmüyorsanız araçların en son sürümü zaten yüklüdür.

## <a name="use-configuration-files"></a>Yapılandırma dosyalarını kullanma
Visual Studio'da bağlı hizmetler/tablolar/işlem hatları için her ortamda farklı özellik yapılandırmak amacıyla yapılandırma dosyalarını kullanabilirsiniz.

Azure Storage bağlı hizmeti için aşağıdaki JSON tanımını dikkate alın. Data Factory varlıklarını dağıttığınız ortama dayanan accountname ve accountkey farklı değerlerini (Dev/Test/Production) **connectionString** olarak belirtmek için. Her ortam için ayrı bir yapılandırma dosyası kullanarak bu davranışı elde edebilirsiniz.

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

### <a name="add-a-configuration-file"></a>Yapılandırma dosyası ekleme
Aşağıdaki adımları uygulayarak her ortam için bir yapılandırma dosyası ekleyin:   

1. Visual Studio çözümünüzde Data Factory projenize sağ tıklayın, **Ekle**’nin üzerine gelin ve **Yeni öğe**’ye tıklayın.
2. Soldaki yüklenmiş şablonlar listesinden **Config**’i ve **Yapılandırma Dosyası**’nı seçin ve yapılandırma dosyası için bir **ad** girip **Ekle**’ye tıklayın.

    ![Yapılandırma dosyasını ekleme](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Yapılandırma parametrelerini ve değerlerini aşağıda gösterilen biçimde ekleyin:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure sql server name>.database.windows.net,1433;Database=<Azure Sql database>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Bu örnek, Azure Storage bağlı hizmetinin ve Azure SQL bağlı hizmetinin connectionString özelliğini yapılandırır. Belirtilen adın sözdiziminin [JsonPath](https://goessner.net/articles/JsonPath/) olduğuna dikkat edin.   

    JSON’un aşağıdaki kodda gösterilen şekilde bir dizi değere sahip bir özelliği varsa:  

    ```json
    "structure": [
          {
              "name": "FirstName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
        }
    ],
    ```

    Özellikleri, aşağıdaki yapılandırma dosyasında gösterildiği gibi (sıfır tabanlı dizin oluşturma kullanın) yapılandırın:

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Boşluklu özellik adları
Özellik adında boşluklar varsa, aşağıdaki örnekte (veritabanı sunucu adı) gösterildiği gibi köşeli ayraç kullanın:

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Yapılandırma kullanarak çözümü dağıtma
Azure Data Factory varlıklarını VS’de dağıttığınızda, bu yayımlama işlemi için kullanmak istediğiniz yapılandırmayı belirtebilirsiniz.

Azure Data Factory projesindeki varlıkları yapılandırma dosyası kullanarak oluşturmak için:   

1. Data Factory projesine sağ tıklayıp, **Öğeleri Yayımla** iletişim kutusunu görüntülemek için **Yayımla**’ya tıklayın.
2. Mevcut bir data factory seçip ya da **Data factory yapılandır** sayfasında bir data factory oluşturulması için değerleri belirtip **İleri**’ye tıklayın.   
3. **Öğeleri Yayımla** sayfasında: **Dağıtım Yapılandırması seç** alanı için kullanılabilir yapılandırmaların açılan listesini görürsünüz.

    ![Yapılandırma dosyası seçme](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Kullanmak istediğiniz **yapılandırma dosyasını** seçip **İleri**’ye tıklayın.
5. **Özet** sayfasında JSON dosyasının adını gördüğünüzü doğrulayıp **İleri**’ye tıklayın.
6. Dağıtım işlemi sona erdikten sonra **Son**’a tıklayın.

Dağıtımı yaptığınızda, yapılandırma dosyasındaki değerler, varlıkların Azure Data Factory hizmetine dağıtılmasından önce JSON dosyalarındaki özelliklerin değerlerini ayarlamak için kullanılır.   

## <a name="use-azure-key-vault"></a>Azure Key Vault kullanma
Bağlantı dizeleri gibi hassas verilerin kod deposuna işlenmesi önerilmez ve genellikle güvenlik ilkesine aykırıdır. Hassas bilgileri Azure Key Vault’ta depolama ve Data Factory varlıklarını yayımlarken bu bilgileri kullanma hakkında bilgi için, GitHub üzerinde [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) örneğine bakın. Visual Studio için Secure Publish uzantısı, gizli anahtarların Key Vault’ta depolanmasına olanak tanır ve bağlı hizmetler/ dağıtım yapılandırmalarında bunların yalnızca başvuruları belirtilir. Bu başvurular, Azure’da Data Factory varlıkları yayımladığınızda çözümlenir. Bu dosyalar daha sonra herhangi bir gizli anahtar kullanıma sunulmadan kaynak depoya işlenebilir.

## <a name="summary"></a>Özet
Bu öğreticide, HDInsight hadoop kümesindeki Hive betiği çalıştırılarak verileri işlemek için bir Azure data factory oluşturdunuz. Aşağıdaki adımları uygulamak için Azure Portal’da Data Factory Düzenleyici’yi kullandınız:  

1. Oluşturulan Azure **data factory**.
2. Oluşturulan iki **bağlı hizmet**:
   1. Girdi/çıktı dosyalarını tutan Azure blob depolamanızı data factory’ye bağlamak için **Azure Storage** bağlı hizmeti.
   2. İsteğe bağlı HDInsight Hadoop kümesini data factory’ye bağlamak için isteğe bağlı **Azure HDInsight** bağlı hizmeti. Azure Data Factory, girdi verilerini işlemek, çıktı verilerini de oluşturmak için tam zamanında HDInsight Hadoop kümesi oluşturur.
3. İşlem hattındaki HDInsight Hive etkinliğiyle ilgili girdi ve çıktı verilerini açıklayan oluşturulan iki **veri kümesi**.
4. **HDInsight Hive** etkinliğine sahip oluşturulan bir **işlem hattı**.  

## <a name="next-steps"></a>Sonraki Adımlar
Bu makalede, isteğe bağlı HDInsight kümesinde bir Hive betiği çalıştıran dönüştürme etkinliğine (HDInsight Etkinliği) sahip işlem hattı oluşturdunuz. Verileri Azure Blob’tan Azure SQL’e kopyalamak için Kopyalama Etkinliği’nin kullanılması hakkında bilgi için bkz. [Öğretici: Verileri Azure blob’tan Azure SQL’e kopyalama](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliği diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Ayrıntılı bilgi için bkz. [Data Factory’de zamanlama ve yürütme](data-factory-scheduling-and-execution.md). 


## <a name="see-also"></a>Ayrıca Bkz.

| Konu | Açıklama |
|:--- |:--- |
| [İşlem hatları](data-factory-create-pipelines.md) |Bu makale, Azure Data Factory’de işlem hatlarını ve etkinlikleri anlamanıza, senaryonuz ya da işletmeniz için veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı öğrenmenize yardımcı olur. |
| [Veri kümeleri](data-factory-create-datasets.md) |Bu makale, Azure Data Factory’deki veri kümelerini anlamanıza yardımcı olur. |
| [Veri Dönüştürme Etkinlikleri](data-factory-data-transformation-activities.md) |Bu makalede, Azure Data Factory’nin desteklediği veri dönüştürme etkinliklerinin (bu öğreticide kullandığınız HDInsight Hive dönüştürmesi gibi) bir listesi sağlanmaktadır. |
| [Zamanlama ve yürütme](data-factory-scheduling-and-execution.md) |Bu makalede Azure Data Factory uygulama modelinin zamanlama ve yürütme yönleri açıklanmaktadır. |
| [İzleme Uygulaması kullanılarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) |Bu makalede İzleme ve Yönetim Uygulaması kullanılarak işlem hatlarını izleme, yönetme ve hatalarını ayıklama işlemleri açıklanmaktadır. |
