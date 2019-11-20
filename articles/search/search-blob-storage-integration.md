---
title: Azure Blob depolamaya tam metin araması ekleme
titleSuffix: Azure Cognitive Search
description: Azure bilişsel bir şekilde tam metin arama dizini oluştururken içeriği ayıklayın ve Azure bloblarına yapı ekleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496487"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Azure Bilişsel Arama kullanarak Azure Blob verilerine tam metin araması ekleme

Azure Blob depolamada depolanan çeşitli içerik türleri arasında arama yapmak zor bir sorun olabilir. Ancak, [Azure bilişsel arama](search-what-is-azure-search.md)kullanarak bloblarınızın içeriğini yalnızca birkaç tıklamayla dizinleyerek ve arayabilirsiniz. Azure Bilişsel Arama, dizin oluşturma için veri kaynağı kullanan yetenekleri ekleyen bir [*BLOB Indexer*](search-howto-indexing-azure-blob-storage.md) aracılığıyla blob depolamayı dizine almayı sağlayan yerleşik tümleştirmeye sahiptir.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Blob verilerine tam metin araması eklemenin anlamı

Azure Bilişsel Arama, arama hizmetinizde barındırılan Kullanıcı tanımlı dizinlerin üzerinde çalışan dizin oluşturma ve sorgu motorları sağlayan bir bulut arama hizmetidir. Ara değerli içeriğinizi bulutta sorgu altyapısından birlikte bulmak, performans için gerekli olan sonuçları hızlı bir şekilde döndürmek için gereklidir.

Azure Bilişsel Arama, dizin oluşturma katmanında Azure Blob depolama ile tümleşir, bu da blob içeriğinizi *ters dizinler* ve serbest form metin sorgularını ve Filtre ifadelerini destekleyen diğer sorgu yapılarına dizinli arama belgeleri olarak içeri aktarın . Blob içeriğiniz bir arama dizininde dizinlendiği için, blob içeriğine erişim Azure Bilişsel Arama 'deki sorgu özelliklerinin tam aralığından yararlanabilir.

Dizin oluşturulup doldurulduktan sonra, blob kapsayıcınızda bağımsız olarak bulunur, ancak dizin oluşturma işlemlerini yeniden çalıştırıp Dizin oluşturmayı temel kapsayıcıda yapılan değişikliklerle yenileyebilirsiniz. Tek bloblarda zaman damgası bilgileri değişiklik algılama için kullanılır. Yenileme mekanizması olarak zamanlanmış yürütme ya da isteğe bağlı dizin oluşturma için kabul edebilirsiniz.

Azure Blob depolama alanında, tek bir kapsayıcıda bulunan bloblarınızın girdileri vardır. Blob 'lar neredeyse her türlü metin verisi olabilir. Bloblarınız görüntü içeriyorsa, görüntülerden metin oluşturup çıkarmak için bir [BLOB dizin oluşturmaya AI zenginleştirme](search-blob-ai-integration.md) ekleyebilirsiniz.

Çıktı, istemci uygulamalarında hızlı metin arama, alma ve araştırma için kullanılan her zaman bir Azure Bilişsel Arama dizinidir. Between, dizin oluşturma ardışık düzen mimarisidir. İşlem hattı, bu makalede daha fazla açıklanılan *Dizin Oluşturucu* özelliğine dayanır.

## <a name="start-with-services"></a>Hizmetlerle başlayın

Azure Bilişsel Arama ve Azure Blob depolamaya ihtiyacınız vardır. BLOB depolama alanında, kaynak içerik sağlayan bir kapsayıcıya ihtiyacınız vardır.

Doğrudan depolama hesabı portalı sayfanızda başlayabilirsiniz. Sol Gezinti sayfasında, **BLOB hizmeti** altında **Azure bilişsel arama Ekle** ' ye tıklayarak yeni bir hizmet oluşturun veya var olan bir hizmeti seçin. 

Depolama hesabınıza Azure Bilişsel Arama eklediğinizde, blob verilerini indekslemek için standart işlemi izleyebilirsiniz. Kolay bir başlangıç girişi için Azure Bilişsel Arama **veri alma** Sihirbazı 'nı öneririz veya Postman gibi bir araç kullanarak REST API 'leri çağırabilirsiniz. Bu öğretici, Postman 'da REST API çağırma adımlarında size yol gösterir: [Azure bilişsel arama 'de yarı yapılandırılmış verileri (JSON blob 'ları) dizin ve arama](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Blob Dizin Oluşturucu kullanma

*Dizin Oluşturucu* , verileri örnekleme, meta veri verileri okuma, verileri alma ve yerel biçimlerdeki verileri, sonraki içeri aktarma için JSON belgelerine serileştirmede iç mantığa sahip olan veri kaynağı kullanan bir alt hizmettir. 

Azure depolama 'daki Bloblar, [azure bilişsel arama blob Storage Indexer](search-howto-indexing-azure-blob-storage.md)kullanılarak dizine alınır. **Veri alma** Sihirbazı 'nı, bir REST API veya .NET SDK 'sını kullanarak bu dizin oluşturucuyu çağırabilirsiniz. Kod içinde, bu dizin oluşturucuyu türü ayarlayarak ve bir blob kapsayıcısı ile birlikte bir Azure depolama hesabı içeren bağlantı bilgilerini sağlayarak kullanırsınız. Daha sonra bir parametre olarak geçirebilen veya bir dosya türü uzantısı üzerinde filtreleyerek sanal bir dizin oluşturarak bloblarınızı alt kümelayabilirsiniz.

Bir Dizin Oluşturucu, içeriği incelemek için bir blob açan "belgeyi çözme" yapmaz. Veri kaynağına bağlandıktan sonra, işlem hattının ilk adımı vardır. Blob verileri için PDF, Office belgeleri ve diğer içerik türlerinin algılandığı yerdir. Metin ayıklama ile belge çözme ücretsizdir. Bloblarınız görüntü içeriği içeriyorsa, [AI zenginleştirme](search-blob-ai-integration.md)eklemediğiniz takdirde görüntüler yok sayılır. Standart dizin oluşturma yalnızca metin içeriği için geçerlidir.

Blob Indexer yapılandırma parametreleriyle birlikte gelir ve temel alınan veriler yeterli bilgi sağlıyorsa değişiklik izlemeyi destekler. [Azure bilişsel arama blob Storage Indexer](search-howto-indexing-azure-blob-storage.md)'ın temel işlevleri hakkında daha fazla bilgi edinebilirsiniz.

### <a name="supported-content-types"></a>Desteklenen içerik türleri

Bir kapsayıcı üzerinde bir blob Indexer çalıştırarak, aşağıdaki içerik türlerinden metin ve meta verileri tek bir sorgu ile ayıklayabilirsiniz:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Blob meta verileri dizinleniyor

Herhangi bir içerik türünün Blobları arasında sıralama yapmayı kolaylaştıran yaygın bir senaryo, her blob için hem özel meta verileri hem de sistem özelliklerini dizinlemek. Bu şekilde, tüm Bloblar için bilgiler, arama hizmetinizde bir dizinde depolanan belge türünden bağımsız olarak dizinlenir. Yeni dizininizi kullanarak, tüm blob depolama içeriklerine göre sıralama, filtreleme ve model işleme devam edebilirsiniz.

### <a name="indexing-json-blobs"></a>JSON bloblarını dizine alma
Dizin oluşturucular, JSON içeren bloblarda bulunan yapılandırılmış içerikleri ayıklamak üzere yapılandırılabilir. Bir Dizin Oluşturucu, JSON bloblarını okuyabilir ve yapılandırılmış içeriği bir arama belgesinin uygun alanlarıyla ayrıştırır. Dizin oluşturucular Ayrıca, bir dizi JSON nesnesi içeren Bloblar alabilir ve her öğeyi ayrı bir arama belgesiyle eşler. Dizin Oluşturucu tarafından oluşturulan JSON nesnesinin türünü etkilemek için bir ayrıştırma modu ayarlayabilirsiniz.

## <a name="search-blob-content-in-a-search-index"></a>Arama dizininde blob içeriğini ara 

Bir dizin oluşturma işleminin çıktısı, bir istemci uygulamasında ücretsiz metin ve filtrelenmiş sorgular kullanan etkileşimli araştırma için kullanılan bir arama dizinidir. İçeriğin ilk araştırılması ve doğrulanması için, belge yapısını incelemek üzere portalda [Arama Gezgini](search-explorer.md) ile başlamasını öneririz. Arama Gezgini 'nde [basit sorgu söz dizimi](query-simple-syntax.md), [tam sorgu söz](query-lucene-syntax.md)dizimi ve [filtre ifadesi sözdizimini](query-odata-filter-orderby-syntax.md) kullanabilirsiniz.

Daha kalıcı bir çözüm, sorgu girişleri toplamaktır ve yanıtı bir istemci uygulamasında arama sonuçları olarak sunar. Aşağıdaki C# öğreticide bir arama uygulamasının nasıl oluşturulacağı açıklanmaktadır: [Azure bilişsel arama ilk uygulamanızı oluşturma](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure portal (Azure Blob depolama) ile Blobları karşıya yükleme, indirme ve listeleme](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Blob Dizin oluşturucuyu ayarlama (Azure Bilişsel Arama)](search-howto-indexing-azure-blob-storage.md) 
