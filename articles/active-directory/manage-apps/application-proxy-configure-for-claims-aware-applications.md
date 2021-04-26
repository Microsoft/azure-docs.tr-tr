---
title: Talep kullanan uygulamalar-Azure AD Uygulaması Proxy | Microsoft Docs
description: Kullanıcılarınız tarafından güvenli uzaktan erişim için ADFS taleplerini kabul eden şirket içi ASP.NET uygulamalarını yayımlama.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: b90c2d47e70a4f7595ac535d5f8ba9506087eb72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258533"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Uygulama proxy 'sinde talep kullanan uygulamalarla çalışma
[Talep kullanan uygulamalar](/previous-versions/windows/desktop/legacy/bb736227(v=vs.85)) , güvenlik belirteci hizmeti 'NE (STS) yeniden yönlendirme gerçekleştirir. STS, bir belirteç için Exchange 'deki kullanıcıdan kimlik bilgilerini ister ve sonra kullanıcıyı uygulamaya yönlendirir. Uygulama proxy 'Sinin bu yeniden yönlendirmelere çalışması için birkaç yol vardır. Talep kullanan uygulamalar için dağıtımınızı yapılandırmak üzere bu makaleyi kullanın. 

## <a name="prerequisites"></a>Önkoşullar
Talep kullanan uygulamanın yönlendirdiği STS 'nin şirket içi ağınızın dışında kullanılabilir olduğundan emin olun. Bir proxy aracılığıyla veya dış bağlantılara izin vererek, STS 'yi kullanılabilir hale getirebilirsiniz. 

## <a name="publish-your-application"></a>Uygulamanızı yayımlama

1. Uygulamanızı [uygulama proxy 'si Ile yayımlama](application-proxy-add-on-premises-application.md)bölümünde açıklanan yönergelere göre yayımlayın.
2. Portalda uygulama sayfasına gidin ve **Çoklu oturum açma** seçeneğini belirleyin.
3. **Ön** kimlik doğrulama yönteminiz olarak **Azure Active Directory** ' yi seçtiyseniz, **Iç KIMLIK doğrulama yönteminiz** olarak **Azure AD çoklu oturum açma devre dışı** ' yı seçin. **Ön kimlik doğrulama yönteminiz** olarak **PASSTHROUGH** ' i seçerseniz, herhangi bir değişiklik yapmanız gerekmez.

## <a name="configure-adfs"></a>ADFS 'yi yapılandırma

, Talebe duyarlı uygulamalar için ADFS 'yi iki şekilde yapılandırabilirsiniz. İlki özel etki alanları kullanmaktır. İkincisi WS-Federation ' dir. 

### <a name="option-1-custom-domains"></a>Seçenek 1: özel etki alanları

Uygulamalarınızın tüm iç URL 'Leri tam etki alanı adları (FQDN) ise, uygulamalarınız için [özel etki alanları](application-proxy-configure-custom-domain.md) yapılandırabilirsiniz. İç URL 'Lerle aynı olan dış URL 'Ler oluşturmak için özel etki alanlarını kullanın. Dış URL 'niz iç URL 'lerinizle eşleşiyorsa, STS yönlendirmeleri kullanıcılarınızın şirket içinde veya uzak olup olmadığına bakılmaksızın çalışır. 

### <a name="option-2-ws-federation"></a>Seçenek 2: WS-Federation

1. ADFS yönetimi 'ni açın.
2. **Bağlı olan taraf güvenleri**' ne gidin, uygulama proxy 'si ile yayımlamakta olduğunuz uygulamaya sağ tıklayın ve **Özellikler**' i seçin.  

   ![Bağlı olan taraf güvenleri uygulama adı ' na sağ tıklayın-ekran görüntüsü](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Uç **noktalar** sekmesinde, **uç nokta türü** altında, **WS-Federation**' ı seçin.
4. **GÜVENILEN URL** altında, **dış URL** altına uygulama proxy 'sine girdiğiniz URL 'yi girin ve **Tamam**' a tıklayın.  

   ![Uç nokta ekleme-güvenilen URL değeri ayarlama-ekran görüntüsü](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Sonraki adımlar
* [Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama](application-proxy-configure-native-client-application.md)