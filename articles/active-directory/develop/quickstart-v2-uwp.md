---
title: Microsoft Identity Platform Windows UWP hızlı başlangıç | Mavisi
description: Bir Evrensel Windows Platformu (XAML) uygulamasının bir erişim belirtecini nasıl alabileceğinizi ve Microsoft Identity platform uç noktası tarafından korunan bir API 'YI nasıl çağırabileceğinizi öğrenin.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.collection: M365-identity-device-management
ms.openlocfilehash: efe6946a3de2a95f15e017dbd65e2ec84cc2f2f3
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964083"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Hızlı Başlangıç: Evrensel Windows Platformu (UWP) uygulamasından Microsoft Graph API'sini çağırma

Bu hızlı başlangıç, bir Evrensel Windows Platformu (UWP) uygulamasının kullanıcıları kişisel hesaplar veya iş ve okul hesaplarıyla nasıl oturum açıp Microsoft Graph API 'sini çağırabileceğinizi gösteren bir kod örneği içerir.

![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme
> [!div renderon="docs" class="sxs-lookup"]
> Hızlı başlangıç uygulamanızı başlatmak için kullanabileceğiniz iki seçenek vardır:
> * [Hızlı] [1. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırma ve ardından kod örneğinizi indirme](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [El ile] [2. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırın ve ardından kod örneğinizi indirin
>
> 1. Yeni [Azure Portal-uygulama kayıtları](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) bölmesine gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'e tıklayın.
> 1. Yönergeleri izleyerek yeni uygulamanızı tek tıkla indirin ve otomatik olarak yapılandırın.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1\. Adım: Uygulamanızı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze eklemek için şu adımları izleyin:
> 1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
> 1. Hesabınız size birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu istediğiniz Azure AD kiracısına ayarlayın.
> 1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://aka.ms/MobileAppReg) sayfasına gidin.
> 1. **Yeni kayıt**seçeneğini belirleyin.
> 1. **Uygulama kaydet** sayfası göründüğünde uygulamanızın kayıt bilgilerini girin:
>      - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `UWP-App-calling-MsGraph`.
>      - **Desteklenen hesap türleri** bölümünde **Herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları (ör. Skype, Xbox, Outlook.com)** seçeneğini belirtin.
>      - Uygulamayı kaydetmek için **Kaydet**'i seçin.
> 1. Uygulama sayfa listesinde **Kimlik doğrulaması**'nı seçin.
> 1. **Masaüstü + cihazlar** bölümünü genişletin.  ( **Masaüstü + cihazlar** görünür değilse, ilk önce üst başlığa tıklayarak önizleme kimlik doğrulama deneyimini görüntüleyin)
> 1. **Yeniden yönlendirme URI 'si** bölümünde **URI Ekle**' yi seçin.  **Urn: ietf: WG: OAuth: 2.0: OOB**yazın.
> 1. **Kaydet**’i seçin.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>1\. Adım: Uygulamanızı yapılandırma
> Bu hızlı başlangıç kod örneğinin çalışması için **urn:ietf:wg:oauth:2.0:oob** gibi bir yeniden yönlendirme URI’si eklemeniz gerekir.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişikliği benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-uwp/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-your-visual-studio-project"></a>2\. Adım: Visual Studio projenizi indirme

 - [Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3\. Adım: Visual Studio projenizi yapılandırma

1. Zip dosyasını diskin köküne yakın bir yerel klasöre (örneğin **C:\Azure-Samples**) ayıklayın.
1. Projeyi Visual Studio'da açın. UWP SDK 'Yı yüklemek isteyip istemediğiniz sorulabilir. Bu durumda kabul edin.
1. **MainPage.xaml.cs** ' i düzenleyin ve `ClientId` alanının değerlerini değiştirin:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Bu hızlı başlangıç, Enter_the_Supported_Account_Info_Here destekler.    

> [!div renderon="docs"]
> Nerede:
> - `Enter_the_Application_Id_here` - Kaydettiğiniz uygulamanın Uygulama Kimliği değeridir.
>
> > [!TIP]
> > *Uygulama kimliği*değerini bulmak için, portaldaki **genel bakış** bölümüne gidin

#### <a name="step-4-run-your-application"></a>4\. Adım: uygulamanızı çalıştırma

Windows makinenizde hızlı başlangıcı denemek istiyorsanız:

1. Visual Studio araç çubuğunda doğru platformu seçin (büyük olasılıkla **x64** veya **x86**, ARM değil).
   > Hedef cihazın *cihazdan* *yerel makineye* değiştiğini gözlemleyin
1. Hata ayıklamayı Seç | **Hata ayıklama olmadan Başlat**

## <a name="more-information"></a>Daha fazla bilgi

Bu bölümde hızlı başlangıç hakkında daha fazla bilgi verilmektedir.

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)), kullanıcıları oturum açmak ve güvenlik belirteçleri istemek için kullanılan kitaplıktır. Güvenlik belirteçleri, geliştiriciler için Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılır. MSAL kitaplığını Visual Studio'nun *Paket Yöneticisi Konsolu*'nda aşağıdaki komutu çalıştırarak yükleyebilirsiniz:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL başlatma

Şu kodu ekleyerek MSAL başvurusunu ekleyebilirsiniz:

```csharp
using Microsoft.Identity.Client;
```

Daha sonra, MSAL aşağıdaki kod kullanılarak başlatılır:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = new PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Nerede: ||
> |---------|---------|
> | `ClientId` | **Uygulama (istemci) Kimliği**, Azure portalda kayıtlı uygulamadır. Bu değeri Azure portalda uygulamanın **Genel bakış** sayfasında bulabilirsiniz. |

### <a name="requesting-tokens"></a>Belirteç isteme

MSAL UWP uygulamasında belirteçleri almak için iki yöntem vardır: `AcquireTokenInteractive` ve `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

Bazı durumlar, kullanıcıların kimlik bilgilerini doğrulamak veya onay vermek üzere bir açılan pencere aracılığıyla Microsoft Identity platform uç noktasıyla etkileşime geçmesini zorunlu kılmalarına gerek duyar. Bazı örnekler:

- İlk kullanıcılar uygulamada oturum açtığında
- Parolanın süresi dolduğundan kullanıcıların kimlik bilgilerini yeniden girmesi gerektiğinde
- Uygulamanız bir kaynağa erişim isteğinde bulunduğunda, kullanıcının onay yapması gerekir
- İki faktörlü kimlik doğrulama gerektiğinde

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Nerede:||
> |---------|---------|
> | `scopes` | İstenen kapsamları (Microsoft Graph için `{ "user.read" }` veya özel Web API'leri için `{ "api://<Application ID>/access_as_user" }` gibi) barındırır. |

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

İlk `AcquireTokenInteractive` yönteminden sonra korumalı kaynaklara erişmek için belirteçleri almak üzere `AcquireTokenSilent` yöntemini kullanın. Bir kaynağa erişmesi gereken her seferinde kullanıcının kimlik bilgilerini doğrulamasını gerektirmek istemezsiniz. Herhangi bir kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri istediğiniz zaman

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Nerede: ||
> |---------|---------|
> | `scopes` | İstenen kapsamları (Microsoft Graph için `{ "user.read" }` veya özel Web API'leri için `{ "api://<Application ID>/access_as_user" }` gibi) barındırır |
> | `firstAccount` | Önbellekteki ilk kullanıcı hesabını belirtir (MSAL, tek bir uygulamadaki birden çok kullanıcıyı destekler) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıcın tam bir açıklamasının da içinde olduğu yeni özellikleri ve uygulamaları oluşturma hakkında eksiksiz adım adım kılavuz için Windows masaüstü öğreticisini deneyin.

> [!div class="nextstepaction"]
> [UWP - Graph API'si çağırma öğreticisi](tutorial-v2-windows-uwp.md)

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin.

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
