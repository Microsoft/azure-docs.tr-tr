---
title: Azure anında geri yükleme özelliği
description: VM yedekleme yığını, Kaynak Yöneticisi dağıtım modeli için Azure anında geri yükleme özelliği ve SSS
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: 3448b162c17dec2ab5b7637a3527d1c470bd415c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618585"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Azure Backup Instant Restore özelliğiyle gelişmiş yedekleme ve geri yükleme performansı alın

> [!NOTE]
> Kullanıcılardan gelen geri bildirimlere bağlı olarak, Azure Stack işlevlerle karışıklık azaltmak için **VM yedekleme yığını v2** 'Yi **anında geri yükleme** olarak yeniden adlandırdık.
> Tüm Azure Backup kullanıcılar artık **anında geri yüklemeye** yükseltildi.

Anında geri yükleme için yeni model aşağıdaki özellik geliştirmelerini sağlar:

* Kasaya veri aktarımını beklemek zorunda kalmadan kurtarma için kullanılabilen bir yedekleme işinin parçası olarak alınan anlık görüntüleri kullanma özelliği. Geri yüklemeyi tetiklemeden önce anlık görüntülerin kasaya kopyalaması için bekleme süresini azaltır.
* , Varsayılan olarak iki gün boyunca anlık görüntüleri yerel olarak tutarak yedekleme ve geri yükleme sürelerini azaltır. Bu varsayılan anlık görüntü saklama değeri 1 ila 5 gün arasında bir değere yapılandırılabilir.
* 32 TB 'a kadar disk boyutlarını destekler. Disklerin yeniden boyutlandırılması Azure Backup önerilmez.
* , Standart HDD disklerle ve Premium SSD disklerle birlikte Standart SSD diskleri destekler.
* Geri yükleme sırasında, yönetilmeyen VM 'Lerin orijinal depolama hesaplarını (disk başına) kullanma özelliği. Bu özellik, VM 'nin depolama hesapları arasında dağıtılan diskleri olduğunda bile vardır. Çok çeşitli VM yapılandırmalarının geri yükleme işlemlerini hızlandırır.
* Depolama hesaplarında yönetilmeyen Premium diskleri kullanan VM 'lerin yedeklenmesi için, anında geri yükleme ile, **yalnızca** ilk yedekleme için gerekli olan toplam ayrılan depolama alanının *%50* boş alanını ayırmayı öneririz. İlk yedekleme tamamlandıktan sonra %50 boş alan yedeklemeler için bir gereksinim değildir.

## <a name="whats-new-in-this-feature"></a>Bu özelliğindeki yenilikler

Yedekleme işi iki aşamadan oluşur:

1. VM'nin anlık görüntüsünü alma.
2. VM'nin anlık görüntüsünü Azure Kurtarma Hizmetleri kasasına aktarma.

Ancak 1 ve 2 numaralı aşamalar tamamlandıktan sonra bir kurtarma noktası oluşturulur. Bu yükseltmenin bir parçası olarak, anlık görüntü tamamlandıktan hemen sonra bir kurtarma noktası oluşturulur ve bu anlık görüntü kurtarma noktası, aynı geri yükleme akışını kullanarak geri yükleme işlemi gerçekleştirmek için kullanılabilir. Bu kurtarma noktasını, kurtarma noktası türü olarak "Snapshot" kullanarak Azure portal tanımlayabilir ve anlık görüntü kasaya aktarıldıktan sonra, kurtarma noktası türü "anlık görüntü ve kasa" olarak değişir.

![VM yedekleme yığınında yedekleme işi Kaynak Yöneticisi dağıtım modeli--depolama ve kasa](./media/backup-azure-vms/instant-rp-flow.png)

Varsayılan olarak, anlık görüntüler iki gün boyunca tutulur. Bu özellik geri yükleme sürelerini kesip bu anlık görüntülerden geri yükleme işlemine izin verir. Verileri kasadan geri dönüştürmek ve kopyalamak için gereken süreyi azaltır.

## <a name="feature-considerations"></a>Özellik konuları

* Anlık görüntüler, kurtarma noktası oluşturmayı artırmak ve geri yükleme işlemlerini hızlandırmak için disklerle birlikte depolanır. Sonuç olarak, bu süre boyunca alınan anlık görüntülere karşılık gelen depolama maliyetlerini görürsünüz.
* Artımlı anlık görüntüler sayfa Blobları olarak depolanır. Yönetilmeyen diskleri kullanan tüm kullanıcılar yerel depolama hesabında depolanan anlık görüntüler için ücretlendirilir. Yönetilen VM yedeklemeleri tarafından kullanılan geri yükleme noktası koleksiyonları, temel alınan depolama düzeyinde blob anlık görüntüleri kullandığından, yönetilen diskler için blob anlık görüntü fiyatlandırmasına karşılık gelen maliyetleri görürsünüz ve bunlar artımlı olarak yapılır.
* Premium Depolama hesapları için, anlık kurtarma noktaları için gerçekleştirilen anlık görüntüler, ayrılan alan 10 TB 'lık sınırına doğru sayılır.
* Anlık görüntü bekletmesini geri yükleme ihtiyaçlarına göre yapılandırma olanağı elde edersiniz. Gereksinime bağlı olarak, aşağıda açıklandığı gibi yedekleme ilkesi bölmesinde anlık görüntü bekletmesini en az bir güne ayarlayabilirsiniz. Bu, sık geri yükleme gerçekleştirmezseniz anlık görüntü bekletme için maliyeti kaydetmenize yardımcı olur.
* Tek yönlü bir yükseltme. Anında geri yüklemeye yükseltildikten sonra geri dönemezsiniz.

>[!NOTE]
>Bu anlık geri yükleme yükseltmesinde, tüm müşterilerin (**yeni ve var olan**) anlık görüntü saklama süresi iki günün varsayılan bir değerine ayarlanır. Ancak, süreyi 1 ila 5 gün arasında bir değere göre gereksinime göre ayarlayabilirsiniz.

## <a name="cost-impact"></a>Maliyet etkisi

Artımlı anlık görüntüler, anlık kurtarma için kullanılan sanal makinenin depolama hesabında depolanır. Artımlı anlık görüntü, bir anlık görüntünün kapladığı alanın, anlık görüntü oluşturulduktan sonra yazılan sayfaların kapladığı alana eşit olduğu anlamına gelir. Faturalandırma, anlık görüntünün kapladığı GB başına kullanılan alan için hala ve GB başına fiyat, [fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/managed-disks/)belirtilen şekilde aynıdır. Yönetilmeyen diskler kullanan VM 'Ler için, anlık görüntüler her diskin VHD dosyasının menüsünde görülebilir. Yönetilen diskler için anlık görüntüler, belirlenen bir kaynak grubundaki bir geri yükleme noktası koleksiyonu kaynağında depolanır ve anlık görüntülerin kendisi doğrudan görünür olmaz.

>[!NOTE]
> Anlık görüntü saklama, haftalık ilkeler için 5 güne kadar düzeltilir.

## <a name="configure-snapshot-retention"></a>Anlık görüntü bekletmeyi yapılandırma

### <a name="using-azure-portal"></a>Azure portalını kullanma

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

Azure portal, **anlık geri yükleme** bölümünün altında **VM yedekleme ilkesi** bölmesine eklenen bir alanı görebilirsiniz. Belirli bir yedekleme ilkesiyle ilişkili tüm VM 'Ler için **VM yedekleme ilkesi** bölmesinden anlık görüntü saklama süresini değiştirebilirsiniz.

![Anında geri yükleme özelliği](./media/backup-azure-vms/instant-restore-capability.png)

### <a name="using-powershell"></a>PowerShell’i kullanma

>[!NOTE]
> Az PowerShell Version 1.6.0 onenlerden itibaren, PowerShell kullanarak ilkede anlık geri yükleme anlık görüntü bekletme süresini güncelleştirebilirsiniz

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM"
$bkpPol.SnapshotRetentionInDays=5
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

Her ilke için varsayılan anlık görüntü bekletme iki güne ayarlanır. Değeri en az 1 ve en fazla beş gün olacak şekilde değiştirebilirsiniz. Haftalık ilkeler için, anlık görüntü bekletme beş güne sabitlenmiştir.

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

### <a name="why-does-my-snapshot-still-exist-even-after-the-set-retention-period-in-backup-policy"></a>Yedekleme ilkesindeki bekletme süresinden sonra bile anlık görüntümi hala var?

Kurtarma noktasının bir anlık görüntüsü varsa ve en son kurtarma noktası varsa, bir sonraki başarılı yedeklemeye kadar tutulur. Bu, belirlenen "çöp toplama" (GC) ilkesine göre belirlenir. Sonraki tüm yedeklemelerin VM 'deki bir sorun nedeniyle başarısız olması durumunda, en az bir en son kurtarma noktasının her zaman mevcut olması gerekir. Normal senaryolarda kurtarma noktaları, süresi dolduktan sonra en fazla 24 saat sonra temizlenir. Nadir senaryolarda, çöp toplayıcısının (GC) daha ağır yüküne göre bir veya daha fazla ek anlık görüntü olabilir.

### <a name="why-do-i-see-more-snapshots-than-my-retention-policy"></a>Neden bekletme ilkenğim daha fazla anlık görüntü görüyorum?

Bekletme ilkesinin "1" olarak ayarlandığı bir senaryoda, iki anlık görüntü bulabilirsiniz. Bu, VM 'deki bir sorun nedeniyle sonraki tüm yedeklemelerin başarısız olması durumunda, en az bir en son kurtarma noktasının her zaman mevcut olduğunu bu şekilde sağlar. Bu, iki anlık görüntü varolup oluşmasına neden olabilir.<br></br>Bu nedenle, ilke "n" anlık görüntülerle "n + 1" anlık görüntülerini zaman içinde bulabilirsiniz. Ayrıca, atık toplamada bir gecikme olursa "n + 1 + 2" anlık görüntülerini bile bulabilirsiniz. Bu durum nadiren zaman alabilir:
- Daha fazla bekletme olan anlık görüntüleri temizleyiyorsunuz.
- Arka uçtaki çöp toplayıcı (GC) ağır yük altında.

### <a name="i-dont-need-instant-restore-functionality-can-it-be-disabled"></a>Anlık geri yükleme işlevselliğine ihtiyacım yok. Devre dışı bırakılabilir mi?

Anlık geri yükleme özelliği herkes için etkin ve devre dışı bırakılamaz. Anlık görüntü bekletmesini en az bir güne azaltabilirsiniz.

### <a name="is-it-safe-to-restart-the-vm-during-the-transfer-process-which-can-take-many-hours-will-restarting-the-vm-interrupt-or-slow-down-the-transfer"></a>Aktarım işlemi sırasında VM 'yi yeniden başlatmak güvenli mi (birkaç saat sürebilir)? VM kesmesi yeniden başlatılıyor veya aktarma yavaşlıyor mu?

Evet ve veri aktarımı hızında kesinlikle hiçbir etkisi yoktur.

