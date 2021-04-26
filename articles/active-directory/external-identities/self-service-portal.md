---
title: B2B işbirliği için self servis kaydolma portalı-Azure AD
description: Azure Active Directory B2B kullanıcılarına kuruluşunuzun ihtiyaçlarına uyacak şekilde ekleme iş akışını özelleştirmeyi öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a5b800e78448afcc970010535ba12b543d3cc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860516"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Azure AD B2B işbirliği kaydı için self servis

Müşteriler, son kullanıcılar için [Azure portalı](https://portal.azure.com) ve [Uygulama Erişim Paneli](https://myapps.microsoft.com) aracılığıyla ortaya çıkan yerleşik özelliklerle birçok işlem yapabilir. Ancak B2B kullanıcıları için ekleme iş akışını, kuruluşunuzun gereksinimlerine uyacak şekilde özelleştirmeniz gerekebilir.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>B2B Konuk Kullanıcı kaydı için Azure AD Yetkilendirme Yönetimi

Davet edilen bir kuruluş olarak, tek tek dış çalışanların kaynaklarınıza erişmesi gereken sürenin önünde haberdar olmayabilirsiniz. İş ortağı şirketlerinden kullanıcıların denetlediğiniz ilkelerle kaydolmalarına yönelik bir yönteme ihtiyacınız vardır. Diğer kuruluşlardan kullanıcıların erişim istemesine ve Grup, uygulamalar ve SharePoint Online sitelerine atanmasını sağlamak istiyorsanız, [dış kullanıcılara erişimi yöneten](../governance/entitlement-management-external-users.md#how-access-works-for-external-users)ilkeleri yapılandırmak IÇIN [Azure AD Yetkilendirme Yönetimi](../governance/entitlement-management-overview.md) ' ni kullanabilirsiniz.

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B davet API 'SI

Kuruluşlar, B2B Konuk kullanıcıları için kendi ekleme deneyimlerini derlemek üzere [Microsoft Graph davet YÖNETICISI API](/graph/api/resources/invitation) 'sini kullanabilir. Self Servis B2B Konuk Kullanıcı kaydı sunmak istediğinizde [Azure AD yetkilendirme yönetimini](../governance/entitlement-management-overview.md)kullanmanızı öneririz. Ancak kendi deneyiminizi derlemek isterseniz, özelleştirilmiş davet e-postanızı doğrudan B2B kullanıcısına otomatik olarak göndermek için [davet oluşturma API](/graph/api/invitation-post?tabs=http) 'sini kullanabilirsiniz. Ya da uygulamanız, davet edilen kullanıcıya kendi davetinizi (iletişim mekanizmanız aracılığıyla) oluşturmak için oluşturma yanıtında döndürülen davet edilen Teredeemurl 'Yi kullanabilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
* [Dış Kimlikler fiyatlandırması](external-identities-pricing.md)
* [Azure Active Directory B2B işbirliği hakkında sık sorulan sorular (SSS)](faq.md)
