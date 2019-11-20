---
title: Linux için Azure N serisi GPU sürücü kurulumu
description: Azure 'da Linux çalıştıran N serisi VM 'Ler için NVıDıA GPU sürücülerini ayarlama
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ebc991d54ef902eb653cf2d99b2f74f18551568
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035620"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Linux çalıştıran N serisi VM 'Lere NVıDıA GPU sürücülerini yükler

Linux çalıştıran Azure N serisi VM 'lerinin GPU Özellikleri avantajlarından yararlanmak için NVıDıA GPU sürücüleri yüklenmelidir. [NVıDıA GPU sürücü uzantısı](../extensions/hpccompute-gpu-linux.md) , bir N serisi VM 'ye uygun NVIDIA CUDA veya kılavuz sürücülerini yükleme. Azure CLı veya Azure Resource Manager şablonları gibi Azure portal veya araçları kullanarak uzantıyı yükler veya yönetir. Desteklenen dağıtımlar ve dağıtım adımları için [NVıDıA GPU sürücü uzantısı belgelerine](../extensions/hpccompute-gpu-linux.md) bakın.

GPU sürücülerini el ile yüklemeyi seçerseniz, bu makale desteklenen dağıtımlar, sürücüler ve yükleme ve doğrulama adımları sağlar. [Windows VM 'leri](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)için el ile sürücü kurulum bilgileri de mevcuttur.

N serisi VM özellikleri, depolama kapasiteleri ve disk ayrıntıları için bkz. [GPU LINUX VM boyutları](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>N serisi VM 'Lere CUDA sürücülerini yükler

Burada, N serisi VM 'lerde NVıDıA CUDA araç setinden CUDA sürücülerini yüklemek için gereken adımlar verilmiştir. 


C ve C++ GELIŞTIRICILER, GPU hızlandırmalı uygulamalar oluşturmak için isteğe bağlı olarak tam araç setini yükleyebilir. Daha fazla bilgi için bkz. [CUDA Yükleme Kılavuzu](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

CUDA sürücülerini yüklemek için her VM 'ye bir SSH bağlantısı oluşturun. Sistemde CUDA özellikli bir GPU olduğunu doğrulamak için şu komutu çalıştırın:

```bash
lspci | grep -i NVIDIA
```
Aşağıdaki örneğe benzer bir çıktı görürsünüz (NVıDıA Tesla K80 kartını gösterme):

![lspcı komut çıktısı](./media/n-series-driver-setup/lspci.png)

Ardından, dağıtıma özgü yükleme komutlarını çalıştırın.

### <a name="ubuntu"></a>Ubuntu 

1. NVıDıA Web sitesinden CUDA Sürücülerini İndirin ve yükleyin. Örneğin, Ubuntu 16,04 LTS için:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

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

1. Çekirdeği güncelleştirin (önerilir). Çekirdeği güncelleştirmeme seçeneğini belirlerseniz, `kernel-devel` ve `dkms` sürümlerinin çekirdeğe uygun olduğundan emin olun.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

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

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   Yükleme birkaç dakika sürebilir. 

4. Tam CUDA araç setini isteğe bağlı olarak yüklemek için şunu yazın:

   ```bash
   sudo yum install cuda
   ```

5. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.

### <a name="verify-driver-installation"></a>Sürücü yüklemeyi doğrulama

GPU cihaz durumunu sorgulamak için VM 'ye SSH yapın ve sürücüyle birlikte yüklenen [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) komut satırı yardımcı programını çalıştırın. 

Sürücü yüklüyse aşağıdakine benzer bir çıktı görürsünüz. Şu anda VM 'de bir GPU iş yükü çalıştırmadığınız takdirde **GPU-Util** 'in %0 gösterdiğini unutmayın. Sürücü sürümünüz ve GPU ayrıntılarınız gösterilenlerden farklı olabilir.

![NVıDıA cihaz durumu](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA ağ bağlantısı

RDMA ağ bağlantısı, aynı Kullanılabilirlik kümesinde veya bir VM Ölçek kümesindeki tek bir yerleştirme grubunda dağıtılan NC24r gibi, RDMA özellikli N serisi VM 'lerde etkinleştirilebilir. RDMA ağı, Intel MPı 5. x veya sonraki bir sürümde çalışan uygulamalar için Ileti geçirme arabirimi (MPı) trafiğini destekler. Ek gereksinimler şunları izler:

### <a name="distributions"></a>Dağıtımları

N serisi VM 'lerde RDMA bağlantısını destekleyen Azure Marketi 'ndeki görüntülerden birinde RDMA özellikli N serisi VM 'Leri dağıtın:
  
* **Ubuntu 16,04 LTS** -sanal makinede RDMA sürücülerini yapılandırma ve Intel MPI Indirmek için Intel ile kaydolma:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS tabanlı 7,4 HPC** -RDMA sürücüleri ve Intel mpı 5,1, VM 'ye yüklenir.

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
3. NVıDıA sürücüsüyle uyumsuz olan Nouveau Çekirdek sürücüsünü devre dışı bırakın. (Yalnızca NV veya NVv2 VM 'lerinde NVıDıA sürücüsünü kullanın.) Bunu yapmak için, `/etc/modprobe.d` adlı `nouveau.conf` aşağıdaki içeriklerle bir dosya oluşturun:

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

8. Aşağıdakileri `/etc/nvidia/gridd.conf`ekleyin:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Varsa `/etc/nvidia/gridd.conf` aşağıdakileri kaldırın:
 
   ```
   FeatureType=0
   ```
10. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS veya Red Hat Enterprise Linux 

1. Kernel ve DKMS 'yi güncelleştirin (önerilir). Çekirdeği güncelleştirmeme seçeneğini belirlerseniz, `kernel-devel` ve `dkms` sürümlerinin çekirdeğe uygun olduğundan emin olun.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. NVıDıA sürücüsüyle uyumsuz olan Nouveau Çekirdek sürücüsünü devre dışı bırakın. (Yalnızca NV veya NV2 VM 'lerinde NVıDıA sürücüsünü kullanın.) Bunu yapmak için, `/etc/modprobe.d` adlı `nouveau.conf` aşağıdaki içeriklerle bir dosya oluşturun:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. [Hyper-V ve Azure IÇIN](https://www.microsoft.com/download/details.aspx?id=55106)VM 'yi yeniden başlatın, yeniden bağlanın ve en son Linux Tümleştirme hizmetlerini kurun.
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. SANAL makineye yeniden bağlanın ve `lspci` komutunu çalıştırın. NVıDıA M60 kartının veya kartlarının PCI cihazlar olarak görünür olduğunu doğrulayın.
 
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
  
8. Aşağıdakileri `/etc/nvidia/gridd.conf`ekleyin:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Varsa `/etc/nvidia/gridd.conf` aşağıdakileri kaldırın:
 
   ```
   FeatureType=0
   ```
10. VM 'yi yeniden başlatın ve yüklemeyi doğrulamaya devam edin.


### <a name="verify-driver-installation"></a>Sürücü yüklemeyi doğrulama


GPU cihaz durumunu sorgulamak için VM 'ye SSH yapın ve sürücüyle birlikte yüklenen [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) komut satırı yardımcı programını çalıştırın. 

Sürücü yüklüyse aşağıdakine benzer bir çıktı görürsünüz. Şu anda VM 'de bir GPU iş yükü çalıştırmadığınız takdirde **GPU-Util** 'in %0 gösterdiğini unutmayın. Sürücü sürümünüz ve GPU ayrıntılarınız gösterilenlerden farklı olabilir.

![NVıDıA cihaz durumu](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 sunucusu
NV veya NVv2 VM 'sine yönelik uzak bağlantılar için X11 sunucusuna ihtiyacınız varsa, grafiklerin donanım hızlandırılmasını sağladığından [x11vnc](http://www.karlrunge.com/x11vnc/) önerilir. M60 cihazının Busıd 'Si, X11 yapılandırma dosyasına (genellikle `etc/X11/xorg.conf`) el ile eklenmelidir. Aşağıdakine benzer bir `"Device"` bölümü ekleyin:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Ayrıca, bu cihazı kullanmak için `"Screen"` bölümünü güncelleştirin.
 
Ondalık Busıd, şu şekilde çalıştırılarak bulunabilir

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
Bir VM yeniden ayrıldığında veya yeniden başlatıldığında Busıd değişebilir. Bu nedenle, bir VM yeniden başlatıldığında X11 yapılandırmasındaki Busıd 'yi güncelleştirmek için bir komut dosyası oluşturmak isteyebilirsiniz. Örneğin, aşağıdakine benzer içeriğe sahip `busidupdate.sh` (veya seçtiğiniz başka bir ad) adlı bir komut dosyası oluşturun:

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

Daha sonra, betik önyüklemede kök olarak çağrılması için `/etc/rc.d/rc3.d` ' de güncelleştirme komut dosyası için bir giriş oluşturun.

## <a name="troubleshooting"></a>Sorun giderme

* `nvidia-smi` kullanarak Kalıcılık modu ayarlayabilirsiniz. böylece, kartları sorgulamak için komutun çıktısının daha hızlı olması gerekir. Kalıcılık modunu ayarlamak için `nvidia-smi -pm 1`yürütün. VM yeniden başlatılırsa mod ayarının dışarıda olacağını unutmayın. Başlatma sırasında her zaman Mod ayarını çalıştırmak için komut dosyası oluşturabilirsiniz.
* NVıDıA CUDA sürücülerini en son sürüme güncelleştirdiyseniz ve RDMA connectivcity bulma işlemi artık çalışmıyorsa, bu bağlantıyı yeniden kurmak için [RDMA sürücülerini yeniden yükleyin](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) . 

## <a name="next-steps"></a>Sonraki adımlar

* Yüklü NVıDıA sürücülerinizin bulunduğu bir Linux VM görüntüsünü yakalamak için bkz. [Linux sanal makinesi Genelleştirme ve yakalama](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
