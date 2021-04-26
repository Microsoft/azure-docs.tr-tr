---
title: Linux için Azure N serisi GPU sürücü kurulumu
description: Azure 'da Linux çalıştıran N serisi VM 'Ler için NVıDıA GPU sürücülerini ayarlama
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: dd9461e30138ee1a59a93db45aa5f739bfe88f94
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565313"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Linux çalıştıran N serisi VM 'Lere NVıDıA GPU sürücülerini yükler

NVıDıA GPU 'Lar tarafından desteklenen Azure N serisi VM 'lerinin GPU Özellikleri avantajlarından yararlanmak için NVıDıA GPU sürücülerini yüklemelisiniz. [NVıDıA GPU sürücü uzantısı](../extensions/hpccompute-gpu-linux.md) , bir N serisi VM 'ye uygun NVIDIA CUDA veya kılavuz sürücülerini yükleme. Azure CLı veya Azure Resource Manager şablonları gibi Azure portal veya araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen dağıtımlar ve dağıtım adımları için [NVıDıA GPU sürücü uzantısı belgelerine](../extensions/hpccompute-gpu-linux.md) bakın.

NVıDıA GPU sürücülerini el ile yüklemeyi seçerseniz, bu makale desteklenen dağıtımlar, sürücüler ve yükleme ve doğrulama adımları sağlar. [Windows VM 'leri](../windows/n-series-driver-setup.md)için el ile sürücü kurulum bilgileri de mevcuttur.

N serisi VM özellikleri, depolama kapasiteleri ve disk ayrıntıları için bkz. [GPU LINUX VM boyutları](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>N serisi VM 'Lere CUDA sürücülerini yükler

Burada, N serisi VM 'lerde NVıDıA CUDA araç setinden CUDA sürücülerini yüklemek için gereken adımlar verilmiştir. 

C ve C++ geliştiricileri, GPU hızlandırmalı uygulamalar oluşturmak için isteğe bağlı olarak tam araç setini yükleyebilir. Daha fazla bilgi için bkz. [CUDA Yükleme Kılavuzu](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

CUDA sürücülerini yüklemek için her VM 'ye bir SSH bağlantısı oluşturun. Sistemde CUDA özellikli bir GPU olduğunu doğrulamak için şu komutu çalıştırın:

```bash
lspci | grep -i NVIDIA
```
Aşağıdaki örneğe benzer bir çıktı görürsünüz (NVıDıA Tesla K80 kartını gösterme):

![lspcı komut çıktısı](./media/n-series-driver-setup/lspci.png)

lspcı, varsa InfiniBand NIC ve GPU 'Lar dahil olmak üzere VM 'deki PCIe cihazlarını listeler. Lspcı başarıyla dönmezse, LIS 'yi CentOS/RHEL üzerine yüklemeniz gerekebilir (aşağıdaki yönergeler).
Ardından, dağıtıma özgü yükleme komutlarını çalıştırın.

### <a name="ubuntu"></a>Ubuntu 

1. NVıDıA Web sitesinden CUDA Sürücülerini İndirin ve yükleyin. 
    > [!NOTE]
   >  Aşağıdaki örnekte, Ubuntu 16,04 için CUDA paket yolu gösterilmektedir. Kullanmayı planladığınız sürüme özgü yolu değiştirin. 
   >  
   >  [NVIDIA Indirme Merkezi] sayfasını ziyaret edin ( https://developer.download.nvidia.com/compute/cuda/repos/) her sürüme özgü tam yol için). 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   Yükleme birkaç dakika sürebilir.
 

2. Tam CUDA araç setini isteğe bağlı olarak yüklemek için şunu yazın:

   ```bash
   sudo apt-get install cuda
   ```

3. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.

#### <a name="cuda-driver-updates"></a>CUDA sürücü güncelleştirmeleri

Dağıtımdan sonra CUDA sürücülerini düzenli olarak güncelleştirmenizi öneririz.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS veya Red Hat Enterprise Linux

1. Çekirdeği güncelleştirin (önerilir). Çekirdeği güncelleştirmeme seçeneğini belirlerseniz, ve sürümlerinin çekirdeğe uygun olduğundan emin olun `kernel-devel` `dkms` .

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. [Hyper-V ve Azure için en son Linux Tümleştirme Hizmetleri](https://www.microsoft.com/download/details.aspx?id=55106)'ni yükler. Lspcı sonuçlarının doğrulanması için LIS 'nin gerekli olup olmadığını denetleyin. Tüm GPU cihazları beklenen (ve yukarıda belgelenmiş) olarak listeleniyorsa, LIS 'nin yüklenmesi gerekli değildir.

   LIS 'nin Red Hat Enterprise Linux, CentOS ve Oracle Linux Red Hat uyumlu çekirdek 5.2-5.11, 6.0-6.10 ve 7.0-7.7 için geçerli olduğunu lütfen unutmayın. Daha fazla ayrıntı için lütfen [Linux Tümleştirme Hizmetleri belgeleri] bölümüne bakın https://www.microsoft.com/en-us/download/details.aspx?id=55106) . 
   Bu sürümler için LIS 'nin artık gerekli olmadığı için CentOS/RHEL 7,8 (veya üzeri sürümler) kullanmayı planlıyorsanız bu adımı atlayın.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. SANAL makineye yeniden bağlanın ve aşağıdaki komutlarla yüklemeye devam edin:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Yükleme birkaç dakika sürebilir. 
   
    > [!NOTE]
   >  Kullanmak istediğiniz CentOS veya RHEL sürümünün doğru paketini seçmek için [Fedora](https://dl.fedoraproject.org/pub/epel/) ve [NVIDIA CUDA depo](https://developer.download.nvidia.com/compute/cuda/repos/) adresini ziyaret edin.
   >  

Örneğin, CentOS 8 ve RHEL 8 ' in aşağıdaki adımları yapması gerekir.

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel8-10.2.89-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

4. Tam CUDA araç setini isteğe bağlı olarak yüklemek için şunu yazın:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Vulkan-FileSystem gibi eksik paketlerle ilgili bir hata iletisi görürseniz,/etc/yum.Repos.d/rh-Cloud ' yi düzenlemeniz, isteğe bağlı-RPMS ' i aramanız ve etkin olarak 1 ' e ayarlamanız gerekebilir.
   >  

5. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.

### <a name="verify-driver-installation"></a>Sürücü yüklemeyi doğrulama

GPU cihaz durumunu sorgulamak için VM 'ye SSH yapın ve sürücüyle birlikte yüklenen [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) komut satırı yardımcı programını çalıştırın. 

Sürücü yüklüyse aşağıdakine benzer bir çıktı görürsünüz. Şu anda VM 'de bir GPU iş yükü çalıştırmadığınız takdirde **GPU-Util** 'in %0 gösterdiğini unutmayın. Sürücü sürümünüz ve GPU ayrıntılarınız gösterilenlerden farklı olabilir.

![NVıDıA cihaz durumu](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA ağ bağlantısı

RDMA ağ bağlantısı, aynı Kullanılabilirlik kümesinde veya bir sanal makine (VM) ölçek kümesindeki tek bir yerleştirme grubunda dağıtılan NC24r gibi, RDMA özellikli N serisi VM 'lerde etkinleştirilebilir. RDMA ağı, Intel MPı 5. x veya sonraki bir sürümde çalışan uygulamalar için Ileti geçirme arabirimi (MPı) trafiğini destekler. Ek gereksinimler şunları izler:

### <a name="distributions"></a>Dağıtımları

N serisi VM 'lerde RDMA bağlantısını destekleyen Azure Marketi 'ndeki görüntülerden birinde RDMA özellikli N serisi VM 'Leri dağıtın:
  
* **Ubuntu 16,04 LTS** -sanal makinede RDMA sürücülerini yapılandırma ve Intel MPI Indirmek için Intel ile kaydolma:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS tabanlı 7,4 HPC** -RDMA sürücüleri ve Intel mpı 5,1, VM 'ye yüklenir.

* **CentOS tabanlı HPC** -CENTOS-HPC 7,6 ve üzeri (ıNFINIBAND 'nin SR-IOV üzerinden desteklendiği SKU 'lar için). Bu görüntülerde, Mellanox OFED ve MPı kitaplıkları önceden yüklenmiş olmalıdır.

> [!NOTE]
> CX3-Pro kartlar yalnızca, Mellanox OFED 'in LTS sürümleri aracılığıyla desteklenir. ConnectX3-Pro kartlarla birlikte N serisi VM 'lerde LTS Mellanox OFED sürümü (4.9-0.1.7.0) kullanın. Daha fazla bilgi için bkz. [Linux sürücüleri](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Ayrıca, en son Azure Marketi HPC görüntülerinin, ConnectX3-Pro kartlarını desteklemeyen, Mellanox OFED 5,1 ve üzeri bir sürümü vardır. ConnectX3-Pro kartlarıyla VM 'lerde kullanmadan önce HPC görüntüsündeki Mellanox OFED sürümünü denetleyin.
>
> Aşağıdaki görüntüler ConnectX3-Pro kartlarını destekleyen en son CentOS-HPC görüntüleridir:
>
> - OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
> - OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
> - OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
> - OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>NV veya NVv3 serisi VM 'Lere KıLAVUZ sürücüleri yükler

NV veya NVv3 serisi VM 'Lere NVıDıA GRID sürücüleri yüklemek için, her VM 'ye bir SSH bağlantısı oluşturun ve Linux dağıtımına yönelik adımları izleyin. 

### <a name="ubuntu"></a>Ubuntu 

1. `lspci` komutunu çalıştırın. NVıDıA M60 kartının veya kartlarının PCI cihazlar olarak görünür olduğunu doğrulayın.

2. Güncelleştirmeleri yükler.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. NVıDıA sürücüsüyle uyumsuz olan Nouveau Çekirdek sürücüsünü devre dışı bırakın. (Yalnızca NV veya NVv2 VM 'lerinde NVıDıA sürücüsünü kullanın.) Bunu yapmak için, `/etc/modprobe.d` aşağıdaki içeriklerle adlandırılmış bir dosya oluşturun `nouveau.conf` :

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. VM 'yi yeniden başlatın ve yeniden bağlanın. Çıkış X Server:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. KıLAVUZ sürücüsünü indirip yükleyin:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. X yapılandırma dosyanızı güncelleştirmek için NVIDIA-xconfig yardımcı programını çalıştırmak isteyip istemediğiniz sorulduğunda **Evet**' i seçin.

7. Yükleme tamamlandıktan sonra,/etc/Nvidia/gridd.conf.Template konumundaki yeni bir gridd. conf dosyasına kopyalayın/etc/Nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Aşağıdakileri öğesine ekleyin `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Varsa şunları kaldırın `/etc/nvidia/gridd.conf` :
 
   ```
   FeatureType=0
   ```
10. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS veya Red Hat Enterprise Linux 

1. Kernel ve DKMS 'yi güncelleştirin (önerilir). Çekirdeği güncelleştirmeme seçeneğini belirlerseniz, ve sürümlerinin çekirdeğe uygun olduğundan emin olun `kernel-devel` `dkms` .
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. NVıDıA sürücüsüyle uyumsuz olan Nouveau Çekirdek sürücüsünü devre dışı bırakın. (Yalnızca NV veya NV3 VM 'lerinde NVıDıA sürücüsünü kullanın.) Bunu yapmak için, `/etc/modprobe.d` aşağıdaki içeriklerle adlandırılmış bir dosya oluşturun `nouveau.conf` :

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. [Hyper-V ve Azure IÇIN](https://www.microsoft.com/download/details.aspx?id=55106)VM 'yi yeniden başlatın, yeniden bağlanın ve en son Linux Tümleştirme hizmetlerini kurun. Lspcı sonuçlarının doğrulanması için LIS 'nin gerekli olup olmadığını denetleyin. Tüm GPU cihazları beklenen (ve yukarıda belgelenmiş) olarak listeleniyorsa, LIS 'nin yüklenmesi gerekli değildir. 

   Bu sürümler için LIS 'nin artık gerekli olmadığı için CentOS/RHEL 7,8 (veya üzeri sürümler) kullanmayı planlıyorsanız bu adımı atlayın.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. SANAL makineye yeniden bağlanın ve komutu çalıştırın `lspci` . NVıDıA M60 kartının veya kartlarının PCI cihazlar olarak görünür olduğunu doğrulayın.
 
5. KıLAVUZ sürücüsünü indirip yükleyin:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. X yapılandırma dosyanızı güncelleştirmek için NVIDIA-xconfig yardımcı programını çalıştırmak isteyip istemediğiniz sorulduğunda **Evet**' i seçin.

7. Yükleme tamamlandıktan sonra,/etc/Nvidia/gridd.conf.Template konumundaki yeni bir gridd. conf dosyasına kopyalayın/etc/Nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Aşağıdakileri öğesine ekleyin `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Varsa şunları kaldırın `/etc/nvidia/gridd.conf` :
 
   ```
   FeatureType=0
   ```
10. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.


### <a name="verify-driver-installation"></a>Sürücü yüklemeyi doğrulama


GPU cihaz durumunu sorgulamak için VM 'ye SSH yapın ve sürücüyle birlikte yüklenen [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) komut satırı yardımcı programını çalıştırın. 

Sürücü yüklüyse aşağıdakine benzer bir çıktı görürsünüz. Şu anda VM 'de bir GPU iş yükü çalıştırmadığınız takdirde **GPU-Util** 'in %0 gösterdiğini unutmayın. Sürücü sürümünüz ve GPU ayrıntılarınız gösterilenlerden farklı olabilir.

![GPU cihaz durumu sorgulandığında çıktıyı gösteren ekran görüntüsü.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 sunucusu
NV veya NVv2 VM 'sine yönelik uzak bağlantılar için X11 sunucusuna ihtiyacınız varsa, grafiklerin donanım hızlandırılmasını sağladığından [x11vnc](http://www.karlrunge.com/x11vnc/) önerilir. M60 cihazının Busıd 'Si, X11 yapılandırma dosyasına (genellikle,) el ile eklenmelidir `etc/X11/xorg.conf` . `"Device"`Aşağıdakine benzer bir bölüm ekleyin:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ayrıca, `"Screen"` Bu cihazı kullanmak için bölümünü güncelleştirin.
 
Ondalık Busıd, şu şekilde çalıştırılarak bulunabilir

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
Bir VM yeniden ayrıldığında veya yeniden başlatıldığında Busıd değişebilir. Bu nedenle, bir VM yeniden başlatıldığında X11 yapılandırmasındaki Busıd 'yi güncelleştirmek için bir komut dosyası oluşturmak isteyebilirsiniz. Örneğin, `busidupdate.sh` aşağıdakine benzer içeriğe sahip (veya seçtiğiniz başka bir ad) adlı bir komut dosyası oluşturun:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Ardından, içinde güncelleştirme betiğinizin bir girdisini oluşturun, `/etc/rc.d/rc3.d` böylece betik önyüklemede kök olarak çağrılır.

## <a name="troubleshooting"></a>Sorun giderme

* ' I kullanarak Kalıcılık modunu ayarlayabilirsiniz `nvidia-smi` . böylece, kartları sorgulamak için komutun çıktısının daha hızlı olması gerekir. Kalıcılık modunu ayarlamak için yürütün `nvidia-smi -pm 1` . VM yeniden başlatılırsa mod ayarının dışarıda olacağını unutmayın. Başlatma sırasında her zaman Mod ayarını çalıştırmak için komut dosyası oluşturabilirsiniz.
* NVıDıA CUDA sürücülerini en son sürüme güncelleştirdiyseniz ve RDMA bağlantısı bulunursa, bu bağlantıyı yeniden kurmak için [RDMA sürücülerini yeniden yükleyin](#rdma-network-connectivity) . 
* LIS yüklemesi sırasında, belirli bir CentOS/RHEL işletim sistemi sürümü (veya çekirdeği) LIS için desteklenmiyorsa, "desteklenmeyen çekirdek sürümü" hatası oluşturulur. Lütfen bu hatayı işletim sistemi ve çekirdek sürümleriyle birlikte bildirin.

## <a name="next-steps"></a>Sonraki adımlar

* Yüklü NVıDıA sürücülerinizin bulunduğu bir Linux VM görüntüsünü yakalamak için bkz. [Linux sanal makinesi Genelleştirme ve yakalama](capture-image.md).
