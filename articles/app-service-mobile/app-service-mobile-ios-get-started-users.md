---
title: Azure Mobile Apps ile iOS üzerinde kimlik doğrulaması ekleme
description: AAD, Google, Facebook, Twitter ve Microsoft gibi çeşitli kimlik sağlayıcıları aracılığıyla iOS uygulamanızın kullanıcılarının kimliğini doğrulamak için Azure Mobile Apps 'yi nasıl kullanacağınızı öğrenin.
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 800d86750f091404ee7f940d7cf8f6631e3fbbeb
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388688"
---
# <a name="add-authentication-to-your-ios-app"></a>İOS uygulamanıza kimlik doğrulaması ekleme
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

Bu öğreticide, desteklenen bir kimlik sağlayıcısı kullanarak [iOS hızlı başlangıç] projesine kimlik doğrulaması eklersiniz. Bu öğretici, önce gerçekleştirmeniz gereken [iOS hızlı başlangıç] öğreticisini temel alır.

## <a name="register"></a>Uygulamanızı kimlik doğrulaması için kaydedin ve App Service yapılandırın
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Uygulamanızı Izin verilen dış yönlendirme URL 'Lerine ekleyin

Güvenli kimlik doğrulaması, uygulamanız için yeni bir URL şeması tanımlamanızı gerektirir.  Bu, kimlik doğrulama işlemi tamamlandıktan sonra kimlik doğrulama sisteminin uygulamanıza yeniden yönlendirilmesini sağlar.  Bu öğreticide, üzerinde URL şeması _uygulamamız_ kullanırız.  Ancak, seçtiğiniz herhangi bir URL şemasını kullanabilirsiniz.  Bu, mobil uygulamanız için benzersiz olmalıdır.  Sunucu tarafında yeniden yönlendirmeyi etkinleştirmek için:

1. [Azure portalda]App Service seçin.

2. **Kimlik doğrulama/yetkilendirme** menü seçeneğine tıklayın.

3. **Kimlik doğrulama sağlayıcıları** bölümünde **Azure Active Directory** ' ye tıklayın.

4. **Yönetim modunu** **Gelişmiş**olarak ayarlayın.

5. **Izin verilen dış yeniden yönlendirme URL 'lerinde**`appname://easyauth.callback` girin.  Bu dizedeki _appname_ , MOBIL uygulamanızın URL şemadır.  Bir protokol için normal URL belirtimini izlemelidir (yalnızca harfler ve rakamlar kullanın ve bir harfle başlar).  Mobil uygulama kodunuzu birkaç yerde URL düzeniyle ayarlamanız gerekeceğinden, seçtiğiniz dizeyi bir yere iade etmeniz gerekir.

6. **Tamam**’a tıklayın.

7. **Kaydet** düğmesine tıklayın.

## <a name="permissions"></a>Kimliği doğrulanmış kullanıcılar için izinleri kısıtla
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode 'da uygulamayı başlatmak için **Çalıştır** ' a basın. Uygulama, kimliği doğrulanmamış bir kullanıcı olarak arka uca erişmeye çalıştığı için bir özel durum oluşur, ancak *TodoItem* tablosu şimdi kimlik doğrulaması gerektiriyor.

## <a name="add-authentication"></a>Uygulamaya kimlik doğrulaması ekle
**Amaç-C**:

1. Mac 'inizde, Xcode 'da *QSTodoListViewController. ı* dosyasını açın ve aşağıdaki yöntemi ekleyin:

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Google 'ı, kimlik sağlayıcınız olarak kullanmıyorsanız Google *taccount*, *Twitter*, *Facebook*veya *windowsazureactivedirectory* *olarak değiştirin.* Facebook kullanıyorsanız, uygulamanızdaki [Facebook etki alanlarını beyaz listeye][1] almanız gerekir.

    **Urlscheme** öğesini uygulamanız için benzersiz bir adla değiştirin.  UrlScheme, Azure portal **Izin verilen dış yeniden yönlendirme URL 'leri** alanında belirttiğiniz URL düzeni protokolüyle aynı olmalıdır. UrlScheme kimlik doğrulama geri çağırması tarafından, kimlik doğrulama isteği tamamlandıktan sonra uygulamanıza geri dönmek için kullanılır.

2. *QSTodoListViewController. d* içindeki `viewDidLoad` ' deki `[self refresh]` değerini aşağıdaki kodla değiştirin:

    ```Objective-C
    [self loginAndGetData];
    ```

3. @No__t-0 dosyasını açın ve aşağıdaki kodu ekleyin:

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. @No__t-0 dosyasını açın ve aşağıdaki kodu ekleyin:

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   Satır okuma `#pragma mark - Core Data stack` ' dan önce bu kodu doğrudan ekleyin.  _Appname_ öğesini, 1. adımda kullandığınız urlscheme değeri ile değiştirin.

5. @No__t-0 dosyasını açın (AppName uygulamanızın adıyla değiştirin) ve aşağıdaki kodu ekleyin:

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Bu kod `<dict>` öğesinin içine yerleştirilmelidir.  _Appname_ dizesini ( **cfpaketlemeli çatı**için dizi içinde) 1. adımda seçtiğiniz uygulama adıyla değiştirin.  Bu değişiklikleri plist düzenleyicisinde da yapabilirsiniz-plist düzenleyicisini açmak için XCode 'daki `AppName-Info.plist` dosyasına tıklayabilirsiniz.

    **Cfpaketkimliği** için `com.microsoft.azure.zumo` dizesini, Apple paket tanımlayıcın ile değiştirin.

6. Uygulamayı başlatmak için *Çalıştır* ' a basın ve oturum açın. Oturum açtığınızda yapılacaklar listesini görüntüleyebilmeniz ve güncelleştirme yapabilmelisiniz.

**Swift**:

1. Mac 'inizde, Xcode 'da *ToDoTableViewController. Swift* dosyasını açın ve aşağıdaki yöntemi ekleyin:

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Google 'ı, kimlik sağlayıcınız olarak kullanmıyorsanız Google *taccount*, *Twitter*, *Facebook*veya *windowsazureactivedirectory* *olarak değiştirin.* Facebook kullanıyorsanız, uygulamanızdaki [Facebook etki alanlarını beyaz listeye][1] almanız gerekir.

    **Urlscheme** öğesini uygulamanız için benzersiz bir adla değiştirin.  UrlScheme, Azure portal **Izin verilen dış yeniden yönlendirme URL 'leri** alanında belirttiğiniz URL düzeni protokolüyle aynı olmalıdır. UrlScheme kimlik doğrulama geri çağırması tarafından, kimlik doğrulama isteği tamamlandıktan sonra uygulamanıza geri dönmek için kullanılır.

2. *ToDoTableViewController. Swift*içindeki `viewDidLoad()` ' nin sonundaki `self.refreshControl?.beginRefreshing()` ve `self.onRefresh(self.refreshControl)` çizgilerini kaldırın. @No__t-0 ' a bir çağrı ekleyin:

    ```swift
    loginAndGetData()
    ```

3. @No__t-0 dosyasını açın ve aşağıdaki satırı `AppDelegate` sınıfına ekleyin:

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    _Appname_ öğesini, 1. adımda kullandığınız urlscheme değeri ile değiştirin.

4. @No__t-0 dosyasını açın (AppName uygulamanızın adıyla değiştirin) ve aşağıdaki kodu ekleyin:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    Bu kod `<dict>` öğesinin içine yerleştirilmelidir.  _Appname_ dizesini ( **cfpaketlemeli çatı**için dizi içinde) 1. adımda seçtiğiniz uygulama adıyla değiştirin.  Bu değişiklikleri plist düzenleyicisinde da yapabilirsiniz-plist düzenleyicisini açmak için XCode 'daki `AppName-Info.plist` dosyasına tıklayabilirsiniz.

    **Cfpaketkimliği** için `com.microsoft.azure.zumo` dizesini, Apple paket tanımlayıcın ile değiştirin.

5. Uygulamayı başlatmak için *Çalıştır* ' a basın ve oturum açın. Oturum açtığınızda yapılacaklar listesini görüntüleyebilmeniz ve güncelleştirme yapabilmelisiniz.

App Service kimlik doğrulaması, uygulamaların uygulamalar arası Iletişimini kullanır.  Bu konuyla ilgili daha fazla bilgi için [Apple belgelerine][2] bakın
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure portalda]: https://portal.azure.com

[iOS hızlı başlangıç]: app-service-mobile-ios-get-started.md

