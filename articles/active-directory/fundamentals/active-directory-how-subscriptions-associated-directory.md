---
title: Kiracınıza mevcut bir Azure aboneliği ekleme-Azure AD
description: Azure Active Directory (Azure AD) kiracısına mevcut bir Azure aboneliğinin nasıl ekleneceği hakkında yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperf-fy20q4
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ac9553660aace8242c81b41fa2cc9171d28219
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594640"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory kiracınıza bir Azure aboneliğini ekleme veya ilişkilendirme

Azure aboneliğinin Azure Active Directory (Azure AD) ile bir güven ilişkisi vardır. Abonelik, kullanıcıların, hizmetlerin ve cihazların kimliğini doğrulamak için Azure AD 'ye güvenir.

Birden çok abonelik aynı Azure AD dizinine güvenebilir. Her abonelik yalnızca tek bir dizine güvenebilir.

Bir veya daha fazla Azure aboneliği, Azure hizmetlerine karşı güvenlik sorumluları ve cihazların kimliğini doğrulamak ve yetkilendirmek için bir Azure Active Directory (Azure AD) örneğiyle bir güven ilişkisi kurabilir.  Bir aboneliğin süresi dolarsa, Azure AD hizmeti 'nin güvenilir örneği kalır, ancak güvenlik sorumluları Azure kaynaklarına erişimi kaybeder.

Bir Kullanıcı bir Microsoft bulut hizmetine kaydolduğunda yeni bir Azure AD kiracısı oluşturulur ve Kullanıcı genel yönetici rolünün bir üyesi yapılır. Ancak, bir aboneliğin sahibi aboneliğini mevcut bir kiracıya katıldığında, sahip genel yönetici rolüne atanmaz.

Tüm kullanıcılarınızın kimlik doğrulaması için tek bir *giriş* dizini vardır. Kullanıcılarınız diğer dizinlerde da konuk olabilir. Azure AD 'de her bir kullanıcı için hem ev hem de Konuk dizinleri görebilirsiniz.

> [!Important]
> Bir aboneliği farklı bir dizinle ilişkilendirdiğinizde, [Azure rol tabanlı erişim denetimi](../../role-based-access-control/role-assignments-portal.md) kullanılarak atanmış rollere sahip kullanıcılar erişimleri kaybeder. Hizmet Yöneticisi ve Ortak Yöneticiler dahil olmak üzere klasik abonelik yöneticileri de erişimi kaybeder.
>
> Azure Kubernetes Service (AKS) kümenizi farklı bir aboneliğe taşımak veya küme sahibi aboneliği yeni bir kiracıya taşımak, kayıp rol atamaları ve hizmet sorumlusunun hakları nedeniyle kümenin işlevselliği kaybetmesine neden olur. AKS hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (aks)](../../aks/index.yml).

## <a name="before-you-begin"></a>Başlamadan önce

Aboneliğinizi ilişkilendirebilmeniz veya ekleyebilmek için önce aşağıdaki görevleri yapın:

- Aboneliğinizi ilişkilendirdikten veya ekledikten sonra gerçekleşecek ve nasıl etkilenebileceğiniz aşağıdaki değişiklikler listesini gözden geçirin:

  - Azure RBAC kullanılarak roller atanmış olan kullanıcılar, erişimleri kaybeder.
  - Hizmet Yöneticisi ve Co-Administrators erişimi kaybedecektir.
  - Herhangi bir Anahtar Kasası varsa, bunlar erişilemez olur ve ilişkilendirmeden sonra bunları çözmeniz gerekir.
  - Sanal makineler veya Logic Apps gibi kaynaklar için yönetilen kimlikleriniz varsa, ilişkilendirmeden sonra yeniden etkinleştirmeniz veya yeniden oluşturmanız gerekir.
  - Kayıtlı bir Azure Stack varsa, ilişkilendirmeden sonra yeniden kaydetmeniz gerekir.
  - Daha fazla bilgi için bkz. [Azure aboneliğini farklı bir Azure AD dizinine aktarma](../../role-based-access-control/transfer-subscription.md).

- Şunları içeren bir hesap kullanarak oturum açın:

  - , Abonelik için bir [sahip](../../role-based-access-control/built-in-roles.md#owner) rolü atamasına sahiptir. Sahip rolünü atama hakkında daha fazla bilgi için bkz. [Azure Portal kullanarak Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md).
  - Hem geçerli dizinde hem de yeni dizinde bulunur. Geçerli dizin abonelikle ilişkili. Yeni dizini abonelikle ilişkilendireceğiz. Başka bir dizine erişim elde etme hakkında daha fazla bilgi için, [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../external-identities/add-users-administrator.md)bölümüne bakın.

- Bir Azure bulut hizmeti sağlayıcıları (CSP) aboneliği (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), bir Microsoft Iç aboneliği (MS-AZR-0015P) veya Öğrenciler için Microsoft Azure Başlangıç Paketi aboneliği (MS-AZR-0144P) kullandığınızdan emin olun.

## <a name="associate-a-subscription-to-a-directory"></a>Aboneliği bir dizinle ilişkilendir<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Mevcut bir aboneliği Azure AD dizininizle ilişkilendirmek için şu adımları izleyin:

1. Oturum açın ve [Azure Portal abonelikler sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)kullanmak istediğiniz aboneliği seçin.

1. **Dizini Değiştir**' i seçin.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png" alt-text="Dizin Değiştir seçeneğinin vurgulandığı abonelikler sayfasını gösteren ekran görüntüsü.":::

1. Görüntülenen tüm uyarıları gözden geçirin ve ardından **Değiştir**' i seçin.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png" alt-text="Dizin sayfasını örnek bir dizinle değiştirme ve Değiştir düğmesi vurgulanmış şekilde gösteren ekran görüntüsü.":::

   Abonelik için Dizin değiştirildikten sonra, başarılı bir ileti alırsınız.

1. Yeni dizininize gitmek için abonelik sayfasında **dizinleri Değiştir** ' i seçin.

   :::image type="content" source="media/active-directory-how-subscriptions-associated-directory/directory-switcher.png" alt-text="Örnek bilgilerle Dizin değiştirici sayfasını gösteren ekran görüntüsü.":::

   Her şeyin düzgün şekilde gösterilmesi birkaç saat sürebilir. Çok uzun sürüyor görünüyorsa **genel abonelik filtresini** kontrol edin. Taşınan aboneliğin gizlenmediğinden emin olun. Yeni dizini görmek için Azure portal oturumunuzu kapatıp yeniden açmanız gerekebilir.

Abonelik dizininin değiştirilmesi, hizmet düzeyi bir işlemdir, bu nedenle Abonelik Faturalandırma sahipliğini etkilemez. Özgün dizini silmek için, Abonelik Faturalandırma sahipliğini yeni bir hesap yöneticisine aktarmanız gerekir. Faturalama sahipliğini aktarma hakkında daha fazla bilgi edinmek için bkz. [Azure aboneliğinin sahipliğini başka bir hesaba aktarma](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>İlişkilendirme sonrası adımları

Bir aboneliği farklı bir dizine ilişkilendirdikten sonra, işlemleri sürdürmek için aşağıdaki görevleri gerçekleştirmeniz gerekebilir:

- Herhangi bir Anahtar Kasası varsa, Anahtar Kasası kiracı KIMLIĞINI değiştirmeniz gerekir. Daha fazla bilgi için bkz. [bir abonelik taşıdıktan sonra Anahtar Kasası KIRACı kimliğini değiştirme](../../key-vault/general/move-subscription.md).

- Kaynaklar için sistem tarafından atanan Yönetilen kimlikler kullandıysanız, bu kimlikleri yeniden etkinleştirmeniz gerekir. Kullanıcı tarafından atanan Yönetilen kimlikler kullandıysanız, bu kimlikleri yeniden oluşturmanız gerekir. Yönetilen kimlikleri yeniden etkinleştirdikten veya yeniden oluşturduktan sonra, bu kimliklere atanan izinleri yeniden oluşturmanız gerekir. Daha fazla bilgi için bkz. [Azure kaynakları için yönetilen kimlikler nedir?](../managed-identities-azure-resources/overview.md).

- Bu aboneliği kullanarak bir Azure Stack kaydolduysanız, yeniden kaydetmeniz gerekir. Daha fazla bilgi için bkz. [Azure ile Azure Stack hub 'ı kaydetme](/azure-stack/operator/azure-stack-registration).

- Daha fazla bilgi için bkz. [Azure aboneliğini farklı bir Azure AD dizinine aktarma](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Sonraki adımlar

- Yeni bir Azure AD kiracısı oluşturmak için bkz. [hızlı başlangıç: Azure Active Directory yeni kiracı oluşturma](active-directory-access-create-new-tenant.md).

- Microsoft Azure kaynak erişimini nasıl denetlediği hakkında daha fazla bilgi edinmek için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Azure AD 'de rol atama hakkında daha fazla bilgi için, bkz. [Azure Active Directory kullanıcılara yönetici ve yönetici olmayan roller atama](active-directory-users-assign-role-azure-portal.md).
