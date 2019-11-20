---
title: PostgreSQL için Azure veritabanı 'nda yüksek kullanılabilirlik – hiper ölçek (Citus)
description: Yüksek kullanılabilirlik ve olağanüstü durum kurtarma kavramları
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 616b5bff735f52d137c12c58ac6023c38a2d4044
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514751"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL için Azure veritabanı 'nda yüksek kullanılabilirlik – hiper ölçek (Citus)

Yüksek kullanılabilirlik (HA), bir sunucu grubundaki her düğümün bekleyen çoğaltmalarını tutarak veritabanı kapalı kalma süresini önler. Düğüm kapalıysa, hiper ölçek başarısız olan düğümden gelen bağlantıları bekleme durumuna geçirir. Yük devretme birkaç dakika içinde gerçekleşir ve Yükseltilen düğümlerin her zaman PostgreSQL zaman uyumlu akış çoğaltması aracılığıyla yeni verileri vardır.

Koordinatör düğümündeki HA avantajlarından yararlanmak için veritabanı uygulamalarının, bırakılan bağlantıları ve başarısız işlemleri algılaması ve yeniden denemesi gerekir. Yeni yükseltilen düzenleyiciye aynı bağlantı dizesiyle erişilebilecektir.

Kurtarma üç aşamaya ayrılabilir: algılama, yük devretme ve tam kurtarma.  Hiper ölçek her düğüm üzerinde düzenli durum denetimleri çalıştırır ve dört başarısız denetim, bir düğümün aşağı doğru olduğunu belirler. Hiper ölçek, bir bekleme moduna birincil düğüm durumunu (yük devretme) yükseltir ve yeni bir bekleme moduna hazırlar.
Akış çoğaltma başlar ve yeni düğüm güncel hale getiriliyor.  Tüm veriler çoğaltıldığında, düğüm tam kurtarmaya ulaştı.

### <a name="next-steps"></a>Sonraki adımlar

- Bir Hyperscale sunucu grubunda [yüksek kullanılabilirliği nasıl etkinleştireceğinizi](howto-hyperscale-high-availability.md) öğrenin.
