---
title: InfiniBand etkin H serisi ve N serisi VM 'lerde yüksek performanslı bilgi işlem-Azure sanal makineleri
description: ", HPC için iyileştirilmiş, InfiniBand etkin H serisi ve N serisi VM 'lerin özellikleri ve özellikleri hakkında bilgi edinin."
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 554764b89e5da4cd6777ec89fcb2f2d5ad104ebf
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600276"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>InfiniBand etkin H serisi ve N serisi VM 'lerde yüksek performanslı bilgi işlem

Azure 'un InfiniBand özellikli H serisi ve N serisi VM 'Ler, çok sayıda gerçek bir HPC ve AI iş yükleri için liderlik sınıfı performans, Ileti geçirme arabirimi (MPı) ölçeklenebilirliği ve maliyet verimliliği sunmak üzere tasarlanmıştır. Bu yüksek performanslı bilgi işlem (HPC) en iyi duruma getirilmiş VM 'Ler, bilim ve mühendislik açısından, örneğin: akışkan Dynamics, dünya modellemesi, hava durumu benzetimleri gibi en yoğun en zorlu sorunlardan bazılarını çözümlemek için kullanılır.

Bu makaleler, Azure 'daki InfiniBand özellikli H serisi ve N serisi VM 'lerde ve ölçeklenebilirlik için VM 'lerde HPC ve AI iş yüklerinin en iyi yapılandırmasına nasıl başlamanızı açıklamaktadır.

## <a name="features-and-capabilities"></a>Özellikler ve yetenekler

InfiniBand etkin H serisi ve N serisi VM 'Ler, HPC iş yükleri için en iyi HPC performansını, MPı ölçeklenebilirliğini ve maliyet verimliliğini sağlamak üzere tasarlanmıştır. VM 'lerin özellikleri ve yetenekleri hakkında daha fazla bilgi edinmek için bkz. [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'ler.

### <a name="rdma-and-infiniband"></a>RDMA ve InfiniBand

[RDMA özellikli](../../sizes-hpc.md#rdma-capable-instances) [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'ler, düşük gecikme süresi ve yüksek bant genişliğine sahip InfiniBand ağı üzerinden iletişim kurar. Bu tür bir bağlantı üzerinden RDMA özelliği, dağıtılmış düğüm HPC ve AI iş yüklerinin ölçeklenebilirliğini ve performansını artırmak için önemlidir. InfiniBand etkin H serisi ve N serisi VM 'Ler, en iyileştirilmiş ve tutarlı RDMA performansı için düşük çapta bir tasarıma sahip engelleyici olmayan bir FAT ağacına bağlanır.
InfiniBand özellikli VM 'lerde InfiniBand ayarlama hakkında daha fazla bilgi edinmek için bkz. [InfiniBand 'Yi etkinleştirme](enable-infiniband.md) .

### <a name="message-passing-interface"></a>İleti geçirme arabirimi

SR-ıOV etkin H serisi ve N serisi, neredeyse tüm MPı kitaplıklarını ve sürümlerini destekler. En yaygın olarak kullanılan MPı kitaplıklarının bazıları şunlardır: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, platform MPı. Tüm uzak doğrudan bellek erişimi (RDMA) fiilleri desteklenir.
Desteklenen çeşitli MPı kitaplıklarını ve en iyi yapılandırmalarını yükleme hakkında daha fazla bilgi edinmek için bkz. [MPI ayarlama](setup-mpi.md) .

## <a name="get-started"></a>başlarken

İlk adım, VM belirtimleri ve [RDMA özelliğine](../../sizes-hpc.md#rdma-capable-instances)göre iş yükü için en uygun [h serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) sanal makine türünü seçmemize yöneliktir.
İkinci olarak, InfiniBand 'yi etkinleştirerek sanal makineyi yapılandırın. Bu işlemi gerçekleştirmek için, sürücülerle birlikte iyileştirilmiş VM görüntülerini kullanma gibi çeşitli yöntemler vardır; bkz. [Linux iyileştirmesi](configure.md) ve Ayrıntılar için [InfiniBand 'ı etkinleştirin](enable-infiniband.md) .
Üçüncü olarak, dağıtılmış düğüm iş yükleri için MPı 'yi uygun şekilde seçip yapılandırın. Ayrıntılar için bkz. [MPI 'Yi ayarlama](setup-mpi.md) .
Dördüncü, performans ve ölçeklenebilirlik için, [HBv3-Series genel](hbv3-series-overview.md) bakış ve [HC Serisi genel bakış](hc-series-overview.md)gibi VM ailesine özgü kılavuzlardan yararlanarak iş yüklerini en iyi şekilde yapılandırın.

## <a name="next-steps"></a>Sonraki adımlar

- InfiniBand etkin [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'ler [yapılandırmak ve iyileştirmek](configure.md) hakkında bilgi edinin.
- Performans ve ölçeklenebilirlik için iş yüklerini en iyi şekilde yapılandırma hakkında bilgi edinmek için [HBv3-Series genel bakış](hb-series-overview.md) ve [HC Serisi genel bakışı](hc-series-overview.md) gözden geçirin
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- [Azure 'DA HPC uygulamalarını en iyi duruma getirmeye yönelik bir öğrenme modülüyle](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)bilginizi test edin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
