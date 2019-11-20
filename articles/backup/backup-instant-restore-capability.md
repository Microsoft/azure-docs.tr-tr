---
title: Azure anında geri yükleme özelliği
description: VM yedekleme yığını, Kaynak Yöneticisi dağıtım modeli için Azure anında geri yükleme özelliği ve SSS
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: c87dc98ddd1d5799f3363d7574063ef752fd8239
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172339"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Azure Backup Instant Restore özelliğiyle gelişmiş yedekleme ve geri yükleme performansı alın

> [!NOTE]
> Kullanıcılardan gelen geri bildirimlere bağlı olarak, Azure Stack işlevlerle karışıklık azaltmak için **VM yedekleme yığını v2** 'Yi **anında geri yüklemeye** yeniden adlandırdık.
> Tüm Azure Backup kullanıcıları artık **anında geri yüklemeye**yükseltildi.

Anında geri yükleme için yeni model aşağıdaki özellik geliştirmelerini sağlar:

* Kasaya veri aktarımını beklemek zorunda kalmadan kurtarma için kullanılabilen bir yedekleme işinin parçası olarak alınan anlık görüntüleri kullanma özelliği. Geri yüklemeyi tetiklemeden önce anlık görüntülerin kasaya kopyalaması için bekleme süresini azaltır.
* , Varsayılan olarak iki gün boyunca anlık görüntüleri yerel olarak tutarak yedekleme ve geri yükleme sürelerini azaltır. Bu varsayılan anlık görüntü saklama değeri 1 ila 5 gün arasında bir değere yapılandırılabilir.
* 4 TB 'a kadar disk boyutlarını destekler. Disklerin yeniden boyutlandırılması Azure Backup önerilmez. 4 TB 'den büyük ve boyutu 30 TB 'a kadar olan diskler için Azure Backup büyük disk desteğinin sınırlı bir genel önizlemesine kaydolmak için bkz. [disk boyutları 30 TB 'a kadar VM yedeklemesi](backup-azure-vms-introduction.md#limited-public-preview-backup-of-vm-with-disk-sizes-up-to-30tb).
* , Standart HDD disklerle ve Premium SSD disklerle birlikte Standart SSD diskleri destekler.
* Geri yükleme sırasında, yönetilmeyen VM 'Lerin orijinal depolama hesaplarını (disk başına) kullanma özelliği. Bu özellik, VM 'nin depolama hesapları arasında dağıtılan diskleri olduğunda bile vardır. Çok çeşitli VM yapılandırmalarının geri yükleme işlemlerini hızlandırır.
* Hızlı geri yükleme ile Premium depolama kullanan VM 'lerin yedeklenmesi için, **yalnızca** ilk yedekleme için gerekli olan toplam ayrılan depolama alanının *%50* boş alanını ayırmayı öneririz. İlk yedekleme tamamlandıktan sonra %50 boş alan yedeklemeler için bir gereksinim değildir.

## <a name="whats-new-in-this-feature"></a>Bu özelliğindeki yenilikler

Şu anda yedekleme işi iki aşamadan oluşur:

1. VM anlık görüntüsü alınıyor.
2. VM anlık görüntüsünü Azure kurtarma hizmetleri kasasına aktarma.

Bir kurtarma noktası yalnızca 1 ve 2. aşama tamamlandıktan sonra oluşturulan olarak değerlendirilir. Bu yükseltmenin bir parçası olarak, anlık görüntü tamamlandıktan hemen sonra bir kurtarma noktası oluşturulur ve bu anlık görüntü türü kurtarma noktası, aynı geri yükleme akışını kullanarak geri yükleme işlemi gerçekleştirmek için kullanılabilir. Bu kurtarma noktasını, kurtarma noktası türü olarak "Snapshot" kullanarak Azure portal tanımlayabilir ve anlık görüntü kasaya aktarıldıktan sonra, kurtarma noktası türü "anlık görüntü ve kasa" olarak değişir.

![VM yedekleme yığınında yedekleme işi Kaynak Yöneticisi dağıtım modeli--depolama ve kasa](./media/backup-azure-vms/instant-rp-flow.png)

Varsayılan olarak, anlık görüntüler iki gün boyunca tutulur. Bu özellik geri yükleme sürelerini kesip bu anlık görüntülerden geri yükleme işlemine izin verir. Verileri kasadan geri dönüştürmek ve kopyalamak için gereken süreyi azaltır.

## <a name="feature-considerations"></a>Özellik konuları

* Anlık görüntüler, kurtarma noktası oluşturmayı artırmak ve geri yükleme işlemlerini hızlandırmak için disklerle birlikte depolanır. Sonuç olarak, bu süre boyunca alınan anlık görüntülere karşılık gelen depolama maliyetlerini görürsünüz.
* Artımlı anlık görüntüler sayfa Blobları olarak depolanır. Yönetilmeyen diskleri kullanan tüm kullanıcılar yerel depolama hesabında depolanan anlık görüntüler için ücretlendirilir. Yönetilen VM yedeklemeleri tarafından kullanılan geri yükleme noktası koleksiyonları, temel alınan depolama düzeyinde blob anlık görüntüleri kullandığından, yönetilen diskler için blob anlık görüntü fiyatlandırmasına karşılık gelen maliyetleri görürsünüz ve bunlar artımlı olur.
* Premium Depolama hesapları için, anlık kurtarma noktaları için gerçekleştirilen anlık görüntüler, ayrılan alan 10 TB 'lık sınırına doğru sayılır.
* Anlık görüntü bekletmesini geri yükleme ihtiyaçlarına göre yapılandırma olanağı elde edersiniz. Gereksinime bağlı olarak, aşağıda açıklandığı gibi yedekleme ilkesi dikey penceresinde anlık görüntü bekletmesini en az bir güne ayarlayabilirsiniz. Bu, sık geri yükleme gerçekleştirmezseniz anlık görüntü bekletme için maliyeti kaydetmenize yardımcı olur.
* Bu tek yönlü bir yükseltmenize ve anında geri yüklemeye yükseltildikten sonra geri dönemezsiniz.

>[!NOTE]
>Bu anlık geri yükleme yükseltmesinde, tüm müşterilerin (**yeni ve var olan**) anlık görüntü saklama süresi iki günün varsayılan bir değerine ayarlanır. Bununla birlikte, gereksiniminize göre süresini 1 ila 5 gün arasında bir değere ayarlayabilirsiniz.

## <a name="cost-impact"></a>Maliyet etkisi

Artımlı anlık görüntüler, anlık kurtarma için kullanılan VM 'nin depolama hesabında depolanır. Artımlı anlık görüntü, bir anlık görüntünün kapladığı alanın, anlık görüntü oluşturulduktan sonra yazılan sayfaların kapladığı alana eşit olduğu anlamına gelir. Faturalandırma, anlık görüntü tarafından bulunan GB başına kullanılan alan için ve GB başına fiyat, [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/managed-disks/)belirtilen şekilde aynıdır.

>[!NOTE]
> Anlık görüntü saklama, haftalık ilkeler için 5 güne kadar düzeltilir.

## <a name="configure-snapshot-retention"></a>Anlık görüntü bekletmeyi yapılandırma

### <a name="using-azure-portal"></a>Azure portalını kullanma

Azure portal, **anlık geri yükleme** bölümünün altındaki **VM yedekleme ilkesi** dikey penceresinde eklenen bir alanı görebilirsiniz. Belirli bir yedekleme ilkesiyle ilişkili tüm VM 'Ler için **VM yedekleme ilkesi** dikey penceresinde anlık görüntü saklama süresini değiştirebilirsiniz.

![Anında geri yükleme özelliği](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell’i kullanma

>[!NOTE]
> Az PowerShell Version 1.6.0 onenlerden itibaren, PowerShell kullanarak ilkede anlık geri yükleme anlık görüntü bekletme süresini güncelleştirebilirsiniz

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Her ilke için varsayılan anlık görüntü bekletme iki güne ayarlanır. Kullanıcı değeri en az 1 ve en fazla beş gün olarak değiştirebilir. Haftalık ilkeler için, anlık görüntü bekletme beş güne sabitlenmiştir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Anlık geri yüklemenin maliyet etkileri nelerdir?

Anlık görüntüler, kurtarma noktası oluşturma ve geri yükleme işlemlerinin hızlandırılmasına yönelik disklerle birlikte depolanır. Sonuç olarak, VM yedekleme ilkesinin bir parçası olarak seçilen anlık görüntü bekletmesine karşılık gelen depolama maliyetlerini görürsünüz.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Premium Depolama hesaplarında, anlık kurtarma noktası için alınan anlık görüntüler 10 TB 'lik anlık görüntü sınırını kaplasın mı?

Evet, Premium Depolama hesaplarında, anlık kurtarma noktası için alınan anlık görüntüler 10 TB ayrılmış anlık görüntü alanı kaplar.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Anlık görüntü bekletme, beş günlük dönemde nasıl çalışır?

Her gün yeni bir anlık görüntü çekilirken, beş ayrı Artımlı anlık görüntü vardır. Anlık görüntünün boyutu, %2 ' nin %7 ' si etrafında olan veri dalgalanmasına bağlıdır.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Anlık geri yükleme, artımlı bir anlık görüntü veya tam anlık görüntü mi?

Anlık geri yükleme özelliğinin bir parçası olarak alınan anlık görüntüler, artımlı anlık görüntülerdir.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Anında geri yükleme özelliği nedeniyle yaklaşık maliyet artışını nasıl hesaplayabilirim?

Bu, VM 'nin dalgalanmasına bağlıdır. Kararlı bir durumda, maliyet artışı = anlık görüntü saklama süresi GB başına VM depolama maliyeti başına günlük dalgalanması olduğunu varsayabilirsiniz.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Geri yükleme noktası için kurtarma türü "anlık görüntü ve kasa" ise ve geri yükleme işlemi gerçekleştirdiğimde, hangi kurtarma türü kullanılacaktır?

Kurtarma türü "anlık görüntü ve kasa" ise, geri yükleme işlemi, kasadan yapılan geri yükleme ile karşılaştırıldığında çok daha hızlı olacak şekilde yerel anlık görüntüden otomatik olarak yapılır.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Geri yükleme noktası bekletme süresi (katman 2), anlık görüntü (Katman1) saklama süresinden daha az olursa ne olur?

Anlık görüntü (Katman1) silinene kadar yeni model geri yükleme noktasını silmeye izin vermez (Katman2). Geri yükleme noktası (Katman2) bekletme dönemini, anlık görüntü saklama süresinden daha büyük bir süre içinde zamanlamayı öneririz.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Yedekleme ilkesindeki bekletme süresinden sonra bile anlık görüntüm neden var?

Kurtarma noktasında anlık görüntü varsa ve en son RP varsa, bir sonraki başarılı yedekleme olana kadar tutulur. Bu, şu anda, sanal makinenin bir sorunu nedeniyle tüm yedeklemelerin daha fazla başarısız olması durumunda, her zaman en az bir en son RP 'nin her zaman mevcut olmasını sağlayacak olan, tasarlanan "çöp toplama" (GC) ilkesine göre belirlenir. Normal senaryolarda RPs, süresi dolduktan sonra en fazla 24 saat içinde temizlenir.
