---
title: Kullanıcılara dizin rolleri atama-Azure Active Directory | Microsoft Docs
description: Azure Active Directory sahip kullanıcılara yönetici ve yönetici olmayan roller atamaya yönelik yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d071ff071c13637b15479d86a699d0b368119196
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73742553"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Azure Active Directory sahip kullanıcılara yönetici ve yönetici olmayan roller atama
Kuruluşunuzdaki bir kullanıcının Azure Active Directory (Azure AD) kaynaklarını yönetmek için izne ihtiyacı varsa, kullanıcının gerçekleştirmesi gereken eylemlere göre kullanıcıyı Azure AD 'de uygun bir rol atamanız gerekir.

Kullanılabilir roller hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolleri atama](../users-groups-roles/directory-assign-admin-roles.md). Kullanıcı ekleme hakkında daha fazla bilgi için bkz. [Azure Active Directory yeni kullanıcı ekleme](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Rol atama
Bir kullanıcıya Azure AD rolleri atama yapmanın yaygın bir yolu, bir kullanıcı için **Dizin rolü** sayfasıdır.

Ayrıca, Privileged Identity Management (PıM) kullanarak roller de atayabilirsiniz. PıM kullanma hakkında daha ayrıntılı bilgi için bkz. [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Bir kullanıcıya rol atama
1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com/) oturum açın.

2. **Azure Active Directory**' yi seçin, **Kullanıcılar**' ı seçin ve ardından rol atamasını alarak Kullanıcı ' yı arayıp seçin. Örneğin, _Charon_.

      ![Tüm kullanıcılar sayfası-kullanıcı seçin](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

3. **Alain Charon profili** sayfasında **atanan roller**' i seçin.

    **Alain Charon-Directory rol** sayfası görüntülenir.

4. **Atama Ekle**' yi seçin, Alain atanacak rolü seçin (örneğin, _Uygulama Yöneticisi_) ve ardından **Seç**' i seçin.

    ![Atanan roller sayfası-seçili rol gösteriliyor](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    Uygulama Yöneticisi rolü ALAIN Charon 'a atanır ve bu, **Alain Charon-Directory rol** sayfasında görünür.

## <a name="remove-a-role-assignment"></a>Rol atamasını kaldırma
Rol atamasını bir kullanıcıdan kaldırmanız gerekiyorsa, bunu de **Alain Charon-Directory rol** sayfasından da yapabilirsiniz.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Bir kullanıcının rol atamasını kaldırmak için

1. **Azure Active Directory**' yi seçin, **Kullanıcılar**' ı seçin ve ardından rol atamasını kaldırma Kullanıcı ' yı arayıp seçin. Örneğin, _Charon_.

2. **Atanan roller**' i seçin, **Uygulama Yöneticisi**' ni seçin ve **Atamayı Kaldır**' ı seçin.

    ![Atanan roller sayfası ve seçili rol ve Kaldır seçeneği gösteriliyor](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    Uygulama Yöneticisi rolü Alain Charon 'dan kaldırılır ve artık **Charon-Directory rol** sayfasında görünmez.

## <a name="next-steps"></a>Sonraki adımlar
- [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md)

- [Profil bilgilerini ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)

- [Başka bir dizinden konuk kullanıcılar ekleme](../b2b/what-is-b2b.md)

Ya da temsilciler atama, ilkeleri kullanma ve Kullanıcı hesaplarını paylaşma gibi diğer kullanıcı yönetim görevlerini de gerçekleştirebilirsiniz. Diğer kullanılabilir eylemler hakkında daha fazla bilgi için bkz. [Kullanıcı yönetimi belgelerini Azure Active Directory](../users-groups-roles/index.yml).


