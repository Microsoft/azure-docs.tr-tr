---
title: İleti geçirme arabirimi oluşturan HPC - Azure sanal makineleri ayarlama | Microsoft Docs
description: Azure'da HPC için MPI ayarlama konusunda bilgi edinin.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 541e42a72ea604c4d71dc546b14dea2f0857bcc1
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797506"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>İleti geçirme arabirimi oluşturan için HPC Kümesi

İleti geçirme arabirimi (MPI) iş yükleri, geleneksel HPC iş yüklerinin önemli bir parçasıdır. SR-IOV etkin VM boyutları, Azure üzerinde neredeyse izin herhangi kullanılacak MPI flavor. 

MPI işlerini VM'ler üzerinde çalışan bir kiracıda bölüm anahtarları (p-anahtarlar) ayarlama gerektirir. Bağlantısındaki [bölüm anahtarlarını bulmak](#discover-partition-keys) p-anahtar değer belirleme hakkında ayrıntılar için bölüm.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) IB ve MPICH ve OpenMPI çalışır en iyi performansı sunar.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Daha önce açıklandığı gibi UCX yükleyin.

```bash
sudo yum install –y openmpi
```

OpenMPI oluşturun.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Run OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Yukarıda da belirtildiği gibi bölüm anahtarı denetleyin.

## <a name="mpich"></a>MPICH

Daha önce açıklandığı gibi UCX yükleyin.

MPICH oluşturun.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

MPICH çalışıyor.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Yukarıda da belirtildiği gibi bölüm anahtarı denetleyin.

## <a name="mvapich2"></a>MVAPICH2

MVAPICH2 oluşturun.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

MVAPICH2 çalışıyor.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platform MPI Community sürümü

Platform MPI için gerekli paketleri yükleyin.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Yükleme işlemini uygulayın.

## <a name="intel-mpi"></a>Intel MPI

[Intel MPI indirme](https://software.intel.com/mpi-library/choose-download).

I_MPI_FABRICS ortam değişkenini sürümüne bağlı olarak değiştirin. Intel MPI 2018 için kullanmak `I_MPI_FABRICS=shm:ofa` ve 2019 için `I_MPI_FABRICS=shm:ofi`.

Sabitleme işlemi düzgün şekilde 15, 30 ve 60 PPN için varsayılan olarak çalışır.

## <a name="osu-mpi-benchmarks"></a>OSU MPI Kıyaslama

[OSU MPI Kıyaslama indirme](http://mvapich.cse.ohio-state.edu/benchmarks/) ve untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Belirli bir MPI kitaplığı kullanarak Kıyaslama derleme:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI Kıyaslama altındadır `mpi/` klasör.


## <a name="discover-partition-keys"></a>Bölüm anahtarları keşfedin

Diğer Vm'lerle aynı kiracıda (kullanılabilirlik kümesi veya VM ölçek kümesi) ile iletişim kurmak için bölüm anahtarları (p-keys) keşfedin.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

İki MPI ile kullanılması gereken Kiracı anahtarınızı büyüktür. Örnek: Aşağıdaki p-keys varsa 0x800b MPI ile kullanılmalıdır.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Varsayılan (0x7fff) bölüm anahtarı dışında bir bölümü kullanın. Temizlenecek p-anahtarın Tamamlama UCX gerektirir. Örneğin, UCX_IB_PKEY 0x000b 0x800b için olarak ayarlayın.

Ayrıca, Kiracı (AVSet veya VMSS) mevcut olduğu sürece, PKEYs aynı kalmasını unutmayın. Düğümleri eklenen ve Silinen olsa bile bu geçerlidir. Yeni kiracılar farklı PKEYs alın.


## <a name="set-up-user-limits-for-mpi"></a>Kullanıcı sınırları ' için MPI Kümesi

Kullanıcı sınırları ' için MPI ayarlayın.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>SSH anahtarları yedeklemek için MPI Kümesi

SSH anahtarları gerektiren MPI türleri için ayarlayın.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Yukarıdaki sözdizimi bir paylaşılan giriş dizini varsayar, her düğüme başka .ssh dizine kopyalanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinin [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) azure'da.
