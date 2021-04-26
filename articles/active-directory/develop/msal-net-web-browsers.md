---
title: Web tarayıcıları kullanma (MSAL.NET) | Mavisi
titleSuffix: Microsoft identity platform
description: .NET için Microsoft kimlik doğrulama kitaplığı (MSAL.NET) ile Xamarin Android kullanırken belirli hususlar hakkında bilgi edinin.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4121d4b9ac73ed18da7dce0e397fe919589ac6f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478769"
---
# <a name="using-web-browsers-msalnet"></a>Web tarayıcıları kullanma (MSAL.NET)

Etkileşimli kimlik doğrulaması için Web tarayıcıları gereklidir. Varsayılan olarak, MSAL.NET, Xamarin. iOS ve Xamarin. Android üzerindeki [sistem Web tarayıcısını](#system-web-browser-on-xamarinios-xamarinandroid) destekler. Ancak, [Xamarin. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) ve [Xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) uygulamalarında gereksinimlerinize (UX, çoklu oturum açma (SSO) ve güvenlik için ihtiyacı vardır) bağlı olarak [Katıştırılmış Web tarayıcısını de etkinleştirebilirsiniz](#enable-embedded-webviews-on-ios-and-android) . Aynı şekilde, grafik varlığına veya Android 'de Chrome özel sekmelerini destekleyen bir tarayıcıya göre [dinamik olarak](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) hangi Web tarayıcısını kullanacağınızı seçebilirsiniz. MSAL.NET yalnızca .NET Core masaüstü uygulamalarında sistem tarayıcısını destekler.

## <a name="web-browsers-in-msalnet"></a>MSAL.NET 'de Web tarayıcıları

### <a name="interaction-happens-in-a-web-browser"></a>Bir Web tarayıcısında etkileşim gerçekleşir

Bir belirteci etkileşimli olarak alırken, iletişim kutusunun içeriği kitaplık tarafından, ancak STS (güvenlik belirteci hizmeti) tarafından sağlanmadığında anlaşılması önemlidir. Kimlik doğrulama uç noktası, bir Web tarayıcısında veya Web denetiminde işlenen etkileşimi denetleyen bazı HTML ve JavaScript 'ı geri gönderir. STS 'nin HTML etkileşimini işlemesine izin vermek birçok avantaj sunar:

- Parola (yazılmışsa), uygulama veya kimlik doğrulama kitaplığı tarafından hiçbir şekilde depolanmaz.
- Diğer kimlik sağlayıcılarına yeniden yönlendirmeler sağlar (örneğin, bir iş okul hesabı veya MSAL ile bir kişisel hesap veya Azure AD B2C olan bir sosyal hesap ile oturum açma için).
- STS 'nin koşullu erişimi denetlemesine olanak tanır. Örneğin, kullanıcının kimlik doğrulama aşamasında [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) (bir Windows Hello PIN 'i girme veya telefonlarındaki bir kimlik doğrulama uygulaması gibi). Gerekli Multi-Factor Authentication 'ın henüz ayarlanamadığı durumlarda Kullanıcı aynı iletişim kutusunda tam zamanında ayarlayabilir.  Kullanıcı cep telefonu numarasını girer ve bir kimlik doğrulama uygulaması yüklemek ve hesaplarını eklemek için bir QR etiketi taramak için tasarlanmıştır. Bu sunucu odaklı etkileşim harika bir deneyimdir!
- Parolanın süresi dolduğunda kullanıcının bu iletişim kutusunda parolasını değiştirmesine izin verir (eski parola ve yeni parola için ek alanlar sağlar).
- Kiracının markalanmasını veya Azure AD Kiracı Yöneticisi/uygulama sahibi tarafından denetlenen uygulamayı (görüntüleri) mümkün.
- Kullanıcıların, kimlik doğrulamasından sonra yalnızca kendi adında kaynakları/kapsamları erişimine izin vermesini sağlar.

### <a name="embedded-vs-system-web-ui"></a>Katıştırılmış vs sistemi Web Kullanıcı arabirimi

MSAL.NET, çok katmanlı bir kitaplıktır ve bir kullanıcı arabirimi denetiminde bir tarayıcıyı barındırmak için çerçeveye özgü koda sahiptir (örneğin, .NET klasik BT 'de WinForms kullanır, Xamarin 'te yerel mobil denetimleri kullanır vb.). Bu denetim `embedded` Web Kullanıcı arabirimi olarak adlandırılır. Alternatif olarak, MSAL.NET, sistem IŞLETIM sistemi tarayıcısını de başlatabilir.

Genellikle, platform varsayılanını kullanmanız önerilir ve bu genellikle sistem tarayıcısıdır. Sistem tarayıcısı, daha önce oturum açmış olan kullanıcıları hatırlama konusunda daha iyidir. Bu davranışı değiştirmek için, şunu kullanın `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Bir bakışta

| Framework        | Ekleme | Sistem | Varsayılan |
| ------------- |-------------| -----| ----- |
| .NET klasik     | Yes | Evet ^ | Ekleme |
| .NET Core     | Hayır | Evet ^ | Sistem |
| .NET Standard | Hayır | Evet ^ | Sistem |
| UWP | Yes | Hayır | Ekleme |
| Xamarin.Android | Yes | Yes  | Sistem |
| Xamarin.iOS | Yes | Yes  | Sistem |
| Xamarin.Mac| Yes | Hayır | Ekleme |

^ " http://localhost " Yeniden yönlendirme URI 'si gerektirir

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Xamarin. iOS, Xamarin. Android üzerinde sistem Web tarayıcısı

Varsayılan olarak, MSAL.NET, Xamarin. iOS, Xamarin. Android ve .NET Core üzerinde sistem Web tarayıcısını destekler. Kullanıcı arabirimi sağlayan tüm platformlar (yani .NET Core) için, Web tarayıcısı denetimi ekleme kitaplığı tarafından bir iletişim kutusu sağlanır. MSAL.NET, UWP platformu için .NET masaüstü ve WAB için gömülü web görünümünü de kullanır. Bununla birlikte, varsayılan olarak Xamarin iOS ve Xamarin Android uygulamaları için **sistem Web tarayıcısını** kullanır. İOS 'ta, Işletim sisteminin (iOS12, iOS11 ve önceki sürümler) sürümüne bağlı olarak kullanılacak web görünümünü de seçer.

Sistem tarayıcısının kullanılması, bir aracı (Şirket portalı/Authenticator) gerekmeden, SSO durumunu diğer uygulamalarla ve Web uygulamalarıyla paylaşmanın önemli avantajlarından yararlanır. Varsayılan olarak, Xamarin iOS ve Xamarin Android platformları için MSAL.NET içinde Sistem tarayıcısı kullanılmıştır çünkü bu platformlarda, sistem Web tarayıcısı ekranın tamamını kaplar ve Kullanıcı deneyimi daha iyidir. Sistem Web görünümü bir iletişim kutusundan ayırt edilemez. Ancak iOS 'ta, kullanıcının uygulamayı geri çağırması için izin vermesi gerekebilir, bu durum sinir bozucu olabilir.

## <a name="system-browser-experience-on-net"></a>.NET üzerinde sistem tarayıcı deneyimi 

.NET Core 'da MSAL.NET, sistem tarayıcısını ayrı bir işlem olarak başlatır. MSAL.NET bu tarayıcı üzerinde denetime sahip değildir ancak kullanıcı kimlik doğrulamasını tamamladıktan sonra, Web sayfası, MSAL.NET 'in URI 'yi ele geçirebilir şekilde yeniden yönlendirilir.

Ayrıca, bu tarayıcıyı kullanarak .NET klasik veya .NET 5 için yazılmış uygulamaları da yapılandırabilirsiniz:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET, kullanıcının uzaklaşacağını algılayamaz veya yalnızca tarayıcıyı kapatır. Bu tekniği kullanan uygulamalar bir zaman aşımı (aracılığıyla) tanımlamak için önerilir `CancellationToken` . Kullanıcıdan parolayı değiştirmesi veya Multi-Factor-Authentication gerçekleştirmesi istenen durumlarda dikkate almanız gereken en az birkaç dakika boyunca bir zaman aşımı önerilir.

### <a name="how-to-use-the-default-os-browser"></a>Varsayılan işletim sistemi tarayıcısını kullanma

MSAL.NET `http://localhost:port` , kullanıcının kimlik doğrulaması TAMAMLANDıĞıNDA AAD tarafından gönderilen kodu dinlemesi ve bu kodun üzerinde işlem yapması gerekir (Ayrıntılar Için bkz. [yetkilendirme kodu](v2-oauth2-auth-code-flow.md) )

Sistem tarayıcısını etkinleştirmek için:

1. Uygulama kaydı sırasında `http://localhost` yeniden yönlendirme URI 'si olarak yapılandırın (Şu anda B2C tarafından desteklenmez)
2. PublicClientApplication oluşturduğunuzda, bu yeniden yönlendirme URI 'sini belirtin:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Yapılandırırsanız `http://localhost` , dahili msal.net rastgele bir açık bağlantı noktası bulur ve onu kullanır.

### <a name="linux-and-mac"></a>Linux ve MAC

Linux 'ta MSAL.NET, xdg-Open aracını kullanarak varsayılan işletim sistemi tarayıcısını açar. Sorunu gidermek için, aracı bir terminalden çalıştırın, örneğin, `xdg-open "https://www.bing.com"` . Mac 'te, tarayıcı çağrılarak açılır `open <url>` .

### <a name="customizing-the-experience"></a>Deneyimi özelleştirme

> [!NOTE]
> Özelleştirme, MSAL.NET 4.1.0 veya üzeri sürümlerde kullanılabilir.

MSAL.NET, bir belirteç alındığında veya hata durumunda HTTP iletisiyle yanıt verebilir. Bir HTML iletisi görüntüleyebilir veya istediğiniz URL 'ye yeniden yönlendirebilirsiniz:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Belirli bir tarayıcıyı açma (deneysel)

MSAL.NET tarayıcıyı açan yöntemi özelleştirebilirsiniz. Örneğin, varsayılan tarayıcı kullanılması yerine, belirli bir tarayıcıyı açmaya zorlayabilirsiniz:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP, System WebView 'u kullanmıyor

Ancak, masaüstü uygulamaları için, bir Sistem WebView 'u başlatmak, kullanıcı tarayıcıyı gördüğü için, başka sekmelerin açık olduğu şekilde bir alt türe sahip kullanıcı deneyimine yol açar. Kimlik doğrulaması gerçekleştiği zaman, kullanıcılar bu pencereyi kapatmasını isteyen bir sayfa alır. Kullanıcı dikkat halinde ödeme yapmazsa, tüm işlemi kapatabilir (kimlik doğrulamasıyla ilgisi olmayan diğer sekmeler dahil). Masaüstü üzerindeki sistem tarayıcısını kullanmak ayrıca, yerel bağlantı noktalarını açıp dinlemeden, uygulama için gelişmiş izinler gerektirebilecek olması gerekir. Bir geliştirici, Kullanıcı veya yönetici olarak bu gereksinim hakkında daha fazla bilgi alabilirsiniz.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>İOS ve Android 'de katıştırılmış Web görünümlerini etkinleştirme

Ayrıca, Xamarin. iOS ve Xamarin. Android uygulamalarında katıştırılmış Web görünümlerini de etkinleştirebilirsiniz. MSAL.NET 2.0.0-Preview ile başlayarak, MSAL.NET ayrıca **Embedded** WebView seçeneğinin kullanılmasını destekler. ADAL.NET için, gömülü Web görünümü desteklenen tek seçenektir.

Xamarin 'i hedefleyen MSAL.NET kullanan bir geliştirici olarak, katıştırılmış Web görünümleri veya sistem tarayıcıları kullanmayı seçebilirsiniz. Bu, hedeflemek istediğiniz kullanıcı deneyimine ve güvenlik kaygıına bağlı olarak sizin seçimidir.

Şu anda MSAL.NET, Android ve iOS aracılarını henüz desteklememektedir. Bu nedenle, çoklu oturum açma (SSO) sağlamak için Sistem tarayıcısı yine de daha iyi bir seçenek olabilir. Katıştırılmış Web tarayıcısıyla aracıları destekleme MSAL.NET kapsamınızdan bulunur.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Katıştırılmış WebView ve sistem tarayıcısı arasındaki farklılıklar
MSAL.NET ' de ekli Web görünümü ve sistem tarayıcısı arasında bazı görsel farklılıklar vardır.

**Katıştırılmış Web görünümü kullanılarak MSAL.NET ile etkileşimli oturum açma:**

![katıştırılmış](media/msal-net-web-browsers/embedded-webview.png)

**Sistem tarayıcısını kullanarak MSAL.NET ile etkileşimli oturum açma:**

![Sistem tarayıcısı](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Geliştirici Seçenekleri

MSAL.NET kullanan bir geliştirici olarak, STS 'den etkileşimli iletişim kutusunu görüntülemek için çeşitli seçenekleriniz vardır:

- **Sistem tarayıcısı.** Sistem tarayıcısı kitaplıkta varsayılan olarak ayarlanır. Android kullanıyorsanız, hangi tarayıcıların kimlik doğrulaması için desteklendiği hakkında belirli bilgiler için [sistem tarayıcılarını](msal-net-system-browser-android-considerations.md) okuyun. Android 'de sistem tarayıcısını kullanırken, cihazın Chrome özel sekmelerini destekleyen bir tarayıcısı olması önerilir.  Aksi takdirde, kimlik doğrulaması başarısız olabilir.
- **Katıştırılmış Web görünümü.** MSAL.NET içinde yalnızca katıştırılmış Web görünümü kullanmak için, `AcquireTokenInteractively` Parametreler Oluşturucusu bir `WithUseEmbeddedWebView()` yöntemi içerir.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Xamarin. iOS üzerinde katıştırılmış Web tarayıcısı veya sistem tarayıcısı arasından seçim yapma

İOS uygulamanızda, ' ı ' a başlatabilirsiniz `AppDelegate.cs` `ParentWindow` `null` . İOS 'ta kullanılmıyor

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Xamarin. Android üzerinde katıştırılmış Web tarayıcısı veya sistem tarayıcısı arasından seçim yapma

Android uygulamanızda, `MainActivity.cs` kimlik doğrulama sonucunun geri dönmesi için üst etkinliği ayarlayabilirsiniz:

```csharp
 App.ParentWindow = this;
```

Ardından, `MainPage.xaml.cs` :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Xamarin. Android üzerinde özel sekmelerin varlığını algılama

Tarayıcıda çalışan uygulamalarla SSO 'yu etkinleştirmek için sistem Web tarayıcısını kullanmak istiyorsanız, ancak özel sekme desteği olan bir tarayıcıya sahip olmayan Android cihazlara yönelik kullanıcı deneyimiyle ilgili endişeli varsa, `IsSystemWebViewAvailable()` yöntemi ' de çağırarak seçeneğini belirleyin `IPublicClientApplication` . Bu yöntem `true` , PackageManager özel sekmeler algıladığında ve `false` cihazda algılanmadığında döndürür.

Bu yöntemin döndürdüğü değere ve gereksinimlerinize bağlı olarak, bir karar verebilir:

- Kullanıcıya özel bir hata iletisi döndürebilirsiniz. Örneğin: "Lütfen, kimlik doğrulamaya devam etmek için Chrome 'ı yükleyip" veya-
- Katıştırılmış WebView seçeneğine geri dönebilmeniz ve Kullanıcı arabirimini gömülü bir Web görünümü olarak başlatmanız gerekebilir.

Aşağıdaki kod katıştırılmış WebView seçeneğini göstermektedir:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core, ekli bir tarayıcıyla etkileşimli kimlik doğrulamayı desteklemez

.NET Core için belirteçleri etkileşimli olarak alma, gömülü Web görünümleriyle değil yalnızca sistem Web tarayıcısı aracılığıyla kullanılabilir. Aslında, .NET Core Kullanıcı arabirimini henüz sağlamıyor.
Gözatma deneyimini sistem web tarayıcısıyla özelleştirmek istiyorsanız, [ıwithcustomuı](scenario-desktop-acquire-token.md#withcustomwebui) arabirimini uygulayabilir ve hatta kendi tarayıcınızı sağlayabilirsiniz.
