---
title: Kaynaklara eş zamanlı yazma işlemlerini yönetme
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama dizinleri, Dizin oluşturucular, veri kaynakları, güncelleştirmeler veya silmeler üzerinde orta ölçekli çarpışmalardan kaçınmak için iyimser eşzamanlılık kullanın.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edfb2fe5cc37a00335ca7b5be851a88825b03eb1
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792210"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Azure Bilişsel Arama eşzamanlılık yönetimi

Dizinler ve veri kaynakları gibi Azure Bilişsel Arama kaynaklarını yönetirken kaynakların güvenli bir şekilde güncelleştirilmesi önemlidir. özellikle de, kaynaklar uygulamanızın farklı bileşenleriyle aynı anda erişilir. İki istemci eşzamanlı olarak bir kaynağı her zaman koordine etmeden güncelleştirdiğinde, yarış koşulları mümkündür. Bunu engellemek için Azure Bilişsel Arama, *iyimser eşzamanlılık modeli*sunar. Bir kaynakta kilit yok. Bunun yerine, yanlışlıkla üzerine yazılmalardan kaçınacak istekleri oluşturabilmeniz için kaynak sürümünü tanımlayan her kaynak için bir ETag vardır.

> [!Tip]
> [Örnek C# çözümde](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) kavramsal kod, eşzamanlılık denetiminin Azure bilişsel arama 'da nasıl çalıştığını açıklar. Kod, eşzamanlılık denetimini çağıran koşullar oluşturur. [Aşağıdaki kod parçasının](#samplecode) okunması büyük olasılıkla çoğu geliştirici için yeterlidir, ancak çalıştırmak istiyorsanız, hizmet adını ve bir yönetici API anahtarını eklemek için appSettings. JSON öğesini düzenleyin. `http://myservice.search.windows.net`hizmet URL 'SI verildiğinde, hizmet adı `myservice`.

## <a name="how-it-works"></a>Nasıl çalışır

İyimser eşzamanlılık, dizinlere, Dizin oluşturuculara, veri kaynaklarına ve eş eşleme kaynaklarına yazma API çağrılarında erişim koşulu denetimleri aracılığıyla uygulanır.

Tüm kaynakların, nesne sürümü bilgilerini sağlayan bir [*varlık etiketi (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag) vardır. Önce ETag 'i denetleyerek, kaynağın ETag 'i yerel kopyalarınızla eşleştirerek tipik bir iş akışında (alma, yerel olarak değiştirme, güncelleştirme) eşzamanlı güncelleştirmelerden kaçınabilirsiniz.

+ REST API, istek üst bilgisinde [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) kullanır.
+ .NET SDK bir accessCondition nesnesi aracılığıyla ETag 'i ayarlıyor, [IF-Match ayarlanıyor | Kaynakta IF-Match-None üst bilgisi](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) . [IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) öğesinden devralan herhangi bir nesne bir accesscondition nesnesine sahiptir.

Bir kaynağı her güncelleştirişinizde ETag, otomatik olarak değişir. Eşzamanlılık yönetimini uyguladığınızda, tek yapmanız gereken, uzak kaynağın istemcide değiştirdiğiniz kaynağın kopyasıyla aynı ETag 'e sahip olmasını gerektiren güncelleştirme isteğine bir ön koşul koyuyor. Eşzamanlı bir işlem uzak kaynağı zaten değiştiriyorsa ETag, önkoşullarla eşleşmez ve istek HTTP 412 ile başarısız olur. .NET SDK kullanıyorsanız, bu bildirim `IsAccessConditionFailed()` uzantısı yönteminin true döndüğü `CloudException` olarak yapılır.

> [!Note]
> Eşzamanlılık için yalnızca bir mekanizma vardır. Kaynak güncelleştirmeleri için hangi API 'nin kullanıldığı bağımsız olarak her zaman kullanılır.

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Kullanım örnekleri ve örnek kod

Aşağıdaki kod, anahtar güncelleştirme işlemlerine yönelik accessCondition denetimlerini gösterir:

+ Kaynak artık yoksa güncelleştirme başarısız olur
+ Kaynak sürümü değişirse güncelleştirme başarısız olur

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>[Dotnetetagsexplainer programından](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) örnek kod

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Cognitive Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Cognitive Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Tasarım stili

İyimser eşzamanlılık uygulamaya yönelik bir tasarım deseninin, erişim koşulu denetimini yeniden deneme, erişim koşulu için test ve isteğe bağlı olarak değişiklikleri yeniden uygulamaya çalışmadan önce güncelleştirilmiş bir kaynağı alan bir döngüsü içermesi gerekir.

Bu kod parçacığı, zaten var olan bir dizine eş bir Eşeşleme eklenmesini gösterir. Bu kod, [Azure bilişsel arama Için C# eş anlamlı örneğidir](search-synonyms-tutorial-sdk.md).

Kod parçacığı, "oteller" dizinini alır, bir güncelleştirme işleminde nesne sürümünü denetler, koşul başarısız olursa bir özel durum oluşturur ve sonra en son sürümü almak için sunucudan Dizin almayla başlayarak işlemi (en fazla üç kez) yeniden dener.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Sonraki adımlar

Var olan bir dizinin güvenli bir şekilde güncelleştirilmesi hakkında daha fazla bağlam için [eş anlamlı C# örneği](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) inceleyin.

ETags veya AccessCondition nesnelerini içerecek şekilde aşağıdaki örneklerden birini değiştirmeyi deneyin.

+ [GitHub üzerinde REST API örneği](https://github.com/Azure-Samples/search-rest-api-getting-started)
+ [GitHub 'da .NET SDK örneği](https://github.com/Azure-Samples/search-dotnet-getting-started). Bu çözüm, bu makalede sunulan kodu içeren "DotNetEtagsExplainer" projesini içerir.

## <a name="see-also"></a>Ayrıca bkz.

[Genel http istek ve yanıt üst bilgileri](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
[HTTP durum kodları](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)
[Dizin işlemleri (REST API)](https://docs.microsoft.com/rest/api/searchservice/index-operations)
