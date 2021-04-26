---
title: Azure Stack üzerinde Azure Backup Sunucusu'nu yükleme
description: Bu makalede, Azure Stack iş yüklerini korumak veya yedeklemek için Azure Backup Sunucusu nasıl kullanacağınızı öğrenin.
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: d7c685a16db50e51a54387dde49ffb137fcfd626
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519485"
---
# <a name="install-azure-backup-server-on-azure-stack"></a>Azure Stack üzerinde Azure Backup Sunucusu'nu yükleme

Bu makalede Azure Stack Azure Backup Sunucusu nasıl yükleneceği açıklanır. Azure Backup Sunucusu ile, Azure Stack çalışan sanal makineler gibi hizmet olarak altyapı (IaaS) iş yüklerinin korunmasını sağlayabilirsiniz. İş yüklerinizi korumak için Azure Backup Sunucusu kullanmanın bir avantajı, tüm iş yükü korumasını tek bir konsoldan yönetebilmeniz için bir avantajdır.

> [!NOTE]
> Güvenlik özellikleri hakkında bilgi edinmek için [Azure Backup güvenlik özellikleri belgelerine](backup-azure-security-feature.md)bakın.
>

## <a name="azure-backup-server-protection-matrix"></a>Azure Backup Sunucusu koruma matrisi

Azure Backup Sunucusu aşağıdaki Azure Stack sanal makine iş yüklerini korur.

| Korumalı veri kaynağı | Koruma ve kurtarma |
| --------------------- | ----------------------- |
| Windows Server yarı yıllık kanal-Datacenter/Enterprise/Standard | Birimler, dosyalar, klasörler |
| Windows Server 2016-Datacenter/Enterprise/Standard | Birimler, dosyalar, klasörler |
| Windows Server 2012 R2-Datacenter/Enterprise/Standard | Birimler, dosyalar, klasörler |
| Windows Server 2012-Datacenter/Enterprise/Standard | Birimler, dosyalar, klasörler |
| Windows Server 2008 R2-Datacenter/Enterprise/Standard | Birimler, dosyalar, klasörler |
| SQL Server 2016 | Veritabanı |
| SQL Server 2014 | Veritabanı |
| SQL Server 2012 SP1 | Veritabanı |
| SharePoint 2016 | Grup, veritabanı, ön uç, Web sunucusu |
| SharePoint 2013 | Grup, veritabanı, ön uç, Web sunucusu |
| SharePoint 2010 | Grup, veritabanı, ön uç, Web sunucusu |

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure Backup Sunucusu ortamı önkoşulları

Azure Stack ortamınıza Azure Backup Sunucusu yüklerken bu bölümdeki önerileri göz önünde bulundurun. Azure Backup Sunucusu yükleyicisi ortamınızın gerekli önkoşullara sahip olduğunu denetler, ancak yüklemeden önce hazırlanırken zaman kazanabilirsiniz.

### <a name="determining-size-of-virtual-machine"></a>Sanal makinenin boyutunu belirleme

Azure Stack bir sanal makinede Azure Backup Sunucusu çalıştırmak için, a2 veya daha büyük boyut kullanın. Bir sanal makine boyutu seçme konusunda yardım için [Azure Stack VM boyutu hesaplayıcısını](https://www.microsoft.com/download/details.aspx?id=56832)indirin.

### <a name="virtual-networks-on-azure-stack-virtual-machines"></a>Azure Stack sanal makinelerde sanal ağlar

Bir Azure Stack iş yükünde kullanılan tüm sanal makineler aynı Azure sanal ağına ve Azure aboneliğine ait olmalıdır.

### <a name="azure-backup-server-vm-performance"></a>VM performansını Azure Backup Sunucusu

Diğer sanal makinelerle paylaşılırsa, depolama hesabı boyutu ve ıOPS sınırları Azure Backup Sunucusu VM performansını etkiler. Bu nedenle, Azure Backup Sunucusu sanal makinesi için ayrı bir depolama hesabı kullanmanız gerekir. Azure Backup Sunucusu çalıştıran Azure Backup Aracısı için geçici depolamaya ihtiyaç duyuyor:

- kendi kullanımı (bir önbellek konumu),
- buluttan geri yüklenen veriler (yerel hazırlama alanı)

### <a name="configuring-azure-backup-temporary-disk-storage"></a>Azure Backup geçici disk depolamayı yapılandırma

Her Azure Stack sanal makine, Kullanıcı tarafından birim olarak kullanılabilen geçici disk depolama alanıyla birlikte gelir `D:\` . Azure Backup için gereken yerel hazırlama alanı, içinde bulunacak şekilde yapılandırılabilir `D:\` ve önbellek konumu üzerine yerleştirilebilecek `C:\` . Bu şekilde, Azure Backup Sunucusu sanal makinesine bağlı veri disklerinden hiçbir depolamanın ayrılması gerekmez.

### <a name="storing-backup-data-on-local-disk-and-in-azure"></a>Yedekleme verilerini yerel diskte ve Azure 'da depolama

Azure Backup Sunucusu, işletimsel kurtarma için sanal makineye bağlı Azure disklerinde yedekleme verilerini depolar. Diskler ve depolama alanı sanal makineye eklendikten sonra, depolamayı sizin için yönetir Azure Backup Sunucusu. Yedekleme veri depolama miktarı, her bir [Azure Stack sanal makinesine](/azure-stack/user/azure-stack-storage-overview)bağlı disklerin sayısına ve boyutuna bağlıdır. Azure Stack VM 'nin her boyutunun, sanal makineye eklenebilecek en fazla disk sayısı vardır. Örneğin, a2 dört diskdir. A3 sekiz disk. A4 16 diskdir. Yeniden, disk boyutu ve sayısı toplam yedekleme depolama havuzunu belirler.

> [!IMPORTANT]
> Beş günden uzun bir süre boyunca Azure Backup Sunucusu bağlı disklerde işlemsel kurtarma (yedekleme) **verilerini saklamamalıdır** .
>

Yedekleme verilerinin Azure 'da depolanması, Azure Stack üzerindeki yedekleme altyapısını azaltır. Veriler beş günden eski olursa Azure 'da depolanması gerekir.

Yedekleme verilerini Azure 'da depolamak için bir kurtarma hizmetleri Kasası oluşturun veya kullanın. Azure Backup Sunucusu iş yükünü yedeklemeye hazırlanırken, [Kurtarma Hizmetleri kasasını yapılandırırsınız](backup-azure-microsoft-azure-backup.md#create-a-recovery-services-vault). Yapılandırıldıktan sonra, bir yedekleme işi her çalıştığında kasada bir kurtarma noktası oluşturulur. Her kurtarma hizmetleri Kasası en fazla 9999 kurtarma noktası içerir. Oluşturulan kurtarma noktası sayısına ve ne kadar süreyle saklandığına bağlı olarak, yedekleme verilerini birçok yıl boyunca koruyabilirsiniz. Örneğin, aylık kurtarma noktaları oluşturabilir ve bunları beş yıl boyunca koruyabilirsiniz.

### <a name="scaling-deployment"></a>Dağıtım ölçeklendiriliyor

Dağıtımınızı ölçeklendirmek istiyorsanız aşağıdaki seçeneklere sahip olursunuz:

- Ölçeği artırma-bir serideki Azure Backup Sunucusu sanal makinenin boyutunu D serisine yükseltin ve [Azure Stack sanal makine yönergeleri temelinde](/azure-stack/user/azure-stack-manage-vm-disks)yerel depolamayı yükseltin.
- Veri boşaltma-eski verileri Azure 'a gönderin ve Azure Backup Sunucusu bağlı depolamada yalnızca en yeni verileri tutun.
- Ölçeği genişletme-iş yüklerini korumak için daha fazla Azure Backup sunucusu ekleyin.

### <a name="net-framework"></a>.NET Framework

.NET Framework 3,5 SP1 veya üzeri sanal makinede yüklü olmalıdır.

### <a name="joining-a-domain"></a>Etki alanına katılma

Azure Backup Sunucusu sanal makinenin bir etki alanına katılması gerekir. Yönetici ayrıcalıklarına sahip bir etki alanı kullanıcısının sanal makineye Azure Backup Sunucusu yüklemesi gerekir.

## <a name="using-an-iaas-vm-in-azure-stack"></a>Azure Stack bir IaaS VM kullanma

Azure Backup Sunucusu için bir sunucu seçerken, bir Windows Server 2012 R2 Datacenter veya Windows Server 2016 Datacenter Galeri görüntüsü ile başlayın. Makalede, [Ilk Windows sanal makinenizi Azure Portal oluşturun](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json), önerilen sanal makine ile çalışmaya başlama hakkında bir öğretici sağlar. Sunucu sanal makinesi (VM) için önerilen minimum gereksinimler şunlardır: iki çekirdek ve 3,5 GB RAM ile a2 standart.

Azure Backup Sunucusu olan iş yüklerini koruma çok sayıda nusmaya sahiptir. [MABS için koruma matrisi](./backup-mabs-protection-matrix.md) bu nusların açıklanmasına yardımcı olur. Makineyi dağıttıktan önce, bu makaleyi tamamen okuyun.

> [!NOTE]
> Azure Backup Sunucusu, özel, tek amaçlı bir sanal makinede çalışmak üzere tasarlanmıştır. Üzerinde Azure Backup Sunucusu yükleyemezsiniz:
>
> - Etki alanı denetleyicisi olarak çalıştırılan bir bilgisayar
> - Uygulama Sunucusu rolünün yüklü olduğu bir bilgisayar
> - Exchange Server’ın çalıştırıldığı bir bilgisayar
> - Bir kümenin düğümü olan bir bilgisayar

Azure Backup Sunucusu her zaman bir etki alanına ekleyin. Azure Backup Sunucusu farklı bir etki alanına taşımanız gerekiyorsa, önce Azure Backup Sunucusu yükledikten sonra yeni etki alanına katın. Azure Backup Sunucusu dağıttıktan sonra, yeni bir etki alanına taşıyamazsınız.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Depolama Çoğaltmayı Ayarlama

Kurtarma Hizmetleri Kasası depolama çoğaltma seçeneği, coğrafi olarak yedekli depolama ve yerel olarak yedekli depolama arasında seçim yapmanıza olanak sağlar. Varsayılan olarak, kurtarma hizmetleri kasaları coğrafi olarak yedekli depolama kullanır. Bu kasa birincil kasanız, depolama seçeneğini coğrafi olarak yedekli depolama olarak ayarlayın. Daha az dayanıklı olan bir ucuz seçeneği istiyorsanız yerel olarak yedekli depolamayı seçin. [Azure Storage Çoğaltmaya genel bakış](../storage/common/storage-redundancy.md)bölümünde [coğrafi olarak yedekli](../storage/common/storage-redundancy.md#geo-redundant-storage), [yerel olarak yedekli](../storage/common/storage-redundancy.md#locally-redundant-storage)ve bölgesel olarak [yedekli depolama seçenekleri](../storage/common/storage-redundancy.md#zone-redundant-storage) hakkında daha fazla bilgi edinin.

Depolama çoğaltma ayarını düzenlemek için:

1. Kasa panosunu ve Ayarlar menüsünü açmak için kasanızı seçin. **Ayarlar** menüsü açılmazsa, kasa panosunda **Tüm ayarlar** ' ı seçin.
2. **Ayarlar** menüsünde, yedekleme **altyapısı**  >  **yedekleme yapılandırması** ' nı seçerek **yedekleme yapılandırma** menüsünü açın. **Yedekleme yapılandırması** menüsünde, kasanızın depolama çoğaltma seçeneğini belirleyin.

    ![Yedekleme kasalarının listesi](./media/backup-azure-vms-first-look-arm/choose-storage-configuration-rs-vault.png)

## <a name="download-azure-backup-server-installer"></a>Azure Backup Sunucusu yükleyiciyi indir

Azure Backup Sunucusu yükleyicisini indirmenin iki yolu vardır. Azure Backup Sunucusu yükleyicisini [Microsoft Indirme merkezi](https://www.microsoft.com/download/details.aspx?id=55269)' nden indirebilirsiniz. Ayrıca, kurtarma hizmetleri kasasını yapılandırırken Azure Backup Sunucusu yükleyicisi de indirebilirsiniz. Aşağıdaki adımlar, bir kurtarma hizmetleri kasasını yapılandırırken yükleyiciyi Azure portal karşıdan yükleme işleminde size kılavuzluk yapar.

1. Azure Stack sanal makinenizden [Azure Portal Azure aboneliğinizde oturum açın](https://portal.azure.com/).
2. Sol taraftaki menüden **tüm hizmetler**' i seçin.

    ![Ana menüdeki tüm hizmetler seçeneğini belirleyin](./media/backup-mabs-install-azure-stack/click-all-services.png)

3. **Tüm hizmetler** Iletişim kutusunda *Kurtarma Hizmetleri* yazın. Yazmaya başladığınızda, giriş, kaynak listesini filtreler. Bunu görtikten sonra **Kurtarma Hizmetleri kasaları**' nı seçin.

    ![Tüm Hizmetler iletişim kutusunda kurtarma hizmetleri yazın](./media/backup-mabs-install-azure-stack/all-services.png)

    Abonelikteki Kurtarma Hizmetleri kasalarının listesi görünür.

4. Kurtarma Hizmetleri kasaları listesinden, kendi panosunu açmak için kasanızı seçin.

    ![Panoyu açmak için kasanızı seçin](./media/backup-mabs-install-azure-stack/rs-vault-dashboard.png)

5. Kasanın Başlarken menüsünde, Başlangıç Sihirbazı 'nı açmak için **Yedekle** ' yi seçin.

    ![Yedekleme başlangıç](./media/backup-mabs-install-azure-stack/getting-started-backup.png)

    Yedekleme menüsü açılır.

    ![Yedekleme-hedefler-varsayılan-açılan](./media/backup-mabs-install-azure-stack/getting-started-menu.png)

6. Yedekleme menüsünde, **iş yükünüzün çalıştırıldığı konum** menüsünde **Şirket içi**' ı seçin. **Neleri yedeklemek istiyorsunuz?** açılan menüsünde, Azure Backup sunucusu kullanarak korumak istediğiniz iş yüklerini seçin. Hangi iş yüklerini seçdiğinizden emin değilseniz, **Hyper-V sanal makineler** ' i seçin ve ardından **altyapıyı hazırla**' yı seçin.

    ![amaç olarak şirket içi ve iş yükleri](./media/backup-mabs-install-azure-stack/getting-started-menu-onprem-hyperv.png)

    **Altyapıyı hazırla** menüsü açılır.

7. **Altyapıyı hazırla** menüsünde, Azure Backup sunucusu yükleme dosyalarını indirmek üzere bir Web sayfası açmak için **İndir** ' i seçin.

    ![Başlarken Sihirbazı değişikliği](./media/backup-mabs-install-azure-stack/prepare-infrastructure.png)

    Azure Backup Sunucusu için indirilebilir dosyaları barındıran Microsoft Web sayfası açılır.

8. Microsoft Azure Backup sunucusu indirme sayfasında bir dil seçin ve **İndir**' i seçin.

    ![İndirme Merkezi açılır](./media/backup-mabs-install-azure-stack/mabs-download-center-page.png)

9. Azure Backup Sunucusu yükleyicisi sekiz dosyadan oluşur-bir yükleyici ve yedi. bin dosyası. Tüm gerekli dosyaları seçmek için **dosya adını** denetleyin ve **İleri ' yi** seçin. Tüm dosyaları aynı klasöre indirin.

    ![İndirme Merkezi, seçili dosyalar](./media/backup-mabs-install-azure-stack/download-center-selected-files.png)

    Tüm yükleme dosyalarının karşıdan yükleme boyutu 3 GB 'den büyük. 10 Mbps bir indirme bağlantısında, tüm yükleme dosyalarını indirmek 60 dakikaya kadar sürebilir. Dosyalar, belirtilen indirme konumunuza indirilir.

## <a name="extract-azure-backup-server-install-files"></a>Dosyaları ayıkla Azure Backup Sunucusu yüklemesi

Tüm dosyaları Azure Stack sanal makinenize indirdikten sonra, indirme konumuna gidin. Azure Backup Sunucusu yüklemenin ilk aşaması, dosyaları ayıklamanız.

![MABS yükleyicisini indir](./media/backup-mabs-install-azure-stack/download-mabs-installer.png)

1. Yüklemeyi başlatmak için indirilen dosyalar listesinden **MicrosoftAzureBackupserverInstaller.exe**' yi seçin.

    > [!WARNING]
    > Kurulum dosyalarını ayıklamak için en az 4 GB boş alan gerekir.
    >

2. Azure Backup Sunucusu sihirbazında, devam etmek için **İleri** ' yi seçin.

    ![Microsoft Azure Backup Kurulum Sihirbazı](./media/backup-mabs-install-azure-stack/mabs-install-wiz-1.png)

3. Azure Backup Sunucusu dosyaları için yolu seçin ve **İleri**' yi seçin.

   ![Dosyalar için hedef seçin](./media/backup-mabs-install-azure-stack/mabs-install-wizard-select-destination-1.png)

4. Ayıklama konumunu doğrulayın ve **Ayıkla**' yı seçin.

   ![Ayıklama konumunu doğrula](./media/backup-mabs-install-azure-stack/mabs-install-wizard-extract-2.png)

5. Sihirbaz dosyaları ayıklar ve yükleme işlemini yeniden işler.

   ![Sihirbaz dosyaları ayıklar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-install-3.png)

6. Ayıklama işlemi tamamlandıktan sonra **son**' u seçin. Varsayılan olarak, **setup.exeÇalıştır** seçilidir. **Son**' u seçtiğinizde, Setup.exe Microsoft Azure Backup sunucusunu belirtilen konuma yükleyecek.

   ![Kurulum Microsoft Azure Backup sunucusu dosyalarını ayıklar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-finish-4.png)

## <a name="install-the-software-package"></a>Yazılım paketini yükler

Önceki adımda, ayıklama aşamasından çıkmak için **son** ' u seçin ve Azure Backup sunucusu Kurulum Sihirbazı ' nı başlatın.

![Microsoft Azure Backup Kurulum Sihirbazı başlar](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5.png)

Azure Backup Sunucusu kodu Data Protection Manager paylaşır. Azure Backup Sunucusu yükleyicisindeki Data Protection Manager ve DPM 'ye yönelik başvuruları görürsünüz. Azure Backup Sunucusu ve Data Protection Manager ayrı ürünlerdir, ancak bu ürünler yakından ilgilidir.

1. Kurulum sihirbazını başlatmak için **Microsoft Azure Backup Server**' ı seçin.

   ![Microsoft Azure Backup sunucusu seçin](./media/backup-mabs-install-azure-stack/mabs-install-wizard-local-5b.png)

2. **Hoş geldiniz** ekranında **İleri**' yi seçin.

    ![Azure Backup Sunucusu-hoş geldiniz](./media/backup-mabs-install-azure-stack/mabs-install-wizard-setup-6.png)

3. **Önkoşul denetimleri** ekranında, Azure Backup sunucusu için donanım ve yazılım önkoşullarının karşılanıp karşılanmadığını belirlemek için **Denetle** ' yi seçin.

    ![Azure Backup Sunucusu-önkoşul denetimi](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-7.png)

    Ortamınız gerekli önkoşullara sahipse, makinenin gereksinimleri karşıladığını belirten bir ileti görürsünüz. **İleri**’yi seçin.  

    ![Azure Backup Sunucusu-önkoşul denetimi başarılı](./media/backup-mabs-install-azure-stack/mabs-install-wizard-pre-check-passed-8.png)

    Ortamınız gerekli önkoşulları karşılamıyorsa, sorunlar belirtilecektir. Karşılanmayan Önkoşullar da DpmSetup. log dosyasında listelenir. Önkoşul hatalarını çözümleyin ve sonra **yeniden denetle**' yi çalıştırın. Tüm Önkoşullar karşılanana kadar yükleme devam edemiyor.

    ![Azure Backup Sunucusu yükleme önkoşulları karşılanmadı](./media/backup-mabs-install-azure-stack/installation-errors.png)

4. Microsoft Azure Backup sunucu SQL Server gerektiriyor. Azure Backup Sunucusu yükleme paketi, uygun SQL Server ikilileriyle paketlenmiş olarak sunulur. Kendi SQL yüklemenizi kullanmak istiyorsanız, bunu yapabilirsiniz. Ancak önerilen seçim, yükleyicinin SQL Server yeni bir örneğini eklemesine izin verir. Seçtiğiniz ortamınızda çalıştığından emin olmak için **Denetle ve yüklensin**' i seçin.

   > [!NOTE]
   > Azure Backup Sunucusu, uzak bir SQL Server örneğiyle çalışmaz. Azure Backup Sunucusu tarafından kullanılan örnek yerel olmalıdır.
   >

    ![Azure Backup Sunucusu-SQL ayarları](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-install-9.png)

    Denetimi yaptıktan sonra, sanal makinede Azure Backup Sunucusu yüklemek için gerekli önkoşullar varsa, **İleri**' yi seçin.

    ![Azure Backup Sunucusu gereksinimleri karşılandı](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-ready-10.png)

    Makineyi yeniden başlatma önerisiyle bir hata oluşursa makineyi yeniden başlatın. Makineyi yeniden başlattıktan sonra yükleyiciyi yeniden başlatın ve **SQL ayarları** ekranına geldiğinizde **yeniden denetle**' yi seçin.

5. **Yükleme ayarları**' nda, Microsoft Azure Backup sunucusu dosyalarının yüklenmesi için bir konum belirtin ve **İleri**' yi seçin.

    ![Dosyaların yüklenmesi için konum belirtin](./media/backup-mabs-install-azure-stack/mabs-install-wizard-settings-11.png)

    Azure 'a yedeklemek için karalama konumu gereklidir. Karalama konumunun boyutunun Azure 'a yedeklenmek üzere planlandığı verilerin en az %5 ' ine eşit olduğundan emin olun. Disk koruması için, yükleme tamamlandıktan sonra ayrı disklerin yapılandırılması gerekir. Depolama havuzları hakkında daha fazla bilgi için bkz. [veri depolamayı hazırlama](/system-center/dpm/plan-long-and-short-term-data-storage).

6. **Güvenlik ayarları** ekranında, kısıtlı yerel kullanıcı hesapları için güçlü bir parola girin ve **İleri ' yi** seçin.

    ![Güvenlik ayarları ekranı](./media/backup-mabs-install-azure-stack/mabs-install-wizard-security-12.png)

7. **Microsoft Update katılım** ekranında, güncelleştirmeleri denetlemek için *Microsoft Update* kullanmak Isteyip istemediğinizi seçin ve **İleri**' yi seçin.

   > [!NOTE]
   > Windows ve Microsoft Azure Backup Server gibi diğer ürünler için güvenlik ve önemli güncelleştirmeler sunan Microsoft Update Windows Update yeniden yönlendirmenizi öneririz.
   >

    ![Microsoft Update Opt-In ekran](./media/backup-mabs-install-azure-stack/mabs-install-wizard-update-13.png)

8. *Ayarların özetini* gözden geçirin ve **yüklemeyi** seçin.

    ![Ayarların Özeti](./media/backup-mabs-install-azure-stack/mabs-install-wizard-summary-14.png)

    Azure Backup Sunucusu yüklemeyi bitirdiğinde, yükleyici Microsoft Azure Kurtarma Hizmetleri Aracı yükleyicisini hemen başlatır.

9. Microsoft Azure Kurtarma Hizmetleri Aracısı yükleyicisi açılır ve Internet bağlantısını denetler. Internet bağlantısı varsa, yükleme işlemine devam edin. Bağlantı yoksa, Internet 'e bağlanmak için proxy ayrıntılarını sağlayın. Proxy ayarlarınızı belirledikten sonra **İleri**' yi seçin.

    ![Ara sunucu yapılandırması](./media/backup-mabs-install-azure-stack/mabs-install-wizard-proxy-15.png)

10. Microsoft Azure Kurtarma Hizmetleri Aracısı 'nı yüklemek için, **yüklensin**' i seçin.

    ![Aracı yüklemesi](./media/backup-mabs-install-azure-stack/mabs-install-wizard-mars-agent-16.png)

    Azure Backup Aracısı olarak da bilinen Microsoft Azure Kurtarma Hizmetleri Aracısı, kurtarma hizmetleri kasasıyla Azure Backup Sunucusu yapılandırır. Azure Backup Sunucusu yapılandırıldıktan sonra, verileri her zaman aynı kurtarma hizmetleri kasasında yedekler.

11. Microsoft Azure Kurtarma Hizmetleri Aracısı yüklemeyi tamamladığında sonraki aşamayı başlatmak için **İleri** ' yi seçin: kurtarma hizmetleri kasasıyla Azure Backup sunucusu kaydetme.

    ![Aracı yüklemesi başarıyla tamamlandı](./media/backup-mabs-install-azure-stack/mabs-install-wizard-complete-16.png)

    Yükleyici, **sunucu kaydetme Sihirbazı 'nı** başlatır.

12. Azure aboneliğinize ve kurtarma hizmetleri kasanıza geçiş yapın. **Altyapıyı hazırla** menüsünde, kasa kimlik bilgilerini Indirmek için **İndir** ' i seçin. 2. adımdaki **İndir** düğmesi etkin değilse, düğmeyi etkinleştirmek için **zaten indirilmiş ' i veya en son Azure Backup sunucusu yüklemesini** kullanın. Kasa kimlik bilgileri, İndirmeleri depoladığınız konuma indirilir. Bir sonraki adımda ihtiyaç duyacağınız için bu konumu unutmayın.

    ![Kasa kimlik bilgilerini indirme](./media/backup-mabs-install-azure-stack/download-mars-credentials-17.png)

13. **Kasa kimliği** menüsünde, kurtarma hizmetleri Kasası kimlik bilgilerini bulmak için **Araştır** ' ı seçin.

    ![Kasa tanımlama menüsü](./media/backup-mabs-install-azure-stack/mabs-install-wizard-vault-id-18.png)

    **Kasa kimlik bilgilerini seçin** iletişim kutusunda, indirme konumuna gidin, kasa kimlik bilgilerinizi seçin ve **Aç**' ı seçin.

    Kimlik bilgilerinin yolu, kasa kimliği menüsünde görüntülenir. **Şifreleme ayarlarına** Ilerlemek için **İleri ' yi** seçin.

14. **Şifreleme ayarı** iletişim kutusunda, yedekleme şifrelemesi için bir parola ve parolayı depolamak için bir konum belirtin ve **İleri**' yi seçin.

    ![Şifreleme ayarları](./media/backup-mabs-install-azure-stack/mabs-install-wizard-encryption-19.png)

    Kendi parolanızı sağlayabilir veya sizin için bir tane oluşturmak üzere parola oluşturucusunu kullanabilirsiniz. Parola sizindir ve Microsoft bu parolayı kaydetmez veya yönetmez. Bir olağanüstü duruma hazırlanmak için parolanızı erişilebilir bir konuma kaydedin.

    **İleri**' yi seçtiğinizde, Azure Backup sunucusu kurtarma hizmetleri kasasına kaydedilir. Yükleyici SQL Server ve Azure Backup Sunucusu yüklemeye devam eder.

    ![Kurulum, SQL ve Azure Backup Sunucusu 'yi yüklüyor](./media/backup-mabs-install-azure-stack/mabs-install-wizard-sql-still-installing-20.png)

15. Yükleyici tamamlandığında, **durum** tüm yazılımın başarıyla yüklendiğini gösterir.

    ![Yazılım başarıyla yüklendi](./media/backup-mabs-install-azure-stack/mabs-install-wizard-done-22.png)

    Yükleme tamamlandığında, sunucu masaüstünde Azure Backup Sunucusu konsolu ve Azure Backup Sunucusu PowerShell simgeleri oluşturulur.

## <a name="add-backup-storage"></a>Yedekleme depolama alanı ekleme

İlk yedekleme kopyası Azure Backup Sunucusu makineye bağlı depolamada tutulur. Disk ekleme hakkında daha fazla bilgi için bkz. [modern yedekleme depolaması ekleme](/system-center/dpm/add-storage).

> [!NOTE]
> Verileri Azure 'a göndermek için planlıyor olsanız bile yedekleme depolama alanı eklemeniz gerekir. Azure Backup Sunucusu mimarisinde, kurtarma hizmetleri Kasası verilerin *ikinci* kopyasını tutar, ancak yerel depolama ise ilk (ve zorunlu) yedek kopyayı tutar.
>
>

## <a name="network-connectivity"></a>Ağ bağlantısı

Azure Backup Sunucusu, ürünün başarıyla çalışması için Azure Backup hizmetine bağlantı kurulmasını gerektirir. Makinenin Azure bağlantısına sahip olup olmadığını doğrulamak için ```Get-DPMCloudConnection``` Azure Backup sunucusu PowerShell konsolundaki cmdlet 'ini kullanın. Cmdlet 'in çıktısı TRUE ise, bağlantı var, aksi takdirde bağlantı yok.

Aynı zamanda Azure aboneliğinin sağlıklı bir durumda olması gerekir. Aboneliğinizin durumunu öğrenmek ve yönetmek için [abonelik portalında](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)oturum açın.

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


Azure 'a bağlantı Azure Backup Sunucusu geri yüklendikten sonra, Azure abonelik durumu gerçekleştirilebileceği işlemleri belirler. Sunucu **bağlandıktan** sonra, kullanılabilir işlemleri görmek için [ağ bağlantısı](backup-mabs-install-azure-stack.md#network-connectivity) 'ndaki tabloyu kullanın.

### <a name="handling-subscription-states"></a>Abonelik durumlarını işleme

Bir Azure aboneliğini, *zaman aşımına uğradı* veya *sağlaması kaldırılmış* durumundan *etkin* duruma değiştirmek mümkündür. Abonelik durumu *etkin* değil:

- Abonelik *sağlaması kaldırılırken, işlevselliği* kaybeder. Aboneliği *etkin* olarak geri yükleme, yedekleme/geri yükleme işlevini yeniden canlandırın. Yerel diskteki yedekleme verileri yeterince büyük bir bekletme süresiyle korunursa, bu yedekleme verileri alınabilir. Ancak, abonelik *sağlanan* duruma girdiğinde Azure 'daki yedekleme verileri irretrievably kaybolur.
- Aboneliğin süre *dolduğunda* işlevselliği kaybeder. Zamanlanan yedeklemeler, aboneliğin süre *dolduğunda* çalışmaz.

## <a name="troubleshooting"></a>Sorun giderme

Microsoft Azure Backup sunucu kurulum aşaması (veya yedekleme ya da geri yükleme) sırasında hata vererek başarısız olursa, [hata kodları belgesine](https://support.microsoft.com/kb/3041338)bakın.
Ayrıca, [Ilgili sss Azure Backup](backup-azure-backup-faq.yml) de başvurabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

[ORTAMıNıZı DPM Için hazırlama](/system-center/dpm/prepare-environment-for-dpm)makalesinde desteklenen Azure Backup sunucusu yapılandırmalarına ilişkin bilgiler yer alır.

Microsoft Azure Backup sunucusu kullanarak iş yükü korumasını daha ayrıntılı olarak anlamak için aşağıdaki makaleleri kullanabilirsiniz.

- [SQL Server yedekleme](./backup-mabs-sql-azure-stack.md)
- [SharePoint Server yedekleme](./backup-mabs-sharepoint-azure-stack.md)
- [Alternatif sunucu yedeklemesi](backup-azure-alternate-dpm-server.md)
