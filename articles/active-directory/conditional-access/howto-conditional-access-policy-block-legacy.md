---
title: Koşullu erişim-blok eski kimlik doğrulaması-Azure Active Directory
description: Eski kimlik doğrulama protokollerini engellemek için özel bir koşullu erişim ilkesi oluşturma
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
ms.openlocfilehash: b992973beb7cb132075e47e104733d812dc06ca0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73151089"
---
# <a name="conditional-access-block-legacy-authentication"></a>Koşullu erişim: eski kimlik doğrulamasını engelle

Eski kimlik doğrulama protokolleriyle ilişkili risk artışı nedeniyle, Microsoft kuruluşların bu protokolleri kullanarak kimlik doğrulama isteklerini engellemesini ve modern kimlik doğrulaması gerektirmesini önerir.

## <a name="create-a-conditional-access-policy"></a>Koşullu erişim ilkesi oluşturma

Aşağıdaki adımlar, eski kimlik doğrulama isteklerini engellemek için bir koşullu erişim ilkesi oluşturmanıza yardımcı olur.

1. **Azure Portal** genel yönetici, güvenlik yöneticisi veya koşullu erişim Yöneticisi olarak oturum açın.
1. **Azure Active Directory** > **koşullu erişime**gidin.
1. **Yeni ilke**' yi seçin.
1. İlkenize bir ad verin. Kuruluşların ilkelerinin adları için anlamlı bir standart oluşturmasını öneririz.
1. **Atamalar**altında **Kullanıcılar ve gruplar** ' ı seçin.
   1. **Ekle**' nin altında **tüm kullanıcılar**' ı seçin.
   1. **Hariç tut**' un altında, **Kullanıcılar ve gruplar** ' ı seçin ve eski kimlik doğrulamasını kullanma yeteneğinin korunması gereken tüm hesapları seçin. 
   1. **Done** (Bitti) öğesini seçin.
1. **Bulut uygulamaları veya eylemler** > **dahil**, **tüm bulut uygulamaları**' nı seçin.
   1. İlkenizin belirli uygulamaları dışlayamazsınız, **Dışlanan bulut uygulamalarını seçin** altında **hariç tut** sekmesinden seçim yapın ve **Seç**' i seçin.
   1. **Done** (Bitti) öğesini seçin.
1. **İstemci uygulamaları (Önizleme)**  > **koşullar** altında **Yapılandır** ' ı **Evet**olarak ayarlayın.
   1. Yalnızca **mobil uygulamalar ve Masaüstü Istemcileri** **diğer istemcileri** > kutulara göz atın.
   2. **Done** (Bitti) öğesini seçin.
1. **Erişim denetimleri** > **izin**' ın altında, **erişimi engelle**' yi seçin.
   1. **Seç**' i seçin.
1. Ayarlarınızı doğrulayın ve **ilke** ayarını **Açık**olarak ayarlayın.
1. İlkenizi etkinleştirmek için oluşturmak **için Oluştur ' u seçin.**

## <a name="next-steps"></a>Sonraki adımlar

[Koşullu erişim ortak ilkeleri](concept-conditional-access-policy-common.md)

[Koşullu erişim What If aracını kullanarak oturum açma davranışının benzetimini yapma](troubleshoot-conditional-access-what-if.md)
