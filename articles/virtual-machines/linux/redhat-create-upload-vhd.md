---
title: Azure 'da kullanmak üzere bir Red Hat Enterprise Linux VHD oluşturma ve karşıya yükleme
description: Red Hat Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: szark
ms.openlocfilehash: 7c03271dc5fda5cee0b210370a965a45a6a7ef42
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035168"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Azure için Red Hat tabanlı bir sanal makine hazırlama
Bu makalede, Azure 'da kullanmak üzere bir Red Hat Enterprise Linux (RHEL) sanal makinesinin nasıl hazırlanacağını öğreneceksiniz. Bu makalede ele alınan RHEL 'nin sürümleri 6.7 + ve 7.1 + ' dir. Bu makalede ele alınan hazırlıklar için hiper yönetici, Hyper-V, çekirdek tabanlı sanal makine (KVM) ve VMware ' dir. Red Hat 'in bulut erişim programına katılma uygunluk gereksinimleri hakkında daha fazla bilgi için bkz. [Red Hat 'In bulut erişimi Web sitesi](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) ve [Azure 'da RHEL çalıştırma](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). RHEL görüntülerini oluşturmayı otomatikleştirebileceğiniz yollar için bkz. [Azure görüntü Oluşturucu](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Hyper-V Yöneticisi 'nden Red Hat tabanlı bir sanal makine hazırlama

### <a name="prerequisites"></a>Önkoşullar
Bu bölümde, Red Hat Web sitesinden bir ISO dosyası edindiğinizi ve RHEL görüntüsünü bir sanal sabit diske (VHD) yüklediğinizi varsaymış olursunuz. Hyper-V Yöneticisi 'Ni bir işletim sistemi görüntüsü yüklemek için kullanma hakkında daha fazla ayrıntı için bkz. [Hyper-v rolünü yüklemek ve bir sanal makineyi yapılandırmak](https://technet.microsoft.com/library/hh846766.aspx).

**RHEL yükleme notları**

* Azure, VHDX biçimini desteklemez. Azure yalnızca sabit VHD 'YI destekler. Hyper-V Yöneticisi 'Ni kullanarak diski VHD biçimine dönüştürebilir veya Convert-VHD cmdlet 'ini kullanabilirsiniz. VirtualBox kullanırsanız, diski oluştururken varsayılan dinamik olarak ayrılan seçeneğe karşılık olarak **sabit boyut** ' u seçin.
* Azure yalnızca 1. nesil sanal makineleri destekler. 1\. nesil bir sanal makineyi VHDX 'ten VHD dosya biçimine dönüştürebilir ve dinamik olarak genişleterek sabit boyutlu bir diske taşıyabilirsiniz. Bir sanal makinenin neslini değiştiremezsiniz. Daha fazla bilgi için bkz. [Hyper-V ' d a 1. nesil veya 2 sanal makine oluşturmalıyım?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* VHD için izin verilen en büyük boyut 1.023 GB 'dir.
* Mantıksal birim Yöneticisi (LVM) desteklenir ve Azure sanal makinelerde işletim sistemi diskinde veya veri disklerinde kullanılabilir. Ancak genel olarak, LVM yerine işletim sistemi diskinde standart bölümlerin kullanılması önerilir. Bu uygulama, özellikle de sorun giderme için bir işletim sistemi diskini başka bir özdeş sanal makineye iliştirmeniz gerekiyorsa, kopyalanmış sanal makinelerle LVM adı çakışmalarını önler. Ayrıca bkz. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ve [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) belgeleri.
* Evrensel Disk Biçimi (UDF) dosya sistemlerini bağlamak için çekirdek desteği gereklidir. Azure 'da ilk önyüklemede, konuğa bağlı olan UDF biçimli medya, sağlama yapılandırmasını Linux sanal makinesine geçirir. Azure Linux Aracısı, yapılandırmasını okumak ve sanal makineyi sağlamak için UDF dosya sistemini bağlayabilmelidir.
* İşletim sistemi diskinde bir takas bölümü yapılandırmayın. Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir.  Bu konuda daha fazla bilgi aşağıdaki adımlarda bulunabilir.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Aşağıdaki adımlarda daha fazla ayrıntı bulabilirsiniz. Daha fazla bilgi için Ayrıca bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Hyper-V Yöneticisi 'nden bir RHEL 6 sanal makinesi hazırlama

1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.

1. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.

1. RHEL 6 ' da NetworkManager, Azure Linux Aracısı 'nı kesintiye uğraşabilir. Aşağıdaki komutu çalıştırarak bu paketi kaldırın:
   
        # sudo rpm -e --nodeps NetworkManager

1. `/etc/sysconfig/network` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Ethernet arabirimi için statik kurallar oluşturmaktan kaçınmak üzere udev kurallarını taşıyın veya kaldırın. Bu kurallar, bir sanal makineyi Microsoft Azure veya Hyper-V ' d a kopyaladığınızda sorun oluşmasına neden olur:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

        # sudo chkconfig network on

1. Aşağıdaki komutu çalıştırarak, RHEL deposundan paketlerin yüklenmesini etkinleştirmek için Red Hat aboneliğinizi kaydedin:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Walınuxagent paketi, `WALinuxAgent-<version>`, Red Hat ekstralar deposuna gönderildi. Aşağıdaki komutu çalıştırarak ek özellikler deposunu etkinleştirin:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bu değişikliği yapmak için, bir metin düzenleyicisinde `/boot/grub/menu.lst` açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir.
    
    Ayrıca, aşağıdaki parametreleri kaldırmanızı öneririz:
    
        rhgb quiet crashkernel=auto
    
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir.  İsterseniz `crashkernel` seçeneğini yapılandırılmış bırakabilirsiniz. Bu parametrenin, sanal makinedeki kullanılabilir bellek miktarını 128 MB veya daha fazla azalttığını unutmayın. Bu yapılandırma, daha küçük sanal makine boyutlarında sorunlu olabilir.


1. Güvenli Kabuk (SSH) sunucusunun yüklü olduğundan ve önyükleme zamanında başlayacak şekilde yapılandırıldığından emin olun, yani genellikle varsayılandır. /Etc/ssh/sshd_config öğesini aşağıdaki satırı içerecek şekilde değiştirin:

        ClientAliveInterval 180

1. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Walınuxagent paketinin yüklenmesi, 3. adımda kaldırılmayan NetworkManager ve NetworkManager-GNOME paketlerini kaldırır.

1. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, sanal makine Azure üzerinde sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin geçici bir disk olduğunu ve sanal makine sağlanmamışsa boşaltılıp boşaltılmadığını unutmayın. Önceki adımda Azure Linux aracısını yükledikten sonra/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Aşağıdaki komutu çalıştırarak aboneliğin kaydını silin (gerekiyorsa):

        # sudo subscription-manager unregister

1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Hyper-V Yöneticisi 'nde **kapat** > **eylem** ' e tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Hyper-V Yöneticisi 'nden bir RHEL 7 sanal makinesi hazırlama

1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.

1. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.

1. `/etc/sysconfig/network` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

        # sudo systemctl enable network

1. Aşağıdaki komutu çalıştırarak, RHEL deposundan paketlerin yüklenmesini etkinleştirmek için Red Hat aboneliğinizi kaydedin:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bu değişikliği yapmak için, bir metin düzenleyicisinde `/etc/default/grub` açın ve `GRUB_CMDLINE_LINUX` parametresini düzenleyin. Örneğin:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Bu yapılandırma, NIC 'ler için yeni RHEL 7 adlandırma kurallarını da kapatır. Ayrıca, aşağıdaki parametreleri kaldırmanızı öneririz:
   
        rhgb quiet crashkernel=auto
   
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. İsterseniz `crashkernel` seçeneğini yapılandırılmış bırakabilirsiniz. Bu parametrenin, sanal makinedeki kullanılabilir bellek miktarını 128 MB veya daha fazla azalttığını ve bu, daha küçük sanal makine boyutlarında sorunlu olabileceğini unutmayın.

1. `/etc/default/grub`düzenlemesini tamamladıktan sonra, grub yapılandırmasını yeniden derlemek için aşağıdaki komutu çalıştırın:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. SSH sunucusunun yüklü olduğundan ve önyükleme zamanında başlayacak şekilde yapılandırıldığından emin olun, genellikle varsayılan değer olan. `/etc/ssh/sshd_config` aşağıdaki satırı içerecek şekilde değiştirin:

        ClientAliveInterval 180

1. Walınuxagent paketi, `WALinuxAgent-<version>`, Red Hat ekstralar deposuna gönderildi. Aşağıdaki komutu çalıştırarak ek özellikler deposunu etkinleştirin:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, sanal makine Azure üzerinde sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin geçici bir disk olduğunu ve sanal makine sağlanmadıysa boşaltılıp boşaltıyacağını unutmayın. Önceki adımda Azure Linux aracısını yükledikten sonra `/etc/waagent.conf` aşağıdaki parametreleri uygun şekilde değiştirin:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Aboneliğin kaydını silmek istiyorsanız aşağıdaki komutu çalıştırın:

        # sudo subscription-manager unregister

1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Hyper-V Yöneticisi 'nde **kapat** > **eylem** ' e tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>KVM 'den Red Hat tabanlı bir sanal makine hazırlama
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>KVM 'den RHEL 6 sanal makinesini hazırlama

1. Red Hat Web sitesinden RHEL 6 ' ın KVM görüntüsünü indirin.

1. Bir kök parolası ayarlayın.

    Şifrelenmiş bir parola oluşturun ve komutun çıkışını kopyalayın:

        # openssl passwd -1 changeme

    Guestbalık ile bir kök parola ayarlayın:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Kök kullanıcının ikinci alanını "!!" olarak değiştir şifrelenmiş parolaya.

1. QCOW2 görüntüsünden KVM 'de bir sanal makine oluşturun. Disk türünü **QCOW2**olarak ayarlayın ve sanal ağ arabirimi cihaz modelini **Virtio**olarak ayarlayın. Ardından, sanal makineyi başlatın ve kök olarak oturum açın.

1. `/etc/sysconfig/network` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Ethernet arabirimi için statik kurallar oluşturmaktan kaçınmak üzere udev kurallarını taşıyın veya kaldırın. Bu kurallar, bir sanal makineyi Azure 'da veya Hyper-V ' d a kopyaladığınızda sorun oluşmasına neden olur:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

        # chkconfig network on

1. Aşağıdaki komutu çalıştırarak, RHEL deposundan paketlerin yüklenmesini etkinleştirmek için Red Hat aboneliğinizi kaydedin:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bu yapılandırmayı yapmak için, bir metin düzenleyicisinde `/boot/grub/menu.lst` açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir.
    
    Ayrıca, aşağıdaki parametreleri kaldırmanızı öneririz:
    
        rhgb quiet crashkernel=auto
    
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. İsterseniz `crashkernel` seçeneğini yapılandırılmış bırakabilirsiniz. Bu parametrenin, sanal makinedeki kullanılabilir bellek miktarını 128 MB veya daha fazla azalttığını ve bu, daha küçük sanal makine boyutlarında sorunlu olabileceğini unutmayın.


1. Initramfs 'ye Hyper-V modülleri ekleyin:  

    `/etc/dracut.conf`düzenleyin ve aşağıdaki içeriği ekleyin:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs yeniden derle:

        # dracut -f -v

1. Bulutu kaldır-init:

        # yum remove cloud-init

1. SSH sunucusunun önyükleme sırasında başlatılacak şekilde yüklendiğinden ve yapılandırıldığından emin olun:

        # chkconfig sshd on

    /Etc/ssh/sshd_config öğesini aşağıdaki satırları içerecek şekilde değiştirin:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Walınuxagent paketi, `WALinuxAgent-<version>`, Red Hat ekstralar deposuna gönderildi. Aşağıdaki komutu çalıştırarak ek özellikler deposunu etkinleştirin:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux Aracısı, sanal makine Azure üzerinde sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin geçici bir disk olduğunu ve sanal makine sağlanmadıysa boşaltılıp boşaltıyacağını unutmayın. Önceki adımda Azure Linux aracısını yükledikten sonra **/etc/waagent.exe** için aşağıdaki parametreleri uygun şekilde değiştirin:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Aşağıdaki komutu çalıştırarak aboneliğin kaydını silin (gerekiyorsa):

        # subscription-manager unregister

1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Sanal makineyi KVM 'de kapatın.

1. QCOW2 görüntüsünü VHD biçimine dönüştürün.

> [!NOTE]
> QEMU-img sürümlerindeki bilinen bir hata vardır > = 2.2.1, hatalı biçimli bir VHD ile sonuçlanır. Bu sorun QEMU 2,6 ' de düzeltildi. QEMU-img 2.2.0 veya Lower ya da 2,6 veya üzeri bir sürümü kullanmanız önerilir. Başvuru: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>KVM 'den RHEL 7 sanal makinesini hazırlama

1. Red Hat Web sitesinden RHEL 7 ' nin KVM görüntüsünü indirin. Bu yordam, örnek olarak RHEL 7 kullanır.

1. Bir kök parolası ayarlayın.

    Şifrelenmiş bir parola oluşturun ve komutun çıkışını kopyalayın:

        # openssl passwd -1 changeme

    Guestbalık ile bir kök parola ayarlayın:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Kök kullanıcının ikinci alanını "!!" olarak değiştirin şifrelenmiş parolaya.

1. QCOW2 görüntüsünden KVM 'de bir sanal makine oluşturun. Disk türünü **QCOW2**olarak ayarlayın ve sanal ağ arabirimi cihaz modelini **Virtio**olarak ayarlayın. Ardından, sanal makineyi başlatın ve kök olarak oturum açın.

1. `/etc/sysconfig/network` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

        # sudo systemctl enable network

1. Aşağıdaki komutu çalıştırarak, RHEL deposundan paketlerin yüklenmesini etkinleştirmek için Red Hat aboneliğinizi kaydedin:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bu yapılandırmayı yapmak için, bir metin düzenleyicisinde `/etc/default/grub` açın ve `GRUB_CMDLINE_LINUX` parametresini düzenleyin. Örneğin:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Bu komut, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Bu komut, NIC 'ler için yeni RHEL 7 adlandırma kurallarını da kapatır. Ayrıca, aşağıdaki parametreleri kaldırmanızı öneririz:
   
        rhgb quiet crashkernel=auto
   
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. İsterseniz `crashkernel` seçeneğini yapılandırılmış bırakabilirsiniz. Bu parametrenin, sanal makinedeki kullanılabilir bellek miktarını 128 MB veya daha fazla azalttığını ve bu, daha küçük sanal makine boyutlarında sorunlu olabileceğini unutmayın.

1. `/etc/default/grub`düzenlemesini tamamladıktan sonra, grub yapılandırmasını yeniden derlemek için aşağıdaki komutu çalıştırın:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Hyper-V modüllerini initramfs içine ekleyin.

    `/etc/dracut.conf` düzenleyin ve içerik ekleyin:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs yeniden derle:

        # dracut -f -v

1. Bulutu kaldır-init:

        # yum remove cloud-init

1. SSH sunucusunun önyükleme sırasında başlatılacak şekilde yüklendiğinden ve yapılandırıldığından emin olun:

        # systemctl enable sshd

    /Etc/ssh/sshd_config öğesini aşağıdaki satırları içerecek şekilde değiştirin:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Walınuxagent paketi, `WALinuxAgent-<version>`, Red Hat ekstralar deposuna gönderildi. Aşağıdaki komutu çalıştırarak ek özellikler deposunu etkinleştirin:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:

        # yum install WALinuxAgent

    Waagent hizmetini etkinleştirin:

        # systemctl enable waagent.service

1. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, sanal makine Azure üzerinde sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin geçici bir disk olduğunu ve sanal makine sağlanmadıysa boşaltılıp boşaltıyacağını unutmayın. Önceki adımda Azure Linux aracısını yükledikten sonra `/etc/waagent.conf` aşağıdaki parametreleri uygun şekilde değiştirin:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Aşağıdaki komutu çalıştırarak aboneliğin kaydını silin (gerekiyorsa):

        # subscription-manager unregister

1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Sanal makineyi KVM 'de kapatın.

1. QCOW2 görüntüsünü VHD biçimine dönüştürün.

> [!NOTE]
> QEMU-img sürümlerindeki bilinen bir hata vardır > = 2.2.1, hatalı biçimli bir VHD ile sonuçlanır. Bu sorun QEMU 2,6 ' de düzeltildi. QEMU-img 2.2.0 veya Lower ya da 2,6 veya üzeri bir sürümü kullanmanız önerilir. Başvuru: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>VMware 'den Red Hat tabanlı bir sanal makine hazırlama
### <a name="prerequisites"></a>Önkoşullar
Bu bölümde, VMware 'de zaten bir RHEL sanal makinesini yüklemiş olduğunuz varsayılmaktadır. VMware 'de bir işletim sisteminin nasıl yükleneceğine ilişkin ayrıntılar için bkz. [VMware Konuk Işletim sistemi yükleme kılavuzu](https://partnerweb.vmware.com/GOSIG/home.html).

* Linux işletim sistemini yüklediğinizde, genellikle çoğu yükleme için varsayılan olan LVM yerine standart bölümler kullanmanızı öneririz. Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir sanal makineye eklenmesi gerekiyorsa, kopyalanmış sanal makineyle LVM adı çakışmalarını önler. Tercih edilen durumlarda LVM veya RAID, veri disklerinde kullanılabilir.
* İşletim sistemi diskinde bir takas bölümü yapılandırmayın. Linux aracısını, geçici kaynak diskinde bir takas dosyası oluşturacak şekilde yapılandırabilirsiniz. Bunu izleyen adımlarda hakkında daha fazla bilgi edinebilirsiniz.
* Sanal sabit diski oluştururken, **sanal diski tek bir dosya olarak depola**' yı seçin.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>VMware 'den bir RHEL 6 sanal makinesi hazırlama
1. RHEL 6 ' da NetworkManager, Azure Linux Aracısı 'nı kesintiye uğraşabilir. Aşağıdaki komutu çalıştırarak bu paketi kaldırın:
   
        # sudo rpm -e --nodeps NetworkManager

1. Aşağıdaki metni içeren/Etc/sysconfig/dizininde **Network** adlı bir dosya oluşturun:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Ethernet arabirimi için statik kurallar oluşturmaktan kaçınmak üzere udev kurallarını taşıyın veya kaldırın. Bu kurallar, bir sanal makineyi Azure 'da veya Hyper-V ' d a kopyaladığınızda sorun oluşmasına neden olur:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

        # sudo chkconfig network on

1. Aşağıdaki komutu çalıştırarak, RHEL deposundan paketlerin yüklenmesini etkinleştirmek için Red Hat aboneliğinizi kaydedin:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Walınuxagent paketi, `WALinuxAgent-<version>`, Red Hat ekstralar deposuna gönderildi. Aşağıdaki komutu çalıştırarak ek özellikler deposunu etkinleştirin:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde `/etc/default/grub` açın ve `GRUB_CMDLINE_LINUX` parametresini düzenleyin. Örneğin:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, aşağıdaki parametreleri kaldırmanızı öneririz:
   
        rhgb quiet crashkernel=auto
   
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. İsterseniz `crashkernel` seçeneğini yapılandırılmış bırakabilirsiniz. Bu parametrenin, sanal makinedeki kullanılabilir bellek miktarını 128 MB veya daha fazla azalttığını ve bu, daha küçük sanal makine boyutlarında sorunlu olabileceğini unutmayın.

1. Initramfs 'ye Hyper-V modülleri ekleyin:

    `/etc/dracut.conf`düzenleyin ve aşağıdaki içeriği ekleyin:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs yeniden derle:

        # dracut -f -v

1. SSH sunucusunun yüklü olduğundan ve önyükleme zamanında başlayacak şekilde yapılandırıldığından emin olun, genellikle varsayılan değer olan. `/etc/ssh/sshd_config` aşağıdaki satırı içerecek şekilde değiştirin:

    ClientAliveInterval 180

1. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, sanal makine Azure üzerinde sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin geçici bir disk olduğunu ve sanal makine sağlanmadıysa boşaltılıp boşaltıyacağını unutmayın. Önceki adımda Azure Linux aracısını yükledikten sonra `/etc/waagent.conf` aşağıdaki parametreleri uygun şekilde değiştirin:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Aşağıdaki komutu çalıştırarak aboneliğin kaydını silin (gerekiyorsa):

        # sudo subscription-manager unregister

1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Sanal makineyi kapatın ve VMDK dosyasını bir. vhd dosyasına dönüştürün.

> [!NOTE]
> QEMU-img sürümlerindeki bilinen bir hata vardır > = 2.2.1, hatalı biçimli bir VHD ile sonuçlanır. Bu sorun QEMU 2,6 ' de düzeltildi. QEMU-img 2.2.0 veya Lower ya da 2,6 veya üzeri bir sürümü kullanmanız önerilir. Başvuru: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>VMware 'den RHEL 7 sanal makinesini hazırlama
1. `/etc/sysconfig/network` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. `/etc/sysconfig/network-scripts/ifcfg-eth0` dosyasını oluşturun veya düzenleyin ve aşağıdaki metni ekleyin:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

        # sudo systemctl enable network

1. Aşağıdaki komutu çalıştırarak, RHEL deposundan paketlerin yüklenmesini etkinleştirmek için Red Hat aboneliğinizi kaydedin:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bu değişikliği yapmak için, bir metin düzenleyicisinde `/etc/default/grub` açın ve `GRUB_CMDLINE_LINUX` parametresini düzenleyin. Örneğin:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Bu yapılandırma aynı zamanda tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, NIC 'ler için yeni RHEL 7 adlandırma kurallarını kapatır. Ayrıca, aşağıdaki parametreleri kaldırmanızı öneririz:
   
        rhgb quiet crashkernel=auto
   
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. İsterseniz `crashkernel` seçeneğini yapılandırılmış bırakabilirsiniz. Bu parametrenin, sanal makinedeki kullanılabilir bellek miktarını 128 MB veya daha fazla azalttığını ve bu, daha küçük sanal makine boyutlarında sorunlu olabileceğini unutmayın.

1. `/etc/default/grub`düzenlemesini tamamladıktan sonra, grub yapılandırmasını yeniden derlemek için aşağıdaki komutu çalıştırın:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Initramfs 'ye Hyper-V modülleri ekleyin.

    `/etc/dracut.conf`düzenleyin, içerik ekleyin:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Initramfs yeniden derle:

        # dracut -f -v

1. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun. Bu ayar genellikle varsayılandır. `/etc/ssh/sshd_config` aşağıdaki satırı içerecek şekilde değiştirin:

        ClientAliveInterval 180

1. Walınuxagent paketi, `WALinuxAgent-<version>`, Red Hat ekstralar deposuna gönderildi. Aşağıdaki komutu çalıştırarak ek özellikler deposunu etkinleştirin:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, sanal makine Azure üzerinde sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin geçici bir disk olduğunu ve sanal makine sağlanmadıysa boşaltılıp boşaltıyacağını unutmayın. Önceki adımda Azure Linux aracısını yükledikten sonra `/etc/waagent.conf` aşağıdaki parametreleri uygun şekilde değiştirin:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Aboneliğin kaydını silmek istiyorsanız aşağıdaki komutu çalıştırın:

        # sudo subscription-manager unregister

1. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

        # Mote: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Sanal makineyi kapatın ve VMDK dosyasını VHD biçimine dönüştürün.

> [!NOTE]
> QEMU-img sürümlerindeki bilinen bir hata vardır > = 2.2.1, hatalı biçimli bir VHD ile sonuçlanır. Bu sorun QEMU 2,6 ' de düzeltildi. QEMU-img 2.2.0 veya Lower ya da 2,6 veya üzeri bir sürümü kullanmanız önerilir. Başvuru: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Bir kickstart dosyasını otomatik olarak kullanarak bir ISO 'dan Red Hat tabanlı bir sanal makine hazırlama
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Bir kickstart dosyasından RHEL 7 sanal makinesi hazırlama

1.  Aşağıdaki içeriği içeren bir kickstart dosyası oluşturun ve dosyayı kaydedin. Kickstart yüklemesi hakkında daha fazla bilgi için, bkz. [kickstart Yükleme Kılavuzu](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Yükleme sisteminin erişebileceği kickstart dosyasını yerleştirin.

1. Hyper-V Yöneticisi 'nde yeni bir sanal makine oluşturun. **Sanal sabit diski bağla** sayfasında, **daha sonra bir sanal sabit disk Ekle**' yi seçin ve yeni sanal makine Sihirbazı ' nı doldurun.

1. Sanal makine ayarlarını açın:

    a.  Sanal makineye yeni bir sanal sabit disk ekleyin. **VHD biçimini** ve **sabit boyutu**seçtiğinizden emin olun.

    b.  Yükleme ISO dosyasını DVD sürücüsüne ekleyin.

    c.  BIOS 'u CD 'den önyükleme olacak şekilde ayarlayın.

1. Sanal makineyi başlatın. Yükleme Kılavuzu göründüğünde, önyükleme seçeneklerini yapılandırmak için **Tab** tuşuna basın.

1. Önyükleme seçeneklerinin sonuna `inst.ks=<the location of the kickstart file>` girin ve **ENTER**tuşuna basın.

1. Yüklemenin bitmesini bekleyin. İşlem tamamlandığında, sanal makine otomatik olarak kapatılır. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="known-issues"></a>Bilinen sorunlar
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Hyper-V sürücüsü, Hyper-V olmayan bir hiper yönetici kullanılırken ilk RAM diskine dahil edilemedi

Bazı durumlarda Linux yükleyicileri, Linux 'un Hyper-V ortamında çalıştığını algılayamadığı takdirde, ilk RAM diskindeki (ınitrd veya ınitramfs) Hyper-V sürücülerini içermeyebilir.

Linux görüntünüzü hazırlamak için farklı bir sanallaştırma sistemi (yani VirtualBox, Xen, vb.) kullanırken, ilk RAM diskinde en az hv_vmbus ve hv_storvsc çekirdek modüllerinin kullanılabilir olmasını sağlamak için ınitrd 'i yeniden oluşturmanız gerekebilir. Bu, en azından yukarı akış Red Hat dağıtımına dayalı olan sistemlerde bilinen bir sorundur.

Bu sorunu çözmek için, ınitramfs 'ye Hyper-V modülleri ekleyin ve yeniden oluşturun:

`/etc/dracut.conf`düzenleyin ve aşağıdaki içeriği ekleyin:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Initramfs yeniden derle:

        # dracut -f -v

Daha fazla ayrıntı için, [ınitramfs](https://access.redhat.com/solutions/1958)'yi yeniden oluşturma hakkında bilgi için bkz.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için Red Hat Enterprise Linux sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).

Red Hat Enterprise Linux çalıştırılmak üzere sertifikalı hiper yöneticiler hakkında daha fazla ayrıntı için [Red Hat Web sitesine](https://access.redhat.com/certified-hypervisors)bakın.
