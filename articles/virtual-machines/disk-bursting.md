---
title: Yönetilen disk patlaması
description: Azure diskleri ve Azure sanal makineleri için disk yakalaması hakkında bilgi edinin.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568724"
---
# <a name="managed-disk-bursting"></a>Yönetilen disk patlaması
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Azure [Premium SSD 'ler](disks-types.md#premium-ssd) iki adet geri ödeme modeli sunar:

- İsteğe bağlı bir istek temelli model (Önizleme); burada disk, her ne kadar her birinin geçerli kapasitesini aşması gerekir. Bu model, her zaman disk patındaki ek ücretler doğurur. Alacak olmayan burdım yalnızca boyut olarak 512 GiB 'den büyük disklerde kullanılabilir.
- Diskin yalnızca kredi demetini birikmiş patlama kredisi varsa, bir kredi tabanlı model. Bu model, disk patlamaz ek ücret uygulamaz. Kredi tabanlı patlama yalnızca 512 GiB ve daha küçük disklerde kullanılabilir.

Ayrıca, [yönetilen disklerin performans katmanı değiştirilebilir](disks-change-performance.md)ve bu, iş yükünüz başka bir şekilde patlama durumunda çalışıyorsa ideal olabilir.

|  |Kredi tabanlı patlama  |İsteğe bağlı patlama  |Performans katmanını değiştirme  |
|---------|---------|---------|---------|
| **Senaryolar**|Kısa vadeli ölçeklendirme için idealdir (30 dakika veya daha az).|Kısa vadeli ölçeklendirme için idealdir (sınırlı süre).|İş yükünüz başka bir şekilde patlaması halinde çalışmaya devam ediyorsa idealdir.|
|**Maliyet**     |Ücretsiz         |Maliyet değişkeni, Ayrıntılar için [Faturalama](#billing) bölümüne bakın.        |Her performans katmanının maliyeti sabittir, Ayrıntılar için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/) .         |
|**Kullanılabilirlik**     |Yalnızca Premium SSDs 512 GiB ve daha küçük bir sitede kullanılabilir.         |Yalnızca 512 GiB 'den büyük Premium SSD 'Ler için kullanılabilir.         |Tüm Premium SSD boyutları için kullanılabilir.         |
|**Etkinleştirme**     |Uygun disklerde varsayılan olarak etkindir.         |Kullanıcı tarafından etkinleştirilmelidir.         |Kullanıcının katmanını el ile değiştirmesi gerekir.         |

## <a name="common-scenarios"></a>Genel senaryolar
Aşağıdaki senaryolar büyük ölçüde patlanabilir:
- **Başlangıç zamanlarını iyileştirme**  : tekrarlaması sayesinde örneğiniz önemli ölçüde daha hızlı bir hızda başlatılır. Örneğin, Premium etkin VM 'Ler için varsayılan işletim sistemi diski, 120 ıOPS ve 25 MB/sn için sağlanan bir performans olan P4 disktir. P4, en fazla 3500 ıOPS ve 170 MB/sn, başlangıç için 6X 'e kadar hızlanmasına izin verebilir.
- **Toplu Işleri işle** – bazı uygulama iş yükleri doğası gereği daha fazla. Bunlar, çoğu zaman bir taban çizgisi performansına ve kısa süreler için daha yüksek performansa ihtiyaç duyar. Bunun bir örneği, az miktarda disk trafiği gerektiren günlük işlemleri işleyen bir muhasebe programıdır. Ayın sonunda, bu program çok daha yüksek miktarda disk trafiğine ihtiyacı olan mutabakat raporlarını tamamlar.
- **Trafik ani artışları** – Web sunucuları ve uygulamaları, her zaman trafik dalgalanmalarına karşılaşabilir. Web sunucunuz, burdıya kullanan VM 'Ler veya disklerle kullanılıyorsa, sunucular trafik artışlarını işlemek için daha iyi şekilde donatılmıştır. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Sonraki adımlar

İsteğe bağlı tekrar açma özelliğini etkinleştirmek için bkz. [isteğe](disks-enable-bursting.md)bağlı patlaması etkinleştirme.
Elde eden kaynaklarınız hakkında fikir edinme hakkında bilgi edinmek için bkz. [disk alma ölçümleri](disks-metrics.md).
