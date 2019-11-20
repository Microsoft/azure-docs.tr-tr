---
title: Artımlı dizin oluşturma (Önizleme)
titleSuffix: Azure Cognitive Search
description: Beceriler, becerileri, Dizin oluşturucular veya veri kaynaklarına yönelik tüm güncelleştirmeleri işlemek üzere verilerinizi nihai tutarlılık altına almak için AI zenginleştirme işlem hattınızı yapılandırın. Bu özellik şu anda genel önizlemede
manager: nitinme
author: Vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 76ab8784f04f3c67e4ea8062505931783048dea1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113594"
---
# <a name="what-is-incremental-indexing-in-azure-cognitive-search"></a>Azure Bilişsel Arama artımlı dizin oluşturma nedir?

> [!IMPORTANT] 
> Artımlı dizin oluşturma şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) bu özelliği sağlar. Şu anda portal veya .NET SDK desteği yok.

Artımlı dizin oluşturma, bir Bilişsel Beceri içindeki içeriğe yönelik önbelleğe alma ve durum ekleyen yeni bir Azure Bilişsel Arama özelliğidir. Bu, bir zenginleştirme ardışık düzeninde bireysel adımların işleme ve yeniden işlenmesine ilişkin denetim sağlar. Bunu yalnızca, işlem sırasında parasal yatırımınızı korumaması, ancak Ayrıca daha verimli bir sistem için de sağlar. Yapılar ve içerik önbelleğe alındığında, bir Dizin Oluşturucu hangi yeteneklerin değiştirildiğini ve yalnızca değiştirilmiş olanları ve herhangi bir aşağı akış bağımlı becerileri tespit edebilir. 

Artımlı dizin oluşturma ile, enzenginleştirme işlem hattının geçerli sürümü, dizininizdeki tüm belgeler için tutarlılığı güvence altına almak üzere en az iş miktarını işler. Tam denetim istediğiniz senaryolar için, beklenen davranışları geçersiz kılmak üzere hassas denetimleri kullanabilirsiniz. Yapılandırma hakkında daha fazla bilgi için bkz. [artımlı Dizin oluşturmayı ayarlama](search-howto-incremental-index.md).

## <a name="indexer-cache"></a>Dizin Oluşturucu önbelleği

Artımlı dizin oluşturma, enzenginleştirme ardışık düzenine bir Dizin Oluşturucu önbelleği ekler. Dizin Oluşturucu sonuçları belge çözme işleminden ve her belge için her bir yeteneğin çıktılarından önbelleğe alır. Bir beceri güncelleştirilirken, yalnızca değiştirilen veya aşağı akış becerileri yeniden çalıştırılır. Güncelleştirilmiş sonuçlar önbelleğe yazılır ve belge dizinde ve bilgi deposunda güncelleştirilir.

Fiziksel olarak, önbellek bir depolama hesabıdır. Bir arama hizmeti içindeki tüm dizinler, Dizin Oluşturucu önbelleğinin aynı depolama hesabını paylaşabilir. Her dizin oluşturucuya benzersiz ve sabit bir önbellek tanımlayıcısı atanır.

### <a name="cache-configuration"></a>Önbellek yapılandırması

Dizin oluşturucudaki `cache` özelliğini, artımlı dizin oluşturma işleminden faydalanmasını başlatmak için ayarlamanız gerekir. Aşağıdaki örnek, önbelleğe alma etkin olan bir dizin oluşturucuyu gösterir. Bu yapılandırmanın belirli kısımları aşağıdaki bölümlerde açıklanmıştır.

```json
{
    "name": "myIndexerName",
    "targetIndexName": "myIndex",
    "dataSourceName": "myDatasource",
    "skillsetName": "mySkillset",
    "cache" : {
        "storageConnectionString" : "Your storage account connection string",
        "enableReprocessing": true,
        "id" : "Auto generated Id you do not need to set"
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters":{}
}
```

Bu özelliği mevcut bir dizin oluşturucuda ilk kez ayarlamak, aynı zamanda veri kaynağınızdaki tüm belgelerin yeniden işlenmesine neden olacak şekilde onu sıfırlamanız gerekir. Artımlı dizin oluşturma işleminin hedefi, dizininizdeki belgeleri veri kaynağınıza ve beceri 'nizin güncel sürümüne tutarlı hale getirir. Dizinin sıfırlanması, önceki beceri sürümleri tarafından zenginleştirilmiş tüm belgeleri ortadan kaldırdığı için bu tutarlılığa doğru ilk adımdır. Dizin oluşturucunun tutarlı bir temel ile başlayacak şekilde sıfırlanması gerekir.

### <a name="cache-lifecycle"></a>Önbellek yaşam döngüsü

Önbelleğin yaşam döngüsü Dizin Oluşturucu tarafından yönetilir. Dizin oluşturucudaki `cache` özelliği null olarak ayarlandıysa veya bağlantı dizesi değiştiyse, varolan önbellek silinir. Önbellek yaşam döngüsü Ayrıca Dizin Oluşturucu yaşam döngüsüne de bağlıdır. Bir Dizin Oluşturucu silinirse, ilişkili önbellek de silinir.

### <a name="indexer-cache-mode"></a>Dizin Oluşturucu önbellek modu

Dizin Oluşturucu önbelleği, verilerin yalnızca önbelleğe yazıldığı ve verilerin önbelleğe yazıldığı ve belgeleri yeniden zenginleştirmenin kullanıldığı modlarda çalışabilir.  Önbellekteki `enableReprocessing` özelliğini `false`olarak ayarlayarak artımlı zenginleştirme işlemini geçici olarak askıya alabilir ve daha sonra artımlı zenginleştirme ve daha sonra `true`olarak ayarlayarak nihai tutarlılığı sağlayabilirsiniz. Bu denetim özellikle, belgelerin yapı 'larınız genelinde tutarlılık sağlamak için yeni belgelerin dizinlemesini belirlemek istediğinizde yararlıdır.

## <a name="change-detection-override"></a>Değişiklik algılama geçersiz kılma

Artımlı dizin oluşturma, zenginleştirme işlem hattının tüm yönleri üzerinde ayrıntılı denetim sağlar. Bu denetim, bir değişikliğin istenmeyen sonuçlara neden olabileceği durumlarla ilgilenmenize olanak tanır. Örneğin, bir beceri düzenlenmek ve özel bir beceri için URL 'YI güncellemek, dizin oluşturucunun bu beceri için önbelleğe alınmış sonuçları geçersiz kılmasına neden olur. Yalnızca uç noktayı farklı bir VM 'ye taşıyor veya becerinizi yeni bir erişim anahtarıyla yeniden dağıtıyorsanız, gerçekten var olan belgelerin yeniden işlenmesini istemezsiniz.

Dizin oluşturucunun yalnızca açıkça gereken zenginleştirme kullandığından emin olmak için beceri güncelleştirmeleri isteğe bağlı olarak `disableCacheReprocessingChangeDetection` QueryString parametresini `true`olarak ayarlayabilir. Ayarlandığında, bu parametre yalnızca beceri güncelleştirmelerinin yapıldığından ve değişikliğin mevcut Corpus üzerindeki etkilere karşı değerlendirilmediğinden emin olur.

Aşağıdaki örnek QueryString kullanımını gösterir. & Ayrılmış anahtar değer çiftleri ile isteğin bir parçasıdır. 

```http
PUT https://customerdemos.search.windows.net/skillsets/callcenter-text-skillset?api-version=2019-05-06-Preview&disableCacheReprocessingChangeDetection=true
```

## <a name="cache-invalidation"></a>Önbellek geçersiz kılma

Bu senaryonun dönüştürüleceği, özel bir yeteneğin yeni bir sürümünü dağıtabileceğiniz, zenginleştirme ardışık düzeninde hiçbir şey olmayan bir şeydir, ancak belirli bir yetenek geçersiz kılınmak ve güncelleştirilmiş bir modelin avantajlarını yansıtacak şekilde, etkilenen tüm belgelerin yeniden işlenmesi gerekir. Bu örneklerde, Beceri üzerindeki becerileri geçersiz kıl işlemini çağırabilirsiniz. Sıfırlama becerileri API 'SI, önbellekte geçersiz kılınmaları gereken yetenek çıkışları listesini içeren bir POST isteğini kabul eder. Yetenek sıfırlama API 'SI hakkında daha fazla bilgi için bkz. [Dizin oluşturucuyu sıfırlama (arama REST API)](https://docs.microsoft.com/rest/api/searchservice/reset-indexer).

## <a name="bi-directional-change-detection"></a>İki yönlü değişiklik algılama

Dizin oluşturucular yalnızca ileri doğru hareket etmez ve yeni belgeler işlemez, ancak artık geriye doğru ve sürücü daha önce işlenmiş belgeleri tutarlı bir şekilde taşıyabilir. Bu yeni özellik sayesinde, kendi zenginleştirme işlem hattı bileşenlerinde yapılan değişikliklerin dizin oluşturucunun nasıl çalıştığını anlamak önemlidir. Dizin Oluşturucu, önbelleğe alınmış içeriğe göre geçersiz kılınabilecek veya tutarsız bir değişikliği tanımladığı zaman yapılacak işleri sıraya alacak.

### <a name="invalidating-changes"></a>Değişiklikler geçersiz kılın

Geçersiz kılınmaz, ancak zenginleştirme işlem hattının durumu üzerinde önemli bir etkisi vardır. Geçersiz kılma değişikliği, önbelleğin tamamının artık geçerli olmadığı bir yerdir. Geçersiz kılma değişikliği örneği, veri kaynağınızın güncelleştirildiği bir örnektir. Değişikliğin önbelleği geçersiz kılanmadığından ve örneğin depolama hesabındaki anahtarı döndürmediğinden ilgili senaryolar için, `ignoreResetRequirement` QueryString parametresi, belirli bir kaynağın güncelleştirme işleminde `true` olarak ayarlanmalıdır, çünkü işlemin reddedildi.

Önbelleğinizi geçersiz kılacak değişikliklerin tamamı aşağıda verilmiştir:

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

### <a name="inconsistent-changes"></a>Tutarsız değişiklikler

Tutarsız değişikliğe örnek olarak, beceri için bir yetenek değiştiren bir güncelleştirmedir. Değişiklik, önbelleğin bir bölümünü tutarsız hale getirir. Dizin Oluşturucu, işleri yeniden tutarlı hale getirmek için işi belirler.  

Önbellek tutarsızlığına neden olan değişikliklerin tamamı listesi:

* Beceri ' deki beceri farklı türde. Yeteneğin OData türü güncelleştirildi
* Beceriye özgü parametreler, örneğin URL, varsayılanlar veya diğer parametreler ile güncelleştirildi
* Beceri çıkışları değişiklikler, Beceri ek veya farklı çıktılar döndürüyor
* Yetenek güncelleştirmeleri sonucunda farklı bir aile, Beceri zinciri değişti yani Beceri girişleri
* Bu beceriye bir giriş sağlayan bir yetenek güncelleştirilirse herhangi bir yukarı akış yetenek doğrulaması
* Bilgi deposu projeksiyon konumundaki güncelleştirmeler, belgelerin yeniden yansıtımasına neden olur
* Bilgi deposu projeksiyonları üzerinde yapılan değişiklikler, belgelerin yeniden yansıtımasına neden olur
* Dizin Oluşturucu üzerinde değiştirilen çıkış alanı eşlemeleri, belgelerin dizine yeniden yansıtımasına neden olur

## <a name="rest-api-reference-for-incremental-indexing"></a>Artımlı dizin oluşturma için REST API başvurusu

REST `api-version=2019-05-06-Preview`, Dizin oluşturucular, becerileri ve veri kaynaklarına eklemeler ile artımlı dizin oluşturma için API 'Ler sağlar. Başvuru belgeleri şu anda bu eklemeleri içermez. Aşağıdaki bölümde API değişiklikleri açıklanmaktadır.

### <a name="indexers"></a>Dizinleyiciler

[Create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) ve [Update Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) artık önbelleğiyle ilgili yeni özellikleri kullanıma sunacaktır:

* `StorageAccountConnectionString`: ara sonuçları önbelleğe almak için kullanılacak depolama hesabına bağlantı dizesi.

* `CacheId`: `cacheId`, bu Dizin Oluşturucu için önbellek olarak kullanılacak `annotationCache` depolama hesabı içindeki kapsayıcının tanımlayıcısıdır. Bu önbellek, bu Dizin Oluşturucu için benzersiz olacak ve Dizin Oluşturucu silinip aynı adla yeniden oluşturulursa, `cacheId` yeniden oluşturulacaktır. `cacheId` ayarlanamaz, hizmet tarafından her zaman oluşturulur.

* `EnableReprocessing`: `true` olarak ayarla, `false`olarak ayarlandığında belgeler önbelleğe yazılmaya devam eder, ancak mevcut belgeler önbellek verilerine göre yeniden işlenir.

Bazı Dizin oluşturucular ( [veri kaynakları](https://docs.microsoft.com/rest/api/searchservice/create-data-source)aracılığıyla) sorgular aracılığıyla veri alır. Veri alan sorgular için, Dizin oluşturucular yeni bir sorgu dizesi parametresini de destekleyecektir: güncelleştirme eyleminiz önbelleği geçersiz kılamadığında `ignoreResetRequirement` `true` olarak ayarlanmalıdır.

### <a name="skillsets"></a>Beceri kümeleri

Becerileri, yeni işlemleri desteklemez, ancak yeni bir QueryString parametresini destekleyecektir: geçerli eyleme göre mevcut belgelerde güncelleştirme yapmak istediğinizde `disableCacheReprocessingChangeDetection` `true` olarak ayarlanmalıdır.

### <a name="datasources"></a>Kaynağı

Veri kaynakları yeni işlemleri desteklemez, ancak yeni bir QueryString parametresini destekleyecektir: güncelleştirme eyleminiz önbelleği geçersiz kılmaz, `ignoreResetRequirement` `true` olarak ayarlanmalıdır.

## <a name="best-practices"></a>En iyi uygulamalar

Artımlı dizin oluşturma özelliğinin kullanılması önerilen yaklaşım, yeni bir dizin oluşturucudaki Cache özelliğini ayarlayarak veya var olan bir dizin oluşturucuyu sıfırlayarak önbellek özelliğini ayarlayıp artımlı Dizin oluşturmayı yapılandırmaktır.

Verilerinize kolayca algılanmayacak, istenmeyen tutarsızlığa yol açacağından, `ignoreResetRequirement` gelişigüzel bir şekilde kullanın.

## <a name="takeaways"></a>Paketler

Artımlı dizin oluşturma, veri kaynağındaki değişiklik izlemeyi veri kaynağı, Beceri 'nizin geçerli sürümü ve Dizin Oluşturucu dahil olmak üzere zenginleştirme işlem hattının tüm yönlerine genişleten güçlü bir özelliktir. Becerilerinizi, becerileri veya zenginleştirmelerinizi geliştirirken, enzenginleştirme işlem hattı, belgelerinizi yine de nihai tutarlılığa yönlendirirken, en az olası çalışmanın yapılmasını sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Mevcut bir dizin oluşturucuya önbellek ekleyerek artımlı dizin oluşturma ile çalışmaya başlayın veya yeni bir Dizin Oluşturucu tanımlarken önbelleği ekleyin.

> [!div class="nextstepaction"]
> [Artımlı dizin oluşturma ayarlama](search-howto-incremental-index.md)
