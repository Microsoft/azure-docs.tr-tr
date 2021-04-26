---
title: Yerel iş ortağı hesaplarını, B2B kullanıcıları olarak buluta eşitleyin-Azure AD
description: Azure AD B2B işbirliğiyle aynı kimlik bilgilerini kullanarak yerel ve bulut kaynaklarına yerel olarak yönetilen dış iş ortakları için erişim verin.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99575932"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Azure AD B2B işbirliğini kullanarak yerel olarak yönetilen iş ortağı hesaplarına bulut kaynakları için erişim verme

Azure Active Directory (Azure AD) öncesinde, şirket içi kimlik sistemlerine sahip kuruluşların şirket içi dizininde geleneksel olarak yönetilen iş ortağı hesapları vardır. Böyle bir kuruluşta, uygulamaları Azure AD 'ye taşımaya başladığınızda, iş ortaklarınızın ihtiyaç duydukları kaynaklara erişebildiklerinden emin olmak istersiniz. Kaynakların şirket içinde mi yoksa bulutta mı olduğuna bakılmaksızın. Ayrıca, iş ortağı kullanıcılarınızın hem şirket içi hem de Azure AD kaynakları için aynı oturum açma kimlik bilgilerini kullanmasını istiyorsunuz. 

Şirket içi dizininizde dış iş ortaklarınız için hesaplar oluşturursanız (örneğin, partners.contoso.com etki alanında gülşen Moran adında bir dış Kullanıcı için "wmoran" adlı bir oturum açma adı olan bir hesap oluşturursanız, bu hesapları artık buluta eşitleyebilirsiniz. Özellikle, iş ortağı hesaplarını buluta eşitlemek için Azure AD Connect kullanabilirsiniz. Bu, UserType = konuğa sahip bir kullanıcı hesabı oluşturur. Bu, iş ortağı kullanıcılarınızın, yerel hesaplarıyla aynı kimlik bilgilerini kullanarak bulut kaynaklarına erişmesini sağlar ve bunlara gereksinimlerinden daha fazla erişim izni vermez.

> [!NOTE]
> Ayrıca bkz. [dahili KULLANıCıLARı B2B işbirliğiyle davet](invite-internal-users.md)etme. Bu özellikle, şirket içi dizininizdeki hesaplarını buluta eşitlemenizden bağımsız olarak, dahili Konuk kullanıcılarını B2B işbirliğinin kullanımına davet edebilirsiniz. Kullanıcı B2B işbirliği kullanma davetini kabul ettikten sonra, kendilerine erişmesini istediğiniz kaynaklarda oturum açmak için kendi kimliklerini ve kimlik bilgilerini kullanabilir. Parola korumanız veya hesap yaşam döngülerini yönetmeniz gerekmez.

## <a name="identify-unique-attributes-for-usertype"></a>UserType için benzersiz öznitelikleri tanımla

UserType özniteliğinin eşitlemesini etkinleştirmeden önce, önce şirket içi Active Directory özniteliğini nasıl türetireceğinize karar vermelisiniz. Diğer bir deyişle, şirket içi ortamınızdaki hangi parametreler dış ortak çalışanlarla benzersizdir? Bu dış ortak çalışanları kendi kuruluşunuzun üyelerinden ayıran bir parametre belirleme.

Bunun için iki yaygın yaklaşım şunlardır:

- Kaynak öznitelik olarak kullanmak için kullanılmamış bir şirket içi Active Directory özniteliği (örneğin, extensionAttribute1) belirleyin. 
- Alternatif olarak, diğer özelliklerden UserType özniteliği için değeri türetebilirsiniz. Örneğin, şirket içi Active Directory UserPrincipalName özniteliği etki alanı *\@ Partners.contoso.com* ile sona erdiğinde tüm kullanıcıları Konuk olarak eşitlemeniz gerekir.
 
Ayrıntılı öznitelik gereksinimleri için bkz. [UserType eşitlemesini etkinleştirme](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype). 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Kullanıcıları buluta eşitlemek için Azure AD Connect yapılandırma

Benzersiz özniteliği tanımladıktan sonra, bu kullanıcıları buluta eşitlemek üzere Azure AD Connect yapılandırabilirsiniz. Bu, UserType = konuğa sahip bir kullanıcı hesabı oluşturur. Bir yetkilendirme noktasından bu kullanıcılar, Azure AD B2B işbirliği daveti aracılığıyla oluşturulan B2B kullanıcılarından ayırt edilemez.

Uygulama yönergeleri için bkz. [UserType eşitlemesini etkinleştirme](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype).

## <a name="next-steps"></a>Sonraki adımlar

- [Karma kuruluşlar için B2B işbirliği Azure Active Directory](hybrid-organizations.md)
- [Azure AD’de B2B kullanıcılarına şirket içi uygulamalarınıza erişim verme](hybrid-cloud-to-on-premises.md)
- Azure AD Connect genel bir bakış için bkz. Şirket [içi dizinlerinizi Azure Active Directory Ile tümleştirme](../hybrid/whatis-hybrid-identity.md).

