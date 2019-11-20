---
title: Öğretici-Azure Backup bir VM diskini geri yükleme
description: Yedekleme ve Kurtarma Hizmetleri ile Azure’da bir diskin nasıl geri yükleneceğini ve kurtarılan bir sanal makinenin nasıl oluşturulacağını öğrenin.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 9b2048d8683ba2dde00a874445eb936cfb775cf1
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171751"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Azure’da bir diski geri yükleme ve kurtarılan bir VM oluşturma

Azure Backup, coğrafi olarak yedekli kurtarma kasalarında depolanan kurtarma noktaları oluşturur. Bir kurtarma noktasından geri yüklediğinizde, tüm sanal makineyi veya tek tek dosyaları geri yükleyebilirsiniz. Bu makalede, CLI kullanarak tam bir sanal makinenin nasıl geri yükleneceği açıklanmaktadır. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Kurtarma noktalarını listeleme ve seçme
> * Bir kurtarma noktasından diski geri yükleme
> * Geri yüklenen diskten sanal makine oluşturma

Disk geri yüklemek ve kurtarılmış bir VM oluşturmak üzere PowerShell kullanmayla ilgili bilgi edinmek için bkz. [PowerShell ile Azure VM’lerini yedekleme ve geri yükleme](backup-azure-vms-automation.md#restore-an-azure-vm).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.18 veya sonraki bir sürümünü kullanmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme]( /cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için Azure Backup ile korunmuş olan bir Linux sanal makinesi gerekir. Yanlışlıkla bir sanal makineyi silme ve kurtarma işleminin benzetimini yapmak için, bir kurtarma noktasındaki diskten bir sanal makine oluşturursunuz. Azure Backup ile korunan bir Linux sanal makinesine ihtiyacınız varsa bkz. [CLI ile Azure’da bir sanal makineyi yedekleme](quick-backup-vm-cli.md).

## <a name="backup-overview"></a>Backup’a genel bakış

Azure bir yedekleme başlattığında sanal makinedeki yedekleme uzantısı, belirli bir noktanın anlık görüntüsünü alır. İlk yedekleme istendiğinde sanal makineye yedekleme uzantısı yüklenir. Azure Backup, yedekleme gerçekleştiğinde sanal makine çalışmıyorsa temel depolamanın anlık görüntüsünü de alabilir.

Varsayılan olarak Azure Backup, bir dosya sisteminin tutarlı yedeklemesini alır. Azure Backup, anlık görüntüyü aldığında veriler Kurtarma Hizmetleri kasasına aktarılır. Verimliliği en üst düzeye çıkarmak için Azure Backup yalnızca önceki yedeklemeden itibaren değişmiş olan veri bloklarını belirler ve aktarır.

Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.

## <a name="list-available-recovery-points"></a>Kullanılabilir kurtarma noktalarını listeleme

Bir diski geri yüklemek için, kurtarma verileri kaynağı olarak bir kurtarma noktası seçersiniz. Varsayılan ilke her gün bir kurtarma noktası oluşturup 30 gün boyunca bunları beklettiğinden, kurtarma için belirli bir nokta seçmenize olanak sağlayan bir kurtarma noktaları kümesini tutabilirsiniz.

Kullanılabilir kurtarma noktalarının listesini görmek için [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) komutunu kullanın. Diskleri kurtarmak için kurtarma noktası **adı** kullanılır. Bu öğreticide, kullanılabilir en son kurtarma noktasını istiyoruz. `--query [0].name` parametresi aşağıdaki şekilde en son kurtarma noktası adını seçer:

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>Sanal makine diskini geri yükleme

Kurtarma noktasından diskinizi geri yüklemek için önce bir Azure depolama hesabı oluşturun. Bu depolama hesabı, geri yüklenen diski depolamak için kullanılır. Ek adımlarda, sanal makine oluşturmak için geri yüklenen disk kullanılır.

1. Depolama hesabı oluşturmak için [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) komutunu kullanın. Depolama hesabı adı tamamen küçük harflerden oluşmalı ve genel olarak benzersiz olmalıdır. *mystorageaccount* değerini kendi benzersiz adınızla değiştirin:

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) komutuyla kurtarma noktanızdan diski geri yükleyin. *mystorageaccount* değerini, önceki komutta oluşturduğunuz depolama hesabının adıyla değiştirin. *myRecoveryPointName* değerini, önceki [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) komutunun çıktısından elde ettiğiniz kurtarma noktası adıyla değiştirin:

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

## <a name="monitor-the-restore-job"></a>Geri yükleme işini izleme

Geri yükleme işinin durumunu izlemek için [az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) komutunu kullanın:

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

Çıktı, geri yükleme işinin *İlerliyor* durumunda olduğunu gösteren aşağıdaki örneğe benzer olacaktır:

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

Geri yükleme işinin *Durumu* *Tamamlandı* olarak bildirildiğinde depolama hesabına disk geri yüklenmiş olur.

## <a name="convert-the-restored-disk-to-a-managed-disk"></a>Geri yüklenen diski Yönetilen Diske dönüştürme

Geri yükleme işi, yönetilmeyen bir disk oluşturur. Diskten bir sanal makine oluşturmak için öncelikle diskin bir yönetilen diske dönüştürülmesi gerekir.

1. [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) komutuyla depolama hesabınız için bağlantı bilgilerini alın. *mystorageaccount* değerini aşağıdaki şekilde depolama hesabınızın adıyla değiştirin:

    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. Yönetilmeyen diskiniz, depolama hesabında güvenli hale getirilir. Aşağıdaki komutlar, yönetilmeyen diskinizle ilgili bilgileri alır ve Yönetilen Diski oluştururken sonraki adımda kullanılan *uri* adlı bir değişken oluşturur.

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. Şimdi [az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create) komutuyla kurtarılan diskinizden bir Yönetilen Disk oluşturabilirsiniz. Önceki adımda yer alan *uri* değişkeni, Yönetilen Diskinizin kaynağı olarak kullanılır.

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. Şimdi geri yüklenen diskinizden bir Yönetilen Diskiniz olduğuna göre, [az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete) komutuyla yönetilmeyen diski ve depolama hesabını temizleyin. *mystorageaccount* değerini aşağıdaki şekilde depolama hesabınızın adıyla değiştirin:

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```

## <a name="create-a-vm-from-the-restored-disk"></a>Geri yüklenen diskten sanal makine oluşturma

Son adım, Yönetilen Diskten bir sanal makine oluşturulmasıdır.

1. [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) komutuyla aşağıdaki şekilde Yönetilen Diskinizden bir sanal makine oluşturun:

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. Kurtarılan diskinizden sanal makinenizin oluşturulduğunu onaylamak için [az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) komutuyla aşağıdaki şekilde kaynak grubunuzdaki sanal makineleri listeleyin:

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir kurtarma noktasından bir diski geri yüklediniz ve sonra diskten bir sanal makine oluşturdunuz. Şunları öğrendiniz:

> [!div class="checklist"]
>
> * Kurtarma noktalarını listeleme ve seçme
> * Bir kurtarma noktasından diski geri yükleme
> * Geri yüklenen diskten sanal makine oluşturma

Bir kurtarma noktasından tek tek dosyaları geri yükleme hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Azure’da dosyaları sanal makineye geri yükleme](tutorial-restore-files.md)
