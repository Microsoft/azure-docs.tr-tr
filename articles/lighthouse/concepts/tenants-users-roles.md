---
title: Azure açık senaryolarındaki kiracılar, kullanıcılar ve roller
description: Azure Active Directory kiracılar, kullanıcılar ve roller kavramlarını ve bunların Azure Use senaryolarında nasıl kullanılabileceğini anlayın.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: d78828cc739030f8e456c64885d77ddf59dd13fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233925"
---
# <a name="tenants-users-and-roles-in-azure-lighthouse-scenarios"></a>Azure açık senaryolarındaki kiracılar, kullanıcılar ve roller

Azure 'u açık bir şekilde [kullanmaya](../overview.md)başlamadan önce, Azure Active Directory (Azure AD) kiracılarının, kullanıcıların ve rollerinin nasıl çalıştığını ve Azure açık thouse senaryolarında nasıl kullanılabileceğini anlamak önemlidir.

*Kiracı* , Azure AD 'nin adanmış ve güvenilir bir örneğidir. Genellikle, her kiracı tek bir kuruluşu temsil eder. [Azure Temsilcili kaynak yönetimi](azure-delegated-resource-management.md) , kaynakların bir kiracıdan başka bir kiracıya mantıksal olarak projeksiyonunu sağlar. Bu, yönetim kiracısındaki (bir hizmet sağlayıcısına ait olan) kullanıcıların, bir müşterinin kiracısındaki Temsilcili kaynaklara erişmesine olanak sağlar veya [birden çok kiracıya sahip kuruluşların yönetim işlemlerini merkezileştirmelerini](enterprise.md)sağlar.

Bu mantıksal projeksiyonu başarmak için, müşteri kiracısındaki bir abonelik (veya bir abonelik içindeki bir veya daha fazla kaynak grubu), Azure açık *eklendi* olmalıdır. Bu ekleme işlemi, [Azure Resource Manager şablonlar aracılığıyla](../how-to/onboard-customer.md) veya [Azure Market 'e genel veya özel bir teklif yayınlayarak](../how-to/publish-managed-services-offers.md)yapılabilir.

Seçtiğiniz ekleme yöntemine ne olursa *yetkilendirmeler* tanımlamanız gerekecektir. Her yetkilendirme, temsilcili kaynaklara erişimi olacak bir **PrincipalId** ve bu kullanıcıların her birinin bu kaynaklar için sahip olacağı izinleri ayarlayan yerleşik bir rol belirler. Bu **PrincipalId** , yönetim kiracısında BIR Azure AD kullanıcısı, grubu veya hizmet sorumlusu tanımlar.

> [!NOTE]
> Açıkça belirtilmedikçe, Azure Mathouse belgelerindeki bir "kullanıcıya" başvurular bir Azure AD kullanıcısına, grubuna veya hizmet sorumlusuna bir yetkilendirmede uygulanabilir.

## <a name="best-practices-for-defining-users-and-roles"></a>Kullanıcıları ve rolleri tanımlamaya yönelik en iyi uygulamalar

Yetkilendirmeleri oluştururken aşağıdaki en iyi yöntemleri öneririz:

- Çoğu durumda, tek bir kullanıcı hesabı serisi yerine bir Azure AD kullanıcı grubuna veya hizmet sorumlusuna izin atamak isteyeceksiniz. Bu, erişim gereksinimleriniz değiştiğinde planı güncelleştirmek ve yeniden yayınlamak zorunda kalmadan bireysel kullanıcılar için erişim eklemenize veya kaldırmanıza olanak sağlar.
- Kullanıcıların yalnızca işini tamamlaması için gerekli izinlere sahip olması için en az ayrıcalık ilkesini izlediğinizden emin olun, böylece yanlışlıkla oluşan hatalar olasılığını azaltmaya yardımcı olur. Daha fazla bilgi için bkz. [Önerilen güvenlik uygulamaları](../concepts/recommended-security-practices.md).
- Gerekirse, daha sonra [temsilciye erişimi kaldırabilmeniz için](../how-to/remove-delegation.md) , [yönetilen hizmetler kayıt ataması silme rolüne](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) sahip bir kullanıcı ekleyin. Bu rol atanmamışsa, atanan kaynaklar yalnızca müşterinin kiracısındaki bir kullanıcı tarafından kaldırılabilir.
- [Azure Portal müşterileri sayfasını görüntülemesi](../how-to/view-manage-customers.md) gereken tüm kullanıcıların [okuyucu](../../role-based-access-control/built-in-roles.md#reader) rolüne (veya okuyucu erişimi de içeren başka bir yerleşik Role) sahip olduğundan emin olun.

> [!IMPORTANT]
> Bir Azure AD grubu için izinler eklemek üzere, **Grup türü** **güvenlik** olarak ayarlanmalıdır. Grup oluşturulduğunda bu seçenek seçilidir. Daha fazla bilgi için bkz. [temel Grup oluşturma ve Azure Active Directory kullanarak üye ekleme](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="role-support-for-azure-lighthouse"></a>Azure ışıklı kullanım için rol desteği

Bir yetkilendirme tanımlarken, her kullanıcı hesabına [Azure yerleşik rollerinin](../../role-based-access-control/built-in-roles.md)biri atanmalıdır. Özel roller ve [Klasik abonelik yöneticisi rolleri](../../role-based-access-control/classic-administrators.md) desteklenmez.

Aşağıdaki özel durumlarla birlikte, tüm [yerleşik roller](../../role-based-access-control/built-in-roles.md) Şu anda Azure Kathouse ile desteklenmektedir:

- [Sahip](../../role-based-access-control/built-in-roles.md#owner) rolü desteklenmiyor.
- [Dataactions](../../role-based-access-control/role-definitions.md#dataactions) iznine sahip yerleşik roller desteklenmez.
- [Kullanıcı erişimi Yöneticisi](../../role-based-access-control/built-in-roles.md#user-access-administrator) yerleşik rolü desteklenir, ancak yalnızca [Müşteri kiracısında yönetilen bir kimliğe rol atamanın](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)sınırlı amacı için geçerlidir. Genellikle bu rol tarafından verilen başka hiçbir izin uygulanmaz. Bu rolle bir Kullanıcı tanımlarsanız, bu kullanıcının yönetilen kimliklere atayabileceği yerleşik rolleri de belirtmeniz gerekir.

> [!NOTE]
> Azure 'a yeni bir ilgili yerleşik rol eklendikten sonra, [Azure Resource Manager şablonları kullanarak bir müşteriyi](../how-to/onboard-customer.md)eklerken atanabilir. [Yönetilen bir hizmet teklifi yayımlanırken](../how-to/publish-managed-services-offers.md), yeni eklenen rol Iş Ortağı Merkezi 'nde kullanılabilir hale gelmeden önce bir gecikme olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure ışıklı kullanım için önerilen güvenlik uygulamaları](recommended-security-practices.md)hakkında bilgi edinin.
- [Azure Resource Manager şablonları kullanarak](../how-to/onboard-customer.md) ya da [Azure Market 'e özel veya genel olarak yönetilen bir hizmet teklifi yayımlayarak](../how-to/publish-managed-services-offers.md), müşterilerinizi Azure aydınlathouse 'a ekleyin.
