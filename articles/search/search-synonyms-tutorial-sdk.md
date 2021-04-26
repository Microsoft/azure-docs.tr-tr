---
title: Eş anlamlı C# örnekleri
titleSuffix: Azure Cognitive Search
description: Bu C# örneğinde, eş anlamlılar özelliğini Azure Bilişsel Arama içindeki bir dizine eklemeyi öğrenin. Eş anlamlı eşleme, eşdeğer terimlerin bir listesidir. Eş anlamlı olan alanlar, Kullanıcı tarafından sunulan terimi ve ilgili tüm eş anlamlıları içerecek şekilde sorguları genişletir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704665"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Örnek: C 'de Azure Bilişsel Arama için eş anlamlılar ekleme #

Eş anlamlılar, giriş terimine anlam bakımından eşdeğer olan terimlerle eşleşerek bir sorguyu genişletir. Örneğin, "araba" aramasının "otomobil" veya "araç" terimlerini içeren belgelerle eşleşmesini isteyebilirsiniz. 

Azure Bilişsel Arama 'de eş anlamlılar, eşdeğer terimleri ilişkilendiren *eşleme kuralları* aracılığıyla bir *eş anlamlı haritada* tanımlanmıştır. Bu örnek, var olan bir dizinle eş anlamlıları eklemek ve kullanmak için gerekli olan adımları içerir.

Bu örnekte, aşağıdakileri nasıl yapacağınızı öğreneceksiniz:

> [!div class="checklist"]
> * Eş anlamlı eşleme [sınıfını](/dotnet/api/azure.search.documents.indexes.models.synonymmap)kullanarak bir eşanlamlı eşlemesi oluşturun. 
> * Eş anlamlıları aracılığıyla sorgu genişletmeyi desteklemesi gereken alanlar üzerinde [eş anlamlılar özelliğini](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames) ayarlayın.

Eş anlamlı etkin bir alanı normal şekilde sorgulayabilirsiniz. Eş anlamlıya erişmek için ek sorgu söz dizimi gerekli değildir.

Birden çok eş anlamlı eşlemi oluşturabilir, bunları bir dizin için kullanılabilen hizmet genelinde kaynak olarak gönderebilir ve alan düzeyinde hangisinin kullanılacağını belirtebilirsiniz. Sorgu zamanında, bir dizin aramanın yanı sıra, sorguda kullanılan alanlarda bir tane belirtilmişse, Azure Bilişsel Arama bir eş anlamlı haritada arama yapar.

> [!NOTE]
> Eş anlamlılar, portalda değil, programlı bir şekilde oluşturulabilir.

## <a name="prerequisites"></a>Önkoşullar

Öğretici gereksinimleri şunları içerir:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Bilişsel Arama hizmeti](search-create-service-portal.md)
* [Azure.Search.Documtalar paketi](https://www.nuget.org/packages/Azure.Search.Documents/)

.NET istemci kitaplığı hakkında bilginiz yoksa bkz. [.net bilişsel arama Azure 'da kullanma](search-howto-dotnet-sdk.md).

## <a name="sample-code"></a>Örnek kod

Bu örnekte kullanılan örnek uygulamanın tam kaynak kodunu [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)' da bulabilirsiniz.

## <a name="overview"></a>Genel Bakış

Eş anlamlıların değerini göstermek için ve sonrasında sorguları kullanılır. Bu örnekte, örnek bir uygulama sorguları yürütür ve iki belgeyle doldurulmuş örnek bir "oteller" dizini üzerinde sonuçları döndürür. İlk olarak, uygulama, dizinde görünmeyen terimleri ve tümceleri kullanarak arama sorguları yürütür. İkinci olarak, kod eşanlamlı özelliğine izin verir, ardından aynı sorguları yeniden yayınlar, bu kez, eş anlamlı haritadaki eşleşmeler temelinde sonuçları döndürür. 

Aşağıdaki kod genel akışı gösterir.

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>"Öncesi" sorguları

`RunQueriesWithNonExistentTermsInIndex` içinde "beş yıldızlı", "internet" ve "ekonomik VE otel" ile arama sorguları gönderin.

"Beş yıldız" gibi tümcecik sorguları, tırnak işaretleri içine alınmalıdır ve istemcinizden bağlı olarak kaçış karakterleri de gerektirebilir.

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

İki dizinli belgeden hiçbiri terimleri içermemelidir, bu nedenle ilk `RunQueriesWithNonExistentTermsInIndex` :  **eşleşen belge yok**.

## <a name="enable-synonyms"></a>Eş anlamlıları etkinleştirme

"Önce" sorguları çalıştırılmadan sonra, örnek kod eş anlamlıları etkinleştirmesine izin vermez. Eş anlamlıların etkinleştirilmesi iki adımlı bir işlemdir. İlk olarak, eş anlamlı kuralları tanımlayın ve karşıya yükleyin. İkincisi, alanları kullanacak şekilde yapılandırın. İşlem, `UploadSynonyms` ve `EnableSynonymsInHotelsIndex` içinde ana hatlarıyla açıklanmıştır.

1. Arama hizmetinize bir eş anlamlı eşlemi ekleyin. `UploadSynonyms` içinde, 'desc-synonymmap' adlı eş anlamlı eşleminde dört kural tanımlanır ve hizmete yüklenir.

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. Dizin tanımında eş anlamlı eşlemini kullanacak aranabilir alanları yapılandırın. `AddSynonymMapsToFields` içinde, `SynonymMapNames` özelliği yeni yüklenen eş anlamlı eşleminin adına ayarlanarak `category` ve `tags` alanlarında eş anlamlılar etkinleştirilir.

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   Bir eş anlamlı eşlemi eklediğinizde, dizinin yeniden oluşturulması gerekmez. Hizmetinize bir eş anlamlı eşlemi ekleyebilir ve sonra herhangi bir dizindeki mevcut alan tanımlarını yeni eş anlamlı eşlemini kullanacak şekilde değiştirebilirsiniz. Yeni özniteliklerin eklenmesi, dizin kullanılabilirliğini etkilemez. Aynı durum, bir alan için eş anlamlıları devre dışı bırakırken de geçerlidir. `SynonymMapNames` özelliğini boş bir listeye ayarlayabilirsiniz.

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>"Sonrası" sorguları

Eş anlamlı eşlemi karşıya yüklendikten ve dizin, eş anlamlı eşlemini kullanacak şekilde güncelleştirildikten sonra, ikinci `RunQueriesWithNonExistentTermsInIndex` çağrısı aşağıdaki çıkışı verir:

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

İlk sorgu, `five star=>luxury` kuralından belgeyi bulur. İkinci sorgu, `internet,wifi` kullanarak aramayı genişletir, üçüncü sorgu ise eşleştikleri belgeleri bulmak için `hotel, motel` ve `economy,inexpensive=>budget` kullanır.

Eş anlamlıların eklenmesi, arama deneyimini tamamen değiştirir. Bu örnekte, dizinimizde bulunan belgeler ilgili olmasına rağmen özgün sorgular anlamlı sonuçlar döndüremedi. Eş anlamlıları etkinleştirerek, dizinde temel alınan verileri değiştirmeden dizini yaygın olarak kullanılan terimleri içerecek şekilde genişletebiliriz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir örnek sonrasında temizlemenin en hızlı yolu, Azure Bilişsel Arama hizmetini içeren kaynak grubunu silkullanmaktır. Kaynak grubunu silerek içindeki her şeyi kalıcı olarak silebilirsiniz. Portalda, kaynak grubu adı Azure Bilişsel Arama hizmeti 'nin genel bakış sayfaalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte, eşleme kurallarını oluşturup göndermek ve sonra bir sorguda eş anlamlı eşlemeyi çağırmak için C# kodundaki eş anlamlılar özelliği gösterilmiştir. [.NET SDK](/dotnet/api/overview/azure/search.documents-readme) ve [REST API](/rest/api/searchservice/) başvuru belgelerinde daha fazla bilgi bulabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama eş anlamlılar kullanma](search-synonyms.md)