---
title: .NET ile Media Services iş bildirimlerini izlemek için Azure kuyruk depolamayı kullanma | Microsoft Docs
description: Media Services iş bildirimlerini izlemek için Azure kuyruk depolamayı nasıl kullanacağınızı öğrenin. Kod örneği C# dilinde yazılır ve .NET için Media Services SDK kullanır.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: dc6ab94b3685e55125032b0afa52076eae72ba5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016228"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>.NET ile Media Services iş bildirimlerini izlemek için Azure kuyruk depolamayı kullanma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2’ye herhangi bir yeni özellik veya işlevsellik eklenmemektedir. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)

Kodlama işlerini çalıştırdığınızda, genellikle işin ilerlemesini izlemek için bir yol gerekir. Media Services [Azure kuyruk depolama](../../storage/queues/storage-dotnet-how-to-use-queues.md)'ya bildirim sunacak şekilde yapılandırabilirsiniz. Sıra depolamadan bildirim alarak iş ilerlemesini izleyebilirsiniz. 

Kuyruk depolamaya gönderilen iletilere dünyanın herhangi bir yerinden erişilebilir. Kuyruk depolama mesajlaşma mimarisi güvenilir ve yüksek oranda ölçeklenebilir. İletiler için yoklama kuyruğu depolaması, diğer yöntemler kullanılarak önerilir.

Media Services bildirimleri dinlemek için bir yaygın senaryo, bir kodlama işi tamamlandıktan sonra bazı ek görevler gerçekleştirmesi gereken bir içerik yönetim sistemi geliştirmemekte (örneğin, bir iş akışında bir sonraki adımı tetiklemek veya içerik yayımlamak için).

Bu makalede, kuyruk depolamadaki bildirim iletilerinin nasıl alınacağı gösterilmektedir.  

## <a name="considerations"></a>Dikkat edilmesi gerekenler
Sıra depolamayı kullanan Media Services uygulamalar geliştirilirken aşağıdakileri göz önünde bulundurun:

* Kuyruk depolama, ilk ilk çıkar (FıFO) sıralı teslimin garantisi sağlamaz. Daha fazla bilgi için bkz. [Azure kuyrukları ve Azure Service Bus kuyrukları karşılaştırması ve](/previous-versions/azure/hh767287(v=azure.100))benzerlikler.
* Kuyruk depolama bir gönderim hizmeti değil. Kuyruğu yoklamak için gerekir.
* İstediğiniz sayıda sıraya sahip olabilirsiniz. Daha fazla bilgi için bkz. [kuyruk hizmeti REST API](/rest/api/storageservices/queue-service-rest-api).
* Kuyruk depolamada bazı sınırlamalar ve bilgiler göz önünde bulundurulmalıdır. Bunlar [Azure kuyruklarıyla ve Azure Service Bus kuyruklarıyla karşılaştırıldığında ve](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)benzerlikler ile açıklanmıştır.

## <a name="net-code-example"></a>.NET kod örneği

Bu bölümdeki kod örneği şunları yapar:

1. Bildirim iletisi biçimiyle eşlenen **EncodingJobMessage** sınıfını tanımlar. Kod, kuyruktan alınan iletileri **EncodingJobMessage** türünün nesnelerine serileştirir.
2. app.config dosyasından Media Services ve depolama hesabı bilgilerini yükler. Kod örneği, **Cloudmediacontext** ve **cloudqueue** nesnelerini oluşturmak için bu bilgileri kullanır.
3. Kodlama işiyle ilgili bildirim iletilerini alan kuyruğu oluşturur.
4. Kuyrukla eşlenen bildirim uç noktasını oluşturur.
5. Bildirim uç noktasını işe iliştirir ve kodlama işini gönderir. Bir işe eklenmiş birden fazla bildirim uç noktası olabilir.
6. **Notificationjobstate. FinalStatesOnly** öğesini **AddNew** yöntemine geçirir. (Bu örnekte, yalnızca iş işlemenin son durumlarıyla ilgileniyoruz.)

    ```csharp
    job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
    ```

7. **Notificationjobstate. All** geçirirseniz, şu durum değişikliği bildirimlerinin tümünü alırsınız: sıraya alınmış, zamanlanmış, işleme ve bitti. Ancak, daha önce belirtildiği gibi, kuyruk depolaması sipariş teslimini garanti etmez. İletileri sıralamak için, **zaman damgası** özelliğini kullanın (aşağıdaki örnekte **EncodingJobMessage** türünde tanımlanmıştır). Yinelenen iletiler mümkündür. Yinelemeleri denetlemek için **ETag özelliğini** kullanın ( **EncodingJobMessage** türü üzerinde tanımlanmıştır). Bazı durum değişikliği bildirimlerinin atlanması da mümkündür.
8. Her 10 saniyede bir kuyruğu denetleyerek işin tamamlanmış duruma gelmesini bekler. İşlendikten sonra iletileri siler.
9. Kuyruğu ve bildirim uç noktasını siler.

> [!NOTE]
> Bir işin durumunu izlemek için önerilen yol, aşağıdaki örnekte gösterildiği gibi bildirim iletilerini dinlemedir:
>
> Alternatif olarak, **ıjob. State** özelliğini kullanarak bir işin durumunu denetleyebilirsiniz.  Bir işin tamammasıyla ilgili bir bildirim iletisi, **ıjob** üzerindeki durum **tamamlandı** olarak ayarlanmadan önce gelebilir. **Ijob. State** özelliği, hafif bir gecikmeyle doğru durumu yansıtır.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio projesi oluşturup yapılandırma

1. Geliştirme ortamınızı kurun ve app.config dosyanızı [.NET ile Media Services geliştirme](media-services-dotnet-how-to-use.md) bölümünde açıklandığı gibi bağlantı bilgileriyle doldurun. 
2. Yeni bir klasör oluşturun (klasör yerel sürücünüzde herhangi bir yerde olabilir) ve kodlayıp akışa almak ya da aşamalı indirmek istediğiniz bir. mp4 dosyasını kopyalayın. Bu örnekte, "C:\Media" yolu kullanılır.
3. **System. Runtime. Serialization** kitaplığına bir başvuru ekleyin.

### <a name="code"></a>Kod

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Yukarıdaki örnek aşağıdaki çıktıyı üretti: değerlerinizin farklılık göstermesi gerekir.

```output
Created assetFile BigBuckBunny.mp4
Upload BigBuckBunny.mp4
Done uploading of BigBuckBunny.mp4

EventType: NotificationEndPointRegistration
MessageVersion: 1.0
ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
TimeStamp: 2013-05-14T20:22:37
    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
    State: Registered
    Name: dde957b2-006e-41f2-9869-a978870ac620
    Created: 2013-05-14T20:22:35

EventType: JobStateChange
MessageVersion: 1.0
ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
TimeStamp: 2013-05-14T20:24:40
    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
    JobName: My MP4 to Smooth Streaming encoding job
    NewState: Finished
    OldState: Processing
    AccountName: westeuropewamsaccount
job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
State: Finished
```

## <a name="next-step"></a>Sonraki adım
Media Services öğrenme yollarını gözden geçirin.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
