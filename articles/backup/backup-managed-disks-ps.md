---
title: Azure PowerShell kullanarak Azure yönetilen disklerini yedekleme
description: Azure PowerShell kullanarak Azure yönetilen disklerini nasıl yedekleyeceğinizi öğrenin.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 2e286128185c1ac7fe4861a9b06de52e10d4139a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630592"
---
# <a name="back-up-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell kullanarak Azure yönetilen disklerini yedekleme

Bu makalede, Azure PowerShell kullanarak [Azure yönetilen diskinin](../virtual-machines/managed-disks-overview.md) nasıl yedekleneceği açıklanır.

Bu makalede şunları yapmayı öğreneceksiniz:

- Yedekleme Kasası oluşturma

- Yedekleme ilkesi oluşturma

- Azure diskinin yedeğini yapılandırma

- İsteğe bağlı yedekleme işi çalıştırma

Azure disk yedekleme bölgesi kullanılabilirliği, desteklenen senaryolar ve sınırlamalar hakkında bilgi için bkz. [destek matrisi](disk-backup-support-matrix.md).

## <a name="create-a-backup-vault"></a>Yedekleme Kasası oluşturma

Yedekleme Kasası, Azure 'da PostgreSQL sunucuları ve Azure diskleri için Azure veritabanı gibi Azure Backup desteklediği çeşitli yeni iş yükleri için yedekleme verileri tutan bir depolama varlıktır. Yedekleme kasaları, yedekleme verilerinizi düzenlemenizi kolaylaştırır, ancak yönetim yükünü en aza indirir. Yedekleme kasaları, yedekleme verilerini güvenli hale getirmeye yardımcı olmak için gelişmiş yetenekler sağlayan Azure Azure Resource Manager modelini temel alır.

Bir yedekleme Kasası oluşturmadan önce, kasadaki verilerin depolama yedekliliği ' ni seçin. Ardından, bu depolama yedekliği ve konumuyla birlikte Yedekleme Kasası oluşturmaya devam edin. Bu makalede, "testBkpVaultRG" kaynak grubunda "westus" bölgesinde "Testbkpkasa" adlı bir yedekleme Kasası oluşturacağız. Bir yedekleme Kasası oluşturmak için [New-Azdataprotectionbackupkasası](/powershell/module/az.dataprotection/new-azdataprotectionbackupvault?view=azps-5.7.0&preserve-view=true) komutunu kullanın. [Yedekleme Kasası oluşturma](./backup-vault-overview.md#create-a-backup-vault)hakkında daha fazla bilgi edinin.

```azurepowershell-interactive
$storageSetting = New-AzDataProtectionBackupVaultStorageSettingObject -Type LocallyRedundant/GeoRedundant -DataStoreType VaultStore
New-AzDataProtectionBackupVault -ResourceGroupName testBkpVaultRG -VaultName TestBkpVault -Location westus -StorageSetting $storageSetting
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault
$TestBKPVault | fl
ETag                :
Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/testBkpVaultRG/providers/Microsoft.DataProtection/backupVaults/TestBkpVault
Identity            : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppIdentityDetails
IdentityPrincipalId :
IdentityTenantId    :
IdentityType        :
Location            : westus
Name                : TestBkpVault
ProvisioningState   : Succeeded
StorageSetting      : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.StorageSetting}
SystemData          : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SystemData
Tag                 : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.DppTrackedResourceTags
Type                : Microsoft.DataProtection/backupVaults
```

Kasa oluşturulduktan sonra Azure disklerini korumak için bir yedekleme ilkesi oluşturalım.

## <a name="create-a-backup-policy"></a>Yedekleme ilkesi oluşturma

Azure disk yedekleme için bir yedekleme ilkesinin iç bileşenlerini anlamak üzere [Get-AzDataProtectionPolicyTemplate](/powershell/module/az.dataprotection/get-azdataprotectionpolicytemplate?view=azps-5.7.0&preserve-view=true)komutunu kullanarak ilke şablonunu alın. Bu komut, belirli bir veri kaynağı türü için varsayılan bir ilke şablonu döndürür. Yeni bir ilke oluşturmak için bu ilke şablonunu kullanın.

```azurepowershell-interactive
$policyDefn = Get-AzDataProtectionPolicyTemplate -DatasourceType AzureDisk
$policyDefn | fl


DatasourceType : {Microsoft.Compute/disks}
ObjectType     : BackupPolicy
PolicyRule     : {BackupHourly, Default}

$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

İlke şablonu bir tetikleyiciden (yedeklemenin ne kadar tetikleyeceğini karar verir) ve yaşam döngüsünün (yedeklemenin ne zaman silineceğini/kopyalanacağını/taşınacağını belirler) oluşur. Azure disk yedekleme 'de, tetikleyici için varsayılan değer her 4 saatte bir zamanlanmış saatlik tetikleyiciden (PT4H) ve her yedeklemeyi 7 gün boyunca korur.

```azurepowershell-interactive
 $policyDefn.PolicyRule[0].Trigger | fl


ObjectType                    : ScheduleBasedTriggerContext
ScheduleRepeatingTimeInterval : {R/2020-04-05T13:00:00+00:00/PT4H}
TaggingCriterion              : {Default}
```

```azurepowershell-interactive
$policyDefn.PolicyRule[1].Lifecycle | fl


DeleteAfterDuration        : P7D
DeleteAfterObjectType      : AbsoluteDeleteOption
SourceDataStoreObjectType  : DataStoreInfoBase
SourceDataStoreType        : OperationalStore
TargetDataStoreCopySetting :
```

Azure disk yedekleme günde birden çok yedekleme sunar. Daha sık yedeklemeler gerekiyorsa, **saatlik** yedekleme sıklığını her 4, 6, 8 veya 12 saat aralıklarıyla yedeklemeler alma özelliğiyle birlikte seçin. Yedeklemeler, seçilen **zaman** aralığı temelinde zamanlanır. Örneğin, **her 4 saatte** bir seçeneğini belirlerseniz yedeklemeler günde eşit olarak dağıtılırsa yedeklemeler her 4 saatte bir zaman aralığında alınır. Günde bir kez yedekleme yeterliyse **günlük** yedekleme sıklığını seçin. Günlük yedekleme sıklığında, yedeklemelerinizin alındığı günün saatini belirtebilirsiniz. Gün zamanının yedeklemenin tamamlandığı zaman değil, yedekleme başlangıç saatini gösterdiğine dikkat edin. Yedekleme işleminin tamamlanması için gereken süre, diskin boyutu ve ardışık yedeklemeler arasındaki dalgalanma oranı dahil olmak üzere çeşitli faktörlere bağımlıdır. Ancak, Azure disk yedekleme, üretim uygulaması performansını etkilemeyen [Artımlı anlık görüntüleri](../virtual-machines/disks-incremental-snapshots.md)kullanan aracısız bir yedeğiniz.

   >[!NOTE]
   > Seçili kasadaki küresel artıklık ayarı olabilir, ancak şu anda Azure disk yedekleme yalnızca anlık görüntü veri deposunu destekler. Tüm yedeklemeler aboneliğinizdeki bir kaynak grubunda depolanır ve Yedekleme Kasası depolamasına kopyalanmaz.

İlke oluşturma hakkında daha fazla bilgi edinmek için [Azure disk yedekleme ilkesi](backup-managed-disks.md#create-backup-policy) belgesine bakın.

Saatlik sıklığı veya bekletme dönemini düzenlemek istiyorsanız, [Edit-Azdataprotectionpolicytriggerclienentobject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicytriggerclientobject?view=azps-5.7.0&preserve-view=true) ve/veya [Edit-AzDataProtectionPolicyRetentionRuleClientObject](/powershell/module/az.dataprotection/edit-azdataprotectionpolicyretentionruleclientobject?view=azps-5.7.0&preserve-view=true) komutlarını kullanın. İlke nesnesi istenen tüm değerlere sahip olduktan sonra, [Yeni-AzDataProtectionBackupPolicy](/powershell/module/az.dataprotection/new-azdataprotectionbackuppolicy?view=azps-5.7.0&preserve-view=true)öğesini kullanarak ilke nesnesinden yeni bir ilke oluşturmaya devam edin.

```azurepowershell-interactive
New-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name diskBkpPolicy -Policy $policyDefn

Name                   Type
----                   ----
diskBkpPolicy       Microsoft.DataProtection/backupVaults/backupPolicies

$diskBkpPol = Get-AzDataProtectionBackupPolicy -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "diskBkpPolicy"
```

## <a name="configure-backup"></a>Yedeklemeyi yapılandırma

Kasa ve ilke oluşturulduktan sonra, kullanıcının bir Azure diskini korumak için göz önünde bulundurmanız gereken 3 önemli noktası vardır.

### <a name="key-entities-involved"></a>Dahil edilen temel varlıklar

#### <a name="disk-to-be-protected"></a>Korunacak disk

Korunacak diskin ARM KIMLIĞINI getirin. Bu, diskin tanımlayıcısı olarak görev yapar. Farklı bir abonelik altında "diskrg" kaynak grubu altında "PSTestDisk" adlı bir disk örneği kullanacağız.

```azurepowershell-interactive
$DiskId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourcegroups/diskrg/providers/Microsoft.Compute/disks/PSTestDisk"
```

#### <a name="snapshot-resource-group"></a>Anlık görüntü kaynak grubu

Disk anlık görüntüleri, aboneliğinizde içindeki bir kaynak grubunda depolanır. Bir kılavuz olarak, Azure Backup hizmeti tarafından kullanılacak anlık görüntü veri deposu olarak adanmış bir kaynak grubu oluşturmanız önerilir. Ayrılmış bir kaynak grubuna sahip olmak, kaynak grubunda erişim izinlerinin kısıtlanması, böylece yedekleme verilerinin güvenliği ve yönetimi kolaylığı sağlar. Disk anlık görüntülerini yerleştirmek istediğiniz kaynak grubu için ARM KIMLIĞINI aklınızda yapın

```azurepowershell-interactive
$snapshotrg = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/snapshotrg"
```

#### <a name="backup-vault"></a>Yedekleme kasası

Yedekleme kasaları, anlık görüntüleri tetikleyebilmek ve yaşam döngüsünü yönetebilmesi için disk ve anlık görüntü kaynak grubunun izinlerini gerektirir. Kasanın sistem tarafından atanan yönetilen kimliği, bu tür izinleri atamak için kullanılır. Kurtarma Hizmetleri Kasası için sistem tarafından atanan yönetilen kimliği etkinleştirmek üzere [Update-Azrecoveryserviceskasa](/powershell/module/az.recoveryservices/update-azrecoveryservicesvault?view=azps-5.7.0&preserve-view=true) komutunu kullanın.

### <a name="assign-permissions"></a>İzinler atama

Kullanıcının RBAC-kasasından (kasa MSI tarafından temsil edilen) ve ilgili disk ve/veya disk RG aracılığıyla birkaç izin ataması gerekir. Bu, portal veya PowerShell aracılığıyla gerçekleştirilebilir. İlgili tüm izinler, [Bu bölümde](backup-managed-disks.md#configure-backup)1, 2, 3. noktalara göre ayrıntılıdır.

### <a name="prepare-the-request"></a>İsteği hazırla

Tüm ilgili izinler ayarlandıktan sonra, yedekleme yapılandırması 2 adımda gerçekleştirilir. İlk olarak, [Initialize-Azdataprotectionbackupınstance](/powershell/module/az.dataprotection/initialize-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) komutunu kullanarak ilgili kasa, ilke, disk ve anlık görüntü kaynak grubunu kullanarak ilgili isteği hazırlarız. Ardından, [New-Azdataprotectionbackupınstance](/powershell/module/az.dataprotection/new-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) komutunu kullanarak diski koruma isteği gönderiyoruz.

```azurepowershell-interactive
$instance = Initialize-AzDataProtectionBackupInstance -DatasourceType AzureDisk -DatasourceLocation $TestBkpvault.Location -PolicyId $diskBkpPol[0].Id -DatasourceId $DiskId 
$instance.Property.PolicyInfo.PolicyParameter.DataStoreParametersList[0].ResourceGroupId = $snapshotrg
New-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstance $instance

Name                                                       Type                                                  BackupInstanceName
----                                                       ----                                                  ------------------
diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166 Microsoft.DataProtection/backupVaults/backupInstances diskrg-PSTestDisk-3df6ac08-9496-4839-8fb5-8b78e594f166
```

## <a name="run-an-on-demand-backup"></a>İsteğe bağlı yedekleme çalıştırma

Kullanıcının [Get-Azdataprotectionbackupınstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) kullanarak bir yedekleme tetikleyebileceği ilgili yedekleme örneğini getir

```azurepowershell-interactive
$instance = Get-AzDataProtectionBackupInstance -SubscriptionId "xxxx-xxx-xxx" -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Name "BackupInstanceName"
```

Yedeklemeyi tetiklerken bir bekletme kuralı belirtebilirsiniz. İlke ' de bekletme kurallarını görüntülemek için, saklama kuralları için İlke nesnesinde ilerleyin. Aşağıdaki örnekte, ' default ' adlı Kural görüntülenir ve bu kuralı isteğe bağlı yedekleme için kullanacağız

```azurepowershell-interactive
$policyDefn.PolicyRule | fl


BackupParameter           : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.AzureBackupParams
BackupParameterObjectType : AzureBackupParams
DataStoreObjectType       : DataStoreInfoBase
DataStoreType             : OperationalStore
Name                      : BackupHourly
ObjectType                : AzureBackupRule
Trigger                   : Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.ScheduleBasedTriggerContext
TriggerObjectType         : ScheduleBasedTriggerContext

IsDefault  : True
Lifecycle  : {Microsoft.Azure.PowerShell.Cmdlets.DataProtection.Models.Api20210201Preview.SourceLifeCycle}
Name       : Default
ObjectType : AzureRetentionRule
```

[Backup-Azdataprotectionbackupınstancegeçici](/powershell/module/az.dataprotection/backup-azdataprotectionbackupinstanceadhoc?view=azps-5.7.0&preserve-view=true) komutunu kullanarak isteğe bağlı yedekleme tetikleyin.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
Backup-AzDataProtectionBackupInstanceAdhoc -BackupInstanceName $AllInstances[0].Name -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupRuleOptionRuleName "Default"
```

## <a name="tracking-jobs"></a>İşleri izleme

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) komutunu kullanarak tüm işleri izleyin. Tüm işleri listeleyebilir ve belirli bir iş ayrıntısı getirebilirsiniz.

Tüm yedekleme kasaları genelinde tüm işleri izlemek için az. ResourceGraph komutunu da kullanabilirsiniz. Herhangi bir yedekleme Kasası genelinde olabilecek ilgili işi almak için [Search-Azdataprotectionjobınazgraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) komutunu kullanın.

```azurepowershell-interactive
  $job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell kullanarak Azure yönetilen disklerini geri yükleme](restore-managed-disks-ps.md)
