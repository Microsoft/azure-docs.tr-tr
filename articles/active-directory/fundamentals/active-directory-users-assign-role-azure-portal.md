---
title: Kullanıcılara Azure AD rolleri atama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory sahip kullanıcılara yönetici ve yönetici olmayan roller atamaya yönelik yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4ffcad6f6be16ba7ac3674d710dd543f729f0c3
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575420"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Azure Active Directory sahip kullanıcılara yönetici ve yönetici olmayan roller atama

Azure Active Directory (Azure AD) ' de, kullanıcılarınızın Azure AD kaynaklarını yönetmek için izne ihtiyacı varsa, bunları gereken izinleri sağlayan bir role atamanız gerekir. Azure kaynaklarını ve hangi rollerin Azure AD kaynaklarını yöneteceği hakkında bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Kullanılabilir Azure AD rolleri hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolleri atama](../roles/permissions-reference.md). Kullanıcıları eklemek için bkz. [Azure Active Directory yeni kullanıcı ekleme](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Rolleri atama

Bir kullanıcıya Azure AD rolleri atamak için ortak bir yol, bir kullanıcı için **atanan roller** sayfasıdır. Ayrıca, Kullanıcı uygunluğunu Privileged Identity Management (PıM) kullanarak bir role tam zamanında Yükseltilmek üzere de yapılandırabilirsiniz. PıM kullanma hakkında daha fazla bilgi için bkz. [Privileged Identity Management](../privileged-identity-management/index.yml).

> [!Note]
> Bir Azure AD Premium P2 lisans planınız varsa ve zaten PıM kullanıyorsanız, tüm rol yönetim görevleri [Privileged Identity Management deneyimde](../roles/manage-roles-portal.md)gerçekleştirilir. Bu özellik şu anda aynı anda yalnızca bir rol atamak için sınırlıdır. Şu anda birden çok rol seçemezsiniz ve bunları bir kullanıcıya tek seferde atayabilirsiniz.
>
> ![Zaten PıM kullanan ve Premium P2 lisansına sahip olan kullanıcılar için PıM 'de yönetilen Azure AD rolleri](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Bir kullanıcıya rol atama

1. [Azure Portal](https://portal.azure.com/) gidin ve Dizin Için bir genel yönetici hesabı kullanarak oturum açın.

2. **Azure Active Directory**'yi bulun ve seçin.

      ![Azure Active Directory arama Azure portal](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. **Kullanıcılar**’ı seçin.

4. Rol atamasını almak için arama yapın ve kullanıcıyı seçin. Örneğin, _Charon_.

      ![Tüm kullanıcılar sayfası-kullanıcı seçin](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. **Alain Charon profili** sayfasında **atanan roller**' i seçin.

    **Alain Charon-yönetimsel roller** sayfası görüntülenir.

6. **Atama Ekle**' yi seçin, Alain atanacak rolü seçin (örneğin, _Uygulama Yöneticisi_) ve ardından **Seç**' i seçin.

    ![Atanan roller sayfası-seçili rol gösteriliyor](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Uygulama Yöneticisi rolü, Charon 'a atanmıştır ve bu, **Charon-yönetimsel roller** sayfasında görünür.

## <a name="remove-a-role-assignment"></a>Rol atamasını kaldırma

Rol atamasını bir kullanıcıdan kaldırmanız gerekiyorsa, bunu Ayrıca, **Yönetim rolleri** sayfasında de kullanabilirsiniz.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Bir kullanıcının rol atamasını kaldırmak için

1. **Azure Active Directory**' yi seçin, **Kullanıcılar**' ı seçin ve ardından rol atamasını kaldırma Kullanıcı ' yı arayıp seçin. Örneğin, _Charon_.

2. **Atanan roller**' i seçin, **Uygulama Yöneticisi**' ni seçin ve **Atamayı Kaldır**' ı seçin.

    ![Atanan roller sayfası ve seçili rol ve Kaldır seçeneği gösteriliyor](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Uygulama Yöneticisi rolü Alain Charon 'dan kaldırılır ve artık **Charon-yönetimsel roller** sayfasında görünmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md)

- [Profil bilgilerini ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)

- [Başka bir dizinden konuk kullanıcılar ekleme](../external-identities/what-is-b2b.md)

Kullanıma aldığınız diğer Kullanıcı yönetimi görevleri [Azure Active Directory Kullanıcı Yönetimi belgelerinde](../enterprise-users/index.yml)bulunabilir.
