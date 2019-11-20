---
title: Azure Active Directory raporlama ve izleme dağıtımını planlayın
description: Raporlama ve izlemenin nasıl planlanacağını ve yürütüleceğini açıklar.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d24a4ab74f7957c068fc664fc4e8898c525afcb
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162535"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Azure Active Directory raporlama ve izleme dağıtımını planlayın

Azure Active Directory (Azure AD) raporlama ve izleme çözümünüz, yasal, güvenlik ve operasyonel gereksinimlerinize ve var olan ortamınıza ve süreçlerinize bağlıdır. Bu makalede, çeşitli tasarım seçenekleri sunulmakta ve doğru dağıtım stratejisinde size rehberlik sunulmaktadır.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD raporlama ve izlemenin avantajları

Azure AD raporlama, ortamınızda oturum açma olayları, denetim olayları ve dizininizde değişiklikler dahil olmak üzere Azure AD etkinliğinin kapsamlı bir görünümünü ve günlüklerini sağlar.

Sağlanan verilerle:

* uygulamalarınızın ve hizmetlerinizin nasıl kullanıldığını saptayın.

* ortamınızın sistem durumunu etkileyen olası riskleri tespit edin.

* Kullanıcılarınızın işlerini halledmesini önlemeye yönelik sorunları giderin.

* Azure AD dizininizde yapılan değişikliklerin denetim olaylarını görerek Öngörüler elde edin.

> [!IMPORTANT]
> Azure AD izleme, Azure AD raporlama tarafından oluşturulan günlüklerinizi farklı hedef sistemlere yönlendirmenizi sağlar. Ardından bu günlükleri uzun vadeli kullanım için saklayabilir veya ortamınızla ilgili içgörülere ulaşmak için üçüncü taraf Güvenlik Bilgileri ve Olay Yönetimi (SIEM) araçlarıyla tümleştirebilirsiniz.

Azure AD izleme ile günlükleri şu şekilde yönlendirebilirsiniz:

* arşivleme amacıyla bir Azure depolama hesabı.
* Daha önce Azure Log Analytics çalışma alanı olarak bilinen, verileri çözümleyebildiğiniz, pano oluşturabileceğiniz ve belirli olaylara uyarı oluşturabileceğiniz Azure Izleyici günlükleri.
* splunk, SumoLogic veya QRadar gibi var olan SıEM araçlarınızla tümleştirebileceğiniz bir Azure Olay Hub 'ı.

> [!NOTE]
Yakın zamanda Log Analytics yerine Azure Izleyici günlükleri terimini kullanmaya başladık. Günlük verileri hala bir Log Analytics çalışma alanında depolanır ve yine de aynı Log Analytics hizmeti tarafından toplanıp çözümlenmektedir. [Azure izleyici 'de günlüklerin](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection)rolünü daha iyi yansıtacak şekilde terminolojiyi güncelleştiriyoruz. Ayrıntılar için bkz. [Azure izleyici terminolojisi değişiklikleri](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand) .

[Rapor bekletme ilkeleri hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD raporlama ve izleme için lisanslama ve Önkoşullar

Azure AD oturum açma günlüklerine erişmek için bir Azure AD Premium lisansına sahip olmanız gerekir.

[Azure Active Directory fiyatlandırma kılavuzunda](https://azure.microsoft.com/pricing/details/active-directory/)ayrıntılı özellik ve lisanslama bilgileri için.

Azure AD izleme ve raporlama dağıtmak için, Azure AD kiracısı için genel yönetici veya güvenlik yöneticisi olan bir kullanıcıya ihtiyacınız vardır.

Günlük verilerinizin son hedefine bağlı olarak, aşağıdakilerden birine sahip olmanız gerekir:

* İçin ListKeys izinleriniz olan bir Azure depolama hesabı. Blob depolama hesabı değil genel bir depolama hesabı kullanmanızı öneririz. Depolamayla fiyatlandırma bilgileri için bkz. [Azure Depolama fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Üçüncü taraf SıEM çözümleriyle tümleştirilecek Azure Event Hubs ad alanı.

* Azure Izleyici günlüklerine Günlükler göndermek için bir Azure Log Analytics çalışma alanı.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Azure raporlama ve izleme dağıtım projesi planlayın

Bu projede, raporları tüketmek ve izlemek için izleyicileri tanımlayacaksınız ve Azure AD izleme mimarinizi tanımlayacaksınız.

### <a name="engage-the-right-stakeholders"></a>Doğru paydaşlara katılın

Teknoloji projeleri başarısız olduğunda, genellikle etki, sonuç ve sorumlulukların eşleşmeyen beklentileri nedeniyle bu, genellikle bu şekilde yapılır. Bu sınırları önlemek için, [doğru paydaşları ilgi çekici olduğunuzdan emin olun](https://aka.ms/deploymentplans). Ayrıca, proje katılımcıları ve bunların proje giriş ve accountatçlarını belgeleyerek projedeki paydaş rollerinin iyi anlaşıldığından emin olun.

### <a name="plan-communications"></a>İletişimleri planlayın

İletişim, her yeni hizmetin başarısı için önemlidir. Deneyimlerinizin nasıl değiştirileceği, ne zaman değiştirileceği ve sorunlarla karşılaştıkları durumlarda nasıl destek kazanabilecekleri hakkında daha etkin bir şekilde iletişim kurun.

### <a name="document-your-current-infrastructure-and-policies"></a>Geçerli altyapınızı ve ilkelerinizi belgeleyin

Geçerli altyapınız ve ilkeleriniz raporlama ve izleme tasarımınızı barındıracak. Haberdar olduğunuzdan emin olun

* Kullandığınız varsa SıEM araçları.

* Mevcut depolama hesapları ve kullanılan izleme dahil olmak üzere Azure altyapınız.

* Tüm geçerli uyumluluk çerçeveleri dahil olmak üzere Günlükler için kuruluş bekletme ilkeleriniz. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Azure AD raporlama ve izleme dağıtımını planlayın

Raporlama ve izleme, iş gereksinimlerinizi karşılamak, kullanım desenleriyle ilgili Öngörüler elde etmek ve kuruluşların güvenlik duruşunuzu artırmak için kullanılır.

### <a name="business-use-cases"></a>İş kullanım örnekleri

* Çözümün iş ihtiyaçlarını karşılaması için gereklidir
* İş ihtiyaçlarını karşılamak için iyi
* Geçerli değil

|Alan |Açıklama |
|-|-|
|Bekletme| **30 günden uzun süre bekletmesi günlüğe kaydedilir**. Yasal veya iş gereksinimleri nedeniyle, Denetim günlüklerini depolamak ve Azure AD 'de oturum açmak 30 günden daha uzun bir süre için gereklidir. |
|Analiz| **Günlüklerin aranabilir olması gerekir**. Depolanan günlüklerin Analitik araçlarla aranabilir olması gerekir. |
| Operasyonel İçgörüler| **Çeşitli ekipler Için Öngörüler**. Uygulama kullanımı, oturum açma hataları, Self Servis kullanımı, eğilimler vb. gibi operasyonel içgörüler elde etmek için farklı kullanıcılara erişim verme ihtiyacı. |
| Güvenlik öngörüleri| **Çeşitli ekipler Için Öngörüler**. Uygulama kullanımı, oturum açma hataları, Self Servis kullanımı, eğilimler vb. gibi operasyonel içgörüler elde etmek için farklı kullanıcılara erişim verme ihtiyacı. |
| SıEM sistemlerinde tümleştirme      | **SIEM tümleştirmesi**. Mevcut SıEM sistemlerine Azure AD oturum açma ve denetim günlüklerini tümleştirme ve akışla birleştirme ihtiyacı. |

### <a name="choose-a-monitoring-solution-architecture"></a>İzleme çözümü mimarisi seçin

Azure AD izleme ile Azure AD etkinlik günlüklerinizi, iş gereksinimlerinizi en iyi şekilde karşılayan bir sisteme yönlendirebilirsiniz. Daha sonra bu bilgileri, ortamınız hakkında öngörüler elde etmek ve SıEM araçlarıyla bütünleştirmek için uzun süreli raporlama ve analizler için koruyabilirsiniz.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sectionsmediareporting-deployment-plandeploy-reporting-flow-diagrampng"></a>Karar akışı grafiği![Sonraki bölümlerde nelerin açıklandığı gösteren resim](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Depolama hesabında arşiv günlükleri

Günlükleri bir Azure depolama hesabına yönlendirerek, bunları [bekletme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)ilkilerimizde özetlenen varsayılan saklama süresinden daha uzun bir süre boyunca tutabilirsiniz. Günlüklerinizi arşivlemeniz gerekiyorsa, ancak bunları SıEM sistemiyle tümleştirmeniz gerekmiyorsa ve sürekli sorgular ve analize gerek duymuyorsanız bu yöntemi kullanın. İsteğe bağlı aramalar yapabilirsiniz.

[Verileri depolama hesabınıza yönlendirmeyi](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account) öğrenin.

#### <a name="send-logs-to-azure-monitor-logs"></a>Günlükleri Azure Izleyici günlüklerine gönder

[Azure izleyici günlükleri](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) , izleme verilerini farklı kaynaklardan birleştirir. Ayrıca, uygulamalarınızın ve kaynak kullanmanın çalışması hakkında Öngörüler sağlayan bir sorgu dili ve analiz altyapısı da sağlar. Azure AD etkinlik günlüklerini Azure Izleyici günlüklerine göndererek, toplanan verileri hızlıca alabilir, izleyebilir ve uyarabilir. Verilerinizi doğrudan göndermek istediğiniz bir SıEM çözümünüz yoksa ve sorgu ve analiz yapmak istiyorsanız bu yöntemi kullanın. Verileriniz Azure Izleyici günlükleriniz olduktan sonra bunu Olay Hub 'ına ve isterseniz SıEM 'ye gönderebilirsiniz.

[Azure izleyici günlüklerine veri gönderme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)hakkında bilgi edinin.

Ayrıca, oturum açma ve denetim olaylarına ilişkin yaygın senaryoları izlemek üzere Azure AD etkinlik günlükleri için önceden oluşturulmuş görünümleri yükleyebilirsiniz.

[Azure ad etkinlik günlükleri için Log Analytics görünümlerini yüklemeyi ve kullanmayı](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)öğrenin.

#### <a name="stream-logs-to-your-azure-event-hub"></a>Günlükleri Azure Olay Hub 'ınıza akışla

Azure Olay Hub 'ına yönlendirme günlükleri, üçüncü taraf SıEM araçlarıyla tümleştirmeyi sağlar. Bu tümleştirme, ortamınızda daha zengin Öngörüler sağlamak için Azure AD etkinlik günlüğü verilerini SıEM 'niz tarafından yönetilen diğer verilerle birleştirmenizi sağlar. 

[Olay hub'ına günlük akışı yapmayı](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub) öğrenin.

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Azure AD raporlama ve izleme için Işlem ve güvenlik planlayın

Paydaşların operasyonel içgörüler elde etmek için Azure AD günlüklerine erişmesi gerekir. Büyük olasılıkla kullanıcılara güvenlik ekibi üyeleri, iç veya dış denetçiler ve kimlik ve erişim yönetimi işlemleri ekibi dahildir.

Azure AD rolleri, Azure AD raporlarını rolünüze göre yapılandırma ve görüntüleme olanağı sağlar. Kuruluşunuzun Azure AD raporlarını okumak için hangi izinlere ihtiyacı olduğunu ve bu kullanıcılara hangi rolün uygun olduğunu belirlemek. 

Aşağıdaki roller Azure AD raporlarını okuyabilir:

* Genel Yönetici

* Güvenlik Yöneticisi

* Güvenlik Okuyucusu

* Rapor okuyucu

[Azure AD Yönetim rolleri](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)hakkında daha fazla bilgi edinin.

*Hesap güvenliğinin tehlikeye düşmesi riskini azaltmak Için her zaman en az ayrıcalık kavramını uygulayın*. Kuruluşunuzun güvenliğini sağlamak için [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) uygulamayı düşünün.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Azure AD raporlama ve izleme dağıtma

Yukarıdaki tasarım kılavuzunu kullanarak daha önce yaptığınız kararlara bağlı olarak, bu bölüm, farklı dağıtım seçeneklerindeki belgelerde size kılavuzluk eder.

### <a name="consume-and-archive-azure-ad-logs"></a>Azure AD günlüklerini tüketme ve arşivleme

[Azure portalda etkinlik raporlarını bulma](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Azure AD günlüklerini bir Azure depolama hesabına arşivleme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>İzleme ve analiz uygulama

[Günlükleri Azure Izleyici 'ye gönderme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Azure Active Directory için Log Analytics görünümlerini yükleyip kullanın](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Azure Izleyici günlükleri ile Azure AD etkinlik günlüklerini çözümleme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Azure İzleyici denetim günlükleri şemasını yorumlama](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Azure Izleyici 'de oturum açma günlüğü şemasını yorumlama](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Azure AD günlüklerini Azure Olay Hub 'ına akış](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Azure İzleyici kullanarak Azure AD günlüklerini Splunk ile tümleştirme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Azure İzleyici kullanarak Azure AD günlüklerini SumoLogic ile tümleştirme](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Sonraki adımlar

[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) uygulamayı deneyin 

[Rol tabanlı erişim denetimi (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) uygulamayı düşünün

 
