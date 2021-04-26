---
title: Azure portal kullanarak Azure yönetilen disklerin performansını değiştirme
description: Azure portal kullanarak, yeni ve mevcut yönetilen disklerin performans katmanlarını değiştirmeyi öğrenin.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901043"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Azure portal kullanarak performans katmanınızı değiştirin

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Kısıtlamalar

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Kullanmaya başlama

### <a name="new-disks"></a>Yeni diskler

Aşağıdaki adımlarda, diski ilk oluşturduğunuzda diskinizin performans katmanının nasıl değiştirileceği gösterilmektedir:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. İçin yeni bir disk oluşturmak istediğiniz VM 'ye gidin.
1. Yeni diski seçerken, önce ihtiyacınız olan diskin boyutunu seçin.
1. Bir boyut seçtikten sonra, performansını değiştirmek için farklı bir performans katmanı seçin.
1. Diski oluşturmak için **Tamam ' ı** seçin.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Disk oluşturma dikey penceresinin ekran görüntüsü, bir disk vurgulanır ve performans katmanı açılan menüsü vurgulanır." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Mevcut diskler

Aşağıdaki adımlarda, var olan bir diskin performans katmanının nasıl değiştirileceği ana hatlarıyla verilmiştir:

1. [Azure portalında](https://portal.azure.com/) oturum açın.
1. Değiştirmek istediğiniz diski içeren VM 'ye gidin.
1. VM 'yi serbest bırakın veya diski ayırın.
1. Diskinizi seçin
1. **Boyut + performans**' ı seçin.
1. **Performans katmanı** açılan menüsünde, diskin geçerli performans katmanından farklı bir katman seçin.
1. **Yeniden boyutlandır**’ı seçin.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Boyut + performans dikey penceresinin ekran görüntüsü, performans katmanı vurgulanır." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Sonraki adımlar

Daha yüksek performans katmanlarından yararlanmak için bir diski yeniden boyutlandırmanız gerekiyorsa, şu makalelere bakın:

- [Azure CLı ile Linux VM 'de sanal sabit diskler genişletme](linux/expand-disks.md)
- [Windows sanal makinesine bağlı yönetilen diski Genişlet](windows/expand-os-disk.md)
