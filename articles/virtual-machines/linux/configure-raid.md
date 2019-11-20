---
title: Linux çalıştıran bir sanal makinede yazılım RAID yapılandırma
description: Azure 'da Linux üzerinde RAID yapılandırmak için mdaddm 'yi nasıl kullanacağınızı öğrenin.
services: virtual-machines-linux
documentationcenter: na
author: rickstercdn
manager: gwallace
editor: tysonn
tag: azure-service-management,azure-resource-manager
ms.assetid: f3cb2786-bda6-4d2c-9aaf-2db80f490feb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/02/2017
ms.author: rclaus
ms.subservice: disks
ms.openlocfilehash: bc53ed3e3a7fd988464b9100df654920d5589596
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036655"
---
# <a name="configure-software-raid-on-linux"></a>Linux’ta Yazılım RAID yapılandırma
Azure 'daki Linux sanal makinelerinde yazılım RAID 'i kullanarak birden çok bağlı veri diskini tek bir RAID cihazı olarak sunmak için yaygın bir senaryodur. Genellikle bu, performansı artırmak ve yalnızca tek bir disk kullanılmasına kıyasla İyileştirilmiş işleme sağlamak için kullanılabilir.

## <a name="attaching-data-disks"></a>Veri diskleri iliştirme
RAID cihazını yapılandırmak için iki veya daha fazla boş veri diski gereklidir.  RAID cihazı oluşturmanın birincil nedeni, disk GÇ 'nizin performansını artırmaktır.  G/ç gereksinimlerinize göre disk başına en fazla 500 GÇ/PS veya disk başına en fazla 5000 GÇ/PS olan Premium depolama alanı ile standart depomızda depolanan diskleri eklemeyi tercih edebilirsiniz. Bu makale, bir Linux sanal makinesine veri diskleri sağlama ve iliştirme hakkında ayrıntılı bilgi vermez.  Azure 'da bir Linux sanal makinesine boş veri diski iliştirme hakkında ayrıntılı yönergeler için Microsoft Azure makalesine [disk iliştirme](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) bölümüne bakın.

## <a name="install-the-mdadm-utility"></a>Mdaddm yardımcı programını yükler
* **Ubuntu**
  ```bash
  sudo apt-get update
  sudo apt-get install mdadm
  ```

* **CentOS & Oracle Linux**
  ```bash
  sudo yum install mdadm
  ```

* **SLES ve openSUSE**
  ```bash  
  zypper install mdadm
  ```

## <a name="create-the-disk-partitions"></a>Disk bölümleri oluşturma
Bu örnekte,/dev/sdc'de tek bir disk bölümü oluşturacağız. Yeni disk bölümü/dev/sdc1olarak adlandırılacaktır.

1. Bölüm oluşturmaya başlamak için `fdisk` başlatın

    ```bash
    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0xa34cb70c.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
                    switch off the mode (command 'c') and change display units to
                    sectors (command 'u').
    ```

1. **Hayır**bölümü oluşturmak için istemde ' n ' tuşuna basın:

    ```bash
    Command (m for help): n
    ```

1. Ardından, bir **p**rimary bölümü oluşturmak için ' p ' tuşuna basın:

    ```bash 
    Command action
            e   extended
            p   primary partition (1-4)
    ```

1. Bölüm numarası 1 ' i seçmek için ' 1 ' tuşuna basın:

    ```bash
    Partition number (1-4): 1
    ```

1. Yeni bölümün başlangıç noktasını seçin veya bölümü sürücüdeki boş alanın başına yerleştirmek için varsayılanı kabul etmek üzere `<enter>` ' a basın:

    ```bash   
    First cylinder (1-1305, default 1):
    Using default value 1
    ```

1. Bölüm boyutunu seçin, örneğin 10 gigabayt bölüm oluşturmak için ' + 10G ' yazın. Ya da tüm sürücüyü kapsayan tek bir bölüm oluşturmak `<enter>` ' a basın:

    ```bash   
    Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
    Using default value 1305
    ```

1. Ardından, varsayılan ' 83 ' (Linux) KIMLIĞI ' FD ' kimliğiyle bölümün ID ve **t**türünü değiştirin (Linux RAID Auto):

    ```bash  
    Command (m for help): t
    Selected partition 1
    Hex code (type L to list codes): fd
    ```

1. Son olarak, bölüm tablosunu sürücüye yazın ve Fdisk programından çıkın:

    ```bash   
    Command (m for help): w
    The partition table has been altered!
    ```

## <a name="create-the-raid-array"></a>RAID dizisi oluşturma
1. Aşağıdaki örnek, üç ayrı veri diskinde bulunan (sdc1, sdd1, sde1) üç bölümden oluşan "Stripe" (RAID düzey 0).  Bu komutu çalıştırdıktan sonra **/dev/MD127** adlı yenı bir RAID cihazı oluşturulur. Ayrıca, bu veri diskleri daha önce başka bir geçersiz RAID dizisinin parçasıysa, `mdadm` komutuna `--force` parametresi eklemek için gerekli olabileceğini unutmayın:

    ```bash  
    sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
        /dev/sdc1 /dev/sdd1 /dev/sde1
    ```

1. Yeni RAID cihazında dosya sistemini oluşturma
   
    **CentOS, Oracle Linux, SLES 12, openSUSE ve Ubuntu**

    ```bash   
    sudo mkfs -t ext4 /dev/md127
    ```
   
    **SLES 11**

    ```bash
    sudo mkfs -t ext3 /dev/md127
    ```
   
    **SLES 11** -Boot.MD etkinleştirin ve mdaddm. conf dosyasını oluşturun

    ```bash
    sudo -i chkconfig --add boot.md
    sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf
    ```
   
   > [!NOTE]
   > SUSE sistemlerinde bu değişiklikler yapıldıktan sonra yeniden başlatma gerekebilir. Bu adım SLES 12 ' de gerekli *değildir* .
   > 
   

## <a name="add-the-new-file-system-to-etcfstab"></a>Yeni dosya sistemini/etc/fstab 'e ekleme
> [!IMPORTANT]
> /Etc/fstab dosyasının yanlış düzenlenmesiyle, önyüklenemeyen bir sistemle sonuçlanabilir. Emin değilseniz, bu dosyayı doğru şekilde düzenleme hakkında bilgi edinmek için dağıtımın belgelerine bakın. Ayrıca,/etc/fstab dosyasının bir yedeğinin düzenlenmeden önce oluşturulması önerilir.

1. Yeni dosya sisteminiz için istenen bağlama noktasını oluşturun, örneğin:

    ```bash
    sudo mkdir /data
    ```
1. /Etc/fstab düzenlenirken, **UUID** 'nin cihaz adı yerine dosya sistemine başvurması için kullanılması gerekir.  Yeni dosya sisteminin UUID 'sini öğrenmek için `blkid` yardımcı programını kullanın:

    ```bash   
    sudo /sbin/blkid
    ...........
    /dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"
    ```

1. /Etc/fstab 'i bir metin düzenleyicisinde açın ve yeni dosya sistemi için bir giriş ekleyin, örneğin:

    ```bash   
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2
    ```
   
    **SLES 11**' de:

    ```bash
    /dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2
    ```
   
    Ardından,/etc/fstaba kaydedin ve kapatın.

1. /Etc/fstab girişinin doğru olduğunu test edin:

    ```bash  
    sudo mount -a
    ```

    Bu komut bir hata iletisiyle sonuçlanırsa, lütfen/etc/fstab dosyasındaki söz dizimini kontrol edin.
   
    Sonra, dosya sisteminin bağlı olduğundan emin olmak için `mount` komutunu çalıştırın:

    ```bash   
    mount
    .................
    /dev/md127 on /data type ext4 (rw)
    ```

1. Seçim Failsafe önyükleme parametreleri
   
    **fstab yapılandırması**
   
    Birçok dağıtım,/etc/fstab dosyasına eklenebilen `nobootwait` veya `nofail` bağlama parametrelerini içerir. Bu parametreler, belirli bir dosya sistemini bağladığınızda ve Linux sisteminin, RAID dosya sistemini düzgün bir şekilde bağlamasa bile önyüklemeye devam etmesine izin veren hatalara izin verir. Bu parametrelerle ilgili daha fazla bilgi için, dağıtım belgelerine bakın.
   
    Örnek (Ubuntu):

    ```bash  
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2
    ```   

    **Linux önyükleme parametreleri**
   
    Yukarıdaki parametrelere ek olarak, "`bootdegraded=true`" çekirdek parametresi, RAID 'in hasarlı veya düzeyi düşürülmüş olarak algılansa bile sistemin önyüklemesine izin verebilir, örneğin, bir veri sürücüsü yanlışlıkla sanal makineden kaldırılırsa. Bu, varsayılan olarak önyüklenebilir olmayan bir sisteme neden olabilir.
   
    Çekirdek parametrelerini düzgün bir şekilde düzenlemek için lütfen dağıtım belgelerine bakın. Örneğin, çoğu dağıtımda (CentOS, Oracle Linux, SLES 11), bu parametreler "`/boot/grub/menu.lst`" dosyasına elle eklenebilir.  Ubuntu 'da, bu parametre "/etc/default/grub" üzerindeki `GRUB_CMDLINE_LINUX_DEFAULT` değişkenine eklenebilir.


## <a name="trimunmap-support"></a>KESME/eşlemeyi kaldır desteği
Bazı Linux çekirdekler, diskteki kullanılmayan blokları atmak için kesme/eşlemeyi Kaldır işlemlerini destekler. Bu işlemler, Azure 'un silinen sayfaların artık geçerli olmadığını ve yoksayılabilir olduğunu bildirmek için öncelikle standart depolamada yararlı olur. Sayfaların atılması, büyük dosyalar oluşturup bunları silerseniz maliyeti kaydedebilir.

> [!NOTE]
> Dizi için öbek boyutu varsayılandan daha az (512KB) ayarlandıysa, RAID, atma komutları yayınlamayabilir. Bunun nedeni, konaktaki ayrıntı düzeyi, ana bilgisayar üzerindeki eşlemeyi de 512 KB 'dir. Dizinin öbek boyutunu mdaddm 'nin `--chunk=` parametresi aracılığıyla değiştirdiyseniz, kesme/eşlemesini Kaldır istekleri çekirdek tarafından yoksayılabilir.

Linux sanal makinenizde KıRPMA desteğini etkinleştirmenin iki yolu vardır. Her zamanki gibi, önerilen yaklaşım için dağıtıma başvurun:

- `/etc/fstab``discard` bağlama seçeneğini kullanın, örneğin:

    ```bash
    UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,discard  0  2
    ```

- Bazı durumlarda `discard` seçeneğinde performans etkileri olabilir. Alternatif olarak, komut satırından `fstrim` komutunu el ile çalıştırabilir veya bunları düzenli olarak çalıştırmak için crontab 'ize ekleyebilirsiniz:

    **Ubuntu**

    ```bash
    # sudo apt-get install util-linux
    # sudo fstrim /data
    ```

    **RHEL/CentOS**
    ```bash
    # sudo yum install util-linux
    # sudo fstrim /data
    ```
