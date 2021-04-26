---
title: Azure Search .NET SDK sürümü 9 ' a yükselt
titleSuffix: Azure Cognitive Search
description: Kodu eski sürümlerden Azure Search .NET SDK sürümü 9 ' a geçirin. Nelerin yeni olduğunu ve hangi kod değişikliklerinin gerekli olduğunu öğrenin.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 76cb2f94c949217c36fdccb8c64a53555bab227c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606343"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Azure Search .NET SDK sürümü 9 ' a yükselt

[Azure Search .NET SDK 'sının](/dotnet/api/overview/azure/search)sürüm 7,0-Önizleme veya daha eski bir sürümünü kullanıyorsanız, bu makale uygulamanızı 9 sürümünü kullanacak şekilde yükseltmenize yardımcı olur.

> [!NOTE]
> Henüz genel olarak kullanılamayan özellikleri değerlendirmek için 8,0-Preview sürümünü kullanmak istiyorsanız, önceki sürümlerden 8,0-Preview sürümüne yükseltmek için bu makaledeki yönergeleri de izleyebilirsiniz.

Örnek içeren SDK hakkında daha genel bir anlatım için bkz. [.NET uygulamasından Azure Search kullanma](search-howto-dotnet-sdk.md).

Azure Search .NET SDK 'sının 9. sürümü, önceki sürümlerden çok sayıda değişiklik içerir. Bunlardan bazıları son değişiklikler olmakla kalmaz, yalnızca kodunuzda görece küçük değişiklikler yapılmasını gerektirir. Kodunuzu yeni SDK sürümünü kullanacak şekilde değiştirme hakkında yönergeler için bkz. [yükseltme adımları](#UpgradeSteps) .

> [!NOTE]
> Sürüm 4,0-Önizleme veya daha eski bir sürümünü kullanıyorsanız, önce sürüm 5 ' e yükseltmeniz ve ardından 9. sürüme yükseltmeniz gerekir. Yönergeler için bkz. [Azure Search .NET SDK sürüm 5 ' e yükseltme](search-dotnet-sdk-migration-version-5.md) .
>
> Azure Search hizmet örneğiniz, en son sürüm dahil olmak üzere birkaç REST API sürümü destekler. Artık en son bir sürüm olmadığında bir sürümü kullanmaya devam edebilirsiniz, ancak kodunuzu en yeni sürümü kullanmak için geçirmeniz önerilir. REST API kullanırken, API sürümü parametresi aracılığıyla her istekte API sürümünü belirtmeniz gerekir. .NET SDK kullanıldığında, kullanmakta olduğunuz SDK sürümü REST API ilgili sürümünü belirler. Daha eski bir SDK kullanıyorsanız, hizmet daha yeni bir API sürümünü destekleyecek şekilde yükseltilse bile, bu kodu hiçbir değişiklik yapmadan çalıştırmaya devam edebilirsiniz.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>Sürüm 9 ' daki yenilikler
Azure Search .NET SDK 'sının 9 sürümü, aşağıdaki özelliklerle Azure Search REST API 2019-05-06 sürümünü hedefliyor:

* [AI zenginleştirme](cognitive-search-concept-intro.md) , görüntülerden, bloblardan ve diğer yapılandırılmamış veri kaynaklarından metin ayıklayarak, bir Azure Search dizininde daha fazla aranabilir olmasını sağlamak için içeriği zenginleştirir.
* [Karmaşık türler](search-howto-complex-data-types.md) için destek, Azure Search bir dizinde neredeyse tüm iç Içe geçmiş JSON yapısını modeletmenizi sağlar.
* [AutoComplete](search-add-autocomplete-suggestions.md) , arama türü davranışı uygulamak için **öneri** API 'sine bir alternatif sağlar. Bir kullanıcının şu anda yazmakta olduğu sözcüğü veya tümceciği otomatik tamamlama "bitiyor".
* Azure Blob dizinleme 'nin bir parçası olan [Jsonlines ayrıştırma modu](search-howto-index-json-blobs.md), bir yeni satır tarafından ayrılan JSON varlığı başına bir arama belgesi oluşturur.

### <a name="new-preview-features-in-version-80-preview"></a>Sürüm 8,0 ' deki yeni Önizleme özellikleri-Önizleme
Sürüm 8,0-Azure Search .NET SDK 'nın önizlemesi API sürüm 2017-11-11-Preview. Bu sürüm, sürüm 9 ' un tüm özelliklerini içerir, ve:

* Hizmet tarafı şifreleme için [müşteri tarafından yönetilen şifreleme anahtarları](search-security-manage-encryption-keys.md) -Rest, yeni bir önizleme özelliğidir. Microsoft tarafından yönetilen yerleşik şifrelemeye ek olarak, anahtarların tek sahibi olduğunuz sırada ek bir şifreleme katmanı da uygulayabilirsiniz.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Yükseltme adımları
İlk olarak, NuGet ' i `Microsoft.Azure.Search` Paket Yöneticisi konsolu 'nu kullanarak veya proje başvurularınızı sağ tıklatıp "NuGet Paketlerini Yönet..." seçeneğini belirleyerek NuGet başvurunuz ' ı güncelleştirin. Visual Studio 'da.

NuGet yeni paketleri ve bağımlılıklarını indirdikten sonra projenizi yeniden derleyin. Kodunuzun nasıl yapılandırıldığına bağlı olarak, başarıyla yeniden oluşturulabilir. Öyleyse başlamaya hazırsınız demektir!

Derlemeniz başarısız olursa, her derleme hatasını çözmeniz gerekecektir. Olası her derleme hatasını çözme hakkında ayrıntılı bilgi için bkz. [sürüm 9 ' da son değişiklikler](#ListOfChanges) .

Eski yöntemler veya özelliklerle ilgili ek derleme uyarıları görebilirsiniz. Uyarılar, kullanım dışı özellik yerine, nelerin kullanılacağı hakkında yönergeler içerir. Örneğin, uygulamanız `DataSourceType.DocumentDb` özelliğini kullanıyorsa, "Bu üyenin kullanım dışı olduğunu belirten bir uyarı almalısınız. Bunun yerine CosmosDb kullanın ".

Herhangi bir derleme hatasını veya uyarıyı düzelttikten sonra, isterseniz yeni işlevlerden yararlanmak için uygulamanızda değişiklikler yapabilirsiniz. SDK 'daki yeni özellikler, [sürüm 9 ' daki](#WhatsNew)Yenilikler bölümünde ayrıntılı olarak açıklanmıştır.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>Sürüm 9 ' da son değişiklikler

Sürüm 9 ' da uygulamanızı yeniden oluşturmak için ek olarak kod değişikliği gerektirebilecek birkaç önemli değişiklik vardır.

> [!NOTE]
> Aşağıdaki değişikliklerin listesi ayrıntılı değildir. Bazı değişiklikler büyük olasılıkla derleme hatalarına neden olmaz, ancak Azure Search .NET SDK derlemelerinin önceki sürümlerine bağlı olan Derlemelerle ikili uyumluluğu kestikleri için teknik olarak kıranacaktır. Bu gibi değişiklikler aşağıda listelenmez. İkili uyumluluk sorunlarından kaçınmak için lütfen 9 sürümüne yükseltirken uygulamanızı yeniden derleyin.

### <a name="immutable-properties"></a>Değişmez Özellikler

Birkaç model sınıfının ortak özellikleri artık sabittir. Test için bu sınıfların özel örneklerini oluşturmanız gerekiyorsa, yeni parametreli oluşturucuları kullanabilirsiniz:

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>Alanda yapılan değişiklikler

`Field`Sınıfı, karmaşık alanları da temsil ettiğinden artık değişmiştir.

Aşağıdaki `bool` Özellikler artık null yapılabilir:

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

Bunun nedeni, bu özelliklerin artık `null` karmaşık alanlar olması durumunda olması gerekir. Bu özellikleri okuyan kodunuz varsa, işleme hazırlanmalıdır `null` . Öğesinin diğer tüm özelliklerinin `Field` her zaman null değer atandığına ve boş bırakılmış olmaya devam etmesine ve bunların bazılarının da `null` karmaşık alanlar olması durumunda (özellikle de şunlar olabilir:

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

Parametresiz oluşturucusu `Field` yapıldı `internal` . Şu andan itibaren, `Field` oluşturma sırasında açık bir ad ve veri türü gerektirir.

### <a name="simplified-batch-and-results-types"></a>Basitleştirilmiş toplu işlem ve sonuç türleri

Sürüm 7,0-Önizleme ve önceki sürümlerde, belge gruplarını kapsülleyen çeşitli sınıflar paralel sınıf hiyerarşileri halinde yapılandırılmıştır:

  -  `DocumentSearchResult` ve `DocumentSearchResult<T>` Devralındığı yer `DocumentSearchResultBase`
  -  `DocumentSuggestResult` ve `DocumentSuggestResult<T>` Devralındığı yer `DocumentSuggestResultBase`
  -  `IndexAction` ve `IndexAction<T>` Devralındığı yer `IndexActionBase`
  -  `IndexBatch` ve `IndexBatch<T>` Devralındığı yer `IndexBatchBase`
  -  `SearchResult` ve `SearchResult<T>` Devralındığı yer `SearchResultBase`
  -  `SuggestResult` ve `SuggestResult<T>` Devralındığı yer `SuggestResultBase`

Genel tür parametresi olmayan türetilmiş türler, "dinamik olarak yazılmış" senaryolarda kullanılmak ve türün kullanımını kabul ediyor `Document` .

Sürüm 8,0-Önizleme sürümünden itibaren, temel sınıfların ve genel olmayan türetilmiş sınıfların hepsi kaldırılmıştır. Dinamik olarak belirlenmiş senaryolar için,, vb `IndexBatch<Document>` . kullanabilirsiniz `DocumentSearchResult<Document>` .
 
### <a name="removed-extensibleenum"></a>ExtensibleEnum kaldırıldı

`ExtensibleEnum`Temel sınıf kaldırılmıştır. Bundan türetilmiş tüm sınıflar,, ve gibi yapılar içindir `AnalyzerName` `DataType` `DataSourceType` . `Create`Yöntemleri de kaldırılmıştır. Yalnızca `Create` Bu türlerin dizelerdeki örtülü olarak dönüştürülemesinden bu yana çağrıları kaldırabilirsiniz. Bu, derleyici hatalarına neden olursa, kesin türleri ayırt etmek için atama yoluyla dönüştürme işlecini açıkça çağırabilirsiniz. Örneğin, aşağıdaki gibi bir kodu değiştirebilirsiniz:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

şu şekilde:

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

Bu türlerin isteğe bağlı değerlerini barındıran özellikler artık isteğe bağlı olarak, isteğe bağlı olmaya devam edecek şekilde null olarak yazılmalıdır.

### <a name="removed-facetresults-and-hithighlights"></a>FacetResults ve Hithighışıkları kaldırıldı

`FacetResults`Ve `HitHighlights` sınıfları kaldırılmıştır. Model sonuçları artık olarak yazılır `IDictionary<string, IList<FacetResult>>` ve vurgulanana kadar vurgular `IDictionary<string, IList<string>>` . Bu değişiklik tarafından tanıtılan derleme hatalarını çözmek için hızlı bir yol, `using` kaldırılan türleri kullanan her bir dosyanın üst kısmına diğer adlar eklemektir. Örnek:

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>Eş Eşmap olarak değiştir 

`SynonymMap`Oluşturucunun `enum` için parametresi artık yoktur `SynonymMapFormat` . Bu Enum yalnızca bir değer içeriyordu ve bu nedenle yedekli. Bunun sonucunda derleme hataları görürseniz, yalnızca parametreye başvuruları kaldırın `SynonymMapFormat` .

### <a name="miscellaneous-model-class-changes"></a>Çeşitli model sınıfı değişiklikleri

`AutocompleteMode`Özelliği `AutocompleteParameters` artık null yapılabilir değildir. Bu özelliği ' ye atayan bir kodunuz varsa `null` , bunu kaldırmanız yeterlidir ve özellik otomatik olarak varsayılan değere başlatılır.

Oluşturucunun parametrelerinin sırası, `IndexAction` bu oluşturucunun otomatik olarak oluşturuldığına göre değiştirilmiştir. Oluşturucuyu kullanmak yerine, fabrika yöntemlerinin, vb. kullanılmasını öneririz `IndexAction.Upload` `IndexAction.Merge` .

### <a name="removed-preview-features"></a>Önizleme özellikleri kaldırıldı

Sürüm 8,0 ' den sürüm 9 ' a yükseltiyorsanız, bu özellik hala önizleme aşamasında olduğundan, müşteri tarafından yönetilen anahtarlarla şifrelemenin kaldırıldığını unutmayın. Özellikle, `EncryptionKey` `Index` ve özellikleri `SynonymMap` kaldırılmıştır.

Uygulamanızda bu özelliğe sabit bir bağımlılık varsa, Azure Search .NET SDK 'sının 9. sürümüne yükseltme yapamazsınız. 8,0-Preview sürümünü kullanmaya devam edebilirsiniz. Ancak, lütfen **Üretim uygulamalarında önizleme SDK 'larını kullanmanızı önermiyoruz**. Önizleme özellikleri yalnızca değerlendirme amaçlıdır ve değişebilir.

> [!NOTE]
> SDK 'nın 8,0-Preview sürümünü kullanarak şifrelenmiş dizinler veya eş anlamlı eşlemeler oluşturduysanız, bunları kullanmaya devam edersiniz ve kendi tanımlarını, şifreleme durumlarını olumsuz etkilemeden SDK sürüm 9 ' u kullanarak değiştirebilirsiniz. SDK 'nın 9. sürümü `encryptionKey` , özelliği REST API göndermez ve bu nedenle REST API kaynağın şifreleme durumunu değiştirmez. 

### <a name="behavioral-change-in-data-retrieval"></a>Veri alma sırasında davranış değişikliği

"Dinamik olarak yazılmış", `Search` `Suggest` ya da `Get` türü örnekleri döndüren API 'leri kullanıyorsanız `Document` , artık boş JSON dizilerinin bunun yerine ' a serisini çıkardıkları farkında olun `object[]` `string[]` .

## <a name="conclusion"></a>Sonuç
Azure Search .NET SDK 'yı kullanma hakkında daha fazla ayrıntıya ihtiyacınız varsa bkz. [.NET nasıl yapılır](search-howto-dotnet-sdk.md).

SDK 'daki geri bildirimlerinize hoş geldiniz. Sorunlarla karşılaşırsanız [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)hakkında yardım almak için bize danışabilirsiniz. Bir hata bulursanız, [Azure .NET SDK GitHub deposunda](https://github.com/Azure/azure-sdk-for-net/issues)bir sorun oluşturabilirsiniz. Sorun başlığınız "[Azure Search]" ile öneklediğinizden emin olun.

Azure Search kullandığınız için teşekkürler!