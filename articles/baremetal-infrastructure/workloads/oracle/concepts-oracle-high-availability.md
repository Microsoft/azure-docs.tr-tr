---
title: BareMetal 'da Oracle için yüksek kullanılabilirlik ve olağanüstü durum kurtarma
description: Azure BareMetal altyapısında Oracle için yüksek kullanılabilirlik ve olağanüstü durum kurtarma hakkında bilgi edinin.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: ab0337622eaa8c1368760fcbcd28533dacb3adce
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590349"
---
# <a name="high-availability-and-disaster-recovery-for-oracle-on-baremetal"></a>BareMetal 'da Oracle için yüksek kullanılabilirlik ve olağanüstü durum kurtarma

Bu makalede yüksek kullanılabilirlik ve olağanüstü durum kurtarma hakkında temel bilgilere bakacağız. Daha sonra BareMetal altyapısında bir Oracle ortamında yüksek kullanılabilirlik ve olağanüstü durum kurtarma elde etmenizi sağlayacağız.

## <a name="high-availability-vs-disaster-recovery"></a>Yüksek kullanılabilirlik ve olağanüstü durum kurtarma

Hem yüksek kullanılabilirlik hem de olağanüstü durum kurtarma, farklı hatalardan oluşan ancak kapsama sağlar. Oracle Database farklı özellikleri ve seçenekleri kullanırlar.

Yüksek kullanılabilirlik, sistemin uygulamanın kullanıcı deneyimini etkilemeden birden çok başarısızlığı ortadan kaldırmanıza olanak tanır. Yüksek oranda kullanılabilir bir sistemin ortak özellikleri şunlardır:

- Tek hata noktası olmayan Yedekli donanım.
- Başarısız disk sürücüleri veya hatalı ağ kabloları gibi kritik olmayan hatalardan otomatik kurtarma.
- İşlem üzerinde belirgin bir etkiye gerek kalmadan donanım ve yazılım değişikliklerini alma özelliği.
- Kurtarma süresi hedefleri (RTO) ve kurtarma noktası amaçları (RPO) için hedefleri karşılar veya aşar.

Yüksek kullanılabilirlik için kullanılan en yaygın Oracle özelliği, [Oracle gerçek uygulama kümeleri (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92)' dir.

Olağanüstü durum kurtarma, sizi birincil yüksek kullanılabilirlik stratejinize zarar verecek kurtarılamaz yerelleştirilmiş hatalardan korur. Oracle ekosisteminde, [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590)olarak da bilinen veritabanı çoğaltması aracılığıyla sağlanır.

## <a name="next-steps"></a>Sonraki adımlar

Oracle için yüksek kullanılabilirlik özellikleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [BareMetal altyapısında Oracle için yüksek kullanılabilirlik özellikleri](high-availability-features.md)
