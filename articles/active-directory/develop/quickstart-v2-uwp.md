---
title: 'Hızlı başlangıç: Kullanıcı oturumu açma ve Evrensel Windows Platformu uygulamada Microsoft Graph çağırma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir Evrensel Windows Platformu (UWP) uygulamasının bir erişim belirtecini nasıl alabileceğinizi ve Microsoft Identity platform tarafından korunan bir API 'YI nasıl çağırabileceğinizi öğrenin.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 88fd6260df0039e960ac87b4b83d43ea497a58b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658518"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Hızlı Başlangıç: Evrensel Windows Platformu (UWP) uygulamasından Microsoft Graph API'sini çağırma

Bu hızlı başlangıçta, bir Evrensel Windows Platformu (UWP) uygulamasının kullanıcılara nasıl oturum açıp Microsoft Graph API 'sini çağırmak için bir erişim belirteci alma yöntemi gösteren bir kod örneği indirip çalıştırırsınız. 

Örneğin bir çizim için [nasıl çalıştığını](#how-the-sample-works) görün.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Önkoşullar
>
> * Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme
> [!div renderon="docs" class="sxs-lookup"]
> Hızlı başlangıç uygulamanızı başlatmak için kullanabileceğiniz iki seçenek vardır:
> * [Hızlı] [1. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırma ve ardından kod örneğinizi indirme](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [El ile] [2. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırın ve ardından kod örneğinizi indirin
>
> 1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyimine gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yönergeleri izleyerek yeni uygulamanızı tek tıkla indirin ve otomatik olarak yapılandırın.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze eklemek için şu adımları izleyin:
> 1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
> 1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. Uygulamanız için bir **ad** girin (örneğin,) `UWP-App-calling-MsGraph` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
> 1. **Desteklenen hesap türleri** bölümünde **Herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları (ör. Skype, Xbox, Outlook.com)** seçeneğini belirtin.
> 1. Uygulamayı oluşturmak için **Kaydet** ' i seçin ve ardından **uygulama (istemci) kimliğini** sonraki bir adımda kullanmak üzere kaydedin.
> 1. **Yönet** altında **kimlik doğrulaması**' nı seçin.
> 1. **Platform**  >  **mobil ve Masaüstü uygulamaları Ekle '** yi seçin.
> 1. **Yeniden yönlendirme URI 'leri** altında öğesini seçin `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. **Yapılandır**'ı seçin.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>1. Adım: uygulamayı yapılandırma
> Bu hızlı başlangıçtaki kod örneğinin çalışması için bir **yeniden yönlendirme URI 'si** ekleyin `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişikliği benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-uwp/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-the-visual-studio-project"></a>2. Adım: Visual Studio projesini Indirme

> [!div renderon="docs"]
> [Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Visual Studio 2019 kullanarak projeyi çalıştırın.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. Adım: uygulamanız yapılandırıldı ve çalıştırılmaya hazırlanıyor
> Projenizi uygulamanızın özelliklerinin değerleriyle yapılandırdık ve çalıştırılmaya hazırlanıyor.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>3. Adım: Visual Studio projesini yapılandırma
>
> 1. . Zip arşivini, sürücünüzün köküne yakın bir yerel klasöre ayıklayın. Örneğin, **C:\Azure-Samples**.
> 1. Projeyi Visual Studio'da açın. İstenirse **Evrensel Windows platformu geliştirme** iş yükünü ve bireysel SDK bileşenlerini yükler.
> 1. *MainPage. xaml. cs* dosyasında, `ClientId` değişkenin değerini daha önce kaydettiğiniz uygulamanın **uygulama (istemci) kimliğiyle** değiştirin.
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    **Uygulama (istemci) kimliğini** Azure Portal (**Azure Active Directory**   >  **uygulama kayıtları**  >  *{uygulamanızın kaydınız}*) uygulamanın genel bakış bölmesinde bulabilirsiniz.
> 1. Paket için otomatik olarak imzalanan yeni bir test sertifikası oluşturun ve seçin:
>     1. **Çözüm Gezgini** *Package. appxmanifest* dosyasına çift tıklayın.
>     1. **Paketleme**  >  **sertifikası seç...**  >  öğesini seçin **Oluştur...**.
>     1. Bir parola girin ve **Tamam**' ı seçin.
>     1. **Dosyadan seç...** öğesini seçin ve ardından yeni oluşturduğunuz *Native_UWP_V2_TemporaryKey. pfx* dosyasını seçin ve **Tamam**' ı seçin.
>     1. *Package. appxmanifest* dosyasını kapatın (dosyayı kaydetmek isteyip Istemediğiniz sorulursa **Tamam ' ı** seçin).
>     1. **Çözüm Gezgini**, **Native_UWP_V2** projesine sağ tıklayın ve **Özellikler**' i seçin.
>     1. **İmza**' yı seçin ve ardından **tanımlayıcı ad seçin anahtar dosya** açılan kutusunda oluşturduğunuz. pfx dosyasını seçin.

#### <a name="step-4-run-the-application"></a>4. Adım: uygulamayı çalıştırma

Örnek uygulamayı yerel makinenizde çalıştırmak için:

1. Visual Studio araç çubuğunda doğru platformu seçin (büyük olasılıkla **x64** veya **x86**, ARM değil). Hedef cihazın *cihazdan* *yerel makineye* değiştirilmesi gerekir.
1. **Hata Ayıklama** > **Hata Ayıklamadan Başlat**’ı seçin.
    
    Bunu yapmanız istenirse, önce **Geliştirici modunu** etkinleştirmeniz ve sonra uygulamayı başlatmak Için **hata ayıklama olmadan yeniden başlatmanız** gerekebilir.

Uygulamanın penceresi göründüğünde, **MICROSOFT Graph API 'Yi çağır** düğmesini seçebilir, kimlik bilgilerinizi girebilir ve uygulama tarafından istenen izinlere izin verebilirsiniz. Başarılı olursa, uygulama Microsoft Graph API 'sine yapılan çağrıdan alınan bazı belirteç bilgilerini ve verileri görüntüler.

## <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı

![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)), kullanıcıları oturum açmak ve güvenlik belirteçleri istemek için kullanılan kitaplıktır. Güvenlik belirteçleri, Microsoft Identity platformu tarafından korunan bir API 'ye erişmek için kullanılır. MSAL kitaplığını Visual Studio'nun *Paket Yöneticisi Konsolu*'nda aşağıdaki komutu çalıştırarak yükleyebilirsiniz:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL başlatma

Şu kodu ekleyerek MSAL başvurusunu ekleyebilirsiniz:

```csharp
using Microsoft.Identity.Client;
```

Daha sonra, MSAL aşağıdaki kod kullanılarak başlatılır:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

Değeri, `ClientId` Azure Portal kaydettiğiniz uygulamanın **uygulama (ISTEMCI) kimliğidir** . Bu değeri Azure portalda uygulamanın **Genel bakış** sayfasında bulabilirsiniz.

### <a name="requesting-tokens"></a>Belirteç isteme

MSAL, UWP uygulamasında belirteçleri almak için iki yönteme sahiptir: `AcquireTokenInteractive` ve `AcquireTokenSilent` .

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

Bazı durumlar, kullanıcıların kimlik bilgilerini doğrulamak veya onay vermek üzere bir açılan pencere aracılığıyla Microsoft Identity platformu ile etkileşime geçmesini zorunlu kılmalarına gerek duyar. Bazı örnekler:

- İlk kullanıcılar uygulamada oturum açtığında
- Parolanın süresi dolduğundan kullanıcıların kimlik bilgilerini yeniden girmesi gerektiğinde
- Uygulamanız bir kaynağa erişim isteğinde bulunduğunda, kullanıcının onay yapması gerekir
- İki öğeli kimlik doğrulaması gerektiğinde

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

`scopes`Parametresi, `{ "user.read" }` Microsoft Graph veya `{ "api://<Application ID>/access_as_user" }` özel Web API 'leri için istenen kapsamları içerir.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`AcquireTokenSilent`Başlangıç yönteminden sonra korumalı kaynaklara erişmek için belirteçleri elde etmek için yöntemini kullanın `AcquireTokenInteractive` . Bir kaynağa erişmesi gereken her seferinde kullanıcının kimlik bilgilerini doğrulamasını gerektirmek istemezsiniz. Herhangi bir kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri istediğiniz zaman

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes``{ "user.read" }`Microsoft Graph veya `{ "api://<Application ID>/access_as_user" }` özel Web API 'leri için istenen kapsamları içerir.
* `firstAccount` Önbellekteki ilk kullanıcı hesabını belirtir (MSAL, tek bir uygulamadaki birden çok kullanıcıyı destekler).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıcın tam bir açıklamasının da içinde olduğu yeni özellikleri ve uygulamaları oluşturma hakkında eksiksiz adım adım kılavuz için Windows masaüstü öğreticisini deneyin.

> [!div class="nextstepaction"]
> [UWP - Graph API'si çağırma öğreticisi](tutorial-v2-windows-uwp.md)
