---
title: Koşullu erişim-konuma göre erişimi engelleyin-Azure Active Directory
description: Kaynaklara erişimi IP konumuna göre engellemek için özel bir koşullu erişim ilkesi oluşturma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: e257ab39257b23c52aaadbe32f0325e8d71a8409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98597975"
---
# <a name="conditional-access-block-access-by-location"></a>Koşullu erişim: konuma göre erişimi engelleyin

Koşullu erişimde konum koşulu ile, bir kullanıcının ağ konumuna bağlı olarak bulut uygulamalarınıza erişimi denetleyebilirsiniz. Konum koşulu, kuruluşunuzun trafiği bildiği ülkelerden/bölgelerden erişimi engellemek için yaygın olarak kullanılır.

## <a name="define-locations"></a>Konumları tanımlama

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**  >  **konumuna gidin**.
1. **Yeni konum**' ı seçin.
1. Konumunuza bir ad verin.
1. Bu konumu veya **ülkeleri/bölgeleri** oluşturan belirli dışarıdan erişilebilir IPv4 adresi aralıklarını biliyorsanız, **IP aralıklarını** seçin.
   1. Belirttiğiniz konum için **IP aralıklarını** sağlayın veya **ülke/bölge** seçin.
      * Ülkeler/bölgeler ' i seçerseniz, isteğe bağlı olarak bilinmeyen alanları eklemeyi seçebilirsiniz.
1. **Kaydet** 'i seçin

Koşullu erişim 'deki konum koşulu hakkında daha fazla bilgi için, [Azure Active Directory Koşullu erişim ' deki konum koşulunun ne olduğunu](location-condition.md) bulabilirsiniz

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**'ne gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar** altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   1. **Dışla** altında, **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
   1. **Bitti** seçeneğini belirleyin.
1. **Bulut uygulamaları veya eylemler** altında  >  , **tüm bulut uygulamaları**' nı seçin.
1. **Koşulların** bulunduğu  >  **konum**.
   1. **Yapılandır** 'ı **Evet** olarak ayarla
   1. **Dahil et** altında **Seçili konumlar** ' ı seçin.
   1. Kuruluşunuz için oluşturduğunuz engellenen konumu seçin.
   1. **Seç**’e tıklayın.
1. **Erişim denetimleri** altında > **erişimi engelle**' yi seçin ve **Seç**' i seçin.
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık** olarak ayarlayın.
1. Koşullu erişim Ilkesi oluşturmak için **Oluştur** ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim yalnızca rapor modunu kullanarak etkiyi belirleme](howto-conditional-access-insights-reporting.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
