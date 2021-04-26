---
title: Azure BareMetal altyapısının koşullarını öğrenin
description: Azure BareMetal altyapısının koşullarını öğrenin.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: 61ff958e75952f73efb222df3f0c4d5437668cd3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725467"
---
# <a name="know-the-terms-for-baremetal-infrastructure"></a>BareMetal altyapısının koşullarını öğrenin

Bu makalede, BareMetal altyapısıyla ilgili bazı önemli koşullar ele alınacaktır.

- **Düzeltme**: BareMetal ıNFRASTRUCTURE (Hana büyük örnek) damgaları için iki farklı damga düzeltmesi vardır. Bunlar, mimaride ve Azure sanal makine konaklarına yakınlığa göre farklılık gösterir:
    - "Düzeltme 3" (Rev 3): özgün tasarım orta 2016 ' i dağıttı.
    - "Düzeltme 4,2" (Rev 4,2): Azure VM 'Leri ve HANA büyük örnekleri arasında Ultra düşük ağ gecikme süresi ile Azure sanal makine konaklarına daha yakından yakınlık sağlayan yeni bir tasarım. Azure portal kaynaklar "BareMetal altyapısı" olarak adlandırılır ve müşteriler kaynaklarına Azure portal BareMetal örnekleri olarak erişebilir.

- **Damga**: BareMetal örneklerinin Microsoft iç dağıtım boyutunu tanımlar. Örneklerin dağıtılması için işlem, ağ ve depolama raflarından oluşan bir BareMetal örnek damgası, bir veri merkezi konumuna dağıtılmalıdır. Böyle bir dağıtıma BareMetal örnek damgası denir.

- **Kiracı**: bir BareMetal örnek damgası dağıtan bir müşteri, kiracı olarak yalıtılır *.* Bir kiracı, diğer kiracılardan ağ, depolama ve işlem katmanında yalıtılmıştır. Farklı kiracılara atanan depolama ve işlem birimleri, her birinin veya BareMetal örnek damgası düzeyinde birbirleriyle iletişim kuramazlar. Müşteri, farklı kiracılarda dağıtımları olmasını seçebilir. Daha sonra bile, BareMetal örnek damgası düzeyinde kiracılar arasında iletişim yoktur.

## <a name="next-steps"></a>Sonraki adımlar

BareMetal altyapısının önemli terimlerinin tanıtıldığına göre, şu bilgileri öğrenmek isteyebilirsiniz:
- [Baremetal altyapısının](concepts-baremetal-infrastructure-overview.md)daha fazla ayrıntısı.
- [Azure 'Da BareMetal altyapı örneklerine bağlanma](connect-baremetal-infrastructure.md).

