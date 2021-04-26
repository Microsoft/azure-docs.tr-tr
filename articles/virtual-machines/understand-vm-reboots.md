---
title: VM yeniden başlatmaları anlama
description: VM yeniden başlatmaları anlama-bakım ve kapalı kalma süresi
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 9342e0c39b558cf8b6ff5773d623ce55062f9ab5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105607482"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM yeniden başlatmaları anlama-bakım ve kapalı kalma süresi
Azure 'daki sanal makinelere etkilenmesine neden olan üç senaryo vardır: planlanmamış donanım bakımı, beklenmedik kapalı kalma süresi ve planlı bakım.

## <a name="unplanned-hardware-maintenance-event"></a>Plansız donanım bakımı olayı
Plansız donanım bakımı, Azure platformu bir fiziksel makineyle ilişkili donanımın veya herhangi bir platform bileşeninin başarısız olmak üzere olduğunu tahmin edildiğinde oluşur. Platform bir arıza öngördüğünde, donanımda barındırılan sanal makineler üzerindeki etkiyi azaltmak amacıyla plansız donanım bakımı olayı düzenler. Azure, sanal makineleri başarısız olan donanımdan sağlıklı fiziksel bir makineye geçirmek için [dinamik geçiş](./maintenance-and-updates.md) teknolojisini kullanır. Dinamik Geçiş, Sanal Makineyi yalnızca kısa bir süre için duraklatan bir VM koruma işlemidir. Bellek, açık dosyalar ve ağ bağlantıları korunur, ancak olaydan önce ve/veya sonra performans azalabilir. Dinamik Geçişin kullanılamadığı durumlarda VM, aşağıda açıklanan Beklenmeyen Kapalı Kalma Süresi yaşar.


## <a name="unexpected-downtime"></a>Beklenmeyen kapalı kalma süresi
Sanal makinenin donanımının veya fiziksel altyapısının beklenmedik şekilde başarısız olması durumunda beklenmedik kapalı kalma süresi. Bu, yerel ağ arızalarını, yerel disk başarısızlıklarını veya diğer raf düzeyi başarısızlıklarını içerebilir. Algılandığında, Azure platformu sanal makinenizi aynı veri merkezinde sağlıklı bir fiziksel makineye otomatik olarak geçirir (toplar). İyileştirme yordamı sırasında sanal makineler kapalı kalır (yeniden başlatma) ve bazı durumlarda geçici sürücü kaybı yaşar. Bağlı işletim sistemi ve veri diskleri her zaman korunur.

Sanal makineler aynı zamanda tüm veri merkezini veya tüm bölgeyi etkileyen bir kesinti veya olağanüstü durum durumunda kapalı kalma süresi yaşar. Azure, bu senaryolar için  [kullanılabilirlik alanları](../availability-zones/az-overview.md) ve [eşleştirilmiş bölgeler](regions.md#region-pairs)dahil olmak üzere koruma seçenekleri sunar.

## <a name="planned-maintenance-events"></a>Planlı bakım olayları
Planlı bakım olayları, Microsoft tarafından, sanal makinelerinizin üzerinde çalıştığı platform altyapısının genel güvenilirlik, performans ve güvenliğini geliştirmek amacıyla temel alınan Azure platformunda yapılan düzenli güncelleştirmelerdir. Bu güncelleştirmelerin çoğu, sanal makineleriniz veya Cloud Services hiçbir etkisi olmadan gerçekleştirilir (bkz. [yeniden başlatma gerektirmeyen bakım](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Azure platformu mümkün olan tüm durumlarda VM Koruyucu Bakımı kullanmaya çalışsa da, gerekli güncelleştirmelerin temel alınan altyapıya uygulanması için bu güncelleştirmelerin sanal makineyi yeniden başlatmayı gerektirdiği nadir örnekler vardır. Bu durumda, uygun zaman penceresi içinde VM’lere yönelik bakımı başlatarak Maintenance-Redeploy işlemi ile Azure Planlı Bakımını gerçekleştirebilirsiniz. Daha fazla bilgi için bkz. [Sanal Makineler için Planlı Bakım](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Kapalı kalma süresini azaltma
Bu olayların bir veya daha fazlası nedeniyle kapalı kalma süresinin etkisini azaltmak için, sanal makinelerinizde aşağıdaki yüksek kullanılabilirlik en iyi uygulamalarının kullanılması önerilir:

* Veri merkezi hatalarından korumak için [kullanılabilirlik alanları](../availability-zones/az-overview.md) kullanma
* Bir [kullanılabilirlik kümesindeki](availability-set-overview.md) birden fazla sanal makineyi artıklık için yapılandırma
* [Windows Için](/azure/virtual-machines/windows/scheduled-events) Linux veya zamanlanan olaylar [için ZAMANLANMıŞ](/azure/virtual-machines/linux/scheduled-events) olayları, VM etkileyen olaylara önceden yanıt vermeye yönelik olarak kullanın
* Her uygulama katmanını ayrı kullanılabilirlik kümeleri halinde yapılandırma
* Bir [Yük dengeleyiciyi](../load-balancer/load-balancer-overview.md) kullanılabilirlik alanları veya kümeleriyle birleştirme

## <a name="next-steps"></a>Sonraki adımlar
Azure 'da kullanılabilirlik seçenekleri hakkında daha fazla bilgi edinmek için bkz. [kullanılabilirliğine genel bakış](availability.md).
