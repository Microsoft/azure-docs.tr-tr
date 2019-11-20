---
title: Azure Notification Hubs kayıtlarını toplu olarak içeri ve dışarı aktarma | Microsoft Docs
description: Bir Bildirim Hub 'ında çok sayıda işlem gerçekleştirmek veya tüm kayıtları dışarı aktarmak için Notification Hubs toplu destek kullanmayı öğrenin.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 8eb03a42f38c0cc7fe82eda6a81d1c8c1213ec74
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212391"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>Azure Notification Hubs kayıtlarını toplu olarak içeri ve dışarı aktarma
Bir Bildirim Hub 'ında çok sayıda kayıt oluşturmak veya değiştirmek için gerekli senaryolar vardır. Bu senaryolardan bazıları, toplu hesaplamalar sonrasında etiket güncelleştirmeleridir veya mevcut bir gönderme uygulamasını Notification Hubs kullanmak üzere geçirmektedir.

Bu makalede, bir Bildirim Hub 'ında çok sayıda işlemin nasıl gerçekleştirileceği veya tüm kayıtların toplu olarak dışarı aktarılması açıklanmaktadır.

## <a name="high-level-flow"></a>Üst düzey akış
Batch desteği milyonlarca kayıt içeren uzun süreli işleri destekleyecek şekilde tasarlanmıştır. Toplu destek, bu ölçeğe ulaşmak için iş ayrıntılarını ve çıktıyı depolamak üzere Azure Storage kullanır. Toplu güncelleştirme işlemleri için, kullanıcının içerik kayıt güncelleştirme işlemleri listesi olan bir blob kapsayıcısında bir dosya oluşturması gerekir. Kullanıcı başlatıldığında, bir çıkış dizinine (aynı zamanda bir blob kapsayıcısında) bir URL ile birlikte giriş blobuna bir URL sağlar. İş başladıktan sonra, Kullanıcı, işin başlangıcında belirtilen bir URL konumunu sorgulayarak durumu denetleyebilir. Belirli bir iş yalnızca belirli bir türdeki (oluşturma, güncelleştirme veya silme) işlemleri gerçekleştirebilir. Dışarı aktarma işlemleri, anormal olarak gerçekleştirilir.

## <a name="import"></a>İçeri Aktarma

### <a name="set-up"></a>Ayarlama
Bu bölüm, aşağıdaki varlıklara sahip olduğunuzu varsayar:

- Sağlanan bir Bildirim Hub 'ı.
- Bir Azure Depolama Blobu kapsayıcısı.
- [Azure depolama NuGet paketine](https://www.nuget.org/packages/windowsazure.storage/) ve [Notification Hubs NuGet paketine](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)başvurur.

### <a name="create-input-file-and-store-it-in-a-blob"></a>Giriş dosyası oluşturma ve bir blob 'da depolama
Bir giriş dosyası, satır başına bir tane olmak üzere XML 'de serileştirilmiş kayıtların bir listesini içerir. Aşağıdaki kod örneği, Azure SDK 'sını kullanarak kayıtların serileştirilme ve BLOB kapsayıcısına nasıl yükleneceği gösterilmektedir.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> Yukarıdaki kod, kayıtları bellekte seri hale getirir ve sonra akışın tamamını bir blob 'a yükler. Yalnızca birkaç megabayttan daha fazla bir dosya yüklediyseniz, bu adımları nasıl gerçekleştireceğiniz hakkında bilgi için bkz. Azure Blob Kılavuzu. Örneğin, [blok Blobları](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>URL belirteçleri oluştur
Giriş dosyanız karşıya yüklendikten sonra, hem giriş dosyası hem de çıkış dizini için Bildirim Hub 'ına sağlanacak URL 'Leri oluşturun. Giriş ve çıkış için iki farklı blob kapsayıcı kullanabilirsiniz.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>İşi gönderme
İki giriş ve çıkış URL 'Si ile, artık toplu işi başlatabilirsiniz.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

Bu örnek, giriş ve çıkış URL 'lerine ek olarak, aşağıdaki türlerden `NotificationHubJob` biri olabilecek bir `JobType` nesnesi içeren bir nesne oluşturur:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

Çağrı tamamlandıktan sonra, iş Bildirim Hub 'ı tarafından devam eder ve [Getnotificationhubjobasync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet)çağrısı ile durumunu kontrol edebilirsiniz.

İş tamamlandığında, çıkış dizininizde aşağıdaki dosyalara bakarak sonuçları inceleyebilirsiniz:

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

Bu dosyalar, toplu işleminizden başarılı ve başarısız işlemlerin listesini içerir. Dosya biçimi, her `.cvs`satırda orijinal giriş dosyasının satır numarası ve işlemin çıkışı (genellikle oluşturulan veya güncelleştirilmiş kayıt açıklaması) ile aynıdır.

### <a name="full-sample-code"></a>Tam örnek kod
Aşağıdaki örnek kod kayıtları bir Bildirim Hub 'ına aktarır.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>Dışarı Aktarma
Kayıt verme, aşağıdaki farklılıklar ile içeri aktarmaya benzer:

- Yalnızca çıkış URL 'sine ihtiyacınız vardır.
- Exportkayıtları türünde bir NotificationHubJob oluşturursunuz.

### <a name="sample-code-snippet"></a>Örnek kod parçacığı
Java 'da kayıtları dışarı aktarmaya yönelik örnek kod parçacığı aşağıda verilmiştir:

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>Sonraki adımlar
Kayıtlar hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:

- [Kayıt yönetimi](notification-hubs-push-notification-registration-management.md)
- [Kayıtlar için Etiketler](notification-hubs-tags-segment-push-message.md)
- [Şablon kayıtları](notification-hubs-templates-cross-platform-push-messages.md)
