---
title: Azure API Management geliştirici portalına genel bakış
titleSuffix: Azure API Management
description: API tüketicilerinin API 'lerinizi keşfedebileceği, API Management-özelleştirilebilir bir Web sitesindeki geliştirici portalı hakkında bilgi edinin.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3f5da5ed6c828278eb25a9fa4fa5cfb90e16f8f4
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815968"
---
# <a name="overview-of-the-developer-portal"></a>Geliştirici portalına genel bakış

Geliştirici portalı, API 'lerinizin belgelerinde otomatik olarak oluşturulan ve tamamen özelleştirilebilir bir Web sitesidir. API tüketicilerinin API 'lerinizi bulabileceği, bunları nasıl kullanacağınızı, erişim isteyeceğini ve bunları nasıl deneyebileceği de vardır.

Bu makalede tanıtıldığında, geliştirici portalını belirli senaryolarınız için özelleştirebilir ve genişletebilirsiniz. 

![API Management geliştirici portalı](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="migration-from-the-legacy-portal"></a>Eski portaldan geçiş

> [!IMPORTANT]
> Eski geliştirici portalı artık kullanım dışıdır ve yalnızca güvenlik güncelleştirmelerini alacaktır. Bu hizmeti, her zamanki gibi kullanmaya devam edebilirsiniz, bu, tüm API Management hizmetlerinden kaldırılacak olan 2023 Ekim 'de devre dışı kalır.

Yeni geliştirici portalına geçiş, [adanmış belgeler makalesinde](developer-portal-deprecated-migration.md)açıklanmaktadır.

## <a name="customization-and-styling"></a>Özelleştirme ve stil oluşturma

Geliştirici portalı, yerleşik, sürükle ve bırak görsel Düzenleyicisi aracılığıyla özelleştirilebilir ve Stillenebilir. Daha fazla bilgi için [Bu öğreticiye](api-management-howto-developer-portal-customize.md) bakın.

## <a name="extensibility"></a><a name="managed-vs-self-hosted"></a> Genişletilebilirlik

API Management hizmetiniz yerleşik, her zaman güncel ve **yönetilen** bir geliştirici portalı içerir. Azure portal arabiriminden erişebilirsiniz.

Kullanıma hazır olmayan, özel mantık ile genişletmeniz gerekiyorsa, kod temelini değiştirebilirsiniz. Portalın kod temeli [bir GitHub deposunda mevcuttur](https://github.com/Azure/api-management-developer-portal). Örneğin, bir üçüncü taraf destek sistemiyle tümleşen yeni bir pencere öğesi uygulayabilirsiniz. Yeni işlevsellik uyguladığınızda, aşağıdaki seçeneklerden birini seçebilirsiniz:

- Elde edilen portalı API Management hizmetinizin dışında **kendi kendine barındırın** . Portalı kendinden barındırdığınızda, bakımcı olur ve yükseltmelerinden sorumlu olursunuz. Azure desteğinin yardımı, yalnızca [Şirket içinde barındırılan portalların temel kurulumuyla](developer-portal-self-host.md)sınırlıdır.
- Yeni işlevselliği **yönetilen** portalın kod tabanına birleştirmek için API Management ekibine yönelik bir çekme isteği açın.

Genişletilebilirlik ayrıntıları ve yönergeleri için [GitHub deposuna](https://github.com/Azure/api-management-developer-portal) ve [pencere öğesi uygulamak](developer-portal-implement-widgets.md)için öğreticiye bakın. [Yönetilen portalı özelleştirme](api-management-howto-developer-portal-customize.md) öğreticisi, portalın yönetim panelinde, **yönetilen** ve **Şirket içinde barındırılan** sürümler için ortak olan bir adım adım yol gösterir.


## <a name="next-steps"></a>Sonraki adımlar

Yeni geliştirici portalı hakkında daha fazla bilgi edinin:

- [Yönetilen geliştirici portalına erişme ve bunları özelleştirme](api-management-howto-developer-portal-customize.md)
- [Portalın şirket içinde barındırılan sürümünü ayarlama](developer-portal-self-host.md)
- [Kendi pencere öğesini uygulama](developer-portal-implement-widgets.md)

Diğer kaynaklara gözatamazsınız:

- [Kaynak kodlu GitHub deposu](https://github.com/Azure/api-management-developer-portal)
- [Geliştirici portalı hakkında sık sorulan sorular](developer-portal-faq.md)
