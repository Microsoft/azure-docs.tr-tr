---
title: Azure VM boyutları-HPC | Microsoft Docs
description: Azure 'da yüksek performanslı bilgi işlem sanal makineleri için kullanılabilen farklı boyutları listeler. Bu serideki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772442"
---
# <a name="high-performance-computing-vm-sizes"></a>Yüksek performanslı bilgi işlem VM boyutları

Azure H serisi sanal makineler (VM 'Ler), çeşitli gerçek dünyada HPC iş yükleri için liderlik sınıfı performans, ölçeklenebilirlik ve maliyet verimliliği sağlamak üzere tasarlanmıştır.

[HBv3 serisi](hbv3-series.md) Sanal makineler, Sıvı dinamikleri, açık ve örtük sınırlı öğe analizi, hava durumu modelleme, seismik işleme, rezervoır benzetimi ve RTL simülasyonu gibi HPC uygulamaları için iyileştirilmiştir. HBv3 VM 'Leri 120 AMD EPI™ 7003-serisi (MILAN) CPU çekirdekleri, 448 GB RAM ve hiper iş parçacığı oluşturma özelliğine sahiptir. HBv3 serisi VM 'Ler ayrıca 350 GB/sn bellek bant genişliği, çekirdek başına en fazla 32 MB L3 önbellek, blok cihaz SSD performansı ve saat sıklıklarının 3,675 GHz 'ye kadar olan GB/sn sağlar. 

Tüm HBv3 serisi VM 'Ler, NVıDıA ağ üzerinden 200 GB/sn HDR InfiniBand özelliği, süper bilgisayar ölçekli MPı iş yüklerini etkinleştirir. Bu VM 'Ler, iyileştirilmiş ve tutarlı RDMA performansı için engelleyici olmayan bir FAT ağacına bağlanır. HDR InfiniBand doku Ayrıca, uyarlamalı yönlendirmeyi ve dinamik bağlı taşımayı (Standart RC ve UD aktarımlarına ek olarak) destekler. Bu özellikler uygulama performansını, ölçeklenebilirliği ve tutarlılığı geliştirir ve kullanımları kesinlikle önerilir.

[HBv2 serisi](hbv2-series.md) Sanal makineler, akıcı Dynamics, sınırlı öğe analizi ve rezervoır benzetimi gibi bellek bant genişliğine göre çalışan uygulamalar için iyileştirilmiştir. HBv2 VM 'Ler özelliği 120 AMD EPIC 7742 işlemci çekirdekleri, CPU çekirdeği başına 4 GB RAM ve eşzamanlı çoklu iş parçacığı yok. Her HBv2 VM, en fazla 340 GB/sn bellek bant genişliği ve en fazla 4 işlem FP64 işlem sağlar.

HBv2 VM 'Ler özelliği 200 GB/sn Mellanox HDR InfiniBand, her ikisi de HB ve HC Serisi VM 'Ler özelliği 100 GB/sn Mellanox EDR InfiniBand. Bu sanal makine türlerinin her biri, iyileştirilmiş ve tutarlı RDMA performansı için engelleyici olmayan bir FAT ağacına bağlanır. HBv2 VM 'Leri, uyarlamalı yönlendirmeyi ve standart RC ve UD aktarımlara ek olarak dinamik bağlı taşımayı (DCT) destekler. Bu özellikler uygulama performansını, ölçeklenebilirliği ve tutarlılığı geliştirir ve kullanımları kesinlikle önerilir.

[HB Serisi](hb-series.md) Sanal makineler, akıcı Dynamics, açık sınırlı öğe analizi ve hava durumu modelleme gibi bellek bant genişliğine göre çalışan uygulamalar için iyileştirilmiştir. HB VM 'Ler özelliği 60 AMD EPIC 7551 işlemci çekirdekleri, CPU çekirdeği başına 4 GB RAM ve hiper iş parçacığı yok. AMD EPYıC platformu 260 GB/sn 'den fazla bellek bant genişliği sağlar.

[HC Serisi](hc-series.md) VM 'Ler, örtük sınırlı öğe analizi, molesel Dynamics ve hesaplama Chemistry gibi yoğun hesaplama tarafından yönetilen uygulamalar için iyileştirilmiştir. HC VM 'Ler özelliği 44 Intel Xeon Platinum 8168 işlemci çekirdekleri, CPU çekirdeği başına 8 GB RAM ve hiper iş parçacığı yok. Intel Xeon Platinum platformu Intel Math çekirdek kitaplığı gibi yazılım araçlarının zengin ekosistemini destekler.

[H serisi](h-series.md) VM 'Ler, yüksek CPU sıklıklarca veya çekirdek gereksinimlerine göre büyük bellek kullanan uygulamalar için iyileştirilmiştir. H serisi VM 'Ler özelliği 8 veya 16 Intel Xeon E5 2667 v3 işlemci çekirdeği, 7 veya CPU çekirdeği başına 14 GB RAM ve hiper iş parçacığı yok. Uyumlu RDMA performansı için engelleyici olmayan bir FAT ağacı yapılandırmasında 56 GB/sn Mellanox FDR InfiniBand içindeki H Serisi özellikleri. H serisi VM 'Ler Intel MPı 5. x ve MS-MPı 'yi destekler.

> [!NOTE]
> Tüm HBv3, HBv2, HB ve HC Serisi VM 'Lerin fiziksel sunuculara özel erişimi vardır. Fiziksel sunucu başına yalnızca 1 VM vardır ve bu VM boyutları için diğer VM 'Ler ile paylaşılan çok kiracılı değildir.

> [!NOTE]
> [A8 – A11 VM 'leri](./sizes-previous-gen.md#a-series---compute-intensive-instances) 3/2021 itibariyle kullanımdan kaldırılır. Bu boyutlarda yeni VM dağıtımları artık mümkün değildir. Mevcut VM 'leriniz varsa, [HPC geçiş kılavuzundaki](https://azure.microsoft.com/resources/hpc-migration-guide/)diğer VM boyutlarına geçiş de dahil olmak üzere sonraki adımlar için e-postayla gönderilen bildirimlere bakın.

## <a name="rdma-capable-instances"></a>RDMA özellikli örnekler

HPC VM boyutlarının çoğu, uzak doğrudan bellek erişimi (RDMA) bağlantısı için bir ağ arabirimi özelliğidir. Seçili [N serisi](./nc-series.md) Boyutlar ' r ' ile ayrılmış olarak da RDMA özellikli. Bu arabirim, diğer VM boyutlarında bulunan standart Azure Ethernet ağ arabirimine ek niteliğindedir.

Bu ikincil arabirim, RDMA özellikli örneklerin bir InfiniBand (ıB) ağı üzerinden iletişim kurmasına izin verir, H16r, H16mr ve diğer RDMA özellikli N serisi sanal makineler için HBv3, HBv2, EDR ücretleri için, HB, HC, NDv2 ve Bu RDMA özellikleri, Ileti geçirme arabirimi (MPı) tabanlı uygulamaların ölçeklenebilirliğini ve performansını artırabilir.

> [!NOTE]
> **SR-IOV desteği**: Azure HPC 'de Şu anda ıNFINIBAND için SR-IOV etkinleştirilmiş olmasına bağlı olarak iki VM sınıfı vardır. Şu anda, Azure 'daki yeni nesil, RDMA özellikli veya Infiniband özellikli VM 'Ler, H16r, H16mr ve NC24r dışında SR-ıOV etkindir.
> RDMA yalnızca InfiniBand (ıB) ağı üzerinden etkinleştirilir ve tüm RDMA özellikli sanal makineler için desteklenir.
> IB üzerinden IP yalnızca SR-ıOV etkinleştirilmiş VM 'lerde desteklenir.
> RDMA, Ethernet ağı üzerinden etkinleştirilmemiş.

- CentOS, RHEL, Ubuntu, SUSE gibi **Işletim sistemi** Linux dağıtımları yaygın olarak kullanılır. Windows Server 2016 ve daha yeni sürümler, tüm HPC serisi sanal makinelerinde desteklenir. Windows Server 2012 R2 ve Windows Server 2012, SR-ıOV etkin olmayan VM 'lerde de desteklenir. [Windows Server 2012 R2 'nin, 64 ' den fazla (sanal veya fiziksel) çekirdekler olan VM boyutları olarak HBv2 Onlerde desteklenmediğini](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)unutmayın. Market 'te desteklenen VM görüntülerinin listesi için [VM görüntülerini](./workloads/hpc/configure.md) ve bunların nasıl uygun şekilde yapılandırılabileceğini görün. İlgili VM boyut sayfaları Ayrıca yazılım yığını desteğini de listeler.

- **InfiniBand ve sürücüler** -InfiniBand etkinleştirilmiş VM 'LERDE, RDMA 'yi etkinleştirmek için uygun sürücüler gereklidir. Market 'te desteklenen VM görüntülerinin listesi için [VM görüntülerini](./workloads/hpc/configure.md) ve bunların nasıl uygun şekilde yapılandırılabileceğini görün. Ayrıca bkz. sanal makine uzantıları veya Infiniband sürücülerini el ile yükleme hakkında bilgi edinmek için [InfiniBand 'yi etkinleştirme](./workloads/hpc/enable-infiniband.md) .

- **MPI** -Azure 'daki SR-ıOV etkinleştirilmiş VM boyutları, her türlü MPI 'ın Mellanox ile kullanılmasına izin verir. SR-ıOV olmayan VM 'lerde desteklenen MPı uygulamaları, VM 'Ler arasında iletişim kurmak için Microsoft ağ doğrudan (ND) arabirimini kullanır. Bu nedenle, yalnızca Intel MPı 5. x ve Microsoft MPı (MS-MPı) 2012 R2 veya sonraki sürümleri desteklenir. Intel MPı çalışma zamanı kitaplığı 'nın sonraki sürümleri, Azure RDMA sürücüleriyle uyumlu olmayabilir veya olmayabilir. Bkz. Azure üzerinde MPı üzerinde HPC VM 'Leri ayarlama hakkında daha fazla bilgi için bkz. [HPC Için Setup MPI](./workloads/hpc/setup-mpi.md) .

  > [!NOTE]
  > **RDMA ağ adresi alanı**: Azure 'daki RDMA ağı, 172.16.0.0/16 adres alanını ayırır. MPı uygulamalarını bir Azure sanal ağında dağıtılan örneklerde çalıştırmak için, sanal ağ adres alanının RDMA ağıyla çakışmadığından emin olun.

## <a name="cluster-configuration-options"></a>Küme yapılandırma seçenekleri

Azure, RDMA ağını kullanarak iletişim kurabilen HPC VM kümeleri oluşturmak için çeşitli seçenekler sunar; örneğin: 

- **Sanal makineler**  -RDMA özellikli HPC VM 'lerini aynı ölçek kümesine veya kullanılabilirlik kümesine dağıtın (Azure Resource Manager dağıtım modelini kullandığınızda). Klasik dağıtım modelini kullanıyorsanız, VM 'Leri aynı bulut hizmetinde dağıtın.

- **Sanal Makine Ölçek Kümeleri** -bir sanal makine ölçek kümesinde dağıtımı, ölçek kümesi içindeki InfiniBand iletişimi için tek bir yerleştirme grubuyla sınırlandırtığınızdan emin olun. Örneğin, bir Kaynak Yöneticisi şablonunda, `singlePlacementGroup` özelliğini olarak ayarlayın `true` . İle en büyük ölçek kümesi boyutunun, `singlePlacementGroup=true` Varsayılan olarak 100 sanal makinelerde kullanılabilir olduğunu unutmayın. HPC iş ölçeği, tek bir Kiracıdaki 100 sanal makinelerinden daha yüksekse, bir artış isteyebilir, [çevrimiçi müşteri destek talebi](../azure-portal/supportability/how-to-create-azure-support-request.md) ücretsiz olarak açabilirsiniz. Tek bir ölçek kümesindeki sanal makine sayısı sınırı 300 'e artırılabilir. Kullanılabilirlik kümeleri kullanarak VM 'Leri dağıttığınızda, en fazla sınır kullanılabilirlik kümesi başına 200 VM 'dir.

  > [!NOTE]
  > **Sanal makineler arasında MPI**: sanal makineler (VM 'ler) arasında RDMA (ör. MPI iletişimi kullanma) gerekiyorsa, VM 'lerin aynı sanal makine ölçek kümesi veya kullanılabilirlik kümesi içinde olduğundan emin olun.

- **Azure CycleCloud** -MPI işlerini çalıştırmak Için [Azure CYCLECLOUD](/azure/cyclecloud/) kullanarak bir HPC kümesi oluşturun.

- **Azure Batch** MPI iş yüklerini çalıştırmak için bir [Azure Batch](../batch/index.yml) havuzu oluşturun. MPı uygulamalarını Azure Batch ile çalıştırırken yoğun işlem yoğunluklu örnekler kullanmak için bkz. [Azure Batch 'de Ileti geçirme arabirimi (MPı) uygulamalarını çalıştırmak için çok örnekli görevleri kullanma](../batch/batch-mpi.md).

- **MICROSOFT HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) , RDMA özellikli Linux sanal makinelerinde DAĞıTıLDıĞıNDA Azure RDMA AĞıNı kullanan MS-MPI için bir çalışma zamanı ortamı içerir. Örneğin, dağıtımlar için bkz. [MPI uygulamalarını çalıştırmak IÇIN HPC Pack Ile LINUX RDMA kümesi ayarlama](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Dağıtma konuları

- **Azure aboneliği** : birkaç işlem yoğunluğu yoğun örneği dağıtmak için, Kullandıkça Öde aboneliğine veya diğer satın alma seçeneklerine göz önünde bulundurun. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/) kullanıyorsanız, yalnızca sınırlı sayıda Azure işlem çekirdeği kullanabilirsiniz.

- **Fiyatlandırma ve kullanılabilirlik** -Azure bölgelerinin [sanal makine fiyatlandırmasına](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ve [kullanılabilirliğine](https://azure.microsoft.com/global-infrastructure/services/) bakın.

- **Çekirdek kotası** – Azure aboneliğinizdeki çekirdek kotasını varsayılan değerden artırmanız gerekebilir. Aboneliğiniz Ayrıca, H serisi de dahil olmak üzere belirli VM boyutu ailelerinde dağıtabileceğiniz çekirdek sayısını sınırlayabilir. Kota artışı istemek için, ücretsiz [bir çevrimiçi müşteri destek isteği açın](../azure-portal/supportability/how-to-create-azure-support-request.md) . (Varsayılan sınırlar, abonelik kategorime bağlı olarak değişebilir.)

  > [!NOTE]
  > Büyük ölçekli kapasite gereksinimleriniz varsa Azure desteği 'ne başvurun. Azure kotaları, kapasite garantisi değil kredi limitlerdir. Kotasından bağımsız olarak yalnızca kullandığınız çekirdekler için ücretlendirilirsiniz.
  
- **Sanal ağ** : yoğun işlem yoğunluklu örnekleri kullanmak Için bir Azure [sanal ağı](https://azure.microsoft.com/documentation/services/virtual-network/) gerekli değildir. Ancak, birçok dağıtım için, şirket içi kaynaklara erişmeniz gerekiyorsa, en az bir bulut tabanlı Azure sanal ağı veya siteden siteye bağlantı gerekir. Gerektiğinde, örnekleri dağıtmak için yeni bir sanal ağ oluşturun. Benzeşim grubundaki bir sanal ağa işlem yoğunluklu VM 'Lerin eklenmesi desteklenmez.

- **Yeniden boyutlandırma** – özel donanımlar nedeniyle, yalnızca aynı büyüklükte aile (h serisi veya N serisi) içindeki işlem yoğunluğu örnekleri yeniden boyutlandırabilirsiniz. Örneğin, bir h serisi VM 'yi yalnızca bir H serisi boyutundan diğerine yeniden boyutlandırabilirsiniz. InfiniBand sürücü desteğinin ve NVMe disklerinin yanı sıra belirli VM 'Ler için göz önünde bulundurulması gerekebilir.


## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal makinelerinizi yapılandırma](./workloads/hpc/configure.md)hakkında daha fazla bilgi edinin, [InfiniBand 'Yi etkinleştirir](./workloads/hpc/enable-infiniband.md) [ve Azure](./workloads/hpc/setup-mpi.md) için HPC uygulamalarını [HPC iş yüklerinde](./workloads/hpc/overview.md)optimize edin.
- [HBv3-Series genel bakış](./workloads/hpc/hbv3-series-overview.md) ve [HC Serisi genel bakış](./workloads/hpc/hc-series-overview.md)konusunu gözden geçirin.
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
