---
title: Konsol uygulamasının kimliğini kullanarak bir belirteç alın ve Microsoft Graph API 'sini çağırın | Mavisi
titleSuffix: Microsoft identity platform
description: .NET Core işleminin bir erişim belirteci alma ve uygulamanın kendi kimliğini kullanarak Microsoft Identity platform uç noktası tarafından korunan bir API 'YI çağırma hakkında bilgi edinin
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2617013b90cc9709a27ff8ab8c5046ebdcc9951
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149423"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Hızlı başlangıç: konsol uygulamasının kimliğini kullanarak bir belirteç alın ve Microsoft Graph API 'sini çağırın

Bu hızlı başlangıçta, uygulamanın kendi kimliğini kullanarak bir erişim belirteci alabilir ve sonra dizindeki [kullanıcıların listesini](https://docs.microsoft.com/graph/api/user-list) göstermek IÇIN Microsoft Graph API 'sini çağırabilmeniz için bir .NET Core uygulaması yazmayı öğreneceksiniz. Bu senaryo, kullanıcının kimliği yerine gözetimsiz, katılımsız iş veya Windows hizmeti 'nin bir uygulama kimliğiyle çalışması gereken durumlar için yararlıdır.

![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç, [.NET Core 2,2](https://www.microsoft.com/net/download/dotnet-core/2.2)gerektirir.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı kaydetme ve indirme

> [!div renderon="docs" class="sxs-lookup"]
>
> Hızlı başlangıç uygulamanızı başlatmak için iki seçeneğiniz vardır: Express (aşağıdaki 1. seçenek) ve manuel (seçenek 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. Seçenek: Uygulamanızı otomatik olarak kaydedip yapılandırın ve ardından kod örneğinizi indirin
>
> 1. Yeni [Azure Portal-uygulama kayıtları](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) bölmesine gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yönergeleri izleyerek yeni uygulamanızı yalnızca tek tıklamayla indirin ve otomatik olarak yapılandırın.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. Seçenek: Uygulamanızı ve kod örneğinizi el ile kaydetme ve yapılandırma

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1\. Adım: Uygulamanızı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalda](https://portal.azure.com) oturum açın.
> 1. Hesabınız size birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu istediğiniz Azure AD kiracısına ayarlayın.
> 1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
> 1. **Yeni kayıt**seçeneğini belirleyin.
> 1. **Bir uygulamayı kaydet** sayfası göründüğünde, uygulamanızın kayıt bilgilerini girin. 
> 1. **Ad** bölümünde, uygulamanın kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `Daemon-console`, ardından uygulamayı oluşturmak için **Kaydet** ' i seçin.
> 1. Kaydolduktan sonra **sertifikalar & gizlilikler** menüsünü seçin.
> 1. **İstemci gizli**dizileri altında **+ yeni istemci parolası**' nı seçin. Bir ad verin ve **Ekle**' yi seçin. Parolayı güvenli bir konuma kopyalayın. Kodunuzda kullanmak için ihtiyacınız olacak.
> 1. Şimdi **API izinleri** menüsünü seçin **+ izin Ekle** düğmesini seçin, **Microsoft Graph**' yi seçin.
> 1. **Uygulama izinleri**' ni seçin.
> 1. **Kullanıcı** düğümü altında **User. Read. All**' ı seçin ve ardından **izin Ekle** ' yi seçin.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı indirin ve yapılandırın
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1\. Adım: Uygulamanızı Azure portalında yapılandırma
> Bu hızlı başlangıçta çalışması için kod örneği için, bir istemci parolası oluşturmanız ve Graph API **Kullanıcı. Read. All** uygulama iznini eklemeniz gerekir.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-netcore-daemon/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-your-visual-studio-project"></a>2\. Adım: Visual Studio projenizi indirme

[Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3\. Adım: Visual Studio projenizi yapılandırma

1. Zip dosyasını diskin köküne yakın bir yerel klasöre (örneğin **C:\Azure-Samples**) ayıklayın.
1. Visual Studio- **1-Call-MSGraph\daemon-Console.sln** (isteğe bağlı) çözümünü açın.
1. **AppSettings. JSON** öğesini düzenleyin ve alanların değerlerini `ClientId`, `Tenant` ve `ClientSecret` aşağıdakiler ile değiştirin:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Yeni bir istemci parolası oluştur]()
    
    > [!div class="sxs-lookup" renderon="portal"]
    > > [!NOTE]
    > > Bu hızlı başlangıç, Enter_the_Supported_Account_Info_Here destekler.
    
    > [!div renderon="docs"]
    >> Nerede:
    >> * `Enter_the_Application_Id_Here` - kaydettiğiniz uygulamanın **Uygulama (istemci) Kimliği** değeridir.
    >> * `Enter_the_Tenant_Id_Here`-bu değeri **Kiracı kimliği** veya **kiracı adıyla** değiştirin (örneğin, contoso.Microsoft.com)
    >> * `Enter_the_Client_Secret_Here`-bu değeri, 1. adımda oluşturulan istemci sırrı ile değiştirin.

    > [!div renderon="docs"]
    > > [!TIP]
    > > **Uygulama (istemci) kimliği**, **Dizin (kiracı) kimliği**değerlerini bulmak Için Azure Portal uygulamanın **genel bakış** sayfasına gidin. Yeni bir anahtar oluşturmak için **sertifikalar & gizlilikler** sayfasına gidin.
    
#### <a name="step-4-admin-consent"></a>4\. Adım: yönetici onayı

Bu noktada uygulamayı çalıştırmayı denerseniz, *HTTP 403-Yasak* hatası: `Insufficient privileges to complete the operation`alırsınız. Bunun nedeni, *yalnızca uygulama Izinlerinin* yönetici onayı gerektirdiğinden oluşur. Bu, dizininizin genel yöneticisinin uygulamanıza onay vermesi gerektiği anlamına gelir. Rolünüze bağlı olarak aşağıdaki seçeneklerden birini belirleyin:

##### <a name="global-tenant-administrator"></a>Genel Kiracı Yöneticisi

> [!div renderon="docs"]
> Küresel kiracı yöneticisiyseniz, Azure portalının uygulama kaydı 'nda (Önizleme) **API izinleri** sayfasına gidin ve **{Tenant Name} Için yönetici onayı ver** ' i seçin (burada {Tenant Name} dizininizin adıdır).

> [!div renderon="portal" class="sxs-lookup"]
> Genel yöneticiyseniz, **API izinleri** sayfasına gidin **Enter_the_Tenant_Name_Here Için yönetici onayı ver** ' i seçin.
> > [!div id="apipermissionspage"]
> > [API Izinleri sayfasına gidin]()

##### <a name="standard-user"></a>Standart Kullanıcı

Kiracınızın standart bir kullanıcısı kullanıyorsanız, uygulamanız için yönetici onayı vermesini istemek üzere bir genel yönetici yapmanız gerekir. Bunu yapmak için, yöneticinize aşağıdaki URL 'YI verin:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Nerede:
>> * `Enter_the_Tenant_Id_Here`-bu değeri **Kiracı kimliği** veya **kiracı adıyla** değiştirin (örneğin, contoso.Microsoft.com)
>> * `Enter_the_Application_Id_Here` - kaydettiğiniz uygulamanın **Uygulama (istemci) Kimliği** değeridir.

> [!NOTE]
> Yukarıdaki URL 'YI kullanarak uygulamaya onay verdikten sonra *' AADSTS50011: uygulama Için hiçbir yanıt adresi kaydedilmedi '* hatasıyla karşılaşabilirsiniz. Bu durum, bu uygulama ve URL 'nin bir yeniden yönlendirme URI 'SI olmadığı için oluşur-lütfen hatayı yoksayın.

#### <a name="step-5-run-the-application"></a>5\. Adım: uygulamayı çalıştırma

Visual Studio kullanıyorsanız, uygulamayı çalıştırmak için **F5** tuşuna basın, aksi takdirde uygulamayı komut istemi veya konsol aracılığıyla çalıştırın:

```console
cd {ProjectFolder}\daemon-console\1-Call-Graph
dotnet run
```

> Nerede:
> * *{ProjectFolder}* , ZIP dosyasını ayıkladığınız klasördür. Örnek **C:\Azure-Samples\active-Directory-dotnetcore-Daemon-v2**

Sonuç olarak Azure AD dizininizde bulunan kullanıcıların listesini görmeniz gerekir.

> [!IMPORTANT]
> Bu hızlı başlangıç uygulaması, kendisini gizli istemci olarak tanımlamak için bir istemci gizli anahtarı kullanır. İstemci parolası proje dosyalarınıza düz metin olarak eklendiğinden, güvenlik nedenleriyle, uygulamayı üretim uygulaması olarak düşünmeden önce istemci parolası yerine bir sertifika kullanmanız önerilir. Sertifika kullanma hakkında daha fazla bilgi için bu örnekteki GitHub deposunda yer alan [yönergelere](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) bakın.

## <a name="more-information"></a>Daha fazla bilgi

### <a name="msalnet"></a>MSAL.NET

MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)), Microsoft Identity platform tarafından korunan bir API 'ye erişmek için kullanılan kullanıcılara ve istek belirteçlerine oturum açmak için kullanılan kitaplıktır. Bu hızlı başlangıç, açıklandığı şekilde, uygulamanın temsilci izinleri yerine kendi kimliğini kullanarak belirteçleri ister. Bu örnekte kullanılan kimlik doğrulama akışı, *[istemci kimlik bilgileri OAuth akışı](v2-oauth2-client-creds-grant-flow.md)* olarak bilinir. İstemci kimlik bilgileri akışı ile MSAL.NET kullanma hakkında daha fazla bilgi için [Bu makaleye](https://aka.ms/msal-net-client-credentials)bakın.

 Visual Studio 'nun **Paket Yöneticisi konsolunda**aşağıdaki komutu çalıştırarak msal.net yükleyebilirsiniz:

```powershell
Install-Package Microsoft.Identity.Client
```

Alternatif olarak, Visual Studio kullanmıyorsanız projenize MSAL eklemek için aşağıdaki komutu çalıştırabilirsiniz:

```console
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL başlatma

Şu kodu ekleyerek MSAL başvurusunu ekleyebilirsiniz:

```csharp
using Microsoft.Identity.Client;
```

Sonra da şu kodu kullanarak MSAL başlatın:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
);
```

> | Nerede: ||
> |---------|---------|
> | `config.ClientSecret` | Azure portalında uygulama için istemci gizli dizisi oluşturulmuştur. |
> | `config.ClientId` | **Uygulama (istemci) Kimliği**, Azure portalda kayıtlı uygulamadır. Bu değeri Azure portalda uygulamanın **Genel bakış** sayfasında bulabilirsiniz. |
> | `config.Authority`    | Seçim Kullanıcının kimlik doğrulaması için STS uç noktası. Genellikle {Tenant}, kiracınızın adı veya kiracı kimliğiniz olduğu ortak bulut için <https://login.microsoftonline.com/{tenant}>.|

Daha fazla bilgi için lütfen [başvuru belgelerine bakın `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.iconfidentialclientapplication?view=azure-dotnet)

### <a name="requesting-tokens"></a>Belirteç isteme

Uygulamanın kimliğini kullanarak bir belirteç istemek için `AcquireTokenForClient` yöntemi kullanın:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Nerede:| |
> |---------|---------|
> | `scopes` | İstenen kapsamları içerir. Gizli istemciler için, istenen kapsamların Azure portalında ayarlanan uygulama nesnesi içinde statik olarak tanımlanmış olduğunu göstermek için `{Application ID URI}/.default` benzer biçimi kullanmalıdır (Microsoft Graph için `{Application ID URI}` noktaları `https://graph.microsoft.com`). Özel Web API 'Leri için `{Application ID URI}` Azure portalının uygulama kaydı 'nda (Önizleme) **BIR API 'Yi kullanıma** sunma bölümünde tanımlanmıştır. |

Daha fazla bilgi için lütfen [başvuru belgelerine bakın `AcquireTokenForClient`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient?view=azure-dotnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daemon uygulamaları hakkında daha fazla bilgi için bkz. senaryo giriş sayfası

> [!div class="nextstepaction"]
> [Web API 'Lerini çağıran Daemon uygulaması](scenario-daemon-overview.md)

Daemon uygulaması öğreticisi için bkz.:

> [!div class="nextstepaction"]
> [Daemon .NET Core konsol öğreticisi](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

İzinler ve onay hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [İzinler ve onay](v2-permissions-and-consent.md)

Bu senaryoya yönelik kimlik doğrulama akışı hakkında daha fazla bilgi edinmek için bkz. OAuth 2,0 istemci kimlik bilgileri akışı:

> [!div class="nextstepaction"]
> [İstemci kimlik bilgileri OAuth akışı](v2-oauth2-client-creds-grant-flow.md)

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin.

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
