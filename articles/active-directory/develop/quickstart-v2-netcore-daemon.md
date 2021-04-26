---
title: 'Hızlı başlangıç: bir konsol uygulamasında belirteci al & çağrısı Microsoft Graph | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu hızlı başlangıçta, bir .NET Core örnek uygulamasının bir belirteç almak ve Microsoft Graph çağırmak için istemci kimlik bilgileri akışını nasıl kullanabileceğinizi öğrenirsiniz.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: a31bf345f523eea940be5d56495890e8ab5c6dbd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861652"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Hızlı başlangıç: bir belirteç alın ve konsol uygulamasının kimliğini kullanarak Microsoft Graph API 'sini çağırın

Bu hızlı başlangıçta, bir .NET Core konsol uygulamasının Microsoft Graph API 'yi çağırmak ve dizindeki [kullanıcıların listesini](/graph/api/user-list) göstermek için bir erişim belirteci nasıl alabilirim olduğunu gösteren bir kod örneği indirip çalıştırırsınız. Kod örneği Ayrıca bir işin veya bir Windows hizmetinin bir kullanıcı kimliği yerine bir uygulama kimliğiyle nasıl çalıştırılabileceğinizi gösterir. Bu hızlı başlangıçta örnek konsol uygulaması aynı zamanda bir Daemon uygulamasıdır ve bu sayede gizli bir istemci uygulamasıdır.

> [!div renderon="docs"]
> Aşağıdaki diyagramda örnek uygulamanın nasıl çalıştığı gösterilmektedir:
>
> ![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösteren diyagram.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıç, .net [Core 3,1 SDK 'sı](https://dotnet.microsoft.com/download) gerektirir, ancak .NET 5,0 SDK ile de çalışır.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Uygulamayı kaydedin ve indirin

> [!div renderon="docs" class="sxs-lookup"]
>
> Uygulamanızı oluşturmaya başlamak için iki seçeneğiniz vardır: otomatik veya el ile yapılandırma.
>
> ### <a name="automatic-configuration"></a>Otomatik yapılandırma
>
> Uygulamanızı kaydetmek ve otomatik olarak yapılandırmak ve ardından kod örneğini indirmek istiyorsanız aşağıdaki adımları izleyin:
>
> 1. <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Uygulama kaydı için Azure Portal sayfasına</a>gidin.
> 1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
> 1. Yeni uygulamanızı tek tıklamayla indirmek ve otomatik olarak yapılandırmak için yönergeleri izleyin.
>
> ### <a name="manual-configuration"></a>El ile yapılandırma
>
> Uygulamanızı ve kod örneğinizi el ile yapılandırmak istiyorsanız aşağıdaki yordamları kullanın.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. Adım: Uygulamanızı kaydetme
> Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze el ile eklemek için şu adımları izleyin:
>
> 1. <a href="https://portal.azure.com/" target="_blank">Azure Portal </span> </a>oturum açın.
> 1. Birden çok kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
> 1. **Azure Active Directory**'yi bulun ve seçin.
> 1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
> 1. **Ad** için, uygulamanız için bir ad girin. Örneğin, **Daemon-Console** yazın. Uygulamanızın kullanıcıları bu adı görür ve daha sonra değiştirebilirsiniz.
> 1. Uygulamayı kaydetmek için **Kaydet**'i seçin.
> 1. **Yönet**’in altında **Sertifikalar ve gizli diziler**’i seçin.
> 1. **İstemci gizli** dizileri altında **yeni istemci parolası**' nı seçin, bir ad girin ve ardından **Ekle**' yi seçin. Daha sonraki bir adımda kullanmak üzere gizli bir konuma gizli değeri kaydedin.
> 1. **Yönet** altında **API izinleri**  >  **bir izin Ekle**' yi seçin. **Microsoft Graph** seçin.
> 1. **Uygulama izinleri**' ni seçin.
> 1. **Kullanıcı** düğümü altında **User. Read. All**' ı seçin ve ardından **izin Ekle**' yi seçin.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Hızlı başlangıç uygulamanızı indirin ve yapılandırın
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. Adım: uygulamanızı Azure portal yapılandırma
> Bu hızlı başlangıçtaki kod örneğinin çalışması için bir istemci parolası oluşturun ve Graph API **User. Read. All** uygulama iznini ekleyin.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Bu değişiklikleri benim için yap]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Zaten yapılandırılmış](media/quickstart-v2-netcore-daemon/green-check.png) Uygulamanız bu özniteliklerle yapılandırılmış.

#### <a name="step-2-download-your-visual-studio-project"></a>2. Adım: Visual Studio projenizi indirme

> [!div renderon="docs"]
> [Visual Studio projesini indirin](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> Belirtilen projeyi Visual Studio 'da veya Mac için Visual Studio çalıştırabilirsiniz.


> [!div class="sxs-lookup" renderon="portal"]
> Visual Studio 2019 kullanarak projeyi çalıştırın.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Kod örneğini indirin](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>3. Adım: Visual Studio projenizi yapılandırma
>
> 1. . Zip dosyasını, diskin köküne yakın bir yerel klasöre ayıklayın. Örneğin, *C:\Azure-Samples*' a ayıklayın.
>
>    Windows üzerinde yol uzunluğu sınırlamalarından kaynaklanan hataları önlemek için, arşivin sürücünüzün köküne yakın bir dizine ayıklanmasını öneririz.
>
> 1. Visual Studio 'da çözümü açın: *1-Call-MSGraph\daemon-Console.sln* (isteğe bağlı).
> 1. *appsettings.jsüzerinde*,, ve değerlerini değiştirin `Tenant` `ClientId` `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    Bu kodda:
>    - `Enter_the_Application_Id_Here` , kaydettiğiniz uygulamanın uygulama (istemci) KIMLIĞIDIR.
        Uygulama (istemci) KIMLIĞI ve dizin (kiracı) KIMLIĞI değerlerini bulmak için, Azure portal uygulamanın **genel bakış** sayfasına gidin.
>    - `Enter_the_Tenant_Id_Here`KIRACı kimliği veya kiracı adıyla değiştirin (örneğin, `contoso.microsoft.com` ).
>    - `Enter_the_Client_Secret_Here`Adım 1 ' de oluşturduğunuz istemci gizli anahtarı ile değiştirin.
    Yeni bir anahtar oluşturmak için **sertifikalar & gizlilikler** sayfasına gidin.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. Adım: yönetici onayı

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. Adım: yönetici onayı

Bu noktada uygulamayı çalıştırmayı denerseniz, bir *HTTP 403-Yasak* hatası alırsınız: "işlemi tamamlamaya yönelik ayrıcalıklar yetersiz". Bu hata oluşur çünkü uygulamanız için izin vermek üzere dizininizin genel yöneticisi olması gerekir. Rolünüze bağlı olarak aşağıdaki seçeneklerden birini belirleyin.

##### <a name="global-tenant-administrator"></a>Genel Kiracı Yöneticisi

> [!div renderon="docs"]
> Genel kiracı yöneticisiyseniz, Azure portal **Kurumsal uygulamalar** ' a gidin. Uygulama kaydınızı seçin ve sol bölmedeki **güvenlik** bölümünden **izinler** ' i seçin. Ardından, **{Tenant Name} için yönetici onayı ver** etiketli büyük düğmeyi seçin (burada **{Tenant Name}** dizininizin adıdır).

> [!div renderon="portal" class="sxs-lookup"]
> Genel yöneticiyseniz, **API izinleri** sayfasına gidin ve **Enter_the_Tenant_Name_Here Için yönetici onayı ver**' i seçin.
> > [!div id="apipermissionspage"]
> > [API Izinleri sayfasına gidin]()

##### <a name="standard-user"></a>Standart Kullanıcı

Kiracınızın standart bir kullanıcısı kullanıyorsanız, genel bir yöneticiden uygulamanız için yönetici onayı vermesini isteyin. Bunu yapmak için, yöneticinize aşağıdaki URL 'YI verin:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Bu URL 'de:
> * `Enter_the_Tenant_Id_Here`KIRACı kimliği veya kiracı adıyla değiştirin (örneğin, `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` , kaydettiğiniz uygulamanın uygulama (istemci) KIMLIĞIDIR.

Yukarıdaki URL 'YI kullanarak uygulamaya onay verdikten sonra, "AADSTS50011: uygulama için kayıtlı yanıt adresi yok" hatasını görebilirsiniz. Bu hata, bu uygulama ve URL 'nin bir yeniden yönlendirme URI 'SI olmadığı için oluşur. Bunu yoksayabilirsiniz.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. Adım: uygulamayı çalıştırma

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. Adım: uygulamayı çalıştırma

Visual Studio veya Mac için Visual Studio kullanıyorsanız **F5** tuşuna basarak uygulamayı çalıştırın. Aksi halde, komut istemi, konsolu veya Terminal aracılığıyla uygulamayı çalıştırın:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Bu kodda:
* `{ProjectFolder}` . zip dosyasını ayıkladığınız klasördür. `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2` bunun bir örneğidir.

Sonuç olarak Azure Active Directory kullanıcıların bir listesini görmeniz gerekir.

Bu hızlı başlangıç uygulaması, kendisini gizli bir istemci olarak tanımlamak için bir istemci gizli anahtarı kullanır. İstemci parolası, proje dosyalarınıza düz metin dosyası olarak eklenir. Güvenlik nedenleriyle, uygulamayı bir üretim uygulaması olarak düşünmeden önce istemci parolası yerine bir sertifika kullanmanızı öneririz. Sertifika kullanma hakkında daha fazla bilgi için bu örnekteki GitHub deposunda yer alan [yönergelere](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) bakın.

## <a name="more-information"></a>Daha fazla bilgi
Bu bölüm, kullanıcıların oturum açması için gereken koda genel bir bakış sağlar. Bu genel bakış, kodun nasıl çalıştığını, ana bağımsız değişkenlerin ne olduğunu ve var olan bir .NET Core konsol uygulamasına nasıl oturum ekleneceğini anlamak için yararlı olabilir.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Örneğin nasıl çalıştığı
>
> ![Bu hızlı başlangıç tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösteren diyagram.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft kimlik doğrulama kitaplığı (MSAL, [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) paketinde), kullanıcıların oturum açması ve Microsoft Identity platformu tarafından korunan bir API 'ye erişim belirteçleri istemek için kullanılan kitaplıktır. Bu hızlı başlangıç, uygulamanın temsilci izinleri yerine kendi kimliğini kullanarak belirteçleri ister. Bu durumda kimlik doğrulama akışı, [istemci kimlik bilgileri OAuth akışı](v2-oauth2-client-creds-grant-flow.md)olarak bilinir. İstemci kimlik bilgileri akışı ile MSAL.NET kullanma hakkında daha fazla bilgi için [Bu makaleye](https://aka.ms/msal-net-client-credentials)bakın.

 Visual Studio Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırarak MSAL.NET yükleyebilirsiniz:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL başlatma

Şu kodu ekleyerek MSAL başvurusunu ekleyebilirsiniz:

```csharp
using Microsoft.Identity.Client;
```

Ardından, aşağıdaki kodu kullanarak MSAL 'i başlatın:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Öğe | Açıklama |
 |---------|---------|
 | `config.ClientSecret` | Azure portal uygulama için istemci parolası oluşturuldu. |
 | `config.ClientId` | Azure portal kayıtlı uygulamanın uygulama (istemci) KIMLIĞI. Bu değeri, Azure portal uygulamanın **genel bakış** sayfasında bulabilirsiniz. |
 | `config.Authority`    | Seçim Kullanıcının kimlik doğrulaması için güvenlik belirteci hizmeti (STS) uç noktası. Genellikle `https://login.microsoftonline.com/{tenant}` , `{tenant}` kiracınızın adı veya kiracı kimliğiniz olduğu genel bulut içindir.|

Daha fazla bilgi için [başvuru belgelerine `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)bakın.

### <a name="requesting-tokens"></a>Belirteç isteme

Uygulamanın kimliğini kullanarak bir belirteç istemek için `AcquireTokenForClient` yöntemini kullanın:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Öğe| Açıklama |
|---------|---------|
| `scopes` | İstenen kapsamları içerir. Gizli istemciler için bu değer, şuna benzer bir biçim kullanmalıdır `{Application ID URI}/.default` . Bu biçim, istenen kapsamların Azure portal ayarlanan uygulama nesnesi içinde statik olarak tanımlanmış olanlar olduğunu gösterir. Microsoft Graph için, `{Application ID URI}` öğesine işaret eder `https://graph.microsoft.com` . Özel Web API 'leri için `{Application ID URI}` Azure Portal tanımlanmıştır, **uygulama kaydı (Önizleme)** altında  >  **bir API 'yi kullanıma sunar**. |

Daha fazla bilgi için [başvuru belgelerine `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)bakın.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

Daemon uygulamaları hakkında daha fazla bilgi için bkz. senaryoya genel bakış:

> [!div class="nextstepaction"]
> [Web API 'Lerini çağıran Daemon uygulaması](scenario-daemon-overview.md)
