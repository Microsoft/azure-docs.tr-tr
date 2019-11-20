---
title: Oracle Linux VHD oluşturma ve karşıya yükleme
description: Oracle Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 16f3bc9e70f8fac6ab28318e1654742a2c3b76a1
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035364"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Azure için Oracle Linux sanal makinesi hazırlama
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu makalede bir Oracle Linux işletim sistemini zaten bir sanal sabit diske yüklediğinizi varsaymış olursunuz. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Oracle Linux yükleme notları
* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* Oracle 'ın Red Hat uyumlu çekirdeği ve bunların UEK3 (yerleşik olmayan kurumsal çekirdek), Hyper-V ve Azure 'da desteklenir. En iyi sonuçlar için lütfen Oracle Linux VHD 'nizi hazırlarken en son çekirdeğe güncelleştirdiğinizden emin olun.
* Oracle 'ın UEK2, gerekli sürücüleri içermediğinden Hyper-V ve Azure üzerinde desteklenmez.
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya Convert-VHD cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , veri disklerinde kullanılabilir.
* 2\.6.37 altındaki Linux çekirdek sürümlerindeki bir hata nedeniyle, NUMA daha büyük VM boyutları için desteklenmez. Bu sorun, birincil olarak yukarı akış Red Hat 2.6.32 çekirdeğini kullanarak dağıtımları etkiler. Azure Linux aracısının (waagent) el ile yüklenmesi, Linux çekirdeğinin GRUB yapılandırmasında NUMA 'yı otomatik olarak devre dışı bırakır. Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir.  Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* `Addons` deposunun etkinleştirildiğinden emin olun. `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) veya `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux 7) dosyasını düzenleyin ve satır `enabled=0` bu dosyada **[`enabled=1`]** veya **[ol6_addons]** altında ol7_addons olacak şekilde değiştirin.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Sanal makinenin Azure 'da çalışması için, işletim sistemindeki belirli yapılandırma adımlarını tamamlamalısınız.

1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.
2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.
3. Aşağıdaki komutu çalıştırarak NetworkManager 'ı kaldırın:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Note:** Paket zaten yüklü değilse, bu komut bir hata iletisiyle başarısız olur. Bu beklenen bir durumdur.
4. Aşağıdaki metni içeren `/etc/sysconfig/` dizininde **ağ** adlı bir dosya oluşturun:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Aşağıdaki metni içeren `/etc/sysconfig/network-scripts/` dizininde **ifcfg-eth0** adlı bir dosya oluşturun:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:
   
        # chkconfig network on
8. Aşağıdaki komutu çalıştırarak Python-pyasn1 ' i yüklemelisiniz:
   
        # sudo yum install python-pyasn1
9. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/boot/grub/menu.lst" öğesini açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Bu, Oracle 'ın Red Hat uyumlu çekirdekte bir hata nedeniyle NUMA 'yı devre dışı bırakır.
   
   Yukarıdaki ' a ek olarak, aşağıdaki parametrelerin *kaldırılması* önerilir:
   
        rhgb quiet crashkernel=auto
   
   Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir.
   
   `crashkernel` seçeneği istenirse yapılandırılmış olabilir, ancak bu parametrenin VM 'deki kullanılabilir bellek miktarını 128 MB veya daha fazla azaltarak, daha küçük VM boyutları üzerinde sorunlu olabilecek bir şekilde azalyacağını unutmayın.
10. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
11. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler. En son sürüm 2.0.15 ' dir.
    
        # sudo yum install WALinuxAgent
    
    Walınuxagent paketinin yüklenmesinde, ağ yöneticisi ve NetworkManager-GNOME paketlerini adım 2 ' de açıklandığı gibi kaldırılmayan bir şekilde kaldıracağına dikkat edin.
12. İşletim sistemi diskinde takas alanı oluşturmayın.
    
    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın),/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

---
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Oracle Linux 7 ' deki değişiklikler**

Oracle Linux 7 sanal makinesini Azure için hazırlamak, Oracle Linux 6 ' ya benzer, ancak dikkat edilmesi gereken bazı önemli farklılıklar vardır:

* Hem Red Hat uyumlu çekirdek hem de Oracle UEK3, Azure 'da desteklenmektedir.  UEK3 çekirdeği önerilir.
* NetworkManager paketi artık Azure Linux aracısıyla çakışmayacaktır. Bu paket varsayılan olarak yüklenir ve kaldırılmadığını öneririz.
* GRUB2 artık varsayılan önyükleme yükleyicisi olarak kullanıldığından, çekirdek parametrelerini düzenlemeyle ilgili yordam değişmiştir (aşağıya bakın).
* XFS artık varsayılan dosya sistemidir. İsterseniz ext4 dosya sistemi hala kullanılabilir.

**Yapılandırma adımları**

1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.
2. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.
3. Aşağıdaki metni içeren `/etc/sysconfig/` dizininde **ağ** adlı bir dosya oluşturun:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Aşağıdaki metni içeren `/etc/sysconfig/network-scripts/` dizininde **ifcfg-eth0** adlı bir dosya oluşturun:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:
   
        # sudo chkconfig network on
7. Aşağıdaki komutu çalıştırarak Python-pyasn1 paketini yüklemelisiniz:
   
        # sudo yum install python-pyasn1
8. Geçerli yum meta verilerini temizlemek ve tüm güncelleştirmeleri yüklemek için aşağıdaki komutu çalıştırın:
   
        # sudo yum clean all
        # sudo yum -y update
9. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/etc/default/grub" dosyasını açın ve `GRUB_CMDLINE_LINUX` parametresini düzenleyin, örneğin:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, NIC 'ler için yeni OEL 7 adlandırma kurallarını da kapatır. Yukarıdaki ' a ek olarak, aşağıdaki parametrelerin *kaldırılması* önerilir:
   
       rhgb quiet crashkernel=auto
   
   Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir.
   
   `crashkernel` seçeneği istenirse yapılandırılmış olabilir, ancak bu parametrenin VM 'deki kullanılabilir bellek miktarını 128 MB veya daha fazla azaltarak, daha küçük VM boyutları üzerinde sorunlu olabilecek bir şekilde azalyacağını unutmayın.
10. Yukarıdaki "/etc/default/grub" düzenlemesini tamamladıktan sonra, grub yapılandırmasını yeniden derlemek için aşağıdaki komutu çalıştırın:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
12. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. İşletim sistemi diskinde takas alanı oluşturmayın.
    
    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın),/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için Oracle Linux. vhd 'nizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).

