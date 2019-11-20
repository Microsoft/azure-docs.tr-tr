---
title: Uygulama proxy 'Si bağlayıcılarını ayıklama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) uygulama proxy bağlayıcılarıyla ilgili sorunları ayıklayın.
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311854"
---
# <a name="debug-application-proxy-connector-issues"></a>Uygulama proxy Bağlayıcısı sorunlarını ayıklama 

Bu makale Azure Active Directory (Azure AD) uygulama proxy bağlayıcılarıyla ilgili sorunları gidermenize yardımcı olur. Şirket içi bir Web uygulamasına uzaktan erişim için uygulama ara sunucusu hizmetini kullanıyorsanız, ancak uygulamaya bağlanırken sorun yaşıyorsanız, bağlayıcı sorunlarını ayıklamak için bu akış çizelgesini kullanın. 

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, uygulama proxy bağlayıcısını yüklediğinizi ve bir sorun yaşadığınızı varsaymaktadır. Uygulama proxy 'Si sorunlarını giderirken, uygulama proxy bağlayıcısının doğru yapılandırılıp yapılandırılmadığını öğrenmek için bu sorun giderme akışıyla başlamanız önerilir. Uygulamaya bağlanırken sorun yaşamaya devam ediyorsanız, [uygulama proxy 'si uygulama sorunlarını ayıklama](application-proxy-debug-apps.md)içindeki sorun giderme akışını izleyin.  


Uygulama proxy 'Si ve bağlayıcılarını kullanma hakkında daha fazla bilgi için bkz.:

- [Uygulama proxy 'Si aracılığıyla şirket içi uygulamalara uzaktan erişim](application-proxy.md)
- [Uygulama proxy bağlayıcıları](application-proxy-connectors.md)
- [Bağlayıcı yükleyip kaydetme](application-proxy-add-on-premises-application.md)
- [Uygulama proxy 'Si sorunlarını ve hata iletilerini sorun giderme](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Bağlayıcı sorunları için akış çizelgesi

Bu akış çizelgesi, daha yaygın bağlayıcı sorunlarından bazılarının hatalarını ayıklamaya yönelik adımlarda size yol gösterir. Her bir adımla ilgili ayrıntılar için, akış çizelgesinin altındaki tabloya bakın.

![Bağlayıcının hatalarını ayıklama adımlarını gösteren akış çizelgesi](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Eylem | Açıklama | 
|---------|---------|---------|
|1 | Uygulamaya atanan bağlayıcı grubunu bulma | Büyük olasılıkla birden çok sunucuya yüklenmiş bir bağlayıcıya sahipsiniz, bu durumda bağlayıcılar [bağlayıcı gruplarına atanmalıdır](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Bağlayıcı grupları hakkında daha fazla bilgi edinmek için bkz. [bağlayıcı gruplarını kullanarak ayrı ağlarda ve konumlarda uygulama yayımlama](application-proxy-connector-groups.md). |
|2 | Bağlayıcıyı yükleyip bir grup atayın | Yüklü bir Bağlayıcınız yoksa, bkz. [bağlayıcı yükleme ve kaydetme](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Bağlayıcıyı yükleme konusunda sorun yaşıyorsanız, bkz. [bağlayıcıyı yükleme sorunu](application-proxy-connector-installation-problem.md).<br></br> Bağlayıcı bir gruba atanmamışsa, bkz. [bağlayıcıyı bir gruba atama](application-proxy-connector-groups.md#create-connector-groups).<br></br>Uygulama bir bağlayıcı grubuna atanmamışsa, bkz. [uygulamayı bir bağlayıcı grubuna atama](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Bağlayıcı sunucusunda bir bağlantı noktası testi çalıştırma | Bağlayıcı sunucusunda, 443 ve 80 bağlantı noktalarının açık olup olmadığını denetlemek için [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) veya diğer bir bağlantı noktası test aracı kullanarak bir bağlantı noktası testi çalıştırın.|
|4 | Etki alanlarını ve bağlantı noktalarını yapılandırma | [Etki alanlarınızın ve bağlantı noktalarınızın doğru yapılandırıldığından emin olun](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Bağlayıcının düzgün çalışması için, açık olması gereken bazı bağlantı noktaları ve sunucunuzun erişebilmesi gereken URL 'Ler vardır. |
|5 | Bir arka uç proxy 'sinin kullanımda olup olmadığını denetleyin | Bağlayıcıların arka uç proxy sunucuları kullanıp kullanmalarını veya onları atlayıp atlamalarını denetleyin. Ayrıntılar için bkz. [bağlayıcı proxy sorunlarını giderme ve hizmet bağlantısı sorunları](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Bağlayıcıyı ve Güncelleştirici 'yi arka uç ara sunucusunu kullanacak şekilde güncelleştirme | Bir arka uç ara sunucusu kullanılıyorsa, bağlayıcının aynı proxy 'yi kullanmakta olduğundan emin olmak isteyeceksiniz. Ara sunucularla çalışacak bağlayıcılar sorunlarını giderme ve yapılandırma hakkında ayrıntılı bilgi için bkz. [mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Uygulamanın iç URL 'sini bağlayıcı sunucusuna yükleme | Bağlayıcı sunucusunda, uygulamanın iç URL 'sini yükleyin. |
|8 | İç ağ bağlantısını denetle | İç ağınızda bu hata ayıklama akışının tanılamamasının bir bağlantı sorunu var. Bağlayıcıların çalışması için uygulamanın dahili olarak erişilebilir olması gerekir. Bağlayıcı olay günlüklerini, [uygulama proxy bağlayıcıları](application-proxy-connectors.md#under-the-hood)bölümünde açıklandığı gibi etkinleştirebilir ve görüntüleyebilirsiniz. |
|9 | Arka uçtaki zaman aşımı değerini uzatın | Uygulamanız için **ek ayarlar** ' da, **arka uç uygulama zaman aşımı** ayarını **uzun**olarak değiştirin. Bkz. [Azure AD 'ye şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Sorun devam ederse, belirli akış sorunlarını hedefleyin, uygulama ve SSO hata ayıklama akışlarını gözden geçirin | [Hata ayıklama uygulama proxy 'si uygulama sorunlarını](application-proxy-debug-apps.md) giderme akışı ' nı kullanın. |

## <a name="next-steps"></a>Sonraki adımlar


* [Bağlayıcı gruplarını kullanarak ayrı ağlarda ve konumlarda uygulama yayımlama](application-proxy-connector-groups.md)
* [Mevcut şirket içi proxy sunucularıyla çalışma](application-proxy-configure-connectors-with-proxy-servers.md)
* [Uygulama proxy 'Si ve bağlayıcı hatalarında sorun giderme](application-proxy-troubleshoot.md)
* [Azure AD Uygulama Ara Sunucusu bağlayıcısını sessizce yüklemek](application-proxy-register-connector-powershell.md)
