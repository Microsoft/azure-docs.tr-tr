---
title: Öğretici-kimlik doğrulaması için Microsoft Identity platformunu kullanan bir Blazor Server uygulaması oluşturma | Mavisi
titleSuffix: Microsoft identity platform
description: Bu öğreticide, bir Blazor sunucu uygulamasında Microsoft Identity platformunu kullanarak kimlik doğrulaması ayarlarsınız.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 5a631d9ae7a7d1792e3c4e4a2cbf8281e1168283
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226025"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir Blazor Server uygulaması oluşturma

Bu öğreticide, kullanıcıları oturum açan ve Microsoft Identity platformunu kullanarak Microsoft Graph verileri alan ve uygulamanızı Azure Active Directory (Azure AD) kaydeden bir Blazor Server uygulaması oluşturacaksınız.

Ayrıca, [Blazor](tutorial-blazor-webassembly.md)te için bir öğretici sunuyoruz.

Bu öğreticide:

> [!div class="checklist"]
> * Kimlik doğrulaması için Azure Active Directory (Azure AD) kullanmak üzere yapılandırılmış yeni bir Blazor Server uygulaması oluşturma
> * Microsoft. Identity. Web kullanarak hem kimlik doğrulamasını hem de yetkilendirmeyi işleme
> * Korumalı bir Web API 'sinden veri alma, Microsoft Graph

## <a name="prerequisites"></a>Önkoşullar

- [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- Bir uygulamayı kaydedebileceğiniz bir Azure AD kiracısı. Bir Azure AD kiracısına erişiminiz yoksa, [Microsoft 365 Geliştirici programına](https://developer.microsoft.com/microsoft-365/dev-program) kaydolarak veya [ücretsiz Azure hesabı](https://azure.microsoft.com/free)oluşturarak bir tane edinebilirsiniz.

## <a name="register-the-app-in-the-azure-portal"></a>Uygulamayı Azure portal kaydetme

Kimlik doğrulaması için Azure Active Directory (Azure AD) kullanan her uygulamanın Azure AD 'ye kayıtlı olması gerekir. Bu eklemeleri kullanarak [bir uygulamayı kaydetme](quickstart-register-app.md) bölümündeki yönergeleri izleyin:

- **Desteklenen hesap türleri** için **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
- **Yeniden yönlendirme URI 'si** açılan öğesini **Web** olarak ayarlayın ve girin `https://localhost:5001/signin-oidc` . Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir. Uygulama farklı bir bağlantı noktasında kullanılabiliyorsa, yerine bu bağlantı noktası numarasını belirtin `5001` .

**Yönet**' in altında **kimlik doğrulama**  >  **örtük verme ve karma akışlar**' ı seçin. **Erişim belirteçleri** ve **Kimlik belirteçleri**' ni seçin ve ardından **Kaydet**' i seçin.

Son olarak, uygulama korumalı bir API 'yi çağırdığı için (Bu durumda Microsoft Graph), bu API 'yi çağırmak için bir erişim belirteci istediğinde kimliğini doğrulamak için bir istemci parolası gerekir.

1. Aynı uygulama kaydı içinde, **Yönet** altında **Sertifikalar & gizlilikler**' ı seçin.
2. Süresi dolmasın **Yeni bir istemci gizli anahtarı** oluşturun.
3. Bir sonraki adımda kullanacağınız için gizli dizi **değerini** unutmayın. Bu bölmeden ayrıldığınızda bu sayfaya yeniden erişemezsiniz. Ancak, gerektiğinde yeniden oluşturabilirsiniz.

## <a name="create-the-app-using-the-net-cli"></a>.NET CLı kullanarak uygulama oluşturma

Bu öğreticide kullanabilmemiz için Microsoft. Identity. Web şablonlarını indirmek üzere aşağıdaki komutu çalıştırın.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

Ardından, uygulamayı oluşturmak için aşağıdaki komutu çalıştırın. Komutta yer tutucuları uygulamanızın genel bakış sayfasından uygun bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün. Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Yer tutucu   | Azure portal adı       | Örnek                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Uygulama (istemci) kimliği | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Dizin (kiracı) kimliği   | `e86c78e2-0000-0000-0000-918e0565a45e` |

Şimdi, düzenleyicinizdeki yeni Blazor uygulamanıza gidin ve "gizli-uygulama-kayıt" metnini değiştirerek istemci gizli anahtarını dosyaya *appsettings.js* ekleyin.

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>Uygulamayı test etme

Artık uygulamayı derleyebilir ve çalıştırabilirsiniz. Bu şablon uygulamasını çalıştırdığınızda,--Framework kullanarak çalıştırılacak Framework 'ü belirtmeniz gerekir. Bu öğretici .NET Core 3,1 SDK 'sını kullanır.

```dotnetcli
dotnet run --framework netcoreapp3.1
```

Tarayıcınızda `https://localhost:5001` , ' a gidin ve uygulamanın çalıştığını görmek için bir Azure AD Kullanıcı hesabı kullanarak oturum açın.

## <a name="retrieving-data-from-microsoft-graph"></a>Microsoft Graph verileri alma

[Microsoft Graph](/graph/overview) kullanıcılarınızın Microsoft 365 verilerine erişim sağlayan bir dizi API sunar. Microsoft Identity platformunu uygulamanızın kimlik sağlayıcısı olarak kullanarak Microsoft Identity platform tarafından verilen belirteçleri Microsoft Graph doğrudan desteklediğinden bu bilgilere daha kolay erişebilirsiniz. Bu bölümde, oturum açan kullanıcının e-postalarını uygulamanın "verileri getir" sayfasında görüntüleyecek kodu ekleyin.

Başlamadan önce, gerekli izinlerde değişiklikler yaptığınız için uygulamanızı oturumunuzu kapatıp geçerli belirteciniz çalışmayacak. Henüz yapmadıysanız, aşağıdaki kodu güncelleştirmeden önce uygulamanızı yeniden çalıştırın ve **Oturumu Kapat** ' ı seçin.

Şimdi, bir kullanıcının e-postasını çekmek ve iletileri uygulama içinde göstermek için uygulamanızın kaydını ve kodunu güncelleştiğinizde. Bunu başarmak için, önce e-posta verilerine erişimi etkinleştirmek üzere Azure AD 'de uygulama kayıt izinleri ' ni genişletin. Ardından, Blazor uygulamasına kod ekleyerek bu verileri sayfalardan birinde alıp görüntüleyin.

1. Azure portal, **uygulama kayıtları**' de Uygulamanızı seçin.
1. **Yönet** altında **API izinleri**' ni seçin.
1. Microsoft Graph **izin Ekle**' yi seçin  >  .
1. **Temsilci izinleri**' ni seçin, sonra **posta. Read** iznini arayıp seçin.
1. **Izin Ekle**' yi seçin.

Dosyadaki *appsettings.js* , doğru izinlerle uygun belirteci getirmemesi için kodunuzu güncelleştirin. "DownstreamAPI" altındaki "User. Read" kapsamından sonra "mail. Read" ekleyin. Bu, uygulamanın hangi kapsamları (veya izinleri) erişim isteyeceğini belirtmektir.

```json
"Scopes": "user.read mail.read"
```

Ardından, *fetchdata. Razor* dosyasındaki kodu, varsayılan (rastgele) Hava durumu ayrıntıları yerine e-posta verilerini almak için güncelleştirin. Bu dosyadaki kodu aşağıdaki kodla değiştirin:

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

        if (dataRequest.IsSuccessStatusCode)
        {
            var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
            userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
        }

        var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

        if (mailRequest.IsSuccessStatusCode)
        {
            var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
            var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

            foreach (var m in messagesArray)
            {
                var message = new MailMessage();
                message.Subject = m.GetProperty("subject").GetString();
                message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                messages.Add(message);
            }
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

Uygulamayı başlatma. Her şeyin beklendiği gibi çalıştığını belirten yeni eklenen izinler için sizden istemde bulunulduğuna dikkat edin. Artık temel Kullanıcı profili verilerinin ötesinde, uygulama e-posta verilerine erişim istiyor.

Onay verdikten sonra, bazı e-postaları okumak için "verileri getir" sayfasına gidin.

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="Son uygulamanın ekran görüntüsü. Hello Nicholas yazan bir başlığa sahiptir ve Nicholas 'e ait e-postaların bir listesini gösterir.":::

## <a name="next-steps"></a>Sonraki adımlar

Çok bölgeli senaryo serimizin kullanıcılarına oturum açmak için Web uygulamaları oluşturmayı çağırma hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Senaryo: kullanıcılarda oturum açan Web uygulaması](scenario-web-app-sign-user-overview.md)
