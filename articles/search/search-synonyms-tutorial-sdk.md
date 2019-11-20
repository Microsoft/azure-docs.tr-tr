---
title: Eş C# anlamlı örnek
titleSuffix: Azure Cognitive Search
description: Bu C# örnekte, eş anlamlılar özelliğini Azure bilişsel arama içindeki bir dizine eklemeyi öğrenin. Eş anlamlı eşleme, eşdeğer terimlerin bir listesidir. Eş anlamlı olan alanlar, Kullanıcı tarafından sunulan terimi ve ilgili tüm eş anlamlıları içerecek şekilde sorguları genişletir.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8cc085fd27004928babd7df305a4452d1b068f6e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794244"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>Örnek: içinde Azure Bilişsel Arama için eş anlamlılar ekleyinC#

Eş anlamlılar, giriş terimine anlam bakımından eşdeğer olan terimlerle eşleşerek bir sorguyu genişletir. Örneğin, "araba" aramasının "otomobil" veya "araç" terimlerini içeren belgelerle eşleşmesini isteyebilirsiniz. 

Azure Bilişsel Arama 'de eş anlamlılar, eşdeğer terimleri ilişkilendiren *eşleme kuralları* aracılığıyla bir *eş anlamlı haritada*tanımlanmıştır. Bu örnek, var olan bir dizinle eş anlamlıları eklemek ve kullanmak için gerekli olan adımları içerir. Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * [Eş anlamlı eşleme sınıfını kullanarak](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) bir eşanlamlı eşlemesi oluşturun. 
> * Eş anlamlıları aracılığıyla sorgu genişletmeyi desteklemesi gereken alanlarda [eş](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) anlamlılar özelliğini ayarlayın.

Eş anlamlı etkin bir alanı normal şekilde sorgulayabilirsiniz. Eş anlamlıya erişmek için ek sorgu söz dizimi gerekli değildir.

Birden çok eş anlamlı eşlemi oluşturabilir, bunları bir dizin için kullanılabilen hizmet genelinde kaynak olarak gönderebilir ve alan düzeyinde hangisinin kullanılacağını belirtebilirsiniz. Sorgu zamanında, bir dizin aramanın yanı sıra, sorguda kullanılan alanlarda bir tane belirtilmişse, Azure Bilişsel Arama bir eş anlamlı haritada arama yapar.

> [!NOTE]
> Eş anlamlılar, portalda değil, programlı bir şekilde oluşturulabilir. Eş anlamlılar için Azure portalı desteği sizin için kullanışlı olacaksa, lütfen [UserVoice](https://feedback.azure.com/forums/263029-azure-search)’te geri bildiriminizi sağlayın

## <a name="prerequisites"></a>Önkoşullar

Öğretici gereksinimleri şunları içerir:

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Bilişsel Arama hizmeti](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET kitaplığı](https://aka.ms/search-sdk)
* [.NET uygulamasından Azure Bilişsel Arama kullanma](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>Genel Bakış

Öncesi ve sonrası sorguları, eş anlamlıların değerini gösterir. Bu örnekte, sorguları yürüten ve sonuçları örnek bir dizin üzerinde döndüren örnek bir uygulama kullanın. Örnek uygulama, iki belgeyle doldurulmuş "oteller" adlı küçük bir dizin oluşturur. Uygulama, dizinde görünmeyen terim ve ifadeleri kullanarak arama sorguları yürütür, eş anlamlılar özelliğini etkinleştirir, ardından aynı aramaları tekrar gerçekleştirir. Aşağıdaki kod genel akışı gösterir.

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
Örnek dizini oluşturma ve doldurma adımları [bir .NET uygulamasından Azure bilişsel arama kullanma](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)bölümünde açıklanmıştır.

## <a name="before-queries"></a>"Öncesi" sorguları

`RunQueriesWithNonExistentTermsInIndex` içinde "beş yıldızlı", "internet" ve "ekonomik VE otel" ile arama sorguları gönderin.
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
Dizini oluşturulan iki belgenin hiçbiri terimleri içermediği için, ilk `RunQueriesWithNonExistentTermsInIndex` için aşağıdaki çıkış alınır.
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>Eş anlamlıları etkinleştirme

Eş anlamlıların etkinleştirilmesi iki adımlı bir işlemdir. İlk olarak eş anlamlı kuralları tanımlanıp karşıya yüklenir, ardından bunları kullanacak alanlar yapılandırılır. İşlem, `UploadSynonyms` ve `EnableSynonymsInHotelsIndex` içinde ana hatlarıyla açıklanmıştır.

1. Arama hizmetinize bir eş anlamlı eşlemi ekleyin. `UploadSynonyms` içinde, 'desc-synonymmap' adlı eş anlamlı eşleminde dört kural tanımlanır ve hizmete yüklenir.
   ```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
   ```
   Bir eş anlamlı eşlemi, açık kaynak standart `solr` biçimine uygun olmalıdır. Biçim, Bölüm `Apache Solr synonym format`altında [Azure bilişsel arama eş anlamlılar](search-synonyms.md) olarak açıklanmaktadır.

2. Dizin tanımında eş anlamlı eşlemini kullanacak aranabilir alanları yapılandırın. `EnableSynonymsInHotelsIndex` içinde, `synonymMaps` özelliği yeni yüklenen eş anlamlı eşleminin adına ayarlanarak `category` ve `tags` alanlarında eş anlamlılar etkinleştirilir.
   ```csharp
   Index index = serviceClient.Indexes.Get("hotels");
   index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
   index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

   serviceClient.Indexes.CreateOrUpdate(index);
   ```
   Bir eş anlamlı eşlemi eklediğinizde, dizinin yeniden oluşturulması gerekmez. Hizmetinize bir eş anlamlı eşlemi ekleyebilir ve sonra herhangi bir dizindeki mevcut alan tanımlarını yeni eş anlamlı eşlemini kullanacak şekilde değiştirebilirsiniz. Yeni özniteliklerin eklenmesi, dizin kullanılabilirliğini etkilemez. Aynı durum, bir alan için eş anlamlıları devre dışı bırakırken de geçerlidir. `synonymMaps` özelliğini boş bir listeye ayarlayabilirsiniz.
   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
   ```

## <a name="after-queries"></a>"Sonrası" sorguları

Eş anlamlı eşlemi karşıya yüklendikten ve dizin, eş anlamlı eşlemini kullanacak şekilde güncelleştirildikten sonra, ikinci `RunQueriesWithNonExistentTermsInIndex` çağrısı aşağıdaki çıkışı verir:

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
İlk sorgu, `five star=>luxury` kuralından belgeyi bulur. İkinci sorgu, `internet,wifi` kullanarak aramayı genişletir, üçüncü sorgu ise eşleştikleri belgeleri bulmak için `hotel, motel` ve `economy,inexpensive=>budget` kullanır.

Eş anlamlıların eklenmesi, arama deneyimini tamamen değiştirir. Bu örnekte, dizinimizde bulunan belgeler ilgili olmasına rağmen özgün sorgular anlamlı sonuçlar döndüremedi. Eş anlamlıları etkinleştirerek, dizinde temel alınan verileri değiştirmeden dizini yaygın olarak kullanılan terimleri içerecek şekilde genişletebiliriz.

## <a name="sample-application-source-code"></a>Örnek uygulama kaynak kodu
Bu kılavuzda kullanılan örnek uygulamanın tam kaynak kodunu [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) üzerinde bulabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir örnek sonrasında temizlemenin en hızlı yolu, Azure Bilişsel Arama hizmetini içeren kaynak grubunu silkullanmaktır. Kaynak grubunu silerek içindeki her şeyi kalıcı olarak silebilirsiniz. Portalda, kaynak grubu adı Azure Bilişsel Arama hizmeti 'nin genel bakış sayfaalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekte, eşleme kuralları oluşturma ve C# gönderme için koddaki eş anlamlılar özelliği gösterilmiştir ve sonra bir sorguda eş anlamlı eşleme çağrısı yapılır. [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) ve [REST API](https://docs.microsoft.com/rest/api/searchservice/) başvuru belgelerinde daha fazla bilgi bulabilirsiniz.

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama eş anlamlılar kullanma](search-synonyms.md)
