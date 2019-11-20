---
title: Azure portal bir Linux sorun giderme sanal makinesi kullanın | Microsoft Docs
description: Azure portal kullanarak işletim sistemi diskini bir kurtarma VM 'sine bağlayarak Linux sanal makinesi sorunlarını giderme hakkında bilgi edinin
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: 87b4d761eb7bd1c4a16998e44e8160cda24a05b4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088256"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure portal kullanarak işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek bir Linux VM sorunlarını giderme
Linux sanal makineniz (VM) bir önyükleme veya disk hatasıyla karşılaşırsa, sanal sabit diskin kendisi üzerinde sorun giderme adımları gerçekleştirmeniz gerekebilir. Ortak bir örnek, VM 'nin başarıyla önyükleme yapabilmesini engelleyen ' de `/etc/fstab` geçersiz bir giriş olabilir. Bu makalede, tüm hataları onarmak için sanal sabit diskinizi başka bir Linux VM 'sine bağlamak üzere Azure portal kullanımı ve ardından özgün VM 'nizi yeniden oluşturmanız için Ayrıntılar açıklanır.

## <a name="recovery-process-overview"></a>Kurtarma işlemine genel bakış
Sorun giderme işlemi aşağıdaki gibidir:

1. Etkilenen VM 'yi durdurun.
1. VM 'nin işletim sistemi diski için bir anlık görüntü alın.
1. Anlık görüntüden bir sanal sabit disk oluşturun.
1. Sorun giderme amacıyla sanal sabit diski başka bir Windows sanal makinesine ekleyin ve bağlayın.
1. Sorun giderme işlemlerini yapacağınız VM'ye bağlanın. Özgün sanal sabit diskteki sorunları gidermek için dosyaları düzenleyin veya herhangi bir araç çalıştırın.
1. Sorun giderme işlemlerini yaptığınız VM’den sanal sabit diski çıkarın.
1. VM için işletim sistemi diskini değiştirin.

> [!NOTE]
> Bu makale, yönetilmeyen disk içeren VM için geçerlidir.

## <a name="determine-boot-issues"></a>Önyükleme sorunlarını belirleme
SANAL makinenizin neden doğru şekilde önyüklenemediğini öğrenmek için önyükleme tanılamayı ve VM ekran görüntüsünü inceleyin. Ortak bir örnek içinde `/etc/fstab`geçersiz bir giriş veya silinmekte veya taşınmakta olan temel bir sanal sabit disk olabilir.

Portalda VM 'nizi seçin ve sonra da **destek + sorun giderme** bölümüne gidin. VM 'nizden akan konsol iletilerini görüntülemek için **önyükleme tanılama** ' ya tıklayın. VM 'nin neden bir sorunla karşılaşacağını tespit edebilir olup olmadığınızı görmek için konsol günlüklerini gözden geçirin. Aşağıdaki örnekte, el ile etkileşim gerektiren bakım modunda takılmış bir VM gösterilmektedir:

![VM önyüklemesi Tanılama Konsolu günlüklerini görüntüleme](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Ayrıca, VM ekran görüntüsünün bir kopyasını indirmek için önyükleme tanılama günlüğü üstündeki **ekran görüntüsü** ' ne tıklayabilirsiniz.

## <a name="take-a-snapshot-of-the-os-disk"></a>İşletim sistemi diskinin anlık görüntüsünü alın
Anlık görüntü, bir sanal sabit sürücünün (VHD) tam, salt okunurdur bir kopyasıdır. Sürmekte olan tüm işlemlerin seçimini kaldırmak için bir anlık görüntüyü almadan önce VM 'yi düzgün bir şekilde kapatmanız önerilir. Bir işletim sistemi diskinin anlık görüntüsünü almak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com)gidin. Kenar çubuğundan **sanal makineler** ' i seçin ve ardından sorunlu VM 'yi seçin.
1. Sol bölmede, **diskler**' i seçin ve ardından işletim sistemi diskinin adını seçin.
    ![İşletim sistemi diskinin adı hakkında resim](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. İşletim sistemi diskinin **genel bakış** sayfasında **anlık görüntü oluştur**' u seçin.
1. İşletim sistemi diskiyle aynı konumda bir anlık görüntü oluşturun.

## <a name="create-a-disk-from-the-snapshot"></a>Anlık görüntüden disk oluşturma
Anlık görüntüden bir disk oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal **Cloud Shell** seçin.

    ![Açık Cloud Shell görüntüsü](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Anlık görüntüden yönetilen disk oluşturmak için aşağıdaki PowerShell komutlarını çalıştırın. Bu örnek adları uygun adlarla değiştirmelisiniz.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Komutlar başarıyla çalışıyorsa, belirttiğiniz kaynak grubunda Yeni diski görürsünüz.

## <a name="attach-disk-to-another-vm"></a>Diski başka bir VM 'ye iliştirme
Sonraki birkaç adımda, sorun giderme amacıyla başka bir VM kullanırsınız. Diski sorun giderme VM 'sine iliştirdikten sonra, diskin içeriğine gözatıp düzenleyebilirsiniz. Bu işlem, yapılandırma hatalarını düzeltmenize veya ek uygulama veya sistem günlük dosyalarını incelemenizi sağlar. Diski başka bir VM 'ye eklemek için şu adımları izleyin:

1. Portaldan kaynak grubunuzu seçin ve ardından sorun giderme sanal makinesini seçin. **Diskler**' i seçin, **Düzenle**' yi seçin ve **veri diski Ekle**' ye tıklayın:

    ![Portala mevcut diski iliştirme](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. **Veri diskleri** listesinde, tanımladığınız VM 'nin işletim sistemi diskini seçin. İşletim sistemi diskini görmüyorsanız, sanal makine ve işletim sistemi diskinin aynı bölgede (konum) olduğundan emin olun. 
3. Değişiklikleri uygulamak için **Kaydet** ' i seçin.

## <a name="mount-the-attached-data-disk"></a>Bağlı veri diskini bağlama

> [!NOTE]
> Aşağıdaki örneklerde bir Ubuntu VM 'de gereken adımlar ayrıntılı olarak verilmiştir. Red Hat Enterprise Linux veya SUSE gibi farklı bir Linux kaldırı kullanıyorsanız, günlük dosyası konumları ve `mount` komutları biraz farklı olabilir. Komutlarınıza ilişkin uygun değişiklikler için özel olarak verilen belgelere başvurun.

1. Uygun kimlik bilgilerini kullanarak sorun giderme sanal makinesine SSH. Bu disk, sorun giderme sanal makinesine bağlı ilk veri diskidir, büyük olasılıkla bağlı `/dev/sdc`demektir. Eklenen `dmseg` diskleri listelemek için kullanın:

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

    Yukarıdaki örnekte, işletim sistemi diski `/dev/sda` ve her sanal makine `/dev/sdb`için belirtilen geçici disk ' dir. Birden çok veri diskine sahipseniz,, vb. olmaları gerekir `/dev/sdd`. `/dev/sde`

2. Var olan sanal sabit diskinizi bağlamak için bir dizin oluşturun. Aşağıdaki örnek adlı `troubleshootingdisk`bir dizin oluşturur:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Var olan sanal sabit diskinizde birden çok bölüm varsa, gerekli bölümü bağlayın. Aşağıdaki örnek, konumundaki `/dev/sdc1`ilk birincil bölümü bağlar:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > En iyi yöntem, sanal sabit diskin evrensel benzersiz tanımlayıcısını (UUID) kullanarak Azure 'daki VM 'Lere veri disklerini bağlamasıdır. Bu kısa sorun giderme senaryosunda, UUID 'yi kullanarak sanal sabit diski bağlama gerekli değildir. Ancak, normal kullanım altında, sanal `/etc/fstab` sabit disklerin UUID yerine cihaz adı kullanılarak bağlanması için düzenlemenin başarısız olması VM 'nin önyüklenememesine neden olabilir.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Özgün sanal sabit diskteki sorunları çözme
Var olan sanal sabit disk takılı olduğunda, artık gereken tüm bakım ve sorun giderme adımlarını gerçekleştirebilirsiniz. Sorunları giderdikten sonra aşağıdaki adımlarla devam edin.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Özgün sanal sabit diski çıkarın ve ayırın
Hatalar çözümlendikten sonra, var olan sanal sabit diski sorun giderme sanal makinenizin dışında ayırın. Sanal sabit diski, sorun giderme VM 'sine iliştirene kadar sanal sabit diskinizi başka bir VM ile kullanamazsınız.

1. SSH oturumundan sorun giderme sanal makinesine var olan sanal sabit diski çıkarın. İlk olarak bağlama noktanız için üst dizinden geçiş yapın:

    ```bash
    cd /
    ```

    Şimdi var olan sanal sabit diski çıkarın. Aşağıdaki örnek, cihazı şu adreste `/dev/sdc1`kaldırır:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Artık sanal sabit diski VM 'den ayırın. Portalda VM 'nizi seçin ve **diskler**' e tıklayın. Var olan sanal sabit diskinizi seçip **Ayır**' a tıklayın:

    ![Var olan sanal sabit diski ayır](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Devam etmeden önce VM veri diskini başarıyla ayırana kadar bekleyin.

## <a name="swap-the-os-disk-for-the-vm"></a>VM için işletim sistemi diskini değiştirme

Azure portal artık VM 'nin işletim sistemi diskini değiştirmeyi destekler. Bunu yapmak için şu adımları uygulayın:

1. [Azure Portal](https://portal.azure.com)gidin. Kenar çubuğundan **sanal makineler** ' i seçin ve ardından sorunlu VM 'yi seçin.
1. Sol bölmede, **diskler**' i seçin ve ardından **Işletim sistemi diski Değiştir**' i seçin.
        ![Azure portal takas OS disk ile ilgili görüntü](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Onarılmakta olduğunuz yeni diski seçin ve ardından değişikliği onaylamak için VM 'nin adını yazın. Diski listede görmüyorsanız, diski sorun giderme VM 'sinden ayırdıktan sonra 10 ~ 15 dakika bekleyin. Ayrıca, diskin VM ile aynı konumda olduğundan emin olun.
1. Tamam ' ı seçin.

## <a name="next-steps"></a>Sonraki adımlar
Sanal makinenize bağlanırken sorun yaşıyorsanız bkz. [Azure VM Ile SSH bağlantılarında sorun giderme](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). VM 'niz üzerinde çalışan uygulamalara erişme sorunları için bkz. [bir LINUX sanal makinesinde uygulama bağlantı sorunlarını giderme](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Kaynak Yöneticisi kullanma hakkında daha fazla bilgi için bkz. [Azure Resource Manager genel bakış](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
