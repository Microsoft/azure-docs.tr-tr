---
title: PostgreSQL için Azure Danışmanı
description: PostgreSQL için Azure Advisor önerileri hakkında bilgi edinin.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: a44f808daf32a1175035005067dfbb5edd664ef6
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310062"
---
# <a name="azure-advisor-for-postgresql"></a>PostgreSQL için Azure Danışmanı
Azure Advisor 'ın PostgreSQL için Azure veritabanı 'na nasıl uygulandığı ve sık sorulan sorulara yanıtlar alma hakkında bilgi edinin.
## <a name="what-is-azure-advisor-for-postgresql"></a>PostgreSQL için Azure Advisor nedir?
Azure Advisor sistemi, PostgreSQL veritabanınız için performans ve güvenilirlik önerileri vermek üzere telemetri kullanır. Danışman önerileri PostgreSQL veritabanı tekliflerimiz arasında bölünür:
* PostgreSQL için Azure Veritabanı - Tek Sunucu
* PostgreSQL için Azure veritabanı-esnek sunucu
* PostgreSQL için Azure Veritabanı - Hiper Ölçek (Citus)

Bazı öneriler, birden çok ürün teklifi için ortaktır, diğer öneriler ürüne özgü iyileştirmelere dayalıdır.
## <a name="where-can-i-view-my-recommendations"></a>Önerilerimi nerede görüntüleyebilirim?
Öneriler, Azure portal **genel bakış** gezintisi kenar çubuğundan kullanılabilir. Önizleme bir başlık bildirimi olarak görünür ve kaynak kullanım grafiklerinin hemen altında bulunan **Bildirimler** bölümünde Ayrıntılar görüntülenebilir.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Azure Advisor önerisi gösteren Azure portal ekran görüntüsü.":::

## <a name="recommendation-types"></a>Öneri türleri
PostgreSQL için Azure veritabanı aşağıdaki öneri türlerini önceliklendirin:
* **Performans**: PostgreSQL sunucunuzun hızını artırmak için. Buna CPU kullanımı, bellek baskısı, bağlantı havuzu, disk kullanımı ve ürüne özgü sunucu parametreleri dahildir. Daha fazla bilgi için bkz. [Advisor performans önerileri](../advisor/advisor-performance-recommendations.md).
* **Güvenilirlik**: iş açısından kritik veritabanlarınızın sürekliliğini sağlamak ve geliştirmek için. Bu, depolama sınırlarını, bağlantı sınırlarını ve hiper ölçekli veri dağıtımı önerilerini içerir. Daha fazla bilgi için bkz. [Advisor güvenilirlik önerileri](../advisor/advisor-high-availability-recommendations.md).
* **Maliyet**: Genel Azure harcamalarınızı iyileştirmek ve azaltmak için. Bu, sunucu doğru boyutlandırma önerilerini içerir. Daha fazla bilgi için bkz. [danışman maliyet önerileri](../advisor/advisor-cost-recommendations.md).

## <a name="understanding-your-recommendations"></a>Önerilerinizi anlama
* **Günlük zamanlama**: Azure PostgreSQL veritabanları için, günlük bir zamanlamaya göre sunucu telemetrisini ve sorun önerilerini denetliyoruz. Sunucu yapılandırmanızda değişiklik yaparsanız, mevcut öneriler, Telemetriyi izleyen gün boyunca yeniden inceleyerek görünür olmaya devam eder. 
* **Performans geçmişi**: bazı önerilerimizde performans geçmişi temel alınır. Bu öneriler yalnızca, 7 gün için aynı yapılandırmaya sahip bir sunucu çalıştıktan sonra görüntülenir. Bu, sürekli bir süre boyunca ağır kullanım düzenlerini (örn. yüksek CPU etkinliği veya yüksek bağlantı hacmi) algılamamızı sağlar. Yeni bir sunucu temin veya yeni bir sanal çekirdek yapılandırmasına geçiş yaparsanız, bu öneriler geçici olarak duraklatılır. Bu, eski telemetrinin yeni yeniden yapılandırılmış bir sunucuda öneriler tetiklemesini önler. Bununla birlikte, bu durum, performans geçmişi tabanlı önerilerin hemen belirlenebileceği anlamına da gelir.

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi için bkz. [Azure Advisor 'A genel bakış](../advisor/advisor-overview.md).
