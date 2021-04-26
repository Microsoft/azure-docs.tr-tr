---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: ffb07220267a2c192b4aad2405185c80bd9abbc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94523854"
---
Bir Azure sanal makinesine çok sayıda veri diski ekleyebilirsiniz. Bir sanal makinenin veri disklerinin ölçeklenebilirlik ve performans hedeflerine bağlı olarak, performans ve kapasite gereksinimlerinizi karşılamak için gereken disk sayısını ve türünü belirleyebilirsiniz.

> [!IMPORTANT]
> En iyi performans için, olası azaltmayı önlemek için sanal makineye bağlı olan yüksek oranda kullanılan disklerin sayısını sınırlayın. Tüm eklenen diskler aynı anda yüksek oranda kullanılıyorsa, sanal makine daha fazla sayıda diski destekleyebilir.

**Azure yönetilen diskler için:**

Aşağıdaki tabloda, abonelik başına bölge başına düşen kaynak sayısı için varsayılan ve en fazla limit gösterilmektedir. Sınırlar, platform tarafından yönetilen anahtarlarla veya müşteri tarafından yönetilen anahtarlarla şifrelenen disklere bakılmaksızın aynı kalır. Kaynak grubu başına yönetilen disk sayısı, anlık görüntüler ve görüntüler için bir sınır yoktur.  

> | Kaynak | Sınır |
> | --- | --- |
> | Standart yönetilen diskler | 50,000 |
> | Standart SSD yönetilen diskler | 50,000 |
> | Premium yönetilen diskler | 50,000 |
> | Standard_LRS anlık görüntüler | 50,000 |
> | Standard_ZRS anlık görüntüler | 50,000 |
> | Yönetilen görüntü | 50,000 |

**Standart depolama hesapları için:** Standart depolama hesabında en fazla 20.000 ıOPS toplam istek oranı vardır. Standart depolama hesabındaki tüm sanal makine disklerinizdeki toplam ıOPS, bu sınırı aşmamalıdır.
  
İstek hızı sınırına göre tek bir standart depolama hesabı tarafından desteklenen yüksek oranda kullanılan disk sayısını kabaca hesaplayabilirsiniz. Örneğin, temel bir katman VM 'si için, en yüksek oranda kullanılan disk sayısı, disk başına 20.000/300 ıOPS olan 66 ' dir. Standart katman bir sanal makine için yüksek oranda kullanılan disk sayısı, disk başına 20000/500 ıOPS olan 40 ' dir. 

**Premium Depolama hesapları için:** Premium Depolama hesabında en fazla 50 Gbps toplam verimlilik ücreti vardır. Tüm sanal makinelerdeki toplam aktarım hızı bu sınırı aşmamalıdır.

