---
title: 'Öğretici: bir uygulamayı kaydetme'
titleSuffix: Azure AD B2C
description: Azure portal kullanarak bir Web uygulamasını Azure Active Directory B2C nasıl kaydedeceğinizi öğrenmek için bu öğreticiyi izleyin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2ec67669edeb52af1044c97c984eb6ba36fd1a0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579646"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>Öğretici: bir Web uygulamasını Azure Active Directory B2C kaydetme

[Uygulamalarınızın](application-types.md) Azure Active Directory B2C (Azure AD B2C) ile etkileşime girebilmesi için, yönettiğiniz bir kiracıda kayıtlı olmaları gerekir. Bu öğreticide, Azure portal kullanarak bir Web uygulamasının nasıl kaydedileceği gösterilmektedir. 

"Web uygulaması", sunucuda uygulama mantığının çoğunu gerçekleştiren geleneksel bir Web uygulamasını ifade eder. Bunlar ASP.NET Core, Maven (Java), Flask (Python) ve Express (Node.js) gibi çerçeveler kullanılarak oluşturulabilir.

> [!IMPORTANT]
> Bunun yerine tek sayfalı bir uygulama ("SPA") kullanıyorsanız (ör. angular, Vue veya yanıt verme kullanarak), [tek sayfalı bir uygulamayı kaydetmeyi](tutorial-register-spa.md)öğrenin.
> 
> Bunun yerine yerel bir uygulama kullanıyorsanız (ör. iOS, Android, mobil & Masaüstü), [yerel bir istemci uygulamasını nasıl kaydedeceğinizi](add-native-application.md)öğrenin.

## <a name="prerequisites"></a>Ön koşullar
Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

Kendi [Azure AD B2C kiracınızı](tutorial-create-tenant.md)önceden oluşturmadıysanız, şimdi bir tane oluşturun. Mevcut bir Azure AD B2C kiracı kullanabilirsiniz.

## <a name="register-a-web-application"></a>Web uygulaması kaydetme

Bir Web uygulamasını Azure AD B2C kiracınıza kaydetmek için yeni Birleşik **uygulama kayıtları** deneyimimizi veya eski  **uygulamalarımız (eski)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** girin. Örneğin, *WebApp1*.
1. **Desteklenen hesap türleri** altında, **herhangi bir kimlik sağlayıcısında veya kuruluş dizininde (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için) hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında **Web**' i seçin ve ardından `https://jwt.ms` URL metin kutusuna girin.

    Yeniden yönlendirme URI 'SI, kullanıcının kullanıcı etkileşimini tamamladıktan sonra, bir erişim belirteci veya yetkilendirme kodu başarıyla yetkilendirmede gönderildiğinde, kullanıcının yetkilendirme sunucusu tarafından (Bu durumda Azure AD B2C) gönderildiği uç noktadır. Bir üretim uygulamasında, genellikle uygulamanızın çalıştığı, genel olarak erişilebilen bir uç noktasıdır `https://contoso.com/auth-response` . Bu öğreticide olduğu gibi test amacıyla, `https://jwt.ms` bir belirtecin kodu çözülmüş içeriğini görüntüleyen Microsoft 'a ait bir Web uygulaması olarak ayarlayabilirsiniz (belirtecin içeriği hiçbir şekilde tarayıcıdan ayrılmayın). Uygulama geliştirme sırasında, uygulamanızın yerel olarak dinlediği uç noktayı ekleyebilirsiniz, örneğin `https://localhost:5000` . İstediğiniz zaman kayıtlı uygulamalarınıza yeniden yönlendirme URI 'Leri ekleyebilir ve bunları değiştirebilirsiniz.

    Yeniden yönlendirme URI 'Leri için aşağıdaki kısıtlamalar geçerlidir:

    * Yanıt URL 'SI, şemayla başlamalıdır `https` .
    * Yanıt URL 'SI, büyük/küçük harfe duyarlıdır. Büyük/küçük harf durumu, çalışan uygulamanızın URL yolu ile aynı olmalıdır. Örneğin, uygulamanız yolunun bir parçası olarak içeriyorsa `.../abc/response-oidc` , `.../ABC/response-oidc` yanıt URL 'sinde belirtmeyin. Web tarayıcısı yollara büyük/küçük harfe duyarlı olarak davrandığı için, bununla ilişkili tanımlama bilgileri, `.../abc/response-oidc` büyük/küçük harfe eşleşmeyen URL 'ye yönlendiriliyorsa dışlanamaz `.../ABC/response-oidc` .

1. **İzinler** altında, *openıd ve offline_access izinleri Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydet**’i seçin.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Uygulamalar (eski)** öğesini seçin ve ardından **Ekle**' yi seçin.
1. Uygulama için bir ad girin. Örneğin, *WebApp1*.
1. **İçerme Web uygulaması/Web API 'si** için **Evet**' i seçin.
1. **Yanıt URL 'si** için Azure AD B2C uygulamanızın isteklerinizin belirteçleri döndürmesi gereken bir uç nokta girin. Örneğin, bunu yerel olarak dinlemek için ayarlayabilirsiniz `http://localhost:5000` . İstediğiniz zaman kayıtlı uygulamalarınıza yeniden yönlendirme URI 'Leri ekleyebilir ve bunları değiştirebilirsiniz.

    Yeniden yönlendirme URI 'Leri için aşağıdaki kısıtlamalar geçerlidir:

    * Yanıt URL 'SI, `https` kullanmadıkça, şemayla başlamalıdır `localhost` .
    * Yanıt URL 'SI, büyük/küçük harfe duyarlıdır. Büyük/küçük harf durumu, çalışan uygulamanızın URL yolu ile aynı olmalıdır. Örneğin, uygulamanız yolunun bir parçası olarak içeriyorsa `.../abc/response-oidc` , `.../ABC/response-oidc` yanıt URL 'sinde belirtmeyin. Web tarayıcısı yollara büyük/küçük harfe duyarlı olarak davrandığı için, bununla ilişkili tanımlama bilgileri, `.../abc/response-oidc` büyük/küçük harfe eşleşmeyen URL 'ye yönlendiriliyorsa dışlanamaz `.../ABC/response-oidc` .

1. Uygulama kaydını tamamlayabilmeniz için **Oluştur** ' u seçin.

* * *

## <a name="create-a-client-secret"></a>İstemci parolası oluşturma

Bir Web uygulaması için bir uygulama gizli anahtarı oluşturmanız gerekir. İstemci parolası, *uygulama parolası* olarak da bilinir. Gizli anahtar, uygulamanız tarafından erişim belirtecinin yetkilendirme kodunu değiş tokuş etmek için kullanılacaktır.

#### <a name="app-registrations"></a>[Uygulama kayıtları](#tab/app-reg-ga/)

1. **Azure AD B2C-uygulama kayıtları** sayfasında, oluşturduğunuz uygulamayı seçin, örneğin *WebApp1*.
1. Sol taraftaki menüde, **Yönet** altında, **Sertifikalar & gizlilikler**' ı seçin.
1. **Yeni istemci gizli dizisi**’ni seçin.
1. **Açıklama** kutusuna istemci parolası için bir açıklama girin. Örneğin, *clientsecret1*.
1. **Süre sonu** altında, parolasının geçerli olduğu bir süre seçin ve ardından **Ekle**' yi seçin.
1. Gizli dizi **değerini** istemci uygulama kodunuzda kullanmak üzere kaydedin. Bu gizli değer bu sayfadan ayrıldıktan sonra hiçbir şekilde hiçbir şekilde gösterilmez. Bu değeri, uygulamanızın kodunda uygulama gizli anahtarı olarak kullanırsınız.

#### <a name="applications-legacy"></a>[Uygulamalar (eski)](#tab/applications-legacy/)

1. **Azure AD B2C-uygulamalar** sayfasında, oluşturduğunuz uygulamayı seçin, örneğin, *WebApp1*.
1. **Anahtarlar** ' ı seçin ve ardından **anahtar oluştur**' u seçin.
1. Anahtarı görüntülemek için **Kaydet** ' i seçin. **Uygulama anahtarı** değerini not edin. Bu değeri, uygulamanızın kodunda uygulama gizli anahtarı olarak kullanırsınız.

* * *

> [!NOTE]
> Güvenlik nedeniyle, uygulama gizliliğini düzenli olarak veya acil durumda anında alabilirsiniz. Azure AD B2C ile tümleştirilen tüm uygulamalar, ne sıklıkta gerçekleşebileceğini bağımsız olarak gizli bir rollover olayını işleyecek şekilde hazırlanmalıdır. Uygulama gizli anahtarı döndürme olayı sırasında uygulamanızın eski gizli dizileri kullanmaya devam etmesini sağlayarak iki uygulama parolası ayarlayabilirsiniz. Başka bir istemci parolası eklemek için bu bölümdeki adımları yineleyin. 

## <a name="enable-id-token-implicit-grant"></a>KIMLIK belirteci örtük izni etkinleştir

Örtük izin tanımlama özelliği, KIMLIK ve erişim belirteçleri gibi belirteçlerin uygulamaya doğrudan Azure AD B2C döndürüldüğünden oluşur. ASP.NET Core Web Apps ve [https://jwt.ms](https://jwt.ms) doğrudan yetkilendirme uç noktasından BIR kimlik belirteci isteyen Web Apps için, uygulama kaydında örtük verme akışını etkinleştirin.

1. Sol taraftaki menüde, **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. Örtük izin ' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. **Kaydet**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Web uygulaması kaydetme
> * İstemci parolası oluşturma

Ardından, kullanıcılarınızın kaydolmalarına, oturum açmalarına ve profillerini yönetmesine olanak tanımak için Kullanıcı akışları oluşturmayı öğrenin.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C >Kullanıcı akışları oluşturma ](tutorial-create-user-flows.md)
