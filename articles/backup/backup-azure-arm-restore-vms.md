---
title: Azure portal kullanarak VM 'Leri geri yükleme
description: Çapraz bölge geri yükleme özelliği de dahil olmak üzere Azure portal kullanarak bir Azure sanal makinesini kurtarma noktasından geri yükleyin.
ms.reviewer: geg
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 0f3a715f4fef85b90fd8f06558a8cfdab1ca8900
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739053"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure portal Azure VM verilerini geri yükleme

Bu makalede, Azure VM verilerinin [Azure Backup](backup-overview.md) kurtarma hizmetleri kasalarında depolanan kurtarma noktalarından nasıl geri yükleneceği açıklanmaktadır.

## <a name="restore-options"></a>Geri yükleme seçenekleri

Azure Backup bir VM 'yi geri yüklemek için çeşitli yollar sağlar.

**Geri yükleme seçeneği** | **Ayrıntılar**
--- | ---
**Yeni VM oluştur** | Geri yükleme noktasından hızlıca temel bir VM oluşturur ve çalıştırmaya başlar.<br/><br/> VM için bir ad belirtebilir, yerleştirileceği kaynak grubunu ve sanal ağı (VNet) seçin ve geri yüklenen VM için bir depolama hesabı belirtin. Yeni VM'nin kaynak VM ile aynı bölgede oluşturulması gerekir.<br><br>Azure VM SKU 'SU belirtilen Azure bölgesinde kullanılamadığından veya başka herhangi bir sorun nedeniyle bir VM geri yüklemesi başarısız olursa, Azure Backup belirtilen kaynak grubundaki diskleri geri yükler.
**Diski geri yükle** | Yeni bir VM oluşturmak üzere kullanılabilecek bir VM diskini geri yükler.<br/><br/> Azure Backup, VM oluşturmanıza ve özelleştirmenize yardımcı olacak bir şablon sağlar. <br/><br> Geri yükleme işi, özel VM ayarlarını belirtmek ve bir VM oluşturmak için indirebileceğiniz ve kullanabileceğiniz bir şablon oluşturur.<br/><br/> Diskler belirttiğiniz kaynak grubuna kopyalanır.<br/><br/> Alternatif olarak, diski mevcut bir sanal makineye iliştirebilir veya PowerShell kullanarak yeni bir VM oluşturabilirsiniz.<br/><br/> Bu seçenek VM'yi özelleştirmek, yedekleme sırasında mevcut olmayan yapılandırma ayarlarını eklemek veya şablon veya PowerShell kullanılarak yapılandırılması gereken ayarlar eklemek istediğinizde faydalı olur.
**Mevcut olanı değiştir** | Bir diski geri yükleyebilir ve mevcut VM 'deki bir diski değiştirmek için kullanabilirsiniz.<br/><br/> Geçerli VM'nin mevcut olması gerekir. Silinmesi durumunda bu seçenek kullanılamaz.<br/><br/> Azure Backup, diski değiştirmeden önce var olan sanal makinenin anlık görüntüsünü alır ve bunu belirttiğiniz hazırlama konumunda depolar. VM'ye bağlı olan mevcut diskler seçilen geri yükleme noktasıyla değiştirilir.<br/><br/> Anlık görüntü kasaya kopyalanır ve bekletme ilkesine göre saklanır. <br/><br/> Disk değiştirme işleminden sonra, özgün disk kaynak grubunda tutulur. Gerekli değilse, özgün diskleri el ile silmeyi tercih edebilirsiniz. <br/><br/>[Özel görüntüler kullanılarak oluşturulan](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)VM 'ler dahil olmak üzere, var olanı Değiştir, şifrelenmemiş yönetilen sanal makineler için desteklenir. Klasik VM 'ler, yönetilmeyen VM 'Ler ve [Genelleştirilmiş VM 'ler](../virtual-machines/windows/upload-generalized-managed.md)için desteklenmez.<br/><br/> Geri yükleme noktası geçerli VM 'den daha fazla veya daha az disk içeriyorsa, geri yükleme noktasındaki disk sayısı yalnızca VM yapılandırmasını yansıtacaktır.<br><br> Aynı zamanda, [Kullanıcı tarafından atanan yönetilen kimlik](../active-directory/managed-identities-azure-resources/overview.md) veya [Key Vault](../key-vault/general/overview.md)gibi bağlantılı kaynaklarla VM 'lerde de desteklenir.
**Bölgeler Arası (ikincil bölge)** | Çapraz bölge geri yükleme, Azure [eşlenmiş bölgesi](../best-practices-availability-paired-regions.md#what-are-paired-regions)olan Ikincil bölgedeki Azure VM 'lerini geri yüklemek için kullanılabilir.<br><br> Yedekleme ikincil bölgede yapıldığında, seçilen kurtarma noktası için tüm Azure VM 'lerini geri yükleyebilirsiniz.<br><br> Yedekleme sırasında, anlık görüntüler ikincil bölgeye çoğaltılmaz. Yalnızca kasada depolanan veriler çoğaltılır. Bu nedenle ikincil bölge geri yüklemeleri yalnızca [kasa katmanı](about-azure-vm-restore.md#concepts) geri yükleme amaçlıdır. İkincil bölgenin geri yükleme süresi, birincil bölgenin kasa katmanı geri yükleme süresi ile neredeyse aynı olacaktır.  <br><br> Bu özellik aşağıdaki seçenekler için kullanılabilir:<br> <li> [VM oluştur](#create-a-vm) <br> <li> [Diskleri geri yükle](#restore-disks) <br><br> [Mevcut diskleri Değiştir](#replace-existing-disks) seçeneğini şu anda desteklemiyoruz.<br><br> İzinler<br> İkincil bölgedeki geri yükleme işlemi, Backup Admins ve App Admins tarafından gerçekleştirilebilir.

> [!NOTE]
> Ayrıca, bir Azure VM 'de belirli dosya ve klasörleri kurtarabilirsiniz. [Daha fazla bilgi edinin](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Depolama hesapları

Depolama hesaplarıyla ilgili bazı ayrıntılar:

- **VM oluşturma**: yenı bir VM OLUŞTURDUĞUNUZDA, VM belirttiğiniz depolama hesabına yerleştirilir.
- **Diski geri yükle**: bir diski geri yüklerken disk, belirttiğiniz depolama hesabına kopyalanır. Geri yükleme işi, karşıdan yükleyebileceğiniz ve özel VM ayarlarını belirtmek için kullanabileceğiniz bir şablon oluşturur. Bu şablon, belirtilen depolama hesabına yerleştirilir.
- **Diski Değiştir**: mevcut bir sanal makinede bulunan bir diski değiştirirken Azure Backup, diski değiştirmeden önce var olan sanal makinenin anlık görüntüsünü alır. Anlık görüntü Ayrıca, bir arka plan işlemi olarak veri aktarımı aracılığıyla kurtarma hizmetleri kasasına kopyalanır. Ancak, anlık görüntü aşaması tamamlandıktan sonra diskleri değiştir işlemi tetiklenir. Disk değiştirme işleminden sonra, kaynak Azure VM 'nin diskleri, işlem için belirtilen kaynak grubunda bırakılır ve VHD 'ler belirtilen depolama hesabında depolanır. Bu VHD 'leri ve diskleri silmeyi veya tutmayı seçebilirsiniz.
- **Depolama hesabı konumu**: depolama hesabı kasayla aynı bölgede olmalıdır. Yalnızca bu hesaplar görüntülenir. Konumda depolama hesabı yoksa, bir tane oluşturmanız gerekir.
- **Depolama türü**: BLOB depolama desteklenmiyor.
- **Depolama artıklığı**: bölge yedekli depolama (ZRS) desteklenmez. Hesap için çoğaltma ve artıklık bilgileri, hesap adından sonra parantez içinde gösterilir.
- **Premium Depolama**:
  - Premium olmayan VM 'Leri geri yüklerken, Premium Depolama hesapları desteklenmez.
  - Yönetilen VM 'Leri geri yüklerken, ağ kurallarıyla yapılandırılmış Premium Depolama hesapları desteklenmez.

## <a name="before-you-start"></a>Başlamadan önce

Bir VM 'yi geri yüklemek için (yeni bir VM oluşturun), VM 'Yi geri yükleme işlemi için doğru Azure rol tabanlı erişim denetimi (Azure RBAC) [izinlerine](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) sahip olduğunuzdan emin olun.

İzinleriniz yoksa [bir diski geri yükleyebilir](#restore-disks)ve ardından disk geri yüklendikten sonra, yenı bir VM oluşturmak için geri yükleme işleminin bir parçası olarak oluşturulan [şablonu kullanabilirsiniz](#use-templates-to-customize-a-restored-vm) .

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="select-a-restore-point"></a>Geri yükleme noktası seçin

1. Geri yüklemek istediğiniz VM ile ilişkili kasada, **yedekleme öğeleri**  >  **Azure sanal makinesi**' ni seçin.
1. Bir VM seçin. Varsayılan olarak, VM panosunda son 30 günün kurtarma noktaları görüntülenir. 30 günden daha eski kurtarma noktalarını görüntüleyebilir veya tarihlere, zaman aralıklarına ve farklı anlık görüntü tutarlılığı türlerine göre kurtarma noktalarını bulmak için filtre uygulayabilirsiniz.
1. VM 'yi geri yüklemek için **VM 'Yi geri yükle**' yi seçin.

    ![Geri yükleme noktası](./media/backup-azure-arm-restore-vms/restore-point.png)

1. Kurtarma için kullanılacak bir geri yükleme noktası seçin.

## <a name="choose-a-vm-restore-configuration"></a>VM geri yükleme yapılandırması seçin

1. **Sanal makineyi geri yükle**' de bir geri yükleme seçeneği belirleyin:
    - **Yeni oluştur**: yenı bir VM oluşturmak istiyorsanız bu seçeneği kullanın. Basit ayarlarla bir VM oluşturabilir veya bir diski geri yükleyebilir ve özelleştirilmiş bir VM oluşturabilirsiniz.
    - **Varolanı değiştir**: mevcut bir sanal makinede bulunan diskleri değiştirmek istiyorsanız bu seçeneği kullanın.

        ![Sanal makine yapılandırma Sihirbazı 'nı geri yükleme](./media/backup-azure-arm-restore-vms/restore-configuration.png)

1. Seçtiğiniz geri yükleme seçeneğiniz için ayarları belirtin.

## <a name="create-a-vm"></a>VM oluşturma

[Geri yükleme seçeneklerinden](#restore-options)biri olarak, bir geri yükleme noktasından temel ayarlarla hızlı bir şekilde sanal makine oluşturabilirsiniz.

1. **Sanal makineyi geri yükle**  >  **Yeni**  >  **geri yükleme türü** oluştur ' da, **sanal makine oluştur**' u seçin.
1. **Sanal makine adı**' nda, abonelikte mevcut olmayan bir VM belirtin.
1. **Kaynak grubu**' nda, yeni VM için var olan bir kaynak grubunu seçin veya genel olarak benzersiz bir adla yeni bir tane oluşturun. Zaten var olan bir ad atarsanız Azure, gruba VM ile aynı adı atar.
1. Sanal ağ ' da, VM 'nin yerleştirileceği **sanal ağı** seçin. Abonelikle ilişkili tüm VNET 'ler görüntülenir. Alt ağı seçin. İlk alt ağ varsayılan olarak seçilidir.
1. **Hazırlama konumu**' nda, VM için depolama hesabını belirtin. [Daha fazla bilgi edinin](#storage-accounts).

    ![Yapılandırma geri yükleme Sihirbazı-geri yükleme seçeneklerini belirleyin](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

1. Geri yükleme işlemini tetiklemek için **geri yükle** ' yi seçin.

## <a name="restore-disks"></a>Diskleri geri yükleme

[Geri yükleme seçeneklerinden](#restore-options)biri olarak, bir geri yükleme noktasından bir disk oluşturabilirsiniz. Ardından disk ile aşağıdaki eylemlerden birini yapabilirsiniz:

- Ayarları özelleştirmek için geri yükleme işlemi sırasında oluşturulan şablonu kullanın ve VM dağıtımını tetikleyin. Varsayılan şablon ayarlarını düzenlersiniz ve VM dağıtımı için şablonu gönderebilirsiniz.
- [Geri yüklenen diskleri](../virtual-machines/windows/attach-managed-disk-portal.md) var olan bir sanal makineye ekleyin.
- PowerShell kullanarak geri yüklenen disklerden [Yeni BIR VM oluşturun](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) .

1. **Yapılandırma geri** yükleme  >  **Yeni**  >  **geri yükleme türü**' nde **diskleri geri yükle**' yi seçin.
1. **Kaynak grubu**' nda, geri yüklenen diskler için var olan bir kaynak grubunu seçin veya genel olarak benzersiz bir adla yeni bir tane oluşturun.
1. **Hazırlama konumu**' nda, VHD 'lerin kopyalanacağı depolama hesabını belirtin. [Daha fazla bilgi edinin](#storage-accounts).

    ![Kaynak grubunu ve hazırlama konumunu seçin](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

1. Geri yükleme işlemini tetiklemek için **geri yükle** ' yi seçin.

Sanal makineniz yönetilen diskler kullandığında ve **sanal makine oluştur** seçeneğini belirlediğinizde, Azure Backup belirtilen depolama hesabını kullanmaz. **Diskleri geri yükleme** ve **anında geri yükleme** durumunda depolama hesabı yalnızca şablonu depolamak için kullanılır. Yönetilen diskler, belirtilen kaynak grubunda oluşturulur.
Sanal makineniz yönetilmeyen diskler kullandığında, depolama hesabına Bloblar olarak geri yüklenir.

### <a name="use-templates-to-customize-a-restored-vm"></a>Geri yüklenen bir VM 'yi özelleştirmek için şablonları kullanma

Disk geri yüklendikten sonra, özelleştirmek ve yeni bir VM oluşturmak için geri yükleme işleminin bir parçası olarak oluşturulan şablonu kullanın:

1. **Yedekleme işleri** bölümünde ilgili geri yükleme işini seçin.

1. **Geri yükle**' de şablon dağıtımını başlatmak Için **şablonu dağıt** ' ı seçin.

    ![Geri yükleme işi detaya gitme](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

1. Şablonda belirtilen VM ayarını özelleştirmek için **Şablonu Düzenle**' yi seçin. Daha fazla özelleştirme eklemek istiyorsanız, **parametreleri Düzenle**' yi seçin.
    - Özel bir şablondan kaynak dağıtma hakkında [daha fazla bilgi edinin](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) .
    - Yazma şablonları hakkında [daha fazla bilgi edinin](../azure-resource-manager/templates/template-syntax.md) .

   ![Şablon dağıtımı yükle](./media/backup-azure-arm-restore-vms/edit-template1.png)

1. VM için özel değerleri girin, **hüküm ve koşulları** kabul edin ve **satın al**' ı seçin.

   ![Şablon dağıtımını gönder](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Mevcut diskleri Değiştir

[Geri yükleme seçeneklerinden](#restore-options)biri olarak, var olan bir VM diskini seçili geri yükleme noktasıyla değiştirebilirsiniz. Tüm geri yükleme seçeneklerini [gözden geçirin](#restore-options) .

1. **Yapılandırma geri yükleme**' de **Varolanı değiştir**' i seçin.
1. **Geri yükleme türü**' nde **disk/s 'yi Değiştir**' i seçin. Bu, var olan VM disklerini değiştirmek için kullanılacak geri yükleme noktasıdır.
1. **Hazırlama konumu** bölümünde, geri yükleme işlemi sırasında geçerli yönetilen disklerin anlık görüntülerinin kaydedileceği yeri belirtin. [Daha fazla bilgi edinin](#storage-accounts).

   ![Yapılandırma geri yükleme Sihirbazı var olanı Değiştir](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Çapraz bölge geri yükleme

[Geri yükleme seçeneklerinden](#restore-options)biri olan çapraz bölge geri yükleme (CRR), Azure sanal makinelerini bir Azure eşlenmiş bölgesi olan ikincil bir bölgeye geri yüklemenize olanak tanır.

Özelliği kullanmaya başlamak için başlamadan [önce bölümünü](./backup-create-rs-vault.md#set-cross-region-restore)okuyun.

CRR 'nin etkin olup olmadığını görmek için [çapraz bölge geri yüklemeyi yapılandırma](backup-create-rs-vault.md#configure-cross-region-restore)bölümündeki yönergeleri izleyin.

### <a name="view-backup-items-in-secondary-region"></a>İkincil bölgedeki yedekleme öğelerini görüntüle

CRR etkinse, yedekleme öğelerini ikincil bölgede görüntüleyebilirsiniz.

1. Portaldan **Kurtarma Hizmetleri Kasası**  >  **yedekleme öğeleri**' ne gidin.
1. İkincil bölgedeki öğeleri görüntülemek için **Ikincil bölge** ' yi seçin.

>[!NOTE]
>Yalnızca CRR özelliğini destekleyen yedekleme yönetim türleri listede gösterilir. Şu anda yalnızca ikincil bölge verilerinin bir ikincil bölgeye geri yüklenmesi için desteğe izin verilir.<br></br>Azure VM 'ler için CRR, Azure yönetilen VM 'ler (şifrelenmiş Azure VM 'Ler dahil) için desteklenir.

![İkincil bölgedeki sanal makineler](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Ikincil bölge seçin](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>İkincil bölgede geri yükleme

İkinci bölge geri yükleme kullanıcı deneyimi, birincil bölge geri yükleme kullanıcı deneyimiyle benzerdir. Geri yüklemeyi yapılandırmak için geri yükleme yapılandırma bölmesindeki Ayrıntılar yapılandırılırken yalnızca ikincil bölge parametreleri sağlamanız istenir.

Şu anda, [Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS)](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) çoğaltması 15 dakika olsa da, Ikincil bölge [RPO 'su](azure-backup-glossary.md#rpo-recovery-point-objective) birincil bölgeden 12 saate kadar sürer.

![Geri yüklenecek VM 'yi seçin](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Geri yükleme noktası seç](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Yapılandırma geri yükleme](./media/backup-azure-arm-restore-vms/rest-config.png)

![Geri yükleme tetikleme devam ediyor bildirimi](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Bir VM 'yi geri yüklemek ve oluşturmak için [VM oluşturma](#create-a-vm)bölümüne bakın.
- Disk olarak geri yüklemek için [diskleri geri yükleme](#restore-disks)bölümüne bakın.

>[!NOTE]
>
>- Geri yükleme tetiklendikten ve veri aktarımı aşamasında geri yükleme işi iptal edilemez.
>- Çapraz bölge geri yükleme özelliği, bir CMK etkin kurtarma hizmetleri kasasında yedeklenmeyen ve ikincil bölgedeki CMK özellikli VM 'Ler olarak yedeklenen CMK (müşteri tarafından yönetilen anahtarlar) etkin Azure VM 'lerini geri yükler.
>- İkincil bölgeye geri yüklemek için gereken Azure rolleri, birincil bölgeyle aynı olanlardır.
>- Bir Azure VM 'yi geri yüklerken, Azure Backup ikincil bölgedeki sanal ağ ayarlarını otomatik olarak yapılandırır. Şablonu dağıtma sırasında [diskleri geri yüklüyorsanız](#restore-disks) , ikincil bölgeye karşılık gelen sanal ağ ayarlarını sağladığınızdan emin olun.

[Azure Zone sabitlenmiş VM 'ler](../virtual-machines/windows/create-portal-availability-zone.md) , aynı bölgenin tüm [kullanılabilirlik bölgelerinde](../availability-zones/az-overview.md) geri yüklenebilir.

Geri yükleme sürecinde, seçenek **kullanılabilirlik bölgesi** ' ni görürsünüz. Önce varsayılan bölgeyi görürsünüz. Farklı bir bölge seçmek için istediğiniz bölgenin numarasını seçin. Sabitlenmiş bölge kullanılamıyorsa, yedeklenen veriler bölge dışı olarak çoğaltılmadığından verileri başka bir bölgeye geri yükleyemezsiniz. Kullanılabilirlik bölgelerinde geri yükleme yalnızca kasa katmanındaki kurtarma noktalarından mümkündür.

![Kullanılabilirlik alanı seç](./media/backup-azure-arm-restore-vms/cross-zonal-restore.png)

### <a name="monitoring-secondary-region-restore-jobs"></a>İkincil bölge geri yükleme işlerini izleme

1. Portaldan **Kurtarma Hizmetleri Kasası**  >  **yedekleme işleri** ' ne gidin
1. İkincil bölgedeki öğeleri görüntülemek için **Ikincil bölge** ' yi seçin.

    ![Filtrelenen yedekleme işleri](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restoring-unmanaged-vms-and-disks-as-managed"></a>Yönetilmeyen VM 'Leri ve diskleri yönetilen olarak geri yükleme

[Yönetilmeyen diskleri](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks) geri yükleme sırasında [yönetilen diskler](../virtual-machines/managed-disks-overview.md) olarak geri yükleme seçeneği sunulur. Varsayılan olarak, yönetilmeyen VM 'Ler/diskler, yönetilmeyen VM 'Ler/diskler olarak geri yüklenir. Ancak, yönetilen VM 'Ler/diskler olarak geri yüklemeyi tercih ederseniz, artık bunu yapmak mümkündür. Bu geri yüklemeler, anlık görüntü aşamasından tetiklenemez, ancak yalnızca kasa aşamasından tetiklenmez. Bu özellik, yönetilmeyen şifreli VM 'Ler için kullanılamaz.

![Yönetilen diskler olarak geri yükleme](./media/backup-azure-arm-restore-vms/restore-as-managed-disks.png)

## <a name="restore-vms-with-special-configurations"></a>Özel yapılandırmalara sahip VM 'Leri geri yükleme

VM 'Leri geri yüklemeniz gerekebilecek pek çok yaygın senaryo vardır.

**Senaryo** | **Rehber**
--- | ---
**Karma kullanım teklifi kullanarak VM 'Leri geri yükleme** | Bir Windows VM [karma kullanım avantajı (hub) lisanslama](../virtual-machines/windows/hybrid-use-benefit-licensing.md)kullanıyorsa, diskleri geri yükleyin ve belirtilen şablonu ( **Lisans türü** **Windows_Server** olarak ayarlanmış olan) veya PowerShell 'i kullanarak yeni bir VM oluşturun.  Bu ayar VM oluşturulduktan sonra da uygulanabilir.
**Azure veri merkezi olağanüstü durum sırasında VM 'Leri geri yükleme** | Kasa GRS kullanıyorsa ve VM için birincil veri merkezi kapalıysa, Azure Backup yedeklenen VM 'Leri eşleştirilmiş veri merkezine geri yüklemeyi destekler. Eşleştirilmiş veri merkezinde bir depolama hesabı seçin ve normal olarak geri yükleyin. Azure Backup, geri yüklenen VM 'yi oluşturmak için eşleştirilmiş bölgedeki işlem hizmetini kullanır. Veri merkezi dayanıklılığı hakkında [daha fazla bilgi edinin](/azure/architecture/resiliency/recovery-loss-azure-region) .<br><br> Kasa GRS kullanıyorsa, [çapraz bölge geri yükleme](#cross-region-restore)yeni özelliğini seçebilirsiniz. Bu, tam veya kısmi kesinti senaryolarında ikinci bir bölgeye geri yükleme yapmanızı sağlar veya hiç kesinti olmasa bile.
**Tam geri yükleme** | Azure VM 'Leri ve şirket içi hiper yöneticilerde yer alan başlıca fark, Azure 'da kullanılabilir VM konsolunun olmaması olabilir. Bir konsol, çıplak kurtarma (BMR) türünde Yedekleme kullanarak kurtarma gibi belirli senaryolar için gereklidir. Ancak, kasadan VM geri yüklemesi BMR 'nin tam yerini alır.
**Özel ağ yapılandırmalarına sahip VM 'Leri geri yükleme** | Özel ağ yapılandırmalarında, birden çok NIC veya birden çok ayrılmış IP adresi kullanarak iç veya dış yük dengelemesi kullanan VM 'Ler bulunur. [Diski geri yükle seçeneğini](#restore-disks)kullanarak bu VM 'leri geri yükleyin. Bu seçenek, VHD 'lerin bir kopyasını belirtilen depolama hesabına yapar ve ardından, yapılandırmanıza uygun olarak bir [iç](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) veya [dış](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) yük DENGELEYICI, [birden çok NIC](../virtual-machines/windows/multiple-nics.md)veya [bırden çok ayrılmış IP adresi](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)olan bir VM oluşturabilirsiniz.
**NIC/alt ağ üzerinde ağ güvenlik grubu (NSG)** | Azure VM yedeklemesi, NSG bilgilerinin VNet, alt ağ ve NIC düzeyinde yedeklenmesini ve geri yüklenmesini destekler.
**Bölge sabitlenmiş VM 'Ler** | Bir bölgeye sabitlenmiş bir Azure VM 'yi (Azure Backup ile) yedeklebiliyorsanız, onu sabitlendiği bölgeye geri yükleyebilirsiniz. [Daha fazla bilgi edinin](../availability-zones/az-overview.md)
**Herhangi bir kullanılabilirlik kümesinde VM 'yi geri yükleme** | Bir VM 'yi portaldan geri yüklerken, kullanılabilirlik kümesi seçme seçeneği yoktur. Geri yüklenen bir VM 'nin kullanılabilirlik kümesi yok. Diski geri yükle seçeneğini kullanırsanız, belirtilen şablonu veya PowerShell 'i kullanarak diskten bir VM oluşturduğunuzda [bir kullanılabilirlik kümesi belirtebilirsiniz](../virtual-machines/windows/tutorial-availability-sets.md) .
**SQL VM 'Leri gibi özel VM 'Leri geri yükleme** | Azure VM yedeklemesi 'ni kullanarak bir SQL VM 'yi yedeklemekten sonra VM 'yi geri yükle seçeneğini veya diskleri geri yükledikten sonra bir VM oluşturursanız, yeni oluşturulan VM 'nin [burada](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%2cbash)bahsedilen SQL sağlayıcısına kayıtlı olması gerekir. Bu işlem, geri yüklenen VM 'yi bir SQL VM 'ye dönüştürür.

### <a name="restore-domain-controller-vms"></a>Etki alanı denetleyicisi VM 'lerini geri yükleme

**Senaryo** | **Rehber**
--- | ---
**Tek bir etki alanında tek bir etki alanı denetleyicisi sanal makinesini geri yükleme** | VM 'yi diğer VM 'ler gibi geri yükleyin. Şunlara dikkat edin:<br/><br/> Azure VM, Active Directory perspektifinden diğer tüm VM 'ler gibidir.<br/><br/> Dizin Hizmetleri geri yükleme modu (DSRM) da kullanılabilir, bu nedenle tüm Active Directory kurtarma senaryoları geçerlidir. Sanallaştırılmış etki alanı denetleyicileri için yedekleme ve geri yükleme konuları hakkında [daha fazla bilgi edinin](#post-restore-steps) .
**Tek bir etki alanında birden çok etki alanı denetleyicisi VM 'yi geri yükleme** | Ağ üzerinden aynı etki alanındaki diğer etki alanı denetleyicilerine ulaşılırsa, etki alanı denetleyicisi herhangi bir VM gibi geri yüklenebilir. Etki alanındaki son kalan etki alanı denetleyicisi ise veya yalıtılmış bir ağda kurtarma gerçekleştirilirse, bir [orman kurtarması](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)kullanın.
**Birden çok etki alanı yapılandırmasındaki tek bir etki alanı denetleyicisi sanal makinesini geri yükleme** |  [PowerShell kullanarak](backup-azure-vms-automation.md#restore-the-disks) diskleri geri yükleme ve VM oluşturma  
**Birden çok etki alanını tek bir ormanda geri yükleme** | [Orman kurtarması](/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)önerilir.

Daha fazla bilgi için bkz. [Active Directory etki alanı denetleyicilerini yedekleme ve geri yükleme](active-directory-backup-restore.md).

## <a name="track-the-restore-operation"></a>Geri yükleme işlemini izleme

Geri yükleme işlemini tetikledikten sonra, yedekleme hizmeti izleme için bir iş oluşturur. Azure Backup portalda iş hakkında bildirimleri görüntüler. Bunlar görünmüyorsa, **Bildirimler** sembolünü seçin ve sonra geri yükleme işlemi durumunu görmek için **etkinlik günlüğündeki diğer olaylar** ' ı seçin.

![Geri yükleme tetiklendi](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Geri yüklemeyi aşağıdaki gibi izleyin:

1. İşle ilgili işlemleri görüntülemek için bildirimler köprüsünü seçin. Alternatif olarak, kasada **yedekleme işleri**' ni seçin ve ardından ilgili VM 'yi seçin.

    ![Kasadaki VM 'lerin listesi](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

1. Geri yükleme ilerlemesini izlemek için **devam eden** durumunda olan herhangi bir geri yükleme işini seçin. Bu, geri yükleme ilerleme durumu hakkında bilgi görüntüleyen ilerleme çubuğunu görüntüler:

    - **Tahmini geri yükleme süresi**: başlangıçta geri yükleme işlemini tamamlaması için geçen süreyi sağlar. İşlem ilerledikçe, geri yükleme işlemi tamamlandığında geçen süre küçülür ve sıfıra ulaşmaz.
    - **Geri yükleme yüzdesi**. Gerçekleştirilen geri yükleme işleminin yüzdesini gösterir.
    - **Aktarılan bayt sayısı**: yenı bir VM oluşturarak geri yüklüyorsanız, aktarılacak baytların toplam sayısına göre aktarılan baytları gösterir.

## <a name="post-restore-steps"></a>Geri yükleme sonrası adımlar

Bir VM 'yi geri yükledikten sonra dikkat etmeniz gereken birkaç nokta vardır:

- Yedekleme yapılandırması sırasında bulunan Uzantılar yüklü, ancak etkin değil. Bir sorun görürseniz, uzantıları yeniden yükleyin.
- Yedeklenen VM 'nin statik bir IP adresi varsa, geri yüklenen VM 'nin çakışmayı önlemek için dinamik bir IP adresi olur. [Geri yüklenen VM 'ye statik BIR IP adresi ekleyebilirsiniz](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description).
- Geri yüklenen bir VM 'nin kullanılabilirlik kümesi yok. Diski geri yükle seçeneğini kullanırsanız, belirtilen şablonu veya PowerShell 'i kullanarak diskten bir VM oluşturduğunuzda [bir kullanılabilirlik kümesi belirtebilirsiniz](../virtual-machines/windows/tutorial-availability-sets.md) .
- Ubuntu gibi Cloud-init tabanlı bir Linux dağıtımı kullanırsanız, güvenlik nedenleriyle geri yüklemeden sonra parolanın engellenmesi engellenir. [Parolayı sıfırlamak](/troubleshoot/azure/virtual-machines/reset-password)için GERI yüklenen VM 'de VMAccess uzantısını kullanın. Bu dağıtımlardaki SSH anahtarlarının kullanılması önerilir, bu nedenle geri yüklemeden sonra parolayı sıfırlamanız gerekmez.
- VM 'nin etki alanı denetleyicisiyle bozulmuş bir ilişkisi olduğundan, bir VM 'yi geri yükledikten sonra, VM 'yi açmak için aşağıdaki adımları izleyin:
  - İşletim sistemi diskini kurtarılan bir VM 'ye veri diski olarak ekleyin.
  - Bu [bağlantıyı](/troubleshoot/azure/virtual-machines/install-vm-agent-offline)Izleyerek Azure aracısının yanıt VERMEMESI durumunda VM aracısını el ile yükleyebilirsiniz.
  - VM 'ye komut satırı erişimine izin vermek için sanal makinede seri konsol erişimini etkinleştirin

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - VM yeniden oluşturulduğunda, yerel yönetici hesabını ve parolasını sıfırlamak için Azure portal kullanın
  - Etki alanından VM 'yi birleştirmek için Seri konsol Access ve CMD kullanın

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- VM 'ye bir kez ve yeniden başlatıldıktan sonra, yerel yönetici kimlik bilgileri ile sanal makineye başarıyla RDP ve etki alanına başarıyla geri dönüş sağlayabileceksiniz.

## <a name="backing-up-restored-vms"></a>Geri yüklenen VM 'Leri yedekleme

- Bir VM 'yi, ilk Yedeklenen VM ile aynı ada sahip aynı kaynak grubuna geri yüklediyseniz yedekleme, geri yüklemeden sonra VM 'de devam eder.
- VM 'yi farklı bir kaynak grubuna geri yüklediyseniz veya geri yüklenen VM için farklı bir ad belirttiyseniz, geri yüklenen VM için yedeklemeyi ayarlamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Geri yükleme işlemi sırasında sorunlarla karşılaşırsanız, genel sorunları ve hataları [gözden geçirin](backup-azure-vms-troubleshoot.md#restore) .
- VM geri yüklendikten sonra, [sanal makineleri yönetme](backup-azure-manage-vms.md) hakkında bilgi edinin