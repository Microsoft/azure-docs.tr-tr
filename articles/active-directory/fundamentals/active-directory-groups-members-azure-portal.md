---
title: Grup üyeleri - Azure Active Directory Ekle Kaldır | Microsoft Docs
description: Üye ekleme veya Azure Active Directory'yi kullanarak bir gruptan kaldırma hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561961"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Azure Active Directory'yi kullanarak grubuna üye ekleme veya kaldırma
Azure Active Directory'yi kullanarak, ekleme ve grubu üyelerini kaldırma devam edebilirsiniz.

## <a name="to-add-group-members"></a>Grup üyeleri eklemek için

1. Dizin için bir Genel yönetici hesabı kullanarak [Azure portalda](https://portal.azure.com) oturum açın.

2. Seçin **Azure Active Directory**ve ardından **grupları**.

3. Gelen **gruplar - tüm gruplar** sayfasında arayın ve üye eklemek istediğiniz grubu seçin. Bu durumda, önceden oluşturulmuş grubumuz kullanın **MDM İlkesi - Batı**.

    ![Tüm grupları grupları sayfasında grubu adı vurgulanmış](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. **MDM ilkesi - Batı Genel Bakışı** sayfasında, **Yönet** alanından **Üyeler** seçeneğini belirleyin.

    ![MDM İlkesi - üyeleri seçeneğinin vurgulandığı Batı genel bakış sayfası](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Seçin **üye ekleme**, her gruba eklemeniz ve ardından istediğiniz üyeleri seçin ve ardından arama **seçin**.

    Üye başarıyla eklendi belirten bir ileti alırsınız.

    ![Üyeleri sayfa ekleme ile gösterilen üyesi için aranır.](media/active-directory-groups-members-azure-portal/update-members.png)

6. Gruba üye adlarını görmek için ekranı yenileyin.

## <a name="to-remove-group-members"></a>Grup üyeleri kaldırmak için

1. Gelen **gruplar - tüm gruplar** sayfasında arayın ve üye kaldırmak istediğiniz grubu seçin. Tekrar kullanacağız, **MDM İlkesi - Batı**.

2. Seçin **üyeleri** gelen **Yönet** alanında, arama ve kaldırın ve ardından üyenin adını işaretleyin **Kaldır**.

    ![Üye bilgileri sayfası, Kaldır seçeneği](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Grupları ve üyeleri görüntüleme](active-directory-groups-view-azure-portal.md)

- [Grup ayarlarınızı düzenleme](active-directory-groups-settings-azure-portal.md)

- [Grupları kullanarak kaynaklara erişimi yönetme](active-directory-manage-groups.md)

- [Bir gruptaki kullanıcılar için dinamik kuralları yönetme](../users-groups-roles/groups-create-rule.md)

- [Azure Active Directory’ye bir Azure aboneliğini ekleme veya ilişkilendirme](active-directory-how-subscriptions-associated-directory.md)
