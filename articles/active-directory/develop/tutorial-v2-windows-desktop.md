---
title: 'Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir Windows Presentation Foundation (WPF) uygulaması oluşturma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıların oturum açması için Microsoft Identity platformunu kullanan bir WPF uygulaması derleyebilir ve Microsoft Graph API 'sini adına çağırmak için bir erişim belirteci alacaksınız.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 60a29efc4d2daa9d1bc90f00e71094da382a83b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686895"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Öğretici: Windows masaüstü uygulamasından Microsoft Graph API 'sini çağırma

Bu öğreticide, kullanıcıları oturum açan ve Microsoft Graph API 'sini çağırmak için bir erişim belirteci alan yerel bir Windows Masaüstü .NET (XAML) uygulaması oluşturacaksınız. 

Kılavuzu tamamladığınızda, uygulamanız kişisel hesapları (outlook.com, live.com ve diğerleri dahil) kullanan korumalı bir API 'yi çağırabilecektir. Uygulama Ayrıca, Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş ve okul hesapları da kullanacaktır.

Bu öğreticide:

> [!div class="checklist"]
> * Visual Studio 'da *Windows Presentation Foundation (WPF)* projesi oluşturma
> * .NET için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) yükler
> * Uygulamayı Azure portal kaydetme
> * Kullanıcı oturum açma ve oturum kapatma desteği için kod ekleme
> * Microsoft Graph API 'sini çağırmak için kod ekleme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Bu kılavuz tarafından oluşturulan örnek uygulamanın nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Bu kılavuzla oluşturduğunuz örnek uygulama, Microsoft Graph API 'sini veya Microsoft Identity platform uç noktasından belirteçleri kabul eden bir Web API 'sini sorgulayan bir Windows masaüstü uygulaması sunar. Bu senaryo için, yetkilendirme üst bilgisi aracılığıyla HTTP isteklerine belirteç eklersiniz. Microsoft kimlik doğrulama kitaplığı (MSAL) belirteç alma ve yenileme işlemleri gerçekleştirir.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Korumalı Web API 'Lerine erişim için belirteç alımı işleme

Kullanıcının kimliği doğrulandıktan sonra örnek uygulama, Microsoft Graph API 'sini veya Microsoft Identity platformu tarafından güvenliği sağlanan bir Web API 'sini sorgulamak için kullanabileceğiniz bir belirteç alır.

Microsoft Graph gibi API 'Ler, belirli kaynaklara erişim izni vermek için bir belirteç gerektirir. Örneğin, bir kullanıcının profilini okumak, kullanıcının takvimine erişmek veya e-posta göndermek için bir belirteç gerekir. Uygulamanız, API kapsamları belirterek bu kaynaklara erişmek için MSAL kullanarak bir erişim belirteci isteyebilir. Bu erişim belirteci daha sonra, korunan kaynakta yapılan her çağrının HTTP yetkilendirme üstbilgisine eklenir.

MSAL, uygulamanızın ihtiyaç duymaması için, erişim belirteçlerini önbelleğe almayı ve yenilemeyi yönetir.

## <a name="nuget-packages"></a>NuGet paketleri

Bu kılavuzda aşağıdaki NuGet paketleri kullanılmaktadır:

|Kitaplık|Description|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft kimlik doğrulama kitaplığı (MSAL.NET)|

## <a name="set-up-your-project"></a>Projenizi ayarlama

Bu bölümde, uygulamanın bir belirteç gerektiren Web API 'Lerini sorgulayabilmesi için bir Windows Masaüstü .NET uygulamasını (XAML) *Microsoft Ile oturum açma* ile tümleştirmeyi göstermek üzere yeni bir proje oluşturursunuz.

Bu kılavuzda oluşturduğunuz uygulama, bir grafiği çağırmak için kullanılan bir düğme, sonuçları ekranda göstermek için bir alan ve bir oturum kapatma düğmesi görüntüler.

> [!NOTE]
> Bunun yerine bu örneğin Visual Studio projesini indirmeyi tercih etmek istiyor musunuz? [Bir proje indirin](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)ve kod örneğini yürütmeden önce yapılandırmak için [yapılandırma adımına](#register-your-application) atlayın.
>

Uygulamanızı oluşturmak için aşağıdakileri yapın:

1. Visual Studio 'da **Dosya**  >  **Yeni**  >  **Proje**' yi seçin.
2. **Şablonlar** altında **Visual C#**' yi seçin.
3. Kullanmakta olduğunuz Visual Studio sürümünün sürümüne bağlı olarak **WPF uygulaması (.NET Framework)** seçeneğini belirleyin.

## <a name="add-msal-to-your-project"></a>Projenize MSAL ekleyin

1. Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi** >  **Paket Yöneticisi konsolu**' nu seçin.
2. Paket Yöneticisi konsolu penceresinde, aşağıdaki Azure PowerShell komutunu yapıştırın:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > Bu komut, Microsoft kimlik doğrulama kitaplığı 'nı yükleme. MSAL, Azure Active Directory v 2.0 tarafından korunan API 'Lere erişmek için kullanılan Kullanıcı belirteçlerini alma, önbelleğe alma ve yenileme işlemleri gerçekleştirir
    >

## <a name="register-your-application"></a>Uygulamanızı kaydetme

Uygulamanızı iki şekilde kaydedebilirsiniz.

### <a name="option-1-express-mode"></a>Seçenek 1: hızlı mod

Aşağıdakileri yaparak uygulamanızı hızlı bir şekilde kaydedebilirsiniz:
1. <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs" target="_blank">Azure portal uygulama kayıtları</a> hızlı başlangıç deneyimine gidin.
1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
1. Yönergeleri izleyerek yeni uygulamanızı yalnızca tek tıklamayla indirin ve otomatik olarak yapılandırın.

### <a name="option-2-advanced-mode"></a>Seçenek 2: Gelişmiş mod

Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze eklemek için aşağıdakileri yapın:
1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin (örneğin,) `Win-App-calling-MsGraph` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** bölümünde **herhangi bir kuruluş dizininde (HERHANGI bir Azure ad dizini-çoklu kiracı) ve kişisel Microsoft hesaplarından (örn. Skype, Xbox) hesaplar**' ı seçin.
1. **Kaydet**’i seçin.
1. **Yönet** altında **kimlik doğrulama**  >  **Platform ekleme**' yi seçin.
1. **Mobil ve Masaüstü uygulamaları '** nı seçin.
1. **Yeniden yönlendirme URI 'leri** bölümünde öğesini seçin **https://login.microsoftonline.com/common/oauth2/nativeclient** .
1. **Yapılandır**'ı seçin.
1. Visual Studio 'ya gidin, *app. xaml. cs* dosyasını açın ve `Enter_the_Application_Id_here` Aşağıdaki kod parçacığında, yeni kaydettiğiniz ve kopyaladığınız uygulama kimliğiyle değiştirin.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>MSAL başlatmak için kodu ekleyin

Bu adımda, belirteçleri işleme gibi MSAL ile etkileşimi işlemek için bir sınıf oluşturursunuz.

1. *App. xaml. cs* dosyasını açın ve msal başvurusunu sınıfa ekleyin:

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. Uygulama sınıfını şu şekilde güncelleştirin:

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .WithDefaultRedirectUri()
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>Uygulama kullanıcı arabirimini oluşturma

Bu bölümde, bir uygulamanın Microsoft Graph gibi korumalı bir arka uç sunucusunu nasıl sorgulayagösterdiği gösterilmektedir.

Bir *MainWindow. xaml* dosyası otomatik olarak proje şablonunuzun bir parçası olarak oluşturulmalıdır. Bu dosyayı açın ve ardından uygulamanızın *\<Grid>* düğümünü aşağıdaki kodla değiştirin:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft Graph API 'sine yönelik bir belirteç almak için MSAL kullanın

Bu bölümde, Microsoft Graph API 'sine yönelik bir belirteç almak için MSAL kullanırsınız.

1. *MainWindow. xaml. cs* dosyasında, msal başvurusunu sınıfına ekleyin:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. `MainWindow`Sınıf kodunu aşağıdaki kodla değiştirin:

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
        }
    ```

### <a name="more-information"></a>Daha fazla bilgi

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

Yöntemi çağırmak, `AcquireTokenInteractive` kullanıcılardan oturum açmasını isteyen bir pencere ile sonuçlanır. Uygulamalar genellikle kullanıcıların, korunan kaynağa ilk kez erişmesi gerektiğinde etkileşimli olarak oturum açmasını gerektirir. Ayrıca, bir belirteci almak için sessiz bir işlem başarısız olduğunda (örneğin, bir kullanıcının parolasının süre dolduğunda) oturum açması gerekebilir.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`AcquireTokenSilent`Yöntemi, Kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri gerçekleştirir. `AcquireTokenInteractive`İlk kez yürütüldükten sonra, `AcquireTokenSilent` sonraki çağrılar için korunan kaynaklara erişen belirteçleri elde etmek üzere kullanılan her zamanki yöntemdir, çünkü istek veya yenileme belirteçleri için çağrılar sessizce yapılır.

Sonuç olarak, `AcquireTokenSilent` Yöntem başarısız olur. Hatanın nedeni, kullanıcının başka bir cihazda oturum açmış veya parolasını değiştirmiş olması olabilir. MSAL, sorunun etkileşimli bir eylem gerektirerek çözümlenemeyeceğini algıladığında, bir `MsalUiRequiredException` özel durum harekete geçirilir. Uygulamanız bu özel durumu iki şekilde işleyebilir:

* Anında bir çağrı yapabilir `AcquireTokenInteractive` . Bu çağrı kullanıcıdan oturum açmasını ister. Bu model genellikle kullanıcı için kullanılabilir çevrimdışı içerik bulunmayan çevrimiçi uygulamalarda kullanılır. Bu Kılavuzlu kurulum tarafından oluşturulan örnek bu düzeni izler ve bu, örneği ilk kez çalıştırdığınızda eylemde görebilirsiniz.

* Hiçbir Kullanıcı uygulamayı kullanmadığından, `PublicClientApp.Users.FirstOrDefault()` null bir değer içerir ve bir `MsalUiRequiredException` özel durum oluşturulur.

* Örnekteki kod daha sonra çağırarak özel durumu işler ve bu, `AcquireTokenInteractive` kullanıcıdan oturum açmasını ister.

* Bunun yerine, etkileşimli bir oturum açma işlemi için kullanıcılara, oturum açmak için doğru zamanı belirleyebilecekleri bir görsel gösterge sunabilir. Ya da uygulama `AcquireTokenSilent` daha sonra yeniden deneyebilir. Bu model, kullanıcıların kesintiye uğramadan diğer uygulama işlevlerini kullanabilmesi durumunda (örneğin, çevrimdışı içerik uygulamada kullanılabilir olduğunda) sık kullanılır. Bu durumda, kullanıcılar korunan kaynağa erişmek veya güncel olmayan bilgileri yenilemek için ne zaman oturum açmak istediğine karar verebilir. Alternatif olarak, uygulama `AcquireTokenSilent` geçici olarak devre dışı olduktan sonra ağ geri yüklendiğinde yeniden denemeye karar verebilir.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Yeni Edindiğiniz belirteci kullanarak Microsoft Graph API 'sini çağırın

Aşağıdaki yeni yöntemi ' e ekleyin `MainWindow.xaml.cs` . Yöntemi, `GET` Yetkilendirme üst bilgisi kullanarak Graph API karşı bir istek yapmak için kullanılır:

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Korunan bir API 'ye karşı REST çağrısı yapma hakkında daha fazla bilgi

Bu örnek uygulamada, `GetHttpContentWithToken` `GET` bir belirteç gerektiren korumalı bir kaynağa karşı http isteği oluşturmak için yöntemini kullanın ve ardından içeriği çağırana geri döndürün. Bu yöntem, alınan belirteci HTTP yetkilendirme üst bilgisine ekler. Bu örnekte, kaynak, kullanıcının profil bilgilerini görüntüleyen Microsoft Graph API *Me* uç noktasıdır.

## <a name="add-a-method-to-sign-out-a-user"></a>Kullanıcı oturumu açmak için yöntem ekleme

Bir kullanıcının oturumunu kapatmak için dosyanıza aşağıdaki yöntemi ekleyin `MainWindow.xaml.cs` :

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>Kullanıcı oturumu kapatma hakkında daha fazla bilgi

`SignOutButton_Click`Yöntemi, msal Kullanıcı önbelleğinden kullanıcıları kaldırır, böylece bir belirteci almak için gelecekteki bir istek yalnızca etkileşimli hale getiriliyorsa başarılı olur.

Bu örnekteki uygulama tek kullanıcıları desteklediğinden, MSAL birden çok hesabın aynı anda imzalanabilmesine neden olan senaryoları destekler. Örneğin, bir kullanıcının birden fazla hesabı olan bir e-posta uygulaması.

## <a name="display-basic-token-information"></a>Temel belirteç bilgilerini görüntüle

Belirteç hakkındaki temel bilgileri göstermek için, *MainWindow. xaml. cs* dosyanıza aşağıdaki yöntemi ekleyin:

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>Daha fazla bilgi

Kullanıcı oturum açtıktan sonra, Microsoft Graph API 'sini çağırmak için kullanılan erişim belirtecine ek olarak, MSAL de bir KIMLIK belirteci edinir. Bu belirteç, kullanıcılarla ilgili olan küçük bir bilgi alt kümesini içerir. `DisplayBasicTokenInfo`Yöntemi, belirteçte bulunan temel bilgileri görüntüler. Örneğin, kullanıcının görünen adını ve KIMLIĞINI, ayrıca belirtecin sona erme tarihini ve erişim belirtecinin kendisini temsil eden dizeyi görüntüler. *Microsoft Graph API 'Yi çağır* düğmesini birden çok kez seçebilir ve sonraki istekler için aynı belirtecin yeniden kullanıldığından emin olabilirsiniz. MSAL, belirtecin yenilenmesi için zaman olduğuna karar verdiğinde, son kullanma tarihini genişletmekte olduğunu da görebilirsiniz.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

## <a name="next-steps"></a>Sonraki adımlar

Çok parçalı senaryo serimizde korumalı Web API 'Lerini çağıran masaüstü uygulamaları oluşturma hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Senaryo: Web API 'Lerini çağıran masaüstü uygulaması](scenario-desktop-overview.md)
