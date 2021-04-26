---
title: İş yüklerini yedeklemek için Azure Backup Sunucusu kullanma
description: Bu makalede, Microsoft Azure Backup sunucusu (MABS) kullanarak iş yüklerini korumak ve yedeklemek için ortamınızı nasıl hazırlayacağınızı öğrenin.
ms.topic: conceptual
ms.date: 04/14/2021
ms.openlocfilehash: 144a5e26f5ad10d120a49f6a0385c3a310448dbc
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713687"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Azure Backup Sunucusu yükleyip yükseltin

> [!div class="op_single_selector"]
>
> * [Azure Backup Sunucusu](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Uygulama hedefi: MABS v3. (MABS v2 artık desteklenmiyor. MABS v3 'den önceki bir sürümü kullanıyorsanız, lütfen en son sürüme yükseltin.)

Bu makalede, Microsoft Azure Backup sunucusu (MABS) kullanarak iş yüklerini yedeklemek için ortamınızın nasıl hazırlanacağı açıklanmaktadır. Azure Backup Sunucusu ile, Hyper-V VM 'Leri, Microsoft SQL Server, SharePoint Server, Microsoft Exchange ve Windows istemcileri gibi uygulama iş yüklerini tek bir konsoldan koruyabilirsiniz.

> [!NOTE]
> Azure Backup Sunucusu, artık VMware VM 'lerini koruyabilir ve gelişmiş güvenlik özellikleri sağlar. Ürünü aşağıdaki bölümlerde ve en son Azure Backup aracısında açıklandığı gibi yükler. Azure Backup Sunucusu ile VMware sunucularını yedekleme hakkında daha fazla bilgi edinmek için, [Azure Backup sunucusu kullanarak bir VMware sunucusunu yedeklemek için](backup-azure-backup-server-vmware.md)makalesine bakın. Güvenlik özellikleri hakkında bilgi edinmek için [Azure Backup güvenlik özellikleri belgelerine](backup-azure-security-feature.md)bakın.
>
>

Azure VM 'de dağıtılan MABS, Azure 'daki VM 'Leri yedekleyebilir, ancak yedekleme işlemini etkinleştirmek için aynı etki alanında olmaları gerekir. Azure VM 'yi geri yükleme işlemi, şirket içi VM 'Leri yedeklemeye benzer kalır, ancak Azure 'da MABS dağıtımı bazı sınırlamalara sahiptir. Sınırlamalar hakkında daha fazla bilgi için bkz. [Azure sanal makinesi olarak DPM](/system-center/dpm/install-dpm#setup-prerequisites).

> [!NOTE]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../azure-resource-manager/management/deployment-models.md). Bu makalede, Kaynak Yöneticisi modeli kullanılarak dağıtılan VM 'Leri geri yüklemeyle ilgili bilgi ve yordamlar sağlanmaktadır.
>
>

Azure Backup Sunucusu, Data Protection Manager (DPM) iş yükü yedekleme işlevselliğinin çoğunu devralır. Bu makale, bazı paylaşılan işlevleri açıklamak için DPM belgelerine bağlantı sağlar. Azure Backup Sunucusu DPM ile aynı işlevlerin çoğunu paylaşıyor, Azure Backup Sunucusu banda yedeklememez veya System Center ile tümleşmez.

## <a name="choose-an-installation-platform"></a>Yükleme platformu seçin

Azure Backup Sunucusu çalışmaya ve çalıştırmaya yönelik ilk adım, bir Windows Server ayarlanalmaktır. Sunucunuz Azure 'da veya şirket içinde olabilir.

* Şirket içi iş yüklerini korumak için, MABS sunucusu şirket içinde yer almalıdır ve bir etki alanına bağlanmalıdır.
* Azure VM 'lerde çalışan iş yüklerini korumak için, MABS sunucusunun Azure 'da bulunması ve bir Azure VM olarak çalıştırılması ve bir etki alanına bağlanması gerekir.

### <a name="using-a-server-in-azure"></a>Azure 'da sunucu kullanma

Azure Backup Sunucusu çalıştırmak için bir sunucu seçerken, Windows Server 2016 Datacenter veya Windows Server 2019 Datacenter 'un Galeri görüntüsüyle başlamanız önerilir. [Ilk Windows sanal makinenizi Azure Portal oluşturun](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json), daha önce Azure 'u hiç kullanmamış olsanız bile, Azure 'da önerilen sanal makineyle çalışmaya başlama hakkında bir öğretici sağlar. Sunucu sanal makinesi (VM) için önerilen minimum gereksinimler: dört çekirdek ve 8 GB RAM ile Standard_A4_v2 olmalıdır.

Azure Backup Sunucusu olan iş yüklerini koruma çok sayıda nusmaya sahiptir. [MABS için koruma matrisi](./backup-mabs-protection-matrix.md) bu nusların açıklanmasına yardımcı olur. Makineyi dağıttıktan önce, bu makaleyi tamamen okuyun.

### <a name="using-an-on-premises-server"></a>Şirket içi sunucu kullanma

Temel sunucuyu Azure 'da çalıştırmak istemiyorsanız, sunucuyu bir Hyper-V VM 'sinde, VMware VM 'de veya fiziksel bir konakta çalıştırabilirsiniz. Sunucu donanımı için önerilen minimum gereksinimler iki çekirdek ve 8 GB RAM 'dir. Desteklenen işletim sistemleri aşağıdaki tabloda listelenmiştir:

| Operating System | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2016 ve en son SPs |64 bit |Standard, Datacenter, Essentials  |

Windows Server yinelenenleri kaldırma 'yı kullanarak DPM depolama alanını yineleyebilirsiniz. Hyper-V VM 'lerinde dağıtıldığında [DPM ve yinelenenleri kaldırma](/system-center/dpm/deduplicate-dpm-storage) özelliğinin birlikte nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!NOTE]
> Azure Backup Sunucusu, özel, tek amaçlı bir sunucuda çalışmak üzere tasarlanmıştır. Üzerinde Azure Backup Sunucusu yükleyemezsiniz:
>
> * Etki alanı denetleyicisi olarak çalıştırılan bir bilgisayar
> * Uygulama Sunucusu rolünün yüklü olduğu bir bilgisayar
> * System Center Operations Manager yönetim sunucusu olan bir bilgisayar
> * Exchange Server’ın çalıştırıldığı bir bilgisayar
> * Bir kümenin düğümü olan bir bilgisayar
>
> Azure Backup Sunucusu yüklemek Windows Server Core veya Microsoft Hyper-V Server 'da desteklenmez.

Azure Backup Sunucusu her zaman bir etki alanına ekleyin. Mevcut bir Azure Backup Sunucusu makinenin dağıtımdan sonra yeni bir etki alanına taşınması *desteklenmez*.

Yedekleme verilerini Azure 'a göndermenizi veya yerel olarak saklamayı, Azure Backup Sunucusu bir kurtarma hizmetleri kasasıyla kaydolmasının gerekip gerekmediğini belirtir.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Depolama çoğaltmasını ayarla

Depolama çoğaltma seçeneği, coğrafi olarak yedekli depolama ve yerel olarak yedekli depolama arasında seçim yapmanıza olanak sağlar. Varsayılan olarak, kurtarma hizmetleri kasaları coğrafi olarak yedekli depolama kullanır. Bu kasa birincil kasanız, depolama seçeneğini coğrafi olarak yedekli depolama olarak ayarlayın. Daha düşük dayanıklılık düzeyinde olan daha uygun maliyetli bir seçenek istiyorsanız yerel olarak yedekli depolamayı seçin. [Azure Storage Çoğaltmaya genel bakış](../storage/common/storage-redundancy.md)bölümünde [coğrafi olarak yedekli](../storage/common/storage-redundancy.md#geo-redundant-storage), [yerel olarak yedekli](../storage/common/storage-redundancy.md#locally-redundant-storage)ve bölgesel olarak [yedekli depolama seçenekleri](../storage/common/storage-redundancy.md#zone-redundant-storage) hakkında daha fazla bilgi edinin.

Depolama çoğaltma ayarını düzenlemek için:

1. **Kurtarma Hizmetleri kasaları** bölmesinden yeni kasayı seçin. **Ayarlar** bölümünde **Özellikler**' i seçin.
2. **Özellikler**' de, **yedekleme yapılandırması** altında **Güncelleştir**' i seçin.

3. Depolama çoğaltma türünü seçin ve **Kaydet**' i seçin.

     ![Yeni kasa için depolama yapılandırması ayarlama](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Yazılım paketi

### <a name="downloading-the-software-package"></a>Yazılım paketi indiriliyor

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Zaten açık bir kurtarma hizmetleri Kasası varsa adım 3 ' e geçin. Kurtarma Hizmetleri Kasası açık değilse, ancak Azure portal, ana menüde, **Araştır**' ı seçin.

   * Kaynak listesinde **Kurtarma Hizmetleri** yazın.
   * Yazmaya başladığınızda liste, girdinize göre filtrelenir. **Kurtarma Hizmetleri kasalarını** gördüğünüzde, bunu seçin.

     ![Kurtarma Hizmetleri Kasası oluşturma adımı 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Kurtarma Hizmetleri kasalarının listesi görünür.
   * Kurtarma Hizmetleri kasalarının listesinden bir kasa seçin.

     Seçilen kasa panosu açılır.

     ![Kasa panosu](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. **Ayarlar** bölmesi varsayılan olarak açılır. Kapalıysa, ayarlar bölmesini açmak için **Ayarlar** ' ı seçin.

    ![Ayarlar bölmesi](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Başlangıç Sihirbazı 'nı açmak için **Yedekle** ' yi seçin.

    ![Yedekleme başlangıç](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Açılan **yedeklemeye Başlarken** bölmesinde, **yedekleme hedefleri** otomatik olarak seçilir.

    ![Yedekleme-hedefler-varsayılan-açılan](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. **Yedekleme hedefi** bölmesinde, **iş yükünüzün çalıştırıldığı konum** menüsünde **Şirket içi**' ı seçin.

    ![amaç olarak şirket içi ve iş yükleri](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    **Neleri yedeklemek istiyorsunuz?** açılan menüsünde, Azure Backup sunucusu kullanarak korumak istediğiniz iş yüklerini seçin ve ardından **Tamam**' ı seçin.

    **Yedeklemeyi kullanmaya** başlama Sihirbazı, Iş yüklerini Azure 'a yedeklemek Için **altyapıyı hazırla** seçeneğini değiştirir.

   > [!NOTE]
   > Yalnızca dosyaları ve klasörleri yedeklemek istiyorsanız, Azure Backup Aracısı 'nı kullanmanızı ve makalenin kılavuzunu takip etmenizi öneririz. [önce dosya ve klasörleri yedekleme konusuna bakmanız](./backup-windows-with-mars-agent.md)önerilir. Dosya ve klasörlerden daha fazlasını koruyorsanız veya gelecekte koruma ihtiyaçlarını genişletmeyi planlıyorsanız, bu iş yüklerini seçin.
   >
   >

    ![Başlarken Sihirbazı değişikliği](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Açılan **altyapıyı hazırla** bölmesinde, yükleme Azure Backup sunucusu bağlantıları **İndir** ' i seçin ve kasa kimlik bilgilerini indirin. Kurtarma Hizmetleri kasasına Azure Backup Sunucusu kayıt sırasında kasa kimlik bilgilerini kullanırsınız. Bağlantılar sizi yazılım paketinin indirilebileceği Indirme merkezine götürür.

    ![Altyapıyı Azure Backup Sunucusu için hazırla](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Tüm dosyalar ' ı seçin ve **İleri ' yi** seçin. Microsoft Azure Backup indirme sayfasından gelen tüm dosyaları indirin ve tüm dosyaları aynı klasöre yerleştirin.

    ![İndirme Merkezi 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Tüm dosyaların karşıdan yükleme boyutu > 3 GB olduğundan, 10 Mbps bir indirme bağlantısında indirme işleminin tamamlanması 60 dakika sürebilir.

### <a name="extracting-the-software-package"></a>Yazılım paketi ayıklanıyor

Tüm dosyaları indirdikten sonra **MicrosoftAzureBackupInstaller.exe**' yi seçin. Bu işlem, kurulum dosyalarını sizin tarafınızdan belirtilen bir konuma ayıklamak için **Microsoft Azure Backup Kurulum Sihirbazı 'nı** başlatır. Sihirbaza devam edin ve ayıklama işlemini başlatmak için **Ayıkla** düğmesini seçin.

> [!WARNING]
> Kurulum dosyalarını ayıklamak için en az 4 GB boş alan gerekir.
>
>

![Yükleme için dosyaları ayıklama kurulumu](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Ayıklama işlemi tamamlandıktan sonra, Microsoft Azure Backup sunucusunu yüklemeye başlamak ve **son** düğmesini seçmek için, yeniden ayıklanan *setup.exe* başlatmak üzere kutuyu işaretleyin.

### <a name="installing-the-software-package"></a>Yazılım paketini yükleme

1. Kurulum sihirbazını başlatmak için **Microsoft Azure Backup** ' ı seçin.

    ![Microsoft Azure Backup Kurulum Sihirbazı](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Hoş Geldiniz ekranında **İleri** düğmesini seçin. Bu sizi *önkoşul denetimleri* bölümüne götürür. Bu ekranda, Azure Backup Sunucusu için donanım ve yazılım önkoşullarının karşılanıp karşılanmadığını belirlemek için **Denetle** ' yi seçin. Tüm Önkoşullar başarılı bir şekilde karşılanıyorsa, makinenin gereksinimleri karşıladığını belirten bir ileti görürsünüz. **İleri** düğmesini seçin.

    ![Azure Backup Sunucusu-hoş geldiniz ve önkoşul denetimi](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Azure Backup Sunucusu yükleme paketi, gereken uygun SQL Server ikilileriyle birlikte paketlenmiştir. Yeni bir Azure Backup Sunucusu yüklemesi başlatırken, **Bu kurulumla yeni SQL Server örneğini yükleme** seçeneğini belirleyin ve **Çek ve yükleme** düğmesini seçin. Önkoşullar başarıyla yüklendikten sonra **İleri**' yi seçin.

    >[!NOTE]
    >Kendi SQL Server 'nizi kullanmak istiyorsanız desteklenen SQL Server sürümleri SQL Server 2014 SP1 veya üzeri, 2016 ve 2017.  Tüm SQL Server sürümleri Standard veya Enterprise 64-bit olmalıdır.
    >Azure Backup Sunucusu, uzak bir SQL Server örneğiyle çalışmaz. Azure Backup Sunucusu tarafından kullanılan örneğin yerel olması gerekir. MABS için mevcut bir SQL Server kullanıyorsanız, MABS kurulumu yalnızca SQL Server 'ın *adlandırılmış örneklerinin* kullanılmasını destekler.

    ![Azure Backup Sunucusu-SQL denetimi](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Makineyi yeniden başlatma önerisiyle ilgili bir hata oluşursa, bunu yapın ve **yeniden denetle**' yi seçin. Herhangi bir SQL yapılandırma sorunu varsa, SQL 'e göre SQL 'i yeniden yapılandırın ve mevcut SQL örneğini kullanarak MABS 'i yüklemeyi/yükseltmeyi yeniden deneyin.

   **El ile yapılandırma**

   Kendi SQL örneğinizi kullandığınızda ana DB 'ye sysadmin rolüne BUILTIN\Administrators eklediğinizden emin olun.

    **SQL 2017 ile SSRS yapılandırması**

    Kendi SQL 2017 örneğinizi kullandığınızda SSRS 'yi el ile yapılandırmanız gerekir. SSRS yapılandırmasından sonra, SSRS 'nin *ısınbaşlatılmış* özelliğinin *true* olarak ayarlandığından emin olun. Bu true olarak ayarlandığında, MABS SSRS 'nin zaten yapılandırıldığını varsayar ve SSRS yapılandırmasını atlar.

    SSRS yapılandırması için aşağıdaki değerleri kullanın:
    * Hizmet hesabı: ' yerleşik hesabı kullan ' ağ hizmeti olmalıdır
    * Web hizmeti URL 'SI: ' sanal dizin ' ReportServer_ olmalıdır\<SQLInstanceName>
    * Veritabanı: DatabaseName ReportServer $ olmalıdır\<SQLInstanceName>
    * Web portalı URL 'SI: ' sanal dizin ' Reports_ olmalıdır\<SQLInstanceName>

    SSRS yapılandırması hakkında [daha fazla bilgi edinin](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) .

    > [!NOTE]
    > MABS için veritabanı olarak kullanılan SQL Server Lisanslama, [Microsoft çevrimiçi hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products) (OST) tarafından yönetilir. OST 'ye göre, MABS ile paketlenmiş SQL Server yalnızca MABS için veritabanı olarak kullanılabilir.

4. Microsoft Azure Backup Server dosyalarının yüklenmesi için bir konum belirtin ve **İleri ' yi** seçin.

    ![Dosyaların yüklenmesi için konum belirtin](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Karalama konumu Azure 'a yedekleme gereksinimidir. Karalama konumunun, buluta yedeklenmek üzere planlandığı verilerin en az %5 ' i olduğundan emin olun. Disk koruması için, yükleme tamamlandıktan sonra ayrı disklerin yapılandırılması gerekir. Depolama havuzları hakkında daha fazla bilgi için bkz. [veri depolamayı hazırlama](/system-center/dpm/plan-long-and-short-term-data-storage).

    Disk depolaması için kapasite gereksinimleri, birincil olarak korunan verilerin boyutuna, günlük kurtarma noktası boyutuna, beklenen birim veri büyüme hızına ve Bekletme aralığı hedeflerine bağlıdır. Disk depolamasını korunan verilerin iki katı boyutta yapmanızı öneririz. Bu, korunan veri boyutunun %10’u olan günlük kurtarma noktası boyutu ve 10 günlük bekletme aralığı varsayar. Boyut iyi bir şekilde tahmin etmek için [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301)gözden geçirin. 

5. Kısıtlanmış yerel kullanıcı hesapları için güçlü bir parola sağlayın ve **İleri ' yi** seçin.

    ![Güçlü parola sağla](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Güncelleştirmeleri denetlemek için *Microsoft Update* kullanmak isteyip istemediğinizi seçin ve **İleri ' yi** seçin.

   > [!NOTE]
   > Windows ve Microsoft Azure Backup Server gibi diğer ürünler için güvenlik ve önemli güncelleştirmeler sunan Microsoft Update Windows Update yeniden yönlendirmenizi öneririz.
   >
   >

    ![Microsoft Update Opt-In](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. *Ayarların özetini* gözden geçirin ve **yüklemeyi** seçin.

    ![Ayarların Özeti](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Yükleme aşamalarda gerçekleşir. İlk aşamada Microsoft Azure Kurtarma Hizmetleri Aracısı sunucuya yüklenir. Sihirbaz ayrıca Internet bağlantısını denetler. Internet bağlantısı varsa, yüklemeye devam edebilirsiniz. Aksi takdirde, Internet 'e bağlanmak için proxy ayrıntılarını sağlamanız gerekir.

    Sonraki adım Microsoft Azure Kurtarma Hizmetleri aracısını yapılandırmaktır. Yapılandırmanın bir parçası olarak, makineyi kurtarma hizmetleri kasasına kaydetmek için kasa kimlik bilgilerinizi sağlamanız gerekir. Ayrıca, Azure ile şirket içi arasında gönderilen verileri şifrelemek/şifrelerini çözmek için bir parola de sağlarsınız. Otomatik olarak bir parola oluşturabilir veya en düşük 16 karakterlik parolanızı sağlayabilirsiniz. Aracı yapılandırılana kadar sihirbaza devam edin.

    ![Sunucu kaydetme Sihirbazı](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure Backup sunucusunun kaydı başarıyla tamamlandıktan sonra, Genel Kurulum Sihirbazı SQL Server ve Azure Backup Sunucusu bileşenleri için yükleme ve yapılandırmaya geçer. SQL Server bileşeni yüklemesi tamamlandıktan sonra, Azure Backup Sunucusu bileşenleri yüklenir.

    ![Azure Backup Sunucusu Kurulum ilerleme durumu](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Yükleme adımı tamamlandığında, ürünün masaüstü simgeleri de oluşturulur. Ürünü başlatmak için simgeye çift tıklayın.

### <a name="add-backup-storage"></a>Yedekleme depolama alanı ekleme

İlk yedekleme kopyası Azure Backup Sunucusu makineye bağlı depolamada tutulur. Disk ekleme hakkında daha fazla bilgi için bkz. [depolama havuzlarını ve Disk depolamayı yapılandırma](./backup-mabs-add-storage.md).

> [!NOTE]
> Verileri Azure 'a göndermek için planlıyor olsanız bile yedekleme depolama alanı eklemeniz gerekir. Geçerli Azure Backup Sunucusu mimarisinde, yerel depolama ilk (ve zorunlu) yedek kopyayı tutarken, Azure Backup Kasası verilerin *ikinci* kopyasını tutar.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Data Protection Manager koruma aracısını yükleyip güncelleştirme

MABS, System Center Data Protection Manager koruma Aracısı 'nı kullanır. Koruma aracısını koruma sunucularınıza yüklemek için aşağıdaki [adımları uygulayın](/system-center/dpm/deploy-dpm-protection-agent) .

Aşağıdaki bölümlerde, istemci bilgisayarlar için koruma aracılarının nasıl güncelleştirilmesi anlatılmaktadır.

1. Yedekleme sunucusu yönetici konsolu, **Yönetim**  >  **aracıları**' nı seçin.

2. Görüntüleme bölmesinde, koruma aracısını güncelleştirmek istediğiniz istemci bilgisayarları seçin.

   > [!NOTE]
   > **Aracı Updates** sütunu, korunan her bilgisayar için bir koruma Aracısı güncelleştirmesi kullanılabileceğini gösterir. **Eylemler** bölmesinde, **güncelleştirme** eylemi yalnızca korumalı bir bilgisayar seçildiğinde ve güncelleştirmeler kullanılabilir olduğunda kullanılabilir.
   >
   >

3. Seçili bilgisayarlara güncelleştirilmiş koruma aracıları yüklemek için, **Eylemler** bölmesinde **Güncelleştir**' i seçin.

4. Ağa bağlı olmayan bir istemci bilgisayar için, bilgisayar ağa bağlanana kadar **Aracı durumu** sütununda **bekleyen güncelleştirme** durumu gösterilmektedir.

   Bir istemci bilgisayar ağa bağlandıktan sonra, istemci bilgisayar için **Aracı Updates** sütununda **güncelleştirme** durumu gösterilir.

## <a name="move-mabs-to-a-new-server"></a>MABS 'leri yeni bir sunucuya taşıma

Depolama alanını korurken MABS 'i yeni bir sunucuya taşımanız gerekiyorsa buradaki adımlar aşağıda verilmiştir. Bu, yalnızca tüm veriler Modern Yedekleme Alanı durumunda yapılabilir.

  > [!IMPORTANT]
  >
  > * Yeni sunucu adı, özgün Azure Backup Sunucusu örneğiyle aynı ada sahip olmalıdır. Kurtarma noktalarını sürdürmek için önceki depolama havuzunu ve MABS veritabanını (DPMDB) kullanmak istiyorsanız yeni Azure Backup Sunucusu örneğinin adını değiştiremezsiniz.
  > * MABS veritabanının (DPMDB) yedeğine sahip olmanız gerekir. Veritabanını geri yüklemek için bu gereklidir.

1. Görüntüleme bölmesinde, koruma aracısını güncelleştirmek istediğiniz istemci bilgisayarları seçin.
2. Özgün Azure Backup sunucusunu kapatın veya çevrimdışına alın.
3. Active Directory makine hesabını sıfırlayın.
4. Sunucu 2016 ' i yeni bir makineye yükleyip özgün Azure Backup sunucusuyla aynı makine adını verin.
5. Etki alanına katın.
6. Azure Backup Sunucusu v3 veya üstünü (MABS depolama havuzu disklerini eski sunucudan taşıyın ve içeri aktarın).
7. Adım 1 ' de alınan DPMDB 'yi geri yükleyin.
8. Özgün yedekleme sunucusundan yeni sunucuya depolama alanını ekleyin.
9. SQL 'den DPMDB 'yi geri yükleyin.
10. Yeni sunucuda CMD 'yi (yönetici olarak) çalıştırın. Microsoft Azure Backup install location ve bin klasörüne gidin.

    Yol örneği: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Azure Backup bağlanmak için, öğesini çalıştırın `DPMSYNC -SYNC` .

    DPM depolama havuzuna eski olanları taşımak yerine **Yeni** diskler eklediyseniz, öğesini çalıştırın `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Ağ bağlantısı

Azure Backup Sunucusu, ürünün başarıyla çalışması için Azure Backup hizmetine bağlantı kurulmasını gerektirir. Makinenin Azure bağlantısına sahip olup olmadığını doğrulamak için ```Get-DPMCloudConnection``` Azure Backup sunucusu PowerShell konsolundaki cmdlet 'ini kullanın. Cmdlet 'in çıktısı TRUE ise, bağlantı var, aksi takdirde bağlantı yok.

Aynı zamanda Azure aboneliğinin sağlıklı bir durumda olması gerekir. Aboneliğinizin durumunu öğrenmek ve yönetmek için [abonelik portalında](https://account.windowsazure.com/Subscriptions)oturum açın.

Azure bağlantısının ve Azure aboneliğinin durumunu öğrendikten sonra, sunulan yedekleme/geri yükleme işlevlerine etkisini öğrenmek için aşağıdaki tabloyu kullanabilirsiniz.

| Bağlantı durumu | Azure Aboneliği | Azure'a yedekleme | Diske Yedekle | Azure 'dan geri yükleme | Diskten geri yükleme |
| --- | --- | --- | --- | --- | --- |
| Bağlı |Etkin |İzin Verildi |İzin Verildi |İzin Verildi |İzin Verildi |
| Bağlı |Süresi doldu |Durduruldu |Durduruldu |İzin Verildi |İzin Verildi |
| Bağlı |Sağlaması kaldırıldı |Durduruldu |Durduruldu |Durdurulmuş ve Azure kurtarma noktaları silindi |Durduruldu |
| Kesilen bağlantı > 15 gün |Etkin |Durduruldu |Durduruldu |İzin Verildi |İzin Verildi |
| Kesilen bağlantı > 15 gün |Süresi doldu |Durduruldu |Durduruldu |İzin Verildi |İzin Verildi |
| Kesilen bağlantı > 15 gün |Sağlaması kaldırıldı |Durduruldu |Durduruldu |Durdurulmuş ve Azure kurtarma noktaları silindi |Durduruldu |

### <a name="recovering-from-loss-of-connectivity"></a>Bağlantı kaybından kurtarma

Makinenizin internet erişimi sınırlı ise, makinedeki veya proxy 'deki güvenlik duvarı ayarlarının aşağıdaki URL 'Lere ve IP adreslerine izin verildiğinden emin olun:

* URL’ler
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* IP adresleri
  * 20.190.128.0/18
  * 40.126.0.0/18

ExpressRoute Microsoft eşlemesi kullanıyorsanız, aşağıdaki hizmetleri/bölgeleri seçin:

* Azure Active Directory (12076:5060)
* Microsoft Azure bölgesi (Kurtarma Hizmetleri kasanızın konumuna göre)
* Azure depolama (Kurtarma Hizmetleri kasanızın konumuna göre)

Daha fazla ayrıntı için [ExpressRoute yönlendirme gereksinimleri](../expressroute/expressroute-routing.md)' ni ziyaret edin.

Azure 'a bağlantı Azure Backup Sunucusu makineye geri yüklendikten sonra, gerçekleştirilebilecek işlemler Azure abonelik durumu tarafından belirlenir. Yukarıdaki tabloda, makine "bağlandı" olduktan sonra izin verilen işlemlerle ilgili ayrıntılar bulunur.

### <a name="handling-subscription-states"></a>Abonelik durumlarını işleme

Bir Azure aboneliğini, *zaman aşımına uğradı* veya *sağlaması kaldırılmış* bir durumdan *etkin* duruma almak mümkündür. Ancak, bu durum *etkin* olmadığında ürünün davranışında bazı etkileri vardır:

* Sağlaması *kaldırılmış* bir abonelik, sağlanan süre için işlevselliği kaybeder. *Etkin* açıldığında, yedekleme/geri yükleme 'nin ürün işlevselliği yeniden canlandırılır. Yerel diskteki yedekleme verileri, yeterince büyük bir bekletme süresi ile tutuluyorsa da alınabilir. Ancak, abonelik *sağlanan* duruma girdiğinde Azure 'daki yedekleme verileri irretrievably kaybolur.
* *Süresi biten* bir abonelik, yalnızca yeniden *etkin* hale getirilene kadar işlevselliği kaybeder. Aboneliğin *süresi dolduğunda* zamanlanan tüm yedeklemeler çalışmaz.

## <a name="upgrade-mabs"></a>MABS 'leri yükselt

MABS 'yi yükseltmek için aşağıdaki yordamları kullanın.

### <a name="upgrade-from-mabs-v2-to-v3"></a>MABS v2 'den v3 'e yükseltme

> [!NOTE]
>
> MABS v2, MABS v3 yüklemesi için bir ön koşul değildir. Ancak MABS v3 sürümüne yalnızca MABS v2 'den yükseltebilirsiniz.

MABS 'yi yükseltmek için aşağıdaki adımları kullanın:

1. MABS v2 'den MABS v3 'e yükseltmek için, gerekirse işletim sistemini Windows Server 2016 veya Windows Server 2019 ' e yükseltin.

2. Sunucunuzu yükseltin. Adımlar [yüklemeye](#install-and-upgrade-azure-backup-server)benzerdir. Bununla birlikte, SQL ayarları için SQL örneğinizi SQL 2017 ' e yükseltme veya kendi SQL Server 2017 örneğini kullanma seçeneğini görürsünüz.

   > [!NOTE]
   >
   > SQL örneğiniz yükseltilirken çıkmayın. Çıkarken SQL Raporlama örneği kaldırılacak ve MABS 'i yeniden yükseltme girişimi başarısız olur.

   > [!IMPORTANT]
   >
   >  SQL 2017 yükseltmesinin bir parçası olarak SQL şifreleme anahtarlarını yedeklememiz ve raporlama hizmetlerini kaldırdık. SQL Server yükseltildikten sonra, raporlama hizmeti (14.0.6827.4788) yüklenir & şifreleme anahtarları geri yüklenir.
   >
   > SQL 2017 'yi el ile yapılandırırken, bkz. Install yönergeleri altındaki *sql 2017 Ile SSRS yapılandırması* bölümüne bakın.

3. Korunan sunuculardaki koruma aracılarını güncelleştirin.
4. Yedeklemeler, üretim sunucularınızı yeniden başlatmaya gerek kalmadan devam etmelidir.
5. Verilerinizi şimdi korumaya başlayabilirsiniz. Koruma sırasında Modern Yedekleme Alanı sürümüne yükseltiyorsanız, yedeklemeleri depolamak istediğiniz birimleri de seçebilir ve sağlanan alanın altında olup olmadığını kontrol edebilirsiniz. [Daha fazla bilgi edinin](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Sorun giderme

Microsoft Azure Backup sunucu kurulum aşaması (veya yedekleme ya da geri yükleme) sırasında hata vererek başarısız olursa daha fazla bilgi için bu [hata kodları belgesine](https://support.microsoft.com/kb/3041338)  bakın.

Ayrıca, [Ilgili sss Azure Backup](backup-azure-backup-faq.yml)de başvurabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

[ORTAMıNıZı DPM için hazırlama](/system-center/dpm/prepare-environment-for-dpm)hakkında ayrıntılı bilgi edinebilirsiniz. Ayrıca, Azure Backup Sunucusu dağıtılacağı ve kullanılabileceği desteklenen konfigürasyonlar hakkında bilgiler içerir. Çeşitli işlemleri gerçekleştirmek için bir dizi [PowerShell cmdlet](/powershell/module/dataprotectionmanager/) 'i kullanabilirsiniz.

Microsoft Azure Backup sunucusu kullanarak iş yükü korumasını daha ayrıntılı bir şekilde anlamak için bu makaleleri kullanabilirsiniz.

* [SQL Server yedekleme](backup-azure-backup-sql.md)
* [SharePoint Server yedekleme](backup-azure-backup-sharepoint.md)
* [Alternatif sunucu yedeklemesi](backup-azure-alternate-dpm-server.md)
