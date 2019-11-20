---
title: İş yüklerini yedeklemek için Azure Backup Sunucusu kullanma
description: Bu makalede, Microsoft Azure Backup sunucusu (MABS) kullanarak iş yüklerini korumak ve yedeklemek için ortamınızı nasıl hazırlayacağınızı öğrenin.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 7379992eeb441372a9140621f9d90b337ad0d2e2
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172984"
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

Azure VM 'de dağıtılan MABS, Azure 'daki VM 'Leri yedekleyebilir, ancak yedekleme işlemini etkinleştirmek için aynı etki alanında olmaları gerekir. Azure VM 'yi geri yükleme işlemi, şirket içi VM 'Leri yedeklemeye benzer kalır, ancak Azure 'da MABS dağıtımı bazı sınırlamalara sahiptir. Sınırlamalar hakkında daha fazla bilgi için bkz. [Azure sanal makinesi olarak DPM](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites)

> [!NOTE]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede, Kaynak Yöneticisi modeli kullanılarak dağıtılan VM 'Leri geri yüklemeyle ilgili bilgi ve yordamlar sağlanmaktadır.
>
>

Azure Backup Sunucusu, Data Protection Manager (DPM) iş yükü yedekleme işlevselliğinin çoğunu devralır. Bu makale, bazı paylaşılan işlevleri açıklamak için DPM belgelerine bağlantı sağlar. Azure Backup Sunucusu DPM ile aynı işlevlerin çoğunu paylaşıyorsa, Azure Backup Sunucusu banda yedeklememez ve System Center ile tümleşmez.

## <a name="choose-an-installation-platform"></a>Yükleme platformu seçin

Azure Backup Sunucusu çalışmaya ve çalıştırmaya yönelik ilk adım, bir Windows Server ayarlanalmaktır. Sunucunuz Azure 'da veya şirket içinde olabilir.

### <a name="using-a-server-in-azure"></a>Azure 'da sunucu kullanma

Azure Backup Sunucusu çalıştırmak için bir sunucu seçerken, Windows Server 2016 Datacenter veya Windows Server 2019 Datacenter 'un Galeri görüntüsüyle başlamanız önerilir. [Ilk Windows sanal makinenizi Azure Portal oluşturun](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), daha önce Azure 'u hiç kullanmamış olsanız bile, Azure 'da önerilen sanal makineyle çalışmaya başlama hakkında bir öğretici sağlar. Sunucu sanal makinesi (VM) için önerilen minimum gereksinimler: dört çekirdek ve 8 GB RAM ile Standard_A4_v2 olmalıdır.

Azure Backup Sunucusu olan iş yüklerini koruma çok sayıda nusmaya sahiptir. [Azure sanal makinesi olarak DPM 'Yi yükleyen](https://technet.microsoft.com/library/jj852163.aspx)makale, bu nusların açıklanmasına yardımcı olur. Makineyi dağıttıktan önce, bu makaleyi tamamen okuyun.

### <a name="using-an-on-premises-server"></a>Şirket içi sunucu kullanma

Temel sunucuyu Azure 'da çalıştırmak istemiyorsanız, sunucuyu bir Hyper-V VM 'sinde, VMware VM 'de veya fiziksel bir konakta çalıştırabilirsiniz. Sunucu donanımı için önerilen minimum gereksinimler iki çekirdek ve 8 GB RAM 'dir. Desteklenen işletim sistemleri aşağıdaki tabloda listelenmiştir:

| İşletim Sistemi | Platform | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2016 ve en son SPs |64 bit |Standard, Datacenter, Essentials  |

Windows Server yinelenenleri kaldırma 'yı kullanarak DPM depolama alanını yineleyebilirsiniz. Hyper-V VM 'lerinde dağıtıldığında [DPM ve yinelenenleri kaldırma](https://technet.microsoft.com/library/dn891438.aspx) özelliğinin birlikte nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!NOTE]
> Azure Backup Sunucusu, özel, tek amaçlı bir sunucuda çalışmak üzere tasarlanmıştır. Üzerinde Azure Backup Sunucusu yükleyemezsiniz:
>
> * Etki alanı denetleyicisi olarak çalıştırılan bir bilgisayar
> * Uygulama Sunucusu rolünün yüklü olduğu bir bilgisayar
> * System Center Operations Manager yönetim grubu olan bir bilgisayar
> * Exchange Server’ın çalıştırıldığı bir bilgisayar
> * Küme düğümü olan bir bilgisayar

Azure Backup Sunucusu her zaman bir etki alanına ekleyin. Sunucuyu farklı bir etki alanına taşımayı planlıyorsanız, önce Azure Backup Sunucusu yükleyip sunucuyu yeni etki alanına katın. Mevcut bir Azure Backup Sunucusu makinenin dağıtımdan sonra yeni bir etki alanına taşınması *desteklenmez*.

Yedekleme verilerini Azure 'a göndermenizi veya yerel olarak saklamayı, Azure Backup Sunucusu bir kurtarma hizmetleri kasasıyla kaydolmasının gerekip gerekmediğini belirtir.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Depolama Çoğaltmayı Ayarlama

Depolama çoğaltma seçeneği, coğrafi olarak yedekli depolama ve yerel olarak yedekli depolama arasında seçim yapmanıza olanak sağlar. Varsayılan olarak, kurtarma hizmetleri kasaları coğrafi olarak yedekli depolama kullanır. Bu kasa birincil kasanız, depolama seçeneğini coğrafi olarak yedekli depolama olarak ayarlayın. Daha düşük dayanıklılık düzeyinde olan daha uygun maliyetli bir seçenek istiyorsanız yerel olarak yedekli depolamayı seçin. [Coğrafi olarak yedekli](../storage/common/storage-redundancy-grs.md) ve [yerel olarak yedekli](../storage/common/storage-redundancy-lrs.md) depolama seçenekleri hakkında daha fazla bilgiyi [Azure Storage çoğaltmaya genel bakış](../storage/common/storage-redundancy.md) bölümünde edinebilirsiniz.

Depolama çoğaltma ayarını düzenlemek için:

1. **Kurtarma Hizmetleri kasaları** dikey penceresinden yeni kasaya tıklayın. **Ayarlar** bölümünde, **Özellikler**' e tıklayın.
2. **Özellikler**' de, **yedekleme yapılandırması**altında **Güncelleştir**' e tıklayın.

3. Depolama çoğaltma türünü seçin ve **Kaydet**' e tıklayın.

     ![Yeni kasa için depolama yapılandırması ayarlama](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Yazılım paketi

### <a name="downloading-the-software-package"></a>Yazılım paketi indiriliyor

1. [Azure portalında](https://portal.azure.com/) oturum açın.
2. Zaten açık bir kurtarma hizmetleri Kasası varsa adım 3 ' e geçin. Bir kurtarma hizmetleri Kasası açık değilse, ancak Azure portal, ana menüde, **Araştır**' a tıklayın.

   * Kaynak listesinde **Kurtarma Hizmetleri** yazın.
   * Yazmaya başladığınızda liste, girdinize göre filtrelenir. **Kurtarma Hizmetleri kasaları** seçeneğini gördüğünüzde buna tıklayın.

     ![Kurtarma Hizmetleri Kasası oluşturma 1. adım](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Kurtarma Hizmetleri kasalarının listesi görünür.
   * Kurtarma Hizmetleri kasalarının listesinden bir kasa seçin.

     Seçilen kasa panosu açılır.

     ![Kasa dikey penceresini açma](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. **Ayarlar** dikey penceresi varsayılan olarak açılır. Kapalıysa, ayarlar dikey penceresini açmak için **Ayarlar** ' a tıklayın.

    ![Kasa dikey penceresini açma](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Başlangıç Sihirbazı 'nı açmak için **Yedekle** ' ye tıklayın.

    ![Yedekleme başlangıç](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Açılan **yedeklemeye Başlarken** dikey penceresinde **yedekleme hedefleri** otomatik olarak seçilir.

    ![Yedekleme-hedefler-varsayılan-açılan](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. **Yedekleme hedefi** dikey penceresinde, **iş yükünüzün çalıştırıldığı konum** menüsünde **Şirket içi**' ı seçin.

    ![amaç olarak şirket içi ve iş yükleri](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    **Neleri yedeklemek istiyorsunuz?** açılan menüsünde, Azure Backup sunucusu kullanarak korumak istediğiniz iş yüklerini seçin ve ardından **Tamam**' a tıklayın.

    **Yedeklemeyi kullanmaya** başlama Sihirbazı, Iş yüklerini Azure 'a yedeklemek Için **altyapıyı hazırla** seçeneğini değiştirir.

   > [!NOTE]
   > Yalnızca dosyaları ve klasörleri yedeklemek istiyorsanız, Azure Backup Aracısı 'nı kullanmanızı ve makalenin kılavuzunu takip etmenizi öneririz. [önce dosya ve klasörleri yedekleme konusuna bakmanız](backup-try-azure-backup-in-10-mins.md)önerilir. Dosya ve klasörlerden daha fazlasını koruyacaksanız veya gelecekte koruma ihtiyaçlarını genişletmeyi planlıyorsanız, bu iş yüklerini seçin.
   >
   >

    ![Başlarken Sihirbazı değişikliği](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Açılan **altyapıyı hazırla** dikey penceresinde **, Yükle Azure Backup sunucusu yükleme bağlantıları '** na tıklayın ve kasa kimlik bilgilerini indirin. Kurtarma Hizmetleri kasasına Azure Backup Sunucusu kayıt sırasında kasa kimlik bilgilerini kullanırsınız. Bağlantılar sizi yazılım paketinin indirilebileceği Indirme merkezine götürür.

    ![Altyapıyı Azure Backup Sunucusu için hazırla](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Tüm dosyalar ' ı seçin ve **İleri**' ye tıklayın. Microsoft Azure Backup indirme sayfasından gelen tüm dosyaları indirin ve tüm dosyaları aynı klasöre yerleştirin.

    ![İndirme Merkezi 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Tüm dosyaların karşıdan yükleme boyutu > 3G olduğundan, 10 Mbps bir indirme bağlantısında, indirme işleminin tamamlanması 60 dakika kadar sürebilir.

### <a name="extracting-the-software-package"></a>Yazılım paketi ayıklanıyor

Tüm dosyaları indirdikten sonra, **MicrosoftAzureBackupInstaller. exe**' ye tıklayın. Bu işlem, kurulum dosyalarını sizin tarafınızdan belirtilen bir konuma ayıklamak için **Microsoft Azure Backup Kurulum Sihirbazı 'nı** başlatır. Sihirbazdan ilerleyin ve ayıklama işlemine başlamak için **Ayıkla** düğmesine tıklayın.

> [!WARNING]
> Kurulum dosyalarını ayıklamak için en az 4 GB boş alan gerekir.
>
>

![Microsoft Azure Backup Kurulum Sihirbazı](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Ayıklama işlemi tamamlandıktan sonra, Microsoft Azure Backup sunucusunu yüklemeye başlamak ve **son** düğmesine tıklamak için, el ile ayıklanan *Setup. exe* ' yi başlatmak için kutuyu işaretleyin.

### <a name="installing-the-software-package"></a>Yazılım paketini yükleme

1. Kurulum sihirbazını başlatmak için **Microsoft Azure Backup** ' a tıklayın.

    ![Microsoft Azure Backup Kurulum Sihirbazı](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Hoş Geldiniz ekranında **İleri** düğmesine tıklayın. Bu sizi *önkoşul denetimleri* bölümüne götürür. Bu ekranda, Azure Backup Sunucusu için donanım ve yazılım önkoşullarının karşılanıp karşılanmadığını öğrenmek için **Denetle** ' ye tıklayın. Tüm Önkoşullar başarılı bir şekilde karşılanıyorsa, makinenin gereksinimleri karşıladığını belirten bir ileti görürsünüz. **İleri** düğmesine tıklayın.

    ![Azure Backup Sunucusu-hoş geldiniz ve önkoşul denetimi](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Microsoft Azure Backup sunucu SQL Server Enterprise gerektiriyor. Ayrıca, Azure Backup Sunucusu yükleme paketi, kendi SQL 'nizi kullanmak istemediğiniz durumlarda gerekli olan uygun SQL Server ikili dosyalarla birlikte paketlenmiştir. Yeni bir Azure Backup Sunucusu yüklemesiyle Başlarken, **Bu kurulum ile yeni SQL Server örneğini yükleme** seçeneğini seçmeniz ve **Çek ve yükleme** düğmesine tıklamanız gerekir. Önkoşullar başarıyla yüklendikten sonra **İleri**' ye tıklayın.

    ![Azure Backup Sunucusu-SQL denetimi](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Makineyi yeniden başlatma önerisiyle ilgili bir hata oluşursa, bunu yapın ve **yeniden denetle**' ye tıklayın. Herhangi bir SQL yapılandırma sorunu varsa, SQL yönergeleri başına SQL 'i yeniden yapılandırın ve mevcut SQL örneğini kullanarak MABS 'i yüklemeyi/yükseltmeyi yeniden deneyin.

   > [!NOTE]
   > Azure Backup Sunucusu, uzak bir SQL Server örneğiyle çalışmayacak. Azure Backup Sunucusu tarafından kullanılan örneğin yerel olması gerekir. Mabs için mevcut bir SQL Server kullanıyorsanız, MABS kurulumu yalnızca SQL Server 'ın *adlandırılmış örneklerinin* kullanılmasını destekler.

   **El ile yapılandırma**

   Kendi SQL örneğinizi kullandığınızda ana DB 'ye sysadmin rolüne BUILTIN\Administrators eklediğinizden emin olun.

    **SQL 2017 ile SSRS yapılandırması**

    Kendi SQL 2017 örneğinizi kullandığınızda SSRS 'yi el ile yapılandırmanız gerekir. SSRS yapılandırmasından sonra, SSRS 'nin *ısınbaşlatılmış* özelliğinin *true*olarak ayarlandığından emin olun. Bu true olarak ayarlandığında, MABS SSRS 'nin zaten yapılandırıldığını varsayar ve SSRS yapılandırmasını atlar.

    SSRS yapılandırması için aşağıdaki değerleri kullanın:
    * Hizmet hesabı: ' yerleşik hesabı kullan ' ağ hizmeti olmalıdır
    * Web hizmeti URL 'SI: ' sanal dizin ' ReportServer_\<SqlInstanceName olmalıdır >
    * Veritabanı: DatabaseName ReportServer $\<SqlInstanceName olmalıdır >
    * Web portalı URL 'SI: ' sanal dizin ' Reports_\<SqlInstanceName > olmalıdır

    SSRS yapılandırması hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) .

    > [!NOTE]
    > MABS için veritabanı olarak kullanılan SQL Server Lisanslama, [Microsoft çevrimiçi hizmet koşulları](https://www.microsoft.com/licensing/product-licensing/products) (OST) tarafından yönetilir. OST 'ye göre, MABS ile paketlenmiş SQL Server yalnızca MABS için veritabanı olarak kullanılabilir.

4. Microsoft Azure Backup sunucusu dosyalarının yüklenmesi için bir konum belirtin ve **İleri**' ye tıklayın.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    Karalama konumu Azure 'a yedekleme gereksinimidir. Karalama konumunun, buluta yedeklenmek üzere planlandığı verilerin en az %5 ' i olduğundan emin olun. Disk koruması için, yükleme tamamlandıktan sonra ayrı disklerin yapılandırılması gerekir. Depolama havuzları hakkında daha fazla bilgi için bkz. [depolama havuzlarını ve Disk depolamayı yapılandırma](https://technet.microsoft.com/library/hh758075.aspx).
5. Kısıtlanmış yerel kullanıcı hesapları için güçlü bir parola sağlayın ve **İleri**' ye tıklayın.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Güncelleştirmeleri denetlemek için *Microsoft Update* kullanmak isteyip istemediğinizi seçin ve **İleri**' ye tıklayın.

   > [!NOTE]
   > Windows ve Microsoft Azure Backup Server gibi diğer ürünler için güvenlik ve önemli güncelleştirmeler sunan Microsoft Update Windows Update yeniden yönlendirmenizi öneririz.
   >
   >

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. *Ayarların özetini* gözden geçirin ve **yükler**' e tıklayın.

    ![Microsoft Azure Backup PreReq2](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. Yükleme aşamalarda gerçekleşir. İlk aşamada Microsoft Azure Kurtarma Hizmetleri Aracısı sunucuya yüklenir. Sihirbaz ayrıca Internet bağlantısını denetler. Internet bağlantısı varsa, yükleme işlemine devam edebilirsiniz, yoksa, Internet 'e bağlanmak için proxy ayrıntıları sağlamanız gerekir.

    Sonraki adım Microsoft Azure Kurtarma Hizmetleri aracısını yapılandırmaktır. Yapılandırmanın bir parçası olarak, makineyi kurtarma hizmetleri kasasına kaydetmek için kasa kimlik bilgilerinizi sağlamanız gerekir. Ayrıca, Azure ile şirket içi arasında gönderilen verileri şifrelemek/şifrelerini çözmek için bir parola de sağlarsınız. Otomatik olarak bir parola oluşturabilir veya en düşük 16 karakterlik parolanızı sağlayabilirsiniz. Aracı yapılandırılana kadar sihirbaza devam edin.

    ![Azure Backup Sunucusu PreReq2](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Microsoft Azure Backup sunucusunun kaydı başarıyla tamamlandıktan sonra, Genel Kurulum Sihirbazı SQL Server ve Azure Backup Sunucusu bileşenleri için yükleme ve yapılandırmaya geçer. SQL Server bileşeni yüklemesi tamamlandıktan sonra, Azure Backup Sunucusu bileşenleri yüklenir.

    ![Azure Backup Sunucusu](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Yükleme adımı tamamlandığında, ürünün masaüstü simgeleri de oluşturulur. Ürünü başlatmak için simgeye çift tıklayın.

### <a name="add-backup-storage"></a>Yedekleme depolama alanı ekleme

İlk yedekleme kopyası Azure Backup Sunucusu makineye bağlı depolamada tutulur. Disk ekleme hakkında daha fazla bilgi için bkz. [depolama havuzlarını ve Disk depolamayı yapılandırma](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> Verileri Azure 'a göndermek için planlıyor olsanız bile yedekleme depolama alanı eklemeniz gerekir. Geçerli Azure Backup Sunucusu mimarisinde, yerel depolama ilk (ve zorunlu) yedek kopyayı tutarken, Azure Backup Kasası verilerin *ikinci* kopyasını tutar.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Data Protection Manager koruma aracısını yükleyip güncelleştirme

MABS, System Center Data Protection Manager koruma Aracısı 'nı kullanır. Koruma aracısını koruma sunucularınıza yüklemek için aşağıdaki [adımları uygulayın](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) .

Aşağıdaki bölümlerde, istemci bilgisayarlar için koruma aracılarının nasıl güncelleştirilmesi anlatılmaktadır.

1. Yedekleme sunucusu Yönetici Konsolu, **yönetim** > **aracıları**' nı seçin.

2. Görüntüleme bölmesinde, koruma aracısını güncelleştirmek istediğiniz istemci bilgisayarları seçin.

   > [!NOTE]
   > **Aracı Updates** sütunu, korunan her bilgisayar için bir koruma Aracısı güncelleştirmesi kullanılabileceğini gösterir. **Eylemler** bölmesinde, **güncelleştirme** eylemi yalnızca korumalı bir bilgisayar seçildiğinde ve güncelleştirmeler kullanılabilir olduğunda kullanılabilir.
   >
   >

3. Seçili bilgisayarlara güncelleştirilmiş koruma aracıları yüklemek için, **Eylemler** bölmesinde **Güncelleştir**' i seçin.

4. Ağa bağlı olmayan bir istemci bilgisayar için, bilgisayar ağa bağlanana kadar **Aracı durumu** sütununda **bekleyen güncelleştirme**durumu gösterilmektedir.

   Bir istemci bilgisayar ağa bağlandıktan sonra, istemci bilgisayar için **Aracı Updates** sütununda **güncelleştirme**durumu gösterilir.

## <a name="move-mabs-to-a-new-server"></a>MABS 'leri yeni bir sunucuya taşıma

Depolama alanını korurken MABS 'i yeni bir sunucuya taşımanız gerekiyorsa buradaki adımlar aşağıda verilmiştir. Bu, yalnızca tüm veriler Modern Yedekleme Alanı durumunda yapılabilir.

  > [!IMPORTANT]
  >
  > * Yeni sunucu adı, özgün Azure Backup Sunucusu örneğiyle aynı ad olmalıdır. Kurtarma noktalarını sürdürmek için önceki depolama havuzunu ve MABS veritabanını (DPMDB) kullanmak istiyorsanız yeni Azure Backup Sunucusu örneğinin adını değiştiremezsiniz.
  > * MABS veritabanının (DPMDB) yedeğine sahip olmanız gerekir. Veritabanını geri yüklemeniz gerekir.

1. Görüntüleme bölmesinde, koruma aracısını güncelleştirmek istediğiniz istemci bilgisayarları seçin.
2. Özgün Azure Backup sunucusunu kapatın veya kablo dışına alın.
3. Active Directory 'de makine hesabını sıfırlayın.
4. Sunucu 2016 ' i yeni makineye yükleyip özgün Azure Backup sunucusuyla aynı makine adına adlandırın.
5. Etki alanına katılarak
6. Azure Backup Server v3 veya üstünü (MABS depolama havuzu disklerini eski sunucudan taşıyın ve içeri aktarın)
7. Adım 1 ' de alınan DPMDB 'yi geri yükleyin.
8. Özgün yedekleme sunucusundan yeni sunucuya depolama alanını ekleyin.
9. SQL geri yükleme 'den DPMDB
10. Yeni sunucu CD 'sindeki yönetici komut satırı Microsoft Azure Backup konum ve bin klasörünü yüklemek için

    Yol örneği: C:\Windows\System32 > CD "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"

11. Azure Backup 'ta DPMSYNC-SYNC çalıştırın

    DPM depolama havuzuna eski olanları taşımak yerine yenı diskler eklediyseniz DPMSYNC-Reallocatereplica çalıştırın

## <a name="network-connectivity"></a>Ağ bağlantısı

Azure Backup Sunucusu, ürünün başarıyla çalışması için Azure Backup hizmetine bağlantı kurulmasını gerektirir. Makinenin Azure bağlantısına sahip olup olmadığını doğrulamak için, Azure Backup Sunucusu PowerShell konsolundaki ```Get-DPMCloudConnection``` cmdlet 'ini kullanın. Cmdlet 'in çıktısı TRUE ise, bağlantı var, aksi durumda bağlantı yok.

Aynı zamanda Azure aboneliğinin sağlıklı bir durumda olması gerekir. Aboneliğinizin durumunu öğrenmek ve yönetmek için [abonelik portalında](https://account.windowsazure.com/Subscriptions)oturum açın.

Azure bağlantısının ve Azure aboneliğinin durumunu öğrendikten sonra, sunulan yedekleme/geri yükleme işlevlerine etkisini öğrenmek için aşağıdaki tabloyu kullanabilirsiniz.

| Bağlantı durumu | Azure Aboneliği | Azure 'a yedekleme | Diske Yedekle | Azure 'dan geri yükleme | Diskten geri yükleme |
| --- | --- | --- | --- | --- | --- |
| Bağlı |Etkin |İzin Verildi |İzin Verildi |İzin Verildi |İzin Verildi |
| Bağlı |Süresi doldu |Durduruldu |Durduruldu |İzin Verildi |İzin Verildi |
| Bağlı |Sağlaması kaldırıldı |Durduruldu |Durduruldu |Durdurulmuş ve Azure kurtarma noktaları silindi |Durduruldu |
| Kesilen bağlantı > 15 gün |Etkin |Durduruldu |Durduruldu |İzin Verildi |İzin Verildi |
| Kesilen bağlantı > 15 gün |Süresi doldu |Durduruldu |Durduruldu |İzin Verildi |İzin Verildi |
| Kesilen bağlantı > 15 gün |Sağlaması kaldırıldı |Durduruldu |Durduruldu |Durdurulmuş ve Azure kurtarma noktaları silindi |Durduruldu |

### <a name="recovering-from-loss-of-connectivity"></a>Bağlantı kaybından kurtarma

Azure 'a erişimi engelleyen bir güvenlik duvarınız veya bir ara sunucu varsa, güvenlik duvarı/proxy profilinde aşağıdaki etki alanı adreslerine izin vermeniz gerekir:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*. microsoftonline.com
* \*. windows.net

Azure 'a bağlantı Azure Backup Sunucusu makineye geri yüklendikten sonra, gerçekleştirilebilecek işlemler Azure abonelik durumu tarafından belirlenir. Yukarıdaki tabloda, makine "bağlandı" olduktan sonra izin verilen işlemlerle ilgili ayrıntılar bulunur.

### <a name="handling-subscription-states"></a>Abonelik durumlarını işleme

Bir Azure aboneliğini, *zaman aşımına uğradı* veya *sağlaması kaldırılmış* bir durumdan *etkin* duruma almak mümkündür. Ancak, durum *etkin*olmadığı sürece ürünün davranışında bazı etkileri vardır:

* Sağlaması *kaldırılmış* bir abonelik, sağlanmakta olan süre için işlevselliği kaybeder. *Etkin*açıldığında, yedekleme/geri yükleme 'nin ürün işlevselliği yeniden canlandırılır. Yerel diskteki yedekleme verileri, yeterince büyük bir bekletme süresi ile tutuluyorsa da alınabilir. Ancak, abonelik *sağlanan* duruma girdiğinde Azure 'daki yedekleme verileri irretrievably kaybolur.
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
   > SQL örneğiniz yükseltilirken çıkmayın, çıkılıyor SQL Raporlama örneğini kaldıracak ve bu nedenle MABS 'i yeniden yükseltme girişimi başarısız olur.

   > [!IMPORTANT]
   >
   >  SQL 2017 yükseltmesinin bir parçası olarak SQL şifreleme anahtarlarını yedeklememiz ve raporlama hizmetlerini kaldırdık. SQL Server yükseltildikten sonra, raporlama hizmeti (14.0.6827.4788) yüklenir & şifreleme anahtarları geri yüklenir.
   >
   > SQL 2017 'yi el ile yapılandırırken, bkz. Install yönergeleri altındaki *sql 2017 Ile SSRS yapılandırması* bölümüne bakın.

3. Korunan sunuculardaki koruma aracılarını güncelleştirin.
4. Yedeklemeler, üretim sunucularınızı yeniden başlatmaya gerek kalmadan devam etmelidir.
5. Verilerinizi şimdi korumaya başlayabilirsiniz. Koruma sırasında Modern Yedekleme Alanı sürümüne yükseltiyorsanız, yedeklemeleri depolamak istediğiniz birimleri de seçebilir ve sağlanan alanın altında olup olmadığını kontrol edebilirsiniz. [Daha fazla bilgi edinin](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Sorun giderme

Microsoft Azure Backup sunucu kurulum aşaması (veya yedekleme ya da geri yükleme) sırasında hata vererek başarısız olursa daha fazla bilgi için bu [hata kodları belgesine](https://support.microsoft.com/kb/3041338) bakın.
Ayrıca, [Ilgili sss Azure Backup](backup-azure-backup-faq.md) de başvurabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

Microsoft TechNet sitesinde [DPM için ortamınızı hazırlama](https://technet.microsoft.com/library/hh758176.aspx) hakkında ayrıntılı bilgi edinebilirsiniz. Ayrıca, Azure Backup Sunucusu dağıtılacağı ve kullanılabileceği desteklenen konfigürasyonlar hakkında bilgiler içerir. Çeşitli işlemleri gerçekleştirmek için bir dizi [PowerShell cmdlet 'i](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) kullanabilirsiniz.

Microsoft Azure Backup sunucusu kullanarak iş yükü korumasını daha ayrıntılı bir şekilde anlamak için bu makaleleri kullanabilirsiniz.

* [SQL Server yedekleme](backup-azure-backup-sql.md)
* [SharePoint Server yedekleme](backup-azure-backup-sharepoint.md)
* [Alternatif sunucu yedeklemesi](backup-azure-alternate-dpm-server.md)
