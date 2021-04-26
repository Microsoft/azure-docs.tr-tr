---
title: include dosyası
description: include dosyası
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99580000"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Boyut tablosu tanımları

- Depolama kapasitesi GiB veya 1024^3 bayt cinsinden gösterilmiştir. GB (1000 ^ 3 bayt) cinsinden ölçülen diskleri GiB 'de ölçülen disklere (1024 ^ 3) karşılaştırdığınızda, GiB 'de verilen kapasite numaralarının daha küçük görünebileceğinden emin unutmayın. Örneğin, 1023 GiB = 1098,4 GB.
- Disk aktarım hızı, saniye başına giriş/çıkış işlemi sayısı (IOPS) ve MB/sn (MB/sn = 10^6 bayt/sn) üzerinden ölçülür.
- Veri diskleri önbelleğe alınmış veya alınmamış modlarda çalışabilir. Önbelleğe alınmış veri diski işlemi için ana bilgisayar önbelleği **SaltOkunur** veya **OkuYaz** moduna ayarlanır.  Önbelleğe alınmamış veri diski işlemi için ana bilgisayar önbelleği **Yok** moduna ayarlanır.
- VM 'leriniz için en iyi depolama performansını nasıl alabileceğinizi öğrenmek için bkz. [sanal makine ve disk performansı](../articles/virtual-machines/disks-performance.md).
- **Beklenen ağ bant** genişliği tüm ormanlarda tüm NIC 'lerde sanal makine türü başına ayrılan en yüksek bant genişliğine sahip olur. Daha fazla bilgi için bkz. [sanal makine ağ bant genişliği](../articles/virtual-network/virtual-machine-network-throughput.md).

  Üst sınırlar garanti edilmez. Hedeflenen uygulama için doğru sanal makine türünü seçmek için teklif kılavuzunu sınırlandırır. Gerçek ağ performansı, ağ tıkanıklığı, uygulama yüklemeleri ve ağ ayarları gibi çeşitli faktörlere bağlıdır. Ağ aktarım hızını iyileştirme hakkında daha fazla bilgi için bkz. [Azure sanal makineler için ağ aktarım hızını iyileştirme](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). Linux veya Windows üzerinde beklenen ağ performansını elde etmek için belirli bir sürüm seçmeniz veya VM 'nizi en iyi hale getirmeniz gerekebilir. Daha fazla bilgi için bkz. [bant genişliği/işleme testi (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



