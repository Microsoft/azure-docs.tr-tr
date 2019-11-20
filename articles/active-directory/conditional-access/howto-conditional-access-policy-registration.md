---
title: Koşullu erişim-Birleşik güvenlik bilgileri-Azure Active Directory
description: Güvenlik bilgileri kaydı için güvenilir bir konum gerektirmek üzere özel bir koşullu erişim ilkesi oluşturma
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
ms.openlocfilehash: 771e4e0ecbda4baf1f38aacd1f39397875bbd0dc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150768"
---
# <a name="conditional-access-require-trusted-location-for-mfa-registration"></a>Koşullu erişim: MFA kaydı için güvenilen konum gerektir

Kullanıcıların Azure Multi-Factor Authentication ve self servis parola sıfırlaması için ne zaman ve nasıl kaydoldığına ilişkin güvenlik, koşullu erişim ilkesindeki Kullanıcı eylemleri ile mümkün değildir. Bu önizleme özelliği, [birleştirilmiş kayıt önizlemesini](../authentication/concept-registration-mfa-sspr-combined.md)etkinleştirmiş kuruluşlar tarafından kullanılabilir. Bu işlev, kullanıcıların Azure Multi-Factor Authentication ve SSPR 'ye, HR ekleme sırasında güvenilen ağ konumu gibi merkezi bir konumdan kaydolmaları istedikleri kuruluşlarda etkinleştirilebilir. Koşullu erişimde güvenilen konumlar oluşturma hakkında daha fazla bilgi için [Azure Active Directory Koşullu erişim bölümünde konum koşulunun ne olduğuna](../conditional-access/location-condition.md#named-locations) bakın.

## <a name="create-a-policy-to-require-registration-from-a-trusted-location"></a>Güvenilen bir konumdan kayıt gerektirecek bir ilke oluşturun

Aşağıdaki ilke, birleştirilmiş kayıt deneyimini kullanarak kaydolmayı deneyen ve güvenilen ağ olarak işaretlenen bir konumdan bağlanmadıkları sürece erişimi engelleyen tüm seçili kullanıcılar için geçerlidir.

1. **Azure Portal**, **koşullu erişim** > **Azure Active Directory** gidin.
1. **Yeni ilke**' yi seçin.
1. Ad alanına bu ilke için bir ad girin. Örneğin, **güvenilir ağlarda Birleşik güvenlik bilgileri kaydı**.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' a tıklayın ve bu ilkenin uygulanmasını istediğiniz kullanıcıları ve grupları seçin.

   > [!WARNING]
   > [Birleşik kayıt önizlemesi](../authentication/howto-registration-mfa-sspr-combined.md)için kullanıcıların etkinleştirilmiş olması gerekir.

1. **Bulut uygulamaları veya eylemler**altında **Kullanıcı eylemleri**' ni seçin, **güvenlik bilgilerini kaydet ' i (Önizleme)** işaretleyin.
1. **Koşullar** ' ın altında **konumlar** > .
   1. **Evet 'i**yapılandırın.
   1. **Herhangi bir konum**ekleyin.
   1. **Tüm güvenilen konumları**hariç tutun.
   1. Konumlar dikey penceresinde **bitti** ' ye tıklayın.
   1. Koşullar dikey penceresinde **bitti** ' ye tıklayın.
1. **Erişim denetimleri** > **izin ver**' in altında.
   1. **Erişimi engelle**' ye tıklayın.
   1. Ardından **Seç**'e tıklayın.
1. **Etkin Ilkeyi** **Açık**olarak ayarlayın.
1. Sonra **Oluştur**’a tıklayın.

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
