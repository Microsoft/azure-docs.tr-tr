---
title: Uygulama proxy 'Si aracı bağlayıcısını yükleme sorunu | Microsoft Docs
description: Uygulama proxy 'Si aracı bağlayıcısını yüklerken karşılaşabileceğiniz sorunları giderme
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
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35558b81d064680981bcf403a3584e3a3d00e4f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311751"
---
# <a name="problem-installing-the-application-proxy-agent-connector"></a>Uygulama Ara Sunucusu Aracı Bağlayıcısı’nı yüklerken sorun oluşuyor

Microsoft AAD uygulama proxy Bağlayıcısı, bulut kullanılabilir uç noktadan iç etki alanına bağlantı kurmak için giden bağlantıları kullanan bir iç etki alanı bileşenidir.

## <a name="general-problem-areas-with-connector-installation"></a>Bağlayıcı yüklemesiyle ilgili genel sorun alanı

Bağlayıcının yüklemesi başarısız olduğunda, kök neden genellikle aşağıdaki alanlardan biridir:

1.  **Bağlantı** – başarılı bir yükleme işleminin tamamlanabilmesi için yeni bağlayıcının sonraki güven özelliklerini kaydetmesi ve kurması gerekir. Bu, AAD uygulama proxy 'Si bulut hizmetine bağlanarak yapılır.

2.  **Güven kurulumu** – yeni bağlayıcı, otomatik olarak imzalanan bir sertifika oluşturur ve bulut hizmetine kaydeder.

3.  **Yöneticinin kimlik doğrulaması** – yükleme sırasında, kullanıcının bağlayıcı yüklemesini tamamlaması için yönetici kimlik bilgilerini sağlaması gerekir.

> [!NOTE]
> Bağlayıcı yükleme günlükleri,% TEMP% klasöründe bulunabilir ve yükleme hatasına neden olan sorun hakkında ek bilgi sağlanmasına yardımcı olabilir.

## <a name="verify-connectivity-to-the-cloud-application-proxy-service-and-microsoft-login-page"></a>Cloud Application Proxy hizmeti ve Microsoft oturum açma sayfasıyla bağlantıyı doğrulama

**Amaç:** Bağlayıcı makinenin AAD uygulama proxy 'Si kayıt uç noktasına ve Microsoft oturum açma sayfasına bağlanabildiğini doğrulayın.

1.  Bağlayıcı sunucusunda, 443 ve 80 bağlantı noktalarının açık olduğunu doğrulamak için [Telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) veya diğer bağlantı noktası test aracı kullanarak bir bağlantı noktası testi çalıştırın.

2.  Bu bağlantı noktalarından herhangi biri başarılı olmazsa, güvenlik duvarının veya arka uç proxy 'sinin gerekli etki alanlarına ve bağlantı noktalarına erişimi olduğunu doğrulayın, Şirket [içi ortamınızı hazırlayın](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

3.  Bir tarayıcı (ayrı bir sekme) açın ve şu Web sayfasına gidin: <https://login.microsoftonline.com>, bu sayfada oturum açabildiğinizden emin olun.

## <a name="verify-machine-and-backend-components-support-for-application-proxy-trust-cert"></a>Uygulama proxy 'Si güven sertifikası için makine ve arka uç bileşenleri desteğini doğrulama

**Amaç:** Bağlayıcı makinesinin, arka uç proxy 'sinin ve güvenlik duvarının, daha sonra güven için bağlayıcı tarafından oluşturulan sertifikayı destekleyebildiğini doğrulayın.

>[!NOTE]
>Bağlayıcı, TLS 1.2 tarafından desteklenen bir SHA512 olur sertifikası oluşturmaya çalışır. Makine veya arka uç güvenlik duvarı ve proxy TLS 1.2 'yi desteklemiyorsa, yükleme başarısız olur.
>
>

**Sorunu çözmek için:**

1.  Makinenin TLS 1.2 'yi desteklediğini doğrulayın – 2012 R2 'den sonraki tüm Windows sürümleri TLS 1,2 ' i desteklemelidir. Bağlayıcı makineniz 2012 R2 veya daha önceki bir sürümden yüklüyse, şu KBs 'lerin makinede yüklü olduğundan emin olun: <https://support.microsoft.com/help/2973337/sha512-is-disabled-in-windows-when-you-use-tls-1.2>

2.  Ağ yöneticinize başvurun ve arka uç proxy 'sinin ve güvenlik duvarının giden trafik için SHA512 olur engellemediğinden emin olun.

## <a name="verify-admin-is-used-to-install-the-connector"></a>Bağlayıcıyı yüklemek için yöneticinin kullanıldığını doğrulayın

**Amaç:** Bağlayıcıyı yüklemeye çalışan kullanıcının doğru kimlik bilgilerine sahip bir yönetici olduğunu doğrulayın. Şu anda, yüklemenin başarılı olması için kullanıcının en az bir uygulama Yöneticisi olması gerekir.

**Kimlik bilgilerinin doğru olduğunu doğrulamak için:**

@No__t-0 ' a bağlanın ve aynı kimlik bilgilerini kullanın. Oturum açmanın başarılı olduğundan emin olun. **Azure Active Directory** - @ no__t-2 **kullanıcıları ve grupları** - @ No__t-5 **tüm kullanıcılar**' a giderek Kullanıcı rolünü kontrol edebilirsiniz. 

Kullanıcı hesabınızı ve ardından ortaya çıkan menüdeki "Dizin rolü" nü seçin. Seçili rolün "uygulama Yöneticisi" olduğunu doğrulayın. Bu adımlarla ilgili sayfaların hiçbirine erişemiyorsanız, gerekli rolünüzün olması gerekmez.

## <a name="next-steps"></a>Sonraki adımlar
[Azure AD Uygulama Ara Sunucusu bağlayıcılarını anlama](application-proxy-connectors.md)
