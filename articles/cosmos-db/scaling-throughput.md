---
title: Azure Cosmos DB 'da ölçek işleme
description: Bu makalede, Azure Cosmos DB üretilen iş esnek nasıl ölçeklenirken açıklanmaktadır
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 29a92f04a1d36004fa082bfafe2310f9e0e3e5c6
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467594"
---
# <a name="globally-scale-provisioned-throughput"></a>Sağlanan aktarım hızını küresel olarak ölçeklendirme 

Azure Cosmos DB, sağlanan aktarım hızı, istek birimi/saniye (RU/s ya da çoğul biçim) olarak temsil edilir. Ru, aşağıdaki görüntüde gösterildiği gibi, Cosmos kapsayıcısında hem okuma hem de yazma işlemlerinin maliyetini ölçer:

![İstek Birimleri](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Bir Cosmos kapsayıcısına veya Cosmos veritabanına RUs sağlayabilirsiniz. Kapsayıcıda sağlanan RUs, bu kapsayıcıda gerçekleştirilen işlemler için özel olarak kullanılabilir. Bir veritabanında sağlanan RUs, bu veritabanındaki tüm kapsayıcılar arasında paylaşılır (yalnızca özel olarak atanmış bir kapsayıcılar hariç).

Esnek ölçeklendirme sağlanan aktarım hızı için, sağlanan RU/s 'yi dilediğiniz zaman artırabilir veya azaltabilirsiniz. Daha fazla bilgi için bkz. [nasıl yapılır sağlama işleme](set-throughput.md) ve esnek ölçekli Cosmos kapsayıcıları ve veritabanları. Küresel olarak ölçeklendirme performansı için, Cosmos hesabınızdan bölge ekleyebilir veya istediğiniz zaman kaldırabilirsiniz. Daha fazla bilgi için bkz. [veritabanı hesabınızdan bölge ekleme/kaldırma](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Birden çok bölgeyi Cosmos hesabıyla ilişkilendirmek, dünyanın dört bir yanındaki gecikme süresi ve [yüksek kullanılabilirlik](high-availability.md) elde etmek için birçok senaryoda önemlidir.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Sağlanan aktarım hızı bölgeler arasında dağıtılır

Cosmos kapsayıcısı (veya veritabanı) üzerinde *' r '* ru 'yi sağlarsanız, Cosmos DB Cosmos hesabınızla ilişkili *her* bölgede *' r '* ru 'nin kullanılabilir olmasını sağlar. Hesabınıza yeni bir bölge eklediğiniz her seferinde, Cosmos DB otomatik olarak yeni eklenen bölgede *' R '* ru 'yi sağlar. Cosmos kapsayıcınıza karşı gerçekleştirilen işlemler, her bölgede *' R '* ru 'yi almak için garanti edilir. Belirli bir bölgeye seçmeli olarak RUs atayamazsınız. Cosmos kapsayıcısı (veya veritabanı) üzerinde sağlanan RUs, Cosmos hesabınızla ilişkili tüm bölgelerde sağlanır.

Cosmos kapsayıcısının *' R '* ru ile yapılandırıldığını ve Cosmos hesabıyla Ilişkili *' N '* bölgesi olduğunu varsayarsak:

- Cosmos hesabı tek bir yazma bölgesi ile yapılandırıldıysa, kapsayıcıda Global olarak bulunan toplam ru = *R* x *N*.

- Cosmos hesabı birden çok yazma bölgesi ile yapılandırıldıysa, kapsayıcıda Global olarak bulunan toplam ru = *R* x (*N*+ 1). Ek *R* ru, bölgeler genelinde güncelleştirme çakışmalarını ve entropi karşı trafiği işlemek için otomatik olarak sağlanır.

[Tutarlılık modeli](consistency-levels.md) seçiminiz, aktarım hızını da etkiler. Daha esnek tutarlılık düzeyleri (örn., *oturum*, *tutarlı ön ek* ve *nihai* tutarlılık) için, daha güçlü tutarlılık düzeylerine kıyasla yaklaşık 2x okuma aktarım hızı edinebilirsiniz (örneğin, *sınırlı Eskime durumu* veya  *güçlü* tutarlılık).

## <a name="next-steps"></a>Sonraki adımlar

Bir sonraki adımda, bir kapsayıcıda veya veritabanında üretilen işi yapılandırmayı öğrenebilirsiniz:

* [Kapsayıcılar ve veritabanları için üretilen iş kazanın ve ayarlayın](set-throughput.md) 

