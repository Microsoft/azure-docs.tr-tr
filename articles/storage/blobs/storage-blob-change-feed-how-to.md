---
title: Azure Blob depolama 'da değişiklik akışını işleme (Önizleme) | Microsoft Docs
description: .NET istemci uygulamasında değişiklik akışı günlüklerini işlemeyi öğrenin
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111862"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob depolama 'da değişiklik akışını işleme (Önizleme)

Değişiklik akışı, bloblarda oluşan tüm değişikliklerin işlem günlükleri ve Depolama hesabınızdaki blob meta verileri sağlar. Bu makalede blob değişiklik akışı işlemci kitaplığını kullanarak değişiklik akışı kayıtlarının nasıl okunacağı gösterilmektedir.

Değişiklik akışı hakkında daha fazla bilgi edinmek için bkz. [Azure Blob depolama (Önizleme) içinde akışı değiştirme](storage-blob-change-feed.md).

> [!NOTE]
> Değişiklik akışı genel önizlemededir ve **westcentralus** ve **westus2** bölgelerinde kullanılabilir. Bu özellik hakkında bilinen sorunlar ve sınırlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure Blob depolamada akış desteğini değiştirme](storage-blob-change-feed.md). Değişiklik akışı işlemci kitaplığı, şu anda ve bu kitaplık genel kullanıma sunulduğunda değişiklik gösterebilir.

## <a name="get-the-blob-change-feed-processor-library"></a>Blob değişiklik akışı işlemci kitaplığını al

1. Visual Studio 'da, NuGet paket kaynaklarınıza `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json` URL 'sini ekleyin. 

   Nasıl yapılacağını öğrenmek için bkz. [paket kaynakları](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources).

2. NuGet Paket Yöneticisi 'nde **Microsoft. Azure. Storage. changefeed** paketini bulun ve projenize yükler. 

   Nasıl yapılacağını öğrenmek için bkz. [paket bulma ve yüklemeyi oluşturma](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

## <a name="connect-to-the-storage-account"></a>Depolama hesabına Bağlan

[Cloudstorageaccount. Tryparo](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse) yöntemini çağırarak bağlantı dizesini ayrıştırın. 

Ardından, [Cloudstorageaccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient) metodunu çağırarak depolama hesabınızda blob depolamayı temsil eden bir nesne oluşturun.

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>Değişiklik akışını Başlat

Aşağıdaki using deyimlerini, kod dosyanızın en üstüne ekleyin. 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

Ardından, **Getcontainerreference** yöntemini çağırarak **changefeed** sınıfının bir örneğini oluşturun. Değişiklik akışı kapsayıcısının adını geçirin.

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>Kayıtları okuma

> [!NOTE]
> Değişiklik akışı, depolama hesabınızda sabit ve salt okunurdur bir varlıktır. Herhangi bir sayıda uygulama değişiklik akışını eşzamanlı ve bağımsız olarak kendi kolaylığını okuyabilir ve işleyebilir. Bir uygulama bunları okurken değişiklik akışından kayıt kaldırılmaz. Her bir tüketen okuyucunun okuma veya yineleme durumu yalnızca uygulamanız tarafından bağımsızdır ve korunur.

Kayıtları okumanın en kolay yolu, **Changefeedreader** sınıfının bir örneğini oluşturmaktır. 

Bu örnek, değişiklik akışındaki tüm kayıtlar boyunca yinelenir ve sonra her bir kayıttaki birkaç değeri konsola yazdırır. 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>Kaydedilmiş bir konumdan kayıtları okumaya devam ediliyor

Okuma konumunuzu değişiklik akışınıza kaydetmeyi ve daha sonra kayıtları daha sonra yineleme işlemini sürdürmeyi seçebilirsiniz. Değişiklik akışı yinelemesinin durumunu herhangi bir zamanda **Changefeedreader. serializet()** yöntemini kullanarak kaydedebilirsiniz. Durum bir **dizedir** ve uygulamanız bu durumu uygulamanızın tasarımına göre kaydedebilir (örneğin: bir veritabanına veya dosyaya).

```csharp
    string currentReadState = processor.SerializeState();
```

**CreateChangeFeedReaderFromPointerAsync** yöntemini kullanarak **changefeedreader** oluşturarak son durumdaki kayıtlar arasında yineleme yapmaya devam edebilirsiniz.

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>Kayıtların akışını işleme

Değişiklik akışı kayıtlarını geldikçe işlemeyi tercih edebilirsiniz. Bkz. [Özellikler](storage-blob-change-feed.md#specifications).

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>Bir zaman aralığı içinde kayıtları okuma

Değişiklik akışı, değişiklik olay zamanına göre saatlik kesimlerde düzenlenir. Bkz. [Özellikler](storage-blob-change-feed.md#specifications). Belirli bir zaman aralığı içinde kalan değişiklik akışı segmentlerinden kayıtları okuyabilirsiniz.

Bu örnek, tüm segmentlerin başlangıç zamanlarını alır. Ardından, başlangıç zamanı son tüketilebilir segmentin veya istenen aralığın bitiş saatinin ötesinde, bu liste üzerinde dolaşır. 

### <a name="selecting-segments-for-a-time-range"></a>Bir zaman aralığı için segmentleri seçme

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>Kesimdeki kayıtları okuma

Tek tek segmentlerden veya segment aralıklarından kayıtları okuyabilirsiniz.

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>Bir saatten başlayarak kayıtları okuma

Bir başlangıç segmentinden bitiş tarihine kadar değişiklik akışı kayıtlarını okuyabilirsiniz. Bir zaman aralığı içinde kayıtları okumaya benzer şekilde, segmentleri listeleyebilir ve yineleme başlatacak bir segment seçebilirsiniz.

Bu örnek, işlenecek ilk segmentin [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) sayısını alır.

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

Bu örnek, başlangıç kesiminin [DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) değerinden başlayarak değişiklik akışı kayıtlarını işler.

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> Bir segmenti bir veya daha fazla *Chunkfilepath*içinde değişiklik akışı günlüklerini içerebilir. Birden çok *Chunkfilepath* söz konusu olduğunda, sistemin yayımlama aktarım hızını yönetmek için kayıtları dahili olarak birden çok parçaya bölümlendirildi. Segmentin her bölümünün birbirini dışlayan Bloblar için değişiklikler içereceği ve sıralamayı ihlal etmeden bağımsız olarak işlenebildiği garanti edilir. Senaryonuz için en verimli olan kayıt düzeyindeki kayıtları yinelemek için **ChangeFeedSegmentShardReader** sınıfını kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Değişiklik akışı günlükleri hakkında daha fazla bilgi edinin. Bkz. [Azure Blob depolamada akışı değiştirme (Önizleme)](storage-blob-change-feed.md)
