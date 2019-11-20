---
title: Uygulama proxy 'Si uygulamalarında hata ayıklama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) uygulama proxy 'Si uygulamalarıyla ilgili sorunları ayıklayın.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 06b8edcb0f912bfd35137e197253b20b9459448f
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057756"
---
# <a name="debug-application-proxy-application-issues"></a>Uygulama proxy 'Si uygulama sorunlarını ayıklama 

Bu makale Azure Active Directory (Azure AD) uygulama proxy 'Si uygulamalarıyla ilgili sorunları gidermenize yardımcı olur. Şirket içi bir Web uygulamasına uzaktan erişim için uygulama ara sunucusu hizmetini kullanıyorsanız, ancak uygulamaya bağlanırken sorun yaşıyorsanız, uygulama sorunlarını ayıklamak için bu akış çizelgesini kullanın. 

## <a name="before-you-begin"></a>Başlamadan önce

Uygulama proxy 'Si sorunlarını giderirken, bağlayıcılarla başlamasını öneririz. İlk olarak, uygulama proxy bağlayıcısının doğru yapılandırıldığından emin olmak için [uygulama proxy Bağlayıcısı sorunlarını ayıklama](application-proxy-debug-connectors.md) içindeki sorun giderme akışını izleyin. Hala sorun yaşıyorsanız, uygulamanın sorunlarını gidermek için bu makaleye geri dönün.  

Uygulama proxy 'Si hakkında daha fazla bilgi için bkz.

- [Uygulama proxy 'Si aracılığıyla şirket içi uygulamalara uzaktan erişim](application-proxy.md)
- [Uygulama proxy bağlayıcıları](application-proxy-connectors.md)
- [Bağlayıcı yükleyip kaydetme](application-proxy-add-on-premises-application.md)
- [Uygulama proxy'si sorunlarını ve hata iletileri sorunlarını giderme](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Uygulama sorunları için akış çizelgesi

Bu akış çizelgesi, uygulamaya bağlanma ile ilgili daha yaygın sorunlardan bazılarının hatalarını ayıklamaya yönelik adımlarda size yol gösterir. Her bir adımla ilgili ayrıntılar için, akış çizelgesinin altındaki tabloya bakın.

![Bir uygulamada hata ayıklama adımlarını gösteren akış çizelgesi](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Action | Açıklama | 
|---------|---------|---------|
|1\. | Bir tarayıcı açın, uygulamaya erişin ve kimlik bilgilerinizi girin | Uygulamada oturum açmak için kimlik bilgilerinizi kullanmayı deneyin ve [Bu şirket uygulamasına erişilemediği](application-proxy-sign-in-bad-gateway-timeout-error.md)gibi kullanıcı ile ilgili herhangi bir hata olup olmadığını denetleyin. |
|2 | Uygulamaya Kullanıcı atamasını doğrulama | Kullanıcı hesabınızın, kurumsal ağın içinden uygulamaya erişim izni olduğundan emin olun ve ardından [uygulamayı test](application-proxy-add-on-premises-application.md#test-the-application)etme bölümündeki adımları izleyerek uygulamada oturum açmayı sınayın. Oturum açma sorunları devam ederse bkz. [oturum açma hatalarıyla ilgili sorun giderme](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Bir tarayıcı açın ve uygulamaya erişmeyi deneyin | Bir hata hemen görüntülenirse, uygulama proxy 'Sinin doğru şekilde yapılandırılıp yapılandırılmadığını denetleyin. Belirli hata iletileri hakkında daha fazla bilgi için bkz. [uygulama proxy sorunlarını ve hata Iletilerini sorun giderme](application-proxy-troubleshoot.md).  |
|4 | Özel etki alanı kurulumunuzu denetleyin veya hatayı giderin | Sayfa hiç görünmezse, özel etki [alanlarıyla çalışmayı](application-proxy-configure-custom-domain.md)inceleyerek özel etki alanınızı doğru şekilde yapılandırıldığından emin olun.<br></br>Sayfa yüklenmez ve bir hata iletisi görünürse, [uygulama proxy 'si sorunlarını ve hata Iletilerini sorun giderme](application-proxy-troubleshoot.md)konusuna başvurarak hatayı giderin. <br></br>Bir hata iletisinin görünmesi 20 saniyeden uzun sürerse bağlantı sorunu olabilir. [Hata ayıklama uygulama proxy 'si bağlayıcıları](application-proxy-debug-connectors.md) sorun giderme makalesine gidin.  |
|5 | Sorun devam ederse bağlayıcı hata ayıklama bölümüne gidin | Ara sunucu ile bağlayıcı arasında veya bağlayıcı ile arka uç arasında bir bağlantı sorunu olabilir. [Hata ayıklama uygulama proxy 'si bağlayıcıları](application-proxy-debug-connectors.md) sorun giderme makalesine gidin. |
|6 | Tüm kaynakları yayımlama, tarayıcı geliştirici araçlarını denetleme ve bağlantıları çözme | Yayımlama yolunun, uygulamanız için gereken tüm görüntüleri, betikleri ve stil sayfalarını içerdiğinden emin olun. Ayrıntılar için bkz. [Azure AD 'ye şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Tarayıcının geliştirici araçlarını kullanın (Internet Explorer veya Microsoft Edge 'de F12 araçları) ve [Uygulama sayfasında](application-proxy-page-appearance-broken-problem.md)açıklandığı şekilde yayımlama sorunlarını kontrol edin. <br></br>[Sayfadaki bağlantılarda](application-proxy-page-links-broken-problem.md)kopuk bağlantıları çözme seçeneklerini gözden geçirin. |
|7 | Ağ gecikmesini denetle | Sayfa yavaş yüklenirse, [gecikme süresini azaltmak Için dikkate alınması gereken](application-proxy-network-topology.md#considerations-for-reducing-latency)ağ gecikmesini en aza indirme yolları hakkında bilgi edinin. | 
|8 | Ek sorun giderme yardımına bakın | Sorun devam ederse, [uygulama proxy 'si sorun giderme belgelerindeki](application-proxy-page-appearance-broken-problem.md)ek sorun giderme makalelerini bulun. |

## <a name="next-steps"></a>Sonraki adımlar


* [Ayrı ağlarda ve konumları bağlayıcı grupları kullanarak uygulama yayımlama](application-proxy-connector-groups.md)
* [Mevcut şirket içi proxy sunucuları ile çalışma](application-proxy-configure-connectors-with-proxy-servers.md)
* [Uygulama Ara sunucusu ve bağlayıcı hatalarını giderme](application-proxy-troubleshoot.md)
* [Azure AD uygulama ara sunucusu Bağlayıcısı sessiz yükleme](application-proxy-register-connector-powershell.md)
