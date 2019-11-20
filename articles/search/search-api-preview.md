---
title: REST API sürüm 2019-05-06-Önizleme
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama Service REST API sürüm 2019-05-06-önizleme bilgi deposu ve müşteri tarafından yönetilen şifreleme anahtarları gibi deneysel özellikleri içerir.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720017"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Bilişsel Arama Service REST API-sürüm 2019-05-06-Önizleme

Bu makalede, arama hizmeti REST API 'ın `api-version=2019-05-06-Preview` sürümü açıklanmakta ve henüz genel kullanıma sunulmayan Deneysel özellikler sunulmaktadır.

> [!NOTE]
> Önizleme özellikleri, test ve deneme ile geri bildirim toplama amacını ve değişikliğe tabi bir şekilde kullanılabilir. Üretim uygulamalarında önizleme API 'Lerini kullanmayı önemle tavsiye ederiz.


## <a name="new-in-2019-05-06-preview"></a>2019-05-06 içinde yeni-önizleme

+ [Artımlı Dizin](cognitive-search-incremental-indexing-conceptual.md) oluşturma işlemi, kaynak verileri, Dizin Oluşturucu ve beceri tanımları değişirken var olan çıktıyı yeniden kullanmanıza olanak sağlayan dizin oluşturma için yeni bir moddur. Bu özellik yalnızca Bilişsel Beceri tanımlanan zenginleştirmeleri için geçerlidir.

+ [Cosmos DB Dizin Oluşturucu](search-howto-index-cosmosdb.md) MongoDB API 'Si, Gremlin apı ve Cassandra API destekler.

+ [Azure Data Lake Storage 2. Indexer](search-howto-index-azure-data-lake-storage.md) Data Lake Storage 2. içerik ve meta verileri dizinedebilir.

+ [Belge ayıklama (Önizleme)](cognitive-search-skill-document-extraction.md) , dizin oluşturma sırasında kullanılan bilişsel bir beceriye sahiptir ve bir dosyanın içeriğini bir beceri içinden ayıklamanızı sağlar. Daha önce belge çözme yalnızca beceri yürütmeden önce oluşmuştur. Bu beceriye ek olarak, bu işlemi beceri yürütme içinde de gerçekleştirebilirsiniz.

+ [Metin çevirisi (Önizleme)](cognitive-search-skill-text-translation.md) , dizinleme sırasında metin değerlendiren ve her kayıt için belirtilen hedef dile çevrilen metni döndüren bilişsel bir yetenküldür.

+ [Bilgi deposu](knowledge-store-concept-intro.md) , bir AI tabanlı zenginleştirme işlem hattının yeni bir hedefi. Fiziksel veri yapısı Azure Blob depolama ve Azure Tablo depolamada bulunur ve ekli bilişsel beceri sahip bir Dizin Oluşturucu çalıştırdığınızda oluşturulur ve doldurulur. Bilgi deposunun kendisi tanımı bir beceri tanımı içinde belirtilir. Bilgi deposu tanımı içinde, verilerin nasıl şekillendirilmiş olduğunu, verilerin tablo depolama veya blob depolama alanında mi depolandığını ve birden çok görünüm olup olmadığını belirten *projeksiyon* öğeleri aracılığıyla verilerinizin fiziksel yapılarını kontrol edersiniz.

+ Hizmet tarafı şifreleme için [müşteri tarafından yönetilen şifreleme anahtarları](search-security-manage-encryption-keys.md) Ayrıca yeni bir önizleme özelliğidir. Microsoft tarafından yönetilen yerleşik şifrelemeye ek olarak, anahtarların tek sahibi olduğunuz sırada ek bir şifreleme katmanı da uygulayabilirsiniz.

## <a name="earlier-preview-features"></a>Önceki Önizleme özellikleri

Önceki önizlemelerde duyurulan Özellikler hala genel önizlemede. Önceki bir Preview API sürümü ile bir API 'yi arıyorsanız, bu sürümü kullanmaya devam edebilir veya beklenen davranışa hiçbir değişiklik yapmadan `2019-05-06-Preview` geçebilirsiniz.

+ [Morelikethis sorgu parametresi](search-more-like-this.md) , belirli bir belgeyle ilgili belgeleri bulur. Bu özellik daha önceki önizlemelerde yer aldı. 

+ [CSV blobu dizin oluşturma](search-howto-index-csv-blobs.md) , her metin blobu için bir belge yerine her satır için bir belge oluşturur.

## <a name="how-to-call-a-preview-api"></a>Önizleme API 'sini çağırma

Daha eski önizlemeler hala çalışır, ancak zaman içinde eski hale gelir. Kodunuz `api-version=2016-09-01-Preview` veya `api-version=2017-11-11-Preview`çağırırsa, bu çağrılar hala geçerlidir. Ancak, geliştirmelerle yalnızca en yeni önizleme sürümü yenilenir. 

Aşağıdaki örnek sözdizimi, önizleme API sürümüne yapılan çağrıyı gösterir.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Bilişsel Arama hizmeti birden çok sürümde kullanılabilir. Daha fazla bilgi için bkz. [API sürümleri](search-api-versions.md).

## <a name="next-steps"></a>Sonraki adımlar

Arama REST API başvuru belgelerini gözden geçirin. Sorunlarla karşılaşırsanız [StackOverflow](https://stackoverflow.com/) hakkında yardım isteyin veya [desteğe başvurun](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Arama hizmeti REST API başvurusu](https://docs.microsoft.com/rest/api/searchservice/)