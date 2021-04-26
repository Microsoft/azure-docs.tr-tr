---
title: Azure sanal makineleri için seçmeli disk yedekleme ve geri yükleme
description: Bu makalede, Azure sanal makine yedekleme çözümünü kullanarak Seçmeli disk yedekleme ve geri yükleme hakkında bilgi edinin.
ms.topic: conceptual
ms.date: 07/17/2020
ms.custom: references_regions , devx-track-azurecli
ms.openlocfilehash: e82c959dc63222e8565243cc9ac805283cab6617
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501843"
---
# <a name="selective-disk-backup-and-restore-for-azure-virtual-machines"></a>Azure sanal makineleri için seçmeli disk yedekleme ve geri yükleme

Azure Backup, sanal makine yedekleme çözümünü kullanarak bir VM 'deki tüm disklerin (işletim sistemi ve veri) yedeklenmesini destekler. Artık, seçmeli diskler Yedekleme ve geri yükleme işlevselliğini kullanarak bir VM 'deki veri disklerinin bir alt kümesini yedekleyebilirsiniz. Bu, yedekleme ve geri yükleme gereksinimleriniz için verimli ve ekonomik bir çözüm sunar. Her kurtarma noktası yalnızca yedekleme işlemine dahil edilen diskleri içerir. Bu, geri yükleme işlemi sırasında verilen kurtarma noktasından geri yüklenen disklerin bir alt kümesine sahip etmenize olanak tanır. Bu, hem anlık görüntülerle hem de kasadan geri yükleme için geçerlidir.

## <a name="scenarios"></a>Senaryolar

Bu çözüm özellikle aşağıdaki senaryolarda yararlı olur:

1. Yalnızca bir diskte veya disklerin bir alt kümesinde yedeklenmek üzere kritik verileriniz varsa ve yedekleme depolama maliyetlerini en aza indirmek için bir VM 'ye bağlı olan disklerin geri kalanını yedeklemek istemiyorsanız.
2. VM 'niz veya verileriniz için başka bir yedekleme çözümleriniz varsa. Örneğin, farklı bir iş yükü yedekleme çözümü kullanarak veritabanlarınızı veya verilerinizi yedekletirseniz ve kullanılabilir en iyi özellikleri kullanarak verimli ve sağlam bir sistem oluşturmak için Azure VM düzeyinde yedekleme 'yi kullanmak istiyorsanız

PowerShell veya Azure CLı kullanarak, Azure VM 'nin seçmeli disk yedeklemesini yapılandırabilirsiniz.  Bir komut dosyası kullanarak, LUN numaralarını kullanarak veri disklerini dahil edebilir veya dışlayabilirsiniz.  Şu anda, Azure portal aracılığıyla seçmeli diskler yedeklemesini yapılandırma özelliği **yalnızca yedekleme işletim sistemi diski** seçeneğiyle sınırlıdır. Böylece, işletim sistemi diski ile Azure VM 'nizin yedeklemesini yapılandırabilir ve ona bağlı tüm veri disklerini dışlayabilirsiniz.

>[!NOTE]
> İşletim sistemi diski, varsayılan olarak VM yedeklemesine eklenir ve dışlanamaz.

## <a name="using-azure-cli"></a>Azure CLI’yı kullanma

Az CLı Version 2.0.80 veya üstünü kullandığınızdan emin olun. CLı sürümünü şu komutla edinebilirsiniz:

```azurecli
az --version
```

Kurtarma Hizmetleri kasasının ve VM 'nin bulunduğu abonelik KIMLIĞINDE oturum açın:

```azurecli
az account set -s {subscriptionID}
```

>[!NOTE]
>Aşağıdaki her komutta kasaya karşılık gelen yalnızca **resourcegroup** adı (nesne değil) gereklidir.

### <a name="configure-backup-with-azure-cli"></a>Azure CLı ile yedeklemeyi yapılandırma

Koruma yapılandırma işlemi sırasında, bir **içerme** dışlama parametresiyle disk listesi ayarını belirtmeniz gerekir ve bu,  /   yedeklemeye dahil edilecek veya hariç tutulacak disklerin LUN numaralarına izin verir.

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --disk-list-setting exclude --diskslist 0 1
```

VM, kasala aynı kaynak grubunda değilse, **ResourceGroup** kasanın oluşturulduğu kaynak grubuna başvurur. VM adı yerine, VM KIMLIĞINI aşağıda gösterildiği gibi sağlayın.

```azurecli
az backup protection enable-for-vm  --resource-group {ResourceGroup} --vault-name {vaultname} --vm $(az vm show -g VMResourceGroup -n MyVm --query id --output tsv) --policy-name {policyname} --disk-list-setting include --diskslist {LUN number(s) separated by space}
```

### <a name="modify-protection-for-already-backed-up-vms-with-azure-cli"></a>Azure CLı ile zaten yedeklenen VM 'Ler için korumayı değiştirme

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting exclude --diskslist {LUN number(s) separated by space}
```

### <a name="backup-only-os-disk-during-configure-backup-with-azure-cli"></a>Azure CLı ile yedekleme yapılandırma sırasında yalnızca işletim sistemi diskini yedekleme

```azurecli
az backup protection enable-for-vm --resource-group {resourcegroup} --vault-name {vaultname} --vm {vmname} --policy-name {policyname} --exclude-all-data-disks
```

### <a name="backup-only-os-disk-during-modify-protection-with-azure-cli"></a>Azure CLı ile koruma değiştirme sırasında yalnızca işletim sistemi diskini yedekleme

```azurecli
az backup protection update-for-vm --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --exclude-all-data-disks
```

### <a name="restore-disks-with-azure-cli"></a>Azure CLı ile diskleri geri yükleme

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --diskslist {LUN number of the disk(s) to be restored}
```

### <a name="restore-only-os-disk-with-azure-cli"></a>Yalnızca Azure CLı ile işletim sistemi diskini geri yükleme

```azurecli
az backup restore restore-disks --resource-group {resourcegroup} --vault-name {vaultname} -c {vmname} -i {vmname} -r {restorepoint} } --target-resource-group {targetresourcegroup} --storage-account {storageaccountname} --restore-only-osdisk
```

### <a name="get-protected-item-to-get-disk-exclusion-details-with-azure-cli"></a>Azure CLı ile disk dışlama ayrıntılarını almak için korumalı öğeyi al

```azurecli
az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Korunan öğeye aşağıda gösterildiği gibi ek bir **Diskexclusionproperties** parametresi eklenmiştir:

```azurecli
"extendedProperties": {
      "diskExclusionProperties": {
        "diskLunList": [
          0,
          1
        ],
        "isInclusionList": true
      }
```

### <a name="get-backup-job-with-azure-cli"></a>Azure CLı ile yedekleme işini al

```azurecli
az backup job show --vault-name {vaultname} --resource-group {resourcegroup} -n {BackupJobID}
```

Bu komut, aşağıda gösterildiği gibi yedeklenen disklerin ve dışlanan disklerin ayrıntılarının sağlanmasına yardımcı olur:

```output
   "Backed-up disk(s)": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b; diskextest_DataDisk_0; diskextest_DataDisk_1",  "Backup Size": "0 MB",
   "Excluded disk(s)": "diskextest_DataDisk_2",
```

### <a name="list-recovery-points-with-azure-cli"></a>Azure CLı ile kurtarma noktalarını listeleme

```azurecli
az backup recoverypoint list --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM
```

Bu, VM 'de takılı ve yedeklenen disk sayısı bilgilerini sağlar.

```azurecli
      "recoveryPointDiskConfiguration": {
        "excludedDiskList": null,
        "includedDiskList": null,
        "numberOfDisksAttachedToVm": 4,
        "numberOfDisksIncludedInBackup": 3
};
```

### <a name="get-recovery-point-with-azure-cli"></a>Azure CLı ile kurtarma noktası al

```azurecli
az backup recoverypoint show --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM -n {recoverypointID}
```

Her kurtarma noktası, dahil edilen ve dışlanan disklerin bilgilerini içerir:

```azurecli
  "recoveryPointDiskConfiguration": {
      "excludedDiskList": [
        {
          "lun": 2,
          "name": "diskextest_DataDisk_2"
        }
      ],
      "includedDiskList": [
        {
          "lun": -1,
          "name": "diskextest_OsDisk_1_170808a95d214428bad92efeecae626b"
        },
        {
          "lun": 0,
          "name": "diskextest_DataDisk_0"
        },
        {
          "lun": 1,
          "name": "diskextest_DataDisk_1"
        }
      ],
      "numberOfDisksAttachedToVm": 4,
      "numberOfDisksIncludedInBackup": 3
```

### <a name="remove-disk-exclusion-settings-and-get-protected-item-with-azure-cli"></a>Azure CLı ile disk dışlama ayarlarını kaldırma ve korumalı öğeyi edinme

```azurecli
az backup protection update-for-vm --vault-name {vaultname} --resource-group {resourcegroup} -c {vmname} -i {vmname} --backup-management-type AzureIaasVM --disk-list-setting resetexclusionsettings

az backup item show -c {vmname} -n {vmname} --vault-name {vaultname} --resource-group {resourcegroup} --backup-management-type AzureIaasVM
```

Bu komutları çalıştırdığınızda görürsünüz `"diskExclusionProperties": null` .

## <a name="using-powershell"></a>PowerShell’i kullanma

Azure PowerShell sürüm 3.7.0 veya üstünü kullandığınızdan emin olun.

Koruma yapılandırma işlemi sırasında, disk listesi ayarını bir içerme/çıkarma parametresiyle belirtmeniz gerekir. Bu parametre, yedeklemeye dahil edilecek veya hariç tutulacak disklerin LUN numaralarına izin verir.

### <a name="enable-backup-with-powershell"></a>PowerShell ile yedeklemeyi etkinleştir

Örnek:

```azurepowershell
$disks = ("0","1")
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "rg-p-recovery_vaults" -Name "rsv-p-servers"
Get-AzRecoveryServicesBackupProtectionPolicy
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "P-Servers"
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -InclusionDisksList $disks -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExclusionDisksList $disks -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-configure-backup-with-powershell"></a>PowerShell ile yedekleme yapılandırma sırasında yalnızca işletim sistemi diskini Yedekle

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1"  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="get-backup-item-object-to-be-passed-in-modify-protection-with-powershell"></a>PowerShell ile değişiklik koruması ile geçirilecek yedekleme öğesi nesnesini Al

```azurepowershell
$item= Get-AzRecoveryServicesBackupItem -BackupManagementType "AzureVM" -WorkloadType "AzureVM" -VaultId $targetVault.ID -FriendlyName "V2VM"
```

Aşağıdaki cmdlet 'lerde, alınan **$item** nesnesini **– item** parametresine geçirmeniz gerekir.

### <a name="modify-protection-for-already-backed-up-vms-with-powershell"></a>PowerShell ile zaten yedeklenen VM 'Ler için korumayı değiştirme

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -InclusionDisksList[Strings] -VaultId $targetVault.ID
```

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ExclusionDisksList[Strings] -VaultId $targetVault.ID
```

### <a name="backup-only-os-disk-during-modify-protection-with-powershell"></a>PowerShell ile koruma değiştirme sırasında yalnızca işletim sistemi diskini yedekleme

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item  -ExcludeAllDataDisks -VaultId $targetVault.ID
```

### <a name="reset-disk-exclusion-setting-with-powershell"></a>PowerShell ile disk dışlama ayarını sıfırlama

```azurepowershell
Enable-AzRecoveryServicesBackupProtection -Item $item -ResetExclusionSettings -VaultId $targetVault.ID
```

### <a name="restore-selective-disks-with-powershell"></a>Seçmeli diskleri PowerShell ile geri yükleme

```azurepowershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $item -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreDiskList [$disks]
```

### <a name="restore-only-os-disk-with-powershell"></a>Yalnızca PowerShell ile işletim sistemi diskini geri yükleme

```azurepowershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID -RestoreOnlyOSDisk
```

## <a name="using-the-azure-portal"></a>Azure portalını kullanma

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Azure portal kullanarak, dahil edilen ve dışlanan diskleri VM yedeklemesi ayrıntıları bölmesinden ve yedekleme işi ayrıntıları bölmesinden görüntüleyebilirsiniz.  Geri yükleme sırasında, ' den geri yüklenecek kurtarma noktasını seçtiğinizde, bu kurtarma noktasındaki yedeklenen diskleri görüntüleyebilirsiniz.

Burada, portaldaki bir sanal makine için dahil edilen ve dışlanan diskleri VM yedekleme ayrıntıları bölmesinden görüntüleyebilirsiniz:

![Dahil edilen ve dışlanan diskleri yedekleme ayrıntıları bölmesinden görüntüleme](./media/selective-disk-backup-restore/backup-details.png)

Burada, eklenen ve dışlanan diskleri bir yedekleme iş ayrıntıları bölmesinden görüntüleyebilirsiniz:

![Dahil edilen ve dışlanan diskleri iş ayrıntıları bölmesinden görüntüleme](./media/selective-disk-backup-restore/job-details.png)

Burada, geri yükleme sırasında kurtarma noktasını seçtiğinizde geri yükleme sırasında yedeklenen diskleri görüntüleyebilirsiniz:

![Geri yükleme sırasında yedeklenen diskleri görüntüle](./media/selective-disk-backup-restore/during-restore.png)

Bir VM için seçmeli diskler Yedekleme deneyimini Azure portal aracılığıyla yapılandırmak, **yalnızca yedekleme işletim sistemi diski** seçeneğiyle sınırlıdır. Zaten yedeklenmiş bir VM 'de seçmeli diskler yedeklemesi kullanmak veya bir VM 'nin belirli veri disklerinin gelişmiş dahil edilmesi veya dışlanmasını sağlamak için PowerShell veya Azure CLı kullanın.

>[!NOTE]
>Veriler disklere yayılmışsa, tüm bağımlı disklerin yedeğe eklendiğinden emin olun. Bir birimdeki tüm bağımlı diskleri yedeklemediyseniz, geri yükleme sırasında bazı yedeklenmeyen disklerden oluşan birim oluşturulmaz.

### <a name="backup-os-disk-only-in-the-azure-portal"></a>İşletim sistemi diskini yalnızca Azure portal yedekleme

Azure portal kullanarak yedeklemeyi etkinleştirdiğinizde, **yalnızca yedekleme işletim sistemi diski** seçeneğini belirleyebilirsiniz. Böylece, işletim sistemi diski ile Azure VM 'nizin yedeklemesini yapılandırabilir ve ona bağlı tüm veri disklerini dışlayabilirsiniz.

![Yalnızca işletim sistemi diski için yedeklemeyi yapılandırma](./media/selective-disk-backup-restore/configure-backup-operating-system-disk.png)

## <a name="using-azure-rest-api"></a>Azure REST API kullanma

Azure VM yedeklemesini birkaç Select diski ile yapılandırabilirsiniz veya mevcut bir sanal makinenin korumasını [burada](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup)belgelenen birkaç diski dahil etmek/hariç tutmak için değiştirebilirsiniz.

## <a name="selective-disk-restore"></a>Seçmeli disk geri yükleme

Seçmeli disk geri yükleme, seçmeli diskler yedekleme özelliğini etkinleştirdiğinizde alacağınız ek bir işlevdir. Bu işlevle, bir kurtarma noktasında yedeklenen tüm disklerden seçmeli diskleri geri yükleyebilirsiniz. Daha verimlidir ve disklerin ne kadar geri yüklenmesi gerektiğini bildiğiniz senaryolarda zamandan tasarruf etmenize yardımcı olur.

- İşletim sistemi diski, VM yedekleme ve geri yükleme için varsayılan olarak dahil edilir ve dışlanamaz.
- Seçmeli disk geri yükleme yalnızca disk dışlama yeteneği etkinleştirildikten sonra oluşturulan kurtarma noktaları için desteklenir.
- Disk **dışlama ayarı olan** yedeklemeler yalnızca **disk geri yükleme** seçeneğini destekler. **VM geri yükleme** veya mevcut geri yükleme seçeneklerini **değiştirme** bu durumda desteklenmez.

![VM 'yi geri yükleme ve varolanı değiştirme seçeneği geri yükleme işlemi sırasında kullanılamıyor](./media/selective-disk-backup-restore/options-not-available.png)

## <a name="limitations"></a>Sınırlamalar

Seçmeli diskler Yedekleme işlevselliği, klasik sanal makineler ve şifrelenmiş sanal makineler için desteklenmez. Bu nedenle, Windows VM şifrelemesi için BitLocker kullanılarak Azure disk şifrelemesi (ADE) ile şifrelenen Azure sanal makineleri ve Linux VM 'Leri için dm-crypt özelliği desteklenmez.

**Yeni sanal makine oluşturmak** ve **varolanı değiştirmek** için geri yükleme seçenekleri, seçmeli DISKLER yedekleme işlevinin etkinleştirildiği VM için desteklenmez.

Şu anda Azure VM yedeklemesi, Ultra disklere veya bunlara bağlı paylaşılan disklere sahip VM 'Leri desteklemez. Seçmeli disk yedeklemesi bu gibi durumlarda kullanılamaz ve bu durumda diski hariç tutabilir ve VM 'yi yedekler.

## <a name="billing"></a>Faturalandırma

Azure sanal makine yedeklemesi, [burada](https://azure.microsoft.com/pricing/details/backup/)ayrıntılı olarak açıklanan mevcut fiyatlandırma modelini izler.

**Korumalı örnek (PI) maliyeti** yalnızca **işletim sistemi diski** seçeneğini kullanarak yedeklemeyi seçerseniz işletim sistemi diski için hesaplanır.  Yedeklemeyi yapılandırır ve en az bir veri diski seçerseniz, VM 'ye bağlı tüm diskler için PI maliyeti hesaplanır. **Yedekleme depolama maliyeti** yalnızca dahil edilen disklere göre hesaplanır ve depolama maliyetine tasarruf etmeniz gerekir. **Anlık görüntü maliyeti** her zaman VM 'deki tüm diskler için hesaplanır (hem dahil edilen hem de hariç tutulan diskler).

Çapraz bölge geri yükleme (CRR) özelliğini seçtiyseniz, [CRR fiyatlandırması](https://azure.microsoft.com/pricing/details/backup/) disk hariç tutularak yedekleme depolama maliyetinde geçerlidir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="how-is-protected-instance-pi-cost-calculated-for-only-os-disk-backup-in-windows-and-linux"></a>Windows ve Linux 'ta yalnızca işletim sistemi disk yedeklemesi için korumalı örnek (PI) maliyeti nasıl hesaplanır?

PI maliyeti, sanal makinenin gerçek (kullanılan) boyutu temel alınarak hesaplanır.

- Windows için: kullanılan alan hesaplama, işletim sistemini depolayan sürücüyü temel alır (genellikle C:).
- Linux için: kullanılan alan hesaplama, kök FileSystem (/) ' ın bağlı olduğu cihaza dayanır.

### <a name="i-have-configured-only-os-disk-backup-why-is-the-snapshot-happening-for-all-the-disks"></a>Yalnızca işletim sistemi disk yedeklemesini yapılandırdım, neden tüm diskler için anlık görüntü oluyor?

Seçmeli disk yedekleme özellikleri, yedeklemenin parçası olan dahil edilen diskleri güçlendirerek Yedekleme Kasası depolama maliyetinde tasarruf etmenize olanak tanır. Ancak anlık görüntü, VM 'ye bağlı tüm diskler için alınır. Bu nedenle, anlık görüntü maliyeti her zaman VM 'deki tüm diskler için hesaplanır (hem dahil edilen hem de hariç tutulan diskler). Daha fazla bilgi için bkz. [faturalandırma](#billing).

### <a name="i-cant-configure-backup-for-the-azure-virtual-machine-by-excluding-ultra-disk-or-shared-disks-attached-to-the-vm"></a>VM 'ye bağlı Ultra disk veya Paylaşılan diskleri dışlayarak Azure sanal makinesi için yedeklemeyi yapılandıramıyorum

Seçmeli disk yedekleme özelliği, Azure sanal makine yedekleme çözümünün en üstünde sağlanmış bir özelliktir. Şu anda Azure VM yedeklemesi, Ultra disk veya paylaşılan disk 'e bağlı olan VM 'Leri desteklemez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure VM yedekleme için destek matrisi](backup-support-matrix-iaas.md)
- [Sık sorulan sorular-Azure VM 'lerini yedekleme](backup-azure-vm-backup-faq.yml)
