---
title: Azure Active Directory cihaz durumlarını kullanarak esnekliği oluşturma
description: Mimarlar ve BT yöneticilerinin cihaz durumlarını kullanarak esnekliği oluşturma kılavuzu
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d476be7a417cfc31cca76d3409074aaaa281a56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724619"
---
# <a name="build-resilience-with-device-states"></a>Cihaz durumlarıyla esnekliği derleme

Yöneticiler, Azure AD ile [cihaz durumlarını](../devices/overview.md) etkinleştirerek, cihaz durumuna bağlı olarak uygulamalara erişimi denetleyen [koşullu erişim ilkeleri](../conditional-access/overview.md) yazabilir. Cihazların eklenen avantajı, kaynaklara erişim için güçlü kimlik doğrulama gereksinimlerini karşılamasının yanı sıra ek MFA kimlik doğrulama isteklerini azalttığından ve dayanıklılığı iyileştirmektir. 

Aşağıdaki akış grafiğinde, cihazları Azure AD 'ye eklemek için cihaz durumlarını etkinleştiren farklı yollar sunulmaktadır. Kuruluşunuzda birden çok tane kullanabilirsiniz.

![cihaz durumlarını seçmek için akış grafiği](./media/resilience-with-device-states/admin-resilience-devices.png)

[Cihaz durumları](../devices/overview.md)kullandığınızda, kullanıcılar çoğu durumda, [birincil yenileme belirteci](../devices/concept-primary-refresh-token.md) (prt) aracılığıyla kaynaklarda çoklu oturum açma deneyimi sağlar. PRT, Kullanıcı ve cihaz hakkında talepler içerir ve cihazdan uygulamalara erişmek için kimlik doğrulama belirteçleri almak için kullanılabilir. PRT 14 gün için geçerlidir ve Kullanıcı cihazı etkin bir şekilde kullandığı sürece, kullanıcılara dayanıklı bir deneyim sağlayarak sürekli olarak yenilenir. Bir PRT, çeşitli yollarla çok faktörlü kimlik doğrulama talebi de alabilir. Daha fazla bilgi için bkz. [BIR dut ne zaman BIR MFA talebi alır](../devices/concept-primary-refresh-token.md).

## <a name="how-do-device-states-help"></a>Cihaz durumları nasıl yardımcı olur?

Bir bir uygulamaya erişim istemek için bir PRT kullanıldığında, cihazı, oturumu ve MFA taleplerine Azure AD tarafından güvenililir. Yöneticiler cihaz tabanlı bir denetim veya Multi-Factor Authentication denetimi gerektiren ilkeler oluştururken, ilke gereksinimi, çok faktörlü kimlik doğrulaması denenmeden cihaz durumuyla karşılanır. Kullanıcılar aynı cihazda ek Multi-Factor Authentication istemlerini görmez. Bu, Azure MFA hizmetinin kesintiye uğramasını veya yerel telekomünikasyon sağlayıcıları gibi bağımlılıklarını esnekliği artırır.

## <a name="how-do-i-implement-device-states"></a>Cihaz durumları Nasıl yaparım? mi?

* Şirkete ait Windows cihazlarına yönelik [karma Azure AD 'ye katılmış](../devices/hybrid-azuread-join-plan.md) ve [Azure AD JOIN](../devices/azureadjoin-plan.md) 'i etkinleştirin ve mümkünse birleştirilmeleri gerekir. Mümkün değilse, kaydolmaları gerekir.

  Kuruluşunuzda Windows 'un eski sürümleri varsa, bu cihazları Windows 10 ' u kullanacak şekilde yükseltin.

* PRT kullanarak Web uygulamalarına sorunsuz SSO 'yu destekleyen [desteklenen](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji) [uzantılara](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb) sahip [Microsoft Edge](/deployedge/microsoft-edge-security-identity) veya Google Chrome 'ı kullanmak için Kullanıcı tarayıcı erişimini standartlaştırın.

* Kişisel veya şirkete ait iOS ve Android cihazlar için [Microsoft Authenticator uygulamasını](../user-help/user-help-auth-app-overview.md)dağıtın. Multi-Factor Authentication ve parola-Less oturum açma özelliklerine ek olarak, Microsoft Authenticator uygulaması, son kullanıcılar için daha az kimlik doğrulama istemiyle [aracılı kimlik doğrulama](../develop/msal-android-single-sign-on.md) aracılığıyla yerel uygulama genelinde çoklu oturum açmayı etkinleştirecektir.

* Kişisel veya şirkete ait iOS ve Android cihazlar için, daha az kimlik doğrulama istekleriyle şirket kaynaklarına güvenli bir şekilde erişmek üzere [mobil uygulama yönetimi](/mem/intune/apps/app-management) 'ni kullanır. 

* [Apple cihazları (Önizleme) Için Microsoft ENTERPRISE SSO eklentisini kullanın](../develop/apple-sso-plugin.md). Bu, cihazı kaydeder ve tarayıcı ve yerel Azure AD uygulamaları arasında SSO sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
Yöneticiler ve Mimarlar için esnekliği kaynakları
 
* [Kimlik bilgisi yönetimiyle derleme esnekliği](resilience-in-credentials.md)

* [Sürekli erişim değerlendirmesi (CAE) kullanarak esnekliği oluşturma](resilience-with-continuous-access-evaluation.md)

* [Dış Kullanıcı kimlik doğrulamasında esnekliği derleme](resilience-b2b-authentication.md)

* [Karma kimlik doğrulamalarınız içinde esnekliği oluşturma](resilience-in-hybrid.md)

* [Uygulama proxy 'Si ile uygulama erişimi içinde derleme esnekliği](resilience-on-premises-access.md)


Geliştiriciler için esnekliği kaynakları

* [Uygulamalarınızda ıAM esnekliği oluşturma](resilience-app-development-overview.md)

* [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)