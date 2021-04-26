---
title: Azure yönetilen diskleri Azure PowerShell aracılığıyla geri yükleme
description: Azure PowerShell kullanarak Azure yönetilen disklerini geri yüklemeyi öğrenin.
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: c6625b43c313d45d4b295dd406e29a2b1d85b387
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520046"
---
# <a name="restore-azure-managed-disks-using-azure-powershell"></a>Azure PowerShell kullanarak Azure yönetilen disklerini geri yükleme

Bu makalede, [Azure yönetilen disklerinin](../virtual-machines/managed-disks-overview.md) Azure Backup tarafından oluşturulan bir geri yükleme noktasından nasıl geri yükleneceği açıklanmaktadır.

Şu anda, yedeklemelerin alındığı kaynak diski değiştirerek geri yükleme Original-Location kurtarma (OLR) seçeneği desteklenmez. Aynı kaynak grubunda yedeklerin alındığı kaynak diskle veya başka bir kaynak grubunda yeni bir disk oluşturmak için bir kurtarma noktasından geri yükleme yapabilirsiniz. Bu, Alternate-Location kurtarma (ALR) olarak bilinir ve bu, hem kaynak diski hem de geri yüklenen (yeni) diski tutmaya yardımcı olur.

Bu makalede şunları yapmayı öğreneceksiniz:

- Yeni bir disk oluşturmak için geri yükleme

- Geri yükleme işlemi durumunu izleme

Örneklerde "testBkpVaultRG" kaynak grubu altında "Testbkpkasa" adlı mevcut bir yedekleme kasasına başvuracağız

```azurepowershell-interactive
$TestBkpVault = Get-AzDataProtectionBackupVault -VaultName TestBkpVault -ResourceGroupName "testBkpVaultRG"
```

## <a name="restore-to-create-a-new-disk"></a>Yeni bir disk oluşturmak için geri yükleme

### <a name="setting-up-permissions"></a>İzinleri ayarlama

Yedekleme Kasası, diğer Azure kaynaklarına erişmek için yönetilen kimlik kullanır. Yedekten geri yüklemek için, yedekleme kasasının yönetilen kimliği, diskin geri yükleneceği kaynak grubunda bir izin kümesi gerektirir.

Yedekleme Kasası, kaynak başına bir tane ile kısıtlanan ve bu kaynağın yaşam döngüsüne bağlı olan sistem tarafından atanmış bir yönetilen kimlik kullanır. Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak yönetilen kimliğe izin verebilirsiniz. Yönetilen kimlik, yalnızca Azure kaynaklarıyla kullanılabilecek özel bir türün hizmet sorumlusundan oluşur. [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)hakkında daha fazla bilgi edinin.

[Burada](restore-managed-disks.md#restore-to-create-a-new-disk)belirtildiği gibi, disklerin geri yükleneceği/oluşturulacağı hedef kaynak grubundaki kasasının sistem tarafından atanan yönetilen kimliği için ilgili izinleri atayın.

### <a name="fetching-the-relevant-recovery-point"></a>İlgili kurtarma noktası getiriliyor

[Get-Azdataprotectionbackupınstance](/powershell/module/az.dataprotection/get-azdataprotectionbackupinstance?view=azps-5.7.0&preserve-view=true) komutunu kullanarak tüm örnekleri getirin ve ilgili örneği belirler.

```azurepowershell-interactive
$AllInstances = Get-AzDataProtectionBackupInstance -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name
```

Birçok kasadaki ve aboneliklerdeki örnekleri aramak için **az. Resourcegraph** ve [Search-Azdataprotectionbackupınstanceınazgraf](/powershell/module/az.dataprotection/search-azdataprotectionbackupinstanceinazgraph?view=azps-5.7.0&preserve-view=true) komutunu da kullanabilirsiniz.

```azurepowershell-interactive
$AllInstances = Search-AzDataProtectionBackupInstanceInAzGraph -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -DatasourceType AzureDisk -ProtectionStatus ProtectionConfigured
```

Örnek tanımlandıktan sonra ilgili kurtarma noktasını getirin.

```azurepowershell-interactive
$rp = Get-AzDataProtectionRecoveryPoint -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -BackupInstanceName $AllInstances[2].BackupInstanceName
```

### <a name="preparing-the-restore-request"></a>Geri yükleme isteği hazırlanıyor

Hedef kaynak grubuyla oluşturulacak yeni diskin ARM KIMLIĞINI, [yukarıda](#setting-up-permissions)ayrıntılı olarak atanmış izinleri ve gerekli disk adını oluşturun. Örneğin, bir disk, farklı bir abonelikle **targetrg** kaynak grubu altında **PSTestDisk2** olarak adlandırılabilir.

```azurepowershell-interactive
$targetDiskId = /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx/resourceGroups/targetrg/providers/Microsoft.Compute/disks/PSTestDisk2
```

Restore isteğini tüm ilgili ayrıntılarla hazırlamak için [Initialize-AzDataProtectionRestoreRequest](/powershell/module/az.dataprotection/initialize-azdataprotectionrestorerequest?view=azps-5.7.0&preserve-view=true) komutunu kullanın.

```azurepowershell-interactive
$restorerequest = Initialize-AzDataProtectionRestoreRequest -DatasourceType AzureDisk -SourceDataStore OperationalStore -RestoreLocation $TestBkpVault.Location  -RestoreType AlternateLocation -TargetResourceId $targetDiskId -RecoveryPoint $rp[0].Name
```

### <a name="trigger-the-restore"></a>Geri yüklemeyi tetikleme

Geri yüklemeyi, yukarıda hazırlanan istekle birlikte tetiklemek için [Start-Azdataprotectionbackupınstanceresger](/powershell/module/az.dataprotection/start-azdataprotectionbackupinstancerestore?view=azps-5.7.0&preserve-view=true) komutunu kullanın.

```azurepowershell-interactive
Start-AzDataProtectionBackupInstanceRestore -BackupInstanceName $AllInstances[2].BackupInstanceName -ResourceGroupName "testBkpVaultRG" -VaultName $TestBkpVault.Name -Parameter $restorerequest
```

## <a name="tracking-job"></a>İzleme işi

[Get-AzDataProtectionJob](/powershell/module/az.dataprotection/get-azdataprotectionjob?view=azps-5.7.0&preserve-view=true) komutunu kullanarak tüm işleri izleyin. Tüm işleri listeleyebilir ve belirli bir iş ayrıntısı getirebilirsiniz.

Tüm yedekleme kasaları genelinde tüm işleri izlemek için **az. ResourceGraph** komutunu da kullanabilirsiniz. Herhangi bir yedekleme Kasası genelinde olabilecek ilgili işi almak için [Search-Azdataprotectionjobınazgraph](/powershell/module/az.dataprotection/search-azdataprotectionjobinazgraph?view=azps-5.7.0&preserve-view=true) komutunu kullanın.

```azurepowershell-interactive
$job = Search-AzDataProtectionJobInAzGraph -Subscription $sub -ResourceGroupName "testBkpVaultRG" -Vault $TestBkpVault.Name -DatasourceType AzureDisk -Operation OnDemandBackup
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure disk yedekleme hakkında SSS](disk-backup-faq.yml)
