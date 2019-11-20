---
title: Özel sağlayıcılar için yeniden bir uç nokta yazar
description: Bu öğreticide, özel sağlayıcılar için bir yeniden deneme uç noktasının nasıl yazılacağı gösterilmektedir. Desteklenen yeniden deneme HTTP yöntemlerine yönelik isteklerin ve yanıtların nasıl işleneceğini ayrıntılarıyla açıklamaktadır.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: ae821f07034b038f49a400de8c00e4ace6787192
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172889"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>Özel sağlayıcılar için yeniden bir uç nokta yazar

Özel sağlayıcı, Azure ile uç nokta arasında bir sözleşmedir. Özel sağlayıcılar sayesinde, Azure 'da iş akışlarını özelleştirebilirsiniz. Bu öğreticide, özel bir sağlayıcının nasıl tekrar uç noktası yazılacağı gösterilmektedir. Azure özel sağlayıcıları hakkında bilginiz varsa bkz. [özel kaynak sağlayıcılarına genel bakış](./custom-providers-overview.md).

> [!NOTE]
> Bu öğretici, [Azure özel sağlayıcıları Için Azure Işlevleri ayarlama](./tutorial-custom-providers-function-setup.md)öğreticisinde oluşturulur. Bu öğreticideki bazı adımlar, yalnızca bir Azure işlev uygulaması özel sağlayıcılarla çalışmak üzere ayarlandıysa çalışır.

## <a name="work-with-custom-actions-and-custom-resources"></a>Özel eylemlerle ve özel kaynaklarla çalışma

Bu öğreticide, işlev uygulamasını özel sağlayıcınız için bir yeniden deneme uç noktası olarak çalışacak şekilde güncelleşolursunuz. Azure 'daki kaynaklar ve eylemler aşağıdaki temel yeniden oluşturma belirtiminden sonra modellenmiştir:

- **YERLEŞTİR**: Yeni kaynak oluştur
- **Get (örnek)** : Var olan bir kaynağı alma
- **SİL**: Var olan bir kaynağı kaldır
- **GÖNDERİ**: Bir eylem tetikleyin
- **Get (koleksiyon)** : Tüm mevcut kaynakları Listele

 Bu öğretici için Azure Tablo Depolamayı kullanırsınız. Ancak, herhangi bir veritabanı veya depolama hizmeti çalışabilir.

## <a name="partition-custom-resources-in-storage"></a>Depolamadaki özel kaynakları bölümle

Yeniden oluşturulmuş bir hizmet oluştururken oluşturulan kaynakları depolamanız gerekir. Azure Tablo depolaması için verileriniz için bölüm ve satır anahtarları oluşturmanız gerekir. Özel sağlayıcılar için, verilerin özel sağlayıcıya bölümlenmesi gerekir. Özel sağlayıcıya gelen bir istek gönderildiğinde, özel sağlayıcı `x-ms-customproviders-requestpath` üst bilgiyi giden istekleri uç noktaya ekler.

Aşağıdaki örnek, özel bir `x-ms-customproviders-requestpath` kaynak için bir üst bilgi gösterir:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

Örneğin `x-ms-customproviders-requestpath` başlığına bağlı olarak, aşağıdaki tabloda gösterildiği gibi, depolama için *partitionkey* ve *rowkey* parametrelerini oluşturabilirsiniz:

Parametre | Şablon | Açıklama
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | *Partitionkey* parametresi, verilerin nasıl bölümlenmiş olduğunu belirtir. Genellikle veriler özel sağlayıcı örneği tarafından bölümlenir.
*rowKey* | `{myResourceType}:{myResourceName}` | *Rowkey* parametresi, veriler için bireysel tanımlayıcıyı belirtir. Genellikle tanımlayıcı, kaynağın adıdır.

Özel kaynağınızı modellemek için de yeni bir sınıf oluşturmanız gerekir. Bu öğreticide, aşağıdaki **Customresource** sınıfını işlev uygulamanıza eklersiniz:

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**Customresource** , herhangi bir giriş verisini kabul eden basit, genel bir sınıftır. Veri depolamak için kullanılan **Tableentity**tabanlıdır. **Customresource** sınıfı **tableentity**: **partitionkey** ve **rowkey**öğesinden iki özelliği devralır.

## <a name="support-custom-provider-restful-methods"></a>Özel sağlayıcı yeniden deneme yöntemlerini destekleme

> [!NOTE]
> Kodu doğrudan bu öğreticiden kopyalamadıysanız, yanıt içeriği `Content-Type` `application/json`üstbilgiyi ayarlayan geçerli bir JSON olmalıdır.

Veri bölümlemesini ayarladığınıza göre, özel kaynaklar ve özel eylemler için temel CRUD ve tetikleme yöntemlerini oluşturun. Özel sağlayıcılar proxy olarak davrandığı için, Restize uç noktasının istek ve yanıtı modellemek ve işlemesi gerekir. Aşağıdaki kod parçacıkları, temel yeniden gerçekleştirilen işlemlerin nasıl işleneceğini gösterir.

### <a name="trigger-a-custom-action"></a>Özel bir eylem tetikleyin

Özel sağlayıcılar için, POST istekleri aracılığıyla özel bir eylem tetiklenir. Özel bir eylem, isteğe bağlı olarak bir giriş parametreleri kümesi içeren bir istek gövdesini kabul edebilir. Eylem daha sonra eylemin sonucunu ve başarılı veya başarısız olup olmadığını belirten bir yanıt döndürür.

Aşağıdaki **Triggercustomaction** yöntemini işlev uygulamanıza ekleyin:

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

**Triggercustomaction** yöntemi gelen bir isteği kabul eder ve yanıtı bir durum kodu ile geri doğru yankılar.

### <a name="create-a-custom-resource"></a>Özel bir kaynak oluşturun

Özel sağlayıcılar için, PUT istekleri aracılığıyla özel bir kaynak oluşturulur. Özel sağlayıcı, özel kaynak için bir özellikler kümesi içeren bir JSON istek gövdesini kabul eder. Azure 'daki kaynaklar, bir yeniden modellidir. Kaynak oluşturmak, almak veya silmek için aynı istek URL 'sini kullanabilirsiniz.

Yeni kaynaklar oluşturmak için aşağıdaki **Createcustomresource** metodunu ekleyin:

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

**Createcustomresource** yöntemi, gelen isteği Azure 'a özgü alanların **kimliğini**, **adını**ve **türünü**içerecek şekilde güncelleştirir. Bu alanlar, Azure genelinde Hizmetler tarafından kullanılan en üst düzey özelliklerdir. Özel sağlayıcının Azure Ilkesi, Azure Resource Manager şablonları ve Azure etkinlik günlüğü gibi diğer hizmetlerle birlikte kullanmasına izin verir.

Özellik | Örnek | Açıklama
---|---|---
**name** | {myCustomResourceName} | Özel kaynağın adı
**type** | Microsoft. CustomProviders/resourceProviders/{resourceTypeName} | Kaynak türü ad alanı
**id** | /Subscriptions/{SubscriptionID}/ResourceGroups/{resourcegroupname}/<br>sağlayıcılar/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | Kaynak KIMLIĞI

Özellikleri eklemenin yanı sıra, JSON belgesini Azure Tablo depolama alanına da kaydettiniz.

### <a name="retrieve-a-custom-resource"></a>Özel bir kaynak alma

Özel sağlayıcılar için, GET istekleri aracılığıyla özel bir kaynak alınır. Özel sağlayıcı bir JSON istek gövdesini kabul *etmez* . GET istekleri için uç nokta, önceden oluşturulmuş `x-ms-customproviders-requestpath` kaynağı döndürmek için üst bilgisini kullanır.

Mevcut kaynakları almak için aşağıdaki **RetrieveCustomResource** yöntemini ekleyin:

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Azure 'da kaynaklar, yeniden bir model izler. Kaynak oluşturan istek URL 'SI, bir GET isteği gerçekleştirilirse kaynağı da döndürür.

### <a name="remove-a-custom-resource"></a>Özel bir kaynağı kaldırma

Özel sağlayıcılar için, SILME istekleri aracılığıyla özel bir kaynak kaldırılır. Özel sağlayıcı bir JSON istek gövdesini kabul *etmez* . Bir silme isteği için uç nokta, zaten oluşturulan `x-ms-customproviders-requestpath` kaynağı silmek için üst bilgisini kullanır.

Mevcut kaynakları kaldırmak için aşağıdaki **Removeccustomresource** metodunu ekleyin:

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Azure 'da kaynaklar, yeniden bir model izler. Bir kaynak oluşturan istek URL 'SI, SILME isteği gerçekleştirilirse kaynağı da siler.

### <a name="list-all-custom-resources"></a>Tüm özel kaynakları Listele

Özel sağlayıcılar için, koleksiyon GET isteklerini kullanarak var olan özel kaynakların bir listesini sıralayabilirsiniz. Özel sağlayıcı bir JSON istek gövdesini kabul *etmez* . Bir get istekleri koleksiyonu için, uç nokta, önceden oluşturulmuş `x-ms-customproviders-requestpath` kaynakları numaralandırmak üzere üst bilgisini kullanır.

Mevcut kaynakları numaralandırmak için aşağıdaki **Enumerateallcustomresources** metodunu ekleyin:

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey Querykarşılaştırmaları. GreaterThan ve Querykarşılaştırmaları. LessThan dizeler için bir "StartsWith" sorgusu gerçekleştirmeye yönelik Azure Tablo depolama söz dizeleridir.

Tüm mevcut kaynakları listelemek için, özel sağlayıcı bölümünüzün altında kaynakların mevcut olmasını sağlayan bir Azure Tablo depolama sorgusu oluşturun. Sorgu daha sonra satır anahtarının aynı `{myResourceType}` değerle başlayacağını denetler.

## <a name="integrate-restful-operations"></a>Yeniden takip eden işlemleri tümleştirin

İşlev uygulamasına tüm yeniden yapılan Yöntemler eklendikten sonra, farklı REST isteklerini işlemek için işlevleri çağıran ana **çalıştırma** yöntemini güncelleştirin:

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

Güncelleştirilmiş **Run** yöntemi artık Azure Tablo Depolaması Için eklediğiniz *TableStorage* giriş bağlamasını içerir. Yöntemin ilk bölümü `x-ms-customproviders-requestpath` üstbilgiyi okur ve bir kaynak kimliği olarak değeri ayrıştırmak `Microsoft.Azure.Management.ResourceManager.Fluent` için kitaplığı kullanır. `x-ms-customproviders-requestpath` Üst bilgi özel sağlayıcı tarafından gönderilir ve gelen isteğin yolunu belirtir.

Ayrıştırılmış kaynak KIMLIĞI ' ni kullanarak, özel kaynakları aramak veya depolamak üzere veri için **partitionkey** ve **rowkey** değerlerini oluşturabilirsiniz.

Yöntemleri ve sınıfları ekledikten sonra, işlev uygulaması için **using** yöntemlerini güncelleştirmeniz gerekir. Aşağıdaki kodu C# dosyanın en üstüne ekleyin:

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

Bu öğreticinin herhangi bir noktasında kayıp alırsanız, tam kod örneğini [özel sağlayıcı C# yeniden deneme uç noktası başvurusunda](./reference-custom-providers-csharp-endpoint.md)bulabilirsiniz. İşlev uygulamasını tamamladıktan sonra, işlev uygulaması URL 'sini kaydedin. Daha sonraki öğreticilerde işlev uygulamasını tetiklemek için kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir Azure özel sağlayıcı uç noktasıyla çalışmak için bir yeniden deneme uç noktası yazıldı. Özel bir sağlayıcı oluşturmayı öğrenmek için, makale [öğreticisine gidin: Özel bir sağlayıcı](./tutorial-custom-providers-create.md)oluşturma.
