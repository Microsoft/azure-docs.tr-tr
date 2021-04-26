---
title: 'Eşitleme Azure AD Connect: AD DS hesabı parolasını değiştirme | Microsoft Docs'
description: Bu konu belgesi, AD DS hesabının parolası değiştirildikten sonra Azure AD Connect nasıl güncelleştirileceğini açıklar.
services: active-directory
keywords: AD DS hesabı, Active Directory hesabı, parola
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4077146292db1266d5dbc51cc577f952b2bff191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357520"
---
# <a name="changing-the-ad-ds-account-password"></a>AD DS hesap parolasını değiştirme
AD DS hesabı, şirket içi Active Directory iletişim kurmak için Azure AD Connect tarafından kullanılan Kullanıcı hesabını ifade eder. AD DS hesabının parolasını değiştirirseniz, Azure AD Connect eşitleme hizmetini yeni parolayla güncelleştirmeniz gerekir. Aksi takdirde, eşitleme artık şirket içi Active Directory doğru şekilde eşitlenemez ve aşağıdaki hatalarla karşılaşacaksınız:

* Synchronization Service Manager, şirket içi AD ile herhangi bir içeri veya dışarı aktarma işlemi, **başlatma kimlik bilgileri olmadan** hata vererek başarısız olur.

* Windows Olay Görüntüleyicisi altında, uygulama olay günlüğü **olay kimliği 6000** ve ileti **' ' contoso.com "Yönetim Aracısı, kimlik bilgileri geçersiz olduğundan çalıştırılamadı**.


## <a name="how-to-update-the-synchronization-service-with-new-password-for-ad-ds-account"></a>AD DS hesabı için yeni parolayla eşitleme hizmetini güncelleştirme
Eşitleme hizmetini yeni parolayla güncelleştirmek için:

1. Synchronization Service Manager başlatın (→ eşitleme hizmetini başlatın).
</br>![Eşitleme Service Manager](./media/how-to-connect-sync-change-addsacct-pass/startmenu.png)  

2. **Bağlayıcılar** sekmesine gidin.

3. Parolasının değiştiği AD DS hesabına karşılık gelen **ad bağlayıcısını** seçin.

4. **Eylemler** altında **Özellikler**' i seçin.

5. Açılır iletişim kutusunda **Active Directory ormana Bağlan**' ı seçin:

6. **Parola** metin kutusuna AD DS hesabının yeni parolasını girin.

7. Yeni parolayı kaydetmek ve açılır iletişim kutusunu kapatmak için **Tamam** ' ı tıklatın.

8. Windows hizmet denetimi Yöneticisi altında Azure AD Connect eşitleme hizmetini yeniden başlatın. Bu, eski parolaya yapılan herhangi bir başvurunun bellek önbelleğinden kaldırılmasını sağlamaktır.

## <a name="next-steps"></a>Sonraki adımlar
**Genel Bakış konuları**

* [Azure AD Connect eşitleme: eşitlemeyi anlama ve özelleştirme](how-to-connect-sync-whatis.md)

* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](whatis-hybrid-identity.md)
