---
title: StorSimple 8000 serisi çözümüne genel bakış | Microsoft Docs
description: StorSimple katmanlamayı, cihazı, sanal cihaz, hizmetleri ve depolama yönetimini açıklar ve StorSimple 'da kullanılan anahtar terimleri tanıtır.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: timlt
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965110"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 serisi: karma bulut depolama çözümü

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Genel Bakış
Şirket içi cihazlar ve bulut depolama Microsoft Azure arasındaki depolama görevlerini yöneten tümleşik bir depolama çözümüne Microsoft Azure StorSimple hoş geldiniz. StorSimple, kurumsal depolama ve veri korumasıyla ilişkili birçok sorunu ve gideri ortadan kaldıran verimli, ekonomik ve kolayca yönetilebilir bir depolama alanı ağı (SAN) çözümüdür. Özel StorSimple 8000 serisi cihazını kullanır, bulut hizmetleriyle tümleştirilir ve bulut depolaması dahil olmak üzere tüm kurumsal depolamanın sorunsuz bir görünümü için bir dizi yönetim aracı sağlar. (Microsoft Azure Web sitesinde yayımlanan StorSimple dağıtım bilgileri yalnızca StorSimple 8000 serisi cihazlar için geçerlidir. StorSimple 5000/7000 Serisi bir cihaz kullanıyorsanız [StorSimple yardımı](http://onlinehelp.storsimple.com/)' na gidin.)

StorSimple, çeşitli depolama ortamları genelinde depolanan verileri yönetmek için [depolama katmanlaması](#automatic-storage-tiering) kullanır. Geçerli çalışma kümesi şirket içinde katı hal sürücülerinde (SSD) depolanır, daha az sıklıkta kullanılan veriler sabit disk sürücülerinde (HDD) depolanır ve arşiv verileri buluta gönderilir. Üstelik, StorSimple, verilerin kullandığı depolama miktarını azaltmak için Yinelenenleri kaldırma ve sıkıştırma kullanır. Daha fazla bilgi için [Yinelenenleri kaldırma ve sıkıştırma](#deduplication-and-compression)' ya gidin. StorSimple 8000 serisi belgelerinde kullanılan diğer anahtar hüküm ve kavramların tanımları için, bu makalenin sonundaki [StorSimple terminolojisi](#storsimple-terminology) ' ne gidin.

StorSimple veri koruma özellikleri, depolama yönetimine ek olarak isteğe bağlı ve zamanlanmış yedeklemeler oluşturmanıza ve bunları yerel olarak veya bulutta depolamanıza olanak tanır. Yedeklemeler, artımlı anlık görüntü biçiminde alınır ve bu, hızlı bir şekilde oluşturulup geri yüklenebilecekleri anlamına gelir. Bulut anlık görüntüleri, olağanüstü durum kurtarma senaryolarında önemli ölçüde önemli olabilir çünkü ikincil depolama sistemlerini (bant yedeklemesi gibi) değiştirir ve verileri veri merkezinize veya gerekirse alternatif sitelere geri yüklemenize olanak tanır.

![video simgesi](./media/storsimple-overview/video_icon.png) Microsoft Azure StorSimple hızlı bir giriş için videoyu izleyin.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>StorSimple neden kullanılmalıdır?
Aşağıdaki tabloda Microsoft Azure StorSimple sağladığı bazı önemli avantajlar açıklanmaktadır.

| Özellik | Avantaj |
| --- | --- |
| Saydam tümleştirme |, Veri depolama olanaklarını ayrıntılı bir şekilde bağlamak için Iscsı protokolünü kullanır. Bu, bulutta depolanan verilerin veri merkezinde veya uzak sunucularda tek bir konumda depolanması gibi göründüğünden emin olmanızı sağlar. |
| Azaltılan depolama maliyetleri |Mevcut taleplerini karşılamak için yeterli yerel veya bulut depolama alanını ayırır ve bulut depolamayı yalnızca gerekli olduğunda genişletir. Aynı verilerin (yinelenenleri kaldırma) yedekli sürümlerini kaldırarak ve sıkıştırma kullanarak, depolama gereksinimlerini ve harcamayı daha da azaltır. |
| Basitleştirilmiş depolama yönetimi |Şirket içinde, uzak bir sunucuda ve bulutta depolanan verileri yapılandırmak ve yönetmek için sistem yönetim araçları sağlar. Ayrıca, Microsoft Yönetim Konsolu (MMC) ek bileşeninden yedekleme ve geri yükleme işlevlerini yönetebilirsiniz.|
| İyileştirilmiş olağanüstü durum kurtarma ve uyumluluk |, Genişletilmiş kurtarma süresi gerektirmez. Bunun yerine, verileri gerektiğinde geri yükler. Bu, normal işlemlerin minimum kesintile devam edebileceği anlamına gelir. Ayrıca, yedekleme zamanlamalarını ve veri bekletmeyi belirtmek için ilkeleri yapılandırabilirsiniz. |
| Veri taşınabilirliği |Microsoft Azure Cloud Services 'a yüklenen verilere, kurtarma ve geçiş amacıyla diğer sitelerden erişilebilir. Ayrıca, Microsoft Azure ' de çalışan sanal makinelerde (VM) StorSimple bulut gereçlerini yapılandırmak için StorSimple kullanabilirsiniz. VM 'Ler daha sonra sanal cihazları kullanarak test veya kurtarma amaçlarıyla depolanmış verilere erişebilir. |
| İş sürekliliği |StorSimple 5000-7000 Serisi kullanıcıların verilerini bir StorSimple 8000 serisi cihazına geçirmelerini sağlar. |
| Azure Kamu portalında kullanılabilirlik |StorSimple, Azure Kamu portalında kullanılabilir. Daha fazla bilgi için bkz. Şirket [Içi StorSimple cihazınızı kamu portalında dağıtma](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Veri koruma ve kullanılabilirlik |StorSimple 8000 serisi, yerel olarak yedekli depolama (LRS) ve coğrafi olarak yedekli depolama (GRS) özelliklerine ek olarak bölgesel olarak yedekli depolamayı (ZRS) destekler. ZRS ayrıntıları için [Azure Storage artıklık seçenekleri ' nde bu makaleye](https://azure.microsoft.com/documentation/articles/storage-redundancy/) bakın. |
| Kritik uygulamalar için destek |StorSimple, yerel olarak sabitlenmiş uygun birimleri tanımlamanızı sağlar ve bu da kritik uygulamalar için gereken verilerin buluta katmanlanmamasını sağlar. Yerel olarak sabitlenmiş birimler, bulut gecikme sürelerine veya bağlantı sorunlarına tabi değildir. Yerel olarak sabitlenmiş birimler hakkında daha fazla bilgi için bkz. [birimleri yönetmek Için StorSimple Aygıt Yöneticisi hizmetini kullanma](storsimple-8000-manage-volumes-u2.md). |
| Düşük gecikme süresi ve yüksek performans |Azure Premium Depolama 'nın yüksek performans ve düşük gecikme süresi özelliklerinden faydalanan bulut gereçlerini oluşturabilirsiniz. StorSimple Premium bulut gereçlerinin hakkında daha fazla bilgi için bkz. [Azure 'da StorSimple Cloud Appliance dağıtma ve yönetme](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple bileşenleri
Microsoft Azure StorSimple çözümü aşağıdaki bileşenleri içerir:

* **Microsoft Azure StorSimple cihaz** : SSD 'Leri ve HDD 'leri içeren şirket içi hibrit depolama dizisi, yedekli denetleyiciler ve otomatik yük devretme özellikleri ile birlikte. Denetleyiciler, daha az sıklıkta kullanılan verileri buluta taşırken, depolama katmanlamasını, yerel depolama (cihazda veya şirket içi sunucular) üzerinde şu anda kullanılmakta olan (ya da şirket içi sunuculardaki) verileri bir kez yönetir.
* **StorSimple Cloud Appliance** – StorSimple Sanal gereci olarak da bilinen, bu, fiziksel karma depolama cihazının mimarisini ve yeteneklerini çoğaltan StorSimple cihazının bir yazılım sürümüdür. StorSimple Cloud Appliance, bir Azure sanal makinesinde tek bir düğümde çalışır. Azure Premium Storage 'ın avantajlarından yararlanan Premium sanal cihazlar güncelleştirme 2 ve sonrasında kullanılabilir.
* **Storsimple Aygıt Yöneticisi hizmeti** : bir StorSimple cihazını veya StorSimple Cloud Appliance tek bir web arabiriminden yönetmenizi sağlayan bir Azure Portal uzantısı. StorSimple Aygıt Yöneticisi hizmetini kullanarak hizmetleri oluşturabilir ve yönetebilir, cihazları görüntüleyebilir ve yönetebilir, uyarıları görüntüleyebilir, birimleri yönetebilir, yedekleme ilkelerini ve yedekleme kataloğunu görüntüleyebilir ve yönetebilirsiniz.
* **StorSimple için Windows PowerShell** : StorSimple cihazını yönetmek için kullanabileceğiniz bir komut satırı arabirimi. StorSimple için Windows PowerShell, StorSimple cihazınızı kaydetmenize, cihazınızda ağ arabirimini yapılandırmanıza, belirli güncelleştirme türlerini yüklemenize, destek oturumuna erişerek cihazınızda sorun gidermenize ve cihazı değiştirmenize olanak tanıyan özellikler içerir. durumunda. StorSimple için Windows PowerShell, seri konsoluna bağlanarak veya Windows PowerShell uzaktan iletişimini kullanarak erişebilirsiniz.
* **Azure PowerShell StorSimple cmdlet 'leri** : komut satırından hizmet düzeyi ve geçiş görevlerini otomatikleştirmenizi sağlayan Windows PowerShell cmdlet 'lerinin bir koleksiyonu. StorSimple için Azure PowerShell cmdlet 'leri hakkında daha fazla bilgi için [cmdlet başvurusuna](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure)gidin.
* **StorSimple Snapshot Manager** : uygulamayla tutarlı yedeklemeler oluşturmak için birim grupları ve Windows birim gölge kopyası hizmeti kullanan bir MMC ek bileşeni. Ayrıca, StorSimple Snapshot Manager kullanarak yedekleme zamanlamaları oluşturabilir, birimleri kopyalayabilir veya geri yükleyebilirsiniz.
* **SharePoint Için StorSimple Bağdaştırıcısı** : Microsoft Azure StorSimple depolama ve veri korumasını SharePoint Server gruplarına şeffaf bir şekilde genişleten bir araç, StorSimple Storage 'un SharePoint Merkezi 'nden görüntülenebilir ve yönetilebilir olmasını sağlar Yönetim portalı.

Aşağıdaki diyagramda Microsoft Azure StorSimple mimarisinin ve bileşenlerinin üst düzey bir görünümü verilmektedir.

![StorSimple mimarisi](./media/storsimple-overview/overview-big-picture.png)

Aşağıdaki bölümlerde, bu bileşenlerin her biri daha ayrıntılı bir şekilde açıklanmıştır ve çözümün verileri nasıl tartışdığını, depolama alanını ayırdığını ve depolama yönetimi ile veri korumasını nasıl kolaylaştırdığını açıklayın. Son bölümde, StorSimple bileşenleriyle ve bunların yönetimiyle ilgili önemli hüküm ve kavramların bazıları için tanımlar sağlanmıştır.

## <a name="storsimple-device"></a>StorSimple cihazı
Microsoft Azure StorSimple cihaz, birincil depolama ve şirket içinde depolanan verilere Iscsı erişimi sağlayan bir şirket içi karma depolama dizisidir. Bulut depolamayla iletişimi yönetir ve Microsoft Azure StorSimple çözümünde depolanan tüm verilerin güvenlik ve gizliliğini sağlamaya yardımcı olur.

StorSimple cihazı SSD 'leri ve sabit disk sürücüleri HDD 'leri ve kümeleme ve otomatik yük devretme desteğini içerir. Paylaşılan bir işlemci, paylaşılan depolama ve iki yansıtılmış denetleyici içerir. Her denetleyici şunları sağlar:

* Ana bilgisayar ile bağlantı
* Yerel ağa (LAN) bağlanmak için en fazla altı ağ bağlantı noktası
* Donanım izleme
* Güç kesintisi olsa bile bilgileri koruyan geçici olmayan rastgele erişim belleği (NVRAM)
* Bir yük devretme kümesindeki sunucularda yazılım güncelleştirmelerini yönetmek için küme durumunu algılayan güncelleştirme, güncelleştirmelerin en düşük düzeyde veya hiçbir etkisi olmaması için
* Bir arka uç kümesi gibi işlev gören Küme hizmeti, yüksek kullanılabilirlik sağlar ve bir HDD veya SSD başarısız olursa veya çevrimdışı duruma getirildiğinde oluşabilecek olumsuz etkileri en aza indirir

Herhangi bir zamanda yalnızca bir denetleyici etkin olur. Etkin denetleyici başarısız olursa ikinci denetleyici otomatik olarak etkin hale gelir.

Daha fazla bilgi için, [StorSimple donanım bileşenlerine ve durumuna](storsimple-8000-monitor-hardware-status.md)gidin.

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Fiziksel karma depolama cihazının mimarisini ve yeteneklerini çoğaltan bir bulut gereci oluşturmak için StorSimple kullanabilirsiniz. StorSimple Cloud Appliance (StorSimple Sanal gereci olarak da bilinir), bir Azure sanal makinesinde tek bir düğümde çalışır. (Bulut gereci yalnızca bir Azure sanal makinesinde oluşturulabilir. StorSimple cihazında veya şirket içi sunucuda bir tane oluşturamazsınız.)

Bulut gereci aşağıdaki özelliklere sahiptir:

* Fiziksel bir gereç gibi davranır ve buluttaki sanal makinelere bir Iscsı arabirimi sunabilir.
* Bulutta sınırsız sayıda bulut aygıtı oluşturabilir, bunları gerektiği gibi açıp kapatabilirsiniz.
* Olağanüstü durum kurtarma, geliştirme ve test senaryolarında şirket içi ortamların benzetimini yapmanıza yardımcı olabilir ve yedeklerden öğe düzeyinde alma konusunda yardımcı olabilir.

StorSimple Cloud Appliance iki modelde mevcuttur: 8010 cihaz (eski adıyla 1100 modeli) ve 8020 cihaz. 8010 cihazının en fazla 30 TB kapasitesi vardır. Azure Premium Storage 'ın avantajlarından yararlanan 8020 cihazı en fazla 64 TB kapasiteye sahiptir. (Yerel katmanlarda, Azure Premium Storage verileri SSD 'lerde depolar, standart depolama ise verileri HDD 'lerde depolar.) Premium Depolama kullanmak için bir Azure Premium Depolama hesabınızın olması gerektiğini unutmayın.

StorSimple Cloud Appliance hakkında daha fazla bilgi için [Azure 'da StorSimple Cloud Appliance dağıtma ve yönetme](storsimple-8000-cloud-appliance-u2.md)konusuna gidin.

## <a name="storsimple-device-manager-service"></a>StorSimple Cihaz Yöneticisi hizmeti
Microsoft Azure StorSimple, veri merkezi ve bulut depolamayı merkezi olarak yönetmenizi sağlayan Web tabanlı bir kullanıcı arabirimi (StorSimple Aygıt Yöneticisi hizmeti) sağlar. StorSimple Aygıt Yöneticisi hizmetini kullanarak aşağıdaki görevleri gerçekleştirebilirsiniz:

* StorSimple cihazları için sistem ayarlarını yapılandırın.
* StorSimple cihazları için güvenlik ayarlarını yapılandırın ve yönetin.
* Bulut kimlik bilgilerini ve özelliklerini yapılandırın.
* Bir sunucudaki birimleri yapılandırma ve yönetme.
* Birim gruplarını yapılandırın.
* Verileri yedekleme ve geri yükleme.
* Performansı izleyin.
* Sistem ayarlarını gözden geçirin ve olası sorunları tespit edin.

Tüm yönetim görevlerini gerçekleştirmek için StorSimple Aygıt Yöneticisi hizmetini, ilk kurulum ve güncelleştirmelerin yüklenmesi gibi sistem durumu gerektiren bir süre gerektirenler dışında kullanabilirsiniz.

Daha fazla bilgi için, StorSimple [cihazınızı yönetmek Için storsimple Aygıt Yöneticisi hizmetini kullanma](storsimple-8000-manager-service-administration.md)bölümüne gidin.

## <a name="windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell
StorSimple için Windows PowerShell, Microsoft Azure StorSimple hizmetini oluşturup yönetmek ve StorSimple cihazlarını ayarlamak ve izlemek için kullanabileceğiniz bir komut satırı arabirimi sağlar. StorSimple cihazınızı yönetmek için adanmış cmdlet 'leri içeren bir Windows PowerShell tabanlı, komut satırı arabirimidir. StorSimple için Windows PowerShell şunları yapmanıza izin veren özelliklere sahiptir:

* Bir cihazı kaydedin.
* Bir cihazda ağ arabirimini yapılandırın.
* Belirli güncelleştirme türlerini yükler.
* Destek oturumuna erişerek cihazınızda sorun giderin.
* Cihaz durumunu değiştirin.

Windows PowerShell uzaktan iletişimini kullanarak, bir seri konsolundan (doğrudan cihaza bağlı bir ana bilgisayarda) veya uzaktan StorSimple için Windows PowerShell erişebilirsiniz. İlk cihaz kaydı gibi bazı StorSimple için Windows PowerShell görevlerinin yalnızca seri konsolunda yapılabileceğini unutmayın.

Daha fazla bilgi için [cihazınızı yönetmek üzere StorSimple için Windows PowerShell kullanma](storsimple-8000-windows-powershell-administration.md)bölümüne gidin.

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple cmdlet 'leri
Azure PowerShell StorSimple cmdlet 'leri, komut satırından hizmet düzeyi ve geçiş görevlerini otomatikleştirmenizi sağlayan Windows PowerShell cmdlet 'lerinin bir koleksiyonudur. StorSimple için Azure PowerShell cmdlet 'leri hakkında daha fazla bilgi için [cmdlet başvurusuna](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)gidin.

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager, yerel ve bulut verilerinin tutarlı, noktadan noktaya yedek kopyalarını oluşturmak için kullanabileceğiniz bir Microsoft Yönetim Konsolu (MMC) ek bileşenidir. Ek bileşen Windows Server tabanlı bir konakta çalışır. StorSimple Snapshot Manager kullanarak şunları yapabilirsiniz:

* Birimleri yapılandırma, yedekleme ve silme.
* Yedeklenen verilerin uygulamayla tutarlı olduğundan emin olmak için birim gruplarını yapılandırın.
* Yedekleme ilkelerini, verilerin önceden belirlenmiş bir zamanlamaya göre yedeklenmesi ve belirlenmiş bir konuma (yerel olarak veya bulutta) depolanması için yönetin.
* Birimleri ve tek tek dosyaları geri yükleyin.

Yedeklemeler, yalnızca son anlık görüntünün alındığı ve tam yedeklerden daha az depolama alanı gerektiren değişiklikleri kaydeden anlık görüntüler olarak yakalanır. Yedekleme zamanlamaları oluşturabilir veya gerektiğinde hemen yedeklemeler alabilirsiniz. Ayrıca, StorSimple Snapshot Manager kullanarak kaç anlık görüntü kaydedileceğini denetleyen bekletme ilkeleri oluşturabilirsiniz. Daha sonra verileri bir yedeklemeden geri yüklemeniz gerekiyorsa, StorSimple Snapshot Manager yerel veya bulut anlık görüntülerinin kataloğundan seçim yapmanızı sağlar. 

Bir olağanüstü durum oluşursa veya başka bir nedenden dolayı verileri geri yüklemeniz gerekiyorsa, StorSimple Snapshot Manager gerektiğinde bunu artımlı olarak geri yükler. Veri geri yükleme, bir dosyayı geri yüklerken, ekipman veya taşıma işlemlerini başka bir siteye taşırken sistemin tamamını kapatmanızı gerektirmez.

Daha fazla bilgi için bkz. [StorSimple Snapshot Manager nedir?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>SharePoint için StorSimple Bağdaştırıcısı
Microsoft Azure StorSimple, StorSimple depolama ve veri koruma özelliklerini SharePoint Server gruplarına şeffaf bir şekilde genişleten isteğe bağlı bir bileşen olan SharePoint için StorSimple bağdaştırıcısını içerir. Bağdaştırıcı, bir uzak BLOB depolama (KÇY) sağlayıcısı ve SQL Server KÇY özelliği ile çalışarak, Blobları Microsoft Azure StorSimple sistemi tarafından yedeklenen bir sunucuya taşımanıza olanak tanır. Microsoft Azure StorSimple daha sonra BLOB verilerini, kullanıma göre yerel olarak veya bulutta depolar.

SharePoint için StorSimple bağdaştırıcısı, SharePoint Yönetim Portalı içinden yönetilir. Sonuç olarak, SharePoint Yönetimi Merkezi olarak kalır ve tüm depolama alanı SharePoint grubunda bulunur.

Daha fazla bilgi için bkz. [SharePoint Için StorSimple Bağdaştırıcısı](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Depolama yönetimi teknolojileri
Adanmış StorSimple cihazına, sanal cihaza ve diğer bileşenlere ek olarak Microsoft Azure StorSimple, verilere hızlı erişim sağlamak ve depolama tüketimini azaltmak için aşağıdaki yazılım teknolojilerini kullanır:

* [Otomatik depolama katmanlama](#automatic-storage-tiering) 
* [Ölçülü kaynak sağlama](#thin-provisioning) 
* [Yinelenenleri kaldırma ve sıkıştırma](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Otomatik depolama katmanlama
Microsoft Azure StorSimple, verileri geçerli kullanım, yaş ve diğer verilerle ilişki temelinde mantıksal katmanlarda otomatik olarak düzenler. En etkin olan veriler yerel olarak depolanır, daha az etkin ve etkin olmayan veriler buluta otomatik olarak geçirilir. Aşağıdaki diyagramda bu depolama yaklaşımı gösterilmektedir.

![StorSimple depolama katmanları](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

StorSimple, Hızlı erişimi etkinleştirmek için, StorSimple cihazında SSD 'lerde çok etkin verileri (sık kullanılan veriler) depolar. Cihazdaki veya veri merkezindeki sunuculardaki HDD 'Ler için zaman zaman kullanılan verileri (ısınma verileri) depolar. Etkin olmayan verileri, yedekleme verilerini ve arşivleme ya da uyumluluk amacıyla korunan verileri buluta taşımaktadır. 

> [!NOTE]
> Güncelleştirme 2 veya sonrasında, yerel olarak sabitlenmiş bir birim belirtebilirsiniz, bu durumda veriler yerel cihazda kalır ve buluta katmanlı değildir. 


StorSimple, kullanım desenleri değiştikçe verileri ve depolama atamalarını ayarlar ve yeniden düzenler. Örneğin, bazı bilgiler zamana göre daha az etkin hale gelebilir. Aşamalı olarak daha az etkin hale geldiğinde, SSD 'lerden HDD 'Ler ve ardından buluta geçirilir. Aynı veriler tekrar etkin hale gelirse, depolama cihazına geri geçirilir.

Depolama katmanlama süreci aşağıdaki gibi gerçekleşir:

1. Bir Sistem Yöneticisi Microsoft Azure bir bulut depolama hesabı ayarlar.
2. Yönetici, cihaz ve dosya sunucusunu yapılandırmak, birim ve veri koruma ilkeleri oluşturmak için seri konsolunu ve StorSimple Aygıt Yöneticisi hizmetini (Azure portal çalıştıran) kullanır. Şirket içi makineler (örneğin, dosya sunucuları), StorSimple cihazına erişmek için Internet küçük bilgisayar sistemi arabirimi 'ni (Iscsı) kullanır.
3. Başlangıçta StorSimple, verileri cihazın hızlı SSD katmanında depolar.
4. SSD katmanı kapasiteye yaklaşırsa, StorSimple yinelenenleri kaldırır ve en eski veri bloklarını sıkıştırır ve bunları HDD katmanına taşıır.
5. HDD katmanı kapasiteye yaklaşırsa, StorSimple en eski veri bloklarını şifreler ve HTTPS aracılığıyla Microsoft Azure depolama hesabına güvenli bir şekilde gönderir.
6. Microsoft Azure, veri merkezinde ve uzak bir veri merkezinde verilerin birden çok kopyasını oluşturarak bir olağanüstü durum oluşursa verilerin kurtarılabilmesini sağlar.
7. Dosya sunucusu bulutta depolanan verileri istediğinde, StorSimple onu sorunsuzca geri döndürür ve StorSimple cihazının SSD katmanında bir kopyasını depolar.

#### <a name="how-storsimple-manages-cloud-data"></a>StorSimple bulut verilerini nasıl yönetir

StorSimple, müşteri verilerini tüm anlık görüntüler ve birincil veriler (konaklar tarafından yazılan veriler) genelinde çoğaltır. Yinelenenleri kaldırma, depolama verimliliği için harika olsa da, "bulutta olanlar" sorusunu karmaşıklaşuyor. Katmanlı birincil veriler ve anlık görüntü verileri birbirleriyle çakışıyor. Buluttaki tek bir veri yığını katmanlı birincil veri olarak kullanılabilir ve ayrıca birkaç anlık görüntü tarafından başvurulmalıdır. Her bulut anlık görüntüsü, anlık görüntü silinene kadar tüm zaman içindeki verilerin bir kopyasının buluta kilitlenmesini sağlar.

Veriler yalnızca bu verilere başvuru olmadığında buluttan silinir. Örneğin, StorSimple cihazında bulunan tüm verilerin bir bulut anlık görüntüsünü alırız ve sonra bazı birincil verileri silerseniz, _birincil veri_ bırakması hemen görüyoruz. Katmanlı verileri ve yedeklemeleri içeren _bulut verileri_ aynı kalır. Bunun nedeni, bir anlık görüntünün bulut verilerine başvurmaya devam etmektedir. Bulut anlık görüntüsü silindikten sonra (ve aynı verilere başvuran diğer bir anlık görüntü), bulut tüketimi düşer. Bulut verilerini kaldırmadan önce, hiçbir anlık görüntü hala bu verilere başvurulacağını denetliyoruz. Bu işleme _çöp toplama_ adı verilir ve cihazda çalışan bir arka plan hizmetidir. Çöp toplama hizmeti silinmeden önce bu verilere yönelik diğer başvuruları denetlediğinde, bulut verilerinin kaldırılması anında çalışmaz. Çöp toplamanın hızı, toplam anlık görüntü sayısına ve toplam veriye bağlıdır. Genellikle, bulut verileri bir haftadan daha az bir sürede temizlenir.


### <a name="thin-provisioning"></a>Ölçülü kaynak sağlama
Ölçülü kaynak sağlama, kullanılabilir depolamanın fiziksel kaynakları aşacak şekilde göründüğü bir sanallaştırma teknolojisidir. StorSimple, önceden yeterli depolama alanı ayırmak yerine, geçerli gereksinimleri karşılamak için yeterli alan ayırmak üzere ölçülü kaynak sağlama kullanır. Bu yaklaşım, StorSimple tarafından değişiklik taleplerini karşılamak üzere bulut depolamasını artırabilir veya azaltababileceğinden, bulut depolamanın esnek yapısı bu yaklaşımı kolaylaştırır.

> [!NOTE]
> Yerel olarak sabitlenmiş birimler ölçülü kaynak olarak sağlanmamıştır. Yalnızca yerel bir birime ayrılan depolama alanı, birim oluşturulduğunda tamamen sağlanır.


### <a name="deduplication-and-compression"></a>Yinelenenleri kaldırma ve sıkıştırma
Microsoft Azure StorSimple, depolama gereksinimlerini azaltmak için Yinelenenleri kaldırma ve veri sıkıştırmayı kullanır.

Yinelenenleri kaldırma, depolanan veri kümesindeki yedekliliği ortadan kaldırarak depolanan toplam veri miktarını azaltır. Bilgi değişiklikleri olarak, StorSimple değişmeyen verileri yoksayar ve yalnızca değişiklikleri yakalar. Ayrıca, StorSimple, gereksiz bilgileri tanımlayarak ve kaldırarak depolanan verilerin miktarını azaltır. 

> [!NOTE]
> Yerel olarak sabitlenmiş birimlerdeki veriler çoğaltılamaz veya sıkıştırılmaz. Ancak, yerel olarak sabitlenmiş birimlerin yedeklemeleri yinelenenleri kaldırılır ve sıkıştırılır.


## <a name="storsimple-workload-summary"></a>StorSimple iş yükü Özeti
Desteklenen StorSimple iş yüklerinin özeti aşağıda verilmiştir.

| Senaryo | İş yükü | Desteklenen | Kısıtlamalar | Version |
| --- | --- | --- | --- | --- |
| İş Birliği |Dosya paylaşımı |Evet | |Tüm sürümler |
| İş Birliği |Dağıtılmış dosya paylaşımı |Evet | |Tüm sürümler |
| İş Birliği |SharePoint |Evet* |Yalnızca yerel olarak sabitlenmiş birimlerle desteklenir |Güncelleştirme 2 ve üzeri |
| Arşivleme |Basit dosya arşivleme |Evet | |Tüm sürümler |
| Sanallaştırma |Sanal makineler |Evet* |Yalnızca yerel olarak sabitlenmiş birimlerle desteklenir |Güncelleştirme 2 ve üzeri |
| Database |SQL |Evet* |Yalnızca yerel olarak sabitlenmiş birimlerle desteklenir |Güncelleştirme 2 ve üzeri |
| Video gözetimi |Video gözetimi |Evet* |StorSimple cihazı yalnızca bu iş yüküne ayrılmışsa desteklenir |Güncelleştirme 2 ve üzeri |
| Yedekle |Birincil hedef yedekleme |Evet* |StorSimple cihazı yalnızca bu iş yüküne ayrılmışsa desteklenir |Güncelleştirme 3 ve üzeri |
| Yedekle |İkincil hedef yedekleme |Evet* |StorSimple cihazı yalnızca bu iş yüküne ayrılmışsa desteklenir |Güncelleştirme 3 ve üzeri |

*Evet&#42; -çözüm yönergeleri ve kısıtlamaları uygulanmalıdır.*

Aşağıdaki iş yükleri StorSimple 8000 serisi cihazlar tarafından desteklenmez. StorSimple üzerinde dağıtılmışsa, bu iş yükleri desteklenmeyen bir yapılandırmaya neden olur.

* Tıp görüntüleme
* Exchange
* VDI
* Oracle
* SAP
* Büyük veri
* İçerik dağıtımı
* SCSI 'den önyükleme

Aşağıda, StorSimple desteklenen altyapı bileşenlerinin bir listesi verilmiştir.

| Senaryo | İş yükü | Desteklenen | Kısıtlamalar | Version |
| --- | --- | --- | --- | --- |
| Genel |Express Route |Evet | |Tüm sürümler |
| Genel |DataCore FC |Evet* |DataCore Sansimphony ile desteklenir |Tüm sürümler |
| Genel |YOKLA |Evet* |Yalnızca yerel olarak sabitlenmiş birimlerle desteklenir |Tüm sürümler |
| Genel |Dizinleme |Evet* |Katmanlı birimlerde yalnızca meta veri dizinleme desteklenir (veri yok).<br>Yerel olarak sabitlenmiş birimlerde, tam dizin oluşturma desteklenir. |Tüm sürümler |
| Genel |Virüsten koruma |Evet* |Katmanlı birimlerde yalnızca açma ve kapatma üzerinde tarama desteklenir.<br> Yerel olarak sabitlenmiş birimler için tam tarama desteklenir. |Tüm sürümler |

*Evet&#42; -çözüm yönergeleri ve kısıtlamaları uygulanmalıdır.*

Aşağıda, StorSimple ile çözüm oluşturmak için kullanılan diğer yazılımların bir listesi verilmiştir.

| İş yükü türü | StorSimple ile kullanılan yazılım | Desteklenen sürümler|Çözüm kılavuzuna bağlantı| 
| --- | --- | --- | --- |
| Yedekleme hedefi |Veeam |Veeab v 9 ve üzeri |[Veahar ile yedekleme hedefi olarak StorSimple](storsimple-configure-backup-target-veeam.md)|
| Yedekleme hedefi |VERITAS Backup Exec |Backup Exec 16 ve üzeri |[Backup Exec ile bir yedekleme hedefi olarak StorSimple](storsimple-configure-backup-target-using-backup-exec.md)|
| Yedekleme hedefi |VERITAS NetBackup |NetBackup 7.7. x ve üzeri  |[NetBackup ile bir yedekleme hedefi olarak StorSimple](storsimple-configure-backuptarget-netbackup.md)|
| Genel dosya paylaşımı <br></br> İş Birliği |Talon  |[Talon ile StorSimple](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple terminolojisi
Microsoft Azure StorSimple çözümünüzü dağıtmaya başlamadan önce, aşağıdaki hüküm ve tanımları incelemenizi öneririz.

### <a name="key-terms-and-definitions"></a>Anahtar hüküm ve tanımlar
| Terim (kısaltma veya kısaltma) | Açıklama |
| --- | --- |
| erişim denetimi kaydı (ACR) |Microsoft Azure StorSimple cihazındaki bir birimle ilişkili bir kayıt, hangi konaklara bağlanamayacağını belirler. Belirleme, StorSimple cihazınıza bağlanan ana bilgisayarların (ACR 'de bulunan) Iscsı tam adını (ıQN) temel alır. |
| AES-256 |Verileri buluta ve buluta taşırken verileri şifrelemek için 256 bitlik bir Gelişmiş Şifreleme Standardı (AES) algoritması. |
| ayırma birimi boyutu (Avustralya) |Windows dosya sistemlerinizde bir dosyayı tutmak için ayrılabilen en küçük disk alanı miktarı. Bir dosya boyutu küme boyutunun hatta daha fazla değilse, dosyanın (küme boyutunun bir sonraki katı kadar) saklanması için ek alan kullanılması gerekir. Bu, sabit diskin kayıp ve parçalanmasına neden olur. <br>Azure StorSimple birimlerine yönelik önerilen Avustralya, yinelenenleri kaldırma algoritmalarıyla iyi çalıştığından 64 KB 'dir. |
| Otomatik depolama katmanlama |SSD 'lerden HDD 'ler ve ardından buluttaki bir katmana daha az etkin olan verileri otomatik olarak taşıma ve sonra bir merkezi kullanıcı arabiriminden tüm depolamanın yönetimini etkinleştirme. |
| Yedekleme kataloğu |Genellikle kullanılan uygulama türü ile ilgili olan yedeklemeler koleksiyonu. Bu koleksiyon, StorSimple Aygıt Yöneticisi hizmeti kullanıcı arabiriminin yedekleme kataloğu dikey penceresinde görüntülenir. |
| Katalog dosyasını Yedekle |Şu anda StorSimple Snapshot Manager yedekleme veritabanında depolanan kullanılabilir anlık görüntülerin listesini içeren dosya. |
| Yedekleme ilkesi |Birimler, yedekleme türü ve önceden tanımlanmış bir zamanlamaya göre yedeklemeler oluşturmanıza olanak sağlayan bir Timetable seçimi. |
| ikili büyük nesneler (blob 'Lar) |Bir veritabanı yönetim sisteminde tek bir varlık olarak depolanan ikili veri koleksiyonu. Blob 'Lar genellikle, bazen ikili yürütülebilir kod bir BLOB olarak depolansa da resimler, ses veya diğer çoklu ortam nesneleridir. |
| Çekişme El Sıkışma Kimlik Doğrulama Protokolü (CHAP) |Bir parola veya gizli anahtarı paylaşan eşe bağlı olarak bir bağlantının eşi kimliğini doğrulamak için kullanılan bir protokol. CHAP tek yönlü veya karşılıklı olabilir. Tek yönlü CHAP ile hedef, bir başlatıcısının kimliğini doğrular. Karşılıklı CHAP, hedefin başlatıcıyı doğrulamasını ve başlatıcısının hedefin kimliğini doğrulamasını gerektirir. |
| oluşturulacak |Birimin yinelenen bir kopyası. |
| Katman olarak bulut (CaaT) |Depolama mimarisi içindeki bir katman olarak tümleşik bulut depolaması, tüm depolamanın bir kurumsal depolama ağının parçası gibi görünmesini sağlar. |
| bulut hizmeti sağlayıcısı (CSP) |Bulut bilgi işlem hizmetleri sağlayıcısı. |
| bulut anlık görüntüsü |Bulutta depolanan birim verilerinin bir zaman içindeki bir kopyası. Bulut anlık görüntüsü, farklı, site dışı bir depolama sisteminde çoğaltılan bir anlık görüntüye eşdeğerdir. Bulut anlık görüntüleri özellikle olağanüstü durum kurtarma senaryolarında yararlıdır. |
| bulut depolama şifreleme anahtarı |Cihazınız tarafından buluta gönderilen şifrelenmiş verilere erişmek için StorSimple cihazınız tarafından kullanılan bir parola veya anahtar. |
| küme durumunu algılayan güncelleştirme |Güncelleştirmelerin en az hizmet kullanılabilirliğine sahip olması veya hiçbir etkisi olmaması için bir yük devretme kümesindeki sunucularda yazılım güncelleştirmelerini yönetme. |
| veri yolu |Birbirine bağlı veri işleme işlemlerini gerçekleştiren işlevsel birimlerin bir koleksiyonu. |
| devre dışı bırak |StorSimple cihazı ile ilişkili bulut hizmeti arasındaki bağlantıyı kesen kalıcı bir eylem. Cihazın bulut anlık görüntüleri bu işlemden sonra kalır ve, olağanüstü durum kurtarma için kopyalanabilir veya kullanılabilir. |
| disk yansıtma |Sürekli kullanılabilirlik sağlamak için, mantıksal disk birimlerinin gerçek zamanlı olarak ayrı sabit sürücülere çoğaltılması. |
| dinamik disk yansıtma |Dinamik disklerde mantıksal disk birimlerinin çoğaltılması. |
| Dinamik diskler |Birden çok fiziksel diskte verileri depolamak ve yönetmek için mantıksal disk Yöneticisi 'Ni (LDM) kullanan bir disk birimi biçimi. Dinamik diskler, daha fazla boş alan sağlamak için büyütülür. |
| Uzatılmış diskler (EBOD) Kasası |Ek depolama için ek sabit sürücü diskleri içeren Microsoft Azure StorSimple cihazınızın ikincil bir Kasası. |
| FAT sağlama |Beklenen gereksinimlere göre ayrılan depolama alanının (ve genellikle geçerli gereksiniminin ötesinde) ayrıldığı geleneksel bir depolama sağlama. Ayrıca bkz. *ölçülü kaynak sağlama*. |
| sabit disk sürücüsü (HDD) |Verileri depolamak için döndürme platformlarını kullanan bir sürücü. |
| karma bulut depolaması |Bulut depolama da dahil olmak üzere yerel ve dışı Site kaynakları kullanan bir depolama mimarisi. |
| Internet küçük bilgisayar sistemi arabirimi (Iscsı) |Veri depolama donanımını veya tesislerini bağlamak için Internet Protokolü (IP) tabanlı bir depolama ağı standardı. |
| Iscsı Başlatıcısı |Windows çalıştıran bir konak bilgisayarın bir dış Iscsı tabanlı depolama ağına bağlanmasını sağlayan bir yazılım bileşeni. |
| Iscsı nitelenmiş adı (ıQN) |Bir Iscsı hedefini veya başlatıcısını tanımlayan benzersiz bir ad. |
| Iscsı hedefi |Depolama alanı ağlarında merkezi Iscsı disk alt sistemleri sağlayan bir yazılım bileşeni. |
| Canlı arşivleme |Arşiv verilerine her zaman erişilebilen bir depolama yaklaşımı (örneğin, bantta yerinde depolanmaz). Microsoft Azure StorSimple, canlı arşivlemeyi kullanır. |
| Yerel olarak sabitlenmiş birim |cihazda bulunan ve hiçbir şekilde buluta katmanlı bir birim. |
| Yerel anlık görüntü |Microsoft Azure StorSimple cihazında depolanan birim verilerinin zaman içinde bir kopyası. |
| Microsoft Azure StorSimple |Veri merkezi depolama gereci ve yazılımlarından oluşan ve BT kuruluşların, veri merkezi depolama gibi bulut depolamadan yararlanmasını sağlayan güçlü bir çözümdür. StorSimple, maliyetleri azaltırken veri koruma ve veri yönetimini basitleştirir. Çözüm, bulut ile sorunsuz tümleştirme aracılığıyla birincil depolama, arşivleme, yedekleme ve olağanüstü durum kurtarma (DR) birleştirir. StorSimple cihazları, SAN depolama ve bulut veri yönetimini kurumsal sınıf bir platformda birleştirerek, depolama ile ilgili tüm gereksinimler için hız, kolaylık ve güvenilirliği etkinleştirir. |
| Güç ve soğutma modülü (PCM) |StorSimple cihazınızın güç kaynakları ve soğutma fanı 'ndan oluşan donanım bileşenleri, bu nedenle güç ve soğutma modülü adı. Cihazın birincil Kasası iki adet 764W PCMs 'e sahiptir, ancak EBOD Kasası iki 580W PCMs içerir. |
| birincil kutu |Uygulama platformu denetleyicilerini içeren StorSimple cihazınızın ana Kasası. |
| Kurtarma süresi hedefi (RTO) |Bir iş süreci veya sistem bir olağanüstü durum sonrasında tamamen geri yüklenmeden önce kullanılması gereken en uzun süre. |
| seri bağlı SCSI (SAS) |Bir sabit disk sürücüsü (HDD) türü. |
| hizmet verileri şifreleme anahtarı |StorSimple Aygıt Yöneticisi hizmeti ile kaydolduktan sonra yeni bir StorSimple cihazı için kullanılabilir hale getirilen bir anahtar. StorSimple Aygıt Yöneticisi hizmeti ile cihaz arasında aktarılan yapılandırma verileri ortak anahtar kullanılarak şifrelenir ve daha sonra yalnızca bir özel anahtar kullanılarak cihazda şifresi çözülür. Hizmet veri şifreleme anahtarı, hizmetin şifre çözme için bu özel anahtarı almasına izin verir. |
| hizmet kayıt anahtarı |StorSimple cihazını StorSimple Aygıt Yöneticisi hizmetine kaydetmeye yardımcı olan bir anahtar, daha fazla yönetim eylemi için Azure portal görünür. |
| Küçük bilgisayar sistemi arabirimi (SCSI) |Bilgisayarların fiziksel olarak bağlanması ve aralarında veri geçirilmesi için bir standartlar kümesi. |
| katı hal sürücüsü (SSD) |Hareketli parçalar içeren bir disk; Örneğin, bir flash sürücü. |
| depolama hesabı |Belirli bir bulut hizmeti sağlayıcısı için depolama hesabınıza bağlı erişim kimlik bilgileri kümesi. |
| SharePoint için StorSimple Bağdaştırıcısı |StorSimple depolama ve veri korumayı SharePoint Server gruplarına şeffaf bir şekilde genişleten Microsoft Azure StorSimple bileşen. |
| StorSimple Cihaz Yöneticisi hizmeti |Azure StorSimple şirket içi ve sanal cihazlarınızı yönetmenizi sağlayan Azure portal uzantısı. |
| StorSimple Snapshot Manager |Microsoft Azure StorSimple 'de yedekleme ve geri yükleme işlemlerini yönetmek için bir Microsoft Yönetim Konsolu (MMC) ek bileşeni. |
| yedek al |Kullanıcının bir birimin etkileşimli yedeklemesini almasına izin veren bir özellik. Bu, bir birimin el ile yedeklenmesini, tanımlı bir ilke aracılığıyla otomatikleştirilmiş bir yedekleme yerine getirmenin alternatif bir yoludur. |
| ölçülü kaynak sağlama |Depolama sistemlerinde kullanılabilir depolama alanının kullanıldığı verimliliği en iyi duruma getirme yöntemi. Ölçülü kaynak sağlama bölümünde, depolama alanı, her bir kullanıcının belirli bir zamanda gereken minimum alana göre birden çok kullanıcı arasında ayrılır. Ayrıca bkz. *FAT sağlama*. |
| katmanlama |Geçerli kullanım, yaş ve diğer verilerle ilişki temelinde mantıksal gruplamalarda verileri düzenleme. StorSimple, verileri katmanlardaki otomatik olarak düzenler. |
| birim |Sürücü biçiminde sunulan mantıksal depolama alanı. StorSimple birimleri, Iscsı ve StorSimple cihazı kullanılarak bulunanlar da dahil olmak üzere, ana bilgisayar tarafından bağlanan birimlere karşılık gelir. |
| birim kapsayıcısı |Birimler ve bunlara uygulanan ayarlar gruplandırması. StorSimple cihazınızdaki tüm birimler birim kapsayıcılarıyla gruplandırılır. Birim kapsayıcısı ayarları, depolama hesapları, ilişkili şifreleme anahtarları ile buluta gönderilen verilerin şifreleme ayarlarını ve bulutu ilgilendiren işlemler için kullanılan bant genişliğini içerir. |
| birim grubu |StorSimple Snapshot Manager, bir birim grubu, yedekleme işlemesini kolaylaştırmak için yapılandırılmış bir birim koleksiyonudur. |
| Birim Gölge Kopyası Hizmeti (VSS) |Artımlı anlık görüntülerin oluşturulmasını koordine etmek üzere VSS kullanan uygulamalarla iletişim kurarak uygulama tutarlılığını kolaylaştıran bir Windows Server işletim sistemi hizmeti. VSS, anlık görüntüler çekilirken uygulamaların geçici olarak devre dışı olmasını sağlar. |
| StorSimple için Windows PowerShell |StorSimple cihazınızı çalıştırmak ve yönetmek için kullanılan Windows PowerShell tabanlı bir komut satırı arabirimi. Windows PowerShell 'in bazı temel özelliklerini koruyarak, bu arabirimin StorSimple cihazını yönetmek için gereken ek adanmış cmdlet 'ler vardır. |

## <a name="next-steps"></a>Sonraki adımlar
[StorSimple güvenliği](storsimple-8000-security.md)hakkında bilgi edinin.

