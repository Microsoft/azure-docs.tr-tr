---
title: Azure portal kullanarak VM 'Leri geri yükleme
description: Azure portal kullanarak bir Azure sanal makinesini bir kurtarma noktasından geri yükleme
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 8b84c7f89c88b54e90a8e1af1236bf205e12cfe6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173466"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure portal Azure VM verilerini geri yükleme

Bu makalede, Azure VM verilerinin [Azure Backup](backup-overview.md) kurtarma hizmetleri kasalarında depolanan kurtarma noktalarından nasıl geri yükleneceği açıklanmaktadır.

## <a name="restore-options"></a>Geri yükleme seçenekleri

Azure Backup bir VM 'yi geri yüklemek için çeşitli yollar sağlar.

**Geri yükleme seçeneği** | **Ayrıntılar**
--- | ---
**Yeni VM oluşturma** | Hızlı bir şekilde bir geri yükleme noktasından bir temel VM oluşturup çalışır.<br/><br/> VM için bir ad belirtebilir, yerleştirileceği kaynak grubunu ve sanal ağı (VNet) seçin ve geri yüklenen VM için bir depolama hesabı belirtin. Yeni VM 'nin, kaynak VM ile aynı bölgede oluşturulması gerekir.
**Diski geri yükle** | Daha sonra yeni bir VM oluşturmak için kullanılabilecek bir VM diskini geri yükler.<br/><br/> Azure Backup, bir VM 'yi özelleştirmenize ve oluşturmanıza yardımcı olacak bir şablon sağlar. <br/><br> Geri yükleme işi, özel VM ayarlarını belirtmek ve bir VM oluşturmak için indirebileceğiniz ve kullanabileceğiniz bir şablon oluşturur.<br/><br/> Diskler belirttiğiniz kaynak grubuna kopyalanır.<br/><br/> Alternatif olarak, diski mevcut bir sanal makineye iliştirebilir veya PowerShell kullanarak yeni bir VM oluşturabilirsiniz.<br/><br/> Bu seçenek, VM 'yi özelleştirmek, yedekleme sırasında orada olmayan yapılandırma ayarlarını eklemek veya şablon ya da PowerShell kullanılarak yapılandırılması gereken ayarları eklemek istiyorsanız faydalıdır.
**Mevcut olanı Değiştir** | Bir diski geri yükleyebilir ve mevcut VM 'deki bir diski değiştirmek için kullanabilirsiniz.<br/><br/> Geçerli VM 'nin mevcut olması gerekir. Silinirse, bu seçenek kullanılamaz.<br/><br/> Azure Backup, diski değiştirmeden önce var olan sanal makinenin anlık görüntüsünü alır ve bunu belirttiğiniz hazırlama konumunda depolar. VM 'ye bağlı mevcut diskler, seçilen geri yükleme noktasıyla değiştirilmiştir.<br/><br/> Anlık görüntü kasaya kopyalanır ve bekletme ilkesine göre saklanır. <br/><br/> Mevcut olanı Değiştir, şifrelenmemiş yönetilen VM 'Ler için desteklenir. Yönetilmeyen diskler, [Genelleştirilmiş VM 'ler](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)veya [özel görüntüler kullanılarak oluşturulan](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/)VM 'ler için desteklenmez.<br/><br/> Geri yükleme noktası geçerli VM 'den daha fazla veya daha az disk içeriyorsa, geri yükleme noktasındaki disk sayısı yalnızca VM yapılandırmasını yansıtacaktır.<br/><br/>

> [!NOTE]
> Ayrıca, bir Azure VM 'de belirli dosya ve klasörleri kurtarabilirsiniz. [Daha fazla bilgi edinin](backup-azure-restore-files-from-vm.md).
>
> Azure VM 'Ler için [en son Azure Backup sürümünü](backup-instant-restore-capability.md) (anında geri yükleme olarak bilinir) çalıştırıyorsanız, anlık görüntüler yedi güne kadar tutulur ve yedekleme verileri kasaya gönderilmeden önce bir VM 'yi anlık görüntülerden geri yükleyebilirsiniz. Bir VM 'yi son yedi gündeki bir yedekten geri yüklemek istiyorsanız, kasadan değil, anlık görüntüden geri yükleme daha hızlıdır.

## <a name="storage-accounts"></a>Depolama hesapları

Depolama hesaplarıyla ilgili bazı ayrıntılar:

- **VM oluşturma**: yenı bir VM OLUŞTURDUĞUNUZDA, VM belirttiğiniz depolama hesabına yerleştirilir.
- **Diski geri yükle**: bir diski geri yüklerken disk, belirttiğiniz depolama hesabına kopyalanır. Geri yükleme işi, karşıdan yükleyebileceğiniz ve özel VM ayarlarını belirtmek için kullanabileceğiniz bir şablon oluşturur. Bu şablon, belirtilen depolama hesabına yerleştirilir.
- **Diski Değiştir**: mevcut bir sanal makinede bulunan bir diski değiştirirken Azure Backup diski değiştirmeden önce var olan sanal makinenin anlık görüntüsünü alır. Anlık görüntü, belirttiğiniz hazırlama konumunda (depolama hesabı) depolanır. Bu depolama hesabı, geri yükleme işlemi sırasında anlık görüntüyü geçici olarak depolamak için kullanılır ve bunu yapmak için, daha sonra kolayca kaldırılabileceği yeni bir hesap oluşturmanız önerilir.
- **Depolama hesabı konumu**: depolama hesabı kasayla aynı bölgede olmalıdır. Yalnızca bu hesaplar görüntülenir. Konumda depolama hesabı yoksa, bir tane oluşturmanız gerekir.
- **Depolama türü**: BLOB depolama desteklenmiyor.
- **Depolama artıklığı**: bölge yedekli depolama (ZRS) desteklenmez. Hesap için çoğaltma ve artıklık bilgileri, hesap adından sonra parantez içinde gösterilir.
- **Premium Depolama**:
  - Premium olmayan VM 'Leri geri yüklerken, Premium Depolama hesapları desteklenmez.
  - Yönetilen VM 'Leri geri yüklerken, ağ kurallarıyla yapılandırılmış Premium Depolama hesapları desteklenmez.

## <a name="before-you-start"></a>Başlamadan önce

Bir VM 'yi geri yüklemek için (yeni bir VM oluşturun), VM 'Yi geri yükleme işlemi için doğru rol tabanlı erişim denetimi (RBAC) [izinlerine](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) sahip olduğunuzdan emin olun.

İzinleriniz yoksa [bir diski geri yükleyebilir](#restore-disks)ve ardından disk geri yüklendikten sonra, yenı bir VM oluşturmak için geri yükleme işleminin bir parçası olarak oluşturulan [şablonu kullanabilirsiniz](#use-templates-to-customize-a-restored-vm) .

## <a name="select-a-restore-point"></a>Geri yükleme noktası seçin

1. Geri yüklemek istediğiniz VM ile ilişkili kasada **Azure sanal makine** > **yedekleme öğeleri** ' ne tıklayın.
2. Bir VM 'ye tıklayın. Varsayılan olarak, VM panosunda son 30 günün kurtarma noktaları görüntülenir. 30 günden daha eski kurtarma noktalarını görüntüleyebilir veya tarihlere, zaman aralıklarına ve farklı anlık görüntü tutarlılığı türlerine göre kurtarma noktalarını bulmak için filtre uygulayabilirsiniz.
3. VM 'yi geri yüklemek için **VM 'Yi geri yükle**'ye tıklayın.

    ![Geri yükleme noktası](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Kurtarma için kullanılacak bir geri yükleme noktası seçin.

## <a name="choose-a-vm-restore-configuration"></a>VM geri yükleme yapılandırması seçin

1. **Yapılandırma geri yükleme**' de bir geri yükleme seçeneği belirleyin:
    - **Yeni oluştur**: yenı bir VM oluşturmak istiyorsanız bu seçeneği kullanın. Basit ayarlarla bir VM oluşturabilir veya bir diski geri yükleyebilir ve özelleştirilmiş bir VM oluşturabilirsiniz.
    - **Varolanı değiştir**: mevcut bir sanal makinede bulunan diskleri değiştirmek istiyorsanız bu seçeneği kullanın.

        ![Yapılandırma geri yükleme Sihirbazı](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Seçtiğiniz geri yükleme seçeneğiniz için ayarları belirtin.

## <a name="create-a-vm"></a>VM oluşturma

[Geri yükleme seçeneklerinden](#restore-options)biri olarak, bir geri yükleme noktasından temel ayarlarla hızlı bir şekilde sanal makine oluşturabilirsiniz.

1. **Yapılandırmayı geri yükle** > **Yeni** > **geri yükleme türü**oluştur ' da, **sanal makine oluştur**' u seçin.
2. **Sanal makine adı**' nda, abonelikte mevcut olmayan bir VM belirtin.
3. **Kaynak grubu**' nda, yeni VM için var olan bir kaynak grubunu seçin veya genel olarak benzersiz bir adla yeni bir tane oluşturun. Zaten var olan bir ad atarsanız Azure, gruba VM ile aynı adı atar.
4. Sanal ağ ' da, VM 'nin yerleştirileceği **sanal ağı**seçin. Abonelikle ilişkili tüm VNET 'ler görüntülenir. Alt ağı seçin. İlk alt ağ varsayılan olarak seçilidir.
5. **Depolama konumu**' nda, VM için depolama hesabını belirtin. [Daha fazla bilgi edinin](#storage-accounts).

    ![Yapılandırma geri yükleme Sihirbazı](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. **Yapılandırma geri yükleme**' de **Tamam**' ı seçin. Geri **Yükle**' ye **tıklayarak geri yükleme işlemini** tetikleyin.

## <a name="restore-disks"></a>Diskleri geri yükleme

[Geri yükleme seçeneklerinden](#restore-options)biri olarak, bir geri yükleme noktasından bir disk oluşturabilirsiniz. Ardından, aşağıdakilerden birini yapabilirsiniz:

- Ayarları özelleştirmek için geri yükleme işlemi sırasında oluşturulan şablonu kullanın ve VM dağıtımını tetikleyin. Varsayılan şablon ayarlarını düzenlersiniz ve VM dağıtımı için şablonu gönderebilirsiniz.
- [Geri yüklenen diskleri](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) var olan bir sanal makineye ekleyin.
- PowerShell kullanarak geri yüklenen disklerden [Yeni BIR VM oluşturun](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) .

1. **Yapılandırmayı geri yükle** > **Yeni** > **geri yükleme türü**Oluştur bölümünde **diskleri geri yükle**' yi seçin.
2. **Kaynak grubu**' nda, geri yüklenen diskler için var olan bir kaynak grubunu seçin veya genel olarak benzersiz bir adla yeni bir tane oluşturun.
3. **Depolama hesabı**' nda, VHD 'lerin kopyalanacağı hesabı belirtin. [Daha fazla bilgi edinin](#storage-accounts).

    ![Kurtarma yapılandırması tamamlandı](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. **Yapılandırma geri yükleme**' de **Tamam**' ı seçin. Geri **Yükle**' ye **tıklayarak geri yükleme işlemini** tetikleyin.

Sanal makineniz yönetilen diskler kullandığında ve **sanal makine oluştur** seçeneğini belirlediğinizde, Azure Backup belirtilen depolama hesabını kullanmaz. **Diskleri geri yükleme** ve **anında geri yükleme**durumunda depolama hesabı yalnızca şablonu depolamak için kullanılır. Yönetilen diskler, belirtilen kaynak grubunda oluşturulur.
Sanal makineniz yönetilmeyen diskler kullandığında, depolama hesabına Bloblar olarak geri yüklenir.

### <a name="use-templates-to-customize-a-restored-vm"></a>Geri yüklenen bir VM 'yi özelleştirmek için şablonları kullanma

Disk geri yüklendikten sonra, özelleştirmek ve yeni bir VM oluşturmak için geri yükleme işleminin bir parçası olarak oluşturulan şablonu kullanın:

1. İlgili iş için **geri yükleme Işi ayrıntıları** ' nı açın.

2. **Iş geri yükleme ayrıntıları**' nda, şablon dağıtımını başlatmak Için **şablonu dağıt** ' ı seçin.

    ![Geri yükleme işi detaya gitme](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Şablonda belirtilen VM ayarını özelleştirmek için **Şablonu Düzenle**' ye tıklayın. Daha fazla özelleştirme eklemek istiyorsanız, **parametreleri Düzenle**' ye tıklayın.
    - Özel bir şablondan kaynak dağıtma hakkında [daha fazla bilgi edinin](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) .
    - Yazma şablonları hakkında [daha fazla bilgi edinin](../azure-resource-manager/resource-group-authoring-templates.md) .

   ![Şablon dağıtımı yükle](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. VM için özel değerleri girin, **hüküm ve koşulları** kabul edin ve **satın al**' a tıklayın.

   ![Şablon dağıtımını gönder](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Mevcut diskleri Değiştir

[Geri yükleme seçeneklerinden](#restore-options)biri olarak, var olan bir VM diskini seçili geri yükleme noktasıyla değiştirebilirsiniz. Tüm geri yükleme seçeneklerini [gözden geçirin](#restore-options) .

1. **Yapılandırma geri yükleme**' de **Varolanı değiştir**' e tıklayın.
2. **Geri yükleme türü**' nde **disk/s 'yi Değiştir**' i seçin. Bu, var olan VM disklerini değiştirmek için kullanılacak geri yükleme noktasıdır.
3. **Hazırlama konumu**bölümünde, geri yükleme işlemi sırasında geçerli yönetilen disklerin anlık görüntülerinin kaydedileceği yeri belirtin. [Daha fazla bilgi edinin](#storage-accounts).

   ![Yapılandırma geri yükleme Sihirbazı var olanı Değiştir](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="restore-vms-with-special-configurations"></a>Özel yapılandırmalara sahip VM 'Leri geri yükleme

VM 'Leri geri yüklemeniz gerekebilecek bazı yaygın senaryolar vardır.

**Senaryo** | **Rehber**
--- | ---
**Karma kullanım teklifi kullanarak VM 'Leri geri yükleme** | Bir Windows VM [karma kullanım avantajı (hub) lisanslama](../virtual-machines/windows/hybrid-use-benefit-licensing.md)kullanıyorsa, diskleri geri yükleyin ve belirtilen şablonu ( **Lisans türü** **Windows_Server**olarak ayarlanmış olan) veya PowerShell 'i kullanarak yeni bir VM oluşturun.  Bu ayar VM oluşturulduktan sonra da uygulanabilir.
**Azure veri merkezi olağanüstü durum sırasında VM 'Leri geri yükleme** | Kasa GRS kullanıyorsa ve VM için birincil veri merkezi kapalıysa, Azure Backup yedeklenen VM 'Leri eşleştirilmiş veri merkezine geri yüklemeyi destekler. Eşleştirilmiş veri merkezinde bir depolama hesabı seçin ve normal olarak geri yükleyin. Azure Backup, geri yüklenen VM 'yi oluşturmak için eşleştirilmiş bölgedeki işlem hizmetini kullanır. Veri merkezi dayanıklılığı hakkında [daha fazla bilgi edinin](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) .
**Tek etki alanındaki tek etki alanı denetleyicisi sanal makinesini geri yükleme** | VM 'yi diğer VM 'ler gibi geri yükleyin. Aşağıdakilere dikkat edin:<br/><br/> Azure VM, Active Directory perspektifinden diğer tüm VM 'ler gibidir.<br/><br/> Dizin Hizmetleri geri yükleme modu (DSRM) da kullanılabilir, bu nedenle tüm Active Directory kurtarma senaryoları geçerlidir. Sanallaştırılmış etki alanı denetleyicileri için yedekleme ve geri yükleme konuları hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) .
**Tek etki alanında birden çok etki alanı denetleyicisi VM 'yi geri yükleme** | Ağ üzerinden aynı etki alanındaki diğer etki alanı denetleyicilerine ulaşılırsa, etki alanı denetleyicisi herhangi bir VM gibi geri yüklenebilir. Etki alanındaki son kalan etki alanı denetleyicisi ise veya yalıtılmış bir ağda kurtarma gerçekleştirilirse, bir [orman kurtarması](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)kullanın.
**Birden çok etki alanını tek bir ormanda geri yükleme** | [Orman kurtarması](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)önerilir.
**Tam geri yükleme** | Azure VM 'Leri ve şirket içi hiper yöneticilerde yer alan başlıca fark, Azure 'da kullanılabilir VM konsolunun olmaması olabilir. Bir konsol, çıplak kurtarma (BMR) türünde Yedekleme kullanarak kurtarma gibi belirli senaryolar için gereklidir. Ancak, kasadan VM geri yüklemesi BMR 'nin tam yerini alır.
**Özel ağ yapılandırmalarına sahip VM 'Leri geri yükleme** | Özel ağ yapılandırmalarında, birden çok NIC veya birden çok ayrılmış IP adresi kullanarak iç veya dış yük dengelemesi kullanan VM 'Ler bulunur. [Diski geri yükle seçeneğini](#restore-disks)kullanarak bu VM 'leri geri yükleyin. Bu seçenek, VHD 'lerin bir kopyasını belirtilen depolama hesabına yapar ve ardından, yapılandırmanıza uygun olarak bir [iç](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) veya [dış](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) yük DENGELEYICI, [birden çok NIC](../virtual-machines/windows/multiple-nics.md)veya [bırden çok ayrılmış IP adresi](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)olan bir VM oluşturabilirsiniz.
**NIC/alt ağ üzerinde ağ güvenlik grubu (NSG)** | Azure VM yedeklemesi, NSG bilgilerinin VNet, alt ağ ve NIC düzeyinde yedeklenmesini ve geri yüklenmesini destekler.
**Bölge sabitlenmiş VM 'Ler** | Azure Backup, bölge sabitlenmiş sanal makinelerin yedeklenmesini ve geri yüklenmesini destekler. [Daha fazla bilgi](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Geri yükleme işlemini izleme

Geri yükleme işlemini tetikledikten sonra, yedekleme hizmeti izleme için bir iş oluşturur. Azure Backup portalda iş hakkında bildirimleri görüntüler. Bunlar görünmüyorsa, **Bildirimler** sembolünü seçin ve sonra geri yükleme işlemi durumunu görmek Için **tüm işleri görüntüle** ' yi seçin.

![Geri yükleme tetiklendi](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Geri yüklemeyi aşağıdaki gibi izleyin:

1. İşle ilgili işlemleri görüntülemek için bildirimler köprüye tıklayın. Alternatif olarak, kasadaki **yedekleme işleri**' ne ve ardından ilgili VM 'ye tıklayın.

    ![Kasadaki VM 'lerin listesi](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Geri yükleme ilerlemesini izlemek için **devam eden**durumunda olan herhangi bir geri yükleme işine tıklayın. Bu, geri yükleme ilerleme durumu hakkında bilgi görüntüleyen ilerleme çubuğunu görüntüler:

    - **Tahmini geri yükleme süresi**: başlangıçta geri yükleme işlemini tamamlaması için geçen süreyi sağlar. İşlem ilerledikçe, geri yükleme işlemi tamamlandığında geçen süre küçülür ve sıfıra ulaşmaz.
    - **Geri yükleme yüzdesi**. Gerçekleştirilen geri yükleme işleminin yüzdesini gösterir.
    - **Aktarılan bayt sayısı**: yenı bir VM oluşturarak geri yüklüyorsanız, aktarılacak baytların toplam sayısına göre aktarılan baytları gösterir.

## <a name="post-restore-steps"></a>Geri yükleme sonrası adımlar

Bir VM 'yi geri yükledikten sonra dikkat etmeniz gereken birkaç nokta vardır:

- Yedekleme yapılandırması sırasında bulunan Uzantılar yüklü, ancak etkin değil. Bir sorun görürseniz, uzantıları yeniden yükleyin.
- Yedeklenen VM 'nin statik bir IP adresi varsa, geri yüklenen VM 'nin çakışmayı önlemek için dinamik bir IP adresi olur. [Geri yüklenen VM 'ye statik BIR IP adresi ekleyebilirsiniz](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Geri yüklenen bir VM 'nin kullanılabilirlik kümesi yok. Diski geri yükle seçeneğini kullanırsanız, belirtilen şablonu veya PowerShell 'i kullanarak diskten bir VM oluşturduğunuzda [bir kullanılabilirlik kümesi belirtebilirsiniz](../virtual-machines/windows/tutorial-availability-sets.md) .
- Ubuntu gibi Cloud-init tabanlı bir Linux dağıtımı kullanırsanız, güvenlik nedenleriyle geri yüklemeden sonra parolanın engellenmesi engellenir. [Parolayı sıfırlamak](../virtual-machines/linux/reset-password.md)için GERI yüklenen VM 'de VMAccess uzantısını kullanın. Bu dağıtımlardaki SSH anahtarlarının kullanılması önerilir, bu nedenle geri yüklemeden sonra parolayı sıfırlamanız gerekmez.
- VM 'nin etki alanı denetleyicisiyle bağlantısı kopmuş olduğundan sanal makineye geri yüklendikten sonra sanal makineye erişemiyorsanız, VM 'yi getirmek için aşağıdaki adımları izleyin:
  - İşletim sistemi diskini kurtarılan bir VM 'ye veri diski olarak ekleyin.
  - Bu [bağlantıyı](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)Izleyerek Azure aracısının yanıt VERMEMESI durumunda VM aracısını el ile yükleyebilirsiniz.
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

- VM 'ye bir kez ve yeniden başlatıldıktan sonra, yerel yönetici kimlik bilgileri ile VM 'ye başarıyla RDP ve etki alanına başarıyla geri dönüş sağlayacaksınız.

## <a name="backing-up-restored-vms"></a>Geri yüklenen VM 'Leri yedekleme

- Bir VM 'yi, ilk Yedeklenen VM ile aynı ada sahip aynı kaynak grubuna geri yüklediyseniz yedekleme, geri yüklemeden sonra VM 'de devam eder.
- VM 'yi farklı bir kaynak grubuna geri yüklediyseniz veya geri yüklenen VM için farklı bir ad belirttiyseniz, geri yüklenen VM için yedeklemeyi ayarlamanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Geri yükleme işlemi sırasında sorunlarla karşılaşırsanız, genel sorunları ve hataları [gözden geçirin](backup-azure-vms-troubleshoot.md#restore) .
- VM geri yüklendikten sonra, [sanal makineleri yönetme](backup-azure-manage-vms.md) hakkında bilgi edinin
