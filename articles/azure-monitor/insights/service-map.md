---
title: Azure 'da Hizmet Eşlemesi çözüm kullanma | Microsoft Docs
description: Hizmet Eşlemesi, Windows ve Linux sistemleri üzerindeki uygulama bileşenlerini otomatik olarak bulan ve hizmetler arasındaki iletişimi eşleyen bir Azure çözümüdür. Bu makalede, ortamınızda Hizmet Eşlemesi dağıtmak ve çeşitli senaryolarda kullanmak için ayrıntılar sağlanmaktadır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/24/2019
ms.openlocfilehash: 00bb58c88b7dc535bf76e1a96e9748a2c366b338
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554007"
---
# <a name="using-service-map-solution-in-azure"></a>Azure 'da Hizmet Eşlemesi çözümü kullanma

Hizmet Eşlemesi, Windows ve Linux sistemleri üzerindeki uygulama bileşenlerini otomatik olarak bulur ve hizmetler arasındaki iletişimi eşler. Hizmet Eşlemesi ile, sunucularınızı planladığınız şekilde kullanabilirsiniz: kritik hizmetler sunabilen birbirine bağlı sistemler. Sunucu Eşlemesi, aracının yüklenmesi dışında herhangi bir yapılandırma gerektirmeden sunucular, işlemler, gelen ve giden bağlantıların gecikme süresi ile TCP aracılığıyla bağlı mimarilerdeki bağlantı noktaları arasındaki bağlantıları gösterir.

Bu makalede Hizmet Eşlemesi Ekleme ve kullanma ayrıntıları açıklanmaktadır. Bu çözüme yönelik önkoşulları yapılandırma hakkında daha fazla bilgi için bkz. [VM'ler için Azure izleyici genel bakışı etkinleştirme](vminsights-enable-overview.md#prerequisites). Özetlemek gerekirse, şunlar gerekir:

* Bu çözümü etkinleştirmek için bir Log Analytics çalışma alanı.

* Çözümü etkinleştirdiğiniz aynı çalışma alanını raporlamak üzere yapılandırılmış Windows bilgisayarına veya Linux sunucusuna yüklenmiş Log Analytics Aracısı.

* Windows bilgisayara veya Linux sunucusuna yüklenmiş bağımlılık Aracısı.

>[!NOTE]
>Zaten Hizmet Eşlemesi dağıttıysanız, Ayrıca, sanal makine sistem durumunu ve performansını izlemek için ek özellikler içeren haritalarınızı VM'ler için Azure İzleyici görüntüleyebilirsiniz. Daha fazla bilgi için bkz. [VM'ler için Azure izleyici genel bakış](../../azure-monitor/insights/vminsights-overview.md). Hizmet Eşlemesi çözümü ve VM'ler için Azure İzleyici eşleme özelliği arasındaki farklar hakkında bilgi edinmek için aşağıdaki [SSS](vminsights-faq.md#how-is-azure-monitor-for-vms-map-feature-different-from-service-map)bölümüne bakın.

## <a name="sign-in-to-azure"></a>Azure'da oturum açın

[https://portal.azure.com](https://portal.azure.com) adresinden Azure portalında oturum açın.

## <a name="enable-service-map"></a>Hizmet Eşlemesi etkinleştir

1. Hizmet Eşlemesi çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview) 'nden veya [Çözüm Galerisi izleme çözümlerini ekleme](solutions.md)bölümünde açıklanan işlemi kullanarak etkinleştirin.
1. [Windows 'A bağımlılık aracısını yükler](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows) veya veri almak istediğiniz her bilgisayara [Linux 'a bağımlılık Aracısı 'nı yükler](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux) . Bağımlılık Aracısı en yakındaki komşularla bağlantıları izleyebildiğinden her bilgisayarda bir aracıya ihtiyacınız olmayabilir.

Log Analytics çalışma alanınızdan Azure portal Hizmet Eşlemesi erişin ve sol bölmeden **çözümler** seçeneğini belirleyin.<br><br> çalışma alanı ](./media/service-map/select-solution-from-workspace.png) ![Select çözüm seçeneği.<br> Çözümler listesinden **Servicemap (Çalışmaalanıadı)** öğesini seçin ve hizmet eşlemesi çözüme genel bakış sayfasında hizmet eşlemesi Özet kutucuğuna tıklayın.<br><br> Harita Özet kutucuğu ](./media/service-map/service-map-summary-tile.png) ![Service.

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>Kullanım örnekleri: BT süreçlerinizi bağımlılıktan haberdar edin

### <a name="discovery"></a>Keşif

Hizmet Eşlemesi, sunucularınız, süreçleriniz ve üçüncü taraf hizmetlerinizde bulunan bağımlılıkların ortak başvuru haritasını otomatik olarak oluşturur. Tüm TCP bağımlılıklarını bulur ve eşler arası bağlantıları, bağlı olduğunuz uzak üçüncü taraf sistemleri ve Active Directory gibi ağınızın geleneksel karanlık bölümlerine bağımlılıklar. Hizmet Eşlemesi, yönetilen sistemlerinizin oluşturmaya çalıştığından başarısız olan ağ bağlantılarını bulur, bu da olası sunucu yanlış yapılandırma, hizmet kesintisi ve ağ sorunlarını tanımanıza yardımcı olur.

### <a name="incident-management"></a>Olay yönetimi

Hizmet Eşlemesi, sistemlerin birbirine bağlı ve birbirini etkileyen sorunları göstererek sorun yalıtımının tahmin düzeyini ortadan kaldırmaya yardımcı olur. Başarısız bağlantıları tanımlamaya ek olarak, yanlış yapılandırılmış yük dengeleyiciler, kritik hizmetlerde fazla yük veya aşırı yük yükü ve geliştirici makineleri üretim sistemleriyle konuşuyor gibi standart dışı istemciler tanımlamaya yardımcı olur. Değişiklik İzleme ile tümleşik iş akışlarını kullanarak, bir arka uç makinesinde veya hizmette bulunan bir değişiklik olayının, bir olayın kök nedenini de açıklıyor olduğunu görebilirsiniz.

### <a name="migration-assurance"></a>Geçiş güvencesi

Hizmet Eşlemesi kullanarak Azure geçişlerini verimli bir şekilde planlayabilir, hızlandırabilir ve doğrulayabilirsiniz, bu da herhangi bir şeyin arkasında kalmaması ve beklenmedik kesintilerin gerçekleşmemesini sağlamaya yardımcı olur. Birlikte geçirilmesi gereken tüm bağımlı sistemleri bulabilir, sistem yapılandırmasını ve kapasitesini değerlendirebilir, çalışan bir sistemin hala kullanıcılara hizmet verip vermediğini veya geçiş yerine yetki alma için bir aday olup olmadığını belirleyebilirsiniz. Taşıma işlemi tamamlandıktan sonra, test sistemlerinin ve müşterilerinin bağlandığını doğrulamak için istemci yükünü ve kimliğini kontrol edebilirsiniz. Alt ağ planlama ve güvenlik duvarı tanımlarınızda sorunlar varsa Hizmet Eşlemesi Maps 'ta başarısız olan bağlantılar sizi bağlantı gerektiren sistemlere işaret ediyor.

### <a name="business-continuity"></a>İş sürekliliği

Azure Site Recovery kullanıyorsanız ve uygulama ortamınız için kurtarma sırasını tanımlamaya yardımcı olması gerekiyorsa, Hizmet Eşlemesi, kurtarma planınızın güvenilir olmasını sağlamak için sistemlerin birbirlerine nasıl bağlı olduğunu otomatik olarak gösterebilir. Kritik bir sunucu veya grup seçerek ve istemcilerini görüntüleyerek, sunucu geri yüklendikten ve kullanılabilir olduktan sonra hangi ön uç sistemlerini kurtaracağınızı belirleyebilirsiniz. Buna karşılık, kritik sunucuların arka uç bağımlılıklarını inceleyerek, odak sistemleriniz geri yüklenmeden önce hangi sistemlerin kurtarılacağını belirleyebilirsiniz.

### <a name="patch-management"></a>Düzeltme Eki Yönetimi

Hizmet Eşlemesi, diğer takımlar ve sunucuların hizmetinize bağlı olduğunu göstererek sistem güncelleştirme değerlendirmesi kullanımını geliştirir. bu sayede, bunları yama için sistemlerinizi kapatmadan önce daha önce bildirimde bulabilirsiniz. Hizmet Eşlemesi Ayrıca, hizmetlerinizin kullanılabilir olduğunu ve düzeltme eki uygulandıktan ve yeniden başlatıldıktan sonra düzgün bağlanıp bağlanmadığını göstererek yama yönetimini geliştirir.

## <a name="mapping-overview"></a>Eşlemeye genel bakış

Hizmet Eşlemesi aracıları, yüklü oldukları sunucudaki tüm TCP-bağlantılı işlemler ve her bir işlem için gelen ve giden bağlantılar hakkında bilgi toplar.

Sol bölmedeki listeden, belirli bir zaman aralığında bağımlılıklarını görselleştirmek için Hizmet Eşlemesi aracıları olan makineleri veya grupları seçebilirsiniz. Makine bağımlılığı eşlemeleri belirli bir makineye odaklanırlar ve bu makinenin doğrudan TCP istemcileri veya sunucuları olan tüm makineleri gösterir.  Makine grubu eşlemeleri sunucu kümelerini ve bağımlılıklarını gösterir.

![Hizmet Eşlemesi Genel Bakış](media/service-map/service-map-overview.png)

Makineler, seçili zaman aralığı sırasında etkin ağ bağlantılarıyla çalışan işlem gruplarını ve işlemleri göstermek üzere haritada genişletilebilir. Hizmet Eşlemesi aracısına sahip bir uzak makine işlem ayrıntılarını göstermek üzere genişletildiğinde, yalnızca odak makinesiyle iletişim kuran işlemler gösterilir. Odak makinesine bağlanan aracısız ön uç makinelerin sayısı, bağlandıkları işlemlerin sol tarafında belirtilmiştir. Odak makinesi aracısına sahip olmayan bir arka uç makineyle bağlantı yapıyor ise, arka uç sunucusu aynı bağlantı noktası numarasına diğer bağlantılarla birlikte sunucu bağlantı noktası grubuna dahil edilir.

Varsayılan olarak, Hizmet Eşlemesi Maps son 30 dakikalık bağımlılık bilgilerini gösterir. Sol üst taraftaki zaman denetimlerini kullanarak, bağımlılıkların geçmişte nasıl arandığı (örneğin, bir olay sırasında veya bir değişikliğin meydana gelmeden önce) göstermek için geçmiş zaman aralıkları için haritaları bir saate göre sorgulayabilirsiniz. Hizmet Eşlemesi veriler, ücretli çalışma alanlarında 30 gün boyunca ve ücretsiz çalışma alanlarında 7 gün boyunca depolanır.

## <a name="status-badges-and-border-coloring"></a>Durum rozetleri ve kenarlık renklendirme

Haritadaki her sunucunun en altında, sunucu hakkında durum bilgilerini kaplayan durum rozetlerinin listesi olabilir. Rozet, çözüm tümleştirmalarından birinden sunucu için bazı ilgili bilgiler olduğunu gösterir. Bir rozet tıklamak sizi doğrudan sağ bölmedeki durumun ayrıntılarına götürür. Şu anda kullanılabilir durumda olan durum rozetleri uyarılar, hizmet masası, değişiklikler, güvenlik ve güncelleştirmeleri içerir.

Durum rozetlerinin önem derecesine bağlı olarak, makine düğümü kenarlıkları kırmızı (kritik), sarı (uyarı) veya mavi (bilgilendirici) olabilir. Renk, durum rozetlerinin herhangi birinin en ciddi durumunu temsil eder. Gri kenarlık, durum göstergeleri olmayan bir düğümü gösterir.

![Durum rozetleri](media/service-map/status-badges.png)

## <a name="process-groups"></a>İşlem grupları

İşlem grupları ortak bir ürün veya hizmetle ilişkili işlemleri bir işlem grubuyla birleştirir.  Bir makine düğümü genişletildiğinde, tek başına işlemleri işlem gruplarıyla birlikte görüntüler.  Bir işlem grubundaki bir işleme gelen ve giden bağlantı başarısız olduysa, bağlantı tüm işlem grubu için başarısız olarak gösterilir.

## <a name="machine-groups"></a>Makine Grupları

Makine grupları bir sunucu kümesi çevresinde ortalanmış olan haritaları görmenizi sağlar, böylece çok katmanlı bir uygulamanın veya sunucu kümesinin tüm üyelerini tek bir haritada görüntüleyebilirsiniz.

Kullanıcılar bir gruba ait olan sunucuları seçer ve grup için bir ad seçer.  Daha sonra, tüm işlem ve bağlantılarıyla grubu görüntülemeyi seçebilir veya yalnızca grubun diğer üyeleriyle bağlantılı olan işlem ve bağlantılarla görüntüleyebilirler.

![Makine grubu](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>Makine grubu oluşturma

Bir grup oluşturmak için, makineler listesinde istediğiniz makine veya makineleri seçin ve **gruba ekle**' ye tıklayın.

![Grup Oluşturma](media/service-map/machine-groups-create.png)

Burada, **Yeni oluştur** ' u seçip gruba bir ad verebilirsiniz.

![Ad grubu](media/service-map/machine-groups-name.png)

>[!NOTE]
>Makine grupları 10 sunucu ile sınırlıdır.

### <a name="viewing-a-group"></a>Bir grubu görüntüleme

Bazı grupları oluşturduktan sonra gruplar sekmesini seçerek bunları görüntüleyebilirsiniz.

![Gruplar sekmesi](media/service-map/machine-groups-tab.png)

Sonra bu makine grubu için Haritayı görüntülemek üzere Grup adını seçin.
![Machine grup ](media/service-map/machine-group.png) gruba ait makineler haritada beyaz olarak özetlenmiştir.

Grup genişletildiğinde makine grubunu oluşturan makineler listelenir.

![Makine grubu makineleri](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>İşlemlere göre filtrele

Gruptaki tüm işlemlerin ve bağlantıların gösterilmesi ve yalnızca makine grubuyla bağlantılı olanlar arasında harita görünümünü değiştirebilirsiniz.  Varsayılan görünüm tüm işlemlerin gösterilmesi için kullanılır.  Haritanın üzerindeki filtre simgesine tıklayarak görünümü değiştirebilirsiniz.

![Filtre grubu](media/service-map/machine-groups-filter.png)

**Tüm süreçler** seçildiğinde, eşleme gruptaki makinelerin her birinde tüm işlem ve bağlantıları içerir.

![Makine grubu tüm süreçler](media/service-map/machine-groups-all.png)

Görünümü yalnızca **Grup ile bağlantılı süreçler**gösterecek şekilde değiştirirseniz, harita yalnızca gruptaki diğer makinelere doğrudan bağlı olan işlemlere ve bağlantılara daha kolay bir şekilde dönüştürülür, Basitleştirilmiş bir görünüm oluşturulur.

![Makine grubu filtrelenmiş işlem sayısı](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>Makineleri bir gruba ekleme

Mevcut bir gruba makineler eklemek için istediğiniz makinelerin yanındaki kutuları işaretleyin ve **gruba ekle**' ye tıklayın.  Ardından, makineleri eklemek istediğiniz grubu seçin.
 
### <a name="removing-machines-from-a-group"></a>Makineleri gruptan kaldırma

Gruplar listesinde, makine grubundaki makineleri listelemek için Grup adını genişletin.  Ardından, kaldırmak istediğiniz makinenin yanındaki üç nokta menüsüne tıklayın ve **Kaldır**' ı seçin.

![Makineyi gruptan Kaldır](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>Bir grubu kaldırma veya yeniden adlandırma

Grup listesindeki Grup adının yanındaki üç nokta menüsüne tıklayın.

![Makine grubu menüsü](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>Rol simgeleri

Bazı süreçler makinelerde belirli rollere sahiptir: Web sunucuları, uygulama sunucuları, veritabanı vb. Bir işlemin veya sunucunun oynadığı rolü bir bakışta belirlemesine yardımcı olmak için rol simgeleriyle birlikte hizmet eşlemesi açıklıyor süreci ve makine kutuları.

| Rol simgesi | Açıklama |
|:--|:--|
| ![Web sunucusu](media/service-map/role-web-server.png) | Web sunucusu |
| ![Uygulama sunucusu](media/service-map/role-application-server.png) | Uygulama sunucusu |
| ![Veritabanı sunucusu](media/service-map/role-database.png) | Veritabanı sunucusu |
| ![LDAP sunucusu](media/service-map/role-ldap.png) | LDAP sunucusu |
| ![SMB sunucusu](media/service-map/role-smb.png) | SMB sunucusu |

![Rol simgeleri](media/service-map/role-icons.png)


## <a name="failed-connections"></a>Başarısız bağlantılar

Başarısız bağlantılar, bir istemci sisteminin bir işlem veya bağlantı noktasına ulaşamamakta olduğunu belirten kesik çizgili kırmızı bir çizgiyle, işlemler ve bilgisayarlar için Hizmet Eşlemesi haritaları içinde gösterilir. Bu sistem, başarısız bağlantıyı denemezse, dağıtılan bir Hizmet Eşlemesi aracısına sahip herhangi bir sistemden başarısız olan bağlantılar bildirilir. Hizmet Eşlemesi, bağlantı kurvermeyen TCP yuvalarını gözlemleyerek bu işlemi ölçer. Bu hata, bir güvenlik duvarının, istemci veya sunucudaki bir yanlış yapılandırmanın veya bir uzak hizmetin kullanılamamasına neden olabilir.

![Başarısız bağlantılar](media/service-map/failed-connections.png)

Hatalı bağlantıları anlamak, sorun giderme, geçiş doğrulama, güvenlik Analizi ve genel mimari anlama konusunda yardımcı olabilir. Başarısız olan bağlantılar bazen zararsız olur, ancak genellikle yük devretme ortamı gibi bir doğrudan bir soruna işaret ederler ya da bir bulut geçişinden sonra bir veya iki uygulama katmanı konuşamazlar.

## <a name="client-groups"></a>İstemci grupları

İstemci grupları, bağımlılık aracıları olmayan istemci makinelerini temsil eden haritada yer alan kutulardır. Tek bir Istemci grubu, tek bir işlem veya makinenin istemcilerini temsil eder.

![İstemci grupları](media/service-map/client-groups.png)

Bir Istemci grubundaki sunucuların IP adreslerini görmek için grubunu seçin. Grubun içeriği **Istemci grubu Özellikler** bölmesinde listelenir.

![İstemci grubu özellikleri](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>Sunucu bağlantı noktası grupları

Sunucu bağlantı noktası grupları, bağımlılık aracıları olmayan sunuculardaki sunucu bağlantı noktalarını temsil eden kutulardır. Kutu, sunucu bağlantı noktasını ve bu bağlantı noktasına bağlantıları olan sunucu sayısı sayısını içerir. Tek tek sunucuları ve bağlantıları görmek için kutuyu genişletin. Kutuda yalnızca bir sunucu varsa, ad veya IP adresi listelenir.

![Sunucu bağlantı noktası grupları](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>Bağlam menüsü

Herhangi bir sunucunun sağ üst köşesindeki üç nokta (...) simgesine tıklanması söz konusu sunucunun bağlam menüsünü görüntüler.

![Başarısız bağlantılar](media/service-map/context-menu.png)

### <a name="load-server-map"></a>Sunucu haritasını yükle

**Sunucu haritasını yükle** ' ye tıklamak, yeni odak makinesi olarak seçili sunucu ile yeni bir haritaya götürür.

### <a name="show-self-links"></a>Kendi kendine bağlantıları göster

**Kendi kendine bağlantıları göster** ' i tıklamak sunucu düğümünü yeniden çizer, bu da sunucu içindeki işlemlere başlayan ve biten TCP bağlantısı olan tüm kendine bağlantılar da dahildir. Kendi kendine bağlantılar gösteriliyorsa, menü komutu **kendi kendine bağlantıları gizleyecek**şekilde değişir, böylece devre dışı bırakabilirsiniz.

## <a name="computer-summary"></a>Bilgisayar Özeti

**Makine Özeti** bölmesi, bir sunucunun işletim sistemine, bağımlılık sayısına ve diğer çözümlerdeki verilere ilişkin bir genel bakış içerir. Bu veriler performans ölçümlerini, hizmet masası biletlerini, değişiklik izlemeyi, güvenliği ve güncelleştirmeleri içerir.

![Makine Özet bölmesi](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>Bilgisayar ve işlem özellikleri

Bir Hizmet Eşlemesi haritasında gezindiğinizde, özellikleri hakkında ek bağlam kazanmak için makineler ve süreçler seçebilirsiniz. Makineler DNS adı, IPv4 adresleri, CPU ve bellek kapasitesi, VM türü, işletim sistemi ve sürüm, son yeniden başlatma zamanı ve OMS ve Hizmet Eşlemesi aracılarının kimlikleri hakkında bilgi sağlar.

![Makine özellikleri bölmesi](media/service-map/machine-properties.png)

İşlem adı, işlem açıklaması, Kullanıcı adı ve etki alanı (Windows 'ta), şirket adı, ürün adı, ürün sürümü, çalışma dizini, komut satırı ve işlem dahil çalışan işlemler hakkında işletim sistemi meta verilerinden işlem ayrıntılarını toplayabilirsiniz. başlangıç zamanı.

![İşlem özellikleri bölmesi](media/service-map/process-properties.png)

**Işlem Özeti** bölmesi, bağlı bağlantı noktaları, gelen ve giden bağlantılar ve başarısız bağlantılar dahil olmak üzere işlemin bağlantısı hakkında ek bilgiler sağlar.

![İşlem Özeti bölmesi](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>Uyarı tümleştirmesi

Hizmet Eşlemesi seçili zaman aralığında seçili sunucu için tetiklenen uyarıları göstermek üzere Azure uyarıları ile tümleşir. Geçerli uyarılar varsa sunucu bir simge görüntüler ve **makine uyarıları** bölmesi uyarıları listeler.

![Makine uyarıları bölmesi](media/service-map/machine-alerts.png)

Hizmet Eşlemesi ilgili uyarıları görüntüleyecek şekilde etkinleştirmek için, belirli bir bilgisayar için tetiklenen bir uyarı kuralı oluşturun. Uygun uyarılar oluşturmak için:
- Bilgisayara göre gruplandırmak için bir yan tümce ekleyin (örneğin, **bilgisayar aralığı 1 dakika**).
- Ölçüm ölçüsüne göre uyarı almak için seçin.

## <a name="log-events-integration"></a>Günlük olayları tümleştirmesi

Hizmet Eşlemesi, seçili zaman aralığında seçili sunucu için tüm kullanılabilir günlük olaylarının sayımını göstermek üzere günlük arama ile tümleşir. Günlük aramasına geçmek ve günlük olaylarını tek tek görmek için olay sayısı listesinden herhangi bir satıra tıklayabilirsiniz.

![Makine günlüğü olayları bölmesi](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>Hizmet Masası tümleştirmesi

Hizmet Eşlemesi BT Hizmet Yönetimi Bağlayıcısı tümleştirme, her iki çözüm de Log Analytics çalışma alanınızda etkinleştirildiğinde ve yapılandırıldıktan sonra otomatik olarak oluşturulur. Hizmet Eşlemesi tümleştirme "Service Desk" olarak etiketlenir. Daha fazla bilgi için bkz. [BT hizmet yönetimi Bağlayıcısı kullanarak ITSM iş öğelerini merkezi olarak yönetme](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview).

**Makine hizmeti** bölmesi bölmesi seçilen zaman aralığında seçili sunucu IÇIN tüm BT hizmet yönetimi olaylarını listeler. Sunucu, geçerli öğeler varsa ve makine hizmeti bölmesi bölmesi tarafından listeleniyorsa bir simge görüntüler.

![Makine hizmeti masası bölmesi](media/service-map/service-desk.png)

Öğeyi bağlı ıTSM çözümünüzde açmak için **Iş öğesini görüntüle**' ye tıklayın.

Günlük aramasında öğenin ayrıntılarını görüntülemek için **günlük aramasında göster**' e tıklayın.
Bağlantı ölçümleri Log Analytics içindeki iki yeni tabloya yazılır 

## <a name="change-tracking-integration"></a>Değişiklik İzleme tümleştirme

Hizmet Eşlemesi tümleştirme, her iki çözüm de Log Analytics çalışma alanınızda etkinleştirildiğinde ve yapılandırıldıktan sonra otomatik olarak Değişiklik İzleme.

**Makine değişiklik izleme** bölmesi, daha önce en son bulunan tüm değişiklikleri, daha fazla ayrıntı Için günlük aramasının detayına bir bağlantı ile birlikte listeler.

![Makine Değişiklik İzleme Bölmesi](media/service-map/change-tracking.png)

Aşağıdaki görüntü, **Log Analytics Içinde göster '** i seçtikten sonra görebileceğiniz bir ConfigurationChange olayının ayrıntılı görünümüdür.

![ConfigurationChange olayı](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>Performans tümleştirmesi

**Makine performansı** bölmesi, seçilen sunucu için standart performans ölçümlerini görüntüler. Ölçümler CPU kullanımı, bellek kullanımı, gönderilen ve alınan ağ baytları ve gönderilen ve alınan ağ baytlarına göre en üst işlemlerin bir listesini içerir.

![Makine performansı bölmesi](media/service-map/machine-performance.png)

Performans verilerini görmek için [uygun Log Analytics performans sayaçlarını etkinleştirmeniz](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)gerekebilir.  Etkinleştirmek istediğiniz sayaçlar:

Windows:
- İşlemci (*) \\% Işlemci zamanı
- Bellek \\ kullanılan kaydedilmiş bayt yüzdesi
- Ağ bağdaştırıcısı (*) gönderilen \\Bytes/sn
- Ağ bağdaştırıcısı (*) alınan \\Bytes/sn

Linux:
- İşlemci (*) \\% Işlemci zamanı
- Bellek (*) \\% kullanılan bellek
- Ağ bağdaştırıcısı (*) gönderilen \\Bytes/sn
- Ağ bağdaştırıcısı (*) alınan \\Bytes/sn

Ağ performansı verilerini almak için, çalışma alanınızdaki Wire Data 2.0 çözümünü da etkinleştirmiş olmanız gerekir.
 
## <a name="security-integration"></a>Güvenlik Tümleştirmesi

Hizmet Eşlemesi tümleştirme, her iki çözüm de Log Analytics çalışma alanınızda etkinleştirildiğinde ve yapılandırıldıktan sonra otomatik olarak Güvenlik ve Denetim.

**Makine güvenliği** bölmesi, seçilen sunucu için güvenlik ve denetim çözümünden verileri gösterir. Bölmesi, seçili zaman aralığı boyunca sunucu için tüm bekleyen güvenlik sorunlarının özetini listeler. Güvenlik sorunlarından herhangi birine tıklayarak bunlarla ilgili ayrıntılar için bir günlük araması yapın.

![Makine güvenliği bölmesi](media/service-map/machine-security.png)

## <a name="updates-integration"></a>Güncelleştirme tümleştirmesi

Hizmet Eşlemesi tümleştirme, her iki çözüm de Log Analytics çalışma alanınızda etkinleştirildiğinde ve yapılandırıldıktan sonra otomatik olarak Güncelleştirme Yönetimi.

**Makine güncelleştirmeleri** bölmesi, seçili sunucu için güncelleştirme yönetimi çözümünden verileri görüntüler. Bölmesi, seçili zaman aralığı boyunca sunucu için eksik güncelleştirmelerin özetini listeler.

![Makine Değişiklik İzleme Bölmesi](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics kayıtları

Hizmet Eşlemesi bilgisayar ve işlem envanter verileri Log Analytics [arama](../../azure-monitor/log-query/log-query-overview.md) için kullanılabilir. Bu verileri, geçiş planlama, Kapasite Analizi, bulma ve isteğe bağlı performans sorunlarını gidermeyle ilgili senaryolara uygulayabilirsiniz.

Her benzersiz bilgisayar ve işlem için saat başına bir kayıt oluşturulur; bir işlem veya bilgisayar başlatıldığında ya da Hizmet Eşlemesi eklenmediyse üzerinde olduğunda oluşturulan kayıtlara ek olarak. Bu kayıtlar aşağıdaki tablolardaki özelliklere sahiptir. ServiceMapComputer_CL olaylardaki alanlar ve değerler, ServiceMap Azure Resource Manager API 'sindeki makine kaynağının alanlarıyla eşlenir. ServiceMapProcess_CL olaylardaki alanlar ve değerler, ServiceMap Azure Resource Manager API 'sindeki Işlem kaynağının alanlarıyla eşlenir. ResourceName_s alanı, karşılık gelen Kaynak Yöneticisi kaynağındaki ad alanıyla eşleşir. 

>[!NOTE]
>Hizmet Eşlemesi Özellikler büyüdükçe, bu alanlar değiştirilebilir.

Benzersiz işlem ve bilgisayarları tanımlamak için kullanabileceğiniz, dahili olarak oluşturulan özellikler vardır:

- Bilgisayar: Log Analytics çalışma alanındaki bir bilgisayarı benzersiz şekilde tanımlamak için *RESOURCEID* veya *ResourceName_s* kullanın.
- İşlem: Log Analytics çalışma alanındaki bir işlemi benzersiz şekilde tanımlamak için *RESOURCEID* kullanın. *ResourceName_s* , işlemin çalıştığı makinenin bağlamı içinde benzersizdir (MachineResourceName_s) 

Belirli bir zaman aralığında belirtilen bir işlem ve bilgisayar için birden çok kayıt olabileceğinden, sorgular aynı bilgisayar veya işlem için birden fazla kayıt döndürebilir. Yalnızca en son kaydı dahil etmek için, Ekle "| RESOURCEID "ile sorgu için Yinelenenleri kaldırma.

### <a name="connections"></a>Bağlantılar

Bağlantı ölçümleri Log Analytics-VMConnection 'daki yeni bir tabloya yazılır. Bu tablo, bir makinenin (gelen ve giden) bağlantıları hakkında bilgi sağlar. Bağlantı ölçümleri Ayrıca bir zaman penceresi sırasında belirli bir ölçümü elde etmek için bir yol sağlayan API 'Ler ile birlikte sunulur.  "Bir dinleme yuvasında*kabul etme*" IŞLEMINDEN kaynaklanan TCP bağlantıları gelen, belırlı bir IP ve bağlantı noktasına *bağlanma*tarafından oluşturulan bağlantılar gidendir. Bir bağlantının yönü, **gelen** veya **giden**olarak ayarlanabilen Direction özelliği ile temsil edilir. 

Bu tablolardaki kayıtlar, bağımlılık Aracısı tarafından bildirilen verilerden oluşturulur. Her kayıt, bir dakikalık bir zaman aralığı üzerinde bir gözlemme temsil eder. TimeGenerated özelliği zaman aralığının başlangıcını gösterir. Her kayıt, ilgili varlığın, yani bağlantının veya bağlantı noktasının yanı sıra söz konusu varlıkla ilişkili ölçümleri belirlemek için bilgiler içerir. Şu anda yalnızca IPv4 üzerinden TCP kullanılarak gerçekleşen ağ etkinliği raporlanır.

Maliyet ve karmaşıklığı yönetmek için, bağlantı kayıtları tek tek fiziksel ağ bağlantılarını temsil etmez. Birden çok fiziksel ağ bağlantısı, daha sonra ilgili tabloya yansıtılmış bir mantıksal bağlantı halinde gruplandırılır.  Yani, *Vmconnection* tablosundaki kayıtlar, gözlemlenen ayrı fiziksel bağlantıları değil, mantıksal gruplandırmayı temsil eder. Belirli bir dakika aralığı sırasında aşağıdaki öznitelikler için aynı değeri paylaşan fiziksel ağ bağlantısı, *Vmconnection*'daki tek bir mantıksal kayıtta toplanır. 

| Özellik | Açıklama |
|:--|:--|
| `Direction` |Bağlantının yönü, değer *gelen* veya *giden* |
| `Machine` |Bilgisayar FQDN 'SI |
| `Process` |İşlemin veya işlem gruplarının kimliği, bağlantıyı başlatma/kabul etme |
| `SourceIp` |Kaynağın IP adresi |
| `DestinationIp` |Hedefin IP adresi |
| `DestinationPort` |Hedefin bağlantı noktası numarası |
| `Protocol` |Bağlantı için kullanılan protokol.  Değerler *TCP*'dir. |

Gruplandırmanın etkisini hesaba eklemek için, kaydın aşağıdaki özelliklerinde gruplanmış fiziksel bağlantı sayısıyla ilgili bilgiler verilmiştir:

| Özellik | Açıklama |
|:--|:--|
| `LinksEstablished` |Raporlama süresi penceresi sırasında kurulan fiziksel ağ bağlantısı sayısı |
| `LinksTerminated` |Raporlama süresi penceresi sırasında sonlandırılan fiziksel ağ bağlantısı sayısı |
| `LinksFailed` |Raporlama zaman penceresi sırasında başarısız olan fiziksel ağ bağlantısı sayısı. Bu bilgiler şu anda yalnızca giden bağlantılar için kullanılabilir. |
| `LinksLive` |Raporlama zamanı penceresinin sonunda açık olan fiziksel ağ bağlantısı sayısı|

#### <a name="metrics"></a>Ölçümler

Bağlantı sayısı ölçümlerine ek olarak, belirli bir mantıksal bağlantı veya ağ bağlantı noktası üzerinde gönderilen ve alınan veri hacmi hakkındaki bilgiler, kaydın aşağıdaki özelliklerine de dahildir:

| Özellik | Açıklama |
|:--|:--|
| `BytesSent` |Raporlama süresi penceresi sırasında gönderilen toplam bayt sayısı |
| `BytesReceived` |Raporlama süresi penceresi sırasında alınan toplam bayt sayısı |
| `Responses` |Raporlama zaman penceresi sırasında gözlemlenen yanıt sayısı. 
| `ResponseTimeMax` |Raporlama zaman penceresi sırasında gözlenen en büyük yanıt süresi (milisaniye).  Değer yoksa, özellik boştur.|
| `ResponseTimeMin` |Raporlama zamanı penceresi sırasında gözlenen en küçük yanıt süresi (milisaniye).  Değer yoksa, özellik boştur.|
| `ResponseTimeSum` |Raporlama zamanı penceresi sırasında gözlenen tüm yanıt sürelerinin (milisaniye) toplamı.  Değer yoksa, özellik boştur|

Bildirilen üçüncü veri türü yanıt süresi-bir çağıran bir bağlantı üzerinden gönderilen bir isteğin, uzak uç nokta tarafından işlenmek ve yanıt vermek için ne kadar süre harcadığını bekliyor. Raporlanan yanıt süresi, temel uygulama protokolünün doğru yanıt süresinin bir tahmindir. Fiziksel bir ağ bağlantısının kaynak ve hedef sonu arasındaki veri akışının gözlemine bağlı olarak buluşsal yöntemler kullanılarak hesaplanır. Kavramsal olarak, bir isteğin son baytındaki zaman arasındaki fark, yanıtın son baytının geri ulaştığı zaman arasındaki farktır. Bu iki zaman damgası, belirli bir fiziksel bağlantıda istek ve yanıt olaylarını belirtmek için kullanılır. Aralarındaki fark, tek bir isteğin yanıt süresini temsil eder. 

Bu özelliğin ilk sürümünde, algoritmız, belirli bir ağ bağlantısı için kullanılan gerçek uygulama protokolüne bağlı olarak değişen başarı derecesine sahip olabilecek bir yaklaşık dır. Örneğin, geçerli yaklaşım HTTP (S) gibi istek yanıt tabanlı protokoller için iyi çalışır, ancak tek yönlü veya ileti kuyruğu tabanlı protokollerle çalışmaz.

Göz önünde bulundurmanız gereken bazı önemli noktaları aşağıda bulabilirsiniz:

1. Bir işlem aynı IP adresinde ancak birden çok ağ arabiriminden bağlantı kabul ederse, her arabirim için ayrı bir kayıt raporlanır. 
2. Joker IP içeren kayıtlar hiçbir etkinlik içermez. Bunlar, makinedeki bir bağlantı noktasının gelen trafiğe açık olduğunu göstermek için dahil edilmiştir.
3. Ayrıntı ve veri hacmini azaltmak için, belirli bir IP adresi ile eşleşen bir kayıt (aynı işlem, bağlantı noktası ve protokol) olduğunda joker IP 'si olan kayıtlar atlanır. Joker karakter bir IP kaydı atlandığında, belirli IP adresine sahip ıyaya Cardbind kaydı özelliği, bağlantı noktasının raporlama makinesinin her arabiriminden açığa çıkardığını göstermek için "true" olarak ayarlanır.
4. Yalnızca belirli bir arabirim üzerinde bağlı olan bağlantı noktaları, ıyaya Cardbind ' i "false" olarak ayarlanmış.

#### <a name="naming-and-classification"></a>Adlandırma ve sınıflandırma

Kolaylık olması için, bir bağlantının uzak ucunun IP adresi Remoteıp özelliğinde yer alır. Gelen bağlantılarda, Remoteıp, SourceIP ile aynıdır, ancak giden bağlantılar için de Destinationıp ile aynıdır. RemoteDnsCanonicalNames özelliği, Remoteıp için makine tarafından raporlanan DNS kurallı adlarını temsil eder. Remotednssorular ve RemoteClassification özellikleri ileride kullanılmak üzere ayrılmıştır. 

#### <a name="geolocation"></a>Coğrafi Konum

*Vmconnection* Ayrıca, kaydın aşağıdaki özelliklerindeki her bir bağlantı kaydının uzak ucuna ait coğrafi konum bilgilerini de içerir: 

| Özellik | Açıklama |
|:--|:--|
| `RemoteCountry` |Remoteıp 'yi barındıran ülkenin/bölgenin adı.  Örneğin, *Birleşik Devletler* |
| `RemoteLatitude` |Coğrafi konum enlem.  Örneğin, *47,68* |
| `RemoteLongitude` |Coğrafi konum boylam.  Örneğin, *-122,12* |

#### <a name="malicious-ip"></a>Kötü amaçlı IP

*Vmconnection* tablosundaki her remoteıp özelliği, bilinen kötü amaçlı etkinliklerle bir IP kümesine karşı denetlenir. Remoteıp kötü amaçlı olarak tanımlanmışsa, kaydın aşağıdaki özelliklerinde aşağıdaki özellikler doldurulur (boş olan, IP kötü amaçlı olarak kabul edilmez).

| Özellik | Açıklama |
|:--|:--|
| `MaliciousIp` |Remoteıp adresi |
| `IndicatorThreadType` |Algılanan tehdit göstergesi, *botnet*, *C2*, *cryptoaraştırma*, *koyu ağ*, *DDoS*, *MaliciousUrl*, *kötü amaçlı yazılım*, *kimlik avı*, *proxy*, *Pua*,  *Listem*.   |
| `Description` |Gözlemlenen tehdit açıklaması. |
| `TLPLevel` |Trafik ışığı Protokolü (TLP) düzeyi, tanımlı değerlerden biridir, *beyaz*, *yeşil* *,,* ve *kırmızı*. |
| `Confidence` |Değerler *0 – 100*' dir. |
| `Severity` |Değerler *0 – 5*' dir; burada *5* en önemdir ve *0* , hiç önemli değildir. Varsayılan değer *3*' dir.  |
| `FirstReportedDateTime` |Sağlayıcı göstergeyi ilk kez raporladı. |
| `LastReportedDateTime` |Göstergenin Interflow tarafından en son görüldüğü zaman. |
| `IsActive` |Göstergelerin *true* veya *false* değeriyle devre dışı bırakıldığını gösterir. |
| `ReportReferenceLink` |Verilen bir observable ile ilgili raporların bağlantıları. |
| `AdditionalInformation` |, Gözlemlenen tehdit hakkında, varsa ek bilgiler sağlar. |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL kayıtları

Bir *ServiceMapComputer_CL* türüne sahip kayıtlar hizmet eşlemesi aracıları olan sunucular için envanter verileri vardır. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Çalışma alanı içindeki bir makinenin benzersiz tanımlayıcısı |
| `ResourceName_s` | Çalışma alanı içindeki bir makinenin benzersiz tanımlayıcısı |
| `ComputerName_s` | Bilgisayar FQDN 'SI |
| `Ipv4Addresses_s` | Sunucunun IPv4 adreslerinin listesi |
| `Ipv6Addresses_s` | Sunucunun IPv6 adreslerinin listesi |
| `DnsNames_s` | Bir dizi DNS adı |
| `OperatingSystemFamily_s` | Windows veya Linux |
| `OperatingSystemFullName_s` | İşletim sisteminin tam adı  |
| `Bitness_s` | Makinenin bit genişliği (32-bit veya 64-bit)  |
| `PhysicalMemory_d` | MB cinsinden fiziksel bellek |
| `Cpus_d` | CPU sayısı |
| `CpuSpeed_d` | MHz cinsinden CPU hızı|
| `VirtualizationState_s` | *bilinmiyor*, *fiziksel*, *sanal*, *hiper yönetici* |
| `VirtualMachineType_s` | *hyperv*, *VMware*, vb. |
| `VirtualMachineNativeMachineId_g` | Hiper Yöneticisi tarafından atanan VM KIMLIĞI |
| `VirtualMachineName_s` | VM 'nin adı |
| `BootTime_t` | Önyükleme saati |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL tür kayıtları

Bir *ServiceMapProcess_CL* türüne sahip kayıtlar, hizmet eşlemesi aracıları olan sunucularda TCP bağlantılı işlemlere yönelik envanter verileri vardır. Bu kayıtlar aşağıdaki tablodaki özelliklere sahiptir:

| Özellik | Açıklama |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | Çalışma alanı içindeki bir işlemin benzersiz tanımlayıcısı |
| `ResourceName_s` | Üzerinde çalıştığı makine içindeki bir işlemin benzersiz tanımlayıcısı|
| `MachineResourceName_s` | Makinenin kaynak adı |
| `ExecutableName_s` | İşlemin yürütülebilir dosyasının adı |
| `StartTime_t` | İşlem havuzu başlangıç zamanı |
| `FirstPid_d` | İşlem havuzundaki ilk PID |
| `Description_s` | İşlem açıklaması |
| `CompanyName_s` | Şirketin adı |
| `InternalName_s` | İç ad |
| `ProductName_s` | Ürünün adı |
| `ProductVersion_s` | Ürün sürümü |
| `FileVersion_s` | Dosya sürümü |
| `CommandLine_s` | Komut satırı |
| `ExecutablePath _s` | Yürütülebilir dosyanın yolu |
| `WorkingDirectory_s` | Çalışma dizini |
| `UserName` | İşlemin üzerinde yürütüldüğü hesap |
| `UserDomain` | İşlemin üzerinde yürütüldüğü etki alanı |

## <a name="sample-log-searches"></a>Örnek günlük aramaları

### <a name="list-all-known-machines"></a>Tüm bilinen makineleri Listele

ServiceMapComputer_CL | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Tüm yönetilen bilgisayarların fiziksel bellek kapasitesini listeleyin.

ServiceMapComputer_CL | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme | Project PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>Bilgisayar adını, DNS, IP ve işletim sistemini listeleyin.

ServiceMapComputer_CL | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme | Project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Komut satırında "SQL" ile tüm süreçler bul

ServiceMapProcess_CL | Burada CommandLine_s contains_cs "SQL" | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Kaynak adına göre bir makine (en son kayıt) bulun

Arama yeri (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46DF-B43C-899ba829e07b" | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>IP adresine göre bir makine (en son kayıt) bulun

ara (ServiceMapComputer_CL) "10.229.243.232" | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme

### <a name="list-all-known-processes-on-a-specified-machine"></a>Belirtilen makinedeki tüm bilinen işlemlerin listesini Listele

ServiceMapProcess_CL | Burada MachineResourceName_s = = "a-559dbcd8-3130-454D-8d1d-f624e57961bc" | RESOURCEID tarafından arg_max (TimeGenerated, *) özetleme

### <a name="list-all-computers-running-sql"></a>SQL çalıştıran tüm bilgisayarları listeleme

ServiceMapComputer_CL | WHERE ResourceName_s in ((içinde ara (ServiceMapProcess_CL) "\*sql \*" | DISTINCT MachineResourceName_s)) | ayrı ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Veri merkezindeki tüm benzersiz ürün sürümlerini listeleyin

ServiceMapProcess_CL | WHERE ExecutableName_s = = "kıvrık" | ayrı ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS çalıştıran tüm bilgisayarların bilgisayar grubunu oluşturma

ServiceMapComputer_CL | Burada OperatingSystemFullName_s contains_cs "CentOS" | ayrı ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Bir makine grubundan giden bağlantıları özetleme

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST API

Hizmet Eşlemesi içindeki tüm sunucu, işlem ve bağımlılık verileri, [Hizmet Eşlemesi REST API](https://docs.microsoft.com/rest/api/servicemap/)ile kullanılabilir.

## <a name="diagnostic-and-usage-data"></a>Tanılama ve kullanım verileri

Microsoft, Hizmet Eşlemesi hizmeti kullanımınız aracılığıyla kullanım ve performans verilerini otomatik olarak toplar. Microsoft bu verileri, Hizmet Eşlemesi hizmetinin kalitesini, güvenliğini ve bütünlüğünü sağlamak ve geliştirmek için kullanır. Doğru ve verimli sorun giderme özellikleri sağlamak için, veriler işletim sistemi ve sürümü, IP adresi, DNS adı ve iş istasyonu adı gibi yazılımınızın yapılandırması hakkında bilgiler içerir. Microsoft ad, adres veya diğer iletişim bilgileri toplamaz.

Veri toplama ve kullanım hakkında daha fazla bilgi için [Microsoft Online Services gizlilik bildirimi](https://go.microsoft.com/fwlink/?LinkId=512132)' ne bakın.

## <a name="next-steps"></a>Sonraki adımlar

Hizmet Eşlemesi tarafından toplanan verileri almak için Log Analytics 'de [günlük aramaları](../../azure-monitor/log-query/log-query-overview.md) hakkında daha fazla bilgi edinin.

## <a name="troubleshooting"></a>Sorun giderme

Hizmet Eşlemesi yüklerken veya çalıştırırken herhangi bir sorun yaşıyorsanız, bu bölüm size yardımcı olabilir. Hala sorununuzu çözemezseniz, lütfen Microsoft Desteği başvurun.

### <a name="dependency-agent-installation-problems"></a>Bağımlılık Aracısı yükleme sorunları

#### <a name="installer-prompts-for-a-reboot"></a>Yükleyici yeniden başlatma sorar
Bağımlılık Aracısı *genellikle* yükleme veya kaldırma sonrasında yeniden başlatma gerektirmez. Ancak, bazı nadir durumlarda Windows Server, yükleme işlemine devam etmek için yeniden başlatma gerektirir. Bu durum, bir bağımlılık olduğunda, genellikle Microsoft Visual C++ yeniden dağıtılabilir kitaplığı, kilitli bir dosya nedeniyle yeniden başlatma gerektirdiğinde oluşur.

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>İleti "bağımlılık Aracısı yüklenemiyor: Visual Studio çalışma zamanı kitaplıkları yüklenemedi (kod = [code_number])" görünüyor

Microsoft bağımlılık Aracısı, Microsoft Visual Studio çalışma zamanı kitaplıkları üzerine kurulmuştur. Kitaplıkların yüklenmesi sırasında bir sorun oluşursa bir ileti alırsınız. 

Çalışma zamanı kitaplığı yükleyicileri,%LOCALAPPDATA%\temp klasöründe Günlükler oluşturur. Dosya `dd_vcredist_arch_yyyymmddhhmmss.log`, *arch* `x86` veya `amd64` ve *yyyyaaggssddss* , günlük oluşturulduğu tarih ve saat (24 saatlik saat). Günlük, yüklemeyi engelleyen sorun hakkında ayrıntılar sağlar.

Önce [en son çalışma zamanı kitaplıklarını](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) yüklemek yararlı olabilir.

Aşağıdaki tabloda kod numaraları ve önerilen çözümler listelenmektedir.

| Kodlayın | Açıklama | Çözünürlük |
|:--|:--|:--|
| 0x17 | Kitaplık yükleyicisi yüklü olmayan bir Windows güncelleştirmesi gerektiriyor. | En son kitaplık yükleyicisi günlüğüne bakın.<br><br>@No__t_0 başvurunun ardından bir satır `Error 0x80240017: Failed to execute MSU package,`, KB2999226 yüklemek için önkoşullara sahip olmanız gerekmez. [Windows makalesinde evrensel C çalışma zamanı](https://support.microsoft.com/kb/2999226) 'nın Önkoşullar bölümündeki yönergeleri izleyin. Önkoşulları yüklemek için Windows Update çalıştırmanız ve birden çok kez yeniden başlatmanız gerekebilir.<br><br>Microsoft bağımlılık Aracısı yükleyicisini yeniden çalıştırın. |

### <a name="post-installation-issues"></a>Yükleme sonrası sorunlar

#### <a name="server-doesnt-appear-in-service-map"></a>Sunucu Hizmet Eşlemesi görünmüyor

Bağımlılık aracısı yüklemeniz başarılı olduysa, ancak makinenizi Hizmet Eşlemesi çözümde görmezsiniz:
* Bağımlılık Aracısı başarıyla yüklendi mi? Hizmetin yüklü ve çalışır olup olmadığını denetleyerek bunu doğrulayabilirsiniz.<br><br>
**Windows**: **Microsoft bağımlılık Aracısı**adlı hizmeti arayın.
**Linux**: çalışan işlem **Microsoft-Dependency-Agent**' ı arayın.

* [Log Analytics ücretsiz katmanda](https://azure.microsoft.com/pricing/details/monitor/)misiniz? Ücretsiz plan, beş adede kadar benzersiz Hizmet Eşlemesi makineye izin verir. Önceki beş, artık veri göndermese bile, sonraki tüm makineler Hizmet Eşlemesi görünmez.

* Sunucunuz Azure Izleyici günlüklerine günlük ve performans verileri gönderiyor mu? Azure Monitor\Logs ' e gidin ve bilgisayarınız için aşağıdaki sorguyu çalıştırın: 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

Sonuçlarda çeşitli olaylar mı kullanıyorsunuz? Veriler son zamanlarda mı? Bu durumda, Log Analytics aracınız doğru şekilde çalışıyor ve çalışma alanıyla iletişim kuruyor. Aksi takdirde, makinenizde aracı denetleyin: [Windows sorun giderme için Log Analytics Aracısı](../platform/agent-windows-troubleshoot.md) veya [Linux sorun giderme için Log Analytics Aracısı](../platform/agent-linux-troubleshoot.md).

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Sunucu Hizmet Eşlemesi görünüyor ancak işlem içermiyor

Makinenizde Hizmet Eşlemesi görürseniz, ancak işlem veya bağlantı verisi yoksa, bağımlılık aracısının yüklü ve çalışır durumda olduğunu ancak çekirdek sürücüsünün yüklenmediğini gösterir. 

@No__t_0 (Windows) veya `/var/opt/microsoft/dependency-agent/log/service.log file` (Linux) denetleyin. Dosyanın son satırları, çekirdeğin neden yüklenmediğini göstermelidir. Örneğin, çekirdeğini güncelleştirdiyseniz, çekirdek Linux üzerinde desteklenmeyebilir.

## <a name="feedback"></a>Geri Bildirim

Hizmet Eşlemesi veya bu belgeler hakkında bizimle ilgili geri bildiriminiz var mı?  Özellik önerdiğiniz veya mevcut önerilere oy oluşturabileceğiniz [Kullanıcı ses](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)sayfamızı ziyaret edin.
