---
title: Azure Geçişi Hakkında
description: Azure geçişi hizmeti hakkında bilgi edinin.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fd8806a02e48481042eb756a0a077d801583cd2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870781"
---
# <a name="about-azure-migrate"></a>Azure Geçişi Hakkında

Bu makalede, Azure geçişi hizmetine hızlı bir genel bakış sunulmaktadır.

Azure geçişi, Azure şirket içi sunucuları, altyapısı, uygulamaları ve verileri değerlendirmek ve bu uygulamalara geçiş yapmak için merkezi bir merkez sağlar. Şunları sağlar:

- **Birleşik geçiş platformu**: Azure 'a geçişinizi başlatmak, çalıştırmak ve izlemek için tek bir portal.
- **Araç aralığı**: değerlendirme ve geçiş için bir dizi araç. Azure geçiş araçları, Azure geçişi: bulma ve değerlendirme ve Azure geçişi: sunucu geçişi içerir. Azure geçişi, diğer Azure hizmetleri ve araçlarıyla ve bağımsız yazılım satıcısı (ISV) teklifleriyle de tümleştirilir.
- **Değerlendirme ve geçiş**: Azure geçiş hub 'ında şunları değerlendirebileceğiniz ve geçirebilirsiniz:
    - **Windows, Linux ve SQL Server**: SQL Server örnekleri dahil olmak üzere şirket içi sunucuları değerlendirin ve Azure sanal makinelerine veya Azure VMware çözümüne (AVS) (Önizleme) geçirin.
    - **Veritabanları**: şirket içi veritabanlarını değerlendirin ve BUNLARı Azure SQL veritabanı 'NA veya SQL yönetilen örneği 'ne geçirin.
    - **Web uygulamaları**: şirket içi Web uygulamalarını değerlendirin ve Azure App Service Geçiş Yardımcısı kullanarak onları Azure App Service geçirin.
    - **Sanal masaüstleri**: şirket içi sanal masaüstü ALTYAPıNıZı (VDI) değerlendirin ve Azure 'Da Windows sanal masaüstüne geçirin.
    - **Veriler**: büyük miktarlarda verileri Azure 'a hızla ve Azure Data Box ürünlerini kullanarak uygun maliyetli bir şekilde geçirin.

## <a name="integrated-tools"></a>Tümleşik araçlar

Azure geçişi hub 'ı şu araçları içerir:

**Araç** | **Değerlendirme ve geçirme** | **Ayrıntılar**
--- | --- | ---
**Azure geçişi: bulma ve değerlendirme** | SQL dahil sunucu bulma ve değerlendirme | Şirket içi VMware VM 'Leri, Hyper-V VM 'Leri ve fiziksel sunucuları Azure 'a geçişe hazırlık olarak bulun ve değerlendirin.
**Azure geçişi: sunucu geçişi** | Sunucuları geçirme | VMware VM 'leri, Hyper-V VM 'Leri, fiziksel sunucular, diğer sanallaştırılmış sunucular ve genel bulut VM 'lerini Azure 'a geçirin.
**Data Migration Yardımcısı** | Azure SQL veritabanı, Azure SQL yönetilen örneği veya SQL Server çalıştıran Azure VM 'lerine geçiş için SQL Server veritabanlarını değerlendirin. | Data Migration Yardımcısı, SQL Severs.It, geçişi engelleyen olası sorunları belirlemenize yardımcı olan tek başına bir araçtır. Desteklenmeyen özellikleri, geçişten sonra size faydalanabilir yeni özellikleri ve veritabanı geçişinin doğru yolunu tanımlar. [Daha fazla bilgi edinin](/sql/dma/dma-overview).
**Azure Veritabanı Geçiş Hizmeti** | Şirket içi veritabanlarını SQL Server, Azure SQL veritabanı veya SQL yönetilen örnekleri çalıştıran Azure VM 'lerine geçirme | Veritabanı geçiş hizmeti hakkında [daha fazla bilgi edinin](../dms/dms-overview.md) .
**Movere** | Sunucuları değerlendir | Movere hakkında [daha fazla bilgi edinin](#movere) .
**Web uygulaması Geçiş Yardımcısı** | Şirket içi Web uygulamalarını değerlendirin ve bunları Azure 'a geçirin. |  Azure App Service geçiş için şirket içi web sitelerini değerlendirmek üzere Azure App Service Geçiş Yardımcısı kullanın.<br/><br/> .NET ve PHP Web uygulamalarını Azure 'a geçirmek için Geçiş Yardımcısı kullanın. Azure App Service Geçiş Yardımcısı hakkında [daha fazla bilgi edinin](https://appmigration.microsoft.com/) .
**Azure Data Box** | Çevrimdışı verileri geçirme | Büyük miktarlarda çevrimdışı verileri Azure 'a taşımak için Azure Data Box ürünlerini kullanın. [Daha fazla bilgi edinin](../databox/index.yml).

> [!NOTE]
> Azure Kamu kullanıyorsanız, dış tümleşik araçlar ve ISV teklifleri verileri Azure geçişi 'ne gönderemez. Araçları bağımsız olarak kullanabilirsiniz.

## <a name="isv-integration"></a>ISV tümleştirmesi

Azure geçişi, çeşitli ISV teklifleriyle tümleştirilir. 

**ISV**    | **Özellik**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Sunucuları geçirin.
[Cloudamize](https://www.cloudamize.com/platform) | Sunucuları değerlendirin.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Sunucuları değerlendirin ve geçirin.
[Device42](https://docs.device42.com/) | Sunucuları değerlendirin.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDı 'yi değerlendirin.
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Sunucuları geçirin.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Sunucuları değerlendirin.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Sunucuları ve veritabanlarını değerlendirin.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2152102) | Sunucuları geçirin.

## <a name="azure-migrate-discovery-and-assessment-tool"></a>Azure geçişi: bulma ve değerlendirme aracı

Azure geçişi: bulma ve değerlendirme aracı, Azure 'a geçiş için şirket içi VMware VM 'Leri, Hyper-V VM 'Leri ve fiziksel sunucuları bulur ve değerlendirir. 

Araç şunları yapar:

- **Azure hazırlığı**: değerlendirir şirket Içi sunucuların Azure 'a geçiş için hazır olup olmadığını belirtir.
- **Azure boyutlandırma**: geçişten sonra Azure VM 'leri/Azure SQL yapılandırması/Azure VMware Çözüm düğümlerinin sayısını tahmin eder.
- **Azure maliyet tahmini**: Azure 'da şirket içi sunucuları çalıştırmaya yönelik maliyetleri tahmin eder.
- **Bağımlılık Analizi**: bağımlı sunucuları Azure 'a taşımak için çapraz sunucu bağımlılıklarını ve iyileştirme stratejilerini belirler. [Bağımlılık Analizi](concepts-dependency-visualization.md)ile bulma ve değerlendirme hakkında daha fazla bilgi edinin.

Bulma ve değerlendirme, şirket içinde dağıttığınız basit bir [Azure geçiş](migrate-appliance.md) gereci kullanır.

- Gereç bir VM veya fiziksel sunucuda çalışır. İndirilen bir şablonu kullanarak kolayca yükleyebilirsiniz.
- Gereç, şirket içi sunucuları bulur. Ayrıca, Azure geçişi 'ne sürekli olarak sunucu meta verileri ve performans verileri gönderir.
- Gereç keşfi aracısız. Bulunan sunucularda hiçbir şey yüklü değil.
- Gereç keşfi sonrasında, bulunan sunucuları gruplar halinde toplayabilir ve her grup için değerlendirmeler çalıştırabilirsiniz.


## <a name="azure-migrate-server-migration-tool"></a>Azure geçişi: sunucu geçiş aracı

Azure geçişi: sunucu geçiş aracı, sunucuları Azure 'a geçirmeye yardımcı olur:

**Geçiş** | **Ayrıntılar**
--- | ---
Şirket içi VMware sanal makineleri | Aracısız veya aracı tabanlı geçiş kullanarak VM 'Leri Azure 'a geçirin.<br/><br/> Aracısız geçiş için sunucu geçişi, VMware VM 'lerinin bulunması ve değerlendirmesi için bulma ve değerlendirme tarafından da kullanılabilen aynı Azure geçiş gerecini kullanır.<br/><br/> Sunucu geçişi, aracı tabanlı geçiş için bir çoğaltma gereci kullanır.
Şirket içi Hyper-V VM'leri | VM 'Leri Azure 'a geçirin.<br/><br/> Sunucu geçişi, geçiş için Hyper-V konağına yüklenen sağlayıcı aracılarını kullanır.
Şirket içi fiziksel sunucular veya diğer bulutlarda barındırılan sunucular | Fiziksel sunucuları Azure 'a geçirebilirsiniz. Ayrıca, diğer sanallaştırılmış sunucuları ve diğer genel bulutlardan VM 'Leri geçiş amacıyla fiziksel sunucu olarak düşünerek geçirebilirsiniz. Sunucu geçişi geçiş için bir çoğaltma gereci kullanır.


## <a name="selecting-assessment-and-migration-tools"></a>Değerlendirme ve geçiş araçlarını seçme

Azure geçişi hub 'ında değerlendirme veya geçiş için kullanmak istediğiniz aracı seçin ve bunu bir projeye ekleyin. Bir ISV aracı veya Movere eklerseniz:

- Başlamak için, araç yönergelerini izleyerek bir lisans alın veya ücretsiz deneme için kaydolun. Her ISV veya araç, araç lisansını belirler.
- Her aracın Azure geçişi 'ne bağlanma seçeneği vardır. Bağlanmak için araç yönergelerini izleyin.
- Projenin içindeki tüm araçlarda geçişlerinizi izleyin.

## <a name="movere"></a>Movere

Movere, hizmet olarak yazılım (SaaS) platformudur. Tek bir gün içinde tüm BT ortamlarını doğru şekilde sunarak iş zekası artar. Kuruluşlar ve kuruluşlar büyür, değişir ve dijital olarak iyileştirmektedir. Bu şekilde, Movere, platformlarını, platformunu, uygulamayı veya coğrafi konumu her ne olursa olsun, ortamları görmek ve denetlemek için gereken güvenle sağlar.

Microsoft movere 'yi [edinmiş](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) ve artık tek başına bir teklif olarak satılmamaktadır. Movere, Microsoft çözüm değerlendirmesi ve Microsoft Bulut Ekonomikler programı aracılığıyla kullanılabilir. Movere hakkında [daha fazla bilgi edinin](https://www.movere.io) .

Ayrıca, yerleşik geçiş hizmetimiz olan Azure geçişi ' ne da bakmanız önerilir. Azure geçişi, bulut geçişinizi basitleştirecek bir merkezi Merkez sağlar. Merkez, fiziksel ve sanal sunucular, veritabanları ve uygulamalar gibi iş yüklerini büyük ölçüde destekler. Uçtan uca görünürlük, bulma, değerlendirme ve geçiş genelinde ilerlemeyi kolayca izlemenize olanak sağlar.

Hem Azure hem de iş ortağı ISV araçlarıyla Azure geçişi, aşağıdakiler dahil olmak üzere çok çeşitli özelliklere sahiptir:

- Sanal ve fiziksel sunucuları bulma.
- Performans tabanlı doğru geliştirme.
- Maliyet planlaması.
- İçeri aktarma temelli değerlendirmeler.
- Aracısız uygulamaların bağımlılık analizi.

Kullanmaya başlamak için uzman yardımı arıyorsanız, Microsoft size rehberlik etmek için [Azure uzman tarafından yönetilen hizmet sağlayıcılarına](https://azure.microsoft.com/partners) sahiptir. [Azure geçişi Web sitesine](https://azure.microsoft.com/services/azure-migrate/)göz atın. 

## <a name="azure-migrate-versions"></a>Azure geçişi sürümleri

Azure geçişi hizmetinin iki sürümü vardır.

- **Güncel sürüm**: bu sürümü kullanarak proje oluşturun, şirket içi sunucuları bulun ve değerlendirmeleri ve geçişleri düzenleyin. Bu sürümdeki yenilikler hakkında [daha fazla bilgi edinin](whats-new.md) .
- **Önceki sürüm**: klasik Azure geçişi olarak da bilinen Azure geçişi 'nin önceki sürümü yalnızca şirket Içi VMware VM 'lerinin değerlendirmesini destekler. Klasik Azure geçişi, Şubat 2024 ' de devre dışı bırakılıyor. Şubat 2024 ' den sonra Azure geçişi 'nin klasik sürümü artık desteklenmeyecektir ve klasik projelerdeki envanter meta verileri silinir. Önceki sürümdeki proje veya bileşenleri yeni sürüme yükseltemezsiniz. [Yeni bir proje oluşturmanız](create-manage-projects.md)ve buna [değerlendirme ve geçiş araçları eklemeniz](./create-manage-projects.md) gerekir. Kullanılabilir değerlendirme ve geçiş araçlarının nasıl kullanılacağını anlamak için öğreticilerini kullanın. Klasik bir projeye eklenmiş bir Log Analytics çalışma alanınız varsa, klasik projeyi sildikten sonra onu geçerli sürümün projesine ekleyebilirsiniz.

    Azure portal mevcut projelere erişmek için **Azure geçişi**' ni arayıp seçin. **Azure geçiş** panosu, eski projelere erişim için bir bildirim ve bağlantı içerir.

## <a name="next-steps"></a>Sonraki adımlar

- [VMware VM](./tutorial-discover-vmware.md)'leri, [Hyper-V VM](./tutorial-discover-hyper-v.md)'lerini veya [fiziksel sunucuları](./tutorial-discover-physical.md)değerlendirmek için öğreticilerimizi deneyin.
- Azure Geçişi hakkında [sık sorulan soruları gözden geçirin](resources-faq.md).