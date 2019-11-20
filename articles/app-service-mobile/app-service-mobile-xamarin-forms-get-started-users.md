---
title: Xamarin Forms uygulamasında Mobile Apps için kimlik doğrulamaya başlama | Microsoft Docs
description: AAD, Google, Facebook, Twitter ve Microsoft gibi çeşitli kimlik sağlayıcıları aracılığıyla Xamarin Forms uygulamanızın kullanıcılarının kimliğini doğrulamak için Mobile Apps nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d9bd698535b09ecb5c484eefcbe31228eb99e04f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388401"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Xamarin Forms uygulamanıza kimlik doğrulaması ekleme
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu konu, istemci uygulamanızdan bir App Service mobil uygulama kullanıcılarının kimliğini nasıl doğrulayacağınızı gösterir. Bu öğreticide, App Service tarafından desteklenen bir kimlik sağlayıcısı kullanarak Xamarin Forms hızlı başlangıç projesine kimlik doğrulaması eklersiniz. Mobil uygulamanız tarafından başarıyla kimlik doğrulamasından ve yetkilendirdikten sonra, Kullanıcı KIMLIĞI değeri görüntülenir ve kısıtlı tablo verilerine erişebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar
Bu öğreticide en iyi sonuç için, önce [Xamarin Forms uygulaması oluşturma][1] öğreticisini tamamlamanızı öneririz. Bu Öğreticiyi tamamladıktan sonra, çok platformlu TodoList uygulaması olan bir Xamarin Forms projenize sahip olursunuz.

İndirilen hızlı başlangıç sunucusu projesini kullanmazsanız, kimlik doğrulama uzantısı paketini projenize eklemeniz gerekir. Sunucu Uzantısı paketleri hakkında daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma][2].

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Uygulamanızı kimlik doğrulaması için kaydetme ve uygulama hizmetlerini yapılandırma
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uygulamanızı Izin verilen dış yönlendirme URL 'Lerine ekleyin

Güvenli kimlik doğrulaması, uygulamanız için yeni bir URL şeması tanımlamanızı gerektirir. Bu, kimlik doğrulama işlemi tamamlandıktan sonra kimlik doğrulama sisteminin uygulamanıza yeniden yönlendirilmesini sağlar. Bu öğreticide, üzerinde URL şeması _uygulamamız_ kullanırız. Ancak, seçtiğiniz herhangi bir URL şemasını kullanabilirsiniz. Bu, mobil uygulamanız için benzersiz olmalıdır. Sunucu tarafında yeniden yönlendirmeyi etkinleştirmek için:

1. [Azure portal][8]App Service seçin.

2. **Kimlik doğrulama/yetkilendirme** menü seçeneğine tıklayın.

3. **Izin verilen dış yeniden yönlendirme URL 'lerinde**`url_scheme_of_your_app://easyauth.callback` girin.  Bu dizedeki **url_scheme_of_your_app** , MOBIL uygulamanızın URL şemadır.  Bir protokol için normal URL belirtimini izlemelidir (yalnızca harfler ve rakamlar kullanın ve bir harfle başlar).  Mobil uygulama kodunuzu birkaç yerde URL düzeniyle ayarlamanız gerekeceğinden, seçtiğiniz dizeyi bir yere iade etmeniz gerekir.

4. **Tamam**’a tıklayın.

5. **Kaydet** düğmesine tıklayın.

## <a name="restrict-permissions-to-authenticated-users"></a>Kimliği doğrulanmış kullanıcılar için izinleri kısıtla
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>Taşınabilir sınıf kitaplığına kimlik doğrulaması ekleme
Mobile Apps, bir kullanıcının App Service kimlik doğrulamasıyla oturum açması için [MobileServiceClient][4] üzerinde [loginasync][3] genişletme yöntemini kullanır. Bu örnek, sağlayıcının oturum açma arabirimini uygulamada görüntüleyen sunucu tarafından yönetilen bir kimlik doğrulama akışı kullanır. Daha fazla bilgi için bkz. [sunucu tarafından yönetilen kimlik doğrulaması][5]. Üretim uygulamanızda daha iyi bir kullanıcı deneyimi sağlamak için, [istemci tarafından yönetilen kimlik doğrulamasını][6]kullanmak yerine göz önünde bulundurmanız gerekir.

Xamarin Forms projesiyle kimlik doğrulaması yapmak için, uygulama için taşınabilir sınıf kitaplığında bir **IAuthenticate** arabirimi tanımlayın. Ardından, taşınabilir sınıf kitaplığında tanımlanan Kullanıcı arabirimine, kimlik doğrulamaya başlamak için tıkladıysanız bir **oturum açma** düğmesi ekleyin. Başarılı bir kimlik doğrulamasından sonra, veriler mobil uygulama arka ucuna yüklenir.

Uygulamanız tarafından desteklenen her platform için **IAuthenticate** arabirimini uygulayın.

1. Visual Studio veya Xamarin Studio içinde, taşınabilir sınıf kitaplığı projesi olan ad içinde **Taşınabilir** bir App.cs açın ve aşağıdaki `using` ifadesini ekleyin:

        using System.Threading.Tasks;
2. App.cs ' de, aşağıdaki `IAuthenticate` arabirimi tanımını `App` sınıf tanımından hemen önce ekleyin.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. Arabirimi platforma özgü bir uygulamayla başlatmak için aşağıdaki statik üyeleri **uygulama** sınıfına ekleyin.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. Taşınabilir sınıf kitaplığı projesinden TodoList. xaml ' i açın, varolan düğmeden sonra *ButtonsPanel* düzen öğesine aşağıdaki **düğme** öğesini ekleyin:

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    Bu düğme, mobil uygulama arka ucunuz ile sunucu tarafından yönetilen kimlik doğrulamasını tetikler.
5. Taşınabilir sınıf kitaplığı projesinden TodoList.xaml.cs öğesini açın, sonra `TodoList` sınıfına aşağıdaki alanı ekleyin:

        // Track whether the user has authenticated.
        bool authenticated = false;
6. **Ongörünme** yöntemini aşağıdaki kodla değiştirin:

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    Bu kod, verilerin kimlik doğrulamasından sonra yalnızca hizmetten yenilenmesini sağlar.
7. **Tıklanan** olay için aşağıdaki işleyiciyi **ToDoList** sınıfına ekleyin:

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. Değişikliklerinizi kaydedin ve taşınabilir sınıf kitaplığı projesini yeniden oluşturun ve hata doğrulama işlemini doğrulayın.

## <a name="add-authentication-to-the-android-app"></a>Android uygulamasına kimlik doğrulaması ekleme
Bu bölümde, Android uygulama projesinde **IAuthenticate** arabiriminin nasıl uygulanacağı gösterilmektedir. Android cihazlarını destekliyorsanız, bu bölümü atlayın.

1. Visual Studio veya Xamarin Studio ' de, **DROID** projesine sağ tıklayın ve ardından **Başlangıç projesi olarak ayarlayın**.
2. F5 tuşuna basarak projeyi hata ayıklayıcıda başlatın ve uygulama başladıktan sonra 401 (yetkisiz) durum koduna sahip işlenmemiş bir özel durumun yapıldığını doğrulayın. 401 kodu üretilir çünkü arka uçtaki erişim yalnızca yetkili kullanıcılarla kısıtlıdır.
3. Android projesinde MainActivity.cs ' i açın ve aşağıdaki `using` deyimlerini ekleyin:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aşağıdaki gibi, **MainActivity** sınıfını, **IAuthenticate** arabirimini uygulayacak şekilde güncelleştirin:

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. Bir **Mobileserviceuser** alanı ve bir **kimlik doğrulama** yöntemi ekleyerek, aşağıdaki gibi, **MainActivity** sınıfını güncelleştirin:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, [Mobileserviceauthenticationprovider][7]için farklı bir değer seçin.

6. @No__t-1 öğesinin içine aşağıdaki XML ekleyerek **AndroidManifest. xml** dosyasını güncelleştirin:

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    @No__t-0 ' yı URL şemanızın yerine koyun.
7. @No__t-2 ' a çağrıdan önce **MainActivity** sınıfının **OnCreate** yöntemine aşağıdaki kodu ekleyin:

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    Bu kod, uygulama yüklenmeden önce Doğrulayıcı 'nın başlatılmasını sağlar.
8. Uygulamayı yeniden oluşturun, çalıştırın ve seçtiğiniz kimlik doğrulama sağlayıcısıyla oturum açın ve kimliği doğrulanmış bir kullanıcı olarak verilere erişebildiğinizi doğrulayın.

### <a name="troubleshooting"></a>Sorun giderme

**Uygulama `Java.Lang.NoSuchMethodError: No static method startActivity` ile kilitlendi**

Bazı durumlarda, destek paketlerindeki çakışmalar yalnızca Visual Studio 'da bir uyarı olarak gösterilir, ancak uygulama çalışma zamanında bu özel durumla kilitleniyor. Bu durumda, projenizde başvurulan tüm destek paketlerinin aynı sürüme sahip olduğundan emin olmanız gerekir. [Azure Mobile Apps NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/), Android platformu için `Xamarin.Android.Support.CustomTabs` bağımlılığına sahiptir, yani projeniz daha yeni destek paketleri kullanıyorsa, çakışmaları önlemek için doğrudan gerekli sürüme sahip bu paketi yüklemeniz gerekir.

## <a name="add-authentication-to-the-ios-app"></a>İOS uygulamasına kimlik doğrulaması ekleme
Bu bölüm, iOS uygulama projesinde **IAuthenticate** arabiriminin nasıl uygulanacağını gösterir. İOS cihazlarını destekliyorsanız, bu bölümü atlayın.

1. Visual Studio veya Xamarin Studio içinde **iOS** projesine sağ tıklayın ve ardından **Başlangıç projesi olarak ayarlayın**.
2. F5 tuşuna basarak projeyi hata ayıklayıcıda başlatın ve uygulama başladıktan sonra 401 (yetkisiz) durum koduna sahip işlenmemiş bir özel durumun yapıldığını doğrulayın. Arka uçta erişim yalnızca yetkili kullanıcılarla kısıtlandığından 401 yanıtı üretilir.
3. İOS projesinde AppDelegate.cs ' i açın ve aşağıdaki `using` deyimlerini ekleyin:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. Aşağıdaki gibi, **IAuthenticate** arabirimini uygulamak Için **appdelegate** sınıfını güncelleştirin:

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. Bir **Mobileserviceuser** alanı ve bir kimlik doğrulama yöntemi ekleyerek, aşağıdaki gibi **IAuthenticate** arabirimi Için gereken bir **kimlik doğrulama** yöntemi ekleyerek **appdelegate** sınıfını güncelleştirin:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertController avAlert = UIAlertController.Create("Sign-in result", message, UIAlertControllerStyle.Alert);
            avAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
            UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(avAlert, true, null);

            return success;
        }

    Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, [MobileServiceAuthenticationProvider] için farklı bir değer seçin.
    
6. **OpenURL** yöntemi aşırı yüklemesini aşağıdaki gibi ekleyerek **appdelegate** sınıfını güncelleştirin:

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. @No__t-1 ' e çağrıdan önce aşağıdaki kod satırını **Sonlandırhedbaşlatılıyor** yöntemine ekleyin:

        App.Init(this);

    Bu kod, uygulama yüklenmeden önce Doğrulayıcı 'nın başlatılmasını sağlar.

8. Info. plist açın ve bir **URL türü**ekleyin. **Tanımlayıcıyı** seçtiğiniz bir ada, **URL şemalarına** uygulamanızın URL şemasına ve **None olarak ayarlayın** .

9. Uygulamayı yeniden oluşturun, çalıştırın ve seçtiğiniz kimlik doğrulama sağlayıcısıyla oturum açın ve kimliği doğrulanmış bir kullanıcı olarak verilere erişebildiğinizi doğrulayın.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Windows 10 (Phone dahil) uygulama projeleri için kimlik doğrulaması ekleme
Bu bölüm, Windows 10 uygulama projelerinde **IAuthenticate** arabiriminin nasıl uygulanacağını gösterir. Evrensel Windows Platformu (UWP) projeleri için aynı adımlar geçerlidir, ancak **UWP** projesi (belirtilen değişikliklerle birlikte) kullanılarak. Windows cihazlarını destekliyorsanız, bu bölümü atlayın.

1. Visual Studio 'da **UWP** projesine sağ tıklayın ve ardından **Başlangıç projesi olarak ayarlayın**.
2. F5 tuşuna basarak projeyi hata ayıklayıcıda başlatın ve uygulama başladıktan sonra 401 (yetkisiz) durum koduna sahip işlenmemiş bir özel durumun yapıldığını doğrulayın. 401 yanıtı, arka uçtaki erişim yalnızca yetkili kullanıcılarla kısıtlandığından oluşur.
3. Windows uygulama projesi için MainPage.xaml.cs açın ve aşağıdaki `using` deyimlerini ekleyin:

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    @No__t-0 ' yı taşınabilir sınıf kitaplığınızın ad alanıyla değiştirin.
4. **MainPage** sınıfını aşağıdaki gibi **IAuthenticate** arabirimini uygulayacak şekilde güncelleştirin:

        public sealed partial class MainPage : IAuthenticate
5. Bir **Mobileserviceuser** alanı ve bir **kimlik doğrulama** yöntemi ekleyerek **MainPage** sınıfını aşağıdaki şekilde güncelleştirin:

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, [Mobileserviceauthenticationprovider][7]için farklı bir değer seçin.

1. @No__t-1 ' e çağrıdan önce **MainPage** sınıfının oluşturucusuna aşağıdaki kod satırını ekleyin:

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    @No__t-0 ' yı taşınabilir sınıf kitaplığınızın ad alanıyla değiştirin.

3. **UWP**kullanıyorsanız, aşağıdaki **OnActivated** yöntemi geçersiz kılmayı **uygulama** sınıfına ekleyin:

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Package. appxmanifest ' i açın ve bir **protokol** bildirimi ekleyin. **Görünen adı** , seçtiğiniz bir ad ve uygulamanızın URL **şeması olarak ayarlayın** .

4. Uygulamayı yeniden oluşturun, çalıştırın ve seçtiğiniz kimlik doğrulama sağlayıcısıyla oturum açın ve kimliği doğrulanmış bir kullanıcı olarak verilere erişebildiğinizi doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar
Bu temel kimlik doğrulama öğreticisini tamamladığınıza göre, aşağıdaki öğreticilerden birine devam etmeyi göz önünde bulundurun:

* [Uygulamanıza anında iletme bildirimleri ekleme](app-service-mobile-xamarin-forms-get-started-push.md)

  Uygulamanıza anında iletme bildirimleri desteği eklemeyi ve anında iletme bildirimleri göndermek için Azure Notification Hubs’ı kullanmak üzere Mobile App arka ucunuzu yapılandırmayı öğrenin.
* [Uygulamanız için çevrimdışı eşitlemeyi etkinleştirme](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  Mobil Uygulama arka ucu kullanarak uygulamanıza çevrimdışı destek eklemeyi öğrenin. Çevrimdışı eşitleme, son kullanıcıların bir mobil uygulamayla etkileşime geçmesini sağlar; ağ bağlantısı olmadığında bile veri görüntüleme, ekleme veya değiştirme.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
