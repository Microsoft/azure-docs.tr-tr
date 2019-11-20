---
title: Azure Aboneliği yöneticileri ekleme veya değiştirme
description: Rol tabanlı erişim denetimi (RBAC) kullanarak Azure abonelik yöneticisi ekleme veya değiştirme işlemini açıklar.
author: genlin
manager: dcscontentpm
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: banders
ms.openlocfilehash: 2054fbb7d0a9f450ad487fc0f03d0af920c6cc4b
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260918"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Azure Aboneliği yöneticileri ekleme veya değiştirme


Azure kaynaklarına erişimi yönetmek için uygun yönetici rolüne sahip olmanız gerekir. Azure, aralarından seçim yapabileceğiniz çeşitli yerleşik rollerle [rol tabanlı erişim denetimi](../role-based-access-control/overview.md) (RBAC) adlı bir yetkilendirme sistemine sahiptir. Bu rolleri, yönetim grubu, abonelik veya kaynak grubu gibi farklı kapsamlara atayabilirsiniz. Varsayılan olarak, yeni bir Azure aboneliği oluşturan kişi, diğer kullanıcıların yönetici erişimini başka bir aboneliğe atayabilir.

Bu makalede, abonelik kapsamında RBAC kullanılarak bir kullanıcı için yönetici rolünün nasıl ekleneceği veya değiştirileceği açıklanmaktadır.

Microsoft, RBAC kullanarak kaynaklara erişimi yönetmenizi önerir. Ancak halen klasik dağıtım modelini kullanıyor ve [Azure Hizmet Yönetimi PowerShell Modülü](https://docs.microsoft.com/powershell/module/servicemanagement/azure)’nü kullanarak klasik kaynakları yönetiyorsanız, klasik bir yönetici kullanmanız gerekir.

> [!TIP]
> Klasik kaynakları yönetmek için yalnızca Azure portalını kullanıyorsanız klasik yöneticiyi kullanmanız gerekmez.

Daha fazla bilgi için bkz. [Azure Resource Manager ve klasik dağıtım karşılaştırması](../azure-resource-manager/resource-manager-deployment-model.md) ve [Azure klasik abonelik yöneticileri](../role-based-access-control/classic-administrators.md).

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-subscription-administrator"></a>Abonelik yöneticisi atama

Bir kullanıcıyı Azure aboneliğinin yöneticisi yapmak için, mevcut bir yöneticinin o kullanıcıya abonelik kapsamında [Sahip](../role-based-access-control/built-in-roles.md#owner) rolünü (RBAC rolü) ataması gerekir. Sahip rolü, kullanıcıya, başkalarına erişim yetkisi devretme hakkı da dahil olmak üzere abonelikteki tüm kaynaklara tam erişim izni verir. Bu adımlar diğer rol atamasıyla aynıdır.

Bir aboneliğin hesap yöneticisinin kim olduğunu bilmiyorsanız aşağıdaki adımları izleyerek bulabilirsiniz.

1. [Azure portalında Abonelikler sayfasını](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) açın.
1. Denetlemek istediğiniz aboneliği seçin ve **Ayarlar** bölümüne bakın.
1. **Özellikler**’i seçin. Aboneliğin hesap yöneticisi, **Hesap Yöneticisi** kutusunda görüntülenir.

### <a name="to-assign-a-user-as-an-administrator"></a>Bir kullanıcıyı yönetici olarak atama

1. Abonelik sahibi olan hesapla Azure portalında oturum açıp [Abonelikler](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) sayfasını açın.

1. Erişim izni vermek istediğiniz aboneliğe tıklayın.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

1. Bu aboneliğin tüm rol atamalarını görüntülemek için **Rol atamaları** sekmesine tıklayın.

    ![Rol atamalarını gösteren ekran görüntüsü](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. **Ekle** > **Rol ataması ekle** seçeneğine tıklayarak **Rol ataması ekle** bölmesini açın.

    Rol atama iznine sahip değilseniz bu seçenek devre dışı bırakılır.

1. **Rol** açılan listesinde **Sahip** rolünü seçin.

1. **Seç** listesinde bir kullanıcı seçin. Listede kullanıcıyı görmüyorsanız **Seç** kutusuna giriş yaparak dizinde görünen ad ve e-posta adresi arayabilirsiniz.

    ![Sahip rolünün seçili olduğunu gösteren ekran görüntüsü](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Rolü atamak için **Kaydet**’e tıklayın.

    Birkaç dakika sonra kullanıcıya abonelik kapsamında Sahip rolü atanmış olur.

## <a name="next-steps"></a>Sonraki adımlar

* [Rol tabanlı erişim denetimi (RBAC) nedir?](../role-based-access-control/overview.md)
* [Azure'daki farklı rolleri anlama](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Nasıl yapılır: Azure Active Directory’ye bir Azure aboneliğini ekleme veya ilişkilendirme](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Azure Active Directory'deki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Yine de yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
