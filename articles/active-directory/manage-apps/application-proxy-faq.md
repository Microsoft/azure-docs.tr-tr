---
title: Azure AD Uygulama Ara Sunucusu sık sorulan sorular | Microsoft Docs
description: Şirket içi uygulamaları uzak kullanıcılara yayımlamak için Azure AD Uygulama Ara Sunucusu kullanma hakkında sık sorulan soruların (SSS) yanıtlarını öğrenin.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 612b6caf47ec4764aa2bbef162592100198ed0c4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832205"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Active Directory (Azure AD) uygulama proxy 'Si hakkında sık sorulan sorular

Bu sayfa Azure Active Directory (Azure AD) uygulama proxy 'Si hakkında sıkça sorulan soruları yanıtlar.

## <a name="enabling-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu etkinleştiriliyor

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu kullanmak için hangi lisans gerekir?

Azure AD Uygulama Ara Sunucusu kullanmak için bir Azure AD Premium P1 veya P2 lisansına sahip olmanız gerekir. Lisanslama hakkında daha fazla bilgi için bkz. [Azure Active Directory fiyatlandırması](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>"Uygulama proxy 'Sini etkinleştir düğmesi neden gri renkte?

En az bir Azure AD Premium P1 veya P2 lisansına sahip olduğunuzdan ve Azure AD Uygulama Ara Sunucusu bağlayıcısının yüklü olduğundan emin olun. İlk bağlayıcınızı başarıyla yükledikten sonra Azure AD Uygulama Ara Sunucusu hizmeti otomatik olarak etkinleştirilir.

## <a name="connector-configuration"></a>Bağlayıcı yapılandırması

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Uygulama proxy Bağlayıcısı Hizmetleri varsayılandan farklı bir kullanıcı bağlamında çalıştırılabilir mi?

Hayır, bu senaryo desteklenmiyor. Varsayılan ayarlar şunlardır:

- Microsoft AAD uygulama proxy Bağlayıcısı-WAPCSvc-Network Service
- Microsoft AAD Uygulama Ara Sunucusu Bağlayıcı Güncelleştiricisi-WAPCUpdaterSvc-NT Authorıty\system

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Arka uç Uygulamam birden çok Web sunucusunda barındırılır ve Kullanıcı oturumu kalıcılığı (süreklilik) gerektirir. Oturum kalıcılığını nasıl sağlayabilirim? 

Öneriler için bkz. [uygulama proxy bağlayıcılarınızın ve uygulamalarınızın yüksek kullanılabilirliği ve yük dengelemesi](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Bağlayıcı sunucu (ler) ve arka uç uygulama sunucusu arasında bir iletme proxy cihazı yerleştirebilir miyim?

Hayır, bu senaryo desteklenmiyor. Yalnızca bağlayıcı ve güncelleştirme hizmetleri, Azure 'a giden trafik için bir iletme proxy 'si kullanacak şekilde yapılandırılabilir. Bkz. [mevcut şirket içi proxy sunucularıyla çalışma](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-ssl-termination-sslhhtps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Bağlayıcı sunuculardan Azure 'a giden trafik üzerinde SSL sonlandırması (SSL/HHTPS denetimi veya hızlandırma) mi kullanıyorsunuz?

Uygulama proxy Bağlayıcısı, Azure 'da sertifika tabanlı kimlik doğrulaması gerçekleştirir. SSL sonlandırma (SSL/HHTPS incelemesi veya hızlandırma) bu kimlik doğrulama yöntemini keser ve desteklenmez. Bağlayıcıdan Azure 'a giden trafik, SSL sonlandırma gerçekleştiren tüm cihazları atmalıdır.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Bağlayıcıyı Azure AD Uygulama Ara Sunucusu kaydetmek için adanmış bir hesap oluşturmalı mıyım?

Bunun için bir neden yoktur. Herhangi bir genel yönetici veya uygulama yöneticisi hesabı çalışacaktır. Yükleme sırasında girilen kimlik bilgileri kayıt işleminden sonra kullanılmaz. Bunun yerine, bu noktadan kimlik doğrulaması için kullanılan bağlayıcıya bir sertifika verilir.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Azure AD Uygulama Ara Sunucusu bağlayıcısının performansını nasıl izleyebilirim?

Bağlayıcıyla birlikte yüklenen performans Izleme sayaçları vardır. Bunları görüntülemek için:  

1. **Başlat**' ı seçin, "perfmon" YAZıN ve ENTER tuşuna basın.
2. **Performans İzleyicisi** ' ni seçin ve yeşil **+** simgesine tıklayın.
3. İzlemek istediğiniz **MICROSOFT AAD uygulama proxy Bağlayıcısı** sayaçlarını ekleyin.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD Uygulama Ara Sunucusu bağlayıcısının kaynakla aynı alt ağda olması gerekir mi?

Bağlayıcının aynı alt ağda olması gerekmez. Ancak, kaynak için ad çözümlemesi (DNS, ana bilgisayar dosyası) ve gerekli ağ bağlantısı (kaynağa yönlendirme, kaynakta açık bağlantı noktaları, vb.) gerekir. Öneriler için [Azure Active Directory uygulama ara sunucusu kullanırken ağ topolojisi konuları](application-proxy-network-topology.md)bölümüne bakın.

## <a name="application-configuration"></a>Uygulama yapılandırması

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Varsayılan ve "uzun" arka uç zaman aşımı uzunluğu nedir? Zaman aşımı genişletilmiş olabilir mi?

Varsayılan uzunluk 85 saniyedir. "Long" ayarı 180 saniyedir. Zaman aşımı sınırı genişletilemiyor.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Uygulamamın giriş sayfasını Nasıl yaparım? değiştirmek mi?

Uygulama kayıtları sayfasında, giriş sayfasının istenen dış URL 'si için giriş sayfası URL 'sini değiştirebilirsiniz. Uygulama Uygulamalarım veya Office 365 portalından başlatıldığında belirtilen sayfa yüklenir. Yapılandırma adımları için bkz. [Azure AD uygulama ara sunucusu ile yayımlanan uygulamalar için özel bir giriş sayfası ayarlama](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Yalnızca IIS tabanlı uygulamalar yayımlanabilir mi? Windows dışı Web sunucularında çalışan Web uygulamaları nelerdir? Bağlayıcının IIS yüklü bir sunucuda yüklü olması gerekir mi?

Hayır, yayımlanan uygulamalar için IIS gereksinimi yoktur. Windows Server dışındaki sunucularda çalışan Web uygulamalarını yayımlayabilirsiniz. Ancak, Web sunucusunun anlaşmayı (Kerberos kimlik doğrulaması) destekleyip desteklemediğine bağlı olarak, Windows dışı bir sunucu ile ön kimlik doğrulaması kullanmeyebilirsiniz. Bağlayıcının yüklü olduğu sunucuda IIS gerekli değildir.

## <a name="integrated-windows-authentication"></a>Tümleşik Windows kimlik doğrulaması

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kerberos kısıtlı temsilci (KCD) ayarlarken Principalsallodilimtodelegatetoaccount metodunu ne zaman kullanmalıyım?

Bağlayıcı sunucular Web uygulaması hizmet hesabından farklı bir etki alanında olduğunda Principalsallodilimtodelegatetoaccount yöntemi kullanılır. Kaynak tabanlı kısıtlanmış temsilin kullanılmasını gerektirir.
Bağlayıcı sunucuları ve Web uygulaması hizmet hesabı aynı etki alanında yer alıyorsa, her bağlayıcı makine hesabında temsilci ayarlarını yapılandırmak için Active Directory Kullanıcıları ve bilgisayarları kullanabilirsiniz, böylece hedef SPN 'nin temsilciliğini alabilir.

Bağlayıcı sunucuları ve Web uygulaması hizmet hesabı farklı etki alanlarındaysa, kaynak tabanlı temsili kullanılır. Temsili izinleri hedef Web sunucusunda ve Web uygulaması hizmeti hesabında yapılandırılır. Bu kısıtlı yetkilendirme yöntemi nispeten yenidir. Yöntemi, kaynak (Web hizmeti) sahibinin hangi makine ve hizmet hesaplarının temsil edebilir olduğunu denetlemesine izin vererek etki alanları arası temsilciyi destekleyen Windows Server 2012 ' de kullanıma sunulmuştur. Bu yapılandırmaya yardımcı olacak bir kullanıcı arabirimi yoktur, bu nedenle PowerShell kullanmanız gerekir.
Daha fazla bilgi için bkz. [uygulama proxy 'si Ile Kerberos kısıtlanmış temsilciyi anlama](https://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Doğrudan kimlik doğrulama

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Doğrudan kimlik doğrulamasıyla yayımlanan uygulamalar için koşullu erişim Ilkelerini kullanabilir miyim?

Koşullu erişim Ilkeleri yalnızca Azure AD 'de başarıyla önceden kimliği doğrulanmış kullanıcılar için zorlanır. Doğrudan kimlik doğrulaması Azure AD kimlik doğrulamasını tetiklemez, bu nedenle koşullu erişim Ilkeleri zorlanamaz. Doğrudan kimlik doğrulaması ile, MFA ilkelerinin şirket içi sunucuda, mümkünse veya Azure AD Uygulama Ara Sunucusu ön kimlik doğrulaması etkinleştirilerek uygulanması gerekir.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>İstemci sertifikası kimlik doğrulaması gereksinimiyle bir Web uygulaması yayımlayabilir miyim?

Hayır, uygulama proxy 'Si TLS trafiğini sonlandıracağından bu senaryo desteklenmez.  

## <a name="remote-desktop-gateway-publishing"></a>Uzak Masaüstü Ağ Geçidi yayımlama

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu üzerinden Uzak Masaüstü Ağ Geçidi nasıl yayımlayabilirim?

[Azure AD uygulama ara sunucusu uzak masaüstünü yayımlama](application-proxy-integrate-with-remote-desktop-services.md)konusuna bakın.

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>Uzak Masaüstü Ağ Geçidi yayımlama senaryosunda Kerberos kısıtlanmış temsilciyi kullanabilir miyim?

Hayır, bu senaryo desteklenmiyor.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Kullanıcılarım Internet Explorer 11 kullanmıyor ve ön kimlik doğrulama senaryosu bunlar için çalışmıyor. Bu beklensin mi?

Evet, bekleniyor. Ön kimlik doğrulama senaryosu, üçüncü taraf tarayıcılarda desteklenmeyen bir ActiveX denetimi gerektirir.

### <a name="is-the-remote-desktop-web-client-supported"></a>Uzak Masaüstü Web Istemcisi destekleniyor mu?

Hayır, bu senaryo şu anda desteklenmiyor. Bu özellikte güncelleştirmeler için [UserVoice](https://aka.ms/aadapuservoice) geri bildirim Forumumuzu izleyin.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Ön kimlik doğrulama senaryosunu yapılandırdıktan sonra, kullanıcının kimlik doğrulamasının iki kez olduğunu fark ediyorum: ilk olarak Azure AD oturum açma formunda ve ardından RDWeb oturum açma formunda. Bu beklensin mi? Bunu bir oturum açma ile nasıl azaltabilirim?

Evet, bekleniyor. Kullanıcının bilgisayarı Azure AD 'ye katılırsa, Kullanıcı Azure AD 'de otomatik olarak oturum açar. Kullanıcının kimlik bilgilerini yalnızca RDWeb oturum açma formunda sağlaması gerekir.

## <a name="sharepoint-publishing"></a>SharePoint yayımlaması

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu SharePoint üzerinden nasıl yayımlayabilirim?

[Azure AD uygulama ara sunucusu Ile SharePoint 'e uzaktan erişimi etkinleştirme](application-proxy-integrate-with-sharepoint-server.md)bölümüne bakın.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Active Directory Federasyon Hizmetleri (AD FS) (AD FS) yayımlama 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Azure AD Uygulama Ara Sunucusu, AD FS proxy (Web uygulaması ara sunucusu gibi) olarak kullanabilir miyim?

Hayır. Azure AD Uygulama Ara Sunucusu, Azure AD ile çalışacak şekilde tasarlanmıştır ve bir AD FS proxy 'si görevi gören gereksinimleri yerine getirmez.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket desteği QlikSense dışındaki uygulamalar için çalışır mı?

Şu anda WebSocket protokol desteği hala genel önizlemede ve diğer uygulamalar için çalışmayabilir. Bazı müşterilerin WebSocket protokolünü diğer uygulamalarla kullanarak karma başarısı vardı. Bu senaryoları test ediyorsanız, sonuçlarınızı duymak isteriz. Lütfen aadapfeedback@microsoft.comadresinden bize geri bildirim gönderin.

## <a name="link-translation"></a>Bağlantı çevirisi

### <a name="does-using-link-translation-affect-performance"></a>Bağlantı çevirisi kullanımı performansı etkiler mi?

Evet. Bağlantı çevirisi performansı etkiler. Uygulama proxy hizmeti, uygulamayı sabit kodlanmış bağlantılar için tarar ve bunları kullanıcıya sunmadan önce ilgili, yayımlanan dış URL 'lerle değiştirir. 

En iyi performansı elde etmek için, [özel etki alanlarını](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)yapılandırarak özdeş iç ve dış URL 'ler kullanmanızı öneririz. Özel etki alanlarının kullanılması mümkün değilse, mobil uygulama veya Microsoft Edge tarayıcısı olan uygulamalarım güvenli oturum açma uzantısı ' nı kullanarak bağlantı çevirisi performansını geliştirebilirsiniz. Bkz. [Azure AD uygulama ara sunucusu ile yayımlanan uygulamalar Için yeniden kodlanmış bağlantıları yeniden yönlendirme](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Joker karakterler

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Aynı özel etki alanı adına sahip, ancak biri HTTP ve HTTPS için olmak üzere farklı protokollerle iki uygulama yayımlamak için joker karakterler kullanın Nasıl yaparım?

Bu senaryo doğrudan desteklenmez. Bu senaryoya ilişkin seçenekleriniz şunlardır:

1. Hem HTTP hem de HTTPS URL 'Lerini joker karakterle ayrı uygulamalar olarak yayımlayın, ancak her birine farklı bir özel etki alanı verin. Bu yapılandırma, farklı dış URL 'ler olduğundan çalışacaktır.

2. HTTPS URL 'sini bir joker uygulama aracılığıyla yayımlayın. HTTP uygulamalarını bu uygulama proxy PowerShell cmdlet 'lerini kullanarak ayrı ayrı yayımlayın:
   - [Uygulama proxy 'Si uygulama yönetimi](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Uygulama proxy Bağlayıcısı yönetimi](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
