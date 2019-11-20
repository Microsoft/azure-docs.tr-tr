---
title: Azure açık senaryolarındaki kiracılar, roller ve kullanıcılar
description: Azure Active Directory kiracılar, kullanıcılar ve roller kavramlarını ve bunların Azure Use senaryolarında nasıl kullanılabileceğini anlayın.
ms.date: 11/05/2019
ms.topic: overview
ms.openlocfilehash: 73c5cd592f07a23edaad23796e498ea9243c5d26
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131350"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Azure açık senaryolarındaki kiracılar, roller ve kullanıcılar

[Azure 'un Temsilcili kaynak yönetimine](azure-delegated-resource-management.md)yönelik olarak müşterileri eklemeden önce, Azure Active Directory (Azure AD) kiracılarının, kullanıcıların ve rollerinin nasıl çalıştığını ve Azure açık thouse senaryolarında nasıl kullanılabileceğini anlamak önemlidir.

*Kiracı* , Azure AD 'nin adanmış ve güvenilir bir örneğidir. Genellikle, her kiracı tek bir kuruluşu temsil eder. Azure Temsilcili kaynak yönetimi, kaynakların bir kiracıdan başka bir kiracıya mantıksal olarak projeksiyonunu sağlar. Bu, yönetim kiracısındaki (bir hizmet sağlayıcısına ait olan) kullanıcıların, bir müşterinin kiracısındaki Temsilcili kaynaklara erişmesine olanak sağlar veya [birden çok kiracıya sahip kuruluşların yönetim işlemlerini merkezileştirmelerini](enterprise.md)sağlar.

Bu mantıksal projeksiyonu başarmak için, müşteri kiracısındaki abonelik (veya bir veya daha fazla kaynak grubu), Azure tarafından yetkilendirilen kaynak yönetimi için *eklendi* olmalıdır. Bu ekleme işlemi, [Azure Resource Manager şablonlar aracılığıyla](../how-to/onboard-customer.md) veya [Azure Market 'e genel veya özel bir teklif yayınlayarak](../how-to/publish-managed-services-offers.md)yapılabilir.

Seçtiğiniz ekleme yöntemine ne olursa *yetkilendirmeler*tanımlamanız gerekecektir. Her yetkilendirme, yönetim kiracısında, temsilcili kaynaklara erişimi olacak bir kullanıcı hesabı ve bu kullanıcıların her birinin bu kaynaklar için sahip olacağı izinleri ayarlayan yerleşik bir rol belirler.

## <a name="role-support-for-azure-delegated-resource-management"></a>Azure tarafından atanan kaynak yönetimi için rol desteği

Bir yetkilendirme tanımlarken, her kullanıcı hesabına [rol tabanlı erişim denetimi (RBAC) yerleşik rollerinin](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)biri atanmalıdır. Özel roller ve [Klasik abonelik yöneticisi rolleri](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) desteklenmez.

Aşağıdaki özel durumlarla birlikte, tüm [yerleşik roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) Şu anda Azure tarafından atanan kaynak yönetimi ile desteklenmektedir:

- [Sahip](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) rolü desteklenmiyor.
- [Dataactions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) iznine sahip yerleşik roller desteklenmez.
- [Kullanıcı erişimi Yöneticisi](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) yerleşik rolü desteklenir, ancak yalnızca [Müşteri kiracısında yönetilen bir kimliğe rol atamanın](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)sınırlı amacı için geçerlidir. Genellikle bu rol tarafından verilen başka hiçbir izin uygulanmaz. Bu rolle bir Kullanıcı tanımlarsanız, bu kullanıcının yönetilen kimliklere atayabileceği yerleşik rolleri de belirtmeniz gerekir.

## <a name="best-practices-for-defining-users-and-roles"></a>Kullanıcıları ve rolleri tanımlamaya yönelik en iyi uygulamalar

Yetkilendirmeleri oluştururken aşağıdaki en iyi yöntemleri öneririz:

- Çoğu durumda, tek bir kullanıcı hesabı serisi yerine bir Azure AD kullanıcı grubuna veya hizmet sorumlusuna izin atamak isteyeceksiniz. Bu, erişim gereksinimleriniz değiştiğinde planı güncelleştirmek ve yeniden yayınlamak zorunda kalmadan bireysel kullanıcılar için erişim eklemenize veya kaldırmanıza olanak sağlar.
- Kullanıcıların yalnızca işini tamamlaması için gerekli izinlere sahip olması için en az ayrıcalık ilkesini izlediğinizden emin olun, böylece yanlışlıkla oluşan hatalar olasılığını azaltmaya yardımcı olur. Daha fazla bilgi için bkz. [Önerilen güvenlik uygulamaları](../concepts/recommended-security-practices.md).
- Gerekirse, daha sonra [temsilciye erişimi kaldırabilmeniz için](../how-to/onboard-customer.md#remove-access-to-a-delegation) , [yönetilen hizmetler kayıt ataması silme rolüne](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) sahip bir kullanıcı ekleyin. Bu rol atanmamışsa, atanan kaynaklar yalnızca müşterinin kiracısındaki bir kullanıcı tarafından kaldırılabilir.
- [Azure Portal müşterileri sayfasını görüntülemesi](../how-to/view-manage-customers.md) gereken tüm kullanıcıların [okuyucu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) rolüne (veya okuyucu erişimi de içeren başka bir yerleşik Role) sahip olduğundan emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- Azure tarafından [atanan kaynak yönetimi için önerilen güvenlik uygulamaları](recommended-security-practices.md)hakkında bilgi edinin.
- Müşterilerinizi [Azure Resource Manager şablonları kullanarak](../how-to/onboard-customer.md) veya [Azure Market 'e özel veya genel olarak yönetilen bir hizmet teklifi yayımlayarak](../how-to/publish-managed-services-offers.md), Azure tarafından atanan kaynak yönetimine ekleyin.
