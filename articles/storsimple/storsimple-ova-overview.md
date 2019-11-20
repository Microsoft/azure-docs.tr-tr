---
title: Microsoft Azure StorSimple Sanal diziye genel bakış | Microsoft Docs
description: Bir şirket içi sanal dizi ve bulut depolama Microsoft Azure arasındaki depolama görevlerini yöneten tümleşik bir depolama çözümü olan StorSimple Sanal dizisini açıklar.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: cbfc69c7d8d9354ae4e727b9eb0180583165abab
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516701"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>StorSimple Sanal dizisine giriş

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Genel Bakış

Microsoft Azure StorSimple Sanal dizisi, Hiper yöneticide çalışan bir şirket içi sanal dizi ve bulut depolama Microsoft Azure arasındaki depolama görevlerini yöneten tümleşik bir depolama çözümüdür. Sanal dizi, kurumsal depolama ve veri korumasıyla ilişkili birçok sorunu ve gideri ortadan kaldıran verimli, ekonomik ve kolayca yönetilen bir dosya sunucusu veya Iscsı sunucu çözümüdür. Sanal dizi, seyrek erişilen arşiv verilerinin depolanması için özellikle uygun bir seçenektir.

Bu makalede, sanal diziye ilişkin bir genel bakış sağlanır. diğer kaynaklar aşağıda verilmiştir:

* En iyi uygulamalar için bkz. [StorSimple Sanal dizi en iyi uygulamaları](storsimple-ova-best-practices.md).
* StorSimple 8000 serisi cihazlara genel bir bakış için, [storsimple 8000 serisi: hibrit bulut çözümü](storsimple-overview.md)' ne gidin.
* StorSimple 5000/7000 Serisi cihazlar hakkında daha fazla bilgi için, [StorSimple çevrimiçi yardım](http://onlinehelp.storsimple.com/)'a gidin.

Sanal dizi, Iscsı veya sunucu Ileti bloğu (SMB) protokolünü destekler. Mevcut hiper yönetici altyapınız üzerinde çalışır ve bulut, bulut yedeklemesi, hızlı geri yükleme, öğe düzeyinde kurtarma ve olağanüstü durum kurtarma özelliklerine katmanlama sağlar.

Aşağıdaki tabloda, StorSimple Sanal dizisinin önemli özellikleri özetlenmektedir.

| Özellik | StorSimple Sanal Dizisi |
| --- | --- |
| Yükleme gereksinimleri |Sanallaştırma altyapısını (Hyper-V veya VMware) kullanır |
| Kullanılabilirlik |Tek düğüm |
| Toplam Kapasite (bulut dahil) |Sanal dizi başına 64 TB 'a kadar kullanılabilir kapasite |
| Yerel kapasite |Sanal dizi başına 390 GB ila 6,4 TB kullanılabilir kapasite (500 GB-8 TB disk alanı sağlamanız gerekir) |
| Yerel protokoller |Iscsı veya SMB |
| Kurtarma süresi hedefi (RTO) |Iscsı: boyuttan bağımsız olarak 2 dakikadan az |
| Kurtarma noktası hedefi (RPO) |Günlük yedeklemeler ve isteğe bağlı yedeklemeler |
| Depolama katmanlama |Hangi verilerin katmanlı veya dışarı olduğunu belirlemek için ısı eşlemesini kullanır |
| Destek |Tedarikçi tarafından desteklenen sanallaştırma altyapısı |
| Performans |Temel altyapıya bağlı olarak farklılık gösterir |
| Veri taşınabilirliği |Aynı cihaza geri yükleyebilir veya öğe düzeyinde kurtarma yapabilir (dosya sunucusu) |
| Depolama katmanları |Yerel hiper yönetici depolaması ve bulutu |
| Paylaşma boyutu |Katmanlı: 20 TB 'a kadar; Yerel olarak sabitlenmiş: 2 TB 'a kadar |
| Birim boyutu |Bağlantılı 500 GB ila 5 TB; Yerel olarak sabitlenmiş: 50 GB ila 200 GB <br> Katmanlı birimler için maksimum yerel ayırma 200 GB 'dir. |
| Anlık Görüntüler |Kilitlenmeyle tutarlı |
| Öğe düzeyinde kurtarma |Yes Kullanıcılar paylaşımların içinden geri yükleyebilir |

## <a name="why-use-storsimple"></a>StorSimple neden kullanılmalıdır?

StorSimple, Kullanıcı ve sunucuları, uygulama değişikliği olmadan dakikalar içinde Azure depolama 'ya bağlar.

Aşağıdaki tabloda, StorSimple Sanal dizi çözümünün sağladığı bazı önemli avantajlar açıklanmaktadır.

| Özellik | Avantaj |
| --- | --- |
| Saydam tümleştirme |Sanal dizi, Iscsı veya SMB protokolünü destekler. Yerel katman ile bulut katmanı arasındaki veri taşıma işlemi sorunsuz ve Kullanıcı için saydamdır. |
| Azaltılan depolama maliyetleri |StorSimple sayesinde, en çok kullanılan etkin verilerin geçerli taleplerini karşılamak için yeterli yerel depolama alanı temin edersiniz. Depolama alanı büyüdükçe StorSimple katmanları, verileri düşük maliyetli bulut depolamaya dönüştürür. Depolama gereksinimlerini ve harcamasını daha fazla azaltmak için veriler buluta gönderilmeden önce çoğaltılır ve sıkıştırılır. |
| Basitleştirilmiş depolama yönetimi |StorSimple, birden çok cihazı yönetmek için StorSimple Aygıt Yöneticisi kullanarak bulutta merkezi yönetim sağlar. |
| İyileştirilmiş olağanüstü durum kurtarma ve uyumluluk |StorSimple, meta verileri hemen geri yükleyerek ve verileri gerektiği şekilde geri yükleyerek daha hızlı olağanüstü durum kurtarmayı kolaylaştırır. Bu, normal işlemlerin minimum kesintile devam edebileceği anlamına gelir. |
| Veri taşınabilirliği |Buluta veri katmanlı verilere, kurtarma ve geçiş amacıyla diğer sitelerden erişilebilir. Verileri yalnızca özgün sanal diziye geri yükleyebilirsiniz. Ancak, tüm sanal diziyi başka bir sanal diziye geri yüklemek için olağanüstü durum kurtarma özelliklerini kullanırsınız. |

## <a name="storsimple-workload-summary"></a>StorSimple iş yükü Özeti

Desteklenen StorSimple iş yüklerinin özeti aşağıda verilmiştir.

|Senaryo     |İş yükü     |Desteklenen      |Kısıtlamalar               | Uygulanabilir sürümler|
|-------------|-------------|---------------|---------------------------|--------------------|
|Uzak Ofis/Şube ofisi (ROBO)  |Dosya paylaşımı     |Evet      |Bkz. [dosya sunucusu için en fazla sınır](storsimple-ova-limits.md).<br></br>[Desteklenen SMB sürümleri için sistem gereksinimleri](storsimple-ova-system-requirements.md)bölümüne bakın.| Tüm sürümler     |
|Bulut arşivleme  |Arşiv dosya paylaşımı     |Evet      |Bkz. [dosya sunucusu için en fazla sınır](storsimple-ova-limits.md).<br></br>[Desteklenen SMB sürümleri için sistem gereksinimleri](storsimple-ova-system-requirements.md)bölümüne bakın.| Tüm sürümler     |

StorSimple Sanal dizisi, seyrek erişilen veriler için idealdir. Sanal dizinin performansı artırmak için yerel bir önbelleği olsa da, kullanıcılar cihaz Hizmetleri dosyalarını en düşük depolama katmanında (bulut) varsaymalıdır. Her sanal dizi, yaklaşık 100 Mbps hızında Azure Storage yazıp okuyabilir. Bu bağlantı, cihaza gelen tüm istekler arasında paylaşılır ve aşağıdaki diyagramda gösterildiği gibi performans sorunu olabilir.

![Bulut arşivleme](./media/storsimple-ova-overview/cloud-archiving.png)

Birden çok eşzamanlı kullanıcı sanal diziye erişebildiklerinde, hepsi Azure ile bağlantıyı daha düşük bir performansla paylaşır. Kullanıcı başına garantili performans yoktur ve cihaz, geldikçe istekleri tek tek işler.

StorSimple Sanal dizisi, yüksek kullanılabilirlik gerektiren iş yükleri için uygun değil. Sanal dizi, yazılım güncelleştirmeleri yüklendiğinde kapalı kalma süresi yaşadığında tek düğümlü bir cihazdır. Yöneticiler, her yıl 30 dakikalık 3-4 kez bir bakım penceresi planlıyor olmalıdır.

## <a name="workflows"></a>Workflows

StorSimple Sanal dizisi, özellikle aşağıdaki iş akışları için uygundur:

* [Bulut tabanlı depolama yönetimi](#cloud-based-storage-management)
* [Konuma bağımsız yedekleme](#location-independent-backup)
* [Veri koruma ve olağanüstü durum kurtarma](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Bulut tabanlı depolama yönetimi
Birden çok cihazda ve birden çok konumda depolanan verileri yönetmek için Azure portal çalıştıran StorSimple Aygıt Yöneticisi hizmetini kullanabilirsiniz. Bu, özellikle dağıtılmış dal senaryolarında yararlı olur. Sanal dizileri ve fiziksel StorSimple cihazlarını yönetmek için StorSimple Aygıt Yöneticisi hizmetinin ayrı örneklerini oluşturmanız gerektiğini unutmayın. Ayrıca, sanal dizinin artık klasik Azure portalı yerine yeni Azure portal kullandığını unutmayın.

![bulut tabanlı depolama yönetimi](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Konuma bağımsız yedekleme
Sanal dizi ile, bulut anlık görüntüleri bir birimin veya paylaşımın konumdan bağımsız, bir zaman noktası kopyasını sağlar. Bulut anlık görüntüleri varsayılan olarak etkindir ve devre dışı bırakılamaz. Tüm birimler ve paylaşımlar, tek bir günlük yedekleme ilkesiyle aynı anda yedeklenir ve gerektiğinde ek yedekleme işlemleri gerçekleştirebilirsiniz.

### <a name="data-protection-and-disaster-recovery"></a>Veri koruma ve olağanüstü durum kurtarma
Sanal dizi, aşağıdaki veri koruma ve olağanüstü durum kurtarma senaryolarını destekler:

* **Birim veya paylaşma geri yükleme** – bir birimi veya payı kurtarmak için geri yüklemeyi yeni iş akışı olarak kullanın. Tüm birimi veya payı kurtarmak için bu yaklaşımı kullanın.
* **Öğe düzeyinde kurtarma** – paylaşımlar son yedeklemelere Basitleştirilmiş erişime izin verir. Bulutta bulunan özel bir *. Backup* klasöründen, tek bir dosyayı kolayca kurtarabilirsiniz. Bu geri yükleme özelliği, Kullanıcı odaklı ve yönetim müdahalesi gerekli değildir.
* **Olağanüstü durum kurtarma** – tüm birimleri veya paylaşımları yeni bir sanal diziye kurtarmak için yük devretme özelliğini kullanın. Yeni sanal diziyi oluşturup StorSimple Aygıt Yöneticisi hizmetine kaydedersiniz, sonra özgün sanal dizinin yükünü devreder. Yeni sanal dizi, daha sonra sağlanan kaynakları varsayacaktır.

## <a name="storsimple-virtual-array-components"></a>StorSimple Sanal dizi bileşenleri

Sanal dizi aşağıdaki bileşenleri içerir:

* [Sanal dizi](#virtual-array) : sanallaştırılmış ortamınızda veya Hiper yöneticide sağlanan bir sanal makineyi temel alan karma bulut depolama cihazı.
* [Storsimple Aygıt Yöneticisi hizmeti](#storsimple-device-manager-service) : bir veya daha fazla StorSimple cihazını farklı coğrafi konumlardan erişebileceğiniz tek bir web arabiriminden yönetmenizi sağlayan bir Azure Portal uzantısı. StorSimple Aygıt Yöneticisi hizmetini kullanarak hizmetler oluşturabilir ve yönetebilir, cihazları ve uyarıları görüntüleyebilir ve yönetebilir, birimleri, paylaşımları ve mevcut anlık görüntüleri yönetebilirsiniz.
* [Yerel Web Kullanıcı arabirimi](#local-web-user-interface) – yerel ağa bağlanabilmeleri için cihazı yapılandırmak için kullanılan Web tabanlı bir kullanıcı arabirimi ve ardından cihazı storsimple Aygıt Yöneticisi hizmetine kaydedin. 
* [Komut satırı arabirimi](#command-line-interface) – sanal dizide bir destek oturumu başlatmak için kullanabileceğiniz bir Windows PowerShell arabirimi.
  Aşağıdaki bölümlerde, bu bileşenlerin her biri daha ayrıntılı bir şekilde açıklanmıştır ve çözümün verileri nasıl tartışdığını, depolama alanını ayırdığını ve depolama yönetimi ile veri korumasını nasıl kolaylaştırdığını açıklayın.

### <a name="virtual-array"></a>Sanal dizi

Sanal dizi, birincil depolama sağlayan tek düğümlü bir depolama çözümüdür, bulut depolamayla iletişimi yönetir ve cihazda depolanan tüm verilerin güvenliğini ve gizliliğini sağlamaya yardımcı olur.

Sanal dizi, indirme için kullanılabilen bir modelde kullanılabilir. Sanal dizinin, cihazda en fazla 6,4 TB kapasitesi vardır (temeldeki depolama gereksinimi 8 TB) ve bulut depolaması dahil 64 TB.

Sanal dizi aşağıdaki özelliklere sahiptir:

* Maliyet açısından etkilidir. Mevcut sanallaştırma altyapınızın kullanımını sağlar ve var olan Hyper-V veya VMware hiper Yöneticinizdeki dağıtılabilir.
* Veri merkezinde bulunur ve bir Iscsı sunucusu ya da dosya sunucusu olarak yapılandırılabilir.
* Bulutla tümleşiktir.
* Yedeklemeler bulutta depolanır, bu, olağanüstü durum kurtarmayı kolaylaştırabilir ve öğe düzeyinde kurtarmayı (ıLR) basitleştirir.
* Fiziksel bir cihaza uyguladığınız gibi, güncelleştirmeleri sanal diziye uygulayabilirsiniz.

> [!NOTE]
> Bir sanal dizi genişletilemiyor. Bu nedenle, sanal diziyi oluştururken yeterli depolama alanı sağlanması önemlidir.

### <a name="storsimple-device-manager-service"></a>StorSimple Cihaz Yöneticisi hizmeti

Microsoft Azure StorSimple, StorSimple depolama alanını merkezi olarak yönetmenize olanak sağlayan, StorSimple Aygıt Yöneticisi hizmeti olan Web tabanlı bir kullanıcı arabirimi sağlar. StorSimple Aygıt Yöneticisi hizmetini kullanarak aşağıdaki görevleri gerçekleştirebilirsiniz:

* Tek bir hizmetten birden çok StorSimple Sanal dizilerini yönetin.
* StorSimple Sanal dizileri için güvenlik ayarlarını yapılandırın ve yönetin. (Bulutta şifreleme Microsoft Azure API 'Lerine bağımlıdır.)
* Depolama hesabı kimlik bilgilerini ve özelliklerini yapılandırın.
* Birimleri veya paylaşımları yapılandırın ve yönetin.
* Birimlerde veya paylaşımlardaki verileri yedekleyin ve geri yükleyin.
* Performansı izleyin.
* Sistem ayarlarını gözden geçirin ve olası sorunları tespit edin.

StorSimple Aygıt Yöneticisi hizmetini kullanarak sanal diziniz için günlük yönetim gerçekleştirebilirsiniz.

Daha fazla bilgi için, StorSimple [cihazınızı yönetmek Için storsimple Aygıt Yöneticisi hizmetini kullanma](storsimple-virtual-array-manager-service-administration.md)bölümüne gidin.

### <a name="local-web-user-interface"></a>Yerel Web Kullanıcı arabirimi

Sanal dizi, StorSimple Aygıt Yöneticisi hizmetiyle cihazın bir kerelik yapılandırma ve kayıt için kullanılan Web tabanlı bir kullanıcı arabirimi içerir. Bu hizmeti kullanarak sanal diziyi kapatabilir, yeniden başlatabilir, tanılama testlerini çalıştırabilir, yazılım güncelleştirebilir, cihaz yönetici parolasını değiştirebilir, sistem günlüklerini görüntüleyebilir, bir hizmet isteği dosya dosyasına Microsoft Desteği başvurabilirsiniz.

Web tabanlı kullanıcı arabirimini kullanma hakkında daha fazla bilgi için, [StorSimple Sanal dizinizi yönetmek üzere Web tabanlı kullanıcı arabirimini kullanma](storsimple-ova-web-ui-admin.md)bölümüne gidin.

### <a name="command-line-interface"></a>Komut satırı arabirimi

Dahil edilen Windows PowerShell arabirimi, sanal dizmenizde karşılaşabileceğiniz sorunları gidermenize ve çözmenize yardımcı olmak için Microsoft Desteği bir destek oturumu başlatabilmenizi sağlar.

## <a name="storage-management-technologies"></a>Depolama yönetimi teknolojileri

StorSimple çözümü, sanal diziye ve diğer bileşenlere ek olarak, önemli verilere hızlı erişim sağlamak, depolama tüketimini azaltmak ve sanal dizinizdeki depolanan verileri korumak için aşağıdaki yazılım teknolojilerini kullanır:

* [Otomatik depolama katmanlama](#automatic-storage-tiering) 
* [Yerel olarak sabitlenmiş paylaşımlar ve birimler](#locally-pinned-shares-and-volumes)
* Veri katmanlı veya buluta yedeklenen veriler için Yinelenenleri kaldırma ve sıkıştırma 
* [Zamanlanmış ve isteğe bağlı yedeklemeler](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Otomatik depolama katmanlama
Sanal dizi, sanal dizi ve bulut genelinde depolanan verileri yönetmek için yeni bir katmanlama mekanizması kullanır. Yalnızca iki katman vardır: yerel sanal dizi ve Azure bulut depolaması. StorSimple Sanal dizisi, geçerli kullanımı, yaşı ve diğer verilerle ilişkilerini izleyen bir ısı eşlemesine göre verileri otomatik olarak katmanlara yerleştirir. En etkin olan (Hottest) veriler yerel olarak depolanır, daha az etkin ve etkin olmayan veriler buluta otomatik olarak geçirilir. (Tüm yedeklemeler bulutta depolanır.) StorSimple, kullanım desenleri değiştikçe verileri ve depolama atamalarını ayarlar ve yeniden düzenler. Örneğin, bazı bilgiler zamana göre daha az etkin hale gelebilir. Aşamalı olarak daha az etkin hale geldiğinde, buluta katmanlı. Aynı veriler tekrar etkin hale gelirse, depolama dizisine katmanlı.

Belirli bir katmanlı paylaşıma veya birime yönelik veriler, kendi yerel katman alanını (Bu paylaşımın veya birimin toplam sağlanan alanının yaklaşık% 10 ' ü) garanti edilir. Bu, bu paylaşım veya birim için sanal dizideki kullanılabilir depolamayı azalttığında, bir paylaşıma veya birime yönelik katmanlama, diğer paylaşımların veya birimlerin katmanlama gereksinimlerinde etkilenmeyecektir. Bu nedenle, bir paylaşımdaki veya birimdeki çok meşgul iş yükü diğer tüm iş yüklerini buluta zorlayamaz.

Iscsı için oluşturulan katmanlı birimlerde, birimin boyutundan bağımsız olarak en fazla 200 GB yerel ayırması vardır.

![Otomatik depolama katmanlama](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Yerel olarak sabitlenmiş bir birim belirtebilirsiniz, bu durumda veriler sanal dizide kalır ve buluta hiçbir şekilde katmanlanmaktadır. Daha fazla bilgi için [yerel olarak sabitlenmiş paylaşımlar ve birimlere](#locally-pinned-shares-and-volumes)gidin.


### <a name="locally-pinned-shares-and-volumes"></a>Yerel olarak sabitlenmiş paylaşımlar ve birimler

Yerel olarak sabitlenmiş uygun paylaşımlar ve birimler oluşturabilirsiniz. Bu özellik, kritik uygulamalar için gereken verilerin sanal dizide kalmasını ve buluta hiçbir şekilde katmanlanmamasını sağlar. Yerel olarak sabitlenmiş paylaşımlar ve birimler aşağıdaki özelliklere sahiptir:

* Bunlar, bulut gecikme süreleriyle veya bağlantı sorunlarıyla ilgili değildir.
* StorSimple bulut yedekleme ve olağanüstü durum kurtarma özelliklerinden de faydalanır.

Yerel olarak sabitlenmiş bir paylaşımın veya birimin katmanlı veya katmanlı bir paylaşımın veya birimin yerel olarak sabitlenmiş olduğu bir birimi geri yükleyebilirsiniz. 

Yerel olarak sabitlenmiş birimler hakkında daha fazla bilgi için, [birimleri yönetmek üzere StorSimple Aygıt Yöneticisi hizmetini kullanma](storsimple-virtual-array-manage-volumes.md)bölümüne gidin.

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Veri katmanlı veya buluta yedeklenen veriler için Yinelenenleri kaldırma ve sıkıştırma

StorSimple, bulutta depolama gereksinimlerini daha fazla azaltmak için Yinelenenleri kaldırma ve veri sıkıştırmayı kullanır. Yinelenenleri kaldırma, depolanan veri kümesindeki yedekliliği ortadan kaldırarak depolanan toplam veri miktarını azaltır. Bilgi değişiklikleri olarak, StorSimple değişmeyen verileri yoksayar ve yalnızca değişiklikleri yakalar. Ayrıca, StorSimple, yinelenen bilgileri tanımlayarak ve kaldırarak depolanan verilerin miktarını azaltır.

> [!NOTE]
> Sanal dizide depolanan veriler çoğaltılamaz veya sıkıştırılmaz. Tüm yinelenenleri kaldırma ve sıkıştırma işlemi, veriler buluta gönderilmeden hemen önce oluşur.

### <a name="scheduled-and-on-demand-backups"></a>Zamanlanmış ve isteğe bağlı yedeklemeler

StorSimple veri koruma özellikleri, isteğe bağlı yedeklemeler oluşturmanızı sağlar. Ayrıca, varsayılan bir yedekleme zamanlaması verilerin her gün yedeklenmesini sağlar. Yedeklemeler, bulutta depolanan Artımlı anlık görüntü biçiminde alınır. Yalnızca son yedeklemeden bu yana yapılan değişiklikleri kaydeden anlık görüntüler oluşturulabilir ve hızlı bir şekilde geri yüklenebilir. Bu anlık görüntüler, ikincil depolama sistemlerini (bant yedekleme gibi) değiştirip verileri veri merkezinize veya gerekirse alternatif sitelere geri yüklemenize olanak sağladığından olağanüstü durum kurtarma senaryolarında kritik ölçüde önemli olabilir.

## <a name="managing-personal-information"></a>Kişisel bilgileri yönetme

Sanal serinin StorSimple Aygıt Yöneticisi, kişisel bilgileri iki anahtar örneğinde toplar:
 - Kullanıcıların e-posta adreslerinin yapılandırıldığı Kullanıcı ayarlarını uyarır. Bu bilgiler yönetici tarafından temizlenebilir. 
 - Paylaşımlar üzerinde bulunan verilere erişebilen kullanıcılar. Paylaşma verilerine erişebilen kullanıcıların listesi görüntülenir ve bu şekilde aktarılabilir. Bu liste paylaşımlar silindiğinde de silinir.

Daha fazla bilgi için [Güven Merkezi 'Nde Microsoft gizlilik ilkesini](https://www.microsoft.com/trustcenter)gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

[Sanal dizi portalını hazırlama](storsimple-virtual-array-deploy1-portal-prep.md)hakkında bilgi edinin.
