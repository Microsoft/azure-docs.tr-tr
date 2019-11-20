---
title: Azure Stream Analytics için yönetim .NET SDK'sini
description: Stream Analytics yönetim .NET SDK'sı ile çalışmaya başlayın. Ayarlama ve analiz işleri çalıştırma hakkında bilgi edinin. Bir proje, girişler, çıkışlar ve dönüştürmeleri oluşturun.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 73b1392adb2beee9e60d078d865257889b3205f8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612721"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Management .NET SDK: Ayarlama ve .NET için Azure Stream Analytics API'si kullanarak analytics işlerini çalıştırın
Ayarlama ve Stream Analytics API'si için .NET Yönetim SDK'sını kullanarak .NET kullanarak analytics işlerini çalıştırma hakkında bilgi edinin. Projesini ayarlarsınız, giriş ve çıkış kaynakları, dönüştürme ve başlangıç oluşturma ve işleri durdur. Analytics işleriniz için Blob depolama alanından veya bir olay hub'ından veri akışını yapabilirsiniz.

Bkz: [Stream Analytics API'si için .NET için yönetim başvuru belgeleri](https://msdn.microsoft.com/library/azure/dn889315.aspx).

Azure Stream Analytics, akış verileri bulutta üzerinden düşük gecikme süreli, yüksek oranda kullanılabilir, ölçeklenebilir, karmaşık olay işleme sağlayan tam olarak yönetilen bir hizmettir. Stream Analytics, müşterilerin veri akışlarını analiz etmek için akış işleri ayarlamanızı sağlar ve gerçek zamanlı analiz sürücü almalarını sağlar.  

> [!NOTE]
> Bu makaledeki örnek kod ile Azure Stream Analytics yönetim .NET SDK'ın v2.x sürümünü güncelleştirdik. Kullanır (1.x) lagecy SDK sürümünü kullanan örnek kod için lütfen bkz [Stream Analytics için yönetim .NET SDK'sı v1.x kullanın](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-dotnet-management-sdk-v1).

## <a name="prerequisites"></a>Önkoşullar
Bu makaleye başlamadan önce aşağıdaki gereksinimleri olması gerekir:

* Visual Studio 2019 veya 2015'i yükleyin.
* İndirme ve yükleme [Azure .NET SDK'sı](https://azure.microsoft.com/downloads/).
* Aboneliğinizde bir Azure kaynak grubu oluşturun. Aşağıdaki örnek bir örnek Azure PowerShell Betiği verilmiştir. Azure PowerShell için bilgi [yüklemek ve Azure PowerShell yapılandırma](/powershell/azure/overview);  

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

* Bir giriş kaynağı ve bağlanmak iş için çıktı hedef ayarlayın.

## <a name="set-up-a-project"></a>Projesi kurun
Bir analiz işi oluşturmak için önce projenizi ayarlayın, .NET için Stream Analytics API'si kullanın.

1. Visual Studio C# .NET konsol uygulaması oluşturun.
2. Paket Yöneticisi Konsolu'nda NuGet paketlerini yüklemek için aşağıdaki komutları çalıştırın. Azure Stream Analytics yönetim .NET SDK'sı ilk hesaptır. Azure istemci kimlik doğrulaması için ikinci bir bileşendir.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Aşağıdaki **appSettings** App.config dosyasına bölümü:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Değerleri Değiştir **Subscriptionıd** ve **ActiveDirectoryTenantId** Azure abonelik ve Kiracı kimliklerine sahip. Aşağıdaki Azure PowerShell cmdlet'ini çalıştırarak bu değerleri alabilirsiniz:

   ```powershell
      Get-AzureAccount
   ```

4. .Csproj dosyanızda şu başvuruyu ekleyin:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Aşağıdaki **kullanarak** deyimleri projedeki kaynak dosyasına (Program.cs):
   
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

6. Bir kimlik doğrulama Yardımcısını yöntemi ekleyin:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Stream Analytics yönetim istemcisi oluşturma
A **StreamAnalyticsManagementClient** nesnesinin, iş ve giriş ve çıkış dönüştürme gibi iş bileşenlerini yönetmenize olanak sağlar.

Aşağıdaki kodu ekleyin başlangıcına **ana** yöntemi:

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

**ResourceGroupName** değişkenin değeri oluşturulduğunda veya bölümündeki önkoşul adımlarını çekilen kaynak grubunun adıyla aynı olmalıdır.

İş oluşturma kimlik bilgisi sunu yönüyle otomatikleştirmek için başvurmak [Azure Resource Manager ile hizmet sorumlusu kimlik doğrulaması](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Bu makalenin kalan bölümünde, bu kod başlangıcında olduğunu varsayın **ana** yöntemi.

## <a name="create-a-stream-analytics-job"></a>Akış Analizi işi oluşturma
Aşağıdaki kod, tanımladığınız bir kaynak grubundaki bir Stream Analytics işi oluşturur. İş için bir giriş, çıkış ve dönüştürme daha sonra ekleyeceksiniz.

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
Aşağıdaki kod blob giriş kaynağı türü ve CSV serileştirme ile bir Stream Analytics giriş kaynağı oluşturur. Bir olay hub'ı giriş kaynağı oluşturmak için kullanın **EventHubStreamInputDataSource** yerine **BlobStreamInputDataSource**. Benzer şekilde, giriş kaynağı serileştirme türü özelleştirebilirsiniz.

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

Giriş kaynakları, varsayılan olarak, belirli bir işin için Blob Depolama veya bir olay hub'ı bağlıdır. Farklı projeler için aynı giriş kaynağı kullanmak için yöntemi yeniden çağırın ve başka bir iş adı belirtmeniz gerekir.

## <a name="test-a-stream-analytics-input-source"></a>Stream Analytics giriş kaynağı test
**TestConnection** yöntemi, Stream Analytics işi giriş kaynağı türüne özel diğer yönleri için giriş kaynağı bağlanabiliyor olup olmadığını sınar. Örneğin, bir önceki adımda oluşturduğunuz blob giriş kaynağı içinde belirtilen kapsayıcının var olup olmadığını denetleyin yanı sıra depolama hesabına bağlanmak için depolama hesabı adı ve anahtar çifti kullanılabilir yöntemi kontrol eder.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```

## <a name="create-a-stream-analytics-output-target"></a>Stream Analytics Çıkış hedefi oluşturma
Bir çıkış hedefi oluşturma, Stream Analytics giriş kaynağı oluşturma işlemiyle benzerdir. Giriş kaynakları gibi çıkış hedefleri için belirli bir işin bağlıdır. Farklı projeler için aynı çıkış hedefi kullanmak için yöntemi yeniden çağırın ve başka bir iş adı belirtmeniz gerekir.

Aşağıdaki kod, bir çıkış hedefi (Azure SQL veritabanı) oluşturur. Çıkış hedefinin veri türü ve/veya serileştirme türü özelleştirebilirsiniz.

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

## <a name="test-a-stream-analytics-output-target"></a>Stream Analytics çıkış hedef test
Bir Stream Analytics çıkış hedef de sahip **TestConnection** bağlantılarını test etme için yöntemi.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Bir Stream Analytics dönüştürme
Aşağıdaki kod bir Stream Analytics dönüştürme sorgu oluşturur. "seçin * girişten alınan" ve Stream Analytics işi için bir akış birimi ayırmak için belirtir. Akış birimleri ayarlama hakkında daha fazla bilgi için bkz. [ölçek Azure Stream Analytics işleri](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

Giriş ve çıkış gibi dönüştürme de altında oluşturulan belirli Stream Analytics işi bağlıdır.

## <a name="start-a-stream-analytics-job"></a>Stream Analytics işini başlatın
Bir Stream Analytics işi ve kendi girişlere, çıkışlar ve dönüştürme oluşturduktan sonra iş çağırarak başlatabilirsiniz **Başlat** yöntemi.

Aşağıdaki örnek bir özel çıkış başlangıç saatine sahip bir Stream Analytics işi, 12:12:12 12 Aralık 2012'ye ayarlayın kod başlatır UTC:

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
Çalışan bir Stream Analytics işi çağrı yaparak da durdurabilirsiniz **Durdur** yöntemi.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics işini sil
**Sil** yöntemi, işi ve bunun yanı sıra iş dönüşümü girişlere ve çıkışlar da dahil olmak üzere temel alınan bir alt kaynakları siler.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Destek alın
Daha fazla yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Sonraki adımlar
Oluşturup analytics işlerini çalıştırmak için bir .NET SDK'sı kullanmanın temel adımlarını öğrendiniz. Daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics yönetim .NET SDK'sini](https://msdn.microsoft.com/library/azure/dn889315.aspx).
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: https://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
