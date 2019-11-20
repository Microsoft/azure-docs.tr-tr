---
title: Grup bilgileri - Azure Active Directory Düzenle | Microsoft Docs
description: Azure Active Directory kullanarak grubunuzun bilgilerini düzenleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc06abca08b2522ac57552e85f7c1bac3ef854af
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561886"
---
# <a name="edit-your-group-information-using-azure-active-directory"></a>Azure Active Directory'yi kullanarak, grup bilgilerini Düzenle

Azure Active Directory (Azure AD) kullanarak, ad, açıklama ve üyelik türünü güncelleştirme dahil olmak üzere grubun ayarlarını düzenleyebilir.

## <a name="to-edit-your-group-settings"></a>Grup ayarlarınızı düzenlemek için
1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com) oturum açın.

2. Seçin **Azure Active Directory**ve ardından **grupları**.

    **Gruplar - tüm gruplar** sayfası görüntülenirse, etkin gruplarınızın tümü gösteriliyor.

3. Gelen **gruplar - tüm gruplar** sayfasında, grup adı kadar içine mümkün olduğunca **arama** kutusu. Bu makalenin amaçları ki aradığınız **MDM İlkesi - Batı** grubu.

    Arama sonuçları altında görüntülenir **arama** kutusunda daha fazla karakter türü olarak güncelleştiriliyor.

    ![Arama kutusuna arama metniyle tüm grupları sayfası](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. Grup seçin **MDM İlkesi - Batı**ve ardından **özellikleri** gelen **Yönet** alan.

    ![Gruba genel bakış sayfası, üye seçeneği ve bilgiler vurgulandı](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. Güncelleştirme **genel ayarlar** dahil olmak üzere, gerektiği gibi bilgileri:

    ![Bir grup için özellikleri ayarlar](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **Grup adı.** Var olan grup adını düzenleyin.
    
    - **Grup açıklaması.** Mevcut Grup açıklamasını düzenleyin.

    - **Grup türü.** Oluşturulduktan sonra bir grup türünü değiştiremezsiniz. Değiştirilecek **grup türü**, grubunu silin ve yeni bir tane oluşturmanız gerekir.
    
    - **Üyelik türü.** Üyelik türünü değiştirin. Kullanılabilir çeşitli üyelik türleri hakkında daha fazla bilgi için bkz [. nasıl yapılır: Azure Active Directory portalını](active-directory-groups-create-azure-portal.md)kullanarak temel bir grup oluşturun ve üye ekleyin.
    
    - **Nesne Kimliği** Nesne Kimliğini değiştiremezsiniz, ancak bu grup için PowerShell komutlarında kullanılacak kopyalayabilirsiniz. PowerShell cmdlet'leri kullanma hakkında daha fazla bilgi için bkz. [Grup ayarlarını yapılandırmak için Azure Active Directory cmdlet'leri](../users-groups-roles/groups-settings-v2-cmdlets.md).

## <a name="next-steps"></a>Sonraki adımlar
Bu makalelerde Azure Active Directory ile ilgili ek bilgi sağlanmıştır.

- [Grupları ve üyeleri görüntüleme](active-directory-groups-view-azure-portal.md)

- [Temel bir grup oluşturma ve üye ekleme](active-directory-groups-create-azure-portal.md)

- [Ekleme veya gruptan üye kaldırma](active-directory-groups-members-azure-portal.md)

- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](../users-groups-roles/groups-create-rule.md)

- [Bir grubun üyeliklerini yönetme](active-directory-groups-membership-azure-portal.md)

- [Grupları kullanarak kaynaklara erişimi yönetme](active-directory-manage-groups.md)

- [Azure Active Directory’ye bir Azure aboneliğini ekleme veya ilişkilendirme](active-directory-how-subscriptions-associated-directory.md)
