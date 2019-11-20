---
title: Kullanıcılarda oturum açan Web uygulaması (uygulama kaydı)-Microsoft Identity platform
description: Kullanıcılara oturum açan bir Web uygulaması oluşturma hakkında bilgi edinin (uygulama kaydı)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 641f71f6111930b54d0a2bd548f16d3cb0c07189
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175269"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>Kullanıcılar-uygulama kaydı 'nda oturum açan Web uygulaması

Bu sayfada, kullanıcıları oturum açan bir Web uygulaması için uygulama kaydı özellikleri açıklanmaktadır.

Uygulamanızı kaydetmek için şunu kullanabilirsiniz:

- [Web uygulaması hızlı](#register-an-app-using-the-quickstarts) başlangıçlar-uygulama oluşturmaya yönelik harika bir ilk deneyim olmasının yanı sıra Azure Portal Hızlı başlangıçlar, **Bu değişikliği bana yap**adlı bir düğme içerir. Bu düğmeyi, mevcut bir uygulama için bile ihtiyacınız olan özellikleri ayarlamak için kullanabilirsiniz. Bu özelliklerin değerlerini kendi servis talebine uyarlamanız gerekir. Özellikle, uygulamanız için Web API URL 'SI, büyük olasılıkla önerilen varsayılan verilerden farklı olacak ve bu da oturum kapatma URI 'sini etkileyecek.
- [Uygulamanızı el ile kaydetmek](#register-an-app-using-azure-portal) için Azure Portal
- PowerShell ve komut satırı araçları

## <a name="register-an-app-using-the-quickstarts"></a>Hızlı başlangıçlarını kullanarak bir uygulamayı kaydetme

Bu bağlantıya gittiğinizde, Web uygulamanızın oluşturulmasını önyükleyebilirsiniz:

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

## <a name="register-an-app-using-azure-portal"></a>Azure portal kullanarak bir uygulamayı kaydetme

> [!NOTE]
> Uygulamanızın Microsoft Azure genel bulutta veya ulusal ya da bağımsız bulutunda çalışmasına bağlı olarak, kullanılacak Portal farklıdır. Daha fazla bilgi için bkz. [Ulusal bulutlar](./authentication-national-cloud.md#app-registration-endpoints)


1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın. Alternatif olarak, tercih ettiğiniz Ulusal bulut Azure portal oturum açın.
1. Hesabınız birden fazla kiracıya erişim veriyorsa, sağ üst köşede hesabınızı seçin ve Portal oturumunuzu istenen Azure AD kiracısı olarak ayarlayın.
1. Sol taraftaki Gezinti bölmesinde **Azure Active Directory** hizmetini seçin ve sonra **yeni kayıt** > **uygulama kayıtları** seçin.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

4. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
   1. uygulamanız için desteklenen hesap türlerini seçin (bkz. [Desteklenen hesap türleri](./v2-supported-account-types.md))
   1. **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `AspNetCore-WebApp`.
   1. **Yeniden yönlendirme URI 'si**içinde, başarıyla kimlik doğrulamasından sonra döndürülen belirteç yanıtlarını kabul edecek olan uygulama türünü ve URI hedefini ekleyin. Örneğin, `https://localhost:44321/`.  **Kaydol**’u seçin.
1. **Kimlik doğrulama** menüsünü seçin ve ardından aşağıdaki bilgileri ekleyin:
   1. **Yanıt URL 'si**' nde, "Web" türünde `https://localhost:44321/signin-oidc` ekleyin.
   1. **Gelişmiş ayarlar** bölümünde, **oturum kapatma URL 'sini** `https://localhost:44321/signout-oidc`olarak ayarlayın.
   1. **Örtük izin**' ın altında, **kimlik belirteçlerini**denetleyin.
   1. **Kaydet**’i seçin.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

4. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
   1. uygulamanız için desteklenen hesap türlerini seçin (bkz. [Desteklenen hesap türleri](./v2-supported-account-types.md))
   - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `MailApp-openidconnect-v2`.
   - Yeniden yönlendirme URI 'SI (isteğe bağlı) bölümünde, açılan kutudan **Web** ' i seçin ve aşağıdaki yeniden yönlendirme URI 'lerini girin: `https://localhost:44326/`.
1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. **Kimlik doğrulama** menüsünü seçin ve ardından aşağıdaki bilgileri ekleyin:
   - **Gelişmiş ayarlar** | **örtük verme** bölümünde, bu örnek, **kimlik belirteçlerini** denetleyerek kullanıcının oturum açması için [örtük verme akışının](v2-oauth2-implicit-grant-flow.md) etkinleştirilmesini gerektirir.
1. **Kaydet**’i seçin.

# <a name="javatabjava"></a>[Java](#tab/java)

4. **Bir uygulamayı Kaydet sayfası** göründüğünde, uygulama için kolay bir ad girin (örneğin, ' Java-WebApp '), "herhangi bir kurumsal dizin ve kişisel Microsoft hesabında (örn. Skype, Xbox, Outlook.com)" seçeneğini belirleyin ve ' Web uygulaması ' nı seçin. *Uygulama türü*olarak.
1. Uygulamayı kaydetmek için **Kaydet** ' e tıklayın.
1. Sol taraftaki menüde **kimlik doğrulaması**' na tıklayın ve *URI 'leri yeniden yönlendir*' in altında "Web" i seçin. İki farklı yeniden yönlendirme URI 'si girmeniz gerekir: bir tane, oturum açma sayfası ve bir grafik sayfası için. Her iki için de aynı ana bilgisayar ve bağlantı noktası numarasını kullanmalı ve ardından oturum açma sayfası için "/msal4jsample/Secure/AAD" ve Kullanıcı bilgileri sayfası için "msal4jsample/Graph/Me" gelmelidir.
 Varsayılan olarak, örnek şunları kullanır:

    - `http://localhost:8080/msal4jsample/secure/aad`.
    - `http://localhost:8080/msal4jsample/graph/me`

    **Gelişmiş ayarlar** bölümünde, **oturum kapatma URL 'sini** `http://localhost:8080/msal4jsample/sign_out`olarak ayarlayın.

     **Kaydet**' e tıklayın.

1. Menüdeki **gizli dizileri &** seçin ve **istemci gizli** dizileri bölümünde **yeni istemci parolası**' na tıklayın:

    - Bir anahtar açıklaması yazın
    - **1 yılda**bir anahtar süresi seçin.
    - **Ekle**' yi seçtiğinizde anahtar değeri görüntülenecektir.
    - Anahtarın değerini daha sonra kopyalayın. Bu anahtar değeri bir daha görüntülenmez veya başka yollarla alınabilir, bu nedenle Azure portal görünür hale geldiğinde onu kaydedin.

# <a name="pythontabpython"></a>[Python](#tab/python)

4. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
   - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `python-webapp`.
   - **Desteklenen hesap türlerini** **Tüm Kurumsal dizin ve kişisel Microsoft hesaplarında (ör. Skype, Xbox, Outlook.com) hesaplar**olarak değiştirin.
   - Yeniden yönlendirme URI 'SI (isteğe bağlı) bölümünde, açılan kutudan **Web** ' i seçin ve aşağıdaki yeniden yönlendirme URI 'lerini girin: `http://localhost:5000/getAToken`.
1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Uygulamaya **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra için kaydedin. Bu proje için Visual Studio yapılandırma dosyasını yapılandırmak için gerekli olacaktır.
1. Uygulamanın genel bakış sayfasından **kimlik doğrulama** bölümünü seçin.
   - **Gelişmiş ayarlar** bölümünde, **oturum kapatma URL 'sini** `http://localhost:5000/logout` olarak ayarlayın

  **Kaydet**’i seçin.
1. Sol taraftaki menüde **sertifikalar & gizlilikler** ' ı seçin ve **istemci** gizli dizileri bölümünde **yeni istemci parolası** ' na tıklayın:

      - Bir anahtar açıklaması yazın
      - **1 yılda**bir anahtar süresi seçin.
      - **Ekle**' ye tıkladığınızda, anahtar değeri görüntülenecektir.
      - Anahtarın değerini kopyalayın. Buna daha sonra ihtiyacınız olacak.
---

## <a name="register-an-app-using-powershell"></a>PowerShell kullanarak bir uygulamayı kaydetme

> [!NOTE]
> Şu anda Azure AD PowerShell yalnızca aşağıdaki desteklenen hesap türlerine sahip uygulamalar oluşturuyor:
>
> - MyOrg (yalnızca bu kuruluş dizinindeki hesaplar)
> - AnyOrg (herhangi bir kuruluş dizinindeki hesaplar).
>
> Kullanıcıları kendi kişisel Microsoft hesaplarıyla (örn. Skype, XBox, Outlook.com) oturum açan bir uygulama oluşturmak istiyorsanız, önce çok kiracılı bir uygulama (desteklenen hesap türleri = hesaplar herhangi bir kuruluş dizininde) oluşturabilir ve sonra değiştirebilirsiniz Azure portal uygulama bildiriminde `signInAudience` özelliği. Bu, ASP.NET Core öğreticinin [1,3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) adımındaki Ayrıntılar açıklanmaktadır (ve herhangi bir dilde web uygulamaları için genelleştirilerek bulunabilir).

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Uygulamanın kod yapılandırması](scenario-web-app-sign-user-app-configuration.md)
