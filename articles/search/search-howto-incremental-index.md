---
title: Artımlı dizin ekleme (Önizleme)
titleSuffix: Azure Cognitive Search
description: Bilişsel Beceri 'de denetimli işleme için, değişiklik izlemeyi ve korunan içeriğin durumunu koru özelliğini etkinleştirin. Bu özellik şu anda genel önizleme aşamasındadır.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 09defe9648208e2300594169add990d4bcbd7a39
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112580"
---
# <a name="how-to-set-up-incremental-indexing-of-enriched-documents-in-azure-cognitive-search"></a>Azure Bilişsel Arama zenginleştirilmiş belgelerin artımlı dizin oluşturma özelliği nasıl ayarlanır

> [!IMPORTANT] 
> Artımlı dizin oluşturma şu anda genel önizlemededir. Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API sürüm 2019-05-06-önizleme](search-api-preview.md) bu özelliği sağlar. Şu anda portal veya .NET SDK desteği yok.

Bu makalede, bir Azure Bilişsel Arama zenginleştirme işlem hattı aracılığıyla taşınan belgelerde, desteklenen veri kaynaklarından herhangi birini artımlı olarak dizinlemek için durum ve önbelleğe alma işlemlerini nasıl ekleyeceğiniz gösterilir. Varsayılan olarak, bir beceri durum bilgisiz değildir ve kompozisyonunun herhangi bir bölümünü değiştirmek, dizin oluşturucunun tam yeniden çalıştırma işlemini gerektirir. Artımlı dizin oluşturma ile, Dizin Oluşturucu, işlem hattının hangi bölümlerinin değiştiğini belirleyebilir, değişmeyen parçalar için mevcut zenginleştirmelerin yeniden kullanımını ve değişiklik yapan adımlarla zenginleştirmeyi yeniden gözden verebilir. Önbelleğe alınmış içerik Azure Storage 'a yerleştirilir.

Dizinleyicilerle ilgili bilgi sahibi değilseniz, [dizin oluşturucuya genel bakış](search-indexer-overview.md) ile başlayın ve ardından zenginleştirme işlem hatları hakkında bilgi edinmek için [becerileri](cognitive-search-working-with-skillsets.md) 'ye devam edin. Temel kavramlar hakkında daha fazla arka plan için bkz. [artımlı dizin oluşturma](cognitive-search-incremental-indexing-conceptual.md).

## <a name="modify-an-existing-indexer"></a>Mevcut bir dizin oluşturucuyu değiştirme

Mevcut bir dizin oluşturucunuz varsa, artımlı Dizin oluşturmayı etkinleştirmek için bu adımları izleyin.

### <a name="step-1-get-the-indexer"></a>1\. Adım: Dizin oluşturucuyu alın

Gerekli veri kaynağına ve dizine zaten tanımlanmış olan geçerli, mevcut bir Dizin Oluşturucu ile başlayın. Dizin oluşturucunun çalıştırılabilir olması gerekir. Bir API istemcisi kullanarak, dizin oluşturucunun geçerli yapılandırmasını almak için, artımlı dizin eklemek istediğiniz bir [get isteği](https://docs.microsoft.com/rest/api/searchservice/get-indexer) oluşturun.

```http
GET https://[service name].search.windows.net/indexers/[your indexer name]?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-2-add-the-cache-property"></a>2\. Adım: cache özelliğini ekleme

`cache` özelliğini dizin oluşturucuya eklemek için GET isteğinden yanıtı düzenleyin. Cache nesnesi yalnızca tek bir özellik gerektirir ve bu bağlantı dizesi bir Azure depolama hesabına ait olur.

```json
    "cache": {
        "storageConnectionString": "[your storage connection string]"
    }
```

### <a name="step-3-reset-the-indexer"></a>3\. Adım: Dizin oluşturucuyu sıfırlama

> [!NOTE]
> Dizin oluşturucunun sıfırlanması, içerik önbelleğe alınabilmesi için veri kaynağınızdaki tüm belgelerin yeniden işlenmesine neden olur. Tüm bilişsel zenginler, tüm belgelerde yeniden çalıştırılır.
>

Tüm belgelerin tutarlı bir durumda olduğundan emin olmak için mevcut dizin oluşturucular için artımlı dizin oluşturma özelliği, dizin oluşturucunun sıfırlanması gerekir. [REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)kullanarak Dizin oluşturucuyu sıfırlayın.

```http
POST https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
```

### <a name="step-4-save-the-updated-definition"></a>4\. Adım: güncelleştirilmiş tanımı kaydetme

Dizin Oluşturucu tanımını bir PUT isteğiyle güncelleştirme, isteğin gövdesi güncelleştirilmiş Dizin Oluşturucu tanımını içermelidir.

```http
PUT https://[service name].search.windows.net/indexers/[your indexer name]/reset?api-version=2019-05-06-Preview
Content-Type: application/json
api-key: [admin key]
{
    "name" : "your indexer name",
    ...
    "cache": {
        "storageConnectionString": "[your storage connection string]",
        "enableReprocessing": true
    }
}
```

Artık Indexer üzerinde başka bir GET isteği verirseniz, hizmetten gelen yanıt Cache nesnesine bir `cacheId` özelliği içerecektir. `cacheId`, bu Dizin Oluşturucu tarafından işlenen her belge için tüm önbelleğe alınmış sonuçları ve ara durumu içeren kapsayıcının adıdır.

## <a name="enable-incremental-indexing-on-new-indexers"></a>Yeni Dizin oluşturucular üzerinde artımlı Dizin oluşturmayı etkinleştir

Yeni bir Dizin Oluşturucu için artımlı dizin oluşturmak üzere, Dizin Oluşturucu tanımı yüküne `cache` özelliğini ekleyin. API 'nin `2019-05-06-Preview` sürümünü kullandığınızdan emin olun.

## <a name="overriding-incremental-indexing"></a>Artımlı Dizin oluşturmayı geçersiz kılma

Yapılandırıldığında, artımlı dizinleme, dizin oluşturma işlem hattınızda ve sürücüler belgelerinde yaptığınız değişiklikleri, dizin ve projeksiyonlar genelinde nihai tutarlılık halinde izler. Bazı durumlarda, dizin oluşturucunun dizin oluşturma işlem hattının bir güncelleştirmesi sonucunda ek iş gerçekleştirmediğinden emin olmak için bu davranışı geçersiz kılmanız gerekir. Örneğin, DataSource bağlantı dizesinin güncelleştirilmesi, DataSource değiştiği için dizin oluşturucunun sıfırlanması ve tüm belgelerin yeniden dizinlenmesini gerektirir. Ancak bağlantı dizesini yalnızca yeni bir anahtarla güncelleştiriyorsanız, değişikliğin mevcut belgelerde herhangi bir güncelleştirme ile sonuçlanabileceğini istemezsiniz. Bunun tersine, dizin oluşturucunun, dizin oluşturma işlem hattında hiçbir değişiklik yapılmasa bile önbelleği ve zenginleştirme belgelerini geçersiz kılmak isteyebilirsiniz. Örneğin, bir özel yeteneği yeni bir modelle yeniden dağıtmanız ve tüm belgeleriniz üzerinde beceri yeniden çalıştırma işlemini istediyseniz, Dizin oluşturucuyu geçersiz kılmak isteyebilirsiniz.

### <a name="override-reset-requirement"></a>Sıfırlama gereksinimini geçersiz kıl

Dizin oluşturma işlem hattında değişiklik yaparken, önbelleğin geçersiz doğrulanmasına neden olan değişiklikler Dizin Oluşturucu sıfırlamayı gerektirir. Dizin Oluşturucu işlem hattına bir değişiklik yapıyorsanız ve değişiklik izlemenin önbelleği geçersiz kılmak istemiyorsanız, Dizin Oluşturucu veya veri kaynağı üzerindeki işlemler için `ignoreResetRequirement` QueryString parametresini `true` olarak ayarlamanız gerekir.

### <a name="override-change-detection"></a>Değişiklik algılamayı geçersiz kıl

Beceri üzerinde güncelleştirmeler yapıldığında, belgeler tutarsız olarak işaretlenmek üzere, örneğin, yetenek yeniden dağıtıldığında özel bir beceri URL 'sini güncelleştirmek için, `disableCacheReprocessingChangeDetection` sorgu dizesi parametresini beceri güncelleştirmelerinde `true` olarak ayarlayın.

### <a name="force-change-detection"></a>Değişiklik algılamayı zorla

Kaynak oluşturma işlem hattının, özel bir yeteneğin yeni bir sürümünü dağıtma gibi bir dış varlığa yapılan değişikliği tanımasını istediğinizde, Beceri ' yi güncelleştirmeniz ve özellikle de zorlayacak URL olan beceri tanımını düzenleyerek belirli beceriye "dokunmanız" gerekir. değişiklik algılama ve bu beceri için önbelleği geçersiz kılın.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, becerileri içeren dizin oluşturucular için artımlı dizin oluşturma ele alınmaktadır. Bilginizi daha fazla yuvarlamak için, Azure Bilişsel Arama 'daki tüm dizin oluşturma senaryolarına uygun olarak, genel olarak yeniden dizin oluşturma hakkındaki makaleleri gözden geçirin.

+ [Azure bilişsel arama dizinini yeniden oluşturma](search-howto-reindex.md). 
+ [Azure bilişsel arama 'de büyük veri kümelerini Dizin](search-howto-large-index.md)oluşturma. 
