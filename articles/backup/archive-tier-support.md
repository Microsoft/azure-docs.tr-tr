---
title: Arşiv katmanı desteği (Önizleme)
description: Azure Backup için Arşiv katmanı desteği hakkında bilgi edinin
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 7a42b8702cfdda14a18aa3cdd4e084ed78767b0a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012157"
---
# <a name="archive-tier-support-preview"></a>Arşiv katmanı desteği (Önizleme)

Müşteriler, Long-Term bekletme (LTR) yedekleme verileri dahil olmak üzere yedekleme verilerinin kuruluşun Uyumluluk kuralları tarafından tanımlanmakta olması için Azure Backup güvenir. Çoğu durumda, eski yedekleme verilerine nadiren erişilir ve yalnızca Uyumluluk ihtiyaçları için depolanır.

Azure Backup, anlık görüntüler ve Standart katmana ek olarak arşiv katmanındaki uzun süreli bekletme noktalarının yedeklenmesini destekler.

## <a name="scope-for-preview"></a>Önizleme kapsamı

Desteklenen iş yükleri:

- Azure sanal makineleri
  - Yalnızca aylık ve yıllık kurtarma noktaları. Günlük ve haftalık kurtarma noktaları desteklenmez.
  - Yaş >= 3 ay Vault-Standard katmanda
  - Bekletme sola >= 6 ay
  - Etkin günlük ve haftalık bağımlılıklar yok
- Azure sanal makineler 'de SQL Server
  - Yalnızca tam kurtarma noktaları. Günlükler ve farklılıklar desteklenmez.
  - Yaş >= Vault-Standard katmanda 45 gün
  - Bekletme sola >= 6 ay
  - Bağımlılık yok

Desteklenen istemciler:

- Yetenek, PowerShell kullanılarak sağlanır

>[!NOTE]
>Azure VM 'lerinde Azure sanal makineleri ve SQL Server için Arşiv katmanı desteği, sınırlı sonuçlarla sınırlı genel önizlemededir. Arşiv desteği için kaydolmak üzere bu [bağlantıyı](https://aka.ms/ArchivePreviewInterestForm)kullanın.

## <a name="get-started-with-powershell"></a>PowerShell ile çalışmaya başlayın

1. [En son](https://github.com/PowerShell/PowerShell/releases) PowerShell sürümünü GitHub 'dan indirin.

1. PowerShell'de aşağıdaki komutu çalıştırın:
  
    ```azurepowershell
    install-module -name Az.RecoveryServices -Repository PSGallery -RequiredVersion 4.0.0-preview -AllowPrerelease -force
    ```

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak Azure 'a bağlanın.
1. Aboneliğinizde oturum açın:

   `Set-AzContext -Subscription "SubscriptionName"`

1. Kasayı alın:

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Yedekleme öğelerinin listesini al:

    - Azure sanal makineleri için:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM" -WorkloadType "AzureVM"`

    - Azure sanal makineler 'de SQL Server için:

        `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureWorkload" -WorkloadType "MSSQL"`

1. Yedekleme öğesini al.

    - Azure sanal makineleri için:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Azure sanal makineler 'de SQL Server için:

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>PowerShell kullanma

### <a name="check-archivable-recovery-points"></a>Arşivlenemez kurtarma noktalarını denetle

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Bu işlem, arşive taşınmaya başlamaya yönelik belirli bir yedekleme öğesiyle ilişkili tüm kurtarma noktalarını listeler.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Kurtarma noktasının neden arşive taşınamayacağını denetleyin

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Burada `$rp[0]` neden arşivlenemez olmadığını denetlemek istediğiniz kurtarma noktasıdır.

Örnek çıktı:

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Önerilen arşivlenemez Points kümesini denetle (yalnızca Azure VM 'Leri için)

Bir sanal makineyle ilişkili kurtarma noktaları, doğası halinde artımlıdır. Belirli bir kurtarma noktası arşive taşındığında, tam yedeklemeye dönüştürülüp sonra arşive taşınır. Bu nedenle, arşive taşıma ile ilişkili maliyet tasarrufları veri kaynağının dalgalanmasına bağlıdır.

Azure Backup, birlikte taşınmışsa maliyet tasarruflarıyla sonuçlanabilecek önerilen bir kurtarma noktaları kümesiyle gelir.

>[!NOTE]
>Maliyet tasarrufları çeşitli nedenlerle farklılık gösterir ve iki örnek için aynı olmayabilir.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Arşive taşı

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Bu komut bir arşivlenemez kurtarma noktasını arşive gider. Hem Portal 'dan hem de PowerShell ile taşıma işlemini izlemek için kullanılabilecek bir iş döndürür.

### <a name="view-archived-recovery-points"></a>Arşivlenmiş kurtarma noktalarını görüntüle

Bu komut, arşivlenmiş tüm kurtarma noktalarını döndürür.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>PowerShell ile geri yükleme

Arşivdeki kurtarma noktaları için, Azure Backup tümleşik bir geri yükleme yöntemi sağlar.

Tümleşik geri yükleme iki adımlı bir işlemdir. İlk adım, arşivde depolanan kurtarma noktalarını yeniden doldurma ve 10 ila 30 gün boyunca bir süre (yeniden doldurma süresi olarak da bilinir) için kasa standart katmanında geçici olarak depolama içerir. Varsayılan değer 15 gündür. İki farklı yeniden doldurma derecesi vardır: Standart ve yüksek öncelik. [Yeniden doldurma önceliği](../storage/blobs/storage-blob-rehydration.md#rehydrate-an-archived-blob-to-an-online-tier)hakkında daha fazla bilgi edinin.

>[!NOTE]
>
>- Yeniden doldurma süresi, seçildikten sonra değiştirilemez ve yeniden doldurma süresi için Standart katmanda yeniden doldurma işlemi kalır.
>- Ek hidrasyon ek adımı ücretlendirilir.

Azure sanal makineleri için çeşitli geri yükleme yöntemleri hakkında daha fazla bilgi için bkz. [Azure VM 'Yi PowerShell Ile geri yükleme](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

SQL Server geri yüklemek için [aşağıdaki adımları](backup-azure-sql-automation.md#restore-sql-dbs)izleyin. Gerekli ek parametreler **yeniden doldurma önceliği** ve **yeniden doldurma süresi**.

### <a name="view-jobs-from-powershell"></a>PowerShell 'den işleri görüntüleme

Taşıma ve geri yükleme işlerini görüntülemek için aşağıdaki PowerShell cmdlet 'ini kullanın:

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Portalı kullanma

### <a name="check-archived-recovery-point"></a>Arşivlenmiş kurtarma noktasını denetle

Artık arşive taşınan tüm kurtarma noktalarını görüntüleyebilirsiniz.

![Tüm kurtarma noktaları.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Portalda geri yükleme

Arşiv 'e taşınan kurtarma noktaları için geri yükleme işlemi, yeniden doldurma süresi ve yeniden doldurma önceliği için parametreleri eklemenizi gerektirir.

![Portalda geri yükleyin.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Portalda işleri görüntüleme

![Portalda işleri görüntüleyin.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Korumayı değiştirme

Bir veri kaynağı için korumayı değiştirebilmenin iki yolu vardır:

- Geçerli bir ilkeyi değiştirme
- Veri kaynağını yeni bir ilkeyle koruma

Her iki durumda da, yeni ilke Standart katmanda olan ve arşiv katmanındaki tüm eski kurtarma noktalarına uygulanır. Bu nedenle, ilkede bir değişiklik varsa eski kurtarma noktaları silinebilir.

Kurtarma noktaları arşive taşındığında, bunlar 180 günlük erken silme dönemine tabi. Ücretler eşit olarak dağıtılır. 180 gün boyunca Arşivi olmayan bir kurtarma noktası silinirse, Standart katmanda harcadığı gün sayısına göre 180 eksi, maliyet ile eşdeğer olur.

En az altı ay için arşivde olmayan kurtarma noktaları, silme sırasında erken silme maliyetine neden olur.

## <a name="stop-protection-and-delete-data"></a>Korumayı durdurma ve verileri silme

Korumayı Durdur ve verileri sil tüm kurtarma noktalarını siler. Arşiv katmanında 180 gün boyunca bir süre olmayan arşiv içindeki kurtarma noktaları için, kurtarma noktalarını silme işlemi erken silme maliyetine neden olur.

## <a name="error-codes-and-troubleshooting-steps"></a>Hata kodları ve sorun giderme adımları

Kurtarma noktası arşive taşınamadığından oluşan birkaç hata kodu vardır.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Hata iletisi** -Recovery-Point türü arşiv taşıma için uygun değil

**Açıklama** – bu hata kodu, seçilen kurtarma noktası türü arşive taşınmaya uygun olmadığında gösterilir.

**Önerilen eylem** – [kurtarma noktasının uygunluğunu](#scope-for-preview) denetleyin

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Hata iletisi** -geri yükleme için etkin bağımlılıklara sahip Recovery-Point arşiv taşıma için uygun değil

**Açıklama –** Seçili kurtarma noktası etkin bağımlılıklara sahip olduğundan arşive taşınamıyor.

**Önerilen eylem** – [kurtarma noktasının uygunluğunu](#scope-for-preview) denetleyin

### <a name="minlifespaninstandardrequiredforarchive"></a>Minlifespanınstandardrequiredforarchive

**Hata iletisi** -Standart katmanda gereken en düşük boyuttan daha az olduğu Için Recovery-Point arşiv taşıma için uygun değil

**Açıklama** : kurtarma noktası, Azure sanal makineleri için en az üç aya sahip Standart katmanda ve Azure sanal makinelerinde SQL Server için 45 gün sonra kalır

**Önerilen eylem** – [kurtarma noktasının uygunluğunu](#scope-for-preview) denetleyin

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Hata iletisi** -Recovery-Point kalan kullanım ömrü gereken en düşük değerden daha küçük.

**Açıklama** : Arşiv taşıma uygunluğu için bir kurtarma noktası için gereken minimum kullanım ömrü altı aydır.

**Önerilen eylem** – [kurtarma noktasının uygunluğunu](#scope-for-preview) denetleyin

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Hata iletisi** -Recovery-Point arşiv katmanına zaten taşındığından arşiv taşıma için uygun değil

**Açıklama** : Seçili kurtarma noktası zaten arşiv içinde. Bu nedenle arşive taşınmaya uygun değildir.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Hata iletisi** -veri kaynağı türü öneri API 'si için uygun değil.

**Açıklama** – ÖNERI API 'Si yalnızca Azure sanal makineleri için geçerlidir. Seçili veri kaynağı türü için geçerli değildir.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>Usererrorrecoverypointalreadyrehited

**Hata iletisi** -kurtarma noktası zaten yeniden kullanılıyor. Bu RP üzerinde yeniden doldurma yapılmasına izin verilmiyor.

**Açıklama** : Seçili kurtarma noktası zaten yeniden doldurma işlemi.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Hata iletisi** -kurtarma noktası arşiv taşıma için uygun değil.

**Açıklama** : Seçili kurtarma noktası arşiv taşıma için uygun değildir.

### <a name="usererrorrecoverypointnotrehydrated"></a>Usererrorrecoverypointnotrehipoted

**Hata** **Iletisi** -arşiv kurtarma noktası yeniden doldurma işlemi geri alınmıştır. Arşiv RP 'de yeniden doldurma işlemi tamamlandıktan sonra geri yüklemeyi yeniden deneyin.

**Açıklama** : kurtarma noktası yeniden sarmalanmıyor. Kurtarma noktasını yeniden doldurma işleminden sonra geri yüklemeyi deneyin.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>Usererrorrecoverypointrehitionnotallowed

**Hata** **iletisi**-yeniden doldurma yalnızca arşiv kurtarma noktaları Için desteklenir-yeniden doldurma yalnızca arşiv kurtarma noktaları için desteklenir

**Açıklama** : Seçili kurtarma noktası için yeniden doldurma yapılmasına izin verilmez.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>Usererrorrecoverypointrehitionalreadınprogress

**Hata iletisi** – yeniden doldurma, arşiv kurtarma noktası için zaten In-Progress.

**Açıklama** : Seçili kurtarma noktası için yeniden doldurma işlemi zaten devam ediyor.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Hata iletisi** -kurtarma noktası ilkede belirtilen yetersiz saklama süresi nedeniyle arşiv katmanına taşınamıyor

**Önerilen eylem** -korunan öğede uygun saklama ayarı ile ilkeyi güncelleştirin ve yeniden deneyin.

### <a name="rpmovereadinesstobedetermined"></a>Rpmovereadinesstobebelirlendiği

**Hata iletisi** -bu kurtarma noktasının taşınıp taşınamayacağını belirlememiz devam ediyoruz.

**Açıklama** : kurtarma noktasının taşıma hazırlığı henüz belirlenememiştir.

**Önerilen eylem** -bir süre bekledikten sonra tekrar denetleyin.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Korumayı durdurup verileri korudum, kurtarma noktalarını arşivlemek için ne olur?

Kurtarma noktası süresiz olarak arşiv içinde kalır. Daha fazla bilgi için bkz. [kurtarma noktalarında korumayı durdurma etkisi](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Backup fiyatlandırması](azure-backup-pricing.md)