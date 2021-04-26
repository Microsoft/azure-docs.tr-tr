---
title: Değişiklik akışı çekme modeli
description: Değişiklik akışını ve çekme modeli ve değişiklik akışı Işlemcisi arasındaki farkları okumak için Azure Cosmos DB değişiklik akışı çekme modelini nasıl kullanacağınızı öğrenin
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 03/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 9279dddc92629b17a2a73f3a41fe261d322d677e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463689"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB akış çekme modelini değiştirme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Değişiklik akışı çekme modeliyle birlikte Azure Cosmos DB değişiklik akışını kendi hızınızda kullanabilirsiniz. Değişiklik [akışı işlemcisi](change-feed-processor.md)ile zaten yaptığınız gibi, değişiklik akışı çekme modelini kullanarak değişikliklerin birden fazla değişiklik akışı tüketicisinden işlenmesini paralel hale getirmek.

> [!NOTE]
> Değişiklik akışı çekme modeli, şu anda yalnızca [Azure Cosmos DB .NET SDK sürümünde önizlemededir](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) . Önizleme henüz diğer SDK sürümleri için kullanılamaz.

## <a name="comparing-with-change-feed-processor"></a>Değişiklik akışı işlemcisi ile karşılaştırma

Pek çok senaryo değişiklik akışını [değişiklik akışı işlemcisini](change-feed-processor.md) veya çekme modelini kullanarak işleyebilir. Çekme modelinin devamlılık belirteçleri ve değişiklik akışı işlemcisinin kira kapsayıcısı, değişiklik akışındaki son işlenen öğe (veya öğe toplu işi) için hem "yer işaretleri" dir.

Ancak, devamlılık belirteçlerini bir kira kapsayıcısına dönüştüremezsiniz (veya tersi).

> [!NOTE]
> Çoğu durumda, değişiklik akışından okumanız gerektiğinde en basit seçenek, [değişiklik akışı işlemcisini](change-feed-processor.md)kullanmaktır.

Bu senaryolarda çekme modelini kullanmayı göz önünde bulundurmanız gerekir:

- Belirli bir bölüm anahtarından değişiklikleri oku
- İstemcinizin işleme için değişiklikleri alacağı hızını denetleyin
- Değişiklik akışında var olan verileri bir kerelik okuma işlemi gerçekleştirin (örneğin, veri geçişi yapmak için)

Aşağıda, değişiklik akışı işlemcisi ve çekme modeli arasındaki bazı önemli farklılıklar verilmiştir:

|Özellik  | Değişiklik akışı işlemcisi| Çekme modeli |
| --- | --- | --- |
| Değişiklik akışındaki geçerli noktayı takip tutma | Kira (Azure Cosmos DB kapsayıcısında depolanır) | Devamlılık belirteci (bellekte depolanan veya el ile kalıcı) |
| Geçmiş değişiklikleri yeniden oynatma yeteneği | Evet, gönderim modeliyle | Evet, çekme modeliyle|
| Gelecekteki değişiklikler için yoklama | Kullanıcı tarafından belirtilen değişiklikleri otomatik olarak denetler `WithPollInterval` | El ile |
| Yeni değişiklik olmadığı davranış | Otomatik olarak bekle `WithPollInterval` ve yeniden denetle | Özel durum yakalanmalıdır ve el ile yeniden denetlenecektir |
| Tüm kapsayıcılardan değişiklikleri işle | Evet ve aynı kapsayıcıdan birden çok iş parçacığı/makine tarafından otomatik olarak paralelleştirildi| Evet ve Feedtoken kullanarak manuel olarak paralelleştirildi |
| Yalnızca tek bir bölüm anahtarından değişiklikleri işle | Desteklenmez | Yes|
| Destek düzeyi | Genel kullanıma sunuldu | Önizleme |

> [!NOTE]
> Değişiklik akışı işlemcisini kullanarak okurken, hiçbir yeni değişiklik olmadığı durumlarda açıkça işlem yapmanız gerekir. 

## <a name="consuming-an-entire-containers-changes"></a>Kapsayıcının tüm değişikliklerinin kullanılması

`FeedIterator`Çekme modelini kullanarak değişiklik akışını işlemek için bir oluşturabilirsiniz. İlk olarak bir oluşturduğunuzda `FeedIterator` , `ChangeFeedStartFrom` değişiklikleri okumak için başlangıç konumundan ve istediğiniz gibi, gerekli bir değeri belirtmeniz gerekir `FeedRange` . , `FeedRange` Bölüm anahtarı değerlerinin bir aralığıdır ve bu belirli özel kullanılarak değişiklik akışından okunacak öğeleri belirtir `FeedIterator` .

İsteğe bağlı olarak `ChangeFeedRequestOptions` bir kümesini belirtebilirsiniz `PageSizeHint` . , `PageSizeHint` Tek bir sayfada döndürülecek en fazla öğe sayısıdır.

`FeedIterator`İki şekilde sunulur. Aşağıdaki örnek varlık nesneleri döndüren örneklere ek olarak, destek ile yanıtı da elde edebilirsiniz `Stream` . Akışlar, verileri ilk seri durumdan çıkmadan, istemci kaynakları üzerinde kaydederek okuyabilmeniz için izin verir.

İşte `FeedIterator` , bu durumda bir nesnesi olan varlık nesnelerini döndüren bir örnek `User` :

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Aşağıda şunu döndüren bir örneği elde etmek için bir örnek verilmiştir `FeedIterator` `Stream` :

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);
```

Bir ' a vermezseniz `FeedRange` `FeedIterator` , bir kapsayıcının değişiklik akışını kendi hızınızda işleyebilirsiniz. İşte geçerli zamandan başlayarak tüm değişiklikleri okumayı Başlatan bir örnek:

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now(), ChangeFeedMode.Incremental);

while (iteratorForTheEntireContainer.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Değişiklik akışı etkili bir şekilde tüm yazma ve güncelleştirme işlemlerini çevreleyen öğelerin sonsuz bir listesi olduğundan, değeri `HasMoreResults` her zaman true 'dur. Değişiklik akışını okumaya çalıştığınızda yeni bir değişiklik yoksa bir özel durum alırsınız. Yukarıdaki örnekte, özel durum, değişiklikler için yeniden denetlenmeden önce 5 saniye bekledikten sonra işlenir.

## <a name="consuming-a-partition-keys-changes"></a>Bölüm anahtarının değişikliklerini kullanma

Bazı durumlarda, yalnızca belirli bir bölüm anahtarının değişikliklerini işlemek isteyebilirsiniz. `FeedIterator`Belirli bir bölüm anahtarı için bir elde edebilir ve değişiklikleri tüm kapsayıcı için kullanabileceğiniz şekilde işleyebilirsiniz.

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(
    ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue")), ChangeFeedMode.Incremental));

while (iteratorForThePartitionKey.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>Paralelleştirme için FeedRange kullanma

[Değişiklik akışı işlemcisinde](change-feed-processor.md), çalışma otomatik olarak birden çok tüketiciye yayılır. Değişiklik akışı Çekme modelinde, `FeedRange` değişiklik akışı işlemesini paralel hale getirmek için kullanabilirsiniz. Bir `FeedRange` bölüm anahtarı değerleri aralığını temsil eder.

Bu, Kapsayıcınız için aralıkların bir listesinin nasıl alınacağını gösteren bir örnek aşağıda verilmiştir:

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

Kapsayıcınız için FeedRanges listesini aldığınızda, `FeedRange` [fiziksel bölüm](partitioning-overview.md#physical-partitions)başına bir tane elde edersiniz.

Bir kullanarak `FeedRange` , `FeedIterator` birden fazla makinede veya iş parçacığında değişiklik akışı işlemesini paralel hale getirmek için bir oluşturabilirsiniz. Tüm kapsayıcı veya tek bir bölüm anahtarı için nasıl elde eteceğinize ilişkin bir önceki örneğin aksine `FeedIterator` , akış aralıklarını kullanarak değişiklik akışını paralel olarak işleyebilen birden fazla Feediterler elde edebilirsiniz.

FeedRanges kullanmak istediğiniz durumda, FeedRanges alıp bu makinelere dağıtan bir Orchestrator işleminiz olması gerekir. Bu dağıtım şu olabilir:

* `FeedRange.ToJsonString`Bu dize değerini kullanarak ve dağıtarak. Tüketiciler bu değeri şu şekilde kullanabilir `FeedRange.FromJsonString`
* Dağıtım sürecde ise, `FeedRange` nesne başvurusunu geçirerek.

Aşağıda, paralel olarak okunan iki kuramsal ayrı makine kullanarak kapsayıcının değişiklik akışından nasıl okunacağını gösteren bir örnek verilmiştir:

Makine 1:

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]), ChangeFeedMode.Incremental);
while (iteratorA.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

Makine 2:

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]), ChangeFeedMode.Incremental);
while (iteratorB.HasMoreResults)
{
    try {
        FeedResponse<User> users = await iteratorA.ReadNextAsync();

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
    }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }
}
```

## <a name="saving-continuation-tokens"></a>Devamlılık belirteçleri kaydediliyor

`FeedIterator`Devamlılık belirteci oluşturarak sitenizin konumunu kaydedebilirsiniz. Devamlılık belirteci, FeedIterator 'ın son işlenen değişikliklerinin izlenmesini tutan bir dize değeridir. Bu, `FeedIterator` daha sonra bu noktada sürdürülmesine izin verir. Aşağıdaki kod, kapsayıcı oluşturulduktan sonra değişiklik akışını okuyacaktır. Daha fazla değişiklik yoksa, değişiklik akışı tüketiminin daha sonra devam edebilmesi için bir devamlılık belirteci kalıcı hale gelir.

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(), ChangeFeedMode.Incremental);

string continuation = null;

while (iterator.HasMoreResults)
{
   try { 
        FeedResponse<User> users = await iterator.ReadNextAsync();
        continuation = users.ContinuationToken;

        foreach (User user in users)
            {
                Console.WriteLine($"Detected change for user with id {user.id}");
            }
   }
    catch (CosmosException exception) when (exception.StatusCode == System.Net.HttpStatusCode.NotModified)
    {
        Console.WriteLine($"No new changes");
        await Task.Delay(TimeSpan.FromSeconds(5));
    }   
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation), ChangeFeedMode.Incremental);
```

Cosmos kapsayıcısı hala mevcut olduğu sürece FeedIterator 'ın devamlılık belirtecinin süresi dolmaz.

## <a name="next-steps"></a>Sonraki adımlar

* [Değişiklik akışına genel bakış](change-feed.md)
* [Değişiklik akışı işlemcisini kullanma](change-feed-processor.md)
* [Azure İşlevleri'ni tetikleme](change-feed-functions.md)
