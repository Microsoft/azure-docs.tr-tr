---
title: Azure 'da bir debir Linux VHD hazırlama
description: Azure 'da dağıtım için deni VHD görüntüleri oluşturmayı öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 7f371e2681ecc2b3bb4bcb3e1a8b642061dc2449
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036468"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure için Debian VHD hazırlama
## <a name="prerequisites"></a>Önkoşullar
Bu bölümde, [de, Web sitesinden](https://www.debian.org/distrib/) bir sanal sabit diske indirilen bir. iso dosyasından bir de, Linux işletim sistemini zaten yüklemiş olduğunuz varsayılmaktadır. . Vhd dosyaları oluşturmak için birden çok araç var; Hyper-V yalnızca bir örnektir. Hyper-V ' y i kullanma hakkında yönergeler için bkz. [Hyper-v rolünü yükleyip sanal makineyi yapılandırma](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Yükleme notları
* Ayrıca bkz. Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* Yeni VHDX biçimi Azure 'da desteklenmez. Hyper-V Yöneticisi 'Ni veya **Convert-VHD** cmdlet 'ini kullanarak diski vhd biçimine dönüştürebilirsiniz.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , veri disklerinde kullanılabilir.
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Azure Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir. Aşağıdaki adımlarda daha fazla bilgi bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage kullanarak detem VHD 'leri oluşturun
Azure için Azure-Manage betikleri, [Credadtiv](https://www.credativ.com/)içindeki [Azure-Manage](https://github.com/credativ/azure-manage) betikleri gibi araçlar oluşturmaya yönelik araçlar mevcuttur. Bu, sıfırdan bir görüntü oluşturulmasına karşı önerilen yaklaşımdır. Örneğin, bir debir 8 VHD oluşturmak için, `azure-manage` yardımcı programını (ve bağımlılıklarını) indirmek ve `azure_build_image` betiğini çalıştırmak için aşağıdaki komutları çalıştırın:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Bir deni VHD 'YI el ile hazırlama
1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.
2. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.
3. İşletim sistemini bir ISO kullanarak yüklediyseniz `/etc/apt/source.list`"`deb cdrom`" ile ilgili herhangi bir satırı açıklama olarak ekleyin.

4. `/etc/default/grub` dosyasını düzenleyin ve Azure için ek çekirdek parametreleri eklemek üzere **GRUB_CMDLINE_LINUX** parametresini aşağıdaki şekilde değiştirin.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Grub 'yi yeniden oluşturun ve çalıştırın:

        # sudo update-grub

6. De, 8 veya 9 ' un Azure depolarını/etc/apt/sources.exe listesine ekleyin:

    **Desek8. x "Jese"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **9. x "uzat"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Azure Linux aracısını yükler:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Deler 9 + için, Azure 'da VM 'lerle kullanılmak üzere yeni de, bulut çekirdeğini kullanmanız önerilir. Bu yeni çekirdeği yüklemek için ilk olarak/etc/apt/Preferences.exe \ \ Linux.exe ' adlı bir dosyayı aşağıdaki içeriklerle oluşturun:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Ardından, yeni detem bulut çekirdeğini yüklemek için "sudo apt-get Install Linux-Image-Cloud-amd64" öğesini çalıştırın.

9. Sanal makinenin sağlamasını kaldırır ve Azure 'da sağlama için hazırlayın ve şunu çalıştırın:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Hyper-V Yöneticisi 'nde **eylem** -> Kapat ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için de, sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).

