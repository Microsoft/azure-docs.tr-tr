---
title: Azure güvenliğine giriş | Microsoft Docs
description: Azure güvenliği, hizmetleri ve nasıl çalıştığı hakkında bilgi edinin.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: ed345fdf564c62e2d323b33013da784344c7a461
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595380"
---
# <a name="introduction-to-azure-security"></a>Azure güvenliğe giriş
## <a name="overview"></a>Genel Bakış
Güvenliğin bulutta bir iş olduğunu ve Azure güvenliğiyle ilgili doğru ve güncel bilgileri nerede bulabileceğinizi biliyoruz. Uygulamalarınız ve hizmetleriniz için Azure kullanmanın en iyi nedenlerinden biri, kendi sunduğu güvenlik araçları ve özellikleri dizisinden faydalanabilir. Bu araçlar ve yetenekler, güvenli Azure platformunda güvenli çözümler oluşturmayı olanaklı hale getirmeye yardımcı olur. Microsoft Azure, müşteri verilerinin gizliliğini, bütünlüğünü ve kullanılabilirliğini sağlar. Ayrıca, saydam sorumluluklığa de olanak tanır.

Bu makalede, Azure ile kullanılabilen güvenliğe kapsamlı bir bakış sunulmaktadır.

### <a name="azure-platform"></a>Azure platformu
Azure, geniş bir işletim sistemi, programlama dili, çerçeve, araç, veritabanı ve cihaz seçimini destekleyen genel bir bulut hizmeti platformudur. Docker tümleştirmesiyle Linux kapsayıcıları çalıştırabilir; JavaScript, Python, .NET, PHP, Java ve Node. js ile uygulamalar oluşturun; iOS, Android ve Windows cihazlar için arka uçlar oluşturun.

Azure genel bulut Hizmetleri, milyonlarca geliştirici ve BT uzmanlarının zaten kullandığı ve güvendiği teknolojilerin aynısını destekler. Üzerinde derleme yaptığınızda veya BT varlıklarını ' ye geçirdiğinizde, bu kuruluşun, uygulama ve verilerinizi hizmetlerle ve verilerinizi, bulut tabanlı varlıklarınızın güvenliğini yönetmek için sağladığı denetimlerle koruma yeteneklerine bağlı olursunuz.

Azure 'un altyapısı, tesislerden milyonlarca müşteriyi aynı anda barındırmak için uygulamalara göre tasarlanmıştır ve işletmelerin güvenlik gereksinimlerini karşılayabilecekleri güvenilir bir temel sağlar.

Ayrıca, Azure geniş kapsamlı bir güvenlik seçenekleri dizisi ve bunları, kuruluşunuzun dağıtımlarınızın benzersiz gereksinimlerini karşılayacak şekilde güvenliği özelleştirebilmeniz için denetlemenize olanak tanır. Bu belge, Azure Güvenlik yeteneklerinin bu gereksinimleri karşılamanıza nasıl yardımcı olduğunu anlamanıza yardımcı olur.

> [!Note]
> Bu belgenin birincil odağı, uygulama ve hizmetlerinize ilişkin güvenliği özelleştirmek ve artırmak için kullanabileceğiniz müşteriye yönelik denetimleridir.
>
> Microsoft 'un Azure platformunun kendisini nasıl güvenlik altına aldığı hakkında daha fazla bilgi için bkz. [Azure altyapı güvenliği](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Azure Güvenlik Özellikleri Özeti

### <a name="features-to-secure-the-azure-platform"></a>Azure platformunun güvenliğini sağlamaya yönelik özellikler
Aşağıdaki özellikler, Azure platformunun güvenli bir şekilde yönetilme güvencesi sağlamak için gözden geçirebilmeniz gereken yeteneklerdir. Microsoft 'un müşteri güveni sorularını dört alanda nasıl ele aldığı hakkında daha fazla ayrıntıya yönelik bağlantılar sunulmaktadır: güvenli Platform, gizlilik & denetimleri, uyumluluk ve saydamlık.

| [Güvenli Platform](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Gizlilik & denetimleri](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Uyumluluk](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Saydamlık](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Güvenlik geliştirme çevrimi](https://www.microsoft.com/sdl/), iç denetimler | [Verilerinizi her zaman yönetin](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Güven Merkezi](https://www.microsoft.com/trustcenter/default.aspx) |[Microsoft, Azure hizmetlerindeki müşteri verilerini nasıl korur?](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Zorunlu güvenlik eğitimi, arka plan denetimleri](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Veri konumunda denetim](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Ortak denetimler Merkezi](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Azure hizmetlerinde Microsoft 'un veri konumunu yönetme](https://azuredatacentermap.azurewebsites.net/)|
| [Sızma testi](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [yetkisiz giriş algılama, DDoS](https://www.microsoft.com/trustcenter/Security/ThreatManagement), [denetimleri günlüğe kaydetme &](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Koşullarınızda veri erişimi sağlayın](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Cloud Services nedeni Dildenetim listesi](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Microsoft 'un kim tarafından verilerinize hangi koşullarda erişebileceği](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Sanat veri merkezi](https://www.microsoft.com/cloud-platform/global-datacenters), fiziksel güvenlik, [güvenli ağ](network-overview.md) durumu | [Yasalar zorlamasına yanıt verme](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Hizmete göre uyumluluk, konum & sektör](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Microsoft, Azure hizmetlerindeki müşteri verilerini nasıl korur?](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Güvenlik olay yanıtı](https://aka.ms/SecurityResponsepaper), [paylaşılan sorumluluk](https://aka.ms/sharedresponsibility) |[Sıkı gizlilik standartları](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Azure Hizmetleri, saydamlık Merkezi için sertifikayı gözden geçirme](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Verilerin ve uygulamanın güvenliğini sağlamaya yönelik özellikler
Bulut hizmeti modeline bağlı olarak, uygulamanın veya hizmetin güvenliğini yönetmekten sorumlu olan değişken sorumluluğu vardır. Azure platformunda sunulan ve yerleşik özellikler aracılığıyla bu sorumlulukları ve bir Azure aboneliğine dağıtılabilecek iş ortağı çözümlerini kullanarak bu sorumlulukları karşılamakta yardımcı olacak özellikler vardır.

Yerleşik yetenekler altı işlevsel alanda düzenlenmiştir: Işlemler, uygulamalar, depolama, ağ, Bilgi Işlem ve kimlik. Bu altı alanda Azure platformunda bulunan özellikler ve yetenekler hakkında ek ayrıntı Özet bilgiler aracılığıyla sağlanır.

## <a name="operations"></a>Operations
Bu bölümde güvenlik işlemlerinde temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="security-and-audit-dashboard"></a>Güvenlik ve Denetim Panosu
[Güvenlik ve denetim çözümü](../../security-center/security-center-intro.md) , dikkat etmeniz gereken önemli sorunlar için [yerleşik arama sorgularıyla](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) kuruluşunuzun BT güvenlik duruşuna kapsamlı bir görünüm sağlar. [Güvenlik ve denetim](https://technet.microsoft.com/library/mt484091.aspx) panosu, Azure izleyici günlüklerinde güvenlikle ilgili her şeyin giriş ekranıdır. Bilgisayarlarınızın güvenlik durumu hakkında üst düzey öngörüler sağlar. Ayrıca son 24 saat, 7 gün veya herhangi bir özel zaman dilimine ait tüm olayları görüntüleme becerisine sahiptir.

Ayrıca, belirli bir olay algılandığında [otomatik olarak belirli eylemleri yürütmek](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) için güvenlik ve uyumluluk yapılandırabilirsiniz.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../../azure-resource-manager/resource-manager-deployment-model.md) çözümünüzdeki kaynaklarla bir grup olarak çalışmanıza olanak sağlar. Çözümünüzdeki tüm kaynakları tek ve eşgüdümlü bir işlemle dağıtabilir, güncelleştirebilir veya silebilirsiniz. Dağıtım için bir [Azure Resource Manager şablonu](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) kullanırsınız ve bu şablon test, hazırlık ve üretim gibi farklı ortamlarda çalışabilir. Resource Manager kaynaklarınızı dağıttıktan sonra yönetmenize yardımcı olmak için güvenlik, denetleme ve etiketleme özellikleri sunar.

Azure Resource Manager şablon tabanlı dağıtımlar, standart güvenlik denetimi ayarları ve standartlaştırılmış şablon tabanlı dağıtımlarla tümleştirilebilen Azure 'da dağıtılan çözümlerin güvenliğini artırmaya yardımcı olur. Bu, el ile dağıtım sırasında gerçekleşebilecek güvenlik yapılandırma hatalarının riskini azaltır.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) , Web geliştiricileri için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Application Insights, Canlı Web uygulamalarınızı izleyebilir ve performans anormalilerini otomatik olarak algılayabilirsiniz. Sorunları tanılamanıza ve hangi kullanıcıların uygulamalarınızla gerçekten ne yaptığını anlamanıza yardımcı olacak güçlü analiz araçları içerir. Uygulamanızı, her ikisi de test sırasında ve yayımladıktan sonra ve dağıttıktan sonra çalıştığı zaman izler.

Application Insights, size en fazla Kullanıcı alacağınız, uygulamanın ne kadar zaman olduğu ve bağımlı olduğu herhangi bir dış hizmet tarafından ne kadar iyi hizmet verdiğini gösteren grafikler ve tablolar oluşturur.

Kilitlenmeler, sorunlar veya performans sorunları varsa, nedeni tanılamak için telemetri verilerinde ayrıntılı arama yapabilirsiniz. Ve uygulamanızın kullanılabilirliği ve performansı üzerinde herhangi bir değişiklik olursa hizmet size e-posta gönderir. Bu nedenle, gizlilik, bütünlük ve kullanılabilirlik güvenliği Triad 'de kullanılabilirliğine yardımcı olduğundan, uygulama öngörüleri değerli bir güvenlik aracı haline gelir.

### <a name="azure-monitor"></a>Azure İzleyici
[Azure izleyici](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) , Azure altyapısından ([etkinlik günlüğünden](../../azure-monitor/platform/activity-logs-overview.md)) ve her bir Azure kaynağından ([tanılama günlükleri](../../azure-monitor/platform/resource-logs-overview.md)) her ikisi de görselleştirme, sorgu, yönlendirme, uyarı, otomatik ölçeklendirme ve verileri otomatikleştirme olanağı sunar. Azure Izleyici 'yi kullanarak Azure günlükleri 'nde oluşturulan güvenlikle ilgili olaylar hakkında sizi uyarabilir.

### <a name="azure-monitor-logs"></a>Azure İzleyici günlükleri
[Azure izleyici günlükleri](https://azure.microsoft.com/documentation/services/log-analytics/) – Azure kaynaklarına ek olarak hem şirket içi hem de üçüncü taraf bulut tabanlı altyapı (AWS gibi) IÇIN bir BT yönetimi çözümü sağlar. Azure Izleyici 'deki veriler doğrudan Azure Izleyici günlüklerine yönlendirilebilir, böylece tüm ortamınız için ölçümleri ve günlükleri tek bir yerde görebilirsiniz.

Azure izleyici günlükleri, esnek bir sorgu yaklaşımına sahip çok sayıda güvenlikle ilgili girişi hızlı bir şekilde aramanızı sağladığından, adli ve diğer güvenlik analizinde yararlı bir araç olabilir. Ayrıca, şirket içi [güvenlik duvarı ve proxy günlükleri Azure 'a aktarılabilir ve Azure izleyici günlükleri kullanılarak analiz için kullanılabilir hale getirilebilir.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Danışmanı](../../advisor/index.yml) , Azure dağıtımlarınızı iyileştirmenize yardımcı olan kişiselleştirilmiş bir bulut danışmanıdır. Kaynak yapılandırmanızı ve kullanım telemetrinizi çözümler. Daha sonra, [Genel Azure harcamalarınızı azaltmaya](../../advisor/advisor-cost-recommendations.md)yönelik fırsatları ararken kaynaklarınızın [performansını](../../advisor/advisor-performance-recommendations.md), [güvenliğini](../../advisor/advisor-security-recommendations.md)ve [yüksek oranda kullanılabilirliğini](../../advisor/advisor-high-availability-recommendations.md) artırmaya yardımcı olmak için çözümler önerir. Azure Danışmanı, Azure 'da dağıttığınız çözümler için genel güvenlik duruşunuzu önemli ölçüde iyileştirebilen güvenlik önerileri sağlar. Bu öneriler, [Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) tarafından gerçekleştirilen güvenlik analizinden çizilir.

### <a name="azure-security-center"></a>Azure Güvenlik Merkezi
[Güvenlik Merkezi](../../security-center/security-center-intro.md) , Azure kaynaklarınızın güvenliğine yönelik artırılmış görünürlük ve denetim ile tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Aboneliklerinizde, tümleşik güvenlik izleme ve ilke yönetimi sağlar; normal koşullarda gözden kaçabilecek tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş ekosistemiyle çalışır.

Ayrıca, Güvenlik Merkezi size anında başlayabilecekleri uyarıları ve önerileri yüzey halinde sunan tek bir pano sunarak güvenlik işlemlerine yardımcı olur. Genellikle, güvenlik merkezi konsolunun içindeki tek bir tıklama ile ilgili sorunları düzeltebilirsiniz.
## <a name="applications"></a>Uygulamalar
Bu bölümde, uygulama güvenliği ve bu özellikler hakkında özet bilgileri ile ilgili ek bilgiler yer almaktadır.

### <a name="web-application-vulnerability-scanning"></a>Web uygulaması güvenlik açığı taraması
[App Service uygulamanızda](../../app-service/overview.md) güvenlik açıklarını test etmeye başlamak için en kolay yollarından biri, uygulamanızda tek tıklamayla bir güvenlik açığı taraması gerçekleştirmek Için [tinfoil Security ile tümleştirmeyi](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) kullanmaktır. Test sonuçlarını kolay anlaşılır bir raporda görüntüleyebilir ve adım adım yönergelerle her bir güvenlik açığını nasıl düzelteceğinizi öğrenebilirsiniz.

### <a name="penetration-testing"></a>Sızma Testi
Kendi sızma testlerinizi gerçekleştirmeyi tercih ediyorsanız veya başka bir tarayıcı paketi veya sağlayıcı kullanmak istiyorsanız, [Azure sızma testi onay işlemini](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) izlemeniz ve istenen Sızma testlerini gerçekleştirmek için önceki onayı edinmeniz gerekir.

### <a name="web-application-firewall"></a>Web uygulaması güvenlik duvarı
[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) Web uygulaması güvenlik duvarı (WAF), Web uygulamalarını SQL ekleme, siteler arası komut dosyası saldırıları ve oturum ele geçirme gibi yaygın web tabanlı saldırılara karşı korumanıza yardımcı olur. Bu [uygulama, Open Web Application Security Project (OWASP) tarafından tanımlanan tehditlere karşı, en sık kullanılan 10 ortak güvenlik açığı olarak](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)önceden yapılandırılmış olarak gelir.

### <a name="authentication-and-authorization-in-azure-app-service"></a>Azure Uygulama Hizmeti’nde kimlik doğrulaması ve yetkilendirme
[App Service kimlik doğrulaması/yetkilendirme](../../app-service/overview-authentication-authorization.md) , uygulamanızın kullanıcı oturum açması için bir yol sağlayan bir özelliktir. böylece, uygulama arka ucunda kodu değiştirmek zorunda kalmazsınız. Uygulamanızı korumanın ve Kullanıcı başına verilerle çalışacak kolay bir yol sağlar.

### <a name="layered-security-architecture"></a>Katmanlı güvenlik mimarisi
[App Service ortamları](../../app-service/environment/app-service-app-service-environment-intro.md) bir [Azure sanal ağına](../../virtual-network/virtual-networks-overview.md)dağıtılan yalıtılmış bir çalışma zamanı ortamı sağladığından, geliştiriciler her uygulama katmanı için farklı düzeylerde ağ erişimi sağlayan katmanlı bir güvenlik mimarisi oluşturabilir. API arka uçlarını genel Internet erişimi 'nden gizlemek ve yalnızca API 'Lerin yukarı akış Web uygulamaları tarafından çağrılmasına izin vermek yaygın bir uygulamadır. [Ağ güvenlik grupları (NSG 'ler)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) , API uygulamalarına genel erişimi kısıtlamak Için App Service ortamları Içeren Azure sanal ağ alt ağlarında kullanılabilir.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web sunucusu tanılama ve Uygulama Tanılama
App Service Web Apps, hem Web sunucusundan hem de Web uygulamasından günlüğe bilgi kaydetmeye yönelik Tanılama işlevleri sağlar. Bunlar, [Web sunucusu tanılama](../../app-service/troubleshoot-diagnostic-logs.md) ve [Uygulama Tanılama](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx)ile mantıksal olarak ayrılır. Web sunucusu, siteleri ve uygulamaları tanılamaya ve sorun gidermeye yönelik iki önemli gelişde sahiptir.

İlk yeni özellik, uygulama havuzları, çalışan süreçler, siteler, uygulama etki alanları ve çalışan istekler hakkında gerçek zamanlı durum bilgileri. İkinci yeni avantajlar, isteği tam istek ve yanıt işlemi boyunca izleyen ayrıntılı izleme olaylardır.

Bu izleme olaylarının toplanmasını etkinleştirmek için, IIS 7, geçen süre veya hata yanıt kodlarına göre belirli bir istek için XML biçiminde tam izleme günlüklerini otomatik olarak yakalanacak şekilde yapılandırılabilir.

#### <a name="web-server-diagnostics"></a>Web sunucusu tanılaması
Aşağıdaki türlerde günlükleri etkinleştirebilir veya devre dışı bırakabilirsiniz:

-   Ayrıntılı hata günlüğü-HTTP durum kodları için hata belirten ayrıntılı hata bilgileri (durum kodu 400 veya üzeri). Bu, sunucunun neden hata kodunu döndürmediğini belirlemede yardımcı olabilecek bilgiler içerebilir.

-   Başarısız Istek Izleme-isteği işlemek için kullanılan IIS bileşenlerinin izlenmesi ve her bileşende geçen süre dahil olmak üzere başarısız istekler hakkında ayrıntılı bilgiler. Bu, site performansını arttırmaya veya belirli bir HTTP hatasının döndürülmesine neden olduğunu ayırmaya çalışıyorsanız yararlı olabilir.

-   Web sunucusu günlüğü-W3C Genişletilmiş günlük dosyası biçimini kullanarak HTTP işlemleri hakkında bilgiler. Bu, işlenen istek sayısı veya belirli bir IP adresinden kaç istek olduğu gibi genel site ölçümlerinin saptanmasında yararlıdır.

#### <a name="application-diagnostics"></a>Uygulama Tanılama
[Uygulama Tanılama](../../app-service/troubleshoot-diagnostic-logs.md) , bir Web uygulaması tarafından üretilen bilgileri yakalamanızı sağlar. ASP.NET uygulamaları, uygulama tanılama günlüğüne bilgileri günlüğe kaydetmek için [System. Diagnostics. Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) sınıfını kullanabilir. Uygulama Tanılama, uygulama performansıyla ilgili olanlar ve uygulama hataları ve hatalarıyla ilgili olan iki önemli tür olay vardır. Hatalar ve hatalar, bağlantı, güvenlik ve başarısızlık sorunlarına göre daha da ayrılabilir. Hata sorunları genellikle uygulama kodundaki bir sorunla ilgilidir.

Uygulama Tanılama, aşağıdaki yollarla gruplanmış olayları görüntüleyebilirsiniz:

-   Tümü (tüm olayları görüntüler)
-   Uygulama hataları (özel durum olaylarını görüntüler)
-   Performans (performans olaylarını görüntüler)

## <a name="storage"></a>Depolama
Bu bölümde, Azure Storage Security 'deki temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="role-based-access-control-rbac"></a>Rol Tabanlı Erişim Denetimi (RBAC)
Depolama hesabınızı rol tabanlı Access Control (RBAC) ile güvenli hale getirebilirsiniz. Erişimi, veri erişimi için güvenlik ilkeleri zorlamak isteyen kuruluşlar için [bilmelidir](https://en.wikipedia.org/wiki/Need_to_know) ve [en az ayrıcalık](https://en.wikipedia.org/wiki/Principle_of_least_privilege) güvenlik ilkelerine göre kısıtlamak zorunludur. Bu erişim hakları, belirli bir kapsamdaki gruplara ve uygulamalara uygun RBAC rolü atanarak verilir. Kullanıcılara ayrıcalık atamak için, depolama hesabı katılımcısı gibi [YERLEŞIK RBAC rollerini](../../role-based-access-control/built-in-roles.md)kullanabilirsiniz. [Azure Resource Manager](../../storage/common/storage-security-guide.md) modelini kullanarak bir depolama hesabı için depolama anahtarlarına erişim, rol tabanlı Access Control (RBAC) aracılığıyla denetlenebilir.

### <a name="shared-access-signature"></a>Paylaşılan erişim Imzası
[Paylaşılan erişim imzası (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md), depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. SAS, belirli bir süre ve belirli bir izin kümesi için Depolama hesabınızdaki nesnelere sınırlı bir istemci izinleri vermeyeceğiniz anlamına gelir. Hesap erişim anahtarlarınızı paylaşmak zorunda kalmadan bu sınırlı izinleri verebilirsiniz.

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme
Aktarım sırasında şifreleme, ağlar arasında iletilirken verilerin korunmasında bir mekanizmadır. Azure depolama ile, aşağıdakileri kullanarak verileri güvenli hale getirebilirsiniz:
-   Azure depolama içine veya dışına veri aktarırken HTTPS gibi [Aktarım düzeyi şifreleme](../../storage/common/storage-security-guide.md).

-   [Azure dosya paylaşımları](../../storage/files/storage-dotnet-how-to-use-files.md)için [SMB 3,0 şifrelemesi](../../storage/common/storage-security-guide.md) gibi bir [hat şifreleme](../../storage/common/storage-security-guide.md).

-   Depolama alanına aktarılmadan önce verileri şifrelemek ve depolama alanı dışına aktarıldıktan sonra verilerin şifresini çözmek için istemci tarafı şifreleme.

### <a name="encryption-at-rest"></a>Bekleme sırasında şifreleme
Birçok kuruluş için, bekleyen veri şifreleme, veri gizliliği, uyumluluk ve veri egemenlik 'e yönelik zorunlu bir adımdır. "Bekleyen" veri şifrelemesini sağlayan üç Azure depolama güvenlik özelliği vardır:

-   [Depolama hizmeti şifrelemesi](../../storage/common/storage-service-encryption.md) , depolama hizmetinin verileri Azure depolama 'ya yazarken otomatik olarak şifrelemesine olanak tanır.

-   [İstemci tarafı şifreleme](../../storage/common/storage-client-side-encryption.md) , bekleyen şifreleme özelliğini de sağlar.

-   [Azure disk şifrelemesi](../azure-security-disk-encryption-overview.md) , bir IaaS sanal makinesi tarafından kullanılan işletim sistemi disklerini ve veri disklerini şifrelemenizi sağlar.

### <a name="storage-analytics"></a>Depolama Analizi
[Azure depolama Analizi](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) günlüğe kaydetme gerçekleştirir ve depolama hesabı için ölçüm verileri sağlar. Bu verileri kullanarak istekleri izleyebilir, kullanım eğilimlerini çözümleyebilir ve depolama hesabınızdaki sorunları tanılayabilirsiniz. Depolama Analizi, başarılı ve başarısız isteklerle ilgili ayrıntılı bilgileri bir depolama hizmetine kaydeder. Bu bilgiler, bireysel istekleri izlemek ve bir depolama hizmetiyle ilgili sorunları tanılamak için kullanılabilir. İstekler en iyi çaba temelinde günlüğe kaydedilir. Aşağıdaki kimlik doğrulamalı istek türleri günlüğe kaydedilir:
-   Başarılı istekler.

-   Zaman aşımı, azaltma, ağ, yetkilendirme ve diğer hatalar da dahil olmak üzere başarısız istekler.

-   Başarısız ve başarılı istekler dahil, paylaşılan erişim Imzası (SAS) kullanan istekler.

-   Analiz verilerine yönelik istekler.

### <a name="enabling-browser-based-clients-using-cors"></a>CORS kullanarak tarayıcı tabanlı Istemcileri etkinleştirme
[Çıkış noktaları arası kaynak paylaşımı (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) , etki alanlarının diğer her kaynağa erişmek için başka bir izin vermesini sağlayan bir mekanizmadır. Kullanıcı Aracısı, belirli bir etki alanından yüklenen JavaScript kodunun başka bir etki alanında bulunan kaynaklara erişmesine izin verildiğinden emin olmak için ek üstbilgiler gönderir. Daha sonra, son etki alanı, kaynakları için özgün etki alanı erişimine izin veren veya reddeden ek üstbilgiler ile yanıt verir.

Azure Storage Hizmetleri, hizmet için CORS kurallarını ayarladıktan sonra, belirttiğiniz kurallara göre izin verilip verilmeyeceğini belirlemede, farklı bir etki alanından hizmete yönelik doğru şekilde kimliği doğrulanmış bir istek değerlendirilmek üzere CORS 'yi destekliyor.

## <a name="networking"></a>Networking (Ağ İletişimi)
Bu bölümde, Azure ağ güvenliği 'ndeki temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="network-layer-controls"></a>Ağ katmanı denetimleri
Ağ erişim denetimi, belirli cihazlarla veya alt ağlardan gelen bağlantıları sınırlama ve ağ güvenliğinin çekirdeğini temsil etme işlemidir. Ağ erişim denetimi amacı, sanal makinelerinize ve hizmetlerinize, yalnızca erişilebilir olmasını istediğiniz kullanıcılar ve cihazlar için erişilebilir olduğundan emin olmaktır.

#### <a name="network-security-groups"></a>Ağ Güvenlik Grupları
[Ağ güvenlik grubu (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) , temel bir durum bilgisi olan paket filtreleme güvenlik duvarıdır ve [5 demet](https://www.techopedia.com/definition/28190/5-tuple)temelinde erişimi denetlemenize olanak sağlar. NSG 'ler uygulama katmanı denetimi veya kimliği doğrulanmış erişim denetimleri sağlamaz. Azure sanal ağı içindeki alt ağlar arasında taşınan trafiği ve bir Azure sanal ağı ile Internet arasındaki trafiği denetlemek için kullanılabilir.

#### <a name="route-control-and-forced-tunneling"></a>Yönlendirme denetimi ve Zorlamalı tünel
Azure sanal ağlarınızdaki yönlendirme davranışını denetleme özelliği, kritik bir ağ güvenliği ve erişim denetimi özelliğidir. Örneğin, Azure sanal ağınıza gelen ve giden tüm trafiğin bu sanal güvenlik gereci üzerinden geldiğinden emin olmak istiyorsanız, yönlendirme davranışını denetleyebilmeniz ve özelleştirebilmeniz gerekir. Bunu, Azure 'da Kullanıcı tanımlı yolları yapılandırarak yapabilirsiniz.

[Kullanıcı tanımlı yollar](../../virtual-network/virtual-networks-udr-overview.md) , mümkün olan en güvenli rotayı sağlamak üzere ayrı sanal makinelere veya alt ağlara taşınan ve giden trafik için gelen ve giden yolları özelleştirmenizi sağlar. [Zorlamalı tünel oluşturma](https://www.petri.com/azure-forced-tunneling) , hizmetlerinizin Internet 'teki cihazlara bağlantı başlatmasına izin verilmediğinden emin olmak için kullanabileceğiniz bir mekanizmadır.

Bu, gelen bağlantıları kabul edebilmesinin ve daha sonra onlara yanıt vermemesine farklıdır. Ön uç Web sunucularının Internet konaklarındaki isteklere yanıt vermesi gerekir ve bu Web sunucularına gelen Internet kaynaklı trafiğe izin verilir ve Web sunucuları yanıt verebilir.

Zorlamalı tünel genellikle şirket içi güvenlik proxy 'leri ve güvenlik duvarları aracılığıyla Internet 'e giden trafiği zorlamak için kullanılır.

#### <a name="virtual-network-security-appliances"></a>Sanal ağ güvenlik gereçleri
Ağ güvenlik grupları, Kullanıcı tanımlı rotalar ve Zorlamalı tünel, [OSI modelinin](https://en.wikipedia.org/wiki/OSI_model)ağ ve aktarım katmanlarında bir güvenlik düzeyi sağlar ve yığının daha yüksek düzeylerinde güvenliği etkinleştirmek istediğiniz zamanlar olabilir. Azure iş ortağı ağ güvenlik gereci çözümünü kullanarak bu gelişmiş ağ güvenliği özelliklerine erişebilirsiniz. [Azure Marketi](https://azure.microsoft.com/marketplace/) ' ni ziyaret ederek ve "güvenlik" ve "ağ güvenliği" araması yaparak en güncel Azure iş ortağı ağ güvenlik çözümlerini bulabilirsiniz.

### <a name="azure-virtual-network"></a>Azure Sanal Ağı
Azure Virtual Network (VNet) buluttaki kendi ağınızın bir gösterimidir. Aboneliğiniz için ayrılmış olan Azure Network Fabric 'in mantıksal bir yalıtımının olması. Bu ağ içindeki IP adres bloklarını, DNS ayarlarını, güvenlik ilkelerini ve yol tablolarını tam olarak denetleyebilirsiniz. Azure sanal ağlarına Azure IaaS sanal makineleri (VM 'Ler) ve/veya [bulut hizmetleri 'ni (PaaS rol örnekleri)](../../cloud-services/cloud-services-choose-me.md) yerleştirebilir ve sanal ağlarınızı alt ağlara segmentleyebilirsiniz.

Bunun yanı sıra, Azure'ın sunduğu [bağlantı seçeneklerinden](../../vpn-gateway/index.yml) birini kullanarak sanal ağı şirket içi ağınıza bağlayabilirsiniz. Özetle, IP adres blokları üzerinde tam bir kontrol sahibi olarak ve Azure'ın sunduğu kurumsal ölçek avantajıyla, ağınızı Azure'a genişletebilirsiniz.

Azure ağ iletişimi, çeşitli güvenli uzaktan erişim senaryolarını destekler. Bunlardan bazıları şunlardır:

-   [Bireysel iş istasyonlarını bir Azure sanal ağına bağlama](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Şirket içi ağı VPN ile bir Azure sanal ağına bağlama](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Şirket içi ağı adanmış WAN bağlantısıyla bir Azure sanal ağına bağlama](../../expressroute/expressroute-introduction.md)

-   [Azure sanal ağlarını birbirlerine bağlama](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Azure sanal ağınız ile şirket içi siteniz arasında ağ trafiği göndermek için Azure sanal ağınız için bir VPN ağ geçidi oluşturmanız gerekir. [VPN ağ geçidi](../../vpn-gateway/vpn-gateway-about-vpngateways.md) , genel bir bağlantı üzerinden şifrelenmiş trafik gönderen bir sanal ağ geçidi türüdür. Azure ağ dokusunda Azure sanal ağları arasında trafik göndermek için VPN ağ geçitlerini de kullanabilirsiniz.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) , şirket içi ağlarınızı bir bağlantı sağlayıcısı tarafından kolaylaştırarak adanmış özel bir bağlantı üzerinden Microsoft bulutuna genişletmenizi sağlayan ADANMıŞ bir WAN bağlantısıdır.

![Express Route](./media/overview/azure-security-fig1.png)

ExpressRoute ile Microsoft Azure, Office 365 ve CRM Online gibi Microsoft bulut hizmetlerine bağlantı kurabilirsiniz. Ortak yerleşim tesisinde bağlantı sağlayıcısı üzerinden herhangi bir ağdan herhangi bir ağa (IP VP), noktadan noktaya Ethernet ağı veya sanal çapraz bağlantısından bağlantı olabilir.

ExpressRoute bağlantıları, genel Internet üzerinden geçmez ve bu nedenle VPN tabanlı çözümlerden daha güvenli olarak düşünülebilir. Bu, ExpressRoute bağlantılarına İnternet üzerindeki sıradan bağlantılara göre daha fazla güvenilirlik, yüksek hız, düşük gecikme ve normal bağlantılardan daha yüksek güvenlik sağlar.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](../../application-gateway/overview.md) , uygulamanız için çeşitli 7. katman yük dengeleme özellikleri sunan bir hizmet olarak [uygulama teslım denetleyicisi (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) sağlar.

![Application Gateway](./media/overview/azure-security-fig2.png)

CPU yoğun SSL sonlandırmasını Application Gateway ("SSL boşaltması" veya "SSL köprüleme" olarak da bilinir) sağlayarak web grubu üretkenliğini en iyi hale getirmenize olanak tanır. Ayrıca, gelen trafiğin hepsini bir kez deneme dağıtımı, tanımlama bilgisi tabanlı oturum benzeşimi, URL yolu tabanlı Yönlendirme ve tek bir Application Gateway arkasında birden fazla Web sitesi barındırma özelliği de dahil olmak üzere diğer 7. katman yönlendirme özelliklerini de sağlar. Azure Application Gateway, bir katman 7 yük dengeleyicidir.

Bulutta veya şirket içinde olmalarından bağımsız olarak, farklı sunucular arasında yük devretme ile HTTP istekleri için performans amaçlı yönlendirme sağlar.

Uygulama, HTTP yük dengelemesi, tanımlama bilgisi tabanlı oturum benzeşimi, [Güvenli Yuva Katmanı (SSL)](../../application-gateway/tutorial-restrict-web-traffic-powershell.md) boşaltma, özel durum araştırmaları, çok siteli destek ve birçok başka uygulama teslim DENETLEYICISI (ADC) özelliği sunar.

### <a name="web-application-firewall"></a>Web Application Firewall
Web uygulaması güvenlik duvarı, standart uygulama teslim denetimi (ADC) işlevleri için Application Gateway kullanan Web uygulamalarına koruma sağlayan bir [Azure Application Gateway](../../application-gateway/overview.md) özelliğidir. Web uygulaması güvenlik duvarı bunu, uygulamaları OWASP tarafından sunulan en yaygın 10 web güvenlik açığının çoğuna karşı koruyarak gerçekleştirir.

![Web Application Firewall](./media/overview/azure-security-fig1.png)

-   SQL ekleme koruması

-   Komut ekleme, HTTP isteği kaçakçılığı, HTTP yanıtı bölme ve uzak dosya ekleme saldırıcı gibi Yaygın Web Saldırıları Koruması

-   HTTP protokolü ihlallerine karşı koruma

-   Eksik konak kullanıcısı-aracısı ve kabul üst bilgileri gibi HTTP protokolü anormalliklerine karşı koruma

-   Robotlar, gezginler ve tarayıcıları önleme

-   Yaygın uygulama yapılandırmalarını algılama (yani, Apache, IIS vb.)

Web saldırılarına karşı korunacak merkezi bir web uygulaması, güvenlik yönetimini çok daha kolay hale getirir ve yetkisiz erişim tehditlerine karşı uygulamayı daha güvende tutar. Bir WAF çözümü, bilinen bir güvenlik açığına merkezi bir konumda düzeltme eki uygulayarak güvenlik tehdidine karşı, web uygulamalarının her birinin güvenliğini sağlamaya göre daha hızlı tepki verebilir. Var olan uygulama ağ geçitleri, web uygulaması güvenlik duvarı bulunan bir uygulama ağ geçidine kolaylıkla dönüştürülebilir.

### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) , farklı veri merkezlerindeki hizmet uç noktaları için Kullanıcı trafiğinin dağıtımını denetlemenize olanak tanır. Traffic Manager tarafından desteklenen hizmet uç noktaları, Azure VM 'Leri, Web Apps ve bulut hizmetlerini içerir. Traffic Manager’ı harici, Azure dışı uç noktalar için de kullanabilirsiniz. Traffic Manager, istemci isteklerini bir [trafik yönlendirme yöntemine](../../traffic-manager/traffic-manager-routing-methods.md) ve uç noktaların sistem durumuna göre en uygun uç noktaya yönlendirmek Için etki alanı adı sistemi 'NI (DNS) kullanır.

Traffic Manager, farklı uygulama ihtiyaçlarına, uç nokta durumuna [izlemeye](../../traffic-manager/traffic-manager-monitoring.md)ve otomatik yük devretmeye uyacak bir dizi trafik yönlendirme yöntemi sağlar. Traffic Manager, bir Azure bölgesinin tamamının devre dışı kalması dahil olmak üzere hatalara dayanıklıdır.

### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](../../load-balancer/load-balancer-overview.md), uygulamalarınıza yüksek düzeyde kullanılabilirlik ve ağ performansı sağlar. Bu, gelen trafiği yük dengeli bir küme içinde tanımlanan sağlıklı hizmet örnekleri arasında dağıtan bir katman 4 (TCP, UDP) yük dengeleyicidir. Azure Load Balancer, şu şekilde yapılandırılabilir:

-   Sanal makinelere gelen Internet trafiğinin yükünü dengeleyin. Bu yapılandırma [Internet 'e yönelik yük dengeleme](../../load-balancer/load-balancer-overview.md#publicloadbalancer)olarak bilinir.

-   Bir sanal ağ içindeki sanal makineler arasında, bulut hizmetlerindeki sanal makineler arasında veya şirket içi bilgisayarlar ile şirketler arası bir sanal ağdaki sanal makineler arasında yük dengeleme trafiği. Bu yapılandırma, [İç Yük Dengeleme](../../load-balancer/load-balancer-overview.md#internalloadbalancer)olarak bilinir.

- Dış trafiği belirli bir sanal makineye ilet

### <a name="internal-dns"></a>İç DNS
Yönetim Portalı veya ağ yapılandırma dosyasında bir VNet 'te kullanılan DNS sunucularının listesini yönetebilirsiniz. Müşteri, her VNet için en fazla 12 DNS sunucusu ekleyebilir. DNS sunucularını belirtirken, müşterinin DNS sunucularını müşterinin ortamı için doğru sırada listediğinizi doğrulamanız önemlidir. DNS sunucusu listeleri hepsini bir kez deneme çalışmaz. Bunlar belirtildikleri sırayla kullanılırlar. Listedeki ilk DNS sunucusuna ulaşılırsa istemci, DNS sunucusunun düzgün çalışıp çalışmadığını ne olursa olsun bu DNS sunucusunu kullanır. Müşterinin sanal ağının DNS sunucusu sırasını değiştirmek için, DNS sunucularını listeden kaldırın ve müşterinin istediği sıraya göre yeniden ekleyin. DNS, "CIA" güvenlik Triad 'nin kullanılabilirlik oranını destekler.

### <a name="azure-dns"></a>Azure DNS
[Etki alanı adı sistemi](https://technet.microsoft.com/library/bb629410.aspx)veya DNS, IP adresine bir Web sitesi veya hizmet adı çevirmekten (veya çözümlemeden) sorumludur. [Azure DNS](../../dns/dns-overview.md) , Microsoft Azure altyapısı kullanılarak ad ÇÖZÜMLEMESI sağlayan DNS etki alanları için bir barındırma hizmetidir. Etki alanlarınızı Azure'da barındırarak DNS kayıtlarınızı diğer Azure hizmetlerinde kullandığınız kimlik bilgileri, API’ler, araçlar ve faturalarla yönetebilirsiniz. DNS, "CIA" güvenlik Triad 'nin kullanılabilirlik oranını destekler.

### <a name="azure-monitor-logs-nsgs"></a>Azure Izleyici günlükleri NSG 'leri
NSG 'ler için aşağıdaki tanılama günlüğü kategorilerini etkinleştirebilirsiniz:

-   Olay: VM 'lere ve MAC adresine göre örnek rollere uygulanan NSG kurallarının girdilerini Içerir. Bu kuralların durumu her 60 saniyede toplanır.

-   Kurallar sayacı: her NSG kuralının trafiği reddetme veya izin verme için kaç kez uygulanacağını gösteren girişleri Içerir.

### <a name="security-center"></a>Güvenlik Merkezi
[Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) , ağ güvenliği en iyi uygulamaları için Azure kaynaklarınızın güvenlik durumunu sürekli olarak analiz eder. Güvenlik Merkezi olası güvenlik açıklarını belirlediğinde, kaynaklarınızı korumak ve korumak için gerekli denetimleri yapılandırma sürecinde size kılavuzluk eden [öneriler](../../security-center/security-center-recommendations.md) oluşturur.

## <a name="compute"></a>İşlem
Bu bölümde, bu alandaki temel özelliklerle ilgili ek bilgiler ve bu yetenekler hakkında özet bilgiler sağlanmaktadır.

### <a name="antimalware--antivirus"></a>Kötü amaçlı yazılımdan koruma &
Azure IaaS ile, bir kötü amaçlı yazılımdan koruma yazılımını Microsoft, Symantec, Trend Micro, McAfee ve Kaspersky gibi güvenlik satıcılarından kullanarak sanal makinelerinizi kötü amaçlı dosyalardan, reklam yazılımlarından ve diğer tehditlere karşı koruyabilirsiniz. Azure Cloud Services için [Microsoft kötü amaçlı yazılımdan](antimalware.md) koruma ve sanal makineler, virüsler, casus yazılım ve diğer kötü amaçlı yazılımların tanımlanmasına ve kaldırılmasına yardımcı olan bir koruma özelliğidir. Microsoft Antimalware, bilinen kötü amaçlı veya istenmeyen yazılımlar kendisini yüklemeye veya Azure sistemlerinizde çalışmaya çalıştığında yapılandırılabilir uyarılar sağlar. Microsoft kötü amaçlı yazılımdan koruma, Azure Güvenlik Merkezi kullanılarak da dağıtılabilir

### <a name="hardware-security-module"></a>Donanım güvenlik modülü
Anahtarlar korunmadığı takdirde şifreleme ve kimlik doğrulama güvenliği geliştirir. Kritik gizli dizilerlerinizin ve anahtarların yönetim ve güvenliğini [Azure Key Vault](../../key-vault/key-vault-overview.md)içinde depolayarak kolaylaştırabilirsiniz. Key Vault, anahtarlarınızı FIPS 140-2 düzey 2 standartlarına sertifikalı donanım güvenlik modüllerinde (HSM 'ler) depolama seçeneği sunar. Yedekleme veya [Saydam veri şifrelemesi](https://msdn.microsoft.com/library/bb934049.aspx) için SQL Server şifreleme anahtarlarınızın tümü, uygulamalarınızda herhangi bir anahtar veya gizli dizi ile Key Vault depolanabilir. Bu korumalı öğelere izinler ve erişim [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)üzerinden yönetilir.

### <a name="virtual-machine-backup"></a>Sanal makine yedekleme
[Azure Backup](../../backup/backup-overview.md) , uygulama verilerinizi sıfır sermaye yatırımı ve en az işletim maliyetiyle koruyan bir çözümdür. Uygulama hataları verilerinizi bozabilir ve insan hataları, uygulamalarınıza güvenlik sorunlarına neden olan hataları ortaya çıkarabilir. Azure Backup, Windows ve Linux çalıştıran sanal makineleriniz korunur.

### <a name="azure-site-recovery"></a>Azure Site Recovery
Kuruluşunuzun [iş sürekliliği/olağanüstü durum kurtarma (BCDR)](../../best-practices-availability-paired-regions.md) stratejisinin önemli bir bölümü, planlı ve plansız kesintiler gerçekleştiğinde kurumsal iş yüklerini ve uygulamaları nasıl çalışır durumda tutabileceğini öğrenmelidir. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) , birincil konumunuz daha sonra ikincil bir konumdan kullanılabilmeleri için iş yükleri ve uygulamaların çoğaltılmasını, yük devretmesini ve kurtarılmasına yardımcı olur.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Saydam veri şifrelemesi (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) ve sütun düzeyinde ŞIFRELEME (CLE), SQL Server şifreleme özellikleridir. Bu şifreleme biçimi müşterilerin şifreleme için kullandığınız şifreleme anahtarlarını yönetmesi ve depolaması gerekir.

Azure Key Vault (AKV) hizmeti, güvenli ve yüksek oranda kullanılabilir bir konumda bu anahtarların güvenliğini ve yönetimini geliştirmek için tasarlanmıştır. SQL Server Bağlayıcısı, SQL Server Azure Key Vault bu anahtarları kullanmasına olanak sağlar.

Şirket içi makinelerle SQL Server çalıştırıyorsanız, şirket içi SQL Server makinenizden Azure Key Vault erişmek için izleyebileceğiniz adımlar vardır. Ancak Azure VM 'lerinde SQL Server için Azure Key Vault tümleştirme özelliğini kullanarak zamandan tasarruf edebilirsiniz. Bu özelliği etkinleştirmek için birkaç Azure PowerShell cmdlet 'i sayesinde, anahtar kasanıza erişmek için bir SQL sanal makinesi için gereken yapılandırmayı otomatikleştirin.

### <a name="vm-disk-encryption"></a>VM disk şifrelemesi
[Azure disk şifrelemesi](../azure-security-disk-encryption-overview.md) , Windows ve Linux IaaS sanal makine disklerinizi şifrelemenize yardımcı olan yeni bir özelliktir. İşletim sistemi ve veri diskleri için birim şifrelemesi sağlamak üzere Windows 'un sektör standardı BitLocker özelliğini ve Linux 'un DM-Crypt özelliğini uygular. Çözüm, Key Vault aboneliğinizdeki disk şifreleme anahtarlarını ve gizli dizileri denetlemenize ve yönetmenize yardımcı olmak için Azure Key Vault ile tümleşiktir. Çözüm Ayrıca, sanal makine disklerindeki tüm verilerin Azure depolamadaki geri kalanta şifrelenmesini de sağlar.

### <a name="virtual-networking"></a>Sanal ağ
Sanal makinelerin ağ bağlantısı olması gerekir. Azure, bu gereksinimi desteklemek için sanal makinelerin bir Azure sanal ağına bağlanmasını gerektirir. Azure sanal ağı, fiziksel Azure ağ dokusunun üzerine oluşturulan mantıksal bir yapıdır. Her mantıksal [Azure sanal ağı](../../virtual-network/virtual-networks-overview.md) , diğer tüm Azure sanal ağlarından yalıtılmıştır. Bu yalıtım, dağıtımlarınızdaki ağ trafiğine diğer Microsoft Azure müşterilerin erişimine açık olmadığından emin olmanıza yardımcı olur.

### <a name="patch-updates"></a>Düzeltme Eki güncelleştirmeleri
Düzeltme Eki güncelleştirmeleri olası sorunları bulma ve düzeltmeye ve yazılım güncelleştirme yönetimi sürecini basitleştirerek, hem kuruluşunuzda dağıtmanız gereken yazılım güncelleştirme sayısını azaltarak hem de uyumluluğu izleme yeteneğinizi artırarak bir temel sağlar.

### <a name="security-policy-management-and-reporting"></a>Güvenlik İlkesi Yönetimi ve raporlama
[Güvenlik Merkezi](../../security-center/security-center-intro.md) tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur ve Azure kaynaklarınızın güvenliğini ve denetimini artırabilir. Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve güvenlik çözümlerinin geniş bir ekosistemiyle birlikte çalışabilir.

## <a name="identity-and-access-management"></a>Kimlik ve erişim yönetimi
Sistemlerin, uygulamaların ve verilerin güvenliğini sağlamak, kimlik tabanlı erişim denetimleriyle başlar. Microsoft iş ürünleri ve Hizmetleri 'nde yerleşik olarak bulunan kimlik ve erişim yönetimi özellikleri, kuruluşunuzun ve kişisel bilgilerinizin, her zaman ve her yerde meşru kullanıcılar tarafından kullanılabilir hale getirilmesi sırasında yetkisiz erişimden korunmasına yardımcı olur. Bunu yapmanız gerekir.

### <a name="secure-identity"></a>Güvenli kimlik
Microsoft, ürün ve hizmetlerinde kimlik ve erişim yönetimi için birden çok güvenlik uygulaması ve teknolojisini kullanır.

-   [Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) , kullanıcıların şirket içinde ve bulutta erişim için birden çok yöntem kullanmasını gerektirir. Kolay bir kimlik doğrulama seçenekleriyle, kullanıcılara basit bir oturum açma işlemi ile konairken güçlü kimlik doğrulaması sağlar.

-   [Microsoft Authenticator](https://aka.ms/authenticator) , hem Microsoft Azure Active Directory hem de Microsoft hesaplarıyla birlikte çalışarak kullanıcı dostu Multi-Factor Authentication deneyimi sağlar ve wearables ve parmak izi tabanlı onaylar için destek içerir.

-   [Parola ilkesi zorlaması](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) , hatalı kimlik doğrulama denemesinden sonra uzunluğu ve karmaşıklık gereksinimlerini, zorla düzenli döndürmeyi ve hesap kilitlemeyi düzenleyerek geleneksel parolaların güvenliğini artırır.

-   [Belirteç tabanlı kimlik doğrulaması](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) , Azure Active Directory aracılığıyla kimlik doğrulaması yapılmasını mümkün.

-   [Rol tabanlı erişim denetimi (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) , kullanıcının atanan rolüne göre erişim sağlamanıza olanak sağlayarak kullanıcılara yalnızca iş görevlerini gerçekleştirmek için ihtiyaç duydukları erişim miktarını vermeyi kolaylaştırır. RBAC 'yi kuruluşunuzun iş modeli ve risk toleransı başına özelleştirebilirsiniz.

-   [Tümleşik kimlik yönetimi (Hibrit kimliği)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) , kullanıcıların iç veri merkezleri ve bulut platformları genelinde erişiminin denetimini korumanıza olanak sağlar. bu sayede, tüm kaynaklara yönelik kimlik doğrulama ve yetkilendirme için tek bir Kullanıcı Kimliği oluşturursunuz.

### <a name="secure-apps-and-data"></a>Uygulamaları ve verileri güvenli hale getirme
Kapsamlı bir kimlik ve erişim yönetimi bulut çözümü olan [Azure Active Directory](https://azure.microsoft.com/services/active-directory/), sitedeki ve buluttaki uygulamalardaki verilere erişimi güvenli hale getirmeye yardımcı olur ve kullanıcıların ve grupların yönetimini basitleştirir. Temel Dizin Hizmetleri, Gelişmiş kimlik yönetimi, güvenlik ve uygulama erişimi yönetimini birleştirir ve geliştiricilerin uygulamalarına ilke tabanlı kimlik yönetimi oluşturmasını kolaylaştırır. Azure Active Directory'nizi geliştirmek için Azure Active Directory Temel, Premium P1 ve Premium P2 sürümleriyle ücretli özellikler ekleyebilirsiniz.

| Ücretsiz/ortak özellikler     | Temel Özellikler    |Premium P1 özellikleri |Premium P2 özellikleri | Azure Active Directory JOIN – yalnızca Windows 10 ile ilgili özellikler|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Dizin nesneleri](../../active-directory/active-directory-whatis.md), [Kullanıcı/Grup Yönetimi (ekleme/güncelleştirme/silme)/Kullanıcı tabanlı sağlama, cihaz kaydı](../../active-directory/active-directory-whatis.md), [Çoklu oturum açma (SSO)](../../active-directory/active-directory-whatis.md), [bulut kullanıcıları için self servis parola değişikliği](../../active-directory/active-directory-whatis.md), [bağlanma (eşitleme motoru Şirket içi dizinleri Azure Active Directory)](../../active-directory/active-directory-whatis.md), [güvenlik/kullanım raporlarını](../../active-directory/active-directory-whatis.md) genişletir       |   [Grup tabanlı erişim yönetimi/sağlama](../../active-directory/active-directory-whatis.md), [bulut kullanıcıları Için self servis parola sıfırlama](../../active-directory/active-directory-whatis.md), [Şirket markası (oturum açma sayfaları/erişim paneli özelleştirmesi)](../../active-directory/active-directory-whatis.md), [uygulama proxy 'si](../../active-directory/active-directory-whatis.md), [SLA 99,9%](../../active-directory/active-directory-whatis.md) |  [Self servis grup ve uygulama yönetimi/self servis uygulama eklemeleri/Dinamik grupları](../../active-directory/active-directory-whatis.md), şirket içi geri yazma, Multi-Factor Authentication (bulut ve şirket ıçı (MFA sunucusu) [ile birlikte self servis parola sıfırlama/değiştirme/kilit açma](../../active-directory/active-directory-whatis.md) [ )](../../active-directory/active-directory-whatis.md), [MıM Cal + MIM sunucusu](../../active-directory/active-directory-whatis.md), [Cloud App Discovery](../../active-directory/active-directory-whatis.md), [Connect Health](../../active-directory/active-directory-whatis.md), [Grup hesapları için otomatik parola geçişi](../../active-directory/active-directory-whatis.md)|    [Kimlik koruması](../../active-directory/identity-protection/overview.md), [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)|   [Bir cihazı Azure AD, Masaüstü SSO, Azure AD için Microsoft Passport, yönetici BitLocker kurtarma](../../active-directory/active-directory-whatis.md), [MDM otomatik kayıt, self servis BitLocker kurtarma, Azure AD katılımı aracılığıyla Windows 10 cihazlarına](../../active-directory/active-directory-whatis.md) ekleyin|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) , kuruluşunuzdaki çalışanlar tarafından kullanılan bulut uygulamalarını tanımlamanızı sağlayan Azure Active Directory Premium bir özelliğidir.

- [Azure Active Directory kimlik koruması](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) , risk algılamaları ve kuruluşunuzun özelliklerini etkileyebilecek olası güvenlik açıklarına birleştirilmiş bir görünüm sağlamak için Azure Active Directory anomali algılama yeteneklerini kullanan bir güvenlik hizmetidir lerinizde.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) , etki alanı denetleyicilerini dağıtmanıza gerek kalmadan Azure VM 'lerine bir etki alanına katmanızı sağlar. Kullanıcılar bu VM 'Lerde kurumsal Active Directory kimlik bilgilerini kullanarak oturum açabilir ve kaynaklara sorunsuz bir şekilde erişebilir.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , tüketiciye yönelik uygulamalar için yüzlerce milyonlarca kimliğe ölçeklenebilen ve mobil ve web platformlarında tümleştirilebilen, yüksek oranda kullanılabilir küresel bir kimlik yönetimi hizmetidir. Müşterileriniz, var olan sosyal medya hesaplarını kullanan özelleştirilebilen deneyimler aracılığıyla tüm uygulamalarınızda oturum açabilir veya yeni tek başına kimlik bilgileri oluşturabilirsiniz.

- [Azure ACTIVE DIRECTORY B2B işbirliği](https://aka.ms/aad-b2b-collaboration) , iş ortaklarının şirket uygulamalarınıza ve verilerinize kendi kendine yönetilen kimliklerini kullanarak erişmesini etkinleştirerek şirketler arası ilişkilerinizi destekleyen güvenli bir iş ortağı tümleştirme çözümüdür. .

- [Azure Active Directory katılması](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) , merkezi yönetim için bulut yeteneklerini Windows 10 cihazlarına genişletmenizi sağlar. Kullanıcıların kurumsal veya kurumsal buluta Azure Active Directory aracılığıyla bağlanmasını sağlar ve uygulama ve kaynaklara erişimi basitleştirir.

- [Azure Active Directory uygulama ara sunucusu](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) , şirket içinde barındırılan Web UYGULAMALARı için SSO ve güvenli uzaktan erişim sağlar.

## <a name="next-steps"></a>Sonraki Adımlar

- [Bulutta paylaşılan sorumluluğu](shared-responsibility.md)anlayın.

- Azure [Güvenlik Merkezi](https://azure.microsoft.com/services/security-center/) 'Nin, Azure kaynaklarınızın güvenliğine yönelik artırılmış görünürlük ve denetim ile tehditleri önlemenize, algılamanıza ve bu tehditlere yanıt vermenize nasıl yardımcı olabileceğini öğrenin.
