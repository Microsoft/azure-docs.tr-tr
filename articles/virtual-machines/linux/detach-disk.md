---
title: Linux VM 'den bir veri diskini ayırma-Azure
description: Azure CLı veya Azure portal kullanarak Azure 'daki bir sanal makineden bir veri diskini kullanımdan çıkarmayı öğrenin.
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a2cbbf2c390b81aa62b064a7cf93decbaa7457
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565997"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Bir Linux sanal makinesindeki veri diskini ayırma

Sanal makineye bağlı bir veri diskine ihtiyacınız olmadığında bunu kolayca ayırabilirsiniz. Bu, diski sanal makineden kaldırır, ancak depolama alanından kaldırmaz. Bu makalede Ubuntu LTS 16,04 dağıtımı ile çalışıyoruz. Farklı bir dağıtım kullanıyorsanız, diskin takılmasını kaldırmak için yönergeler farklı olabilir.

> [!WARNING]
> Bir diski ayırdıysanız otomatik olarak silinmez. Premium depolamaya abone olduysa, disk için depolama ücretleri uygulanmaya devam edersiniz. Daha fazla bilgi için [Premium Depolama kullanılırken fiyatlandırma ve faturalandırma](https://azure.microsoft.com/pricing/details/storage/page-blobs/)bölümüne bakın.

Disk üzerinde var olan verileri yeniden kullanmak isterseniz bu verileri aynı sanal makineye veya başka birine yeniden ekleyebilirsiniz.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Diski çıkarmak için VM 'ye bağlanın

CLı veya Portal kullanarak diski ayırabilmeniz için önce fstab dosyanızdaki disk bağlantısını çıkarmanız ve başvuruları kaldıralmanız gerekir.

VM’ye bağlanın. Bu örnekte, VM 'nin genel IP adresi Kullanıcı adı *azureuser* ile *10.0.1.4* olur: 

```bash
ssh azureuser@10.0.1.4
```

İlk olarak, ayırmak istediğiniz veri diskini bulun. Aşağıdaki örnek, SCSI disklerinde filtrelemek için dmesg kullanır:

```bash
dmesg | grep SCSI
```

Çıktı aşağıdaki örneğe benzer:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

Burada, *SDC* ayırmak istediğimiz disktir. Ayrıca, diskin UUID 'sini de almalısınız.

```bash
sudo -i blkid
```

Çıktı aşağıdaki örneğe benzer şekilde görünür:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


*/Etc/fstab* dosyasını düzenleyerek diske başvuruları kaldırın. 

> [!NOTE]
> **/Etc/fstab** dosyasının yanlış düzenlenmesiyle, önyüklenemeyen bir sistemle sonuçlanabilir. Emin değilseniz, bu dosyayı doğru düzenleme hakkındaki bilgiler için dağıtımın belgelerine bakın. Ayrıca,/etc/fstab dosyasının bir yedeğinin düzenlenmeden önce oluşturulması önerilir.

*/Etc/fstab* dosyasını bir metin düzenleyicisinde şu şekilde açın:

```bash
sudo vi /etc/fstab
```

Bu örnekte, */etc/fstab* dosyasından aşağıdaki satırın silinmesi gerekir:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

`umount`Diski çıkarmak için kullanın. Aşağıdaki örnek */datadrive* bağlama noktasından */dev/sdc1* bölümünü çıkarır:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Azure CLı kullanarak veri diskini ayırma 

Bu örnek, *Myresourcegroup* Içindeki *MYVM* adlı VM 'den *mydatadisk* diskini ayırır.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disk depolamada kalır, ancak artık bir sanal makineye bağlı değildir.


## <a name="detach-a-data-disk-using-the-portal"></a>Portalı kullanarak veri diski çıkarma

1. Sol taraftaki menüden **sanal makineler**' i seçin.
1. Sanal makine dikey penceresinde **diskler**' i seçin.
1. **Diskler** dikey penceresinde, ayırmak istediğiniz veri diskinin en sağında, diski ayırmak için **X** düğmesini seçin.
1. Disk kaldırıldıktan sonra, dikey pencerenin en üstündeki **Kaydet** ' i seçin.

Disk depolamada kalır, ancak artık bir sanal makineye bağlı değildir. Disk silinmez.

## <a name="next-steps"></a>Sonraki adımlar
Veri diskini yeniden kullanmak istiyorsanız, yalnızca [başka BIR sanal](add-disk.md)makineye ekleyebilirsiniz.

Artık depolama maliyetlerine neden olmayacak şekilde diski silmek istiyorsanız, bkz. [eklenmemiş Azure yönetilen ve yönetilmeyen diskleri bulma ve silme-Azure Portal](../disks-find-unattached-portal.md).