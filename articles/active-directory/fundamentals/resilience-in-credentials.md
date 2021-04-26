---
title: Azure Active Directory 'da kimlik bilgisi yönetimiyle derleme esnekliği
description: Mimarların ve BT yöneticilerinin dayanıklı bir kimlik bilgisi stratejisi oluşturma kılavuzu.
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
ms.openlocfilehash: 399d2f71fa20d63dce89cf3be5c12ffd63264895
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724719"
---
# <a name="build-resilience-with-credential-management"></a>Kimlik bilgisi yönetimiyle derleme esnekliği

Bir belirteç isteğindeki Azure AD 'ye bir kimlik bilgisi sunulursa, doğrulama için kullanılabilir olması gereken birden çok bağımlılık vardır. İlk kimlik doğrulama faktörü, Azure AD kimlik doğrulamasını ve şirket içi altyapıdaki bazı durumlarda temel alır. Karma kimlik doğrulama mimarileri hakkında daha fazla bilgi için bkz. [karma altyapınızda derleme esnekliği](resilience-in-hybrid.md). 

İkinci bir faktör uygularsanız, ikinci faktörün bağımlılıkları ilk için bağımlılıklara eklenir. Örneğin, ilk faktörün PTA ile olması ve ikinci faktörü SMS ise, bağımlılıklarınız şunlardır:

* Azure AD kimlik doğrulama hizmetleri

* Azure MFA hizmeti

* Şirket içi altyapı

* Telefon taşıyıcısı

* Kullanıcının cihazı (görünmez)

 
![Kimlik doğrulama yöntemlerinin ve bağımlılıkların görüntüsü](./media/resilience-in-credentials/admin-resilience-credentials.png)

Kimlik bilgisi stratejiniz her bir kimlik doğrulama türünün bağımlılıklarını göz önünde bulundurmalı ve tek bir hata noktasını önleyebileceğiniz Yöntemler sağlamalıdır. 

Kimlik doğrulama yöntemlerinin farklı bağımlılıkları olduğundan, kullanıcıların mümkün olduğunca fazla ikinci faktör seçeneği için kaydolmanızı sağlamak iyi bir fikirdir. Mümkünse, farklı bağımlılıklarla ikinci faktörleri eklediğinizden emin olun. Örneğin, sesli çağrı ve SMS as Second faktörleri aynı bağımlılıkları paylaşır; bu nedenle, tek seçenekler riski azaltmaz.

En dayanıklı kimlik bilgisi stratejisi, passwordless kimlik doğrulaması kullanmaktır. Iş için Windows Hello ve FIDO 2,0 güvenlik anahtarlarının, iki ayrı faktörle güçlü kimlik doğrulamasından kıyasla daha az bağımlılığı vardır. Microsoft Authenticator uygulaması, Iş için Windows Hello ve Fido 2,0 güvenlik anahtarları en güvenli seçenektir. 

İkinci etkenler için, Microsoft Authenticator uygulaması veya zaman tabanlı bir saat geçiş kodu (TOTP) veya OATH Donanım belirteçleri kullanan diğer kimlik doğrulayıcı uygulamaları en az bağımlılıklara sahiptir ve bu nedenle daha dayanıklı bir uygulamadır.

## <a name="how-do-multiple-credentials-help-resilience"></a>Birden çok kimlik bilgisi nasıl esnekliği?

Birden çok kimlik bilgisi türünün sağlanması, kullanıcılara tercihleri ve çevresel kısıtlamalarını sunan seçenekler sağlar. Sonuç olarak, kullanıcıların Multi-Factor Authentication tarafından istenen etkileşimli kimlik doğrulaması, istek sırasında kullanılamayan belirli bağımlılıklara daha dayanıklı olacaktır. [Multi-Factor Authentication için yeniden kimlik doğrulama istemlerini iyileştirebilirsiniz](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

Kuruluşlar, yukarıda açıklanan bireysel kullanıcı dayanıklılığına ek olarak, bir yanlış yapılandırma, doğal bir olağanüstü durum veya şirket genelinde bir kaynak kesinti (özellikle çok faktörlü kimlik doğrulaması için kullanıldığında) sunan işletimsel hatalar gibi büyük ölçekli kesintiler için de planlıyor olmalıdır. 

## <a name="how-do-i-implement-resilient-credentials"></a>Nasıl yaparım? dayanıklı kimlik bilgilerini uygulasın mı?

* Bağımlılıkları azaltmak için Iş için Windows Hello, telefon kimlik doğrulaması ve FIDO2 güvenlik anahtarları gibi [parolasız kimlik bilgilerini](../authentication/howto-authentication-passwordless-deployment.md) dağıtın.

* [Microsoft Authenticator uygulamasını](../user-help/user-help-auth-app-overview.md) ikinci bir faktör olarak dağıtın.

* Windows Server Active Directory eşitlenen karma hesaplar için [Parola karması eşitlemesini](../hybrid/whatis-phs.md) açın. Bu seçenek, AD FS gibi Federasyon Hizmetleri ile etkinleştirilebilir ve Federasyon hizmetinin başarısız olması durumunda geri dönüş sağlar.

* Kullanıcıların deneyimini geliştirmek için [Multi-Factor Authentication yöntemlerinin kullanımını çözümleyin](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/) .

* [Esnek erişim denetimi stratejisi uygulama](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Sonraki adımlar
Yöneticiler ve Mimarlar için esnekliği kaynakları
 
* [Cihaz durumlarıyla esnekliği derleme](resilience-with-device-states.md)

* [Sürekli erişim değerlendirmesi (CAE) kullanarak esnekliği oluşturma](resilience-with-continuous-access-evaluation.md)

* [Dış Kullanıcı kimlik doğrulamasında esnekliği derleme](resilience-b2b-authentication.md)

* [Karma kimlik doğrulamalarınız içinde esnekliği oluşturma](resilience-in-hybrid.md)

* [Uygulama proxy 'Si ile uygulama erişimi içinde derleme esnekliği](resilience-on-premises-access.md)

Geliştiriciler için esnekliği kaynakları

* [Uygulamalarınızda ıAM esnekliği oluşturma](resilience-app-development-overview.md)

* [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)