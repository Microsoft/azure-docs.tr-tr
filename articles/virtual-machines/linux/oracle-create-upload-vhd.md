---
title: Oracle Linux VHD oluşturma ve karşıya yükleme
description: Oracle Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 9984589b19f15ab00e895bca75c295a92a68d0fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557803"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Azure için Oracle Linux bir sanal makine hazırlama

Bu makalede bir Oracle Linux işletim sistemini zaten bir sanal sabit diske yüklediğinizi varsaymış olursunuz. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Oracle Linux yükleme notları
* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* Hyper-V ve Azure desteği, uygun olmayan kurumsal çekirdek (UEK) veya Red Hat uyumlu çekirdekle Oracle Linux.
* Oracle 'ın UEK2, gerekli sürücüleri içermediğinden Hyper-V ve Azure üzerinde desteklenmez.
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya Convert-VHD cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz.
* **UDF dosya sistemlerini bağlamak için çekirdek desteği gereklidir.** Azure 'da ilk önyüklemede sağlama yapılandırması, konuğa bağlı olan UDF biçimli medya aracılığıyla Linux VM 'ye geçirilir. Azure Linux Aracısı, yapılandırmasını okumak ve VM 'yi sağlamak için UDF dosya sistemini bağlayabilmelidir.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) veya [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) , veri disklerinde kullanılabilir.
* 2.6.37 ' den önceki Linux çekirdek sürümleri, Hyper-V üzerinde NUMA 'yı daha büyük VM boyutları ile desteklemez. Bu sorun öncelikle yukarı akış Red Hat 2.6.32 çekirdeğini kullanarak eski dağıtımları etkiler ve Oracle Linux 6,6 ve üzeri sürümlerde düzeltildi
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* Deponun etkinleştirildiğinden emin olun `Addons` . Dosyayı `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) veya `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) düzenleyin ve `enabled=0` `enabled=1` Bu dosyada **[ol6_addons]** veya **[ol7_addons]** altındaki satırı değiştirin.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 ve üzeri
Sanal makinenin Azure 'da çalışması için, işletim sistemindeki belirli yapılandırma adımlarını tamamlamalısınız.

1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.
2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.
3. Aşağıdaki komutu çalıştırarak NetworkManager 'ı kaldırın:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Note:** Paket zaten yüklü değilse, bu komut bir hata iletisiyle başarısız olur. Bu beklenen bir durumdur.
4. Aşağıdaki metni içeren dizinde **ağ** adlı bir dosya oluşturun `/etc/sysconfig/` :

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Aşağıdaki metni içeren dizinde **ifcfg-eth0** adlı bir dosya oluşturun `/etc/sysconfig/network-scripts/` :

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

    ```console
    # chkconfig network on
    ```

8. Aşağıdaki komutu çalıştırarak Python-pyasn1 ' i yüklemelisiniz:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/boot/grub/menu.lst" öğesini açın ve çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir.
   
   Yukarıdaki ' a ek olarak, aşağıdaki parametrelerin *kaldırılması* önerilir:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir.
   
   `crashkernel`İsterseniz seçenek, istenirse yapılandırılmış olabilir, ancak bu PARAMETRENIN VM 'deki kullanılabilir bellek miktarını 128 MB veya daha fazla azaltır ve bu da daha küçük VM boyutları üzerinde sorunlu olabilir.
10. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
11. Aşağıdaki komutu çalıştırarak Azure Linux aracısını yükler. En son sürüm 2.0.15 ' dir.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Walınuxagent paketinin yüklenmesinde, ağ yöneticisi ve NetworkManager-GNOME paketlerini adım 2 ' de açıklandığı gibi kaldırılmayan bir şekilde kaldıracağına dikkat edin.
12. İşletim sistemi diskinde takas alanı oluşturmayın.
    
    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın),/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 ve üzeri
**Oracle Linux 7 ' deki değişiklikler**

Oracle Linux 7 sanal makinesini Azure için hazırlamak, Oracle Linux 6 ' ya benzer, ancak dikkat edilmesi gereken bazı önemli farklılıklar vardır:

* Azure, uygun olmayan kurumsal çekirdek (UEK) veya Red Hat uyumlu çekirdekle Oracle Linux destekler. UEK ile Oracle Linux önerilir.
* NetworkManager paketi artık Azure Linux aracısıyla çakışmayacaktır. Bu paket varsayılan olarak yüklenir ve kaldırılmadığını öneririz.
* GRUB2 artık varsayılan önyükleme yükleyicisi olarak kullanıldığından, çekirdek parametrelerini düzenlemeyle ilgili yordam değişmiştir (aşağıya bakın).
* XFS artık varsayılan dosya sistemidir. İsterseniz ext4 dosya sistemi hala kullanılabilir.

**Yapılandırma adımları**

1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.
2. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.
3. Aşağıdaki metni içeren dizinde **ağ** adlı bir dosya oluşturun `/etc/sysconfig/` :

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Aşağıdaki metni içeren dizinde **ifcfg-eth0** adlı bir dosya oluşturun `/etc/sysconfig/network-scripts/` :

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Aşağıdaki komutu çalıştırarak ağ hizmetinin önyükleme zamanında başlamasını sağlayın:

    ```console
    # sudo chkconfig network on
    ```

7. Aşağıdaki komutu çalıştırarak Python-pyasn1 paketini yüklemelisiniz:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Geçerli yum meta verilerini temizlemek ve tüm güncelleştirmeleri yüklemek için aşağıdaki komutu çalıştırın:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/etc/default/grub" dosyasını açın ve `GRUB_CMDLINE_LINUX` parametreyi düzenleyin, örneğin:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini de sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, Oracle Linux 7 ' deki NIC 'ler için adlandırma kurallarını, uygun olmayan kurumsal çekirdekle devre dışı bırakır. Yukarıdaki ' a ek olarak, aşağıdaki parametrelerin *kaldırılması* önerilir:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir.
   
   `crashkernel`İsterseniz seçenek, istenirse yapılandırılmış olabilir, ancak bu PARAMETRENIN VM 'deki kullanılabilir bellek miktarını 128 MB veya daha fazla azaltır ve bu da daha küçük VM boyutları üzerinde sorunlu olabilir.
10. Yukarıdaki "/etc/default/grub" düzenlemesini tamamladıktan sonra, grub yapılandırmasını yeniden derlemek için aşağıdaki komutu çalıştırın:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
12. Azure Linux aracısını ve bağımlılıklarını yükler:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Sağlamayı işlemek için Cloud-init ' i yüklemek

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

14. Değiştirme yapılandırması, işletim sistemi diskinde takas alanı oluşturmaz.

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


15. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

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

16. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için Oracle Linux. vhd 'nizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).