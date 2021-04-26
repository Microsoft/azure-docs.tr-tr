---
title: Linux VM 'ye veri diski iliştirme
description: Bir Linux sanal makinesine yeni veya mevcut veri diski eklemek için portalını kullanın.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/28/2020
ms.author: cynthn
ms.subservice: disks
ms.collection: linux
ms.openlocfilehash: 0fe584ea8559c285ee7e25caca958ff56aa9454d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601842"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Bir Linux VM 'sine veri diski eklemek için portalı kullanma 
Bu makalede, Azure portal aracılığıyla bir Linux sanal makinesine hem yeni hem de mevcut diskleri nasıl ekleyebileceğiniz gösterilmektedir. Ayrıca [, Azure Portal bir WINDOWS sanal makinesine veri diski ekleyebilirsiniz](../windows/attach-managed-disk-portal.md). 

VM 'nize disk iliştirmadan önce şu ipuçlarını gözden geçirin:

* Sanal makinenin boyutu, ekleyebileceğiniz veri disklerinin sayısını denetler. Ayrıntılar için bkz. [sanal makineler Için boyutlar](../sizes.md).
* Sanal makinelere eklenen diskler aslında. vhd dosyaları Azure 'da depolanır. Ayrıntılar için bkz. [yönetilen disklere giriş](../managed-disks-overview.md).
* Diski iliştirdikten sonra, [Yeni diski bağlamak Için LINUX VM 'ye bağlanmanız](#connect-to-the-linux-vm-to-mount-the-new-disk)gerekir.


## <a name="find-the-virtual-machine"></a>Sanal makineyi bul
1. VM 'yi bulmak için [Azure Portal](https://portal.azure.com/) gidin. **Sanal makineleri** arayın ve seçin.
2. Listeden VM 'yi seçin.
3. **Sanal makineler** sayfasında, **Ayarlar** altında **diskler**' i seçin.


## <a name="attach-a-new-disk"></a>Yeni Disk Ekle

1. **Diskler** bölmesinde, **veri diskleri** altında **Yeni bir disk oluştur ve Ekle**' yi seçin.

1. Yönetilen diskiniz için bir ad girin. Varsayılan ayarları gözden geçirin ve **depolama türü**, **Boyut (gib)**, **şifreleme** ve **ana bilgisayar önbelleğe almayı** gerektiği şekilde güncelleştirin.
   
   :::image type="content" source="./media/attach-disk-portal/create-new-md.png" alt-text="Disk ayarlarını gözden geçirin.":::


1. İşiniz bittiğinde, yönetilen diski oluşturmak ve VM yapılandırmasını güncelleştirmek için sayfanın en üstündeki **Kaydet** ' i seçin.


## <a name="attach-an-existing-disk"></a>Var olan bir diski ekleme
1. **Diskler** bölmesinde, **veri diskleri** altında, **var olan diskleri Ekle**' yi seçin.
1. **Disk adı** ' nın açılan menüsüne tıklayın ve kullanılabilir yönetilen diskler listesinden bir disk seçin. 

1. Mevcut yönetilen diski eklemek ve VM yapılandırmasını güncelleştirmek için **Kaydet** ' e tıklayın:
   

## <a name="connect-to-the-linux-vm-to-mount-the-new-disk"></a>Yeni diski bağlamak için Linux VM 'ye bağlanma
Linux sanal makinenizin kullanabilmesi için yeni diskinizi bölümlemek, biçimlendirmek ve bağlamak üzere sanal makinenize SSH. Daha fazla bilgi için bkz. [Azure’da Linux ile SSH kullanma](mac-create-ssh-keys.md). Aşağıdaki örnek, *10.123.123.25* Kullanıcı adı Ile genel IP adresi olan bir VM 'ye bağlanır *azureuser*: 

```bash
ssh azureuser@10.123.123.25
```

## <a name="find-the-disk"></a>Diski bulma

Sanal makinenize bağlandıktan sonra diski bulmanız gerekir. Bu örnekte, `lsblk` diskleri listelemek için kullandık. 

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Çıktı aşağıdaki örneğe benzer:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
```

Bu örnekte, eklediğim disk `sdc` . Bu bir LUN 0 ve 1 GB 'dir.

Daha karmaşık bir örnek için, portalda birden çok veri diski şöyle görünür:

:::image type="content" source="./media/attach-disk-portal/find-disk.png" alt-text="Portalda gösterilen birden çok diskin ekran görüntüsü.":::

Görüntüde 3 veri diski olduğunu görebilirsiniz: LUN 0 üzerinde 4 GB, LUN 1 ' de 16GB ve LUN 2 ' de 32G.

Şöyle görünebileceğini aşağıda bulabilirsiniz `lsblk` :

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
sdd     3:0:0:1      16G
sde     3:0:0:2      32G
```

Çıktısından, LUN `lsblk` 0 ' dakı 4 GB diskin olduğunu `sdc` , LUN 1 ' DEKI 16GB diskinin olduğunu `sdd` ve LUN 2 ' deki 32 g diskinin olduğunu görebilirsiniz `sde` .

### <a name="partition-a-new-disk"></a>Yeni bir disk bölümleme

Veri içeren mevcut bir disk kullanıyorsanız, diski bağlamak için atlayın. Yeni bir disk iliştirmeye çalışıyorsanız, diski bölümlemek gerekir.

`parted`Yardımcı programı bir veri diski bölümlemek ve biçimlendirmek için kullanılabilir.

> [!NOTE]
> Sizin için uygun olan en son sürümü kullanmanız önerilir `parted` .
> Disk boyutu 2 tebibayt (Tib) veya daha büyükse, GPT bölümlendirme kullanmanız gerekir. Disk boyutu 2 TiB altındaysa, MBR veya GPT bölümlemesini kullanabilirsiniz.  


Aşağıdaki örnek `parted` `/dev/sdc` , ilk veri diskinin genellikle çoğu VM 'de olacağı, üzerinde kullanır. `sdc`Diskinizin doğru seçeneği ile değiştirin. Ayrıca, [XFS](https://xfs.wiki.kernel.org/) FileSystem kullanarak biçimlendirme de yapılır.

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

[`partprobe`](https://linux.die.net/man/8/partprobe)Çekirdeğin yeni bölüm ve FileSystem 'ın farkında olduğundan emin olmak için yardımcı programını kullanın. Kullanım hatası `partprobe` , blkıd veya lslbk komutlarının yeni FileSystem IÇIN UUID 'yi hemen döndürmemesine neden olabilir.

### <a name="mount-the-disk"></a>Diski bağlama

Kullanarak dosya sistemini bağlamak için bir dizin oluşturun `mkdir` . Aşağıdaki örnek şurada bir dizin oluşturur `/datadrive` :

```bash
sudo mkdir /datadrive
```

`mount`Daha sonra dosya sistemini bağlamak için kullanın. Aşağıdaki örnek, */dev/sdc1* bölümünü `/datadrive` bağlama noktasına bağlar:

```bash
sudo mount /dev/sdc1 /datadrive
```

Sürücünün yeniden başlatma işleminden sonra otomatik olarak yeniden takıldığından emin olmak için, */etc/fstab* dosyasına eklenmesi gerekir. Ayrıca, */etc/fstab* içinde, yalnızca cihaz adı (örneğin, */dev/sdc1*) yerine, sürücüye başvurmak Için UUID 'Nin (evrensel olarak benzersiz tanımlayıcı) kullanılması önemle tavsiye edilir. Önyükleme sırasında işletim sistemi bir disk hatası algılarsa, UUID'nin kullanılması belirlenen konuma yanlış diskin bağlanmasını önler. Bundan sonra kalan veri diskleri aynı cihaz kimliklerine atanabilir. Yeni sürücünün UUID'sini bulmak için `blkid` yardımcı programını kullanın:

```bash
sudo blkid
```

Çıktı aşağıdaki örneğe benzer şekilde görünür:

```bash
/dev/sda1: LABEL="cloudimg-rootfs" UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4" PARTUUID="1a1b1c1d-11aa-1234-1a1a1a1a1a1a"
/dev/sda15: LABEL="UEFI" UUID="BCD7-96A6" TYPE="vfat" PARTUUID="1e1g1cg1h-11aa-1234-1u1u1a1a1u1u"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4" TYPE="ext4" PARTUUID="1a2b3c4d-01"
/dev/sda14: PARTUUID="2e2g2cg2h-11aa-1234-1u1u1a1a1u1u"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs" PARTLABEL="xfspart" PARTUUID="c1c2c3c4-1234-cdef-asdf3456ghjk"
```

> [!NOTE]
> **/Etc/fstab** dosyasının yanlış düzenlenmesiyle, önyüklenemeyen bir sistemle sonuçlanabilir. Emin değilseniz, bu dosyayı doğru düzenleme hakkındaki bilgiler için dağıtımın belgelerine bakın. Ayrıca,/etc/fstab dosyasının bir yedeğinin düzenlenmeden önce oluşturulması önerilir.

Sonra, */etc/fstab* dosyasını bir metin düzenleyicisinde şu şekilde açın:

```bash
sudo nano /etc/fstab
```

Bu örnekte, `/dev/sdc1` önceki adımlarda oluşturulan cihaz IÇIN UUID değerini ve ' nin bağlama noktasını kullanın `/datadrive` . Aşağıdaki satırı dosyanın sonuna ekleyin `/etc/fstab` :

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,nofail   1   2
```

Nano düzenleyiciyi kullandık, bu nedenle dosyayı düzenleme işiniz bittiğinde `Ctrl+O` dosyayı yazmak ve `Ctrl+X` düzenleyiciden çıkmak için öğesini kullanın.

> [!NOTE]
> Daha sonra fstab düzenlemeden bir veri diskinin kaldırılması, sanal makinenin önyüklenememesine neden olabilir. Çoğu dağıtım, *nofail* ve/veya *nobootwaıt* fstab seçeneklerini sağlar. Bu seçenekler, disk önyükleme zamanında takılamazsa bile sistemin önyüklenmesine izin verir. Bu parametrelerle ilgili daha fazla bilgi için, dağıtım belgelerine bakın.
> 
> *NOFAIL* seçeneği, dosya sistemi bozuk olsa da veya önyükleme zamanında disk mevcut olmasa bile VM 'nin başlamasını sağlar. Bu seçenek olmadan, [FSTAB hataları nedeniyle LINUX sanal MAKINESINE SSH yapılamıyor](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) bölümünde açıklandığı gibi davranışla karşılaşabilirsiniz


## <a name="verify-the-disk"></a>Diski doğrulama

Artık `lsblk` diski ve bağlama noktasını görmek için yeniden kullanabilirsiniz.

```bash
lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i "sd"
```

Çıkış şuna benzer olacaktır:

```bash
sda     0:0:0:0      30G
├─sda1             29.9G /
├─sda14               4M
└─sda15             106M /boot/efi
sdb     1:0:1:0      14G
└─sdb1               14G /mnt
sdc     3:0:0:0       4G
└─sdc1                4G /datadrive
```

`sdc`Şimdi ' nin bağlı olduğunu görebilirsiniz `/datadrive` .

### <a name="trimunmap-support-for-linux-in-azure"></a>Azure 'da Linux için KıRPMA/eşlemeyi kaldır

Bazı Linux çekirdekler, diskteki kullanılmayan blokları atmak için kesme/eşlemeyi Kaldır işlemlerini destekler. Bu özellik, Azure 'un silinen sayfaların artık geçerli olmadığını ve atılamayacağını, daha sonra da büyük dosyalar oluşturup bunları silerek paradan tasarruf etmesini bildirmek için standart depolamada yararlıdır.

Linux sanal makinenizde KıRPMA desteğini etkinleştirmenin iki yolu vardır. Her zamanki gibi, önerilen yaklaşım için dağıtıma başvurun:

* `discard` */Etc/fstab* içindeki bağlama seçeneğini kullanın, örneğin:

    ```bash
    UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   xfs   defaults,discard   1   2
    ```
* Bazı durumlarda, `discard` seçeneğinde performans olumsuz etkileri olabilir. Alternatif olarak, komut `fstrim` satırından komutu el ile çalıştırabilir veya bunları düzenli olarak çalıştırmak için crontab 'ize ekleyebilirsiniz:
  
    **Ubuntu**
  
    ```bash
    sudo apt-get install util-linux
    sudo fstrim /datadrive
    ```
  
    **RHEL/CentOS**

    ```bash
    sudo yum install util-linux
    sudo fstrim /datadrive
    ```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi edinmek ve disk sorunlarını gidermeye yardımcı olmak için bkz. [LINUX VM cihaz adı değişikliklerinde sorun giderme](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems).

Ayrıca, Azure CLı kullanarak [bir veri diski](add-disk.md) ekleyebilirsiniz.
