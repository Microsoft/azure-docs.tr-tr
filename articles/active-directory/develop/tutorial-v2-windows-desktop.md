---
title: Microsoft Identity Platform Windows Desktop 'ı kullanmaya başlama | Microsoft Docs
description: Bir Windows Masaüstü .NET (XAML) uygulaması, bir erişim belirteci alabilir ve Microsoft Identity platform tarafından korunan bir API 'YI çağırabilir.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f816091e3e8682069a950ff6f6eb839e285bb2f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512453"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows masaüstü uygulamasından Microsoft Graph API 'sini çağırma

Bu kılavuzda, yerel bir Windows Masaüstü .NET (XAML) uygulamasının Microsoft Graph API 'sini çağırmak için bir erişim belirteci nasıl kullandığı gösterilmektedir. Uygulama, geliştirici v 2.0 uç noktası için Microsoft Identity platformu 'ndan erişim belirteçleri gerektiren diğer API 'Lere de erişebilir. Bu platformun daha önce Azure AD olarak adlandırılmıştı.

Kılavuzu tamamladığınızda, uygulamanız kişisel hesapları (outlook.com, live.com ve diğerleri dahil) kullanan korumalı bir API 'yi çağırabilecektir. Uygulama Ayrıca, Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş ve okul hesapları da kullanacaktır.  

> [!NOTE]
> Rehber, Visual Studio 2015 güncelleştirme 3, Visual Studio 2017 veya Visual Studio 2019 gerektirir. Bu sürümlerden herhangi birine sahip değil misiniz? [Visual Studio 2019 ' ü ücretsiz indirin](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Bu kılavuz tarafından oluşturulan örnek uygulamanın nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Bu kılavuzla oluşturduğunuz örnek uygulama, Microsoft Graph API 'sini veya Microsoft Identity platform uç noktasından belirteçleri kabul eden bir Web API 'sini sorgulayan bir Windows masaüstü uygulaması sunar. Bu senaryo için, yetkilendirme üst bilgisi aracılığıyla HTTP isteklerine belirteç eklersiniz. Microsoft kimlik doğrulama kitaplığı (MSAL) belirteç alma ve yenileme işlemleri gerçekleştirir.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Korumalı Web API 'Lerine erişim için belirteç alımı işleme

Kullanıcının kimliği doğrulandıktan sonra örnek uygulama, Microsoft Graph API 'sini veya geliştiriciler için Microsoft Identity platform tarafından güvenliği sağlanmış bir Web API 'sini sorgulamak için kullanabileceğiniz bir belirteç alır.

Microsoft Graph gibi API 'Ler, belirli kaynaklara erişim izni vermek için bir belirteç gerektirir. Örneğin, bir kullanıcının profilini okumak, kullanıcının takvimine erişmek veya e-posta göndermek için bir belirteç gerekir. Uygulamanız, API kapsamları belirterek bu kaynaklara erişmek için MSAL kullanarak bir erişim belirteci isteyebilir. Bu erişim belirteci daha sonra, korunan kaynakta yapılan her çağrının HTTP yetkilendirme üstbilgisine eklenir.

MSAL, uygulamanızın ihtiyaç duymaması için, erişim belirteçlerini önbelleğe almayı ve yenilemeyi yönetir.

## <a name="nuget-packages"></a>NuGet paketleri

Bu kılavuzda aşağıdaki NuGet paketleri kullanılmaktadır:

|Kitaplık|Açıklama|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft kimlik doğrulama kitaplığı (MSAL.NET)|

## <a name="set-up-your-project"></a>Projenizi ayarlama

Bu bölümde, uygulamanın bir belirteç gerektiren Web API 'Lerini sorgulayabilmesi için bir Windows Masaüstü .NET uygulamasını (XAML) *Microsoft Ile oturum açma* ile tümleştirmeyi göstermek üzere yeni bir proje oluşturursunuz.

Bu kılavuzda oluşturduğunuz uygulama, bir grafiği çağırmak için kullanılan bir düğme, sonuçları ekranda göstermek için bir alan ve bir oturum kapatma düğmesi görüntüler.

> [!NOTE]
> Bunun yerine bu örneğin Visual Studio projesini indirmeyi tercih etmek istiyor musunuz? [Bir proje indirin](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)ve kod örneğini yürütmeden önce yapılandırmak için [yapılandırma adımına](#register-your-application) atlayın.
>

Uygulamanızı oluşturmak için aşağıdakileri yapın:

1. Visual Studio'da **dosya** > **yeni** > **proje**.
2. **Şablonlar**altında **görsel C#** ' i seçin.
3. Kullanmakta olduğunuz Visual Studio sürümünün sürümüne bağlı olarak **WPF uygulaması (.NET Framework)** seçeneğini belirleyin.

## <a name="add-msal-to-your-project"></a>Projenize MSAL ekleyin

1. Visual Studio’da **Araçlar** > **NuGet Paket Yöneticisi**> **Paket Yöneticisi Konsolu**’nu seçin.
2. Paket Yöneticisi konsolu penceresinde, aşağıdaki Azure PowerShell komutunu yapıştırın:

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE] 
    > Bu komut, Microsoft kimlik doğrulama kitaplığı 'nı kurar. MSAL, Azure Active Directory v 2.0 tarafından korunan API 'Lere erişmek için kullanılan Kullanıcı belirteçlerini alma, önbelleğe alma ve yenileme işlemleri gerçekleştirir
    >

## <a name="register-your-application"></a>Uygulamanızı kaydedin

Uygulamanızı iki şekilde kaydedebilirsiniz.

### <a name="option-1-express-mode"></a>Seçenek 1: Hızlı mod

Aşağıdakileri yaparak uygulamanızı hızlı bir şekilde kaydedebilirsiniz:
1. [Azure Portal-uygulama kaydına](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)gidin.
1. Uygulamanız için bir ad girin ve **Kaydet**'i seçin.
1. Yönergeleri izleyerek yeni uygulamanızı yalnızca tek tıklamayla indirin ve otomatik olarak yapılandırın.

### <a name="option-2-advanced-mode"></a>Seçenek 2: Gelişmiş mod

Uygulamanızı kaydetmek ve uygulama kayıt bilgilerinizi çözümünüze eklemek için aşağıdakileri yapın:
1. Bir iş veya okul hesabını ya da kişisel bir Microsoft hesabını kullanarak [Azure portalında](https://portal.azure.com) oturum açın.
1. Hesabınız size birden fazla Azure AD kiracısına erişim sunuyorsa sağ üst köşeden hesabınızı seçin ve portal oturumunuzu istediğiniz Azure AD kiracısına ayarlayın.
1. Geliştiriciler için Microsoft Identity platformu [uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin.
1. **Yeni kayıt**seçeneğini belirleyin.
   - **Ad** alanına uygulama kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin `Win-App-calling-MsGraph`.
   - **Desteklenen hesap türleri** bölümünde **Herhangi bir kuruluş dizinindeki hesaplar ve kişisel Microsoft hesapları (ör. Skype, Xbox, Outlook.com)** seçeneğini belirtin.
   - Uygulamayı kaydetmek için **Kaydet**'i seçin.
1. Uygulama sayfa listesinde **Kimlik doğrulaması**'nı seçin.
   1. Yeniden yönlendirme **URI** 'leri bölümünde, yeniden yönlendirme URI 'leri listesinde:
   1. **Tür** sütununda **ortak istemci (mobil & Masaüstü)** öğesini seçin.
   1. **Yeniden yönlendirme URI 'si** sütununda, şunu girin`urn:ietf:wg:oauth:2.0:oob`
1. **Kaydet**’i seçin.
1. Visual Studio 'ya gidin, *app.xaml.cs* dosyasını açın ve aşağıdaki kod parçacığında, `Enter_the_Application_Id_here` yeni kaydettiğiniz ve kopyaladığınız uygulama kimliğiyle değiştirin.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>MSAL başlatmak için kodu ekleyin

Bu adımda, belirteçleri işleme gibi MSAL ile etkileşimi işlemek için bir sınıf oluşturursunuz.

1. *App.xaml.cs* dosyasını açın ve msal başvurusunu sınıfa ekleyin:

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

Bir *MainWindow. xaml* dosyası otomatik olarak proje şablonunuzun bir parçası olarak oluşturulmalıdır. Bu dosyayı açın ve ardından uygulamanızın  *\<kılavuz >* düğümünü aşağıdaki kodla değiştirin:

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

1. *MainWindow.xaml.cs* dosyasında, msal için başvurusunu sınıfa ekleyin:

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. `MainWindow` Sınıf kodunu aşağıdaki kodla değiştirin:

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
    ```

<!--start-collapse-->
### <a name="more-information"></a>Daha fazla bilgi

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma

Yöntemi çağırmak `AcquireTokenInteractive` , kullanıcılardan oturum açmasını isteyen bir pencere ile sonuçlanır. Uygulamalar genellikle kullanıcıların, korunan kaynağa ilk kez erişmesi gerektiğinde etkileşimli olarak oturum açmasını gerektirir. Ayrıca, bir belirteci almak için sessiz bir işlem başarısız olduğunda (örneğin, bir kullanıcının parolasının süre dolduğunda) oturum açması gerekebilir.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

Yöntemi `AcquireTokenSilent` , Kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri gerçekleştirir. İlk `AcquireTokenInteractive` kez yürütüldükten sonra, `AcquireTokenSilent` sonraki çağrılar için korunan kaynaklara erişen belirteçleri elde etmek üzere kullanılan her zamanki yöntemdir, çünkü istek veya yenileme belirteçleri için çağrılar sessizce yapılır.

Sonuç olarak, `AcquireTokenSilent` yöntem başarısız olur. Hatanın nedeni, kullanıcının başka bir cihazda oturum açmış veya parolasını değiştirmiş olması olabilir. MSAL, sorunun etkileşimli bir eylem gerektirerek çözümlenemeyeceğini algıladığında, bir `MsalUiRequiredException` özel durum harekete geçirilir. Uygulamanız bu özel durumu iki şekilde işleyebilir:

* `AcquireTokenInteractive` Anında bir çağrı yapabilir. Bu çağrı kullanıcıdan oturum açmasını ister. Bu model genellikle kullanıcı için kullanılabilir çevrimdışı içerik bulunmayan çevrimiçi uygulamalarda kullanılır. Bu Kılavuzlu kurulum tarafından oluşturulan örnek bu düzeni izler ve bu, örneği ilk kez çalıştırdığınızda eylemde görebilirsiniz. 

* Hiçbir Kullanıcı uygulamayı kullanmadığından, `PublicClientApp.Users.FirstOrDefault()` null bir değer içerir ve bir `MsalUiRequiredException` özel durum oluşturulur. 

* Örnekteki kod daha sonra çağırarak `AcquireTokenInteractive`özel durumu işler ve bu, kullanıcıdan oturum açmasını ister.

* Bunun yerine, etkileşimli bir oturum açma işlemi için kullanıcılara, oturum açmak için doğru zamanı belirleyebilecekleri bir görsel gösterge sunabilir. Ya da uygulama daha sonra `AcquireTokenSilent` yeniden deneyebilir. Bu model, kullanıcıların kesintiye uğramadan diğer uygulama işlevlerini kullanabilmesi durumunda (örneğin, çevrimdışı içerik uygulamada kullanılabilir olduğunda) sık kullanılır. Bu durumda, kullanıcılar korunan kaynağa erişmek veya güncel olmayan bilgileri yenilemek için ne zaman oturum açmak istediğine karar verebilir. Alternatif olarak, uygulama geçici olarak devre dışı `AcquireTokenSilent` olduktan sonra ağ geri yüklendiğinde yeniden denemeye karar verebilir.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Yeni Edindiğiniz belirteci kullanarak Microsoft Graph API 'sini çağırın

Aşağıdaki yeni yöntemi ' e `MainWindow.xaml.cs`ekleyin. Yöntemi, yetkilendirme üst bilgisi kullanarak Graph API `GET` karşı bir istek yapmak için kullanılır:

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

<!--start-collapse-->
### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>Korunan bir API 'ye karşı REST çağrısı yapma hakkında daha fazla bilgi

Bu örnek uygulamada, bir belirteç gerektiren korumalı `GetHttpContentWithToken` bir kaynağa karşı http `GET` isteği oluşturmak için yöntemini kullanın ve ardından içeriği çağırana geri döndürün. Bu yöntem, alınan belirteci HTTP yetkilendirme üst bilgisine ekler. Bu örnekte, kaynak, kullanıcının profil bilgilerini görüntüleyen Microsoft Graph API *Me* uç noktasıdır.
<!--end-collapse-->

## <a name="add-a-method-to-sign-out-a-user"></a>Kullanıcı oturumu açmak için yöntem ekleme

Bir kullanıcının oturumunu kapatmak için `MainWindow.xaml.cs` dosyanıza aşağıdaki yöntemi ekleyin:

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

<!--start-collapse-->
### <a name="more-information-about-user-sign-out"></a>Kullanıcı oturumu kapatma hakkında daha fazla bilgi

`SignOutButton_Click` Yöntemi, msal Kullanıcı önbelleğinden kullanıcıları kaldırır, böylece bir belirteci almak için gelecekteki bir istek yalnızca etkileşimli hale getiriliyorsa başarılı olur.

Bu örnekteki uygulama tek kullanıcıları desteklediğinden, MSAL birden çok hesabın aynı anda imzalanabilmesine neden olan senaryoları destekler. Örneğin, bir kullanıcının birden fazla hesabı olan bir e-posta uygulaması.
<!--end-collapse-->

## <a name="display-basic-token-information"></a>Temel belirteç bilgilerini görüntüle

Belirteç hakkındaki temel bilgileri göstermek için, *MainWindow.xaml.cs* dosyanıza aşağıdaki yöntemi ekleyin:

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

<!--start-collapse-->
### <a name="more-information"></a>Daha fazla bilgi

Kullanıcı oturum açtıktan sonra, Microsoft Graph API 'sini çağırmak için kullanılan erişim belirtecine ek olarak, MSAL de bir KIMLIK belirteci edinir. Bu belirteç, kullanıcılarla ilgili olan küçük bir bilgi alt kümesini içerir. `DisplayBasicTokenInfo` Yöntemi, belirteçte bulunan temel bilgileri görüntüler. Örneğin, kullanıcının görünen adını ve KIMLIĞINI, ayrıca belirtecin sona erme tarihini ve erişim belirtecinin kendisini temsil eden dizeyi görüntüler. *Microsoft Graph API 'Yi çağır* düğmesini birden çok kez seçebilir ve sonraki istekler için aynı belirtecin yeniden kullanıldığından emin olabilirsiniz. MSAL, belirtecin yenilenmesi için zaman olduğuna karar verdiğinde, son kullanma tarihini genişletmekte olduğunu da görebilirsiniz.
<!--end-collapse-->

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin.

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
