---
title: Artımlı zenginleştirme kavramları (Önizleme)
titleSuffix: Azure Cognitive Search
description: Mevcut işlenen belgelerdeki yatırımları korumak için Azure depolama 'daki AI zenginleştirme ardışık düzeninde bulunan ara içeriği ve artımlı değişiklikleri önbelleğe alma. Bu özellik şu anda genel önizleme aşamasındadır.
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: 2448609b1184c8e91947bffbd13cfea8e3fe5d52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390870"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Azure Bilişsel Arama artımlı zenginleştirme ve önbelleğe alma

> [!IMPORTANT] 
> Artımlı zenginleştirme Şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> [REST API önizleme sürümleri](search-api-preview.md) bu özelliği sağlar. Şu anda portal veya .NET SDK desteği yok.

*Artımlı zenginleştirme* , [becerileri](cognitive-search-working-with-skillsets.md)hedefleyen bir özelliktir. Daha sonra bir zenginleştirme işlem hattı tarafından oluşturulan işleme çıkışını gelecekteki Dizin Oluşturucu çalıştırmalarının yeniden kullanmak üzere kaydetmek için Azure depolama 'dan yararlanır. Mümkün olan yerlerde, Dizin Oluşturucu halen geçerli olan önbelleğe alınmış çıktıyı yeniden kullanır. 

Yalnızca artımlı zenginleştirme, işlem sırasında (özellikle OCR ve görüntü işleme) parasal yatırımınızı korumasına karşın daha verimli bir sistem de sağlar. 

Artımlı önbelleğe alma kullanan bir iş akışı aşağıdaki adımları içerir:

1. Önbelleği depolamak için [bir Azure depolama hesabı oluşturun veya oluşturun](../storage/common/storage-account-create.md) .
1. Dizin oluşturucuda [artımlı zenginleştirme özelliğini etkinleştirin](search-howto-incremental-index.md) .
1. İşlem hattını çağırmak için bir [Dizin Oluşturucu](/rest/api/searchservice/create-indexer) -Plus [beceri](/rest/api/searchservice/create-skillset) -oluşturun. İşlem sırasında, enzenginleştirme aşamaları, daha sonra kullanılmak üzere BLOB depolama alanındaki her belge için kaydedilir.
1. Kodunuzu test edin ve değişiklik yaptıktan sonra, bir tanımı değiştirmek için [Update beceri](/rest/api/searchservice/update-skillset) kullanın.
1. İşlem hattını çağırmak için [Dizin oluşturucuyu çalıştırın](/rest/api/searchservice/run-indexer) , daha hızlı ve daha düşük maliyetli işleme için önbelleğe alınmış çıktıyı alır.

Mevcut bir Dizin Oluşturucu ile çalışırken yapılan adımlar ve konular hakkında daha fazla bilgi için bkz. [artımlı zenginleştirme ayarlama](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Dizin Oluşturucu önbelleği

Artımlı zenginleştirme, enzenginleştirme ardışık düzenine bir önbellek ekler. Dizin Oluşturucu sonuçları belge çözme işleminden ve her belge için her bir yeteneğin çıktılarından önbelleğe alır. Bir beceri güncelleştirilirken, yalnızca değiştirilen veya aşağı akış becerileri yeniden çalıştırılır. Güncelleştirilmiş sonuçlar önbelleğe yazılır ve belge arama dizininde veya bilgi deposunda güncelleştirilir.

Fiziksel olarak, önbellek, Azure Depolama hesabınızdaki bir blob kapsayıcısında depolanır. Önbellek Ayrıca, işleme güncelleştirmelerinin dahili bir kaydı için tablo depolamayı kullanır. Bir arama hizmeti içindeki tüm dizinler, Dizin Oluşturucu önbelleğinin aynı depolama hesabını paylaşabilir. Her dizin oluşturucuya, kullandığı kapsayıcıya benzersiz ve sabit bir önbellek tanımlayıcısı atanır.

## <a name="cache-configuration"></a>Önbellek yapılandırması

`cache`Faydalanmasını, Dizin oluşturucudaki özelliğini artımlı zenginleştirme 'dan başlatmak için ayarlamanız gerekir. Aşağıdaki örnek, önbelleğe alma etkin olan bir dizin oluşturucuyu gösterir. Bu yapılandırmanın belirli kısımları aşağıdaki bölümlerde açıklanmıştır. Daha fazla bilgi için bkz. [artımlı zenginleştirme ayarlama](search-howto-incremental-index.md).

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

Mevcut bir dizin oluşturucuda bu özelliğin ayarlanması, Dizin oluşturucuyu sıfırlamanıza ve yeniden çalıştırmanız gerekir, bu da veri kaynağınızdaki tüm belgelerin yeniden işlenmesini sağlar. Bu adım, önceki beceri sürümleri tarafından zenginleştirilmiş belgelerin ortadan kaldırılması için gereklidir. 

## <a name="cache-management"></a>Önbellek yönetimi

Önbelleğin yaşam döngüsü Dizin Oluşturucu tarafından yönetilir. `cache`Dizin oluşturucudaki özellik null olarak ayarlandıysa veya bağlantı dizesi değiştirilirse, varolan önbellek bir sonraki Dizin Oluşturucu çalıştırmasında silinir. Önbellek yaşam döngüsü Ayrıca Dizin Oluşturucu yaşam döngüsüne de bağlıdır. Bir Dizin Oluşturucu silinirse, ilişkili önbellek de silinir.

Artımlı zenginleştirme, sizin bölümleriniz üzerinde müdahale olmadan değişiklikleri tespit etmek ve bunlara yanıt vermek üzere tasarlanırken, varsayılan davranışları geçersiz kılmak için kullanabileceğiniz parametreler vardır:

+ Yeni belgelerin önceliğini belirleme
+ Beceri denetimlerini atla
+ Veri kaynağı denetimlerini atla
+ Beceri değerlendirmesini zorla

### <a name="prioritize-new-documents"></a>Yeni belgelerin önceliğini belirleme

`enableReprocessing`Özelliği önbellekte zaten temsil edilen gelen belgelerde işlemeyi denetlemek için ayarlayın. `true`(Varsayılan), Dizin oluşturucuyu yeniden çalıştırdığınızda zaten önbellekte bulunan belgeler yeniden işlenir, böylece beceri güncelleştirmeniz bu belgeyi etkiler. 

Ne zaman `false` , mevcut belgeler yeniden işlenmediği zaman, yeni, gelen içeriğin var olan içerik üzerinde etkin bir şekilde önceliklerini. Yalnızca geçici olarak ' i ayarlamanız gerekir `enableReprocessing` `false` . Corpus genelinde tutarlılık sağlamak için, `enableReprocessing` `true` her iki yeni ve var olan tüm belgelerin geçerli beceri tanımına göre geçerli olduğundan emin olmak için çoğu zaman olmalıdır.

### <a name="bypass-skillset-evaluation"></a>Beceri değerlendirmesini atla

Bu beceri 'in bir beceri ve yeniden işlemesini değiştirmek genellikle el ile yapılır. Ancak, bir beceri üzerinde yapılan bazı değişiklikler yeniden işlemeye neden olmaz (örneğin, bir özel yeteneği yeni bir konuma veya yeni bir erişim anahtarıyla dağıtma). Büyük olasılıkla, Beceri 'in bir üyesi üzerinde orijinal etkisi olmayan çevresel değişiklikler vardır. 

Beceri bir değişikliğin gerçekten yararlanmayan olduğunu biliyorsanız, parametreyi şu şekilde ayarlayarak beceri değerlendirmesini geçersiz kılmanız gerekir `disableCacheReprocessingChangeDetection` `true` :

1. Update beceri 'i çağırın ve beceri tanımını değiştirin.
1. `disableCacheReprocessingChangeDetection=true`İstek üzerine parametreyi ekleyin.
1. Değişikliği gönder.

Bu parametre ayarlandığında, yalnızca beceri tanımı güncelleştirmelerinin yürütülmesi ve değişikliğin mevcut Corpus üzerindeki etkiler için değerlendirilmemesi sağlanır.

Aşağıdaki örnek, parametresiyle bir Update beceri isteği gösterir:

```http
PUT https://[search service].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview&disableCacheReprocessingChangeDetection=true
```

### <a name="bypass-data-source-validation-checks"></a>Veri kaynağı doğrulama denetimlerini atla

Bir veri kaynağı tanımında yapılan çoğu değişiklik önbelleği geçersiz kılar. Bununla birlikte, bir değişikliğin bir bağlantı dizesini değiştirme veya depolama hesabındaki anahtarı döndürme gibi bir değişikliği önbelleğin geçersiz kılamadı olduğunu bildiğiniz senaryolarda, `ignoreResetRequirement` veri kaynağı güncelleştirmesinde parametresini ekleyin. Bu parametreyi olarak ayarlamak `true` , işlemenin, tüm nesnelerin yeniden oluşturulmasına ve sıfırdan doldurulmasına neden olacak bir sıfırlama koşulu tetiklemeden, devam etmesine izin verir.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview&ignoreResetRequirement=true
```

### <a name="force-skillset-evaluation"></a>Beceri değerlendirmesini zorla

Önbelleğin amacı gereksiz işlemden kaçınmaktır, ancak dizin oluşturucunun algılamadığı bir yeteneğe değişiklik yapmayı (örneğin, özel bir beceri gibi harici koddaki bir şeyi değiştirme) varsayalım.

Bu durumda, bu yeteneğin çıktısına bağımlılığı olan tüm aşağı akış becerileri de dahil olmak üzere belirli bir yeteneğin yeniden işlenmesini zorlamak için [sıfırlama becerileri](/rest/api/searchservice/preview-api/reset-skills) kullanabilirsiniz. Bu API, yeniden işlenmek üzere geçersiz kılınmaları ve işaretlenmesi gereken yetenekler listesini içeren bir POST isteğini kabul eder. Yeteneklerini sıfırladıktan sonra, işlem hattını çağırmak için Dizin oluşturucuyu çalıştırın.

### <a name="reset-documents"></a>Belgeleri Sıfırla

[Bir dizin oluşturucunun sıfırlanması](/rest/api/searchservice/reset-indexer) , arama corpmundaki tüm belgelerin yeniden işlenmesini sağlayacaktır. Yalnızca birkaç belgenin yeniden işlenmesi gereken senaryolarda ve veri kaynağı güncelleştirilemeyebilir, belirli belgelerin yeniden işlenmesini zorlamak için [belgeleri Sıfırla (Önizleme)](/rest/api/searchservice/preview-api/reset-documents) kullanın. Bir belge sıfırlandığında, Dizin Oluşturucu bu belge için önbelleği geçersiz kılar ve belge veri kaynağından okunarak yeniden işlenir. Daha fazla bilgi için bkz. [Dizinleyicileri, becerileri ve belgeleri çalıştırma veya sıfırlama](search-howto-run-reset-indexers.md).

## <a name="change-detection"></a>Değişiklik algılama

Bir önbelleği etkinleştirdikten sonra, Dizin Oluşturucu, hangi içeriğin yeniden kullanılabilir olduğunu ve ne tekrar işlenmesi gerektiğini belirleyen işlem hattı kompozisyoninizdeki değişiklikleri değerlendirir. Bu bölümde, önbellek ve ardından artımlı işleme tetikleyen değişiklikler tarafından geçersiz kılan değişiklikler numaralandırılır. 

### <a name="changes-that-invalidate-the-cache"></a>Önbelleği geçersiz kılan değişiklikler

Geçersiz kılma değişikliği, önbelleğin tamamının artık geçerli olmadığı bir yerdir. Geçersiz kılma değişikliği örneği, veri kaynağınızın güncelleştirildiği bir örnektir. Önbelleğinizi geçersiz kılacak değişikliklerin tamamı aşağıda verilmiştir:

* Veri kaynağı türüne değiştirme
* Veri kaynağı kapsayıcısına geçiş
* Veri kaynağı kimlik bilgileri
* Veri kaynağı değişiklik algılama ilkesi
* Veri kaynakları algılama ilkesini silme
* Dizin Oluşturucu alan eşlemeleri
* Dizin Oluşturucu parametreleri
    * Ayrıştırma modu
    * Dışlanan dosya adı uzantıları
    * Dizinli dosya adı uzantıları
    * Yalnızca büyük belgeler için dizin depolama meta verileri
    * Sınırlandırılmış metin üstbilgileri
    * Sınırlandırılmış metin sınırlayıcısı
    * Belge kökü
    * Görüntü eylemi (görüntülerin ayıklandığı değişiklikler)

### <a name="changes-that-trigger-incremental-processing"></a>Artımlı işlemeyi tetikleyen değişiklikler

Artımlı işleme, Beceri tanımınızı değerlendirir ve hangi yeteneklerin yeniden çalıştırılacağını ve belge ağacının etkilenen bölümlerinin seçmeli olarak güncelleştirilmesini belirler. Artımlı zenginleştirme ile sonuçlanan tüm değişikliklerin listesi aşağıda verilmiştir:

* Beceri ' deki beceri farklı türde. Yeteneğin OData türü güncelleştirildi
* Beceriye özgü parametreler, örneğin URL, varsayılanlar veya diğer parametreler ile güncelleştirildi
* Beceri çıkışları değişiklikler, Beceri ek veya farklı çıktılar döndürüyor
* Yetenek güncelleştirmeleri sonucunda farklı bir aile, Beceri zinciri değişti yani Beceri girişleri
* Bu beceriye bir giriş sağlayan bir yetenek güncelleştirilirse herhangi bir yukarı akış yetenek doğrulaması
* Bilgi deposu projeksiyon konumundaki güncelleştirmeler, belgelerin yeniden yansıtımasına neden olur
* Bilgi deposu projeksiyonları üzerinde yapılan değişiklikler, belgelerin yeniden yansıtımasına neden olur
* Dizin Oluşturucu üzerinde değiştirilen çıkış alanı eşlemeleri, belgelerin dizine yeniden yansıtımasına neden olur

## <a name="api-reference"></a>API başvurusu

REST API sürüm `2020-06-30-Preview` , Dizin oluşturucularda ek özellikler aracılığıyla artımlı zenginleştirme sağlar. Becerileri ve veri kaynakları genel kullanıma sunulan sürümü kullanabilir. Başvuru belgelerinin yanı sıra, API 'Leri çağırma hakkındaki ayrıntılar için bkz.  [artımlı zenginleştirme için önbelleğe almayı yapılandırma](search-howto-incremental-index.md) .

+ [Dizin Oluşturucu oluştur (api-Version = 2020-06 -30-Preview)](/rest/api/searchservice/create-indexer) 

+ [Dizin oluşturucuyu güncelleştir (api-Version = 2020-06 -30-Preview)](/rest/api/searchservice/update-indexer) 

+ [Güncelleştirme beceri (api-Version = 2020-06-30)](/rest/api/searchservice/update-skillset) (ISTEKTE yeni URI parametresi)

+ [Becerileri sıfırlama (api-Version = 2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ Veritabanı Dizin oluşturucular (Azure SQL, Cosmos DB). Bazı Dizin oluşturucular verileri sorgular aracılığıyla alır. Veri alan sorgular için [güncelleştirme veri kaynağı](/rest/api/searchservice/update-data-source) , güncelleştirme eyleminiz önbelleği geçersiz kılamadığında olarak ayarlanması gereken, **ıgnoreresetrequirement** isteği üzerinde yeni bir parametreyi destekler `true` . 

  , Verilerinizde kolayca algılanmayacak istenmeyen tutarsızlığa yol açacağından, **ıgnoreresetrequirement** 'ı gelişigüzel bir şekilde kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Artımlı zenginleştirme, değişiklik izlemeyi becerileri ve AI zenginleştirme olarak genişleten güçlü bir özelliktir. Artımlı zenginleştirme, Beceri tasarımı üzerinde yineleme yaparken var olan işlenmiş içeriğin yeniden kullanılmasını mümkün.

Sonraki adım olarak, var olan bir dizin oluşturucuda önbelleğe almayı etkinleştirin veya yeni bir Dizin Oluşturucu tanımlarken bir önbellek ekleyin.

> [!div class="nextstepaction"]
> [Artımlı zenginleştirme için önbelleğe almayı yapılandırın](search-howto-incremental-index.md)