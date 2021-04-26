---
title: 'Öğretici: Microsoft Graph iş verilerine erişen çok kiracılı bir Daemon oluşturun | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, bir Windows Masaüstü (WPF) uygulamasından Azure Active Directory tarafından korunan bir ASP.NET Web API 'sinin nasıl çağrılacağını öğrenin. WPF istemcisi bir kullanıcının kimliğini doğrular, bir erişim belirteci ister ve Web API 'sini çağırır.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 5816bc542ff550accedd33ba9a7fd9d98583b3d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653771"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Öğretici: Microsoft Identity platformunu kullanan çok kiracılı bir Daemon oluşturma

Bu öğreticide, Microsoft Graph API 'sini çağırmak için bir erişim belirteci almak üzere OAuth 2,0 istemci kimlik bilgilerini kullanmayı gösteren bir ASP.NET Daemon Web uygulaması indirip çalıştırırsınız.

Bu öğreticide:

> [!div class="checklist"]
> * Bir Daemon uygulamasını Microsoft Identity platformu ile tümleştirme
> * Uygulamaya yönetici tarafından doğrudan uygulama izinleri verme
> * Microsoft Graph API 'sini çağırmak için bir erişim belirteci alın
> * Microsoft Graph API 'sini çağırın.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

- [Visual Studio 2017 veya 2019](https://visualstudio.microsoft.com/downloads/).
- Azure AD kiracısı. Daha fazla bilgi için bkz. [Azure AD kiracısı alma](quickstart-create-new-tenant.md).
- Azure AD kiracınızdaki bir veya daha fazla kullanıcı hesabı. Bu örnek Microsoft hesabı çalışmaz. [Azure Portal](https://portal.azure.com) bir Microsoft hesabı ile oturum açtıysanız ve dizininizde hiç bir kullanıcı hesabı oluşturmadıysanız, şimdi bunu yapın.

## <a name="scenario"></a>Senaryo

Uygulama, bir ASP.NET MVC uygulaması olarak oluşturulmuştur. Kullanıcıların oturum açması için OWIN OpenID Connect ara yazılımını kullanır.

Bu örnekteki "Daemon" bileşeni bir API denetleyicisidir `SyncController.cs` . Denetleyici çağrıldığında, Microsoft Graph tarafından müşterinin Azure Active Directory (Azure AD) kiracısındaki kullanıcıların listesini alır. `SyncController.cs` Web uygulamasındaki bir AJAX çağrısıyla tetiklenir. Microsoft Graph için bir erişim belirteci almak üzere [.net Için Microsoft kimlik doğrulama kitaplığı 'nı (msal)](msal-overview.md) kullanır.

Uygulama, Microsoft iş müşterilerine yönelik çok kiracılı bir uygulama olduğundan, müşterilerin uygulamayı şirket verilerine "kaydolmalarına" veya "bağlanmasına" olanak sağlaması gerekir. Bağlantı akışı sırasında, genel yönetici önce uygulamaya doğrudan uygulama *izinleri* verir, böylece oturum açmış bir Kullanıcı mevcut olmadan şirket verilerine etkileşimli olmayan bir biçimde erişebilir. Bu örnekteki mantığın çoğunluğunda, kimlik platformunun [Yönetici onay](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) uç noktası kullanılarak bu bağlantı akışının nasıl elde edilebileceği gösterilmektedir.

![Diyagram, başlangıç noktası kimlik doğrulaması ile Azure 'a bağlanmak için bir belirteç almak üzere Azure A, AccountController 'ın Azure a 'ya bağlanmasına yönelik yönetici onayı alma ve Microsoft Graph bağlanmak üzere kullanıcıyı okuma ile eşitleme için bir belirteç alma ile UserSync uygulamasını gösterir.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Bu örnekte kullanılan kavramlar hakkında daha fazla bilgi için [kimlik platformunun istemci kimlik bilgileri Protokolü belgelerini](v2-oauth2-client-creds-grant-flow.md)okuyun.

## <a name="clone-or-download-this-repository"></a>Bu depoyu Kopyala veya indir

Kabuk veya komut satırınızdan şu komutu girin:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Ya da [örneği bir zip dosyasına indirin](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Uygulamanızı kaydetme

Bu örnekte bir proje vardır. Uygulamayı Azure AD kiracınızla kaydetmek için şunlardan birini yapabilirsiniz:

- [Örneği Azure Active Directory kiracınızla kaydetme](#register-the-client-app-dotnet-web-daemon-v2) bölümündeki adımları izleyin ve [örneği Azure AD kiracınızı kullanacak şekilde yapılandırın](#choose-the-azure-ad-tenant).
- PowerShell betiklerini şu şekilde kullanın:
  - Azure AD uygulamalarını ve ilgili nesneleri (parolalar, izinler, bağımlılıklar) sizin için *otomatik olarak* oluşturun.
  - Visual Studio projelerinin yapılandırma dosyalarını değiştirin.

Otomasyonu kullanmak istiyorsanız:

1. Windows üzerinde PowerShell 'i çalıştırın ve kopyalanan dizinin köküne gidin.
1. Şu komutu çalıştırın:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Azure AD uygulamanızı oluşturmak ve örnek uygulamanın kodunu uygun şekilde yapılandırmak için betiği çalıştırın:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Komut dosyalarını çalıştırmanın diğer yolları [uygulama oluşturma betiklerine](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)göre açıklanmıştır.

1. Visual Studio çözümünü açın ve kodu çalıştırmak için **Başlat** ' ı seçin.

Otomasyonu kullanmak istemiyorsanız, aşağıdaki bölümlerde bulunan adımları kullanın.

### <a name="choose-the-azure-ad-tenant"></a>Azure AD kiracısını seçme

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>İstemci uygulamasını kaydetme (DotNet-Web-Daemon-v2)

1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin (örneğin,) `dotnet-web-daemon-v2` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** bölümünde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si (isteğe bağlı)** bölümünde, açılan kutuda **Web** ' i seçin ve `https://localhost:44316/` `https://localhost:44316/Account/GrantPermissions` yeniden yönlendirme URI 'leri girin.

    İkiden fazla yeniden yönlendirme URI 'si varsa, uygulama başarıyla oluşturulduktan sonra bunları daha sonra **kimlik doğrulama** sekmesinden eklemeniz gerekir.
1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Uygulamanın **genel bakış** sayfasında, **uygulama (istemci) kimlik** değerini bulun ve daha sonra kullanmak üzere kaydedin. Bu proje için Visual Studio yapılandırma dosyasını yapılandırmak için gerekli olacaktır.
1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Ön kanal oturum kapatma URL 'sini** olarak ayarlayın `https://localhost:44316/Account/EndSession` .
1. **Örtük verme ve karma akışlar** bölümünde, **erişim belirteçleri** ve **Kimlik belirteçleri**' ni seçin. Bu örnek, kullanıcının oturum açması ve bir API çağırması için [örtük verme akışının](v2-oauth2-implicit-grant-flow.md) etkinleştirilmesini gerektirir.
1. **Kaydet**’i seçin.
1. **Yönet**’in altında **Sertifikalar ve gizli diziler**’i seçin.
1. **İstemci gizli** dizileri bölümünde **yeni istemci parolası**' nı seçin. 
1. Bir anahtar açıklaması girin (örneğin, **uygulama gizli** dizisi).
1. **1 yılda**, **2 yıl içinde** bir anahtar süresi seçin veya **hiçbir zaman sona ermez**.
1. **Add (Ekle)** seçeneğini belirleyin. Anahtar değerini güvenli bir konuma kaydedin. Bu anahtar daha sonra Visual Studio 'da projeyi yapılandırmak için gereklidir.
1. **Yönet** altında **API izinleri**  >  **bir izin Ekle**' yi seçin.
1. **Yaygın olarak kullanılan Microsoft API 'leri** bölümünde **Microsoft Graph**' yi seçin.
1. **Uygulama izinleri** bölümünde, doğru izinlerin seçildiğinden emin olun: **User. Read. All**.
1. **Izin Ekle**' yi seçin.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Örneği Azure AD kiracınızı kullanacak şekilde yapılandırın

Aşağıdaki adımlarda, **ClientID** "uygulama kimliği" veya **AppID** ile aynıdır.

Projeleri yapılandırmak için Visual Studio 'da çözümü açın.

### <a name="configure-the-client-project"></a>İstemci projesini yapılandırma

Kurulum betiklerini kullandıysanız, sizin için aşağıdaki değişiklikler uygulanmış olur.

1. **UserSync\Web.Config** dosyasını açın.
1. Uygulama anahtarı 'nı bulun **: ClientID**. Mevcut değeri, Azure portal kopyalanmış olan **DotNet-Web-Daemon-v2** UYGULAMASıNıN uygulama kimliğiyle değiştirin.
1. Uygulama anahtarı 'nı bulun **: ClientSecret**. Mevcut değeri, Azure portal **DotNet-Web-Daemon-v2** uygulamasının oluşturulması sırasında kaydettiğiniz anahtarla değiştirin.

## <a name="run-the-sample"></a>Örneği çalıştırma

Çözümü temizleyin, çözümü yeniden derleyin, UserSync uygulamasını çalıştırın ve ardından Azure AD kiracınızda yönetici olarak oturum açın. Test için bir Azure AD kiracınız yoksa, bir tane almak için [Bu yönergeleri izleyebilirsiniz](quickstart-create-new-tenant.md) .

Oturum açtığınızda, uygulama öncelikle oturumunuzu açmak ve Kullanıcı profilinizi okumak için sizden izin ister. Bu izin, uygulamanın bir iş kullanıcısı olduğunuzdan emin olmanızı sağlar.

![Kullanıcı onayı](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Uygulama daha sonra Microsoft Graph aracılığıyla Azure AD kiracınızdan bir kullanıcı listesini eşitlemeye çalışır. Bu, kiracınızı uygulamaya bağlamak için sizi (kiracı yöneticisi) ister.

Daha sonra uygulama, kiracınızdaki kullanıcıların listesini okumak için izin ister.

![Yönetici onayı](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

İzin verdikten sonra uygulamadan oturumunuz açıldı. Bu oturum açma, Microsoft Graph için mevcut erişim belirteçlerinin belirteç önbelleğinden kaldırılmasını sağlar. Yeniden oturum açtığınızda, elde edilen yeni belirteç Microsoft Graph çağrısı yapmak için gerekli izinlere sahip olur.


İzin verdiğinizde, uygulama herhangi bir noktada kullanıcıları sorgulayabilir. **Kullanıcıları Eşitle** düğmesini seçerek ve Kullanıcı listesini yenileyerek bunu doğrulayabilirsiniz. Bir Kullanıcı eklemeyi veya kaldırmayı deneyin ve listeyi yeniden eşitleme. (Ancak uygulamanın yalnızca ilk Kullanıcı sayfasını eşitlediği unutulmamalıdır.)

## <a name="about-the-code"></a>Kod hakkında

Bu örnek için ilgili kod aşağıdaki dosyalardır:

- **App_Start \startup.auth.cs**, **Controllers\accountcontroller.cs**: ilk oturum açma. Özellikle, denetleyicideki eylemlerin bir **Yetkilendir** özniteliği vardır ve bu, kullanıcıyı oturum açmaya zorlar. Uygulama, kullanıcının oturum açması için [yetkilendirme kodu akışını](v2-oauth2-auth-code-flow.md) kullanır.
- **Controllers\synccontroller.cs**: Kullanıcı listesini yerel bellek içi depoya eşitleniyor.
- **Controllers\usercontroller.cs**: yerel bellek içi depodan Kullanıcı listesi görüntülüyor.
- **Controllers\accountcontroller.cs**: yönetici onay uç noktasını kullanarak kiracı yöneticisinden izinler alınıyor.

## <a name="re-create-the-sample-app"></a>Örnek uygulamayı yeniden oluşturma

1. Visual Studio 'da yeni bir **visual C#** **ASP.NET Web uygulaması (.NET Framework)** projesi oluşturun.
1. Sonraki ekranda, **MVC** proje şablonunu seçin. Daha sonra bir Web API denetleyicisi ekleyeceğiniz için **Web API 'si** için klasör ve çekirdek başvurular da ekleyin. Projenin seçili kimlik doğrulama modunu varsayılan: **kimlik doğrulaması yok** olarak bırakın.
1. **Çözüm Gezgini** penceresinde projeyi seçin ve **F4** tuşunu seçin.
1. Proje Özellikleri ' nde **SSL etkin** ' i **true** olarak ayarlayın. **SSL URL 'sindeki** bilgileri aklınızda edin. Azure portal bu uygulamanın kaydını yapılandırırken ihtiyacınız olacak.
1. Aşağıdaki ASP.NET OWIN ara yazılım NuGet paketlerini ekleyin:
   - Microsoft. Owin. Security. ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft. IdentityModel. Protocol. Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft. Identity. Client
1. **App_Start** klasöründe:
   1. **Startup. auth. cs** adlı bir sınıf oluşturun.
   1. Kaldırın **.** Ad alanı adından App_Start.
   1. **Başlangıç** sınıfının kodunu, örnek uygulamanın aynı dosyasındaki kodla değiştirin.
   Tüm sınıf tanımını aldığınızdan emin olun. Tanım, **ortak sınıf başlatmasının** **genel kısmi sınıf başlatması** olarak değişir.
1. **Startup. auth. cs** dosyasında, Visual Studio IntelliSense tarafından önerilen **using** deyimlerini ekleyerek eksik başvuruları çözün.
1. Projeye sağ tıklayın, **Ekle**' yi ve ardından **sınıf**' ı seçin.
1. Arama kutusuna **Owın** girin. **Owın başlangıç sınıfı** seçim olarak görünür. Bunu seçin ve sınıfı **Startup. cs** olarak adlandırın.
1. **Startup. cs** dosyasında, **Başlangıç** sınıfının kodunu örnek uygulamanın aynı dosyasındaki kodla değiştirin. Yine, tanımın **ortak sınıf başlatmasından** **genel kısmi sınıf başlatmaya** değiştiği unutulmamalıdır.
1. **Modeller** klasöründe **msgraphuser. cs** adlı yeni bir sınıf ekleyin. Uygulamayı örnekteki aynı adlı dosyanın içeriğiyle değiştirin.
1. **Accountcontroller** adlı yeni bir **MVC 5 denetleyici-boş** örnek ekleyin. Uygulamayı örnekteki aynı adlı dosyanın içeriğiyle değiştirin.
1. **Usercontroller** adlı yeni bir **MVC 5 denetleyici-boş** örnek ekleyin. Uygulamayı örnekteki aynı adlı dosyanın içeriğiyle değiştirin.
1. **Synccontroller** adlı yeni BIR **Web API 2 denetleyicisi (boş** örnek) ekleyin. Uygulamayı örnekteki aynı adlı dosyanın içeriğiyle değiştirin.
1. Kullanıcı arabirimi için **Views\account** klasöründe, **grantpermissions**, **Index** ve **useruyuşmazlığını** adlı üç **boş (model olmadan) görüntüleme** örnekleri ekleyin. **Views\user** klasörüne bir adlandırılmış **Dizin** ekleyin. Uygulamayı örnekteki aynı adlı dosyanın içeriğiyle değiştirin.
1. **Paylaşılan \_ Layout. cshtml** ve **Home\ındex.cshtml** güncellerini, çeşitli görünümleri birbirine doğru şekilde bağlamak için güncelleştirin.

## <a name="deploy-the-sample-to-azure"></a>Örneği Azure 'a dağıtma

Bu projede Web uygulaması ve Web API projeleri vardır. Azure Web siteleri 'ne dağıtmak için, her biri için aşağıdaki adımları uygulayın:

1. Azure Web sitesi oluşturun.
1. Web uygulamasını ve Web API 'Lerini Web sitesinde yayımlayın.
1. İstemcileri IIS Express yerine Web sitesini çağırmak için güncelleştirin.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Bir Azure Web sitesinde DotNet-Web-Daemon-v2 oluşturma ve yayımlama

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Sol üst köşeden **Kaynak oluştur**'u seçin.
1. **Web**  >  **Web uygulaması**' nı seçin ve ardından Web sitenize bir ad verin. Örneğin, **DotNet-Web-Daemon-v2-contoso.azurewebsites.net** olarak adlandırın.
1. **Abonelik**, **kaynak grubu** ve **App Service planı ve konum** bilgilerini seçin. **Işletim sistemi** **Windows** ve **Yayımlama** **kodudur**.
1. **Oluştur** ' u seçin ve App Service 'in oluşturulmasını bekleyin.
1. **Dağıtım başarılı** bildirimini aldığınızda, yeni oluşturulan App Service 'e gitmek Için **Kaynağa Git** ' i seçin.
1. Web sitesi oluşturulduktan sonra **panoyu panoda** bulun ve App Service 'In **genel bakış** ekranını açmak için seçin.
1. App Service 'in **genel bakış** sekmesinde yayımlama **profilini al** bağlantısını seçerek yayımlama profilini indirin ve kaydedin. Kaynak denetiminden dağıtma gibi diğer dağıtım mekanizmalarını de kullanabilirsiniz.
1. Visual Studio 'ya geçin ve ardından:
   1. **DotNet-Web-Daemon-v2** projesine gidin.
   1. Çözüm Gezgini projeye sağ tıklayın ve ardından **Yayımla**' yı seçin.
   1. Alt çubukta **profili Içeri aktar** ' ı seçin ve daha önce indirdiğiniz yayımlama profilini içeri aktarın.
1. **Yapılandır**'ı seçin.
1. **Bağlantı** sekmesinde, hedef URL 'yi "https" kullanacak şekilde güncelleştirin. Örneğin, kullanın `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` . **İleri**’yi seçin.
1. **Ayarlar** sekmesinde, **Kurumsal kimlik doğrulamasını etkinleştir** ' in temizlenmiş olduğundan emin olun.
1. **Kaydet**’i seçin. Ana ekranda **Yayımla** ' yı seçin.

Visual Studio projeyi yayımlayacak ve projenin URL 'sine otomatik olarak bir tarayıcı açacak. Projenin varsayılan Web sayfasını görürseniz, yayın başarılı olmuştur.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>DotNet-Web-Daemon-v2 için Azure AD kiracı uygulaması kaydını güncelleştirme

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>geri dönün.
1. Sol bölmede **Azure Active Directory** hizmetini seçin ve **uygulama kayıtları**' ı seçin.
1. **DotNet-Web-Daemon-v2** uygulamasını seçin.
1. Uygulamanızın **kimlik doğrulama** sayfasında, **ön kanal oturum kapatma URL 'si** alanlarını hizmetinizin adresiyle güncelleştirin. Örneğin, kullanın `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession` .
1. **Marka** menüsünde, **giriş sayfası URL 'sini** hizmetinizin adresi olarak güncelleştirin. Örneğin, kullanın `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` .
1. Yapılandırmayı kaydedin.
1. **Kimlik doğrulaması**  >  **yeniden yönlendirme URI** 'lerinin değerleri listesinde aynı URL 'yi ekleyin. Birden çok yeniden yönlendirme URL 'SI varsa, her yönlendirme URL 'si için App Service 'in URI 'sini kullanan yeni bir giriş olduğundan emin olun.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Artık gerekli değilse, [Uygulamanızı kaydetme](#register-your-application) adımındaki oluşturduğunuz uygulama nesnesini silin.  Uygulamayı kaldırmak için [siz veya kuruluşunuz tarafından yazılmış bir uygulamayı kaldırma](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)bölümündeki yönergeleri izleyin.

## <a name="get-help"></a>Yardım alın

Topluluktan destek almak için [Microsoft Q&A](/answers/products/) kullanın.
İlk olarak [Microsoft Q&](/answers/products/) sorularınızı sorun ve bir kişinin sorunuzu isteyip istemediğini öğrenmek için mevcut sorunları inceleyin.
Sorularınıza veya yorumlarınızın "Azure-AD-adal-kullanımdan kaldırma", "Azure-AD-msal" ve "DotNet-Standard" ile etiketlendiğinden emin olun.

Örnekte bir hata bulursanız, lütfen [GitHub sorunlarında](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues)sorunu yükseltin.

MSAL.NET içinde bir hata bulursanız, lütfen sorunu [msal.net GitHub sorunları](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)üzerinde yükseltin.

Öneri sağlamak için [Kullanıcı sesi sayfasına](https://feedback.azure.com/forums/169401-azure-active-directory)gidin.

## <a name="next-steps"></a>Sonraki adımlar

Korumalı Web API 'Lerine erişmek için Microsoft Identity platformunu kullanan Daemon uygulamaları oluşturma hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Senaryo: Web API 'Lerini çağıran Daemon uygulaması](scenario-daemon-overview.md)