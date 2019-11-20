---
title: SSS-Azure sanal makinelerini yedekleme
description: Bu makalede, Azure VM 'Leri Azure Backup hizmetiyle yedekleme hakkında sık sorulan soruların yanıtlarını bulun.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 30036d6cf241e1ac840b2be67ca78fbda6c60061
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172571"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Sık sorulan sorular-Azure VM 'lerini yedekleme

Bu makalede, Azure VM 'Leri [Azure Backup](backup-introduction-to-azure-backup.md) hizmetiyle yedekleme hakkında sık sorulan sorular yanıtlanmaktadır.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Hangi VM görüntülerini oluştururken yedekleme için etkinleştirilebilir?

Bir VM oluşturduğunuzda, [desteklenen işletim sistemlerini](backup-support-matrix-iaas.md#supported-backup-actions) çalıştıran VM 'ler için yedeklemeyi etkinleştirebilirsiniz

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Yedekleme maliyeti VM maliyetine dahil mi?

Hayır. Yedekleme maliyetleri, bir VM 'nin maliyetlerinden ayrıdır. [Azure Backup fiyatlandırması](https://azure.microsoft.com/pricing/details/backup/)hakkında daha fazla bilgi edinin.

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Bir VM için yedeklemeyi etkinleştirmek için hangi izinler gereklidir?

Sanal makine katılımcısı ise, VM 'de yedeklemeyi etkinleştirebilirsiniz. Özel bir rol kullanıyorsanız, VM 'de yedeklemeyi etkinleştirmek için aşağıdaki izinlere sahip olmanız gerekir:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft. RecoveryServices/konumlar/*
- Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/*/Read
- Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/Read
- Microsoft. RecoveryServices/Vaults/Backupdokuları/protectionContainers/korunabilir/yazma
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Kurtarma Hizmetleri kasanızın ve sanal makinenizin farklı kaynak grupları varsa, kurtarma hizmetleri kasasının kaynak grubunda yazma izinlerinizin olduğundan emin olun.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>İsteğe bağlı bir yedekleme işi, zamanlanmış yedeklemeler ile aynı bekletme zamanlamasını kullanıyor mu?

Hayır. İsteğe bağlı yedekleme işi için bekletme aralığını belirtin. Varsayılan olarak, portaldan tetiklendiğinde 30 gün boyunca korunur.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Kısa süre önce bazı sanal makinelerde Azure Disk Şifrelemesi'ni etkinleştirdim. Yedeklemelerim çalışmaya devam edecek mi?

Azure Backup erişim Key Vault için izinler sağlayın. PowerShell 'de [Azure Backup PowerShell](backup-azure-vms-automation.md) belgelerindeki **yedeklemeyi etkinleştirme** bölümünde açıklandığı gibi izinleri belirtin.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>VM disklerini yönetilen disklere geçirdim. Yedeklemelerim çalışmaya devam edecek mi?

Evet, yedeklemeler sorunsuz şekilde çalışır. Herhangi bir şeyi yeniden yapılandırmanız gerekmez.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Yedeklemeyi Yapılandır sihirbazında neden VM’mi göremiyorum?

Sihirbaz yalnızca kasala aynı bölgedeki VM 'Leri listeler ve henüz yedeklenmemektedir.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>VM 'im kapatıldı. İsteğe bağlı veya zamanlanmış bir yedekleme işi mi olacak?

Evet. Yedeklemeler bir makine kapatıldığında çalışır. Kurtarma noktası kilitlenmeyle tutarlı olarak işaretlenir.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Devam eden bir yedekleme işini iptal edebilir miyim?

Evet. Bir **anlık görüntü** durumunda yedekleme işini iptal edebilirsiniz. Anlık görüntüden veri aktarımı devam ediyorsa bir işi iptal edemezsiniz.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Azure Backup hizmeti tarafından oluşturulan kaynak grubunda kilidi etkinleştirdim (ör. `AzureBackupRG_<geo>_<number>`), yedeklemelerim çalışmaya devam edecek mi?

Azure Backup hizmeti tarafından oluşturulan kaynak grubunu kilitlerseniz, en fazla 18 geri yükleme noktası sınırı olduğundan yedeklemeler başarısız olur.

Sonraki yedeklemelerin başarılı olması için kullanıcının kilidi kaldırması ve bu kaynak grubundan geri yükleme noktası koleksiyonunu temizlenmesi gerekir, geri yükleme noktası koleksiyonunu kaldırmak için [aşağıdaki adımları izleyin](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) .

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Azure Backup, SSD tarafından yönetilen standart diskleri destekliyor mu?

Evet, Azure Backup [Standart SSD yönetilen diskleri](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)destekler.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Yazma Hızlandırıcısı (WA) özellikli bir diskle VM 'yi yedekleyebiliriz?

Anlık görüntüler, WA özellikli diskte alınamaz. Ancak Azure Backup hizmeti, WA özellikli diski yedekten hariç tutabilir.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Yazma Hızlandırıcısı (WA) diskleri olan ve SAP HANA yüklü bir VM kullanıyorum. Nasıl yaparım? yedekleme yapılsın mı?

Azure Backup, WA özellikli diski yedekleyebilir, ancak bunu yedekten hariç tutabilir. Ancak, WA özellikli disk hakkında bilgi yedeklenmediği için yedekleme veritabanı tutarlılığı sağlamaz. İşletim sistemi diski yedeklemesini ve WA etkin olmayan disklerin yedeklemesini istiyorsanız bu yapılandırmayla diskleri yedekleyebilirsiniz.

Bir SAP HANA yedeklemesi için, 15 dakikalık bir RPO 'SU için özel önizleme çalıştırıyoruz. SQL DB yedeklemesine benzer bir şekilde oluşturulmuştur ve SAP HANA tarafından sertifikalandırılmış üçüncü taraf çözümler için Backınt arabirimini kullanır. İlginizi çekiyorsunuz, **Azure VM 'lerinde SAP HANA yedeklemesi için konunun özel önizlemesine**sahip `AskAzureBackupTeam@microsoft.com` adresinden bize e-posta gönderin.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Yedekleme başlangıç zamanında, VM yedekleme ilkemdeki ayarlanmış olan zamanlanmış yedekleme zamanından maksimum gecikme süresini tahmin edebilirim?

Zamanlanan yedekleme, zamanlanan yedekleme zamanının 2 saat içinde tetiklenecektir. Örneğin, 100 VM 'Lerin yedekleme başlangıç zamanı 2:00 ' de zamanlanırsa, en fazla 4:00 ' ye kadar tüm 100 VM 'Lerde yedekleme işi devam eder. Zamanlanan yedeklemeler kesinti ve sürdürülme/yeniden denenme nedeniyle duraklatılmışsa, yedekleme Bu zamanlanmış iki saatlik pencerenin dışında başlayabilir.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Günlük yedekleme noktası için izin verilen en düşük saklama aralığı nedir?

Azure sanal makine yedekleme ilkesi, en az 9999 güne kadar yedi günlük bekletme aralığını destekler. Yedi günden daha kısa bir süre içinde mevcut bir VM yedekleme ilkesinde yapılan değişiklikler, yedi günlük en düşük bekletme aralığını karşılamak için bir güncelleştirme gerektirir.

## <a name="restore"></a>Geri Yükleme

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Yalnızca disklerin mi yoksa tam bir VM 'nin mi geri yüklenemeyeceğine karar mi Nasıl yaparım??

Bir Azure VM için hızlı oluşturma seçeneği olarak bir VM geri yüklemeyi düşünün. Bu seçenek disk adlarını, diskler tarafından kullanılan kapsayıcıları, genel IP adreslerini ve ağ arabirimi adlarını değiştirir. Değişiklik, bir VM oluşturulduğunda benzersiz kaynakları korur. VM bir kullanılabilirlik kümesine eklenmez.

Şunları yapmak istiyorsanız diski geri yükle seçeneğini kullanabilirsiniz:

- Oluşturulan VM 'yi özelleştirin. Örneğin, boyutunu değiştirin.
- Yedekleme sırasında orada olmayan yapılandırma ayarlarını ekleyin.
- Oluşturulan kaynaklar için adlandırma kuralını denetleyin.
- VM 'yi bir kullanılabilirlik kümesine ekleyin.
- PowerShell veya şablon kullanılarak yapılandırılması gereken başka bir ayar ekleyin.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Yönetilen disklere yükselttikten sonra, yönetilmeyen VM disklerinin yedeklerini geri yükleyebilir miyim?

Evet, diskler yönetilmeyen 'dan yönetilene geçirilmeden önce alınan yedeklemeleri kullanabilirsiniz.

- Varsayılan olarak, bir geri yükleme VM işi yönetilmeyen bir VM oluşturur.
- Ancak, diskleri geri yükleyebilir ve yönetilen bir VM oluşturmak için kullanabilirsiniz.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>VM’yi nasıl yönetilen disklere geçirilmeden önceki bir geri yükleme noktasına geri yüklerim?

Varsayılan olarak, bir geri yükleme VM işi, yönetilmeyen disklere sahip bir VM oluşturur. Yönetilen disklerle bir VM oluşturmak için:

1. [Yönetilmeyen disklere geri yükleyin](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Geri yüklenen diskleri yönetilen disklere dönüştürün](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Yönetilen disklerle BIR VM oluşturun](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

PowerShell 'de bunu yapma hakkında [daha fazla bilgi edinin](backup-azure-vms-automation.md#restore-an-azure-vm) .

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Silinen VM 'yi geri yükleyebilir miyim?

Evet. VM 'yi silseniz bile kasadaki karşılık gelen yedekleme öğesine gidebilir ve bir kurtarma noktasından geri yükleme yapabilirsiniz.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Bir VM 'yi aynı Kullanılabilirlik kümelerine geri yükleme

Yönetilen disk Azure VM için, kullanılabilirlik kümelerine geri yükleme, yönetilen diskler olarak geri yüklenirken şablon içinde bir seçenek sunarak etkinleştirilir. Bu şablon, **kullanılabilirlik kümeleri**adlı giriş parametresine sahiptir.

### <a name="how-do-we-get-faster-restore-performances"></a>Daha hızlı geri yükleme performanslarını nasıl sağlıyoruz?

[Anında geri yükleme](backup-instant-restore-capability.md) özelliği, anlık görüntülerden daha hızlı yedeklemeler ve anında geri yüklemeler sağlanmasına yardımcı olur.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Şifrelenmiş VM için Anahtar Kasası ayarlarını değiştirdiğimiz zaman ne olur?

Şifrelenmiş VM için Anahtar Kasası ayarlarını değiştirdikten sonra yedeklemeler, yeni ayrıntı kümesiyle çalışmaya devam edecektir. Ancak, değişiklikten önceki bir kurtarma noktasından geri yükleme yapıldıktan sonra, VM 'den oluşturabilmeniz için bir anahtar kasasındaki gizli dizileri geri yüklemeniz gerekir. Daha fazla bilgi için bu [makaleye](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret) bakın

Gizli/anahtar alma işlemleri gibi işlemler bu adımı gerektirmez ve geri yüklemeden sonra aynı Keykasası kullanılabilir.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>VM 'nin etki alanı denetleyicisiyle ilişkisi kopmuş olması nedeniyle, geri yüklendikten sonra sanal makineye erişebilir miyim?

Evet, bir VM 'nin etki alanı denetleyicisiyle ilişkisi kopmuş olması nedeniyle VM 'ye bir kez erişirsiniz. Daha fazla bilgi için bu [makaleye](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) bakın

## <a name="manage-vm-backups"></a>VM yedeklemelerini yönetme

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Bir yedekleme ilkesini değiştirdiğimde ne olur?

VM, değiştirilen veya yeni ilkedeki zamanlama ve bekletme ayarları kullanılarak yedeklenir.

- Bekletme genişletildiğinde, var olan kurtarma noktaları işaretlenir ve yeni ilkeye uygun olarak tutulur.
- Bekletme azaltıldığında, kurtarma noktaları sonraki temizleme işinde ayıklanmayı işaretlenir ve sonra silinir.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Azure Backup tarafından yedeklenen bir VM 'yi farklı bir kaynak grubuna taşımak Nasıl yaparım? istiyor musunuz?

1. Yedeklemeyi geçici olarak durdurun ve yedekleme verilerini koruyun.
2. VM 'yi hedef kaynak grubuna taşıyın.
3. Yedekleme, aynı veya yeni kasada yeniden etkinleştirildi.

Taşıma işleminden önce oluşturulan kullanılabilir geri yükleme noktalarından VM 'yi geri yükleyebilirsiniz.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Aynı yedekleme ilkesiyle ilişkilendirilebilen VM sayısıyla ilgili bir sınır var mı?

Evet, portaldan aynı yedekleme ilkesiyle ilişkilendirilebilen 100 VM sınırının bir sınırı vardır. 100 ' den fazla VM için, aynı zamanlamaya veya farklı zamanlamaya sahip birden fazla yedekleme ilkesi oluşturmanız önerilir.
