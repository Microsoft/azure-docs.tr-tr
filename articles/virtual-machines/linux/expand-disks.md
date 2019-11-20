---
title: Azure 'da Linux VM 'de sanal sabit diskler genişletme
description: Azure CLı ile Linux VM üzerinde sanal sabit diskler genişletmeyi öğrenin
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 3bd85048cf12760d5918544ed6aac803e9fe120a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036201"
---
# <a name="expand-virtual-hard-disks-on-a-linux-vm-with-the-azure-cli"></a>Azure CLı ile Linux VM 'de sanal sabit diskler genişletme

Bu makalede, Azure CLı ile bir Linux sanal makinesi (VM) için yönetilen disklerin nasıl genişlemekte olduğu açıklanır. Ek depolama alanı sağlamak için [veri diskleri ekleyebilirsiniz](add-disk.md) ve ayrıca var olan bir veri diskini genişletebilirsiniz. İşletim sistemi (OS) için varsayılan sanal sabit disk boyutu genellikle Azure 'daki bir Linux sanal makinesi üzerinde 30 GB 'dir. 

> [!WARNING]
> Her zaman FileSystem 'ın sağlıklı durumda olduğundan emin olun, disk bölümü tablo türü yeni boyutu destekleyecektir ve disk yeniden boyutlandırma işlemlerini gerçekleştirmeden önce verilerinizin yedeklendiğinden emin olun. Daha fazla bilgi için bkz. [Azure 'Da Linux VM 'Leri yedekleme](tutorial-backup-vms.md). 

## <a name="expand-an-azure-managed-disk"></a>Azure yönetilen diskini genişletme
En son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olduğundan ve [az Login](/cli/azure/reference-index#az-login)kullanarak bir Azure hesabında oturum açtığınızdan emin olun.

Bu makalede, Azure 'da en az bir veri diski eklenmiş ve hazırlanmış bir VM mevcut olmalıdır. Kullanabileceğiniz bir sanal makine yoksa, bkz. [veri diskleri Ile VM oluşturma ve hazırlama](tutorial-manage-disks.md#create-and-attach-disks).

Aşağıdaki örneklerde, *Myresourcegroup* ve *myvm* gibi örnek parametre adlarını kendi değerlerinizle değiştirin.

1. Sanal sabit disklerdeki işlemler, çalıştıran VM ile gerçekleştirilemez. [Az VM serbest bırakma](/cli/azure/vm#az-vm-deallocate)ile sanal makineyi serbest bırakın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı VM 'yi kaldırır:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

    > [!NOTE]
    > Sanal sabit diski genişletmek için VM 'nin serbest bırakılmış olması gerekir. VM 'nin `az vm stop` ile durdurulması, işlem kaynaklarını serbest bırakmaz. İşlem kaynaklarını serbest bırakmak için `az vm deallocate`kullanın.

1. [Az disk List](/cli/azure/disk#az-disk-list)komutuyla bir kaynak grubundaki yönetilen disklerin listesini görüntüleyin. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubundaki yönetilen disklerin listesini görüntüler:

    ```azurecli
    az disk list \
        --resource-group myResourceGroup \
        --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' \
        --output table
    ```

    [Az disk Update](/cli/azure/disk#az-disk-update)ile gerekli diski genişletin. Aşağıdaki örnek, *mydatadisk* adlı yönetilen diski *200* GB 'ye genişletir:

    ```azurecli
    az disk update \
        --resource-group myResourceGroup \
        --name myDataDisk \
        --size-gb 200
    ```

    > [!NOTE]
    > Yönetilen bir diski genişlettiğinizde, güncelleştirilmiş boyut, en yakın yönetilen disk boyutuna yuvarlanır. Kullanılabilir yönetilen disk boyutları ve katmanlarının bir tablosu için bkz. [Azure yönetilen disklere genel bakış-fiyatlandırma ve faturalandırma](../windows/managed-disks-overview.md).

1. [Az VM start](/cli/azure/vm#az-vm-start)ile sanal makineyi başlatın. Aşağıdaki örnek, *Myresourcegroup*adlı kaynak grubunda *MYVM* adlı sanal makineyi başlatır:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```


## <a name="expand-a-disk-partition-and-filesystem"></a>Disk bölümünü ve dosya sistemini genişletme
Genişletilmiş bir disk kullanmak için temeldeki bölümü ve dosya sistemini genişletin.

1. Uygun kimlik bilgileriyle sanal makinenize SSH. [Az VM Show](/cli/azure/vm#az-vm-show)ile sanal MAKINENIZIN genel IP adresini görebilirsiniz:

    ```azurecli
    az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
    ```

1. Temel alınan bölümü ve dosya sistemini genişletin.

    a. Disk zaten bağlanmışsa, bağlantısını çıkarın:

    ```bash
    sudo umount /dev/sdc1
    ```

    b. Disk bilgilerini görüntülemek ve bölümü yeniden boyutlandırmak için `parted` kullanın:

    ```bash
    sudo parted /dev/sdc
    ```

    `print`ile mevcut bölüm düzeni hakkındaki bilgileri görüntüleyin. Çıktı, temeldeki diskin 215 GB olduğunu gösteren aşağıdaki örneğe benzer:

    ```bash
    GNU Parted 3.2
    Using /dev/sdc1
    Welcome to GNU Parted! Type 'help' to view a list of commands.
    (parted) print
    Model: Unknown Msft Virtual Disk (scsi)
    Disk /dev/sdc1: 215GB
    Sector size (logical/physical): 512B/4096B
    Partition Table: loop
    Disk Flags:
    
    Number  Start  End    Size   File system  Flags
        1      0.00B  107GB  107GB  ext4
    ```

    c. `resizepart`bölümü genişletin. Bölüm numarasını, *1*ve yeni bölüm için bir boyut girin:

    ```bash
    (parted) resizepart
    Partition number? 1
    End?  [107GB]? 215GB
    ```

    d. Çıkmak için `quit`girin.

1. Bölüm yeniden boyutlandırılırken, Bölüm tutarlılığını `e2fsck`ile doğrulayın:

    ```bash
    sudo e2fsck -f /dev/sdc1
    ```

1. Dosya sistemini `resize2fs`yeniden boyutlandırın:

    ```bash
    sudo resize2fs /dev/sdc1
    ```

1. Bölümü istenen konuma bağlayın, örneğin `/datadrive`:

    ```bash
    sudo mount /dev/sdc1 /datadrive
    ```

1. Veri diskinin yeniden boyutlandırıldığından emin olmak için `df -h`kullanın. Aşağıdaki örnek çıktı, */dev/sdc1* veri sürücüsünü artık 200 GB olarak göstermektedir:

    ```bash
    Filesystem      Size   Used  Avail Use% Mounted on
    /dev/sdc1        197G   60M   187G   1% /datadrive
    ```

## <a name="next-steps"></a>Sonraki adımlar
* Ek depolamaya ihtiyacınız varsa, [bir LINUX sanal makinesine veri diskleri de ekleyebilirsiniz](add-disk.md). 
* Disk şifrelemesi hakkında daha fazla bilgi için bkz. [Linux VM 'leri Için Azure disk şifrelemesi](disk-encryption-overview.md).
