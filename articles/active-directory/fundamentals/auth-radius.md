---
title: Azure Active Directory ile RADIUS kimlik doğrulaması
description: Azure Active Directory ile RADIUS kimlik doğrulamasını elde etmeye yönelik mimari rehberlik.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97c524e1f4c05787f1dd61dea5a463e8fa83511a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168636"
---
# <a name="radius-authentication-with-azure-active-directory"></a>Azure Active Directory ile RADIUS kimlik doğrulaması

Arayan Kullanıcının Uzaktan Kimliğini Doğrulama Hizmeti (YARıÇAP), çevirmeli kullanıcıların merkezi kimlik doğrulaması ve yetkilendirmesini etkinleştirerek bir ağın güvenliğini sağlayan bir ağ protokolüdür. Birçok uygulama, kullanıcıların kimliğini doğrulamak için RADIUS protokolüne hala güvenir.

Microsoft Windows Server, bir RADIUS sunucusu olarak görev yapacak ve RADIUS kimlik doğrulamasını destekleyen ağ Ilkesi sunucusu (NPS) adlı bir role sahiptir.

Azure Active Directory (Azure AD), RADIUS tabanlı sistemlerle Multi-Factor Authentication 'ı sunar. Bir müşteri, daha önce bahsedilen RADIUS iş yüklerinden herhangi birine Azure AD Multi-Factor Authentication uygulamak isterse, Azure AD Multi-Factor Authentication NPS uzantısını Windows NPS sunucusuna yükleyebilir. 

Windows NPS sunucusu bir kullanıcının kimlik bilgilerini Active Directory doğrular ve sonra Multi-Factor Authentication isteği Azure 'a gönderir. Böylece Kullanıcı, mobil kimlik doğrulayıcısında bir zorluk alır. Başarılı olduktan sonra istemci uygulamanın hizmete bağlanmasına izin verilir. 

## <a name="use-when"></a>Şu durumlarda kullanın: 

Şunun gibi uygulamalara Multi-Factor Authentication eklemeniz gerekir
* bir sanal özel ağ (VPN)
* WiFi erişimi
* Uzak Masaüstü Ağ Geçidi (RDG)
* Sanal Masaüstü Altyapısı (VDı)
* Kullanıcılara kimlik doğrulaması yapmak için RADIUS protokolüne bağlı olan diğerleri. 

> [!NOTE]
> VPN iş yüklerine Azure AD Multi-Factor Authentication uygulamak için RADIUS ve Azure AD Multi-Factor Authentication NPS uzantısının güvenmek yerine, VPN 'nizi SAML 'ye yükseltmenizi ve VPN 'nizi doğrudan Azure AD ile Federasyonu etkinleştirmenizi öneririz. Bu, VPN 'nize koşullu erişim, Multi-Factor Authentication, cihaz uyumluluğu ve kimlik koruması gibi Azure AD korumasının tam kapsamını verir.

![mimari diyagram](./media/authentication-patterns/radius-auth.png)


## <a name="components-of-the-system"></a>Sistemin bileşenleri 

* **İstemci uygulaması (VPN istemcisi)**: RADIUS istemcisine kimlik doğrulama isteği gönderir.

* **RADIUS istemcisi**: istemci uygulamasından gelen istekleri dönüştürür ve NPS uzantısının yüklü olduğu RADIUS sunucusuna gönderir.

* **RADIUS sunucusu**: RADIUS isteği için birincil kimlik doğrulamasını gerçekleştirmek üzere Active Directory ile bağlanır. Başarılı olduğunda, isteği Azure AD Multi-Factor Authentication NPS uzantısına geçirir.

* **NPS uzantısı**: ikincil kimlik doğrulaması Için BIR Azure AD Multi-Factor Authentication isteği tetikler. Başarılı olursa, NPS uzantısı, Azure 'un güvenlik belirteci hizmeti tarafından verilen Multi-Factor Authentication talebi içeren güvenlik belirteçleri ile RADIUS sunucusunu sağlayarak kimlik doğrulama isteğini tamamlar.

* **Azure ad Multi-Factor Authentication**: kullanıcının ayrıntılarını almak IÇIN Azure AD ile iletişim kurar ve Kullanıcı tarafından yapılandırılan bir doğrulama yöntemi kullanarak ikincil kimlik doğrulaması gerçekleştirir.

## <a name="implement-radius-with-azure-ad"></a>Azure AD ile RADIUS uygulama 

* [NPS kullanarak Azure AD Multi-Factor Authentication olanakları sağlama](../authentication/howto-mfa-nps-extension.md) 

* [Azure AD Multi-Factor Authentication NPS uzantısını yapılandırma](../authentication/howto-mfa-nps-extension-advanced.md) 

* [NPS uzantısını kullanarak Azure AD Multi-Factor Authentication ile VPN](../authentication/howto-mfa-nps-extension-vpn.md) 

  
‎ 

