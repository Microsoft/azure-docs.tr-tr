---
title: Dizin oluşturucuyu çalıştırma veya sıfırlama
titleSuffix: Azure Cognitive Search
description: Bir dizin oluşturucuyu, becerileri veya tek tek belgeleri, bir veya daha fazla bilgi deposunun tümünü veya bir kısmını yenilemek üzere sıfırlayın.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: bf8a4e51e23f438265af706914a6bc73ec30f64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667664"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Dizin oluşturucular, yetenekler veya belgeler çalıştırma veya sıfırlama

Dizin oluşturucuyu, istek üzerine bir Dizin Oluşturucu çalıştırırken veya bir zamanlamaya göre ayarlarken Dizin oluşturucuyu [yürütme, bir](search-indexer-overview.md)Dizin oluşturucuyu ilk oluşturduğunuzda gerçekleşebilir. İlk çalıştırmasından sonra bir Dizin Oluşturucu, iç "yüksek su işareti" ile hangi arama belgelerinin dizine alınacağını izler. İşaretleyici, API 'de hiçbir yerde gösterilmez, ancak dahili olarak dizin oluşturucunun, bir sonraki çalıştırmada nerede kaldığını görebilmesi için dizin oluşturucunun nerede durduğunu bilir.

Sıfırdan yeniden işlemek istiyorsanız Dizin oluşturucuyu sıfırlayarak yüksek su işaretini temizleyebilirsiniz. API 'Leri sıfırlama, nesne hiyerarşisindeki azaltma düzeylerinde mevcuttur:

+ Tüm arama yapı ( [sıfırlama dizin oluşturucular](#reset-indexers)kullanın)
+ Belirli bir belge veya belge listesi ( [belgeleri sıfırlama-önizleme](#reset-docs)kullanın)
+ Belgedeki belirli bir beceri veya zenginleştirme ( [sıfırlama yeteneklerini kullanın-önizleme](#reset-skills))

Sıfırlama API 'Leri, önbelleğe alınmış içeriği yenilemek ( [AI zenginleştirme](cognitive-search-concept-intro.md) senaryolarında geçerlidir) veya yüksek su işaretini temizlemek ve dizini yeniden oluşturmak için kullanılır.

Sıfırlayın, ardından Çalıştır, varolan belgeleri ve yeni belgeleri yeniden işleyebilir, ancak önceki çalışmalarta oluşturulan arama dizinindeki yalnız bırakılmış arama belgelerini kaldırmaz. Silme hakkında daha fazla bilgi için bkz. [belge ekleme, güncelleştirme veya silme](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Dizin oluşturucular Çalıştır

[Dizin Oluşturucu oluşturma](/rest/api/searchservice/create-indexer) , devre dışı durumda ("devre dışı": true) oluşturmadıkça Dizin oluşturucuyu oluşturur ve çalıştırır. İlk çalıştırma biraz daha uzun sürer, çünkü nesne oluşturma de bu şekilde ele alır.

[Çalıştırma Dizin Oluşturucu](/rest/api/searchservice/run-indexer) , yalnızca arama dizinini veri kaynağıyla eşitlemeyi gerekli şekilde algılar ve işler. Blob depolamada yerleşik değişiklik algılaması vardır. Azure SQL veya Cosmos DB gibi diğer veri kaynaklarının, dizin oluşturucunun yalnızca yeni ve güncelleştirilmiş satırları okuyabilmesi için değişiklik algılama için yapılandırılması gerekir.

Aşağıdaki yaklaşımlardan herhangi birini kullanarak bir Dizin Oluşturucu çalıştırabilirsiniz:

+ Azure portal, Dizin Oluşturucu sayfasındaki **Çalıştır** komutunu kullanarak
+ [Dizin oluşturucuyu Çalıştır (REST)](/rest/api/searchservice/run-indexer)
+ Azure .NET SDK 'sında [Runındexers yöntemi](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) (veya başka bir SDK 'Da eşdeğer runındexer yöntemi kullanılarak)

Dizin Oluşturucu yürütmesi Aşağıdaki sınırlara tabidir:

+ Çoğaltma başına en fazla Dizin Oluşturucu işi sayısı, eşzamanlı iş yok.

  Dizin Oluşturucu yürütmesi zaten kapasitede ise şu bildirimi alırsınız: "Indexer ' <Dizin Oluşturucu-adı> ' çalıştırılamadı, hata:" başka bir dizin oluşturucu çağrısı şu anda devam ediyor; eşzamanlı çağırmaları kullanılamaz. "

+ En fazla çalışma süresi, beceri ve olmadan 24 saat kullanılıyorsa 2 saat olur. 

  Dizin Oluşturucuyu bir zamanlamaya göre yerleştirerek, işlemeyi uzatabilirsiniz. Ücretsiz katmanda çalışma süresi sınırları daha düşüktür. Tam liste için bkz. [Dizin Oluşturucu sınırları](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Dizin oluşturucuyu sıfırlama

Bir dizin oluşturucunun sıfırlanması her şey dahil değildir. Arama dizini içinde, Dizin Oluşturucu tarafından başlangıçta doldurulan herhangi bir arama belgesi tam işleme için işaretlenir. Temel alınan kaynağın bulunduğu yeni belgeler, arama belgeleri olarak dizine eklenir. Dizin Oluşturucu bir beceri ve [önbelleğe alma](search-howto-incremental-index.md)kullanacak şekilde yapılandırıldıysa, Beceri yeniden çalıştırılır ve önbellek yenilenir.

Bu yaklaşımlardan herhangi birini kullanarak bir dizin oluşturucuyu sıfırlayabilirsiniz ve ardından yukarıda açıklanan yöntemlerden birini kullanarak bir Dizin Oluşturucu çalıştırabilirsiniz.

+ Dizin Oluşturucu sayfasındaki **Reset** komutunu kullanarak Azure Portal
+ [Dizin oluşturucuyu Sıfırla (REST)](/rest/api/searchservice/reset-indexer)
+ Azure .NET SDK 'sında [Resetındexers yöntemi](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) (veya başka bir SDK 'Da eşdeğer runındexer yöntemi kullanılarak)

Çalıştırma tamamlandıktan sonra bir sıfırlama bayrağı temizlenir. Veri kaynağınız için çalışır durumda olan herhangi bir normal değişiklik algılama mantığı, veri kümesinin geri kalanında başka bir yeni veya güncelleştirilmiş değer seçerek bir sonraki çalıştırmada devam edecektir.

> [!NOTE]
> Bir sıfırlama isteği, nelerin yeniden işlendiğini (Dizin Oluşturucu, beceri veya belge) belirler, ancak başka bir şekilde Dizin Oluşturucu çalışma zamanı davranışını etkilemez. Dizin oluşturucunun çalışma zamanı parametreleri, alan eşlemeleri, önbelleğe alma, toplu iş seçenekleri ve benzeri bir dizin varsa, bu ayarlar, sıfırlama işleminden sonra bir Dizin Oluşturucu çalıştırdığınızda etkilidir.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Becerileri sıfırlama (Önizleme)

> [!IMPORTANT] 
> Yalnızca önizleme REST API aracılığıyla kullanılabilen [yetenekler](/rest/api/searchservice/preview-api/reset-skills) , genel önizlemededir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur.

Becerileri olan Dizin oluşturucular için, bu beceriye ve çıktısına bağlı olan tüm aşağı akış becerilerini işlemeye zorlamak üzere belirli becerileri sıfırlayabilirsiniz. [Önbelleğe alınmış zenginler](search-howto-incremental-index.md) de yenilenir. Yeteneklerin sıfırlanması, bir yeteneğin yeni bir sürümü dağıtıldığında ve dizin oluşturucunun tüm belgeler için bu yeteneği yeniden çalıştırmak istediğinizde yararlı olan önbelleğe alınmış yetenek sonuçlarını geçersiz kılar. 

Yeniden [sıfırlama becerılerı](/rest/api/searchservice/preview-api/reset-skills) Rest aracılığıyla kullanılabilir **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Yukarıdaki örnekte gösterildiği gibi bireysel becerileri belirtebilirsiniz, ancak bu yeteneklerin herhangi biri listelenmemiş yetenekler (#2 #4) için bir çıkış gerektiriyorsa, önbellek gerekli bilgileri sağlayamadığı sürece, listelenmemiş yetenekler çalışacaktır. Bunun doğru olması için, #4 üzerinden #2 becerilerinin önbelleğe alınmış zenginleştirmelerinin #1 bağımlılığı olmaması gerekir (sıfırlama için listelenmiştir).

Herhangi bir yetenek belirtilmemişse, tüm beceri yürütülür ve önbelleğe alma etkinse önbellek de yenilenir.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Belgeleri Sıfırla (Önizleme)

> [!IMPORTANT] 
> [Belge sıfırlama](/rest/api/searchservice/preview-api/reset-documents) , yalnızca önizleme REST API aracılığıyla kullanılabilen genel önizlemededir. Önizleme özellikleri, olduğu gibi, [ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)altında sunulur.

Belge [sıfırlama API 'si](/rest/api/searchservice/preview-api/reset-documents) belge anahtarlarının bir listesini kabul eder, böylece belirli belgeleri yenileyebilmenizi sağlayabilirsiniz. Belirtilmişse, sıfırlama parametreleri, temel verilerdeki diğer değişikliklerden bağımsız olarak işlendiklerin tek bir determinantı olur. Örneğin, son Dizin Oluşturucu çalıştırılmasından bu yana 20 blob eklendiyse veya güncelleştirilirse, ancak yalnızca bir belge sıfırladıysanız yalnızca bir belge işlenir.

Belge başına temelinde, bu arama belgesindeki tüm alanlar veri kaynağındaki değerlerle yenilenir. Yenilenecek alanları seçip seçemezsiniz. 

Belge bir beceri ile zenginleştirirse ve önbelleğe alınmış veriler içeriyorsa, Beceri yalnızca belirtilen belgeler için çağrılır ve önbelleğe alınan belgeler için önbelleğe alınmış olarak güncelleştirilir.

Bu API 'YI ilk kez sınarken, aşağıdaki API 'Ler davranışları doğrulamanıza ve test etmenize yardımcı olur:

+ Sıfırlama durumunu ve yürütme durumunu denetlemek için, API sürümüyle [Dizin Oluşturucu durumunu alın](/rest/api/searchservice/get-indexer-status) `2020-06-30-Preview` . Durum yanıtının sonunda sıfırlama isteğiyle ilgili bilgileri bulabilirsiniz.
+ Hangi belgelerin işleyeceğini belirtmek için API sürümü olan [belgeleri sıfırlayın](/rest/api/searchservice/preview-api/reset-documents) `2020-06-30-Preview` .
+ Dizin oluşturucuyu (herhangi bir API sürümü) çalıştırmak için [Dizin oluşturucuyu çalıştırın](/rest/api/searchservice/run-indexer) .
+ Güncelleştirilmiş değerleri denetlemek için [belgeleri arayın](/rest/api/searchservice/search-documents) ve değerden emin değilseniz belge anahtarlarını geri döndürün. `"select": "<field names>"`Yanıtta hangi alanların görüneceğini sınırlamak istiyorsanız kullanın.

### <a name="formulate-and-send-the-reset-request"></a>Sıfırlama isteğini formülleştirmek ve Gönder

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

İstekte belirtilen belge anahtarları, arama dizininden alınan değerlerdir ve bu, veri kaynağındaki ilgili alanlardan farklı olabilir. Anahtar değerinden emin değilseniz, değeri döndürmek için [bir sorgu gönderin](search-query-create.md) . `select` Yalnızca belge anahtarı alanını döndürmek için ' i kullanabilirsiniz.

Birden çok arama belgesinde Ayrıştırılan Bloblar için (örneğin, [Jsonlines veya jsonArrays](search-howto-index-json-blobs.md)ya da [Delimitedtext](search-howto-index-csv-blobs.md)) ayrıştırma modu olarak, belge anahtarı Dizin Oluşturucu tarafından oluşturulur ve size bilinmiyor olabilir. Bu durumda, belge anahtarı sorgusu, doğru değeri sağlamak için bir sorgu olacaktır.

API 'YI farklı anahtarlarla birden çok kez çağırmak, yeni anahtarları belge anahtarları sıfırlama listesine ekler. **`overwrite`** Parametresini true olarak ayarlanan parametre ile çağırmak, isteğin yüküyle sıfırlanacak geçerli belge anahtarları listesinin üzerine yazar:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Sıfırlama durumunu denetle

Bir sıfırlamanın durumunu denetlemek ve hangi belge anahtarlarının işlenmek üzere kuyruğa alınacağını görmek için ile [Dizin Oluşturucu durumunu Al](/rest/api/searchservice/get-indexer-status) ' ı kullanın **`api-version=06-30-2020-Preview`** . Önizleme API 'SI, **`currentState`** Get Indexer durum yanıtının sonunda bulabileceğiniz bölümünü döndürür.

"Mode" değeri **`indexingAllDocs`** sıfırlama becerileri olacaktır (çünkü, büyük olasılıkla tüm belgeler etkilenir, çünkü AI zenginleştirme aracılığıyla doldurulmuş alanlar için).

Belge sıfırlama için, mod olarak ayarlanır **`indexingResetDocs`** . Dizin Oluşturucu, belgeleri Sıfırla çağrısında sunulan tüm belge anahtarları işlenene ve işlem devam ederken başka bir Dizin Oluşturucu işi yürütülene kadar bu durumu korur. Belge anahtarları listesindeki tüm belgelerin bulunması, her belgenin anahtar üzerinde konumlandırılmaları ve eşleşmesi gerekir ve veri kümesi büyükse bu biraz zaman alabilir. Bir blob kapsayıcısı yüzlerce blob içeriyorsa ve sıfırlamak istediğiniz belgeler sonda ise, Dizin Oluşturucu önce tüm diğerleri denetlenene kadar eşleşen blob 'ları bulamaz.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Belgeler yeniden işlendikten sonra, Dizin Oluşturucu **`indexingAllDocs`** moda döner ve sonraki çalıştırmada diğer yeni veya güncelleştirilmiş belgeleri işleyirler.

## <a name="next-steps"></a>Sonraki adımlar

Sıfırlama API 'Leri, sonraki Dizin Oluşturucu çalıştırmasının kapsamını bilgilendirmek için kullanılır. Gerçek işleme için, isteğe bağlı bir Dizin Oluşturucu çalıştırmayı çağırmanız veya zamanlanmış bir işin işi tamamlaması gerekir. Çalıştırma tamamlandıktan sonra, Dizin Oluşturucu, bir zamanlamaya ya da isteğe bağlı işleme açık olsun normal işleme döner.

Dizin Oluşturucu işlerini sıfırladıktan ve yeniden çalıştırdıktan sonra, arama hizmetinden durumu izleyebilir veya tanılama günlüğü aracılığıyla ayrıntılı bilgi edinebilirsiniz.

+ [Dizin Oluşturucu işlemleri (REST)](/rest/api/searchservice/indexer-operations)
+ [Arama Dizin Oluşturucu durumunu izleme](search-howto-monitor-indexers.md)
+ [Günlük verilerini toplayın ve çözümleyin](search-monitor-logs.md)
+ [Dizin Oluşturucu zamanlama](search-howto-schedule-indexers.md)