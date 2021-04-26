---
title: Azure rollerini Azure portal kullanarak atama-Azure RBAC
description: Azure portal ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcılar, gruplar, hizmet sorumluları veya yönetilen kimlikler için Azure kaynaklarına nasıl erişim sağlayacağınızı öğrenin.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 02/15/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 6b159d9ca7d8d7739d623bb3a48752b4d03e24bc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385981"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Azure portal kullanarak Azure rolleri atama

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Bu makalede, Azure portal kullanarak rollerin nasıl atanacağı açıklanır.

Azure Active Directory ' de yönetici rolleri atamanız gerekiyorsa, bkz. [Azure Active Directory yönetici rollerini görüntüleme ve atama](../active-directory/roles/manage-roles-portal.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-identify-the-needed-scope"></a>1. Adım: gerekli kapsamı tanımla

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)]

[!INCLUDE [Scope for Azure RBAC least privilege](../../includes/role-based-access-control/scope-least.md)] Kapsam hakkında daha fazla bilgi için bkz. [kapsamı anlama](scope-overview.md).

![Azure RBAC için kapsam düzeyleri](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. Üstteki arama kutusunda, erişim vermek istediğiniz kapsam için arama yapın. Örneğin, **Yönetim gruplarını**, **abonelikleri**, **kaynak gruplarını** veya belirli bir kaynağı arayın.

    ![Kaynak grubu Azure portal arama](./media/shared/rg-portal-search.png)

1. Bu kapsam için özel kaynağa tıklayın.

    Aşağıda örnek bir kaynak grubu gösterilmektedir.

    ![Kaynak grubuna genel bakış](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>2. Adım: rol ataması Ekle bölmesini açın

**Erişim denetimi (IAM)** , genellikle Azure kaynaklarına erişim izni vermek üzere rol atamak için kullandığınız sayfasıdır. Kimlik ve erişim yönetimi (ıAM) olarak da bilinir ve Azure portal çeşitli konumlarda görüntülenir.

1. **Erişim denetimi (IAM)** öğesine tıklayın.

    Aşağıda, bir kaynak grubu için erişim denetimi (ıAM) sayfasının bir örneği gösterilmektedir.

    ![Kaynak grubu için erişim denetimi (ıAM) sayfası](./media/shared/rg-access-control.png)

1. Bu kapsamdaki rol atamalarını görüntülemek için **rol atamaları** sekmesine tıklayın.

1.   >  **Rol Ekle ataması** Ekle ' ye tıklayın.
   Rol atama izniniz yoksa rol ataması Ekle seçeneği devre dışı bırakılır.

   ![Rol atama menüsü Ekle](./media/shared/add-role-assignment-menu.png)

    Rol ataması ekle bölmesi açılır.

   ![Rol atama sayfası ekle](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-select-the-appropriate-role"></a>3. Adım: uygun rolü seçin

1. **Rol** listesinde, atamak istediğiniz rolü bulmak için arama veya kaydırma yapın.

    Uygun rolü belirlemenize yardımcı olmak için, rol için bir açıklama göstermek üzere bilgi simgesinin üzerine gelin. Daha fazla bilgi için, [Azure yerleşik rolleri](built-in-roles.md) makalesini görüntüleyebilirsiniz.

   ![Rol ataması Ekle ' de rol seçin](./media/role-assignments-portal/add-role-assignment-role.png)

1. Rolü seçmek için tıklayın.

## <a name="step-4-select-who-needs-access"></a>4. Adım: kimin erişmesi gerektiğini seçin

1. **Erişim ata** listesinde, erişim atanacak güvenlik sorumlusu türünü seçin.

    | Tür | Açıklama |
    | --- | --- |
    | **Kullanıcı, Grup veya hizmet sorumlusu** | Rolü bir Kullanıcı, Grup veya hizmet sorumlusu (uygulama) için atamak istiyorsanız, bu türü seçin. |
    | **Kullanıcı tarafından atanan yönetilen kimlik** | Rolü [Kullanıcı tarafından atanan bir yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md)atamak istiyorsanız, bu türü seçin. |
    | *Sistem tarafından atanan yönetilen kimlik* | Rolü [sistem tarafından atanan bir yönetilen kimliğe](../active-directory/managed-identities-azure-resources/overview.md)atamak istiyorsanız, yönetilen kimliğin bulunduğu Azure hizmeti örneğini seçin. |

   ![Rol ataması Ekle içinde güvenlik sorumlusu türünü seçin](./media/role-assignments-portal/add-role-assignment-type.png)

1. Kullanıcı tarafından atanan yönetilen bir kimlik veya sistem tarafından atanan yönetilen kimlik seçtiyseniz, yönetilen kimliğin bulunduğu **aboneliği** seçin.

1. **Seç** bölümünde, bir dize girerek veya listede kaydırarak güvenlik sorumlusunu arayın.

   ![Rol ataması Ekle içinde Kullanıcı seçin](./media/role-assignments-portal/add-role-assignment-user.png)

1. Güvenlik sorumlusunu bulduktan sonra seçmek için tıklayın.

## <a name="step-5-assign-role"></a>5. Adım: rol atama

1. Rolü atamak için **Kaydet**' e tıklayın.

   Birkaç dakika sonra, güvenlik sorumlusu seçili kapsamda role atanır.

1. **Rol atamaları** sekmesinde, listede rol atamasını gördiğinizi doğrulayın.

    ![Kaydedilmiş rol ataması ekleme](./media/role-assignments-portal/rg-role-assignments.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcıyı Azure aboneliğinin yöneticisi olarak atama](role-assignments-portal-subscription-admin.md)
- [Azure rol atamalarını kaldırma](role-assignments-remove.md)
- [Azure RBAC sorunlarını giderme](troubleshooting.md)
