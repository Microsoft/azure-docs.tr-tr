---
title: Kuruluşunuzun gizlilik bilgilerini ekleme-Azure Active Directory | Microsoft Docs
description: Kuruluşunuzun gizlilik bilgilerini Azure Active Directory özellikleri alanına nasıl ekleyeceğiniz hakkında yönergeler.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996772"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Azure Active Directory kullanarak kuruluşunuzun gizlilik bilgilerini ekleyin
Bu makalede, bir kiracı yöneticisinin Azure portal aracılığıyla bir kuruluşun Azure Active Directory (Azure AD) kiracısına gizlilikle ilgili bilgileri nasıl ekleyebileceği açıklanmaktadır.

İç çalışanlarınız ve dış konularınızın ilkelerinizi gözden geçirebilmesi için hem genel gizlilik kişinizi hem de kuruluşunuzun gizlilik bildirimini eklemenizi önemle öneririz. Gizlilik deyimleri her işletme için benzersiz olarak oluşturulup uygun olduğundan, yardım için bir avuya başvurmanız önemle önerilir.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Azure AD 'de gizlilik bilgilerinizi ekleyin
Kuruluşunuzun gizlilik bilgilerini Azure AD 'nin **Özellikler** alanına eklersiniz.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Özellikler alanına erişmek ve gizlilik bilgilerinizi eklemek için

1. Azure portal Kiracı Yöneticisi olarak oturum açın.

2. Sol gezinti çubuğunda **Azure Active Directory**' yi seçin ve ardından **Özellikler**' i seçin.

    **Özellikler** alanı görüntülenir.

    ![Gizlilik bilgileri alanını vurgulayan Azure AD özellikleri alanı](media/active-directory-properties-area/properties-area.png)

3. Çalışanlarınıza ait gizlilik bilgilerinizi ekleyin:

    - **Teknik iletişim.** Kuruluşunuz dahilinde teknik destek için iletişim kurulacak kişinin e-posta adresini yazın.
    
    - **Genel Gizlilik kişisi.** Kişisel veri gizliliği hakkındaki sorgular için kişinin e-posta adresini yazın. Bu kişi, Microsoft 'un de bir veri ihlali olup olmadığını da kim. Burada listelenen bir kişi yoksa, Microsoft genel yöneticilerinizle iletişim kurar.

    - **Gizlilik bildirimi URL 'SI.** Kuruluşunuzun hem iç hem de dış konuğun veri gizliliğini nasıl işlediğini açıklayan, kuruluşunuzun belgesinin bağlantısını yazın.

        >[!Important]
        >Kendi gizlilik bildirimimizi veya gizlilik kişinizi eklemezseniz, dış konularınız, gözden **geçirme izinleri** kutusunda bir metin görür, bu da **< _kuruluş adınızın_> gözden geçirmeniz için kendi koşullarına bağlantı sağlamamıştır**. Örneğin, bir Konuk Kullanıcı B2B işbirliği aracılığıyla kuruluşa erişim daveti aldıklarında bu iletiyi görür.

        ![B2B Işbirliği Inceleme Izinleri kutusu ileti](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Active Directory B2B işbirliği davetini sıfırlama](../external-identities/redemption-experience.md)
- [Azure Active Directory bir kullanıcının profil bilgilerini ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)