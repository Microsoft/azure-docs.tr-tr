---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 7cbf6ca7606210b70ae9efa69a8cc6551eb6f36f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106073590"
---
Web API kaynakları, erişim belirteci sunan istemci uygulamalarına göre korunan kaynak isteklerini kabul etmeden ve bunlara yanıt verebilmeleri için kiracınızda kayıtlı olmalıdır.

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](../articles/active-directory-b2c/app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *webapi1*.
1. **Yeniden yönlendirme URI 'si** altında **Web**' i seçin ve ardından Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gerektiği bir uç nokta girin. Bu öğreticide, örnek yerel olarak çalışır ve tarihinde dinler `http://localhost:5000` .
1. **Kaydet**’i seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Ardından, örtük izin akışını etkinleştirin:

1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. **Kaydet**’i seçin.

#### <a name="applications"></a>[Uygulamalar](#tab/applications/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Uygulamalar (eski)** öğesini seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *webapi1*.
1. **Web uygulaması/Web API 'si** için **Evet**' i seçin.
1. **Örtük akışa Izin ver** için **Evet**' i seçin.
1. **Yanıt URL 'si** için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Bu öğreticide, örnek yerel olarak çalışır ve tarihinde dinler `https://localhost:5000` .
1. **Uygulama KIMLIĞI URI**'si IÇIN gösterilen URI 'ye bir API uç noktası tanımlayıcısı ekleyin. Bu öğreticide `api` , tam URI 'nin şuna benzer olması için girin `https://contosob2c.onmicrosoft.com/api` .
1. **Oluştur**’u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama kimliğini** kaydedin.