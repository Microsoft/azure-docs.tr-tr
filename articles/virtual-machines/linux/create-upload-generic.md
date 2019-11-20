---
title: Azure 'da bir Linux VHD oluşturma ve karşıya yükleme
description: Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: eb6ef87edd2ff16750573c6b8c719fa4b81d3a4c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083595"
---
# <a name="information-for-non-endorsed-distributions"></a>Onaylı olmayan dağıtımlar için bilgi
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure platformu SLA 'Sı, yalnızca bir [onaylama dağılımından](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) biri kullanıldığında Linux işletim sistemini çalıştıran sanal makinelere uygulanır. Bu onaylı dağıtımlar için, önceden yapılandırılmış Linux görüntüleri Azure Marketi 'nde sunulmaktadır.

* [Azure tarafından onaylanan dağıtımlara Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Microsoft Azure Linux görüntüleri için destek](https://support.microsoft.com/kb/2941892)

Azure üzerinde çalışan tüm dağıtımların sayıda önkoşulları vardır. Her dağıtım farklı olduğu için bu makale kapsamlı olamaz. Aşağıdaki tüm kriterleri karşılasanız bile, Linux sisteminizin düzgün şekilde çalışması için önemli ölçüde ince ayar gerekebilir.

Azure tarafından onaylanan dağıtımlarla bir [Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)ile başlamasını öneririz. Aşağıdaki makalelerde, Azure 'da desteklenen çeşitli desteklenen Linux dağıtımlarını nasıl hazırlayacağınız gösterilmektedir:

* **[CentOS tabanlı dağıtımlar](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Linux 'u kaldırma](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Bu makalede, Azure 'da Linux dağıtımınızı çalıştırmaya yönelik genel rehberlik ele alınmaktadır.

## <a name="general-linux-installation-notes"></a>Genel Linux yükleme notları
* Hyper-V sanal sabit disk (VHDX) biçimi Azure 'da desteklenmiyor, yalnızca *sabıt VHD*.  Hyper-V Yöneticisi 'Ni veya [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) cmdlet 'ini kullanarak diski vhd biçimine dönüştürebilirsiniz. VirtualBox kullanıyorsanız, disk oluştururken varsayılan (dinamik olarak ayrılan) yerine **sabit boyut** ' u seçin.
* Azure yalnızca 1. nesil sanal makineleri destekler. 1\. nesil bir sanal makineyi VHDX 'ten VHD dosya biçimine dönüştürebilir ve dinamik olarak genişleyen sabit boyutlu bir diske dönüştürebilirsiniz. Bir sanal makinenin neslini değiştiremezsiniz. Daha fazla bilgi için bkz. [Hyper-V ' d a 1. nesil veya 2 sanal makine oluşturmalıyım?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* VHD için izin verilen en büyük boyut 1.023 GB 'dir.
* Linux sistemini yüklerken, çoğu yükleme için varsayılan olan mantıksal birim Yöneticisi (LVM) yerine standart bölümleri kullanmanızı öneririz. Standart bölümlerin kullanılması, özellikle de sorun giderme için bir işletim sistemi diski başka bir özdeş VM 'ye iliştirilmişse, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veri disklerinde kullanılıyor olabilir.
* UDF dosya sistemlerini bağlamak için çekirdek desteği gereklidir. Azure 'da ilk önyüklemede sağlama yapılandırması, konuğa bağlı olan UDF biçimli medya kullanılarak Linux VM 'ye geçirilir. Azure Linux Aracısı, yapılandırmasını okumak ve VM 'yi sağlamak için UDF dosya sistemini takmalıdır.
* 2\.6.37 ' den önceki Linux çekirdek sürümleri, Hyper-V üzerinde NUMA 'yı daha büyük VM boyutları ile desteklemez. Bu sorun öncelikle yukarı akış Red Hat 2.6.32 çekirdeğini kullanarak eski dağıtımları etkiler ve Red Hat Enterprise Linux (RHEL) 6,6 (Kernel-2.6.32-504) içinde düzeltilmiştir. 2\.6.37 'den eski olan özel çekirdekler çalıştıran sistemler veya 2.6.32-504 ' den daha eski RHEL tabanlı çekirdekler, grub. conf `numa=off` içindeki çekirdek komut satırında önyükleme parametresini ayarlamış olmalıdır. Daha fazla bilgi için bkz. [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Linux Aracısı, aşağıdaki adımlarda açıklandığı gibi geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir.
* Azure 'daki tüm VHD 'Lerin 1 MB 'a hizalanmış bir sanal boyutu olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken, aşağıdaki adımlarda açıklandığı gibi, ham disk boyutunun dönüştürmeden önce 1 MB 'tan fazla olduğundan emin olmanız gerekir.

### <a name="installing-kernel-modules-without-hyper-v"></a>Hyper-V içermeyen çekirdek modülleri yükleme
Azure, Hyper-V hiper yöneticisinde çalışır, bu nedenle Linux bazı çekirdek modüllerinin Azure 'da çalıştırılmasını gerektirir. Hyper-V dışında oluşturulmuş bir VM varsa, sanal makine Hyper-V ortamında çalıştığını algıladıkça, Linux yükleyicileri ilk Ramdisk (ınitrd veya initramfs) Hyper-V sürücülerini içermeyebilir. Linux görüntünüzü hazırlamak için farklı bir sanallaştırma sistemi (VirtualBox, KVM vb. gibi) kullanırken, ilk Ramdisk 'de hv_vmbus ve hv_storvsc çekirdek modüllerinin kullanılabilmesi için ınitrd 'yı yeniden oluşturmanız gerekebilir.  Bu bilinen sorun, yukarı akış Red Hat dağıtımına ve belki de diğerlerine bağlı olarak sistemler içindir.

Initrd veya initramfs görüntüsünü yeniden oluşturma mekanizması dağıtıma göre farklılık gösterebilir. Doğru yordam için dağıtım belgelerine veya desteğe başvurun.  `mkinitrd` Yardımcı programını kullanarak ınitrd 'yi yeniden oluşturmak için bir örnek aşağıda verilmiştir:

1. Var olan ınitrd görüntüsünü yedekle:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Initrd 'yi hv_vmbus ve hv_storvsc çekirdek modülleriyle yeniden oluşturun:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>VHD 'leri yeniden boyutlandırma
Azure üzerinde VHD görüntülerinin 1 MB 'a hizalanmış bir sanal boyutu olmalıdır.  Genellikle, Hyper-V kullanılarak oluşturulan VHD 'ler doğru şekilde hizalanmıştır.  VHD doğru hizalanmazsa, VHD 'nizden bir görüntü oluşturmaya çalıştığınızda aşağıdakine benzer bir hata iletisi alabilirsiniz.

* VHD http:\//mystorageaccount >. blob. Core. Windows. net/VHD/mylinuxvm. vhd, 21475270656 baytlık bir sanal boyuta sahiptir.\< Boyut bir tam sayı olmalıdır (MB cinsinden).

Bu durumda, Hyper-V Yöneticisi konsolunu veya [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx) PowerShell cmdlet 'ini kullanarak VM 'yi yeniden boyutlandırın.  Bir Windows ortamında çalıştırmıyorsanız, (gerekirse) dönüştürmek ve VHD 'yi `qemu-img` yeniden boyutlandırmak için kullanmanızı öneririz.

> [!NOTE]
> [QEMU-img sürümlerindeki bilinen bir hata](https://bugs.launchpad.net/qemu/+bug/1490611) vardır > = 2.2.1, hatalı BIÇIMLI bir VHD ile sonuçlanır. Bu sorun QEMU 2,6 ' de düzeltildi. `qemu-img` 2.2.0 veya daha düşük ya da 2,6 ya da daha yüksek bir sürümü kullanmanızı öneririz.
> 

1. `qemu-img` Veya`vbox-manage` gibi araçları kullanarak VHD 'nin doğrudan yeniden boyutlandırılması, önyüklenemeyen bir VHD ile sonuçlanabilir.  Önce VHD 'YI bir ham disk görüntüsüne dönüştürmeniz önerilir.  VM görüntüsü ham disk görüntüsü olarak oluşturulduysa (KVM gibi bazı hiper Yöneticiler için varsayılan değer), bu adımı atlayabilirsiniz.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Sanal boyutun 1 MB 'a hizalanması için, disk görüntüsünün gerekli boyutunu hesaplayın.  Aşağıdaki Bash Shell betiği, disk `qemu-img info` görüntüsünün sanal boyutunu belirlemede kullanır ve ardından boyutu sonraki 1 MB olarak hesaplar.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Yukarıda ayarlandığı gibi kullanarak `$rounded_size` ham diski yeniden boyutlandırın.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Şimdi, ham diski sabit boyutlu bir VHD 'ye geri dönüştürün.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Veya QEMU sürüm 2.6 + ile `force_size` seçeneğini içerir.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Linux çekirdek gereksinimleri

Hyper-V ve Azure için Linux Integration Services (LIS) sürücüleri doğrudan yukarı akış Linux çekirdeğine katkıda bulunur. Son Linux çekirdek sürümü (3. x gibi) içeren birçok dağıtım, bu sürücülere zaten erişilebilir veya bu sürücülerin kernels ile birlikte karşılanamayan sürümlerini sağlar.  Bu sürücüler, yeni düzeltmeler ve özelliklerle yukarı akış çekirdeğinden sürekli olarak güncelleştirilir. bu nedenle, bu düzeltmeler ve güncelleştirmeler içeren bir [onaylı dağıtım](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) çalıştırmak tavsiye ederiz.

6,0 sürümünü 6,3 ' de bir Red Hat Enterprise Linux değişken çalıştırıyorsanız, [Hyper-V için en son LIS sürücülerini](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409)yüklemeniz gerekir. RHEL 6.4 + (ve türetmeleri) ile başlayarak, LIS sürücüleri zaten çekirdeğe dahil edilmiştir ve ek yükleme paketlerine gerek yoktur.

Özel bir çekirdek gerekiyorsa, yeni bir çekirdek sürümü (örneğin, 3.8 +) önerilir. Kendi çekirdeğini koruyacak dağıtımlar veya satıcılar için, LIS sürücülerinin yukarı akış çekirdeğinden özel çekirdeğe düzenli olarak geri bağlantı noktası oluşturmanız gerekir.  Daha önce görece yeni bir çekirdek sürümü çalıştırıyor olsanız bile, LIS sürücülerinde yukarı akış düzeltmelerinin izlenmesini ve bunları gerektiği şekilde geri almanızı öneririz. LIS sürücü kaynak dosyalarının konumları, Linux çekirdek kaynak ağacındaki [bakım](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) dosyasında belirtilmiştir:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Aşağıdaki düzeltme eklerinin çekirdeğe dahil olması gerekir. Bu liste tüm dağıtımlar için tamamlanamıyor.

* [ata_piix: diskleri Hyper-V sürücülerine varsayılan olarak erteleyin](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc SıFıRLAMA yolundaki iletim içi paketlerin hesabı](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: WRITE_SAME kullanımını önleyin](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc RAID ve sanal konak bağdaştırıcı sürücüleri için yazma AYNıSıNı devre dışı bırak](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc NULL işaretçi başvuru onarımı](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: halka arabelleği sorunları g/ç dondurmasına neden olabilir](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: __scsi_remove_device 'ın çift yürütmeye karşı koruma](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux Aracısı
[Azure Linux Aracısı](../extensions/agent-linux.md) `waagent` , Azure 'da bir Linux sanal makinesi sağlar. [Linux Aracısı GitHub](https://github.com/Azure/WALinuxAgent)deposunda en son sürümü alabilir, sorunları değiştirebilir veya çekme istekleri gönderebilirsiniz.

* Linux Aracısı Apache 2,0 lisansı altında serbest bırakılır. Birçok dağıtım aracı için zaten RPM veya Deb paketleri sağlıyor ve bu paketler kolayca yüklenip güncelleştirilemeyebilir.
* Azure Linux Aracısı Python v 2.6 + gerektirir.
* Aracı Ayrıca Python-pyasn1 modülünü de gerektirir. Çoğu dağıtım bu modülü yüklenecek ayrı bir paket olarak sağlar.
* Bazı durumlarda, Azure Linux Aracısı NetworkManager ile uyumlu olmayabilir. Dağıtımlar tarafından sunulan RPM/Deb paketlerinin birçoğu, NetworkManager 'ı waagent paketine bir çakışma olarak yapılandırır. Bu durumlarda, Linux Aracısı paketini yüklediğinizde NetworkManager kaldırılır.
* Azure Linux aracısının [En düşük desteklenen sürümde](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)olması gerekir.

## <a name="general-linux-system-requirements"></a>Genel Linux sistem gereksinimleri

1. Tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesi için, GRUB veya GRUB2 ' deki çekirdek önyükleme satırını aşağıdaki parametreleri içerecek şekilde değiştirin. Bu iletiler, her türlü sorunu gidermek için Azure desteğine yardımcı olabilir.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Ayrıca, varsa aşağıdaki parametreleri *kaldırmanızı* öneririz.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafik ve sessiz önyükleme, tüm günlüklerin seri bağlantı noktasına gönderilmesini istiyoruz bir bulut ortamında yararlı değildir. Bu `crashkernel` seçenek gerekirse yapılandırılmış olabilir, ancak bu parametrenin VM 'deki kullanılabilir bellek miktarını en az 128 MB ile azalttığını ve bu parametrenin daha küçük VM boyutları için sorunlu olabileceğini unutmayın.

1. Azure Linux aracısını yükler.
  
    Azure Linux Aracısı, Azure 'da bir Linux görüntüsü sağlamak için gereklidir.  Birçok dağıtım aracıyı RPM veya Deb paketi olarak sağlar (paket genellikle Walınuxagent veya walınuxagent olarak adlandırılır).  Aracı, [Linux Aracısı kılavuzundaki](../extensions/agent-linux.md)adımları izleyerek el ile de yüklenebilir.

1. SSH sunucusunun yüklü olduğundan ve önyükleme sırasında başlatılacak şekilde yapılandırıldığından emin olun.  Bu yapılandırma genellikle varsayılandır.

1. İşletim sistemi diskinde takas alanı oluşturmayın.
  
    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak disk *geçici* bir DISKTIR ve VM 'nin sağlaması tamamlandığında boşaltılır. Azure Linux aracısını yükledikten sonra (yukarıdaki 2. adım),/etc/waagent.exe içindeki aşağıdaki parametreleri gerektiği şekilde değiştirin.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Sanal makineyi sağlamayı kaldırmak için aşağıdaki komutları çalıştırın.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > VirtualBox üzerinde çalıştırdıktan sonra `waagent -force -deprovision` `[Errno 5] Input/output error`aşağıdaki hatayla karşılaşabilirsiniz. Bu hata iletisi kritik değildir ve yoksayılabilir.

* Sanal makineyi kapatın ve VHD 'yi Azure 'a yükleyin.

