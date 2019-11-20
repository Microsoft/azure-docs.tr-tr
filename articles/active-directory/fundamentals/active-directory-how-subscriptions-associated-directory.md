---
title: Mevcut bir Azure aboneliği, kiracınıza - Azure Active Directory Ekle | Microsoft Docs
description: Azure Active Directory kiracınız için mevcut bir Azure aboneliği ekleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2f61792235d13ef801cffb3a69c32df54f058a9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073587"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Azure Active Directory kiracınız için Azure aboneliği ekleme veya ilişkilendirme

Azure aboneliğinin Azure Active Directory (Azure AD) ile bir güven ilişkisi vardır. Abonelik, kullanıcıların, hizmetlerin ve cihazların kimliğini doğrulamak için Azure AD 'ye güvenir.

Birden çok abonelik aynı Azure AD dizinine güvenebilir. Her abonelik yalnızca tek bir dizine güvenebilir.

Aboneliğinizin süresi dolarsa abonelikle ilişkili diğer kaynaklara erişimlerini kaybeder. Ancak Azure AD dizini Azure 'da kalır. Farklı bir Azure aboneliği kullanarak dizini ilişkilendirebilir ve yönetebilirsiniz.

Tüm kullanıcılarınızın kimlik doğrulaması için tek bir *giriş* dizini vardır. Kullanıcılarınız diğer dizinlerde da konuk olabilir. Azure AD'de her kullanıcı için her iki bir ev ve Konuk dizin görebilirsiniz.

> [!Important]
> Bir aboneliği farklı bir dizinle ilişkilendirdiğinizde, [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/role-assignments-portal.md) kullanılarak atanmış rollere sahip kullanıcılar erişimleri kaybeder. Hizmet Yöneticisi ve ortak yöneticiler dahil olmak üzere klasik abonelik yöneticileri de erişimi kaybeder.
>
> Abonelik farklı bir dizinle ilişkilendirildiğinde, ilke atamaları da bir abonelikten kaldırılır.
>
> Azure Kubernetes Service (AKS) kümenizi farklı bir aboneliğe taşımak veya küme sahibi aboneliği yeni bir kiracıya taşımak, kayıp rol atamaları ve hizmet sorumlusunun hakları nedeniyle kümenin işlevselliği kaybetmesine neden olur. AKS hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (aks)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Başlamadan önce

Aboneliğinizi ilişkilendirebilmeniz veya ekleyebilmek için önce aşağıdaki görevleri yapın:

- Aşağıdaki değişiklik listesini ve nasıl etkilenebileceğini gözden geçirin:

  - RBAC kullanılarak roller atanmış olan kullanıcılar, erişimini kaybeder
  - Hizmet Yöneticisi ve ortak yöneticiler erişimi kaybedecektir
  - Herhangi bir Anahtar Kasası varsa, bunlar erişilemez olur ve ilişkilendirmeden sonra bunları çözmeniz gerekir
  - Sanal makineler veya Logic Apps gibi kaynaklar için yönetilen kimlikleriniz varsa, ilişkilendirmeden sonra yeniden etkinleştirmeniz veya yeniden oluşturmanız gerekir
  - Kayıtlı bir Azure Stack varsa ilişkilendirmeden sonra yeniden kaydetmeniz gerekir

- Bir hesap kullanarak oturum açın:

  - , Abonelik için bir [sahip](../../role-based-access-control/built-in-roles.md#owner) rolü atamasına sahiptir. Sahip rolünü atama hakkında daha fazla bilgi için bkz. [RBAC ve Azure Portal kullanarak Azure kaynaklarına erişimi yönetme](../../role-based-access-control/role-assignments-portal.md).
  - Hem geçerli dizinde hem de yeni dizinde bulunur. Geçerli dizin abonelikle ilişkili. Yeni dizini abonelikle ilişkilendireceğiz. Başka bir dizine erişim elde etme hakkında daha fazla bilgi için, [Azure portal Azure ACTIVE DIRECTORY B2B işbirliği kullanıcıları ekleme](../b2b/add-users-administrator.md)bölümüne bakın.

- Bir Azure bulut hizmeti sağlayıcıları (CSP) aboneliği (MS-AZR - 0145 P, MS - AZR - 0146 P, MS - AZR - 159 P), bir Microsoft Internal abonelik (MS-AZR - 0015 P) veya Microsoft Imagine aboneliği (MS-AZR - 0144 P) kullanmadığınızdan emin olun.

## Aboneliği bir dizinle ilişkilendir<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Mevcut bir aboneliği Azure AD dizininizle ilişkilendirmek için şu adımları izleyin:

1. Oturum açın ve kullanmak istediğiniz aboneliği seçin [Azure portalındaki abonelikler sayfasından](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Seçin **dizinini Değiştir**.

    ![Abonelik sayfasında, dizin seçeneği vurgulanmış olarak değiştir](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Görüntülenen ve ardından tüm uyarıları gözden **değişiklik**.

    ![Dizini değiştirmek için gösteren dizin sayfası Değiştir](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Abonelik için dizinin değiştirilir ve bir başarı iletisi alın.

    ![Dizin değişikliği hakkında başarı iletisi](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Yeni dizininize gitmek için **Switch dizinini** kullanın. Her şeyin düzgün şekilde gösterilmesi birkaç saat sürebilir. Çok uzun sürüyor görünüyorsa **genel abonelik filtresini**kontrol edin. Taşınan aboneliğin gizlenmediğinden emin olun. Yeni dizini görmek için Azure portal oturumunuzu kapatıp yeniden açmanız gerekebilir.

![Örnek bilgilerle Dizin değiştirici sayfası](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Abonelik faturalandırma sahipliğini etkilemez abonelik dizininin değiştirilmesi bir hizmet düzeyi işlemi olduğundan. Hesap Yöneticisi hala Hizmet Yöneticisi'nden değiştirebilirsiniz [hesap Merkezi](https://account.azure.com/subscriptions). Özgün dizini silmek için, Abonelik Faturalandırma sahipliğini yeni bir hesap yöneticisine aktarmanız gerekir. Faturalama sahipliğini aktarma hakkında daha fazla bilgi edinmek için bkz. [Azure aboneliğinin sahipliğini başka bir hesaba aktarma](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>İlişkilendirme sonrası adımları

Bir aboneliği farklı bir dizine ilişkilendirdikten sonra, işlemleri sürdürmek için aşağıdaki görevleri gerçekleştirmeniz gerekebilir:

- Herhangi bir Anahtar Kasası varsa, Anahtar Kasası kiracı KIMLIĞINI değiştirmeniz gerekir. Daha fazla bilgi için bkz. [bir abonelik taşıdıktan sonra Anahtar Kasası KIRACı kimliğini değiştirme](../../key-vault/key-vault-subscription-move-fix.md).

- Kaynaklar için sistem tarafından atanan Yönetilen kimlikler kullandıysanız, bu kimlikleri yeniden etkinleştirmeniz gerekir. Kullanıcı tarafından atanan Yönetilen kimlikler kullandıysanız, bu kimlikleri yeniden oluşturmanız gerekir. Yönetilen kimlikleri yeniden etkinleştirdikten veya yeniden oluşturduktan sonra, bu kimliklere atanan izinleri yeniden oluşturmanız gerekir. Daha fazla bilgi için [Azure kaynakları için yönetilen kimlikleri nedir?](../managed-identities-azure-resources/overview.md).

- Bu aboneliği kullanarak bir Azure Stack kaydolduysanız, yeniden kaydetmeniz gerekir. Daha fazla bilgi için bkz. [Azure ile Azure Stack kaydetme](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Sonraki adımlar

- Yeni bir Azure AD kiracısı oluşturmak için bkz. [hızlı başlangıç: Azure Active Directory yeni kiracı oluşturma](active-directory-access-create-new-tenant.md).

- Microsoft Azure kaynak erişimini nasıl denetlediği hakkında daha fazla bilgi edinmek için bkz. [Klasik abonelik yöneticisi rolleri, Azure RBAC rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Azure AD 'de rol atama hakkında daha fazla bilgi için, bkz. [Azure Active Directory kullanıcılara yönetici ve yönetici olmayan roller atama](active-directory-users-assign-role-azure-portal.md).
