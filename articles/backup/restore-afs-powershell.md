---
title: Azure dosyalarını PowerShell ile geri yükleme
description: Bu makalede, Azure Backup hizmetini ve PowerShell 'i kullanarak Azure dosyalarını geri yüklemeyi öğrenin.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 60c9848e12de80bcafe4553a9e8f3e27e8876d41
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021393"
---
# <a name="restore-azure-files-with-powershell"></a>Azure dosyalarını PowerShell ile geri yükleme

Bu makalede, Azure PowerShell kullanılarak [Azure Backup](backup-overview.md) hizmeti tarafından oluşturulan bir geri yükleme noktasından tüm dosya paylaşımının veya belirli dosyaların nasıl geri yükleneceği açıklanmaktadır.

Dosya paylaşımının tamamını veya paylaşımdaki belirli dosyaları geri yükleyebilirsiniz. Özgün konuma veya alternatif bir konuma geri yükleyebilirsiniz.

> [!WARNING]
> AFS yedeklemeleri için PowerShell sürümünün ' az. RecoveryServices 2.6.0 ' için en düşük sürüme yükseltildiğinden emin olun. Daha fazla bilgi için bu değişiklik için gereksinimin ana hattı oluşturma [bölümüne](backup-azure-afs-automation.md#important-notice-backup-item-identification) bakın.

>[!NOTE]
>Azure Backup artık, PowerShell kullanarak birden çok dosya veya klasörü özgün veya alternatif konuma geri yüklemeyi desteklemektedir. Hakkında bilgi edinmek için belgenin [Bu bölümüne](#restore-multiple-files-or-folders-to-original-or-alternate-location) bakın.

## <a name="fetch-recovery-points"></a>Kurtarma noktalarını getir

Yedeklenen öğenin tüm kurtarma noktalarını listelemek için [Get-AzRecoveryServicesBackupRecoveryPoint](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) komutunu kullanın.

Aşağıdaki betikte:

* **$RP** değişkeni, son yedi gündeki seçili yedekleme öğesi için bir kurtarma noktaları dizisidir.
* Dizi, Dizin **0**' daki en son kurtarma noktasıyla ters sırada sıralanır.
* Kurtarma noktasını seçmek için standart PowerShell dizisi dizinlemeyi kullanın.
* Örnekte, **$RP [0]** en son kurtarma noktasını seçer.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"
$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID
$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
$rp[0] | fl
```

Çıktı aşağıdakine benzer.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```

İlgili kurtarma noktası seçildikten sonra, dosya paylaşma veya dosyayı özgün konuma veya alternatif bir konuma geri yükleyin.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure dosya paylaşımından alternatif bir konuma geri yükleme

Seçili kurtarma noktasına geri yüklemek için [restore-Azrecoveryservicesbackupıtem öğesini](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) kullanın. Alternatif konumu tanımlamak için bu parametreleri belirtin:

* **Targetstorageaccountname**: yedeklenen içeriğin geri yüklendiği depolama hesabı. Hedef depolama hesabı kasayla aynı konumda olmalıdır.
* **Targetfilesharename**: yedeklenen içeriğin geri yüklendiği hedef depolama hesabı içindeki dosya paylaşımları.
* **TargetFolder**: verilerin geri yüklendiği dosya paylaşımının altındaki klasör. Yedeklenen içerik bir kök klasöre geri yüklenirse, hedef klasör değerlerini boş bir dize olarak verin.
* **ResolveConflict**: geri yüklenen verilerle bir çakışma varsa yönerge. **Overwrite** veya **Skip** kabul eder.

Cmdlet 'ini parametrelerle aşağıdaki gibi çalıştırın:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

Komut, aşağıdaki örnekte gösterildiği gibi, bir KIMLIĞE sahip bir iş döndürür.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure dosyasını alternatif bir konuma geri yükleme

Seçili kurtarma noktasına geri yüklemek için [restore-Azrecoveryservicesbackupıtem öğesini](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) kullanın. Alternatif konumu tanımlamak ve geri yüklemek istediğiniz dosyayı benzersiz olarak tanımlamak için bu parametreleri belirtin.

* **Targetstorageaccountname**: yedeklenen içeriğin geri yüklendiği depolama hesabı. Hedef depolama hesabı kasayla aynı konumda olmalıdır.
* **Targetfilesharename**: yedeklenen içeriğin geri yüklendiği hedef depolama hesabı içindeki dosya paylaşımları.
* **TargetFolder**: verilerin geri yüklendiği dosya paylaşımının altındaki klasör. Yedeklenen içerik bir kök klasöre geri yüklenirse, hedef klasör değerlerini boş bir dize olarak verin.
* **SourceFilePath**: dosya paylaşımında, bir dize olarak geri yüklenecek dosyanın mutlak yolu. Bu yol, **Get-AzStorageFile** PowerShell cmdlet 'inde kullanılan yoldur.
* **Sourcefiletype**: bir dizin veya dosya seçili olup olmadığı. **Dizini** veya **dosyayı** kabul eder.
* **ResolveConflict**: geri yüklenen verilerle bir çakışma varsa yönerge. **Overwrite** veya **Skip** kabul eder.

Ek parametreler (SourceFilePath ve SourceFileType) yalnızca geri yüklemek istediğiniz tek dosya ile ilgilidir.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Bu komut, önceki bölümde gösterildiği gibi, bir KIMLIĞE sahip bir iş döndürür.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure dosya paylaşımlarını ve dosyalarını özgün konumuna geri yükleme

Özgün bir konuma geri yüklediğinizde, hedef ve hedefle ilgili parametreleri belirtmeniz gerekmez. Yalnızca **ResolveConflict** sağlanmalıdır.

### <a name="overwrite-an-azure-file-share"></a>Azure dosya paylaşımının üzerine yaz

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Azure dosyasının üzerine yaz

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Birden çok dosyayı veya klasörü orijinal veya alternatif konuma geri yükleme

Restore [-Azrecoveryservicesbackupıtem](/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) komutunu, geri yüklemek istediğiniz tüm dosya veya klasörlerin yolunu **multiplesourcefilepath** parametresi için bir değer olarak geçirerek kullanın.

### <a name="restore-multiple-files"></a>Birden çok dosyayı geri yükleme

Aşağıdaki betikte, *FileSharePage.png* ve *MyTestFile.txt* dosyalarını geri yüklemeye çalışıyoruz.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Birden çok dizini geri yükleme

Aşağıdaki betikte, *zrs1_restore* geri yüklemeye ve dizinleri *geri* yüklemeye çalışıyoruz.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

Çıktı şununla benzerlik gösterecektir:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Farklı konuma birden çok dosya veya klasör geri yüklemek isterseniz, [Azure dosyasını alternatif bir konuma geri yükleme](#restore-an-azure-file-to-an-alternate-location)bölümünde anlatıldığı gibi hedef konuma ilişkin parametre değerlerini belirterek yukarıdaki betikleri kullanın.

## <a name="next-steps"></a>Sonraki adımlar

Azure portal Azure dosyalarını geri yükleme [hakkında bilgi edinin](restore-afs.md) .
