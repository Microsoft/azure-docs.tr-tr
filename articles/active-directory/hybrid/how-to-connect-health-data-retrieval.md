---
title: Azure AD Connect Health yönergeleri verileri alma | Microsoft Docs
description: Bu sayfa Azure AD Connect Health verilerin nasıl alınacağını açıklar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/02/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d147d2c094923e971e52e1dbfe3f7a19776d38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89463899"
---
# <a name="azure-ad-connect-health-instructions-for-data-retrieval"></a>Veri alma yönergelerini Azure AD Connect Health

Bu belgede, Azure AD Connect Health verileri almak için Azure AD Connect nasıl kullanılacağı açıklanmaktadır.

[!INCLUDE [active-directory-app-provisioning.md](../../../includes/gdpr-intro-sentence.md)]

## <a name="retrieve-all-email-addresses-for-users-configured-for-health-alerts"></a>Sistem durumu uyarıları için yapılandırılmış kullanıcılar için tüm e-posta adreslerini alın.

Uyarıları almak üzere Azure AD Connect Health ' de yapılandırılan tüm kullanıcılarınızın e-posta adreslerini almak için aşağıdaki adımları kullanın.

1.  Azure Active Directory Connect sistem durumu dikey penceresinde başlatın ve sol taraftaki Gezinti çubuğundan **Hizmetleri Eşitle** ' yi seçin.
 ![Eşitleme Hizmetleri](./media/how-to-connect-health-data-retrieval/retrieve1.png)

2.  **Uyarılar** kutucuğuna tıklayın.</br>
 ![Uyarı](./media/how-to-connect-health-data-retrieval/retrieve3.png)

3.  **Bildirim ayarları**' na tıklayın.
 ![Bildirim](./media/how-to-connect-health-data-retrieval/retrieve4.png)

4.  **Bildirim ayarı** dikey penceresinde, sistem durumu uyarı bildirimleri için alıcı olarak etkinleştirilen e-posta adreslerinin listesini bulacaksınız.
 ![E-postalar](./media/how-to-connect-health-data-retrieval/retrieve5a.png)
 
## <a name="retrieve-accounts-that-were-flagged-with-ad-fs-bad-password-attempts"></a>AD FS hatalı parola denemelerinde işaretlenen hesapları alma

İşaretlenen hesapları AD FS hatalı parola denemelerinde almak için aşağıdaki adımları kullanın.

1.  Azure Active Directory sistem durumu dikey penceresinden başlayarak **Eşitleme hataları**' nı seçin.
 ![Eşitleme hataları](./media/how-to-connect-health-data-retrieval/retrieve6.png)

2.  **Eşitleme hataları** dikey penceresinde **dışarı aktar**' a tıklayın. Bu, kaydedilen eşitleme hatalarının bir listesini dışarı aktarır.
 ![Dışarı aktarma](./media/how-to-connect-health-data-retrieval/retrieve7.png)

## <a name="next-steps"></a>Sonraki Adımlar
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Aracı yüklemesini Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health İşlemleri](how-to-connect-health-operations.md)
* [Azure AD Connect Health ile ilgili SSS](reference-connect-health-faq.md)
* [Azure AD Connect Health sürümü geçmişi](reference-connect-health-version-history.md)