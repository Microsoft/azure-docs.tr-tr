---
title: Azure VM boyutları-Işlem için iyileştirilmiş | Microsoft Docs
description: Azure 'daki sanal makineler için kullanılabilen farklı işlem için iyileştirilmiş boyutları listeler. Bu serideki boyutlarda sanal CPU 'lar, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557701"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>İşlem için iyileştirilmiş sanal makine boyutları

İşlem için iyileştirilmiş VM boyutları yüksek CPU-bellek oranına sahiptir. Bu boyutlar orta trafikli web sunucuları, ağ gereçleri, toplu süreçler ve uygulama sunucuları için uygundur. Bu makale, vCPU sayısı, veri diskleri ve NIC 'ler hakkında bilgi sağlar. Ayrıca, bu gruplandırmadaki her bir boyut için depolama alanı işleme ve ağ bant genişliği hakkında bilgiler içerir.

[Fsv2 serisi](fsv2-series.md) , 2. nesil Intel® Xeon® Platinum 8272CL (Cascade Lake) Işlemcileri ve Intel® Xeon® Platinum 8168 (ufuk Gölü) işlemcileri üzerinde çalışır. Sürekli olarak 3,4 GHz 'nin tüm Core Turbo saat hızına ve en yüksek çekirdekli Turbo 3,7 GHz sıklığına sahiptir. Intel® AVX-512 yönergeleri Intel Ölçeklenebilir Işlemcilerde yenidir. Bu yönergeler, tek ve çift duyarlıklı kayan nokta işlemlerinde vektör işleme iş yükleri için bir 2X performans artışı sağlar. Diğer bir deyişle, tüm hesaplama iş yükleri oldukça hızlıdır.

Daha düşük bir saatlik liste fiyatına göre, Fsv2-Series, vCPU başına Azure Işlem birimi (ACU) temelinde Azure portföyündeki fiyat performansı için en iyi değerdir.

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.

Azure 'un VM 'lerini nasıl adlandırmasının hakkında daha fazla bilgi için bkz. [Azure sanal makine boyutları adlandırma kuralları](./vm-naming-conventions.md).