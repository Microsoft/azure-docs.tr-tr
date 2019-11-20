---
title: 'Öğretici: Azure Data Factory işlem hattı oluşturmak için REST API kullanma '
description: Bu öğreticide, Azure blob depolama alanından Azure SQL veritabanına veri kopyalamak için REST API kullanarak Kopyalama Etkinliği içeren Azure Data Factory işlem hattı oluşturursunuz.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 860b2f20b4ffda0a1a588ed5d5893ad2c0521a43
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682842"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Öğretici: Verileri kopyalamak amacıyla Azure Data Factory işlem hattı oluşturmak için REST API kullanma 
> [!div class="op_single_selector"]
> * [Genel bakış ve önkoşullar](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopyalama Sihirbazı](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager şablonu](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API’si](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız bkz. [kopyalama etkinliği öğreticisi](../quickstart-create-data-factory-rest-api.md). 

Bu makalede, Azure blob depolama alanından Azure SQL veritabanına veri kopyalayan bir işlem hattıyla veri fabrikası oluşturmak için REST API’yi nasıl kullanacağınızı öğreneceksiniz. Azure Data Factory’yi ilk kez kullanıyorsanız bu öğreticiyi tamamlamadan önce [Azure Data Factory’ye Giriş](data-factory-introduction.md) makalesini okuyun.   

Bu öğreticide, içinde bir etkinlik olan işlem hattı oluşturursunuz: Kopyalama Etkinliği. Kopyalama etkinliği, verileri, desteklenen bir veri deposundan desteklenen bir havuz veri deposuna kopyalar. Kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Etkinlik, çeşitli veri depolama alanları arasında güvenli, güvenilir ve ölçeklenebilir bir yolla veri kopyalayabilen genel olarak kullanılabilir bir hizmet tarafından desteklenir. Kopyalama Etkinliği hakkında daha fazla bilgi için bkz. [Veri Taşıma Etkinlikleri](data-factory-data-movement-activities.md).

Bir işlem hattında birden fazla etkinlik olabilir. Bir etkinliğin çıkış veri kümesini diğer etkinliğin giriş veri kümesi olarak ayarlayarak iki etkinliği zincirleyebilir, yani bir etkinliğin diğerinden sonra çalıştırılmasını sağlayabilirsiniz. Daha fazla bilgi için bkz. [bir işlem hattında birden fazla etkinlik](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> Bu makale, Data Factory REST API’sinin tamamını kapsamaz. Data Factory cmdlet’leri hakkında kapsamlı belgeler için bkz. [Data Factory REST API Başvurusu](/rest/api/datafactory/).
>  
> Bu öğreticideki veri işlem hattı, bir kaynak veri deposundaki verileri hedef veri deposuna kopyalar. Azure Data Factory kullanarak verileri dönüştürme hakkında bir öğretici için bkz. [Öğretici: Hadoop kümesi kullanarak verileri dönüştürmek için işlem hattı oluşturma](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Ön koşullar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* [Öğreticiye Genel Bakış](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) bölümünü inceleyin ve **ön koşul** adımlarını tamamlayın.
* [Curl](https://curl.haxx.se/dlwiz/) aracını makinenize yükleyin. Bir veri fabrikası oluşturmak için Curl aracını REST komutlarıyla kullanırsınız. 
* Aşağıdakileri yapmak için [bu makaledeki](../../active-directory/develop/howto-create-service-principal-portal.md) yönergeleri izleyin: 
  1. Azure Active Directory’de **ADFCopyTutorialApp** adlı bir Web uygulaması oluşturun.
  2. **İstemci kimliği** ve **gizli anahtarı** alın. 
  3. **İstemci kimliğini** alın. 
  4. **ADFCopyTutorialApp** uygulamasını **Data Factory Katılımcısı** rolüne atayın.  
* [Azure PowerShell](/powershell/azure/overview)'i yükleyin.  
* **PowerShell**’i başlatın ve aşağıdaki adımları uygulayın. Bu öğreticide sonuna kadar Azure PowerShell’i açık tutun. Kapatıp yeniden açarsanız komutları yeniden çalıştırmanız gerekir.
  
  1. Aşağıdaki komutu çalıştırın ve Azure portalda oturum açmak için kullandığınız kullanıcı adı ve parolayı girin:
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Bu hesapla ilgili tüm abonelikleri görmek için aşağıdaki komutu çalıştırın:

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Çalışmak isteğiniz aboneliği seçmek için aşağıdaki komutu çalıştırın. **&lt;NameOfAzureSubscription**&gt; değerini Azure aboneliğinizin adıyla değiştirin. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. PowerShell’de aşağıdaki komutu çalıştırarak **ADFTutorialResourceGroup** adlı bir Azure kaynak grubu oluşturun:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Kaynak grubu zaten varsa bunun güncelleştirileceğini mi (Y) yoksa (N) olarak tutulacağını mı belirtirsiniz. 
     
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
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> **accountname** ve **accountkey** sözcüklerini Azure depolama hesabınızın adı ve anahtarıyla değiştirin. Depolama erişim anahtarınızı nasıl alacağınız hakkında bilgi için bkz. [Depolama erişim anahtarlarını görüntüleme, kopyalama ve yeniden oluşturma](../../storage/common/storage-account-manage.md#access-keys).

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

JSON özellikleri hakkındaki ayrıntılar için bkz. [Azure Depolama bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### <a name="azuresqllinkedservicejson"></a>azuressqllinkedservice. JSON
> [!IMPORTANT]
> **servername**, **databasename**, **username** ve **password** sözcüklerini Azure SQL sunucunuzun adı, SQL veritabanınızın adı, kullanıcı hesabınız ve hesap parolanızla değiştirin.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

JSON özellikleri hakkındaki ayrıntılar için bkz. [Azure SQL bağlı hizmeti](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
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
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

| Özellik | Açıklama |
|:--- |:--- |
| type | Veriler Azure blob depolama alanında yer aldığından type özelliği **AzureBlob** olarak ayarlanmıştır. |
| linkedServiceName | Daha önce oluşturduğunuz **AzureStorageLinkedService**’e başvurur. |
| folderPath | blob **kapsayıcıyı** ve girdi blob’larını içeren **klasörü** belirtir. Bu öğreticide adftutorial, blob kapsayıcısıdır ve klasör, kök klasördür. | 
| fileName | Bu özellik isteğe bağlıdır. Bu özelliği atarsanız tüm folderPath dosyaları alınır. Bu öğreticide fileName için **emp.txt** belirtilir, bu nedenle işlem için yalnızca bu dosya seçilir. |
| format -> type |Girdi dosyası metin biçiminde olduğundan **TextFormat**'ı kullanırız. |
| columnDelimiter | Girdi dosyasındaki sütunlar, **virgül (`,`)** ile ayrılmıştır. |
| frequency/interval | frequency **Saat**, interval da **1** olarak ayarlanmıştır. Bu, girdi dilimlerinin **saatlik** olarak kullanılabileceğini belirtir. Başka bir deyişle, Data Factory hizmeti belirttiğiniz blob kapsayıcısının (**adftutorial**) kök klasöründe girdi verilerini saatte bir kere arar. İşlem hattı başlangıç ve bitiş zamanlarındaki verileri arar, bu zamanlardan önceki veya sonraki verileri aramaz.  |
| external | Bu özellik, veriler bu işlem hattı tarafından oluşturulmazsa **true** olarak ayarlanır. Bu öğreticideki girdi verileri, bu işlem hattı tarafından oluşturulmayan emp.txt dosyasında bulunur, bu nedenle bu özelliği true olarak ayarlarız. |

Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure Blob bağlayıcısı makalesi](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
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
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "emp"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Aşağıdaki tabloda, kod parçacığında kullanılan JSON özellikleri için açıklamalar verilmektedir:

| Özellik | Açıklama |
|:--- |:--- |
| type | type özelliği, veriler Azure SQL veritabanındaki bir tabloya kopyalandığından **AzureSqlTable** olarak ayarlanır. |
| linkedServiceName | Daha önce oluşturduğunuz **AzureSqlLinkedService**’e başvurur. |
| tableName | Verilerin kopyalandığı **tabloyu** belirtir. | 
| frequency/interval | frequency **Saatlik** ve interval **1** olarak ayarlanır. Bu durumda çıktı dilimleri, işlem hattı başlangıç ve bitiş zamanları arasında **saatlik** olarak üretilir, bu zamanlardan önce veya sonra üretilmez.  |

Veritabanındaki emp tablosunda üç sütun vardır: **ID**, **FirstName** ve **LastName**. ID bir kimlik sütunu olduğundan, burada yalnızca **FirstName** ve **LastName** değerlerini belirtmeniz gerekir.

Bu JSON özellikleri hakkında daha fazla bilgi için bkz. [Azure SQL bağlayıcısı makalesi](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Aşağıdaki noktalara dikkat edin:

- Etkinlikler bölümünde, **türü** **Copy** olarak ayarlanmış yalnızca bir etkinlik vardır. Kopyalama etkinliği hakkında daha fazla bilgi için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). Data Factory çözümlerinde, [veri dönüştürme etkinliklerini](data-factory-data-transformation-activities.md) de kullanabilirsiniz.
- Etkinlik girdisi **AzureBlobInput** olarak, etkinlik çıktısıysa **AzureSqlOutput** olarak ayarlanmıştır. 
- **typeProperties** bölümünde **BlobSource** kaynak türü, **SqlSink** de havuz türü olarak belirtilir. Kaynak ve havuz olarak kopyalama etkinliği tarafından desteklenen veri depolarının eksiksiz listesi için bkz. [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Kaynak/havuz olarak desteklenen belirli bir veri deposunu nasıl kullanacağınızı öğrenmek için tablodaki bağlantıya tıklayın.  
 
**start** özelliğinin değerini geçerli günle, **end** değerini de sonraki günle değiştirin. Tarih saatin yalnızca tarih bölümünü belirtip saat bölümünü atlayabilirsiniz. Örneğin, "2017-02-03", "2017-02-03T00:00:00Z" ile eşdeğerdir
 
Başlangıç ve bitiş tarih saatleri [ISO biçiminde](https://en.wikipedia.org/wiki/ISO_8601) olmalıdır. Örneğin: 2016-10-14T16:32:41Z. **End** zamanı isteğe bağlıdır; ancak bu öğreticide bunu kullanacağız. 
 
**end** özelliği için değer belirtmezseniz "**start + 48 hours**" olarak hesaplanır. İşlem hattını süresiz olarak çalıştırmak için **end** özelliği değerini **9999-09-09** olarak ayarlayın.
 
Önceki örnekte, her veri dilimi saatlik oluşturulduğundan 24 veri dilimi vardır.

İşlem hattı tanımındaki JSON özelliklerinin açıklamaları için [işlem hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Kopyalama etkinliği tanımındaki JSON özelliklerinin açıklamaları için bkz. [veri taşıma etkinlikleri](data-factory-data-movement-activities.md). BlobSource tarafından desteklenen JSON özelliklerinin açıklamaları için bkz. [Azure Blob bağlayıcısı makalesi](data-factory-azure-blob-connector.md). SqlSink tarafından desteklenen JSON özelliklerinin açıklamaları için bkz. [Azure SQL Veritabanı bağlayıcısı makalesi](data-factory-azure-sql-connector.md).

## <a name="set-global-variables"></a>Genel değişkenleri ayarlama
Azure PowerShell’de değerleri kendi değerlerinizle değiştirdikten sonra aşağıdaki komutları çalıştırın:

> [!IMPORTANT]
> İstemci kimliği, istemci parolası, kiracı kimliği ve abonelik kimliğini edinme konusunda yönergeler için [Önkoşullar](#prerequisites) bölümüne bakın.   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Kullanmakta olduğunuz veri fabrikasının adını güncelleştirdikten sonra aşağıdaki komutu çalıştırın: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>AAD ile kimlik doğrulama
Azure Active Directory (AAD) ile kimlik doğrulamak için aşağıdaki komutu çalıştırın: 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Veri fabrikası oluşturma
Bu adımda, **ADFCopyTutorialDF** adlı bir Azure Data Factory oluşturacaksınız. Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem hattında bir veya daha fazla etkinlik olabilir. Örneğin, bir kaynaktan hedef veri depolama alanına veri kopyalamak için bir Kopyalama Etkinliği. Giriş verilerini ürün çıktı verilerine dönüştürmek için bir Hive betiği çalıştırmak üzere kullanılan bir HDInsight Hive etkinliği. Veri fabrikasını oluşturmak için aşağıdaki komutu çalıştırın: 

1. Komutu **cmd** adlı değişkene atayın. 
   
    > [!IMPORTANT]
    > Burada belirttiğiniz veri fabrikasının (ADFCopyTutorialDF) adının **datafactory.json**’da belirtilen adla eşleştiğini doğrulayın. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri fabrikası başarıyla oluşturulduysa, **results** bölümünde veri fabrikasının JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.  
   
    ```
    Write-Host $results
    ```

Aşağıdaki noktalara dikkat edin:

* Azure Data Factory adı küresel olarak benzersiz olmalıdır. Sonuçlarda **Veri fabrikası adı “ADFCopyTutorialDF” kullanılamıyor** hatasını görürseniz aşağıdaki adımları uygulayın:  
  
  1. **datafactory.json** dosyasında adı değiştirin (örneğin, adınızADFCopyTutorialDF).
  2. **$cmd** değişkenine bir değerin atandığı ilk komutta, ADFCopyTutorialDF’yi yeni adla değiştirip komutu çalıştırın. 
  3. Veri fabrikasını oluşturmak ve işlemin sonuçlarını yazdırmak üzere REST API’yi çağırmak için sonraki iki komutu çalıştırın. 
     
     Data Factory yapıtlarının adlandırma kuralları için [Data Factory - Adlandırma Kuralları](data-factory-naming-rules.md) konusuna bakın.
* Data Factory örnekleri oluşturmak için, Azure aboneliğinde katılımcı/yönetici rolünüz olmalıdır
* Veri fabrikasının adı gelecekte bir DNS adı olarak kaydedilmiş ve herkese görünür hale gelmiş olabilir.
* Şu hatayı alırsanız: "**Abonelik, Microsoft.DataFactory ad alanını kullanacak şekilde kaydedilmemiş**", aşağıdakilerden birini yapın ve yeniden yayımlamayı deneyin: 
  
  * Azure PowerShell’de Data Factory sağlayıcısını kaydetmek için aşağıdaki komutu çalıştırın: 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Data Factory sağlayıcısının kayıtlı olduğunu onaylamak için aşağıdaki komutu çalıştırabilirsiniz. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Azure aboneliğini kullanarak [Azure portalında](https://portal.azure.com) oturum açın ve Data Factory dikey penceresine gidin (ya da) Azure portalında bir data factory oluşturun. Bu eylem sağlayıcıyı sizin için otomatik olarak kaydeder.

İşlem hattı oluşturmadan önce, öncelikle birkaç Data Factory varlığı oluşturmanız gerekir. İlk olarak kaynak ve hedef veri depolarını kendi veri deponuza bağlamak için bağlı hizmetler oluşturun. Daha sonra, bağlı veri depolarındaki verileri temsil eden girdi ve çıktı veri kümeleri tanımlayın. Son olarak bu veri kümelerini kullanan bir etkinlik ile işlem hattını oluşturun.

## <a name="create-linked-services"></a>Bağlı hizmetler oluşturma
Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu öğreticide, Azure HDInsight veya Azure Data Lake Analytics gibi herhangi bir işlem hizmeti kullanmazsınız. Azure Depolama (kaynak) ve Azure SQL Veritabanı (hedef) türünde iki veri deposu kullanırsınız. Bu nedenle, AzureStorage ve AzureSqlDatabase türlerinde AzureStorageLinkedService ve AzureSqlLinkedService adlı iki bağlı hizmet oluşturursunuz.  

AzureStorageLinkedService, Azure depolama hesabınızı veri fabrikasına bağlar. Bu depolama hesabı, kapsayıcı oluşturduğunuz ve verileri [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak yüklediğiniz hesaptır.   

AzureSqlLinkedService, Azure SQL veritabanınızı veri fabrikasına bağlar. Blob depolama alanından kopyalanan veriler bu veritabanında depolanır. Bu veritabanındaki emp tablosunu, [ön koşulların](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) parçası olarak oluşturdunuz.  

### <a name="create-azure-storage-linked-service"></a>Azure Storage bağlı hizmeti oluşturma
Bu adımda, Azure depolama hesabınızı veri fabrikanıza bağlarsınız. Bu bölümde Azure depolama hesabınızın adını ve anahtarını belirtirsiniz. Bir Azure Storage bağlı hizmetini tanımlamak için kullanılan JSON özelliklerine ilişkin ayrıntılar için bkz. [Azure Storage bağlı hizmeti](data-factory-azure-blob-connector.md#azure-storage-linked-service).  

1. Komutu **cmd** adlı değişkene atayın. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Bağlı hizmet başarıyla oluşturulduysa, **results** bölümünde bağlı hizmetin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Azure SQL bağlı hizmeti oluşturma
Bu adımda, Azure SQL veritabanınızı veri fabrikanıza bağlarsınız. Bu bölümde Azure SQL sunucu adı, veritabanı adı, kullanıcı adı ve kullanıcı parolasını belirtirsiniz. Bir Azure SQL bağlı hizmetini tanımlamak için kullanılan JSON özelliklerine ilişkin ayrıntılar için bkz. [Azure SQL bağlı hizmeti](data-factory-azure-sql-connector.md#linked-service-properties).

1. Komutu **cmd** adlı değişkene atayın. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azuresqllinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Bağlı hizmet başarıyla oluşturulduysa, **results** bölümünde bağlı hizmetin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Önceki adımda, Azure Depolama hesabınızı ve Azure SQL veritabanınızı veri fabrikanıza bağlamak için bağlı hizmetler oluşturdunuz. Bu adımda, AzureBlobInput ve AzureSqlOutput adlı iki veri kümesi tanımlarsınız. Bu veri kümeleri, sırasıyla AzureStorageLinkedService ve AzureSqlLinkedService tarafından başvurulan veri depolarında depolanan girdi ve çıktı verilerini temsil eder.

Azure depolama bağlı hizmeti, Data Factory hizmetinin Azure depolama hesabınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Giriş blob veri kümesi (AzureBlobInput) ise kapsayıcıyı ve girdi verilerini içeren klasörü belirtir.  

Benzer şekilde, Azure SQL Veritabanı bağlı hizmeti, Data Factory hizmetinin Azure SQL veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Çıktı SQL tablosu veri kümesi (OutputDataset) ise blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir. 

### <a name="create-input-dataset"></a>Girdi veri kümesi oluşturma
Bu adımda, AzureBlobInput adlı bir veri kümesi oluşturursunuz. Bu veri kümesi, AzureStorageLinkedService bağlı hizmetiyle temsil edilen Azure Depolama’daki bir blob kapsayıcısının (adftutorial) kök klasöründe bulunan blob dosyasını (emp.txt) işaret eder. fileName için bir değer belirtmezseniz (veya bu adımı atlarsanız) girdi klasöründe bulunan tüm blob’lardaki veriler hedefe kopyalanır. Bu öğreticide, fileName için bir değer belirtirsiniz. 

1. Komutu **cmd** adlı değişkene atayın. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri kümesi başarıyla oluşturulduysa, **results** bölümünde veri kümesinin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Çıktı veri kümesi oluşturma
Azure SQL Veritabanı bağlı hizmeti, Data Factory hizmetinin Azure SQL veritabanınıza bağlanmak için çalışma zamanında kullandığı bağlantı dizesini belirtir. Bu adımda oluşturduğunuz çıktı SQL tablosu veri kümesi (OututDataset), blob depolama alanındaki verilerin kopyalandığı veritabanında tabloyu belirtir.

1. Komutu **cmd** adlı değişkene atayın.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri kümesi başarıyla oluşturulduysa, **results** bölümünde veri kümesinin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>İşlem hattı oluşturma
Bu adımda, girdi olarak **AzureBlobInput**, çıktı olaraksa **AzureSqlOutput** kullanan bir **kopyalama etkinliğine** sahip bir işlem hattı oluşturursunuz.

Şu anda zamanlamayı çıktı veri kümesi yürütmektedir. Bu öğreticide, çıktı veri kümesi saatte bir dilim oluşturacak şekilde yapılandırılır. İşlem hattının başlangıç zamanı ve bitiş zamanı arasında bir gün, yani 24 saat vardır. Bu nedenle, işlem hattı çıktı veri kümesinden 24 dilim oluşturur. 

1. Komutu **cmd** adlı değişkene atayın.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. **Invoke-Command** komutunu kullanarak komutu çalıştırın.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Sonuçlara bakın. Veri kümesi başarıyla oluşturulduysa, **results** bölümünde veri kümesinin JSON’unu görürsünüz; aksi takdirde bir hata iletisi görürsünüz.  

    ```PowerShell   
    Write-Host $results
    ```

**Tebrikler!** Azure Blob Depolama’dan Azure SQL veritabanına veri kopyalayan bir işlem hattına sahip bir Azure veri fabrikasını başarıyla oluşturdunuz.

## <a name="monitor-pipeline"></a>İşlem hattını izleme
Bu adımda, Data Factory REST API’sini kullanarak işlem hattı tarafından üretilmekte olan dilimleri izlersiniz.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Aşağıdaki komutta belirtilen başlangıç ve bitiş zamanlarının, işlem hattının başlangıç ve bitiş zamanlarıyla eşleştiğinden emin olun. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Bir dilimi **Hazır** veya **Başarısız** durumunda görene kadar Invoke komutunu ve sonraki komutu çalıştırın. Dilim Ready durumundayken Azure SQL veritabanınızdaki **emp** tablosunda çıktı verileri olup olmadığını denetleyin. 

Her dilim için kaynak dosyasından Azure SQL veritabanındaki emp tablosuna iki satır veri kopyalanır. Bu nedenle, tüm dilimler başarıyla işlendiğinde (Ready durumunda) emp tablosunda 24 yeni kayıt görürsünüz. 

## <a name="summary"></a>Özet
Bu öğreticide bir Azure blob’undan Azure SQL veritabanına veri kopyalamak için REST API kullanarak bir Azure veri fabrikası oluşturdunuz. Bu öğreticide gerçekleştirilen üst düzey adımlar şunlardır:  

1. Oluşturulan Azure **data factory**.
2. Oluşturulan **bağlı hizmetler**:
   1. Girdi verilerinizi barındıran Azure Depolama hesabınızı bağlamak için bir Azure Depolama bağlı hizmeti.     
   2. Çıktı verilerinizi barındıran Azure SQL veritabanınızı bağlamak için bir Azure SQL bağlı hizmeti. 
3. İşlem hatları için girdi ve çıktı verilerini açıklayan **veri kümeleri** oluşturuldu.
4. Kaynak olarak BlobSource’u, havuz olarak SqlSink’i kapsayan bir Kopyalama Etkinliği’ne sahip bir **işlem hattı** oluşturuldu. 

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, bir kopyalama işleminde kaynak veri deposu olarak Azure blob depolama alanını ve hedef veri deposu olarak Azure SQL veritabanını kullandınız. Aşağıdaki tabloda, kopyalama etkinliği tarafından kaynaklar ve hedefler olarak desteklenen veri depolarının listesi sağlanmıştır: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Veri deposundan/veri deposuna veri kopyalama hakkında bilgi edinmek için tablodaki veri deposunun bağlantısına tıklayın.
