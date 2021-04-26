---
title: C++ istemci kitaplığı ile Azure depolama kaynaklarını listeleme
description: Kapsayıcıları, Blobları, kuyrukları, tabloları ve varlıkları numaralandırmak için C++ için Microsoft Azure Depolama Istemci kitaplığı ' nda listeleme API 'Lerini nasıl kullanacağınızı öğrenin.
author: twooley
ms.author: twooley
ms.date: 01/23/2017
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 72b08a79ead90207c48c4fa682b708a0627b5df3
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279638"
---
# <a name="list-azure-storage-resources-in-c"></a>Azure Depolama Kaynaklarını C++ dilinde listeleme

Listeleme işlemleri, Azure depolama ile birçok geliştirme senaryosunda önemli değildir. Bu makalede, C++ için Microsoft Azure Depolama Istemci kitaplığı 'nda belirtilen liste API 'Lerini kullanarak Azure Storage 'da nesnelerin nasıl etkili bir şekilde numaralandırılacağı açıklanır.

> [!NOTE]
> Bu kılavuz, [NuGet](https://www.nuget.org/packages/wastorage) veya [GitHub](https://github.com/Azure/azure-storage-cpp)aracılığıyla kullanılabilen C++ sürüm 2. x Için Azure Storage istemci kitaplığı 'nı hedefler.

Depolama Istemci kitaplığı, Azure Storage 'daki nesneleri listelemek veya sorgulamak için çeşitli yöntemler sağlar. Bu makalede aşağıdaki senaryolar ele alınmaktadır:

* Bir hesaptaki kapsayıcıları listeleme
* Kapsayıcıda veya sanal blob dizininde Blobları listeleme
* Bir hesaptaki kuyrukları listeleme
* Bir hesaptaki tabloları listeleme
* Bir tablodaki varlıkları sorgulama

Bu yöntemlerin her biri farklı senaryolar için farklı aşırı yüklemeler kullanılarak gösterilmektedir.

## <a name="asynchronous-versus-synchronous"></a>Zaman uyumsuz ve zaman uyumlu

C++ için depolama Istemci kitaplığı, [C++ Rest kitaplığının](https://github.com/Microsoft/cpprestsdk)üzerine oluşturulduğundan, zaman uyumsuz işlemleri [pplx:: Task](https://microsoft.github.io/cpprestsdk/classpplx_1_1task.html)kullanarak bir şekilde destekliyoruz. Örnek:

```cpp
pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;
```

Zaman uyumlu işlemler, karşılık gelen zaman uyumsuz işlemleri kaydırır:

```cpp
list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
{
    return list_blobs_segmented_async(token).get();
}
```

Birden çok iş parçacığı uygulaması veya hizmetleriyle çalışıyorsanız, eşitleme API 'lerini çağırmak için bir iş parçacığı oluşturmak yerine, zaman uyumsuz API 'Leri doğrudan kullanmanızı öneririz. bu sayede performansı önemli ölçüde etkiler.

## <a name="segmented-listing"></a>Bölümlenmiş liste

Bulut depolamanın ölçeği, bölümlenmiş liste gerektirir. Örneğin, bir Azure Blob kapsayıcısında veya bir Azure tablosundaki milyardan fazla varlıkta bir milyon blobunuz olabilir. Bunlar teorik sayı değildir, ancak gerçek müşteri kullanım durumları.

Bu nedenle, tüm nesneleri tek bir yanıtta listelemek pratik değildir. Bunun yerine, sayfalama kullanarak nesneleri listeleyebilirsiniz. Liste API 'lerinin her birinde *kesimli* bir aşırı yükleme vardır.

Bölümlenmiş bir listeleme işleminin yanıtı şunları içerir:

* liste API 'sine tek bir çağrı için döndürülen sonuç kümesini içeren *_segment*.
* sonraki sonuçların bir sonraki sayfasına ulaşmak için bir sonraki çağrıya geçirilen *continuation_token*. Döndürülecek daha fazla sonuç olmadığında devamlılık belirteci null olur.

Örneğin, bir kapsayıcıdaki tüm Blobları listelemek için tipik bir çağrı aşağıdaki kod parçacığı gibi görünebilir. Kod, [örneklerimizde](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted.cpp)mevcuttur:

```cpp
// List blobs in the blob container
azure::storage::continuation_token token;
do
{
    azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}

    token = segment.continuation_token();
}
while (!token.empty());
```

Bir sayfada döndürülen sonuç sayısının, her API 'nin aşırı yükteki *max_results* parametresi tarafından denetlenebileceğini unutmayın, örneğin:

```cpp
list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
    blob_listing_details::values includes, int max_results, const continuation_token& token,
    const blob_request_options& options, operation_context context)
```

*Max_results* parametresini belirtmezseniz, en fazla 5000 sonuç olan varsayılan değer tek bir sayfada döndürülür.

Ayrıca, Azure Tablo depolamaya yönelik bir sorgu, devamlılık belirteci boş olmasa bile, belirttiğiniz *max_results* parametresinin değerinden daha az kayıt döndürebileceğini unutmayın. Bir nedenden dolayı sorgunun beş saniye içinde tamamlanmamasının nedeni olabilir. Devamlılık belirteci boş olmadığı sürece sorgu devam etmelidir ve kodunuz, kesim sonuçlarının boyutunu varsaymamalıdır.

Çoğu senaryo için önerilen kodlama deseninin, listeleme veya sorgulama üzerinde açık ilerleme durumu ve hizmetin her bir isteğe nasıl yanıt verdiği ile ilgili olarak bölümlenmiş listesi verilmiştir. Özellikle C++ uygulamaları veya hizmetleri için, liste ilerleme durumunun alt düzey denetimi belleği ve performansı denetlemeye yardımcı olabilir.

## <a name="greedy-listing"></a>Greedy listeleme

C++ için depolama Istemcisi kitaplığı 'nın (sürümler 0.5.0 Preview ve önceki sürümler), aşağıdaki örnekte olduğu gibi, tablolar ve kuyruklar için bölünmeyen listeleme API 'Leri dahil olmak üzere daha önceki sürümleri:

```cpp
std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
std::vector<table_entity> execute_query(const table_query& query) const;
std::vector<cloud_queue> list_queues() const;
```

Bu yöntemler, kesimli API 'lerin sarmalayıcıları olarak uygulanmıştır. Bölümlenmiş listenin her yanıtı için, kod sonuçları bir Vector öğesine eklenmiş ve tam kapsayıcılar tarandıktan sonra tüm sonuçları döndürdü.

Bu yaklaşım, depolama hesabı veya tablosu az sayıda nesne içerdiğinde işe alabilir. Ancak, nesne sayısında bir artış ile, tüm sonuçlar bellekte kaldığı için gereken bellek sınır olmadan artabilir. Tek bir listeleme işlemi çok uzun sürebilir, bu süre içinde çağıranın ilerleme durumu hakkında hiçbir bilgi yoktur.

SDK 'daki bu doyumsuz listeleme API 'leri C#, Java veya JavaScript Node.js ortamında bulunmamaktadır. Bu doyumsuz API 'lerini kullanmanın olası sorunlarından kaçınmak için, bunları sürüm 0.6.0 Önizleme sürümünde kaldırdık.

Kodunuz bu doyumsuz API 'lerini arıyorsanız:

```cpp
std::vector<azure::storage::table_entity> entities = table.execute_query(query);
for (auto it = entities.cbegin(); it != entities.cend(); ++it)
{
    process_entity(*it);
}
```

Daha sonra, bölümlenmiş listeleme API 'Lerini kullanmak için kodunuzu değiştirmelisiniz:

```cpp
azure::storage::continuation_token token;
do
{
    azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
    for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        process_entity(*it);
    }

    token = segment.continuation_token();
} while (!token.empty());
```

Segmentin *max_results* parametresini belirterek, uygulamanızın performans konularını karşılamak için istek sayısı ve bellek kullanımı arasında denge yapabilirsiniz.

Ayrıca, bölümlenmiş liste API 'Leri kullanıyorsanız, ancak verileri bir "Greedy" stilinde bir yerel koleksiyonda depoluyorsanız, verileri yerel bir koleksiyonda depolamayı ölçeklendirerek dikkatle işlemek için kodunuzu yeniden düzenlemeniz de önemle tavsiye ederiz.

## <a name="lazy-listing"></a>Yavaş listeleme

Doyumsuz, olası sorunları ortaya çıkarsa da kapsayıcıda çok fazla nesne yoksa bu, kullanışlı bir yöntemdir.

Ayrıca C# veya Oracle Java SDK 'Ları kullanıyorsanız, belirli bir uzaklığında verilerin yalnızca gerekli olması durumunda getirileceği bir geç stil listesi sunan, sıralanabilir programlama modeliyle ilgili bilgi sahibi olmanız gerekir. C++ ' da, yineleyici tabanlı şablon da benzer bir yaklaşım sağlar.

Örnek olarak **list_blobs** kullanan tipik bir yavaş listeleme API 'si şöyle görünür:

```cpp
list_blob_item_iterator list_blobs() const;
```

Yavaş liste modelini kullanan tipik bir kod parçacığı şöyle görünebilir:

```cpp
// List blobs in the blob container
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        process_blob(it->as_blob());
    }
    else
    {
        process_directory(it->as_directory());
    }
}
```

Yavaş listenin yalnızca zaman uyumlu modda kullanılabileceğini unutmayın.

Doyumsuz listelemesi ile karşılaştırıldığında, yavaş listeleme yalnızca gerektiğinde verileri getirir. Bu bölümde, Azure Storage 'dan verileri, yalnızca sonraki Yineleyici bir sonraki kesimye geldiğinde getirir. Bu nedenle, bellek kullanımı sınırlı bir boyutla denetlenir ve işlem hızlıdır.

Yavaş listeleme API 'Leri, 2.2.0 sürümündeki C++ için depolama Istemci kitaplığı 'na dahildir.

## <a name="conclusion"></a>Sonuç

Bu makalede, C++ için depolama Istemci kitaplığındaki çeşitli nesnelere yönelik API 'Leri listelemek için farklı aşırı yüklemeler ele alınmıştır. Özetlemek gerekirse:

* Zaman uyumsuz API 'Lerin birden çok iş parçacığı senaryosu altında kullanılması önemle önerilir.
* Kesimli liste çoğu senaryo için önerilir.
* Yavaş listeleme, kitaplıkta zaman uyumlu senaryolarda uygun bir sarmalayıcı olarak sağlanır.
* Greedy listesi önerilmez ve kitaplıktan kaldırılmıştır.

## <a name="next-steps"></a>Sonraki adımlar

Azure depolama ve C++ için Istemci kitaplığı hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın.

* [C++ ' dan blob depolamayı kullanma](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [C++ ' dan Tablo Depolamayı kullanma](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [C++’tan Kuyruk Depolama kullanma](../queues/storage-c-plus-plus-how-to-use-queues.md)
* [C++ API belgeleri için Azure Storage Istemci kitaplığı.](https://azure.github.io/azure-storage-cpp/)
* [Azure Depolama Ekibi Blogu](/archive/blogs/windowsazurestorage/)
* [Azure depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/)