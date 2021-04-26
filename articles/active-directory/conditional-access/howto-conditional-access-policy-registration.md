---
title: Koşullu erişim-Birleşik güvenlik bilgileri-Azure Active Directory
description: Güvenlik bilgileri kaydı için özel bir koşullu erişim ilkesi oluşturma
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 04/20/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05aca853e2eba98d224131c98751b4e2f4200024
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765656"
---
# <a name="conditional-access-securing-security-info-registration"></a>Koşullu Erişim: Güvenlik bilgileri kaydının güvenliğini sağlama

Kullanıcıların Azure AD Multi-Factor Authentication ve self servis parola sıfırlaması için ne zaman ve nasıl kaydolacağı, koşullu erişim ilkesindeki Kullanıcı eylemleriyle nasıl mümkün olduğunu öğrenin. Bu özellik, [Birleşik kaydı](../authentication/concept-registration-mfa-sspr-combined.md)etkinleştiren kuruluşlar tarafından kullanılabilir. Bu işlevsellik, kuruluşların bir koşullu erişim ilkesindeki herhangi bir uygulama gibi kayıt işlemini işlemesini ve deneyimin güvenliğini sağlamak için Koşullu erişimin tam gücünü kullanmasını sağlar. 

Geçmişteki bazı kuruluşlar, kayıt deneyimini güvenli hale getirmek için bir yol olarak güvenilir ağ konumu veya cihaz uyumluluğu kullanmış olabilir. Azure AD 'de [geçici erişim geçişinin](../authentication/howto-authentication-temporary-access-pass.md) eklenmesiyle, Yöneticiler kullanıcılara herhangi bir cihazdan veya konumdan kaydolmalarına izin veren zamana sınırlı kimlik bilgileri sağlayabilir. Geçici erişim geçiş kimlik bilgileri, çok faktörlü kimlik doğrulaması için koşullu erişim gereksinimlerini karşılar.

## <a name="create-a-policy-to-secure-registration"></a>Kayıt güvenliğini sağlamak için bir ilke oluşturun

Aşağıdaki ilke, birleştirilmiş kayıt deneyimini kullanarak kaydolmayı deneyen seçili kullanıcılar için geçerlidir. İlke, kullanıcıların Multi-Factor Authentication gerçekleştirmesini veya geçici erişim geçiş kimlik bilgilerini kullanmasını gerektirir.

1. **Azure Portal** **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**' ne gidin.
1. **Yeni ilke**' yi seçin.
1. Ad alanına bu ilke için bir ad girin. Örneğin, **dokunarak Birleşik güvenlik bilgileri kaydı**.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' ı seçin ve bu ilkenin uygulanmasını istediğiniz kullanıcıları ve grupları seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.

      > [!WARNING]
      > [Birleşik kayıt](../authentication/howto-registration-mfa-sspr-combined.md)için kullanıcıların etkinleştirilmiş olması gerekir.

   1. **Hariç tut**.
      1. **Tüm konuk ve dış kullanıcılar ' ı** seçin.
      
         > [!NOTE]
         > Geçici erişim geçişi, Konuk kullanıcılar için çalışmaz.

      1. **Kullanıcılar ve gruplar** ' ı seçin ve kuruluşunuzun acil erişim veya kesme camı hesaplarını seçin. 
1. **Bulut uygulamaları veya eylemler** altında **Kullanıcı eylemleri**' ni seçin, **güvenlik bilgilerini kaydet**' i işaretleyin.
1. **Erişim denetimleri**  >  **izni** altında.
   1. **Erişime izin ver**’i seçin.
   1. **Multi-Factor Authentication gerektir**' i seçin.
   1. **Seç**’e tıklayın.
1. **İlkeyi etkinleştir**’i **Açık** duruma getirin.
1. Ardından **Oluştur**’u seçin.

Yöneticiler artık yeni kullanıcılara geçici erişim geçiş kimlik bilgileri vermek zorunda kalacak, böylelikle Multi-Factor Authentication 'ın kaydedileceği gereksinimleri karşılayabilirler. Bu görevi gerçekleştirmek için gereken adımlar, [Azure AD portalında geçici erişim geçişi oluşturma](../authentication/howto-authentication-temporary-access-pass.md#create-a-temporary-access-pass)bölümünde bulunur.

Kuruluşlar, 6B adımında **Multi-Factor Authentication gerektir** ' in yanı sıra diğer izin denetimleri gerektirmesinin gerekli olmasını seçebilirler. Birden çok denetim seçerken, bu değişikliği yaparken seçili denetimlerden **Tümünü** veya **birini** gerektirmek için uygun radyo düğmesini seçtiğinizden emin olun.

### <a name="guest-user-registration"></a>Konuk Kullanıcı kaydı

Dizininizde Multi-Factor Authentication 'a kaydolmaları gereken [Konuk kullanıcılar](../external-identities/what-is-b2b.md) için, aşağıdaki kılavuzu kullanarak, [Güvenilen ağ konumları](concept-conditional-access-conditions.md#locations) dışındaki kayıtları engellemeyi tercih edebilirsiniz.

1. **Azure Portal** **Azure Active Directory**  >  **güvenlik**  >  **koşullu erişimi**' ne gidin.
1. **Yeni ilke**' yi seçin.
1. Ad alanına bu ilke için bir ad girin. Örneğin, **güvenilir ağlarda Birleşik güvenlik bilgileri kaydı**.
1. **Atamalar**' ın altında, **Kullanıcılar ve gruplar**' ı seçin ve bu ilkenin uygulanmasını istediğiniz kullanıcıları ve grupları seçin.
   1. **Ekle**' nin altında **tüm konuk ve dış kullanıcılar**' ı seçin.
1. **Bulut uygulamaları veya eylemler** altında **Kullanıcı eylemleri**' ni seçin, **güvenlik bilgilerini kaydet**' i işaretleyin.
1. **Koşul**  >  **konumları** altında.
   1. **Evet 'i** yapılandırın.
   1. **Herhangi bir konum** ekleyin.
   1. **Tüm güvenilen konumları** hariç tutun.
   1. Konumlar dikey penceresinde **bitti** ' yi seçin.
   1. Koşullar dikey penceresinde **bitti** ' yi seçin.
1. **Erişim denetimleri**  >  **izni** altında.
   1. **Erişimi engelle**' yi seçin.
   1. Ardından **Seç**'e tıklayın.
1. **İlkeyi etkinleştir**’i **Açık** duruma getirin.
1. Sonra **Kaydet**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim yalnızca rapor modunu kullanarak etkiyi belirleme](howto-conditional-access-insights-reporting.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)

[Kullanıcıların kimlik doğrulama bilgilerini yeniden onaylamasını gerektir](../authentication/concept-sspr-howitworks.md#reconfirm-authentication-information)
