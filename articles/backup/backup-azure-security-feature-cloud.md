---
title: Azure Backup için geçici silme
description: Yedeklemeleri daha güvenli hale getirmek için Azure Backup güvenlik özelliklerini kullanmayı öğrenin.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: da473b1d886ec2fe95a7baae76b09aff38fb3cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95254060"
---
# <a name="soft-delete-for-azure-backup"></a>Azure Backup için geçici silme

Kötü amaçlı yazılım, fidye ve yetkisiz erişim gibi güvenlik sorunları hakkında sorunlar artıyor. Bu güvenlik sorunları hem para hem de veri bakımından maliyetli olabilir. Bu tür saldırılara karşı koruma için Azure Backup artık, silme işleminden sonra bile yedekleme verilerini korumaya yardımcı olacak güvenlik özellikleri sağlamaktadır.

Bu tür bir özellik, geçici bir DELETE. Kötü niyetli bir aktör bir yedeği silse (veya yedekleme verileri yanlışlıkla silinse), yedekleme verileri 14 ek gün boyunca tutulur ve bu yedekleme öğesinin veri kaybı olmadan kurtarılmasını sağlar. "Geçici silme" durumundaki yedekleme verileri için 14 günlük ek bekletme, size herhangi bir maliyet vermez.

Bu hizmetler için geçici silme koruması kullanılabilir:

- [Azure sanal makineleri için geçici silme](soft-delete-virtual-machines.md)
- [Azure VM 'de SQL Server için geçici silme ve Azure VM iş yükleri SAP HANA için geçici silme](soft-delete-sql-saphana-in-azure-vm.md)

Bu akış grafiği, geçici silme etkinleştirildiğinde bir yedekleme öğesinin farklı adımlarını ve durumlarını gösterir:

![Geçici olarak silinen yedekleme öğesinin yaşam döngüsü](./media/backup-azure-security-feature-cloud/lifecycle.png)

## <a name="enabling-and-disabling-soft-delete"></a>Geçici silmeyi etkinleştirme ve devre dışı bırakma

Geçici silme, yeni oluşturulan kasaların yanlışlıkla veya kötü amaçlı silmeleri arasında yedekleme verilerini korumak için varsayılan olarak etkindir.  Bu özelliğin devre dışı bırakılması önerilmez. Geçici silme işleminin devre dışı bırakılmasını göz önünde bulundurmanız gereken tek durumlar, korumalı öğelerinizi yeni bir kasaya taşımayı planlıyorsanız ve silme ve yeniden koruma (örneğin, bir test ortamında) için gereken 14 gün bekleyemez. Yalnızca kasa sahibi bu özelliği devre dışı bırakabilir. Bu özelliği devre dışı bırakırsanız, korunan öğelerin gelecekteki tüm silmeleri, geri yükleme özelliği olmadan hemen kaldırılmasına neden olur. Bu özelliği devre dışı bırakmadan önce geçici olarak silinen durumunda bulunan yedekleme verileri, 14 günlük süre boyunca geçici olarak silinmiş durumda kalır. Bunları hemen kalıcı olarak silmek isterseniz, kalıcı olarak silinmesi için silmeyi geri almanız ve silmeniz gerekir.

Geçici silme devre dışı bırakıldığında özelliğin tüm iş yükü türleri için devre dışı bırakıldığını unutmamak önemlidir. Örneğin, aynı kasadaki sanal makineler için etkin tutulurken yalnızca SQL Server veya SAP HANA DBs için geçici silme devre dışı bırakmak mümkün değildir. Ayrıntılı denetim için ayrı kasa oluşturabilirsiniz.

### <a name="disabling-soft-delete-using-azure-portal"></a>Azure portal kullanarak geçici silme devre dışı bırakılıyor

Geçici silme devre dışı bırakmak için şu adımları izleyin:

1. Azure Portal, kasanıza gidin ve **Ayarlar**  ->  **Özellikler**' e gidin.
2. Özellikler bölmesinde **güvenlik ayarları**  ->  **güncelleştirme**' yi seçin.  
3. Güvenlik ayarları bölmesinde, **geçici silme** altında **devre dışı bırak**' ı seçin.

![Geçici silmeyi devre dışı bırak](./media/backup-azure-security-feature-cloud/disable-soft-delete.png)

### <a name="disabling-soft-delete-using-azure-powershell"></a>Azure PowerShell kullanarak geçici silme devre dışı bırakılıyor

> [!IMPORTANT]
> Azure PowerShell kullanarak geçici silme kullanmak için gereken az. RecoveryServices sürümü en az 2.2.0. ```Install-Module -Name Az.RecoveryServices -Force```En son sürümü almak için kullanın.

Devre dışı bırakmak için [set-AzRecoveryServicesVaultBackupProperty](/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) PowerShell cmdlet 'ini kullanın.

```powershell
Set-AzRecoveryServicesVaultProperty -VaultId $myVaultID -SoftDeleteFeatureState Disable


StorageModelType       :
StorageType            :
StorageTypeState       :
EnhancedSecurityState  : Enabled
SoftDeleteFeatureState : Disabled
```

### <a name="disabling-soft-delete-using-rest-api"></a>REST API kullanarak geçici silme devre dışı bırakılıyor

REST API kullanarak geçici silme işlevini devre dışı bırakmak için [burada](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)bahsedilen adımlara bakın.

## <a name="permanently-deleting-soft-deleted-backup-items"></a>Geçici olarak silinen yedekleme öğelerini kalıcı olarak silme

Bu özelliğin devre dışı bırakılmasından önce geçici olarak silinen durumda verileri yedekleyin, geçici olarak silinmiş durumda kalır. Bunları hemen kalıcı olarak silmek istiyorsanız silmeyi geri alın ve kalıcı olarak silmek için yeniden silin.

### <a name="using-azure-portal"></a>Azure portalını kullanma

Şu adımları izleyin:

1. [Geçici silme özelliğini devre dışı bırakmak](#enabling-and-disabling-soft-delete)için adımları izleyin.

2. Azure portal kasanıza gidin, **yedekleme öğeleri**' ne gidin ve geçici olarak silinen öğesini seçin.

   ![Geçici silinen öğe seçin](./media/backup-azure-security-feature-cloud/vm-soft-delete.png)

3. **Silmeyi geri al** seçeneğini belirleyin.

   ![Silmeyi geri al seçeneğini belirleyin](./media/backup-azure-security-feature-cloud/choose-undelete.png)

4. Bir pencere görüntülenir. **Silmeyi geri al** seçeneğini belirleyin.

   ![Silmeyi geri al seçeneğini belirleyin](./media/backup-azure-security-feature-cloud/undelete-vm.png)

5. Yedekleme verilerini kalıcı olarak silmek için **yedekleme verilerini sil** ' i seçin.

   ![Yedekleme verilerini Sil ' i seçin](./media/backup-azure-manage-vms/delete-backup-button.png)

6. Kurtarma noktalarını silmek istediğinizi onaylamak için yedekleme öğesinin adını yazın.

   ![Yedekleme öğesinin adını yazın](./media/backup-azure-manage-vms/delete-backup-data.png)

7. Öğe için yedekleme verilerini silmek için **Sil**' i seçin. Bir bildirim iletisi, yedekleme verilerinin silindiğini bilmenizi sağlar.

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

Yazılım, geçici silme devre dışı bırakıldıktan sonra siliniyorsa, geçici olarak silinen bir durumda olur. Bunları hemen silmek için silme işleminin ters alınması ve sonra yeniden gerçekleştirilmesi gerekir.

Geçici olarak silinmiş durumdaki öğeleri belirler.

```powershell

Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID | Where-Object {$_.DeleteState -eq "ToBeDeleted"}

Name                                     ContainerType        ContainerUniqueName                      WorkloadType         ProtectionStatus     HealthStatus         DeleteState
----                                     -------------        -------------------                      ------------         ----------------     ------------         -----------
VM;iaasvmcontainerv2;selfhostrg;AppVM1    AzureVM             iaasvmcontainerv2;selfhostrg;AppVM1       AzureVM              Healthy              Passed               ToBeDeleted

$myBkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -VaultId $myVaultID -Name AppVM1
```

Ardından, geçici silme etkinken gerçekleştirilen silme işlemini tersine çevirin.

```powershell
Undo-AzRecoveryServicesBackupItemDeletion -Item $myBKpItem -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           Undelete             Completed            12/5/2019 12:47:28 PM     12/5/2019 12:47:40 PM     65311982-3755-46b5-8e53-c82ea4f0d2a2
```

Geçici silme devre dışı olduğundan, silme işlemi yedekleme verilerinin anında kaldırılmasına neden olur.

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $myBkpItem -RemoveRecoveryPoints -VaultId $myVaultID -Force

WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
AppVM1           DeleteBackupData     Completed            12/5/2019 12:44:15 PM     12/5/2019 12:44:50 PM     0488c3c2-accc-4a91-a1e0-fba09a67d2fb
```

### <a name="using-rest-api"></a>REST API kullanma

Yazılım, geçici silme devre dışı bırakıldıktan sonra siliniyorsa, geçici olarak silinen bir durumda olur. Bunları hemen silmek için silme işleminin ters alınması ve sonra yeniden gerçekleştirilmesi gerekir.

1. İlk olarak, [burada](backup-azure-arm-userestapi-backupazurevms.md#undo-the-deletion)bahsedilen adımlarla silme işlemlerini geri alın.
2. Ardından, [burada](use-restapi-update-vault-properties.md#update-soft-delete-state-using-rest-api)bahsedilen adımları kullanarak REST API kullanarak geçici silme işlevini devre dışı bırakın.
3. Ardından, [burada](backup-azure-arm-userestapi-backupazurevms.md#stop-protection-and-delete-data)belirtildiği gibi REST API kullanarak yedeklemeleri silin.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="do-i-need-to-enable-the-soft-delete-feature-on-every-vault"></a>Her kasada geçici silme özelliğini etkinleştirmem gerekir mi?

Hayır, yerleşik ve tüm kurtarma hizmetleri kasaları için varsayılan olarak etkinleştirilir.

### <a name="can-i-configure-the-number-of-days-for-which-my-data-will-be-retained-in-soft-deleted-state-after-the-delete-operation-is-complete"></a>Silme işlemi tamamlandıktan sonra verileri geçici olarak silinen durumda tutulacak gün sayısını yapılandırabilir miyim?

Hayır, silme işleminden sonra 14 gün daha fazla bekletme için düzeltildi.

### <a name="do-i-need-to-pay-the-cost-for-this-additional-14-day-retention"></a>Bu ek 14 günlük bekletme için maliyeti ödemem gerekiyor mu?

Hayır, bu 14 günlük ek bekletme, geçici silme işlevselliğinin bir parçası olarak ücretsiz olarak sunulur.

### <a name="can-i-perform-a-restore-operation-when-my-data-is-in-soft-delete-state"></a>Verilerim geçici silme durumunda olduğunda geri yükleme işlemi gerçekleştirebilir miyim?

Hayır, geri yüklemek için geçici olarak silinen kaynağı geri almanız gerekir. Silmeyi geri alma işlemi, kaynağı, zaman içinde herhangi bir noktaya geri yüklediğiniz **verileri koruyun durumuyla yeniden durdur** 'a geri gönderir. Çöp toplayıcı bu durumda duraklatılmaya devam eder.

### <a name="will-my-snapshots-follow-the-same-lifecycle-as-my-recovery-points-in-the-vault"></a>Anlık görüntülerim kasadaki kurtarma noktalarım ile aynı yaşam döngüsünü izlesin mi?

Evet.

### <a name="how-can-i-trigger-the-scheduled-backups-again-for-a-soft-deleted-resource"></a>Zamanlanmış yedeklemeleri, geçici olarak silinen bir kaynak için yeniden nasıl tetikleyebilirim?

Geri alma sonrasında devam eden bir işlem, kaynağı yeniden korur. Özgeçmişi işlemi, seçilen bekletme süresiyle zamanlanan yedeklemeleri tetiklemek için bir yedekleme ilkesini ilişkilendirir. Ayrıca, çöp toplayıcı, işlem tamamlandıktan hemen sonra çalışır. Sona erme tarihini aşan bir kurtarma noktasından geri yükleme gerçekleştirmek istiyorsanız, devam eden işlemi tetiklemeden önce bunu yapmanız önerilir.

### <a name="can-i-delete-my-vault-if-there-are-soft-deleted-items-in-the-vault"></a>Kasada geçici olarak silinen öğeler varsa kasamı silebilir miyim?

Kasadaki geçici olarak silinen durumda yedekleme öğeleri varsa kurtarma hizmetleri Kasası silinemez. Geçici olarak silinen öğeler, silme işleminden sonra 14 gün sonra kalıcı olarak silinir. 14 gün bekleyemez, [geçici silme işlemini devre dışı bırakın](#enabling-and-disabling-soft-delete), geçici olarak silinen öğeleri geri alın ve kalıcı olarak silmek için yeniden silin. Korunan öğe olmadığından ve geçici olarak silinen öğeler olmadığından, kasa silinebilir.  

### <a name="can-i-delete-the-data-earlier-than-the-14-days-soft-delete-period-after-deletion"></a>Silme işleminden sonra 14 gün geçici silme süresinden önceki verileri silebilir miyim?

Hayır. Geçici olarak silinen öğeleri silmeye zorlayamaz. Bunlar 14 gün sonra otomatik olarak silinir. Bu güvenlik özelliği, yedeklenen verilerin yanlışlıkla veya kötü amaçlı silmelerden korunmasını sağlamak için etkinleştirilir.  Öğede başka bir işlem yapmadan önce 14 gün beklemeniz gerekir.  Geçici olarak silinen öğeler ücretlendirilmeyecektir.  Yeni bir kasadaki 14 gün içinde geçici silme için işaretlenen öğeleri yeniden korumanız gerekiyorsa, Microsoft destek 'e başvurun.

### <a name="can-soft-delete-operations-be-performed-in-powershell-or-cli"></a>PowerShell veya CLı 'de geçici silme işlemleri yapılabilir mi?

Geçici silme işlemleri, PowerShell kullanılarak gerçekleştirilebilir. Şu anda CLı desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup güvenlik özelliklerine genel bakış](security-overview.md)
