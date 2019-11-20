---
title: Sanal Makine Ölçek Kümeleri için Azure disk şifrelemesini etkinleştir
description: Bu makale, sanal makine ölçek kümeleri için Microsoft Azure disk şifrelemeyi etkinleştirme hakkında yönergeler sağlar
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0097d0e1d5ea7de092da14683d4bab3d673b2219
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177785"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Sanal Makine Ölçek Kümeleri için Azure disk şifrelemesi

Azure disk şifrelemesi, sanal makinelerinizin işletim sistemi ve veri diskleri için birim şifrelemesi sağlar ve böylece kurumsal güvenlik ve uyumluluk taahhütlerini karşılamak üzere verilerinizi korumanıza ve korumaya yardımcı olur. Daha fazla bilgi için bkz [. Azure disk şifrelemesi: Linux VM 'leri](../virtual-machines/linux/disk-encryption-overview.md) ve [Azure disk şifrelemesi: Windows VM 'leri](../virtual-machines/windows/disk-encryption-overview.md)  

Azure disk şifrelemesi, bu örneklerde Windows ve Linux sanal makine ölçek kümelerine de uygulanabilir:
- Yönetilen disklerle oluşturulan ölçek kümeleri. Yerel (veya yönetilmeyen) disk ölçek kümeleri için Azure disk şifrelemesi desteklenmez.
- Windows ölçek kümelerinde işletim sistemi ve veri birimleri.
- Linux ölçek kümelerinde veri birimleri. Linux ölçek kümeleri için işletim sistemi disk şifrelemesi desteklenmiyor.

[Azure CLI kullanarak](disk-encryption-cli.md) sanal makine ölçek kümelerini şifreleme veya [Azure PowerShell kullanarak bir sanal makine ölçek kümesi şifreleme](disk-encryption-powershell.md) ile yalnızca birkaç dakika içinde sanal makine ölçek kümeleri için Azure disk şifrelemesi temelleri hakkında bilgi edinebilirsiniz izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Resource Manager kullanarak bir sanal makine ölçek kümelerini şifreleme](disk-encryption-azure-resource-manager.md)
- [Azure disk şifrelemesi için Anahtar Kasası oluşturma ve yapılandırma](disk-encryption-key-vault.md)
- [Sanal makine ölçek kümesi uzantısı sıralaması ile Azure disk şifrelemesini kullanma](disk-encryption-extension-sequencing.md)
