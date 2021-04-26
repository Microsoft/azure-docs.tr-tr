---
title: PowerShell ile Azure VM 'lerini yedekleme ve kurtarma
description: PowerShell ile Azure Backup kullanarak Azure VM 'lerinin nasıl yedekleneceği ve kurtarılacağı açıklanmaktadır
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: f59c18aecf577bc7f7d0b1360dd36504305af893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633198"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>PowerShell ile Azure VM 'lerini yedekleme ve geri yükleme

Bu makalede, PowerShell cmdlet 'lerini kullanarak bir [Azure Backup](backup-overview.md) Recovery Services kasasında BIR Azure VM 'nin nasıl yedekleneceği ve geri yükleneceği açıklanmaktadır.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
>
> * Bir kurtarma hizmetleri Kasası oluşturun ve kasa bağlamını ayarlayın.
> * Yedekleme ilkesi tanımlama
> * Birden çok sanal makineyi korumak için yedekleme ilkesini uygulama
> * Korumalı sanal makineler için isteğe bağlı bir yedekleme işi tetikleyebilmeniz (veya koruyabilmeniz) için, ortamınızı sanal makinelerinizi korumak üzere hazırlamak için [önkoşulları](backup-azure-arm-vms-prepare.md) gerçekleştirmeniz gerekir.

## <a name="before-you-start"></a>Başlamadan önce

* Kurtarma Hizmetleri kasaları hakkında [daha fazla bilgi edinin](backup-azure-recovery-services-vault-overview.md) .
* Azure VM yedeklemesi mimarisini [gözden geçirin](backup-architecture.md#architecture-built-in-azure-vm-backup) , yedekleme süreci [hakkında bilgi edinin](backup-azure-vms-introduction.md) ve destek, sınırlamalar ve önkoşulları [gözden geçirin](backup-support-matrix-iaas.md) .
* Kurtarma Hizmetleri için PowerShell nesne hiyerarşisini gözden geçirin.

## <a name="recovery-services-object-hierarchy"></a>Kurtarma Hizmetleri nesne hiyerarşisi

Nesne hiyerarşisi aşağıdaki diyagramda özetlenir.

![Kurtarma Hizmetleri nesne hiyerarşisi](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Azure kitaplığı 'ndaki **az. RecoveryServices** [cmdlet başvuru](/powershell/module/az.recoveryservices/) başvurusunu gözden geçirin.

## <a name="set-up-and-register"></a>Ayarlama ve kaydetme

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Başlamak için:

1. [PowerShell 'in en son sürümünü indirin](/powershell/azure/install-az-ps)

2. Aşağıdaki komutu yazarak kullanılabilir PowerShell cmdlet 'lerini Azure Backup bulun:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Azure Backup, Azure Site Recovery ve kurtarma hizmetleri Kasası için diğer adlar ve cmdlet 'ler görüntülenir. Aşağıdaki görüntü, gördüklerinize bir örnektir. Cmdlet 'lerin tamamen listesi değildir.

    ![Kurtarma Hizmetleri listesi](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. **Connect-AzAccount** kullanarak Azure hesabınızda oturum açın. Bu cmdlet bir Web sayfasını getirir ve sizden hesap kimlik bilgilerinizi ister:

    * Alternatif olarak, **-Credential** parametresini kullanarak, hesap kimlik bilgilerinizi **Connect-azaccount** cmdlet 'ine bir parametre olarak dahil edebilirsiniz.
    * Bir kiracı adına çalışan bir CSP iş ortağıysanız, istemci Tenantıd veya kiracı birincil etki alanı adını kullanarak müşteriyi kiracı olarak belirtin. Örneğin: **Connect-AzAccount-Tenant "fabrikam.com"**

4. Hesap birden çok aboneliğe sahip olduğundan, hesapla birlikte kullanmak istediğiniz aboneliği ilişkilendirin:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Azure Backup ilk kez kullanıyorsanız, Azure kurtarma hizmeti sağlayıcısını aboneliğinizle kaydettirmek için **[register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** cmdlet 'ini kullanmanız gerekir.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Aşağıdaki komutları kullanarak sağlayıcıların başarıyla kaydedildiğini doğrulayabilirsiniz:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    Komut çıkışında, **Registrationstate** **kayıtlı** olarak değiştirilmelidir. Aksi takdirde, **[register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider)** cmdlet 'ini tekrar çalıştırmanız yeterlidir.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Aşağıdaki adımlar, bir kurtarma hizmetleri Kasası oluşturma konusunda size yol açabilir. Kurtarma Hizmetleri Kasası, bir yedekleme kasasından farklı.

1. Kurtarma Hizmetleri Kasası bir Kaynak Yöneticisi kaynağıdır, bu nedenle onu bir kaynak grubuna yerleştirmeniz gerekir. Var olan bir kaynak grubunu kullanabilir veya **[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)** cmdlet 'i ile bir kaynak grubu oluşturabilirsiniz. Bir kaynak grubu oluştururken, kaynak grubu için adı ve konumu belirtin.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Kurtarma Hizmetleri kasasını oluşturmak için [New-Azrecoveryserviceskasa](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) cmdlet 'ini kullanın. Kaynak grubu için kullanılan kasa için aynı konumu belirttiğinizden emin olun.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Kullanılacak depolama yedekliliği türünü belirtin. [Yerel olarak yedekli depolama (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage), coğrafi olarak [yedekli depolama (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage)veya [bölge yedekli depolama (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage)kullanabilirsiniz. Aşağıdaki örnek, *testkasasının* **geoyedekli** olarak ayarlandığı **-BackupStorageRedundancy** seçeneğini gösterir.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Çoğu Azure Backup cmdlet’i, girdi olarak Kurtarma Hizmetleri kasasını gerektirir. Bu nedenle, yedekleme kurtarma hizmetleri Kasası nesnesinin bir değişkende depolanması uygundur.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>Bir abonelikteki kasaları görüntüleme

Abonelikteki tüm kasaları görüntülemek için [Get-Azrecoveryserviceskasasını](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault)kullanın:

```powershell
Get-AzRecoveryServicesVault
```

Çıktı aşağıdaki örneğe benzer, ilişkili ResourceGroupName ve Location değerinin sağlandığını görürsünüz.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Azure VM'lerini yedekleme

Sanal makinelerinizi korumak için bir kurtarma hizmetleri Kasası kullanın. Korumayı uygulamadan önce, kasa bağlamını ayarlayın (kasada korunan veri türü) ve koruma ilkesini doğrulayın. Koruma ilkesi, yedekleme işlerinin çalıştırıldığı zamanlamanın ve her bir yedek anlık görüntüsünün ne kadar süre korunduğu zamanlamadır.

### <a name="set-vault-context"></a>Kasa bağlamını ayarla

Bir VM 'de korumayı etkinleştirmeden önce, kasa bağlamını ayarlamak için [set-AzRecoveryServicesVaultContext](/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext) komutunu kullanın. Kasa bağlamı ayarlandıktan sonra, sonraki tüm cmdlet’ler için geçerli olur. Aşağıdaki örnek, kasadaki *Test* Kasası için kasa bağlamını ayarlar.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Kasa KIMLIĞINI getir

Azure PowerShell yönergelerine uygun olarak kasa bağlamı ayarını kullanımdan kaldırmayı planlıyoruz. Bunun yerine, kasa KIMLIĞINI depolayıp alıp ilgili komutlara geçirebilmeniz gerekir. Bu nedenle, kasa bağlamını ayarlamadıysanız veya belirli bir kasa için çalıştırılacak komutu belirtmek istiyorsanız, kasa KIMLIĞINI aşağıdaki gibi tüm ilgili komuta "-Vaultıd" olarak geçirin:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Veya

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>Depolama çoğaltma ayarlarını değiştirme

Kasanın depolama çoğaltma yapılandırmasını LRS/GRS olarak ayarlamak için [set-AzRecoveryServicesBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) komutunu kullanın

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> Depolama artıklığı yalnızca kasada korunan yedekleme öğesi yoksa değiştirilebilir.

### <a name="create-a-protection-policy"></a>Koruma ilkesi oluşturma

Bir Kurtarma Hizmetleri kasası oluşturduğunuzda bu, varsayılan koruma ve saklama ilkeleri ile birlikte gelir. Varsayılan koruma ilkesi, her gün belirtilen saatte bir yedekleme işini tetikler. Varsayılan saklama ilkesi, 30 gün boyunca günlük kurtarma noktasını korur. Varsayılan ilkeyi kullanarak VM 'nizi hızlı bir şekilde koruyabilir ve ilkeyi daha sonra farklı ayrıntılarla düzenleyebilirsiniz.

Kasada kullanılabilen koruma ilkelerini görüntülemek için **[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** komutunu kullanın. Bu cmdlet 'i belirli bir ilkeyi almak veya bir iş yükü türüyle ilişkili ilkeleri görüntülemek için kullanabilirsiniz. Aşağıdaki örnek, AzureVM iş yükü türü ilkelerini alır.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

Çıktı aşağıdaki örneğe benzer:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> PowerShell 'deki BackupTime alanının saat dilimi UTC 'dir. Ancak, Azure portal yedekleme saati gösterildiğinde, saat yerel saat diliminiz olarak ayarlanır.
>
>

Bir yedekleme koruma ilkesi en az bir bekletme ilkesiyle ilişkilidir. Bir bekletme ilkesi, bir kurtarma noktasının silinmeden önce ne kadar süreyle saklanacağını tanımlar.

* Varsayılan saklama ilkesini görüntülemek için [Get-AzRecoveryServicesBackupRetentionPolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) kullanın.
* Benzer şekilde, varsayılan zamanlama ilkesini almak için [Get-AzRecoveryServicesBackupSchedulePolicyObject](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) komutunu kullanabilirsiniz.
* [New-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) cmdlet 'i, yedekleme ilkesi bilgilerini tutan bir PowerShell nesnesi oluşturur.
* Zamanlama ve bekletme ilkesi nesneleri New-AzRecoveryServicesBackupProtectionPolicy cmdlet 'ine giriş olarak kullanılır.

Varsayılan olarak, bir başlangıç saati zamanlama Ilkesi nesnesinde tanımlanmıştır. Başlangıç saatini istenen başlangıç saatine dönüştürmek için aşağıdaki örneği kullanın. İstenen başlangıç saati UTC biçiminde de olmalıdır. Aşağıdaki örnek, günlük yedeklemeler için istenen başlangıç zamanının 01:00. UTC olduğunu varsayar.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Başlangıç saatini yalnızca 30 dakikalık katları olarak sağlamanız gerekir. Yukarıdaki örnekte yalnızca "01:00:00" veya "02:30:00" olabilir. Başlangıç saati "01:15:00" olamaz

Aşağıdaki örnek, zaman çizelgesi ilkesini ve bekletme ilkesini değişkenler halinde depolar. Örnek, bu değişkenleri bir koruma ilkesi oluştururken *newpolicy* parametrelerini tanımlamak için kullanır.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

Çıktı aşağıdaki örneğe benzer:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Korumayı etkinleştir

Koruma ilkesini tanımladıktan sonra yine de bir öğe için ilkeyi etkinleştirmeniz gerekir. Korumayı etkinleştirmek için [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) komutunu kullanın. Korumayı etkinleştirmek için iki nesne gerekir-öğe ve ilke. İlke kasayla ilişkilendirildikten sonra, yedekleme iş akışı, ilke zamanlamasında tanımlanan zamanda tetiklenir.

> [!IMPORTANT]
> Aynı anda birden çok VM için yedeklemeyi etkinleştirmek üzere PowerShell 'i kullanırken, tek bir ilkenin kendisiyle ilişkilendirilmiş 100 ' den fazla VM 'ye sahip olmadığından emin olun. Bu [Önerilen en iyi uygulamadır](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy). Şu anda, PowerShell istemcisi 100 ' den fazla VM varsa açıkça engellenmez, ancak bu denetim ileride eklenmek üzere planlanmaktadır.

Aşağıdaki örnekler, NewPolicy ilkesini kullanarak V2VM öğesi için korumayı etkinleştirir. Örnekler, VM 'nin şifrelenme ve ne tür şifreleme türü temel alınarak farklılık gösterir.

**Şifrelenmemiş Kaynak Yöneticisi VM**'lerde korumayı etkinleştirmek için:

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Şifrelenmiş VM 'lerde korumayı etkinleştirmek için (BEK ve KEK kullanılarak şifrelenir), anahtar kasasındaki anahtarları ve gizli dizileri okumak için Azure Backup hizmetine izin vermeniz gerekir.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

Şifrelenmiş VM 'lerde korumayı etkinleştirmek için **(yalnızca bek kullanılarak şifrelenir)**, anahtar kasasından gizli dizileri okumak için Azure Backup hizmetine izin vermeniz gerekir.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Azure Kamu Bulutu kullanıyorsanız, `ff281ffe-705c-4f53-9f37-a40e6f2c68f3` [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet 'inde **servicePrincipalName** parametresinin değerini kullanın.
>

Birkaç diski seçmeli olarak yedeklemek ve diğerlerini [Bu senaryolarda](selective-disk-backup-restore.md#scenarios)bahsedildiğinde dışlamak istiyorsanız, korumayı yapılandırabilir ve [burada](selective-disk-backup-restore.md#enable-backup-with-powershell)belirtildiği gibi yalnızca ilgili diskleri yedekleyebilirsiniz.

## <a name="monitoring-a-backup-job"></a>Bir yedekleme işini izleme

Yedekleme işleri gibi uzun süre çalışan işlemleri Azure portal kullanmadan izleyebilirsiniz. Sürmekte olan bir işin durumunu almak için [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet 'ini kullanın. Bu cmdlet belirli bir kasa için yedekleme işlerini alır ve bu kasa kasa bağlamında belirtilir. Aşağıdaki örnek, bir işlem sürüyor işinin durumunu bir dizi olarak alır ve durumu $joblist değişkeninde depolar.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

Çıktı aşağıdaki örneğe benzer:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Bu işleri tamamlamak için yoklamak yerine, gereksiz ek kod- [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet 'ini kullanın. Bu cmdlet, iş tamamlanana veya belirtilen zaman aşımı değerine ulaşılana kadar yürütmeyi duraklatır.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Azure VM yedeklemelerini yönetme

### <a name="modify-a-protection-policy"></a>Koruma ilkesini değiştirme

Koruma ilkesini değiştirmek için, SchedulePolicy veya RetentionPolicy nesnelerini değiştirmek üzere [set-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) komutunu kullanın.

#### <a name="modifying-scheduled-time"></a>Zamanlanan saati değiştirme

Bir koruma ilkesi oluşturduğunuzda, varsayılan olarak bir başlangıç zamanı atanır. Aşağıdaki örneklerde, bir koruma ilkesinin başlangıç zamanının nasıl değiştirileceği gösterilmektedir.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that you want to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Saklama değiştirme

Aşağıdaki örnek, kurtarma noktası bekletmesini 365 gün olarak değiştirir.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Anlık geri yükleme anlık görüntü bekletmesini yapılandırma

> [!NOTE]
> Azure PowerShell sürüm 1.6.0 onde, birisi PowerShell kullanarak ilke içinde anlık geri yükleme anlık görüntü bekletme süresini güncelleştirebilir

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Varsayılan değer 2 olacaktır. Değeri en az 1 ve en fazla 5 olacak şekilde ayarlayabilirsiniz. Haftalık yedekleme ilkeleri için, dönem 5 olarak ayarlanır ve değiştirilemez.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Anlık görüntü bekletme sırasında Azure Backup kaynak grubu oluşturuluyor

> [!NOTE]
> Azure PowerShell sürüm 3.7.0 onde, bir diğeri anlık görüntüleri depolamak için oluşturulmuş kaynak grubunu oluşturabilir ve düzenleyebilir.

Kaynak grubu oluşturma kuralları ve diğer ilgili ayrıntılar hakkında daha fazla bilgi edinmek için, [sanal makineler için Azure Backup kaynak grubuna](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines) bakın.

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="exclude-disks-for-a-protected-vm"></a>Korunan bir VM için diskleri hariç tut

Azure VM yedeklemesi, [Bu senaryolarda](selective-disk-backup-restore.md#scenarios)yararlı olan diskleri seçmeli olarak hariç tutmak veya dahil etmek için bir yetenek sağlar. Sanal makine zaten Azure VM yedeklemesi tarafından korunuyorsa ve tüm diskler yedekleniyorsa, korumayı [burada](selective-disk-backup-restore.md#modify-protection-for-already-backed-up-vms-with-powershell)belirtildiği gibi seçmeli olarak dahil etmek veya hariç tutmak için korumayı değiştirebilirsiniz.

### <a name="trigger-a-backup"></a>Bir yedekleme tetikleyin

Yedekleme işini tetiklemek için [Backup-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) komutunu kullanın. İlk yedekleme ise, tam bir yedeklemedir. Sonraki yedeklemeler artımlı bir kopya alır. Aşağıdaki örnek bir VM yedeklemesini 60 gün boyunca tutulacak şekilde alır.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Çıktı aşağıdaki örneğe benzer:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> PowerShell 'deki StartTime ve bitişsaati alanlarının saat dilimi UTC 'dir. Ancak, saat Azure portal gösterildiğinde, saat yerel saat diliminiz olarak ayarlanır.
>
>

### <a name="change-policy-for-backup-items"></a>Yedekleme öğeleri için ilkeyi değiştirme

Mevcut ilkeyi değiştirebilir veya yedeklenen öğenin ilkesini Policy1 ' den Policy2 ' ye değiştirebilirsiniz. Yedeklenen bir öğeye yönelik ilkeleri değiştirmek için ilgili ilkeyi ve yedekleme öğesini getirin ve parametresi olarak Backup öğesiyle [Enable-AzRecoveryServices](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) komutunu kullanın.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

Komut, yapılandırma yedeklemesi tamamlanana kadar bekler ve aşağıdaki çıktıyı döndürür.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Korumayı durdurma

#### <a name="retain-data"></a>Verileri tutma

Korumayı durdurmak isterseniz, [Disable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet 'ini kullanabilirsiniz. Bu, zamanlanmış yedeklemeleri durdurur, ancak şu anda yedeklenene kadar yedeklenen veriler sürekli olarak korunur.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Yedekleme verilerini silme

Kasadaki depolanan yedekleme verilerini tamamen kaldırmak için '-RemoveRecoveryPoints ' bayrağını/anahtarını [' devre dışı bırak ' koruma komutuna](#retain-data)ekleyin.

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure VM 'yi geri yükleme

Azure portal kullanarak VM geri yükleme ve PowerShell kullanarak bir VM 'yi geri yükleme arasında önemli bir farklılık vardır. PowerShell ile kurtarma noktasından diskler ve yapılandırma bilgileri oluşturulduktan sonra geri yükleme işlemi tamamlanır. Geri yükleme işlemi, sanal makineyi oluşturmaz. Diskten bir sanal makine oluşturmak için, [geri yüklenen DISKLERDEN VM oluşturma](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)bölümüne bakın. Tüm VM 'yi geri yüklemek istemiyorsanız, ancak bir Azure VM yedeğinden birkaç dosyayı geri yüklemek veya kurtarmak istiyorsanız, [dosya kurtarma bölümüne](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)bakın.

> [!Tip]
> Geri yükleme işlemi, sanal makineyi oluşturmaz.
>
>

Aşağıdaki grafikte, Recoveryserviceskasasından BackupRecoveryPoint 'e kadar olan nesne hiyerarşisi gösterilmektedir.

![Yedekleme kapsayıcısını gösteren kurtarma hizmetleri nesne hiyerarşisi](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

Yedekleme verilerini geri yüklemek için, yedeklenen öğeyi ve zaman içinde yer alan verileri tutan kurtarma noktasını tespit edin. Verileri kasadan hesabınıza geri yüklemek için [restore-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) komutunu kullanın.

Bir Azure VM 'yi geri yüklemek için temel adımlar şunlardır:

* VM’yi seçin.
* Bir kurtarma noktası seçin.
* Diskleri geri yükleyin.
* Depolanan disklerden VM oluşturma.

### <a name="select-the-vm-when-restoring-files"></a>VM 'yi seçin (dosyaları geri yüklerken)

Doğru yedekleme öğesini tanımlayan PowerShell nesnesini almak için, kasadaki kapsayıcıdan başlayın ve nesne hiyerarşisinde bir şekilde çalışın. VM 'yi temsil eden kapsayıcıyı seçmek için [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet 'Ini ve [Get-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet 'ine yönelik kanalı kullanın.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-files"></a>Bir kurtarma noktası seçin (dosyaları geri yüklerken)

Yedekleme öğesinin tüm kurtarma noktalarını listelemek için [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet 'ini kullanın. Sonra geri yüklemek için kurtarma noktasını seçin. Hangi kurtarma noktasının kullanılacağı konusunda emin değilseniz, listede en son RecoveryPointType = Apptutarlı noktasını seçmek iyi bir uygulamadır.

Aşağıdaki betikte, **$RP** değişkeni, son yedi gün içindeki seçili yedekleme öğesi için bir kurtarma noktaları dizisidir. Dizi, dizin 0 ' daki en son kurtarma noktasıyla ters sırada sıralanır. Kurtarma noktasını seçmek için standart PowerShell dizisi dizinlemeyi kullanın. Örnekte, $rp [0] en son kurtarma noktasını seçer.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Çıktı aşağıdaki örneğe benzer:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>Diskleri geri yükleme

Bir yedekleme öğesinin verilerini ve yapılandırmasını bir kurtarma noktasına geri yüklemek için [restore-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) cmdlet 'ini kullanın. Bir kurtarma noktasını tanımladıktan sonra, **-RecoveryPoint** parametresinin değeri olarak kullanın. Yukarıdaki örnekte, **$RP [0]** kullanılacak kurtarma noktasıdır. Aşağıdaki örnek kodda, **$RP [0]** , diski geri yüklemek için kullanılacak kurtarma noktasıdır.

Diskleri ve yapılandırma bilgilerini geri yüklemek için:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Yönetilen diskleri geri yükleme

> [!NOTE]
> Desteklenen VM 'nin yönetilen diskleri varsa ve bunları yönetilen diskler olarak geri yüklemek istiyorsanız, Azure PowerShell RM Module v 6.7.0 ' den özelliği kullanıma sunduk. ten başlayarak.
>
>

Hangi yönetilen disklerin geri yükleneceği RG 'yi belirtmek için **Targetresourcegroupname** ek parametresini sağlayın.

> [!IMPORTANT]
> Önemli performans geliştirmeleriyle sonuçlandığından yönetilen diskleri geri yüklemek için **Targetresourcegroupname** parametresinin kullanılması önemle önerilir. Bu parametre verilmezse, anlık geri yükleme işlevlerinden faydalanabilirsiniz ve geri yükleme işlemi daha yavaş karşılaştırılacaktır. Amaç, yönetilen diskleri yönetilmeyen diskler olarak geri yüklemek için bu parametreyi sağlamaktan sonra, parametresini sağlayarak amaç seçimini yapın `-RestoreAsUnmanagedDisks` . `-RestoreAsUnmanagedDisks`Parametresi Azure PowerShell 3.7.0 onenlerden kullanılabilir. Gelecek sürümlerde, doğru geri yükleme deneyimi için bu parametrelerden birini sağlamak zorunlu olacaktır.
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

Dosyadaki **VMConfig.JS** depolama hesabına geri yüklenecek ve yönetilen diskler BELIRTILEN hedef RG öğesine geri yüklenecek.

Çıktı aşağıdaki örneğe benzer:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Geri yükleme işinin tamamlanmasını beklemek için [wait-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet 'ini kullanın.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Geri yükleme işi tamamlandıktan sonra, geri yükleme işleminin ayrıntılarını almak için [Get-AzRecoveryServicesBackupJobDetails](/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) cmdlet 'ini kullanın. JobDetails özelliği, VM 'yi yeniden derlemek için gereken bilgileri içerir.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

#### <a name="restore-selective-disks"></a>Seçmeli diskleri geri yükleme

Bir Kullanıcı, yedeklenen kümenin tamamı yerine birkaç diski seçmeli şekilde geri yükleyebilir. [Burada](selective-disk-backup-restore.md#restore-selective-disks-with-powershell)belgelenen tüm küme yerine yalnızca onları geri yüklemek için gerekli disk LUN 'larını parametre olarak sağlayın.

> [!IMPORTANT]
> Bunlardan biri seçmeli olarak disklere geri yüklemek için diskleri seçmeli olarak yedekleyecek. Daha fazla ayrıntı [burada](selective-disk-backup-restore.md#selective-disk-restore)verilmiştir.

Diskleri geri yükledikten sonra, VM 'yi oluşturmak için sonraki bölüme gidin.

#### <a name="restore-disks-to-a-secondary-region"></a>Diskleri ikincil bir bölgeye geri yükleme

VM 'lerinizi koruduğunuz kasada çapraz bölge geri yükleme etkinse, yedekleme verileri ikincil bölgeye çoğaltılır. Yedekleme verilerini kullanarak geri yükleme gerçekleştirebilirsiniz. İkincil bölgede geri yükleme tetiklenmesi için aşağıdaki adımları gerçekleştirin:

1. VM 'lerinizin korunduğu [kasa kimliğini getirin](#fetch-the-vault-id) .
1. [Geri yüklenecek doğru yedekleme öğesini](#select-the-vm-when-restoring-files)seçin.
1. Geri yüklemeyi gerçekleştirmek için kullanmak istediğiniz ikincil bölgede uygun kurtarma noktasını seçin.

    Bu adımı gerçekleştirmek için şu komutu çalıştırın:

    ```powershell
    $rp=Get-AzRecoveryServicesBackupRecoveryPoint -UseSecondaryRegion -Item $backupitem -VaultId $targetVault.ID
    $rp=$rp[0]
    ```

1. İkincil bölgede geri yükleme tetiklenmesi için [restore-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) cmdlet 'ini `-RestoreToSecondaryRegion` parametresiyle yürütün.

    Bu adımı gerçekleştirmek için şu komutu çalıştırın:

    ```powershell
    $restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -VaultLocation $targetVault.Location -RestoreToSecondaryRegion -RestoreOnlyOSDisk
    ```

    Çıkış aşağıdaki örneğe benzeyecektir:

    ```output
    WorkloadName     Operation             Status              StartTime                 EndTime          JobID
    ------------     ---------             ------              ---------                 -------          ----------
    V2VM             CrossRegionRestore   InProgress           4/23/2016 5:00:30 PM                       cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
    ```

1. Restore işini izlemek için parametresiyle [Get-AzRecoveryServicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) cmdlet 'ini yürütün `-UseSecondaryRegion` .

    Bu adımı gerçekleştirmek için şu komutu çalıştırın:

    ```powershell
    Get-AzRecoveryServicesBackupJob -From (Get-Date).AddDays(-7).ToUniversalTime() -To (Get-Date).ToUniversalTime() -UseSecondaryRegion -VaultId $targetVault.ID
    ```

    Çıkış aşağıdaki örneğe benzeyecektir:

    ```output
    WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
    ------------     ---------            ------               ---------                 -------                   -----
    V2VM             CrossRegionRestore   InProgress           2/8/2021 4:24:57 PM                                 2d071b07-8f7c-4368-bc39-98c7fb2983f7
    ```

## <a name="replace-disks-in-azure-vm"></a>Azure VM 'de diskleri değiştirme

Diskleri ve yapılandırma bilgilerini değiştirmek için aşağıdaki adımları gerçekleştirin:

* 1. Adım: [diskleri geri yükleme](backup-azure-vms-automation.md#restore-the-disks)
* 2. Adım: [PowerShell kullanarak veri diskini ayırma](../virtual-machines/windows/detach-disk.md#detach-a-data-disk-using-powershell)
* 3. Adım: [PowerShell Ile WINDOWS VM 'ye veri diski iliştirme](../virtual-machines/windows/attach-disk-ps.md)

## <a name="create-a-vm-from-restored-disks"></a>Geri yüklenen disklerden bir VM oluşturma

Diskleri geri yükledikten sonra, sanal makineyi diskten oluşturup yapılandırmak için aşağıdaki adımları kullanın.

> [!NOTE]
>
> 1. AzureAz modülü 3.0.0 veya üzeri gereklidir. <br>
> 2. Geri yüklenen disklerden şifrelenmiş VM 'Ler oluşturmak için Azure rolünüzün, **Microsoft. Keykasası/Vaults/Deploy/ACTION** eylemini gerçekleştirme izni olması gerekir. Rolünüzün bu izni yoksa, bu eylemle özel bir rol oluşturun. Daha fazla bilgi için bkz. [Azure özel rolleri](../role-based-access-control/custom-roles.md). <br>
> 3. Diskleri geri yükledikten sonra, artık yeni bir VM oluşturmak için doğrudan kullanabileceğiniz bir dağıtım şablonu edinebilirsiniz. Şifrelenmiş/şifrelenmemiş olan yönetilen/yönetilmeyen VM 'Ler oluşturmak için farklı PowerShell cmdlet 'lerinin olması gerekmez.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Dağıtım şablonunu kullanarak VM oluşturma

Sonuç iş ayrıntıları, sorgulanabilen ve dağıtılabilen şablon URI 'SI sağlar.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

Şablon, müşterinin depolama hesabı ve verilen kapsayıcı altında olduğundan doğrudan erişilebilir değildir. Bu şablona erişmek için URL 'nin tamamı (geçici bir SAS belirteci ile birlikte) gereklidir.

1. Önce templateBlobURI 'den şablon adını ayıklayın. Biçim aşağıda belirtilmiştir. Bu URL 'den son şablon adını ayıklamak için PowerShell 'de Split işlemini kullanabilirsiniz.

    ```http
    https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
    ```

2. Ardından tam URL [burada](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-powershell#provide-sas-token-during-deployment)açıklandığı gibi oluşturulabilir.

    ```powershell
    Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
    $templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
    ```

3. [Burada](../azure-resource-manager/templates/deploy-powershell.md)açıklandığı gibi yenı bir VM oluşturmak için şablonu dağıtın.

    ```powershell
    New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI
    ```

### <a name="create-a-vm-using-the-config-file"></a>Yapılandırma dosyasını kullanarak VM oluşturma

Aşağıdaki bölümde, "VMConfig" dosyasını kullanarak bir VM oluşturmak için gereken adımlar listelenmektedir.

> [!NOTE]
> VM oluşturmak için yukarıda ayrıntılı dağıtım şablonunu kullanmanız önemle önerilir. Bu bölüm (Işaret 1-6) yakında kullanım dışı bırakılacak.

1. İş ayrıntıları için geri yüklenen disk özelliklerini sorgulayın.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Azure depolama bağlamını ayarlayın ve JSON yapılandırma dosyasını geri yükleyin.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. VM yapılandırmasını oluşturmak için JSON yapılandırma dosyasını kullanın.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. İşletim sistemi diskini ve veri disklerini ekleyin. Bu adım, çeşitli yönetilen ve şifreli VM yapılandırmalarına yönelik örnekler sağlar. VM yapılandırmanıza uygun örneği kullanın.

    * **Yönetilmeyen ve şifreli olmayan VM 'ler** -yönetilmeyen, şifrelenmemiş VM 'ler için aşağıdaki örneği kullanın.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
            $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD ile yönetilmeyen ve şifreli VM 'ler (yalnızca bek)** -Azure AD ile yönetilmeyen ve şifrelenmiş VM 'ler için (yalnızca bek kullanılarak şifrelenir), diskleri iliştirebilmeniz için gizli anahtarı anahtar kasasına geri yüklemeniz gerekir. Daha fazla bilgi için [Azure Backup kurtarma noktasından şifrelenmiş bir sanal makineyi geri yükleme](backup-azure-restore-key-secret.md)bölümüne bakın. Aşağıdaki örnek, şifrelenmiş VM 'Ler için işletim sistemi ve veri disklerinin nasıl ekleneceğini gösterir. İşletim sistemi diskini ayarlarken ilgili işletim sistemi türünden bahsetdiğinizden emin olun.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD ile yönetilmeyen ve şifreli VM 'ler (bek ve kek)** -Azure AD ile yönetilmeyen ve şifrelenmiş VM 'ler IÇIN (bek ve kek kullanılarak şifrelenir), diskleri eklemeden önce anahtarı ve gizli anahtarı anahtar kasasına geri yükleyin. Daha fazla bilgi için bkz. [Azure Backup kurtarma noktasından şifrelenmiş bir sanal makineyi geri yükleme](backup-azure-restore-key-secret.md). Aşağıdaki örnek, şifrelenmiş VM 'Ler için işletim sistemi ve veri disklerinin nasıl ekleneceğini gösterir.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
        foreach($dd in $obj.'properties.storageProfile'.dataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD olmayan, yönetilmeyen ve şifreli VM 'ler (yalnızca bek)** -Azure AD olmayan, yönetilmeyen ve şifreli VM 'ler için (yalnızca bek kullanılarak şifrelenir), kaynak **Anahtar Kasası/gizli** anahtarı yoksa, [şifreli olmayan bir sanal makineyi Azure Backup kurtarma noktasından geri yükleme](backup-azure-restore-key-secret.md)bölümündeki yordamı kullanarak gizli dizileri Anahtar Kasası 'na geri yükleyin. Ardından, geri yüklenen işletim sistemi blobundan şifreleme ayrıntılarını ayarlamak için aşağıdaki komut dosyalarını yürütün (bir veri blobu için bu adım gerekli değildir). $Dekurl, geri yüklenen Keykasasından getirilebilir.

    Aşağıdaki betiğin yalnızca kaynak Keykasası/gizli dizi kullanılamadığında yürütülmesi gerekir.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    Gizli dizileri **kullanılabilir** olduktan ve şifreleme ayrıntıları Işletim sistemi blobu üzerinde de ayarlandıktan sonra, diskleri aşağıda verilen betiği kullanarak ekleyin.

    Kaynak Keykasası/gizlilikler zaten kullanılabiliyorsa yukarıdaki betiğin yürütülmesi gerekmez.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Azure AD (bek ve kek) olmadan yönetilmeyen ve şifrelenmiş sanal makineler** -Azure AD olmayan, yönetilmeyen ve şifreli VM 'ler IÇIN (bek & kek kullanılarak şifrelenir), kaynak **keykasası/anahtar/gizli** anahtar yoksa, [şifreli olmayan bir sanal makineyi Azure Backup kurtarma noktasından geri yükleme](backup-azure-restore-key-secret.md)bölümündeki yordamı kullanarak anahtarı ve gizli dizileri Anahtar Kasası 'na geri yükleyin. Ardından, geri yüklenen işletim sistemi blobundan şifreleme ayrıntılarını ayarlamak için aşağıdaki komut dosyalarını yürütün (bir veri blobu için bu adım gerekli değildir). $Dekurl ve $kekurl, geri yüklenen Keykasasından getirilebilir.

    Aşağıdaki komut dosyasının yalnızca kaynak Keykasası/anahtar/parola kullanılabilir olmadığında yürütülmesi gerekir.

    ```powershell
        $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
        $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
        $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
        $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
        $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
        $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
        $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
        $osBlob.ICloudBlob.SetMetadata()
    ```

    **Anahtar/gizli dizileri kullanılabilir** olduktan ve şifreleme ayrıntıları Işletim sistemi blobu üzerinde ayarlandıktan sonra, aşağıda verilen betiği kullanarak diskleri bağlayın.

    Kaynak Keykasası/anahtar/gizli dizileri varsa yukarıdaki betiğin yürütülmesi gerekmez.

    ```powershell
        Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
        $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
        foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
        {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
        }
    ```

    * **Yönetilen ve şifreli olmayan VM 'ler** -yönetilen şifreli olmayan VM 'ler için, geri yüklenen yönetilen diskleri iliştirin. Ayrıntılı bilgi için bkz. [PowerShell kullanarak bir WINDOWS VM 'ye veri diski iliştirme](../virtual-machines/windows/attach-disk-ps.md).

    * **Azure AD Ile yönetilen ve şifrelenmiş VM 'ler (yalnızca bek)** -Azure AD ile yönetilen şifrelenmiş VM 'ler için (yalnızca bek kullanılarak şifrelenir), geri yüklenen yönetilen diskleri bağlayın. Ayrıntılı bilgi için bkz. [PowerShell kullanarak bir WINDOWS VM 'ye veri diski iliştirme](../virtual-machines/windows/attach-disk-ps.md).

    * **Azure AD Ile yönetilen ve şifrelenmiş VM 'ler (bek ve kek)** -Azure AD ile yönetilen şifreli VM 'ler IÇIN (bek ve kek kullanılarak şifrelenir), geri yüklenen yönetilen diskleri iliştirin. Ayrıntılı bilgi için bkz. [PowerShell kullanarak bir WINDOWS VM 'ye veri diski iliştirme](../virtual-machines/windows/attach-disk-ps.md).

    * **Azure AD olmayan yönetilen ve şifrelenmiş VM 'ler (yalnızca bek)** -Azure AD olmayan yönetilen ve şifrelenmiş VM 'ler için (yalnızca bek kullanılarak şifrelenir), kaynak **keykasası/gizli** anahtarı yoksa, [şifreli olmayan bir sanal makineyi Azure Backup kurtarma noktasından geri yükleme](backup-azure-restore-key-secret.md)bölümündeki yordamı kullanarak gizli dizileri Anahtar Kasası 'na geri yükleyin. Ardından, geri yüklenen işletim sistemi diskinde şifreleme ayrıntılarını ayarlamak için aşağıdaki komut dosyalarını yürütün (bir veri diski için bu adım gerekli değildir). $Dekurl, geri yüklenen Keykasasından getirilebilir.

    Aşağıdaki betiğin yalnızca kaynak Keykasası/gizli dizi kullanılamadığında yürütülmesi gerekir.  

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Gizli dizileri kullanılabilir olduktan ve şifreleme ayrıntıları işletim sistemi diskinde ayarlandıktan sonra, geri yüklenen yönetilen diskleri eklemek için bkz. [PowerShell kullanarak bir WINDOWS sanal makinesine veri diski iliştirme](../virtual-machines/windows/attach-disk-ps.md).

    * **Azure AD olmayan yönetilen ve şifrelenmiş VM 'ler (bek ve kek)** -kaynak **Anahtar Kasası/anahtar/gizli** anahtar yoksa, Azure AD olmayan şifreli VM 'ler IÇIN (bek & kek kullanılarak şifrelenir),, [şifreli olmayan bir sanal makineyi Azure Backup kurtarma noktasından geri yükleme](backup-azure-restore-key-secret.md)bölümündeki yordamı kullanarak anahtarı ve gizli dizileri Anahtar Kasası 'na geri yükleyin. Ardından, geri yüklenen işletim sistemi diskinde şifreleme ayrıntılarını ayarlamak için aşağıdaki komut dosyalarını yürütün (veri diskleri için bu adım gerekli değildir). $Dekurl ve $kekurl, geri yüklenen Keykasasından getirilebilir.

    Aşağıdaki betiğin yalnızca kaynak Keykasası/anahtar/parola kullanılabilir olmadığında yürütülmesi gerekir.

    ```powershell
    $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
    $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
    $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
    $diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
    $encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
    $encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
    $encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
    $encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
    $encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
    $diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
    Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
    ```

    Anahtar/gizli dizi kullanılabilir olduğunda ve şifreleme ayrıntıları işletim sistemi diskinde ayarlandıktan sonra, geri yüklenen yönetilen diskleri eklemek için bkz. [PowerShell kullanarak bir WINDOWS sanal makinesine veri diski iliştirme](../virtual-machines/windows/attach-disk-ps.md).

5. Ağ ayarlarını ayarlayın.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Sanal makineyi oluşturun.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. ADE uzantısı gönder.
   ADE uzantıları itilmemişse, veri diskleri şifrelenmemiş olarak işaretlenir, bu nedenle aşağıdaki adımların yürütülmesi zorunludur:

   * **Azure AD Ile VM için** -veri diskleri şifrelemesini el ile etkinleştirmek için aşağıdaki komutu kullanın  

     **Yalnızca BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK ve KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Azure AD olmayan VM için** -veri disklerinin şifrelemesini el ile etkinleştirmek için aşağıdaki komutu kullanın.

     Komut yürütme sırasında Aadclientıd istediğinde, Azure PowerShell güncelleştirmeniz gerekir.

     **Yalnızca BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK ve KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Şifrelenmiş VM geri yükleme diski işleminin bir parçası olarak oluşturulan JASON dosyalarını el ile silmenin emin olun.

## <a name="restore-files-from-an-azure-vm-backup"></a>Azure VM yedeğinden dosyaları geri yükleme

Diskleri geri yüklemenin yanı sıra, Azure VM yedeklemesinden tek tek dosyaları da geri yükleyebilirsiniz. Dosyaları geri yükleme işlevselliği, bir kurtarma noktasındaki tüm dosyalara erişim sağlar. Dosyaları, normal dosyalar için yaptığınız gibi dosya Gezgini aracılığıyla yönetin.

Azure VM yedeğinden bir dosyayı geri yüklemenin temel adımları şunlardır:

* VM 'yi seçin
* Bir kurtarma noktası seçin
* Kurtarma noktası disklerini bağlama
* Gerekli dosyaları kopyalayın
* Diski çıkarın

### <a name="select-the-vm-when-restoring-the-vm"></a>VM 'yi seçin (VM geri yüklenirken)

Doğru yedekleme öğesini tanımlayan PowerShell nesnesini almak için, kasadaki kapsayıcıdan başlayın ve nesne hiyerarşisinde bir şekilde çalışın. VM 'yi temsil eden kapsayıcıyı seçmek için [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) cmdlet 'Ini ve [Get-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) cmdlet 'ine yönelik kanalı kullanın.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point-when-restoring-the-vm"></a>Bir kurtarma noktası seçin (VM geri yüklenirken)

Yedekleme öğesinin tüm kurtarma noktalarını listelemek için [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) cmdlet 'ini kullanın. Sonra geri yüklemek için kurtarma noktasını seçin. Hangi kurtarma noktasının kullanılacağı konusunda emin değilseniz, listede en son RecoveryPointType = Apptutarlı noktasını seçmek iyi bir uygulamadır.

Aşağıdaki betikte, **$RP** değişkeni, son yedi gün içindeki seçili yedekleme öğesi için bir kurtarma noktaları dizisidir. Dizi, dizin 0 ' daki en son kurtarma noktasıyla ters sırada sıralanır. Kurtarma noktasını seçmek için standart PowerShell dizisi dizinlemeyi kullanın. Örnekte, $rp [0] en son kurtarma noktasını seçer.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

Çıktı aşağıdaki örneğe benzer:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>Kurtarma noktası disklerini bağlama

Kurtarma noktasındaki tüm diskleri bağlamak üzere betiği almak için [Get-Azrecoveryservicesbackuprpbağlamabetiği](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript) cmdlet 'ini kullanın.

> [!NOTE]
> Diskler, betiğin çalıştırıldığı makineye Iscsı ekli diskler olarak bağlanır. Bağlama hemen gerçekleşir ve ücretlendirilmezsiniz.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

Çıktı aşağıdaki örneğe benzer:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Dosyaları kurtarmak istediğiniz makinede betiği çalıştırın. Betiği yürütmek için, girilen parolayı girmeniz gerekir. Diskler eklendikten sonra, yeni birimlere ve dosyalara gitmek için Windows Dosya Gezgini 'ni kullanın. Daha fazla bilgi için bkz. yedekleme makalesi, [Azure sanal makine yedeğinden dosya kurtarma](backup-azure-restore-files-from-vm.md).

### <a name="unmount-the-disks"></a>Diskleri çıkarın

Gerekli dosyalar kopyalandıktan sonra, diskleri çıkarmak için [Disable-Azrecoveryservicesbackuprpbağlamascrıpt](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) komutunu kullanın. Kurtarma noktası dosyalarına erişmek için diskleri çıkardığınızdan emin olun.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>Sonraki adımlar

PowerShell 'i Azure kaynaklarınızla birlikte çalışmak üzere kullanmayı tercih ediyorsanız, [Windows Server Için Yedekleme dağıtımı ve yönetimi](backup-client-automation.md)PowerShell makalesine bakın. DPM yedeklemelerini yönetiyorsanız, [DPM Için yedeklemeyi dağıtma ve yönetme](backup-dpm-automation.md)makalesine bakın.
