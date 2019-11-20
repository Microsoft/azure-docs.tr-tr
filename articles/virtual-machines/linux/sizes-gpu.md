---
title: Azure Linux VM boyutları-hızlandırılmış işlem
description: Azure 'da Linux sanal makineleri için kullanılabilen farklı GPU iyileştirilmiş boyutları listeler. Bu serideki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 983e85dc0ebefb27804c0f3a794360def6050ba9
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034917"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU iyileştirilmiş sanal makine boyutları

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Desteklenen dağıtımlar ve sürücüler

Linux çalıştıran Azure N serisi VM 'lerinin GPU Özellikleri avantajlarından yararlanmak için NVıDıA GPU sürücüleri yüklenmelidir. [NVıDıA GPU sürücü uzantısı](../extensions/hpccompute-gpu-linux.md) , bir N serisi VM 'ye uygun NVIDIA CUDA veya kılavuz sürücülerini yükleme. Azure CLı veya Azure Resource Manager şablonları gibi Azure portal veya araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen dağıtımlar ve dağıtım adımları için [NVıDıA GPU sürücü uzantısı belgelerine](../extensions/hpccompute-gpu-linux.md) bakın. VM uzantıları hakkında genel bilgi için bkz. [Azure sanal makine uzantıları ve özellikleri](../extensions/overview.md).

NVıDıA GPU sürücülerini el ile yüklemeyi tercih ederseniz desteklenen dağıtımlar, sürücüler ve yükleme ve doğrulama adımları için bkz. [Linux Için N SERISI GPU sürücü kurulumu](n-series-driver-setup.md) .


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Ubuntu NC VM 'lerinde `Nouveau` sürücüsünü kullanan X Server veya diğer sistemleri yüklememelisiniz. NVıDıA GPU sürücülerini yüklemeden önce `Nouveau` sürücüsünü devre dışı bırakmanız gerekir.  

## <a name="other-sizes"></a>Diğer boyutlar
- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar
Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.