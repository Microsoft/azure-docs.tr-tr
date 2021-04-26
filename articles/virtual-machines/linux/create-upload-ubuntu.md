---
title: Azure 'da bir Ubuntu Linux VHD oluşturma ve karşıya yükleme
description: Ubuntu Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 92ceecd16a428593764fe5ab6478cc4ea7ab91d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554624"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Azure’da Ubuntu sanal makinesi hazırlama


Ubuntu artık, adresinden indirmek üzere resmi Azure VHD 'leri yayımlar [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Azure için kendi özelleştirilmiş Ubuntu görüntünüzü oluşturmanız gerekiyorsa, bu bilinen çalışma VHD 'leri ile başlamanız ve gerektiğinde özelleştirmeniz önerilir. En son görüntü yayınları her zaman aşağıdaki konumlarda bulunabilir:

* Ubuntu 16.04/Xenial: [Ubuntu-16,04-Server-cloudimg-AMD64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-AMD64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Önkoşullar
Bu makalede bir Ubuntu Linux işletim sistemini zaten bir sanal sabit diske yüklediğinizi varsaymış olursunuz. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Ubuntu yükleme notları**

* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz `Convert-VHD` .
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) veya [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) , veri disklerinde kullanılabilir.
* İşletim sistemi diskinde bir takas bölümü veya Swapfile yapılandırmayın. Cloud-init sağlama Aracısı, geçici kaynak diskinde bir takas dosyası veya takas bölümü oluşturacak şekilde yapılandırılabilir. Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>El ile adımlar
> [!NOTE]
> Azure için kendi özel Ubuntu görüntünüzü oluşturmayı denemeden önce lütfen bunun yerine önceden oluşturulmuş ve test edilmiş görüntüleri kullanmayı göz önünde bulundurun [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.

2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.

3. Görüntüdeki geçerli depoları, Ubuntu 'ın Azure deposunu kullanacak şekilde değiştirin.

    Düzenlemeden önce `/etc/apt/sources.list` , bir yedekleme yapmanız önerilir:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 ve Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Ubuntu Azure görüntüleri artık [Azure tarafından hazırlanmış çekirdeği](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel)kullanıyor. Aşağıdaki komutları çalıştırarak işletim sistemini Azure ile özel en son çekirdeğe güncelleştirin ve Azure Linux araçları 'nı (Hyper-V bağımlılıkları dahil) yükler:

    Ubuntu 16,04 ve Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Grub için çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu `/etc/default/grub` bir metin düzenleyicisinde açmak için, adlı değişkeni bulun `GRUB_CMDLINE_LINUX_DEFAULT` (veya gerekirse ekleyin) ve aşağıdaki parametreleri içerecek şekilde düzenleyin:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Bu dosyayı kaydedip kapatın ve sonra çalıştırın `sudo update-grub` . Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarıyla birlikte Azure teknik desteğine yardımcı olabilir.

6. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.

7. Cloud-init (sağlama Aracısı) ve Azure Linux Aracısı 'nı (konuk uzantıları işleyicisi) yükler. Cloud-init, sağlama sırasında ve sonraki her önyükleme sırasında sistem ağ yapılandırmasını yapılandırmak için Netplan kullanır.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `walinuxagent`Paket, `NetworkManager` yüklüyse ve paketlerini kaldırabilir `NetworkManager-gnome` .

8. Azure 'da Cloud-init sağlama ile çakışabilecek Cloud-init varsayılan yapılandırmalarını ve kalan Netplan yapıtları kaldırın:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Cloud-init ' i, Azure veri kaynağını kullanarak sistem sağlamak için yapılandırın:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Sağlamayı gerçekleştirmek için Azure Linux aracısını Cloud-init ' i temel almak üzere yapılandırın. Bu seçenekler hakkında daha fazla bilgi için [Walınuxagent projesine](https://github.com/Azure/WALinuxAgent) göz atın.

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Cloud-init ve Azure Linux Aracısı çalışma zamanı yapılarını ve günlüklerini Temizleme:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:

    > [!NOTE]
    > `sudo waagent -force -deprovision+user`Komut, sistemi temizlemeye çalışır ve yeniden sağlama için uygun hale getirir. `+user`Seçeneği, son sağlanan kullanıcı hesabını ve ilişkili verileri siler.

    > [!WARNING]
    > Yukarıdaki komutu kullanarak sağlamayı kaldırma, görüntünün tüm hassas bilgileri temizleneceğini ve yeniden dağıtım için uygun olduğunu garanti etmez.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın.

14. Azure yalnızca sabit boyutlu VHD 'leri kabul eder. VM 'nin işletim sistemi diski sabit boyutlu bir VHD değilse, `Convert-VHD` PowerShell cmdlet 'ini kullanın ve `-VHDType Fixed` seçeneğini belirtin. Lütfen şu belgelere bakın `Convert-VHD` : [Convert-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için Ubuntu Linux sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).