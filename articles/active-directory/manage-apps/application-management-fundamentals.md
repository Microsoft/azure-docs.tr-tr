---
title: 'Uygulama Yönetimi: En Iyi uygulamalar ve öneriler | Microsoft Docs'
description: Azure Active Directory uygulamaları yönetmeye yönelik en iyi yöntemleri ve önerileri öğrenin. Uygulama proxy 'Si ile otomatik sağlama ve şirket içi uygulamalar yayımlamayı kullanma hakkında bilgi edinin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085297"
---
# <a name="application-management-best-practices"></a>Uygulama yönetimi en iyi uygulamaları
Bu makalede, Azure Active Directory (Azure AD) içinde uygulamaları yönetmeye yönelik öneriler ve en iyi uygulamalar, otomatik sağlama ve uygulama proxy 'Si ile şirket içi uygulamaları yayımlama işlemlerini içerir.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Bulut uygulaması ve çoklu oturum açma önerileri
| Öneri | Yorumlar |
| --- | --- |
| Uygulamalar için Azure AD Uygulama Galerisine bakın  | Azure AD 'de Kurumsal Çoklu oturum açma (SSO) ile etkinleştirilen binlerce önceden tümleştirilmiş uygulama içeren bir galeri bulunur. Uygulamaya özgü Kurulum Kılavuzu için [SaaS uygulama öğreticileri listesine](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)bakın.  | 
| Federal SAML tabanlı SSO kullanın  | Bir uygulama bunu destekliyorsa, parola tabanlı SSO ve ADFS yerine Azure AD ile Federal ve SAML tabanlı SSO kullanın.  | 
| Sertifika imzalama için SHA-256 kullanın  | Azure AD, SAML Yanıtını imzalamak için varsayılan olarak SHA-256 algoritmasını kullanır. Uygulama SHA-1 gerektirmediği takdirde SHA-256 kullanın (bkz. [sertifika imzalama seçenekleri](certificate-signing-options.md) ve [uygulama oturum açma sorunu](application-sign-in-problem-application-error.md).)  | 
| Kullanıcı Ataması gerektir  | Varsayılan olarak, kullanıcılar bu kullanıcılara atanmadan kurumsal uygulamalarınıza erişebilir. Ancak, uygulama rolleri kullanıma sunarsa veya uygulamanın bir kullanıcının erişim panelinde görünmesini istiyorsanız, Kullanıcı Ataması gerektir. (Bkz. [uygulamaları tümleştirmek Için Geliştirici Kılavuzu](developer-guidance-for-integrating-applications.md).)  | 
| Uygulamalarım erişim panelinden kullanıcılarınıza dağıtın | `https://myapps.microsoft.com` [erişim paneli](end-user-experiences.md) , kullanıcılara atanan bulut tabanlı uygulamalar için tek bir giriş noktası sağlayan Web tabanlı bir portaldır. Grup Yönetimi ve self servis parola sıfırlama gibi ek yetenekler eklendikçe, kullanıcılar bunları erişim panelinde bulabilir. Bkz. [erişim bölmesi dağıtımı planı](access-panel-deployment-plan.md).
| Grup atamasını kullanma  | Aboneliğinize dahil ise, grup sahibine devam eden erişim yönetimini devredebilmeniz için bir uygulamaya gruplar atayın. (Bkz. [uygulamaları tümleştirmek Için Geliştirici Kılavuzu](developer-guidance-for-integrating-applications.md).)   | 
| Sertifikaları yönetmek için bir işlem oluşturma | İmza sertifikasının maksimum ömrü üç yıldır. Sertifikanın süresi dolduğunda kesinti oluşmasını engellemek veya en aza indirmek için, sertifikayla ilgili değişiklik bildirimlerinin yakından izlendiğinden emin olmak için roller ve e-posta dağıtım listelerini kullanın. |

## <a name="provisioning-recommendations"></a>Sağlama önerileri
| Öneri | Yorumlar |
| --- | --- |
| Bulut uygulamalarıyla sağlamayı ayarlamak için öğreticileri kullanın | Eklemek istediğiniz Galeri uygulaması için sağlamayı yapılandırmaya yönelik adım adım yönergeler için [SaaS uygulama öğreticileri listesini](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) kontrol edin. |
| Durumu izlemek için sağlama günlüklerini (Önizleme) kullanma | [Sağlama günlükleri](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) , bireysel kullanıcılar için durum da dahil olmak üzere, sağlama hizmeti tarafından gerçekleştirilen tüm eylemlerin ayrıntılarını verir. |
| Sağlama bildirim e-postasına bir dağıtım grubu atama | Sağlama hizmeti tarafından gönderilen kritik uyarıların görünürlüğünü artırmak için, bildirim e-postaları ayarına bir dağıtım grubu atayın. |


## <a name="application-proxy-recommendations"></a>Uygulama proxy 'Si önerileri
| Öneri | Yorumlar |
| --- | --- |
| İç kaynaklara uzaktan erişim için uygulama proxy 'Si kullanma | Uygulama proxy 'Si, uzak kullanıcılara iç kaynaklara erişim verilmesi için önerilir ve bu da VPN veya ters proxy gereksinimini değiştirir. Gecikme ekleyebileceğinden, kurumsal ağ içinden kaynaklara erişmek için tasarlanmamıştır.
| Özel etki alanlarını kullanma | Uygulamalarınız için özel etki alanları ayarlayın (bkz. [özel etki alanlarını yapılandırma](application-proxy-configure-custom-domain.md)). böylece kullanıcılar ve uygulamalar arasında URL 'lerin ağınızın içinden veya dışından çalışması gerekir. Ayrıca markanızı denetleyebilir ve URL 'nizi özelleştirebilirsiniz.  Özel etki alanı adları kullanırken, Microsoft olmayan bir güvenilen sertifika yetkilisinden ortak bir sertifika almayı planlayın. Azure uygulama proxy 'Si standart, ([joker karakter](application-proxy-wildcard.md)) veya San tabanlı sertifikaları destekler. (Bkz. [uygulama proxy planlaması](application-proxy-deployment-plan.md).) |
| Uygulama proxy 'Si dağıtılmadan önce kullanıcıları eşitler | Uygulama proxy 'sini dağıtmadan önce, şirket içi bir dizinden Kullanıcı kimliklerini eşitler veya doğrudan Azure AD 'de oluşturun. Kimlik eşitlemesi, Azure AD 'nin, uygulama proxy 'Si yayımlanmış uygulamalarına erişim vermeden önce kullanıcıların kimliğini önceden doğrulayabilmesine izin verir. Ayrıca, çoklu oturum açma (SSO) gerçekleştirmek için gerekli Kullanıcı tanımlayıcı bilgilerini sağlar. (Bkz. [uygulama proxy planlaması](application-proxy-deployment-plan.md).) |
| Yüksek kullanılabilirlik ve yük dengeleme için ipuçlarımızı izleyin | Trafiğin kullanıcılar, uygulama proxy bağlayıcıları ve arka uç uygulama sunucuları arasında nasıl akacağını öğrenmek ve performans ve yük dengelemeyi iyileştirmeye yönelik ipuçları almak için, bkz. [uygulama proxy bağlayıcılarınızın ve uygulamalarınızın yüksek kullanılabilirlik ve yük dengelemesi](application-proxy-high-availability-load-balancing.md). |
| Birden çok bağlayıcı kullanma | Daha fazla esneklik, kullanılabilirlik ve ölçek için iki veya daha fazla uygulama proxy Bağlayıcısı kullanın (bkz. [uygulama proxy bağlayıcıları](application-proxy-connectors.md)). Bağlayıcı grupları oluşturun ve her bağlayıcı grubunda en az iki bağlayıcı olduğundan emin olun (üç bağlayıcı en iyi şekilde bulunur). |
| Bağlayıcı sunucularını uygulama sunucularına Kapat ' ı bulun ve aynı etki alanında olduklarından emin olun | Performansı iyileştirmek için bağlayıcı sunucusunu uygulama sunucularına yakın fiziksel olarak bulun (bkz. [ağ topolojisi konuları](application-proxy-network-topology.md)). Ayrıca, bağlayıcı sunucusu ve Web uygulamaları sunucularının aynı Active Directory etki alanına ait olması veya güvenen etki alanlarını kapsamaları gerekir. Bu yapılandırma, tümleşik Windows kimlik doğrulaması (ıWA) ve Kerberos kısıtlanmış temsili (KCD) içeren SSO için gereklidir. Sunucular farklı etki alanlarındaysa, SSO için kaynak tabanlı temsilciyi kullanmanız gerekir (bkz. [uygulama proxy 'si ile çoklu oturum açma Için KCD](application-proxy-configure-single-sign-on-with-kcd.md)). |
| Bağlayıcılar için otomatik güncelleştirmeleri etkinleştir | En son özellikler ve hata düzeltmeleri için bağlayıcılarınız için otomatik güncelleştirmeleri etkinleştirin. Microsoft, en son bağlayıcı sürümü ve bir sürümü için doğrudan destek sağlar. (Bkz. [uygulama proxy yayın sürümü geçmişi](application-proxy-release-version-history.md).) |
| Şirket içi proxy 'nizi atlayın | Daha kolay bakım için, bağlayıcıyı doğrudan Azure hizmetlerine bağlanacak şirket içi ara sunucusunu atlayacak şekilde yapılandırın. (Bkz. [uygulama proxy bağlayıcıları ve proxy sunucuları](application-proxy-configure-connectors-with-proxy-servers.md).) |
| Web uygulaması ara sunucusu üzerinden Azure AD Uygulama Ara Sunucusu kullanma | Azure AD Uygulama Ara Sunucusu 'yi şirket içi senaryolar için kullanın. Web uygulaması ara sunucusu yalnızca AD FS için bir ara sunucu gerektiren ve Azure Active Directory özel etki alanlarını kullanabileceğiniz senaryolarda tercih edilir. (Bkz. [uygulama proxy 'si geçişi](application-proxy-migration.md).) |
