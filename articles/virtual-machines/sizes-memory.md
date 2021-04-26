---
title: Azure VM boyutları-bellek | Microsoft Docs
description: Azure 'daki sanal makineler için kullanılabilir olan farklı bellek için iyileştirilmiş boyutları listeler. Bu serideki boyutlarda sanal CPU 'lar, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM yalıtımı, yalıtılmış VM, yalıtım, yalıtılmış
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 08a14a1c97a1eae9c07c50334929fb16304e635a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556528"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Bellek için iyileştirilmiş sanal makine boyutları

Bellek için iyileştirilmiş VM boyutları, ilişkisel veritabanı sunucuları, orta ve büyük önbellekler ve bellek içi analizler için harika olan yüksek bir bellek-CPU oranı sunar. Bu makalede, bu gruplandırmadaki her bir boyut için sanal CPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkında bilgi sağlanır.

- Özgün D serisi için bir takip olan [dv2 ve DSv2 serisi](dv2-dsv2-series-memory.md), daha güçlü bir CPU sunar. Dv2 serisi, D serisinden daha hızlı %35 daha hızlıdır. Intel &reg; Xeon &reg; 8171m 2,1 GHz (ufuk Gölü) veya Intel &reg; Xeon &reg; E5-2673 v4 2,3 GHz (çok iyi) veya Intel &reg; xeon &reg; E5-2673 v3 2,4 GHz (Haswell) Işlemcileri üzerinde çalışır ve Intel Turbo Boost Technology 2,0. Dv2 Serisi, D Serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

    Dv2 ve DSv2 serisi, daha hızlı vCPU 'Ları, daha iyi geçici depolama performansını talep eden veya daha yüksek bellek taleplerine sahip olan uygulamalar için idealdir. Bu seçenekler birçok kurumsal sınıf uygulama için güçlü bir bileşim sunar.

- [Eav4 ve Easv4 serisi](eav4-easv4-series.md) , en fazla 256 MB boyutlu önbellek kullanan çok iş parçacıklı BIR yapılandırmada AMD 'Nin 2.35 GHz epyıc<sup>TM</sup> 7452 işlemcisini kullanır ve en fazla bellek için iyileştirilmiş iş yüklerini çalıştırmaya yönelik seçenekleri artırır. Eav4-Series ve Easv4 serisi, Ev3 & Esv3 serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

- [Ev3 ve Esv3-Series](ev3-esv3-series.md) Intel &reg; Xeon &reg; 8171M 2,1 GHz (ufuk Gölü) veya &reg; &reg; bir hiper iş yükleri için ıntel Xeon E5-2673 v4 2,3 GHz (çok iyi) işlemcisi, çoğu genel amaçlı iş yükleri için daha Iyi bir değer teklifi sağlar ve Ev3 diğer birçok bulutun genel amaçlı VM 'leri ile hizalı hale getiriliyor. Bellek ve ağ sınırları, Hyper-Threading ' e geçiş ile hizalamak için, disk ve ağ sınırları her bir çekirdek temelinde ayarlanırken, bellek ve ağ sınırları, her bir çekirdek için bir saniyede ayarlanmışsa (7 GiB/vCPU 'dan 8 GiB Ev3, D/dv2 ailelerinin yüksek bellek sanal makine boyutlarına göre takip edilir.

- [Ev4 ve Esv4 serisi](ev4-esv4-series.md) , &reg; &reg; hiper iş parçacıklı bir yapılandırmada 2. nesil Intel Xeon Platinum 8272Cl (Cascade Lake) işlemcileri üzerinde çalışır, bellek açısından yoğun çeşitli kurumsal uygulamalar ve 504 GIB 'a kadar RAM için idealdir. Intel [ &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ve [Intel &reg; Gelişmiş vektör Uzantıları 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)özelliklerine sahiptir. Ev4 ve Esv4 serisi yerel bir geçici disk içermez. Daha fazla bilgi için  [Yerel geçici disk olmadan Azure VM boyutlarına](azure-vms-no-temp-disk.md)bakın.

- [Edv4 ve Edsv4 serisi](edv4-edsv4-series.md) 2. nesil Intel &reg; Xeon &reg; Platinum 8272Cl (Cascade Lake) işlemcileri üzerinde çalışarak, son derece büyük veritabanları veya yüksek vCPU sayıları ve büyük miktarda bellekten faydalanabilecek diğer uygulamalar için idealdir. Ayrıca, bu VM boyutları, düşük gecikme süresi, yüksek hızlı yerel depolama 'dan faydalanabilir uygulamalar için hızlı, daha büyük yerel SSD depolaması içerir. 3,4 GHz, [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) ve [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)tüm Core Turbo saat hızına sahiptir.

- [M serisi](m-series.md) , yüksek bir vCPU sayısı (128 vCPU kadar) ve büyük miktarda bellek (3,8 TİB 'ye kadar) sunar. Son derece büyük veritabanları veya yüksek sanal CPU sayılarından ve büyük miktarda bellekten faydalanabilir diğer uygulamalar için de idealdir.

- [Mv2-Series](mv2-series.md) , buluttaki HERHANGI bir VM 'nin en yüksek vCPU sayısını (416 vCPU 'ya kadar) ve en büyük bellek miktarını (11,4 TİB 'ye kadar) sunar. Son derece büyük veritabanları veya yüksek sanal CPU sayılarından ve büyük miktarda bellekten faydalanabilir diğer uygulamalar için idealdir.

Azure Işlem, belirli bir donanım türüne yalıtılmış ve tek bir müşteriye adanmış sanal makine boyutları sunar. Bu sanal makine boyutları, uyumluluk ve mevzuat gereksinimleri gibi öğeleri içeren iş yükleri için diğer müşterilerden yüksek derecede yalıtım gerektiren iş yükleri için idealdir. Müşteriler Ayrıca, [iç içe geçmiş sanal makineler Için Azure desteği](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)'ni kullanarak bu yalıtılmış sanal makinelerin kaynaklarını daha fazla alt bölümlere ayırmak da tercih edebilir. Yalıtılmış VM seçenekleriniz için aşağıdaki sanal makine aileleri sayfalarına bakın.

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.

Azure 'un VM 'lerini nasıl adlandırmasının hakkında daha fazla bilgi için bkz. [Azure sanal makine boyutları adlandırma kuralları](./vm-naming-conventions.md).
