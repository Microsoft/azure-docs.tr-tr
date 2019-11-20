---
title: İlk Data Factory 'nizi derleme (REST)
description: Bu öğreticide Data Factory REST API’sini kullanarak örnek bir Azure Data Factory işlem hattı oluşturursunuz.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/01/2017
ms.openlocfilehash: 7644bac01784ea795f24b967223b0eb37354ca87
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682970"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Öğretici: Data Factory REST API kullanarak ilk Azure data factory’nizi derleme
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager Şablonu](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory'nin geçerli sürümünü kullanıyorsanız [Hızlı Başlangıç: Azure Data Factory'yi kullanarak veri fabrikası oluşturma](../quickstart-create-data-factory-rest-api.md) konusunu inceleyin.

Bu makalede, ilk Azure data factory’nizi oluşturmak için Data Factory REST API’sini kullanırsınız. Diğer araçları/SDK’ları kullanarak öğreticiyi uygulamak için açılır listedeki seçeneklerden birini belirleyin.

Bu öğreticideki işlem hattı bir etkinlik içerir: **HDInsight Hive etkinliği**. Bu etkinlik, Azure HDInsight kümesi üzerinde çıkış verileri üretmek üzere giriş verilerini dönüştüren bir hive betiği çalıştırır. İşlem hattı, belirtilen başlangıç ve bitiş saatleri arasında ayda bir kez çalışacak şekilde zamanlanmıştır.

> [!NOTE]
> Bu makalede REST API'nin tamamı ele alınmamaktadır. REST API ile ilgili kapsamlı belgeler için bkz. [Data Factory REST API Başvurusu](/rest/api/datafactory/).
> 
> Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [Data Factory'de zamanlama ve yürütme](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [Öğreticiye Genel Bakış](data-factory-build-your-first-pipeline.md) makalesinin tamamını okuyun ve **ön koşul** adımlarını tamamlayın.
* [Curl](https://curl.haxx.se/dlwiz/) aracını makinenize yükleyin. Bir veri fabrikası oluşturmak için CURL aracını REST komutlarıyla kullanırsınız.
* Aşağıdakileri yapmak için [bu makaledeki](../../active-directory/develop/howto-create-service-principal-portal.md) yönergeleri izleyin:
  1. Azure Active Directory’de **ADFGetStartedApp** adlı bir Web uygulaması oluşturun.
  2. **İstemci kimliği** ve **gizli anahtarı** alın.
  3. **İstemci kimliğini** alın.
  4. **ADFGetStartedApp** uygulamasını **Data Factory Katılımcısı** rolüne atayın.
* [Azure PowerShell](/powershell/azure/overview)'i yükleyin.
* **PowerShell**’i başlatın ve aşağıdaki komutu çalıştırın. Bu öğreticide sonuna kadar Azure PowerShell’i açık tutun. Kapatıp yeniden açarsanız komutları yeniden çalıştırmanız gerekir.
  1. **Connect-AzAccount** komutunu çalıştırın ve Azure Portal oturum açmak için kullandığınız kullanıcı adını ve parolayı girin.
  2. Bu hesaba ilişkin tüm abonelikleri görüntülemek için **Get-AzSubscription** komutunu çalıştırın.
  3. **Get-AzSubscription-SubscriptionName Nameofazuyeniden adlandırma | komutunu çalıştırın** Birlikte çalışmak istediğiniz aboneliği seçmek için set-AzContext. **NameOfAzureSubscription** değerini Azure aboneliğinizin adıyla değiştirin.
* PowerShell’de aşağıdaki komutu çalıştırarak **ADFTutorialResourceGroup** adlı bir Azure kaynak grubu oluşturun:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

   Bu öğreticideki adımlardan bazıları ADFTutorialResourceGroup adlı kaynak grubunu kullandığınızı varsayar. Farklı bir kaynak grubu kullanıyorsanız, bu öğreticide kullanılan ADFTutorialResourceGroup yerine kaynak grubunuzun adını kullanmanız gerekir.

## <a name="create-json-definitions"></a>JSON tanımları oluşturma
Curl.exe’nin bulunduğu klasörde aşağıdaki JSON dosyalarını oluşturun.

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Adın genel olarak benzersiz olması gerektiğinden, ADFCopyTutorialDF’yi benzersiz bir ada dönüştürmek için önüne/sonuna bir şeyler eklemek isteyebilirsiniz.
>
>

```JSON
{
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> **accountname** ve **accountkey** sözcüklerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alabileceğinizi öğrenmek için [Depolama hesabınızı yönetme](../../storage/common/storage-account-manage.md#access-keys) sayfasındaki depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma bilgilerine bakın.
>
>

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.JSON

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

Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

| Özellik | Açıklama |
|:--- |:--- |
| clusterSize |HDInsight kümesinin boyutudur. |
| timeToLive |Silinmeden önce HDInsight kümesinin boşta kalma süresini belirtir. |
| linkedServiceName |HDInsight tarafından oluşturulan günlükleri depolamak için kullanılan depolama hesabını belirtir |

Aşağıdaki noktalara dikkat edin:

* Data Factory, sizin için yukarıdaki JSON ile **Linux tabanlı** bir HDInsight kümesi oluşturur. Ayrıntılar için bkz. [İsteğe Bağlı HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).
* İsteğe bağlı HDInsight kümesi yerine **kendi HDInsight kümenizi** kullanabilirsiniz. Ayrıntılar için bkz. [HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
* HDInsight kümesi JSON’da belirttiğiniz blob depolamada (**linkedServiceName**) bir **varsayılan kapsayıcı** oluşturur. HDInsight, küme silindiğinde bu kapsayıcıyı silmez. Bu davranış tasarım gereğidir. İsteğe bağlı HDInsight bağlı hizmetiyle HDInsight kümesi her oluşturulduğunda burada mevcut canlı bir küme (**timeToLive**) olmadıkça bir dilim işlenir ve işlem bittiğinde silinir.

    Daha fazla dilim işlendikçe, Azure blob depolamanızda çok sayıda kapsayıcı görürsünüz. İşlerin sorunları giderilmesi için bunlara gerek yoksa, depolama maliyetini azaltmak için bunları silmek isteyebilirsiniz. Bu kapsayıcıların adları şu deseni izler: "adf**yourdatafactoryname**-**linkedservicename**-datetimestamp". Azure blob depolamada kapsayıcı silmek için [Microsoft Storage Gezgini](https://storageexplorer.com/) gibi araçları kullanın.

Ayrıntılar için bkz. [İsteğe Bağlı HDInsight Bağlı Hizmeti](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

### <a name="inputdatasetjson"></a>inputdataset.json

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

### <a name="outputdatasetjson"></a>outputdataset.json

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

Bu JSON, işlem hattındaki bir etkinliğin çıktı verilerini temsil eden **AzureBlobOutput** adlı veri kümesini tanımlamaktadır. Ek olarak, sonuçların **adfgetstarted** adlı blob kapsayıcısında ve **partitioneddata** adlı klasörde depolandığını belirtir. Burada, **availability** bölümü çıktı veri kümesinin aylık tabanda oluşturulduğunu belirtiyor.

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> **storageaccountname**’i Azure depolama hesabınızın adıyla değiştirin.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

JSON parçacığında, HDInsight kümesinde veri işlemek için Hive’ı kullanan tek bir etkinlikten oluşan bir işlem hattı oluşturuyorsunuz.

**partitionweblogs.hql** Hive betik dosyası Azure depolama hesabında (scriptLinkedService tarafından belirtilen **StorageLinkedService** adıyla) ve **adfgetstarted** kapsayıcısındaki **betik** klasöründe depolanır.

**defines** bölümü hive betiğine Hive yapılandırma değerleri olarak (örn., ${hiveconf:inputtable}, ${hiveconf:partitionedtable}) geçirilecek çalışma zamanı ayarlarını belirtir.

İşlem hattının **start** ve **end** özellikleri işlem hattının etkin dönemini belirtir.

JSON etkinliğinde, Hive betiğinin **linkedServiceName** – **HDInsightOnDemandLinkedService** tarafından belirtilen işlemde çalışacağını belirtirsiniz.

> [!NOTE]
> Önceki örnekte kullanılan JSON özellikleri hakkında ayrıntılı bilgi için [Azure Data Factory’deki işlem hatları ve etkinlikler](data-factory-create-pipelines.md) sayfasındaki “JSON İşlem Hatları” bölümüne bakın.
>
>

## <a name="set-global-variables"></a>Genel değişkenleri ayarlama
Azure PowerShell’de değerleri kendi değerlerinizle değiştirdikten sonra aşağıdaki komutları çalıştırın:

> [!IMPORTANT]
> İstemci kimliği, istemci parolası, kiracı kimliği ve abonelik kimliğini edinme konusunda yönergeler için [Önkoşullar](#prerequisites) bölümüne bakın.
>
>

```powershell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>AAD ile kimlik doğrulama

```powershell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Veri fabrikası oluşturma
Bu adımda, **FirstDataFactoryREST** adlı bir Azure Data Factory oluşturursunuz. Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Örneğin, verileri bir kaynaktan hedef veri deposuna kopyalamak için Kopyalama Etkinliği; verileri dönüştürecek bir Hive betiğini çalıştırmak için de HDInsight Hive etkinliği. Veri fabrikasını oluşturmak için aşağıdaki komutu çalıştırın:

1. Komutu **cmd** adlı değişkene atayın.

    Burada belirttiğiniz veri fabrikasının (ADFCopyTutorialDF) adının **datafactory.json**’da belirtilen adla eşleştiğini doğrulayın.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri fabrikası başarıyla oluşturulduysa, **results** bölümünde veri fabrikasının JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```powershell
    Write-Host $results
    ```

Aşağıdaki noktalara dikkat edin:

* Azure Data Factory adı küresel olarak benzersiz olmalıdır. Sonuçlarda **Veri fabrikası adı “FirstDataFactoryREST” kullanılamıyor** hatasını görürseniz aşağıdaki adımları uygulayın:
  1. **datafactory.json** dosyasında adı değiştirin (örneğin, adınızFirstDataFactoryREST). Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.
  2. **$cmd** değişkenine bir değerin atandığı ilk komutta, FirstDataFactoryREST’i yeni adla değiştirip komutu çalıştırın.
  3. Veri fabrikasını oluşturmak ve işlemin sonuçlarını yazdırmak üzere REST API’yi çağırmak için sonraki iki komutu çalıştırın.
* Data Factory örnekleri oluşturmak için, Azure aboneliğinde katılımcı/yönetici rolünüz olmalıdır
* Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş ve herkese görünür hale gelmiş olabilir.
* Şu hatayı alırsanız: "**Abonelik, Microsoft.DataFactory ad alanını kullanacak şekilde kaydedilmemiş**", aşağıdakilerden birini yapın ve yeniden yayımlamayı deneyin:

  * Azure PowerShell’de Data Factory sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Data Factory sağlayıcısının kayıtlı olduğunu onaylamak için aşağıdaki komutu çalıştırabilirsiniz:
    ```powershell
    Get-AzResourceProvider
    ```
  * Azure aboneliğini kullanarak [Azure portalında](https://portal.azure.com) oturum açın ve Data Factory dikey penceresine gidin (ya da) Azure portalında bir data factory oluşturun. Bu eylem sağlayıcıyı sizin için otomatik olarak kaydeder.

İşlem hattı oluşturmadan önce, öncelikle birkaç Data Factory varlığı oluşturmanız gerekir. Veri depolarını/işlemlerini veri deponuza bağlamak için önce bağlı hizmetleri oluşturun ve bağlı veri depolarında veriyi tanıtmak için girdi ve çıktı veri kümelerini tanımlayın.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Bu adımda, Azure Depolama hesabınızı ve isteğe bağlı Azure HDInsight kümesini data factory’nize bağlarsınız. Azure Depolama hesabı, bu örnekteki işlem hattı için girdi ve çıktı verilerini tutar. HDInsight bağlı hizmeti, bu örnekteki işlem hattının etkinliğinde belirtilen Hive betiğini çalıştırmak için kullanılır.

### <a name="create-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma
Bu adımda, Azure Depolama hesabınızı veri fabrikanıza bağlarsınız. Bu öğreticide girdi/çıktı verilerin ve HQL betiğini depolamak için aynı Azure Depolama hesabını kullanırsınız.

1. Komutu **cmd** adlı değişkene atayın.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Bağlı hizmet başarıyla oluşturulduysa, **results** bölümünde bağlı hizmetin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```powershell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Azure HDInsight bağlı hizmeti oluşturma
Bu adımda, isteğe bağlı HDInsight kümesini data factory’nize bağlarsınız. HDInsight kümesi çalışma zamanında otomatik olarak oluşturulur ve işlenmesi bittiğinde ve belirtilen sürede boşta kalırsa silinir. İsteğe bağlı HDInsight kümesi yerine kendi HDInsight kümenizi kullanabilirsiniz. Ayrıntılar için bkz. [İşlem Bağlı Hizmetleri](data-factory-compute-linked-services.md).

1. Komutu **cmd** adlı değişkene atayın.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Bağlı hizmet başarıyla oluşturulduysa, **results** bölümünde bağlı hizmetin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```powershell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Bu adımda, Hive işlenmesi için girdi ve çıktı verilerini temsil edecek veri kümeleri oluşturursunuz. Bu veri kümeleri, bu öğreticide daha önce oluşturduğunuz **StorageLinkedService** öğesine başvurur. Bağlı hizmet Azure Storage hesabını belirtirken, veri kümeleri de girdi ve çıktı verilerini tutan depolama biriminde kapsayıcı, klasör, dosya adı belirtir.

### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma
Bu adımda, Azure Blob depolamada depolanan girdi verilerini göstermek için girdi veri kümesini oluşturursunuz.

1. Komutu **cmd** adlı değişkene atayın.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri kümesi başarıyla oluşturulduysa, **results** bölümünde veri kümesinin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```powershell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Çıktı veri kümesi oluşturma
Bu adımda, Azure Blob depolamada depolanan çıktı verilerini göstermek için çıktı veri kümesini oluşturursunuz.

1. Komutu **cmd** adlı değişkene atayın.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri kümesi başarıyla oluşturulduysa, **results** bölümünde veri kümesinin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```powershell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>İşlem hattı oluşturma
Bu adımda, **HDInsightHive** etkinliğiyle ilk işlem hattınızı oluşturursunuz. Girdi diliminin ayda bir (frequency: Month, interval: 1) kullanılabilir, çıktı dilimi ayda bir oluşturulur ve etkinlik zamanlayıcı özelliği de ayda bir olacak şekilde ayarlanır. Çıktı veri kümesi ve etkinlik zamanlayıcı ayarlarının eşleşmesi gerekir. Şu anda, çıktı veri kümesi zamanlamayı yönetendir; bu nedenle etkinlik hiçbir çıktı oluşturmasa bile sizin bir çıktı veri kümesi oluşturmanız gerekir. Etkinlik herhangi bir girdi almazsa, girdi veri kümesi oluşturma işlemini atlayabilirsiniz.

Azure blob depolamada **adfgetstarted/inputdata** klasöründeki **input.log** dosyasını gördüğünüzü doğrulayın ve işlem hattına dağıtmak için aşağıdaki komutu çalıştırın. **start** ve **end** zamanları geçmişe ayarlanmış ve **isPaused** yanlış olarak ayarlanmış olduğundan işlem hattı (işlem hattında etkinlik) dağıtıldıktan hemen sonra çalışır.

1. Komutu **cmd** adlı değişkene atayın.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri kümesi başarıyla oluşturulduysa, **results** bölümünde veri kümesinin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```powershell
    Write-Host $results
    ```
4. Tebrikler, Azure PowerShell kullanarak ilk işlem hattınızı başarıyla oluşturdunuz.

## <a name="monitor-pipeline"></a>İşlem hattını izleme
Bu adımda, Data Factory REST API’sini kullanarak işlem hattı tarafından üretilmekte olan dilimleri izlersiniz.

```powershell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> İsteğe bağlı HDInsight kümesinin oluşturulması genellikle biraz zaman alır (yaklaşık 20 dakika). Bu nedenle, işlem hattının dilimi işlemesi için **yaklaşık 30 dakika** bekleyin.
>
>

Çıktı dilimini **Hazır** veya **Başarısız** durumunda görene kadar Invoke komutunu ve sonraki komutu çalıştırın. Dilim Hazır durumunda olduğunda, çıktı verileri için blob depolamanızın **adfgetstarted** klasöründe **partitioneddata** klasörünü denetleyin.  İsteğe bağlı HDInsight kümesinin oluşturulması genellikle biraz zaman alır.

![çıktı verileri](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> Dilim başarıyla işlendiğinde girdi dosyası silinir. Bu nedenle, dilimi yeniden çalıştırmak veya öğreticiyi yeniden uygulamak isterseniz girdi dosyasını (input.log) adfgetstarted kapsayıcısının inputdata klasörüne yükleyin.
>
>

Dilimleri izlemek ve sorunları gidermek için Azure Portal’ı da kullanabilirsiniz. Ayrıntılar için bkz. [Azure Portal’ı kullanarak işlem hatlarını izleme](data-factory-monitor-manage-pipelines.md).

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
| [Data Factory REST API Başvurusu](/rest/api/datafactory/) |Data Factory cmdlet'leri hakkında kapsamlı belgelere bakma |
| [İşlem hatları](data-factory-create-pipelines.md) |Bu makale, Azure Data Factory’de işlem hatlarının ve etkinliklerini anlamanıza ve senaryonuz ya da işletmeniz için uçtan uca veri odaklı iş akışları oluşturmak amacıyla bunları nasıl kullanacağınızı anlamanıza yardımcı olur. |
| [Veri kümeleri](data-factory-create-datasets.md) |Bu makale, Azure Data Factory’deki veri kümelerini anlamanıza yardımcı olur. |
| [Zamanlama ve Yürütme](data-factory-scheduling-and-execution.md) |Bu makalede Azure Data Factory uygulama modelinin zamanlama ve yürütme yönleri açıklanmaktadır. |
| [İzleme Uygulaması kullanılarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md) |Bu makalede İzleme ve Yönetim Uygulaması kullanılarak işlem hatlarını izleme, yönetme ve hatalarını ayıklama işlemleri açıklanmaktadır. |
