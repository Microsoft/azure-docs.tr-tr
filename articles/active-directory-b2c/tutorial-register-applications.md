---
title: Öğretici-uygulamayı kaydetme-Azure Active Directory B2C
description: Azure portal kullanarak bir Web uygulamasını Azure Active Directory B2C nasıl kaydedeceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3a5d5099c91736276aca56c8b94b846b53a190c1
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73642427"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>Öğretici: uygulamayı Azure Active Directory B2C kaydetme

[Uygulamalarınızın](active-directory-b2c-apps.md) Azure Active Directory B2C (Azure AD B2C) ile etkileşime girebilmesi için, yönettiğiniz bir kiracıda kayıtlı olmaları gerekir. Bu öğreticide, Azure portal kullanarak bir Web uygulamasının nasıl kaydedileceği gösterilmektedir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Web uygulaması kaydetme
> * İstemci parolası oluşturma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Ön koşullar

Kendi [Azure AD B2C kiracınızı](tutorial-create-tenant.md)önceden oluşturmadıysanız, şimdi bir tane oluşturun. Mevcut bir Azure AD B2C kiracı kullanabilirsiniz.

## <a name="register-a-web-application"></a>Web uygulaması kaydetme

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için, geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. Üst menüdeki **Dizin + abonelik** filtresini seçip kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Uygulamalar**' ı seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *WebApp1*.
1. **Web uygulaması/Web API 'Si Ekle** ve **örtük akışa Izin ver**için **Evet**' i seçin.
1. **Yanıt URL 'si**için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Örneğin, bunu `https://localhost:44316`yerel olarak dinlemek için ayarlayabilirsiniz. Bağlantı noktası numarasını henüz bilmiyorsanız, bir yer tutucu değeri girip daha sonra değiştirebilirsiniz.

    Bu öğreticide test amacıyla, inceleme için bir belirtecin içeriğini görüntüleyen `https://jwt.ms` olarak ayarlayabilirsiniz. Bu öğreticide, **yanıt URL** 'sini `https://jwt.ms`olarak ayarlayın.

    Yanıt URL 'Leri için aşağıdaki kısıtlamalar geçerlidir:

    * Yanıt URL 'SI `https`düzeniyle başlamalıdır.
    * Yanıt URL 'SI, büyük/küçük harfe duyarlıdır. Büyük/küçük harf durumu, çalışan uygulamanızın URL yolu ile aynı olmalıdır. Örneğin, uygulamanız `.../abc/response-oidc`yolunun bir parçası olarak içeriyorsa, yanıt URL 'sinde `.../ABC/response-oidc` belirtmeyin. Web tarayıcısı yollara büyük/küçük harfe duyarlı olarak davrandığı için, `.../abc/response-oidc` ile ilişkili tanımlama bilgileri, büyük/küçük harfe eşleşmeyen `.../ABC/response-oidc` URL 'sine yönlendiriliyorsa dışlanamaz.

1. Uygulama kaydını tamamlayabilmeniz için **Oluştur** ' a tıklayın.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüden **Dizin + abonelik** filtresi ' ni seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *WebApp1*.
1. **Herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar '** ı seçin.
1. **Yeniden yönlendirme URI 'si**altında **Web**' i seçin ve ardından URL metin kutusuna `https://jwt.ms` yazın.

    Yeniden yönlendirme URI 'SI, kullanıcının kullanıcı etkileşimini tamamladıktan sonra, bir erişim belirteci veya yetkilendirme kodu başarıyla yetkilendirmede gönderildiğinde, kullanıcının yetkilendirme sunucusu tarafından (Bu durumda Azure AD B2C) gönderildiği uç noktadır. Bir üretim uygulamasında, genellikle uygulamanızın çalıştığı, `https://contoso.com/auth-response`gibi genel olarak erişilebilen bir uç noktasıdır. Bu öğreticide olduğu gibi test amacıyla, bir belirtecin kodu çözülmüş içeriğini görüntüleyen Microsoft 'a ait bir Web uygulaması olan `https://jwt.ms`olarak ayarlayabilirsiniz (belirtecin içeriği hiçbir şekilde tarayıcıdan ayrılmayın). Uygulama geliştirme sırasında, uygulamanızın yerel olarak dinlediği `https://localhost:5000`gibi bir uç nokta ekleyebilirsiniz. İstediğiniz zaman kayıtlı uygulamalarınıza yeniden yönlendirme URI 'Leri ekleyebilir ve bunları değiştirebilirsiniz.

    Yeniden yönlendirme URI 'Leri için aşağıdaki kısıtlamalar geçerlidir:

    * Yanıt URL 'SI `https`düzeniyle başlamalıdır.
    * Yanıt URL 'SI, büyük/küçük harfe duyarlıdır. Büyük/küçük harf durumu, çalışan uygulamanızın URL yolu ile aynı olmalıdır. Örneğin, uygulamanız `.../abc/response-oidc`yolunun bir parçası olarak içeriyorsa, yanıt URL 'sinde `.../ABC/response-oidc` belirtmeyin. Web tarayıcısı yollara büyük/küçük harfe duyarlı olarak davrandığı için, `.../abc/response-oidc` ile ilişkili tanımlama bilgileri, büyük/küçük harfe eşleşmeyen `.../ABC/response-oidc` URL 'sine yönlendiriliyorsa dışlanamaz.

1. **İzinler**altında, *OpenID ve offline_access Permissions Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydol**’u seçin.

Uygulama kaydı tamamlandıktan sonra, örtük verme akışını etkinleştirin:

1. **Yönet**altında **kimlik doğrulaması**' nı seçin.
1. **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. **Kaydet**’i seçin.

* * *

## <a name="create-a-client-secret"></a>İstemci parolası oluşturma

Uygulamanız belirteç için bir kod alışverişi yaptıysanız, bir uygulama gizli anahtarı oluşturmanız gerekir.

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. **Azure AD B2C-uygulamalar** sayfasında, oluşturduğunuz uygulamayı seçin, örneğin, *WebApp1*.
1. **Anahtarlar** ' ı seçin ve ardından **anahtar oluştur**' u seçin.
1. Anahtarı görüntülemek için **Kaydet** ' i seçin. **Uygulama anahtarı** değerini not edin. Bu değeri, uygulamanızın kodunda uygulama gizli anahtarı olarak kullanırsınız.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. **Azure AD B2C-uygulama kayıtları (Önizleme)** sayfasında, oluşturduğunuz uygulamayı seçin, örneğin *WebApp1*.
1. **Yönet**altında **Sertifikalar & gizlilikler**' ı seçin.
1. **Yeni istemci gizli dizisi**’ni seçin.
1. **Açıklama** kutusuna istemci parolası için bir açıklama girin. Örneğin, *clientsecret1*.
1. **Süre sonu**altında, parolasının geçerli olduğu bir süre seçin ve ardından **Ekle**' yi seçin.
1. Gizli dizi **değerini**kaydedin. Bu değeri, uygulamanızın kodunda uygulama gizli anahtarı olarak kullanırsınız.

* * *

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Web uygulaması kaydetme
> * İstemci parolası oluşturma

Ardından, kullanıcılarınızın kaydolmalarına, oturum açmalarına ve profillerini yönetmesine olanak tanımak için Kullanıcı akışları oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C > Kullanıcı akışları oluşturma](tutorial-create-user-flows.md)
