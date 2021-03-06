---
title: Azure VM 'de SQL Server ve Azure VM iş yükleri SAP HANA için geçici silme
description: Azure VM 'de SQL Server için geçici silmenin ve Azure VM iş yüklerindeki SAP HANA, yedeklemelerin daha güvenli hale gelmesini öğrenin.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 2a442997d426ff0bf4c74b0b45f7657cc0593b82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91254304"
---
# <a name="soft-delete-for-sql-server-in-azure-vm-and-sap-hana-in-azure-vm-workloads"></a>Azure VM 'de SQL Server ve Azure VM iş yükleri SAP HANA için geçici silme

Azure Backup artık Azure VM 'de SQL Server için geçici silme ve Azure VM iş yükleri SAP HANA. Bu, zaten desteklenen [Azure sanal makinesi geçici silme senaryosuna](soft-delete-virtual-machines.md)ek niteliğindedir.

[Geçici silme](backup-azure-security-feature-cloud.md) , silme işleminden sonra bile yedekleme verilerini korumaya yardımcı olan bir güvenlik özelliğidir. Kötü niyetli bir aktör bir veritabanının yedeğini silse (veya yedekleme verileri yanlışlıkla siliniyorsa), yedekleme verileri 14 ek gün boyunca tutulur. Bu, veri kaybı olmadan bu yedekleme öğesinin kurtarılmasına izin verir. "Geçici silme" durumundaki yedekleme verilerinin 14 günlük ek saklama süresi müşteriye hiçbir ücret vermez.

## <a name="soft-delete-for-sql-server-in-azure-vm-using-azure-portal"></a>Azure portal kullanarak Azure VM 'de SQL Server için geçici silme

>[!NOTE]
>Bu yönergeler, Azure VM 'deki SAP HANA de geçerlidir.

1. SQL Server 'daki bir veritabanının yedekleme verilerini silmek için yedeklemenin durdurulması gerekir. Azure portal, kurtarma hizmetleri kasanıza gidin, Yedekleme öğesine gidin ve **Yedeklemeyi Durdur**' u seçin.

   ![Yedeklemeyi Durdur](./media/soft-delete-sql-saphana-in-azure-vm/stop-backup.png)

2. Aşağıdaki pencerede, yedekleme verilerini silme veya tutma seçeneği sunulur. **Yedekleme verilerini sil**' i seçerseniz, veritabanı yedeklemesi kalıcı olarak silinmez. Bunun yerine, yedekleme verileri geçici olarak silinen durumunda 14 gün boyunca tutulacaktır. Silme işlemi, 15. güne kadar ilk, 12. ve 15. günde normal uyarı e-postalarına kadar ertelenir.

   ![Yedekleme verilerini silme](./media/soft-delete-sql-saphana-in-azure-vm/delete-backup-data.png)

3. Bu 14 gün boyunca, kurtarma hizmetleri kasasında, geçici olarak silinen öğe yanında kırmızı bir "geçici silme" simgesiyle görüntülenir.

   ![Geçici silinen öğeler](./media/soft-delete-sql-saphana-in-azure-vm/soft-deleted-items.png)

4. Geçici olarak silinen DB 'yi geri yüklemek için önce silinmesi gerekir. Silmeyi geri almak için, geçici olarak silinen DB 'yi seçin ve sonra **silmeyi geri al** seçeneğini belirleyin.

   ![Veritabanını geri al](./media/soft-delete-sql-saphana-in-azure-vm/undelete-database.png)

   Silmeyi geri alma seçilirse bir pencere görünür ve geri yükleme işlemi gerçekleştirmek için veritabanının tüm geri yükleme noktalarının silinmesi ve kullanılabilir olduğunu gösterir. Yedekleme, yedekleme duraklatılmasıyla birlikte yedekleme verileri sürekli tutulur ve yedekleme ilkesi etkin olmadan yedeklenmek üzere yedekleme öğesi "verileri ALIMAK ile korumayı Durdur" durumunda saklanır.

   ![Silmeyi geri alma uyarısı](./media/soft-delete-sql-saphana-in-azure-vm/undelete-warning.png)

5. Bu noktada, seçilen geçici olarak silinen yedekleme öğesi için **geri yükle** ' yi seçerek de verileri geri yükleyebilirsiniz.

   ![Sanal makineyi geri yükleme](./media/soft-delete-sql-saphana-in-azure-vm/restore-vm.png)

6. Silmeyi geri alma işlemi tamamlandıktan sonra, durum "verileri koruyacak şekilde Yedeklemeyi Durdur" ' a döner ve sonra **yedeklemeyi devam et**' i seçebilirsiniz. **Yedeklemeyi yeniden başlatma** işlemi, yedekleme ve bekletme zamanlamalarını tanımlayan Kullanıcı tarafından seçilen bir yedekleme ilkesiyle ilişkili olan yedekleme öğesini etkin duruma getirir.

   ![Yedeklemeyi sürdürür](./media/soft-delete-sql-saphana-in-azure-vm/resume-backup.png)

## <a name="soft-delete-for-sql-server-in-vm-using-azure-powershell"></a>Azure PowerShell kullanarak VM 'de SQL Server için geçici silme

>[!NOTE]
>Azure PowerShell kullanarak geçici silme kullanmak için gereken az. RecoveryServices sürümü en az 2.2.0. `Install-Module -Name Az.RecoveryServices -Force`En son sürümü almak için kullanın.

Azure PowerShell kullanımı için adım sırası, yukarıda özetlenen Azure portal ile aynıdır.

### <a name="delete-the-backup-item-using-azure-powershell"></a>Azure PowerShell kullanarak yedekleme öğesini silme

[Devre dışı bırak-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection) PowerShell cmdlet 'ini kullanarak yedekleme öğesini silin.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force
```

Yedekleme öğesinin **DeleteState** 'ı **Notdeleted** iken **tobedeleted** olarak değiştirilecek. Yedekleme verileri 14 gün boyunca tutulacaktır. Silme işlemini geri almak isterseniz geri alma-silme gerçekleştirilmelidir.

### <a name="undoing-the-deletion-operation-using-azure-powershell"></a>Azure PowerShell kullanarak silme işlemini geri alma

İlk olarak, geçici silme durumundaki (yani, silinecek) ilgili yedekleme öğesini getirin.

```powershell
Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType SQLDataBase -VaultId $myVaultID -Name AppVM1
```

Ardından, [Undo-Azrecoveryservicesbackupıtemsilinmeye](/powershell/module/az.recoveryservices/undo-azrecoveryservicesbackupitemdeletion) PowerShell cmdlet 'ini kullanarak geri alma silme işlemini gerçekleştirin.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force
```

Yedekleme öğesinin **DeleteState** öğesi **notdeleted** olarak döndürülür. Ancak koruma hala durdurulmuş. Korumayı yeniden etkinleştirmek için yedeklemeyi sürdürür.

## <a name="how-to-disable-soft-delete"></a>Geçici silme devre dışı bırakma

Bu özelliğin devre dışı bırakılması önerilmez. Geçici silme işleminin devre dışı bırakılmasını göz önünde bulundurmanız gereken tek durumlar, korumalı öğelerinizi yeni bir kasaya taşımayı planlıyorsanız ve silme ve yeniden koruma (örneğin, bir test ortamında) için gereken 14 gün bekleyemez. Geçici silmeyi devre dışı bırakma hakkında yönergeler için bkz. [geçici silme etkinleştirme ve devre dışı bırakma](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete).

## <a name="next-steps"></a>Sonraki adımlar

- Geçici silme hakkında [sık sorulan soruları](backup-azure-security-feature-cloud.md#frequently-asked-questions) okuyun
- [Azure Backup tüm güvenlik özellikleri](security-overview.md) hakkında bilgi edinin
