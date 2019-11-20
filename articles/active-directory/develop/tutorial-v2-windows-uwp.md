---
title: Microsoft Identity platform UWP Başlarken | Mavisi
description: Evrensel Windows Platformu uygulamalar (UWP), Microsoft Identity platform uç noktası tarafından erişim belirteçleri gerektiren bir API 'YI nasıl çağırabilirler.
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
ms.date: 09/24/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0cafc439a24c10c4c5a678219a0e0dce84476ff
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71290846"
---
# <a name="call-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>Evrensel Windows Platformu uygulamasından Microsoft Graph API çağrısı (XAML)

> [!div renderon="docs"]

Bu kılavuzda, yerel bir Evrensel Windows Platformu (UWP) uygulamasının bir erişim belirteci isteme yöntemi açıklanmaktadır. Uygulama daha sonra Microsoft Graph API 'sini çağırır. Kılavuz, Microsoft Identity platform uç noktasından erişim belirteçleri gerektiren diğer API 'Ler için de geçerlidir.

Bu kılavuzun sonunda, uygulamanız kişisel hesapları kullanarak korumalı bir API çağırır. Örnekler şunlardır outlook.com, live.com ve diğerleri. Uygulamanız Ayrıca, Azure Active Directory (Azure AD) olan herhangi bir şirketten veya kuruluştan iş ve okul hesaplarını da çağırır.

>[!NOTE]
> Bu kılavuzda Evrensel Windows Platformu geliştirme yüklü Visual Studio gerekir. Evrensel Windows Platformu uygulamalar geliştirmek için Visual Studio 'Yu indirme ve yapılandırma [yönergeleri bölümüne bakın](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) .

## <a name="how-this-guide-works"></a>Bu kılavuz nasıl çalışır?

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

Bu kılavuz Microsoft Graph API 'YI sorgulayan bir örnek UWP uygulaması oluşturur. Bu senaryo için, yetkilendirme üst bilgisi kullanılarak HTTP isteklerine bir belirteç eklenir. Microsoft kimlik doğrulama kitaplığı (MSAL) belirteç alma ve yenileme işlemleri gerçekleştirir.

## <a name="nuget-packages"></a>NuGet paketleri

Bu kılavuz aşağıdaki NuGet paketini kullanır:

|Kitaplık|Açıklama|
|---|---|
|[Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|

## <a name="set-up-your-project"></a>Projenizi ayarlama

Bu bölümde, Microsoft ile oturum açma ile bir Windows Masaüstü .NET uygulaması 'nı (XAML) bütünleştirmek için adım adım yönergeler sağlanmaktadır. Ardından uygulama, Microsoft Graph API gibi bir belirteç gerektiren Web API 'Lerini sorgulayabilir.

Bu kılavuz, Graph API sorgulayan bir düğme ve oturumu kapatmak için bir düğme görüntüleyen bir uygulama oluşturur. Ayrıca, çağrıların sonuçlarını içeren metin kutularını da görüntüler.

> [!NOTE]
> Bu örnek Visual Studio projesini oluşturmak yerine indirmek istiyor musunuz? Kod örneğini çalıştırmadan önce yapılandırmak için [bir proje indirin](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip) ve [uygulama kaydı](#register-your-application "auygulama kayıt adımı ") adımına atlayın.

### <a name="create-your-application"></a>Uygulamanızı oluşturma

1. Visual Studio 'Yu açın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur**' da, Için C# boş uygulama (Evrensel Windows) öğesini seçin ve **İleri ' yi**seçin.
1. **Yeni projenizi yapılandırın**bölümünde uygulamayı adlandırın ve **Oluştur**' u seçin.
1. İstenirse, **yeni Evrensel Windows platformu projesinde**, **hedef** ve **En düşük** sürümler Için herhangi bir sürüm seçin ve **Tamam**' ı seçin.

   ![En düşük ve hedef sürümler](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Projenize Microsoft kimlik doğrulama Kitaplığı ekleyin

1. Visual Studio’da **Araçlar** > **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu**’nu seçin.
1. Aşağıdaki komutu kopyalayıp **Paket Yöneticisi konsol** penceresine yapıştırın:

    ```powershell
    Install-Package Microsoft.Identity.Client
    ```

   > [!NOTE]
   > Bu komut, [Microsoft kimlik doğrulama kitaplığı](https://aka.ms/msal-net)'nı kurar. MSAL Microsoft Identity platform tarafından korunan API 'Lere erişen Kullanıcı belirteçlerini alır, önbelleğe alır ve yeniler.

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

### <a name="use-msal-to-get-a-token-for-microsoft-graph-api"></a>Microsoft Graph API belirteci almak için MSAL kullanma

Bu bölümde, Microsoft Graph API 'sinin bir belirtecini almak için MSAL nasıl kullanılacağı gösterilmektedir. *MainPage.xaml.cs* dosyasında değişiklik yapın.

1. *MainPage.xaml.cs*' de, aşağıdaki başvuruları ekleyin:

    ```csharp
    using Microsoft.Identity.Client;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. `MainPage` Sınıfınızı aşağıdaki kodla değiştirin:

    ```csharp
    public sealed partial class MainPage : Page
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information. 
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant with the information about the accounts allowed to sign in in your application:
        //   - for Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use organizations
        //   - for any Work or School accounts, or Microsoft personal account, use common
        //   - for Microsoft Personal account, use consumers
        private const string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";        

        public IPublicClientApplication PublicClientApp { get; } 

        public MainPage()
        {
          this.InitializeComponent();

          PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount)
                .WithLogging((level, message, containsPii) =>
                {
                    Debug.WriteLine($"MSAL: {level} {message} ");
                }, LogLevel.Warning, enablePiiLogging:false,enableDefaultPlatformLogging:true)
                .WithUseCorporateNetwork(true)
                .Build();
        }

        /// <summary>
        /// Call AcquireTokenInteractive - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
         AuthenticationResult authResult = null;
         ResultText.Text = string.Empty;
         TokenInfoText.Text = string.Empty;

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
          // A MsalUiRequiredException happened on AcquireTokenSilent.
          // This indicates you need to call AcquireTokenInteractive to acquire a token
          System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

          try
          {
           authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                      .ExecuteAsync()
                                                      .ConfigureAwait(false);
           }
           catch (MsalException msalex)
           {
            await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalex}");
           }
          }
          catch (Exception ex)
          {
           await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
           return;
          }

          if (authResult != null)
          {
           var content = await GetHttpContentWithToken(graphAPIEndpoint,
                                                       authResult.AccessToken).ConfigureAwait(false);

           // Go back to the UI thread to make changes to the UI
           await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
           {
            ResultText.Text = content;
            DisplayBasicTokenInfo(authResult);
            this.SignOutButton.Visibility = Visibility.Visible;
           });
          }
        }
    }
    ```

#### Kullanıcı belirtecini etkileşimli olarak alın<a name="more-information"></a>

Yöntemi `AcquireTokenInteractive` , kullanıcılardan oturum açmasını isteyen bir pencere ile sonuçlanır. Uygulamalar genellikle kullanıcıların korumalı bir kaynağa ilk kez erişmesi için kullanıcıların etkileşimli olarak oturum açmasını gerektirir. Ayrıca, bir belirteci almak için sessiz bir işlem başarısız olduğunda oturum açması gerekebilir. Bir Kullanıcı parolasının süresi dolduğunda örnek bir örnektir.

#### <a name="get-a-user-token-silently"></a>Kullanıcı belirtecini sessizce alma

Yöntemi `AcquireTokenSilent` , Kullanıcı etkileşimi olmadan belirteç alma ve yenileme işlemleri gerçekleştirir. İlk `AcquireTokenInteractive` kez çalıştıktan sonra kullanıcıdan kimlik bilgilerini girmesini isterse, daha sonra çağrılar için belirteçleri `AcquireTokenSilent` istemek üzere yöntemini kullanın. Bu yöntem belirteçleri sessizce devralır. MSAL, belirteç önbelleğini ve yenilemeyi işler.

Sonuç olarak, `AcquireTokenSilent` yöntem başarısız olur. Hatanın nedeni, başka bir cihazda parolasını imzalayan veya değiştiren bir Kullanıcı içerir. MSAL, sorunun etkileşimli bir eylem gerektirdiğini algıladığında, bir `MsalUiRequiredException` özel durum oluşturur. Uygulamanız bu özel durumu iki şekilde işleyebilir:

* Uygulamanız hemen çağırır `AcquireTokenInteractive` . Bu çağrı kullanıcıdan oturum açmasını ister. Normalde, Kullanıcı için kullanılabilir çevrimdışı içerik bulunmayan çevrimiçi uygulamalar için bu yaklaşımı kullanın. Bu Kılavuzlu kurulum tarafından oluşturulan örnek, düzeni izler. Örneği ilk kez çalıştırdığınızda eylem olarak görürsünüz.

   Hiçbir Kullanıcı uygulamayı kullanmadığından, `accounts.FirstOrDefault()` null bir değer içerdiğinden bir `MsalUiRequiredException` özel durum oluşturur.

   Daha sonra örnekteki kod, çağırarak `AcquireTokenInteractive`özel durumu işler. Bu çağrı kullanıcıdan oturum açmasını ister.

* Uygulamanız kullanıcıların oturum açması için gereken görsel bir bildirim sunar. Ardından, oturum açmak için doğru zamanı seçebilirsiniz. Uygulama daha sonra yeniden `AcquireTokenSilent` deneyebilir. Kullanıcılar başka uygulama işlevlerini kesintiye uğramadan kullanbiliyor olduğunda bu yaklaşımı kullanın. Uygulamada çevrimdışı içerik kullanılabilir olduğunda örnek bir örnektir. Bu durumda, kullanıcılar oturum açmak istedikleri zaman karar verebilir. Ağ geçici olarak devre `AcquireTokenSilent` dışı olduktan sonra uygulama yeniden deneyebilir.

### <a name="call-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>Yeni Edindiğiniz belirteci kullanarak Microsoft Graph API çağrısı yapın

Aşağıdaki yeni yöntemi *MainPage.xaml.cs*öğesine ekleyin:

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
           // Add the token in Authorization header
           request.Headers.Authorization = 
             new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

 Bu yöntem, bir `GET` `Authorization` üst bilgi kullanarak Graph API bir istek yapar.

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Korunan bir API 'ye karşı REST çağrısı yapma hakkında daha fazla bilgi

Bu örnek uygulamada `GetHttpContentWithToken` yöntemi, bir belirteç gerektiren korumalı bir `GET` kaynağa karşı http isteği yapar. Sonra yöntem, içeriği çağırana döndürür. Bu yöntem, alınan belirteci **http yetkilendirme** üst bilgisine ekler. Bu örnekte, kaynak, kullanıcının profil bilgilerini görüntüleyen Microsoft Graph API **Me** uç noktasıdır.

### <a name="add-a-method-to-sign-out-the-user"></a>Kullanıcının oturumunu kapatmak için bir yöntem ekleyin

Kullanıcının oturumunu kapatmak için, *MainPage.xaml.cs*'e aşağıdaki yöntemi ekleyin:

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

> [!NOTE]
> MSAL.NET, belirteçleri almak veya hesapları işlemek için zaman uyumsuz yöntemler kullanır. UI iş parçacığında Kullanıcı Arabirimi eylemlerini desteklemeniz gerekir. Bu, `Dispatcher.RunAsync` çağrının ve çağrı `ConfigureAwait(false)`önleminden dolayı nedenidir.

#### Oturumu kapatma hakkında daha fazla bilgi<a name="more-information-on-sign-out"></a>

`SignOutButton_Click` Yöntemi, kullanıcıyı msal Kullanıcı önbelleğinden kaldırır. Bu yöntem, MSAL 'in geçerli kullanıcıyı unutmasını etkin bir şekilde söyler. Bir belirteci almak için gelecekteki bir istek, yalnızca etkileşimli olması durumunda başarılı olur.

Bu örnekteki uygulama, tek bir kullanıcıyı destekler. MSAL, kullanıcının birden fazla hesapta oturum açıp geçirebileceği senaryoları destekler. Örneğin, bir kullanıcının birkaç hesabı bulunan bir e-posta uygulaması.

### <a name="display-basic-token-information"></a>Temel belirteç bilgilerini görüntüle

Belirteç hakkındaki temel bilgileri göstermek için *MainPage.xaml.cs* 'e aşağıdaki yöntemi ekleyin:

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

#### Daha fazla bilgi<a name="more-information-1"></a>

**OpenID Connect** KULLANıLARAK alınan kimlik belirteçleri, kullanıcıya yönelik bir dizi bilginin küçük bir alt kümesini de içerir. `DisplayBasicTokenInfo`belirteçte bulunan temel bilgileri görüntüler. Bu bilgiler kullanıcının görünen adını ve KIMLIĞINI içerir. Ayrıca, belirtecin sona erme tarihini ve erişim belirtecinin kendisini temsil eden dizeyi içerir. **MICROSOFT Graph API** düğmesini birkaç kez çağır ' ı seçerseniz, sonraki istekler için aynı belirtecin yeniden kullanılmış olduğunu görürsünüz. Ayrıca MSAL, belirtecin yenileme zamanına karar verdiğinde, süre sonu genişletilmiş tarihini de görebilirsiniz.

### <a name="display-message"></a>İleti görüntüle

Aşağıdaki yeni yöntemi *MainPage.xaml.cs*öğesine ekleyin:

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

## <a name="register-your-application"></a>Uygulamanızı kaydedin

Şimdi uygulamanızı kaydetmeniz gerekir:

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Seçin **Azure Active Directory** > **uygulama kayıtları**.
1. **Yeni kayıt**seçeneğini belirleyin. Uygulamanın kullanıcılarına gösterilecek anlamlı bir uygulama adı girin, örneğin *UWP-app-çağıran-MSGraph*.
1. **Desteklenen hesap türleri**altında, **herhangi bir kurumsal dizin ve kişisel Microsoft hesabında (örn. Skype, Xbox) hesaplar**' ı seçin ve ardından devam etmek için **Kaydet** ' i seçin.
1. Genel Bakış sayfasında, **uygulama (istemci) kimlik** değerini bulun ve kopyalayın. Visual Studio 'ya geri dönün, *MainPage.xaml.cs*açın ve değerini `ClientId` bu değerle değiştirin.

Uygulamanız için kimlik doğrulamasını yapılandırın:

1. [Azure Portal](https://portal.azure.com)geri döndüğünüzde, **Yönet**altında **kimlik doğrulaması**' nı seçin.
1. **Yeniden yönlendirme URI 'leri** listesinde, **türü**için **ortak istemci (mobil & Masaüstü)** öğesini seçin ve `urn:ietf:wg:oauth:2.0:oob` **yeniden yönlendirme URI 'si**için girin.
1. **Kaydet**’i seçin.

Uygulamanız için API izinlerini yapılandırın:

1. **Yönet**altında **API izinleri**' ni seçin.
1. **Izin Ekle** ' yi seçin ve ardından **Microsoft API 'leri**seçtiğinizden emin olun.
1. **Microsoft Graph**seçin.
1. **Temsilci izinleri**seçin, Kullanıcı araması yapın *. oku* ve **Kullanıcı. Read** 'in seçili olduğunu doğrulayın.
1. Herhangi bir değişiklik yaptıysanız, bunları kaydetmek için **Izinleri Ekle** ' yi seçin.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Federasyon etki alanlarında tümleşik kimlik doğrulamasını etkinleştirme (isteğe bağlı)

Windows ile tümleşik kimlik doğrulamasını, Federe bir Azure AD etki alanı ile kullanıldığında etkinleştirmek için, uygulama bildiriminin ek özellikleri etkinleştirmesi gerekir. Visual Studio 'da uygulamanıza geri dönün.

1. *Package. appxmanifest*öğesini açın.
1. **Özellikleri** seçin ve aşağıdaki ayarları etkinleştirin:

   * **Kurumsal kimlik doğrulama**
   * **Özel ağlar (Istemci & sunucusu)**
   * **Paylaşılan Kullanıcı sertifikaları**

> [!IMPORTANT]
> [Tümleşik Windows kimlik doğrulaması](https://aka.ms/msal-net-iwa) Bu örnek için varsayılan olarak yapılandırılmamıştır. İstek `Enterprise Authentication` veya`Shared User Certificates` Özellik isteyen uygulamalar, Windows Mağazası tarafından daha yüksek bir doğrulama düzeyi gerektirir. Ayrıca, tüm geliştiriciler daha yüksek doğrulama düzeyini gerçekleştirmek istemekdedir. Bu ayarı yalnızca, Federe bir Azure AD etki alanı ile Windows tümleşik kimlik doğrulaması gerekiyorsa etkinleştirin.

## <a name="test-your-code"></a>Kodunuzu test etme

Uygulamanızı test etmek için F5 ' i seçerek projenizi Visual Studio 'da çalıştırın. Ana pencereniz görüntülenir:

![Uygulamanın kullanıcı arabirimi](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Sınamaya hazırsanız **MICROSOFT Graph API çağrısı**' nı seçin. Ardından, oturum açmak için bir Azure AD kuruluş hesabı veya live.com veya outlook.com gibi bir Microsoft hesabı kullanın. Kullanıcı bu uygulamayı ilk kez çalıştırdığında, uygulama kullanıcıdan oturum açmasını isteyen bir pencere görüntüler.

### <a name="consent"></a>Onayla

Uygulamanızda ilk kez oturum açtığınızda aşağıdakine benzer bir izin ekranı sunulur. Erişime açık olarak izin vermek için **Evet** ' i seçin:

![Erişim izni ekranı](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Beklenen sonuçlar

**API çağrı sonuçları** EKRANıNDA Microsoft Graph API çağrısı tarafından döndürülen Kullanıcı profili bilgilerini görürsünüz:

![API çağrı sonuçları ekranı](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

Ayrıca, `AcquireTokenInteractive` **belirteç bilgisi** kutusunda veya `AcquireTokenSilent` aracılığıyla alınan belirteçle ilgili temel bilgileri görürsünüz:

|Özellik  |Biçimi  |Açıklama |
|---------|---------|---------|
|`Username` |`user@domain.com` |Kullanıcıyı tanımlayan Kullanıcı adı.|
|`Token Expires` |`DateTime` |Belirtecin süresinin dolacağı zaman. MSAL, belirteci gerektiği şekilde yenileyerek sona erme tarihini genişletir.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Kapsamlar ve temsilci izinleri hakkında daha fazla bilgi

Microsoft Graph API 'si, `user.read` kapsamın bir kullanıcının profilini okumasını gerektirir. Bu kapsam, uygulama kayıt portalı 'nda kayıtlı olan her uygulamada varsayılan olarak eklenir. Arka uç sunucunuz için Microsoft Graph ve özel API 'Lere yönelik diğer API 'Ler için ek kapsamlar gerekebilir. Örneğin, Microsoft Graph API 'sinin kullanıcının takvimlerini `Calendars.Read` listeme kapsamını belirtmesini gerektirir.

Kullanıcının takvimlerine bir uygulama bağlamında erişmek için, `Calendars.Read` temsilci iznini uygulama kayıt bilgilerine ekleyin. Ardından `Calendars.Read` kapsamı`acquireTokenSilent` çağrıya ekleyin.

> [!NOTE]
> Kapsam sayısını artırdıkça kullanıcılara ek yarışlar istenebilir.

## <a name="known-issues"></a>Bilinen sorunlar

### <a name="issue-1"></a>Sorun 1

Uygulamanızda bir federasyon Azure AD etki alanında oturum açtığınızda aşağıdaki hata iletilerinden birini alırsınız:

* İstekte geçerli bir istemci sertifikası bulunamadı.
* Kullanıcının sertifika deposunda geçerli sertifika bulunamadı.
* Farklı bir kimlik doğrulama yöntemi seçmeyi yeniden deneyin.

Neden: Kuruluş ve sertifika özellikleri etkin değil.

Çözümden [Federasyon etki alanlarında tümleşik kimlik doğrulamasını etkinleştirme (isteğe bağlı)](#enable-integrated-authentication-on-federated-domains-optional)bölümündeki adımları izleyin.

### <a name="issue-2"></a>Sorun 2

[Federasyon etki alanlarında tümleşik kimlik doğrulamasını](#enable-integrated-authentication-on-federated-domains-optional) etkinleştirir ve Multi-Factor Authentication yapılandırılmış bir ortamda oturum açmak için Windows 10 bilgisayarında Windows Hello 'yu kullanmayı deneyin. Sertifika listesi sunulur. Ancak PIN 'inizi kullanmayı seçerseniz, PIN penceresi hiçbir şekilde sunulmaz.

Neden: Bu sorun, Windows 10 Masaüstü 'nde çalışan UWP uygulamalarında Web kimlik doğrulama aracısının bilinen bir sınırlamasıdır. Windows 10 Mobile üzerinde sorunsuz bir şekilde çalışıyor.

Geçici çözüm: **Diğer seçeneklerle oturum aç '** ı seçin. Ardından **Kullanıcı adı ve parolayla oturum aç '** ı seçin. **Parolanızı belirtin**' i seçin. Ardından telefon kimlik doğrulama sürecini gözden geçin.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin:

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
