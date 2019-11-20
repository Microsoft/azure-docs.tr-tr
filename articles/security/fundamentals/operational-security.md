---
title: Azure operasyonel güvenlik | Microsoft Docs
description: Microsoft Azure Izleme günlükleri, hizmetleri ve nasıl çalıştığı hakkında bilgi edinin.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 69b25f22d448436db5e906a3b56b5e50ec412b40
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934669"
---
# <a name="azure-operational-security"></a>Azure operasyonel güvenlik
## <a name="introduction"></a>Giriş

### <a name="overview"></a>Genel Bakış
Güvenliğin bulutta bir iş olduğunu ve Azure güvenliğiyle ilgili doğru ve güncel bilgileri nerede bulabileceğinizi biliyoruz. Uygulamalarınız ve hizmetleriniz için Azure kullanmanın en iyi nedenlerinden biri, kullanılabilir güvenlik araçları ve yetenekler dizisinin avantajlarından biridir. Bu araçlar ve yetenekler, güvenli Azure platformunda güvenli çözümler oluşturmayı olanaklı hale getirmeye yardımcı olur. Microsoft Azure, müşteri verilerinin gizliliğini, bütünlüğünü ve kullanılabilirliğini sağlamalıdır, ayrıca saydam sorumluluklığa de olanak tanır.

Müşterilerin hem müşterinin hem de Microsoft operasyonel perspektiflerinden Microsoft Azure uygulanmış olan güvenlik denetimlerinin dizisini daha iyi anlamasına yardımcı olmak için, bu teknik incelemeye ("Azure Operasyonel Güvenlik") kapsamlı bir Windows Azure ile kullanılabilen işlemsel Güvenlik bölümüne bakın.

### <a name="azure-platform"></a>Azure platformu
Azure, geniş bir işletim sistemi, programlama dili, çerçeve, araç, veritabanı ve cihaz seçimini destekleyen genel bir bulut hizmeti platformudur. Docker tümleştirmesiyle Linux kapsayıcıları çalıştırabilir; JavaScript, Python, .NET, PHP, Java ve Node. js ile uygulamalar oluşturun; iOS, Android ve Windows cihazlar için arka uçlar oluşturun. Azure bulut hizmeti, milyonlarca geliştirici ve BT uzmanlarının zaten kullandığı ve güveneceği teknolojilerin aynısını destekler.

Üzerinde derleme yaptığınızda veya BT varlıklarını ' ye geçirdiğinizde, bu kuruluşun, uygulama ve verilerinizi hizmetlerle ve verilerinizi, bulut tabanlı varlıklarınızın güvenliğini yönetmek için sağladığı denetimlerle koruma yeteneklerine bağlı olursunuz.

Azure 'un altyapısı, tesis aracılığıyla milyonlarca müşteriyi aynı anda barındırmak için uygulamalara tasarlanmıştır ve işletmelerin güvenlik gereksinimlerini karşılayabilecekleri güvenilir bir temel sağlar. Ayrıca, Azure geniş kapsamlı bir güvenlik seçenekleri dizisi ve bunları, kuruluşunuzun dağıtımlarınızın benzersiz gereksinimlerini karşılayacak şekilde güvenliği özelleştirebilmeniz için denetlemenize olanak tanır. Bu belge, Azure Güvenlik özellikleri 'nin bu gereksinimleri karşılamanıza nasıl yardımcı olduğunu anlamanıza yardımcı olur.

### <a name="abstract"></a>Özet
Azure operasyonel güvenlik, kullanıcıların verilerini, uygulamalarını ve diğer varlıklarını Microsoft Azure korumak için kullanıcılara sunulan hizmetleri, denetimleri ve özellikleri ifade eder. Azure operasyonel güvenlik, Microsoft güvenlik geliştirme yaşam döngüsü (SDL), Microsoft Güvenlik Yanıt Merkezi programı gibi Microsoft 'a özgü çeşitli yetenekler aracılığıyla elde edilen bilgileri içeren bir çerçeve üzerine kurulmuştur. ve siber güvenlik tehdidi yatay olarak ayrıntılı bir şekilde tanıma.

Bu Teknik İnceleme, Microsoft 'un Microsoft Azure bulut platformunda Azure operasyonel güvenlik yaklaşımını ve aşağıdaki hizmetleri kapsamakta olduğunu özetler:
1.  [Azure İzleyici](../../azure-monitor/index.yml)

2.  [Azure Güvenlik Merkezi](../../security-center/security-center-intro.md)

3.  [Azure İzleyici](../../azure-monitor/overview.md)

4.  [Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure depolama Analizi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Izleme günlüklerini Microsoft Azure

Microsoft Azure Izleme günlükleri, hibrit bulut için BT yönetimi çözümüdür. Tek başına veya mevcut System Center dağıtımınızı genişletmek için, Azure Izleyici günlükleri, altyapınızın bulut tabanlı yönetimi için en yüksek esneklik ve denetim sağlar.

![Azure İzleyici günlükleri](./media/operational-security/azure-operational-security-fig1.png)

Azure Izleyici günlükleri sayesinde, şirket içi, Azure, AWS, Windows Server, Linux, VMware ve OpenStack dahil olmak üzere herhangi bir bulutu, rekabet çözümleriyle daha düşük bir maliyetle yönetebilirsiniz. Bulut öncelikli dünya için tasarlanan Azure Izleyici günlükleri, yeni iş sorunlarını karşılamak ve yeni iş yükleri, uygulamalar ve bulut ortamları barındırmak için en hızlı ve en düşük maliyetli yol olan kuruluşunuzu yönetmeye yönelik yeni bir yaklaşım sunar.

### <a name="azure-monitor-services"></a>Azure Izleyici Hizmetleri

Azure Izleyici günlüklerinin temel işlevleri, Azure 'da çalışan bir dizi hizmet tarafından sağlanır. Her hizmet belirli bir yönetim işlevi sağlar ve farklı hizmetleri birleştirerek farklı yönetim senaryoları elde edebilirsiniz.

| Hizmet  | Açıklama|
| :------------- | :-------------|
| Azure İzleyici günlükleri | Fiziksel ve sanal makineler dahil olmak üzere çeşitli kaynakların kullanılabilirliğini ve performansını izleyin ve analiz edin. |
|Otomasyon | El ile gerçekleştirilen işlemleri otomatikleştirin; fiziksel ve sanal makinelere yapılandırma uygulayın. |
| Yedekle | Kritik verileri yedekleyin ve geri yükleyin. |
| Site Recovery | Kritik uygulamalar için yüksek kullanılabilirlik sağlayın. |

### <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri

[Azure izleyici günlükleri](https://azure.microsoft.com/documentation/services/log-analytics) yönetilen kaynaklardan merkezi bir depoya veri toplayarak izleme hizmetleri sağlar. Bu verilere olaylar, performans verileri ya da API aracılığıyla sağlanan özel veriler dahil olabilir. Toplanan veriler uyarı, analiz ve dışarı aktarma için kullanılabilir hale gelir.


Bu yöntem, çeşitli kaynaklardan gelen verileri birleştirerek Azure hizmetinizdeki verileri mevcut şirket içi ortamınızdan birleştirebilmenizi sağlar. Ayrıca, veri toplama işlemini veriler üzerinde gerçekleştirilen eylemden ayırarak tüm eylemlerin her tür veri üzerinde kullanılabilmesini mümkün kılar.


![Azure İzleyici günlükleri](./media/operational-security/azure-operational-security-fig2.png)

Azure Izleyici hizmeti, aşağıdaki yöntemleri kullanarak bulut tabanlı verilerinizi güvenli bir şekilde yönetir:
-   veri ayırma
-   veri saklama
-   fiziksel güvenlik
-   Olay yönetimi
-   Uyumluluk
-   güvenlik standartları sertifikaları

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) , veri yedekleme ve geri yükleme hizmetleri sağlar ve Azure izleyici ürün ve hizmet paketi 'nin bir parçasıdır.
Uygulama verilerinizi korur ve herhangi bir sermaye yatırımı olmadan en düşük işletim giderleriyle yıllar boyunca saklar. Bu, SQL Server ve SharePoint gibi uygulama iş yüklerine ek olarak fiziksel ve sanal Windows sunucularından verileri yedekleyebilir. Ayrıca, yedekleme ve uzun vadeli depolama için korunan verileri Azure 'a çoğaltmak üzere [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) tarafından da kullanılabilir.


Azure Backup'ta korunan veriler belirli bir coğrafi bölgede yer alan bir yedekleme kasasında depolanır. Veriler aynı bölge içinde çoğaltılır ve kasa türüne bağlı olarak, daha fazla esneklik için başka bir bölgeye de çoğaltılabilir.

### <a name="management-solutions"></a>Yönetim Çözümleri
[Azure izleyici](../../security-center/security-center-intro.md) , Microsoft 'un Şirket içi ve bulut altyapınızı yönetmenize ve korumanıza yardımcı olan bulut tabanlı BT yönetimi çözümüdür.


[Yönetim çözümleri](../../monitoring/monitoring-solutions.md) , bir veya daha fazla Azure izleyici hizmetini kullanarak belirli bir yönetim senaryosunu uygulayan, önceden paketlenmiş, günlüğe kaydetme kümeleridir. Azure Izleyici 'de yatırımınızın değerini artırmak için Microsoft ve Azure aboneliğinize kolayca ekleyebileceğiniz iş ortakları tarafından sunulan farklı çözümler vardır. İş ortağı olarak, uygulamalarınızı ve hizmetlerinizi desteklemek için kendi çözümlerinizi oluşturabilir ve bunları Azure Marketi veya hızlı başlangıç şablonları aracılığıyla kullanıcılara sağlayabilirsiniz.


![Yönetim Çözümleri](./media/operational-security/azure-operational-security-fig4.png)

Ek işlevsellik sağlamak için birden çok hizmet kullanan çözüme iyi bir örnek [güncelleştirme yönetimi çözümüdür](../../automation/automation-update-management.md). Bu çözüm, her aracıdaki gerekli güncelleştirmeler hakkında bilgi toplamak için Windows ve Linux için [Azure izleyici günlükleri](../../log-analytics/log-analytics-queries.md) Aracısı 'nı kullanır. Bu verileri, eklenen bir panoyla çözümleyebileceğiniz Azure Izleyici günlükleri deposuna yazar.

Bir dağıtım oluşturduğunuzda, gerekli güncelleştirmeleri yüklemek için [Azure Otomasyonu](../../automation/automation-intro.md) 'nda runbook 'lar kullanılır. Bu işlemi baştan sona portalda yönetirsiniz ve altyapısal ayrıntılar konusunda endişelenmeniz gerekmez.

## <a name="azure-security-center"></a>Azure Güvenlik Merkezi

Azure Güvenlik Merkezi, Azure kaynaklarınızı korumanıza yardımcı olur. Azure abonelikleriniz genelinde tümleşik güvenlik izleme ve ilke yönetimi sağlar. Hizmet içinde, ilkeleri yalnızca Azure aboneliklerinize değil, [kaynak gruplarında](../../azure-resource-manager/resource-group-overview.md#resource-groups)da tanımlayabilir, böylece daha ayrıntılı olabilirsiniz.

### <a name="security-policies-and-recommendations"></a>Güvenlik ilkeleri ve öneriler

Güvenlik ilkeleri, belirtilen abonelik veya kaynak grubundaki kaynaklar için önerilen denetim kümesini tanımlar.

Güvenlik Merkezi'nde, şirketinizin güvenlik gereksinimleri ve uygulamaların türü veya verilerin duyarlılığına göre ilkeleri tanımlarsınız.

![Güvenlik ilkeleri ve öneriler](./media/operational-security/azure-operational-security-fig5.png)


Abonelik düzeyinde etkinleştirilen ilkeler, sağ taraftaki diyagramda gösterildiği gibi abonelik içindeki tüm kaynak gruplarına otomatik olarak yayılır:


### <a name="data-collection"></a>Veri toplama

Güvenlik Merkezi, verilerinizin güvenlik durumunu değerlendirmek, güvenlik önerileri sağlamak ve sizi tehditlere karşı uyarmak için sanal makinelerinizden (VM) veri toplar. İlk erişim güvenlik merkezinizde veri koleksiyonunuz aboneliğinizdeki tüm VM 'lerde etkinleştirilir. Veri toplama önerilir, ancak Güvenlik Merkezi ilkesinde veri toplamayı kapatarak bu seçimi kaldırabilirsiniz.

### <a name="data-sources"></a>Veri kaynakları

- Azure Güvenlik Merkezi, güvenlik durumunuzu görüntüleme, güvenlik açıklarını tanımlayıp çözümler önerme ve etkin tehditleri saptama amacıyla aşağıdaki kaynaklarda bulunan verileri analiz eder:

-   Azure Hizmetleri: Bu hizmetin kaynak sağlayıcısıyla iletişim kurarak dağıttığınız Azure hizmetlerinin yapılandırmasıyla ilgili bilgileri kullanır.

- Ağ trafiği: Kaynak/hedef IP/bağlantı noktası, paket boyutu ve ağ protokolü gibi Microsoft 'un altyapısından örneklenmiş ağ trafiği meta verilerini kullanır.

-   İş ortağı çözümleri: Güvenlik duvarları ve kötü amaçlı yazılımdan koruma çözümleri gibi tümleşik iş ortağı çözümlerinden güvenlik uyarılarını kullanır.

-   Sanal makineleriniz: , Sanal makinelerinizdeki Windows olay ve denetim günlükleri, IIS günlükleri, syslog iletileri ve kilitlenme dökümü dosyaları gibi güvenlik olaylarıyla ilgili yapılandırma bilgilerini ve bilgileri kullanır.

### <a name="data-protection"></a>Veri koruma

Müşterilerin tehditleri önlemesine, algılamasına ve yanıt vermesine yardımcı olmak amacıyla Azure Güvenlik Merkezi, güvenlikle ilgili veriler, meta veriler, olay günlükleri, kilitlenme dökümü dosyaları ve diğer verileri toplar ve işler. Microsoft kodlamadan hizmet çalıştırma konularına kadar her alanda uyumluluk ve güvenlik yönergelerine kesin olarak bağlı kalmaktadır.

-   **Veri**ayırma: Veriler, hizmetin tamamında her bir bileşen üzerinde mantıksal olarak ayrı tutulur. Tüm veriler kuruluşa göre etiketlenir. Bu etiketleme, veri yaşam döngüsü boyunca devam eder ve her bir hizmet katmanında uygulanır.

-   **Veri erişimi**: Microsoft personeli, güvenlik önerileri sağlamak ve olası güvenlik tehditlerini araştırmak için, kilitlenme bilgi döküm dosyaları, işlem oluşturma olayları, VM diski anlık görüntüleri ve yapıtlar dahil olmak üzere Azure hizmetleri tarafından toplanan veya çözümlenen bilgilere erişebilir. istemeden müşteri verilerini veya sanal makinelerinizdeki kişisel verileri içerebilir. Microsoft [çevrimiçi hizmet koşulları ve gizlilik bildirimi](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)'ne uyduk, bu durum Microsoft 'un müşteri verilerini kullanmamasından veya herhangi bir reklam veya benzer ticari amaçlar için bilgileri türettiğiniz durumdur.

-   **Veri kullanımı**: Microsoft, önleme ve algılama olanaklarımızı geliştirmek için birden çok kiracı genelinde görülen desenleri ve tehdit bilgilerini kullanır; Bu konuda [Gizlilik](https://www.microsoft.com/en-us/privacystatement/OnlineServices/)Bildirimizdeki gizlilik taahhütlerine göre yaptık.

### <a name="data-location"></a>Veri konumu

Azure Güvenlik Merkezi, kilitlenme döküm dosyalarının kısa ömürlü kopyalarını toplar ve açıktan yararlanma girişimlerinin ve başarılı uzlaşmaların kanıtı olarak analiz eder. Azure Güvenlik Merkezi bu analizi çalışma alanıyla aynı coğrafi bölgede gerçekleştirir ve analiz tamamlandığında kısa ömürlü kopyaları siler. Makine yapıları VM ile aynı bölgede merkezi olarak depolanır.

-   **Depolama hesaplarınız**: Sanal makinelerin çalıştığı her bölge için bir depolama hesabı belirtilir. Bunun yapılması verileri, verilerin toplandığı sanal makine ile aynı bölgede depolamanızı sağlar.

-   **Azure Güvenlik merkezi depolaması**: İş ortağı uyarıları, öneriler ve güvenlik sistem durumu da dahil olmak üzere güvenlik uyarıları hakkında bilgiler, şu anda Birleşik Devletler merkezi olarak depolanır. Bu bilgiler size güvenlik uyarısı, öneri veya sistem durumu sağlamak üzere gerektiğinde sanal makinelerinizden toplanan ilgili yapılandırma bilgilerini ve güvenlik olaylarını içerebilir.


## <a name="azure-monitor"></a>Azure İzleyici

[Azure izleyici Günlükleri güvenlik](../../security-center/security-center-monitoring.md) ve denetim çözümü, güvenlik olaylarının etkilerini en aza indirmenize yardımcı olan tüm kaynakları etkin bir şekilde izlemesini sağlar. Azure Izleyici günlükleri Güvenlik ve Denetim, kaynakları izlemek için kullanılabilen güvenlik etki alanları vardır. Güvenlik etki alanı, seçeneklere hızlı erişim sağlar, güvenlik izleme için aşağıdaki etki alanları daha ayrıntılı olarak ele alınmıştır:

-   Kötü amaçlı yazılım değerlendirmesi
-   Güncelleştirme değerlendirmesi
-   Kimlik ve erişim.

Azure Izleyici, belirli kaynak türleri hakkında bilgi işaretçileri sağlar. Hem Azure altyapısından (etkinlik günlüğünden) hem de her bir Azure kaynağından (tanılama günlükleri) veri üzerinde görselleştirme, sorgu, yönlendirme, uyarı verme, otomatik ölçeklendirme ve otomasyon olanakları sunar.

![Azure İzleyici](./media/operational-security/azure-operational-security-fig6.png)


Bulut uygulamaları birçok hareketli parça ile karmaşıktır. İzleme, uygulamanızın sağlıklı durumda kaldığından ve çalıştığından emin olmak için veri sağlar. Ayrıca, olası sorunları veya geçmişteki sorunları gidermenize yardımcı olur.

Ayrıca, uygulamanız hakkında derin Öngörüler elde etmek için izleme verilerini de kullanabilirsiniz. Bu bilgi, uygulama performansını veya bakımlılığını iyileştirebilmeniz ya da aksi takdirde el ile müdahale gerektiren eylemleri otomatikleştirmenize yardımcı olabilir.

### <a name="azure-activity-log"></a>Azure etkinlik günlüğü


Bu, aboneliğinizdeki kaynaklarda gerçekleştirilen işlemlere ilişkin Öngörüler sağlayan bir günlüğsü. Etkinlik günlüğü, abonelikleriniz için denetim düzlemi olaylarını bildirdiğinden daha önce "Denetim günlükleri" veya "Işletimsel Günlükler" olarak bilinirdi.

![Azure etkinlik günlüğü](./media/operational-security/azure-operational-security-fig7.png)

Etkinlik günlüğünü kullanarak, aboneliğinizdeki kaynaklar üzerinde herhangi bir yazma işlemi (PUT, POST, SILME) için ' ne, kim ve ne zaman ' i belirleyebilirsiniz. Ayrıca, işlemi ve ilgili diğer özellikleri durumunu anlayabilirsiniz. Etkinlik günlüğü, klasik modeli kullanan kaynaklara yönelik okuma (GET) işlemlerini veya işlemlerini içermez.

### <a name="azure-diagnostic-logs"></a>Azure tanılama günlükleri

Bu günlükler bir kaynak tarafından dağıtılır ve bu kaynağın çalışması hakkında zengin ve sık veriler sağlar. Bu günlüklerin içeriği kaynak türüne göre değişir.

Örneğin, Windows olay sistemi günlükleri VM 'Ler ve blob, tablo ve kuyruk günlükleri için bir tanılama günlüğü kategorisidir ve depolama hesapları için tanılama günlükleri kategorileridir.

Tanılama günlükleri, [etkinlik günlüğünden (eski adıyla denetim günlüğü veya Işlem günlüğü olarak bilinir)](../../azure-monitor/platform/activity-logs-overview.md)farklıdır. Etkinlik günlüğü, aboneliğinizdeki kaynaklarda gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Tanılama günlükleri, kaynağınızın kendisi tarafından gerçekleştirilen işlemler hakkında bilgi sağlar.

### <a name="metrics"></a>Ölçümler

Azure Izleyici, Azure 'da iş yüklerinizin performans ve sistem durumu hakkında görünürlük elde etmek için telemetri kullanmanıza olanak sağlar. Azure Telemetri verilerinin en önemli türü, çoğu Azure kaynağı tarafından yayınlanan ölçümleridir (performans sayaçları da denir). Azure Izleyici, izleme ve sorun giderme amacıyla bu [ölçümleri](../../monitoring/monitoring-data-collection.md) yapılandırmak ve kullanmak için çeşitli yollar sağlar. Ölçümler, önemli bir telemetri kaynağıdır ve aşağıdaki görevleri yerine etkinleştirecektir:

-   Bir portal grafiğinde ölçümlerini ayırarak ve bu grafiği bir panoya sabitleyerek kaynağınızın (VM, Web sitesi veya mantıksal uygulama gibi) **performansını izleyin** .

-   Bir ölçüm belirli bir eşiği aştığında kaynağınızın performansını etkileyen **bir sorun hakkında bildirim alın** .

-   Bir ölçüm belirli bir eşiği aştığında bir kaynağı otomatik olarak ölçeklendirme veya Runbook 'u tetikleme gibi **otomatikleştirilmiş eylemleri yapılandırın**.

-   Kaynağınızın performans veya kullanım eğilimlerini hakkında **Gelişmiş analiz** veya raporlama gerçekleştirin.

-   Uyumluluk veya denetim amaçlarıyla kaynağınızın performans veya sistem durumu geçmişini **arşivleyin** .

### <a name="azure-diagnostics"></a>Azure Tanılama

Bu, dağıtılmış bir uygulamada Tanılama verileri toplamayı sağlayan, Azure 'daki bir özelliktir. Tanılama uzantısını farklı kaynaklardan kullanabilirsiniz. Şu anda desteklenen [Azure bulut hizmeti Web ve çalışan rolleri](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), Microsoft Windows çalıştıran [Azure sanal makineleri](../../virtual-machines/windows/overview.md) ve [Service Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md). Diğer Azure hizmetlerinin kendi ayrı tanılamaları vardır.

## <a name="azure-network-watcher"></a>Azure ağ Izleyicisi

Ağ güvenliğinizi denetlemek, ağ güvenlik açıklarını saptamak ve BT güvenlik ve mevzuat idare modeliyle uyumluluk sağlamak için önemlidir. Güvenlik grubu görünümü ile yapılandırılan ağ güvenlik grubunu ve güvenlik kurallarını ve etkin güvenlik kurallarını alabilirsiniz. Uygulanan kuralların listesi ile açık ve ağ güvenlik açığı değerlendirme bağlantı noktalarını belirleyebilirsiniz.

[Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) , Azure 'Daki ve Azure 'dan bir ağ düzeyindeki koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Ağ Izleyicisi ile kullanılabilen ağ tanılama ve görselleştirme araçları, Azure 'da ağınızı anlamanıza, tanılamanıza ve elde etmenize yardımcı olur. Bu hizmet, paket yakalama, sonraki atlama, IP akışı doğrulama, güvenlik grubu görünümü, NSG akış günlükleri içerir. Senaryo düzeyi izleme, tek tek ağ kaynağı izlemeye karşılık olarak ağ kaynaklarının uçtan uca bir görünümünü sağlar.

![Azure ağ Izleyicisi](./media/operational-security/azure-operational-security-fig8.png)

Ağ Izleyicisi Şu anda aşağıdaki yeteneklere sahiptir:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Denetim günlükleri</a>** -ağ yapılandırmasının bir parçası olarak gerçekleştirilen işlemler günlüğe kaydedilir. Bu Günlükler Azure portal görüntülenebilir veya Power BI ya da üçüncü taraf araçları gibi Microsoft araçları kullanılarak alınabilir. Denetim günlükleri Portal, PowerShell, CLı ve REST API aracılığıyla kullanılabilir. Denetim günlükleri hakkında daha fazla bilgi için bkz. Kaynak Yöneticisi ile denetim işlemleri. Denetim günlükleri tüm ağ kaynaklarında yapılan işlemler için kullanılabilir.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP akışı doğrulama</a>** -akış bilgileri 5 demet paket parametrelerine (hedef IP, kaynak IP, hedef bağlantı noktası, kaynak bağlantı noktası ve protokol) bağlı olarak bir pakete izin verilip verilmediğini denetler. Paket bir ağ güvenlik grubu tarafından reddedilirse, paketi reddeden kural ve ağ güvenlik grubu döndürülür.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Sonraki atlama</a>** -Azure ağ dokusunda yönlendirilmekte olan paketlerin bir sonraki atlamasını belirler ve bu, Kullanıcı tanımlı hatalı yapılandırılmış yolların tanılanmasına olanak tanır.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Güvenlik grubu görünümü</a>** -bir VM 'ye uygulanan geçerli ve uygulanan güvenlik kurallarını alır.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG akış günlüğü</a>** -ağ güvenlik grupları için akış günlükleri, gruptaki güvenlik kuralları tarafından izin verilen veya reddedilen trafikle ilgili günlükleri yakalamanızı sağlar. Akış, 5 demet bilgi ile tanımlanır: kaynak IP, hedef IP, kaynak bağlantı noktası, hedef bağlantı noktası ve protokol.

## <a name="azure-storage-analytics"></a>Azure Depolama Analizi

[Depolama Analizi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) , toplanan işlem istatistiklerini ve bir depolama hizmetine yapılan isteklere ilişkin kapasite verilerini içeren ölçümleri saklayabilir. İşlemler hem API işlem düzeyinde hem de depolama hizmeti düzeyinde raporlanır ve depolama hizmeti düzeyinde kapasite raporlanır. Ölçüm verileri, depolama hizmeti kullanımını analiz etmek, depolama hizmetinde yapılan isteklerle ilgili sorunları tanılamak ve bir hizmeti kullanan uygulamaların performansını geliştirmek için kullanılabilir.

[Azure depolama Analizi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) günlüğe kaydetme gerçekleştirir ve depolama hesabı için ölçüm verileri sağlar. Bu verileri kullanarak istekleri izleyebilir, kullanım eğilimlerini çözümleyebilir ve depolama hesabınızdaki sorunları tanılayabilirsiniz. [BLOB, kuyruk ve tablo Hizmetleri](../../storage/common/storage-introduction.md)için depolama Analizi günlüğe kaydetme kullanılabilir. Depolama Analizi, başarılı ve başarısız isteklerle ilgili ayrıntılı bilgileri bir depolama hizmetine kaydeder.

Bu bilgiler, bireysel istekleri izlemek ve bir depolama hizmetiyle ilgili sorunları tanılamak için kullanılabilir. İstekler en iyi çaba temelinde günlüğe kaydedilir. Günlük girişleri yalnızca hizmet uç noktasında yapılan istekler varsa oluşturulur. Örneğin, bir depolama hesabının kendi BLOB uç noktasında etkinliği varsa ancak tablo veya sıra uç noktalarında etkinlik varsa, yalnızca blob hizmeti ile ilgili günlükler oluşturulur.

Depolama Analizi kullanmak için, izlemek istediğiniz her hizmet için tek tek etkinleştirmeniz gerekir. Bunu [Azure Portal](https://portal.azure.com/)etkinleştirebilirsiniz; Ayrıntılar için bkz. [Azure Portal bir depolama hesabını izleme](../../storage/common/storage-monitor-storage-account.md). Ayrıca, REST API veya istemci kitaplığı aracılığıyla Depolama Analizi programlı bir şekilde etkinleştirebilirsiniz. Her hizmet için Depolama Analizi tek tek etkinleştirmek üzere hizmet özelliklerini ayarlama işlemini kullanın.

Toplanan veriler, blob hizmeti ve tablo hizmeti API 'Leri kullanılarak erişilebilen, iyi bilinen bir blob 'da (günlük için) ve iyi bilinen tablolarda (ölçümler için) depolanır.

Depolama Analizi, depolama hesabınızın toplam sınırından bağımsız olan depolanan verilerin miktarına göre 20 TB 'lik bir sınıra sahiptir. Tüm Günlükler, bir depolama hesabı için Depolama Analizi etkinleştirildiğinde otomatik olarak oluşturulan $logs adlı kapsayıcıda [Blok Bloblarında](../../storage/common/storage-analytics.md) depolanır.

Depolama Analizi tarafından gerçekleştirilen aşağıdaki eylemler faturalandırılabilir:

-   Günlüğe kaydetme için blob oluşturma istekleri
-   Ölçümler için tablo varlıkları oluşturma istekleri.

> [!Note]
> Faturalandırma ve veri saklama ilkeleri hakkında daha fazla bilgi için bkz. [depolama Analizi ve faturalandırma](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> En iyi performans için, olası azaltmayı önlemek üzere sanal makineye bağlı yüksek oranda kullanılan disklerin sayısını sınırlandırmak istersiniz. Tüm diskler aynı anda yüksek oranda kullanılıyorsa, depolama hesabı daha büyük bir sayı diski destekleyebilir.

> [!Note]
> Depolama hesabı sınırları hakkında daha fazla bilgi için bkz. [Azure Storage ölçeklenebilirlik ve performans hedefleri](../../storage/common/storage-scalability-targets.md).


Aşağıdaki kimlik doğrulamalı ve anonim istek türleri günlüğe kaydedilir.

| Kimliği Doğrulandı  | Adsız|
| :------------- | :-------------|
| Başarılı istekler | Başarılı istekler |
|Zaman aşımı, azaltma, ağ, yetkilendirme ve diğer hatalar da dahil olmak üzere başarısız istekler | Başarısız ve başarılı istekler dahil, paylaşılan erişim Imzası (SAS) kullanan istekler |
| Başarısız ve başarılı istekler dahil, paylaşılan erişim Imzası (SAS) kullanan istekler |İstemci ve sunucu için zaman aşımı hataları |
|   Analiz verilerine yönelik istekler |    304 hata koduyla başarısız olan istek ALıNAMADı (değiştirilmedi) |
| Günlük oluşturma veya silme gibi Depolama Analizi kendisi tarafından yapılan istekler günlüğe kaydedilmez. Günlüğe kaydedilen verilerin tam listesi, [depolama Analizi günlüğe kaydedilmiş işlemler ve durum iletileri](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) ve [depolama Analizi günlük biçimi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) konularında belgelenmiştir. | Diğer tüm başarısız anonim istekler günlüğe kaydedilmez. Günlüğe kaydedilen verilerin tam listesi, [depolama Analizi günlüğe kaydedilmiş işlemler ve durum iletileri](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) ve [depolama Analizi günlük biçiminde](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format)belgelenmiştir. |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD Ayrıca Multi-Factor Authentication, cihaz kaydı, self servis parola yönetimi, Self Servis Grup Yönetimi, ayrıcalıklı hesap yönetimi, rol tabanlı erişim dahil olmak üzere tam kimlik yönetimi özellikleri paketini içerir. Denetim, uygulama kullanımı izleme, zengin denetim ve güvenlik izleme ve uyarı.

-   Azure AD çok faktörlü kimlik doğrulaması ve koşullu erişim ile uygulama güvenliğini geliştirme.

-   Güvenlik raporlama ve izleme ile uygulama kullanımını izleyin ve işinizi gelişmiş tehditlere karşı koruyun.

Azure Active Directory (Azure AD), dizininize yönelik güvenlik, etkinlik ve denetim raporlarını içerir. [Azure Active Directory Denetim raporu,](../../active-directory/active-directory-reporting-azure-portal.md) müşterilerin Azure Active Directory oluşan ayrıcalıklı eylemleri belirlemesine yardımcı olur. Ayrıcalıklı eylemler, yükseltme değişiklikleri (örneğin, rol oluşturma veya parola sıfırlama), ilke yapılandırmalarını değiştirme (örneğin parola ilkeleri) veya dizin yapılandırmasındaki değişiklikler (örneğin, etki alanı Federasyon ayarlarındaki değişiklikler) içerir.

Raporlar, olay adı, eylemi gerçekleştiren aktör, değişiklikten etkilenen hedef kaynak ve Tarih ve saat (UTC) için denetim kaydı sağlar. Müşteriler, [Denetim günlüklerinizi görüntüleme](../../active-directory/reports-monitoring/overview-reports.md)başlığı altında açıklandığı gibi, Azure Active Directory [Azure Portal](https://portal.azure.com/)aracılığıyla denetim olaylarının listesini alabilir. Kapsama dahil olan raporların listesi şu şekildedir:

| Güvenlik raporları  | Etkinlik raporları| Denetim raporları |
| :------------- | :-------------| :-------------|
|Bilinmeyen kaynaklardan gerçekleştirilen oturum açma işlemleri | Uygulama kullanımı: özet | Dizin denetimi raporu |
|Birden çok hatadan sonra gerçekleştirilen oturum açma işlemleri | Uygulama kullanımı: ayrıntılı |   |
|Birden çok coğrafyadan gerçekleştirilen oturum açma işlemleri | Uygulama panosu |  |
|Şüpheli etkinliğin olduğu IP adreslerinden oturum açma işlemleri |Hesap hazırlama hataları |  |
|Düzensiz oturum açma etkinliği |Bireysel kullanıcı cihazları |  |
|Muhtemelen virüs bulaşmış cihazlardan gerçekleştirilen oturum açma işlemleri |Bireysel kullanıcı Etkinliği |   |
|Anormal oturum açma etkinliği gösteren kullanıcılar |Grup etkinlik raporu |   |
| |Parola Sıfırlama Kayıt Etkinlik Raporu |   |
| |Parola sıfırlama etkinliği |   |



Bu raporların verileri, uygulamanızın SıEM sistemleri, denetim ve iş zekası araçları gibi yararlı olabilir. Azure AD raporlama [API 'leri](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) , BIR dizi REST tabanlı API aracılığıyla verilere programlı erişim sağlar. Çeşitli programlama dilleri ve araçlarından bu API 'Leri çağırabilirsiniz.

Azure AD denetim raporundaki olaylar 180 gün boyunca tutulur.

> [!Note]
> Raporlarda bekletme hakkında daha fazla bilgi için bkz. [Azure Active Directory rapor bekletme ilkeleri](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

[Denetim olaylarını](../../active-directory/active-directory-reporting-activity-audit-logs.md) daha uzun bekletme dönemlerinde depolamayla ilgilenen müşteriler Için, Raporlama API 'si düzenli olarak denetim olaylarını ayrı bir veri deposuna çekmek üzere kullanılabilir.

## <a name="summary"></a>Özet

Bu makale, kuruluşunuzun BT altyapısını yönetmenize yardımcı olan yazılım ve hizmetleri sunarken gizliliğinizi korumayı ve verilerinizi güvenli hale getirmenize özetler. Microsoft, verilerine başkalarına güvendiklerinde bu güvenin ciddi bir güvenlik gerektirdiğini algılar. Microsoft kodlamadan hizmet çalıştırma konularına kadar her alanda uyumluluk ve güvenlik yönergelerine kesin olarak bağlı kalmaktadır. Verilerin güvenliğini sağlama ve koruma, Microsoft 'ta en önemli önceliktir.

Bu makalede açıklanmaktadır

-   Azure Izleyici paketinde verilerin toplanması, işlenmesi ve güvenliğini belirleme.

-   Birden çok veri kaynağında olayları hızla çözümleyin. Güvenlik ihlalinden doğabilecek zararları azaltmak için güvenlik risklerini tanımlayın, tehdit ve saldırıların kapsamını ve etkisini anlayın.

-   Giden kötü amaçlı IP trafiğini ve kötü amaçlı tehdit türlerini görselleştirerek saldırı desenlerini tanımlayın. Platformundan bağımsız olarak tüm ortamınızın güvenlik duruşunu anlayın.

-   Bir güvenlik veya uyumluluk denetimi için gereken tüm günlük ve olay verilerini yakalayın. Tüm, aranabilir ve dışarı aktarılabilir günlük ve olay veri kümesiyle bir güvenlik denetimi sağlamak için gereken saat ve kaynakları bölme.

<ul>
<li>Varlıklarınızı yakın bir şekilde korumak için güvenlikle ilgili olayları, denetimi ve ihlal analizini toplayın:</li>
<ul>
<li>Güvenlik sonrası</li>
<li>Önemli sorun</li>
<li>Özet tehditler</li>
</ul>
</ul>

## <a name="next-steps"></a>Sonraki Adımlar

- [Tasarım ve operasyonel güvenlik](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft, kendi hizmet ve yazılımlarını, bulut altyapısının dayanıklı ve saldırılardan savunma olmasını sağlamaya yardımcı olmak için güvenliği göz önünde bulundurularak tasarlar.

- [Azure Izleyici günlükleri | Güvenlik ve Uyumluluk](https://www.microsoft.com/cloud-platform/security-and-compliance)

Daha akıllı ve etkili tehdit algılama işlemleri gerçekleştirmek için Microsoft güvenlik verilerini ve analizini kullanın.

- [Azure Güvenlik Merkezi planlama ve işlemler](../../security-center/security-center-planning-and-operations-guide.md) Kuruluşunuzun güvenlik gereksinimlerine ve bulut yönetimi modeline göre güvenlik merkezi kullanımını iyileştirmek için izleyebileceğiniz bir dizi adım ve görev.

