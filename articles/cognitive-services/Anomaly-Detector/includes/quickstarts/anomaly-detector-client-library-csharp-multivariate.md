---
title: Anomali algılayıcısı .NET çok değişen istemci kitaplığı hızlı başlangıç
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: a657b8f5bf967131a0168dbea5bb1db86b3b559e
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800350"
---
.NET için anomali algılayıcısının çok sayıda istemci kitaplığını kullanmaya başlayın. Paketi yüklemek ve hizmet tarafından sunulan algoritmaları kullanmaya başlamak için bu adımları izleyin. Yeni multivarianomali algılama API 'Leri, Machine Learning bilgisine veya etiketli verilere gerek kalmadan, gelişmiş AI 'nin ölçüm gruplarından aykırları tespit etmek üzere kolayca tümleştirilmesine olanak tanır. Farklı sinyaller arasındaki bağımlılıklar ve eş düzeyli ilişkiler otomatik olarak anahtar faktörleri olarak sayılır. Bu, karmaşık sistemlerinizi hatalara karşı korumanıza yardımcı olur.

.NET için anomali algılayıcısının çok değişkenli istemci kitaplığını kullanarak şunları yapın:

* Bir zaman serisi grubundan sistem düzeyi bozuklulıkları algılayın.
* Herhangi bir bireysel zaman serisi size çok bilgi vermez ve bir sorunu tespit etmek için tüm sinyallere bakmanız gerekir.
* Sistem durumunun çeşitli yönlerini ölçen, yüzlerce fiziksel varlık için onlarca yüzlerce farklı algılayıcı türü ile tahmine dayalı bakım.

[Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/anomalydetector/Azure.AI.AnomalyDetector)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.AnomalyDetector/3.0.0-preview.3)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* Geçerli [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) sürümü
* Azure aboneliğiniz olduktan sonra <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" "  target="_blank"> </a> anahtar ve uç noktanıza ulaşmak için Azure Portal bir anomali algılayıcı kaynağı oluşturun. Dağıtım için bekleyin ve **Kaynağa Git** düğmesini seçin.
    * Uygulamanızı anomali algılayıcı API 'sine bağlamak için oluşturduğunuz kaynaktaki anahtar ve uç nokta gerekir. Anahtarınızı ve uç noktanızı sonraki hızlı başlangıçta aşağıdaki koda yapıştırın.
    `F0`Hizmeti denemek ve daha sonra üretime yönelik ücretli bir katmana yükseltmek için ücretsiz fiyatlandırma katmanını () kullanabilirsiniz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-net-core-application"></a>Yeni bir .NET Core uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `anomaly-detector-quickstart-multivariate` . Bu komut, tek bir C# kaynak dosyası olan basit bir "Merhaba Dünya" projesi oluşturur: *program. cs*.

```dotnetcli
dotnet new console -n anomaly-detector-quickstart-multivariate
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin. Uygulamayı ile oluşturabilirsiniz:

```dotnetcli
dotnet build
```

Derleme çıktısı hiçbir uyarı veya hata içermemelidir.

```output
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>İstemci kitaplığını yükler

Uygulama dizini içinde, aşağıdaki komutla .NET için anomali algılayıcı istemci Kitaplığı ' nı bir daha yükleyeceksiniz:

```dotnetcli
dotnet add package Azure.AI.AnomalyDetector --version 3.0.0-preview.3
```

Proje dizininden *program. cs* dosyasını açın ve aşağıdakileri kullanarak aşağıdakileri ekleyin `directives` :

```csharp
using System;
using System.Collections.Generic;
using System.Drawing.Text;
using System.IO;
using System.Linq;
using System.Linq.Expressions;
using System.Net.NetworkInformation;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;
using Azure.AI.AnomalyDetector.Models;
using Azure.Core.TestFramework;
using Microsoft.Identity.Client;
using NUnit.Framework;
```

Uygulamanın `main()` yönteminde, kaynağınızın Azure uç noktası, API anahtarınız ve özel bir veri kaynağı için değişkenler oluşturun.

```csharp
string endpoint = "YOUR_API_KEY";
string apiKey =  "YOUR_ENDPOINT";
string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Anomali algılayıcı çok değişkenli API 'Leri kullanmak için, algılamayı kullanmadan önce kendi modelimizi eğitmemiz gerekir. Eğitim için kullanılan veriler, her zaman serisinin iki sütunlu, zaman damgası ve değer içeren CSV biçiminde olması gereken bir zaman dizisidir. Tüm zaman serileri tek bir zip dosyasında sıkıştırılmalıdır ve [Azure Blob depolama](../../../../storage/blobs/storage-blobs-introduction.md#blobs)alanına yüklenmelidir. Varsayılan olarak, zaman serisi değişkenini temsil etmek için dosya adı kullanılacaktır. Alternatif olarak, değişkenin adını. zip dosya adından farklı olacak şekilde isterseniz, dosyanın bir ek meta.jsZIP dosyasına dahil edilebilir. [BLOB SAS (paylaşılan erişim imzaları) URL 'si](../../../../storage/common/storage-sas-overview.md)oluşturduğumuzdan, eğitim için zip dosyasının URL 'sini kullanabiliriz.

## <a name="code-examples"></a>Kod örnekleri

Bu kod parçacıkları, .NET için anomali algılayıcı çok değişkenli istemci kitaplığı ile aşağıdakilerin nasıl yapılacağını göstermektedir:

* [İstemcinin kimliğini doğrulama](#authenticate-the-client)
* [Modeli eğitme](#train-the-model)
* [Anormallikleri Algıla](#detect-anomalies)
* [Modeli dışarı aktar](#export-model)
* [Modeli Sil](#delete-model)

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Uç nokta ve anahtarınızla bir anomali algılayıcı istemcisi oluşturun.

```csharp
var endpointUri = new Uri(endpoint);
var credential = new AzureKeyCredential(apiKey)

AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);
```

## <a name="train-the-model"></a>Modeli eğitme

Modelinize yönelik eğitimi işlemek için aşağıdaki gibi yeni bir özel zaman uyumsuz görev oluşturun. `TrainMultivariateModel`Modeli eğitme ve `GetMultivariateModelAysnc` eğitimin ne zaman tamamlandığını kontrol etmek için kullanacaksınız.

```csharp
private async Task trainAsync(AnomalyDetectorClient client, string datasource, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Training new model...");

        int model_number = await getModelNumberAsync(client, false).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models before training.", model_number));

        ModelInfo data_feed = new ModelInfo(datasource, start_time, end_time);
        Response response_header = client.TrainMultivariateModel(data_feed);
        response_header.Headers.TryGetValue("Location", out string trained_model_id_path);
        Guid trained_model_id = Guid.Parse(trained_model_id_path.Split('/').LastOrDefault());
        Console.WriteLine(trained_model_id);

        // Wait until the model is ready. It usually takes several minutes
        Response<Model> get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
        ModelStatus? model_status = null;
        int tryout_count = 0;
        TimeSpan create_limit = new TimeSpan(0, 3, 0);
        while (tryout_count < max_tryout & model_status != ModelStatus.Ready)
        {
            System.Threading.Thread.Sleep(10000);
            get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);
            ModelInfo model_info = get_response.Value.ModelInfo;
            Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}, status: {3}.", get_response.Value.ModelId, get_response.Value.CreatedTime, get_response.Value.LastUpdatedTime, model_info.Status));

            if (model_info != null)
            {
                model_status = model_info.Status;
            }
            tryout_count += 1;
        };
        get_response = await client.GetMultivariateModelAsync(trained_model_id).ConfigureAwait(false);

        if (model_status != ModelStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
        }

        model_number = await getModelNumberAsync(client).ConfigureAwait(false);
        Console.WriteLine(String.Format("{0} available models after training.", model_number));
        return trained_model_id;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Train error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="detect-anomalies"></a>Anormallikleri Algıla

Yeni eğitilen modeli kullanarak anormallikleri algılamak için `private async Task` adlı bir ad oluşturun `detectAsync` . Yeni bir oluşturacak ve bunu `DetectionRequest` parametresi olarak geçitirsiniz `DetectAnomalyAsync` .

```csharp
private async Task<DetectionResult> detectAsync(AnomalyDetectorClient client, string datasource, Guid model_id, DateTimeOffset start_time, DateTimeOffset end_time, int max_tryout = 500)
{
    try
    {
        Console.WriteLine("Start detect...");
        Response<Model> get_response = await client.GetMultivariateModelAsync(model_id).ConfigureAwait(false);

        DetectionRequest detectionRequest = new DetectionRequest(datasource, start_time, end_time);
        Response result_response = await client.DetectAnomalyAsync(model_id, detectionRequest).ConfigureAwait(false);
        var ok = result_response.Headers.TryGetValue("Location", out string result_id_path);
        Guid result_id = Guid.Parse(result_id_path.Split('/').LastOrDefault());
        // get detection result
        Response<DetectionResult> result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
        int tryout_count = 0;
        while (result.Value.Summary.Status != DetectionStatus.Ready & tryout_count < max_tryout)
        {
            System.Threading.Thread.Sleep(2000);
            result = await client.GetDetectionResultAsync(result_id).ConfigureAwait(false);
            tryout_count += 1;
        }

        if (result.Value.Summary.Status != DetectionStatus.Ready)
        {
            Console.WriteLine(String.Format("Request timeout after {0} tryouts", max_tryout));
            return null;
        }

        return result.Value;
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Detection error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="export-model"></a>Modeli dışarı aktar

Daha önce eğitilen modeli dışarı aktarmak için adlandırılmış bir `private async Task` ad oluşturun `exportAysnc` . `ExportModelAsync`Dışarı aktarmak istediğiniz modelin model kimliğini kullanacaksınız ve geçitirsiniz.

```csharp
private async Task exportAsync(AnomalyDetectorClient client, Guid model_id, string model_path = "model.zip")
{
    try
    {
        Stream model = await client.ExportModelAsync(model_id).ConfigureAwait(false);
        if (model != null)
        {
            var fileStream = File.Create(model_path);
            model.Seek(0, SeekOrigin.Begin);
            model.CopyTo(fileStream);
            fileStream.Close();
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(String.Format("Export error. {0}", e.Message));
        throw new Exception(e.Message);
    }
}
```

## <a name="delete-model"></a>Modeli Sil

Daha önce oluşturduğunuz bir modeli silmek `DeleteMultivariateModelAsync` ve silmek istediğiniz modelin model kimliğini iletmek için. Bir model KIMLIĞI almak için bize şunları yapabilirsiniz `getModelNumberAsync` :

```csharp
private async Task deleteAsync(AnomalyDetectorClient client, Guid model_id)
{
    await client.DeleteMultivariateModelAsync(model_id).ConfigureAwait(false);
    int model_number = await getModelNumberAsync(client).ConfigureAwait(false);
    Console.WriteLine(String.Format("{0} available models after deletion.", model_number));
}
private async Task<int> getModelNumberAsync(AnomalyDetectorClient client, bool delete = false)
{
    int count = 0;
    AsyncPageable<ModelSnapshot> model_list = client.ListMultivariateModelAsync(0, 10000);
    await foreach (ModelSnapshot x in model_list)
    {
        count += 1;
        Console.WriteLine(String.Format("model_id: {0}, createdTime: {1}, lastUpdateTime: {2}.", x.ModelId, x.CreatedTime, x.LastUpdatedTime));
        if (delete & count < 4)
        {
            await client.DeleteMultivariateModelAsync(x.ModelId).ConfigureAwait(false);
        }
    }
    return count;
}
```

## <a name="main-method"></a>Main yöntemi

Tüm bileşen parçalarına sahip olduğunuza göre, yeni oluşturulan görevlerinizi çağırmak için ana yönteminizin ek kod eklemeniz gerekir.

```csharp

{
    //read endpoint and apiKey
     string endpoint = "YOUR_API_KEY";
    string apiKey =  "YOUR_ENDPOINT";
    string datasource = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
    Console.WriteLine(endpoint);
    var endpointUri = new Uri(endpoint);
    var credential = new AzureKeyCredential(apiKey);

    //create client
    AnomalyDetectorClient client = new AnomalyDetectorClient(endpointUri, credential);

    // train
    TimeSpan offset = new TimeSpan(0);
    DateTimeOffset start_time = new DateTimeOffset(2021, 1, 1, 0, 0, 0, offset);
    DateTimeOffset end_time = new DateTimeOffset(2021, 1, 2, 12, 0, 0, offset);
    Guid? model_id_raw = null;
    try
    {
        model_id_raw = await trainAsync(client, datasource, start_time, end_time).ConfigureAwait(false);
        Console.WriteLine(model_id_raw);
        Guid model_id = model_id_raw.GetValueOrDefault();

        // detect
        start_time = end_time;
        end_time = new DateTimeOffset(2021, 1, 3, 0, 0, 0, offset);
        DetectionResult result = await detectAsync(client, datasource, model_id, start_time, end_time).ConfigureAwait(false);
        if (result != null)
        {
            Console.WriteLine(String.Format("Result ID: {0}", result.ResultId));
            Console.WriteLine(String.Format("Result summary: {0}", result.Summary));
            Console.WriteLine(String.Format("Result length: {0}", result.Results.Count));
        }

        // export model
        await exportAsync(client, model_id).ConfigureAwait(false);

        // delete
        await deleteAsync(client, model_id).ConfigureAwait(false);
    }
    catch (Exception e)
    {
        String msg = String.Format("Multivariate error. {0}", e.Message);
        if (model_id_raw != null)
        {
            await deleteAsync(client, model_id_raw.GetValueOrDefault()).ConfigureAwait(false);
        }
        Console.WriteLine(msg);
        throw new Exception(msg);
    }
}

```

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Uygulamayı `dotnet run` uygulama dizininizdeki komutla çalıştırın.

```dotnetcli
dotnet run
```

## <a name="next-steps"></a>Sonraki adımlar

* [Anomali algılayıcısı çok değişen en iyi uygulamalar](../../concepts/best-practices-multivariate.md)
