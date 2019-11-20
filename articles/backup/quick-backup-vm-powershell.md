---
title: Hızlı başlangıç-PowerShell ile sanal makine yedekleme
description: Bu hızlı başlangıçta Azure PowerShell modülü ile Azure sanal makinelerinizi nasıl yedekleyeceğinizi öğrenin.
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.custom: mvc
ms.openlocfilehash: 8021ca553a1434c891bee911e85d351c61938594
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171953"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>PowerShell ile Azure'daki bir sanal makineyi yedekleme

[Azure POWERSHELL az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) Module, komut satırından veya betiklerden Azure kaynaklarını oluşturmak ve yönetmek için kullanılır.

[Azure Backup](backup-overview.md) , şirket içi makineleri ve uygulamaları ve Azure VM 'lerini yedekler. Bu makalede, AZ modülle bir Azure VM 'yi nasıl yedekleyeceğiniz gösterilmektedir. Alternatif olarak, [Azure CLI](quick-backup-vm-cli.md)veya [Azure Portal](quick-backup-vm-portal.md)kullanarak bir VM 'yi yedekleyebilirsiniz.

Bu hızlı başlangıç belgesi var olan bir Azure VM'de yedeklemeyi etkinleştirir. Bir sanal makine oluşturmanız gerekiyorsa [Azure PowerShell ile sanal makine oluşturabilirsiniz](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json).

Bu hızlı başlangıç Azure PowerShell AZ Module Version 1.0.0 veya üstünü gerektirir. Sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure PowerShell Modülü yükleme](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>Oturum açın ve kaydolun

1. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

    ```powershell
    Connect-AzAccount
    ```

2. Azure Backup ilk kez kullandığınızda, Azure kurtarma hizmeti sağlayıcısı 'nı [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)ile aboneliğinize aşağıda gösterildiği gibi kaydetmeniz gerekir:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

[Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md) , Azure VM 'leri gibi korunan kaynaklar için yedekleme verilerini depolayan bir mantıksal kapsayıcıdır. Bir yedekleme işi çalıştırıldığında, kurtarma hizmetleri kasasının içinde bir kurtarma noktası oluşturur. Daha sonra bu kurtarma noktalarından birini kullanarak verileri dilediğiniz zaman geri yükleyebilirsiniz.

Kasayı oluştururken:

- Kaynak grubu ve konum için, yedeklemek istediğiniz sanal makinenin kaynak grubunu ve konumunu belirtin.
- VM oluşturmak için bu [örnek betiği](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json) kullandıysanız kaynak grubu **myresourcegroup**, VM ***myvm**ve kaynaklar **westeurope** bölgedeyse.
- Azure Backup, yedeklenen veriler için depolamayı otomatik olarak işler. Kasa, varsayılan olarak [coğrafi olarak yedekli depolama (GRS)](../storage/common/storage-redundancy-grs.md)kullanır. Coğrafi yedeklilik, yedeklenen verilerin birincil bölgeden yüzlerce mil uzakta olan ikincil bir Azure bölgesine çoğaltılmasını sağlar.

Şimdi bir kasa oluşturun:

1. Kasayı oluşturmak için [New-Azrecoveryserviceskasasını](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) kullanın:

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)ile kasa bağlamını aşağıdaki şekilde ayarlayın:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)ile kasanın depolama artıklığı yapılandırmasını (LRS/GRS) aşağıdaki gibi değiştirin:

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```

    > [!NOTE]
    > Depolama artıklığı yalnızca kasada korunan yedekleme öğesi yoksa değiştirilebilir.

## <a name="enable-backup-for-an-azure-vm"></a>Bir Azure sanal makinesi için yedeklemeyi etkinleştirme

Bir Azure VM için yedeklemeyi etkinleştirir ve bir yedekleme ilkesi belirtin.

- İlke, yedeklemelerin ne zaman çalışacağını ve yedeklemeler tarafından oluşturulan kurtarma noktalarının ne zaman korunacağını tanımlar.
- Varsayılan koruma ilkesi, sanal makine için günde bir kez yedekleme çalıştırır ve oluşturulan kurtarma noktalarını 30 gün boyunca tutar. Bu varsayılan ilkeyi kullanarak sanal makinenizin hızlı bir şekilde korunmasını sağlayabilirsiniz.

Yedeklemeyi şu şekilde etkinleştirin:

1. İlk olarak, [Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)ile varsayılan ilkeyi ayarlayın:

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection)ile VM yedeklemesini etkinleştirin. İlkeyi, kaynak grubunu ve VM adını belirtin.

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```

## <a name="start-a-backup-job"></a>Bir yedekleme işi başlatma

Yedeklemeler, yedekleme ilkesinde belirtilen zamanlamaya göre çalışır. İsteğe bağlı yedekleme de çalıştırabilirsiniz:

- İlk ilk yedekleme işi tam kurtarma noktası oluşturur.
- İlk yedeklemeden sonra her bir yedekleme işi artımlı kurtarma noktaları oluşturur.
- Yalnızca son yedekleme sonrasında yapılan değişiklikleri aktardığından artımlı kurtarma noktaları depolama alanı ve süre açısından verimlilik sağlar.

İsteğe bağlı yedekleme çalıştırmak için [Backup-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)komutunu kullanın.

- Kasada, [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer)ile yedekleme verilerinizi tutan bir kapsayıcı belirtirsiniz.
- Yedeklenecek her sanal makine ayrı bir öğe olarak ele alınır. Bir yedekleme işi başlatmak için [Get-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem)ile VM hakkında bilgi edinebilirsiniz.

İsteğe bağlı yedekleme işini aşağıdaki şekilde çalıştırın:

1. Kapsayıcıyı belirtin, VM bilgilerini alın ve yedeklemeyi çalıştırın.

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. İlk yedekleme işi tam kurtarma noktası oluşturduğundan 20 dakikaya kadar beklemeniz gerekebilir. Sonraki yordamda açıklandığı gibi işi izleyin.

## <a name="monitor-the-backup-job"></a>Yedekleme işini izleme

1. İş durumunu izlemek için [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) komutunu çalıştırın.

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```

    Çıkış, işi **sürüyor**olarak gösteren aşağıdaki örneğe benzer:

    ```output
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. İş durumu **tamamlandığında**, VM korunur ve tam kurtarma noktası depolanır.

## <a name="clean-up-the-deployment"></a>Dağıtımı temizle

Artık VM 'yi yedeklemeniz gerekmiyorsa, temizleyebilirsiniz.

- VM 'yi geri yüklemeyi denemek istiyorsanız, temizleme işlemini atlayın.
- Var olan bir VM kullandıysanız, kaynak grubunu ve VM 'yi yerinde bırakmak için son [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet 'ini atlayabilirsiniz.

Korumayı devre dışı bırakın, geri yükleme noktalarını ve kasasını kaldırın. Daha sonra kaynak grubunu ve ilişkili VM kaynaklarını aşağıdaki gibi silin:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Kurtarma Hizmetleri kasası oluşturdunuz, bir sanal makine için koruma özelliklerini etkinleştirdiniz ve ilk kurtarma noktasını oluşturdunuz.

- Azure portal VM 'Leri [nasıl yedekleyeceğinizi öğrenin](tutorial-backup-vm-at-scale.md) .
- Bir VM 'yi hızlıca geri yüklemeyi [öğrenin](tutorial-restore-disk.md)
