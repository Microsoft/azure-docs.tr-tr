---
title: Kısıtlanan istekler için yönergeler
description: Azure Kaynak Grafiği tarafından daraltılan isteklerin önüne geçmek için, paralel olarak Grup, Stagger, sayfal ve sorgu yapmayı öğrenin.
ms.date: 04/09/2021
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 891d5951670dd6022b66ae2936ee855f73f8b33a
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107283550"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure Kaynak grafiğinde kısıtlanmış isteklere yönelik kılavuz

Azure Kaynak Grafiği verilerinin programlı ve sık kullanımını oluştururken, azaltma işlemlerinin sorguların sonuçlarını nasıl etkilediğini göz önünde bulundurmanız gerekir. Verilerin istendiği şekilde değiştirilmesi, sizin ve kuruluşunuzun daralmasını ve Azure kaynaklarınızla ilgili zamanında verilerin akışını sürdürmenize yardımcı olabilir.

Bu makalede, Azure Kaynak Grafında sorguların oluşturulmasıyla ilgili dört alan ve desen ele alınmaktadır:

- Azaltma üst bilgilerini anlama
- Sorguları gruplandırma
- Sorguları kademelendirme
- Sayfalandırmaya etkisi

## <a name="understand-throttling-headers"></a>Azaltma üst bilgilerini anlama

Azure Kaynak Grafiği, bir zaman penceresine göre her bir kullanıcı için bir kota numarası ayırır. Örneğin, bir Kullanıcı, daralmadan 5 saniyelik her pencerede en fazla 15 sorgu gönderebilir. Kota değeri birçok faktöre göre belirlenir ve değişikliğe tabidir.

Her sorgu yanıtında, Azure Kaynak Grafiği iki daraltma üst bilgisi ekler:

- `x-ms-user-quota-remaining` (int): Kullanıcı için kalan Kaynak kotası. Bu değer sorgu sayısı ile eşlenir.
- `x-ms-user-quota-resets-after` (SS: DD: SS): kullanıcının kota tüketimi sıfırlanana kadar geçen süre.

Bir güvenlik sorumlusu, kiracı veya yönetim grubu [sorgu kapsamı](./query-language.md#query-scope)içinde 5000 ' den fazla aboneliğe erişebilmediğinde, yanıt ilk 5000 abonelikle sınırlıdır ve `x-ms-tenant-subscription-limit-hit` üst bilgi olarak döndürülür `true` .

Üst bilgilerin nasıl çalıştığını görmek için, ve değerlerini içeren bir sorgu yanıtına göz atalım `x-ms-user-quota-remaining: 10` `x-ms-user-quota-resets-after: 00:00:03` .

- Sonraki 3 saniye içinde, en fazla 10 sorgu kısıtlanmadan gönderilebilir.
- 3 saniye içinde, `x-ms-user-quota-remaining` ve değerleri `x-ms-user-quota-resets-after` `15` `00:00:05` sırasıyla ve sırasıyla sıfırlanacak.

Sorgu isteklerinde _geri_ dönmek üzere üst bilgileri kullanmanın bir örneğini görmek için bkz. [sorgudaki örnek paralel](#query-in-parallel).

## <a name="grouping-queries"></a>Sorguları gruplandırma

Sorguları aboneliğe, kaynak grubuna veya tek tek kaynağa göre gruplandırma, paralelleştirme sorgularıyla daha etkilidir. Daha büyük bir sorgunun kota maliyeti genellikle birçok küçük ve hedeflenen sorgunun kota maliyetinden düşüktür. Grup boyutunun _300_' den küçük olması önerilir.

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

- En iyi duruma getirilmiş gruplama yaklaşımının örnek #1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Tek bir sorguda birden çok kaynak almak için en iyi duruma getirilmiş gruplama yaklaşımının örnek #2

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Sorguları kademelendirme

Kısıtlama zorlandığı için sorguların kademeli olmasını öneririz. Diğer bir deyişle, aynı anda 60 sorgu göndermek yerine, sorguları 4 5 saniyelik pencereler halinde yeniden dağıtın:

- Aşamalı olmayan sorgu zamanlaması

  | Sorgu sayısı         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Zaman aralığı (sn) | 0-5 | 5-10 | 10-15 | 15-20 |

- Aşamalı sorgu zamanlaması

  | Sorgu sayısı         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Zaman aralığı (sn) | 0-5 | 5-10 | 10-15 | 15-20 |

Azure Kaynak Grafını sorgularken azaltma üst bilgilerini önceden oluşturma hakkında bir örnek aşağıda verilmiştir:

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

Gruplandırmanın paralelleştirme üzerinde kullanılması önerilse de, sorguların kolayca gruplanamamasının zaman vardır. Bu durumlarda, paralel bir biçimde birden fazla sorgu göndererek Azure Kaynak grafiğini sorgulamak isteyebilirsiniz. Bu tür senaryolarda azaltma üst bilgilerine göre _geri_ alma hakkında bir örnek aşağıda verilmiştir:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

  Azure CLı veya Azure PowerShell kullanırken, Azure Kaynak grafiğine yönelik sorgular, en fazla 5000 girişi getirecek şekilde otomatik olarak sayfalandırılır. Sorgu sonuçları, tüm sayfalandırılmış çağrılardaki girişlerin birleştirilmiş bir listesini döndürür. Bu durumda, sorgu sonucundaki giriş sayısına bağlı olarak, tek bir sayfalandırılmış sorgu birden fazla sorgu kotası tüketebilir. Örneğin, aşağıdaki örneklerde sorgunun tek bir çalıştırması en fazla beş sorgu kotası tüketebilir:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Hala kısıtlanıyor musunuz?

Yukarıdaki önerileri kullandıktan sonra daraldıysanız [Azure Kaynak Grafiği ekibine](mailto:resourcegraphsupport@microsoft.com)başvurun.

Şu ayrıntıları sağlayın:

- Özel kullanım örneği ve iş sürücünüzün daha yüksek bir azaltma sınırı olması gerekir.
- Kaç kaynak erişiminiz var? Tek bir sorgudan kaç tane döndürüldü?
- Hangi kaynak türlerini ilgileniyorsunuz?
- Sorgu deseninin anlamı nedir? Y saniye başına X sorgusu ve bu şekilde devam eder.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Başlangıç sorgularında](../samples/starter.md)kullanılan dil.
- Gelişmiş [sorgularda](../samples/advanced.md)gelişmiş kullanımlar bölümüne bakın.
- [Kaynakları araştırma](explore-resources.md)hakkında daha fazla bilgi edinin.