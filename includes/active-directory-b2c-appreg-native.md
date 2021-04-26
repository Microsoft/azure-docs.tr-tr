---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/02/2020
ms.author: mimart
ms.openlocfilehash: 2ad6b90616077a6d18550e86692b109bda622af7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027013"
---
Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *nativeapp1*.
1. **Desteklenen hesap türleri** altında **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında, **genel istemci/yerel ' i (mobil & Masaüstü)** seçmek için açılan eklentiyi kullanın.
1. Benzersiz bir düzene sahip bir yeniden yönlendirme URI 'SI girin. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Yeniden yönlendirme URI 'SI seçilirken önemli noktalar vardır:
    * **Geliştirme** Geliştirme kullanımı için yeniden yönlendirme URI 'sini olarak ayarlayabilirsiniz `http://localhost` ve Azure AD B2C istekteki herhangi bir bağlantı noktasına göre değişir. Kayıtlı URI bir bağlantı noktası içeriyorsa Azure AD B2C yalnızca bu bağlantı noktasını kullanır. Örneğin, kayıtlı yeniden yönlendirme URI 'SI ise, `http://localhost` istekteki yeniden YÖNLENDIRME URI 'si olabilir `http://localhost:<randomport>` . Kayıtlı yeniden yönlendirme URI 'SI ise `http://localhost:8080` , istekteki yeniden YÖNLENDIRME URI 'si olmalıdır `http://localhost:8080` .
    * **Benzersiz**: YENIDEN yönlendirme URI 'sinin şeması her uygulama için benzersiz olmalıdır. Örnekte `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` , `com.onmicrosoft.contosob2c.exampleapp` şemadır. Bu düzenin izlenmesi gerekir. İki uygulama aynı düzeni paylaşıyorsa, kullanıcıya bir uygulama seçme seçeneği verilir. Kullanıcı yanlış seçerse, oturum açma başarısız olur.
    * **Tamam**: yeniden yönlendirme URI 'sinin hem bir düzen hem de yol olması gerekir. Yol, etki alanından sonra en az bir eğik çizgi içermelidir. Örneğin, `//oauth/` başarısız olsa da işe yarar `//oauth` . URI 'de, alt çizgi gibi özel karakterler eklemeyin.
1. **İzinler** altında, *openıd ve offline_access izinleri Için yönetici izni ver* onay kutusunu seçin.
2. **Kaydet**’i seçin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulamalar (eski)** öğesini seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *nativeapp1*.
1. **Yerel istemci** için **Evet**' i seçin.
1. Benzersiz bir şemaya sahip **özel bir yeniden yönlendirme URI 'si** girin. Örneğin, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`. Yeniden yönlendirme URI 'SI seçerken dikkat etmeniz gereken iki önemli nokta vardır:
    * **Benzersiz**: YENIDEN yönlendirme URI 'sinin şeması her uygulama için benzersiz olmalıdır. Örnekte `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect` , `com.onmicrosoft.contosob2c.exampleapp` şemadır. Bu düzenin izlenmesi gerekir. İki uygulama aynı düzeni paylaşıyorsa, kullanıcıya bir uygulama seçme seçeneği verilir. Kullanıcı yanlış seçerse, oturum açma başarısız olur.
    * **Tamam**: yeniden yönlendirme URI 'sinin hem bir düzen hem de yol olması gerekir. Yol, etki alanından sonra en az bir eğik çizgi içermelidir. Örneğin, `//oauth/` başarısız olsa da işe yarar `//oauth` . URI 'de, alt çizgi gibi özel karakterler eklemeyin.
1. **Oluştur**’u seçin.