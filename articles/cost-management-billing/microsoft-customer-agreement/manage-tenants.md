---
title: Microsoft Müşteri Sözleşmesi faturalandırma hesabınızda kiracıları yönetme-Azure
description: Makale, Microsoft Müşteri Sözleşmesi Faturalandırma hesabınızla ilişkili kiracılar anlamanıza ve yönetmenize yardımcı olur.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493167"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Microsoft Müşteri Sözleşmesi faturalandırma hesabınızdaki kiracılar 'ı yönetin

Makale, Microsoft Müşteri Sözleşmesi Faturalandırma hesabınızla ilişkili kiracılar anlamanıza ve yönetmenize yardımcı olur. Faturalandırma ortamınıza güvenli erişim sağlamak için kiracılar yönetmek, abonelikleri aktarmak ve faturalandırma sahipliğini yönetmek için bu bilgileri kullanın.

## <a name="whats-a-tenant"></a>Kiracı nedir?

Kiracı, kuruluşunuzun dijital bir gösterimidir ve öncelikle Microsoft.com gibi bir etki alanıyla ilişkilendirilir. Bu, Azure kaynaklarını ve faturalandırmayı yönetmek için kullanıcılara izin atamanızı sağlayan Azure Active Directory aracılığıyla yönetilen bir ortamdır.

Her kiracı, diğer kiracılardan farklıdır ve ayrıdır, ancak diğer kiracılardan konuk kullanıcıların maliyetlerinizi izlemek ve faturalandırmayı yönetmek üzere kiracınıza erişmesine izin verebilirsiniz.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Kiracılar ve abonelikler faturalandırma hesabıyla nasıl ilişkilidir

Maliyetleri izlemek ve faturalandırmayı yönetmek için Microsoft Müşteri sözleşmenizi (faturalandırma hesabı) kullanırsınız. Her faturalandırma hesabının en az bir faturalandırma profili vardır. Faturalandırma profili faturanızı ve ödeme yönteminizi yönetmenizi sağlar. Her faturalandırma profili, varsayılan olarak bir fatura bölümü içerir. Gerekirse daha ayrıntılı bir düzeyde maliyeti gruplamak, izlemek ve yönetmek için daha fazla fatura bölümü oluşturabilirsiniz.

- Faturalandırma hesabınız tek bir kiracı ile ilişkili. Bu, yalnızca kiracının parçası olan kullanıcıların faturalandırma hesabınıza erişebileceği anlamına gelir.
- Faturalandırma hesabınız için yeni bir Azure aboneliği oluşturduğunuzda, her zaman faturalandırma hesabı kiracınızda oluşturulur. Ancak, abonelikleri diğer kiracılara taşıyabilirsiniz. Ayrıca, diğer kiracılardan Mevcut abonelikleri faturalandırma hesabınıza bağlayabilirsiniz. Gereksinimlerinize bağlı olarak, diğer kiracılarda kaynakları ve abonelikleri korurken, tek bir kiracı aracılığıyla faturalandırma yönetimini merkezi olarak yönetmenize olanak tanır.

Aşağıdaki diyagramda, faturalandırma hesabı ve aboneliklerinin kiracılar ile nasıl bağlandığı gösterilmektedir. Contoso PAYG hesabı kiracı 2 ile ilişkilendirildiğinde contoso MCA faturalandırma hesabı kiracı 1 ile ilişkilendirilir. Contoso 'nun, bu abonelikleri abonelik hesabı üzerinden bu abonelik için ödeme yapmak istediğini varsayalım, aboneliği MCA faturalandırma hesabına bağlamak için bir faturalandırma Sahiplik aktarımı kullanabilirler. Abonelik ve kaynakları hala kiracı 2 ile ilişkilendirilecektir, ancak bunlar MCA faturalandırma hesabının kullanımı için ücretlendirilir.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Örnek faturalandırma hiyerarşisini gösteren diyagram." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Tek bir Microsoft Müşteri anlaşmasındaki birden çok kiracı kapsamındaki abonelikleri yönetme

Faturalandırma sahipleri, faturalandırma hesabı için [uygun izinlere](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) sahip olduklarında abonelikler oluşturabilir. Varsayılan olarak, Microsoft Müşteri Sözleşmesi altında oluşturulan tüm yeni abonelikler Microsoft Müşteri Sözleşmesi kiracısında bulunur.

- Diğer kiracılardan abonelikleri, Microsoft Müşteri Sözleşmesi faturalandırma hesabınıza bağlayabilirsiniz. Bir aboneliğin faturalama sahipliğini almak yalnızca faturalandırma düzenlemesini değiştirir. Hizmet kiracısını veya Azure RBAC rollerini etkilemez.
- Hizmet kiracısındaki abonelik sahibini değiştirmek için, [aboneliği farklı bir Azure Active Directory dizinine](../../role-based-access-control/transfer-subscription.md)aktarmanız gerekir.

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Microsoft Müşteri Sözleşmesi kiracınıza Konuk kullanıcılar ekleme

Farklı bir kiracıya ait faturalandırma sorumluluklarını yönetmek için Microsoft Müşteri Sözleşmesi faturalandırma kiracınıza eklenen kullanıcıların Konuk olarak davet edilmesi gerekir.

Bir kullanıcıyı Konuk olarak davet etmek için, Kullanıcı Azure Active Directory (AD) etki alanından farklı bir etki alanı içeren bir e-posta adresine sahip olmalıdır. Azure AD, Konuk kullanıcısına kimlik doğrulaması için bağlantı içeren bir e-posta gönderir.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Örnek bir e-posta davetini gösteren ekran görüntüsü." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

Bir Kullanıcı Microsoft Müşteri Sözleşmesi kiracısına eklendiğinde [daveti kabul](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)etmelidir.

**Daveti kabul et** bağlantısını seçerken Azure ile kimlik doğrulaması istenir.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Azure ile kimlik doğrulaması yapmak için bir istem gösteren ekran görüntüsü." lightbox="./media/manage-tenants/authenticate.png" :::

Ardından **kabul et**' i seçin.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Daveti kabul etmek için bir istem gösteren ekran görüntüsü." lightbox="./media/manage-tenants/accept-invitation.png" :::

Kabul ettikten sonra, [maliyet yönetimi + faturalandırma kapsamında Microsoft Müşteri Sözleşmesi faturalandırma hesabı ' nı görüntüleyebilirler](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Faturalama hesapları listesinde Microsoft Müşteri anlaşmasını gösteren ekran görüntüsü." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

Konuk kullanıcıları davet etme yetkilendirmesi, Azure AD ayarlarınız tarafından denetlenir. Ayarların değeri, **Kuruluş ilişkileri** sayfasındaki **Ayarlar** altında gösterilir. Ayarın seçili olduğundan emin olun, aksi takdirde davetiye başarısız olur. Daha fazla bilgi için bkz. [Konuk Kullanıcı erişim Izinlerini kısıtlama](../../active-directory/enterprise-users/users-restrict-guest-permissions.md).

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Dış işbirliği ayarlarını gösteren ekran görüntüsü." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Konuk kullanıcılar Microsoft Müşteri Sözleşmesi kiracısına erişim sağlar ve bu da güvenlik açısından bir sorun teşkil edebilir. Daha fazla bilgi için bkz. [Konuk kullanıcıların varsayılan izinlerini kısıtlama hakkında bilgi edinin](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions).

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Azure AD kiracısı kapsamında birden çok Microsoft bulut hizmetini yönetme

Kuruluşunuz için birden çok bulut hizmetini tek bir Azure AD kiracısı altında yönetebilirsiniz. Tüm Microsoft bulut teklifleri için Kullanıcı hesapları, Kullanıcı hesapları ve grupları içeren Azure AD kiracısında depolanır. Aşağıdaki diyagramda, hesapları içeren ortak bir Azure AD kiracısı kullanan birden çok hizmet içeren bir kuruluşun örneği gösterilmektedir. Her hizmetin kendi portalı vardır ve bu, kullanıcıların hizmetlerini yönetmeleri için mavi bir metindir.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Hesapları içeren ortak bir Azure AD kiracısı kullanarak birden çok hizmet içeren bir kuruluşun örneğini gösteren diyagram." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Sonraki adımlar

Esnek faturalama sahipliğini yönetmeyi öğrenmek ve Microsoft Müşteri anlaşmanıza güvenli erişim sağlamak için aşağıdaki makaleleri okuyun.

- [Kiracı ayarlama](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Yerleşik Azure rolleri](../../role-based-access-control/built-in-roles.md)
- [Azure aboneliğini farklı bir Azure AD dizinine aktarma](../../role-based-access-control/transfer-subscription.md)
- [Azure Active Directory 'de Konuk erişim izinlerini (Önizleme) kısıtlama](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Azure portalındaki dizininize konuk kullanıcı ekleme](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory varsayılan kullanıcı izinleri nelerdir?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Azure Active Directory nedir?](../../active-directory/fundamentals/active-directory-whatis.md)