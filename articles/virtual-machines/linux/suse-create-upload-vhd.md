---
title: Azure 'da SUSE Linux VHD oluşturma ve karşıya yükleme
description: SUSE Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 276f5f4542ecea42c665764b8c4e5f66f2531126
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552720"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Azure'da SLES veya openSUSE sanal makinesi hazırlama


Bu makalede, bir sanal sabit diske zaten SUSE veya openSUSE Linux işletim sistemi yüklediğinizi varsaymış olursunuz. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE yükleme notları
* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya Convert-VHD cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) veya [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) , veri disklerinde kullanılabilir.
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir.  Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>SUSE Studio 'Yu kullanma
[SUSE Studio](https://studioexpress.opensuse.org/) , Azure ve Hyper-V IÇIN SLES ve openSUSE görüntülerinizi kolayca oluşturabilir ve yönetebilir. Bu, kendi SLES ve openSUSE görüntülerinizi özelleştirmek için önerilen yaklaşımdır.

Kendi VHD 'nizi oluşturmaya alternatif olarak, SUSE [sanal](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)makineleri için SLES için KCG (kendi aboneliğini getir) görüntülerini da yayımlar.

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Azure için SUSE Linux Enterprise Server hazırlama
1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.
2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.
3. SUSE Linux Enterprise sisteminizi, güncelleştirmeleri indirmesine ve paketleri yüklemeye izin verecek şekilde kaydedin.
4. En son düzeltme ekleriyle sistemi güncelleştirin:

    ```console
    # sudo zypper update
    ```
    
5. Azure Linux Aracısı ve Cloud-init 'yi yükler

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Boot & Cloud-init için waagent 'ı etkinleştirin

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Waagent ve Cloud-init yapılandırmasını Güncelleştir

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Konsol günlüklerinin seri bağlantı noktasına gönderildiğinden emin olmak için/etc/default/grub dosyasını düzenleyin ve ardından GRUB2-mkconfig-o/Boot/GRUB2/grub.cfg ile ana yapılandırma dosyasını güncelleştirin

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir.
    
9. /Etc/fstab dosyasının UUID 'sini (UUID) kullanarak diske başvurması sağlayın
         
10. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. "/Etc/sysconfig/Network/DHCP" dosyasını düzenlemeniz ve `DHCLIENT_SET_HOSTNAME` parametreyi aşağıdaki şekilde değiştirmeniz önerilir:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. "/Etc/sudoers" içinde, varsa, aşağıdaki satırları açıklama veya kaldırma:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun. Bu genellikle varsayılandır.

14. Değiştirme yapılandırması
 
    İşletim sistemi diskinde takas alanı oluşturmayın.

    Daha önce Azure Linux Aracısı, sanal makine Azure 'da sağlandıktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırmıştı. Ancak bu artık Cloud-init tarafından işlenirse, kaynak diski biçimlendirmek için Linux Aracısı 'nı kullanmanız **gerekir** . değiştirme dosyasını oluşturmak için aşağıdaki parametreleri `/etc/waagent.conf` uygun şekilde değiştirin:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Bağlama, biçimlendirme ve değiştirme oluşturmak istiyorsanız şunlardan birini yapabilirsiniz:
    * Sanal makineyi her oluşturduğunuzda bunu Cloud-init yapılandırması olarak geçirin.
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
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

---
## <a name="prepare-opensuse-131"></a>OpenSUSE 13.1 + hazırlama
1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.
2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.
3. Kabukta ' ' komutunu çalıştırın `zypper lr` . Bu komut aşağıdakine benzer bir çıktı döndürürse, depolar beklenen şekilde yapılandırılır; hiçbir ayarlama gerekmez (sürüm numaralarının değişebileceğini unutmayın):

   | # | Diğer ad                 | Name                  | Etkin | Yenile
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Bulut: Tools_13.1      | Bulut: Tools_13.1      | Yes     | Yes
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Yes     | Yes
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Yes     | Yes

    Komut "tanımlı depo yok..." öğesini döndürürse daha sonra bu depoyu eklemek için aşağıdaki komutları kullanın:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Daha sonra ' ' komutunu çalıştırarak depoların eklendiğini doğrulayabilirsiniz `zypper lr` . İlgili güncelleştirme depolarından birinin etkin olmaması durumunda aşağıdaki komutla etkinleştirin:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Çekirdeği kullanılabilir en son sürüme güncelleştirin:

    ```console
    # sudo zypper up kernel-default
    ```

    Ya da sistemi en son düzeltme ekleriyle güncelleştirmek için:

    ```console
    # sudo zypper update
    ```

5. Azure Linux aracısını yükler.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/boot/grub/menu.lst" öğesini açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, varsa çekirdek önyükleme satırından aşağıdaki parametreleri kaldırın:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. "/Etc/sysconfig/Network/DHCP" dosyasını düzenlemeniz ve `DHCLIENT_SET_HOSTNAME` parametreyi aşağıdaki şekilde değiştirmeniz önerilir:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Önemli:** "/Etc/sudoers" içinde, varsa, aşağıdaki satırları açıklama veya kaldırma:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
10. İşletim sistemi diskinde takas alanı oluşturmayın.

    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın),/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Azure Linux aracısının başlangıçta çalıştığından emin olun:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için SUSE Linux sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).