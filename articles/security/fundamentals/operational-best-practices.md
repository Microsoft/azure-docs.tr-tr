---
title: Varlıklarınızı korumak için en iyi güvenlik yöntemleri-Microsoft Azure
description: Bu makalede, Azure 'daki verilerinizi, uygulamalarınızı ve diğer varlıkları korumaya yönelik işlemsel en iyi uygulamalar sağlanır.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 69e55b17814c5b5ada0813bd0de66cc3a9a591f4
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219514"
---
# <a name="azure-operational-security-best-practices"></a>Azure operasyonel güvenlik en iyi uygulamaları
Bu makalede, Azure 'daki verilerinizi, uygulamalarınızı ve diğer varlıkları korumaya yönelik işlemsel en iyi uygulamalar sağlanır.

En iyi uygulamalar, görüşlerden oluşan yarışmaları temel alır ve geçerli Azure platformu özellikleri ve özellik kümeleriyle çalışır. Zamanla ve teknolojiler zaman içinde değişir ve bu makale, bu değişiklikleri yansıtacak şekilde düzenli olarak güncelleştirilir.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Güçlü işlemsel güvenlik uygulamalarını tanımlama ve dağıtma
Azure operasyonel güvenlik, Azure 'daki verilerini, uygulamalarını ve diğer varlıklarını korumak için kullanıcılara sunulan hizmetleri, denetimleri ve özellikleri ifade eder. Azure operasyonel güvenlik, [güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl), [Microsoft Güvenlik Yanıt Merkezi](https://www.microsoft.com/msrc?rtc=1) dahil olmak üzere Microsoft 'a özgü yetenekler aracılığıyla elde edilen bilgileri içeren bir çerçeve üzerine kurulmuştur. siber güvenlik tehdidi için program ve derin bir şekilde tanıma.

## <a name="manage-and-monitor-user-passwords"></a>Kullanıcı parolalarını yönetme ve izleme
Aşağıdaki tabloda kullanıcı parolalarının yönetimiyle ilgili bazı en iyi uygulamalar listelenmektedir:

**En iyi uygulama**: Bulutta uygun parola koruma düzeyine sahip olduğunuzdan emin olun.   
**Ayrıntı**: Microsoft kimlik platformları (Azure Active Directory, Active Directory ve Microsoft hesabı) kapsamındaki [Microsoft parola Kılavuzu](https://www.microsoft.com/research/publication/password-guidance/)'ndaki yönergeleri izleyin.

**En iyi uygulama**: Kullanıcı hesaplarınızla ilgili şüpheli eylemleri izleyin.   
**Ayrıntı**: Azure AD güvenlik raporlarını kullanarak [riskli kullanıcılar](/azure/active-directory/reports-monitoring/concept-user-at-risk) ve [riskli oturum açma](../../active-directory/reports-monitoring/concept-risk-events.md) işlemlerini izleyin.

**En iyi uygulama**: Yüksek riskli parolaları otomatik olarak algıla ve düzelt.   
**Ayrıntı**: [Azure AD kimlik koruması](/azure/active-directory/identity-protection/overview) , aşağıdakileri yapmanızı sağlayan Azure AD Premium P2 sürümünün bir özelliğidir:

- Kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını algılama
- Otomatik yanıtları, kuruluşunuzun kimlikleri ile ilgili şüpheli, algılanan şüpheli eylemler için yapılandırın
- Şüpheli olayları araştırın ve bunları çözmek için gerekli işlemleri yapın

## <a name="receive-incident-notifications-from-microsoft"></a>Microsoft 'tan olay bildirimleri al
Güvenlik işlemleri takımınızın Microsoft 'un Azure olay bildirimlerini aldığından emin olun. Bir olay bildirimi, güvenlik takımınızın, olası güvenlik risklerini hızlı bir şekilde yanıtlaması ve düzeltebilmeleri için Azure kaynaklarınızın güvenliğinin aşıldığını bilmesini sağlar.

Azure kayıt portalında, yönetici iletişim bilgilerinin güvenlik işlemlerine bildirimde bulunan ayrıntıları içerdiğinden emin olabilirsiniz. İletişim bilgileri, bir e-posta adresi ve telefon numarasıdır.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure aboneliklerini yönetim gruplarında düzenleme
Kuruluşunuzun birçok aboneliği varsa, bu abonelikler için erişimi, ilkeleri ve uyumluluğu verimli bir şekilde yönetmeniz için bir yol gerekebilir. [Azure Yönetim grupları](/azure/governance/management-groups/create) , aboneliklerdeki bir kapsam düzeyi sağlar. Abonelikleri yönetim grupları adlı kapsayıcılara düzenler ve idare koşullarınızı yönetim gruplarına uygularsınız. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan koşulları devralır.

Bir dizinde yönetim gruplarının ve aboneliklerinin esnek bir yapısını oluşturabilirsiniz. Her dizine kök yönetim grubu adlı tek bir en üst düzey yönetim grubu verilir. Diğer tüm yönetim grupları ve abonelikler hiyerarşide en üstte yer alan bu kök yönetim grubunun altındadır. Kök yönetim grubu, genel ilkelerin ve RBAC atamalarının dizin düzeyinde uygulanmasını sağlar.

Yönetim gruplarını kullanmaya yönelik bazı en iyi yöntemler aşağıda verilmiştir:

**En iyi uygulama**: Yeni aboneliklerin, eklendikçe ilkeler ve izinler gibi idare öğeleri uygulandığından emin olun.   
**Ayrıntı**: Tüm Azure varlıkları için geçerli olan kurumsal çapta güvenlik öğeleri atamak için kök yönetim grubunu kullanın. İlkeler ve izinler, öğe örnekleridir.

**En iyi uygulama**: Her segment içinde denetim ve ilke tutarlılığı için bir nokta sağlamak üzere yönetim gruplarının en üst düzeylerini segmentleme stratejisiyle hizalayın.   
**Ayrıntı**: Kök yönetim grubu altındaki her segment için tek bir yönetim grubu oluşturun. Kök altında başka bir yönetim grubu oluşturmayın.

**En iyi uygulama**: Hem işlemleri hem de güvenliği engelleyen karışıklık oluşmasını önlemek için yönetim grubu derinliğini sınırlayın.   
**Ayrıntı**: Hiyerarşinizi, kök dahil olmak üzere üç düzeyle sınırlayın.

**En iyi uygulama**: Kök yönetim grubuyla tüm kuruluşa hangi öğelerin uygulanacağını dikkatle seçin.   
**Ayrıntı**: Kök yönetim grubu öğelerinin her kaynak genelinde uygulanması ve düşük etkileri olduğundan emin olun.

İyi adaylar şunlardır:

- Açık bir iş etkisi olan mevzuata gereksinimleri (örneğin, veri ile ilgili kısıtlamalar)
- Denetim efektinin ilkesi veya dikkatle incelenmiş RBAC izin atamaları gibi işlemler üzerinde neredeyse sıfır potansiyel olumsuz etkileri olan gereksinimler

**En iyi uygulama**: Kök yönetim grubundaki tüm kurumsal çapta değişiklikleri uygulamadan önce dikkatle planlayın ve test edin (ilke, RBAC modeli vb.).   
**Ayrıntı**: Kök yönetim grubundaki değişiklikler, Azure 'daki her kaynağı etkileyebilir. Kuruluşlar genelinde tutarlılık sağlamak için güçlü bir yol sağlarken, hatalar veya yanlış kullanımlar üretim işlemlerini olumsuz etkileyebilir. Test laboratuvarında veya üretim pilot ortamında kök yönetim grubundaki tüm değişiklikleri test edin.

## <a name="streamline-environment-creation-with-blueprints"></a>Planlar ile ortam oluşturmayı kolaylaştırma
[Azure şemaları hizmeti,](/azure/governance/blueprints/overview) bulut mimarlarının ve merkezi bilgi teknolojisi gruplarının, bir kuruluşun standartlarına, desenlerine ve gereksinimlerine uygun olan ve bunları uygulayan, yinelenebilir bir Azure kaynakları kümesi tanımlamasına olanak sağlar. Azure şemaları, geliştirme ekiplerinin yerleşik bir bileşen kümesi ve bu ortamları kurumsal uyumluluk kapsamında oluşturdukları güvenlerle hızlı bir şekilde oluşturmasını ve yeni ortamları kullanmasını mümkün kılar.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Davranıştaki beklenmedik değişiklikler için depolama hizmetlerini izleyin
Bulut ortamında barındırılan dağıtılmış bir uygulamada tanılama ve sorun giderme sorunları, geleneksel ortamlarda olduğundan daha karmaşık olabilir. Uygulamalar PaaS veya IaaS altyapısına, şirket içinde, mobil cihaza veya bu ortamların bir birleşimine dağıtılabilir. Uygulamanızın ağ trafiği ortak ve özel ağlarla geçiş gösterebilir ve uygulamanız birden çok depolama teknolojisini kullanabilir.

Uygulamanızdaki beklenmedik değişiklikler (daha yavaş yanıt süreleri gibi) için uygulamanızın kullandığı depolama hizmetlerini sürekli olarak izlemeniz gerekir. Daha ayrıntılı veriler toplamak ve bir sorunu ayrıntılı olarak çözümlemek için günlüğe kaydetme özelliğini kullanın. Hem izleme hem de günlüğe kaydetme işleminden elde ettiğiniz tanılama bilgileri, uygulamanızın karşılaştığı sorunun kök nedenini belirlemenize yardımcı olur. Daha sonra sorunu giderebilmeniz için ilgili adımları belirleyebilirsiniz.

[Azure depolama Analizi](../../storage/common/storage-analytics.md) , günlüğe kaydetme işlemini gerçekleştirir ve bir Azure depolama hesabı için ölçüm verileri sağlar. İstekleri izlemek, kullanım eğilimlerini analiz etmek ve depolama hesabınızla ilgili sorunları tanılamak için bu verileri kullanmanızı öneririz.

## <a name="prevent-detect-and-respond-to-threats"></a>Tehditleri önleyin, algılayın ve yanıtlayın
[Azure Güvenlik Merkezi](../../security-center/security-center-intro.md) , Azure kaynaklarınızın güvenliğine yönelik daha fazla görünürlük (ve denetim) sağlayarak tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve çeşitli güvenlik çözümleriyle birlikte çalışabilir.

Güvenlik Merkezi 'nin ücretsiz katmanı, yalnızca Azure kaynaklarınız için sınırlı güvenlik sunar. Standart katman bu özellikleri şirket içi ve diğer bulutlara genişletir. Güvenlik Merkezi standardı, güvenlik açıklarını bulmanıza ve düzeltmenize, kötü amaçlı etkinlikleri engellemek için erişim ve uygulama denetimleri uygulamanıza, analiz ve zekası kullanarak tehditleri algılamanıza ve saldırı altında hızla yanıt vermenize yardımcı olur. Güvenlik Merkezi Standart katmanını ilk 60 gün boyunca hiçbir ücret ödemeden deneyebilirsiniz. [Azure aboneliğinizi Güvenlik Merkezi standardına yükseltmenizi](../../security-center/security-center-get-started.md)öneririz.

Tüm Azure kaynaklarınızın güvenlik durumuna ilişkin merkezi bir görünüm almak için Güvenlik Merkezi 'ni kullanın. Bir bakışta, uygun güvenlik denetimlerinin yerinde olduğunu ve doğru şekilde yapılandırıldığını doğrulayın ve ilgilenilmesi gereken tüm kaynakları hızlıca doğrulayın.

Güvenlik Merkezi Ayrıca, kapsamlı uç nokta algılama ve yanıt (EDR) özellikleri sağlayan [Microsoft Defender Gelişmiş tehdit koruması (ATP)](../../security-center/security-center-wdatp.md)ile tümleşir. Microsoft Defender ATP tümleştirmesi ile, normalleştirimler oluşturabilirsiniz. Ayrıca güvenlik merkezi tarafından izlenen sunucu uç noktalarında gelişmiş saldırıları algılayabilir ve bunlara yanıt verebilirsiniz.

Neredeyse tüm kurumsal kuruluşların, farklı sinyal toplama cihazlarından günlük bilgilerini birleştirerek gelişmekte olan tehditleri belirlemesine yardımcı olmak için bir güvenlik bilgileri ve olay yönetimi (SıEM) sistemi vardır. Günlükler daha sonra, tüm günlük toplama ve analiz çözümlerinde kaçınılmaz olan parazitin "ilginç" olduğunu belirlemenize yardımcı olmak için bir veri analizi sistemi tarafından çözümlenir.

[Azure Sentinel](/azure/sentinel/overview) , ölçeklenebilir, bulutta yerel, güvenlik bilgileri ve olay yönetimi (SIEM) ve güvenlik Orchestration otomatik yanıtı (Soar) çözümüdür. Azure Sentinel, uyarı algılama, tehdit görünürlüğü, proaktif arama ve otomatikleştirilmiş tehdit yanıtı aracılığıyla akıllı güvenlik Analizi ve tehdit bilgileri sağlar.

Tehditleri önlemek, algılamak ve yanıtlamak için bazı en iyi yöntemler şunlardır:

**En iyi uygulama**: Bulut tabanlı SıEM kullanarak SıEM çözümünüzün hızını ve ölçeklenebilirliğini artırın.   
**Ayrıntı**: [Azure Sentinel](/azure/sentinel/overview) 'in özelliklerini ve yeteneklerini araştırın ve şirket içinde kullanmakta olduğunuz özelliklerin özellikleriyle karşılaştırın. Kuruluşunuzun SıEM gereksinimlerini karşılıyorsa Azure Sentinel 'i benimseme seçeneğini göz önünde bulundurun.

**En iyi uygulama**: Araştırmanın önceliklerini belirleyebilmeniz için en önemli güvenlik açıklarını bulun.   
**Ayrıntı**: Azure Güvenlik Merkezi 'Nde yerleşik olarak bulunan Azure ilkelerinden ve girişimlerden kaynaklanan önerileri görmek için [Azure güvenli puanınızı](../../security-center/security-center-secure-score.md) gözden geçirin. Bu öneriler, güvenlik güncelleştirmeleri, uç nokta koruması, şifreleme, güvenlik yapılandırması, eksik WAF, internet 'e bağlı VM 'Ler ve çok daha fazlası gibi popüler riskleri ele almanıza yardımcı olur.

Internet güvenliği (CIS) denetimlerinin merkezini temel alan güvenli puan, kuruluşunuzun Azure güvenliğini dış kaynaklara karşı kıyaslamanızı sağlar. Dış doğrulama, takımınızın güvenlik stratejisini doğrulamaya ve zenginleştirmenize yardımcı olur.

**En iyi uygulama**: Olası güvenlik sorunlarını bulup önceliklendirmek için makinelerin, ağların, depolama ve veri hizmetlerinin ve uygulamaların güvenlik duruşunu izleyin.  
**Ayrıntı**: Güvenlik Merkezi 'ndeki [güvenlik önerilerini](../../security-center/security-center-recommendations.md) , en yüksek öncelikli öğelerle başlayarak izleyin.

**En iyi uygulama**: Güvenlik Merkezi uyarılarını güvenlik bilgileriniz ve olay yönetimi (SıEM) çözümünüzle tümleştirin.   
**Ayrıntı**: SıEM olan kuruluşların çoğu, bir analist yanıtı gerektiren güvenlik uyarıları için merkezi bir Clearinghouse olarak kullanır. Güvenlik Merkezi tarafından üretilen işlenen olaylar, Azure Izleyici aracılığıyla kullanılabilen günlüklerden biri olan Azure etkinlik günlüğünde yayımlanır. Azure Izleyici, izleme verilerinizin herhangi birini SıEM aracında yönlendirmek için birleştirilmiş bir işlem hattı sunar. Yönergeler için bkz. [Güvenlik Merkezi 'nde güvenlik çözümlerini tümleştirme](../../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) . Azure Sentinel kullanıyorsanız bkz. [Azure Güvenlik Merkezi 'Ni bağlama](../../sentinel/connect-azure-security-center.md).

**En iyi uygulama**: Azure günlüklerini SıEM 'inizle tümleştirin.   
**Ayrıntı**: [Verileri toplamak ve dışarı aktarmak Için Azure izleyici 'yi](/azure/azure-monitor/overview#integrate-and-export-data)kullanın. Bu uygulama, güvenlik olay araştırmasını etkinleştirmek için önemlidir ve çevrimiçi günlük tutma sınırlı olur. Azure Sentinel kullanıyorsanız, bkz. [veri kaynaklarını bağlama](../../sentinel/connect-data-sources.md).

**En iyi uygulama**: Uç nokta algılama ve yanıt (EDR) yeteneklerini saldırı araştırmanıza tümleştirerek, araştırma ve işleme işlemlerinizi hızlandırın ve hatalı pozitif sonuçları azaltabilirsiniz.   
**Ayrıntı**: Güvenlik Merkezi güvenlik ilkeniz aracılığıyla [Microsoft Defender ATP tümleştirmesini etkinleştirin](../../security-center/security-center-wdatp.md#enable-microsoft-defender-atp-integration) . Tehdit ve olay yanıtı için Azure Sentinel kullanmayı göz önünde bulundurun.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Uçtan uca senaryo tabanlı ağ izlemeyi izleme
Müşteriler, ağ kaynaklarını bir sanal ağ, ExpressRoute, Application Gateway ve yük dengeleyiciler gibi birleştirerek Azure 'da uçtan uca bir ağ oluşturur. İzleme, ağ kaynaklarının her birinde kullanılabilir.

[Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) , bölgesel bir hizmettir. Bir ağ senaryosu düzeyinde, Azure 'dan ve Azure 'dan koşulları izlemek ve tanılamak için tanılama ve görselleştirme araçlarını kullanın.

Ağ izleme ve kullanılabilir araçlar için en iyi yöntemler aşağıda verilmiştir.

**En iyi uygulama**: Paket yakalama ile uzak ağ izlemeyi otomatikleştirin.  
**Ayrıntı**: Ağ Izleyicisi 'ni kullanarak sanal makinelerinize oturum açmadan ağ sorunlarını izleyin ve tanılayın. Uyarı ayarlayarak ve paket düzeyinde gerçek zamanlı performans bilgilerine erişim elde ederek [paket yakalamayı](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) tetikleyin. Bir sorunla karşılaştığınızda, daha iyi tanılama için ayrıntılı olarak inceleme yapabilirsiniz.

**En iyi uygulama**: Akış günlüklerini kullanarak ağ trafiğinize ilişkin Öngörüler elde edin.  
**Ayrıntı**: Ağ trafiği desenlerinizi [ağ güvenlik grubu akış günlüklerini](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)kullanarak daha ayrıntılı bir şekilde geliştirin. Akış günlüklerindeki bilgiler, ağ güvenlik profilinizi uyumluluk, denetleme ve izleme için veri toplamanıza yardımcı olur.

**En iyi uygulama**: VPN bağlantı sorunlarını tanılayın.  
**Ayrıntı**: [En yaygın VPN Gateway ve bağlantı sorunlarınızı tanılamak](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)Için Ağ İzleyicisi 'ni kullanın. Sorunu belirleyemezseniz de ayrıntılı günlükleri kullanarak daha fazla araştırma yapabilirsiniz.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Kanıtlanmış DevOps araçlarını kullanarak güvenli dağıtım
Kurumsal ve takımlarınızın üretken ve verimli olmasını sağlamak için aşağıdaki DevOps en iyi yöntemlerini kullanın.

**En iyi uygulama**: Hizmetlerin derlemesini ve dağıtımını otomatikleştirin.  
**Ayrıntı**: [Kod olarak altyapı](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) , BT uzmanlarının, modüler altyapının günlük oluşturma ve yönetimi yükünü kaldırmasını sağlayan bir teknikler ve uygulamalar kümesidir. BT uzmanlarının, yazılım geliştiricilerinin uygulama kodu oluşturup bakımını yapma gibi bir şekilde modern sunucu ortamlarını oluşturup bakımını sağlar.

Bildirim temelli bir şablon kullanarak uygulamalarınızı sağlamak için [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) kullanabilirsiniz. Tek bir şablonda birden çok hizmeti bağımlılıklarıyla birlikte dağıtabilirsiniz. Uygulama yaşam döngüsünün her aşamasında uygulamanızı tekrar tekrar dağıtmak için aynı şablonu kullanırsınız.

**En iyi uygulama**: Otomatik olarak Azure Web Apps veya Cloud Services 'ı oluşturun ve dağıtın.  
**Ayrıntı**: Azure DevOps Projeleri Azure Web Apps veya Cloud Services 'ı [otomatik olarak derlemek ve dağıtmak](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) için yapılandırabilirsiniz. Azure DevOps, her kod iadeden sonra Azure 'a bir derleme gerçekleştirdikten sonra ikilileri otomatik olarak dağıtır. Paket oluşturma işlemi, Visual Studio 'daki paket komutuna eşdeğerdir ve yayımlama adımları Visual Studio 'daki Yayımla komutuna eşdeğerdir.

**En iyi uygulama**: Yayın yönetimini otomatikleştirin.  
**Ayrıntı**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) , birden çok aşamalı dağıtımı otomatikleştirmek ve yayın sürecini yönetmek için bir çözümdür. Hızla, kolayca ve sıklıkla yayınlamak için yönetilen sürekli dağıtım işlem hatları oluşturun. Azure Pipelines, yayın işleminizi otomatikleştirebilir ve önceden tanımlanmış onay iş akışlarına sahip olabilirsiniz. Şirket içinde ve buluta dağıtın, gereken şekilde genişletin ve özelleştirin.

**En iyi uygulama**: Uygulamayı başlatıp üretim için güncelleştirmeleri dağıtmadan önce uygulamanızın performansını denetleyin.  
**Ayrıntı**: Bulut tabanlı [yük testlerini](/azure/devops/test/load-test/overview#alternatives) şu şekilde çalıştırın:

- Uygulamanızdaki performans sorunlarını bulun.
- Dağıtım kalitesini geliştirme.
- Uygulamanızın her zaman kullanılabilir olduğundan emin olun.
- Uygulamanızın bir sonraki başlatma veya pazarlama kampanyanız için trafiği işleyebilmesine dikkat edin.

[Apache JMeter](https://jmeter.apache.org/) , güçlü bir topluluk yedeklemeli, popüler, popüler bir açık kaynak aracıdır.

**En iyi uygulama**: Uygulama performansını izleyin.  
**Ayrıntı**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) , birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performans YÖNETIMI (APM) hizmetidir. Canlı Web uygulamanızı izlemek için Application Insights kullanın. Performans bozuklulıkları otomatik olarak algılar. Sorunları tanılamanıza ve hangi kullanıcıların uygulamanızla gerçekten ne yaptığını anlamanıza yardımcı olacak analiz araçları içerir. Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır.

## <a name="mitigate-and-protect-against-ddos"></a>DDoS 'a karşı azaltma ve koruma
Dağıtılmış hizmet reddi (DDoS), uygulama kaynaklarını tüketmeye çalışan bir saldırı türüdür. Amaç, uygulamanın kullanılabilirliğini ve meşru istekleri işleme yeteneğini etkiler. Bu saldırılar, boyut ve etki açısından daha karmaşık ve daha büyük hale geliyor. Bunlar, internet üzerinden genel olarak erişilebilen herhangi bir uç noktaya hedeflenebilir.

DDoS dayanıklılığı için tasarlama ve oluşturma, çeşitli başarısızlık modları için planlama ve tasarlama gerektirir. Azure 'da DDoS-dayanıklı hizmetler oluşturmak için en iyi yöntemler aşağıda verilmiştir.

**En iyi uygulama**: Tasarımın, bir uygulamanın tüm yaşam döngüsünün tamamında, tasarım ve uygulama ile dağıtım ve işlemlere kadar öncelikli olduğundan emin olun. Uygulamalar görece düşük bir istek hacminin çok fazla kaynak kullanmasına izin veren hatalara sahip olabilir ve bu da hizmet kesintisi oluşmasına neden olur.  
**Ayrıntı**: Microsoft Azure üzerinde çalışan bir hizmetin korunmasına yardımcı olmak için, uygulama mimarinizi iyi bir şekilde kavramanız ve [yazılım kalitesinin beş](https://docs.microsoft.com/azure/architecture/guide/pillars)ile ilgili olarak odaklanmanız gerekir. Tipik trafik birimlerini, uygulama ve diğer uygulamalar arasındaki bağlantı modelini ve genel İnternet 'e açık olan hizmet uç noktalarını bilmeniz gerekir.

Bir uygulamanın, uygulamanın kendisi için hedeflenen bir hizmet reddine yetecek kadar dayanıklı olmasını sağlamak, en önemli öneme sahiptir. Güvenlik ve gizlilik, [güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl)Ile başlayan Azure platformunda yerleşik olarak bulunur. SDL her geliştirme aşamasında güvenliği adresleyen ve Azure 'un sürekli olarak daha güvenli hale getirmek için güncelleştirilmesini sağlar.

**En iyi uygulama**: Uygulamalarınızı, özellikle de DDoS saldırısı durumunda, yükseltilmiş bir yükün talebini karşılayacak şekilde [ölçeklendirmek](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) üzere tasarlayın. Uygulamanız bir hizmetin tek bir örneğine bağımlıysa, tek bir hata noktası oluşturur. Birden çok örneği sağlamak, sisteminizi daha dayanıklı ve daha ölçeklenebilir hale getirir.  
**Ayrıntı**: [Azure App Service](/azure/app-service/app-service-value-prop-what-is)için, birden çok örnek sunan bir [App Service planı](../../app-service/overview-hosting-plans.md) seçin.

Azure Cloud Services için, rollerinizin her birini [birden çok örnek](../../cloud-services/cloud-services-choose-me.md)kullanacak şekilde yapılandırın.

[Azure sanal makineler](/azure/virtual-machines/windows/overview)için VM mimarinizin bırden fazla VM içerdiğinden ve her VM 'nin bir [kullanılabilirlik kümesine](/azure/virtual-machines/virtual-machines-windows-manage-availability)eklendiğinden emin olun. Otomatik ölçeklendirme özellikleri için sanal makine ölçek kümeleri kullanmanızı öneririz.

**En iyi uygulama**: Bir uygulamadaki güvenlik savunmaları katmanlama, başarılı bir saldırı olasılığını azaltır. Azure platformunun yerleşik yeteneklerini kullanarak uygulamalarınız için güvenli tasarımlar uygulayın.  
**Ayrıntı**: Saldırının riski uygulamanın boyutuyla (yüzey alanı) artar. Açık IP adresi alanını ve yük dengeleyiciler üzerinde gerekli olmayan dinleme bağlantı noktalarını ([Azure Load Balancer](/azure/load-balancer/load-balancer-get-started-internet-portal) ve [Azure Application Gateway](/azure/application-gateway/application-gateway-create-probe-portal)) kapatmak için, Beyaz listeyi kullanarak yüzey alanını azaltabilirsiniz.

[Ağ güvenlik grupları](../../virtual-network/security-overview.md) , saldırı yüzeyini azaltmak için başka bir yoldur. Uygulama yapısının doğal bir uzantısı olarak güvenlik kuralları oluşturma ve ağ güvenliğini yapılandırma karmaşıklığını en aza indirmek için [hizmet etiketlerini](../../virtual-network/security-overview.md#service-tags) ve [uygulama güvenlik gruplarını](../../virtual-network/security-overview.md#application-security-groups) kullanabilirsiniz.

Mümkün olduğunda Azure hizmetlerini bir [Sanal ağda](../../virtual-network/virtual-networks-overview.md) dağıtmanız gerekir. Bu uygulama, hizmet kaynaklarının özel IP adresleri üzerinden iletişim kurmasına izin verir. Bir sanal ağdan gelen Azure hizmet trafiği, varsayılan olarak kaynak IP adresleri olarak genel IP adreslerini kullanır.

[Hizmet uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md) , Azure hizmetine bir sanal ağdan ERIŞIRKEN kaynak IP adresleri olarak sanal ağ özel adreslerini kullanmak için hizmet trafiğine geçiş yapar.

Müşterilerin Şirket içi kaynaklarını Azure 'daki kaynaklarıyla birlikte saldırıya uğradığını görtik. Şirket içi bir ortamı Azure 'a bağlıyorsanız, şirket içi kaynakların genel İnternet 'e yönelik olma olasılığını en aza indirin.

Azure 'da ağ saldırılarına karşı koruma sağlayan iki DDoS [hizmeti teklifi](../../virtual-network/ddos-protection-overview.md) vardır:

- Temel koruma, hiçbir ek ücret ödemeden varsayılan olarak Azure ile tümleşiktir. Küresel olarak dağıtılan Azure ağının ölçeği ve kapasitesi, her zaman açık trafik izleme ve gerçek zamanlı risk azaltma aracılığıyla ortak ağ katmanı saldırılarına karşı savunma sağlar. Temel, Kullanıcı Yapılandırması veya uygulama değişikliği gerektirmez ve Azure DNS gibi PaaS hizmetleri de dahil olmak üzere tüm Azure hizmetlerini korumanıza yardımcı olur.
- Standart koruma, ağ saldırılarına karşı gelişmiş DDoS azaltma özellikleri sağlar. Belirli Azure kaynaklarınızı korumak için otomatik olarak ayarlanır. Sanal ağların oluşturulması sırasında korumanın etkinleştirilmesi basittir. Ayrıca, oluşturulduktan sonra yapılabilir ve uygulama ya da kaynak değişikliği gerektirmez.

## <a name="enable-azure-policy"></a>Azure Ilkesini etkinleştir
[Azure ilkesi](/azure/governance/policy/overview) , Azure 'da ilke oluşturmak, atamak ve yönetmek için kullandığınız bir hizmettir. Bu ilkeler kaynaklarınızın üzerindeki kuralları ve etkileri uygular, böylece bu kaynaklar kurumsal standartlarınızla ve hizmet düzeyi sözleşmelerle uyumlu kalır. Azure İlkesi, uyumsuzluk atanan ilkelerle kaynaklarınızı değerlendirerek bu gereksinimini karşılar.

Kuruluşunuzun yazılı ilkesini izleyip zorlamak için Azure Ilkesini etkinleştirin. Bu, hibrit bulut iş yüklerinizde güvenlik ilkelerini merkezi olarak yöneterek şirketiniz veya yasal güvenlik gereksinimleriyle uyumluluğu güvence altına alacak. [Uyumluluğu zorlamak için ilke oluşturma ve yönetme](../../governance/policy/tutorials/create-and-manage.md)hakkında bilgi edinin. Bir ilkenin öğelerine genel bakış için bkz. [Azure ilke tanımı yapısı](../../governance/policy/concepts/definition-structure.md) .

Azure Ilkesini benimsedikten sonra izlenecek en iyi güvenlik uygulamaları aşağıda verilmiştir:

**En iyi uygulama**: İlke çeşitli efekt türlerini destekler. [Azure ilke tanımı yapısında](../../governance/policy/concepts/definition-structure.md#policy-rule)bunlarla ilgili bilgi edinebilirsiniz. İş işlemleri, **reddetme** etkisinden olumsuz etkilenebilir ve bu **nedenle, ilkeden** olumsuz etkisi riskini sınırlamak için **Denetim** efektiyle başlayın.   
**Ayrıntı**: [İlke dağıtımlarını denetim modunda başlatın](../../governance/policy/concepts/definition-structure.md#policy-rule) ve daha sonra **reddetme** **veya düzeltme**için ilerleme durumunu yapın. **Reddet** veya **Düzelt**'e geçmeden önce denetim efektinin sonuçlarını test edin ve gözden geçirin.

Daha fazla bilgi için bkz. [uyumluluğu zorlamak için Ilke oluşturma ve yönetme](../../governance/policy/tutorials/create-and-manage.md).

**En iyi uygulama**: İlke ihlallerinin izlenmesinden sorumlu rolleri ve doğru düzeltme eyleminin hızla alındığının belirlenmesi.   
**Ayrıntı**: Atanan rol izleyicisine [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) veya [komut satırı](../../governance/policy/how-to/get-compliance-data.md#command-line)aracılığıyla uyumluluğunu sağlayabilirsiniz.

**En iyi uygulama**: Azure Ilkesi, kuruluşun yazılı ilkelerinin teknik bir gösterimidir. Karışıklığı azaltmak ve tutarlılığı artırmak için tüm Azure ilkelerini kuruluş ilkelerine eşleyin.   
**Ayrıntı**: Azure [ilke açıklamasında](../../governance/policy/concepts/definition-structure.md#display-name-and-description) veya Azure Policy [Initiative](../../governance/policy/concepts/definition-structure.md#initiatives) açıklamasında kuruluş ilkesine bir başvuru ekleyerek kuruluşunuzun belgelerindeki veya Azure ilkesinin içinde belge eşlemesi.

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD risk raporlarını izleme
Saldırganlar bir kullanıcının kimliğini çalarak bir ortama erişim kazanıyorsa güvenlik ihlallerinin büyük çoğunluğu bu şekilde gerçekleşir. Güvenliği aşılmış kimlikleri bulma işlemi kolay bir görev değildir. Azure AD, kullanıcı hesaplarınızla ilgili şüpheli eylemleri algılamak için uyarlamalı makine öğrenimi algoritmaları ve buluşsal yöntemler kullanır. Algılanan her şüpheli eylem, [risk algılama](../../active-directory/reports-monitoring/concept-risk-events.md)adlı bir kayıtta saklanır. Risk algılamaları Azure AD güvenlik raporlarında kaydedilir. Daha fazla bilgi için [risk altındaki kullanıcılar güvenlik raporu](../../active-directory/reports-monitoring/concept-user-at-risk.md) ve [riskli oturum açma güvenlik raporu](../../active-directory/reports-monitoring/concept-risky-sign-ins.md)hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .

Aşağıdaki kaynaklar, Azure güvenliği ve ilgili Microsoft hizmetleri hakkında daha genel bilgiler sağlamak için kullanılabilir:
* Azure [güvenlik ekibi blogu](https://blogs.msdn.microsoft.com/azuresecurity/) -Azure güvenliği ile ilgili en son bilgiler için
* [Microsoft Güvenlik](https://technet.microsoft.com/library/dn440717.aspx) açıkları, Azure ile ilgili sorunlar da dahil olmak üzere Microsoft güvenlik açıklarına göre bildirilebilir veya e-posta ilesecure@microsoft.com
