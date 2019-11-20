---
title: 'Öğretici: PowerShell ile birden çok Azure VM yedeklemesi'
description: Bu öğretici, Azure PowerShell kullanarak birden fazla Azure VM 'yi bir kurtarma hizmetleri kasasına yedeklemeye ilişkin ayrıntılar sağlar.
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc
ms.openlocfilehash: 154238eae78ce44b9fc91058e58d9a11e254c0f9
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171777"
---
# <a name="back-up-azure-vms-with-powershell"></a>Azure VM’lerini PowerShell ile yedekleme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Bu öğreticide, PowerShell kullanarak birden fazla Azure VM 'yi yedeklemek için bir [Azure Backup](backup-overview.md) Recovery Services kasasının nasıl dağıtılacağı açıklanmaktadır.  

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Bir kurtarma hizmetleri Kasası oluşturun ve kasa bağlamını ayarlayın.
> * Yedekleme ilkesi tanımlama
> * Birden çok sanal makineyi korumak için yedekleme ilkesini uygulama
> * Korumalı sanal makineler için isteğe bağlı bir yedekleme işi tetikleyebilmeniz (veya koruyabilmeniz) için, ortamınızı sanal makinelerinizi korumak üzere hazırlamak için [önkoşulları](backup-azure-arm-vms-prepare.md) gerçekleştirmeniz gerekir.

> [!IMPORTANT]
> Bu öğretici, önceden bir kaynak grubu ve bir Azure sanal makinesi oluşturmuş olduğunuzu varsayar.

## <a name="sign-in-and-register"></a>Oturum açın ve kaydolun

1. `Connect-AzAccount` komutuyla Azure aboneliğinizde oturum açın ve ekrandaki yönergeleri izleyin.

    ```powershell
    Connect-AzAccount
    ```

2. Azure Backup ilk kez kullandığınızda, Azure kurtarma hizmeti sağlayıcısı 'nı [register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider)ile aboneliğinize kaydetmeniz gerekir. Zaten kaydolduysanız, bu adımı atlayın.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

[Kurtarma Hizmetleri Kasası](backup-azure-recovery-services-vault-overview.md) , Azure VM 'leri gibi korunan kaynaklar için yedekleme verilerini depolayan bir mantıksal kapsayıcıdır. Bir yedekleme işi çalıştırıldığında, kurtarma hizmetleri kasasının içinde bir kurtarma noktası oluşturur. Daha sonra bu kurtarma noktalarından birini kullanarak verileri dilediğiniz zaman geri yükleyebilirsiniz.

* Bu öğreticide, kasayı, yedeklemek istediğiniz VM ile aynı kaynak grubunda ve konumda oluşturursunuz.
* Azure Backup, yedeklenen veriler için depolamayı otomatik olarak işler. Kasa, varsayılan olarak [coğrafi olarak yedekli depolama (GRS)](../storage/common/storage-redundancy-grs.md)kullanır. Coğrafi yedeklilik, yedeklenen verilerin birincil bölgeden yüzlerce mil uzakta olan ikincil bir Azure bölgesine çoğaltılmasını sağlar.

Kasayı aşağıdaki gibi oluşturun:

1. Kasayı oluşturmak için [New-Azrecoveryserviceskasasını](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault)kullanın. Yedeklemek istediğiniz sanal makinenin kaynak grubu adını ve konumunu belirtin.

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Çoğu Azure Backup cmdlet’i, girdi olarak Kurtarma Hizmetleri kasasını gerektirir. Bu nedenle, Yedekleme Kurtarma Hizmetleri kasasının bir değişkende depolanması uygundur.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext)ile kasa bağlamını ayarlayın.

   * Kasa bağlamı, kasada korunan veri türüdür.
   * Bağlam ayarlandıktan sonra, sonraki tüm cmdlet 'ler için geçerli olur

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Azure VM'lerini yedekleme

Yedeklemeler, yedekleme ilkesinde belirtilen zamanlamaya uygun olarak çalışır. Bir Kurtarma Hizmetleri kasası oluşturduğunuzda bu, varsayılan koruma ve saklama ilkeleri ile birlikte gelir.

* Varsayılan koruma ilkesi, bir yedekleme işini belirtilen zamanda günde bir kez tetikler.
* Varsayılan saklama ilkesi, 30 gün boyunca günlük kurtarma noktasını korur.

Bu öğreticide Azure VM 'yi etkinleştirmek ve yedeklemek için aşağıdakileri yaptık:

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)ile yedekleme verilerinizi tutan kasada bir kapsayıcı belirtin.
2. Yedekleme için her VM bir öğedir. Bir yedekleme işi başlatmak için [Get-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)ile VM hakkında bilgi edinebilirsiniz.
3. [Backup-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem)ile isteğe bağlı bir yedekleme çalıştırın.
    * İlk ilk yedekleme işi tam kurtarma noktası oluşturur.
    * İlk yedeklemeden sonra her bir yedekleme işi artımlı kurtarma noktaları oluşturur.
    * Yalnızca son yedekleme sonrasında yapılan değişiklikleri aktardığından artımlı kurtarma noktaları depolama alanı ve süre açısından verimlilik sağlar.

Yedeklemeyi aşağıdaki şekilde etkinleştirip çalıştırın:

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>Sorun giderme

Sanal makinenizi yedeklerken sorunlarla karşılaşırsanız, bu [sorun giderme makalesini](backup-azure-vms-troubleshoot.md)inceleyin.

### <a name="deleting-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası silme

Bir kasayı silmeniz gerekiyorsa, öncelikle kasadaki kurtarma noktalarını silin ve ardından kasanın kaydını aşağıdaki gibi silin:

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>Sonraki adımlar

* PowerShell ile Azure VM 'lerini yedekleme ve geri yükleme hakkında daha ayrıntılı bir yol [inceleyin](backup-azure-vms-automation.md) .
* [Azure VM 'lerini yönetme ve izleme](backup-azure-manage-vms.md)
* [Azure VM 'lerini geri yükleme](backup-azure-arm-restore-vms.md)
