---
title: Uygulama proxy 'Si ile uygulama erişimi içinde derleme esnekliği
description: Şirket içi uygulamalara dayanıklı erişim için uygulama proxy 'Si kullanarak mimarlar ve BT yöneticileri için kılavuz
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
ms.openlocfilehash: 8fed78d7d2250d749ced7fe343689df76329b60d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724669"
---
# <a name="build-resilience-in-application-access-with-application-proxy"></a>Uygulama proxy 'Si ile uygulama erişimi içinde derleme esnekliği

Uygulama proxy 'Si, kullanıcıların uzak bir istemciden şirket içi Web uygulamalarına erişmesini sağlayan bir Azure AD özelliğidir. Uygulama proxy 'Si, hem bulutta hem de uygulama proxy 'si hizmetini ve şirket içi sunucuda çalışan uygulama proxy bağlayıcılarını içerir. 

Kullanıcılar, uygulama proxy 'si aracılığıyla yayınlanan bir URL aracılığıyla şirket içi kaynaklara erişir. Azure AD oturum açma sayfasına yönlendirilir. Azure AD 'deki uygulama proxy 'Si hizmeti daha sonra şirket ağındaki uygulama proxy Bağlayıcısı 'na bir belirteç gönderir ve kimliği doğrulanmış kullanıcı daha sonra şirket içi kaynağa erişebilir Active Directory. Aşağıdaki diyagramda [Bağlayıcılar](../manage-apps/application-proxy-connectors.md) bir [bağlayıcı grubunda](../manage-apps/application-proxy-connector-groups.md)gösterilmiştir.

> [!IMPORTANT]
> Uygulamalarınızı uygulama proxy 'Si aracılığıyla yayımladığınızda, [uygulama proxy bağlayıcıları için kapasite planlaması ve uygun artıklık](../manage-apps/application-proxy-connectors.md#capacity-planning)uygulamanız gerekir.

![Uygulama y 'nin mimari diyagramı](./media/resilience-on-prem-access/admin-resilience-app-proxy.png))

## <a name="how-do-i-implement-application-proxy"></a>Uygulama proxy 'Si Nasıl yaparım? mi?

Azure AD Uygulama Ara Sunucusu ile uzaktan erişim uygulamak için aşağıdaki kaynaklara bakın.

* [Uygulama Ara Sunucusu dağıtımını planlama](../manage-apps/application-proxy-deployment-plan.md)

* [Yüksek kullanılabilirlik ve yük dengeleme en iyi uygulamaları](../manage-apps/application-proxy-high-availability-load-balancing.md)

* [Proxy sunucularını yapılandırma](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)

* [Esnek erişim denetimi stratejisi tasarlama](../authentication/concept-resilient-controls.md)

## <a name="next-steps"></a>Sonraki adımlar
Yöneticiler ve Mimarlar için esnekliği kaynakları
 
* [Kimlik bilgisi yönetimiyle derleme esnekliği](resilience-in-credentials.md)

* [Cihaz durumlarıyla esnekliği derleme](resilience-with-device-states.md)

* [Sürekli erişim değerlendirmesi (CAE) kullanarak esnekliği oluşturma](resilience-with-continuous-access-evaluation.md)

* [Dış Kullanıcı kimlik doğrulamasında esnekliği derleme](resilience-b2b-authentication.md)

* [Karma kimlik doğrulamalarınız içinde esnekliği oluşturma](resilience-in-hybrid.md)

Geliştiriciler için esnekliği kaynakları

* [Uygulamalarınızda ıAM esnekliği oluşturma](resilience-app-development-overview.md)

* [CıHAR sistemlerinizde derleme esnekliği](resilience-b2c.md)