---
title: Azure AD Yetkilendirme Yönetimi 'nde erişim paketi için yaşam döngüsü ayarlarını değiştirme-Azure Active Directory
description: Azure Active Directory yetkilendirme yönetimindeki bir erişim paketi için yaşam döngüsü ayarlarını değiştirmeyi öğrenin.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 959d85f496a4a573a969bf736aba137d5b86154a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73174742"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD yetkilendirme yönetimindeki bir erişim paketi için yaşam döngüsü ayarlarını değiştirme

Bir erişim paketi Yöneticisi olarak, mevcut bir ilkeyi düzenleyerek, erişim paketinin yaşam döngüsü ayarlarını dilediğiniz zaman değiştirebilirsiniz. Bir ilkenin sona erme tarihini değiştirirseniz, zaten bekleyen bir onaylama veya onaylanan durumda olan isteklerin sona erme tarihi değişmez.

Bu makalede, var olan bir erişim paketinin yaşam döngüsü ayarlarının nasıl değiştirileceği açıklanır.

## <a name="open-lifecycle-settings"></a>Yaşam döngüsü ayarlarını açma

Bir erişim paketinin yaşam döngüsü ayarlarını değiştirmek için, ilgili ilkeyi açmanız gerekir. Bir erişim paketinin yaşam döngüsü ayarlarını açmak için aşağıdaki adımları izleyin.

**Önkoşul rolü:** Genel yönetici, Kullanıcı Yöneticisi, Katalog sahibi veya erişim paketi Yöneticisi

1. Azure portal, **Azure Active Directory** ' a ve ardından **kimlik**Yönetimi ' ne tıklayın.

1. Sol menüde, **erişim paketleri** ' ne tıklayın ve ardından erişim paketini açın.

1. **İlkeler** ' e tıklayın ve ardından düzenlemek istediğiniz yaşam döngüsü ayarlarına sahip ilkeye tıklayın.

    Ilke ayrıntıları bölmesi sayfanın altında açılır.

    ![Erişim paketi-Ilke ayrıntıları bölmesi](./media/entitlement-management-shared/policy-details.png)

1. İlkeyi düzenlemek için **Düzenle** ' ye tıklayın.

    ![Erişim paketi-ilkeyi Düzenle](./media/entitlement-management-shared/policy-edit.png)

1. Yaşam döngüsü ayarlarını açmak için **yaşam döngüsü** sekmesine tıklayın.

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Erişim paketi için değişiklik isteği ve onay ayarları](entitlement-management-access-package-request-policy.md)