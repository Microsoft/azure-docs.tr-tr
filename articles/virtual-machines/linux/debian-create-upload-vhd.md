---
title: Debir Linux VHD hazırlama
description: Azure 'da VM dağıtımları için deni VHD görüntüleri oluşturmayı öğrenin.
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: 7dcb6dbc62513535c562a430f5958a62dae9d005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554522"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure için bir de, VHD 'YI hazırlama
## <a name="prerequisites"></a>Önkoşullar
Bu bölümde, [de, Web sitesinden](https://www.debian.org/distrib/) bir sanal sabit diske indirilen bir. iso dosyasından bir de, Linux işletim sistemini zaten yüklemiş olduğunuz varsayılmaktadır. . Vhd dosyaları oluşturmak için birden çok araç var; Hyper-V yalnızca bir örnektir. Hyper-V ' y i kullanma hakkında yönergeler için bkz. [Hyper-v rolünü yükleyip sanal makineyi yapılandırma](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Yükleme notları
* Ayrıca bkz. Azure için Linux hazırlama hakkında daha fazla ipucu için bkz. [Genel Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .
* Yeni VHDX biçimi Azure 'da desteklenmez. Hyper-V Yöneticisi 'Ni veya **Convert-VHD** cmdlet 'ini kullanarak diski vhd biçimine dönüştürebilirsiniz.
* Linux sistemini yüklerken, LVM yerine standart bölümler kullanmanız önerilir (genellikle çoğu yükleme için varsayılan değer). Bu, özellikle de bir işletim sistemi diskinin sorun gidermeye yönelik başka bir VM 'ye bağlanması gerekiyorsa, kopyalanmış VM 'lerle LVM adı çakışmalarını önler. Tercih edilen durumlarda [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) veya [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) , veri disklerinde kullanılabilir.
* İşletim sistemi diski üzerinde takas bölümü yapılandırmayın. Azure Linux Aracısı, geçici kaynak diskinde bir takas dosyası oluşturmak için yapılandırılabilir. Aşağıdaki adımlarda daha fazla bilgi bulabilirsiniz.
* Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürme yaparken,, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Daha fazla bilgi için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Deni VHD 'ler oluşturmak için Azure-Manage kullanın
Azure için Azure-Manage betikleri, [Credadtiv](https://www.credativ.com/)içindeki [Azure-Manage](https://github.com/credativ/azure-manage) betikleri gibi araçlar oluşturmaya yönelik araçlar mevcuttur. Bu, sıfırdan bir görüntü oluşturulmasına karşı önerilen yaklaşımdır. Örneğin, bir debir 8 VHD oluşturmak için, `azure-manage` yardımcı programı (ve bağımlılıkları) indirmek ve betiği çalıştırmak için aşağıdaki komutları çalıştırın `azure_build_image` :

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Bir deni VHD 'YI el ile hazırlama
1. Hyper-V Yöneticisi 'nde sanal makineyi seçin.
2. **Bağlan** ' a tıklayarak sanal makine için bir konsol penceresi açın.
3. İşletim sistemini bir ISO kullanarak yüklediyseniz içindeki "" ile ilgili herhangi bir satırı açıklama olarak yapın `deb cdrom` `/etc/apt/source.list` .

4. Dosyayı düzenleyin `/etc/default/grub` ve Azure için ek çekirdek parametrelerini dahil etmek üzere **GRUB_CMDLINE_LINUX** parametresini aşağıdaki şekilde değiştirin.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Grub 'yi yeniden oluşturun ve çalıştırın:

    ```console
    # sudo update-grub
    ```

6. De, 8, 9 veya 10 için Azure depoları 'nı/etc/apt/sources.exe listesine ekleyin:

    **Desek8. x "Jese"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **9. x "uzat"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```
    
    **10. x "Buster"**
    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian buster main
    deb-src http://debian-archive.trafficmanager.net/debian buster main
    deb http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security buster/updates main
    deb http://debian-archive.trafficmanager.net/debian buster-updates main
    deb-src http://debian-archive.trafficmanager.net/debian buster-updates main
    deb http://debian-archive.trafficmanager.net/debian buster-backports main
    deb-src http://debian-archive.trafficmanager.net/debian buster-backports main
    ```

7. Azure Linux aracısını yükler:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. Deler 9 + için, Azure 'da VM 'lerle kullanılmak üzere yeni de, bulut çekirdeğini kullanmanız önerilir. Bu yeni çekirdeği yüklemek için ilk olarak/etc/apt/Preferences.exe \ \ Linux.exe ' adlı bir dosyayı aşağıdaki içeriklerle oluşturun:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Ardından, yeni detem bulut çekirdeğini yüklemek için "sudo apt-get Install Linux-Image-Cloud-amd64" öğesini çalıştırın.

9. Sanal makinenin sağlamasını kaldırır ve Azure 'da sağlama için hazırlayın ve şunu çalıştırın:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Hyper-V Yöneticisi 'nde **eylem** -> Kapat ' a tıklayın. Linux VHD 'niz artık Azure 'a yüklenmeye hazırdır.

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure 'da yeni sanal makineler oluşturmak için de, sanal sabit diskinizi kullanmaya hazırsınız. . Vhd dosyasını ilk kez Azure 'a yüklüyorsanız, bkz. [özel bir diskten LINUX VM oluşturma](upload-vhd.md#option-1-upload-a-vhd).