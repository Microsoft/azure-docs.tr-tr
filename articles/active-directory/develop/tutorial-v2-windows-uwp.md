---
title: 'Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir Evrensel Windows Platformu (UWP) uygulaması oluşturma | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıların oturum açması için Microsoft Identity platformunu kullanan bir UWP uygulaması derleyebilir ve Microsoft Graph API 'sini adına çağırmak için bir erişim belirteci alacaksınız.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: 6383f63d2118d8618f07bf3cb6cd08a0b16140f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102657"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Öğretici: Evrensel Windows Platformu (UWP) uygulamasından Microsoft Graph API 'sini çağırma

Bu öğreticide, kullanıcıları oturum açan ve Microsoft Graph API 'sini çağırmak için bir erişim belirteci alan bir yerel Evrensel Windows Platformu (UWP) uygulaması oluşturacaksınız. 

Bu kılavuzun sonunda, uygulamanız kişisel hesapları kullanarak korumalı bir API çağırır. Örnekler şunlardır outlook.com, live.com ve diğerleri. Uygulamanız Ayrıca, Azure Active Directory (Azure AD) olan herhangi bir şirketten veya kuruluştan iş ve okul hesaplarını da çağırır.

Bu öğreticide:

> [!div class="checklist"]
> * Visual Studio 'da *Evrensel Windows platformu (UWP)* projesi oluşturma
> * Uygulamayı Azure portal kaydetme
> * Kullanıcı oturum açma ve oturum kapatma desteği için kod ekleme
> * Microsoft Graph API 'sini çağırmak için kod ekleme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) [Evrensel Windows platformu geliştirme](/windows/uwp/get-started/get-set-up) iş yükü yüklendi

## <a name="how-this-guide-works"></a>Bu kılavuz nasıl çalışır?

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Bu kılavuz Microsoft Graph API 'sini sorgulayan bir örnek UWP uygulaması oluşturur. Bu senaryo için, yetkilendirme üst bilgisi kullanılarak HTTP isteklerine bir belirteç eklenir. Microsoft kimlik doğrulama kitaplığı, belirteç alma ve yenileme işlemleri gerçekleştirir.

## <a name="nuget-packages"></a>NuGet paketleri

Bu kılavuz aşağıdaki NuGet paketini kullanır:

|Kitaplık|Description|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)| Microsoft Authentication Library|
|[Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph Istemci kitaplığı|

## <a name="set-up-your-project"></a>Projenizi ayarlama

Bu bölümde, Microsoft ile oturum açma ile bir Windows Masaüstü .NET uygulaması 'nı (XAML) bütünleştirmek için adım adım yönergeler sağlanmaktadır. Ardından uygulama, Microsoft Graph API gibi bir belirteç gerektiren Web API 'Lerini sorgulayabilir.

Bu kılavuz, Microsoft Graph API 'sini sorgulayan ve oturumu kapatmak için bir düğme görüntüleyen bir uygulama oluşturur. Ayrıca, çağrıların sonuçlarını içeren metin kutularını da görüntüler.

> [!Tip]
> Bu öğreticide oluşturduğunuz projenin tamamlanmış bir sürümünü görmek için [GitHub 'dan indirebilirsiniz](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip).

### <a name="create-your-application"></a>Uygulamanızı oluşturma

1. Visual Studio 'yu açın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, C# için **boş uygulama (Evrensel Windows)** öğesini seçin ve **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** bölümünde uygulamayı adlandırın ve **Oluştur**' u seçin.
1. İstenirse, **yeni Evrensel Windows platformu projesinde**, **hedef** ve **En düşük** sürümler Için herhangi bir sürüm seçin ve **Tamam**' ı seçin.

   ![En düşük ve hedef sürümler](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-the-microsoft-authentication-library-to-your-project"></a>Projenize Microsoft kimlik doğrulama kitaplığı ekleme

1. Visual Studio 'da **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu**' nu seçin.
1. Aşağıdaki komutları Kopyala ve **Paket Yöneticisi konsol** penceresinde Yapıştır:

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > İlk komut, [Microsoft kimlik doğrulama kitaplığı 'nı (msal.net)](https://aka.ms/msal-net)yüklüyor. MSAL.NET Microsoft Identity platformu tarafından korunan API 'Lere erişen Kullanıcı belirteçlerini alır, önbelleğe alır ve yeniler. İkinci komut, Microsoft Graph istekleri doğrulamak ve hizmete çağrı yapmak için [Microsoft Graph .net Istemci kitaplığı](https://github.com/microsoftgraph/msgraph-sdk-dotnet) 'nı kurar.

### <a name="create-your-applications-ui"></a>Uygulamanızın Kullanıcı arabirimini oluşturma

Visual Studio, proje şablonunuzun bir parçası olarak *MainPage. xaml* oluşturur. Bu dosyayı açın ve ardından uygulamanızın **kılavuz** düğümünü aşağıdaki kodla değiştirin:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-the-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft Graph API 'sinin bir belirtecini almak için Microsoft kimlik doğrulama kitaplığı 'nı kullanın

Bu bölümde, Microsoft Graph API 'sinin bir belirtecini almak için Microsoft kimlik doğrulama kitaplığı 'nın nasıl kullanılacağı gösterilmektedir. *MainPage. xaml. cs* dosyasında değişiklik yapın.

1. *MainPage. xaml. cs* dosyasında aşağıdaki başvuruları ekleyin:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. `MainPage`Sınıfınızı aşağıdaki kodla değiştirin:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Etkileşimli olarak kullanıcı belirteci alma<a name="more-information"></a>

`AcquireTokenInteractive`Yöntemi, kullanıcılardan oturum açmasını isteyen bir pencere ile sonuçlanır. Uygulamalar genellikle kullanıcıların korumalı bir kaynağa ilk kez erişmesi için kullanıcıların etkileşimli olarak oturum açmasını gerektirir. Ayrıca, bir belirteci almak için sessiz bir işlem başarısız olduğunda oturum açması gerekebilir. Bir Kullanıcı parolasının süresi dolduğunda örnek bir örnektir.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

`AcquireTokenSilent`Yöntemi, Kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri gerçekleştirir. `AcquireTokenInteractive`İlk kez çalıştıktan sonra kullanıcıdan kimlik bilgilerini girmesini isterse, `AcquireTokenSilent` daha sonra çağrılar için belirteçleri istemek üzere yöntemini kullanın. Bu yöntem belirteçleri sessizce devralır. Microsoft kimlik doğrulama kitaplığı, belirteç önbelleğini ve yenilemeyi işler.

Sonuç olarak, `AcquireTokenSilent` Yöntem başarısız olur. Hatanın nedeni, başka bir cihazda parolasını imzalayan veya değiştiren bir Kullanıcı içerir. Microsoft kimlik doğrulama kitaplığı, sorunun etkileşimli bir eylem gerektirdiğini algıladığında, bir `MsalUiRequiredException` özel durum oluşturur. Uygulamanız bu özel durumu iki şekilde işleyebilir:

* Uygulamanız hemen çağırır `AcquireTokenInteractive` . Bu çağrı kullanıcıdan oturum açmasını ister. Normalde, Kullanıcı için kullanılabilir çevrimdışı içerik bulunmayan çevrimiçi uygulamalar için bu yaklaşımı kullanın. Bu Kılavuzlu kurulum tarafından oluşturulan örnek, düzeni izler. Örneği ilk kez çalıştırdığınızda eylem olarak görürsünüz.

   Hiçbir Kullanıcı uygulamayı kullanmadığından, `accounts.FirstOrDefault()` null bir değer içerdiğinden bir `MsalUiRequiredException` özel durum oluşturur.

   Daha sonra örnekteki kod, çağırarak özel durumu işler `AcquireTokenInteractive` . Bu çağrı kullanıcıdan oturum açmasını ister.

* Uygulamanız kullanıcıların oturum açması için gereken görsel bir bildirim sunar. Ardından, oturum açmak için doğru zamanı seçebilirsiniz. Uygulama `AcquireTokenSilent` daha sonra yeniden deneyebilir. Kullanıcılar başka uygulama işlevlerini kesintiye uğramadan kullanbiliyor olduğunda bu yaklaşımı kullanın. Uygulamada çevrimdışı içerik kullanılabilir olduğunda örnek bir örnektir. Bu durumda, kullanıcılar oturum açmak istedikleri zaman karar verebilir. `AcquireTokenSilent`Ağ geçici olarak devre dışı olduktan sonra uygulama yeniden deneyebilir.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>SignInUserAndGetTokenUsingMSAL yönteminden belirteci alarak Microsoft Graph hizmet Istemcisinin örneğini oluşturun

*MainPage. xaml. cs*' ye aşağıdaki yeni yöntemi ekleyin:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Korunan bir API 'ye karşı REST çağrısı yapma hakkında daha fazla bilgi

Bu örnek uygulamada, `GetGraphServiceClient` yöntemi `GraphServiceClient` bir erişim belirteci kullanarak örneği oluşturur. Ardından, `GraphServiceClient` kullanıcının profil bilgilerini **Me** uç noktasından almak için kullanılır.

### <a name="add-a-method-to-sign-out-the-user"></a>Kullanıcının oturumunu kapatmak için bir yöntem ekleyin

Kullanıcının oturumunu kapatmak için *MainPage. xaml. cs*' ye aşağıdaki yöntemi ekleyin:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

MSAL.NET, belirteçleri almak veya hesapları işlemek için zaman uyumsuz yöntemler kullanır. Bu nedenle, UI iş parçacığında Kullanıcı Arabirimi eylemlerini destekler. Bu, `Dispatcher.RunAsync` çağrının ve çağrı önleminden dolayı nedenidir `ConfigureAwait(false)` .

#### <a name="more-information-about-signing-out"></a>Oturumu kapatma hakkında daha fazla bilgi<a name="more-information-on-sign-out"></a>

`SignOutButton_Click`Yöntemi, kullanıcıyı Microsoft kimlik doğrulama kitaplığı Kullanıcı önbelleğinden kaldırır. Bu yöntem, Microsoft kimlik doğrulama kitaplığı 'nın geçerli kullanıcıyı unutmasını etkin bir şekilde söyler. Bir belirteci almak için gelecekteki bir istek, yalnızca etkileşimli olması durumunda başarılı olur.

Bu örnekteki uygulama, tek bir kullanıcıyı destekler. Microsoft kimlik doğrulama kitaplığı, kullanıcının birden fazla hesapta oturum açıp geçirebileceği senaryoları destekler. Örneğin, bir kullanıcının birkaç hesabı bulunan bir e-posta uygulaması.

### <a name="display-basic-token-information"></a>Temel belirteç bilgilerini görüntüle

Aşağıdaki yöntemi *MainPage. xaml. cs* ' ye ekleyerek belirteç hakkındaki temel bilgileri görüntüleyebilirsiniz:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Daha fazla bilgi<a name="more-information-1"></a>

**OpenID Connect** KULLANıLARAK alınan kimlik belirteçleri, kullanıcıya yönelik bir dizi bilginin küçük bir alt kümesini de içerir. `DisplayBasicTokenInfo` belirteçte bulunan temel bilgileri görüntüler. Bu bilgiler kullanıcının görünen adını ve KIMLIĞINI içerir. Ayrıca, belirtecin sona erme tarihini ve erişim belirtecinin kendisini temsil eden dizeyi içerir. **MICROSOFT Graph API** düğmesini birkaç kez çağır ' ı seçerseniz, sonraki istekler için aynı belirtecin yeniden kullanılmış olduğunu görürsünüz. Ayrıca, Microsoft kimlik doğrulama kitaplığı, belirtecin yenilenmesi için zaman karar verdiğinde genişletilmiş süre sonu tarihini de görebilirsiniz.

### <a name="display-message"></a>İleti görüntüle

*MainPage. xaml. cs*' ye aşağıdaki yeni yöntemi ekleyin:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Uygulamanızı kaydetme

Şimdi, uygulamanızı kaydedin:

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin (örneğin,) `UWP-App-calling-MSGraph` . Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** altında **herhangi bir kuruluş dizininde (HERHANGI bir Azure ad dizini-çoklu kiracı) ve kişisel Microsoft hesaplarından (örn. Skype, Xbox) hesaplar**' ı seçin. 
1. **Kaydet**’i seçin.
1. Genel Bakış sayfasında, **uygulama (istemci) kimlik** değerini bulun ve kopyalayın. Visual Studio 'ya geri dönün, *MainPage. xaml. cs*' yi açın ve değerini `ClientId` bu değerle değiştirin.

Uygulamanız için kimlik doğrulamasını yapılandırın:

1. <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>geri döndüğünüzde, **Yönet** altında **kimlik doğrulama**  >  **Platform Ekle**' yi seçin ve ardından **mobil ve Masaüstü uygulamaları**' nı seçin.
1. **Yeniden yönlendirme URI 'leri** bölümünde, girin `https://login.microsoftonline.com/common/oauth2/nativeclient` .
1. **Yapılandır**'ı seçin.

Uygulamanız için API izinlerini yapılandırın:

1. **Yönet** altında **API izinleri**  >  **bir izin Ekle**' yi seçin.
1. **Microsoft Graph** seçin.
1. **Temsilci izinleri**' ni seçin, *Kullanıcı. okuma*' yı arayın ve **Kullanıcı. Read** ' in seçildiğini doğrulayın.
1. Herhangi bir değişiklik yaptıysanız, bunları kaydetmek için **Izinleri Ekle** ' yi seçin.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Federasyon etki alanlarında tümleşik kimlik doğrulamasını etkinleştirme (isteğe bağlı)

Tümleşik Windows kimlik doğrulamasını federe bir Azure AD etki alanı ile kullanıldığında etkinleştirmek için, uygulama bildiriminin ek özellikleri etkinleştirmesi gerekir. Visual Studio 'da uygulamanıza geri dönün.

1. *Package. appxmanifest* öğesini açın.
1. **Özellikleri** seçin ve aşağıdaki ayarları etkinleştirin:

   * **Kurumsal kimlik doğrulama**
   * **Özel ağlar (Istemci & sunucusu)**
   * **Paylaşılan Kullanıcı Sertifikaları**

> [!IMPORTANT]
> [Tümleşik Windows kimlik doğrulaması](https://aka.ms/msal-net-iwa) Bu örnek için varsayılan olarak yapılandırılmamıştır. İstek `Enterprise Authentication` veya özellik isteyen uygulamalar `Shared User Certificates` , Windows Mağazası tarafından daha yüksek bir doğrulama düzeyi gerektirir. Ayrıca, tüm geliştiriciler daha yüksek doğrulama düzeyini gerçekleştirmek istemekdedir. Bu ayarı yalnızca, Federe bir Azure AD etki alanı ile tümleşik Windows kimlik doğrulaması gerekiyorsa etkinleştirin.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>WithDefaultRedirectURI () kullanımına alternatif yaklaşım

Geçerli örnekte, `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` yöntemi kullanılır. Kullanmak için `WithDefaultRedirectURI()` şu adımları uygulayın:

1. *MainPage. xaml. cs* dosyasında şunu `WithRedirectUri` ile değiştirin `WithDefaultRedirectUri` :

   **Geçerli kod**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Güncelleştirilmiş kod**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  `redirectURI` *MainPage. xaml. cs* içindeki alanı ekleyerek ve üzerinde bir kesme noktası ayarlayarak uygulamanız IÇIN geri çağırma URI 'sini bulun:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Uygulamayı çalıştırın ve ardından `redirectUri` kesme noktası isabet edildiğinde değerini kopyalayın. Değer aşağıdaki değere benzer bir şekilde görünmelidir: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    Daha sonra, değeri getirmek için yalnızca bir kez gerekli olduğundan kod satırını kaldırabilirsiniz.

3. Uygulama kayıt Portalı ' nda, döndürülen değeri **kimlik doğrulama** bölmesinde **redirecturi** ' ye ekleyin.

## <a name="test-your-code"></a>Kodunuza test etme

Uygulamanızı test etmek için **F5** tuşunu seçerek projenizi Visual Studio 'da çalıştırın. Ana pencereniz görüntülenir:

![Uygulamanın kullanıcı arabirimi](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Sınamaya hazırsanız **MICROSOFT Graph API çağrısı**' nı seçin. Ardından, oturum açmak için bir Azure AD kuruluş hesabı veya live.com veya outlook.com gibi bir Microsoft hesabı kullanın. Kullanıcı bu testi ilk kez çalıştırdığında, uygulama kullanıcıdan oturum açmasını isteyen bir pencere görüntüler.

### <a name="consent"></a>Onay

Uygulamanızda ilk kez oturum açtığınızda aşağıdaki görüntüye benzer bir onay ekranı görünür. Erişime açık olarak izin vermek için **Evet** ' i seçin:

![Erişim izni ekranı](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Beklenen sonuçlar

**API çağrı sonuçları** EKRANıNDA Microsoft Graph API çağrısı tarafından döndürülen Kullanıcı profili bilgilerini görürsünüz:

![API çağrı sonuçları ekranı](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Ayrıca, `AcquireTokenInteractive` `AcquireTokenSilent` **belirteç bilgisi** kutusunda veya aracılığıyla alınan belirteçle ilgili temel bilgileri görürsünüz:

|Özellik  |Biçimlendir  |Açıklama |
|---------|---------|---------|
|`Username` |`user@domain.com` |Kullanıcıyı tanımlayan Kullanıcı adı.|
|`Token Expires` |`DateTime` |Belirtecin süresinin dolacağı zaman. Microsoft kimlik doğrulama kitaplığı, belirteci gerektiği şekilde yenileyerek sona erme tarihini genişletir.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Kapsamlar ve temsilci izinleri hakkında daha fazla bilgi

Microsoft Graph API 'SI, `user.read` kapsamın bir kullanıcının profilini okumasını gerektirir. Bu kapsam, uygulama kayıt portalı 'nda kayıtlı olan her uygulamada varsayılan olarak eklenir. Arka uç sunucunuz için Microsoft Graph ve özel API 'Lere yönelik diğer API 'Ler için ek kapsamlar gerekebilir. Örneğin, Microsoft Graph API 'sinin `Calendars.Read` kullanıcının takvimlerini listeme kapsamını belirtmesini gerektirir.

Kullanıcının takvimlerine bir uygulama bağlamında erişmek için, `Calendars.Read` temsilci iznini uygulama kayıt bilgilerine ekleyin. Ardından `Calendars.Read` kapsamı `acquireTokenSilent` çağrıya ekleyin.

Kapsam sayısını artırdıkça kullanıcılara ek yarışlar istenebilir.

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="issue-1"></a>Sorun 1

Uygulamanızda bir federasyon Azure AD etki alanında oturum açtığınızda aşağıdaki hata iletilerinden birini alırsınız:

* "İstekte geçerli istemci sertifikası bulunamadı."
* "Kullanıcının sertifika deposunda geçerli sertifika bulunamadı."
* "Farklı bir kimlik doğrulama yöntemi seçmeyi yeniden deneyin."

**Neden:** Kuruluş ve sertifika özellikleri etkin değil.

**Çözüm:** [Federasyon etki alanlarında tümleşik kimlik doğrulamasını etkinleştirme (isteğe bağlı)](#enable-integrated-authentication-on-federated-domains-optional)bölümündeki adımları izleyin.

### <a name="issue-2"></a>Sorun 2

[Federasyon etki alanlarında tümleşik kimlik doğrulamasını](#enable-integrated-authentication-on-federated-domains-optional) etkinleştirir ve çok faktörlü kimlik doğrulaması yapılandırılmış bir ortamda oturum açmak için Windows 10 bilgisayarında Windows Hello 'yu kullanmayı deneyin. Sertifika listesi görüntülenir. PIN 'inizi kullanmayı seçerseniz, PIN penceresi hiçbir şekilde görünmez.

**Neden:** Bu sorun, Windows 10 masaüstleri üzerinde çalışan UWP uygulamalarında Web kimlik doğrulama aracısının bilinen bir sınırlamasıdır. Windows 10 Mobile üzerinde sorunsuz bir şekilde çalışıyor.

**Geçici çözüm:** **Diğer seçeneklerle oturum aç '** ı seçin. Ardından **Kullanıcı adı ve parolayla oturum aç '** ı seçin. **Parolanızı belirtin**' i seçin. Ardından telefon kimlik doğrulama sürecini gözden geçin.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

.NET uygulamalarında yetkilendirme ve kimlik doğrulama için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanma hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Microsoft kimlik doğrulama kitaplığı 'na genel bakış (MSAL)](msal-overview.md)
