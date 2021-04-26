---
title: Azure CLı kullanarak Linux sanal makinesine veri diski ekleme
description: Azure CLı ile Linux VM 'nize kalıcı bir veri diski eklemeyi öğrenin
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: adf6198cf12011c77fcf3f93d4b595ea433ddefd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104580394"
---
# <a name="add-a-disk-to-a-linux-vm"></a>Linux VM'ye disk ekleme

Bu makalede, VM 'niz bakım veya yeniden boyutlandırma nedeniyle yeniden sağlansa bile verilerinizi koruyabilmeniz için sanal makinenize kalıcı bir disk nasıl iliştirilebileceğiniz gösterilmektedir.


## <a name="attach-a-new-disk-to-a-vm"></a>VM 'ye yeni bir disk iliştirme

VM 'nize yeni, boş bir veri diski eklemek istiyorsanız, parametresiyle [az VM disk Attach](/cli/azure/vm/disk) komutunu kullanın `--new` . VM 'niz bir kullanılabilirlik Bölgeindeyse, disk VM ile aynı bölgede otomatik olarak oluşturulur. Daha fazla bilgi için bkz. [kullanılabilirlik alanları genel bakış](../../availability-zones/az-overview.md). Aşağıdaki örnek, 50 GB boyutundaki *mydatadisk* adlı bir disk oluşturur:

```azurecli
az vm disk attach \
   -g myResourceGroup \
   --vm-name myVM \
   --name myDataDisk \
   --new \
   --size-gb 50
```

## <a name="attach-an-existing-disk"></a>Var olan bir diski ekleme

Var olan bir diski eklemek için disk KIMLIĞINI bulup KIMLIĞI [az VM disk Attach](/cli/azure/vm/disk) komutuna geçirin. Aşağıdaki örnek, *Myresourcegroup* Içinde *mydatadisk* adlı bir disk Için sorgular ve *myvm* adlı VM 'ye iliştirir:

```azurecli
diskId=$(az disk show -g myResourceGroup -n myDataDisk --query 'id' -o tsv)

az vm disk attach -g myResourceGroup --vm-name myVM --name $diskId
```

## <a name="format-and-mount-the-disk"></a>Diski biçimlendirme ve bağlama

Linux sanal makinenizin kullanabilmesi için yeni diskinizi bölümlemek, biçimlendirmek ve bağlamak üzere sanal makinenize SSH. Daha fazla bilgi için bkz. [Azure’da Linux ile SSH kullanma](mac-create-ssh-keys.md). Aşağıdaki örnek, *10.123.123.25* Kullanıcı adı Ile genel IP adresi olan bir VM 'ye bağlanır *azureuser*:

```bash
ssh azureuser@10.123.123.25
```

### <a name="find-the-disk"></a>Diski bulma

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
sdc     3:0:0:0      50G
```

`sdc`50G olduğundan, istediğimiz disk burada. Tek başına boyutu temel alan diskte emin değilseniz, portalda VM sayfasına gidebilir, **diskler**' i seçebilir ve **veri diskleri** altındaki disk için LUN numarasını kontrol edebilirsiniz. 


### <a name="format-the-disk"></a>Diski biçimlendirme

Diski ile biçimlendirin `parted` ; disk boyutu 2 tebibayt (Tib) veya daha büyükse, GPT bölümlendirme kullanmanız gerekir, bu durumda Ise MBR veya GPT bölümlemesini kullanabilirsiniz. 

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

Şimdi, kullanarak dosya sistemini bağlamak için bir dizin oluşturun `mkdir` . Aşağıdaki örnek şurada bir dizin oluşturur `/datadrive` :

```bash
sudo mkdir /datadrive
```

`mount`Daha sonra dosya sistemini bağlamak için kullanın. Aşağıdaki örnek, `/dev/sdc1` bölümü `/datadrive` bağlama noktasına bağlar:

```bash
sudo mount /dev/sdc1 /datadrive
```

### <a name="persist-the-mount"></a>Bağlama devam ettir

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

Bu örnekte, nano düzenleyicisini kullanıyoruz, bu yüzden dosyayı düzenleme işiniz bittiğinde `Ctrl+O` dosyayı yazmak ve `Ctrl+X` düzenleyiciden çıkmak için kullanın.

> [!NOTE]
> Daha sonra fstab düzenlemeden bir veri diskinin kaldırılması, sanal makinenin önyüklenememesine neden olabilir. Çoğu dağıtım, *nofail* ve/veya *nobootwaıt* fstab seçeneklerini sağlar. Bu seçenekler, disk önyükleme zamanında takılamazsa bile sistemin önyüklenmesine izin verir. Bu parametrelerle ilgili daha fazla bilgi için, dağıtım belgelerine bakın.
>
> *NOFAIL* seçeneği, dosya sistemi bozuk olsa da veya önyükleme zamanında disk mevcut olmasa bile VM 'nin başlamasını sağlar. Bu seçenek olmadan, [FSTAB hataları nedeniyle LINUX sanal MAKINESINE SSH yapılamıyor](/archive/blogs/linuxonazure/cannot-ssh-to-linux-vm-after-adding-data-disk-to-etcfstab-and-rebooting) bölümünde açıklandığı gibi davranışla karşılaşabilirsiniz
>
> Azure VM seri konsolu, fstab değiştirme bir önyükleme hatası ile sonuçlanmışsa sanal makinenize konsol erişimi için kullanılabilir. [Seri konsol belgelerinde](/troubleshoot/azure/virtual-machines/serial-console-linux)daha fazla ayrıntı bulunabilir.

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

## <a name="troubleshooting"></a>Sorun giderme

[!INCLUDE [virtual-machines-linux-lunzero](../../../includes/virtual-machines-linux-lunzero.md)]

## <a name="next-steps"></a>Sonraki adımlar

* Linux sanal makinenizin doğru bir şekilde yapılandırıldığından emin olmak için [Linux makinenizin performans önerilerinizi iyileştirin](/previous-versions/azure/virtual-machines/linux/optimization) ' i gözden geçirin.
* Ek diskler ekleyerek depolama kapasitenizi genişletin ve ek performans için [RAID 'i yapılandırın](/previous-versions/azure/virtual-machines/linux/configure-raid) .