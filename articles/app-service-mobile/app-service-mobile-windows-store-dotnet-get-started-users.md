---
title: Evrensel Windows Platformu (UWP) uygulamanıza kimlik doğrulaması ekleme | Microsoft Docs
description: 'Azure App Service Mobile Apps kullanarak Evrensel Windows Platformu (UWP) uygulamanızın kullanıcılarının kimliğini doğrulamak için: AAD, Google, Facebook, Twitter ve Microsoft gibi çeşitli kimlik sağlayıcıları kullanarak kimlik doğrulaması yapmayı öğrenin.'
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: d5012ccc503e48785e23ff00564bbc9f6735eea8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388546"
---
# <a name="add-authentication-to-your-windows-app"></a>Windows uygulamanıza kimlik doğrulaması ekleme
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

## <a name="overview"></a>Genel Bakış
Bu konuda, mobil uygulamanıza bulut tabanlı kimlik doğrulaması ekleme konusu gösterilmektedir. Bu öğreticide, Azure App Service tarafından desteklenen bir kimlik sağlayıcısı kullanarak Mobile Apps için Evrensel Windows Platformu (UWP) hızlı başlangıç projesine kimlik doğrulaması eklersiniz. Mobil uygulama arka ucunuz tarafından başarıyla kimlik doğrulamasından ve yetkilendirdikten sonra, Kullanıcı KIMLIĞI değeri görüntülenir.

Bu öğretici Mobile Apps hızlı başlangıcı ' nı temel alır. İlk [olarak Mobile Apps kullanmaya başlama](app-service-mobile-windows-store-dotnet-get-started.md)öğreticisini doldurmanız gerekir.

## <a name="register"></a>Uygulamanızı kimlik doğrulaması için kaydedin ve App Service yapılandırın
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uygulamanızı Izin verilen dış yönlendirme URL 'Lerine ekleyin

Güvenli kimlik doğrulaması, uygulamanız için yeni bir URL şeması tanımlamanızı gerektirir. Bu, kimlik doğrulama işlemi tamamlandıktan sonra kimlik doğrulama sisteminin uygulamanıza yeniden yönlendirilmesini sağlar. Bu öğreticide, üzerinde URL şeması _uygulamamız_ kullanırız. Ancak, seçtiğiniz herhangi bir URL şemasını kullanabilirsiniz. Bu, mobil uygulamanız için benzersiz olmalıdır. Sunucu tarafında yeniden yönlendirmeyi etkinleştirmek için:

1. [Azure portal](https://ms.portal.azure.com)App Service seçin.

2. **Kimlik doğrulama/yetkilendirme** menü seçeneğine tıklayın.

3. **Izin verilen dış yeniden yönlendirme URL 'lerinde**`url_scheme_of_your_app://easyauth.callback` girin.  Bu dizedeki **url_scheme_of_your_app** , MOBIL uygulamanızın URL şemadır.  Bir protokol için normal URL belirtimini izlemelidir (yalnızca harfler ve rakamlar kullanın ve bir harfle başlar).  Mobil uygulama kodunuzu birkaç yerde URL düzeniyle ayarlamanız gerekeceğinden, seçtiğiniz dizeyi bir yere iade etmeniz gerekir.

4. **Kaydet** düğmesine tıklayın.

## <a name="permissions"></a>Kimliği doğrulanmış kullanıcılar için izinleri kısıtla
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Şimdi, arka ucunuza anonim erişimin devre dışı bırakıldığını doğrulayabilirsiniz. UWP uygulama projesi, başlangıç projesi olarak ayarlanmış şekilde, uygulamayı dağıtıp çalıştırın; uygulama başladıktan sonra 401 (yetkisiz) durum koduna sahip işlenmemiş bir özel durumun yapıldığını doğrulayın. Bunun nedeni, uygulamanın mobil uygulama kodunuza kimliği doğrulanmamış bir kullanıcı olarak erişmeye çalıştığı, ancak *TodoItem* tablosunun artık kimlik doğrulaması gerektirdiğinden meydana gelir.

Bundan sonra, App Service kaynakları isteyerek önce kullanıcıların kimliğini doğrulamak üzere uygulamayı güncelleşolursunuz.

## <a name="add-authentication"></a>Uygulamaya kimlik doğrulaması ekleme
1. UWP uygulama proje dosyasında MainPage.xaml.cs ve aşağıdaki kod parçacığını ekleyin:
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    Bu kod, kullanıcının kimliğini Facebook oturumu ile doğrular. Facebook dışında bir kimlik sağlayıcısı kullanıyorsanız, yukarıdaki **Mobileserviceauthenticationprovider** değerini sağlayıcınızın değerine değiştirin.
2. MainPage.xaml.cs içinde **OnNavigatedTo ()** metodunu değiştirin. Ardından, uygulamaya kimlik doğrulaması tetikleyen bir **oturum açma** düğmesi ekleyeceksiniz.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. Aşağıdaki kod parçacığını MainPage.xaml.cs ekleyin:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. MainPage. xaml proje dosyasını açın, **Kaydet** düğmesini tanımlayan öğeyi bulun ve aşağıdaki kodla değiştirin:
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. Aşağıdaki kod parçacığını App.xaml.cs ekleyin:

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Package. appxmanifest dosyasını açın, **Bildirimler**' e gidin, **kullanılabilir bildirimler** açılan listesinde **protokol** ' ü seçin ve düğme **Ekle** ' ye tıklayın. Şimdi **protokol** bildiriminin **özelliklerini** yapılandırın. **Görünen ad**' ta, uygulamanızın kullanıcılarına göstermek istediğiniz adı ekleyin. **Ad**alanına {url_scheme_of_your_app} uygulamanızı ekleyin.
7. Uygulamayı çalıştırmak için F5 tuşuna basın, **oturum aç** düğmesine tıklayın ve seçtiğiniz kimlik sağlayıcınızla birlikte uygulamada oturum açın. Oturum açma işlemi başarılı olduktan sonra, uygulama hatasız çalışır ve arka ucunuzu sorgulayabilir ve verilerde güncelleştirmeler yapabilirsiniz.

## <a name="tokens"></a>Kimlik doğrulama belirtecini istemcide depolayın
Önceki örnekte, istemcinin her başlatıldığında hem kimlik sağlayıcısı hem de App Service ile iletişim kurabilmesi gereken standart bir oturum açma gösterildi. Bu yöntem verimsiz değildir, kullanım ile ilgili konular, birçok müşteri aynı anda uygulamanızı başlatmaya çalışır. Daha iyi bir yaklaşım, App Service tarafından döndürülen yetkilendirme belirtecini önbelleğe almak ve sağlayıcı tabanlı bir oturum açma kullanmadan önce bunu kullanmayı denemenin bir yaklaşımdır.

> [!NOTE]
> İstemci tarafından yönetilen veya hizmet tarafından yönetilen kimlik doğrulaması kullanıyor olinizden bağımsız olarak, App Services tarafından verilen belirteci önbelleğe alabilirsiniz. Bu öğretici, hizmet tarafından yönetilen kimlik doğrulaması kullanır.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Sonraki adımlar
Bu temel kimlik doğrulama öğreticisini tamamladığınıza göre, aşağıdaki öğreticilerden birine devam etmeyi göz önünde bulundurun:

* [Uygulamanıza anında iletme bildirimleri ekleme](app-service-mobile-windows-store-dotnet-get-started-push.md)  
  Uygulamanıza anında iletme bildirimleri desteği eklemeyi ve anında iletme bildirimleri göndermek için Azure Notification Hubs’ı kullanmak üzere Mobile App arka ucunuzu yapılandırmayı öğrenin.
* [Uygulamanız için çevrimdışı eşitlemeyi etkinleştirme](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Mobil Uygulama arka ucu kullanarak uygulamanıza çevrimdışı destek eklemeyi öğrenin. Çevrimdışı eşitleme son kullanıcıların, ağ bağlantısı yokken dahi, mobil uygulama ile etkileşim kurmalarına &mdash;veri görüntüleme, ekleme ya da değiştirme&mdash; olanak tanır.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
