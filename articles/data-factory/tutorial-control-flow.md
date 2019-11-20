---
title: 'Azure Data Factory ardışık düzeninde dallanma '
description: Dallanma ve zincirleme etkinlikleriyle Azure Data Factory'de veri akışını denetleme hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 9/27/2019
ms.openlocfilehash: 0a7e5f56fe71c174c78f1363e403ae41a2ec90a6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683667"
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Data Factory işlem hattında dallanma ve zincirleme etkinlikleri

Bu öğreticide, bazı denetim akışı özelliklerini gösteren bir Data Factory işlem hattı oluşturacaksınız. Bu işlem hattı, Azure Blob depolama alanındaki bir kapsayıcıdan aynı depolama hesabındaki başka bir kapsayıcıya kopyalar. Kopyalama etkinliği başarılı olursa, işlem hattı başarılı kopyalama işleminin ayrıntılarını bir e-posta ile gönderir. Bu bilgiler, yazılan veri miktarını içerebilir. Kopyalama etkinliği başarısız olursa, kopyalama hatasının ayrıntılarını (örneğin, hata iletisi) bir e-posta ile gönderir. Öğretici boyunca parametreleri nasıl geçireceğinizi göreceksiniz.

Bu grafik senaryoya genel bakış sağlar:

![Genel Bakış](media/tutorial-control-flow/overview.png)

Bu öğreticide, aşağıdaki görevlerin nasıl yapılacağı gösterilmektedir:

> [!div class="checklist"]
> * Veri fabrikası oluşturma
> * Azure Depolama bağlı hizmeti oluşturma
> * Azure blob veri kümesi oluşturma
> * Kopyalama etkinliği ve bir web etkinliği içeren işlem hattı oluşturma
> * Etkinliklerin çıktılarını sonraki etkinliklere gönderme
> * Parametre geçişini ve sistem değişkenlerini kullanma
> * Bir işlem hattı çalıştırması başlatma
> * İşlem hattı ve etkinlik çalıştırmalarını izleme

Bu öğreticide .NET SDK kullanılır. Azure Data Factory etkileşimde bulunmak için diğer mekanizmaları kullanabilirsiniz. Hızlı başlangıç Data Factory için bkz. [5 dakikalık hızlı](/azure/data-factory/quickstart-create-data-factory-portal)başlangıçlara bakın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

* Azure depolama hesabı. BLOB depolama alanını kaynak veri deposu olarak kullanırsınız. Azure depolama hesabınız yoksa, bkz. [depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md).
* Azure Depolama Gezgini. Bu aracı yüklemek için bkz. [Azure Depolama Gezgini](https://storageexplorer.com/).
* Azure SQL veritabanı. Veritabanını havuz veri deposu olarak kullanırsınız. Azure SQL veritabanınız yoksa bkz. [Azure SQL veritabanı oluşturma](../sql-database/sql-database-get-started-portal.md).
* Visual Studio. Bu makalede Visual Studio 2019 kullanılmaktadır.
* Azure .NET SDK. [Azure .NET SDK 'sını](https://azure.microsoft.com/downloads/)indirin ve yükleyin.

Data Factory Şu anda kullanılabildiği Azure bölgelerinin listesi için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/). Veri depoları ve hesaplar diğer bölgelerde olabilir. Depolar Azure Storage ve Azure SQL veritabanı 'nı içerir. Data Factory, ' nin kullandığı HDInsight içerir.

[Azure Active Directory uygulaması oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)bölümünde açıklandığı gibi bir uygulama oluşturun. Aynı makaledeki yönergeleri izleyerek uygulamayı **katkıda bulunan** rolüne atayın. Bu öğreticinin sonraki bölümleri için **uygulama (istemci) kimliği** ve **Dizin (kiracı) kimliği**gibi birkaç değer gerekir.

### <a name="create-a-blob-table"></a>Blob tablosu oluşturma

1. Bir metin düzenleyicisi açın. Aşağıdaki metni kopyalayın ve *input. txt*olarak yerel olarak kaydedin.

   ```
   Ethel|Berg
   Tamika|Walsh
   ```

1. Azure Depolama Gezgini açın. Depolama hesabınızı genişletin. **BLOB kapsayıcıları** ' na sağ tıklayın ve **BLOB kapsayıcısı oluştur**' u seçin.
1. Yeni kapsayıcıyı *adfv2branch* olarak adlandırın ve *input. txt* dosyanızı kapsayıcıya eklemek için **karşıya yükle** ' yi seçin.

## Visual Studio projesi oluştur<a name="create-visual-studio-project"></a>

C# .NET konsol uygulaması oluşturma:

1. Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, Için C# konsol uygulaması (.NET Framework) öğesini seçin ve **İleri**' yi seçin.
1. Projeyi *ADFv2BranchTutorial*olarak adlandırın.
1. **.NET Version 4.5.2** veya üstünü seçip **Oluştur**' u seçin.

### <a name="install-nuget-packages"></a>NuGet paketlerini yükleme

1. **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.
1. **Paket Yöneticisi konsolunda**, paketleri yüklemek için aşağıdaki komutları çalıştırın. Ayrıntılar için [Microsoft. Azure. Management. DataFactory NuGet paketini](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/) inceleyin.

   ```powershell
   Install-Package Microsoft.Azure.Management.DataFactory
   Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```

### <a name="create-a-data-factory-client"></a>Veri fabrikası istemcisi oluşturma

1. *Program.cs* açın ve aşağıdaki deyimleri ekleyin:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.DataFactory;
   using Microsoft.Azure.Management.DataFactory.Models;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```

1. Bu statik değişkenleri `Program` sınıfına ekleyin. Yer tutucuları kendi değerlerinizle değiştirin.

   ```csharp
   // Set variables
   static string tenantID = "<tenant ID>";
   static string applicationId = "<application ID>";
   static string authenticationKey = "<Authentication key for your application>";
   static string subscriptionId = "<Azure subscription ID>";
   static string resourceGroup = "<Azure resource group name>";

   static string region = "East US";
   static string dataFactoryName = "<Data factory name>";

   // Specify the source Azure Blob information
   static string storageAccount = "<Azure Storage account name>";
   static string storageKey = "<Azure Storage account key>";
   // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
   static string inputBlobPath = "adfv2branch/input";
   static string inputBlobName = "input.txt";
   static string outputBlobPath = "adfv2branch/output";
   static string emailReceiver = "<specify email address of the receiver>";

   static string storageLinkedServiceName = "AzureStorageLinkedService";
   static string blobSourceDatasetName = "SourceStorageDataset";
   static string blobSinkDatasetName = "SinkStorageDataset";
   static string pipelineName = "Adfv2TutorialBranchCopy";

   static string copyBlobActivity = "CopyBlobtoBlob";
   static string sendFailEmailActivity = "SendFailEmailActivity";
   static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
   ```

1. Aşağıdaki kodu `Main` yöntemine ekleyin. Bu kod, `DataFactoryManagementClient` sınıfının bir örneğini oluşturur. Daha sonra Veri Fabrikası, bağlı hizmet, veri kümeleri ve işlem hattı oluşturmak için bu nesneyi kullanırsınız. Bu nesneyi Ayrıca işlem hattı çalıştırma ayrıntılarını izlemek için de kullanabilirsiniz.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
   ```

### <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

1. *Program.cs* dosyanıza bir `CreateOrUpdateDataFactory` yöntemi ekleyin:

   ```csharp
   static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating data factory " + dataFactoryName + "...");
       Factory resource = new Factory
       {
           Location = region
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

       Factory response;
       {
           response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
       }

       while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
       {
           System.Threading.Thread.Sleep(1000);
       }
       return response;
   }
   ```

1. Aşağıdaki satırı, bir veri fabrikası oluşturan `Main` yöntemine ekleyin:

   ```csharp
   Factory df = CreateOrUpdateDataFactory(client);
   ```

## <a name="create-an-azure-storage-linked-service"></a>Azure Depolama bağlı hizmeti oluşturma

1. *Program.cs* dosyanıza bir `StorageLinkedServiceDefinition` yöntemi ekleyin:

   ```csharp
   static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
   {
      Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
      AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
      {
          ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
      };
      Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
      LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
      return linkedService;
   }
   ```

1. Aşağıdaki satırı, Azure depolama bağlı hizmeti oluşturan `Main` yöntemine ekleyin:

   ```csharp
   client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
   ```

Desteklenen özellikler ve ayrıntılar hakkında daha fazla bilgi için bkz. [bağlı hizmet özellikleri](connector-azure-blob-storage.md#linked-service-properties).

## <a name="create-datasets"></a>Veri kümeleri oluşturun

Bu bölümde, biri kaynak ve bir havuz için olmak üzere iki veri kümesi oluşturursunuz.

### <a name="create-a-dataset-for-a-source-azure-blob"></a>Kaynak Azure blobu için veri kümesi oluşturma

*Azure blob veri kümesi*oluşturan bir yöntem ekleyin. Desteklenen özellikler ve ayrıntılar hakkında daha fazla bilgi için bkz. [Azure blob veri kümesi özellikleri](connector-azure-blob-storage.md#dataset-properties).

*Program.cs* dosyanıza bir `SourceBlobDatasetDefinition` yöntemi ekleyin:

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

Azure Blob’da kaynak verilerini temsil eden bir veri kümesi tanımlayın. Bu blob veri kümesi, önceki adımda desteklenen Azure depolama bağlı hizmetini ifade eder. Blob veri kümesi, kopyalanacak Blobun konumunu açıklar: *FolderPath* ve *filename*.

*FolderPath*için parametrelerin kullanımına dikkat edin. `sourceBlobContainer`, parametrenin adıdır ve ifade, işlem hattı çalıştırmasında geçirilen değerlerle birlikte değişir. Parametreleri tanımlamaya yönelik söz dizimi `@pipeline().parameters.<parameterName>`

### <a name="create-a-dataset-for-a-sink-azure-blob"></a>Havuz Azure blobu için veri kümesi oluşturma

1. *Program.cs* dosyanıza bir `SourceBlobDatasetDefinition` yöntemi ekleyin:

   ```csharp
   static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
   {
       Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
       AzureBlobDataset blobDataset = new AzureBlobDataset
       {
           FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
           LinkedServiceName = new LinkedServiceReference
           {
               ReferenceName = storageLinkedServiceName
           }
       };
       Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
       DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
       return dataset;
   }
   ```

1. Aşağıdaki kodu hem Azure Blob kaynağı hem de havuz veri kümeleri oluşturan `Main` yöntemine ekleyin.

   ```csharp
   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

   client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
   ```

## <a name="create-a-c-class-emailrequest"></a>C# sınıfı oluşturma: EmailRequest

C# Projenizde `EmailRequest`adlı bir sınıf oluşturun. Bu sınıf, bir e-posta gönderilirken işlem hattının gövde isteğinde gönderdiği özellikleri tanımlar. Bu öğreticide işlem hattı, işlem hattından e-postaya dört özellik gönderir:

* İleti. E-postanın gövdesi. Başarılı bir kopya için, bu özellik yazılan veri miktarını içerir. Başarısız bir kopyalama için, bu özellik hatanın ayrıntılarını içerir.
* Veri Fabrikası adı. Veri fabrikasının adı.
* İşlem hattı adı. İşlem hattının adı.
* Bildiği. Üzerinden geçen parametre. Bu özellik, e-postanın alıcısını belirtir.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```

## <a name="create-email-workflow-endpoints"></a>E-posta iş akışı uç noktaları oluşturma

E-posta göndermeyi tetiklemek için, [Logic Apps](../logic-apps/logic-apps-overview.md) kullanarak iş akışını tanımlayın. Logic Apps iş akışı oluşturma hakkında daha fazla bilgi için bkz. [mantıksal uygulama oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Başarı e-postası iş akışı

[Azure Portal](https://portal.azure.com), *Copybaşarısemail*adlı bir Logic Apps iş akışı oluşturun. İş akışı tetikleyicisini `When an HTTP request is received`olarak tanımlayın. İstek tetikleyiciniz için `Request Body JSON Schema` alanını aşağıdaki JSON ile doldurun:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

İş akışınız aşağıdaki örneğe benzer bir şekilde görünür:

![Başarı e-postası iş akışı](media/tutorial-control-flow/success-email-workflow-trigger.png)

Bu JSON içeriği, önceki bölümde oluşturduğunuz `EmailRequest` sınıfıyla hizalanır.

`Office 365 Outlook – Send an email`bir eylem ekleyin. **E-posta gönder** eylemi için, istek **gövdesi** JSON şemasında geçirilen özellikleri kullanarak e-postayı nasıl biçimlendirmek istediğinizi özelleştirin. Bir örneği aşağıda verilmiştir:

![Logic App Designer-e-posta eylemi gönder](media/tutorial-control-flow/customize-send-email-action.png)

İş akışını kaydettikten sonra, tetikleyiciden **http post URL 'si** değerini kopyalayın ve kaydedin.

## <a name="fail-email-workflow"></a>Hata e-postası iş akışı

*Copyfailemail*adlı başka bir Logic Apps iş akışı olarak **copyardılsemail** 'i kopyalayın. İstek tetikleyicisinde `Request Body JSON schema` değeri aynıdır. Hata e-postasına uyarlamak için e-postanızın biçimini `Subject` olarak değiştirin. Örnek aşağıda verilmiştir:

![Mantıksal uygulama Tasarımcısı-hata e-postası iş akışı](media/tutorial-control-flow/fail-email-workflow.png)

İş akışını kaydettikten sonra, tetikleyiciden **http post URL 'si** değerini kopyalayın ve kaydedin.

Artık aşağıdaki örneklerde olduğu gibi iki iş akışı URL 'Si olmalıdır:

```csharp
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Visual Studio 'da projenize geri dönün. Şimdi bir kopyalama etkinliği ve `DependsOn` özelliği ile işlem hattı oluşturan kodu ekleyeceğiz. Bu öğreticide, işlem hattı bir etkinlik, bir kaynak olarak blob veri kümesini ve havuz olarak başka bir blob veri kümesini alan bir kopyalama etkinliği içerir. Kopyalama etkinliği başarılı veya başarısız olursa, farklı e-posta görevlerini çağırır.

Bu işlem hattında aşağıdaki özellikleri kullanırsınız:

* Parametreler
* Web etkinliği
* Etkinlik bağımlılığı
* Etkinlikten başka bir etkinliğe giriş olarak çıkış kullanma

1. Bu yöntemi projenize ekleyin. Aşağıdaki bölümlerde daha ayrıntılı bilgi sağlanmaktadır.

    ```csharp
    static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
            {
                Console.WriteLine("Creating pipeline " + pipelineName + "...");
                PipelineResource resource = new PipelineResource
                {
                    Parameters = new Dictionary<string, ParameterSpecification>
                    {
                        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                        { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                    },
                    Activities = new List<Activity>
                    {
                        new CopyActivity
                        {
                            Name = copyBlobActivity,
                            Inputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSourceDatasetName
                                }
                            },
                            Outputs = new List<DatasetReference>
                            {
                                new DatasetReference
                                {
                                    ReferenceName = blobSinkDatasetName
                                }
                            },
                            Source = new BlobSource { },
                            Sink = new BlobSink { }
                        },
                        new WebActivity
                        {
                            Name = sendSuccessEmailActivity,
                            Method = WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Succeeded" }
                                }
                            }
                        },
                        new WebActivity
                        {
                            Name = sendFailEmailActivity,
                            Method =WebActivityMethod.POST,
                            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                            Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                            DependsOn = new List<ActivityDependency>
                            {
                                new ActivityDependency
                                {
                                    Activity = copyBlobActivity,
                                    DependencyConditions = new List<String> { "Failed" }
                                }
                            }
                        }
                    }
                };
                Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
                return resource;
            }
    ```

1. Aşağıdaki satırı, işlem hattını oluşturan `Main` yöntemine ekleyin:

   ```csharp
   client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
   ```

### <a name="parameters"></a>Parametreler

Ardışık düzen kodumuz ilk bölümü parametreleri tanımlar.

* `sourceBlobContainer`. Kaynak blob veri kümesi bu parametreyi ardışık düzende tüketir.
* `sinkBlobContainer`. Havuz blob veri kümesi bu parametreyi ardışık düzende kullanır.
* `receiver`. İşlem hattındaki, alıcıya başarı veya hata e-postaları gönderen iki Web etkinliği bu parametreyi kullanır.

```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```

### <a name="web-activity"></a>Web etkinliği

Web etkinliği herhangi bir REST uç noktasına çağrı yapılmasına izin verir. Etkinlik hakkında daha fazla bilgi için bkz. [Azure Data Factory Web etkinliği](control-flow-web-activity.md). Bu işlem hattı, Logic Apps e-posta iş akışını çağırmak için bir Web etkinliği kullanır. İki Web etkinliği oluşturursunuz: `CopySuccessEmail` iş akışına ve `CopyFailWorkFlow`çağıran birine çağırır.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```

`Url` özelliğinde, Logic Apps İş akışlarınızın **http post URL** uç noktalarını yapıştırın. `Body` özelliğinde `EmailRequest` sınıfının bir örneğini geçirin. E-posta isteği aşağıdaki özellikleri içerir:

* İleti. `@{activity('CopyBlobtoBlob').output.dataWritten`değerini geçirir. Önceki kopyalama etkinliğinin bir özelliğine erişir ve `dataWritten`değerini geçirir. Hata durumunda `@{activity('CopyBlobtoBlob').error.message` yerine hata çıktısını geçirir.
* Data Factory adı. `@{pipeline().DataFactory}` değerini geçirir bu sistem değişkeni, ilgili veri fabrikası adına erişmenize olanak tanır. Sistem değişkenlerinin listesi için bkz. [Sistem değişkenleri](control-flow-system-variables.md).
* İşlem hattı adı. `@{pipeline().Pipeline}`değerini geçirir. Bu sistem değişkeni, karşılık gelen işlem hattı adına erişmenize olanak tanır.
* Bildiği. `"@pipeline().parameters.receiver"`değerini geçirir. İşlem hattı parametrelerine erişir.

Bu kod, önceki kopyalama etkinliğine bağlı yeni bir etkinlik bağımlılığı oluşturur.

## <a name="create-a-pipeline-run"></a>İşlem hattı çalıştırması oluşturma

Aşağıdaki kodu bir işlem hattı çalıştırmasını tetikleyen `Main` yöntemine ekleyin.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};
 
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Main sınıfı

Son `Main` yönteminiz aşağıdaki gibi görünmelidir.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

Bir işlem hattı çalıştırması tetiklemek için programınızı derleyip çalıştırın!

## <a name="monitor-a-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

1. `Main` yöntemine aşağıdaki kodu ekleyin:

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

    Bu kod, verileri kopyalamayı bitirene kadar, çalıştırmanın durumunu sürekli olarak denetler.

1. Kopyalama etkinliği çalıştırma ayrıntılarını alan `Main` yöntemine aşağıdaki kodu ekleyin. Örneğin, okunan/yazılan verilerin boyutu:

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı derleyip başlatın, ardından işlem hattı yürütmesini doğrulayın.

Uygulama, veri fabrikası oluşturma, bağlı hizmet, veri kümeleri, işlem hattı ve işlem hattı çalıştırma ilerlemesini görüntüler. Daha sonra işlem hattı çalıştırma durumunu denetler. Okunan/yazılan veri boyutunu içeren kopyalama etkinliği ayrıntılarını görene kadar bekleyin. Ardından, blob 'da belirttiğiniz şekilde *inputblobpath* ' den *outputblobpath* ' e kopyalandığını denetlemek için Azure Depolama Gezgini gibi araçları kullanın.

Çıktın aşağıdaki örneğe benzer olması gerekir:

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide aşağıdaki görevleri yaptınız:

> [!div class="checklist"]
> * Veri fabrikası oluşturma
> * Azure Depolama bağlı hizmeti oluşturma
> * Azure blob veri kümesi oluşturma
> * Kopyalama etkinliği ve bir web etkinliği içeren işlem hattı oluşturma
> * Etkinliklerin çıktılarını sonraki etkinliklere gönderme
> * Parametre geçişini ve sistem değişkenlerini kullanma
> * Bir işlem hattı çalıştırması başlatma
> * İşlem hattı ve etkinlik çalıştırmalarını izleme

Artık Azure Data Factory hakkında daha fazla bilgi için kavramlar bölümüne devam edebilirsiniz.
> [!div class="nextstepaction"]
>[İşlem hatları ve etkinlikler](concepts-pipelines-activities.md)