---
title: Azure Application Insights Batch 'i izleme | Microsoft Docs
description: Azure Application Insights kitaplığı kullanarak Azure Batch .NET uygulamasını nasıl ayarlayacağınızı öğrenin.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: .NET
ms.topic: article
ms.workload: na
ms.date: 04/05/2018
ms.author: lahugh
ms.openlocfilehash: 8d896785a2f000a22f68611d5b3b1162c2021236
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322575"
---
# <a name="monitor-and-debug-an-azure-batch-net-application-with-application-insights"></a>Application Insights ile Azure Batch .NET uygulamasında izleme ve hata ayıklama

[Application Insights](../azure-monitor/app/app-insights-overview.md) , geliştiricilerin Azure hizmetlerine dağıtılan uygulamaları izlemelerine ve hata ayıklamasına yönelik zarif ve güçlü bir yol sağlar. Performans sayaçlarını ve özel durumları izlemek ve özel ölçümler ve izleme ile kodunuzu işaretlemek için Application Insights kullanın. Application Insights Azure Batch uygulamanızla tümleştirmek, davranışlar konusunda derin Öngörüler elde etmenizi ve sorunları neredeyse gerçek zamanlı olarak araştırmanızı sağlar.

Bu makalede, Azure Batch .NET çözümünüze Application Insights kitaplığı ekleme ve yapılandırma ve uygulama kodunuzu işaretleme gösterilmektedir. Ayrıca, Azure portal aracılığıyla uygulamanızı izlemeye yönelik yolları gösterir ve özel panolar oluşturur. Diğer dillerde Application Insights destek için [Diller, platformlar ve tümleştirmeler belgelerine](../azure-monitor/app/platforms.md)bakın.

Bu makaleye C# eşlik eden kod içeren örnek bir çözüm [GitHub](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ApplicationInsights)' dan edinilebilir. Bu örnek, [Topnwords](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords) örneğine Application Insights izleme kodu ekler. Bu örneğe alışkın değilseniz, önce TopNWords oluşturmayı ve çalıştırmayı deneyin. Bunun yapılması, birden fazla işlem düğümünde paralel olarak bir giriş blobu kümesini işlemenin temel toplu iş akışını anlamanıza yardımcı olur. 

> [!TIP]
> Alternatif olarak, Batch çözümünüzü Batch Explorer VM performans sayaçları gibi Application Insights verileri görüntüleyecek şekilde yapılandırın. [Batch Explorer](https://github.com/Azure/BatchExplorer) , Azure Batch uygulamaları oluşturmaya, hata ayıklamanıza ve izlemenize yardımcı olan ücretsiz, zengin özellikli, tek başına bir istemci aracıdır. Mac, Linux veya Windows için [yükleme paketi](https://azure.github.io/BatchExplorer/) indirebilirsiniz. Batch Explorer Application Insights verileri etkinleştirmeye yönelik hızlı adımlar için [Batch-Insights](https://github.com/Azure/batch-insights) deposuna bakın. 
>

## <a name="prerequisites"></a>Önkoşullar
* [Visual Studio 2017 veya üzeri](https://www.visualstudio.com/vs)

* [Batch hesabı ve bağlı depolama hesabı](batch-account-create-portal.md)

* [Application Insights kaynağı](../azure-monitor/app/create-new-resource.md )
  
   * Bir Application Insights *kaynağı*oluşturmak için Azure Portal kullanın. *Genel* **uygulama türünü**seçin.

   * [İzleme anahtarını](../azure-monitor/app/create-new-resource.md #copy-the-instrumentation-key) portaldan kopyalayın. Bu makalenin ilerleyen kısımlarında gereklidir.
  
  > [!NOTE]
  > Application Insights depolanan veriler için [ücretlendirilmeyebilirsiniz](https://azure.microsoft.com/pricing/details/application-insights/) . Bu, bu makalede ele alınan tanılama ve izleme verilerini içerir.
  > 

## <a name="add-application-insights-to-your-project"></a>Projenize Application Insights ekleyin

Projeniz için **Microsoft. ApplicationInsights. WindowsServer** NuGet paketi ve bağımlılıkları gereklidir. Bunları uygulamanızın projesine ekleyin veya geri yükleyin. Paketi yüklemek için `Install-Package` komutunu veya NuGet Paket Yöneticisi 'ni kullanın.

```powershell
Install-Package Microsoft.ApplicationInsights.WindowsServer
```
**Microsoft. ApplicationInsights** ad alanını kullanarak .net uygulamanızdan başvuru Application Insights.

## <a name="instrument-your-code"></a>Kodunuzu işaretleme

Kodunuzu işaretlemek için çözümünüzün bir Application Insights [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient)oluşturması gerekir. Örnekte, TelemetryClient yapılandırmasını [ApplicationInsights. config](../azure-monitor/app/configuration-with-applicationinsights-config.md) dosyasından yükler. Aşağıdaki projelerde ApplicationInsights. config dosyasını Application Insights izleme anahtarınızla güncelleştirdiğinizden emin olun: Microsoft. Azure. Batch. Samples. TelemetryStartTask ve TopNWordsSample.

```xml
<InstrumentationKey>YOUR-IKEY-GOES-HERE</InstrumentationKey>
```
Ayrıca TopNWords.cs dosyasına izleme anahtarını da ekleyin.

TopNWords.cs ' deki örnek, Application Insights API 'sinden aşağıdaki [izleme çağrılarını](../azure-monitor/app/api-custom-events-metrics.md) kullanır:
* `TrackMetric()`-Bir işlem düğümünün gerekli metin dosyasını indirmek için ne kadar süreyle sürdüğünü izler.
* `TrackTrace()`-Kodunuza hata ayıklama çağrıları ekler.
* `TrackEvent()`-Yakalanacak ilginç olayları izler.

Bu örnek, özellikle özel durum işlemenin dışında bırakır. Bunun yerine Application Insights, hata ayıklama deneyimini önemli ölçüde geliştiren işlenmemiş özel durumları otomatik olarak raporlar. 

Aşağıdaki kod parçacığında, bu yöntemlerin nasıl kullanılacağı gösterilmektedir.

```csharp
public void CountWords(string blobName, int numTopN, string storageAccountName, string storageAccountKey)
{
    // simulate exception for some set of tasks
    Random rand = new Random();
    if (rand.Next(0, 10) % 10 == 0)
    {
        blobName += ".badUrl";
    }

    // log the url we are downloading the file from
    insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Download file from: {1}", this.taskId, blobName), SeverityLevel.Verbose));

    // open the cloud blob that contains the book
    var storageCred = new StorageCredentials(storageAccountName, storageAccountKey);
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(blobName), storageCred);
    using (Stream memoryStream = new MemoryStream())
    {
        // calculate blob download time
        DateTime start = DateTime.Now;
        blob.DownloadToStream(memoryStream);
        TimeSpan downloadTime = DateTime.Now.Subtract(start);

        // track how long the blob takes to download on this node
        // this will help debug timing issues or identify poorly performing nodes
        insightsClient.TrackMetric("Blob download in seconds", downloadTime.TotalSeconds, this.CommonProperties);

        memoryStream.Position = 0; //Reset the stream
        var sr = new StreamReader(memoryStream);
        var myStr = sr.ReadToEnd();
        string[] words = myStr.Split(' ');

        // log how many words were found in the text file
        insightsClient.TrackTrace(new TraceTelemetry(string.Format("Task {0}: Found {1} words", this.taskId, words.Length), SeverityLevel.Verbose));
        var topNWords =
            words.
                Where(word => word.Length > 0).
                GroupBy(word => word, (key, group) => new KeyValuePair<String, long>(key, group.LongCount())).
                OrderByDescending(x => x.Value).
                Take(numTopN).
                ToList();
        foreach (var pair in topNWords)
        {
            Console.WriteLine("{0} {1}", pair.Key, pair.Value);
        }

        // emit an event to track the completion of the task
        insightsClient.TrackEvent("Done counting words");
    }
}
```

### <a name="azure-batch-telemetry-initializer-helper"></a>Telemetri başlatıcısı Yardımcısı Azure Batch
Belirli bir sunucu ve örnek için telemetri raporlarken, Application Insights varsayılan değerler için Azure VM rolü ve VM adı ' nı kullanır. Azure Batch bağlamında örnek, bunun yerine havuz adının ve işlem düğümü adının nasıl kullanılacağını gösterir. Varsayılan değerleri geçersiz kılmak için bir [telemetri başlatıcısı](../azure-monitor/app/api-filtering-sampling.md#add-properties) kullanın. 

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;
using System;
using System.Threading;

namespace Microsoft.Azure.Batch.Samples.TelemetryInitializer
{
    public class AzureBatchNodeTelemetryInitializer : ITelemetryInitializer
    {
        // Azure Batch environment variables
        private const string PoolIdEnvironmentVariable = "AZ_BATCH_POOL_ID";
        private const string NodeIdEnvironmentVariable = "AZ_BATCH_NODE_ID";

        private string roleInstanceName;
        private string roleName;

        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleName))
            {
                // override the role name with the Azure Batch Pool name
                string name = LazyInitializer.EnsureInitialized(ref this.roleName, this.GetPoolName);
                telemetry.Context.Cloud.RoleName = name;
            }

            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // override the role instance with the Azure Batch Compute Node name
                string name = LazyInitializer.EnsureInitialized(ref this.roleInstanceName, this.GetNodeName);
                telemetry.Context.Cloud.RoleInstance = name;
            }
        }

        private string GetPoolName()
        {
            return Environment.GetEnvironmentVariable(PoolIdEnvironmentVariable) ?? string.Empty;
        }

        private string GetNodeName()
        {
            return Environment.GetEnvironmentVariable(NodeIdEnvironmentVariable) ?? string.Empty;
        }
    }
}
```

Telemetri başlatıcısı 'nı etkinleştirmek için, TopNWordsSample projesindeki ApplicationInsights. config dosyası aşağıdakileri içerir:

```xml
<TelemetryInitializers>
    <Add Type="Microsoft.Azure.Batch.Samples.TelemetryInitializer.AzureBatchNodeTelemetryInitializer, Microsoft.Azure.Batch.Samples.TelemetryInitializer"/>
</TelemetryInitializers>
``` 

## <a name="update-the-job-and-tasks-to-include-application-insights-binaries"></a>Application Insights ikilileri içerecek şekilde işi ve görevleri güncelleştirme

Application Insights işlem düğümleriniz üzerinde doğru şekilde çalışması için ikililerin doğru yerleştirildiğinden emin olun. Görevin yürütüldüğü sırada indirilmeleri için gerekli ikilileri görevin kaynak dosyaları koleksiyonuna ekleyin. Aşağıdaki kod parçacıkları Job.cs içindeki koda benzerdir.

İlk olarak, karşıya yüklenecek Application Insights dosyaların statik bir listesini oluşturun.

```csharp
private static readonly List<string> AIFilesToUpload = new List<string>()
{
    // Application Insights config and assemblies
    "ApplicationInsights.config",
    "Microsoft.ApplicationInsights.dll",
    "Microsoft.AI.Agent.Intercept.dll",
    "Microsoft.AI.DependencyCollector.dll",
    "Microsoft.AI.PerfCounterCollector.dll",
    "Microsoft.AI.ServerTelemetryChannel.dll",
    "Microsoft.AI.WindowsServer.dll",
    
    // custom telemetry initializer assemblies
    "Microsoft.Azure.Batch.Samples.TelemetryInitializer.dll",
 };
...
```

Sonra, görev tarafından kullanılan hazırlama dosyalarını oluşturun.
```csharp
...
// create file staging objects that represent the executable and its dependent assembly to run as the task.
// These files are copied to every node before the corresponding task is scheduled to run on that node.
FileToStage topNWordExe = new FileToStage(TopNWordsExeName, stagingStorageAccount);
FileToStage storageDll = new FileToStage(StorageClientDllName, stagingStorageAccount);

// Upload Application Insights assemblies
List<FileToStage> aiStagedFiles = new List<FileToStage>();
foreach (string aiFile in AIFilesToUpload)
{
    aiStagedFiles.Add(new FileToStage(aiFile, stagingStorageAccount));
}
...
```

`FileToStage` Yöntemi, bir dosyayı yerel diskten bir Azure Storage blobuna kolayca yüklemenize olanak tanıyan kod örneğindeki bir yardımcı işlevdir. Her dosya daha sonra bir işlem düğümüne indirilir ve bir görev tarafından başvurulur.

Son olarak, görevleri işe ekleyin ve gerekli Application Insights ikililerini dahil edin.
```csharp
...
// initialize a collection to hold the tasks that will be submitted in their entirety
List<CloudTask> tasksToRun = new List<CloudTask>(topNWordsConfiguration.NumberOfTasks);
for (int i = 1; i <= topNWordsConfiguration.NumberOfTasks; i++)
{
    CloudTask task = new CloudTask("task_no_" + i, String.Format("{0} --Task {1} {2} {3} {4}",
        TopNWordsExeName,
        string.Format("https://{0}.blob.core.windows.net/{1}",
            accountSettings.StorageAccountName,
            documents[i]),
        topNWordsConfiguration.TopWordCount,
        accountSettings.StorageAccountName,
        accountSettings.StorageAccountKey));

    //This is the list of files to stage to a container -- for each job, one container is created and 
    //files all resolve to Azure Blobs by their name (so two tasks with the same named file will create just 1 blob in
    //the container).
    task.FilesToStage = new List<IFileStagingProvider>
                        {
                            // required application binaries
                            topNWordExe,
                            storageDll,
                        };
    foreach (FileToStage stagedFile in aiStagedFiles)
   {
        task.FilesToStage.Add(stagedFile);
   }    
    task.RunElevated = false;
    tasksToRun.Add(task);
}
```

## <a name="view-data-in-the-azure-portal"></a>Azure portal verileri görüntüleme

İşi ve görevleri Application Insights kullanacak şekilde yapılandırdığınıza göre, havuzunuzdaki örnek işi çalıştırın. Azure portal gidin ve sağladığınız Application Insights kaynağını açın. Havuz sağlandıktan sonra, verilerin akışını ve günlüğe kaydedilmesini görmeniz gerekir. Bu makalenin geri kalanı yalnızca birkaç Application Insights özelliğine dokunduğunda, tüm özellik kümesini keşfetmeye ücretsizdir.

### <a name="view-live-stream-data"></a>Canlı akış verilerini görüntüle

Uygulama öngörüleri kaynağınızın izleme günlüklerini görüntülemek için **canlı akış**' ye tıklayın. Aşağıdaki ekran görüntüsünde, havuzdaki işlem düğümlerinden gelen canlı verilerin (örneğin, işlem düğümü başına CPU kullanımı) nasıl görüntüleneceği gösterilmektedir.

![Canlı akış işlem düğümü verileri](./media/monitor-application-insights/applicationinsightslivestream.png)

### <a name="view-trace-logs"></a>İzleme günlüklerini görüntüle

Uygulama öngörüleri kaynağınızın izleme günlüklerini görüntülemek için **Ara**' ya tıklayın. Bu görünümde izlemeler, olaylar ve özel durumlar dahil Application Insights tarafından yakalanan tanılama verilerinin bir listesi gösterilir. 

Aşağıdaki ekran görüntüsünde, bir görev için tek bir izlemenin günlüğe nasıl kaydedildiği ve daha sonra hata ayıklama amacıyla sorgulandığı gösterilmektedir.

![İzleme günlükleri resmi](./media/monitor-application-insights/tracelogsfortask.png)

### <a name="view-unhandled-exceptions"></a>İşlenmemiş özel durumları görüntüle

Aşağıdaki ekran görüntülerinde Application Insights, uygulamanızdan oluşturulan özel durumların nasıl günlüğe kaydettiği gösterilmektedir. Bu durumda, özel durumu oluşturan uygulamanın saniye içinde, belirli bir özel duruma gidebilir ve sorunu tanılayabilirsiniz.

![İşlenmeyen özel durumlar](./media/monitor-application-insights/exception.png)

### <a name="measure-blob-download-time"></a>Ölçü blobu karşıdan yükleme süresi

Özel ölçümler de portalda değerli bir araçtır. Örneğin, her işlem düğümünün işlendiği gereken metin dosyasını indirmesi için geçen ortalama süreyi görüntüleyebilirsiniz.

Örnek grafik oluşturmak için:
1. Application Insights kaynağınız **Ölçüm Gezgini** > **Grafik Ekle**' ye tıklayın.
2. Eklenen grafikte **Düzenle** ' ye tıklayın.
2. Grafik ayrıntılarını aşağıdaki gibi güncelleştirin:
   * **Grafik türünü** **Grid**olarak ayarlayın.
   * **Toplamayı** **Ortalama**olarak ayarlayın.
   * **Group By** - **NodeId**olarak ayarlanır.
   * **Ölçümler**' de,  > **Saniyeler içinde özel blob indirme**' yı seçin.
   * Görüntü **renk paletini** tercih ettiğiniz şekilde ayarlayın. 

![Düğüm başına blob indirme süresi](./media/monitor-application-insights/blobdownloadtime.png)


## <a name="monitor-compute-nodes-continuously"></a>İşlem düğümlerini sürekli izleyin

Performans sayaçları dahil olmak üzere tüm ölçümlerin yalnızca görevler çalışırken günlüğe kaydedildiğini fark etmiş olabilirsiniz. Bu davranış, Application Insights günlüklerin veri miktarını sınırladığından yararlıdır. Ancak, her zaman işlem düğümlerini izlemek istediğiniz durumlar vardır. Örneğin, Batch hizmeti aracılığıyla zamanlanmamış bir arka plan çalışması çalışıyor olabilir. Bu durumda, işlem düğümünün ömrü için çalışacak bir izleme işlemi ayarlayın. 

Bu davranışı gerçekleştirmenin bir yolu, Application Insights Kitaplığı yükleyen ve arka planda çalışan bir işlem üretme işlemidir. Örnekte, başlangıç görevi, ikili dosyaları makineye yükler ve bir işlemi süresiz olarak çalışır halde tutar. Bu işlem için Application Insights yapılandırma dosyasını, performans sayaçları gibi ilgilendiğiniz ek verileri yayacak şekilde yapılandırın.

```csharp
...
 // Batch start task telemetry runner
private const string BatchStartTaskFolderName = "StartTask";
private const string BatchStartTaskTelemetryRunnerName = "Microsoft.Azure.Batch.Samples.TelemetryStartTask.exe";
private const string BatchStartTaskTelemetryRunnerAIConfig = "ApplicationInsights.config";
...
CloudPool pool = client.PoolOperations.CreatePool(
    topNWordsConfiguration.PoolId,
    targetDedicated: topNWordsConfiguration.PoolNodeCount,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));
...

// Create a start task which will run a dummy exe in background that simply emits performance
// counter data as defined in the relevant ApplicationInsights.config.
// Note that the waitForSuccess on the start task was not set so the Compute Node will be
// available immediately after this command is run.
pool.StartTask = new StartTask()
{
    CommandLine = string.Format("cmd /c {0}", BatchStartTaskTelemetryRunnerName),
    ResourceFiles = resourceFiles
};
...
```

> [!TIP]
> Çözümünüzün yönetilebilirlik düzeyini artırmak için, derlemeyi bir [uygulama paketinde](./batch-application-packages.md)paketleyebilir. Ardından, uygulama paketini havuzlarınız için otomatik olarak dağıtmak üzere havuz yapılandırmasına bir uygulama paketi başvurusu ekleyin.
>

## <a name="throttle-and-sample-data"></a>Kısıtlama ve örnek veriler 

Üretimde çalışan Azure Batch uygulamalarının büyük ölçekli doğası nedeniyle, maliyetleri yönetmek için Application Insights tarafından toplanan veri miktarını sınırlamak isteyebilirsiniz. Bunu elde etmek için bazı mekanizmalarda [Application Insights örnekleme](../azure-monitor/app/sampling.md) bölümüne bakın.


## <a name="next-steps"></a>Sonraki adımlar
* [Application Insights](../azure-monitor/app/app-insights-overview.md)hakkında daha fazla bilgi edinin.

* Diğer dillerde Application Insights destek için [Diller, platformlar ve tümleştirmeler belgelerine](../azure-monitor/app/platforms.md)bakın.


