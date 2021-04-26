---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 38261556579fb5db86fb231b0edf3412eed51d35
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073075"
---
Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *ROPC_Auth_app*.
1. Diğer değerleri olduğu gibi bırakın ve **Kaydet**' i seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.
1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Varsayılan istemci türü** altında, uygulamayı ortak istemci olarak değerlendirmek için **Evet** ' i seçin. Bu ayar, ROPC akışı için gereklidir.
1. **Kaydet**’i seçin.
1. Sol taraftaki menüden **bildirim** ' ı seçerek bildirim düzenleyicisini açın. 
1. **Oauth2AllowImplicitFlow** özniteliğini *true* olarak ayarlayın:
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. **Kaydet**’i seçin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulamalar (eski)** öğesini seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *ROPC_Auth_app*.
1. **Yerel istemci** için **Evet**' i seçin.
1. Diğer değerleri olduğu gibi bırakın ve ardından **Oluştur**' u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama kimliğini** kaydedin.