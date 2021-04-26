---
title: Azure Cosmos DB için otomatik performans, maliyet ve güvenlik önerileri
description: İş yükü desenlerinize göre Azure Cosmos DB için özelleştirilmiş performans, maliyet, güvenlik ve diğer önerilerin nasıl görüntüleneceğini öğrenin.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087834"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Azure Cosmos DB için otomatik öneriler
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB dahil olmak üzere tüm bulut Hizmetleri, yeni özellikler, yetenekler ve geliştirmeler hakkında sık kullanılan güncelleştirmeleri alabilirler. Uygulamanızın en son performans ve güvenlik güncelleştirmeleriyle devam etmesini önemlidir. Azure portal, uygulamanızın performansını en üst düzeye çıkarmanıza olanak sağlayan özelleştirilmiş öneriler sunar. Azure Cosmos DB danışmanlık altyapısı, Azure Cosmos DB kaynaklarınızın kullanım geçmişini sürekli olarak analiz eder ve iş yükü desenlerinize göre öneriler sağlar. Bu öneriler, bölümleme, dizin oluşturma, ağ, güvenlik vb. gibi alanlara karşılık gelir. Bu özelleştirilmiş öneriler, uygulamanızın performansını artırmanıza yardımcı olur.

## <a name="view-recommendations"></a>Önerileri görüntüleme

Azure Cosmos DB önerilerini aşağıdaki yollarla görüntüleyebilirsiniz:

- Önerileri görüntülemenin bir yolu bildirimler sekmesinin içindedir. Yeni öneriler varsa, bir ileti çubuğu görürsünüz. [Azure Portal](https://portal.azure.com) oturum açın ve Azure Cosmos hesabınıza gidin. Azure Cosmos hesabınızda, **Bildirimler** bölmesini açın ve ardından **öneriler** sekmesini seçin. İletiyi seçip önerileri görüntüleyebilirsiniz.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Azure Cosmos DB bölmesinden önerileri görüntüle":::

- Ayrıca, maliyet, güvenlik, güvenilirlik, performans ve işlemsel üstün çalışma gibi farklı demetlere göre sınıflandırılan [Azure Advisor](../advisor/advisor-overview.md) aracılığıyla önerileri de bulabilirsiniz. Belirli abonelikleri seçebilir ve **Azure Cosmos DB hesaplar** olan kaynak türüne göre filtreleyebilirsiniz.  Belirli bir öneriyi seçtiğinizde, iş yüklerinize yarar sağlamak için gerçekleştirebileceğiniz eylemleri görüntüler.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Azure Advisor bölmesinden önerileri görüntüleme":::

Azure Cosmos DB bölmesinde gösterilen önerilerin hepsi Azure Advisor 'da kullanılabilir değildir ve tam tersi de geçerlidir. Bunun nedeni, Azure Advisor bölmesine, Azure Cosmos DB bölmesine veya her ikisine de uydukları önerinin türüne bağlıdır.

Şu anda Azure Cosmos DB aşağıdaki alanlarda önerileri destekler. Bu önerilerin her biri, belgelerin ilgili bölümünün bir bağlantısını içerir, bu nedenle sonraki adımları uygulamanız kolaydır.

## <a name="sdk-usage-recommendations"></a>SDK kullanım önerileri

Bu kategoride, danışman bir SDK 'nın eski sürümünün kullanımını algılar ve en son hata düzeltmeleri ve performans iyileştirmelerinden yararlanmak için daha yeni bir sürüme yükseltmenizi önerir. Şu anda aşağıdaki SDK 'ya özgü öneriler mevcuttur:

|Ad  |Açıklama  |
|---------|---------|
| Eski Spark Bağlayıcısı | Spark bağlayıcısının eski sürümlerinin kullanımını algılar ve yükseltmeyi önerir. |
| Eski .NET SDK | .NET SDK 'sının eski sürümlerinin kullanımını algılar ve yükseltmeyi önerir. |
| Eski Java SDK 'Sı | Java bağlayıcısının eski sürümlerinin kullanımını algılar ve yükseltmeyi önerir. |

## <a name="indexing-recommendations"></a>Dizin oluşturma önerileri

Bu kategoride, danışman dizin oluşturma modu, dizin oluşturma ilkesi, dizine alınmış yollar algılanır ve geçerli yapılandırmanın sorgu performansını etkileyip etkilemediğini değiştirmenizi önerir. Şu anda aşağıdaki dizin oluşturmaya özgü öneriler mevcuttur:

|Ad  |Açıklama  |
|---------|---------|
| Geç dizin oluşturma | Geç dizin oluşturma modunun kullanımını algılar ve bunun yerine tutarlı dizin oluşturma modunun kullanılmasını önerir. Azure Cosmos DB yavaş dizin oluşturma modunun amacı sınırlıdır ve sorgu sonuçlarının etkisini bazı durumlarda etkileyebilir, böylece tutarlı dizin oluşturma modunun kullanılması önerilir. |
| Bileşik dizin oluşturma| Sorguların bileşik dizinlerden faydaladığı hesapları algılar ve bunları kullanmanızı öneririz. Bileşik dizinler bazı sorguların performans ve verimlilik tüketimini önemli ölçüde iyileştirebilir.|
| Birden çok dizinli yol içeren varsayılan dizin oluşturma ilkesi | Birden çok dizinli yol içeren varsayılan dizin oluşturma üzerinde çalışan kapsayıcıları algılar ve dizin oluşturma ilkesinin özelleştirilmesini önerir.|
| Yüksek RU/s ücretine sahip sorgulara göre SıRALAMA| Yüksek RU/s ücretine sahip sorgulara göre SIPARIŞ veren kapsayıcıları algılar ve bileşik dizinlerin araştırmalarını önerir.|
| Dizin ve yüksek RU/sn tüketimi olmayan MongoDB 3,6 hesapları| , Yüksek RU/s ücretine sahip sorgular veren ve Dizin eklemeyi öneren kapsayıcı 3,6 sürümü ile MongoDB için Azure Cosmos DB API 'sini algılar.|

## <a name="cost-optimization-recommendations"></a>Maliyet iyileştirme önerileri

Bu kategoride, danışman RU/s kullanımını algılar ve kaynaklarınız üzerinde bazı değişiklikler yaparak veya farklı bir fiyatlandırma modelinden yararlanarak fiyatı iyileştirebileceğinizi belirler. Şu anda aşağıdaki maliyet iyileştirmeye özgü öneriler mevcuttur:

|Ad  |Açıklama  |
|---------|---------|
| Ayrılmış kapasite | RU/s kullanımınızı algılar ve bundan faydalanabilecek kullanıcılara ayrılmış örnekler önerir. |
| Etkin olmayan kapsayıcılar | 30 günden uzun süredir kullanılmamış olan kapsayıcıları algılar ve bu kapsayıcılar için üretilen iş verimini azaltmayı veya bunları silmeyi önerir.|
| Yüksek aktarım hızı olan yeni abonelikler | Her gün alışılmadık yüksek RU/sn miktarı olan hesaplara sahip yeni abonelikler algılar ve onlara bir bildirim sağlar. Bu bildirim özellikle Azure Cosmos DB, tüketim tabanlı model değil, sağlanan aktarım hızı tabanlı modelde çalışan yeni müşterilere daha fazla tanıma getirmek için kullanılır. |

## <a name="migration-recommendations"></a>Geçiş önerileri

Bu kategoride, danışman eski özellikleri kullandığınızı algılar ve Azure Cosmos DB çok büyük ölçeklenebilirlik ve diğer avantajlardan yararlanabilir. Şu anda aşağıdaki geçişe özgü öneriler mevcuttur:

|Ad  |Açıklama  |
|---------|---------|
| Bölümlendirilmemiş kapsayıcılar | En fazla depolama sınırına yaklaştığı sabit boyutlu kapsayıcıları algılar ve bunları bölümlenmiş kapsayıcılara geçirmeyi önerir.|

## <a name="query-usage-recommendations"></a>Sorgu kullanım önerileri

Bu kategoride, danışman sorgu yürütmesini algılar ve sorgu performansının bazı değişikliklerle ayarlanabilir olduğunu tanımlar. Şu anda aşağıdaki sorgu kullanım önerileri mevcuttur:

|Ad  |Açıklama  |
|---------|---------|
| Sabit sayfa boyutuna sahip sorgular | Sabit sayfa boyutuyla verilen sorguları algılar ve belirli bir değer tanımlamak yerine-1 (sayfa boyutunda sınır olmadan) kullanılmasını önerir. Bu seçenek, tüm sonuçları almak için gereken ağ gidiş dönüşlerin sayısını azaltır. |

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Cosmos DB 'de sorgu performansını ayarlama](sql-api-query-metrics.md)
* Azure Cosmos DB kullanırken [sorgu sorunlarını giderme](troubleshoot-query-performance.md)
