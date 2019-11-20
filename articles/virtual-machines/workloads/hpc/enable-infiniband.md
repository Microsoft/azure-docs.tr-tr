---
title: SR-ıOV-Azure sanal makineleri ile ınifınband 'i etkinleştirme | Microsoft Docs
description: SR-ıOV ile InfiniBand 'i etkinleştirmeyi öğrenin.
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
ms.openlocfilehash: 7218fceae71969f204c6c25ba4793a7c94341693
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858475"
---
# <a name="enable-infiniband-with-sr-iov"></a>SR-ıOV ile InfiniBand 'yi etkinleştirme

HPC için IaaS VM 'Leri kullanmaya başlamanın en basit ve önerilen yolu, CentOS-HPC 7,6 VM OS görüntüsünü kullanmaktır. Özel VM görüntünüzü kullanıyorsanız, bunu InfiniBand (ıB) ile yapılandırmanın en kolay yolu, ınfinıbanddriverlinux veya ınfinibanddriverwindows VM uzantısını dağıtımınıza eklemektir.
[Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#rdma-capable-instances) ve [WINDOWS](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc#rdma-capable-instances) ile bu VM uzantılarını kullanmayı öğrenin

SR-ıOV etkinleştirilmiş VM 'lerde (Şu anda HB ve HC Serisi) InfiniBand 'yi el ile yapılandırmak için aşağıdaki adımları izleyin. Bu adımlar yalnızca RHEL/CentOS içindir. Ubuntu (16,04 ve 18,04) ve SLES (12 SP4 ve 15) için gelen kutusu sürücüleri iyi çalışır.

## <a name="manually-install-ofed"></a>El ile yüklemesi

[Mellanox](https://www.mellanox.com/page/products_dyn?product_family=26)-5 ' ten en son MLNX_OFED sürücülerini yükler.

RHEL/CentOS için (7,6 için aşağıdaki örnek):

```bash
sudo yum install -y kernel-devel python-devel
sudo yum install -y redhat-rpm-config rpm-build gcc-gfortran gcc-c++
sudo yum install -y gtk2 atk cairo tcl tk createrepo
wget --retry-connrefused --tries=3 --waitretry=5 http://content.mellanox.com/ofed/MLNX_OFED-4.5-1.0.1.0/MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
tar zxvf MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64.tgz
sudo ./MLNX_OFED_LINUX-4.5-1.0.1.0-rhel7.6-x86_64/mlnxofedinstall --add-kernel-support
```

Windows için, [Mellanox](https://www.mellanox.com/page/products_dyn?product_family=32&menu_section=34) 'den ConnectX-5 Için winof-2 sürücülerini indirip yükleyin

## <a name="enable-ipoib"></a>Ipoıb 'i etkinleştir

```bash
sudo sed -i 's/LOAD_EIPOIB=no/LOAD_EIPOIB=yes/g' /etc/infiniband/openib.conf
sudo /etc/init.d/openibd restart
if [ $? -eq 1 ]
then
  sudo modprobe -rv  ib_isert rpcrdma ib_srpt
  sudo /etc/init.d/openibd restart
fi
```

## <a name="assign-an-ip-address"></a>Bir IP adresi atayın

İb0 arabirimine aşağıdakilerden birini kullanarak bir IP adresi atayın:

- IP adresini İb0 arabirimine el ile atayın (kök olarak).

    ```bash
    ifconfig ib0 $(sed '/rdmaIPv4Address=/!d;s/.*rdmaIPv4Address="\([0-9.]*\)".*/\1/' /var/lib/waagent/SharedConfig.xml)/16
    ```

OR

- IP adresi atamak ve kalıcı hale getirmek için Walınuxagent 'ı kullanın.

    ```bash
    yum install -y epel-release
    yum install -y python-pip
    python -m pip install --upgrade pip setuptools wheel
    wget "https://github.com/Azure/WALinuxAgent/archive/release-2.2.36.zip"
    unzip release-2.2.36.zip
    cd WALinuxAgent*
    python setup.py install --register-service --force
    sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
    sed -i -e 's/# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
    systemctl restart waagent
    ```

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) hakkında daha fazla bilgi edinin.
