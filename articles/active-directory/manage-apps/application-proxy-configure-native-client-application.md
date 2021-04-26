---
title: Yerel istemci uygulamalarını yayımlama-Azure AD | Microsoft Docs
description: Şirket içi uygulamalarınıza güvenli uzaktan erişim sağlamak amacıyla yerel istemci uygulamalarının Azure AD Uygulama Ara Sunucusu Bağlayıcısı ile iletişim kurmasını sağlar.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e830b52accefa0bcb76cc3434cae2fe94eed97db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258671"
---
# <a name="how-to-enable-native-client-applications-to-interact-with-proxy-applications"></a>Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama

Web uygulamaları yayımlamak için Azure Active Directory (Azure AD) uygulama proxy 'Si kullanabilirsiniz, ancak Microsoft kimlik doğrulama kitaplığı (MSAL) ile yapılandırılmış yerel istemci uygulamalarını yayımlamak için de kullanılabilir. Yerel istemci uygulamaları bir cihaza yüklendiğinden, Web uygulamalarına bir tarayıcıdan erişildiği için Web Apps 'ten farklıdır.

Yerel istemci uygulamalarını desteklemek için, uygulama proxy 'Si, üst bilgide gönderilen Azure AD tarafından verilen belirteçleri kabul eder. Uygulama proxy hizmeti, kullanıcıların kimlik doğrulamasını yapar. Bu çözüm, kimlik doğrulaması için uygulama belirteçlerini kullanmaz.

![Son kullanıcılar, Azure AD ve yayımlanan uygulamalar arasındaki ilişki](./media/application-proxy-configure-native-client-application/richclientflow.png)

Yerel uygulamaları yayımlamak için, kimlik doğrulamasından geçen ve birçok istemci ortamını destekleyen Microsoft kimlik doğrulama Kitaplığı ' nı kullanın. Uygulama proxy 'Si, [oturum açmış bir Kullanıcı senaryosu adına bir Web API 'si çağıran masaüstü uygulamasına](../develop/authentication-flows-app-scenarios.md#desktop-app-that-calls-a-web-api-on-behalf-of-a-signed-in-user) uyar.

Bu makalede, uygulama proxy 'Si ve Azure AD kimlik doğrulama kitaplığı ile yerel bir uygulama yayımlamanın dört adımı anlatılmaktadır.

## <a name="step-1-publish-your-proxy-application"></a>1. Adım: proxy uygulamanızı yayımlama

Proxy uygulamanızı diğer tüm uygulamalar gibi yayımlayın ve uygulamanıza erişmek için kullanıcıları atayın. Daha fazla bilgi için bkz. [uygulama proxy 'si Ile uygulama yayımlama](application-proxy-add-on-premises-application.md).

## <a name="step-2-register-your-native-application"></a>2. Adım: yerel Uygulamanızı kaydetme

Artık uygulamanızı şu şekilde Azure AD 'ye kaydetmeniz gerekir:

1. [Azure Active Directory portalında](https://aad.portal.azure.com/)oturum açın. **Azure Active Directory Yönetim Merkezi** **panosu** görüntülenir.
1. Kenar çubuğunda **Azure Active Directory**' yi seçin. **Azure Active Directory** genel bakış sayfası görüntülenir.
1. Azure AD Genel Bakış kenar çubuğunda **uygulama kayıtları**' yi seçin. Tüm uygulama kayıtlarının listesi görüntülenir.
1. **Yeni kayıt** seçeneğini belirleyin. **Uygulama kaydetme** sayfası görüntülenir.

   ![Azure portal yeni bir uygulama kaydı oluşturun](./media/application-proxy-configure-native-client-application/create.png)

1. **Ad** başlığında, uygulamanız için kullanıcıya dönük bir görünen ad belirtin.
1. **Desteklenen hesap türleri** başlığı altında, aşağıdaki yönergeleri kullanarak bir erişim düzeyi seçin:

   - Yalnızca kuruluşunuz için dahili olan hesapları hedeflemek için **yalnızca bu kuruluş dizininde hesaplar**' ı seçin.
   - Yalnızca iş veya Eğitim müşterilerini hedeflemek için **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
   - En geniş Microsoft kimlikleri kümesini hedeflemek için **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında **ortak istemci ' yi (mobil & Masaüstü)** seçin ve ardından uygulamanızın yeniden yönlendirme URI 'sini yazın `https://login.microsoftonline.com/common/oauth2/nativeclient` .
1. **Microsoft Platformu ilkelerini** seçin ve okuyun ve ardından **Kaydet**' i seçin. Yeni uygulama kaydı için bir genel bakış sayfası oluşturulur ve görüntülenir.

Yeni bir uygulama kaydı oluşturma hakkında daha ayrıntılı bilgi için bkz. [uygulamaları Azure Active Directory tümleştirme](../develop/quickstart-register-app.md).

## <a name="step-3-grant-access-to-your-proxy-application"></a>3. Adım: proxy uygulamanıza erişim Izni verme

Yerel uygulamanızı kaydettirdiğiniz için, bu durumda ara sunucu uygulamasına erişmek üzere dizininizdeki diğer uygulamalara erişim izni verebilirsiniz. Yerel uygulamanın proxy uygulamasına gösterilmesini sağlamak için:

1. Yeni uygulama kaydı sayfasının kenar çubuğunda, **API izinleri**' ni seçin. Yeni uygulama kaydı için **API izinleri** sayfası görüntülenir.
1. **Izin Ekle**' yi seçin. **İstek API 'si izinleri** sayfası görüntülenir.
1. **BIR API seçin** ayarı altında **Kuruluşumun kullandığı API 'ler**' i seçin. Dizininizde API 'Leri kullanıma sunan uygulamaları içeren bir liste görüntülenir.
1. [1. Adım: ara sunucu uygulamanızı yayımlama](#step-1-publish-your-proxy-application)bölümünde yayımladığınız proxy uygulamasını bulmak için arama kutusuna veya kaydırın, sonra da ara sunucu uygulamasını seçin.
1. **Uygulamanızın ne tür izinler gerekiyor?** başlığına izin türünü seçin. Yerel uygulamanızın oturum açmış kullanıcı olarak proxy uygulama API 'sine erişmesi gerekiyorsa, **temsilci izinleri**' ni seçin.
1. **Izinleri Seç** başlığında, istenen izni seçin ve **izin Ekle**' yi seçin. Yerel uygulamanız için **API izinleri** sayfası artık eklediğiniz proxy uygulamasını ve izin API 'sini gösterir.

## <a name="step-4-add-the-microsoft-authentication-library-to-your-code-net-c-sample"></a>4. Adım: Microsoft kimlik doğrulama kitaplığı 'nı kodunuza ekleme (.NET C# örneği)

Microsoft kimlik doğrulama kitaplığı 'nın (MSAL) kimlik doğrulama bağlamındaki yerel uygulama kodunu aşağıdaki metni içerecek şekilde düzenleyin: 

```         
// Acquire Access Token from AAD for Proxy Application
IPublicClientApplication clientApp = PublicClientApplicationBuilder
.Create(<App ID of the Native app>)
.WithDefaultRedirectUri() // will automatically use the default Uri for native app
.WithAuthority("https://login.microsoftonline.com/{<Tenant ID>}")
.Build();

AuthenticationResult authResult = null;
var accounts = await clientApp.GetAccountsAsync();
IAccount account = accounts.FirstOrDefault();

IEnumerable<string> scopes = new string[] {"<Scope>"};

try
 {
    authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
 }
    catch (MsalUiRequiredException ex)
 {
     authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();                
 }

if (authResult != null)
 {
  //Use the Access Token to access the Proxy Application

  HttpClient httpClient = new HttpClient();
  HttpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authResult.AccessToken);
  HttpResponseMessage response = await httpClient.GetAsync("<Proxy App Url>");
 }
```

Örnek kodda gerekli bilgiler, Azure AD portalında şu şekilde bulunabilir:

| Bilgi gerekiyor | Azure AD portalında bulma |
| --- | --- |
| \<Tenant ID> | **Azure Active Directory**  >  **Özellikler**  >  **DIZIN kimliği** |
| \<App ID of the Native app> | **Uygulama kaydı**  >  *Yerel uygulamanız*  >  **Genel bakış**  >  **Uygulama kimliği** |
| \<Scope> | **Uygulama kaydı**  >  *Yerel uygulamanız*  >  **API izinleri** > izin API 'sine (User_impersonation) tıklayarak, sağ tarafta başlık **user_impersonation** olan bir panel >. Kapsam >, düzenleme kutusunda URL 'dir.
| \<Proxy App Url> | Dış URL ve API yolu

MSAL kodunu bu parametrelerle düzenledikten sonra, kullanıcılarınız şirket ağı dışında olsalar bile yerel istemci uygulamalarında kimlik doğrulaması yapabilir.

## <a name="next-steps"></a>Sonraki adımlar

Yerel uygulama akışı hakkında daha fazla bilgi için, [Azure Active Directory Içindeki yerel uygulamalar](../azuread-dev/native-app.md)bölümüne bakın.

[Azure Active Directory uygulamalarda çoklu oturum açmayı](sso-options.md#choosing-a-single-sign-on-method)ayarlama hakkında bilgi edinin.