---
title: PostgreSQL için Azure veritabanı 'na genel bakış-hiper ölçek (Citus)
description: Hyperscale (Citus) dağıtım seçeneğine genel bakış sağlar
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95025149"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı-hiper ölçek (Citus) nedir?

PostgreSQL için Azure veritabanı, geliştiriciler için derlenmiş Microsoft bulutundaki bir ilişkisel veritabanı hizmetidir. Bu, açık kaynak [PostgreSQL](https://www.postgresql.org/) veritabanı altyapısının topluluk sürümünü temel alır.

Hiper ölçek (Citus), parçaları kullanarak birden çok makine genelinde sorguları yatay olarak ölçeklendirerek kullanılan bir dağıtım seçeneğidir. Sorgu altyapısı, büyük veri kümelerinde daha hızlı yanıt elde etmek için gelen SQL sorgularını paralel bir şekilde bu sunuculara dağıtır. Diğer dağıtım seçeneklerinden daha fazla ölçek ve performans gerektiren uygulamalar sunar: genellikle yaklaştığı veya zaten aşmış--100 GB veri olan iş yükleri.

Hiper ölçek (Citus) şunları sağlar:

- Parçalama kullanarak birden çok makinenin yatay olarak ölçeklendirilmesi
- Büyük veri kümelerinde daha hızlı yanıt almak için sunucular arasında sorgu paralelleştirme
- Çok kiracılı uygulamalar, gerçek zamanlı operasyonel çözümlemeler ve yüksek verimlilik işlem iş yükleri için mükemmel destek

PostgreSQL için derlenmiş uygulamalar, standart [bağlantı kitaplıkları](./concepts-connection-libraries.md) ve en az değişiklikle hiper ölçekte (Citus) dağıtılmış sorgular çalıştırabilir.

## <a name="next-steps"></a>Sonraki adımlar

- İlk PostgreSQL için Azure veritabanı-hiper ölçek (Citus) sunucu grubu [oluşturarak](./quickstart-create-hyperscale-portal.md) başlayın.
- Maliyet karşılaştırmaları ve hesaplayıcıları için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/postgresql/) bakın. Hiper ölçek (Citus) ön ödemeli ayrılmış örnek indirimleri de sunar, Ayrıntılar için bkz. [Hyperscale (Citus) RI fiyatlandırma](concepts-hyperscale-reserved-pricing.md) sayfaları.
- Sunucu grubunuz için en iyi [ilk boyutu](howto-hyperscale-scale-initial.md) belirleme
