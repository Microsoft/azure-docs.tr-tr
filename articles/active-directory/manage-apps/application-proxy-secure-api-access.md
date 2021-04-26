---
title: Azure AD Uygulama Ara Sunucusu ile şirket içi API 'Lere erişme
description: Azure Active Directory uygulama proxy 'Si, yerel uygulamaların şirket içinde veya bulut VM 'Lerinde barındırmanıza olanak sağlayan API 'Lere ve iş mantığına güvenli bir şekilde erişmesini sağlar.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/12/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 9341646f32f6a2e05397b072d3f63186964fbd88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258991"
---
# <a name="secure-access-to-on-premises-apis-with-azure-ad-application-proxy"></a>Azure AD Uygulama Ara Sunucusu ile şirket içi API 'lere güvenli erişim

Şirket içinde çalışan veya bulutta sanal makinelerde barındırılan iş mantığı API 'Leri olabilir. Yerel Android, iOS, Mac veya Windows uygulamalarınızın, verileri kullanmak veya Kullanıcı etkileşimi sağlamak için API uç noktalarıyla etkileşimde olması gerekir. Azure AD Uygulama Ara Sunucusu ve [Microsoft kimlik doğrulama kitaplığı (msal)](../azuread-dev/active-directory-authentication-libraries.md) , yerel uygulamalarınızın şirket içi API 'lerinize güvenli bir şekilde erişmesini sağlar. Azure Active Directory Uygulama Ara Sunucusu, güvenlik duvarı bağlantı noktalarını açan ve uygulama katmanında kimlik doğrulama ve yetkilendirmeyi denetleyen daha hızlı ve daha güvenli bir çözümdür.

Bu makalede, yerel uygulamaların erişebileceği bir Web API hizmetini barındırmak için bir Azure AD Uygulama Ara Sunucusu çözümü ayarlama işlemi adım adım açıklanmaktadır.

## <a name="overview"></a>Genel Bakış

Aşağıdaki diyagramda şirket içi API 'Leri yayımlamanın geleneksel bir yolu gösterilmektedir. Bu yaklaşım, 80 ve 443 gelen bağlantı noktalarını açmaya gerek duyar.

![Geleneksel API erişimi](./media/application-proxy-secure-api-access/overview-publish-api-open-ports.png)

Aşağıdaki diyagramda, herhangi bir gelen bağlantı noktasını açmadan API 'Leri güvenli bir şekilde yayımlamak için Azure AD Uygulama Ara Sunucusu nasıl kullanabileceğiniz gösterilmektedir:

![Azure AD Uygulama Ara Sunucusu API erişimi](./media/application-proxy-secure-api-access/overview-publish-api-app-proxy.png)

Azure AD Uygulama Ara Sunucusu, API erişimi için genel bir uç nokta olarak çalışarak ve kimlik doğrulama ve yetkilendirme sağlayarak çözümün omurgasını oluşturur. [Microsoft kimlik doğrulama kitaplığı (msal)](../azuread-dev/active-directory-authentication-libraries.md) kitaplıklarını kullanarak çok sayıda platformda API 'lerinize erişebilirsiniz.

Azure AD Uygulama Ara Sunucusu kimlik doğrulaması ve yetkilendirme, Azure AD 'de oluşturulduğundan, yalnızca güvenilen cihazların uygulama proxy 'Si aracılığıyla yayınlanan API 'Lere erişebildiğinden emin olmak için Azure AD koşullu erişim 'i kullanabilirsiniz. Azure AD JOIN veya masaüstleri için Azure AD hibrit ve cihazlar için Intune ile yönetilen ' i kullanın. Ayrıca Azure AD Multi-Factor Authentication gibi Azure Active Directory Premium özelliklerden ve [Azure kimlik koruması](../identity-protection/overview-identity-protection.md)'nın makine öğrenimi ile desteklenen güvenliğinin avantajlarından yararlanabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu yönergeyi izlemek için şunlar gerekir:

- Uygulama oluşturup kaydedebilirler bir hesapla Azure dizinine yönetici erişimi
- Örnek Web API 'SI ve yerel istemci uygulamaları [https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp](https://github.com/jeevanbisht/API-NativeApp-ADAL-SampleApp)

## <a name="publish-the-api-through-application-proxy"></a>API 'YI uygulama proxy 'Si aracılığıyla yayımlama

Uygulama proxy 'si aracılığıyla intranetinizin dışında bir API yayımlamak için, Web uygulamalarını yayımlama ile aynı kalıbı takip edersiniz. Daha fazla bilgi için bkz. [öğretici: Azure Active Directory Içindeki uygulama proxy 'si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md).

SecretAPI Web API 'sini uygulama proxy 'Si aracılığıyla yayımlamak için:

1. Örnek SecretAPI projesini yerel bilgisayarınızda veya intranette bir ASP.NET Web uygulaması olarak derleyin ve yayımlayın. Web uygulamasına yerel olarak erişebildiğinizden emin olun.

1. [Azure portalda](https://portal.azure.com)**Azure Active Directory**'yi seçin. Ardından **Kurumsal uygulamalar**' ı seçin.

1. **Kurumsal uygulamalar-tüm uygulamalar** sayfasının en üstünde **Yeni uygulama**' yı seçin.

1. **Uygulama Ekle** sayfasında şirket **içi uygulamalar**' ı seçin. **Kendi şirket içi uygulamanızı ekleyin** sayfası görüntülenir.

1. Yüklü bir uygulama proxy Bağlayıcısı yoksa, yüklemek isteyip istemediğiniz sorulur. Bağlayıcıyı indirmek ve yüklemek için **uygulama proxy bağlayıcısını indir** ' i seçin.

1. Uygulama proxy bağlayıcısını yükledikten sonra, **kendi şirket içi uygulamanızı ekleyin** sayfasında:

   1. **Ad**' ın yanındaki *secretapi* yazın.

   1. **Iç URL**'nin yanına, INTRANETINIZDEKI API 'ye erişmek için kullandığınız URL 'yi girin.

   1. **Ön kimlik doğrulamanın** **Azure Active Directory** olarak ayarlandığından emin olun.

   1. Sayfanın üst kısmında **Ekle** ' yi seçin ve uygulamanın oluşturulmasını bekleyin.

   ![API uygulaması ekleme](./media/application-proxy-secure-api-access/3-add-api-app.png)

1. **Kurumsal uygulamalar-tüm uygulamalar** sayfasında, **secretapi** uygulamasını seçin.

1. **Secretapi-genel bakış** sayfasında sol gezinmede **Özellikler** ' i seçin.

1. API 'Lerin **Uygps** panelindeki son kullanıcılar için kullanılabilir olmasını istemezsiniz, bu nedenle **kullanıcılar için** **Özellikler** sayfasının alt kısmında **Hayır** olarak görünür yapın ve ardından **Kaydet**' i seçin.

   ![Kullanıcılara görünür değil](./media/application-proxy-secure-api-access/5-not-visible-to-users.png)

Web API 'nizi Azure AD Uygulama Ara Sunucusu ile yayımladınız. Şimdi, uygulamaya erişebilen kullanıcıları ekleyin.

1. **Secretapi-genel bakış** sayfasında, sol gezinti bölmesinde **Kullanıcılar ve gruplar** ' ı seçin.

1. **Kullanıcılar ve gruplar** sayfasında **Kullanıcı Ekle**' yi seçin.

1. **Atama Ekle** sayfasında, **Kullanıcılar ve gruplar**' ı seçin.

1. **Kullanıcılar ve gruplar** sayfasında, uygulamaya erişebilen kullanıcıları arayın ve en azından kendinize dahil edebilirsiniz. Tüm kullanıcılar ' ı seçtikten sonra **Seç**' i seçin.

   ![Kullanıcı Seç ve ata](./media/application-proxy-secure-api-access/7-select-admin-user.png)

1. **Atama Ekle** sayfasında, **ata**' yı seçin.

> [!NOTE]
> Tümleşik Windows kimlik doğrulaması kullanan API 'Ler için [ek adımlar](./application-proxy-configure-single-sign-on-with-kcd.md)gerekebilir.

## <a name="register-the-native-app-and-grant-access-to-the-api"></a>Yerel uygulamayı kaydetme ve API 'ye erişim izni verme

Yerel uygulamalar, belirli bir platformda veya cihazda kullanılmak üzere geliştirilen programlardır. Yerel uygulamanızın bir API 'ye bağlanıp bir API 'ye erişebilmesi için Azure AD 'ye kaydetmeniz gerekir. Aşağıdaki adımlarda, yerel bir uygulamanın nasıl kaydedileceği ve uygulama proxy 'Si aracılığıyla yayımladığınız Web API 'sine nasıl erişim verilecek gösterilmektedir.

AppProxyNativeAppSample yerel uygulamasını kaydetmek için:

1. Azure Active Directory **genel bakış** sayfasında **uygulama kayıtları**' i seçin ve **uygulama kayıtları** bölmesinin üst kısmında **Yeni kayıt**' yi seçin.

1. **Uygulama kaydetme** sayfasında:

   1. **Ad** alanına *Appproxynativeappsample* yazın.

   1. **Desteklenen hesap türleri** altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında hesaplar**' ı seçin.

   1. **Yeniden yönlendirme URL 'si** altında, açılır istemci ' ı **(mobil & Masaüstü)** seçin ve ardından girin *https://login.microsoftonline.com/common/oauth2/nativeclient* .

   1. **Kaydet**' i seçin ve uygulamanın başarıyla kaydedilmesini bekleyin.

      ![Yeni uygulama kaydı](./media/application-proxy-secure-api-access/8-create-reg-ga.png)

AppProxyNativeAppSample uygulamasını şimdi Azure Active Directory kaydettiniz. Yerel uygulamanıza SecretAPI Web API 'sine erişim sağlamak için:

1. Azure Active Directory **genel bakış**  >  **uygulama kayıtları** sayfasında **appproxynativeappsample** uygulamasını seçin.

1. **Appproxynativeappsample** sayfasında, sol gezinti bölmesinde **API izinleri** ' ni seçin.

1. **API izinleri** sayfasında **izin Ekle**' yi seçin.

1. İlk **istek API 'si izinleri** sayfasında, **Kuruluşumun kullandığı API 'leri** seçin ve ardından **secretapi**' ı arayıp seçin.

1. Sonraki **istek API 'si izinleri** sayfasında, **user_impersonation**' nin yanındaki onay kutusunu Işaretleyin ve ardından **izin Ekle**' yi seçin.

    ![Bir API seçin](./media/application-proxy-secure-api-access/10-secretapi-added.png)

1. **API izinleri** sayfasına geri döndüğünüzde, diğer kullanıcıların uygulamaya tek tek izin vermesini engellemek için **contoso Için yönetici izni ver** ' i seçebilirsiniz.

## <a name="configure-the-native-app-code"></a>Yerel uygulama kodunu yapılandırma

Son adım, yerel uygulamayı yapılandırmaktır. NativeClient örnek uygulamasındaki *Form1. cs* dosyasındaki aşağıdaki kod parçacığı, msal kitaplığının API çağrısını istemek için belirteci almasına ve bunu uygulama üstbilgisine taşıyıcı olarak iliştirmesine neden olur.

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

Yerel uygulamayı Azure Active Directory bağlanacak ve API uygulama ara sunucusunu çağıracak şekilde yapılandırmak için, NativeClient Sample uygulamasının *App.config* dosyasındaki yer tutucu DEğERLERINI Azure AD ile güncelleştirin:

- Alan içindeki **Dizin (kiracı) kimliğini** yapıştırın `<add key="ida:Tenant" value="" />` . Bu değeri (bir GUID), uygulamalarınızdan birinin **genel bakış** sayfasından bulabilir ve kopyalayabilirsiniz.

- AppProxyNativeAppSample **uygulaması (istemci) kimliğini** `<add key="ida:ClientId" value="" />` alana yapıştırın. Bu değeri (bir GUID) AppProxyNativeAppSample **genel bakış** sayfasından bulup kopyalayabilirsiniz.

- AppProxyNativeAppSample **yeniden YÖNLENDIRME URI** 'sini alana yapıştırın `<add key="ida:RedirectUri" value="" />` . Bu değeri (bir URI) AppProxyNativeAppSample **kimlik doğrulama** sayfasından bulup kopyalayabilirsiniz.

- Alana Secretapı **uygulama KIMLIĞI URI** 'sini yapıştırın `<add key="todo:TodoListResourceId" value="" />` . Bu değeri (bir URI), SecretAPI **BIR API** sayfasından bulup kopyalayabilirsiniz.

- Alana SecretAPI **giriş sayfası URL 'sini** yapıştırın `<add key="todo:TodoListBaseAddress" value="" />` . Bu değeri (bir URL), SecretAPI **marka** sayfasından bulup kopyalayabilirsiniz.

Parametreleri yapılandırdıktan sonra yerel uygulamayı derleyin ve çalıştırın. **Oturum aç** düğmesini seçtiğinizde, uygulama oturum açmanıza olanak tanır ve sonra, secretapi 'e başarıyla bağlandığını doğrulamak için bir başarı ekranı görüntüler.

![Ekran görüntüsünde, başarılı bir BH ve Tamam düğmesi olan bir ileti gizli anahtarı gösterilmektedir.](./media/application-proxy-secure-api-access/success.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure Active Directory içindeki uygulama proxy 'Si aracılığıyla uzaktan erişim için şirket içi uygulama ekleme](application-proxy-add-on-premises-application.md)
- [Hızlı başlangıç: Web API 'Lerine erişmek için bir istemci uygulaması yapılandırma](../develop/quickstart-configure-app-access-web-apis.md)
- [Yerel istemci uygulamalarının proxy uygulamalarıyla etkileşime geçmesini sağlama](application-proxy-configure-native-client-application.md)