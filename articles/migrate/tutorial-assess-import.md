---
title: Azure geçişi sunucu değerlendirmesi ile içeri aktarılan sunucu verilerini kullanarak sunucuları değerlendirme
description: İçeri aktarılan verileri kullanarak Azure geçişi sunucu değerlendirmesi ile Azure 'a geçiş için şirket içi sunucuların nasıl değerlendirileneceğini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 7bf47731f2a3621e7bbdc1b104d94e97f2d03099
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158659"
---
# <a name="assess-servers-using-imported-data"></a>İçeri aktarılan verileri kullanarak sunucuları değerlendirme

Bu makalede, [Azure geçişi: Sunucu değerlendirmesi](migrate-services-overview.md#azure-migrate-server-assessment-tool)Ile, CSV kullanarak sunucu meta verilerini içeri aktararak şirket içi sunucuların nasıl değerlendirileneceği açıklanmaktadır. Bu değerlendirme yöntemiyle, değerlendirme oluşturmak için Azure geçişi gereci ayarlamanız gerekmez. Bu, şu durumlarda yararlı olur:

- Gereci dağıtmadan önce hızlı bir başlangıç değerlendirmesi oluşturmak istiyorsunuz.
- Azure geçişi gerecini kuruluşunuza dağıtamazsınız.
- Şirket içi sunuculara erişime izin veren kimlik bilgilerini paylaşamazsınız.
- Güvenlik kısıtlamaları, Gereç tarafından toplanan verileri Azure 'a toplamayı ve göndermeyi önler. İçeri aktarılan bir dosya ile, paylaştığınız verileri denetleyebilir ve çok fazla veri (örneğin, IP adresleri sağlamak) isteğe bağlıdır.


## <a name="before-you-start"></a>Başlamadan önce

Aşağıdakilere dikkat edin:

- Tek bir CSV dosyasında en fazla 20000 sunucu ekleyebilirsiniz.
- CSV kullanarak bir Azure geçişi projesine en fazla 20000 sunucu ekleyebilirsiniz.
- Azure sunucu değerlendirmesini geçirmek için CSV 'yi birden çok kez kullanarak sunucu bilgilerini karşıya yükleyebilirsiniz.
- Şirket içi ortamınızı geçiş için değerlendirirken uygulama bilgilerinin toplanması yararlı olsa da, Azure geçişi sunucu değerlendirmesi Şu anda uygulama düzeyi değerlendirmesi gerçekleştirmez ve şu durumlarda uygulamaları hesaba almaz değerlendirme oluşturma.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Bir Azure geçişi projesi ayarlayın.
> * Sunucu bilgileriyle bir CSV dosyası girin.
> * Azure geçişi sunucu değerlendirmesi 'ne sunucu bilgilerini eklemek için dosyayı içeri aktarın.
> * Bir değerlendirme oluşturun ve gözden geçirin.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, nasıl yapılır makalelerini gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="set-azure-permissions-for-azure-migrate"></a>Azure geçişi için Azure izinleri ayarlama

Azure hesabınız, Azure geçişi projesi oluşturmak için izinlere ihtiyaç duyuyor.

1. Azure portal aboneliğini açın ve **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda bulunan** veya **sahip** izinlerinizin olması gerekir.
    - Henüz ücretsiz bir Azure hesabı oluşturduysanız, aboneliğinizin sahibi olursunuz.
    - Abonelik sahibi değilseniz, rolü atamak için sahip ile çalışın.


## <a name="set-up-an-azure-migrate-project"></a>Azure geçişi projesi ayarlama

Aşağıdaki şekilde yeni bir Azure Geçişi projesi oluşturun.

1. Azure portalı > **Tüm hizmetler** bölümünde **Azure Geçişi**’ni arayın.
2. **Hizmetler** altında **Azure Geçişi**’ni seçin.
3. **Genel Bakış** bölümünde **Sunucuları bul, değerlendir ve geçiş** altında **Sunucuları değerlendir ve geçir** üzerine tıklayın.

    ![Sunucuları bulma ve değerlendirme](./media/tutorial-assess-import/assess-migrate.png)

4. **Başlarken** bölümünde **Araç ekle**’ye tıklayın.
5. **Projeyi geçir** bölümünde Azure aboneliğinizi seçin ve henüz yapmadıysanız bir kaynak grubu oluşturun.     
6. **Proje ayrıntıları**' nda proje adını ve projeyi oluşturmak istediğiniz coğrafi konumu belirtin.

    - Desteklenen coğrafi lıkları [gözden geçirin](migrate-support-matrix.md#supported-geographies) . Proje coğrafyası yalnızca şirket içi sanal makinelerden toplanan meta verileri depolamak için kullanılır.
    - Bir geçiş çalıştırdığınızda herhangi bir hedef bölgeyi seçebilirsiniz.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-import/migrate-project.png)


7. **İleri**’ye tıklayın.
8. **Değerlendirme Seç aracında** **Azure geçişi: Sunucu değerlendirmesi** > **İleri**' yi seçin.

    ![Azure geçişi projesi oluşturma](./media/tutorial-assess-import/assessment-tool.png)

9. **Geçiş aracını seç** bölümünde **Şimdilik geçiş aracı eklemeyi atla** > **İleri** seçeneğini belirleyin.
10. **İnceleme + araç ekleme** bölümünde ayarları gözden geçirip **Araç ekle**’ye tıklayın.
11. Azure Geçişi projesinin dağıtılması için birkaç dakika bekleyin. Proje sayfasına yönlendirilirsiniz. Projeyi görmüyorsanız Azure Geçişi panosundaki **Sunuculardan** erişebilirsiniz.


## <a name="prepare-the-csv"></a>CSV 'yi hazırlama

CSV şablonunu indirin ve buna sunucu bilgilerini ekleyin.


### <a name="download-the-template"></a>Şablonu indirme

1. **Azure geçişi: Sunucu değerlendirmesi** > **sunucuları** > **geçiş hedeflerde** **bul**' a tıklayın.
2. **Makine bul**' da, **kullanarak içeri aktar ' ı seçin. CSV**.
3. İndirmek için **İndir** ' e tıklayın. CSV şablonu. Alternatif olarak, [doğrudan indirebilirsiniz](https://go.microsoft.com/fwlink/?linkid=2108404).

    ![İndirme. CSV şablonu](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>Sunucu bilgileri ekleme

Sunucu verilerini toplayın ve CSV dosyasına ekleyin.

- Verileri toplamak için, VMware vSphere veya yapılandırma yönetimi veritabanınız (CMDB) gibi şirket içi sunucu yönetimi için kullandığınız araçlardan dışarı aktarabilirsiniz.
- Örnek verileri gözden geçirmek için [örnek dosyayı](https://go.microsoft.com/fwlink/?linkid=2108405)indirin.


Aşağıdaki tabloda, doldurulacak dosya alanları özetlenmektedir.

**Alan adı** | **Girilmesi** | **Ayrıntılar**
--- | --- | ---
**Sunucu adı** | Yes | FQDN 'yi belirtmeyi öneririz.
**IP adresi** | Hayır | Sunucu adresi.
**Çekirdek sayısı** | Yes | Sunucuya ayrılan işlemci çekirdeklerinin sayısı.
**Bellek** | Yes | Sunucuya ayrılan toplam RAM (MB).
**İşletim sistemi adı** | Yes | Sunucu işletim sistemi.
**İşletim sistemi sürümü** | Hayır | Sunucu işletim sistemi sürümü.
**Disk sayısı** | Hayır | Ayrı disk ayrıntıları sağlanmışsa gerekli değildir.
**Disk 1 boyutu**  | Hayır | Maksimum disk boyutu (GB)<br/> Şablona [sütun ekleyerek](#add-multiple-disks) daha fazla disk için ayrıntılar ekleyebilirsiniz. En fazla sekiz disk ekleyebilirsiniz.
**Disk 1 okuma Ops** | Hayır | Saniye başına disk okuma işlemi.
**Disk 1 yazma Ops** | Hayır | Saniye başına disk yazma işlemi.
**Disk 1 okuma aktarım hızı** | Hayır | Diskten saniyede MB/saniye cinsinden okunan veriler.
**Disk 1 yazma aktarım hızı** | Hayır | Saniye başına saniyede diske yazılan veriler (saniye başına MB).
**CPU kullanım yüzdesi** | Hayır | CPU kullanım yüzdesi.
**Bellek kullanım yüzdesi** | Hayır | RAM kullanım yüzdesi.
**Toplam disk okuma Ops** | Hayır | Saniye başına disk okuma işlemi.
**Toplam disk yazma Ops** | Hayır | Saniye başına disk yazma işlemi.
**Toplam disk okuma performansı** | Hayır | Diskten MB/saniye cinsinden okunan veriler.
**Toplam disk yazma performansı** | Hayır | Saniye başına MB cinsinden diske yazılan veriler.
**Ağa gelen üretilen iş** | Hayır | Sunucu tarafından saniyede MB cinsinden alınan veriler.
**Ağdan giden üretilen iş** | Hayır | Sunucu tarafından saniyede MB cinsinden aktarılan veriler.
**Bellenim türü** | Hayır | Sunucu üretici yazılımı. Değerler "BIOS" veya "UEFı" olabilir
**Sunucu türü** | Hayır | Değerler "fiziksel" veya "sanal" olabilir.
**Yöneticiye** | Hayır | Makinenin çalıştığı hiper yönetici. <br/> Değerler "VMware", "Hyper-V", "Xen", "AWS", "GCP" veya "Other" olabilir.
**Hiper yönetici sürüm numarası** | Hayır | Hiper yönetici sürümü.
**Sanal makine KIMLIĞI** | Hayır | VM tanımlayıcısı. Bu, VMware vCenter VM için **ınstanceuuıd** veya Hyper-v için **Hyper-v VM kimliği** 'dir.
**Virtual Machine Manager KIMLIĞI** | Hayır | Bu, VMWare vCenter için **ınstanceuuıd** 'dir. Hyper-V için gerekli değildir.
**MAC adresi**| Hayır | Sunucu MAC adresi.
**BıOS KIMLIĞI** | Hayır | Sunucu BIOS KIMLIĞI.
**Özel sunucu KIMLIĞI**| Hayır | Şirket içinde yerel benzersiz sunucu kimlikleri. <br/> İçeri aktarılan sunucuyu yerel KIMLIĞE göre izlemek için faydalıdır.
**Uygulama 1 adı** | Hayır | Sunucuda çalışan iş yüklerinin adı.<br/> Şablona [sütun ekleyerek](#add-multiple-applications) daha fazla uygulama için ayrıntılar ekleyebilirsiniz. En fazla beş uygulama ekleyebilirsiniz.
**Uygulama 1 türü** | Hayır | Sunucuda çalışan iş yükünün türü
**Uygulama 1 sürümü** | Hayır | Sunucuda çalışan iş yükünün sürümü.
**Uygulama 1 lisansı süre sonu** | Hayır | İş yükü için lisans bitiş tarihi (varsa).
**İş birimi** | Hayır | Sunucunun ait olduğu iş birimi.
**İşletme sahibi** | Hayır | İş birimi sahibi.
**İş uygulaması adı** | Hayır | Uygulamanın ait olduğu uygulamanın adı.
**Konum** | Hayır | Sunucunun bulunduğu veri merkezi.
**Sunucu yetkisini alma tarihi** | Hayır | Fiziksel sunucunun veya sanal sunucunun temel alınan fiziksel sunucusunun yetkisini alma

### <a name="add-operating-systems"></a>İşletim sistemleri ekleme

Değerlendirme, belirli işletim sistemi adlarını tanır. Belirttiğiniz herhangi bir işletim sistemi adı, [desteklenen adlar](#supported-operating-system-names) listesindeki seçeneklerden biriyle aynı olmalıdır.


### <a name="add-multiple-disks"></a>Birden çok disk Ekle

Şablon, ilk disk için varsayılan alanlar sağlar.  8 ' e kadar disk için benzer sütunlar ekleyebilirsiniz.

Örneğin, ikinci bir disk için tüm alanları belirtmek için şu sütunları ekleyin:

Disk 2 boyut disk 2 okuma Ops disk 2 yazma Ops disk 2 okuma işleme disk 2 yazma işleme

İsteğe bağlı olarak, yalnızca bir disk için belirli alanlar ekleyebilirsiniz.


### <a name="add-multiple-applications"></a>Birden çok uygulama ekleme

Şablon, tek bir uygulama için alanlar sağlar. Beş adede kadar uygulama için benzer sütunlar ekleyebilirsiniz.  

Örneğin, ikinci bir uygulama için tüm alanları belirtmek için şu sütunları ekleyin:

Uygulama 2 ad uygulama 2 tür uygulama 2 sürüm uygulama 2 lisans bitiş tarihi


İsteğe bağlı olarak, yalnızca bir uygulama için belirli alanlar ekleyebilirsiniz.

> [!NOTE]
> Uygulama bilgileri, geçiş için şirket içi ortamınızı değerlendirirken faydalıdır. Ancak, Azure geçişi sunucu değerlendirmesi Şu anda uygulama düzeyi değerlendirmesi gerçekleştirmez ve bir değerlendirme oluştururken uygulamaları hesaba almaz.


## <a name="upload-the-server-information"></a>Sunucu bilgilerini karşıya yükle

CSV şablonuna bilgi ekledikten sonra, sunucuları Azure geçişi: Sunucu değerlendirmesi ' ne aktarın.

1. Azure geçişi > **bilgisayarları bul**' da, doldurulmuş şablona gidin.
2. **İçeri Aktar**’a tıklayın.
3. İçeri aktarma durumu gösterilir.
    - Durum durumunda uyarı görünürse, bunları çözebilir ya da bunları bilmeden devam edebilirsiniz.
    - Uyarılarla önerildiği şekilde sunucu bilgilerini iyileştirmek, değerlendirme doğruluğunu geliştirir.
    - Görüntülenen uyarıları görüntülemek ve onarmak için **uyarı ayrıntılarını İndir ' e tıklayın. CSV**. Bu, uyarılar eklenmiş şekilde CSV 'yi indirir. Uyarıları gözden geçirebilir ve gerektiğinde sorunları giderebilirsiniz.
    Durum durumunda hata görünüyorsa (içeri aktarma durumu **başarısız**olur), içeri aktarmaya devam edebilmeniz için önce bunları çözmeniz gerekir. Bunu yapmak için, şimdi hata ayrıntıları eklenmiş CSV 'yi indirin. Gerektiğinde hataları gözden geçirin ve çözün. Ardından değiştirilen dosyayı yeniden karşıya yükleyin.
4. İçeri aktarma durumu **tamamlandığında**, sunucu bilgileri içeri aktarılır.


> [!NOTE]
> Azure geçişi 'ne yüklenen sunucu bilgilerini güncelleştirmek için aynı **sunucu adını**kullanarak sunucuya yönelik verileri yeniden yükleyin. Şablon alındıktan sonra **sunucu adı** alanının değiştirilemeyeceğini unutmayın. Sunucuları silme Şu anda desteklenmiyor.

## <a name="updating-server-information"></a>Sunucu bilgileri güncelleştiriliyor

Sunucu için verileri aynı **sunucu adıyla**yeniden karşıya yükleyerek sunucu bilgilerini güncelleştirebilirsiniz. **Sunucu adı** alanını değiştiremezsiniz.

Sunucuları silme işlemi şu anda desteklenmiyor.

### <a name="verify-servers-in-the-portal"></a>Portalda sunucuları doğrulama

Bulmadan sonra, sunucuların Azure portal göründüğünü doğrulayabilirsiniz.

1. Azure geçişi panosunu açın.
2. **Azure geçişi-sunucular** > **Azure geçişi: Sunucu değerlendirmesi** sayfasında, **bulunan sunucuların**sayısını görüntüleyen simgeye tıklayın.
3. **Içeri aktarma tabanlı** sekmesine tıklayın.

## <a name="set-up-an-assessment"></a>Değerlendirme ayarlama

Azure geçişi: Sunucu değerlendirmesi kullanarak oluşturabileceğiniz iki tür değerlendirme vardır.

**Değerlendirme** | **Ayrıntılar** | **Veri**
--- | --- | ---
**Performans tabanlı** | Belirtilen performans verileri değerlerini temel alan değerlendirmeler | **ÖNERILEN VM boyutu**: CPU ve bellek kullanım verilerine göre.<br/><br/> **Önerilen disk türü (Standart veya Premium yönetilen disk)** : Şirket ıçı disklerin IOPS ve aktarım hızına göre.
**Şirket içi olarak** | Şirket içi boyutlandırmayı temel alan değerlendirmeler. | **ÖNERILEN VM boyutu**: belirtilen sunucu boyutu temel alınarak<br/><br> **Önerilen disk türü**: değerlendirme için seçtiğiniz depolama türü ayarına göre.


### <a name="run-an-assessment"></a>Değerlendirme çalıştırma

Bir değerlendirmeyi aşağıdaki gibi çalıştırın:

1. Değerlendirme oluşturmak için [en iyi uygulamaları](best-practices-assessment.md) gözden geçirin.
2. **Sunucular** sekmesinde, **Azure geçişi: Sunucu değerlendirmesi** kutucuğunda **değerlendir**' e tıklayın.

    ![Değerlendirme](./media/tutorial-assess-physical/assess.png)

2. **Sunucuları değerlendir**bölümünde, değerlendirme için bir ad belirtin.
3. **Bulma kaynağı**' nda, **Azure geçişi ' ne içeri aktarma yoluyla eklenen makineleri** seçin
3. Değerlendirme özelliklerini gözden geçirmek için **Tümünü görüntüle**’ye tıklayın.

    ![Değerlendirme özellikleri](./media/tutorial-assess-physical/view-all.png)

3. **Grup Seç veya oluştur**' da, **Yeni oluştur**' u seçin ve bir grup adı belirtin. Bir grup, değerlendirme için bir veya daha fazla VM 'yi toplar.
4. **Gruba makine ekleme**' de gruba eklenecek sunucular ' ı seçin.
5. Grubu oluşturmak için **değerlendirme oluştur** ' a tıklayın ve değerlendirmeyi çalıştırın.

    ![Değerlendirme oluşturma](./media/tutorial-assess-physical/assessment-create.png)

6. Değerlendirme oluşturulduktan sonra, **Azure geçişi: Sunucu değerlendirmesi** > **değerlendirmeleri** > **sunucularda** görüntüleyin.
7. Excel dosyası olarak indirmek için **Değerlendirmeyi dışarı aktar**’a tıklayın.



## <a name="review-an-assessment"></a>Değerlendirmeyi gözden geçirme

Bir değerlendirme şunları açıklar:

- **Azure hazırlığı**: sunucuların Azure 'a geçiş için uygun olup olmadığı.
- **Aylık maliyet tahmini**: Azure 'da sunucuları çalıştırmaya yönelik tahmini aylık işlem ve depolama maliyetleri.
- **Aylık depolama maliyeti tahmini**: geçişten sonra disk depolaması için tahmini maliyetler.

### <a name="view-an-assessment"></a>Değerlendirme görüntüleme

1. **Sunucular** >  **geçiş hedefleri** ' nde **Azure geçişi: Sunucu değerlendirmesi**' nde **değerlendirmeler** ' a tıklayın.
2. **Değerlendirmede**, bir değerlendirmeye tıklayarak açın.

    ![Değerlendirme özeti](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure hazırlığını gözden geçirme

1. **Azure 'a hazırlık**bölümünde, sunucuların Azure 'a geçiş için hazır olup olmadığını doğrulayın.
2. Durumu gözden geçirin:
    - **Azure Için hazırlanma**: Azure geçişi, değerlendirmede VM 'ler IÇIN bir VM boyutu ve maliyet tahminleri önerir.
    - **Koşullara hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Azure için hazırlanma**: sorunları ve önerilen düzeltmeyi gösterir.
    - **Hazır olma durumu bilinmiyor**: veri kullanılabilirliği sorunları nedeniyle Azure geçişi hazırlığı değerlendiremez kullanılır.

2. **Azure hazırlık** durumuna tıklayın. Sunucu hazırlığı ayrıntılarını görüntüleyebilir ve işlem, depolama ve ağ ayarları dahil sunucu ayrıntılarını görmek için ayrıntıya gidebilirsiniz.

### <a name="review-cost-details"></a>Maliyet ayrıntılarını gözden geçirin

Bu görünüm Azure 'da çalışan VM 'lerin tahmini işlem ve depolama maliyetini gösterir.

1. Aylık işlem ve depolama maliyetlerini gözden geçirin. Ücretler, değerlendirilen gruptaki tüm sunucular için toplanır.

    - Maliyet tahminleri, bir makine için boyut önerilerini ve bunların disklerini ve özelliklerini temel alır.
    - İşlem ve depolama için tahmini aylık maliyetler gösterilir.
    - Maliyet tahmini, şirket içi sunucuları IaaS VM 'Leri olarak çalıştırmak içindir. Azure geçişi sunucu değerlendirmesi PaaS veya SaaS maliyetlerini göz önünde bulundurmaz.

2. Aylık depolama maliyeti tahminlerini gözden geçirebilirsiniz. Bu görünüm, farklı türlerdeki depolama disklerinin üzerine bölünen, değerlendirilen grup için toplanan depolama maliyetlerini gösterir.
3. Belirli VM 'Lerin ayrıntılarını görmek için ayrıntıya gidebilirsiniz.

> [!NOTE]
> Güven derecelendirmeleri, CSV kullanarak Azure geçişi sunucu değerlendirmesi ' ne içeri aktarılan sunucu değerlendirmelerine atanmaz.


## <a name="supported-operating-system-names"></a>Desteklenen işletim sistemi adları

Ad | Ad
--- | ---
**A-H** |
Apple Mac OS X 10 | Asıanux 3<br/>Asıanux 4<br/>Asıanux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux
Borçlu GNU/Linux 4<br/>Borçlu GNU/Linux 5<br/>Borçlu GNU/Linux 6<br/>Borçlu GNU/Linux 7<br/>De, GNU/Linux 8 | FreeBSD
**I-R** |
IBM OS/2 | MS-DOS |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora |
**S-T** |
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenlik sistemleri eComStation 1<br/>Çevre sistemleri, eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** |
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server eşiği<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * İçeri aktarılan sunucular Azure geçişi: CSV kullanarak sunucu değerlendirmesi.
> * Bir değerlendirme oluşturulup gözden geçirildi

Şimdi daha doğru değerlendirmeler için [bir gereç dağıtın](./migrate-appliance.md) ve [bağımlılık Analizi](./concepts-dependency-visualization.md)kullanarak daha derin değerlendirme için sunucuları birlikte gruplar halinde toplayın.
