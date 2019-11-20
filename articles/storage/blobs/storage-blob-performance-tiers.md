---
title: BLOB depolama performans katmanlarını engelle — Azure depolama
description: Azure blok blob depolaması için Premium ve standart performans katmanları arasındaki farkı açıklar.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 64efd1a02b903ec3874066f6c663b86a8080f746
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932264"
---
# <a name="performance-tiers-for-block-blob-storage"></a>Blok Blobu depolaması için performans katmanları

Kuruluşlar, performansa duyarlı, bulutta yerel uygulamalar dağıtdığından, farklı performans düzeylerinde uygun maliyetli veri depolama seçeneklerine sahip olmak önemlidir.

Azure Blok Blobu depolama iki farklı performans katmanı sunar:

- **Premium**: yüksek işlem hızları ve tek basamaklı tutarlı depolama gecikmesi için iyileştirildi
- **Standart**: yüksek kapasite ve yüksek aktarım hızı için iyileştirilmiş

Farklı performans katmanları için aşağıdaki noktalar geçerlidir:

| Alan |Standart performans  |Premium performans  |
|---------|---------|---------|
|Bölge kullanılabilirliği     |   Tüm bölgeler      | [Select bölgelerinde](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|Desteklenen [depolama hesabı türleri](../common/storage-account-overview.md#types-of-storage-accounts)     |     Genel amaçlı v2, BlobStorage, genel amaçlı v1    |    BlockBlobStorage     |
|[Yüksek verimlilik blok bloblarını](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) destekler     |    Evet, 4 MIB PutBlock veya PutBlob boyutlarından daha fazla     |    Evet, 256 KiB PutBlock veya PutBlob boyutlarından daha büyük    |
|Yedeklilik     |     Bkz. [depolama hesabı türleri](../common/storage-account-overview.md#types-of-storage-accounts)   |  Şu anda yalnızca yerel olarak yedekli depolama (LRS) ve bölge-redudant depolama (ZRS) destekleniyor<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup> Bölgesel olarak yedekli depolama (ZRS), Premium performans bloğu BLOB depolama hesapları için seçim bölgelerinde kullanılabilir.</div>

Maliyet açısından, Premium performans, bu iş yükleri için [toplam depolama maliyetini düşürmenize](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) yardımcı olmak üzere yüksek işlem ücretleri olan uygulamalar için iyileştirilmiş fiyatlandırma sağlar.

## <a name="premium-performance"></a>Premium performans

Premium performans bloğu blob depolaması, verilerin yüksek performanslı donanımlar aracılığıyla kullanılabilmesini sağlar. Veriler, düşük gecikme süresi için iyileştirilmiş katı hal sürücülerinde (SSD) depolanır. SSD 'Ler geleneksel sabit sürücülerle karşılaştırıldığında daha yüksek aktarım hızı sağlar.

Premium performans depolaması, hızlı ve tutarlı yanıt süreleri gerektiren iş yükleri için idealdir. Çok sayıda küçük işlem gerçekleştiren iş yükleri için idealdir. Örnek iş yükleri şunlardır:

- **Etkileşimli iş yükleri**. Bu iş yükleri, e-ticaret ve eşleme uygulamaları gibi anlık güncelleştirmeler ve Kullanıcı geri bildirimi gerektirir. Örneğin, bir e-ticaret uygulamasında daha az sıklıkta görüntülenen öğeler muhtemelen önbelleğe alınmaz. Ancak, bu müşterilerin isteğe bağlı olarak müşteriye anında gösterilmesi gerekir.

- **Analiz**. IoT senaryosunda, buluta her saniye çok sayıda küçük yazma işlemi itilmiş olabilir. Büyük miktarlarda veri alınabilir, analiz amacıyla toplanır ve hemen hemen silinir. Premium Blok Blobu depolamanın yüksek alım özellikleri, bu iş yükü türü için verimli hale getirir.

- **Yapay zeka/makine öğrenimi (AI/ml)** . AI/ML, görseller, konuşma ve metin gibi farklı veri türlerinin tüketimine ve işlenmesiyle ilgilidir. Bu yüksek performanslı bilgi işlem iş yükü, veri analizi için hızlı yanıt ve verimli alma süreleri gerektiren büyük miktarda verilerle ilgilidir.

- **Veri dönüştürme**. Verilerin sabit düzenlemesini, değiştirilmesini ve dönüştürülmesini gerektiren süreçler anında güncelleştirmeler gerektirir. Doğru veri gösterimi için bu verilerin tüketicilerinin hemen yansıtıldığı bu değişiklikleri görmeniz gerekir.

## <a name="standard-performance"></a>Standart performans

Standart performans, verileri en düşük maliyetli şekilde depolamak için farklı [erişim katmanlarını](storage-blob-storage-tiers.md) destekler. Büyük veri kümelerinde yüksek kapasite ve yüksek aktarım hızı için en iyi duruma getirilmiştir.

## <a name="migrate-from-standard-to-premium"></a>Standart 'ten Premium 'a geçiş

Mevcut bir standart performans depolama hesabını Premium performansa sahip bir blok BLOB depolama hesabına dönüştüremezsiniz. Premium performans depolama hesabına geçiş yapmak için bir BlockBlobStorage hesabı oluşturmanız ve verileri yeni hesaba geçirmeniz gerekir. Daha fazla bilgi için bkz. [blok Blobstorage hesabı oluşturma](storage-blob-create-account-block-blob.md).

Blob 'ları depolama hesapları arasında kopyalamak için [AzCopy](../common/storage-use-azcopy-blobs.md) komut satırı aracının en son sürümünü kullanabilirsiniz. Azure Data Factory gibi diğer araçlar da veri taşıma ve dönüştürme için de kullanılabilir.

## <a name="blob-lifecycle-management"></a>Blob yaşam döngüsü yönetimi

Blob Storage yaşam döngüsü yönetimi, zengin, kural tabanlı bir ilke sunar:

- **Premium**: yaşam döngüsünün sonundaki verilerin süresi doluyor.
- **Standart**: verileri en iyi erişim katmanına geçirin ve yaşam döngüsünün sonunda verileri sona erer.

Daha fazla bilgi için bkz. [Azure Blob depolama yaşam döngüsünü yönetme](storage-lifecycle-management-concepts.md).

Premium Blok Blobu depolama hesabında depolanan verileri sık erişimli, seyrek erişimli ve arşiv katmanları arasında taşıyamazsınız. Ancak, blob 'ları bir blok BLOB depolama hesabından *farklı* bir hesaptaki sık erişimli erişim katmanına kopyalayabilirsiniz. Verileri farklı bir hesaba kopyalamak için [URL 'Den put bloğunu](/rest/api/storageservices/put-block-from-url) veya [AzCopy ile v10 arasındaki](../common/storage-use-azcopy-v10.md)komutunu kullanın. **URL API 'Den put bloğu** , sunucudaki verileri eşzamanlı olarak kopyalar. Çağrı yalnızca tüm veriler özgün sunucu konumundan hedef konuma taşındıktan sonra tamamlanır.

## <a name="next-steps"></a>Sonraki adımlar

GPv2 ve BLOB depolama hesaplarında sık erişimli, seyrek erişimli ve arşivi değerlendirin.

- [Blob verilerini arşiv katmanından yeniden doldurma hakkında bilgi edinin](storage-blob-rehydration.md)
- [Azure Depolama ölçümlerini etkinleştirerek geçerli depolama hesaplarınızın kullanımını değerlendirme](../common/storage-enable-and-view-metrics.md)
- [Blob depolama ve GPv2 hesaplarında bölgeye göre sık erişimli, seyrek erişimli ve arşiv fiyatlarını denetleme](https://azure.microsoft.com/pricing/details/storage/)
- [Veri aktarımı fiyatlandırmasını denetleme](https://azure.microsoft.com/pricing/details/data-transfers/)
