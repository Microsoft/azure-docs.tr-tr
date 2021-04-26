---
title: HBv3-Series VM genel bakış, mimari, topoloji-Azure sanal makineleri | Microsoft Docs
description: Azure 'da HBv3 serisi VM boyutu hakkında bilgi edinin.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608293"
---
# <a name="hbv3-series-virtual-machine-overview"></a>HBv3-serisi sanal makineye genel bakış 

Bir [HBv3 serisi](../../hbv3-series.md) sunucu, Toplam 128 fiziksel "Zen3" çekirdeği için 2 * 64-çekirdekli epyıc 7V13 CPU özelliklerine sahiptir. Eş zamanlı çoklu Iş parçacığı (SMT) HBv3 üzerinde devre dışıdır. Bu 128 çekirdekler, 16 MB 'lık 32 bir önbellekte tek biçimli erişimli, 8 işlemci çekirdeği içeren her bölüm 16 bölüme (yuva başına 8) bölünmüştür. Azure HBv3 sunucuları aşağıdaki AMD BIOS ayarlarını da çalıştırır:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Sonuç olarak, sunucu her 32 çekirdekte çekirdek olarak 4 NUMA etki alanı (yuva başına 2) ile önyüklenir. Her NUMA, 3200 MT/sn tarihinde fiziksel DRAM 'nin 4 kanala doğrudan erişmesini sağlar.

Azure Hiper Yöneticisi 'nin VM ile kesintiye uğramadan çalışması için oda sağlamak üzere sunucu başına 8 fiziksel çekirdek ayırdık.

## <a name="vm-topology"></a>VM topolojisi

Aşağıdaki diyagramda sunucusunun topolojisi gösterilmektedir. Bu 8 hiper yönetici ana bilgisayar çekirdeğini (sarı), her NUMA etki alanındaki belirli çekirdek karmaşık kollarından (CCD) ilk 2 çekirdeği HBv3 serisi VM (yeşil) için kalan çekirdeklerle ayırarak, her iki CPU yuvası arasında simetrik olarak ayırdık.

![HBv3-Series sunucusunun topolojisi](./media/architecture/hbv3/hbv3-topology-server.png)

CCD sınırının bir NUMA sınırına eşit olmadığına unutmayın. HBv3 üzerinde dört ardışık (4) CCD grubu, hem konak sunucu düzeyinde hem de Konuk VM içinde bir NUMA etki alanı olarak yapılandırılır. Bu nedenle, tüm HBv3 VM boyutları aşağıda gösterildiği gibi bir işletim sistemi ve uygulama için, her biri belirli [HBV3 VM boyutuna](../../hbv3-series.md)bağlı olarak farklı sayıda çekirdekte olacak şekilde, bir işletim sistemi ve uygulamada görünecek 4 NUMA etki alanı sunar.

![HBv3 serisi VM 'nin topolojisi](./media/architecture/hbv3/hbv3-topology-vm.png)

Her HBv3 VM boyutu, aşağıdaki gibi AMD EPYC 7003-Series ' ten farklı bir CPU 'nun fiziksel düzeni, özellikleri ve performansı ile benzerdir:

| HBv3 serisi VM boyutu             | NUMA etki alanları | NUMA etki alanı başına çekirdek  | AMD EPRIVC ile benzerlik         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | Çift Soketli EPYıC 7713            |
Standard_HB120r 96s_v3            | 4            | 24                     | Çift Soketli EPYıC 7643            |
Standard_HB120r 64s_v3            | 4            | 16                     | Çift Soketli EPYıC 7543            |
Standard_HB120r 32s_v3            | 4            | 8                      | Çift Soketli EPYıC 7313            |
Standard_HB120r 16s_v3            | 4            | 4                      | Çift Soketli EPYıC 72F3            |

> [!NOTE]
> Kısıtlanmış çekirdekler VM boyutları yalnızca VM 'ye açık olan fiziksel çekirdek sayısını azaltır. Tüm genel paylaşılan varlıklar (RAM, bellek bant genişliği, L3 önbelleği, GMı ve Xgmı bağlantısı, InfiniBand, Azure Ethernet ağı, yerel SSD) sabit kalır. Bu, bir müşterinin belirli bir iş yükü veya yazılım lisanslama gereksinimlerine göre en uygun bir VM boyutu seçmesine olanak sağlar.

Her bir HBv3 VM boyutunun sanal NUMA eşlemesi, temeldeki fiziksel NUMA topolojisine eşlenir. Donanım topolojisinin potansiyel olarak yanıltıcı soyutlama yoktur. 

Çeşitli [HBV3 VM boyutu](../../hbv3-series.md) için tam topoloji, [lstopo](https://linux.die.net/man/1/lstopo)çıkışı kullanılarak şu şekilde görünür:
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Standard_HB120rs_v3 için lstopo çıkışını görüntülemek için tıklayın</summary>

![HBv3 için lstopo çıkışı-120 VM](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-96_v3 için lstopo çıkışını görüntülemek için tıklayın</summary>

![HBv3-96 VM için lstopo çıkışı](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-64_v3 için lstopo çıkışını görüntülemek için tıklayın</summary>

![HBv3-64 VM için lstopo çıkışı](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-32_v3 için lstopo çıkışını görüntülemek için tıklayın</summary>

![HBv3-32 VM için lstopo çıkışı](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-16_v3 için lstopo çıkışını görüntülemek için tıklayın</summary>

![HBv3-16 VM için lstopo çıkışı](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>InfiniBand ağ iletişimi
HBv3 VM 'Leri, 200 Gigabit/sn 'ye kadar çalışan NVIDIA Mellanox HDR InfiniBand ağ bağdaştırıcıları (ConnectX-6) özelliğini de kullanabilir. NIC, SRLOV aracılığıyla sanal makineye geçirilir ve ağ trafiğini hiper yöneticiyi atlayacak şekilde etkinleştirir. Sonuç olarak, müşteriler çıplak bir ortama göre HBv3 VM 'lerinde standart Mellanox Ed sürücüleri yükler.

HBv3 VM 'Leri, uyarlamalı yönlendirmeyi, dinamik bağlı taşımayı (Standart RC ve UD aktarımlarına ek olarak DCT) ve MPı 'nin donanım tabanlı yük boşaltma 'yi, ConnectX-6 bağdaştırıcısının yerleşik işlemcisine göre destekler. Bu özellikler uygulama performansı, ölçeklenebilirliği ve tutarlılığı geliştirir ve bunların kullanımı kesinlikle önerilir.

## <a name="temporary-storage"></a>Geçici depolama
HBv3 VM 'Ler özelliği 2 fiziksel olarak yerel SSD cihazları. Bir cihaz, sayfa dosyası olarak kullanılmak üzere önceden biçimlendirilmiş olur ve VM 'niz içinde genel "SSD" cihazı olarak görünür.

Diğer iki, daha büyük SSD 'ler NVMeDirect aracılığıyla biçimlendirilmemiş bir NVMe cihaz olarak sağlanır. Blok NVMe cihazı hiper yöneticiyi atladığından, ıOP başına daha yüksek bant genişliğine, yüksek ıOPS ve düşük gecikme süresine sahip olur.

Bir şeritli dizide eşlendiğinde NVMe SSD, en fazla 7 GB/sn okuma ve 3 GB/s yazma ve derin sıra derinlikleri için 186.000 ıOPS (okuma) ve 201.000 ıOPS (yazma) sağlar.

## <a name="hardware-specifications"></a>Donanım belirtimleri 

| Donanım belirtimleri          | HBv3 serisi VM 'Ler              |
|----------------------------------|----------------------------------|
| Çekirdekler                            | 120, 96, 64, 32 veya 16 (SMT devre dışı)               | 
| CPU                              | AMD EPYıC 7V13                   | 
| CPU sıklığı (AVX olmayan)          | 3,1 GHz (tüm çekirdekler), 3,675 GHz (en fazla 10 çekirdek)    | 
| Bellek                           | 448 GB (çekirdek başına RAM VM boyutuna bağlıdır)         | 
| Yerel Disk                       | 2 * 960 GB NVMe (blok), 480 GB SSD (sayfa dosyası) | 
| InfiniBand                       | 200 GB/s Mellanox ConnectX-6 HDR InfiniBand | 
| Ağ                          | 50 GB/sn Ethernet (40 GB/s kullanılabilir) Azure ikinci gen Smartnıc | 

## <a name="software-specifications"></a>Yazılım belirtimleri 

| Yazılım belirtimleri        | HBv3 serisi VM 'Ler                                            | 
|--------------------------------|-----------------------------------------------------------|
| Maksimum MPı Işi boyutu               | 36.000 çekirdek (tek bir sanal makine ölçek kümesindeki Tekplacementgroup = true ile, 300 VM) |
| MPı desteği                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Ek çerçeveler          | UCX, libfabric, PGAS                  |
| Azure depolama desteği          | Standart ve Premium diskler (en fazla 32 disk)              |
| SRLOV RDMA için işletim sistemi desteği      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Performans için önerilen işletim sistemi | CentOS 8,1, Windows Server 2019 +
| Orchestrator desteği           | Azure CycleCloud, Azure Batch, AKS; [küme yapılandırma seçenekleri](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2, 64 (sanal veya fiziksel) çekirdekleri HBv3 ve diğer VM 'lerde desteklenmez. Daha ayrıntılı bilgi için [buraya](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
