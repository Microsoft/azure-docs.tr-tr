---
title: Xamarin iOS 'ta Mobile Apps kimlik doğrulaması ile çalışmaya başlama
description: AAD, Google, Facebook, Twitter ve Microsoft gibi çeşitli kimlik sağlayıcıları aracılığıyla Xamarin iOS uygulamanızın kullanıcılarının kimliğini doğrulamak için Mobile Apps nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 859c2d4cc1c2be7b4e96a955e78dc0339875c96f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388334"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Xamarin.iOS uygulamanıza kimlik doğrulaması ekleyin
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış

Bu konu, istemci uygulamanızdan bir App Service mobil uygulama kullanıcılarının kimliğini nasıl doğrulayacağınızı gösterir. Bu öğreticide, App Service tarafından desteklenen bir kimlik sağlayıcısı kullanarak Xamarin. iOS hızlı başlangıç projesine kimlik doğrulaması eklersiniz. Mobil uygulamanız tarafından başarıyla kimlik doğrulamasından ve yetkilendirdikten sonra, Kullanıcı KIMLIĞI değeri görüntülenir ve kısıtlı tablo verilerine erişebilirsiniz.

Önce [Xamarin. iOS uygulaması oluşturma]öğreticisini doldurmanız gerekir. İndirilen hızlı başlangıç sunucusu projesini kullanmazsanız, kimlik doğrulama uzantısı paketini projenize eklemeniz gerekir. Sunucu Uzantısı paketleri hakkında daha fazla bilgi için bkz. [Azure için .net arka uç sunucu SDK 'sı Mobile Apps çalışma](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>Uygulamanızı kimlik doğrulaması için kaydetme ve uygulama hizmetlerini yapılandırma
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>Uygulamanızı Izin verilen dış yönlendirme URL 'Lerine ekleyin

Güvenli kimlik doğrulaması, uygulamanız için yeni bir URL şeması tanımlamanızı gerektirir. Bu, kimlik doğrulama işlemi tamamlandıktan sonra kimlik doğrulama sisteminin uygulamanıza yeniden yönlendirilmesini sağlar. Bu öğreticide, üzerinde URL şeması _uygulamamız_ kullanırız. Ancak, seçtiğiniz herhangi bir URL şemasını kullanabilirsiniz. Bu, mobil uygulamanız için benzersiz olmalıdır. Sunucu tarafında yeniden yönlendirmeyi etkinleştirmek için:

1. [Azure portal](https://portal.azure.com/)App Service seçin.

2. **Kimlik doğrulama/yetkilendirme** menü seçeneğine tıklayın.

3. **Izin verilen dış yeniden yönlendirme URL 'lerinde**`url_scheme_of_your_app://easyauth.callback` girin.  Bu dizedeki **url_scheme_of_your_app** , MOBIL uygulamanızın URL şemadır.  Bir protokol için normal URL belirtimini izlemelidir (yalnızca harfler ve rakamlar kullanın ve bir harfle başlar).  Mobil uygulama kodunuzu birkaç yerde URL düzeniyle ayarlamanız gerekeceğinden, seçtiğiniz dizeyi bir yere iade etmeniz gerekir.

4. **Tamam**’a tıklayın.

5. **Kaydet** düğmesine tıklayın.

## <a name="restrict-permissions-to-authenticated-users"></a>Kimliği doğrulanmış kullanıcılar için izinleri kısıtla
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Visual Studio veya Xamarin Studio, istemci projesini bir cihazda veya Öykünücüde çalıştırın. Uygulama başladıktan sonra 401 (yetkisiz) durum koduna sahip işlenmemiş bir özel durumun yapıldığını doğrulayın. Hata, hata ayıklayıcının konsoluna kaydedilir. Bu nedenle, Visual Studio 'da hatayı çıkış penceresinde görmeniz gerekir.

    Bu yetkisiz bir hata, uygulamanın mobil uygulama arka ucunuza kimliği doğrulanmamış bir kullanıcı olarak erişmeye çalıştığı için oluşur. *TodoItem* tablosu artık kimlik doğrulaması gerektiriyor.

Daha sonra, istemci uygulamasını, kimliği doğrulanmış bir kullanıcıyla mobil uygulama arka ucuna kaynak isteyecek şekilde güncelleşolursunuz.

## <a name="add-authentication-to-the-app"></a>Uygulamaya kimlik doğrulaması ekleme
Bu bölümde, verileri görüntülemeden önce uygulamayı bir oturum açma ekranı görüntüleyecek şekilde değiştirirsiniz. Uygulama başlatıldığında, App Service bağlanmayacak ve herhangi bir veri görüntülemecektir. Kullanıcı yenileme hareketini ilk kez gerçekleştirdikten sonra oturum açma ekranı görünür; başarılı oturum açma işleminden sonra Todo öğeleri listesi görüntülenir.

1. İstemci projesinde, **QSTodoService.cs** dosyasını açın ve aşağıdaki using ifadesini ve `MobileServiceUser` değerini QSTodoService sınıfına ekleyin:

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. Aşağıdaki tanım ile **Qstodoservice** 'e **kimlik doğrulaması** adlı yeni bir yöntem ekleyin:

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, yukarıda **Loginasync** değerine geçirilen değeri şunlardan birine değiştirin: _MicrosoftAccount_, _Twitter_, _Google_veya _windowsazureactivedirectory_.

3. **QSTodoListViewController.cs**'i açın. **ViewDidLoad** 'ın yöntem tanımını değiştirme **RefreshAsync ()** çağrısının sonuna kadar olan çağrısını kaldırma:

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. **Kullanıcı** özelliği null ise kimlik doğrulaması yapmak için **RefreshAsync** metodunu değiştirin. Aşağıdaki kodu yöntem tanımının üst kısmına ekleyin:

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. **AppDelegate.cs**'i açın, aşağıdaki yöntemi ekleyin:

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. **Info. plist** dosyasını açın, **Gelişmiş** bölümünde **URL türleri** ' ne gidin. Şimdi URL 'nizin **tanımlayıcısını** ve **URL DÜZENLERINI** yapılandırın ve **URL türü Ekle**' ye tıklayın. **URL şemaları** {url_scheme_of_your_app} ile aynı olmalıdır.
7. Visual Studio 'da Mac ana bilgisayarınıza veya Mac için Visual Studio bağlı olarak, bir cihazı veya öykünücüyü hedefleyen istemci projesini çalıştırın. Uygulamanın veri görüntülediğini doğrulayın.

    Öğe listesini çekerek yenileme hareketini gerçekleştirin ve bu, oturum açma ekranının görünmesine neden olur. Geçerli kimlik bilgilerini başarıyla girdikten sonra uygulama Todo öğelerinin listesini görüntüler ve verilerde güncelleştirmeler yapabilirsiniz.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin. iOS uygulaması oluşturma]: app-service-mobile-xamarin-ios-get-started.md
