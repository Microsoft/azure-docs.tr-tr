---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 0b5c5fdddeea961858a2c3b8cd69c365bb28f26e
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73800176"
---
Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için, geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulamalar**' ı seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *nativeapp1*.
1. **Yerel istemci**için **Evet**' i seçin.
1. Benzersiz bir şemaya sahip **özel bir yeniden yönlendirme URI 'si** girin. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Yeniden yönlendirme URI 'SI seçerken dikkat etmeniz gereken iki önemli nokta vardır:
    * **Benzersiz**: YENIDEN yönlendirme URI 'sinin şeması her uygulama için benzersiz olmalıdır. Örnek `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` şemadır. Bu düzenin izlenmesi gerekir. İki uygulama aynı düzeni paylaşıyorsa, kullanıcıya bir uygulama seçme seçeneği verilir. Kullanıcı yanlış seçerse, oturum açma başarısız olur.
    * **Tamam**: yeniden yönlendirme URI 'sinin hem bir düzen hem de yol olması gerekir. Yol, etki alanından sonra en az bir eğik çizgi içermelidir. Örneğin, `//oauth` başarısız olduğunda `//oauth/` işe yarar. URI 'de, alt çizgi gibi özel karakterler eklemeyin.
1. **Oluştur**'u seçin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *nativeapp1*.
1. **Desteklenen hesap türleri**altında **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si**altında, **genel istemci/yerel ' i (mobil & Masaüstü)** seçmek için açılan eklentiyi kullanın.
1. Benzersiz bir düzene sahip bir yeniden yönlendirme URI 'SI girin. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Yeniden yönlendirme URI 'SI seçerken dikkat etmeniz gereken iki önemli nokta vardır:
    * **Benzersiz**: YENIDEN yönlendirme URI 'sinin şeması her uygulama için benzersiz olmalıdır. Örnek `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` şemadır. Bu düzenin izlenmesi gerekir. İki uygulama aynı düzeni paylaşıyorsa, kullanıcıya bir uygulama seçme seçeneği verilir. Kullanıcı yanlış seçerse, oturum açma başarısız olur.
    * **Tamam**: yeniden yönlendirme URI 'sinin hem bir düzen hem de yol olması gerekir. Yol, etki alanından sonra en az bir eğik çizgi içermelidir. Örneğin, `//oauth` başarısız olduğunda `//oauth/` işe yarar. URI 'de, alt çizgi gibi özel karakterler eklemeyin.
1. **İzinler**altında, *openıd ve offline_access izinleri Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydol**’u seçin.