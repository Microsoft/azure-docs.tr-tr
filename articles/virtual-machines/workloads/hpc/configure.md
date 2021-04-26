---
title: InfiniBand etkin H serisi ve N serisi Azure sanal makinelerinin yapılandırması ve Iyileştirmesi
description: HPC için InfiniBand etkin H serisi ve N serisi VM 'Ler yapılandırmak ve iyileştirmek hakkında bilgi edinin.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 470d5efae68366b5cc96243bab4ebb8552771650
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600888"
---
# <a name="configure-and-optimize-vms"></a>VM’leri yapılandırma ve iyileştirme

Bu makalede, HPC için InfiniBand özellikli [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'ler yapılandırma ve iyileştirerek bazı yönergeler paylaşır.

## <a name="vm-images"></a>VM görüntüleri
InfiniBand etkinleştirilmiş VM 'lerde, RDMA 'yı etkinleştirmek için uygun sürücüler gereklidir.
- Market 'teki [CentOS-HPC VM görüntüleri](#centos-hpc-vm-images) , uygun IB sürücüleriyle önceden yapılandırılmış olarak gelir ve başlamak için en kolay yoldur.
- [Ubuntu VM görüntüleri](#ubuntu-vm-images) , doğru IB sürücüleriyle yapılandırılabilir. Uygun sürücü ve yapılandırmayla [özel VM görüntüleri](../../linux/tutorial-custom-images.md) oluşturmanız ve bu yinelenen öğeyi yeniden kullanmanız önerilir.

GPU etkin [N serisi](../../sizes-gpu.md) VM 'lerde, uygun GPU sürücüleri [VM uzantıları](../../extensions/hpccompute-gpu-linux.md) veya [el ile](../../linux/n-series-driver-setup.md)eklenebilecek şekilde de gereklidir. Market 'teki bazı VM görüntüleri, NVIDIA 'daki bazı VM görüntüleri dahil olmak üzere NVIDIA GPU sürücüleriyle önceden yüklenmiş olarak da gelir.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM görüntüleri

#### <a name="sr-iov-enabled-vms"></a>SR-ıOV özellikli VM 'Ler
SR-ıOV özellikli [RDMA özellikli VM 'ler](../../sizes-hpc.md#rdma-capable-instances)Için, market sürüm 7,6 ve üzeri [üzerinde CENTOS-HPC VM görüntüleri](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) uygundur. Bu VM görüntüleri, RDMA ve çeşitli yaygın olarak kullanılan MPı kitaplıkları ve bilimsel bilgi işlem paketleri ve başlamak için en kolay yol ile en iyi duruma getirilmiş ve önceden yüklenmiş olarak gelir.
- CentOS-HPC sürüm 7,6 ve üzeri VM görüntülerine nelerin dahil olduğuna ilişkin ayrıntılar bir [Techcommunity makalesinde](https://techcommunity.microsoft.com/t5/Azure-Compute/CentOS-HPC-VM-Image-for-SR-IOV-enabled-Azure-HPC-VMs/ba-p/665557)bulunabilir.
- Bir taban CentOS Market görüntüsünden CentOS-HPC sürüm 7,6 ve üzeri VM görüntülerinin oluşturulmasında kullanılan betikler [azhpc-Images](https://github.com/Azure/azhpc-images/tree/master/centos)depolarından oluşur.
  
> [!NOTE] 
> En son Azure HPC Market görüntülerinin, ConnectX3-Pro InfiniBand kartlarını desteklemeyen, Mellanox OFED 5,1 ve üzeri bir sürüm vardır. SR-ıOV etkinleştirilmiş N serisi VM boyutları, FDR InfiniBand (ör. NCv3 ve üzeri) ile aşağıdaki CentOS-HPC VM görüntüsünü veya eski sürümlerini Market 'ten kullanabilir:
>- OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
>- OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
>- OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
>- OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
>- OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
>- OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>SR-ıOV etkin olmayan VM 'Ler
SR-ıOV olmayan [RDMA özellikli VM 'ler](../../sizes-hpc.md#rdma-capable-instances)için, CENTOS-HPC sürüm 6,5 veya sonraki bir sürümü, market 'te 7,4 ' e kadar uygundur. Örneğin, [H16 serisi VM 'ler](../../h-series.md)için 7,1 sürümleri 7,4 ' ye önerilir. Bu VM görüntüleri, RDMA ve Intel MPı sürüm 5,1 için ağ doğrudan sürücüleriyle önceden yüklenmiş olarak gelir.

> [!NOTE]
> SR-ıOV etkin olmayan VM 'Ler için bu CentOS tabanlı HPC görüntülerinde, **VUM** yapılandırma dosyasında çekirdek güncelleştirmeleri devre dışı bırakılır. Bunun nedeni, NetworkDirect Linux RDMA sürücülerinin bir RPM paketi olarak dağıtılmaktadır ve çekirdek güncelleştirilirse sürücü güncelleştirmeleri çalışmayabilir.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM görüntüleri
Market 'teki RHEL veya CentOS tabanlı, HPC olmayan VM görüntüleri, SR-ıOV özellikli [RDMA özellikli VM](../../sizes-hpc.md#rdma-capable-instances)'lerde kullanılmak üzere yapılandırılabilir. [InfiniBand 'yi etkinleştirme](enable-infiniband.md) ve VM 'lerde [MPI ayarlama](setup-mpi.md) hakkında daha fazla bilgi edinin.
- [Azhpc-Images](https://github.com/Azure/azhpc-images/tree/master/centos) depolarından bir taban CentOS Market görüntüsünden CENTOS-HPC sürüm 7,6 ve üzeri VM görüntülerinin oluşturulmasında kullanılan betikler de kullanılabilir.
  
> [!NOTE]
> Mellanox OFED 5,1 ve üzeri, FDR InfiniBand (ör. NCv3) ile SR-ıOV etkinleştirilmiş N serisi VM boyutlarında ConnectX3-Pro InfiniBand kartlarını desteklemez. Lütfen ConnectX3-Pro kartlarla birlikte N serisi VM 'de LTS Mellanox OFED Version 4.9-0.1.7.0 veya daha eski bir sürümü kullanın. Lütfen daha [fazla ayrıntı görüntüleyin](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).

### <a name="ubuntu-vm-images"></a>Ubuntu VM görüntüleri
Market 'teki Ubuntu Server 16,04 LTS, 18,04 LTS ve 20,04 LTS VM görüntüleri hem SR-ıOV hem de SR-ıOV olmayan [RDMA özellikli VM 'ler](../../sizes-hpc.md#rdma-capable-instances)için desteklenir. [InfiniBand 'yi etkinleştirme](enable-infiniband.md) ve VM 'lerde [MPI ayarlama](setup-mpi.md) hakkında daha fazla bilgi edinin.
- Ubuntu VM görüntülerinde InfiniBand 'yi etkinleştirmeye yönelik yönergeler bir [Techcommunity makalesinde](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)bulunur.
- Bir temel Ubuntu marketi görüntüsünden Ubuntu 18,04 ve 20,04 LTS tabanlı HPC VM görüntülerinin oluşturulmasında kullanılan betikler [azhpc-Images](https://github.com/Azure/azhpc-images/tree/master/ubuntu)depolarından oluşur.

### <a name="suse-linux-enterprise-server-vm-images"></a>VM görüntülerini SUSE Linux Enterprise Server
HPC için SLES 12 SP3, HPC için SLES 12 SP3 (Premium), HPC için SLES 12 SP1, HPC için SLES 12 SP1 (Premium), marketteki SLES 12 SP4 ve SLES 15 VM görüntüleri desteklenir. Bu VM görüntüleri, RDMA ve Intel MPı sürüm 5,1 için ağ doğrudan sürücüleriyle önceden yüklenmiş olarak gelir. VM 'lerde [MPI ayarlama](setup-mpi.md) hakkında daha fazla bilgi edinin.

## <a name="optimize-vms"></a>VM 'Leri iyileştirme

Aşağıda, sanal makinede iyileştirilmiş performans için bazı isteğe bağlı iyileştirme ayarları verilmiştir.

### <a name="update-lis"></a>LIS 'yi Güncelleştir

İşlevsellik veya performans için gerekliyse, [Linux Integration Services (LIS) sürücüleri](../../linux/endorsed-distros.md) desteklenen işletim sistemi distroları üzerinde yüklenebilir veya güncelleştirilemeyebilir, özellikle de özel bir görüntü veya CentOS/RHEL 6. x veya daha önceki 7. x sürümü gibi eski bir işletim sistemi sürümü kullanılarak dağıtımı yapılabilir.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>Belleği geri kazanma

Uzak bellek erişimini önlemek için otomatik olarak geri kazanma belleği aracılığıyla performansı artırabilirsiniz.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM yeniden başlatıldıktan sonra bunu kalıcı hale getirmek için:

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>Güvenlik duvarını ve SELinux 'u devre dışı bırakma

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Cpugücünü devre dışı bırak

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>Walınuxagent 'ı yapılandırma

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
İsteğe bağlı olarak, Walınuxagent, HPC iş yüküne maksimum VM kaynak kullanılabilirliği için iş öncesi adım ve iş sonrası için devre dışı bırakılmış olabilir.


## <a name="next-steps"></a>Sonraki adımlar

- InfiniBand özellikli [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'lerde [InfiniBand 'yi etkinleştirme](enable-infiniband.md) hakkında daha fazla bilgi edinin.
- VM 'lerde [desteklenen çeşitli MPI kitaplıklarını](setup-mpi.md) yükleme ve çalıştırma hakkında daha fazla bilgi edinin.
- [HBv3-Series genel bakış](hbv3-series-overview.md) ve [HC Serisi genel bakış](hc-series-overview.md)konusunu gözden geçirin.
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
