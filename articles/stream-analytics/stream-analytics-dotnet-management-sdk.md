---
title: Azure Stream Analytics için yönetim .NET SDK
description: Stream Analytics Management .NET SDK ile çalışmaya başlayın. Analiz işlerini ayarlamayı ve çalıştırmayı öğrenin. Bir proje, giriş, çıkış ve dönüşüm oluşturun.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103232894"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Yönetim .NET SDK: .NET için Azure Stream Analytics API 'sini kullanarak analiz işlerini ayarlama ve çalıştırma
Yönetim .NET SDK 'sını kullanarak .NET için Stream Analytics API 'sini kullanarak analiz işlerini ayarlamayı ve çalıştırmayı öğrenin. Bir proje kurun, giriş ve çıkış kaynakları, dönüşümler ve işleri başlatın ve durdurun. Analiz işleriniz için blob depolamadan veya bir olay hub 'ından veri akışı yapabilirsiniz.

[.NET için Stream Analytics API 'si için yönetim başvurusu belgelerine](/previous-versions/azure/dn889315(v=azure.100))bakın.

Azure Stream Analytics, bulutta akış verileri üzerinde düşük gecikmeli, yüksek oranda kullanılabilir, ölçeklenebilir ve karmaşık olay işleme sağlayan, tam olarak yönetilen bir hizmettir. Stream Analytics, müşterilerin veri akışlarını çözümlemek için akış işleri ayarlamasına olanak tanır ve neredeyse gerçek zamanlı analizler sağlar.  

> [!NOTE]
> Bu makaledeki örnek kodu, Azure Stream Analytics Management .NET SDK v2. x sürümü ile güncelleştirdik. Kullanılan örnek kod için lagecy (1. x) SDK sürümünü kullanır, lütfen bkz. [Stream Analytics Için yönetim .NET SDK v1. x kullanın]().

## <a name="prerequisites"></a>Önkoşullar
Bu makaleye başlamadan önce, aşağıdaki gereksinimlere sahip olmanız gerekir:

* Visual Studio 2019 veya 2015 ' ü yükler.
* [Azure .NET SDK](https://azure.microsoft.com/downloads/)’yı indirip yükleyin.
* Aboneliğinizde bir Azure Kaynak grubu oluşturun. Aşağıdaki örnek bir örnek Azure PowerShell betiğine örnektir. Azure PowerShell bilgi için bkz. [Azure PowerShell 'Yi yükleyip yapılandırma](/powershell/azure/);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Bağlanılacak iş için bir giriş kaynağı ve çıkış hedefi ayarlayın.

## <a name="set-up-a-project"></a>Proje ayarlama
Bir analiz işi oluşturmak için, .NET için Stream Analytics API 'sini kullanın, önce projenizi ayarlayın.

1. Visual Studio C# .NET konsol uygulaması oluşturun.
2. Paket Yöneticisi konsolunda, NuGet paketlerini yüklemek için aşağıdaki komutları çalıştırın. Birincisi Azure Stream Analytics Management .NET SDK 'dır. İkinci bir Azure istemci kimlik doğrulaması içindir.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Aşağıdaki **appSettings** bölümünü App.config dosyasına ekleyin:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    **SubscriptionID** ve **activedirectorytenantıd** değerlerini Azure aboneliğiniz ve kiracı kimlikleri ile değiştirin. Aşağıdaki Azure PowerShell cmdlet 'ini çalıştırarak bu değerleri alabilirsiniz:

   ```powershell
      Get-AzureAccount
   ```

4. Aşağıdaki başvuruyu. csproj dosyanıza ekleyin:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Aşağıdaki **using** deyimlerini projedeki kaynak dosyasına (program. cs) ekleyin:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Kimlik doğrulama Yardımcısı yöntemi ekleme:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Stream Analytics yönetim istemcisi oluşturma
Bir **StreamAnalyticsManagementClient** nesnesi, işi ve iş bileşenlerini (giriş, çıkış ve dönüşüm gibi) yönetmenize olanak sağlar.

Aşağıdaki kodu **Main** yönteminin başına ekleyin:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

**Resourcegroupname** değişkeninin değeri, sizin oluşturduğunuz veya önkoşul adımlarında seçtiğiniz kaynak grubunun adı ile aynı olmalıdır.

İş oluşturma işleminin kimlik bilgileri sunumunu otomatikleştirmek için [Azure Resource Manager ile hizmet sorumlusu kimlik doğrulaması gerçekleştirme](../active-directory/develop/howto-authenticate-service-principal-powershell.md)konusuna bakın.

Bu makalenin geri kalan bölümlerinde, bu kodun **Main** yönteminin başlangıcında olduğu varsayılır.

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma
Aşağıdaki kod, tanımladığınız kaynak grubu altında bir Stream Analytics işi oluşturur. İşe daha sonra bir giriş, çıkış ve dönüştürme ekleyeceksiniz.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Stream Analytics giriş kaynağı oluşturma
Aşağıdaki kod, blob giriş kaynağı türü ve CSV serileştirmesi ile bir Stream Analytics giriş kaynağı oluşturur. Bir olay hub 'ı giriş kaynağı oluşturmak için **Blobstreaminputdatasource** yerine **Eventhubstreaminputdatasource** kullanın. Benzer şekilde, giriş kaynağının serileştirme türünü özelleştirebilirsiniz.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

BLOB depolama veya bir olay hub 'ından gelen giriş kaynakları, belirli bir işe bağlı olup olmadığı. Farklı işler için aynı giriş kaynağını kullanmak için, yöntemi yeniden çağırmanız ve farklı bir iş adı belirtmeniz gerekir.

## <a name="test-a-stream-analytics-input-source"></a>Stream Analytics giriş kaynağını test etme
**TestConnection** yöntemi, Stream Analytics işinin giriş kaynağına ve giriş kaynak türüne özgü diğer yönlere bağlanıp bağlanamamadığını sınar. Örneğin, önceki bir adımda oluşturduğunuz blob giriş kaynağında, yöntemi depolama hesabı adı ve anahtar çiftinin depolama hesabına bağlanmak için ve belirtilen kapsayıcının var olup olmadığını kontrol etmek için kullanılabilir olup olmadığını kontrol eder.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
TestConnection çağrısının sonucu, iki özellik içeren bir *Resourcetestresult* nesnesidir:

- *durum*: Şu dizelerden biri olabilir: ["Testnotatmey", "testsucceeded", "testfailed"]
- *hata*: aşağıdaki özellikleri Içeren errorResponse türünde.
   - *kod*: dize türünde gerekli bir özellik. Değer, test sırasında alınan standart sistem .net. HttpStatusCode değeridir.
   - *ileti*: hatayı temsil eden String türünde gerekli bir özellik. 

## <a name="create-a-stream-analytics-output-target"></a>Stream Analytics çıkış hedefi oluşturma
Çıkış hedefi oluşturmak, Stream Analytics giriş kaynağı oluşturmaya benzer. Giriş kaynakları gibi, çıkış hedefleri de belirli bir işe bağlıdır. Farklı işler için aynı çıkış hedefini kullanmak üzere, yöntemi yeniden çağırmanız ve farklı bir iş adı belirtmeniz gerekir.

Aşağıdaki kod bir çıkış hedefi (Azure SQL veritabanı) oluşturur. Çıktı hedefinin veri türünü ve/veya serileştirme türünü özelleştirebilirsiniz.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Stream Analytics çıkış hedefini test etme
Stream Analytics çıkış hedefi, bağlantıları test etmek için **TestConnection** yöntemine de sahiptir.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Stream Analytics dönüştürmesi oluşturma
Aşağıdaki kod, "select * from Input" sorgusuyla bir Stream Analytics dönüşümü oluşturur ve Stream Analytics iş için bir akış birimi ayırmayı belirtir. Akış birimlerini ayarlama hakkında daha fazla bilgi için bkz. [ölçek Azure Stream Analytics işleri](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Giriş ve çıkış gibi, bir dönüşüm de oluşturulduğu belirli Stream Analytics işine bağlıdır.

## <a name="start-a-stream-analytics-job"></a>Stream Analytics işini başlatma
Bir Stream Analytics işi ve giriş, çıkış ve dönüşümünü oluşturduktan sonra, **Start** yöntemini çağırarak işi başlatabilirsiniz.

Aşağıdaki örnek kod, özel bir çıkış başlangıç saati 12 Aralık 2012, 12:12:12 UTC olarak ayarlanan Stream Analytics işini başlatır:

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stream Analytics işini durdurma
**Durdur** metodunu çağırarak çalışan bir Stream Analytics işini durdurabilirsiniz.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics işini silme
**Delete** yöntemi, işin yanı sıra giriş (lar), çıktı ve iş dönüştürme dahil olmak üzere temel alınan alt kaynakları da silecektir.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Destek alın
Daha fazla yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin.

## <a name="next-steps"></a>Sonraki adımlar
Analiz işleri oluşturmak ve çalıştırmak için .NET SDK kullanmanın temellerini öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure Stream Analytics giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Management .NET SDK](/previous-versions/azure/dn889315(v=azure.100)).
* [Azure Akış Analizi Sorgu Dili Başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
