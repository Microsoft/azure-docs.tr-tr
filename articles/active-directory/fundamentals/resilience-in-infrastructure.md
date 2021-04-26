---
title: Azure Active Directory ile ıAM altyapınızda esnekliği oluşturun
description: Mimarların ve BT yöneticilerinin ıAM altyapısının kesintiye uğramaması için esnekliği oluşturma kılavuzu.
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
ms.openlocfilehash: 64fe4b8c217ec46cbb6dd046339c3ac65eebb121
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724686"
---
# <a name="build-resilience-in-your-identity-and-access-management-infrastructure"></a>Kimlik ve erişim yönetimi altyapınızda esnekliği oluşturma

Azure Active Directory, kuruluşunuzun Kaynaklarına yönelik kimlik doğrulaması ve yetkilendirme gibi kritik hizmetler sağlayan küresel bir bulut kimliği ve erişim yönetimi sistemidir. Bu belgede, Azure Active Directory (Azure AD) kullanan kaynaklar için kimlik doğrulama veya yetkilendirme hizmetlerinden kaynaklanan riskleri anlamak, eklemek ve azaltmak için kullanabileceğiniz yönergeler sunulmaktadır. 

Belge kümesi için tasarlandı

* Kimlik mimarları

* Kimlik hizmeti sahipleri

* Kimlik Işlemleri takımları

Lütfen [uygulama geliştiricileri](./resilience-app-development-overview.md) ve [Azure AD B2C sistemleri](resilience-b2c.md)için belgelere bakın.

## <a name="what-is-resilience"></a>Esnekliği nedir?

Kimlik altyapınızın bağlamında, esnekliği kimlik doğrulaması ve yetkilendirme gibi hizmetlerde veya iş, Kullanıcı ve işlemlerinize en düşük düzeyde etkilenmeden veya diğer bileşenlerin başarısızlığından vazgeçmek mümkün değildir. Kesintiden olumsuz bir etkisi ciddi olabilir ve esnekliği, dilsiz planlamayı gerektirir.

## <a name="why-worry-about-disruption"></a>Neden kesintiye uğramayın?

Her bir çağrının bileşeni başarısız olursa, kimlik doğrulama sistemine yapılan her çağrı kesintiye uğraar. Kimlik doğrulaması kesintiye uğradığında, temeldeki bileşen hatalarından dolayı kullanıcılarınız uygulamalarına erişemez. Bu nedenle, kimlik doğrulama çağrılarının sayısını ve bu çağrılarındaki bağımlılıkların sayısını azaltmak, esnekliği için önemlidir. Uygulama geliştiricileri, belirteçlerin ne sıklıkta istendiğine ilişkin bir denetim sağlayabilir. Örneğin, geliştiricilerle birlikte çalışarak uygulamalar için mümkün olan yerlerde Azure AD yönetilen kimliklerini kullandığından emin olun. 

Azure AD gibi belirteç tabanlı bir kimlik doğrulama sisteminde, bir kullanıcının uygulaması (istemci) bir uygulamaya veya başka bir kaynağa erişmeden önce kimlik sisteminden bir güvenlik belirteci almalıdır. Geçerlilik süresi boyunca, bir istemci uygulamaya erişmek için aynı belirteci birden çok kez sunabilir.

Uygulamaya sunulan belirtecin süresi dolarsa, uygulama belirteci reddeder ve istemcinin Azure AD 'den yeni bir belirteç edinmesi gerekir. Yeni bir belirteç almak, kimlik bilgileri istemleri gibi kullanıcı etkileşimini veya kimlik doğrulama sisteminin diğer gereksinimlerini karşılamayı gerektirir. Daha uzun süreli belirteçlerle yapılan kimlik doğrulama çağrılarının sıklığını azaltmak gereksiz etkileşimleri azaltır. Ancak, belirteç ömrünü daha az ilke değerlendirmesi tarafından oluşturulan riske göre dengeetmeniz gerekir. Belirteç yaşam sürelerini yönetme hakkında daha fazla bilgi için, bu makaleye bkz. yeniden [kimlik doğrulama istemlerini iyileştirme](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

## <a name="ways-to-increase-resilience"></a>Esnekliği artırma yolları
Aşağıdaki diyagramda esnekliği artırabileceğiniz altı somut yol gösterilmektedir. Her yöntem, bu makalenin sonraki adımlar bölümünde bağlantılı makalelerdeki ayrıntılı olarak açıklanır.
  
![Admin esnekliği genel bakışını gösteren diyagram](./media/resilience-in-infrastructure/admin-resilience-overview.png)

## <a name="next-steps"></a>Sonraki adımlar
Yöneticiler ve Mimarlar için esnekliği kaynakları
 
* [Kimlik bilgisi yönetimiyle derleme esnekliği](resilience-in-credentials.md)

* [Cihaz durumlarıyla esnekliği derleme](resilience-with-device-states.md)

* [Sürekli erişim değerlendirmesi (CAE) kullanarak esnekliği oluşturma](resilience-with-continuous-access-evaluation.md)

* [Dış Kullanıcı kimlik doğrulamasında esnekliği derleme](resilience-b2b-authentication.md)

* [Karma kimlik doğrulamalarınız içinde esnekliği oluşturma](resilience-in-hybrid.md)

* [Uygulama proxy 'Si ile uygulama erişimi içinde derleme esnekliği](resilience-on-premises-access.md)

Geliştiriciler için esnekliği kaynakları

* [Uygulamalarınızda ıAM esnekliği oluşturma](resilience-app-development-overview.md)

* [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)