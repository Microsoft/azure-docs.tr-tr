---
title: Kısıtlanan istekler için yönergeler
description: Azure Kaynak Grafının kısıtlanmasını önlemek için daha iyi sorgular oluşturmayı öğrenin.
ms.date: 10/18/2019
ms.topic: conceptual
ms.openlocfilehash: 651a5daa9e7e19a5dc157ba0cfa17da2c8abe3db
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038321"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Kaynak grafiğinde kısıtlanmış isteklere yönelik kılavuz

Azure Kaynak Grafiği verilerinin programlı ve sık kullanımını oluştururken, azaltma işlemlerinin sorguların sonuçlarını nasıl etkilediğini göz önünde bulundurmanız gerekir. Verilerin istendiği şekilde değiştirilmesi, sizin ve kuruluşunuzun daralmasını ve Azure kaynaklarınızla ilgili zamanında verilerin akışını sürdürmenize yardımcı olabilir.

Bu makalede, Azure Kaynak Grafında sorguların oluşturulmasıyla ilgili dört alan ve desen ele alınmaktadır:

- Azaltma üst bilgilerini anlama
- Sorguları toplu işleme
- Kademelendirme sorguları
- Sayfalandırmaya etkisi

## <a name="understand-throttling-headers"></a>Azaltma üst bilgilerini anlama

Azure Kaynak Grafiği, her bir kullanıcı için kota numarasını bir zaman penceresine göre ayırır. Örneğin, bir Kullanıcı, daralmadan 5 saniyelik her pencerede en fazla 15 sorgu gönderebilir. Kota değeri birçok faktöre göre belirlenir ve değişikliğe tabidir.

Her sorgu yanıtında, Azure Kaynak Grafiği iki daraltma üst bilgisi ekler:

- `x-ms-user-quota-remaining` (int): Kullanıcı için kalan Kaynak kotası. Bu değer sorgu sayısı ile eşlenir.
- `x-ms-user-quota-resets-after` (SS: DD: SS): kullanıcının kota tüketimi sıfırlanana kadar geçen süre.

Üst bilgilerin nasıl çalıştığını görmek için, `x-ms-user-quota-remaining: 10` ve `x-ms-user-quota-resets-after: 00:00:03`üst bilgi ve değerlerini içeren bir sorgu yanıtına bakalım.

- Sonraki 3 saniye içinde, en fazla 10 sorgu kısıtlanmadan gönderilebilir.
- 3 saniye içinde `x-ms-user-quota-remaining` ve `x-ms-user-quota-resets-after` değerleri sırasıyla `15` ve `00:00:05` sıfırlanacak.

Sorgu isteklerinde _geri_ dönmek üzere üst bilgileri kullanmanın bir örneğini görmek için bkz. [sorgudaki örnek paralel](#query-in-parallel).

## <a name="batching-queries"></a>Sorguları toplu işleme

Bir aboneliğe, kaynak grubuna veya tek tek kaynağa göre sorguları toplu hale getirme, paralelleştirme sorgularıyla daha etkilidir. Daha büyük bir sorgunun kota maliyeti genellikle birçok küçük ve hedeflenen sorgunun kota maliyetinden düşüktür. Toplu iş boyutunun _300_' den küçük olması önerilir.

- Kötü iyileştirilmiş bir yaklaşım örneği

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- İyileştirilmiş bir toplu işlem yaklaşımının örnek #1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- İyileştirilmiş bir toplu işlem yaklaşımının örnek #2

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Kademelendirme sorguları

Kısıtlama zorlandığı için sorguların kademeli olmasını öneririz. Diğer bir deyişle, aynı anda 60 sorgu göndermek yerine sorguları dört saniyelik bir Windows 'a ayırır:

- Aşamalı olmayan sorgu zamanlaması

  | Sorgu sayısı         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Zaman aralığı (sn) | 0-5 | 5-10 | 10-15 | 15-20 |

- Aşamalı sorgu zamanlaması

  | Sorgu sayısı         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Zaman aralığı (sn) | 0-5 | 5-10 | 10-15 | 15-20 |

Aşağıda, Azure Kaynak grafı sorgulanırken azaltma üst bilgilerinin önceden belirtilme örneği verilmiştir:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Paralel olarak sorgula

Toplu işlemenin paralelleştirme üzerinde kullanılması önerilse de, sorguların kolayca toplu olarak oluşturamamasına neden olur. Bu durumlarda, paralel bir biçimde birden fazla sorgu göndererek Azure Kaynak grafiğini sorgulamak isteyebilirsiniz. Aşağıda, bu senaryolarda azaltma üst bilgilerine göre nasıl _geri_ alınacağını gösteren bir örnek verilmiştir:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Sayfalandırma

Azure Kaynak Graph tek bir sorgu yanıtında en fazla 1000 girişi döndürdüğünden, Aradığınız veri kümesini almak için sorgularınızın [sayfalanmasını](./work-with-data.md#paging-results) yapmanız gerekebilir. Ancak, bazı Azure Kaynak grafik istemcileri sayfalandırmayı diğerlerinden farklı işler.

- C# SDK’sı

  ResourceGraph SDK kullanırken, önceki sorgu yanıtından döndürülen atlama belirtecini sonraki sayfalandırılmış sorguya geçirerek sayfalandırma yapmanız gerekir. Bu tasarım, tüm sayfalandırılmış çağrılardan sonuçları toplamanız ve bunları sonda bir araya getirmek için ihtiyacınız olduğu anlamına gelir. Bu durumda, oluşturduğunuz her sayfalandırılmış sorgu bir sorgu kotası alır:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLı/Azure PowerShell

  Azure CLı veya Azure PowerShell kullanırken, Azure Kaynak grafiğine yönelik sorgular, en fazla 5000 girişi getirecek şekilde otomatik olarak sayfalandırılır. Sorgu sonuçları, tüm sayfalandırılmış çağrılardaki girişlerin birleştirilmiş bir listesini döndürür. Bu durumda, sorgu sonucundaki giriş sayısına bağlı olarak, tek bir sayfalandırılmış sorgu birden fazla sorgu kotası tüketebilir. Örneğin, aşağıdaki örnekte, bir sorgunun tek bir çalıştırması en fazla beş sorgu kotası tüketebilir:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Hala kısıtlanıyor musunuz?

Yukarıdaki önerileri kullandıktan sonra azaldıysanız, [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)' de ekibe başvurun.

Şu ayrıntıları sağlayın:

- Özel kullanım örneği ve iş sürücünüzün daha yüksek bir azaltma sınırı olması gerekir.
- Kaç kaynak erişiminiz var? Tek bir sorgudan kaç tane döndürüldü?
- Hangi kaynak türlerini ilgileniyorsunuz?
- Sorgu deseninin anlamı nedir? Y saniye başına X sorgusu vb.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Başlangıç sorgularında](../samples/starter.md)kullanılan dil.
- Gelişmiş [sorgularda](../samples/advanced.md)gelişmiş kullanımlar bölümüne bakın.
- [Kaynakları araştırma](explore-resources.md)hakkında daha fazla bilgi edinin.