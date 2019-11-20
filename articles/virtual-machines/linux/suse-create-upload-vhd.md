---
title: Azure 'da SUSE Linux VHD oluşturma ve karşıya yükleme
description: SUSE Linux işletim sistemi içeren bir Azure sanal sabit diski (VHD) oluşturmayı ve yüklemeyi öğrenin.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: d3241229fcf3ef99f71185c452ae615ec2cfc889
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70091217"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Azure için SLES veya openSUSE sanal makinesi hazırlama
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Önkoşullar
Bu makalede, bir sanal sabit diske zaten SUSE veya openSUSE Linux işletim sistemi yüklediğinizi varsaymış olursunuz. . Vhd dosyaları, örneğin Hyper-V gibi bir sanallaştırma çözümü oluşturmak için birden çok araç vardır. Yönergeler için bkz. [Hyper-V rolünü yükleyip sanal makineyi yapılandırma](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES/openSUSE yükleme notları
* Lütfen Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* VHDX biçimi Azure 'da desteklenmiyor, yalnızca **sabıt VHD**.  Hyper-V Yöneticisi 'Ni veya Convert-VHD cmdlet 'ini kullanarak diski VHD biçimine dönüştürebilirsiniz.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) veya [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , veri disklerinde kullanılabilir.
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir.  Bunun hakkında daha fazla bilgiyi aşağıdaki adımlarda bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>SUSE Studio 'Yu kullanma
[SUSE Studio](http://www.susestudio.com) , Azure ve Hyper-V IÇIN SLES ve openSUSE görüntülerinizi kolayca oluşturabilir ve yönetebilir. Bu, kendi SLES ve openSUSE görüntülerinizi özelleştirmek için önerilen yaklaşımdır.

Kendi VHD 'nizi oluşturmaya alternatif olarak, SUSE 'ler, [vmdepot 'u keşfedin](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf)adresindeki SLES için KCG (kendi aboneliğini getir) görüntülerini da yayımlar.

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>SUSE Linux Enterprise Server 11 SP4 'Ü hazırlayın
1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.
2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.
3. SUSE Linux Enterprise sisteminizi, güncelleştirmeleri indirmesine ve paketleri yüklemeye izin verecek şekilde kaydedin.
4. En son düzeltme ekleriyle sistemi güncelleştirin:
   
        # sudo zypper update
5. SLES deposundan Azure Linux aracısını yükler:
   
        # sudo zypper install python-azure-agent
6. Chkconfig dosyasında waagent 'ın "açık" olarak ayarlanmış olup olmadığını denetleyin ve değilse, otomatik başlatma için etkinleştirin:
   
        # sudo chkconfig waagent on
7. Waagent hizmetinin çalışıp çalışmadığını denetleyin ve yoksa başlatın: 
   
        # sudo service waagent start
8. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/boot/grub/menu.lst" öğesini açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir.
9. /Boot/grub/menu.lst ve/etc/fstab 'nin her ikisi de disk KIMLIĞI (kimliğe göre) yerine UUID (UUID) kullanarak diske başvurulacağını doğrulayın. 
   
    Disk UUID 'sini al
   
        # ls /dev/disk/by-uuid/
   
    /Dev/disk/by-id/kullanılıyorsa, hem/boot/grub/menu.lst hem de/etc/fstab öğesini uygun-UUID değeriyle güncelleştirin
   
    Değişiklikten önce
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Değişiklikten sonra
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Ethernet arabirimleri için statik kurallar oluşturmaktan kaçınmak için uıdev kurallarını değiştirin. Bu kurallar Microsoft Azure veya Hyper-V ' d a bir sanal makine kopyalanırken sorunlara neden olabilir:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. "/Etc/sysconfig/Network/DHCP" dosyasını düzenlemeniz ve `DHCLIENT_SET_HOSTNAME` parametreyi aşağıdaki şekilde değiştirmeniz önerilir:
    
     DHCLIENT_SET_HOSTNAME="no"
12. "/Etc/sudoers" içinde, varsa, aşağıdaki satırları açıklama veya kaldırma:
    
     Varsayılanlar targetpw # hedef kullanıcının parolasını ister, yani tümünü root = (tümü) # uyarı! Bunu yalnızca ' varsayılanlar targetpw ' ile birlikte kullanın!
13. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
14. İşletim sistemi diskinde takas alanı oluşturmayın.
    
    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın),/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:
    
     ResourceDisk. Format = y ResourceDisk. FileSystem = ext4 ResourceDisk. Bağlamanoktası =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # NOTE: bunu, gereken her türlü şekilde ayarlayın.
15. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

---
## <a name="prepare-opensuse-131"></a>OpenSUSE 13.1 + hazırlama
1. Hyper-V Yöneticisi 'nin orta bölmesinde, sanal makineyi seçin.
2. Sanal makine penceresini açmak için **Bağlan** ' a tıklayın.
3. Kabukta '`zypper lr`' komutunu çalıştırın. Bu komut aşağıdakine benzer bir çıktı döndürürse, depolar beklenen şekilde yapılandırılır; hiçbir ayarlama gerekmez (sürüm numaralarının değişebileceğini unutmayın):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Komut "tanımlı depo yok..." öğesini döndürürse daha sonra bu depoyu eklemek için aşağıdaki komutları kullanın:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Daha sonra '`zypper lr`' komutunu çalıştırarak depoların eklendiğini doğrulayabilirsiniz. İlgili güncelleştirme depolarından birinin etkin olmaması durumunda aşağıdaki komutla etkinleştirin:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Çekirdeği kullanılabilir en son sürüme güncelleştirin:
   
        # sudo zypper up kernel-default
   
    Ya da sistemi en son düzeltme ekleriyle güncelleştirmek için:
   
        # sudo zypper update
5. Azure Linux aracısını yükler.
   
        # sudo zypper install WALinuxAgent
6. Grub yapılandırmanızda çekirdek önyükleme satırını, Azure için ek çekirdek parametreleri içerecek şekilde değiştirin. Bunu yapmak için, bir metin düzenleyicisinde "/boot/grub/menu.lst" öğesini açın ve varsayılan çekirdeğin aşağıdaki parametreleri içerdiğinden emin olun:
   
     Console = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Bu, tüm konsol iletilerinin ilk seri bağlantı noktasına gönderilmesini sağlar ve bu da hata ayıklama sorunlarını gidermek için Azure desteğine yardımcı olabilir. Ayrıca, varsa çekirdek önyükleme satırından aşağıdaki parametreleri kaldırın:
   
     libata. atapi_enabled = 0 Reserve = 0x1f0, 0x8
7. "/Etc/sysconfig/Network/DHCP" dosyasını düzenlemeniz ve `DHCLIENT_SET_HOSTNAME` parametreyi aşağıdaki şekilde değiştirmeniz önerilir:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Önemli:** "/Etc/sudoers" içinde, varsa, aşağıdaki satırları açıklama veya kaldırma:
   
     Varsayılanlar targetpw # hedef kullanıcının parolasını ister, yani tümünü root = (tümü) # uyarı! Bunu yalnızca ' varsayılanlar targetpw ' ile birlikte kullanın!
9. SSH sunucusunun, önyükleme zamanında başlayacak şekilde yüklendiğinden ve yapılandırıldığından emin olun.  Bu genellikle varsayılandır.
10. İşletim sistemi diskinde takas alanı oluşturmayın.
    
    Azure Linux Aracısı, Azure 'da sağlamaktan sonra sanal makineye bağlı yerel kaynak diskini kullanarak takas alanını otomatik olarak yapılandırabilir. Yerel kaynak diskinin *geçici* bir disk olduğunu ve VM 'nin sağlaması geri edildiğinde boşaltılıp boşaltıyacağını unutmayın. Azure Linux aracısını yükledikten sonra (önceki adıma bakın),/etc/waagent.exe için aşağıdaki parametreleri uygun şekilde değiştirin:
    
     ResourceDisk. Format = y ResourceDisk. FileSystem = ext4 ResourceDisk. Bağlamanoktası =/mnt/Resource ResourceDisk. EnableSwap = y ResourceDisk. SwapSizeMB = 2048 # # NOTE: bunu, gereken her türlü şekilde ayarlayın.
11. Sanal makinenin sağlamasını kaldırmak ve Azure 'da sağlamak üzere hazırlamak için aşağıdaki komutları çalıştırın:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Azure Linux aracısının başlangıçta çalıştığından emin olun:
    
        # sudo systemctl enable waagent.service
13. Hyper-V Yöneticisi 'nde **eylem-> kapat** ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için SUSE Linux sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).
