---
title: Bir kullanıcıyı Azure aboneliğinin Yöneticisi olarak atama-Azure RBAC
description: Azure portal ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak bir kullanıcıyı Azure aboneliğinin Yöneticisi yapma hakkında bilgi edinin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 01/11/2021
ms.author: rolyon
ms.openlocfilehash: dec5888127ed1fc291bec244a44cfb71e343e3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556843"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Kullanıcıyı Azure aboneliğinin yöneticisi olarak atama

Bir kullanıcıyı bir Azure aboneliğinin Yöneticisi yapmak için, abonelik kapsamında bu rolü [sahip](built-in-roles.md#owner) rolüne atayın. Sahip rolü, kullanıcıya, başkalarına erişim verme izni dahil olmak üzere abonelikteki tüm kaynaklara tam erişim sağlar. Bu adımlar diğer rol atamasıyla aynıdır.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>1. Adım: aboneliği açma

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Üstteki arama kutusunda abonelikler ' i arayın.

    ![Kaynak grubu Azure portal arama](./media/shared/sub-portal-search.png)

1. Kullanmak istediğiniz aboneliğe tıklayın.

    Aşağıda örnek bir abonelik gösterilmektedir.

    ![Kaynak grubuna genel bakış](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>2. Adım: rol ataması Ekle bölmesini açın

**Erişim denetimi (IAM)** , genellikle Azure kaynaklarına erişim izni vermek üzere rol atamak için kullandığınız sayfasıdır. Kimlik ve erişim yönetimi (ıAM) olarak da bilinir ve Azure portal çeşitli konumlarda görüntülenir.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

    Aşağıda, abonelik için erişim denetimi (ıAM) sayfasının bir örneği gösterilmektedir.

    ![Kaynak grubu için erişim denetimi (ıAM) sayfası](./media/shared/sub-access-control.png)

1. Bu kapsamdaki rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.

1.   >  **Rol Ekle ataması** Ekle ' ye tıklayın.
   Rol atama izniniz yoksa rol ataması Ekle seçeneği devre dışı bırakılır.

   ![Rol atama menüsü Ekle](./media/shared/add-role-assignment-menu.png)

    Rol ataması ekle bölmesi açılır.

   ![Rol atama bölmesi Ekle](./media/shared/add-role-assignment.png)

## <a name="step-3-select-the-owner-role"></a>3. Adım: sahip rolünü seçin

[Sahip](built-in-roles.md#owner) rolü, Azure RBAC 'de rol atama özelliği de dahil olmak üzere tüm kaynakları yönetmek için tam erişim verir. Güvenliği aşılmış bir sahibe göre ihlal olasılığını azaltmak için en fazla 3 abonelik sahibi olmanız gerekir.

- **Rol** listesinde, **sahip** rolünü seçin.

   ![Rol ataması Ekle bölmesinde sahip rolünü seçin](./media/role-assignments-portal-subscription-admin/add-role-assignment-role-owner.png)

## <a name="step-4-select-who-needs-access"></a>4. Adım: kimin erişmesi gerektiğini seçin

1. **Erişim ata** listesinde **Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.

1. **Seç** bölümünde, Kullanıcı için bir dize girerek veya listede kaydırma yaparak arama yapın.

   ![Rol ataması Ekle içinde Kullanıcı seçin](./media/role-assignments-portal-subscription-admin/add-role-assignment-user-admin.png)

1. Kullanıcıyı bulduktan sonra seçmek için tıklayın.

## <a name="step-5-assign-role"></a>5. Adım: rol atama

1. Rolü atamak için **Kaydet**' e tıklayın.

   Birkaç dakika sonra, kullanıcıya seçili kapsamda rol atanır.

1. **Rol atamaları** sekmesinde, listede rol atamasını gördiğinizi doğrulayın.

    ![Kaydedilmiş rol ataması ekleme](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak Azure rolleri atama](role-assignments-portal.md)
- [Azure portal kullanarak Azure rol atamalarını listeleyin](role-assignments-list-portal.md)
- [Kaynaklarınızı Azure yönetim gruplarıyla düzenleme](../governance/management-groups/overview.md)
