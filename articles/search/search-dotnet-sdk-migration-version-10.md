---
title: Azure Bilişsel Arama .NET SDK sürüm 10 ' a yükseltme
titleSuffix: Azure Cognitive Search
description: Kodu eski sürümlerden Azure Bilişsel Arama .NET SDK sürüm 10 ' a geçirin. Nelerin yeni olduğunu ve hangi kod değişikliklerinin gerekli olduğunu öğrenin.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ad912eb0b26354d40a654a1c8782dfcb960235e5
ms.sourcegitcommit: 16c5374d7bcb086e417802b72d9383f8e65b24a7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73847514"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Azure Bilişsel Arama .NET SDK sürüm 10 ' a yükseltme

[Azure Search .NET SDK 'sının](https://aka.ms/search-sdk)sürüm 9,0 veya daha eski bir sürümünü kullanıyorsanız, bu makale uygulamanızı sürüm 10 ' u kullanacak şekilde yükseltmenize yardımcı olur.

Azure Search, sürüm 10 ' da Azure Bilişsel Arama olarak yeniden adlandırıldı, ancak ad alanları ve paket adları değiştirilmez. SDK 'nın önceki sürümleri (9,0 ve öncesi), eski adı kullanmaya devam eder. SDK 'yı kullanma hakkında daha fazla bilgi için örnekler de dahil olmak üzere [.NET uygulamasından Azure bilişsel arama kullanma](search-howto-dotnet-sdk.md)konusuna bakın.

Sürüm 10, birkaç özellik ve hata düzeltmesi ekleyerek REST API sürümünün `2019-05-06`en son sürümüyle aynı işlev düzeyine getirir. Bir değişikliğin mevcut kodu kesen durumlarda, [sorunu çözmek için gereken adımlarda](#UpgradeSteps)size yol göstereceğiz.

> [!NOTE]
> Sürüm 8,0-Önizleme veya daha eski bir sürümünü kullanıyorsanız, önce sürüm 9 ' a yükseltmeniz ve sonra sürüm 10 ' a yükseltmeniz gerekir. Yönergeler için bkz. [Azure Search .NET SDK sürüm 9](search-dotnet-sdk-migration-version-9.md) ' a yükseltme.
>
> Arama hizmeti örneğiniz, en son bir dahil olmak üzere birkaç REST API sürümü destekler. Artık en son bir sürüm olmadığında bir sürümü kullanmaya devam edebilirsiniz, ancak kodunuzu en yeni sürümü kullanmak için geçirmeniz önerilir. REST API kullanırken, API sürümü parametresi aracılığıyla her istekte API sürümünü belirtmeniz gerekir. .NET SDK kullanıldığında, kullanmakta olduğunuz SDK sürümü REST API ilgili sürümünü belirler. Daha eski bir SDK kullanıyorsanız, hizmet daha yeni bir API sürümünü destekleyecek şekilde yükseltilse bile, bu kodu hiçbir değişiklik yapmadan çalıştırmaya devam edebilirsiniz.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>Sürüm 10 ' daki yenilikler
Azure Bilişsel Arama .NET SDK 'sının 10. sürümü, bu güncelleştirmelerle REST API (`2019-05-06`) en son genel kullanılabilir sürümünü hedefler:

* İki yeni becerinin tanıtımı- [koşullu yetenek](cognitive-search-skill-conditional.md) ve [metin çeviri yeteneği](cognitive-search-skill-text-translation.md).
* [Tek tek beceri](cognitive-search-skill-shaper.md) girişleri, iç içe bağlamlardan birleştirmeye uyum sağlayacak şekilde yeniden yapılandırılmış. Daha fazla bilgi için bu [örnek JSON tanımına](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts)bakın.
* İki yeni [alan eşleme işlevinin](search-indexer-field-mappings.md)eklenmesi:
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [URL kod çözme](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Belirli durumlarda, [Dizin Oluşturucu yürütme durumunda](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) görüntülenen hatalar ve uyarılar hata ayıklamada yardımcı olan ek ayrıntılara sahip olabilir. `IndexerExecutionResult`, bu davranışı yansıtacak şekilde güncelleştirilmiştir.
* Bir [beceri](cognitive-search-defining-skillset.md) içinde tanımlanan bireysel yetenekler, isteğe bağlı olarak bir `name` özelliği belirtilerek belirlenebilir.
* `ServiceLimits` [karmaşık türlerin](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) sınırlarını gösterir ve `IndexerExecutionInfo` ilgili Dizin Oluşturucu sınırlarını/kotalarını gösterir.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Yükseltme adımları

1. NuGet Paket Yöneticisi konsolunu kullanarak veya projenizin başvurularına sağ tıklayıp "NuGet Paketlerini Yönet..." seçeneğini belirleyerek NuGet başvurunuz `Microsoft.Azure.Search` güncelleştirin. Visual Studio 'da.

2. NuGet yeni paketleri ve bağımlılıklarını indirdikten sonra projenizi yeniden derleyin. 

3. Derlemeniz başarısız olursa, her derleme hatasını çözmeniz gerekecektir. Olası her derleme hatasını çözme hakkında ayrıntılı bilgi için [Sürüm 10 ' daki son değişikliklere](#ListOfChanges) bakın.

4. Herhangi bir derleme hatasını veya uyarıyı düzelttikten sonra, isterseniz yeni işlevlerden yararlanmak için uygulamanızda değişiklikler yapabilirsiniz. SDK 'daki yeni özellikler [Sürüm 10 ' daki](#WhatsNew)Yenilikler bölümünde ayrıntılı olarak açıklanmıştır.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Sürüm 10 ' daki son değişiklikler

Sürüm 10 ' da uygulamanızı yeniden oluşturmak için ek olarak kod değişikliği gerektirebilecek birçok önemli değişiklik vardır.

> [!NOTE]
> Aşağıdaki değişikliklerin listesi ayrıntılı değildir. Bazı değişiklikler büyük olasılıkla derleme hatalarına neden olmaz, ancak Azure Bilişsel Arama .NET SDK derlemelerinin önceki sürümlerine bağlı olan Derlemelerle ikili uyumluluğu kestikleri için teknik olarak kıranacaktır. Bu kategori altında yer alan önemli değişiklikler, önerilerle birlikte da listelenir. İkili uyumluluk sorunlarından kaçınmak için lütfen sürüm 10 ' a yükseltirken uygulamanızı yeniden derleyin.

### <a name="custom-web-api-skill-definition"></a>Özel Web API 'SI yetenek tanımı

[Özel Web API 'si yeteneğin](cognitive-search-custom-skill-web-api.md) tanımı, sürüm 9 ve daha eski sürümlerde yanlış belirtildi. 

Belirtilen `WebApiSkill` modeli, sözlük _içeren_ bir nesne özelliği olarak `HttpHeaders`. Bu şekilde oluşturulan `WebApiSkill` bir beceri oluşturmak REST API, isteğin hatalı biçimlendirilmiş olduğunu düşüneceğinden özel durum oluşmasına neden olur. Bu sorun, REST API geçerli bir istek olarak kabul edilen `WebApiSkill` modelinin kendisi üzerinde **en üst düzey sözlük özelliği** `HttpHeaders` düzeltildi.

Örneğin, daha önce bir `WebApiSkill` şu şekilde örneğini oluşturmaya çalıştınız:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

REST API doğrulama hatasını önlemek için aşağıdaki şekilde değiştirin:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Beceri başına mil iç içe bağlam birleştirmeye izin verir

Beceri başına mil, artık iç içe bağlamlardan giriş birleştirmeye izin verebilir. Bu değişikliği etkinleştirmek için `InputFieldMappingEntry`, yalnızca bir `Source` özelliği veya `SourceContext` ve `Inputs` özellikleri belirtilerek örneklenebilir şekilde değiştirdik.

Büyük olasılıkla herhangi bir kod değişikliği yapmanız gerekmez; Ancak, bu iki birleşimden yalnızca birine izin verildiğini unutmayın. Bunun anlamı:

- Yalnızca `Source` başlatıldığı `InputFieldMappingEntry` oluşturma geçerlidir.
- Yalnızca `SourceContext` ve `Inputs` başlatıldığı `InputFieldMappingEntry` oluşturma geçerlidir.
- Bu üç özelliği içeren diğer tüm birleşimler geçersizdir.

Bu yeni özellikten birini kullanmaya başlatmaya karar verirseniz, bu değişikliği yapmadan önce tüm istemcilerinizin sürüm 10 ' u kullanacak şekilde güncelleştirildiğinden emin olun. Aksi takdirde, bir istemci tarafından (SDK 'nın eski bir sürümünü kullanarak), bir yetenek başına Biçimlendiriciye, doğrulama hatalarına neden olabilir.

> [!NOTE]
> Temel alınan `InputFieldMappingEntry` modeli, iç içe bağlamlardan birleştirmeye izin verecek şekilde değiştirilmiş olsa da, kullanım yeteneği yalnızca bir mil tanımı dahilinde geçerlidir. Bu özelliği, derleme zamanında geçerli olan diğer becerilerle kullanmak çalışma zamanında bir doğrulama hatasına neden olur.

## <a name="skills-can-be-identified-by-a-name"></a>Yetenekler, bir adla tanımlanabilir

Bir beceri içindeki her yetenek artık, beceriye olanak tanımak için kodunuzda başlatılan yeni bir `Name`özelliğine sahiptir. Bu isteğe bağlıdır-belirtilmemişse (varsayılan olarak, açıkça kod değişikliği yapılmamıştır), Beceri içindeki yeteneğin 1 tabanlı dizinini kullanarak varsayılan bir ad atanır ve ' # ' karakteri önüne gelir. Örneğin, aşağıdaki beceri tanımında (breçekimi için en fazla başlatma atlandı):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill` bir ad `#1`atanır `WebApiSkill` `#2`atanır, `ShaperSkill` atanır.`#3`

Becerileri özel bir ad ile belirlemeyi seçerseniz, istemcilerinizin tüm örneklerini öncelikle SDK 'nın 10. sürümüne güncelleştirdiğinizden emin olun. Aksi takdirde, SDK 'nın eski bir sürümünü kullanan bir istemcinin bir yeteneğin `Name` özelliğini `null`, istemcinin varsayılan adlandırma düzenine geri dönmesine neden olduğu bir olasılık vardır.

## <a name="details-about-errors-and-warnings"></a>Hatalar ve uyarılar hakkındaki ayrıntılar

bir Dizin Oluşturucu yürütmesi sırasında oluşan hataların ve uyarıların ayrıntılarını kapsülleyen `ItemError` ve `ItemWarning` modelleri, dizin oluşturucuda hata ayıklamaya yardımcı olmak için amaca yönelik üç yeni özellik içerecek şekilde değiştirilmiştir. Bu özellikler şunlardır:

- `Name`: hatanın kaynaklandığı kaynağın adı. Örneğin, ekli beceri belirli bir yeteneğe başvurabilir.
- `Details`: hata veya uyarı hakkında ek ayrıntılı ayrıntılar.
- `DocumentationLink`: belirli bir hata veya uyarı için sorun giderme kılavuzu bağlantısı.

> [!NOTE]
> Hata ve uyarılarımızı, mümkün olan her durumda bu faydalı ayrıntıları içerecek şekilde yapılandırmak için başlatıldık. Tüm hatalar ve uyarılar için bu ayrıntıların mevcut olduğundan emin olmak için çalışıyoruz, ancak sürmekte olan bir çalışmadır ve bu ek ayrıntılar her zaman doldurulmayabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Beceri başına Biçimlendiricideki değişiklikler yeni veya mevcut kod üzerinde en olası etkiye sahiptir. Bir sonraki adım olarak, giriş yapısını gösteren bu örneği yeniden ziyaret ettiğinizden emin olun: [Shaper for YETENEĞI JSON tanımı örneği](cognitive-search-skill-shaper.md)
- [AI zenginleştirme ' ye Genel Bakış ' a](cognitive-search-concept-intro.md)gidin.
- SDK 'daki geri bildirimlerinize hoş geldiniz. Sorunlarla karşılaşırsanız [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search)hakkında yardım almak için bize danışabilirsiniz. Bir hata bulursanız, [Azure .NET SDK GitHub deposunda](https://github.com/Azure/azure-sdk-for-net/issues)bir sorun oluşturabilirsiniz. Sorun başlığınız "[Azure Bilişsel Arama]" ile öneklediğinizden emin olun.

