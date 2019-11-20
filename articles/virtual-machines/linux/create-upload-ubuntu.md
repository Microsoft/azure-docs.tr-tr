---
title: Azure 'da bir Ubuntu Linux VHD oluşturma ve karşıya yükleme
description: Ubuntu Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: szark
ms.openlocfilehash: cdf2c6c0d5621223655fc4571affcdde4563ac97
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71258277"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Azure’da Ubuntu sanal makinesi hazırlama
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Resmi Ubuntu bulut görüntüleri
Ubuntu artık, adresinden [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)Indirmek üzere resmi Azure VHD 'leri yayımlar. Azure için kendi özelleştirilmiş Ubuntu görüntünüzü oluşturmanız gerekiyorsa, bu bilinen çalışma VHD 'leri ile başlamanız ve gerektiğinde özelleştirmeniz önerilir. En son görüntü yayınları her zaman aşağıdaki konumlarda bulunabilir:

* Ubuntu 12.04/kesinlikli: [Ubuntu-12,04-Server-cloudımg-AMD64-Disk1. vhd. zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [Ubuntu-14,04-Server-cloudimg-AMD64-Disk1. vhd. zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [Ubuntu-16,04-Server-cloudimg-AMD64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-AMD64. vhd. zip](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/COSMIC: [Cosmic-Server-cloudimg-AMD64. vhd. zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Önkoşullar
Bu makalede bir Ubuntu Linux işletim sistemini zaten bir sanal sabit diske yüklediğinizi varsaymış olursunuz. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](https://technet.microsoft.com/library/hh846766.aspx).

**Ubuntu yükleme notları**

* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya Convert-VHD cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , veri disklerinde kullanılabilir.
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir.  Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>El ile adımlar
> [!NOTE]
> Azure için kendi özel Ubuntu görüntünüzü oluşturmayı denemeden önce lütfen [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) bunun yerine önceden oluşturulmuş ve test edilmiş görüntüleri kullanmayı göz önünde bulundurun.
> 
> 

1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.

2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.

3. Görüntüdeki geçerli depoları, Ubuntu 'un Azure depolarını kullanacak şekilde değiştirin. Adımlar, Ubuntu sürümüne bağlı olarak biraz farklılık gösterir.
   
    Düzenlemeden `/etc/apt/sources.list`önce, bir yedekleme yapmanız önerilir:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16,04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Ubuntu Azure görüntüleri artık *donanım etkinleştirme* (Hwe) çekirdeğini takip ediyoruz. Aşağıdaki komutları çalıştırarak işletim sistemini en son çekirdeğe güncelleştirin:

    Ubuntu 12,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16,04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Ayrıca bkz:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Grub için çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu bir metin düzenleyicisinde `/etc/default/grub` açmak için, adlı `GRUB_CMDLINE_LINUX_DEFAULT` değişkeni bulun (veya gerekirse ekleyin) ve aşağıdaki parametreleri içerecek şekilde düzenleyin:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Bu dosyayı kaydedip kapatın ve sonra çalıştırın `sudo update-grub`. Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarıyla birlikte Azure teknik desteğine yardımcı olabilir.

6. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.

7. Azure Linux aracısını yükler:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Paket, yüklüyse `NetworkManager` ve`NetworkManager-gnome`paketlerinikaldırabilir. `walinuxagent`


1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="references"></a>Başvurular
[Ubuntu donanım etkinleştirme (HWE) çekirdeği](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için Ubuntu Linux sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).

