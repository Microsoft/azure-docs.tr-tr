---
title: Kuruluşunuzun gizlilik bilgisi - Azure Active Directory'ye ekleme | Microsoft Docs
description: Kuruluşunuzun gizlilik bilgileri için Azure Active Directory özellikleri alan ekleme hakkında yönergeler.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a2397662112bda6c6789b25691848344a4e611b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561818"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Azure Active Directory'yi kullanarak, kuruluşunuzun gizlilik bilgilerini ekleyin
Bu makalede, bir kiracı Yöneticisi Azure portalı üzerinden bir kuruluşun Azure Active Directory (Azure AD) kiracısına gizlilikle ilgili bilgileri nasıl ekleyebileceğinizi açıklar.

İç çalışanlarınızın ve dış Konukları ilkelerinizi gözden geçirebilmeniz için genel gizlilik ilgili kişisi hem kuruluşunuzun gizlilik bildirimini ekleyin kesinlikle öneririz. Gizlilik bildirimlerini benzersiz olarak oluşturulur ve her işletme için hazırlanmış olduğundan bir avukat Yardım almak için sizinle kesinlikle öneririz.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Azure AD'de gizlilik bilgilerinizi ekleyin
Kuruluşunuzun gizlilik bilgilerinde eklediğiniz **özellikleri** Azure ad alanı.

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>Özellikler alanı erişim ve gizlilik bilgilerinizi ekleyin

1.  Kiracı Yöneticisi olarak Azure portalında oturum açın.

2.  Sol gezinti seçin **Azure Active Directory**ve ardından **özellikleri**.

    **Özellikleri** alanında görünür.

    ![Azure AD özellikleri alanı gizlilik bilgileri alanı vurgulama](media/active-directory-properties-area/properties-area.png)

3.  Çalışanlarınız için gizlilik bilgilerinizi ekleyin:

    - **Teknik konular ilgili kişisi** Kuruluşunuzdaki teknik destek için iletişim kurulacak kişinin e-posta adresini yazın.
    
    - **Genel gizlilik ilgili kişisi.** Kişinin kişisel veri gizliliği ile ilgili sorguları kişiye e-posta adresini yazın. Bu kişi ayrıca bir veri ihlali ise Microsoft ile iletişim kim. Burada listelenen herhangi bir kişi varsa, genel Yöneticiler Microsoft ile iletişim kurar.

    - **Gizlilik bildirimi URL'si.** Kuruluşunuzun her ikisi de nasıl işlediğini açıklar, kuruluşunuzun belge bağlantı türü iç ve dış konuğun veri gizliliği.

        >[!Important]
        >Kendi gizlilik bildirimi ya da kendi gizlilik ilgili kişisi dahil etmezseniz, dış konuklarınız metnini görmek **Gözden Geçiren izinleri** ifadesini kutusu  **< _kuruluş adınız_> incelemeniz için koşullarına bir bağlantı sağlamadı**. Örneğin, bir kuruluşun B2B işbirliği erişmek için davet aldıklarında Konuk kullanıcı bu iletiyi görür.

        ![B2B işbirliği gözden geçirme izinler kutusunda iletisi](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4.  **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Active Directory B2B işbirliği Davetiyesi kullanımı](https://aka.ms/b2bredemption)
- [Azure Active Directory'de bir kullanıcı profili bilgileri ekleme veya değiştirme](active-directory-users-profile-azure-portal.md)
