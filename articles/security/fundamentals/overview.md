---
title: Azure güvenliğine giriş | Microsoft Docs
description: Azure Güvenlik 'e, çeşitli hizmetlerine ve bu genel bakışı okuyarak nasıl çalıştığını tanıtın.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: TomSh
ms.openlocfilehash: b5f9df4e6f682b5d1e9e3cd35affe6e4191e3d53
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047788"
---
# <a name="introduction-to-azure-security"></a>Azure güvenliğe giriş

## <a name="overview"></a>Genel Bakış

Güvenliğin bulutta bir iş olduğunu ve Azure güvenliğiyle ilgili doğru ve güncel bilgileri nerede bulabileceğinizi biliyoruz. Uygulamalarınız ve hizmetleriniz için Azure kullanmanın en iyi nedenlerinden biri, kendi sunduğu güvenlik araçları ve özellikleri dizisinden faydalanabilir. Bu araçlar ve yetenekler, güvenli Azure platformunda güvenli çözümler oluşturmayı olanaklı hale getirmeye yardımcı olur. Microsoft Azure, müşteri verilerinin gizliliğini, bütünlüğünü ve kullanılabilirliğini sağlar. Ayrıca, saydam sorumluluklığa de olanak tanır.

Bu makalede, Azure ile kullanılabilen güvenliğe kapsamlı bir bakış sunulmaktadır.

### <a name="azure-platform"></a>Azure platformu

Azure, geniş bir işletim sistemi, programlama dili, çerçeve, araç, veritabanı ve cihaz seçimini destekleyen genel bir bulut hizmeti platformudur. Docker tümleştirmesiyle Linux kapsayıcıları çalıştırabilir; JavaScript, Python, .NET, PHP, Java ve Node.js; uygulamalar oluşturun; iOS, Android ve Windows cihazlar için arka uçlar oluşturun.

Azure genel bulut Hizmetleri, milyonlarca geliştirici ve BT uzmanlarının zaten kullandığı ve güvendiği teknolojilerin aynısını destekler. Üzerinde derleme yaptığınızda veya BT varlıklarını ' ye geçirdiğinizde, bu kuruluşun, uygulama ve verilerinizi hizmetlerle ve verilerinizi, bulut tabanlı varlıklarınızın güvenliğini yönetmek için sağladığı denetimlerle koruma yeteneklerine bağlı olursunuz.

Azure 'un altyapısı, tesislerden milyonlarca müşteriyi aynı anda barındırmak için uygulamalara göre tasarlanmıştır ve işletmelerin güvenlik gereksinimlerini karşılayabilecekleri güvenilir bir temel sağlar.

Ayrıca, Azure geniş kapsamlı bir güvenlik seçenekleri dizisi ve bunları, kuruluşunuzun dağıtımlarınızın benzersiz gereksinimlerini karşılayacak şekilde güvenliği özelleştirebilmeniz için denetlemenize olanak tanır. Bu belge, Azure Güvenlik yeteneklerinin bu gereksinimleri karşılamanıza nasıl yardımcı olduğunu anlamanıza yardımcı olur.

> [!Note]
> Bu belgenin birincil odağı, uygulama ve hizmetlerinize ilişkin güvenliği özelleştirmek ve artırmak için kullanabileceğiniz müşteriye yönelik denetimleridir.
>
> Microsoft 'un Azure platformunun kendisini nasıl güvenlik altına aldığı hakkında daha fazla bilgi için bkz. [Azure altyapı güvenliği](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Azure Güvenlik Özellikleri Özeti

Bulut hizmeti modeline bağlı olarak, uygulamanın veya hizmetin güvenliğini yönetmekten sorumlu olan değişken sorumluluğu vardır. Azure platformunda sunulan ve yerleşik özellikler aracılığıyla bu sorumlulukları ve bir Azure aboneliğine dağıtılabilecek iş ortağı çözümlerini kullanarak bu sorumlulukları karşılamakta yardımcı olacak özellikler vardır.

Yerleşik yetenekler altı işlevsel alanda düzenlenmiştir: Işlemler, uygulamalar, depolama, ağ, Bilgi Işlem ve kimlik. Bu altı alanda Azure platformunda bulunan özellikler ve yetenekler hakkında ek ayrıntı Özet bilgiler aracılığıyla sağlanır.

## <a name="operations"></a>Operations

Bu bölümde güvenlik işlemlerinde temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="azure-security-center"></a>Azure Güvenlik Merkezi

[Güvenlik Merkezi](../../security-center/security-center-introduction.md) , Azure kaynaklarınızın güvenliğine yönelik artırılmış görünürlük ve denetim ile tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Aboneliklerinizde, tümleşik güvenlik izleme ve ilke yönetimi sağlar; normal koşullarda gözden kaçabilecek tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş ekosistemiyle çalışır.

Ayrıca, Güvenlik Merkezi size anında başlayabilecekleri uyarıları ve önerileri yüzey halinde sunan tek bir pano sunarak güvenlik işlemlerine yardımcı olur. Genellikle, güvenlik merkezi konsolunun içindeki tek bir tıklama ile ilgili sorunları düzeltebilirsiniz.

### <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager](../../azure-resource-manager/management/overview.md) çözümünüzdeki kaynaklarla bir grup olarak çalışmanıza olanak sağlar. Çözümünüzdeki tüm kaynakları tek ve eşgüdümlü bir işlemle dağıtabilir, güncelleştirebilir veya silebilirsiniz. Dağıtım için bir [Azure Resource Manager şablonu](../../azure-resource-manager/templates/overview.md) kullanırsınız ve bu şablon test, hazırlık ve üretim gibi farklı ortamlarda çalışabilir. Resource Manager kaynaklarınızı dağıttıktan sonra yönetmenize yardımcı olmak için güvenlik, denetleme ve etiketleme özellikleri sunar.

Azure Resource Manager şablon tabanlı dağıtımlar, standart güvenlik denetimi ayarları ve standartlaştırılmış şablon tabanlı dağıtımlarla tümleştirilebilen Azure 'da dağıtılan çözümlerin güvenliğini artırmaya yardımcı olur. Bu, el ile dağıtım sırasında gerçekleşebilecek güvenlik yapılandırma hatalarının riskini azaltır.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) , Web geliştiricileri için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Application Insights, Canlı Web uygulamalarınızı izleyebilir ve performans anormalilerini otomatik olarak algılayabilirsiniz. Sorunları tanılamanıza ve hangi kullanıcıların uygulamalarınızla gerçekten ne yaptığını anlamanıza yardımcı olacak güçlü analiz araçları içerir. Uygulamanızı, her ikisi de test sırasında ve yayımladıktan sonra ve dağıttıktan sonra çalıştığı zaman izler.

Application Insights, size en fazla Kullanıcı alacağınız, uygulamanın ne kadar zaman olduğu ve bağımlı olduğu herhangi bir dış hizmet tarafından ne kadar iyi hizmet verdiğini gösteren grafikler ve tablolar oluşturur.

Kilitlenmeler, sorunlar veya performans sorunları varsa, nedeni tanılamak için telemetri verilerinde ayrıntılı arama yapabilirsiniz. Ve uygulamanızın kullanılabilirliği ve performansı üzerinde herhangi bir değişiklik olursa hizmet size e-posta gönderir. Bu nedenle, gizlilik, bütünlük ve kullanılabilirlik güvenliği Triad 'de kullanılabilirliğine yardımcı olduğundan, uygulama öngörüleri değerli bir güvenlik aracı haline gelir.

### <a name="azure-monitor"></a>Azure İzleyici

[Azure izleyici](/azure/monitoring-and-diagnostics/) , Azure aboneliğinden ([etkinlik günlüğü](../../azure-monitor/essentials/platform-logs-overview.md)) ve her bir Azure kaynağından ([kaynak günlükleri](../../azure-monitor/essentials/platform-logs-overview.md)) her ikisi de görselleştirme, sorgu, yönlendirme, uyarı, otomatik ölçeklendirme ve verileri otomatikleştirme olanağı sunar. Azure Izleyici 'yi kullanarak Azure günlükleri 'nde oluşturulan güvenlikle ilgili olaylar hakkında sizi uyarabilir.

### <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri

[Azure izleyici günlükleri](../../azure-monitor/logs/log-query-overview.md) – Azure kaynaklarına ek olarak hem şirket içi hem de üçüncü taraf bulut tabanlı altyapı (AWS gibi) IÇIN bir BT yönetimi çözümü sağlar. Azure Izleyici 'deki veriler doğrudan Azure Izleyici günlüklerine yönlendirilebilir, böylece tüm ortamınız için ölçümleri ve günlükleri tek bir yerde görebilirsiniz.

Azure izleyici günlükleri, esnek bir sorgu yaklaşımına sahip çok sayıda güvenlikle ilgili girişi hızlı bir şekilde aramanızı sağladığından, adli ve diğer güvenlik analizinde yararlı bir araç olabilir. Ayrıca, şirket içi [güvenlik duvarı ve proxy günlükleri Azure 'a aktarılabilir ve Azure izleyici günlükleri kullanılarak analiz için kullanılabilir hale getirilebilir.](../../azure-monitor/agents/agent-windows.md)

### <a name="azure-advisor"></a>Azure Danışmanı

[Azure Danışmanı](../../advisor/advisor-overview.md) , Azure dağıtımlarınızı iyileştirmenize yardımcı olan kişiselleştirilmiş bir bulut danışmanıdır. Kaynak yapılandırmanızı ve kullanım telemetrinizi çözümler. Daha sonra, [Genel Azure harcamalarınızı azaltmaya](../../advisor/advisor-cost-recommendations.md)yönelik fırsatları ararken kaynaklarınızın [performansını](../../advisor/advisor-performance-recommendations.md), [güvenliğini](../../advisor/advisor-security-recommendations.md)ve [güvenilirliğini](../../advisor/advisor-high-availability-recommendations.md) artırmaya yardımcı olmak için çözümler önerir. Azure Danışmanı, Azure 'da dağıttığınız çözümler için genel güvenlik duruşunuzu önemli ölçüde iyileştirebilen güvenlik önerileri sağlar. Bu öneriler, [Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md) tarafından gerçekleştirilen güvenlik analizinden çizilir.

## <a name="applications"></a>Uygulamalar

Bu bölümde, uygulama güvenliği ve bu özellikler hakkında özet bilgileri ile ilgili ek bilgiler yer almaktadır.

### <a name="web-application-vulnerability-scanning"></a>Web uygulaması güvenlik açığı taraması

[App Service uygulamanızda](../../app-service/overview.md) güvenlik açıklarını test etmeye başlamak için en kolay yollarından biri, uygulamanızda tek tıklamayla bir güvenlik açığı taraması gerçekleştirmek Için [tinfoil Security ile tümleştirmeyi](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) kullanmaktır. Test sonuçlarını kolay anlaşılır bir raporda görüntüleyebilir ve adım adım yönergelerle her bir güvenlik açığını nasıl düzelteceğinizi öğrenebilirsiniz.

### <a name="penetration-testing"></a>Sızma Testi

Uygulamanızı sizin için [sızma testi](./pen-testing.md) gerçekleştirmedik, ancak kendi uygulamalarınızda test yapmanız gerektiğini ve istediğinizi anladık. Bu iyi bir şeydir çünkü uygulamalarınızın güvenliğini geliştirmenizde tüm Azure ekosisteminin daha güvenli hale getirilmesine yardımcı olursunuz. Pen test etkinliklerinin Microsoft 'a bildirimde bulunmak için artık gerekli olan müşteriler, [görevlendirmenin Microsoft bulut sızma testi kurallarına](https://www.microsoft.com/msrc/pentest-rules-of-engagement)uymaya devam etmelidir.

### <a name="web-application-firewall"></a>Web uygulaması güvenlik duvarı

[Azure Application Gateway](../../application-gateway/features.md#web-application-firewall) Web uygulaması güvenlik duvarı (WAF), Web uygulamalarını SQL ekleme, siteler arası komut dosyası saldırıları ve oturum ele geçirme gibi yaygın web tabanlı saldırılara karşı korumanıza yardımcı olur. Bu [uygulama, Open Web Application Security Project (OWASP) tarafından tanımlanan tehditlere karşı, en sık kullanılan 10 ortak güvenlik açığı olarak](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)önceden yapılandırılmış olarak gelir.

### <a name="authentication-and-authorization-in-azure-app-service"></a>Azure App Service’de kimlik doğrulaması ve yetkilendirme

[App Service kimlik doğrulaması/yetkilendirme](../../app-service/overview-authentication-authorization.md) , uygulamanızın kullanıcı oturum açması için bir yol sağlayan bir özelliktir. böylece, uygulama arka ucunda kodu değiştirmek zorunda kalmazsınız. Uygulamanızı korumanın ve Kullanıcı başına verilerle çalışacak kolay bir yol sağlar.

### <a name="layered-security-architecture"></a>Katmanlı güvenlik mimarisi

[App Service ortamları](../../app-service/environment/app-service-app-service-environment-intro.md) bir [Azure sanal ağına](../../virtual-network/virtual-networks-overview.md)dağıtılan yalıtılmış bir çalışma zamanı ortamı sağladığından, geliştiriciler her uygulama katmanı için farklı düzeylerde ağ erişimi sağlayan katmanlı bir güvenlik mimarisi oluşturabilir. API arka uçlarını genel Internet erişimi 'nden gizlemek ve yalnızca API 'Lerin yukarı akış Web uygulamaları tarafından çağrılmasına izin vermek yaygın bir uygulamadır. [Ağ güvenlik grupları (NSG 'ler)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) , API uygulamalarına genel erişimi kısıtlamak Için App Service ortamları Içeren Azure sanal ağ alt ağlarında kullanılabilir.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web sunucusu tanılama ve Uygulama Tanılama
[App Service Web Apps](../../app-service/troubleshoot-diagnostic-logs.md) , hem Web sunucusundan hem de Web uygulamasından günlüğe bilgi kaydetmeye yönelik Tanılama işlevleri sağlar. Bunlar, Web sunucusu tanılama ve Uygulama Tanılama ile mantıksal olarak ayrılır. Web sunucusu, siteleri ve uygulamaları tanılamaya ve sorun gidermeye yönelik iki önemli gelişde sahiptir.

İlk yeni özellik, uygulama havuzları, çalışan süreçler, siteler, uygulama etki alanları ve çalışan istekler hakkında gerçek zamanlı durum bilgileri. İkinci yeni avantajlar, isteği tam istek ve yanıt işlemi boyunca izleyen ayrıntılı izleme olaylardır.

Bu izleme olaylarının toplanmasını etkinleştirmek için, IIS 7, geçen süre veya hata yanıt kodlarına göre belirli bir istek için XML biçiminde tam izleme günlüklerini otomatik olarak yakalanacak şekilde yapılandırılabilir.

## <a name="storage"></a>Depolama
Bu bölümde, Azure Storage Security 'deki temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="azure-role-based-access-control-azure-rbac"></a>Azure rol tabanlı erişim denetimi (Azure RBAC)
[Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)ile depolama hesabınızı güvenli hale getirebilirsiniz. Erişimi, veri erişimi için güvenlik ilkeleri zorlamak isteyen kuruluşlar için [bilmelidir](https://en.wikipedia.org/wiki/Need_to_know) ve [en az ayrıcalık](https://en.wikipedia.org/wiki/Principle_of_least_privilege) güvenlik ilkelerine göre kısıtlamak zorunludur. Bu erişim hakları, belirli bir kapsamdaki gruplara ve uygulamalara uygun Azure rolü atanarak verilir. Kullanıcılara ayrıcalık atamak için depolama hesabı katılımcısı gibi [Azure yerleşik rollerini](../../role-based-access-control/built-in-roles.md)kullanabilirsiniz. [Azure Resource Manager](../../storage/blobs/security-recommendations.md#data-protection) modelini kullanarak bir depolama hesabı için depolama anahtarlarına erişim, Azure RBAC aracılığıyla denetlenebilir.

### <a name="shared-access-signature"></a>Paylaşılan erişim Imzası
[Paylaşılan erişim imzası (SAS)](../../storage/common/storage-sas-overview.md), depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. SAS, belirli bir süre ve belirli bir izin kümesi için Depolama hesabınızdaki nesnelere sınırlı bir istemci izinleri vermeyeceğiniz anlamına gelir. Hesap erişim anahtarlarınızı paylaşmak zorunda kalmadan bu sınırlı izinleri verebilirsiniz.

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme
Aktarım sırasında şifreleme, ağlar arasında iletilirken verilerin korunmasında bir mekanizmadır. Azure depolama ile, aşağıdakileri kullanarak verileri güvenli hale getirebilirsiniz:
- Azure depolama içine veya dışına veri aktarırken HTTPS gibi [Aktarım düzeyi şifreleme](../../storage/blobs/security-recommendations.md).

- [Azure dosya paylaşımları](../../storage/files/storage-dotnet-how-to-use-files.md)için [SMB 3,0 şifrelemesi](../../storage/blobs/security-recommendations.md) gibi bir [hat şifreleme](../../storage/blobs/security-recommendations.md).

- Depolama alanına aktarılmadan önce verileri şifrelemek ve depolama alanı dışına aktarıldıktan sonra verilerin şifresini çözmek için istemci tarafı şifreleme.

### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme
Birçok kuruluş için, bekleyen veri şifreleme, veri gizliliği, uyumluluk ve veri egemenlik 'e yönelik zorunlu bir adımdır. "Bekleyen" veri şifrelemesini sağlayan üç Azure depolama güvenlik özelliği vardır:

- [Depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md) , depolama hizmetinin verileri Azure depolama 'ya yazarken otomatik olarak şifrelemesine olanak tanır.

- [İstemci tarafı şifreleme](../../storage/common/storage-client-side-encryption.md) , bekleyen şifreleme özelliğini de sağlar.

- [Azure disk şifrelemesi](./azure-disk-encryption-vms-vmss.md) , bir IaaS sanal makinesi tarafından kullanılan işletim sistemi disklerini ve veri disklerini şifrelemenizi sağlar.

### <a name="storage-analytics"></a>Depolama Analizi

[Azure depolama Analizi](/rest/api/storageservices/fileservices/storage-analytics) günlüğe kaydetme gerçekleştirir ve depolama hesabı için ölçüm verileri sağlar. Bu verileri kullanarak istekleri izleyebilir, kullanım eğilimlerini çözümleyebilir ve depolama hesabınızla ilgili sorunları tanılayabilirsiniz. Depolama Analizi, bir depolama cihazına gönderilen başarılı ve başarısız isteklerle ilgili ayrıntılı bilgileri günlüğe kaydeder. Bu bilgileri kullanarak istekleri ayrı ayrı izleyebilir ve depolama hizmetiyle ilgili sorunları tanılayabilirsiniz. İstekler en iyi çaba temelinde günlüğe kaydedilir. Aşağıdaki türden kimliği doğrulanmış istekler kaydedilir:

- Başarılı istekler.
- Zaman aşımı, azaltma, ağ, yetkilendirme ve diğer hatalar da dahil olmak üzere başarısız istekler.
- Başarısız ve başarılı istekler dahil, paylaşılan erişim Imzası (SAS) kullanan istekler.
- Analiz verilerine yönelik istekler.

### <a name="enabling-browser-based-clients-using-cors"></a>CORS kullanarak Browser-Based Istemcileri etkinleştirme

[Çıkış noktaları arası kaynak paylaşımı (CORS)](/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) , etki alanlarının diğer her kaynağa erişmek için başka bir izin vermesini sağlayan bir mekanizmadır. Kullanıcı Aracısı, belirli bir etki alanından yüklenen JavaScript kodunun başka bir etki alanında bulunan kaynaklara erişmesine izin verildiğinden emin olmak için ek üstbilgiler gönderir. Daha sonra, son etki alanı, kaynakları için özgün etki alanı erişimine izin veren veya reddeden ek üstbilgiler ile yanıt verir.

Azure Storage Hizmetleri, hizmet için CORS kurallarını ayarladıktan sonra, belirttiğiniz kurallara göre izin verilip verilmeyeceğini belirlemede, farklı bir etki alanından hizmete yönelik doğru şekilde kimliği doğrulanmış bir istek değerlendirilmek üzere CORS 'yi destekliyor.

## <a name="networking"></a>Ağ

Bu bölümde, Azure ağ güvenliği 'ndeki temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="network-layer-controls"></a>Ağ katmanı denetimleri

Ağ erişim denetimi, belirli cihazlarla veya alt ağlardan gelen bağlantıları sınırlama ve ağ güvenliğinin çekirdeğini temsil etme işlemidir. Ağ erişim denetimi amacı, sanal makinelerinize ve hizmetlerinize, yalnızca erişilebilir olmasını istediğiniz kullanıcılar ve cihazlar için erişilebilir olduğundan emin olmaktır.

#### <a name="network-security-groups"></a>Ağ Güvenlik Grupları

[Ağ güvenlik grubu (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md#security) , temel bir durum bilgisi olan paket filtreleme güvenlik duvarıdır ve 5 demet temelinde erişimi denetlemenize olanak sağlar. NSG 'ler uygulama katmanı denetimi veya kimliği doğrulanmış erişim denetimleri sağlamaz. Azure sanal ağı içindeki alt ağlar arasında taşınan trafiği ve bir Azure sanal ağı ile Internet arasındaki trafiği denetlemek için kullanılabilir.

#### <a name="route-control-and-forced-tunneling"></a>Yönlendirme denetimi ve Zorlamalı tünel

Azure sanal ağlarınızdaki yönlendirme davranışını denetleme özelliği, kritik bir ağ güvenliği ve erişim denetimi özelliğidir. Örneğin, Azure sanal ağınıza gelen ve giden tüm trafiğin bu sanal güvenlik gereci üzerinden geldiğinden emin olmak istiyorsanız, yönlendirme davranışını denetleyebilmeniz ve özelleştirebilmeniz gerekir. Bunu Azure 'da User-Defined yollarını yapılandırarak yapabilirsiniz.

[Kullanıcı tanımlı yollar](../../virtual-network/virtual-networks-udr-overview.md#custom-routes) , mümkün olan en güvenli rotayı sağlamak üzere ayrı sanal makinelere veya alt ağlara taşınan ve giden trafik için gelen ve giden yolları özelleştirmenizi sağlar. [Zorlamalı tünel oluşturma](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) , hizmetlerinizin Internet 'teki cihazlara bağlantı başlatmasına izin verilmediğinden emin olmak için kullanabileceğiniz bir mekanizmadır.

Bu, gelen bağlantıları kabul edebilmesinin ve daha sonra onlara yanıt vermemesine farklıdır. Ön uç Web sunucularının Internet konaklarındaki isteklere yanıt vermesi gerekir ve bu Web sunucularına gelen Internet kaynaklı trafiğe izin verilir ve Web sunucuları yanıt verebilir.

Zorlamalı tünel genellikle şirket içi güvenlik proxy 'leri ve güvenlik duvarları aracılığıyla Internet 'e giden trafiği zorlamak için kullanılır.

#### <a name="virtual-network-security-appliances"></a>Sanal ağ güvenlik gereçleri

Ağ güvenlik grupları, User-Defined rotaları ve Zorlamalı tünel, [OSI modelinin](https://en.wikipedia.org/wiki/OSI_model)ağ ve aktarım katmanlarında bir güvenlik düzeyi sağlar, ancak yığının daha yüksek düzeylerinde güvenliği etkinleştirmek istediğiniz zamanlar olabilir. Azure iş ortağı ağ güvenlik gereci çözümünü kullanarak bu gelişmiş ağ güvenliği özelliklerine erişebilirsiniz. [Azure Marketi](https://azure.microsoft.com/marketplace/) ' ni ziyaret ederek ve "güvenlik" ve "ağ güvenliği" araması yaparak en güncel Azure iş ortağı ağ güvenlik çözümlerini bulabilirsiniz.

### <a name="azure-virtual-network"></a>Azure Sanal Ağ

Azure Virtual Network (VNet) buluttaki kendi ağınızın bir gösterimidir. Aboneliğiniz için ayrılmış olan Azure Network Fabric 'in mantıksal bir yalıtımının olması. Bu ağ içindeki IP adres bloklarını, DNS ayarlarını, güvenlik ilkelerini ve yol tablolarını tam olarak denetleyebilirsiniz. Azure sanal ağlarına Azure IaaS sanal makineleri (VM 'Ler) ve/veya [bulut hizmetleri 'ni (PaaS rol örnekleri)](../../cloud-services/cloud-services-choose-me.md) yerleştirebilir ve sanal ağlarınızı alt ağlara segmentleyebilirsiniz.

Bunun yanı sıra, Azure'ın sunduğu [bağlantı seçeneklerinden](../../vpn-gateway/index.yml) birini kullanarak sanal ağı şirket içi ağınıza bağlayabilirsiniz. Özetle, IP adres blokları üzerinde tam bir kontrol sahibi olarak ve Azure'ın sunduğu kurumsal ölçek avantajıyla, ağınızı Azure'a genişletebilirsiniz.

Azure ağ iletişimi, çeşitli güvenli uzaktan erişim senaryolarını destekler. Bunlardan bazıları:

- [Bireysel iş istasyonlarını bir Azure sanal ağına bağlama](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

- [Şirket içi ağı VPN ile bir Azure sanal ağına bağlama](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

- [Şirket içi ağı adanmış WAN bağlantısıyla bir Azure sanal ağına bağlama](../../expressroute/expressroute-introduction.md)

- [Azure sanal ağlarını birbirlerine bağlama](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="azure-private-link"></a>Azure Özel Bağlantı

[Azure özel bağlantısı](https://azure.microsoft.com/services/private-link/) , Azure PaaS hizmetlerine (örneğin, Azure depolama ve SQL veritabanı) ve Azure 'da barındırılan, müşteriye ait/iş ortağı hizmetlerinin [özel bir uç nokta](../../private-link/private-endpoint-overview.md)üzerinden özel olarak bir şekilde erişmenizi sağlar. Azure özel bağlantısını kullanarak kurulum ve tüketim, Azure PaaS, müşteriye ait ve paylaşılan iş ortağı hizmetleri arasında tutarlıdır. Sanal ağınızdan Azure hizmetine giden trafik her zaman Microsoft Azure omurga ağında kalır.

[Özel uç noktalar](../../private-link/private-endpoint-overview.md) , kritik Azure hizmeti kaynaklarınızı yalnızca sanal ağlarınızla korumanıza olanak sağlar. Azure özel uç noktası, Azure özel bağlantısı tarafından desteklenen bir hizmete özel olarak ve güvenli bir şekilde bağlanmak için sanal ağınızdan özel bir IP adresi kullanır ve hizmeti sanal ağınıza etkin bir şekilde geri getiriyor. Azure 'da hizmetleri kullanmak için Sanal ağınızı genel İnternet 'e sunma işlemi artık gerekli değildir. 

Ayrıca, sanal ağınızda kendi özel bağlantı hizmetinizi de oluşturabilirsiniz. [Azure özel bağlantı hizmeti](../../private-link/private-link-service-overview.md) , Azure özel bağlantısı tarafından desteklenen kendi hizmetinize yapılan başvurudur. Azure Standart Load Balancer arkasında çalışan hizmetiniz, özel bağlantı erişimi için etkinleştirilebilir ve böylece hizmetinize ait tüketiciler kendi sanal ağlarından özel olarak erişebilir. Müşterileriniz, sanal ağı içinde özel bir uç nokta oluşturabilir ve bu hizmetle eşlenir. Hizmetinizi genel İnternet 'e açmak artık Azure 'da Hizmetleri işlemek için gerekli değildir. 

### <a name="vpn-gateway"></a>VPN Gateway

Azure sanal ağınız ile şirket içi siteniz arasında ağ trafiği göndermek için Azure sanal ağınız için bir VPN ağ geçidi oluşturmanız gerekir. [VPN ağ geçidi](../../vpn-gateway/vpn-gateway-about-vpngateways.md) , genel bir bağlantı üzerinden şifrelenmiş trafik gönderen bir sanal ağ geçidi türüdür. Azure ağ dokusunda Azure sanal ağları arasında trafik göndermek için VPN ağ geçitlerini de kullanabilirsiniz.

### <a name="express-route"></a>Express Route

Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) , şirket içi ağlarınızı bir bağlantı sağlayıcısı tarafından kolaylaştırarak adanmış özel bir bağlantı üzerinden Microsoft bulutuna genişletmenizi sağlayan ADANMıŞ bir WAN bağlantısıdır.

![Express Route](./media/overview/azure-security-figure-1.png)

ExpressRoute ile Microsoft Azure, Microsoft 365 ve CRM Online gibi Microsoft bulut hizmetleriyle bağlantı kurabilirsiniz. Ortak yerleşim tesisinde bağlantı sağlayıcısı üzerinden herhangi bir ağdan herhangi bir ağa (IP VPN), noktadan noktaya Ethernet ağı veya sanal çapraz bağlantısından bağlantı olabilir. 

ExpressRoute bağlantıları, genel Internet üzerinden geçmez ve bu nedenle VPN tabanlı çözümlerden daha güvenli olarak düşünülebilir. Bu, ExpressRoute bağlantılarına İnternet üzerindeki sıradan bağlantılara göre daha fazla güvenilirlik, yüksek hız, düşük gecikme ve normal bağlantılardan daha yüksek güvenlik sağlar.

### <a name="application-gateway"></a>Application Gateway

Microsoft [Azure Application Gateway](../../application-gateway/overview.md) , uygulamanız için çeşitli 7. katman yük dengeleme özellikleri sunan bir hizmet olarak [uygulama teslım denetleyicisi (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) sağlar.

![Application Gateway](./media/overview/azure-security-figure-2.png)

CPU yoğun TLS sonlandırmasını Application Gateway ("TLS yük boşaltma" veya "TLS köprüleme" olarak da bilinir) boşaltarak Web grubu üretkenliğini en iyi hale getirmenize olanak tanır. Ayrıca, gelen trafiğin hepsini bir kez deneme dağıtımı, tanımlama bilgisi tabanlı oturum benzeşimi, URL yolu tabanlı Yönlendirme ve tek bir Application Gateway arkasında birden fazla Web sitesi barındırma özelliği de dahil olmak üzere diğer 7. katman yönlendirme özelliklerini de sağlar. Azure Application Gateway, bir katman 7 yük dengeleyicidir.

Bulutta veya şirket içinde olmalarından bağımsız olarak, farklı sunucular arasında yük devretme ile HTTP istekleri için performans amaçlı yönlendirme sağlar.

Uygulama, HTTP yük dengelemesi, tanımlama bilgisi tabanlı oturum benzeşimi, [TLS yük boşaltma](../../web-application-firewall/ag/tutorial-restrict-web-traffic-powershell.md), özel sistem durumu araştırmaları, çoklu site desteği ve birçok başka uygulama teslim DENETLEYICISI (ADC) özelliği sunar.

### <a name="web-application-firewall"></a>Web Uygulaması Güvenlik Duvarı

Web uygulaması güvenlik duvarı, standart uygulama teslim denetimi (ADC) işlevleri için Application Gateway kullanan Web uygulamalarına koruma sağlayan bir [Azure Application Gateway](../../application-gateway/overview.md) özelliğidir. Web uygulaması güvenlik duvarı bunu, uygulamaları OWASP tarafından sunulan en yaygın 10 web güvenlik açığının çoğuna karşı koruyarak gerçekleştirir.

![Web Uygulaması Güvenlik Duvarı](./media/overview/azure-security-figure-3.png)

- SQL ekleme koruması

- Komut ekleme, HTTP isteği kaçakçılığı, HTTP yanıtı bölme ve uzak dosya ekleme saldırıcı gibi Yaygın Web Saldırıları Koruması

- HTTP protokolü ihlallerine karşı koruma

- Eksik konak kullanıcısı-aracısı ve kabul üst bilgileri gibi HTTP protokolü anormalliklerine karşı koruma

- Robotlar, gezginler ve tarayıcıları önleme

- Yaygın uygulama yapılandırmalarını algılama (yani, Apache, IIS vb.)

Web saldırılarına karşı korunacak merkezi bir web uygulaması, güvenlik yönetimini çok daha kolay hale getirir ve yetkisiz erişim tehditlerine karşı uygulamayı daha güvende tutar. Bir WAF çözümü, bilinen bir güvenlik açığına merkezi bir konumda düzeltme eki uygulayarak güvenlik tehdidine karşı, web uygulamalarının her birinin güvenliğini sağlamaya göre daha hızlı tepki verebilir. Var olan uygulama ağ geçitleri, web uygulaması güvenlik duvarı bulunan bir uygulama ağ geçidine kolaylıkla dönüştürülebilir.

### <a name="traffic-manager"></a>Traffic Manager

Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) , farklı veri merkezlerindeki hizmet uç noktaları için Kullanıcı trafiğinin dağıtımını denetlemenize olanak tanır. Traffic Manager tarafından desteklenen hizmet uç noktaları, Azure VM 'Leri, Web Apps ve bulut hizmetlerini içerir. Traffic Manager’ı Azure olmayan dış uç noktalarla da kullanabilirsiniz. Traffic Manager, istemci isteklerini bir [trafik yönlendirme yöntemine](../../traffic-manager/traffic-manager-routing-methods.md) ve uç noktaların sistem durumuna göre en uygun uç noktaya yönlendirmek Için etki alanı adı sistemi 'NI (DNS) kullanır.

Traffic Manager, farklı uygulama ihtiyaçlarına, uç nokta durumuna [izlemeye](../../traffic-manager/traffic-manager-monitoring.md)ve otomatik yük devretmeye uyacak bir dizi trafik yönlendirme yöntemi sağlar. Traffic Manager, bir Azure bölgesinin tamamının devre dışı kalması dahil olmak üzere hatalara dayanıklıdır.

### <a name="azure-load-balancer"></a>Azure Load Balancer

[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) uygulamalarınıza yüksek düzeyde kullanılabilirlik ve ağ performansı sunar. Bu, gelen trafiği yük dengeli bir küme içinde tanımlanan sağlıklı hizmet örnekleri arasında dağıtan bir katman 4 (TCP, UDP) yük dengeleyicidir. Azure Load Balancer, şu şekilde yapılandırılabilir:

- Sanal makinelere gelen Internet trafiğinin yükünü dengeleyin. Bu yapılandırma, [genel yük dengeleme](../../load-balancer/components.md#frontend-ip-configurations)olarak bilinir.

- Bir sanal ağ içindeki sanal makineler arasında, bulut hizmetlerindeki sanal makineler arasında veya şirket içi bilgisayarlar ile şirketler arası bir sanal ağdaki sanal makineler arasında yük dengeleme trafiği. Bu yapılandırma, [İç Yük Dengeleme](../../load-balancer/components.md#frontend-ip-configurations)olarak bilinir.

- Dış trafiği belirli bir sanal makineye ilet

### <a name="internal-dns"></a>İç DNS

Yönetim Portalı veya ağ yapılandırma dosyasında bir VNet 'te kullanılan DNS sunucularının listesini yönetebilirsiniz. Müşteri, her VNet için en fazla 12 DNS sunucusu ekleyebilir. DNS sunucularını belirtirken, müşterinin DNS sunucularını müşterinin ortamı için doğru sırada listediğinizi doğrulamanız önemlidir. DNS sunucusu listeleri hepsini bir kez deneme çalışmaz. Bunlar belirtildikleri sırayla kullanılırlar. Listedeki ilk DNS sunucusuna ulaşılırsa istemci, DNS sunucusunun düzgün çalışıp çalışmadığını ne olursa olsun bu DNS sunucusunu kullanır. Müşterinin sanal ağının DNS sunucusu sırasını değiştirmek için, DNS sunucularını listeden kaldırın ve müşterinin istediği sıraya göre yeniden ekleyin. DNS, "CIA" güvenlik Triad 'nin kullanılabilirlik oranını destekler.

### <a name="azure-dns"></a>Azure DNS

Etki alanı adı sistemi veya DNS, IP adresine bir Web sitesi veya hizmet adı çevirmekten (veya çözümlemeden) sorumludur. [Azure DNS](../../dns/dns-overview.md) , Microsoft Azure altyapısı kullanılarak ad ÇÖZÜMLEMESI sağlayan DNS etki alanları için bir barındırma hizmetidir. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz. DNS, "CIA" güvenlik Triad 'nin kullanılabilirlik oranını destekler.

### <a name="azure-monitor-logs-nsgs"></a>Azure Izleyici günlükleri NSG 'leri

NSG 'ler için aşağıdaki tanılama günlüğü kategorilerini etkinleştirebilirsiniz:

- Olay: VM 'lere ve MAC adresine göre örnek rollere uygulanan NSG kurallarının girdilerini Içerir. Bu kuralların durumu her 60 saniyede toplanır.

- Kurallar sayacı: her NSG kuralının trafiği reddetme veya izin verme için kaç kez uygulanacağını gösteren girişleri Içerir.

### <a name="security-center"></a>Güvenlik Merkezi

[Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md) , ağ güvenliği en iyi uygulamaları için Azure kaynaklarınızın güvenlik durumunu sürekli olarak analiz eder. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde, kaynaklarınızı korumak ve korumak için gerekli denetimleri yapılandırma sürecinde size kılavuzluk eden [öneriler](../../security-center/security-center-recommendations.md) oluşturur.

## <a name="compute"></a>İşlem
Bu bölümde, bu alandaki temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="antimalware--antivirus"></a>Kötü amaçlı yazılımdan koruma &
Azure IaaS ile, bir kötü amaçlı yazılımdan koruma yazılımını Microsoft, Symantec, Trend Micro, McAfee ve Kaspersky gibi güvenlik satıcılarından kullanarak sanal makinelerinizi kötü amaçlı dosyalardan, reklam yazılımlarından ve diğer tehditlere karşı koruyabilirsiniz. Azure Cloud Services için [Microsoft kötü amaçlı yazılımdan](antimalware.md) koruma ve sanal makineler, virüsler, casus yazılım ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olan bir koruma özelliğidir. Microsoft Antimalware, bilinen kötü amaçlı veya istenmeyen yazılımlar kendisini yüklemeye veya Azure sistemlerinizde çalışmaya çalıştığında yapılandırılabilir uyarılar sağlar. Microsoft kötü amaçlı yazılımdan koruma, Azure Güvenlik Merkezi kullanılarak da dağıtılabilir

### <a name="hardware-security-module"></a>Donanım güvenlik modülü
Anahtarlar korunmadığı takdirde şifreleme ve kimlik doğrulama güvenliği geliştirir. Kritik gizli dizilerlerinizin ve anahtarların yönetim ve güvenliğini [Azure Key Vault](../../key-vault/general/overview.md)içinde depolayarak kolaylaştırabilirsiniz. Key Vault, anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) depolama seçeneği sunar. Yedekleme veya [Saydam veri şifrelemesi](/sql/relational-databases/security/encryption/transparent-data-encryption) için SQL Server şifreleme anahtarlarınızın tümü, uygulamalarınızda herhangi bir anahtar veya gizli dizi ile Key Vault depolanabilir. Bu korumalı öğelere izinler ve erişim [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)üzerinden yönetilir.

### <a name="virtual-machine-backup"></a>Sanal makine yedeklemesi
[Azure Backup](../../backup/backup-overview.md) , uygulama verilerinizi sıfır sermaye yatırımı ve en az işletim maliyetiyle koruyan bir çözümdür. Uygulama hataları verilerinizi bozabilir ve insan hataları, uygulamalarınıza güvenlik sorunlarına neden olan hataları ortaya çıkarabilir. Azure Backup, Windows ve Linux çalıştıran sanal makineleriniz korunur.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Kuruluşunuzun [iş sürekliliği/olağanüstü durum kurtarma (BCDR)](../../best-practices-availability-paired-regions.md) stratejisinin önemli bir bölümü, planlı ve plansız kesintiler gerçekleştiğinde kurumsal iş yüklerini ve uygulamaları nasıl çalışır durumda tutabileceğini öğrenmelidir. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) , birincil konumunuz daha sonra ikincil bir konumdan kullanılabilmeleri için iş yükleri ve uygulamaların çoğaltılmasını, yük devretmesini ve kurtarılmasına yardımcı olur.

### <a name="sql-vm-tde"></a>SQL VM TDE
Saydam veri şifrelemesi (TDE) ve sütun düzeyinde şifreleme (CLE), SQL Server şifreleme özellikleridir. Bu şifreleme biçimi müşterilerin şifreleme için kullandığınız şifreleme anahtarlarını yönetmesi ve depolaması gerekir.

Azure Key Vault (AKV) hizmeti, güvenli ve yüksek oranda kullanılabilir bir konumda bu anahtarların güvenliğini ve yönetimini geliştirmek için tasarlanmıştır. SQL Server Bağlayıcısı, SQL Server Azure Key Vault bu anahtarları kullanmasına olanak sağlar.

Şirket içi makinelerle SQL Server çalıştırıyorsanız, şirket içi SQL Server örneğinden Azure Key Vault erişmek için izleyebileceğiniz adımlar vardır. Ancak Azure VM 'lerinde SQL Server için Azure Key Vault tümleştirme özelliğini kullanarak zamandan tasarruf edebilirsiniz. Bu özelliği etkinleştirmek için birkaç Azure PowerShell cmdlet 'i sayesinde, anahtar kasanıza erişmek için bir SQL sanal makinesi için gereken yapılandırmayı otomatikleştirin.

### <a name="vm-disk-encryption"></a>VM disk şifrelemesi
[Azure disk şifrelemesi](./azure-disk-encryption-vms-vmss.md) , Windows ve Linux IaaS sanal makine disklerinizi şifrelemenize yardımcı olan yeni bir özelliktir. İşletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Windows 'un sektör standardı BitLocker özelliğini ve Linux 'un DM-Crypt özelliğini uygular. Çözüm, Key Vault aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için Azure Key Vault ile tümleşiktir. Çözüm Ayrıca, sanal makine disklerindeki tüm verilerin Azure depolamadaki geri kalanta şifrelenmesini de sağlar.

### <a name="virtual-networking"></a>Sanal ağ
Sanal makinelerin ağ bağlantısı olması gerekir. Azure, bu gereksinimi desteklemek için sanal makinelerin bir Azure sanal ağına bağlanmasını gerektirir. Azure sanal ağı, fiziksel Azure ağ dokusunun üzerine oluşturulan mantıksal bir yapıdır. Her mantıksal [Azure sanal ağı](../../virtual-network/virtual-networks-overview.md) , diğer tüm Azure sanal ağlarından yalıtılmıştır. Bu yalıtım, dağıtımlarınızdaki ağ trafiğine diğer Microsoft Azure müşterilerin erişimine açık olmadığından emin olmanıza yardımcı olur.

### <a name="patch-updates"></a>Düzeltme Eki güncelleştirmeleri
Düzeltme Eki güncelleştirmeleri olası sorunları bulma ve düzeltmeye ve yazılım güncelleştirme yönetimi sürecini basitleştirerek, hem kuruluşunuzda dağıtmanız gereken yazılım güncelleştirme sayısını azaltarak hem de uyumluluğu izleme yeteneğinizi artırarak bir temel sağlar.

### <a name="security-policy-management-and-reporting"></a>Güvenlik İlkesi Yönetimi ve raporlama
[Güvenlik Merkezi](../../security-center/security-center-introduction.md) tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur ve Azure kaynaklarınızın güvenliğini ve denetimini artırabilir. Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi
Sistemlerin, uygulamaların ve verilerin güvenliğini sağlamak, kimlik tabanlı erişim denetimleriyle başlar. Microsoft iş ürünleri ve Hizmetleri 'nde yerleşik olarak bulunan kimlik ve erişim yönetimi özellikleri, kurumsal ve kişisel bilgilerinizin yetkisiz erişimden korunmasına yardımcı olmakla kalmaz, her zaman ve her yerde meşru kullanıcılar tarafından kullanılabilir hale gelir.

### <a name="secure-identity"></a>Güvenli kimlik
Microsoft, ürün ve hizmetlerinde kimlik ve erişim yönetimi için birden çok güvenlik uygulaması ve teknolojisini kullanır.

- [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) , kullanıcıların şirket içinde ve bulutta erişim için birden çok yöntem kullanmasını gerektirir. Kolay bir kimlik doğrulama seçenekleriyle, kullanıcılara basit bir oturum açma işlemi ile konairken güçlü kimlik doğrulaması sağlar.

- [Microsoft Authenticator](https://aka.ms/authenticator) , hem Microsoft Azure Active Directory hem de Microsoft hesaplarıyla birlikte çalışarak kullanıcı dostu Multi-Factor Authentication deneyimi sağlar ve wearables ve parmak izi tabanlı onaylar için destek içerir.

- [Parola ilkesi zorlaması](../../active-directory/authentication/concept-sspr-policy.md) , hatalı kimlik doğrulama denemesinden sonra uzunluğu ve karmaşıklık gereksinimlerini, zorla düzenli döndürmeyi ve hesap kilitlemeyi düzenleyerek geleneksel parolaların güvenliğini artırır.

- [Belirteç tabanlı kimlik doğrulaması](../../active-directory/develop/authentication-vs-authorization.md) , Azure Active Directory aracılığıyla kimlik doğrulaması yapılmasını mümkün.

- [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/built-in-roles.md) , kullanıcının atanan rolüne göre erişim sağlamanıza olanak sağlayarak kullanıcılara yalnızca iş görevlerini gerçekleştirmek için ihtiyaç duydukları erişim miktarını vermeyi kolaylaştırır. Azure RBAC 'yi kuruluşunuzun iş modeli ve risk toleransı başına özelleştirebilirsiniz.

- [Tümleşik kimlik yönetimi (Hibrit kimliği)](../../active-directory/hybrid/plan-hybrid-identity-design-considerations-overview.md) , kullanıcıların iç veri merkezleri ve bulut platformları genelinde erişiminin denetimini korumanıza olanak sağlar. bu sayede, tüm kaynaklara yönelik kimlik doğrulama ve yetkilendirme için tek bir Kullanıcı Kimliği oluşturursunuz.

### <a name="secure-apps-and-data"></a>Uygulamaları ve verileri güvenli hale getirme
Kapsamlı bir kimlik ve erişim yönetimi bulut çözümü olan [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), sitedeki ve buluttaki uygulamalardaki verilere erişimi güvenli hale getirmeye yardımcı olur ve kullanıcıların ve grupların yönetimini basitleştirir. Temel Dizin Hizmetleri, Gelişmiş kimlik yönetimi, güvenlik ve uygulama erişimi yönetimini birleştirir ve geliştiricilerin uygulamalarına ilke tabanlı kimlik yönetimi oluşturmasını kolaylaştırır. Azure Active Directory'nizi geliştirmek için Azure Active Directory Temel, Premium P1 ve Premium P2 sürümleriyle ücretli özellikler ekleyebilirsiniz.

| Ücretsiz/ortak özellikler     | Temel Özellikler    |Premium P1 özellikleri |Premium P2 özellikleri | Azure Active Directory JOIN – yalnızca Windows 10 ile ilgili özellikler|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Dizin nesneleri](../../active-directory/fundamentals/active-directory-whatis.md),    [Kullanıcı/Grup Yönetimi (ekleme/güncelleştirme/silme)/Kullanıcı tabanlı sağlama, cihaz kaydı](../../active-directory/fundamentals/active-directory-whatis.md),  [tek Sign-On (SSO)](../../active-directory/fundamentals/active-directory-whatis.md),     [bulut kullanıcıları için self servis parola değişikliği](../../active-directory/fundamentals/active-directory-whatis.md),     [bağlantı (Şirket içi dizinleri Azure Active Directory Ile genişleten eşitleme altyapısı)](../../active-directory/fundamentals/active-directory-whatis.md),     [güvenlik/kullanım raporları](../../active-directory/fundamentals/active-directory-whatis.md)       |  [Grup tabanlı erişim yönetimi/sağlama](../../active-directory/fundamentals/active-directory-whatis.md), [bulut kullanıcıları Için self servis parola sıfırlama](../../active-directory/fundamentals/active-directory-whatis.md),  [Şirket markası (oturum açma sayfaları/erişim paneli özelleştirmesi)](../../active-directory/fundamentals/active-directory-whatis.md),    [uygulama proxy 'si](../../active-directory/fundamentals/active-directory-whatis.md),    [SLA 99,9%](../../active-directory/fundamentals/active-directory-whatis.md) |  [Self servis grup ve uygulama yönetimi/self servis uygulama eklemeleri/Dinamik grupları](../../active-directory/fundamentals/active-directory-whatis.md), Şirket   [içi geri yazma](../../active-directory/fundamentals/active-directory-whatis.md),    [Multi-Factor Authentication (bulut ve şirket içi (MFA sunucusu))](../../active-directory/fundamentals/active-directory-whatis.md), [mım Cal + MIM sunucusu](../../active-directory/fundamentals/active-directory-whatis.md),     [Cloud App Discovery](../../active-directory/fundamentals/active-directory-whatis.md),  [Connect Health](../../active-directory/fundamentals/active-directory-whatis.md),   [Grup hesapları için otomatik parola geçişi](../../active-directory/fundamentals/active-directory-whatis.md)|   [Kimlik koruması](../../active-directory/identity-protection/overview-identity-protection.md),  [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Bir cihazı Azure AD, Masaüstü SSO, Azure AD için Microsoft Passport, yönetici BitLocker kurtarma](../../active-directory/fundamentals/active-directory-whatis.md), [MDM otomatik kayıt, Self-Service BitLocker kurtarma, Azure AD JOIN aracılığıyla Windows 10 cihazlarına ek yerel Yöneticiler](../../active-directory/fundamentals/active-directory-whatis.md) için birleştirin|

- [Cloud App Discovery](/cloud-app-security/set-up-cloud-discovery) , kuruluşunuzdaki çalışanlar tarafından kullanılan bulut uygulamalarını tanımlamanızı sağlayan Azure Active Directory Premium bir özelliğidir.

- [Azure Active Directory kimlik koruması](../../active-directory/identity-protection/overview-identity-protection.md) , risk algılamalarını ve kuruluşunuzun kimliklerini etkileyebilecek olası güvenlik açıklarını içeren birleştirilmiş bir görünüm sağlamak için Azure Active Directory anomali algılama yeteneklerini kullanan bir güvenlik hizmetidir.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) , etki alanı denetleyicilerini dağıtmanıza gerek kalmadan Azure VM 'lerine bir etki alanına katmanızı sağlar. Kullanıcılar bu VM 'Lerde kurumsal Active Directory kimlik bilgilerini kullanarak oturum açabilir ve kaynaklara sorunsuz bir şekilde erişebilir.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , tüketiciye yönelik uygulamalar için yüzlerce milyonlarca kimliğe ölçeklenebilen ve mobil ve web platformlarında tümleştirilebilen, yüksek oranda kullanılabilir küresel bir kimlik yönetimi hizmetidir. Müşterileriniz, var olan sosyal medya hesaplarını kullanan özelleştirilebilen deneyimler aracılığıyla tüm uygulamalarınızda oturum açabilir veya yeni tek başına kimlik bilgileri oluşturabilirsiniz.

- [Azure ACTIVE DIRECTORY B2B işbirliği](../../active-directory/external-identities/what-is-b2b.md) , iş ortaklarının şirket uygulamalarınıza ve verilerinize kendi kendine yönetilen kimliklerini kullanarak erişmesini sağlayarak şirketler arası ilişkilerinizi destekleyen güvenli bir iş ortağı tümleştirme çözümüdür.

- [Azure Active Directory katılmış](../../active-directory/devices/overview.md) , merkezi yönetim için bulut yeteneklerini Windows 10 cihazlarına genişletmenizi sağlar. Kullanıcıların kurumsal veya kurumsal buluta Azure Active Directory aracılığıyla bağlanmasını sağlar ve uygulama ve kaynaklara erişimi basitleştirir.

- [Azure Active Directory uygulama ara sunucusu](../../active-directory/manage-apps/application-proxy.md) , şirket içinde barındırılan Web UYGULAMALARı için SSO ve güvenli uzaktan erişim sağlar.

## <a name="next-steps"></a>Sonraki Adımlar

- [Bulutta paylaşılan sorumluluğu](shared-responsibility.md)anlayın.

- Azure [Güvenlik Merkezi](../../security-center/security-center-introduction.md) 'Nin, Azure kaynaklarınızın güvenliğine yönelik artırılmış görünürlük ve denetim ile tehditleri önlemenize, algılamanıza ve bu tehditlere yanıt vermenize nasıl yardımcı olabileceğini öğrenin.