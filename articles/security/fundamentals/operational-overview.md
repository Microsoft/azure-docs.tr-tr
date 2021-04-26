---
title: Azure operasyonel güvenliğe genel bakış | Microsoft Docs
description: Bu genel bakışta Azure operasyonel güvenlik hakkında bilgi edinin. İşlemsel güvenlik, varlık koruma hizmetleri, denetimler ve Özellikler anlamına gelir.
services: security
documentationcenter: na
author: unifycloud
manager: rkarlin
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: tomsh
ms.openlocfilehash: 4bc30fbf342a9bc85b52c9f88ce7ca1df3c36e23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595501"
---
# <a name="azure-operational-security-overview"></a>Azure operasyonel güvenliğe genel bakış

[Azure operasyonel güvenlik](./operational-security.md) , kullanıcıların verilerini, uygulamalarını ve diğer varlıklarını Microsoft Azure korumak için kullanıcılara sunulan hizmetleri, denetimleri ve özellikleri ifade eder. Bu, Microsoft 'a özgü olan çeşitli yetenekler aracılığıyla elde edilen bilgileri içeren bir çerçevedir. Bu yetenekler Microsoft Security Development Lifecycle (SDL), Microsoft Güvenlik Yanıt Merkezi programını ve siber güvenlik tehdidi yatay ' ın derinlemesine farkında ' nı içerir.

## <a name="azure-management-services"></a>Azure Yönetim Hizmetleri

BT operasyon ekibi, bu sistemlerin kararlılığı ve güvenliği de dahil olmak üzere veri merkezi altyapısını, uygulamaları ve verileri yönetmekten sorumludur. Ancak, karmaşık BT ortamlarının arttırılmasıyla ilgili güvenlik öngörüleri elde etmek, kuruluşların birden çok güvenlik ve Yönetim sisteminden veri Cobble olmasını gerektirir.

[Microsoft Azure izleme günlükleri](../../azure-monitor/overview.md) , şirket içi ve bulut altyapınızı yönetmenize ve korumanıza yardımcı olan bulut tabanlı bir BT yönetimi çözümüdür. Temel işlevselliği, Azure 'da çalışan aşağıdaki hizmetler tarafından sağlanır. Azure, şirket içi ve bulut altyapınızı yönetmenize ve korumanıza yardımcı olan birden çok hizmet içerir. Her hizmet belirli bir yönetim işlevi sağlar. Farklı yönetim senaryolarına ulaşmak için Hizmetleri birleştirebilirsiniz. 

### <a name="azure-monitor"></a>Azure İzleyici

[Azure izleyici](../../azure-monitor/overview.md) , yönetilen kaynaklardan verileri Merkezi veri depolarına toplar. Bu veriler, API aracılığıyla sunulan olayları, performans verilerini veya özel verileri içerebilir. Veriler toplandıktan sonra, uyarı, analiz ve dışarı aktarma için kullanılabilir.

Verileri çeşitli kaynaklardan birleştirebilir ve mevcut şirket içi ortamınızdan Azure hizmetinizdeki verileri birleştirebilirsiniz. Azure Izleyici günlükleri Ayrıca, tüm eylemlerin tüm veri türlerinde kullanılabilmesi için, verilerin toplanmasını bu verilerde gerçekleştirilen eylemden açıkça ayırır.

### <a name="automation"></a>Otomasyon

[Azure Otomasyonu](../../automation/automation-intro.md) , bir bulut ve kurumsal ortamda yaygın olarak gerçekleştirilen el ile, uzun süreli, hataya açık ve sık sık tekrarlanan görevleri otomatikleştirmenizi sağlayan bir yol sağlar. Zaman kazandırır ve yönetim görevlerinin güvenilirliğini artırır. Bu görevleri düzenli aralıklarla otomatik olarak gerçekleştirilmesini de zamanlar. İşlem runbook 'ları kullanarak işlemleri otomatikleştirebilir veya Istenen durum yapılandırmasını kullanarak yapılandırma yönetimini otomatik hale getirebilirsiniz.

### <a name="backup"></a>Backup

[Azure Backup](../../backup/backup-overview.md) , Microsoft bulut verilerinizi yedeklemek (veya korumak) ve geri yüklemek Için kullanabileceğiniz Azure tabanlı hizmettir. Azure Backup, mevcut şirket içi veya site dışı yedekleme çözümünüzü güvenilir, güvenli ve maliyet rekabetçi bir bulut tabanlı çözümle değiştirir.

Azure Backup, karşıdan yüklediğiniz ve uygun bilgisayar ya da sunucuda ya da bulutta dağıttığınız bileşenleri sunar. Dağıtacağınız bileşen veya aracı, korumak istediğiniz nesnelere göre değişiklik gösterir. Tüm Azure Backup bileşenleri (Şirket içi veya bulutta verileri koruduğunuz), verileri Azure 'daki bir Azure kurtarma hizmetleri kasasına yedeklemek için kullanılabilir.

Daha fazla bilgi için [Azure Backup bileşenleri tablosuna](../../backup/backup-overview.md#what-can-i-back-up)bakın.

### <a name="site-recovery"></a>Site Recovery

[Azure Site Recovery](https://azure.microsoft.com/documentation/services/site-recovery) , şirket içi sanal ve fiziksel makinelerin Azure 'a veya ikincil bir siteye çoğaltılmasını düzenleyerek iş sürekliliği sağlar. Birincil siteniz kullanılamıyorsa, kullanıcıların çalışmaya devam edebilmesi için ikincil konuma yük devreder. Sistemler çalışma sırasına geri dönzaman geri döneolursunuz. Daha akıllı ve etkili tehdit algılama işlemleri gerçekleştirmek için Azure Güvenlik Merkezi 'ni kullanın.

## <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory (Azure AD)](../../active-directory/manage-apps/what-is-application-management.md) şunları sağlayan kapsamlı bir kimlik hizmetidir:

-   Bulut hizmeti olarak kimlik ve erişim yönetimi (ıAM) sunar.
-   Merkezi erişim yönetimi, çoklu oturum açma (SSO) ve raporlama sağlar.
-   Salesforce, Google Apps, Box ve Concur dahil olmak üzere Azure Marketi 'nde [binlerce uygulama](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) için tümleşik erişim yönetimini destekler.

Azure AD, aşağıdakiler dahil olmak üzere tam [kimlik yönetimi özellikleri](./identity-management-overview.md#security-monitoring-alerts-and-machine-learning-based-reports)paketini de içerir:

- [Çok faktörlü kimlik doğrulaması](../../active-directory/authentication/concept-mfa-howitworks.md)
- [Self servis parola yönetimi](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/)
- [Self servis grup yönetimi](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md)
- [Ayrıcalıklı hesap yönetimi](../../active-directory/privileged-identity-management/pim-configure.md)
- [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)
- [Uygulama kullanımı izleme](../../active-directory/hybrid/whatis-hybrid-identity.md)
- [Zengin denetim](../../active-directory/reports-monitoring/concept-audit-logs.md)
- [Güvenlik izleme ve uyarı](../../security-center/security-center-managing-and-responding-alerts.md)

Azure Active Directory, iş ortaklarınız ve müşterileriniz için yayımladığınız tüm uygulamaların (iş veya tüketici) aynı kimlik ve erişim yönetimi özelliklerine sahip olması gerekir. Bu, işletimsel maliyetlerinizi önemli ölçüde azaltmanıza olanak sağlar.

## <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md) , Azure kaynaklarınızın güvenliğine yönelik artırılmış görünürlük (ve üzerinde denetim) ile tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Abonelikleriniz genelinde tümleşik güvenlik izleme ve ilke yönetimi sağlar. Başka türlü fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

Sanal makinenizin güvenlik ayarlarını ve tehditleri izlemeye yönelik görünürlük sağlayarak Azure 'daki [sanal makine (VM) verilerini koruma](../../security-center/security-center-introduction.md) . Güvenlik Merkezi, sanal makinelerinizi şu açılardan izleyebilir:

- Önerilen yapılandırma kuralları ile işletim sistemi güvenlik ayarları.
- Sistem güvenliği ve eksik olan kritik güncelleştirmeler.
- Uç nokta koruma önerileri.
- Disk şifreleme doğrulaması.
- Ağ tabanlı saldırılar.

Güvenlik Merkezi, [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)kullanır. Azure RBAC, Azure 'daki kullanıcılara, gruplara ve hizmetlere atanabilecek [yerleşik roller](../../role-based-access-control/built-in-roles.md) sağlar.

Güvenlik Merkezi, güvenlik sorunlarını ve güvenlik açıklarını belirlemek için kaynaklarınızın yapılandırmasını değerlendirir. Güvenlik Merkezi 'nde, bir kaynakla ilgili bilgileri, yalnızca bir kaynağın ait olduğu abonelik veya kaynak grubu için sahip, katkıda bulunan veya okuyucu rolüne atadığınız zaman görürsünüz.

>[!Note]
>Güvenlik Merkezi 'nde roller ve izin verilen eylemler hakkında daha fazla bilgi edinmek için bkz. [Azure Güvenlik Merkezi 'Nde izinler](../../security-center/security-center-permissions.md).

Güvenlik Merkezi Microsoft Monitoring Agent kullanır. Bu, Azure Izleyici hizmetinin kullandığı aracıdır. Bu aracıdan toplanan veriler, sanal makinenin coğrafi konumunu hesaba katarak Azure aboneliğinizle ilişkili mevcut bir Log Analytics [çalışma alanında](../../azure-monitor/logs/manage-access.md) veya yeni bir çalışma alanında depolanır.

## <a name="azure-monitor"></a>Azure İzleyici

Bulut uygulamanızdaki performans sorunları işinizi etkileyebilir. Birden çok bağlantılı bileşen ve sık kullanılan yayınlar sayesinde, herhangi bir zamanda düşürülme gerçekleşecektir. Bir uygulama geliştiriyorsanız, kullanıcılarınız genellikle test sırasında bulamadığınız sorunları bulur. Bu sorunlar hakkında hemen bilmeniz gerekir ve sorunları tanılamaya ve düzeltmeye yönelik araçlara sahip olmanız gerekir.

[Azure izleyici](../../azure-monitor/overview.md) , Azure üzerinde çalışan hizmetleri izlemeye yönelik temel araçtır. Bu, bir hizmetin ve çevreleyen ortamın verimlilik hakkında altyapı düzeyinde veriler sağlar. Uygulamalarınızı Azure 'da yönetiyorsanız ve kaynak ölçeği büyütme veya küçültme konusunda karar verirseniz, Azure Izleyici 'nin başlangıç yeri vardır.

Uygulamanız hakkında derin Öngörüler elde etmek için izleme verilerini de kullanabilirsiniz. Bu bilgi, uygulama performansını veya bakımlılığını iyileştirebilmeniz ya da aksi takdirde el ile müdahale gerektiren eylemleri otomatikleştirmenize yardımcı olabilir.

Azure Izleyici aşağıdaki bileşenleri içerir.

### <a name="azure-activity-log"></a>Azure Etkinlik Günlüğü

[Azure etkinlik günlüğü](../../azure-monitor/essentials/platform-logs-overview.md) , aboneliğinizdeki kaynaklarda gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Abonelikleriniz için denetim düzlemi olaylarını bildirdiğinden, daha önce "denetim günlüğü" veya "Işletimsel günlük" olarak bilinirdi.

### <a name="azure-diagnostic-logs"></a>Azure tanılama günlükleri

[Azure tanılama günlükleri](../../azure-monitor/essentials/platform-logs-overview.md) bir kaynak tarafından dağıtılır ve bu kaynağın çalışması hakkında zengin ve sık veriler sağlar. Bu günlüklerin içeriği kaynak türüne göre değişir.

Windows olay sistemi günlükleri, VM 'Ler için bir tanılama günlüğü kategorisidir. Blob, tablo ve kuyruk günlükleri, depolama hesapları için tanılama günlüğü kategorileridir.

Tanılama günlükleri [etkinlik günlüğünden](../../azure-monitor/essentials/platform-logs-overview.md)farklı. Etkinlik günlüğü, aboneliğinizdeki kaynaklarda gerçekleştirilen işlemlere ilişkin öngörüler sağlar. Tanılama günlükleri, kaynağınızın kendisi tarafından gerçekleştirilen işlemlere ilişkin öngörüler sağlar.

### <a name="metrics"></a>Ölçümler

Azure Izleyici, Azure 'da iş yüklerinizin performansına ve sistem durumuna ilişkin görünürlük sunan telemetri sağlar. Azure Telemetri verilerinin en önemli türü, çoğu Azure kaynağı tarafından yayınlanan [ölçümleridir](../../azure-monitor/data-platform.md) (performans sayaçları da denir). Azure Izleyici, izleme ve sorun giderme amacıyla bu ölçümleri yapılandırmak ve kullanmak için çeşitli yollar sağlar.

### <a name="azure-diagnostics"></a>Azure Tanılama

Azure Tanılama dağıtılan bir uygulamadaki tanılama verilerinin toplanmasını mümkün. Tanılama uzantısını çeşitli kaynaklardan kullanabilirsiniz. Şu anda desteklenen [Azure bulut hizmeti rolleri](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), Microsoft Windows çalıştıran [Azure sanal makineleri](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) ve [Azure Service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md).

## <a name="azure-network-watcher"></a>Azure Ağ İzleyicisi

Müşteriler, sanal ağlar, Azure ExpressRoute, Azure Application Gateway ve yük dengeleyiciler gibi bireysel ağ kaynaklarını düzenleyerek ve oluşturarak Azure 'da uçtan uca bir ağ oluşturur. İzleme, ağ kaynaklarının her birinde kullanılabilir.

Uçtan uca ağ, kaynaklar arasında karmaşık yapılandırmalara ve etkileşimlere sahip olabilir. Sonuç, [Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md)aracılığıyla senaryo tabanlı izleme gerektiren karmaşık senaryolardır.

Ağ Izleyicisi, Azure ağınızı izlemeyi ve tanılamayı basitleştirir. Ağ izleyicisinden tanılama ve görselleştirme araçlarını kullanarak şunları yapabilirsiniz:

- Bir Azure sanal makinesinde uzak paket yakalamalarını alın.
- Akış günlüklerini kullanarak ağ trafiğiniz hakkında öngörüler elde edin.
- Azure VPN Gateway ve bağlantılarını tanılayın.

Ağ Izleyicisi Şu anda aşağıdaki yeteneklere sahiptir:

- [Topoloji](../../network-watcher/view-network-topology.md): bir kaynak grubundaki ağ kaynakları arasındaki çeşitli bağlantılar ve ilişkilerin bir görünümünü sağlar.
- [Değişken paket yakalama](../../network-watcher/network-watcher-packet-capture-overview.md): sanal bir makinenin içindeki ve içindeki paket verilerini yakalar. Zaman ve boyut sınırlamaları ayarlama gibi gelişmiş filtreleme seçenekleriyle ince ayarlı denetimler çok yönlülük getirir. Paket verileri bir blob deposunda veya. Cap biçimindeki yerel diskte depolanabilir.
- [IP akışı doğrulama](../../network-watcher/network-watcher-ip-flow-verify-overview.md): akış bilgileri (hedef IP, kaynak IP, hedef bağlantı noktası, kaynak bağlantı noktası ve protokol) için 5 demet paket parametrelerine bağlı olarak bir pakete izin verildiğini veya reddedildiğini denetler. Bir güvenlik grubu paketi reddettiğinde, paketi reddeden kural ve Grup döndürülür.
- [Sonraki atlama](../../network-watcher/network-watcher-next-hop-overview.md): Azure ağ dokusunda yönlendirilmekte olan paketlerin bir sonraki atlamasını belirler; böylece, Kullanıcı tanımlı yanlış yapılandırılmış yollar tanılayabilirsiniz.
- [Güvenlik grubu görünümü](../../network-watcher/network-watcher-security-group-view-overview.md): bir VM 'ye uygulanan geçerli ve uygulanan güvenlik kurallarını alır.
- [Ağ güvenlik grupları Için NSG akış günlükleri](../../network-watcher/network-watcher-nsg-flow-logging-overview.md): gruptaki güvenlik kuralları tarafından izin verilen veya reddedilen trafikle ilgili günlükleri yakalamanızı sağlar. Akış, 5 demet bilgi tarafından tanımlanır: kaynak IP, hedef IP, kaynak bağlantı noktası, hedef bağlantı noktası ve protokol.
- [Sanal ağ geçidi ve bağlantı sorunlarını giderme](../../network-watcher/network-watcher-troubleshoot-manage-rest.md): sanal ağ geçitlerinin ve bağlantılarının sorunlarını giderme özelliğini sağlar.
- [Ağ abonelik limitleri](../../network-watcher/network-watcher-monitoring-overview.md): ağ kaynak kullanımını sınırlara karşı görüntülemenize olanak sağlar.
- [Tanılama günlükleri](../../network-watcher/network-watcher-monitoring-overview.md): bir kaynak grubundaki ağ kaynakları için tanılama günlüklerini etkinleştirmek veya devre dışı bırakmak için tek bir bölme sağlar.

Daha fazla bilgi için bkz. [ağ Izleyicisini yapılandırma](../../network-watcher/network-watcher-create.md).

## <a name="cloud-service-provider-access-transparency"></a>Bulut hizmeti sağlayıcısı erişim saydamlığı

[Microsoft Azure için müşteri kasası](customer-lockbox-overview.md) , bir Microsoft desteği mühendisinin bir sorunu çözmek için verilerinize erişmesi gerekebilme olanağı sunan Azure Portal ile tümleştirilmiş bir hizmettir.
Bir Microsoft Desteği mühendisinin bu sorunu çözmek için yükseltilmiş izinler gerektirdiği bir hata ayıklama uzaktan erişim sorunu gibi çok az örnek vardır. Bu gibi durumlarda, Microsoft mühendisleri, hizmetle sınırlı erişime sahip sınırlı ve zamana sınırlı yetkilendirme sağlayan tam zamanında erişim hizmeti kullanır.  
Microsoft her zaman erişim için müşteri onayı edinirken Müşteri Kasası artık Azure portalından bu istekleri gözden geçirebilir ve onaylayabilir veya reddedebilirsiniz. İstek onaylanana kadar Microsoft destek mühendislerine erişim verilmeyecektir.

## <a name="standardized-and-compliant-deployments"></a>Standartlaştırılmış ve uyumlu dağıtımlar

[Azure şemaları](../../governance/blueprints/overview.md) , bulut mimarlarını ve merkezi bilgi teknolojisi gruplarını, bir kuruluşun standartlarına, desenlerine ve gereksinimlerine uyan ve bunları uygulayan yinelenebilir bir Azure kaynakları kümesi tanımlamak için etkinleştirir.  
Bu, DevOps takımlarının yeni ortamları hızlı bir şekilde oluşturup hazırlamanızı ve bunları kurumsal uyumluluğun bulunduğu altyapıyla yaradıklarından güvenmesini mümkün kılar.
Planlar çeşitli kaynak şablonlarının ve diğer yapıtların dağıtımını düzenlemek için bildirim temelli bir yol sağlar:

- Rol Atamaları
- İlke Atamaları
- Azure Resource Manager şablonları
- Kaynak Grupları

## <a name="devops"></a>DevOps

[Geliştirici işlemleri (DevOps)](https://azure.microsoft.com/overview/what-is-devops/) uygulaması geliştirmeden önce, takımlar bir yazılım programı için iş gereksinimlerini toplamaya ve kod yazmaya ücretsizdir. Ardından, ayrı bir QA ekibi, programı yalıtılmış bir geliştirme ortamında test ediyor. Gereksinimler karşılanıyorsa, QA ekibi dağıtılacak işlemler için kodu serbest bıraktı. Dağıtım takımları ağ ve veritabanı gibi gruplara daha fazla parçalanmıştı. Bir yazılım programının bir bağımsız ekibe "her ne kadar her" bir şekilde oluşturulduğu "her seferinde performans sorunları oluşur.

DevOps, ekiplerin daha hızlı ve daha fazla sayıda daha güvenli, daha fazla kaliteli çözüm sunmalarını sağlar. Müşteriler, yazılım ve Hizmetleri kullanırken dinamik ve güvenilir bir deneyim bekler. Ekipler, yazılım güncelleştirmelerinde hızla yineleyebilir ve güncelleştirmelerin etkisini ölçmelidir. Sorunları gidermek veya daha fazla değer sağlamak için yeni geliştirme yinelemeleriyle hızla yanıt vermelidir.  

Microsoft Azure gibi bulut platformları, geleneksel performans sorunlarını kaldırmış ve metalaşmasına altyapısına yardımcı olmuştur. Her işletmeden yazılım, anahtar farklılama ve iş sonuçları faktörü olarak yeniden yapılır. Kuruluş, geliştirici veya BT çalışanı DevOps hareketini önyükleyebilir veya kaçınmamalıdır.

Yetişkinlere yönelik DevOps uygulayıcıları aşağıdaki uygulamalardan birkaçını benimseyin. Bu uygulamalar, iş senaryolarına göre stratejileri oluşturacak [kişileri içerir](/azure/devops/learn/what-is-devops-culture) . Araç araçları çeşitli uygulamaların otomatikleştirilmesine yardımcı olabilir.

- [Çevik planlama ve proje yönetimi](https://www.visualstudio.com/learn/what-is-agile/) teknikleri, işleri planlamak ve Sprint 'ler halinde yalıtmak, takım kapasitesini yönetmek ve takımların değişen iş ihtiyaçlarına hızla uyum sağlamasına yardımcı olmak için kullanılır.
- [Genellikle git Ile sürüm denetimi](/azure/devops/learn/git/what-is-git), dünyanın herhangi bir yerinden bulunan ekiplerin kaynak paylaşmasını ve yazılım geliştirme araçlarıyla tümleşmesini sağlar ve yayın işlem hattını otomatik hale getirir.
- [Sürekli tümleştirme](/azure/devops/learn/what-is-continuous-integration) , kodun sürekli olarak birleştirilmesini ve test edilmesini sağlar ve bu da kusurların erken olarak bulunmasına yol açar.  Diğer avantajlar, birleştirme sorunları ve geliştirme ekipleri için hızlı geri bildirimde bulunmak için harcanan süreyi daha az zaman alır.
- Yazılım çözümlerinin üretime ve test ortamlarına [sürekli teslimi](/azure/devops/learn/what-is-continuous-delivery) , kuruluşların hataları hızla düzeltmesine ve sürekli değişen iş gereksinimlerine yanıt vermesine yardımcı olur.
- Çalışan uygulamaların [izlenmesi](/azure/devops/learn/what-is-monitoring) --uygulama durumu için üretim ortamları ve müşteri kullanımı dahil olmak üzere, kuruluşların bir varsayım oluşturmasına ve stratejileri hızlı bir şekilde doğrulamasına veya kanıtlarına yardımcı olur.  Zengin veriler yakalanır ve çeşitli günlük biçimlerinde depolanır.
- [Kod olarak altyapı (IAC)](/azure/devops/learn/what-is-infrastructure-as-code) , güvenli, kararlı uygulama barındırma platformları sunmaya yardımcı olmak üzere ağların ve sanal makinelerin oluşturulması ve test edilmesi için Otomasyon ve doğrulamanın doğrulanmasını sağlayan bir uygulamadır.
- [Mikro hizmetler](/azure/devops/learn/what-are-microservices) mimarisi, iş kullanım örneklerini küçük yeniden kullanılabilir hizmetlere ayırmak için kullanılır.  Bu mimari ölçeklenebilirlik ve verimliliği sunar.

## <a name="next-steps"></a>Sonraki adımlar

Güvenlik ve Denetim çözümü hakkında bilgi edinmek için aşağıdaki makalelere bakın:

- [Güvenlik ve uyumluluk](https://azure.microsoft.com/overview/trusted-cloud/)
- [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md)
- [Azure İzleyici](../../azure-monitor/overview.md)