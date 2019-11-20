---
title: Yüksek performanslı bilgi işlem gerçekleştiren H serisi Vm'lerde - Azure sanal makineler | Microsoft Docs
description: Özellikler ve yetenekler, HPC için en iyi duruma getirilmiş H serisi VM'ler hakkında bilgi edinin.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 07/02/2019
ms.author: amverma
ms.openlocfilehash: d6e857a87e4c7df8ffb2be1eefb7a0290da5b10a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67800067"
---
# <a name="high-performance-computing-on-h-series-vms"></a>Yüksek performanslı bilgi işlem H serisi Vm'lerde

Yüksek performanslı bilgi işlem (HPC) HB serisi ve HC serisi VM'ler üzerinde en iyileştirilmiş tüm Vm'leri azure'da HPC performansını etkinleştirin. HPC için iyileştirilmiş, VM'ler gibi bazı matematik en zor sorunları çözmek için kullanılır: sıvı dinamiği, Petrol ve gaz simülasyonlar ve hava durumu modelleme.

Bu makale, önemli özelliklerinden bazıları HB serisi ve HC serisi VM'ler, neden bu VM'ler de HPC senaryoları ve kullanmaya nasıl başlayacağınızı gerçekleştirme kapsar.

## <a name="features-and-capabilities"></a>Özellikler ve yetenekler

HB serisi ve HC serisi VM'ler en iyi bir HPC performansı, ileti geçirme arabirimi (MPI) ölçeklenebilirlik sağlamak üzere tasarlanmış ve maliyet verimliliği HPC iş yükleri için.

### <a name="message-passing-interface"></a>İleti geçirme arabirimi

HB serisi ve HC serisi neredeyse tüm MPI türleri ve sürümleri destekler. En yaygın, desteklenen MPI türlerinden bazıları şunlardır: OpenMPI, MVAPICH2, Platform MPI, Intel MPI ve doğrudan uzak bellek (RDMA) fiilleri erişin. Daha fazla bilgi için [ileti geçirme arabirimi oluşturan için HPC Kümesi](setup-mpi.md).

### <a name="rdma-and-infiniband"></a>RDMA ve Infiniband

HB serisi ve HC serisi Vm'lerde standart RDMA arabirimidir. RDMA özellikli örnekler HB serisi ve HC serisi sanal makineler için geliştirilmiş veri fiyatlarıyla (EDR) çalışan bir InfiniBand ağ üzerinden iletişim kurar. RDMA özellikli örnekler ölçeklenebilirlik ve bazı MPI uygulamaları performansını artırabilir.

HC serisi VM'ler HB serisi destekleyen InfiniBand yapılandırma engelleyici olmayan fat ağaçları RDMA tutarlı bir performans için düşük çapı tasarım.

Bkz: [etkinleştirme InfiniBand](enable-infiniband.md) InfiniBand HB serisi veya HC serisi VM'ler ayarlama hakkında daha fazla bilgi için.

## <a name="get-started"></a>başlarken

İlk olarak, kullanılacak gideceğinizi H serisi VM karar verin. HPC hakkında ayrıntılar için iyileştirilmiş VM'ler için bkz: [HB serisi Genel Bakış](hb-series-overview.md) ve [HC serisi Genel Bakış](hc-series-overview.md). Belirtimleri için bkz: [yüksek performanslı işlem VM boyutları](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc).

Seçili ve uygulamanız için oluşturulan bir VM'yi sonra InfiniBand etkinleştirerek yapılandırmanız gerekir. Hem Windows hem de Linux Vm'lerinin InfiniBand etkinleştirme hakkında bilgi için bkz: [etkinleştirme InfiniBand](enable-infiniband.md).

HPC iş yüklerinin önemli bir bileşeni MPI ' dir. HB serisi ve HC serisi neredeyse tüm MPI türleri ve sürümleri destekler. Daha fazla bilgi için [ileti geçirme arabirimi oluşturan için HPC Kümesi](setup-mpi.md).

Infiniband ve MPI, ayarlama, VM serisi belirledikten sonra HPC iş yüklerinizi oluşturmaya başlamak hazırsınız demektir.

## <a name="next-steps"></a>Sonraki adımlar

- Gözden geçirme [HB serisi Genel Bakış](hb-series-overview.md) ve [HC serisi Genel Bakış](hc-series-overview.md) temel farklılıklar ve özellikleri hakkında bilgi edinmek için.

- Daha yüksek bir düzeyi için HPC iş yüklerinin çalışan mimari görmek [yüksek performanslı bilgi işlem (HPC) azure'da](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/).
