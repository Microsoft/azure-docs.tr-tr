---
title: Kurtarma Hizmetleri kasaları oluşturma ve yapılandırma
description: Bu makalede, yedeklemeleri ve kurtarma noktalarını depolayan kurtarma hizmetleri kasalarını oluşturma ve yapılandırma hakkında bilgi edinin. İkincil bir bölgede geri yüklemek için çapraz bölge geri yükleme 'yi nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 04/14/2021
ms.custom: references_regions
ms.openlocfilehash: 5e2983e473fac72d02f0fdbc8c307e96326ac0a6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518584"
---
# <a name="create-and-configure-a-recovery-services-vault"></a>Kurtarma Hizmetleri Kasası oluşturma ve yapılandırma

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy"></a>Depolama artıklığı ayarlama

Azure Backup kasa için depolamayı otomatik olarak işler. Bu depolamanın nasıl çoğaltılacağı belirtmeniz gerekir.

> [!NOTE]
> Bir kurtarma hizmetleri Kasası için **depolama çoğaltma türünü** değiştirme (yerel olarak yedekli/coğrafi olarak yedekli), kasadaki yedeklemeleri yapılandırmadan önce yapılmalıdır. Yedeklemeyi yapılandırdıktan sonra, değiştirme seçeneği devre dışı bırakılır.
>
>- Yedeklemeyi henüz yapılandırmadıysanız, ayarları gözden geçirmek ve değiştirmek için [aşağıdaki adımları izleyin](#set-storage-redundancy) .
>- Yedeklemeyi zaten yapılandırdıysanız ve GRS 'den LRS 'ye geçiş yapmanız gerekiyorsa, [Bu geçici çözümleri gözden geçirin](#how-to-change-from-grs-to-lrs-after-configuring-backup).

1. **Kurtarma Hizmetleri kasaları** bölmesinden yeni kasayı seçin. **Ayarlar** bölümünde **Özellikler**' i seçin.
1. **Özellikler**' de, **yedekleme yapılandırması** altında **Güncelleştir**' i seçin.

1. Depolama çoğaltma türünü seçin ve **Kaydet**' i seçin.

     ![Yeni kasa için depolama yapılandırması ayarlama](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

   - Azure 'u birincil yedek depolama uç noktası olarak kullanıyorsanız, varsayılan **coğrafi olarak yedekli** ayarını kullanmaya devam edebilirsiniz.
   - Azure’u birincil yedek depolama uç noktası olarak kullanmıyorsanız, Azure depolama maliyetlerini azaltan **Yerel olarak yedekli** seçeneğini belirleyin.
   - [Coğrafi](../storage/common/storage-redundancy.md#geo-redundant-storage) ve [Yerel](../storage/common/storage-redundancy.md#locally-redundant-storage) artıklık hakkında daha fazla bilgi edinin.
   - Bir bölgede kesinti olmadan veri kullanılabilirliğine ihtiyaç duyuyorsanız, veri fazlalığını garanti edin, sonra [bölge yedekli depolama](../storage/common/storage-redundancy.md#zone-redundant-storage)' yı seçin.

>[!NOTE]
>Kasa için depolama çoğaltma ayarları, geçerli çözüm bağlı olduğu için Azure dosya paylaşma yedeğine uygun değildir ve kasaya hiçbir veri aktarılmaz. Anlık görüntüler, yedeklenen dosya paylaşımıyla aynı depolama hesabında depolanır.

## <a name="set-cross-region-restore"></a>Çapraz bölge geri yüklemeyi ayarla

**Çapraz bölge geri yükleme (CRR)** geri yükleme seçeneği, verileri Ikincil, [Azure eşlenmiş bir bölgeye](../best-practices-availability-paired-regions.md)geri yüklemenize olanak tanır.

Aşağıdaki veri kaynaklarını destekler:

- Azure VM 'Leri (genel kullanılabilirlik)
- Azure VM 'lerinde barındırılan SQL veritabanları (Önizleme)
- Azure VM 'lerinde barındırılan SAP HANA veritabanları (Önizleme)

Çapraz bölge geri yükleme kullanmak şunları yapmanıza olanak sağlar:

- Denetim veya uyumluluk gereksinimi olduğunda ayrıntıya ait ayrıntıları yürütün
- Birincil bölgede bir olağanüstü durum varsa verileri geri yükleme

Bir VM 'yi geri yüklerken VM 'yi veya diskini geri yükleyebilirsiniz. Azure VM 'lerinde barındırılan SQL/SAP HANA veritabanlarından geri yükleme yapıyorsanız veritabanlarını veya dosyalarını geri yükleyebilirsiniz.

Bu özelliği seçmek için **yedekleme yapılandırması** bölmesinden **çapraz bölge geri yüklemeyi etkinleştir** ' i seçin.

Bu işlem depolama düzeyinde olduğundan, [fiyatlandırma etkileri](https://azure.microsoft.com/pricing/details/backup/)vardır.

>[!NOTE]
>Başlamadan önce:
>
>- Desteklenen yönetilen türlerin ve bölgelerin listesi için [destek matrisini](backup-support-matrix.md#cross-region-restore) gözden geçirin.
>- Azure VM 'Leri için çapraz bölge geri yükleme (CRR) özelliği artık tüm Azure genel bölgelerinde genel kullanıma sunuldu.
>- SQL ve SAP HANA veritabanları için çapraz bölge geri yükleme, tüm Azure genel bölgelerinde önizleme aşamasındadır.
>- CRR, herhangi bir GRS Kasası için kasa düzeyi katılım özelliğidir (varsayılan olarak kapalıdır).
>- Bu işlem yapıldıktan sonra, yedekleme öğelerinin ikincil bölgelerde kullanılabilmesi 48 saat kadar sürebilir.
>- Azure VM 'leri için CRR Şu anda Azure VM 'leri ve şifrelenmiş Azure VM 'ler için Azure Resource Manager desteklenir. Klasik Azure VM 'Leri desteklenmez. Ek yönetim türleri CRR 'yi desteklediklerinde, bunlar **otomatik olarak** kaydedilir.
>- Koruma ilk kez başlatıldığında çapraz bölge geri yüklemesi şu anda GRS veya LRS 'ye **geri döndürülemez** .
>- Şu anda, [Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) çoğaltması 15 dakika olsa da, Ikincil bölge [RPO 'su](azure-backup-glossary.md#rpo-recovery-point-objective) birincil bölgeden 12 saate kadar sürer.

### <a name="configure-cross-region-restore"></a>Çapraz bölge geri yüklemeyi yapılandırma

GRS yedekliliği ile oluşturulan bir kasa, çapraz bölge geri yükleme özelliğini yapılandırma seçeneğini içerir. Her GRS kasasının bir başlığı olur ve bu, belgelere bağlanır. Kasa için CRR 'yi yapılandırmak için, bu özelliği etkinleştirme seçeneğini içeren yedekleme yapılandırması bölmesine gidin.

 ![Yedekleme yapılandırması başlığı](./media/backup-azure-arm-restore-vms/banner.png)

1. Portaldan kurtarma hizmetleri kasanıza > **Özellikler** ' e gidin ( **Ayarlar** altında).
1. **Yedekleme yapılandırması** altında **Güncelleştir**' i seçin.
1. İşlevselliği etkinleştirmek için **Bu kasada çapraz bölge geri yüklemeyi etkinleştir '** i seçin.

   ![Çapraz bölge geri yüklemeyi etkinleştir](./media/backup-azure-arm-restore-vms/backup-configuration.png)

CRR ile yedekleme ve geri yükleme hakkında daha fazla bilgi için şu makalelere bakın:

- [Azure VM 'Leri için çapraz bölge geri yükleme](backup-azure-arm-restore-vms.md#cross-region-restore)
- [SQL veritabanları için çapraz bölge geri yükleme](restore-sql-database-azure-vm.md#cross-region-restore)
- [SAP HANA veritabanları için çapraz bölge geri yükleme](sap-hana-db-restore.md#cross-region-restore)

## <a name="set-encryption-settings"></a>Şifreleme ayarlarını ayarla

Varsayılan olarak, kurtarma hizmetleri kasasındaki veriler platform tarafından yönetilen anahtarlar kullanılarak şifrelenir. Bu şifrelemeyi etkinleştirmek için sonlandırmadan açık bir eylem gerekmez ve kurtarma hizmetleri kasanıza yedeklenen tüm iş yükleri için geçerlidir.  Bu kasadaki yedekleme verilerini şifrelemek için kendi anahtarınızı getirmeyi tercih edebilirsiniz. Bu, müşteri tarafından yönetilen anahtarlar olarak adlandırılır. Yedekleme verilerini kendi anahtarınızı kullanarak şifrelemek isterseniz, herhangi bir öğe bu kasaya korunmadan önce şifreleme anahtarı belirtilmelidir. Anahtarınızla şifrelemeyi etkinleştirdikten sonra geri alınamaz.

### <a name="configuring-a-vault-to-encrypt-using-customer-managed-keys"></a>Müşteri tarafından yönetilen anahtarları kullanarak şifrelemek için bir kasa yapılandırma

Kasalarınızı müşteri tarafından yönetilen anahtarlarla şifrelemek üzere yapılandırmak için bu adımların bu sırada izlenmesi gerekir:

1. Kurtarma Hizmetleri kasanızda yönetilen kimliği etkinleştirin

1. Azure Key Vault şifreleme anahtarına erişmek için kasaya izin atayın

1. Azure Key Vault geçici silme ve Temizleme korumasını etkinleştirme

1. Şifreleme anahtarını kurtarma hizmetleri kasasına atama

Bu adımların her birine ilişkin yönergeler, [Bu makalede](encryption-at-rest-with-cmk.md#configuring-a-vault-to-encrypt-using-customer-managed-keys)bulunabilir.

## <a name="modifying-default-settings"></a>Varsayılan ayarları değiştirme

Kasada yedekleme yapılandırmadan önce **Depolama çoğaltma türü** ve **Güvenlik ayarları** için varsayılan değerleri mutlaka gözden geçirmeniz önerilir.

- **Depolama çoğaltma türü** varsayılan olarak **coğrafi olarak yedekli** (GRS) olarak ayarlanır. Yedeklemeyi yapılandırdıktan sonra, değiştirme seçeneği devre dışı bırakılır.
  - Yedeklemeyi henüz yapılandırmadıysanız, ayarları gözden geçirmek ve değiştirmek için [aşağıdaki adımları izleyin](#set-storage-redundancy) .
  - Yedeklemeyi zaten yapılandırdıysanız ve GRS 'den LRS 'ye geçiş yapmanız gerekiyorsa, [Bu geçici çözümleri gözden geçirin](#how-to-change-from-grs-to-lrs-after-configuring-backup).

- Varsayılan olarak **geçici silme** , yeni oluşturulan kasaların yanlışlıkla veya kötü amaçlı silmelerden yedekleme verilerini korumak için **etkinleştirilir** . Ayarları gözden geçirmek ve değiştirmek için [aşağıdaki adımları izleyin](./backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) .

### <a name="how-to-change-from-grs-to-lrs-after-configuring-backup"></a>Yedekleme yapılandırıldıktan sonra GRS 'den LRS 'ye değiştirme

GRS 'den yerel olarak yedekli depolama (LRS) ' ye taşımaya karar vermeden önce, senaryonuza uyan daha düşük maliyetli ve daha yüksek veri dayanıklılığı arasındaki denge konusunu gözden geçirin. GRS 'den LRS 'ye taşımanız gerekiyorsa iki seçeneğiniz vardır. Bunlar, yedekleme verilerini bekletmek için iş gereksinimlerinize bağlıdır:

- [Önceki yedeklenen verileri korumanız gerekmez](#dont-need-to-preserve-previous-backed-up-data)
- [Önceki yedeklenen verilerin korunması gerekir](#must-preserve-previous-backed-up-data)

#### <a name="dont-need-to-preserve-previous-backed-up-data"></a>Önceki yedeklenen verileri korumanız gerekmez

Yeni bir LRS kasasındaki iş yüklerini korumak için, geçerli koruma ve verilerin, GRS kasasında ve yedeklemelerde yeniden yapılandırılmış olması gerekir.

>[!WARNING]
>Aşağıdaki işlem bozucu olduğundan geri alınamaz. Korunan sunucuyla ilişkili tüm yedekleme verileri ve yedekleme öğeleri kalıcı olarak silinecek. Dikkatli olun.

GRS kasasında geçerli korumayı durdurun ve silin:

1. GRS Kasası özelliklerinde geçici silme devre dışı bırakın. Geçici silme devre dışı bırakmak için [aşağıdaki adımları](backup-azure-security-feature-cloud.md#disabling-soft-delete-using-azure-portal) izleyin.

1. Korumayı durdurun ve mevcut GRS kasasından yedeklemeleri silin. Kasa panosu menüsünde **yedekleme öğeleri**' ni seçin. Burada listelenen ve LRS kasasına taşınması gereken öğelerin, yedekleme verileriyle birlikte kaldırılması gerekir. [Bulutta korunan öğeleri silme](backup-azure-delete-vault.md#delete-protected-items-in-the-cloud) ve [Şirket içi korumalı öğeleri silme](backup-azure-delete-vault.md#delete-protected-items-on-premises)bölümüne bakın.

1. AFS 'yi (Azure dosya paylaşımlarını), SQL Server 'ı veya SAP HANA sunucularını taşımayı planlıyorsanız, bunları da silmeniz gerekir. Kasa panosu menüsünde, **Yedekleme altyapısı**' nı seçin. Bkz. [SQL Server kaydını silme](manage-monitor-sql-database-backup.md#unregister-a-sql-server-instance), [Azure dosya paylaşımları ile ilişkili bir depolama hesabının kaydını silme](manage-afs-backup.md#unregister-a-storage-account)ve [bir SAP HANA örneğinin kaydını silme](sap-hana-db-manage.md#unregister-an-sap-hana-instance).

1. GRS kasasından kaldırıldıktan sonra, yeni LRS kasasında iş yükünüz için yedeklemeleri yapılandırmaya devam edin.

#### <a name="must-preserve-previous-backed-up-data"></a>Önceki yedeklenen verilerin korunması gerekir

Geçerli korunan verileri GRS kasasında tutmanız ve yeni bir LRS kasasında korumaya devam etmeniz gerekiyorsa, bazı iş yükleri için sınırlı seçenekler vardır:

- MARS için, verileri tutma ve aracıyı yeni LRS kasasında kaydetme [ile korumayı durdurabilirsiniz](backup-azure-manage-mars.md#stop-protecting-files-and-folder-backup) .

  - Azure Backup hizmet, GRS kasasının tüm mevcut kurtarma noktalarını tutmaya devam edecektir.
  - Kurtarma noktalarını GRS kasasında tutmak için ödeme yapmanız gerekir.
  - Yalnızca GRS kasasındaki süre dolma edilmemiş kurtarma noktaları için yedeklenmiş verileri geri yükleyebileceksiniz.
  - LRS kasasında verilerin yeni bir ilk kopyasının oluşturulması gerekir.

- Bir Azure VM için, GRS kasasında VM için [verileri koruma ile korumayı durdurabilir](backup-azure-manage-vms.md#stop-protecting-a-vm) , VM 'yi başka bir kaynak grubuna taşıyabilir ve LRS KASASıNDAKI VM 'yi koruyabilirsiniz. Bir VM 'yi başka bir kaynak grubuna taşımak için [rehberlik ve sınırlamalar](../azure-resource-manager/management/move-limitations/virtual-machines-move-limitations.md) bölümüne bakın.

  Bir VM tek seferde yalnızca bir kasada korunabilir. Ancak, yeni kaynak grubundaki VM, farklı bir VM olarak kabul edildiği için LRS kasasında korunabilir.

  - Azure Backup hizmet, GRS kasasında yedeklenen kurtarma noktalarını korur.
  - Kurtarma noktalarını GRS kasasında tutmak için ödeme yapmanız gerekir (Ayrıntılar için [Azure Backup fiyatlandırmasına](azure-backup-pricing.md) bakın).
  - Gerekirse, GRS kasasından VM 'yi geri yükleyebileceksiniz.
  - Yeni kaynaktaki sanal makinenin LRS kasasındaki ilk yedekleme bir ilk çoğaltma olacaktır.

## <a name="next-steps"></a>Sonraki adımlar

[Hakkında bilgi edinin](backup-azure-recovery-services-vault-overview.md) Kurtarma Hizmetleri kasaları.
[Hakkında bilgi edinin](backup-azure-delete-vault.md) Kurtarma Hizmetleri kasalarını silin.