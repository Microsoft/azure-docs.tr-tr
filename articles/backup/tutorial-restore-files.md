---
title: Öğretici-Azure Backup ile bir sanal makineye dosya geri yükleme
description: Backup ve Recovery Services ile bir Azure sanal makinesinde nasıl dosya düzeyinde geri yüklemeler gerçekleştirileceğini öğrenin.
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: dc3ce601a92020c0fe405935b8dc57242d852a1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765332"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Azure’da dosyaları sanal makineye geri yükleme

Azure Backup, coğrafi olarak yedekli kurtarma kasalarında depolanan kurtarma noktaları oluşturur. Bir kurtarma noktasından geri yüklediğinizde, tüm sanal makineyi veya tek tek dosyaları geri yükleyebilirsiniz. Bu makalede tek tek dosyaların nasıl geri yükleneceği açıklanmaktadır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
>
> * Kurtarma noktalarını listeleme ve seçme
> * Bir kurtarma noktasını sanal makineye bağlama
> * Bir kurtarma noktasından dosyaları geri yükleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici için Azure Backup ile korunmuş olan bir Linux sanal makinesi gerekir. Yanlışlıkla dosya silme ve kurtarma işleminin benzetimini yapmak için bir web sunucusundan bir sayfayı silin. Bir web sunucusu çalıştıran ve Azure Backup ile korunan bir Linux sanal makinesine ihtiyacınız varsa bkz. [CLI ile Azure’da bir sanal makineyi yedekleme](quick-backup-vm-cli.md).

Ortamınızı hazırlayın:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.18 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="backup-overview"></a>Backup’a genel bakış

Azure bir yedekleme başlattığında sanal makinedeki yedekleme uzantısı, belirli bir noktanın anlık görüntüsünü alır. İlk yedekleme istendiğinde sanal makineye yedekleme uzantısı yüklenir. Azure Backup, yedekleme gerçekleştiğinde VM çalışmıyorsa temel depolamanın anlık görüntüsünü de alabilir.

Varsayılan olarak Azure Backup, bir dosya sisteminin tutarlı yedeklemesini alır. Azure Backup, anlık görüntüyü aldığında veriler Kurtarma Hizmetleri kasasına aktarılır. Verimliliği en üst düzeye çıkarmak için Azure Backup yalnızca önceki yedeklemeden itibaren değişmiş olan veri bloklarını belirler ve aktarır.

Veri aktarımı tamamlandığında, anlık görüntü kaldırılır ve bir kurtarma noktası oluşturulur.

## <a name="delete-a-file-from-a-vm"></a>Sanal makineden bir dosyayı silme

Yanlışlıkla bir dosyayı siler veya dosya üzerinde değişiklik yaparsanız, bir kurtarma noktasından tek tek dosyaları geri yükleyebilirsiniz. Bu işlem, bir kurtarma noktasında yedeklenen dosyalara göz atmanıza ve yalnızca ihtiyaç duyduğunuz dosyaları geri yüklemenize olanak sağlar. Bu örnekte, dosya düzeyinde kurtarma işlemini göstermek için bir web sunucusundan dosyayı sileriz.

1. Sanal makinenize bağlanmak için [az vm show](/cli/azure/vm#az_vm_show) ile sanal makinenizin IP adresini edinin:

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Web sitenizin şu anda çalıştığını onaylamak için, sanal makinenizin genel IP adresiyle bir web tarayıcısını açın. Web tarayıcısı penceresini açık bırakın.

    ![Varsayılan NGINX web sayfası](./media/tutorial-restore-files/nginx-working.png)

3. SSH ile sanal makinenize bağlanın. *publicIpAddress* değerini, önceki bir komutta aldığınız genel IP adresiyle değiştirin:

    ```bash
    ssh publicIpAddress
    ```

4. */var/www/html/index.nginx-debian.html* adresindeki web sunucusundan varsayılan sayfayı aşağıdaki şekilde silin:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Web tarayıcınızda web sayfasını yenileyin. Aşağıdaki örnekte gösterildiği gibi web sitesi sayfası artık sayfayı yüklemez:

    ![NGINX web sitesi artık varsayılan sayfayı yüklemez](./media/tutorial-restore-files/nginx-broken.png)

6. Sanal makinenize yönelik SSH oturumunu aşağıdaki şekilde kapatın:

    ```bash
    exit
    ```

## <a name="generate-file-recovery-script"></a>Dosya kurtarma betiği oluşturma

Dosyalarınızı geri yüklemek için Azure Backup, yerel dosya olarak kurtarma noktanızı bağlayan sanal makinenizde çalıştırılacak bir betik sağlar. Bu yerel sürücüye göz atabilir, sanal makineye dosyaları geri yükleyebilir, ardından kurtarma noktasının bağlantısını kesebilirsiniz. Azure Backup, zamanlama ve bekletme için atanan ilke temelinde verilerinizi yedeklemeye devam eder.

1. Sanal makinenize yönelik kurtarma noktalarını listelemek için [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az_backup_recoverypoint_list) komutunu kullanın. Bu örnekte, *Myrecoveryserviceskasasında* korunan *MYVM* adlı sanal makine için en son kurtarma noktasını seçtik:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Kurtarma noktasını sanal makinenize bağlayan veya takan betiği almak için [az backup restore files mount-rp](/cli/azure/backup/restore/files#az_backup_restore_files_mount_rp) komutunu kullanın. Aşağıdaki örnek, *Myrecoveryserviceskasasında* korunan *MYVM* adlı sanal makine için betiği edinir.

    *myRecoveryPointName* değerini, önceki komutta aldığınız kurtarma noktasının adıyla değiştirin:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Aşağıdaki örnekte olduğu gibi betik indirilir ve parola görüntülenir:

    ```output
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Betiği sanal makinenize aktarmak için Güvenli Kopya (SCP) kullanın. İndirdiğiniz betiğin adını belirtin ve *publicIpAddress* değerini sanal makinenizin genel IP adresiyle değiştirin. SCP komutunun sonuna aşağıdaki şekilde `:` eklediğinizden emin olun:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```

## <a name="restore-file-to-your-vm"></a>Dosyayı sanal makinenize geri yükleme

Sanal makinenize kurtarma betiği kopyalandığına göre artık kurtarma noktasını bağlayabilir ve dosyaları geri yükleyebilirsiniz.

>[!NOTE]
> Devam etmeden önce betiği sanal makinenizde çalıştırıp çalıştıracağınızı görmek için [buraya](backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script) bakın.

1. SSH ile sanal makinenize bağlanın. *publicIpAddress* değerini, aşağıdaki şekilde sanal makinenizin genel IP adresiyle değiştirin:

    ```bash
    ssh publicIpAddress
    ```

2. Betiğinizin düzgün şekilde çalışmasını sağlamak için **chmod** ile yürütme izinleri ekleyin. Kendi betiğinizin adını girin:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Kurtarma noktasını bağlamak için betiği çalıştırın. Kendi betiğinizin adını girin:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    Betik çalışırken, kurtarma noktasına erişmek için bir parola girmeniz istenir. Kurtarma betiğini oluşturan önceki [az backup restore files mount-rp](/cli/azure/backup/restore/files#az_backup_restore_files_mount_rp) komutundan elde edilen çıktıda gösterilen parolayı girin.

    Betikteki çıktı size kurtarma noktasının yolunu sunar. Aşağıdaki örnek çıktı, */home/azureuser/myVM-20170919213536/Volume1* dizinine bağlanan kurtarma noktasını gösterir:

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1

    ************ Open File Explorer to browse for files. ************
    ```

4. Bağlanan kurtarma noktasından varsayılan NGINX web sayfasını özgün dosya konumuna geri kopyalamak için **cp** kullanın. */home/azureuser/myVM-20170919213536/Volume1* bağlama noktasını kendi konumunuzla değiştirin:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

5. Web tarayıcınızda web sayfasını yenileyin. Aşağıdaki örnekte gösterildiği gibi web sitesi artık düzgün şekilde yüklenir:

    ![NGINX web sitesi artık düzgün şekilde yüklenir](./media/tutorial-restore-files/nginx-restored.png)

6. Sanal makinenize yönelik SSH oturumunu aşağıdaki şekilde kapatın:

    ```bash
    exit
    ```

7. [az backup restore files unmount-rp](/cli/azure/backup/restore/files#az_backup_restore_files_unmount_rp) ile sanal makinenizden kurtarma noktasını çıkarın. Aşağıdaki örnek, *myRecoveryServicesVault* içindeki *myVM* adlı sanal makineden kurtarma noktasını çıkarır.

    *myRecoveryPointName* değerini, önceki komutlarda aldığınız kurtarma noktasının adıyla değiştirin:

    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir kurtarma noktasını sanal makineye bağladınız ve bir web sunucusu için dosyaları geri yüklediniz. Şunları öğrendiniz:

> [!div class="checklist"]
>
> * Kurtarma noktalarını listeleme ve seçme
> * Bir kurtarma noktasını sanal makineye bağlama
> * Bir kurtarma noktasından dosyaları geri yükleme

Windows Server’ın Azure’da nasıl yedekleneceği hakkında bilgi edinmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Windows Server’ı Azure’da Yedekleme](tutorial-backup-windows-server-to-azure.md)
