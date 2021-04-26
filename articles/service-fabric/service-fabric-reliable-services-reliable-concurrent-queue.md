---
title: Azure Service Fabric ReliableConcurrentQueue
description: ReliableConcurrentQueue, paralel sıraya ve sıralara izin veren yüksek performanslı bir sıralardır.
ms.topic: conceptual
ms.date: 5/1/2017
ms.openlocfilehash: d6852982621d3efd3f4a8597a2959fceb13abd12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784300"
---
# <a name="introduction-to-reliableconcurrentqueue-in-azure-service-fabric"></a>Azure Service Fabric ReliableConcurrentQueue 'a giriş
Güvenilir eşzamanlı sıra, sıraya alma ve sıradan çıkarma işlemleri için yüksek eşzamanlılık özelliklerine sahip zaman uyumsuz, işlemsel ve çoğaltılan bir sıradır. [Güvenilir sıra](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) tarafından sağlanan katı FIFO sıralamasını inceleyerek yüksek aktarım hızı ve düşük gecikme süresi sunmak üzere tasarlanmıştır ve bunun yerine en iyi çaba sıralaması sağlar.

## <a name="apis"></a>API'ler

|Eşzamanlı sıra                |Güvenilir Eşzamanlı Kuyruk                                         |
|--------------------------------|------------------------------------------------------------------|
| void sıraya alma (T öğesi)           | Görev EnqueueAsync (ITransaction TX, T öğesi)                       |
| bool Trysıradan çıkarma (çıkan T sonucu)  | Görev< ConditionalValue < T > > TryDequeueAsync (ITransaction TX)  |
| int sayısı ()                    | uzun sayı ()                                                     |

## <a name="comparison-with-reliable-queue"></a>[Güvenilir sıra](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) ile karşılaştırma

Güvenilir bir eşzamanlı sıra, [güvenilir bir kuyruğa](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1)alternatif olarak sunulur. FıFO, eşzamanlılık içeren bir zorunluluğunu getirir gerektirdiğinden, kesin FıFO sıralaması gerekmediği durumlarda kullanılmalıdır.  [Güvenilir sıra](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) , en fazla bir işlem sıraya alma izni verilen en fazla bir işlem ve aynı anda sıraya alma IZNI verilen FIFO sıralamasını zorlamak için kilitleri kullanır. Karşılaştırma ' da, güvenilir eşzamanlı sıra sıralama kısıtlamasını sağlar ve her türlü sayıda eşzamanlı işlem sıraya alma ve sıradan çıkarma işlemlerine izin verir. En iyi çaba sıralaması sağlanır, ancak güvenilir bir eşzamanlı kuyruktaki iki değerin göreli sıralaması hiçbir şekilde garanti edilemez.

Güvenilir eşzamanlı sıra, en çok eşzamanlı işlem sırası ve/veya sıra sıraları gerçekleştirirken [güvenilir kuyruktan](/dotnet/api/microsoft.servicefabric.data.collections.ireliablequeue-1#microsoft_servicefabric_data_collections_ireliablequeue_1) daha yüksek aktarım hızı ve daha düşük gecikme süresi sağlar.

ReliableConcurrentQueue için bir örnek kullanım örneği, [Ileti sırası](https://en.wikipedia.org/wiki/Message_queue) senaryosudur. Bu senaryoda, bir veya daha fazla ileti üreticileri kuyruğa öğe oluşturup ekler ve bir veya daha fazla ileti tüketicileri kuyruktan ileti çeker ve bunları işler. Birden çok üreticileri ve tüketici, sırayı işlemek için eşzamanlı işlemler kullanarak bağımsız olarak çalışabilir.

## <a name="usage-guidelines"></a>Kullanım Yönergeleri
* Sıra, kuyruktaki öğelerin düşük saklama süresine sahip olmasını bekler. Diğer bir deyişle, öğeler uzun süredir kuyrukta kalmayabilir.
* Sıra, kesin FıFO sıralaması garantisi vermez.
* Sıra kendi yazma işlemlerini okumaz. Bir öğe bir işlem içinde sıraya alınmışsa, aynı işlem içindeki bir kuyruktan atılamaz.
* Dekuyruklar birbirinden yalıtılmaz. Öğe *a* Işlem *txna* öğesinde sıraya alınmışsa, yani *txna* yürütülmese de, öğe *a* , eşzamanlı bir işlem *txnb* olarak görünür olmaz.  *Txna* durdurulduğunda, *bir* , hemen *txnb* olarak görünür hale gelir.
* *Trtypeınfo ' zaman uyumsuz* davranışı, bir *Trydequeueasync* kullanılarak uygulanabilir ve sonra işlem iptal edilebilir. Bu davranışa bir örnek, programlama desenleri bölümünde bulunabilir.
* Sayı işlemsel değil. Kuyruktaki öğe sayısının bir fikrini almak için kullanılabilir, ancak bir noktayı temsil eder ve üzerinde güvenlenemez.
* İşlem etkin durumdayken, sistem üzerinde performans etkisi olabilecek uzun süreli işlemlere engel olmak için, kuyruğa alınmış öğeler üzerinde pahalı işlemler gerçekleştirilmemelidir.

## <a name="code-snippets"></a>Kod Parçacıkları
Birkaç kod parçacığı ve bunların beklenen çıktılarına bakmamıza izin verin. Özel durum işleme bu bölümde yok sayıldı.

### <a name="instantiation"></a>Örnek oluşturma
Güvenilir bir eşzamanlı kuyruğun bir örneğini oluşturmak, diğer güvenilir koleksiyona benzer.

```csharp
IReliableConcurrentQueue<int> queue = await this.StateManager.GetOrAddAsync<IReliableConcurrentQueue<int>>("myQueue");
```

### <a name="enqueueasync"></a>EnqueueAsync
Aşağıda, EnqueueAsync 'in ardından beklenen çıkışları tarafından kullanılması için birkaç kod parçacığı verilmiştir.

- *Durum 1: tek kuyruğa alma görevi*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}
```

Görevin başarıyla tamamlandığını ve sırayı değiştiren bir eşzamanlı işlem olmadığını varsayın. Kullanıcı kuyruğun aşağıdaki siparişlerin herhangi birinde bulunan öğeleri içermesini bekleyebilir:

> 10, 20
> 
> 20, 10


- *Durum 2: paralel sıraya alma görevi*

```
// Parallel Task 1
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 10, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 20, cancellationToken);

    await txn.CommitAsync();
}

// Parallel Task 2
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.EnqueueAsync(txn, 30, cancellationToken);
    await this.Queue.EnqueueAsync(txn, 40, cancellationToken);

    await txn.CommitAsync();
}
```

Görevlerin başarıyla tamamlandığını, görevlerin paralel olarak çalıştığını ve sırayı değiştiren başka bir eşzamanlı işlem olmadığını varsayın. Kuyruktaki öğelerin sıralaması hakkında çıkarımı yapılamaz. Bu kod parçacığı için öğeler 4 ' ün herhangi birinde görünebilir! olası sipariş ayarları.  Sıra, öğeleri özgün (sıraya alınmış) sırada tutmaya çalışır, ancak eşzamanlı işlemler veya hatalar nedeniyle bunları yeniden sıralamak zorunlu olabilir.


### <a name="dequeueasync"></a>DequeueAsync
Aşağıda, TryDequeueAsync kullanımına yönelik birkaç kod parçacığı ve ardından beklenen çıktılar verilmiştir. Kuyruğun kuyruktaki şu öğelerle zaten doldurulmuş olduğunu varsayın:
> 10, 20, 30, 40, 50, 60

- *Durum 1: tek bir sıradan çıkarma görevi*

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    await txn.CommitAsync();
}
```

Görevin başarıyla tamamlandığını ve sırayı değiştiren bir eşzamanlı işlem olmadığını varsayın. Kuyruktaki öğelerin sıralaması hakkında çıkarıcı bulunmadığından, öğelerin üçü herhangi bir sırada sıradan kaldırılabilir. Sıra, öğeleri özgün (sıraya alınmış) sırada tutmaya çalışır, ancak eşzamanlı işlemler veya hatalar nedeniyle bunları yeniden sıralamak zorunlu olabilir.  

- *Durum 2: paralel sıradan çıkarma görevi*

```
// Parallel Task 1
List<int> dequeue1;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue1.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}

// Parallel Task 2
List<int> dequeue2;
using (var txn = this.StateManager.CreateTransaction())
{
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;
    dequeue2.Add(await this.Queue.TryDequeueAsync(txn, cancellationToken)).val;

    await txn.CommitAsync();
}
```

Görevlerin başarıyla tamamlandığını, görevlerin paralel olarak çalıştığını ve sırayı değiştiren başka bir eşzamanlı işlem olmadığını varsayın. Kuyruktaki öğelerin sıralaması hakkında çıkarımı yapılmayacak olduğundan, *dequeue1* ve *dequeue2* listeleri her biri herhangi bir sırada iki öğe içerir.

Aynı öğe her iki listede *de görünmez.* Bu nedenle, dequeue1 *10*, *30* ise, dequeue2 *20*, *40* olur.

- *Durum 3: Işlem Iptali Ile sıralamayı sıradan çıkarma*

Uçuş dışı kuyrukla bir işlemi iptal etmek, öğeleri sıranın baş üzerine geri koyar. Sıranın baş üzerine maddelerin geri alındığı sıra garanti edilmez. Aşağıdaki koda bakmamıza izin verin:

```
using (var txn = this.StateManager.CreateTransaction())
{
    await this.Queue.TryDequeueAsync(txn, cancellationToken);
    await this.Queue.TryDequeueAsync(txn, cancellationToken);

    // Abort the transaction
    await txn.AbortAsync();
}
```
Öğelerin aşağıda belirtilen sırada kuyruğa atılmış olduğunu varsayın:
> 10, 20

İşlemi iptal ettiğimiz zaman, aşağıdaki siparişlerin herhangi birine ait öğeler sıranın baş bir kısmında geri eklenir:
> 10, 20
> 
> 20, 10

Aynı işlem, işlemin başarıyla *tamamlanmadığı* tüm durumlarda geçerlidir.

## <a name="programming-patterns"></a>Programlama desenleri
Bu bölümde, ReliableConcurrentQueue kullanımı yararlı olabilecek birkaç programlama desenlerine bakmamıza izin verin.

### <a name="batch-dequeues"></a>Toplu iş sıraları
Önerilen programlama deseninin her seferinde tek bir sıradan çıkarma gerçekleştirmek yerine, kendi sıralarını toplu olarak gerçekleştirmesi için önerilen bir programlama modelidir. Kullanıcı, her toplu iş veya toplu iş boyutu arasındaki gecikmeleri azaltmayı tercih edebilir. Aşağıdaki kod parçacığı, bu programlama modelini gösterir. Bu örnekte, işlem tamamlandıktan sonra işlemin yapıldığından, işleme sırasında bir hata oluşması durumunda işlenmemiş öğelerin işlenmeksizin kaybedilmesi gerekir.  Alternatif olarak, işlem işlemin kapsamı içinde yapılabilir, ancak performans üzerinde olumsuz bir etkiye sahip olabilir ve zaten işlenmiş olan öğelerin işlenmesini gerektirir.

```
int batchSize = 5;
long delayMs = 100;

while(!cancellationToken.IsCancellationRequested)
{
    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        for(int i = 0; i < batchSize; ++i)
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
            else
            {
                // else break the for loop
                break;
            }
        }

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }

    int delayFactor = batchSize - processItems.Count;
    await Task.Delay(TimeSpan.FromMilliseconds(delayMs * delayFactor), cancellationToken);
}
```

### <a name="best-effort-notification-based-processing"></a>Best-Effort Notification-Based Işleme
Diğer bir ilginç programlama deseninin Count API 'SI kullanılır. Burada, sıra için en iyi deneme bildirim tabanlı işleme uygulayabiliriz. Sıra sayısı, bir sıraya alma veya sıradan çıkarma görevini kısıtlamak için kullanılabilir.  Önceki örnekte olduğu gibi, işleme işlem dışında gerçekleşdiğinden, işlem sırasında bir hata oluşursa işlenmemiş öğeler kaybolabilir.

```
int threshold = 5;
long delayMs = 1000;

while(!cancellationToken.IsCancellationRequested)
{
    while (this.Queue.Count < threshold)
    {
        cancellationToken.ThrowIfCancellationRequested();

        // If the queue does not have the threshold number of items, delay the task and check again
        await Task.Delay(TimeSpan.FromMilliseconds(delayMs), cancellationToken);
    }

    // If there are approximately threshold number of items, try and process the queue

    // Buffer for dequeued items
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        ConditionalValue<int> ret;

        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if (ret.HasValue)
            {
                // If an item was dequeued, add to the buffer for processing
                processItems.Add(ret.Value);
            }
        } while (processItems.Count < threshold && ret.HasValue);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
}
```

### <a name="best-effort-drain"></a>Boşaltma Best-Effort
Veri yapısının eşzamanlı yapısı nedeniyle kuyruğun bir boşaltma garantisi olamaz.  Kuyruktaki hiçbir Kullanıcı işlemi uçuş aşamasında olsa bile, bir TryDequeueAsync öğesine yapılan belirli bir çağrı daha önce kuyruğa alınmış ve kaydedilmiş bir öğe döndürmeyebilir.  Sıraya alınan öğe, *sonunda* , bant dışı bir iletişim mekanizması olmadan sıradan olarak görünür hale gelmiştir, ancak tüm üreticileri durdurulduysa ve yeni bir sıraya alma işlemine izin verilmiyorsa, bağımsız bir tüketici kuyruğun kararlı duruma geldiğini bilmez. Bu nedenle, boşaltma işlemi aşağıda uygulanan en iyi çabadır.

Kullanıcı, tüm üretici ve tüketici görevlerini durdurmalı ve kuyruğu boşaltmaya çalışmadan önce, tüm uçuş işlemlerinin tamamlanmasını veya durdurulmasına izin vermez.  Kullanıcı kuyruktaki öğe sayısını biliyorsa, tüm öğelerin sıraya alınmış olduğunu işaret eden bir bildirim ayarlayabilir.

```
int numItemsDequeued;
int batchSize = 5;

ConditionalValue ret;

do
{
    List<int> processItems = new List<int>();

    using (var txn = this.StateManager.CreateTransaction())
    {
        do
        {
            ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);

            if(ret.HasValue)
            {
                // Buffer the dequeues
                processItems.Add(ret.Value);
            }
        } while (ret.HasValue && processItems.Count < batchSize);

        await txn.CommitAsync();
    }

    // Process the dequeues
    for (int i = 0; i < processItems.Count; ++i)
    {
        Console.WriteLine("Value : " + processItems[i]);
    }
} while (ret.HasValue);
```

### <a name="peek"></a>Göz At
ReliableConcurrentQueue, *Trtypeınfo Için zaman uyumsuz* API sağlamıyor. Kullanıcılar, bir *Trydequeueasync* kullanarak ve sonra işlemi iptal ederek göz atmayı alabilir. Bu örnekte, dequeues yalnızca öğenin değeri *10*' dan büyükse işlenir.

```
using (var txn = this.StateManager.CreateTransaction())
{
    ConditionalValue ret = await this.Queue.TryDequeueAsync(txn, cancellationToken);
    bool valueProcessed = false;

    if (ret.HasValue)
    {
        if (ret.Value > 10)
        {
            // Process the item
            Console.WriteLine("Value : " + ret.Value);
            valueProcessed = true;
        }
    }

    if (valueProcessed)
    {
        await txn.CommitAsync();    
    }
    else
    {
        await txn.AbortAsync();
    }
}
```

## <a name="must-read"></a>Okunmalıdır
* [Hızlı başlangıç Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Güvenilir Koleksiyonlar ile çalışma](service-fabric-work-with-reliable-collections.md)
* [Reliable Services bildirimleri](service-fabric-reliable-services-notifications.md)
* [Reliable Services yedekleme ve geri yükleme (olağanüstü durum kurtarma)](service-fabric-reliable-services-backup-restore.md)
* [Güvenilir durum Yöneticisi yapılandırması](service-fabric-reliable-services-configuration.md)
* [Service Fabric Web API hizmetlerini kullanmaya başlama](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services programlama modelinin gelişmiş kullanımı](./service-fabric-reliable-services-lifecycle.md)
* [Güvenilir koleksiyonlar için geliştirici başvurusu](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
