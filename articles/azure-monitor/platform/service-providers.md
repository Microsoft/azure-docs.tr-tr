---
title: Hizmet sağlayıcıları için Azure Izleyici | Microsoft Docs
description: Azure Izleyici, yönetilen hizmet sağlayıcılarının (MSPs), büyük kuruluşların, bağımsız yazılım satıcılarının (ISV 'Ler) ve barındırma hizmeti sağlayıcılarının, müşterinin Şirket içi veya bulut altyapısındaki sunucuları yönetmesine ve izlemesine yardımcı olabilir.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 08/06/2019
ms.openlocfilehash: b0f25d01421edd329b03d8f2b7e1aafaa2ba67d5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932070"
---
# <a name="azure-monitor-for-service-providers"></a>Hizmet sağlayıcıları için Azure Izleyici
Azure Izleyici 'deki Log Analytics çalışma alanları, yönetilen hizmet sağlayıcılarının (MSPs), büyük kuruluşların, bağımsız yazılım satıcılarının (ISV) ve barındırma hizmeti sağlayıcılarının, müşterinin Şirket içi veya bulut altyapısındaki sunucuları yönetmesine ve izlemesine yardımcı olabilir. 

Büyük kuruluşlar, özellikle birçok farklı iş birimi için yönetmekten sorumlu bir merkezi BT ekibi olduğunda, servis sağlayıcılarıyla birçok benzerlik paylaşır. Kolaylık olması için, bu belge *hizmet sağlayıcısını* kullanır, ancak kuruluşlar ve diğer müşteriler için aynı işlevsellik de mevcuttur.

[Bulut çözümü sağlayıcısı (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) programının parçası olan iş ortakları ve hizmet sağlayıcıları Için Azure izleyici 'de log ANALYTICS Azure [CSP aboneliklerinde](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)bulunan Azure hizmetlerinden biridir. 

## <a name="architectures-for-service-providers"></a>Hizmet sağlayıcıları için mimariler

Log Analytics çalışma alanları, yöneticinin [günlük](data-platform-logs.md) verilerinin akışını ve yalıtımının denetlenmesi ve belirli iş ihtiyaçlarını karşılayan bir mimari oluşturması için bir yöntem sağlar. [Bu makalede](design-logs-deployment.md) bir çalışma alanı için tasarım, dağıtım ve geçiş konuları açıklanmakta ve [erişim yönetimi](manage-access.md) makalesi, günlük verilerine yönelik izinlerin nasıl uygulanacağını ve yönetileceğini açıklar. Hizmet sağlayıcılarının ek konuları vardır.

Hizmet sağlayıcılarının Log Analytics çalışma alanlarıyla ilgili üç olası mimarisi vardır:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. dağıtılmış Günlükler müşterinin kiracısında bulunan çalışma alanlarında depolanır 

Bu mimaride, müşterinin kiracısında müşterinin tüm günlükleri için kullanılan bir çalışma alanı dağıtılır. Hizmet sağlayıcı yöneticilerine [Azure Active Directory Konuk kullanıcılar (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)kullanılarak bu çalışma alanına erişim verilir. Hizmet sağlayıcı yöneticilerinin, bu çalışma alanlarına erişebilmek için Azure portal kendi müşterilerinin dizinine geçmesi gerekir.

Bu mimarinin avantajları şunlardır:
* Müşteri, kendi [rol tabanlı erişimini](https://docs.microsoft.com/azure/role-based-access-control/overview)kullanarak günlüklere erişimi yönetebilir.
* Her müşteri, çalışma alanı için bekletme ve veri dönüşü gibi farklı ayarlara sahip olabilir.
* Yönetmelik ve zorluk açısından müşteriler arasında yalıtım.
* Her çalışma alanı için ücret müşterinin aboneliğine alınacaktır.
* Günlükler yalnızca aracı tabanlı değil, tüm kaynak türleri için toplanabilir. Örneğin, Azure denetim günlükleri.

Bu mimarinin dezavantajları şunlardır:
* Hizmet sağlayıcısının aynı anda çok sayıda müşteri kiracıyı yönetmesi daha zordur.
* Hizmet sağlayıcısı yöneticilerinin müşteri dizininde sağlanması gerekir.
* Hizmet sağlayıcı, müşterileri genelinde verileri analiz edemiyor.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. orta Günlükler, hizmet sağlayıcı kiracısında bulunan bir çalışma alanında depolanır

Bu mimaride, Günlükler müşterinin kiracılarında depolanmaz, ancak yalnızca hizmet sağlayıcının aboneliklerinde bulunan merkezi bir konumda depolanır. Müşterinin VM 'lerine yüklenen aracılar, çalışma alanı KIMLIĞI ve gizli anahtarı kullanılarak günlüklerini bu çalışma alanına gönderecek şekilde yapılandırılmıştır.

Bu mimarinin avantajları şunlardır:
* Çok sayıda müşteriyi yönetmek ve bunları çeşitli arka uç sistemleriyle bütünleştirmek kolaydır.

* Hizmet sağlayıcısı Günlükler üzerinde tam sahiplik ve işlevler ve kaydedilmiş sorgular gibi çeşitli yapıtlar içerir.

* Hizmet sağlayıcı, tüm müşterileri genelinde analiz gerçekleştirebilir.

Bu mimarinin dezavantajları şunlardır:
* Bu mimari yalnızca aracı tabanlı VM verileri için geçerlidir; PaaS, SaaS ve Azure Fabric veri kaynaklarını kapsamaz.

* Tek bir çalışma alanında birleştirildiklerinde müşteriler arasında verileri ayırmak zor olabilir. Bunu yapmak için tek iyi yöntem bilgisayarın tam etki alanı adını (FQDN) veya Azure abonelik KIMLIĞINI kullanmaktır. 

* Tüm müşterilerin tüm verileri, tek bir fatura ve aynı bekletme ve yapılandırma ayarları ile aynı bölgede depolanır.

* Azure Tanılama ve Azure denetim günlükleri gibi Azure Fabric ve PaaS hizmetleri, çalışma alanının kaynakla aynı kiracıda olmasını gerektirir, bu nedenle günlükleri merkezi çalışma alanına gönderemez.

* Tüm müşterilerin tüm VM aracılarının, aynı çalışma alanı KIMLIĞI ve anahtarı kullanılarak merkezi çalışma alanında kimlik doğrulaması yapılır. Belirli bir müşteriden diğer müşterileri kesintiye uğramadan günlüklerin engellenme yöntemi yoktur.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. karma Günlükler müşterinin kiracısında bulunan çalışma alanında depolanır ve bazıları merkezi bir konuma çekilir.

İki seçenek arasındaki üçüncü mimari karışımı. Günlüklerin her müşteri için yerelden olduğu, ancak merkezi bir günlük deposu oluşturmak için bazı mekanizmalarının kullanıldığı ilk dağıtılmış mimariye dayanır. Günlüklerin bir kısmı, raporlama ve analiz için merkezi bir konuma çekilir. Bu bölüm, az sayıda veri türü veya günlük istatistikler gibi etkinliğin bir özeti olabilir.

Günlükleri merkezi bir konumda uygulamak için iki seçenek vardır:

1. Merkezi çalışma alanı: hizmet sağlayıcı kiracısında bir çalışma alanı oluşturabilir ve verileri çeşitli çalışma alanlarından bu merkezi konuma getirmek için [veri toplama API](../../azure-monitor/platform/data-collector-api.md) 'Siyle bırlıkte [sorgu API](https://dev.loganalytics.io/) 'sini kullanan bir komut dosyası kullanabilir. Bir komut dosyası dışında başka bir seçenek [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)kullanmaktır.

2. Merkezi bir konum olarak Power BI: Power BI, Log Analytics çalışma alanı ve [Power BI](../../azure-monitor/platform/powerbi.md)arasındaki tümleştirmeyi kullanarak verileri dışa aktardığınızda merkezi konum olarak görev yapabilir. 

## <a name="next-steps"></a>Sonraki adımlar

* [Kaynak Yöneticisi şablonlarını](template-workspace-configuration.md) kullanarak çalışma alanlarının oluşturulmasını ve yapılandırılmasını otomatikleştirin

* [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) kullanarak çalışma alanlarının oluşturulmasını otomatikleştirme 

* Mevcut sistemlerle tümleştirme için [uyarıları](../../azure-monitor/platform/alerts-overview.md) kullanma

* [Power BI](../../azure-monitor/platform/powerbi.md) kullanarak özet raporlar oluşturma

* Log Analytics yapılandırma sürecini gözden geçirin [ve birden çok CSP müşterisi izlemek için Power BI](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
