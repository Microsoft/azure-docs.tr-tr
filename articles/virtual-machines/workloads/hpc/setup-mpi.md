---
title: HPC için Ileti geçirme arabirimi (MPı) ayarlama-Azure sanal makineleri | Microsoft Docs
description: Azure 'da HPC için MPı ayarlamayı öğrenin.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f43fc94174ebdcfdf447d3635a696193959849fa
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600304"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC için Ileti geçirme arabirimini ayarlama

[Ileti geçirme arabirimi (MPı)](https://en.wikipedia.org/wiki/Message_Passing_Interface) , açık bir kitaplıktır ve dağıtılmış bellek paralelleştirme için standart olarak standart bir kitaplıktır. Birçok HPC iş yükü genelinde yaygın olarak kullanılır. [RDMA özellikli](../../sizes-hpc.md#rdma-capable-instances) [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'lerde HPC iş yükleri, düşük gecikme süresi ve yüksek bant genişliği InfiniBand ağı üzerinden iletişim kurmak için MPI kullanabilir.
- Azure 'daki SR-ıOV etkinleştirilmiş VM boyutları, her türlü MPı 'nin Mellanox ile kullanılmasına izin verir.
- SR-ıOV olmayan VM 'lerde desteklenen MPı uygulamaları, VM 'Ler arasında iletişim kurmak için Microsoft ağ doğrudan (ND) arabirimini kullanır. Bu nedenle, yalnızca Microsoft MPı (MS-MPı) 2012 R2 veya üzeri ve Intel MPı 5. x sürümleri desteklenir. Intel MPı çalışma zamanı kitaplığı 'nın sonraki sürümleri (2017, 2018), Azure RDMA sürücüleriyle uyumlu olmayabilir veya olmayabilir.

SR-ıOV özellikli [RDMA özellikli VM 'ler](../../sizes-hpc.md#rdma-capable-instances)için, [CENTOS-HPC VM görüntüleri](configure.md#centos-hpc-vm-images) sürüm 7,6 ve üzeri uygundur. Bu VM görüntüleri, RDMA ve çeşitli yaygın olarak kullanılan MPı kitaplıkları ve bilimsel bilgi işlem paketleri ve başlamak için en kolay yol ile en iyi duruma getirilmiş ve önceden yüklenmiş olarak gelir.

Buradaki örnekler RHEL/CentOS için de olsa da, adımlar genel ve Ubuntu (16,04, 18,04 19,04, 20,04) ve SLES (12 SP4 ve 15) gibi uyumlu bir Linux işletim sistemi için kullanılabilir. Diğer MPı uygulamalarını diğer uygulamalar üzerinde ayarlamaya yönelik daha fazla örnek [azhpc-Images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)deposunsunlar.

> [!NOTE]
> MPı işlerinin, belirli MPı kitaplıklarına (Platform MPı gibi) sahip SR-ıOV özellikli VM 'lerde çalıştırılması, bir kiracı genelinde yalıtım ve güvenlik için bölüm anahtarlarının (p-anahtarlar) ayarlanmasını gerektirebilir. P anahtar değerlerini belirleme ve bu MPı kitaplığı ile bir MPı işi için doğru ayarlama hakkında ayrıntılar için [bölüm anahtarlarını bul](#discover-partition-keys) bölümündeki adımları izleyin.

> [!NOTE]
> Aşağıdaki kod parçacıkları örnektir. Paketlerin en son kararlı sürümlerini kullanmanızı veya [azhpc-Images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)deposuna başvurmalarını öneririz.

## <a name="choosing-mpi-library"></a>MPı kitaplığı seçme
HPC uygulaması belirli bir MPı kitaplığı öner, bu sürümü önce deneyin. Hangi MPı 'nin seçebileceği konusunda esneklik varsa ve en iyi performansı istiyorsanız, HPC-X ' i deneyin. Genel olarak, HPC-X MPı, InfiniBand arabirimi için UCX çerçevesini kullanarak en iyi şekilde çalışır ve tüm Mellanox InfiniBand donanım ve yazılım yeteneklerinin avantajlarından yararlanır. Ayrıca, HPC-X ve OpenMPI ABı uyumlu olduğundan, OpenMPI ile oluşturulmuş HPC-X ile bir HPC uygulamasını dinamik olarak çalıştırabilirsiniz. Benzer şekilde, Intel MPI, MVAPICH ve MPICH ABı uyumludur.

Aşağıdaki şekilde popüler MPı kitaplıkları için mimari gösterilmektedir.

![Popüler MPı kitaplıkları için mimari](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[Birleşik Iletişim X (UCX)](https://github.com/openucx/ucx) HPC Için Iletişim API 'leri çerçevesidir. InfiniBand üzerinden MPı iletişimi için en iyi duruma getirilmiştir ve OpenMPI ve MPICH gibi birçok MPı uygulaması ile işe yarar.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Yeni bir UCX derlemesi, birden çok NIC arabirimi olması durumunda doğru InfiniBand arabiriminin seçildiği bir [sorunu](https://github.com/openucx/ucx/pull/5965) düzelttik. VM 'de hızlandırılmış ağ etkinleştirildiğinde, InfiniBand üzerinde MPı 'nin çalıştırılmasına ilişkin daha fazla ayrıntı [bulabilirsiniz](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) .

## <a name="hpc-x"></a>HPC-X

[HPC-x yazılım araç seti](https://www.mellanox.com/products/hpc-x-toolkit) , UCX ve hcoll içerir ve UCX 'e göre derlenebilir.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Aşağıdaki komut, HPC-X ve OpenMPI için önerilen bazı mpırun bağımsız değişkenlerini gösterir.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
burada:

|Parametre|Açıklama                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |MPı işlemlerinin sayısını belirtir. Örneğin: `-n 16`.|
|`$HOSTFILE`|MPı işlemlerinin çalışacağı konumu belirtmek için, ana bilgisayar adı veya IP adresini içeren bir dosyayı belirtir. Örneğin: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Her NUMA etki alanında çalıştırılacak MPı işlemlerinin sayısını belirtir. Örneğin, NUMA başına dört MPı işlemi belirtmek için kullanırsınız `--map-by ppr:4:numa:pe=1` .|
|`$NUMBER_THREADS_PER_PROCESS`  |MPı işlemi başına iş parçacığı sayısını belirtir. Örneğin, bir MPı işlemi ve NUMA başına dört iş parçacığı belirtmek için kullanırsınız `--map-by ppr:1:numa:pe=4` .|
|`-report-bindings` |MPI işlem sabitlemenin doğru olduğunu doğrulamak için kullanışlı olan çekirdekler ile eşleşen MPı işlemleri yazdırır.|
|`$MPI_EXECUTABLE`  |MPI kitaplıklarında oluşturulan MPı yürütülebilirini belirtir. MPı derleyici sarmalayıcıları bunu otomatik olarak yapın. Örneğin: `mpicc` veya `mpif90` .|

OSU gecikme mikro kıyaslama süresini çalıştırmaya örnek olarak aşağıdaki gibidir:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>MPı collectları iyileştirme

MPı toplu iletişim temelleri, Grup iletişim işlemlerini uygulamak için esnek ve taşınabilir bir yöntem sunar. Bunlar, çeşitli bilimsel paralel uygulamalar genelinde yaygın olarak kullanılır ve genel uygulama performansı üzerinde önemli bir etkiye sahiptir. Toplu iletişim için HPC-X ve HCOLL kitaplığı kullanılarak toplu iletişim performansını iyileştirmek üzere yapılandırma parametreleriyle ilgili ayrıntılar için [Techcommunity makalesine](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) başvurun.

Örnek olarak, sıkı şekilde bağlanmış MPı uygulamanızın aşırı miktarda toplu iletişim yaptığına karşı kuşkulanıyorsanız, hiyerarşik toplanabilir (HCOLL) etkinleştirmeyi deneyebilirsiniz. Bu özellikleri etkinleştirmek için aşağıdaki parametreleri kullanın.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> HPC-X 2.7.4 + ile, MOFED üzerindeki UCX sürümü, HPC-X ' de farklıysa LD_LIBRARY_PATH açıkça geçirmek gerekebilir.

## <a name="openmpi"></a>OpenMPI

Yukarıda açıklanan şekilde UCX 'i yükler. HCOLL, [HPC-X yazılım araç setinin](https://www.mellanox.com/products/hpc-x-toolkit) parçasıdır ve özel yükleme gerektirmez.

Depoda bulunan paketten OpenMPI yüklenebilir.

```bash
sudo yum install –y openmpi
```

UCX ile OpenMPI 'nin en son, kararlı bir sürümünü oluşturmanızı öneririz.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

En iyi performans için, ve ile OpenMPI ' yi çalıştırın `ucx` `hcoll` . Ayrıca [HPC-X](#hpc-x)ile örneğe bakın.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Yukarıdaki bölümde belirtilen bölüm anahtarınızı denetleyin.

## <a name="intel-mpi"></a>Intel MPı

[Intel MPI](https://software.intel.com/mpi-library/choose-download)'nin seçtiğiniz sürümünü indirin. Intel MPı 2019 sürümü Open Fabric Alliance (OFA) çerçevesinden açık yapılar arabirimleri (OFı) çerçevesine geçti ve şu anda libfabric 'i destekliyor. InfiniBand desteği için iki sağlayıcı vardır: MLX ve fiiller.
Sürüme bağlı olarak I_MPI_FABRICS ortam değişkenini değiştirin.
- Intel MPı 2019 ve 2021: kullanın `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . `mlx`Sağlayıcı UCX kullanır. Fiillerin kullanım kararlı ve daha az performans olduğunu belirledi. Daha fazla bilgi için bkz. [Techcommunity makalesi](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) .
- Intel MPı 2018: kullanma `I_MPI_FABRICS=shm:ofa`
- Intel MPı 2016: kullanma `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Intel MPı 2019 güncelleştirme 5 + için önerilen bazı mpırun bağımsız değişkenleri aşağıda verilmiştir.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
burada:

|Parametre|Açıklama                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Kullanılacak olan libfabric sağlayıcısını belirtir; bu işlem API, protokol ve kullanılan ağı etkiler. fiiller başka bir seçenektir, ancak genellikle MLX size daha iyi performans sağlar.|
|`I_MPI_DEBUG`|İşlemin nerede sabitlendiği ve hangi protokol ve ağın kullanıldığı hakkında ayrıntı sağlayabilen ek hata ayıklama çıkışının düzeyini belirtir.|
|`I_MPI_PIN_DOMAIN` |İşlemlerinizi nasıl sabitlemek istediğinizi belirtir. Örneğin, çekirdekler, yuvalar veya NUMA etki alanlarına sabitleyebilir. Bu örnekte, bu ortam değişkenini NUMA olarak ayarlarsınız, bu da işlemlerin NUMA düğümü etki alanlarına sabitlenebileceği anlamına gelir.|

### <a name="optimizing-mpi-collectives"></a>MPı collectları iyileştirme

Özellikle de toplu işlemler önemli bir süre kullanıyorsa, deneyebileceğiniz bazı diğer seçenekler vardır. Intel MPı 2019 güncelleştirme 5 +, MLX sağlamasını destekler ve InfiniBand ile iletişim kurmak için UCX çerçevesini kullanır. Ayrıca, HCOLL 'yi destekler.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>SR-ıOV olmayan VM 'Ler

SR-ıOV olmayan VM 'Ler için, 5. x çalışma zamanı [ücretsiz değerlendirme sürümünü](https://registrationcenter.intel.com/en/forms/?productid=1740) karşıdan yüklemeyle ilgili bir örnek aşağıdaki gibidir:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Yükleme adımları için bkz. [Intel MPI kitaplığı yükleme kılavuzu](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
İsteğe bağlı olarak, kök olmayan hata ayıklayıcı olmayan işlemlere yönelik ptrace 'i etkinleştirmek isteyebilirsiniz (Intel MPı 'nin en son sürümleri için gereklidir).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
SUSE Linux Enterprise Server VM görüntü sürümleri-HPC için SLES 12 SP3, HPC için SLES 12 SP3, HPC için SLES 12 SP1, HPC için SLES 12 SP1, HPC için SLES 12 SP1, SLES 12 SP4 ve SLES 15, RDMA sürücüleri yüklenir ve Intel MPı paketleri sanal makinede dağıtılır. Aşağıdaki komutu çalıştırarak Intel MPı 'yi çalıştırın:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Aşağıda MVAPICH2 oluşturma örneği verilmiştir. Daha yeni sürümler aşağıda kullanılandan daha fazla kullanılabilir olabilir.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

OSU gecikme mikro kıyaslama süresini çalıştırmaya örnek olarak aşağıdaki gibidir:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

Aşağıdaki listede birkaç önerilen `mpirun` bağımsız değişken bulunmaktadır.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
burada:

|Parametre|Açıklama                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Hangi bağlama ilkesinin kullanılacağını belirtir; Bu, işlemlerin temel kimliklere nasıl sabitlendiğini etkiler. Bu durumda dağılım belirtirsiniz, böylece işlem NUMA etki alanları arasında eşit olarak dağılmış olur.|
|`MV2_CPU_BINDING_LEVEL`|İşlemlerin nerede sabitlenebileceği belirtir. Bu durumda, bunu NumaNode olarak ayarlarsınız, bu da işlemlerin NUMA etki alanı birimlerine sabitlendiği anlamına gelir.|
|`MV2_SHOW_CPU_BINDING` |İşlemlerin nerede sabitlendiği hakkında hata ayıklama bilgileri almak istediğinizi belirtir.|
|`MV2_SHOW_HCA_BINDING` |Her bir işlemin hangi konak Kanal bağdaştırıcısı tarafından kullanıldığı hakkında hata ayıklama bilgileri almak istediğinizi belirtir.|

## <a name="platform-mpi"></a>Platform MPı

Platform MPı Community Edition için gerekli paketleri yükleyin.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Yükleme işlemini izleyin.

Aşağıdaki komutlar MPı pingpong çalıştırmaya örnek olarak, CentOS-HPC 7,6, 7,8 ve 8,1 VM görüntülerini kullanarak HBv3 VM 'lerinde platform MPı 'yi kullanmayı azaltır.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Yukarıda açıklanan şekilde UCX 'i yükler. MPICH oluşturun.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH çalıştırma.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Yukarıdaki bölümde belirtilen bölüm anahtarınızı denetleyin.

## <a name="osu-mpi-benchmarks"></a>OSU MPı değerlendirmeleri

[OSU MPı değerlendirmeleri](http://mvapich.cse.ohio-state.edu/benchmarks/) ve UNIK 'yi indirin.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Belirli bir MPı kitaplığı kullanarak kıyaslamalar oluşturun:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPı değerlendirmeleri klasörü altında `mpi/` .


## <a name="discover-partition-keys"></a>Bölüm anahtarlarını bul

Aynı kiracı içindeki diğer VM 'lerle iletişim için bölüm anahtarlarını (p-Keys) bulun (kullanılabilirlik kümesi veya sanal makine ölçek kümesi).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

İkinin büyük olması MPı ile kullanılması gereken kiracı anahtarıdır. Örnek: aşağıda p anahtarları kullanılıyorsa, 0x800b MPı ile kullanılmalıdır.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Varsayılan bölümü (0x7FFF) bölüm anahtarını kullanın. UCX, p-anahtarının işaretsiz olmasını gerektirir. Örneğin, 0x800b için 0x000b olarak UCX_IB_PKEY ayarlayın.

Ayrıca, kiracı (kullanılabilirlik kümesi veya sanal makine ölçek kümesi) varolduğu sürece PKEYs 'in de aynı kaldığı unutulmamalıdır. Bu, düğümler eklendiğinde/silindiğinde bile geçerlidir. Yeni kiracılar farklı PKEYs 'ler alır.


## <a name="set-up-user-limits-for-mpi"></a>MPı için Kullanıcı sınırlarını ayarla

MPı için Kullanıcı sınırlarını ayarlayın.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>MPı için SSH anahtarlarını ayarlama

Gerekli MPı türleri için SSH anahtarlarını ayarlayın.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Yukarıdaki sözdizimi paylaşılan bir giriş dizinini varsayar, başka bir deyişle, her bir düğüme tek bir SSH dizini kopyalanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [InfiniBand etkin](../../sizes-hpc.md#rdma-capable-instances) [H serisi](../../sizes-hpc.md) ve [N serisi](../../sizes-gpu.md) VM 'ler hakkında bilgi edinin
- [HBv3-Series genel bakış](hbv3-series-overview.md) ve [HC Serisi genel bakış](hc-series-overview.md)konusunu gözden geçirin.
- [Azure Işlem Tech Community bloglarında](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)en son Duyurular, HPC iş yükü örnekleri ve performans sonuçları hakkında bilgi edinin.
- Çalıştırılan HPC iş yüklerinin daha yüksek düzey mimari görünümü için bkz. [Azure 'Da yüksek performanslı bilgi işlem (HPC)](/azure/architecture/topics/high-performance-computing/).
