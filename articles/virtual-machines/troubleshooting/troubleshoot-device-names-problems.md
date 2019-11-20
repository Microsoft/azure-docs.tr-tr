---
title: Azure 'da Linux VM cihaz adı değişikliklerinin sorunlarını giderme | Microsoft Docs
description: Linux VM cihaz adlarının neden değişmesinin ve sorunun nasıl çözüleceğini açıklar.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 7d8a7e7e88837214042fb8f1c109c0b93bfe771b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058213"
---
# <a name="troubleshoot-linux-vm-device-name-changes"></a>Linux VM cihaz adı değişikliklerinde sorun giderme

Bu makalede, bir Linux VM 'yi yeniden başlattıktan veya veri disklerini yeniden iliştirdikten sonra cihaz adlarının neden değiştirileceği açıklanmaktadır. Makale Ayrıca bu soruna yönelik çözümler sağlar.

## <a name="symptoms"></a>Belirtiler
Microsoft Azure ' de Linux VM 'Leri çalıştırırken aşağıdaki sorunlarla karşılaşabilirsiniz:

- Yeniden başlatma işleminden sonra VM önyükleme başarısız olur.
- Veri diskleri ayrıldıktan ve yeniden iliştirilmesi halinde disk cihazı adları değişir.
- Cihaz adı değiştiği için cihaz adı kullanılarak diske başvuran bir uygulama veya betik başarısız olur.

## <a name="cause"></a>Nedeni

Linux 'taki cihaz yollarının yeniden başlatmalar arasında tutarlı olması garanti edilmez. Cihaz adları, büyük numaralardan (harfler) ve küçük numaralardan oluşur. Linux depolama cihazı sürücüsü yeni bir cihaz algıladığında, sürücü kullanılabilir aralıktan cihaza büyük ve küçük sayılar atar. Bir cihaz kaldırıldığında, cihaz numaraları yeniden kullanım için serbest bırakılır.

Bu sorun, Linux 'ta cihaz taramanın zaman uyumsuz olarak gerçekleşmesi için SCSI alt sistemi tarafından zamanlandığından oluşur. Sonuç olarak, cihaz yolu adı yeniden başlatmalar arasında farklılık gösterebilir.

## <a name="solution"></a>Çözüm

Bu sorunu çözmek için kalıcı adlandırma kullanın. Kalıcı adlandırma kullanmanın dört yolu vardır: dosya sistemi etiketi, UUID, KIMLIĞE göre veya yola göre. Azure Linux VM 'Leri için dosya sistemi etiketi veya UUID kullanmanızı öneririz.

Çoğu dağıtım, `fstab` **nofail** veya **nobootwaıt** parametrelerini sağlar. Bu parametreler, disk başlangıç sırasında takılamazsa sistemin önyüklemesine olanak tanır. Bu parametreler hakkında daha fazla bilgi için dağıtım belgelerinize bakın. Bir veri diski eklediğinizde bir UUID kullanmak üzere Linux VM yapılandırma hakkında bilgi için, bkz. [Yeni diski bağlamak Için LINUX VM 'ye bağlanma](../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk).

Azure Linux Aracısı bir VM 'ye yüklendiğinde, aracı/dev/disk/Azure yolu altında bir sembolik bağlantı kümesi oluşturmak için Uıdev kurallarını kullanır. Uygulamalar ve betikler, VM 'ye bağlı diskleri tanımlamak için udev kurallarını disk türü ve disk LUN 'Ları ile birlikte kullanır.

Fstab ' yi zaten sanal makinenizin önyüklemesine ve sanal makinenize SSH ile ilgili bir şekilde düzenlediyseniz, [tek kullanıcı moduna](./serial-console-grub-single-user-mode.md) girmek ve fstab 'nizi değiştirmek Için [VM seri konsolunu](./serial-console-linux.md) kullanabilirsiniz.

### <a name="identify-disk-luns"></a>Disk LUN 'larını tanımla

Uygulamalar, eklenen tüm diskleri bulmak ve sembolik bağlantılar oluşturmak için LUN 'Ları kullanır. Azure Linux Aracısı, bir LUN 'dan cihazlara sembolik bağlantılar oluşturan udev kurallarını içerir:

    $ tree /dev/disk/azure

    /dev/disk/azure
    ├── resource -> ../../sdb
    ├── resource-part1 -> ../../sdb1
    ├── root -> ../../sda
    ├── root-part1 -> ../../sda1
    └── scsi1
        ├── lun0 -> ../../../sdc
        ├── lun0-part1 -> ../../../sdc1
        ├── lun1 -> ../../../sdd
        ├── lun1-part1 -> ../../../sdd1
        ├── lun1-part2 -> ../../../sdd2
        └── lun1-part3 -> ../../../sdd3

Linux konuk hesabındaki LUN bilgileri, veya benzer bir araç kullanılarak `lsscsi` alınır:

      $ sudo lsscsi

      [1:0:0:0] cd/dvd Msft Virtual CD/ROM 1.0 /dev/sr0

      [2:0:0:0] disk Msft Virtual Disk 1.0 /dev/sda

      [3:0:1:0] disk Msft Virtual Disk 1.0 /dev/sdb

      [5:0:0:0] disk Msft Virtual Disk 1.0 /dev/sdc

      [5:0:0:1] disk Msft Virtual Disk 1.0 /dev/sdd

Konuk LUN bilgileri, Azure depolama 'da bölüm verilerini içeren VHD 'YI bulmak için Azure abonelik meta verileri ile birlikte kullanılır. Örneğin, `az` CLI 'yi kullanabilirsiniz:

    $ az vm show --resource-group testVM --name testVM | jq -r .storageProfile.dataDisks
    [
    {
    "caching": "None",
      "createOption": "empty",
    "diskSizeGb": 1023,
      "image": null,
    "lun": 0,
    "managedDisk": null,
    "name": "testVM-20170619-114353",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-114353.vhd"
    }
    },
    {
    "caching": "None",
    "createOption": "empty",
    "diskSizeGb": 512,
    "image": null,
    "lun": 1,
    "managedDisk": null,
    "name": "testVM-20170619-121516",
    "vhd": {
      "uri": "https://testVM.blob.core.windows.net/vhd/testVM-20170619-121516.vhd"
      }
      }
    ]

### <a name="discover-filesystem-uuids-by-using-blkid"></a>Blkıd kullanarak FileSystem UUID 'ler bulma

Uygulamalar ve betikler,/dev yolunda `blkid`sembolik bağlantılar oluşturmak için, veya benzer bilgi kaynaklarının çıktısını okur. Çıkış, sanal makineye ve ilişkili cihaz dosyasına bağlı tüm disklerin UUID 'ler gösterir:

    $ sudo blkid -s UUID

    /dev/sr0: UUID="120B021372645f72"
    /dev/sda1: UUID="52c6959b-79b0-4bdd-8ed6-71e0ba782fb4"
    /dev/sdb1: UUID="176250df-9c7c-436f-94e4-d13f9bdea744"
    /dev/sdc1: UUID="b0048738-4ecc-4837-9793-49ce296d2692"

Azure Linux Aracısı udev kuralları/dev/disk/Azure yolu altında bir sembolik bağlantılar kümesi oluşturur:

    $ ls -l /dev/disk/azure

    total 0
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 resource -> ../../sdb
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 resource-part1 -> ../../sdb1
    lrwxrwxrwx 1 root root  9 Jun  2 23:17 root -> ../../sda
    lrwxrwxrwx 1 root root 10 Jun  2 23:17 root-part1 -> ../../sda1

Uygulamalar, önyükleme disk cihazını ve kaynak (kısa ömürlü) diski tanımlamak için bağlantıları kullanır. Azure 'da uygulamalar, bu bölümleri bulmak için/dev/disk/Azure/root-part1 veya/dev/disk/Azure-Resource-part1 yollarında görünmelidir.

`blkid` Listedeki tüm ek bölümler bir veri diskinde bulunur. Uygulamalar bu bölümlerin UUID 'sini korur ve çalışma zamanında cihaz adını bulacak bir yol kullanır:

    $ ls -l /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692

    lrwxrwxrwx 1 root root 10 Jun 19 15:57 /dev/disk/by-uuid/b0048738-4ecc-4837-9793-49ce296d2692 -> ../../sdc1


### <a name="get-the-latest-azure-storage-rules"></a>En son Azure depolama kurallarını al

En son Azure depolama kurallarını almak için aşağıdaki komutları çalıştırın:

    # sudo curl -o /etc/udev/rules.d/66-azure-storage.rules https://raw.githubusercontent.com/Azure/WALinuxAgent/master/config/66-azure-storage.rules
    # sudo udevadm trigger --subsystem-match=block

## <a name="see-also"></a>Ayrıca bkz.

Daha fazla bilgi için aşağıdaki makalelere bakın:

- [Ubuntu UUID kullanma](https://help.ubuntu.com/community/UsingUUID)
- [Red Hat: Kalıcı adlandırma](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Storage_Administration_Guide/persistent_naming.html)
- ['Un UUID 'ler ne yapabilir?](https://www.linux.com/news/what-uuids-can-do-you)
- [Udev Modern bir Linux sisteminde cihaz yönetimine giriş](https://www.linux.com/news/udev-introduction-device-management-modern-linux-system)

