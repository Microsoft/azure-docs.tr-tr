---
title: Koşullu erişim-konuma göre erişimi engelleyin-Azure Active Directory
description: Kaynaklara erişimi IP konumuna göre engellemek için özel bir koşullu erişim ilkesi oluşturma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64b7add9ddb1734d763c46e93d6788ce21a2c321
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150882"
---
# <a name="conditional-access-block-access-by-location"></a>Koşullu erişim: konuma göre erişimi engelleyin

Koşullu erişimde konum koşulu ile, bir kullanıcının ağ konumuna bağlı olarak bulut uygulamalarınıza erişimi denetleyebilirsiniz. Konum koşulu genellikle kuruluşunuzun trafiği bildiği ülkelerden gelen erişimi engellemek için kullanılır.

## <a name="define-locations"></a>Konumları tanımlama

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory** > **koşullu erişime**gidin.
1. **Yeni konum**' ı seçin.
1. Konumunuza bir ad verin.
1. Bu konumu veya **ülkeleri/bölgeleri**oluşturan belirli dışarıdan erişilebilir IPv4 adresi aralıklarını biliyorsanız, **IP aralıklarını** seçin.
   1. Belirttiğiniz konum için **IP aralıklarını** sağlayın veya **ülke/bölge** seçin.
      * Ülkeler/bölgeler ' i seçerseniz, isteğe bağlı olarak bilinmeyen alanları eklemeyi seçebilirsiniz.
1. **Kaydet** 'i seçin

Koşullu erişim 'deki konum koşulu hakkında daha fazla bilgi için, [Azure Active Directory Koşullu erişim ' deki konum koşulunun ne olduğunu](location-condition.md) bulabilirsiniz

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory** > **koşullu erişime**gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar**altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   1. **Done** (Bitti) öğesini seçin.
1. **Bulut uygulamaları veya eylemler** altında **dahil** > , **tüm bulut uygulamaları**' nı seçin ve **bitti**' yi seçin.
1. **Koşullar** > **konum**altında.
   1. **Yapılandır** 'ı **Evet** olarak ayarla
   1. **Seçili konumların** seçimini **Ekle**
   1. Kuruluşunuz için oluşturduğunuz engellenen konumu seçin.
   1.  > **Seç** ' e tıklayın > **bitti**' ye tıklayın.
1. **Erişim denetimleri** > **blok**altında, **Seç**' i seçin.
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık**olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
