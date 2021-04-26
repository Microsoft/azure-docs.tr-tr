---
title: HBv2-Series VM 'ye genel bakış-Azure sanal makineleri | Microsoft Docs
description: Azure 'da HBv2 serisi VM boyutu hakkında bilgi edinin.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 59dd953b2116bc1ec7bd0a581cc181df64fbf49e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721156"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 serisi sanal makineye genel bakış 

 
Yüksek performanslı bilgi işlem (HPC) uygulama performansının AMD EPıC üzerinde en üst düzeye çıkarmak, düşünceli bir yaklaşım bellek konumu ve işlem yerleşimi gerektirir. Aşağıda, AMD EPıC mimarisini ve bu uygulamayı HPC uygulamaları için Azure 'da uygulamamız ana hatlarıyla planlıyoruz. Bir fiziksel NUMA etki alanına ve **sanal NUMA** 'ya BIR sanallaştırılmış NUMA etki alanına başvurmak Için **pnuma** terimini kullanacağız. 

Fiziksel olarak, bir [HBv2 serisi](../../hbv2-series.md) sunucu, toplam 128 7742 fiziksel çekirdek için 2 * 64-çekirdekli epyıc CPU olur. Bu 128 çekirdekler, her biri 4 çekirdek olan ve **çekirdek karmaşık** (veya **CCX**) olarak AMD tarafından oluşturulan 32 pnuma etki alanlarına (yuva başına 16) bölünmüştür. Her CCX 'in kendi L3 önbelleği vardır. Bu, bir işletim sisteminin bir pNUMA/vNUMA sınırını nasıl görebileceği anlamına gelir. Dört komşu CCXs, 2 fiziksel DRAM kanalına erişir. 

Azure Hiper Yöneticisi 'nin VM ile kesintiye uğramadan çalışması için oda sağlamak üzere fiziksel pNUMA etki alanlarını 0 ve 16 olarak ayırdık (her CPU yuvasının ilk CCX 'i). Kalan tüm 30 pNUMA etki alanları, sanal NUMA haline geldiği noktada VM 'ye atanır. Bu nedenle, VM şunları görür:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` VM başına çekirdek 

Sanal makinede, pNUMA 0 ve 16 ' nın ayrıldığı bir tanıma sahip değildir. 0-29 olarak gördüğü vNUMA 'yı, yuva için her zaman simetrik olarak 15 vNUMA, vSocket 0 üzerinde vNUMA 0-14 ve vSocket 1 üzerinde vNUMA 15-29 olarak numaralandırır. Sonraki bölümde, bu asimetrik NUMA düzeninde MPı uygulamalarının ne kadar iyi çalıştırılacağını gösteren yönergeler vardır. 

İşlem sabitleme, HBv2 serisi VM 'lerde çalışarak temel bir Silicon as 'yi konuk VM 'de kullanıma sunduk. En iyi performans ve tutarlılık için işlem sabitlenmesini önemle öneririz. 


## <a name="hardware-specifications"></a>Donanım belirtimleri 

| Donanım belirtimleri          | HBv2 serisi VM                   | 
|----------------------------------|----------------------------------|
| Çekirdekler                            | 120 (SMT devre dışı)               | 
| CPU                              | AMD EPYıC 7742                    | 
| CPU sıklığı (AVX olmayan)          | ~ 3,1 GHz (tek + tüm çekirdekler)    | 
| Bellek                           | 4 GB/çekirdek (480 GB toplam)         | 
| Yerel Disk                       | 960 GB NVMe (blok), 480 GB SSD (sayfa dosyası) | 
| InfiniBand                       | 200 GB/sn EDR Mellanox ConnectX-6 | 
| Ağ                          | 50 GB/sn Ethernet (40 GB/s kullanılabilir) Azure ikinci gen Smartnıc | 


## <a name="software-specifications"></a>Yazılım belirtimleri 

| Yazılım belirtimleri     | HBv2 serisi VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| Maksimum MPı Işi boyutu            | 36000 çekirdek (tek bir sanal makine ölçek kümesindeki Tekplacementgroup = true ile, 300 VM) |
| MPı desteği                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPı  |
| Ek çerçeveler       | UCX, libfabric, PGAS |
| Azure depolama desteği       | Standart ve Premium diskler (en fazla 8 disk) |
| SRLOV RDMA için işletim sistemi desteği   | CentOS/RHEL 7.6 +, Ubuntu 16.04 +, SLES 12 SP4 +, WinServer 2016 +  |
| Orchestrator desteği        | CycleCloud, Batch, AKS; [küme yapılandırma seçenekleri](../../sizes-hpc.md#cluster-configuration-options)  |

> [!NOTE] 
> Windows Server 2012 R2, 64 (sanal veya fiziksel) çekirdekleri HBv2 ve diğer VM 'lerde desteklenmez. Daha ayrıntılı bilgi için [buraya](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [AMD epi mimarisi](https://bit.ly/2Epv3kC) ve [çok yongalı mimariler](https://bit.ly/2GpQIMb)hakkında daha fazla bilgi edinin. Daha ayrıntılı bilgi için bkz. [AMD EPYıC işlemcileri Için HPC ayarlama Kılavuzu](https://bit.ly/2T3AWZ9).
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).