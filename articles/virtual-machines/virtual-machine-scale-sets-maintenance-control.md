---
title: Azure sanal makine ölçek kümelerinde işletim sistemi görüntüsü yükseltmeleri için bakım denetimine genel bakış
description: Otomatik işletim sistemi görüntüsü yükseltmelerinden, bakım denetimi kullanarak Azure sanal makine ölçek kümeleriniz için nasıl alınacağını nasıl denetleyeceğinizi öğrenin.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90532704"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Önizleme: Azure sanal makine ölçek kümeleri için bakım denetimi 

Bakım denetimini kullanarak sanal makine ölçek kümeleriniz için [Otomatik işletim sistemi görüntüsü yükseltmelerini](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) yönetin.

Bakım denetimi, sanal makine ölçek kümelerinizdeki işletim sistemi disklerine ne zaman daha kolay ve daha öngörülebilir bir deneyim aracılığıyla güncelleştirmelerin uygulanacağını belirlemenize olanak sağlar. 

Bakım yapılandırması abonelikler ve kaynak grupları arasında çalışır.

İş akışının tamamı şu adımlardan oluşur: 
- Bakım yapılandırması oluşturun.
- Bir sanal makine ölçek kümesini bakım yapılandırması ile ilişkilendirin.
- Otomatik işletim sistemi yükseltmelerini etkinleştirin.

> [!IMPORTANT]
> Azure sanal makine ölçek kümelerinde işletim sistemi görüntüsü yükseltmeleri için bakım denetimi şu anda genel önizlemededir.
> Bu önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Sınırlamalar

- VM 'Ler bir ölçek kümesinde olmalıdır.
- Kullanıcının, **kaynak katılımcısı** erişimi olmalıdır.
- Bakım süresi, bakım yapılandırmasında 5 saat veya daha uzun olmalıdır.
- Bakım yapılandırmasında bakım tekrarının ' gün ' olarak ayarlanması gerekir


## <a name="management-options"></a>Yönetim seçenekleri

Aşağıdaki seçeneklerden herhangi birini kullanarak, bakım yapılandırması oluşturabilir ve yönetebilirsiniz:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Sonraki adımlar

Bakım denetimi ve PowerShell kullanarak Azure sanal makine ölçek kümeleriniz için bakımın ne zaman uygulanacağını nasıl denetleyeceğinizi öğrenin.

> [!div class="nextstepaction"]
> [PowerShell kullanarak sanal makine ölçek kümesi bakım denetimi](virtual-machine-scale-sets-maintenance-control-powershell.md)
