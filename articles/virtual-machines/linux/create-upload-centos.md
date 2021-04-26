---
title: CentOS tabanlı bir Linux VHD oluşturma ve karşıya yükleme
description: CentOS tabanlı bir Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 4745e631bd92675f8dd1ef0d390baa88f7666552
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554675"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Azure'da CentOS tabanlı bir sanal makine hazırlama

CentOS tabanlı bir Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.

* [Azure için CentOS 6. x sanal makinesini hazırlama](#centos-6x)
* [Azure için CentOS 7.0 + sanal makinesi hazırlama](#centos-70)


## <a name="prerequisites"></a>Önkoşullar

Bu makalede, bir sanal sabit diske bir CentOS (veya benzer türev) Linux işletim sistemini zaten yüklemiş olduğunuz varsayılmaktadır. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**CentOS yükleme notları**

* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya Convert-VHD cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz. VirtualBox kullanıyorsanız, disk oluştururken dinamik olarak ayrılan varsayılan değer tersine, **sabit boyutun** seçilmesi anlamına gelir.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız *önerilir* (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun giderme için başka bir özdeş VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) veya [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) veri disklerinde kullanılıyor olabilir.
* **UDF dosya sistemlerini bağlamak için çekirdek desteği gereklidir.** Azure 'da ilk önyüklemede sağlama yapılandırması, konuğa bağlı olan UDF biçimli medya aracılığıyla Linux VM 'ye geçirilir. Azure Linux Aracısı, yapılandırmasını okumak ve VM 'yi sağlamak için UDF dosya sistemini bağlayabilmelidir.
* Linux çekirdek sürümleri 2.6.37, Hyper-V üzerinde daha büyük VM boyutlarına sahip NUMA 'yı desteklemez. Bu sorun öncelikle yukarı akış Red Hat 2.6.32 çekirdeğini kullanarak eski dağıtımları etkiler ve RHEL 6,6 (Kernel-2.6.32-504) içinde düzeltilmiştir. 2.6.37 'den eski olan özel çekirdekler çalıştıran sistemler veya 2.6.32-504 ' den eski RHEL tabanlı çekirdekler, `numa=off` grub. conf içindeki çekirdek komut satırında önyükleme parametresini ayarlanmalıdır. Daha fazla bilgi için bkz. Red hat [KB 436883](https://access.redhat.com/solutions/436883).
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

## <a name="centos-6x"></a>CentOS 6. x

1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.

2. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.

3. CentOS 6 ' da, NetworkManager, Azure Linux Aracısı 'nı kesintiye uğratabilirler. Aşağıdaki komutu çalıştırarak bu paketi kaldırın:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Dosyayı oluşturun veya düzenleyin `/etc/sysconfig/network` ve aşağıdaki metni ekleyin:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Dosyayı oluşturun veya düzenleyin `/etc/sysconfig/network-scripts/ifcfg-eth0` ve aşağıdaki metni ekleyin:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

    ```bash
    sudo chkconfig network on
    ```

8. Azure veri merkezleri içinde barındırılan OpenLogic yansıtılarını kullanmak istiyorsanız, `/etc/yum.repos.d/CentOS-Base.repo` dosyayı aşağıdaki depolarla değiştirin.  Bu, Azure Linux Aracısı gibi ek paketleri de içeren **[OpenLogic]** deposunu ekler:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Bu kılavuzun geri kalanı, `[openlogic]` aşağıda Azure Linux aracısını yüklemek için kullanılacak en az depoyu kullandığınızı varsayacaktır.

9. Aşağıdaki satırı/etc/yum.conf öğesine ekleyin:

    ```console
    http_caching=packages
    ```

10. Geçerli yum meta verilerini temizlemek ve sistemi en son paketlerle güncelleştirmek için aşağıdaki komutu çalıştırın:

    ```bash
    yum clean all
    ```

    CentOS 'ın eski bir sürümü için bir görüntü oluşturmadığınız takdirde, tüm paketlerin en son sürümüne güncelleştirilmesi önerilir:

    ```bash
    sudo yum -y update
    ```

    Bu komut çalıştırıldıktan sonra yeniden başlatma gerekebilir.

11. Seçim Linux Integration Services (LIS) için sürücüleri yükler.

    > [!IMPORTANT]
    > Bu adım, CentOS 6,3 ve önceki sürümleri için **gereklidir** ve sonraki sürümler için isteğe bağlıdır.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Alternatif olarak, RPM 'yi sanal makinenize yüklemek için [LIS indirme sayfasındaki](https://www.microsoft.com/download/details.aspx?id=55106) el ile yükleme talimatlarını izleyebilirsiniz.

12. Azure Linux aracısını ve bağımlılıklarını yükler. Waagent hizmetini başlatma ve etkinleştirme:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Walınuxagent paketi NetworkManager ve NetworkManager-GNOME paketlerini, adım 3 ' te açıklandığı şekilde kaldırıldıklarında kaldırır.

13. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, `/boot/grub/menu.lst` bir metin düzenleyicisinde açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir.

    Yukarıdaki ' a ek olarak, aşağıdaki parametrelerin *kaldırılması* önerilir:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir.  `crashkernel`İsterseniz seçenek, istenirse yapılandırılmış olabilir, ancak bu PARAMETRENIN VM 'deki kullanılabilir bellek miktarını 128 MB veya daha fazla azaltır ve bu da daha küçük VM boyutları üzerinde sorunlu olabilir.

    > [!Important]
    > CentOS 6,5 ve önceki sürümleri de çekirdek parametresini ayarlamış olmalıdır `numa=off` . Bkz. Red hat [KB 436883](https://access.redhat.com/solutions/436883).

14. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.

15. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın), aşağıdaki parametreleri `/etc/waagent.conf` uygun şekilde değiştirin:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.



## <a name="centos-70"></a>CentOS 7.0 +

**CentOS 7 (ve benzer türetme) değişiklikleri**

Azure için bir CentOS 7 sanal makinesinin hazırlanması, CentOS 6 ' ya çok benzer, ancak dikkat edilmesi gereken birkaç önemli fark vardır:

* NetworkManager paketi artık Azure Linux aracısıyla çakışmayacaktır. Bu paket varsayılan olarak yüklenir ve kaldırılmadığını öneririz.
* GRUB2 artık varsayılan önyükleme yükleyicisi olarak kullanıldığından, çekirdek parametrelerini düzenlemeyle ilgili yordam değişmiştir (aşağıya bakın).
* XFS artık varsayılan dosya sistemidir. İsterseniz ext4 dosya sistemi hala kullanılabilir.

**Yapılandırma adımları**

1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.

2. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.

3. Dosyayı oluşturun veya düzenleyin `/etc/sysconfig/network` ve aşağıdaki metni ekleyin:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Dosyayı oluşturun veya düzenleyin `/etc/sysconfig/network-scripts/ifcfg-eth0` ve aşağıdaki metni ekleyin:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Azure veri merkezleri içinde barındırılan OpenLogic yansıtılarını kullanmak istiyorsanız, `/etc/yum.repos.d/CentOS-Base.repo` dosyayı aşağıdaki depolarla değiştirin.  Bu, Azure Linux Aracısı için paketleri içeren **[OpenLogic]** deposunu da ekler:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Bu kılavuzun geri kalanı, `[openlogic]` aşağıda Azure Linux aracısını yüklemek için kullanılacak en az depoyu kullandığınızı varsayacaktır.

7. Geçerli yum meta verilerini temizlemek ve tüm güncelleştirmeleri yüklemek için aşağıdaki komutu çalıştırın:

    ```bash
    sudo yum clean all
    ```

    CentOS 'ın eski bir sürümü için bir görüntü oluşturmadığınız takdirde, tüm paketlerin en son sürümüne güncelleştirilmesi önerilir:

    ```bash
    sudo yum -y update
    ```

    Bu komut çalıştırıldıktan sonra bir yeniden başlatma gerekli olabilir.

8. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için `/etc/default/grub` bir metin düzenleyicisinde açın ve `GRUB_CMDLINE_LINUX` parametresini düzenleyin, örneğin:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, NIC 'ler için yeni CentOS 7 adlandırma kurallarını kapatır. Yukarıdaki ' a ek olarak, aşağıdaki parametrelerin *kaldırılması* önerilir:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. `crashkernel`İsterseniz seçenek, istenirse yapılandırılmış olabilir, ancak bu PARAMETRENIN VM 'deki kullanılabilir bellek miktarını 128 MB veya daha fazla azaltır ve bu da daha küçük VM boyutları üzerinde sorunlu olabilir.

9. Yukarıdaki her başına bir kez düzenlemenizi tamamladıktan sonra `/etc/default/grub` , grub yapılandırmasını yeniden derlemek için aşağıdaki komutu çalıştırın:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Görüntüyü **VMware, VirtualBox veya KVM** 'den oluşturuyorsanız: Hyper-V sürücülerinin initramfs 'ye eklendiğinden emin olun:

    Düzenleme `/etc/dracut.conf` , içerik ekleme:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    İnitramfs 'yi yeniden oluşturun:

    ```bash
    sudo dracut -f -v
    ```

11. Azure VM uzantıları için Azure Linux aracısını ve bağımlılıklarını yükler:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Sağlamayı işlemek için Cloud-init ' i yüklemek

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```


13. Değiştirme yapılandırması, işletim sistemi diskinde takas alanı oluşturmaz.

    Daha önce Azure Linux Aracısı, sanal makine Azure 'da sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırmıştı. Ancak bu artık Cloud-init tarafından işlenirse, kaynak diski biçimlendirmek için Linux Aracısı 'nı kullanmanız **gerekir** . değiştirme dosyasını oluşturmak için aşağıdaki parametreleri `/etc/waagent.conf` uygun şekilde değiştirin:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Bağlama, biçimlendirme ve değiştirme oluşturmak istiyorsanız şunlardan birini yapabilirsiniz:
    * VM 'yi her oluşturduğunuzda bunu Cloud-init yapılandırması olarak geçirin
    * Bir Cloud-init yönergesini, sanal makine her oluşturulduğunda bunu yapan görüntüye bir kez kullanın:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

14. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

    ```console
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history


    # export HISTSIZE=0

    # logout
    ```

15. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar

Artık Azure 'da yeni sanal makineler oluşturmak için CentOS Linux sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).