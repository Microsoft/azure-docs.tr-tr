---
title: Azure VMware çözümü için Azure Backup Sunucusu ayarlama
description: Azure Backup Sunucusu kullanarak sanal makineleri yedeklemek için Azure VMware Çözüm ortamınızı ayarlayın.
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: e9204b9f86c7e9ef67d2e3d6b45ccf4248d00b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581491"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Azure VMware çözümü için Azure Backup Sunucusu ayarlama

Azure Backup Sunucusu iş sürekliliği ve olağanüstü durum kurtarma (BCDR) stratejinize katkıda bulunur. Azure VMware çözümü ile yalnızca Azure Backup Sunucusu kullanarak bir sanal makine (VM) düzeyinde bir yedekleme yapılandırabilirsiniz. 

Azure Backup Sunucusu, yedekleme verilerini şu şekilde saklayabilir:

- **Disk**: kısa vadeli depolama için, verileri disk havuzlarına Yedekler Azure Backup sunucusu.
- **Azure**: hem kısa vadeli hem de uzun vadeli depolama için şirket içi Azure Backup sunucusu, disk havuzlarında depolanan verileri Azure Backup kullanılarak Microsoft Azure buluta yedeklenebilir.

Verileri kaynağa veya alternatif bir konuma geri yüklemek için Azure Backup Sunucusu kullanın. Bu şekilde, planlanan veya beklenmeyen sorunlar nedeniyle özgün veriler kullanılamıyorsa, verileri alternatif bir konuma geri yükleyebilirsiniz.

Bu makale, Azure Backup Sunucusu kullanarak VM 'Leri yedeklemek için Azure VMware Çözüm ortamınızı hazırlamanıza yardımcı olur. Şu adımlarda size kılavuzluk ederiz: 

> [!div class="checklist"]
> * Önerilen VM disk türünü ve kullanılacak boyutu belirleme.
> * Kurtarma noktalarını depolayan bir kurtarma hizmetleri Kasası oluşturun.
> * Kurtarma Hizmetleri Kasası için depolama çoğaltmasını ayarlayın.
> * Azure Backup Sunucusu depolama ekleyin.

## <a name="supported-vmware-features"></a>Desteklenen VMware özellikleri

- **Aracısız yedekleme:** Azure Backup Sunucusu, VM 'yi yedeklemek için vCenter veya ESXi sunucusuna bir aracının yüklenmesini gerektirmez. Bunun yerine, Azure Backup Sunucusu ile VMware sunucusunun kimliğini doğrulamak için kullanılan IP adresini veya tam etki alanı adını (FQDN) ve oturum açma kimlik bilgilerini sağlamanız yeterlidir.
- **Bulutta tümleşik yedekleme:** Azure Backup Sunucusu, iş yüklerini disk ve buluta korur. Azure Backup Sunucusu yedekleme ve kurtarma iş akışı, uzun süreli saklama ve şirket dışı yedekleme yönetmenize yardımcı olur.
- **VCenter tarafından yönetilen VM 'Leri Algıla ve koru:** Azure Backup Sunucusu vCenter veya ESXi sunucusunda dağıtılan VM 'Leri algılar ve korur. Azure Backup Sunucusu, büyük dağıtımları koruyabilmeniz için vCenter tarafından yönetilen VM 'Leri de algılar.
- **Klasör düzeyinde otomatik koruma:** vCenter, VM 'lerinizi VM klasörlerinde düzenlemenizi sağlar. Azure Backup Sunucusu bu klasörleri algılar. Tüm alt klasörleri dahil olmak üzere, klasör düzeyinde VM 'Leri korumak için kullanabilirsiniz. Klasörler korunurken, Azure Backup Sunucusu bu klasördeki VM 'Leri korur ve daha sonra eklenen VM 'Leri korur. Azure Backup Sunucusu, her gün yeni VM 'Leri algılar ve bunları otomatik olarak koruyabilirsiniz. Sanal makinelerinizi özyinelemeli klasörlerde düzenlediğinizde, Azure Backup Sunucusu özyinelemeli klasörlerde dağıtılan yeni VM 'Leri otomatik olarak algılar ve korur.
- **Azure Backup sunucusu küme Içindeki sanal makineleri korumaya devam eder:** VM 'Ler küme içinde yük dengeleme için Vmoas olarak Azure Backup Sunucusu, VM korumasını otomatik olarak algılar ve devam ettirir.
- **Gerekli dosyaları daha hızlı kurtarın:** Azure Backup Sunucusu, tüm VM 'leri kurtarmadan bir Windows sanal makinesi 'nden dosya veya klasör kurtarabilir.

## <a name="limitations"></a>Sınırlamalar

- Azure Backup Sunucusu v3 için güncelleştirme paketi 1 ' in yüklü olması gerekir.
- İlk Azure Backup Sunucusu yedeklemeden önce Kullanıcı anlık görüntülerini yedekleyemiyoruz. Azure Backup Sunucusu ilk yedeklemeyi tamamladıktan sonra, Kullanıcı anlık görüntülerini yedekleyebilirsiniz.
- Azure Backup Sunucusu, geçişli disklerle ve fiziksel ham cihaz eşlemeleriyle (pRDMs) VMware VM 'lerini koruyamaz.
- Azure Backup Sunucusu, VMware vApps 'i algılayamaz veya koruyamaz.

Azure VMware çözümü için Azure Backup Sunucusu ayarlamak için aşağıdaki adımları izlemeniz gerekir:

- Önkoşulları ve ortamı ayarlayın.
- Kurtarma Hizmetleri kasası oluşturun.
- Azure Backup Sunucusu indirin ve yükleyin.
- Azure Backup Sunucusu depolama ekleyin.

### <a name="deployment-architecture"></a>Dağıtım mimarisi

Azure Backup Sunucusu, Azure VMware Çözüm VM 'lerini korumak için bir Azure hizmet olarak altyapı (IaaS) VM 'si olarak dağıtılır.

:::image type="content" source="media/azure-vmware-solution-backup/deploy-backup-server-azure-vmware-solution-diagram.png" alt-text="Azure Backup Sunucusu, Azure VMware Çözüm VM 'lerini korumak için bir Azure hizmet olarak altyapı (IaaS) VM 'si olarak dağıtılır." border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Azure Backup Sunucusu ortamı önkoşulları

Azure ortamınızda Azure Backup Sunucusu yüklerken bu bölümdeki önerileri göz önünde bulundurun.

### <a name="azure-virtual-network"></a>Azure Sanal Ağ

[Azure 'Da VMware özel bulutunuz için ağ iletişimini yapılandırmadiğinizden](tutorial-configure-networking.md)emin olun.

### <a name="determine-the-size-of-the-vm"></a>VM'nin boyutunu belirleme

Azure portal öğreticisindeki [Ilk WINDOWS VM 'Nizi oluşturma](../virtual-machines/windows/quick-create-portal.md) bölümündeki yönergeleri izleyin.  VM 'yi, önceki adımda oluşturduğunuz sanal ağda oluşturacaksınız. Azure Backup Sunucusu çalıştırmak için Windows Server 2019 Datacenter 'un bir galeri görüntüsü ile başlayın. 

Tablo, her bir Azure Backup Sunucusu VM boyutu için en fazla korunan iş yükü sayısını özetler. Bilgiler, iç performansa ve iş yükü boyutu ile karmaşıklık için kurallı değerlere dayanır. Gerçek iş yükü boyutu daha büyük olabilir, ancak Azure Backup Sunucusu sanal makinesine bağlı diskler tarafından konamelidir.

| En fazla korumalı iş yükü | Ortalama iş yükü boyutu | Ortalama iş yükü karmaşıklığı (günlük) | Minimum depolama ıOPS 'si | Önerilen disk türü/boyutu      | Önerilen VM boyutu |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Net %5 karmaşıklık                   | 2.000             | Standart HDD (disk başına 8 TB veya üzeri boyut)  | A4V2       |
| 40                      | 150 GB                | Net %10 karmaşıklık                  | 4.500             | Premium SSD * (disk başına 1 TB veya üzeri boyut) | DS3_V2     |
| 60                      | 200 GB                | Net %10 karmaşıklık                  | 10.500            | Premium SSD * (disk başına 8 TB veya üzeri boyut) | DS3_V2     |

* Gerekli IOPS 'yi almak için önerilen en düşük veya daha yüksek boyutlu diskleri kullanın. Küçük boyutlu diskler daha düşük IOPS sağlar.

> [!NOTE]
> Azure Backup Sunucusu, özel, tek amaçlı bir sunucuda çalışmak üzere tasarlanmıştır. Azure Backup Sunucusu şu şekilde bir bilgisayara yükleyemezsiniz:
> * Bir etki alanı denetleyicisi olarak çalışır.
> * Uygulama sunucusu rolünü yüklemiştir.
> * Bir System Center Operations Manager yönetim sunucusudur.
> * Exchange Server çalıştırır.
> * , Bir kümenin düğümüdür.

### <a name="disks-and-storage"></a>Diskler ve depolama alanı

Azure Backup Sunucusu yükleme için disk gerektirir. 

| Gereksinim                      | Önerilen boyut  |
|----------------------------------|-------------------------|
| Azure Backup Sunucusu yükleme                | Yükleme konumu: 3 GB<br />Veritabanı dosyaları sürücüsü: 900 MB<br />Sistem sürücüsü: SQL Server yüklemesi için 1 GB<br /><br />Ayrıca, arşivleme sırasında dosya kataloğunu geçici bir yükleme konumuna kopyalamak için Azure Backup Sunucusu alana ihtiyacınız vardır.      |
| Depolama havuzu için disk<br />(Temel birimleri kullanır, dinamik bir diskte olamaz) | Korunan veri boyutunun iki ile üç katı.<br />Ayrıntılı depolama hesaplaması için bkz. [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

Mevcut bir Azure VM 'ye yeni bir yönetilen veri diski iliştirme hakkında bilgi edinmek için, bkz. [Azure Portal kullanarak bir WINDOWS VM 'ye yönetilen veri diski iliştirme](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Tek bir Azure Backup Sunucusu, depolama havuzu için geçici 120 TB 'lik sınıra sahiptir.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Yedekleme verilerini yerel diskte ve Azure 'da depolayın

Yedekleme verilerinin Azure 'da depolanması, Azure Backup Sunucusu VM 'deki yedekleme altyapısını azaltır. İşletimsel kurtarma (yedekleme) için Azure Backup Sunucusu, yedekleme verilerini VM 'ye bağlı Azure disklerinde depolar. Diskler ve depolama alanı VM 'ye eklendikten sonra, depolamayı sizin için yönetir Azure Backup Sunucusu. Depolama miktarı, her bir Azure sanal makinesine bağlı disklerin sayısına ve boyutuna bağlıdır. Azure VM 'nin her boyutunun, eklenebilecek en fazla disk sayısı vardır. Örneğin, a2 dört diskdir, a3 sekiz diskdir ve A4 16 diskdir. Yeniden, disk boyutu ve sayısı toplam yedekleme depolama havuzu kapasitesini tespit.

> [!IMPORTANT]
> Beş günden uzun bir süre içinde Azure Backup Sunucusu bağlı disklerde işlemsel kurtarma *verilerini korumamalıdır* . Veriler beş günden eski olursa, bunu bir kurtarma hizmetleri kasasında saklayın.

Yedekleme verilerini Azure 'da depolamak için bir kurtarma hizmetleri Kasası oluşturun veya kullanın. Azure Backup Sunucusu iş yükünü yedeklemeye hazırlandığınızda, [Kurtarma Hizmetleri kasasını yapılandırırsınız](#create-a-recovery-services-vault). Yapılandırıldıktan sonra, bir çevrimiçi yedekleme işi her çalıştığında kasada bir kurtarma noktası oluşturulur. Her kurtarma hizmetleri Kasası en fazla 9.999 kurtarma noktası içerir. Oluşturulan kurtarma noktası sayısına ve ne kadar süreyle tutuldığına bağlı olarak, yedekleme verilerini birçok yıl boyunca tutabilirsiniz. Örneğin, aylık kurtarma noktaları oluşturabilir ve bunları beş yıl boyunca tutabilirsiniz.

> [!IMPORTANT]
> Yedekleme verilerini Azure 'a göndermenize veya yerel olarak tutmaya bakılmaksızın, Azure Backup Sunucusu kurtarma hizmetleri kasasıyla kaydetmeniz gerekir.

### <a name="scale-deployment"></a>Ölçek dağıtımı

Dağıtımınızı ölçeklendirmek istiyorsanız aşağıdaki seçeneklere sahip olursunuz:

- **Ölçeği** artırma: Azure Backup sunucusu VM 'Nin boyutunu BIR serinin DS3 serisine artırın ve yerel depolamayı arttırın.
- **Yük boşaltma verileri**: eski verileri Azure 'a gönderin ve Azure Backup sunucusu makineye bağlı depolamada yalnızca en yeni verileri tutun.
- **Ölçeği genişletme**: iş yüklerini korumak için daha fazla Azure Backup sunucusu makine ekleyin.

### <a name="net-framework"></a>.NET Framework

VM 'de .NET Framework 3,5 SP1 veya üzeri yüklü olmalıdır.

### <a name="join-a-domain"></a>Bir etki alanına katılma

Azure Backup Sunucusu VM 'nin bir etki alanına katılması gerekir. VM üzerinde yönetici ayrıcalıklarına sahip bir etki alanı kullanıcısının Azure Backup Sunucusu yüklemesi gerekir.

Azure VM 'de dağıtılan Azure Backup Sunucusu, Azure VMware çözümünde VM 'lerde iş yüklerini yedekleyebilir. Yedekleme işleminin etkinleştirilmesi için iş yüklerinin aynı etki alanında olması gerekir.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri Kasası, zaman içinde oluşturulan kurtarma noktalarını depolayan bir depolama varlıktır. Ayrıca, korunan öğelerle ilişkili yedekleme ilkeleri de içerir.

1. [Azure portalında](https://portal.azure.com/) aboneliğinizde oturum açın.

1. Sol taraftaki menüden **Tüm hizmetler**'i seçin.

   ![Sol taraftaki menüden Tüm hizmetler'i seçin.](../backup/media/backup-create-rs-vault/click-all-services.png)

1. **Tüm hizmetler** Iletişim kutusunda **Kurtarma Hizmetleri** ' ni girin ve listeden **Kurtarma Hizmetleri kasaları** ' nı seçin.

   ![Kurtarma Hizmetleri kasalarını girin ve seçin.](../backup/media/backup-create-rs-vault/all-services.png)

   Abonelikteki Kurtarma Hizmetleri kasalarının listesi görünür.

1. **Kurtarma Hizmetleri kasası** panosunda **Ekle**'yi seçin.

   ![Kurtarma Hizmetleri Kasası ekleyin.](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   **Kurtarma Hizmetleri kasası** iletişim kutusu açılır.

1. **Ad**, **abonelik**, **kaynak grubu** ve **konum** için değerler girin.

   ![Kurtarma Hizmetleri kasasını yapılandırın.](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Ad**: Kasayı tanımlamak için kolay bir ad girin. Adın Azure aboneliği için benzersiz olması gerekir. En az iki tane 50 karakterden oluşan bir ad belirtin. Ad bir harf ile başlamalıdır ve yalnızca harf, rakam ve kısa çizgi içerebilir.
   - **Abonelik**: Kullanılacak aboneliği seçin. Tek bir aboneliğiniz varsa yalnızca o seçenek görüntülenir. Hangi aboneliğin kullanılacağından emin değilseniz varsayılan (önerilen) aboneliği kullanın. Yalnızca iş veya okul hesabınızın birden çok Azure aboneliği ile ilişkili olması durumunda birden çok seçenek olur.
   - **Kaynak grubu**: Mevcut kaynak grubunu kullanın ya da yeni bir tane oluşturun. Aboneliğinizdeki kullanılabilir kaynak gruplarının listesini görmek için **Var olanı kullan**'ı seçip açılan listeden bir kaynak grubu seçin. Yeni bir kaynak grubu oluşturmak için **Yeni oluştur**'u seçip bir ad girin.
   - **Konum**: Kasa için coğrafi bölgeyi seçin. Azure VMware Çözüm sanal makinelerini korumaya yönelik bir kasa oluşturmak için kasanın Azure VMware Çözüm özel bulutuyla aynı bölgede olması *gerekir* .

1. Kurtarma Hizmetleri kasasını oluşturmaya hazırsanız **Oluştur**'u seçin.

   ![Kurtarma Hizmetleri kasasını oluşturun.](../backup/media/backup-create-rs-vault/click-create-button.png)

   Kurtarma Hizmetleri kasasının oluşturulması biraz zaman alabilir. Portalın sağ üst köşesindeki **Bildirimler** alanında durum bildirimlerini izleyin. Kasanızı oluşturduktan sonra kurtarma hizmetleri kasaları listesinde görünür. Kasanızı görmüyorsanız **Yenile**'yi seçin.

   ![Backup kasaları listesini yenileyin.](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Depolama çoğaltmasını ayarla

Depolama çoğaltma seçeneği, coğrafi olarak yedekli depolama (varsayılan) ve yerel olarak yedekli depolama arasında seçim yapmanızı sağlar. Coğrafi olarak yedekli depolama, Depolama hesabınızdaki verileri ikincil bir bölgeye kopyalar ve böylece verilerinizin dayanıklı olmasını sağlar. Yerel olarak yedekli depolama, dayanıklı olmayan bir ucuz seçeneğidir. Coğrafi olarak yedekli ve yerel olarak yedekli depolama seçenekleri hakkında daha fazla bilgi edinmek için bkz. [Azure depolama artıklığı](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Bir kurtarma hizmetleri Kasası için **yerel olarak yedekli/coğrafi olarak yedekli depolama çoğaltma türü** ayarını değiştirmek, kasadaki yedeklemeleri yapılandırmadan önce yapılmalıdır. Yedeklemeleri yapılandırdıktan sonra, bunu değiştirme seçeneği devre dışı bırakılır ve depolama çoğaltma türünü değiştiremezsiniz.

1. **Kurtarma Hizmetleri kasalarından** yeni kasayı seçin. 

1. **Ayarlar** bölümünde **Özellikler**’i seçin. **Yedekleme yapılandırması** altında **Güncelleştir**' i seçin.

1. Depolama çoğaltma türünü seçin ve **Kaydet**' i seçin.

## <a name="download-and-install-the-software-package"></a>Yazılım paketini indirme ve yükleme

Yazılım paketini indirmek, ayıklamak ve yüklemek için bu bölümdeki adımları izleyin.

### <a name="download-the-software-package"></a>Yazılım paketini indirin

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. Zaten açık bir kurtarma hizmetleri Kasası varsa, bir sonraki adımla devam edin. Açık bir kurtarma hizmetleri Kasası yoksa ve Azure portal, ana menüden, **Araştır**' ı seçin.

   1. Kaynak listesinde **Kurtarma Hizmetleri**' ni girin.

   1. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Kurtarma Hizmetleri kasalarını** gördüğünüzde, bunu seçin.

   ![Kurtarma Hizmetleri Kasası oluşturma adımı 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Kurtarma Hizmetleri kasalarının listesinden bir kasa seçin.

   Seçilen kasa panosu açılır.

   ![Seçilen kasa panosu açılır.](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   **Ayarlar** seçeneği varsayılan olarak açılır. Kapalıysa, açmak için **Ayarlar** ' ı seçin.

   ![Ayarlar seçeneği varsayılan olarak açılır. Kapalıysa, açmak için ayarlar ' ı seçin.](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. **Başlangıç Sihirbazı 'nı** açmak için **Yedekle** ' yi seçin.

   ![Başlangıç Sihirbazı 'nı açmak için yedekle ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Açılan pencerede:

   1. **İş yükünüz nerede çalışıyor?** menüsünde **Şirket içi**' ı seçin.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-on-premises-workload.png" alt-text="İş yükünüz nerede çalışıyor?":::

   1. **Neleri yedeklemek istiyorsunuz?** menüsünde, Azure Backup sunucusu kullanarak korumak istediğiniz iş yüklerini seçin.

   1. Azure Backup Sunucusu ve kasa kimlik bilgilerini indirmek ve yüklemek için **altyapıyı hazırla** ' yı seçin.

      :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Altyapıyı hazırla":::

1. Açılan **altyapıyı hazırla** penceresinde:

   1. Azure Backup Sunucusu yüklemek için **indirme** bağlantısını seçin.

   1. Zaten indirilmiş ' i seçin **veya en son Azure Backup sunucusu yüklemesini kullanarak** kasa kimlik bilgilerini Indirmek için **indirin** . Kurtarma Hizmetleri kasasına Azure Backup Sunucusu kaydettiğinizde bu kimlik bilgilerini kullanacaksınız. Bağlantılar sizi, yazılım paketini indirdiğiniz Indirme merkezi 'ne götürür.

   :::image type="content" source="media/azure-vmware-solution-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Altyapıyı hazırla-Azure Backup Sunucusu":::

1. İndirme sayfasında, tüm dosyalar ' ı seçin ve **İleri**' yi seçin.

   > [!NOTE]
   > Tüm dosyaları aynı klasöre indirmeniz gerekir. Dosyaların karşıdan yükleme boyutu 3 GB 'tan büyük olduğundan, indirmenin tamamlanması 60 dakika sürebilir. 

   ![İndirme sayfasında, tüm dosyalar ' ı seçin ve Ileri ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Yazılım paketini Ayıkla

Yazılım paketini farklı bir sunucuya indirdiyseniz, dosyaları dağıtmak için oluşturduğunuz sanal makineye kopyalayın Azure Backup Sunucusu.

> [!WARNING]
> Kurulum dosyalarını ayıklamak için en az 4 GB boş alan gerekir.

1. Tüm dosyaları indirdikten sonra, **Microsoft Azure Backup** Kurulum Sihirbazı 'nı açmak için **MicrosoftAzureBackupInstaller.exe** çift tıklayın ve ardından **İleri**' yi seçin.

1. Dosyaların ayıklanacağı konumu seçin ve **İleri ' yi** seçin.

1. Ayıklama işlemini başlatmak için **Ayıkla** ' yı seçin.

   ![Ayıklama işlemini başlatmak için Ayıkla ' yı seçin.](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Ayıklandıktan sonra **setup.exeyürütme** seçeneğini belirleyip **son**' u seçin.

> [!TIP]
> setup.exe dosyasını, yazılım paketini ayıkladığınız klasörden de bulabilirsiniz.

### <a name="install-the-software-package"></a>Yazılım paketini yükler

1. **Yükleme** altındaki kurulum penceresinde, Kurulum Sihirbazı 'nı açmak için **Microsoft Azure Backup** ' yi seçin.

   ![Yükleme altındaki kurulum penceresinde, Kurulum Sihirbazı 'nı açmak için Microsoft Azure Backup ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. **Karşılama** ekranında, **önkoşul denetimleri** sayfasına devam etmek için **İleri** ' yi seçin.

1. Donanım ve yazılımın Azure Backup Sunucusu önkoşullarını karşılayıp karşılamadığını belirlemek için **yeniden denetle** ' yi seçin. Başarıyla karşılanırsa, **İleri**' yi seçin.

   ![ Donanım ve yazılımın Azure Backup Sunucusu önkoşullarını karşılayıp karşılamadığını belirlemek için yeniden denetle ' yi seçin. Başarıyla karşılanırsa, Ileri ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. Azure Backup Sunucusu yükleme paketi, gerekli olan uygun SQL Server ikilileriyle birlikte sunulur. Yeni bir Azure Backup Sunucusu yüklemesi başlattığınızda, **Bu kurulum ile yeni SQL Server örneğini yükleme** seçeneğini belirleyin. Sonra **Denetle ve yüklensin '** i seçin.

   ![Azure Backup Sunucusu yükleme paketi, gerekli olan uygun SQL Server ikilileriyle birlikte sunulur.](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Kendi SQL Server örneğinizi kullanmak istiyorsanız desteklenen SQL Server sürümleri SQL Server 2014 SP1 veya üzeri, 2016 ve 2017. Tüm SQL Server sürümleri Standard veya Enterprise 64-bit olmalıdır. Azure Backup Sunucusu tarafından kullanılan örnek yalnızca yerel olmalıdır; uzak olamaz. Azure Backup Sunucusu için mevcut bir SQL Server örneğini kullanıyorsanız, kurulum yalnızca SQL Server *adlandırılan örneklerin* kullanımını destekler.

   Makineyi yeniden başlatma önerisiyle ilgili bir hata oluşursa, bunu yapın ve **yeniden denetle**' yi seçin. SQL Server yapılandırma sorunları için, SQL Server SQL Server yönergelerine göre yeniden yapılandırın. Ardından Azure Backup Sunucusu SQL Server var olan örneğini kullanarak yüklemeyi veya yükseltmeyi yeniden deneyin.

   **El ile yapılandırma**

   Kendi SQL Server örneğinizi kullandığınızda ana veritabanının sysadmin rolüne sysadmin rolüne BUILTIN\Administrators eklediğinizden emin olun.

   **Raporlama Hizmetlerini SQL Server 2017 ile yapılandırma**

   SQL Server 2017 örneğinizi kullanıyorsanız, SQL Server 2017 Raporlama Hizmetleri 'ni (SSRS) el ile yapılandırmanız gerekir. SSRS yapılandırıldıktan sonra, SSRS 'nin **IsInitialized** özelliğini **true** olarak ayarladığınızdan emin olun. **True** olarak AYARLANDıĞıNDA Azure Backup sunucusu SSRS 'nin zaten yapılandırıldığını VARSAYAR ve SSRS yapılandırmasını atlar.

   SSRS yapılandırma durumunu denetlemek için şunu çalıştırın:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   SSRS yapılandırması için aşağıdaki değerleri kullanın:

   * **Hizmet hesabı**: **yerleşik hesap kullan** **ağ hizmeti** olmalıdır.
   * **Web hizmeti URL 'si**: **sanal dizin** **ReportServer_ \<SQLInstanceName>** olmalıdır.
   * **Veritabanı**: **DatabaseName** **ReportServer $ \<SQLInstanceName>** olmalıdır.
   * **Web portalı URL 'si**: **sanal dizin** **Reports_ \<SQLInstanceName>** olmalıdır.

   SSRS yapılandırması hakkında [daha fazla bilgi edinin](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) .

   > [!NOTE]
   > [Microsoft Online Services terms](https://www.microsoft.com/licensing/product-licensing/products) (OST), Azure Backup sunucusu veritabanı olarak kullanılan SQL Server lisansını yönetir. OST 'e göre yalnızca Azure Backup Sunucusu veritabanı olarak Azure Backup Sunucusu birlikte paketlenmiş SQL Server kullanın.

1. Yükleme başarılı olduktan sonra **İleri**' yi seçin.

1. Microsoft Azure Backup Server dosyalarını yüklemek için bir konum belirtin ve **İleri**' yi seçin.

   > [!NOTE]
   > Azure 'a yedekleme için karalama konumu gereklidir. Karalama konumunun, buluta yedeklemek için planlanmış verilerin en az %5 ' i olduğundan emin olun. Disk koruması için, yükleme bittikten sonra ayrı disklerin yapılandırılması gerekir. Depolama havuzları hakkında daha fazla bilgi için bkz. [depolama havuzlarını ve Disk depolamayı yapılandırma](/previous-versions/system-center/system-center-2012-r2/hh758075(v=sc.12)).

   ![Microsoft Azure Backup Server dosyalarının yüklenmesi için bir konum belirtin ve Ileri ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Kısıtlı yerel kullanıcı hesapları için güçlü bir parola sağlayın ve **İleri**' yi seçin.

   ![Kısıtlı yerel kullanıcı hesapları için güçlü bir parola sağlayın ve Ileri ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Güncelleştirmeleri denetlemek için Microsoft Update kullanmak isteyip istemediğinizi seçin ve **İleri**' yi seçin.

   > [!NOTE]
   > Windows ve Azure Backup Sunucusu gibi diğer ürünler için güvenlik ve önemli güncelleştirmeler sunan Microsoft Update Windows Update yeniden yönlendirmenizi öneririz.

   ![Güncelleştirmeleri denetlemek için Microsoft Update kullanmak isteyip istemediğinizi seçin ve Ileri ' yi seçin.](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. **Ayarların özetini** gözden geçirin ve **yükler**' i seçin.

   Yükleme aşamalarda gerçekleşir. 
   - İlk aşamada Microsoft Azure Kurtarma Hizmetleri Aracısı yüklenir.
   - İkinci aşama Internet bağlantısını denetler. Varsa, yükleme işlemine devam edebilirsiniz. Kullanılabilir değilse, internet 'e bağlanmak için proxy ayrıntılarını sağlamalısınız. 
   - Son aşama Önkoşul yazılımını denetler. Yüklü değilse, eksik yazılımlar Microsoft Azure Kurtarma Hizmetleri aracısıyla birlikte yüklenir.

1. Makineyi kurtarma hizmetleri kasasına kaydetmek için kasa kimlik bilgilerini bulmak üzere **Araştır** ' ı seçin ve ardından **İleri**' yi seçin.

1. Azure ile şirket içi arasında gönderilen verileri şifrelemek veya şifresini çözmek için bir parola seçin.

   > [!TIP]
   > Otomatik olarak bir parola oluşturabilir veya en düşük 16 karakterlik parolanızı sağlayabilirsiniz.

1. Parolayı kaydetmek için konumu girin ve ardından sunucuyu kaydetmek için **İleri** ' yi seçin.

   > [!IMPORTANT]
   > Parolayı yerel sunucu dışında bir güvenli konuma kaydedin. Parolayı depolamak için Azure Key Vault kullanmanızı kesinlikle öneririz.

   Microsoft Azure Kurtarma Hizmetleri Aracısı kurulumu bittikten sonra, yükleme adımı SQL Server ve Azure Backup Sunucusu bileşenleri için yükleme ve yapılandırmaya gider.

   ![Microsoft Azure Kurtarma Hizmetleri Aracısı kurulumu bittikten sonra, yükleme adımı SQL Server ve Azure Backup Sunucusu bileşenleri için yükleme ve yapılandırmaya gider.](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Yükleme adımı tamamlandıktan sonra **Kapat**' ı seçin.

### <a name="install-update-rollup-1"></a>Güncelleştirme paketi 1 ' i yükler

İş yüklerini koruyabilmeniz için Azure Backup Sunucusu v3 için güncelleştirme paketi 1 ' in yüklenmesi zorunludur.  Hata düzeltmelerini ve yükleme yönergelerini [Bilgi Bankası makalesinde](https://support.microsoft.com/en-us/help/4534062/)bulabilirsiniz.

## <a name="add-storage-to-azure-backup-server"></a>Azure Backup Sunucusu’na depolama alanı ekleme

Azure Backup Sunucusu v3 şunları sağlayan Modern Yedekleme Alanı destekler:

-  %50 depolama tasarrufu.
-  Üç kat daha hızlı yedeklemeler.
-  Daha verimli depolama.
-  İş yükü kullanan depolama.

### <a name="volumes-in-azure-backup-server"></a>Azure Backup Sunucusu birimler

Daha önce eklenmemişse, Azure Backup Sunucusu VM 'nin gerekli depolama kapasitesine sahip veri disklerini ekleyin.

Azure Backup Sunucusu v3 yalnızca depolama birimlerini kabul eder. Bir birim eklediğinizde Azure Backup Sunucusu, birimi Modern Yedekleme Alanı gereken dayanıklı dosya sistemi (ReFS) olarak biçimlendirir.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Azure Backup Sunucusu disk depolamaya birim ekleme

1. **Yönetim** bölmesinde, depolamayı yeniden tarayın ve **Ekle**' yi seçin. 

1. Depolama havuzuna eklemek için kullanılabilir birimler arasından seçim yapın. 

1. Kullanılabilir birimleri ekledikten sonra, bunları yönetmenize yardımcı olacak kolay bir ad verin. 

1. Azure Backup Sunucusu, Modern Yedekleme Alanı avantajları kullanabilmesi için bu birimleri ReFS olarak biçimlendirmek üzere **Tamam ' ı** seçin.


## <a name="next-steps"></a>Sonraki adımlar

Azure VMware çözümü için Azure Backup Sunucusu ayarlamayı artık kapsadığınıza göre, şunları öğrenmek isteyebilirsiniz:

- [Azure VMware Çözüm sanal makinelerinize yönelik yedeklemeleri yapılandırma](backup-azure-vmware-solution-virtual-machines.md).
- Azure [Güvenlik Merkezi tümleştirmesiyle Azure VMware Çözüm VM 'Lerinizi koruma](azure-security-integration.md).
