---
title: Kullanıcının profil bilgilerini ekleme veya güncelleştirme-Azure Active Directory | Microsoft Docs
description: Bir resim ve iş ayrıntıları dahil olmak üzere Azure Active Directory kullanıcının profiline bilgi ekleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c416d7279dd558b8a793064e295b7654925b1c8
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034898"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Azure Active Directory kullanarak bir kullanıcının profil bilgilerini ekleme veya güncelleştirme
Profil resmi, işe özel bilgiler ve Azure Active Directory (Azure AD) kullanarak bazı ayarlar dahil olmak üzere Kullanıcı profili bilgilerini ekleyin. Yeni Kullanıcı ekleme hakkında daha fazla bilgi için bkz. [Azure Active Directory Kullanıcı ekleme veya silme](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Profil bilgilerini ekleme veya değiştirme
Gördüğünüz gibi, kullanıcının profilinde kullanıcı oluşturma sırasında ekleyebilecekler bölümünde daha fazla bilgi bulabilirsiniz. Bu ek bilgilerin tümü isteğe bağlıdır ve kuruluşunuzun gerektirdiği şekilde eklenebilir.

## <a name="to-add-or-change-profile-information"></a>Profil bilgilerini eklemek veya değiştirmek için
1. [Azure Portal](https://portal.azure.com/) , kuruluş için Kullanıcı Yöneticisi olarak oturum açın.

2. **Azure Active Directory**' yi seçin, **Kullanıcılar**' ı seçin ve ardından bir kullanıcı seçin. Örneğin, _Charon_.

    **Alain Charon profili** sayfası görüntülenir.

    ![Düzenlenebilir bilgiler de dahil olmak üzere kullanıcının profil sayfası](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. İsteğe bağlı olarak, kullanılabilir bölümlerin her birine dahil edilen bilgileri eklemek veya güncelleştirmek için **Düzenle** ' yi seçin.

    ![Düzenlenebilir alanların gösterildiği kullanıcının profil sayfası](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profil resmi.** Kullanıcı hesabı için bir küçük resim seçin. Bu resim, Azure Active Directory ve kullanıcının kişisel sayfalarında, myapps.microsoft.com sayfası gibi görünür.

    - **IDENTITY.** Kullanıcı için evli soyadı gibi ek bir kimlik değeri ekleyin veya güncelleştirin. Bu adı, adı ve soyadı değerlerini bağımsız olarak ayarlayabilirsiniz. Örneğin, bunu, harfleri, bir şirket adını veya gösterilen adların sırasını değiştirmek için kullanabilirsiniz. Başka bir örnekte, adları ' Chris Green ' olan iki kullanıcı için kimlik dizesini kullanarak adlarını ' Chris B. yeşil ' ' Chris R. Green (contoso) olarak ayarlayabilirsiniz.

    - **İş bilgileri.** Kullanıcının iş unvanı, departmanı veya Yöneticisi gibi işle ilgili herhangi bir bilgi ekleyin.

    - **Ayarlar.** Kullanıcının Azure Active Directory kiracıya oturum açıp yükleyemeyeceğine karar verin. Kullanıcının genel konumunu da belirtebilirsiniz.

    - **İletişim bilgileri.** Bazı kullanıcıların telefonu veya mobil iletişim bilgileri dışında, kullanıcının ilgili iletişim bilgilerini ekleyin (yalnızca bir genel yönetici Yönetici rollerindeki kullanıcılar için güncelleştirebilir).

    - **Kimlik doğrulaması iletişim bilgileri.** Kullanıcı için etkin bir telefon numarası ve e-posta adresi olduğundan emin olmak için bu bilgileri doğrulayın. Bu bilgiler, kullanıcının oturum açma işlemi sırasında gerçekten Kullanıcı olduğundan emin olmak için Azure Active Directory tarafından kullanılır. Kimlik doğrulama iletişim bilgileri yalnızca genel bir yönetici tarafından güncelleştirilebilen olabilir.

4. **Kaydet**’i seçin.

    Tüm değişiklikleriniz Kullanıcı için kaydedilir.

    >[!Note]
    >Yetkili kaynağı Windows Server Active Directory olan kullanıcılar için kimliği, iletişim bilgilerini veya iş bilgilerini güncelleştirmek üzere Windows Server Active Directory kullanmanız gerekir. Güncelleştirmenizi tamamladıktan sonra, değişiklikleri görebilmeniz için bir sonraki eşitleme döngüsünün tamamlanmasını beklemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar
Kullanıcılarınızın profillerini güncelleştirdikten sonra, aşağıdaki temel işlemleri gerçekleştirebilirsiniz:

- [Kullanıcı ekleme veya silme](add-users-azure-active-directory.md)

- [Kullanıcılara rol atama](active-directory-users-assign-role-azure-portal.md)

- [Temel Grup oluşturma ve üye ekleme](active-directory-groups-create-azure-portal.md)

Ya da temsilciler atama, ilkeleri kullanma ve Kullanıcı hesaplarını paylaşma gibi diğer kullanıcı yönetim görevlerini de gerçekleştirebilirsiniz. Diğer kullanılabilir eylemler hakkında daha fazla bilgi için bkz. [Kullanıcı yönetimi belgelerini Azure Active Directory](../users-groups-roles/index.yml).
