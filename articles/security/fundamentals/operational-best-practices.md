---
title: Azure varlıklarınız için en iyi güvenlik uygulamaları
titleSuffix: Azure security
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
ms.openlocfilehash: 6634a536828b3c19d771d135fdb3a1224d3dfdf3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717432"
---
# <a name="azure-operational-security-best-practices"></a>Azure operasyonel güvenlik en iyi uygulamaları
Bu makalede, Azure 'daki verilerinizi, uygulamalarınızı ve diğer varlıkları korumaya yönelik işlemsel en iyi uygulamalar sağlanır.

En iyi uygulamalar, görüşlerden oluşan yarışmaları temel alır ve geçerli Azure platformu özellikleri ve özellik kümeleriyle çalışır. Zamanla ve teknolojiler zaman içinde değişir ve bu makale, bu değişiklikleri yansıtacak şekilde düzenli olarak güncelleştirilir.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Güçlü işlemsel güvenlik uygulamalarını tanımlama ve dağıtma
Azure operasyonel güvenlik, Azure 'daki verilerini, uygulamalarını ve diğer varlıklarını korumak için kullanıcılara sunulan hizmetleri, denetimleri ve özellikleri ifade eder. Azure operasyonel güvenlik, [güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl), [Microsoft Güvenlik Yanıt Merkezi](https://www.microsoft.com/msrc?rtc=1) programı ve siber güvenlik tehdidi yatay 'ın derinlemesine farkında olmak üzere Microsoft 'a özgü yetenekler aracılığıyla elde edilen bilgileri içeren bir çerçeve üzerine kurulmuştur.

## <a name="manage-and-monitor-user-passwords"></a>Kullanıcı parolalarını yönetme ve izleme
Aşağıdaki tabloda kullanıcı parolalarının yönetimiyle ilgili bazı en iyi uygulamalar listelenmektedir:

**En iyi yöntem**: bulutta uygun parola koruma düzeyine sahip olduğunuzdan emin olun.   
**Ayrıntı**: Microsoft kimlik platformları (Azure Active Directory, Active Directory ve Microsoft hesabı) kapsamındaki [Microsoft parola Kılavuzu](https://www.microsoft.com/research/publication/password-guidance/)'ndaki yönergeleri izleyin.

**En iyi yöntem**: Kullanıcı hesaplarınızla ilgili şüpheli eylemleri izleyin.   
**Ayrıntı**: Azure AD güvenlik raporlarını kullanarak riskli ve riskli [oturum açma](../../active-directory/identity-protection/overview-identity-protection.md) işlemlerini takip eden [kullanıcılara](../../active-directory/identity-protection/overview-identity-protection.md) yönelik izleyici.

**En iyi yöntem**: yüksek riskli parolaları otomatik olarak algıla ve düzelt.   
**Ayrıntı**: [Azure AD kimlik koruması](../../active-directory/identity-protection/overview-identity-protection.md) Azure AD Premium P2 sürümünün şunları yapmanızı sağlayan bir özelliğidir:

- Kuruluşunuzun kimliklerini etkileyen olası güvenlik açıklarını algılama
- Otomatik yanıtları, kuruluşunuzun kimlikleri ile ilgili şüpheli, algılanan şüpheli eylemler için yapılandırın
- Şüpheli olayları araştırın ve bunları çözmek için gerekli işlemleri yapın

## <a name="receive-incident-notifications-from-microsoft"></a>Microsoft 'tan olay bildirimleri al
Güvenlik işlemleri takımınızın Microsoft 'un Azure olay bildirimlerini aldığından emin olun. Bir olay bildirimi, güvenlik takımınızın, olası güvenlik risklerini hızlı bir şekilde yanıtlaması ve düzeltebilmeleri için Azure kaynaklarınızın güvenliğinin aşıldığını bilmesini sağlar.

Azure kayıt portalında, yönetici iletişim bilgilerinin güvenlik işlemlerine bildirimde bulunan ayrıntıları içerdiğinden emin olabilirsiniz. İletişim bilgileri, bir e-posta adresi ve telefon numarasıdır.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure aboneliklerini yönetim gruplarında düzenleme
Kuruluşunuzun çok sayıda aboneliği varsa, söz konusu aboneliklerde erişimi, ilkeleri ve uyumluluğu yönetmek için verimli bir yönteme ihtiyacınız olabilir. [Azure Yönetim grupları](../../governance/management-groups/create-management-group-portal.md) , aboneliklerdeki bir kapsam düzeyi sağlar. Abonelikleri yönetim grubu adlı kapsayıcılarda düzenler ve idare koşullarınızı yönetim gruplarına uygularsınız. Bir yönetim grubu içindeki aboneliklerin tümü otomatik olarak yönetim grubuna uygulanmış olan koşulları devralır.

Bir dizinde yönetim gruplarının ve aboneliklerinin esnek bir yapısını oluşturabilirsiniz. Her dizine kök yönetim grubu adlı tek bir en üst düzey yönetim grubu verilir. Diğer tüm yönetim grupları ve abonelikler hiyerarşide en üstte yer alan bu kök yönetim grubunun altındadır. Kök yönetim grubu, genel ilkelerin ve Azure rol atamalarının dizin düzeyinde uygulanmasını sağlar.

Yönetim gruplarını kullanmaya yönelik bazı en iyi yöntemler aşağıda verilmiştir:

**En iyi uygulama**: yeni aboneliklerin, eklendikçe ilkeler ve izinler gibi idare öğeleri uygulandığından emin olun.   
**Ayrıntı**: tüm Azure varlıkları için geçerli olan kurumsal çapta güvenlik öğeleri atamak için kök yönetim grubunu kullanın. İlkeler ve izinler, öğe örnekleridir.

**En iyi yöntem**: Her kesimde denetim ve ilke tutarlılığı için bir nokta sağlamak üzere, segment stratejisi olan yönetim gruplarının en üst düzeylerini hizalayın.   
**Ayrıntı**: kök yönetim grubu altındaki her segment için tek bir yönetim grubu oluşturun. Kök altında başka bir yönetim grubu oluşturmayın.

**En iyi yöntem**: hem işlemleri hem de güvenliği engelleyen karışıklık oluşmasını önlemek için yönetim grubu derinliğini sınırlayın.   
**Ayrıntı**: hiyerarşinizi, kök dahil olmak üzere üç düzeyle sınırlayın.

**En iyi yöntem**: kök yönetim grubuyla tüm kuruluşa hangi öğelerin uygulanacağını dikkatle seçin.   
**Ayrıntı**: kök yönetim grubu öğelerinin her kaynak genelinde uygulanması ve düşük etkileri olduğundan emin olun.

İyi adaylar şunlardır:

- Açık bir iş etkisi olan mevzuata gereksinimleri (örneğin, veri ile ilgili kısıtlamalar)
- Denetim efektli ilke veya dikkatle incelenmiş Azure RBAC izin atamaları gibi işlemler üzerinde neredeyse sıfır olası olumsuz etkileri olan gereksinimler

**En iyi yöntem**: kök yönetim grubundaki tüm kurumsal çapta değişiklikleri uygulamadan önce dikkatle planlayın ve test edin (Ilke, Azure RBAC modeli vb.).   
**Ayrıntı**: kök yönetim grubundaki değişiklikler, Azure 'daki her kaynağı etkileyebilir. Kuruluşlar genelinde tutarlılık sağlamak için güçlü bir yol sağlarken, hatalar veya yanlış kullanımlar üretim işlemlerini olumsuz etkileyebilir. Test laboratuvarında veya üretim pilot ortamında kök yönetim grubundaki tüm değişiklikleri test edin.

## <a name="streamline-environment-creation-with-blueprints"></a>Planlar ile ortam oluşturmayı kolaylaştırma
[Azure şemaları hizmeti,](../../governance/blueprints/overview.md) bulut mimarlarının ve merkezi bilgi teknolojisi gruplarının, bir kuruluşun standartlarına, desenlerine ve gereksinimlerine uygun olan ve bunları uygulayan, yinelenebilir bir Azure kaynakları kümesi tanımlamasına olanak sağlar. Azure şemaları, geliştirme ekiplerinin yerleşik bir bileşen kümesi ve bu ortamları kurumsal uyumluluk kapsamında oluşturdukları güvenlerle hızlı bir şekilde oluşturmasını ve yeni ortamları kullanmasını mümkün kılar.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Davranıştaki beklenmedik değişiklikler için depolama hizmetlerini izleyin
Bulut ortamında barındırılan dağıtılmış bir uygulamada tanılama ve sorun giderme sorunları, geleneksel ortamlarda olduğundan daha karmaşık olabilir. Uygulamalar PaaS veya IaaS altyapısına, şirket içinde, mobil cihaza veya bu ortamların bir birleşimine dağıtılabilir. Uygulamanızın ağ trafiği ortak ve özel ağlarla geçiş gösterebilir ve uygulamanız birden çok depolama teknolojisini kullanabilir.

Uygulamanızdaki beklenmedik değişiklikler (daha yavaş yanıt süreleri gibi) için uygulamanızın kullandığı depolama hizmetlerini sürekli olarak izlemeniz gerekir. Daha ayrıntılı veriler toplamak ve bir sorunu ayrıntılı olarak çözümlemek için günlüğe kaydetme özelliğini kullanın. Hem izleme hem de günlüğe kaydetme işleminden elde ettiğiniz tanılama bilgileri, uygulamanızın karşılaştığı sorunun kök nedenini belirlemenize yardımcı olur. Daha sonra sorunu giderebilmeniz için ilgili adımları belirleyebilirsiniz.

[Azure depolama Analizi](../../storage/common/storage-analytics.md) , günlüğe kaydetme işlemini gerçekleştirir ve bir Azure depolama hesabı için ölçüm verileri sağlar. İstekleri izlemek, kullanım eğilimlerini analiz etmek ve depolama hesabınızla ilgili sorunları tanılamak için bu verileri kullanmanızı öneririz.

## <a name="prevent-detect-and-respond-to-threats"></a>Tehditleri önleyin, algılayın ve yanıtlayın
[Azure Güvenlik Merkezi](../../security-center/security-center-introduction.md) , Azure kaynaklarınızın güvenliğine yönelik daha fazla görünürlük (ve denetim) sağlayarak tehditleri önlemenize, algılamanıza ve yanıtlamanıza yardımcı olur. Azure aboneliklerinizde tümleşik güvenlik izleme ve ilke yönetimi sağlar, aksi takdirde fark edilmemiş tehditleri algılamaya yardımcı olur ve çeşitli güvenlik çözümleriyle birlikte çalışabilir.

Güvenlik Merkezi 'nin ücretsiz katmanı, yalnızca Azure kaynaklarınız için sınırlı güvenlik sunar. Standart katman bu özellikleri şirket içi ve diğer bulutlara genişletir. Güvenlik Merkezi standardı, güvenlik açıklarını bulmanıza ve düzeltmenize, kötü amaçlı etkinlikleri engellemek için erişim ve uygulama denetimleri uygulamanıza, analiz ve zekası kullanarak tehditleri algılamanıza ve saldırı altında hızla yanıt vermenize yardımcı olur. Güvenlik Merkezi Standart katmanını ilk 60 gün boyunca hiçbir ücret ödemeden deneyebilirsiniz. [Azure aboneliğinizi Güvenlik Merkezi standardına yükseltmenizi](../../security-center/security-center-get-started.md)öneririz.

Tüm Azure kaynaklarınızın güvenlik durumuna ilişkin merkezi bir görünüm almak için Güvenlik Merkezi 'ni kullanın. Bir bakışta, uygun güvenlik denetimlerinin yerinde olduğunu ve doğru şekilde yapılandırıldığını doğrulayın ve ilgilenilmesi gereken tüm kaynakları hızlıca doğrulayın.

Güvenlik Merkezi Ayrıca, kapsamlı uç nokta algılama ve yanıt (EDR) özellikleri sağlayan [Microsoft Defender Gelişmiş tehdit koruması (ATP)](../../security-center/security-center-wdatp.md)ile tümleşir. Microsoft Defender ATP tümleştirmesi ile, normalleştirimler oluşturabilirsiniz. Ayrıca güvenlik merkezi tarafından izlenen sunucu uç noktalarında gelişmiş saldırıları algılayabilir ve bunlara yanıt verebilirsiniz.

Neredeyse tüm kurumsal kuruluşların, farklı sinyal toplama cihazlarından günlük bilgilerini birleştirerek gelişmekte olan tehditleri belirlemesine yardımcı olmak için bir güvenlik bilgileri ve olay yönetimi (SıEM) sistemi vardır. Günlükler daha sonra, tüm günlük toplama ve analiz çözümlerinde kaçınılmaz olan parazitin "ilginç" olduğunu belirlemenize yardımcı olmak için bir veri analizi sistemi tarafından çözümlenir.

[Azure Sentinel](../../sentinel/overview.md) , ölçeklenebilir, bulutta yerel, güvenlik bilgileri ve olay yönetimi (SIEM) ve güvenlik Orchestration otomatik yanıtı (Soar) çözümüdür. Azure Sentinel, uyarı algılama, tehdit görünürlüğü, proaktif arama ve otomatikleştirilmiş tehdit yanıtı aracılığıyla akıllı güvenlik Analizi ve tehdit bilgileri sağlar.

Tehditleri önlemek, algılamak ve yanıtlamak için bazı en iyi yöntemler şunlardır:

**En iyi yöntem**: bulut tabanlı SIEM kullanarak SIEM çözümünüzün hızını ve ölçeklenebilirliğini artırın.   
**Ayrıntı**: [Azure Sentinel](../../sentinel/overview.md) 'in özelliklerini ve yeteneklerini araştırın ve şirket içinde kullanmakta olduğunuz özelliklerin özellikleriyle karşılaştırın. Kuruluşunuzun SıEM gereksinimlerini karşılıyorsa Azure Sentinel 'i benimseme seçeneğini göz önünde bulundurun.

**En iyi yöntem: araştırmanın** önceliklerini belirleyebilmeniz için en ciddi güvenlik açıklarını bulun.   
**Ayrıntı**: Azure Güvenlik Merkezi 'nde yerleşik olarak bulunan Azure ilkelerinden ve girişimlerden kaynaklanan önerileri görmek için [Azure güvenli puanınızı](../../security-center/secure-score-security-controls.md) gözden geçirin. Bu öneriler, güvenlik güncelleştirmeleri, uç nokta koruması, şifreleme, güvenlik yapılandırması, eksik WAF, internet 'e bağlı VM 'Ler ve çok daha fazlası gibi popüler riskleri ele almanıza yardımcı olur.

Internet güvenliği (CIS) denetimlerinin merkezini temel alan güvenli puan, kuruluşunuzun Azure güvenliğini dış kaynaklara karşı kıyaslamanızı sağlar. Dış doğrulama, takımınızın güvenlik stratejisini doğrulamaya ve zenginleştirmenize yardımcı olur.

**En iyi yöntem**: makinelerin, ağların, depolama ve veri hizmetlerinin ve uygulamaların güvenlik duruşunu, olası güvenlik sorunlarını bulma ve önceliklendirmesini izleyin.  
**Ayrıntı**: Güvenlik Merkezi 'ndeki [güvenlik önerilerini](../../security-center/security-center-recommendations.md) , en yüksek öncelikli öğelerle başlayarak izleyin.

**En iyi yöntem**: Güvenlik Merkezi uyarılarını güvenlik bilgileriniz ve olay yönetimi (SIEM) çözümünüz ile tümleştirin.   
**Ayrıntı**: BIR SIEM olan kuruluşların çoğu, bir analist yanıtı gerektiren güvenlik uyarıları için merkezi bir Clearinghouse olarak kullanır. Güvenlik Merkezi tarafından üretilen işlenen olaylar, Azure Izleyici aracılığıyla kullanılabilen günlüklerden biri olan Azure etkinlik günlüğünde yayımlanır. Azure Izleyici, izleme verilerinizin herhangi birini SıEM aracında yönlendirmek için birleştirilmiş bir işlem hattı sunar. Yönergeler için bkz. [BIR SıEM, SOAR veya BT hizmet yönetimi çözümüne akış uyarıları](../../security-center/export-to-siem.md) . Azure Sentinel kullanıyorsanız bkz. [Azure Güvenlik Merkezi 'Ni bağlama](../../sentinel/connect-azure-security-center.md).

**En iyi yöntem**: Azure günlüklerini SIEM 'inizle tümleştirin.   
**Ayrıntı**: [Azure izleyici 'yi kullanarak veri toplayın ve dışarı aktarın](../../azure-monitor/overview.md#integrate-and-export-data). Bu uygulama, güvenlik olay araştırmasını etkinleştirmek için önemlidir ve çevrimiçi günlük tutma sınırlı olur. Azure Sentinel kullanıyorsanız, bkz. [veri kaynaklarını bağlama](../../sentinel/connect-data-sources.md).

**En iyi yöntem**: uç nokta algılama ve yanıt (EDR) yeteneklerini saldırı araştırmanıza tümleştirerek, araştırma ve işleme işlemlerinizi hızlandırın ve hatalı pozitif sonuçları azaltabilirsiniz.   
**Ayrıntı**: Güvenlik Merkezi güvenlik Ilkeniz aracılığıyla [uç nokta tümleştirmesi Için Microsoft Defender 'ı etkinleştirin](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) . Tehdit ve olay yanıtı için Azure Sentinel kullanmayı göz önünde bulundurun.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Uçtan uca senaryo tabanlı ağ izlemeyi izleme
Müşteriler, ağ kaynaklarını bir sanal ağ, ExpressRoute, Application Gateway ve yük dengeleyiciler gibi birleştirerek Azure 'da uçtan uca bir ağ oluşturur. İzleme, ağ kaynaklarının her birinde kullanılabilir.

[Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) , bölgesel bir hizmettir. Bir ağ senaryosu düzeyinde, Azure 'dan ve Azure 'dan koşulları izlemek ve tanılamak için tanılama ve görselleştirme araçlarını kullanın.

Ağ izleme ve kullanılabilir araçlar için en iyi yöntemler aşağıda verilmiştir.

**En iyi yöntem**: paket yakalama ile uzak ağ izlemeyi otomatikleştirin.  
**Ayrıntı**: Ağ İzleyicisi 'Ni kullanarak sanal makinelerinize oturum açmadan ağ sorunlarını izleyin ve tanılayın. Uyarı ayarlayarak ve paket düzeyinde gerçek zamanlı performans bilgilerine erişim elde ederek [paket yakalamayı](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) tetikleyin. Bir sorun gördüğünüzde daha iyi tanılar için ayrıntılı araştırma yapabilirsiniz.

**En iyi yöntem**: akış günlüklerini kullanarak ağ trafiğinize ilişkin Öngörüler elde edin.  
**Ayrıntı**: ağ [güvenlik grubu akış günlüklerini](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)kullanarak ağ trafiği desenlerinizi daha ayrıntılı bir şekilde geliştirin. Akış günlüklerindeki bilgiler, ağ güvenlik profilinizi uyumluluk, denetleme ve izleme için veri toplamanıza yardımcı olur.

**En iyi yöntem**: VPN bağlantı sorunlarını tanılayın.  
**Ayrıntı**: Ağ İzleyicisi 'ni kullanarak [en yaygın VPN Gateway ve bağlantı sorunlarınızı tanılayın](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Sorunu belirleyemezseniz de ayrıntılı günlükleri kullanarak daha fazla araştırma yapabilirsiniz.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Kanıtlanmış DevOps araçlarını kullanarak güvenli dağıtım
Kurumsal ve takımlarınızın üretken ve verimli olmasını sağlamak için aşağıdaki DevOps en iyi yöntemlerini kullanın.

**En iyi yöntem**: hizmetlerin derlemesini ve dağıtımını otomatikleştirin.  
**Ayrıntı**: [kod olarak altyapı](/azure/devops/learn/what-is-infrastructure-as-code) , BT uzmanlarının, modüler altyapının günlük oluşturma ve yönetimi yükünü kaldırmasına yardımcı olan bir teknikler ve uygulamalar kümesidir. BT uzmanlarının, yazılım geliştiricilerinin uygulama kodu oluşturup bakımını yapma gibi bir şekilde modern sunucu ortamlarını oluşturup bakımını sağlar.

Bildirim temelli bir şablon kullanarak uygulamalarınızı sağlamak için [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) kullanabilirsiniz. Tek bir şablonda birden çok hizmeti bağımlılıklarıyla birlikte dağıtabilirsiniz. Uygulama yaşam döngüsünün her aşamasında uygulamanızı tekrar tekrar dağıtmak için aynı şablonu kullanırsınız.

**En iyi yöntem**: otomatik olarak Azure Web Apps veya bulut hizmetleri oluşturun ve dağıtın.  
**Ayrıntı**: Azure DevOps Projeleri, otomatik olarak Azure Web Apps veya bulut Hizmetleri  [oluşturmak ve dağıtmak](/azure/devops/pipelines/index) için yapılandırabilirsiniz. Azure DevOps, her kod iadeden sonra Azure 'a bir derleme gerçekleştirdikten sonra ikilileri otomatik olarak dağıtır. Paket oluşturma işlemi, Visual Studio 'daki paket komutuna eşdeğerdir ve yayımlama adımları Visual Studio 'daki Yayımla komutuna eşdeğerdir.

**En iyi yöntem**: Release Management 'ı otomatikleştirme.  
**Ayrıntı**: [Azure Pipelines](/azure/devops/pipelines/index) , birden çok aşamalı dağıtımı otomatikleştirmek ve yayın sürecini yönetmek için bir çözümdür. Hızla, kolayca ve sıklıkla yayınlamak için yönetilen sürekli dağıtım işlem hatları oluşturun. Azure Pipelines, yayın işleminizi otomatikleştirebilir ve önceden tanımlanmış onay iş akışlarına sahip olabilirsiniz. Şirket içinde ve buluta dağıtın, gereken şekilde genişletin ve özelleştirin.

**En iyi yöntem**: uygulamayı çalıştırmadan veya üretime güncelleştirme dağıtmadan önce uygulamanızın performansını denetleyin.  
**Ayrıntı**: bulut tabanlı [yük testlerini](/azure/devops/test/load-test/overview#alternatives) şu şekilde çalıştırın:

- Uygulamanızdaki performans sorunlarını bulun.
- Dağıtım kalitesini geliştirme.
- Uygulamanızın her zaman kullanılabilir olduğundan emin olun.
- Uygulamanızın bir sonraki başlatma veya pazarlama kampanyanız için trafiği işleyebilmesine dikkat edin.

[Apache JMeter](https://jmeter.apache.org/) , güçlü bir topluluk yedeklemeli, popüler, popüler bir açık kaynak aracıdır.

**En iyi yöntem**: uygulama performansını izleyin.  
**Ayrıntı**: [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) , birden çok platformda Web geliştiricileri için genişletilebilir bir uygulama performans yönetimi (APM) hizmetidir. Canlı Web uygulamanızı izlemek için Application Insights kullanın. Performans bozuklulıkları otomatik olarak algılar. Sorunları tanılamanıza ve hangi kullanıcıların uygulamanızla gerçekten ne yaptığını anlamanıza yardımcı olacak analiz araçları içerir. Performansı ve kullanılabilirliği sürekli geliştirmenize yardımcı olmak amacıyla tasarlanmıştır.

## <a name="mitigate-and-protect-against-ddos"></a>DDoS 'a karşı azaltma ve koruma
Dağıtılmış hizmet reddi (DDoS), uygulama kaynaklarını tüketmeye çalışan bir saldırı türüdür. Amaç, uygulamanın kullanılabilirliğini ve meşru istekleri işleme yeteneğini etkiler. Bu saldırılar, boyut ve etki açısından daha karmaşık ve daha büyük hale geliyor. Bunlar, internet üzerinden genel olarak erişilebilen herhangi bir uç noktaya hedeflenebilir.

DDoS dayanıklılığı için tasarlama ve oluşturma, çeşitli başarısızlık modları için planlama ve tasarlama gerektirir. Azure 'da DDoS-dayanıklı hizmetler oluşturmak için en iyi yöntemler aşağıda verilmiştir.

**En iyi yöntem**: bir uygulamanın, tasarım ve uygulama açısından dağıtım ve işlemlere kadar tüm yaşam döngüsünün bir öncelik olduğundan emin olun. Uygulamalar görece düşük bir istek hacminin çok fazla kaynak kullanmasına izin veren hatalara sahip olabilir ve bu da hizmet kesintisi oluşmasına neden olur.  
**Ayrıntı**: Microsoft Azure üzerinde çalışan bir hizmetin korunmasına yardımcı olmak için, uygulama mimarinizi iyi bir şekilde kavramanız ve [yazılım kalitesinin beş](/azure/architecture/guide/pillars)bir yanındaki ile odaklanmanız gerekir. Tipik trafik birimlerini, uygulama ve diğer uygulamalar arasındaki bağlantı modelini ve genel İnternet 'e açık olan hizmet uç noktalarını bilmeniz gerekir.

Bir uygulamanın, uygulamanın kendisi için hedeflenen bir hizmet reddine yetecek kadar dayanıklı olmasını sağlamak, en önemli öneme sahiptir. Güvenlik ve gizlilik, [güvenlik geliştirme yaşam döngüsü (SDL)](https://www.microsoft.com/sdl)Ile başlayan Azure platformunda yerleşik olarak bulunur. SDL her geliştirme aşamasında güvenliği adresleyen ve Azure 'un sürekli olarak daha güvenli hale getirmek için güncelleştirilmesini sağlar.

**En iyi yöntem**: uygulamanızı, yükseltilmiş bir yükün taleplerini karşılamak üzere [yatay olarak ölçeklendirmek](/azure/architecture/guide/design-principles/scale-out) üzere tasarlayın, özellikle de DDoS saldırısı durumunda. Uygulamanız bir hizmetin tek bir örneğine bağımlıysa, tek bir hata noktası oluşturur. Birden çok örneği sağlamak, sisteminizi daha dayanıklı ve daha ölçeklenebilir hale getirir.  
**Ayrıntı**: [Azure App Service](../../app-service/overview.md)için, birden çok örnek sunan bir [App Service planı](../../app-service/overview-hosting-plans.md) seçin.

Azure Cloud Services için, rollerinizin her birini [birden çok örnek](../../cloud-services/cloud-services-choose-me.md)kullanacak şekilde yapılandırın.

[Azure sanal makineler](../../virtual-machines/windows/overview.md)için VM mimarinizin bırden fazla VM içerdiğinden ve her VM 'nin bir [kullanılabilirlik kümesine](../../virtual-machines/windows/tutorial-availability-sets.md)eklendiğinden emin olun. Otomatik ölçeklendirme özellikleri için sanal makine ölçek kümeleri kullanmanızı öneririz.

**En iyi yöntem**: bir uygulamadaki güvenlik savunmaları katmanlama, başarılı bir saldırının olasılığını azaltır. Azure platformunun yerleşik yeteneklerini kullanarak uygulamalarınız için güvenli tasarımlar uygulayın.  
**Ayrıntı**: saldırının riski uygulamanın boyutuyla (yüzey alanı) artar. Açık IP adresi alanını ve yük dengeleyiciler üzerinde gerekli olmayan dinleme bağlantı noktalarını ([Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) ve [Azure Application Gateway](../../application-gateway/application-gateway-create-probe-portal.md)) kapatmak için bir onay listesi kullanarak yüzey alanını azaltabilirsiniz.

[Ağ güvenlik grupları](../../virtual-network/network-security-groups-overview.md) , saldırı yüzeyini azaltmak için başka bir yoldur. Uygulama yapısının doğal bir uzantısı olarak güvenlik kuralları oluşturma ve ağ güvenliğini yapılandırma karmaşıklığını en aza indirmek için [hizmet etiketlerini](../../virtual-network/network-security-groups-overview.md#service-tags) ve [uygulama güvenlik gruplarını](../../virtual-network/network-security-groups-overview.md#application-security-groups) kullanabilirsiniz.

Mümkün olduğunda Azure hizmetlerini bir [Sanal ağda](../../virtual-network/virtual-networks-overview.md) dağıtmanız gerekir. Bu uygulama, hizmet kaynaklarının özel IP adresleri üzerinden iletişim kurmasına izin verir. Bir sanal ağdan gelen Azure hizmet trafiği, varsayılan olarak kaynak IP adresleri olarak genel IP adreslerini kullanır.

[Hizmet uç noktaları](../../virtual-network/virtual-network-service-endpoints-overview.md) , Azure hizmetine bir sanal ağdan ERIŞIRKEN kaynak IP adresleri olarak sanal ağ özel adreslerini kullanmak için hizmet trafiğine geçiş yapar.

Müşterilerin Şirket içi kaynaklarını Azure 'daki kaynaklarıyla birlikte saldırıya uğradığını görtik. Şirket içi bir ortamı Azure 'a bağlıyorsanız, şirket içi kaynakların genel İnternet 'e yönelik olma olasılığını en aza indirin.

Azure 'da ağ saldırılarına karşı koruma sağlayan iki DDoS [hizmeti teklifi](../../ddos-protection/ddos-protection-overview.md) vardır:

- Temel koruma, hiçbir ek ücret ödemeden varsayılan olarak Azure ile tümleşiktir. Küresel olarak dağıtılan Azure ağının ölçeği ve kapasitesi, her zaman açık trafik izleme ve gerçek zamanlı risk azaltma aracılığıyla ortak ağ katmanı saldırılarına karşı savunma sağlar. Temel, Kullanıcı Yapılandırması veya uygulama değişikliği gerektirmez ve Azure DNS gibi PaaS hizmetleri de dahil olmak üzere tüm Azure hizmetlerini korumanıza yardımcı olur.
- Standart koruma, ağ saldırılarına karşı gelişmiş DDoS azaltma özellikleri sağlar. Belirli Azure kaynaklarınızı korumak için otomatik olarak ayarlanır. Sanal ağların oluşturulması sırasında korumanın etkinleştirilmesi basittir. Ayrıca, oluşturulduktan sonra yapılabilir ve uygulama ya da kaynak değişikliği gerektirmez.

## <a name="enable-azure-policy"></a>Azure Ilkesini etkinleştir
[Azure ilkesi](../../governance/policy/overview.md) , Azure 'da ilke oluşturmak, atamak ve yönetmek için kullandığınız bir hizmettir. Bu ilkeler kaynaklarınızın üzerindeki kuralları ve etkileri uygular, böylece bu kaynaklar kurumsal standartlarınızla ve hizmet düzeyi sözleşmelerle uyumlu kalır. Azure İlkesi, kaynaklarınızın atanan ilkelerle uyumlu olup olmadığını değerlendirerek bu ihtiyacı karşılar.

Kuruluşunuzun yazılı ilkesini izleyip zorlamak için Azure Ilkesini etkinleştirin. Bu, hibrit bulut iş yüklerinizde güvenlik ilkelerini merkezi olarak yöneterek şirketiniz veya yasal güvenlik gereksinimleriyle uyumluluğu güvence altına alacak. [Uyumluluğu zorlamak için ilke oluşturma ve yönetme](../../governance/policy/tutorials/create-and-manage.md)hakkında bilgi edinin. Bir ilkenin öğelerine genel bakış için bkz. [Azure ilke tanımı yapısı](../../governance/policy/concepts/definition-structure.md) .

Azure Ilkesini benimsedikten sonra izlenecek en iyi güvenlik uygulamaları aşağıda verilmiştir:

**En iyi yöntem**: ilke çeşitli efekt türlerini destekler. [Azure ilke tanımı yapısında](../../governance/policy/concepts/definition-structure.md#policy-rule)bunlarla ilgili bilgi edinebilirsiniz. İş işlemleri, **reddetme** etkisinden olumsuz etkilenebilir ve bu **nedenle, ilkeden** olumsuz etkisi riskini sınırlamak için **Denetim** efektiyle başlayın.   
**Ayrıntı**: [ilke dağıtımlarını denetim modunda başlatın](../../governance/policy/concepts/definition-structure.md#policy-rule) ve daha sonra **reddetme** veya düzeltme için ilerleme durumunu **yapın.** **Reddet** veya **Düzelt**'e geçmeden önce denetim efektinin sonuçlarını test edin ve gözden geçirin.

Daha fazla bilgi için bkz. [uyumluluğu zorlamak için Ilke oluşturma ve yönetme](../../governance/policy/tutorials/create-and-manage.md).

**En iyi yöntem**: ilke ihlallerinin izlenmesinden sorumlu rolleri ve doğru düzeltme eyleminin hızlı bir şekilde alınmasını sağlar.   
**Ayrıntı**: atanan rol izleyicisine [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) veya [komut satırı](../../governance/policy/how-to/get-compliance-data.md#command-line)aracılığıyla uyumluluğunu sağlayabilirsiniz.

**En iyi yöntem**: Azure ilkesi, kuruluşun yazılı ilkelerinin teknik bir gösterimidir. Karışıklığı azaltmak ve tutarlılığı artırmak için tüm Azure Ilke tanımlarını kurumsal ilkelerle eşleyin.   
**Ayrıntı**: [ilke tanımında](../../governance/policy/concepts/definition-structure.md#display-name-and-description) veya [girişim tanımı](../../governance/policy/concepts/initiative-definition-structure.md#metadata) açıklamasında kuruluş ilkesine bir başvuru ekleyerek kuruluşunuzun belgelerindeki veya Azure ilke tanımındaki belge eşlemesini belgeleyin.

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD risk raporlarını izleme
Saldırganlar bir kullanıcının kimliğini çalarak bir ortama erişim kazanıyorsa güvenlik ihlallerinin büyük çoğunluğu bu şekilde gerçekleşir. Güvenliği aşılmış kimlikleri bulma işlemi kolay bir görev değildir. Azure AD, kullanıcı hesaplarınızla ilgili şüpheli eylemleri algılamak için uyarlamalı makine öğrenimi algoritmaları ve buluşsal yöntemler kullanır. Algılanan her şüpheli eylem, [risk algılama](../../active-directory/identity-protection/overview-identity-protection.md)adlı bir kayıtta saklanır. Risk algılamaları Azure AD güvenlik raporlarında kaydedilir. Daha fazla bilgi için [risk altındaki kullanıcılar güvenlik raporu](../../active-directory/identity-protection/overview-identity-protection.md) ve [riskli oturum açma güvenlik raporu](../../active-directory/identity-protection/overview-identity-protection.md)hakkında bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Azure 'u kullanarak bulut çözümlerinizi tasarlarken, dağıttığınızda ve yönetirken en iyi güvenlik uygulamaları için bkz. [Azure Güvenlik en iyi uygulamaları ve desenleri](best-practices-and-patterns.md) .

Aşağıdaki kaynaklar, Azure güvenliği ve ilgili Microsoft hizmetleri hakkında daha genel bilgiler sağlamak için kullanılabilir:
* Azure [güvenlik ekibi blogu](/archive/blogs/azuresecurity/) -Azure güvenliği ile ilgili en son bilgiler için
* [Microsoft Güvenlik](https://technet.microsoft.com/library/dn440717.aspx) açıkları, Azure ile ilgili sorunlar da dahil olmak üzere Microsoft güvenlik açıklarına göre bildirilebilir veya e-posta ile secure@microsoft.com