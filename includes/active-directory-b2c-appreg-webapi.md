---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: a286f8d401ec98a5069c07b1a82acdf4af2b8185
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641832"
---
Web API kaynakları, erişim belirteci sunan istemci uygulamalarına göre korunan kaynak isteklerini kabul etmeden ve bunlara yanıt verebilmeleri için kiracınızda kayıtlı olmalıdır.

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için, geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulamalar**' ı seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *webapi1*.
1. **Web uygulaması/Web API 'si**için **Evet**' i seçin.
1. **Örtük akışa Izin ver**için **Evet**' i seçin.
1. **Yanıt URL 'si**için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Bu öğreticide, örnek yerel olarak çalışır ve `https://localhost:5000`' de dinler.
1. **Uygulama KIMLIĞI URI**'si IÇIN gösterilen URI 'ye bir API uç noktası tanımlayıcısı ekleyin. Bu öğreticide, tam URI 'nin `https://contosob2c.onmicrosoft.com/api`benzer olması için `api`girin.
1. **Oluştur**'u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama kimliğini** kaydedin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *webapi1*.
1. **Yeniden yönlendirme URI 'si**altında **Web**' i seçin ve ardından Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gerektiği bir uç nokta girin. Bu öğreticide, örnek yerel olarak çalışır ve `http://localhost:5000`' de dinler.
1. **Kaydol**’u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Ardından, örtük izin akışını etkinleştirin:

1. **Yönet**altında **kimlik doğrulaması**' nı seçin.
1. **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. **Kaydet**’i seçin.