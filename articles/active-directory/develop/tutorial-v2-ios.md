---
title: 'Öğretici: kimlik doğrulaması için Microsoft Identity platformunu kullanan bir iOS veya macOS uygulaması oluşturun | Mavisi'
titleSuffix: Microsoft identity platform
description: Bu öğreticide, kullanıcıları oturum açmak için Microsoft Identity platformunu kullanan bir iOS veya macOS uygulaması derleyebilir ve Microsoft Graph API 'sini adına çağırmak için bir erişim belirteci alacaksınız.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e273b1c41a9c418bf0121e87e73ec59e65f2242e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102963"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Öğretici: Kullanıcı oturum açma ve iOS veya macOS uygulamasından Microsoft Graph çağırma

Bu öğreticide, kullanıcıları imzalamak ve Microsoft Graph API 'sini çağırmak için bir erişim belirteci almak üzere Microsoft Identity platformu ile tümleştirilen bir iOS veya macOS uygulaması oluşturacaksınız.

Kılavuzu tamamladığınızda, uygulamanız kişisel Microsoft hesaplarının (outlook.com, live.com ve diğerleri dahil) ve Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş veya okul hesapları için oturum açma işlemlerini kabul eder. Bu öğretici hem iOS hem de macOS uygulamaları için geçerlidir. Bazı adımlar iki platform arasında farklıdır.

Bu öğreticide:

> [!div class="checklist"]
> * *Xcode* 'da IOS veya MacOS uygulama projesi oluşturma
> * Uygulamayı Azure portal kaydetme
> * Kullanıcı oturum açma ve oturum kapatma desteği için kod ekleme
> * Microsoft Graph API 'sini çağırmak için kod ekleme
> * Uygulamayı test etme

## <a name="prerequisites"></a>Önkoşullar

- [Xcode 11. x +](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Öğretici uygulamasının nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Bu öğreticideki uygulama, kullanıcılar için oturum açabilir ve Microsoft Graph verileri alabilir. Bu verilere, yetkilendirme gerektiren ve Microsoft Identity platform tarafından korunan korumalı bir API (Bu durumda Microsoft Graph API) aracılığıyla erişilir.

Daha ayrıntılı belirtmek gerekirse:

* Uygulamanız kullanıcı tarafından bir tarayıcı veya Microsoft Authenticator aracılığıyla oturum açacaktır.
* Son Kullanıcı, uygulamanızın istediği izinleri kabul eder.
* Uygulamanıza Microsoft Graph API 'SI için bir erişim belirteci verilmeyecektir.
* Erişim belirteci, Web API 'sine HTTP isteğine dahil edilir.
* Microsoft Graph yanıtını işleyin.

Bu örnek, kimlik doğrulamasını uygulamak için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır. MSAL, belirteçleri otomatik olarak yenileyecek, cihazdaki diğer uygulamalar arasında çoklu oturum açma (SSO) sunacaktır ve hesapları yönetir.

Bu öğreticide oluşturduğunuz uygulamanın tamamlanmış bir sürümünü indirmek isterseniz GitHub üzerinde her iki sürümü de bulabilirsiniz:

- [iOS kod örneği](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [MacOS kod örneği](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Yeni proje oluşturma

1. Xcode ' u açın ve **Yeni bir Xcode projesi oluştur**' u seçin.
2. İOS uygulamaları için **iOS**  >  **tek görünüm uygulaması** ' nı seçin ve **İleri ' yi** seçin.
3. MacOS uygulamaları için **MacOS**  >  **Cocoa uygulaması** ' nı seçin ve **İleri ' yi** seçin.
4. Bir ürün adı belirtin.
5. **Dili** **Swift** olarak ayarlayın ve ileri ' **yi** seçin.
6. Uygulamanızı oluşturmak için bir klasör seçin ve **Oluştur**' u seçin.

## <a name="register-your-application"></a>Uygulamanızı kaydetme

1. <a href="https://portal.azure.com/" target="_blank">Azure portalında</a> oturum açın.
1. Birden fazla kiracıya erişiminiz varsa, uygulamayı kaydetmek istediğiniz kiracıyı seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: .
1. **Azure Active Directory**'yi bulun ve seçin.
1. **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.
1. Uygulamanız için bir **ad** girin. Uygulamanızın kullanıcıları bu adı görebilir ve daha sonra değiştirebilirsiniz.
1. **Desteklenen hesap türleri** altındaki **herhangi bir kurumsal dizinde (HERHANGI bir Azure ad dizini-çoklu kiracı) ve kişisel Microsoft hesaplarının (örn. Skype, Xbox) hesaplarını** seçin.
1. **Kaydet**’i seçin.
1. **Yönet** altında **kimlik doğrulama**  >  **Platform Ekle**  >  **iOS/MacOS**' u seçin.
1. Projenizin paket KIMLIĞINI girin. Kodu indirdiyseniz, bu `com.microsoft.identitysample.MSALiOS` . Kendi projenizi oluşturuyorsanız, Xcode 'da projenizi seçin ve **genel** sekmesini açın. Paket tanımlayıcısı **kimlik** bölümünde görünür.
1. Uygulamanızı daha sonra yapılandırırken girmeniz için **msal yapılandırma** sayfasında görünen **msal yapılandırmasını** **Yapılandır** ve Kaydet ' i seçin. 
1. **Bitti** seçeneğini belirleyin.

## <a name="add-msal"></a>MSAL Ekle

Uygulamanıza MSAL kitaplığını yüklemek için aşağıdaki yöntemlerle birini seçin:

### <a name="cocoapods"></a>CocoaPods

1. [Cocoapods](https://cocoapods.org/)kullanıyorsanız, `MSAL` önce, projenizin dosyası ile aynı klasörde adlı boş bir dosya oluşturarak uygulamasını yükleyebilirsiniz `podfile` `.xcodeproj` . Aşağıdakileri öğesine ekleyin `podfile` :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. `<your-target-here>`Projenizin adıyla değiştirin.
3. Bir Terminal penceresinde, `podfile` `pod install` msal kitaplığını yüklemek için oluşturduğunuz ve çalıştırdığınız klasörü içeren klasöre gidin.
4. `<your project name>.xcworkspace`Projeyi Xcode 'da yeniden yüklemek Için Xcode 'u kapatın ve açın.

### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage)kullanıyorsanız, yüklemek `MSAL` için şunu ekleyin `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Bir terminal penceresinden, güncelleştirilmiş ile aynı dizinde `Cartfile` , Carthage 'in projenizdeki bağımlılıkları güncelleştirmesi için aşağıdaki komutu çalıştırın.

iOS:

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>El ile

Git alt modülünü de kullanabilir veya uygulamanızda bir çerçeve olarak kullanmak için en son sürümü kullanıma alabilirsiniz.

## <a name="add-your-app-registration"></a>Uygulama kaydınızı ekleyin

Daha sonra, uygulama kaydınızı kodunuza ekleyeceğiz.

İlk olarak, aşağıdaki içeri aktarma ifadesini ve ya da dosyalarının en üstüne ekleyin `ViewController.swift` `AppDelegate.swift` `SceneDelegate.swift` :

```swift
import MSAL
```

Ardından, aşağıdaki kodu ' dan `ViewController.swift` önce ekleyin `viewDidLoad()` :

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Yukarıda değiştirdiğiniz tek değer, `kClientID` [Uygulama Kimliğiniz](./developer-glossary.md#application-id-client-id)olarak atanan değerdir. Bu değer, uygulamayı Azure portal kaydetmek için Bu öğreticinin başındaki adım sırasında kaydettiğiniz MSAL yapılandırma verilerinin bir parçasıdır.

## <a name="configure-xcode-project-settings"></a>Xcode proje ayarlarını yapılandırma

Proje **imzalama & olanaklarınız** için yeni bir anahtarlık grubu ekleyin. Anahtarlık grubu, `com.microsoft.adalcache` iOS ve `com.microsoft.identity.universalstorage` MacOS üzerinde olmalıdır.

![Anahtarlık grubunun nasıl ayarlanacağını gösteren Xcode Kullanıcı arabirimi](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Yalnızca iOS için, URL düzenlerini yapılandırın

Bu adımda, `CFBundleURLSchemes` oturum açtıktan sonra kullanıcının uygulamaya geri yönlendirilebilmesi için kayıt yaptıracaktır. Böylece `LSApplicationQueriesSchemes` uygulamanızın Microsoft Authenticator kullanmasına de olanak tanır.

Xcode 'da, `Info.plist` kaynak kodu dosyası olarak açın ve bölümünün içine aşağıdakini ekleyin `<dict>` . `[BUNDLE_ID]`Azure Portal kullandığınız değerle değiştirin. Kodu indirdiyseniz, paket tanımlayıcısı olur `com.microsoft.identitysample.MSALiOS` . Kendi projenizi oluşturuyorsanız, Xcode 'da projenizi seçin ve **genel** sekmesini açın. Paket tanımlayıcısı **kimlik** bölümünde görünür.

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.[BUNDLE_ID]</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Yalnızca macOS için uygulama korumalı alanını yapılandırın

1. Xcode proje ayarlarınıza > **Yetenekler sekmesi**  >  **uygulama korumalı alanı** ' na gidin
2. **Giden bağlantılar (istemci)** onay kutusunu seçin.

## <a name="create-your-apps-ui"></a>Uygulamanızın Kullanıcı arabirimini oluşturma

Şimdi, aşağıdaki kodu sınıfına ekleyerek Microsoft Graph API 'sini çağırmak için bir düğme, başka bir oturum kapatmak için de bir metin görünümü ve bir çıktı görmek için bir kullanıcı arabirimi oluşturun `ViewController` :

### <a name="ios-ui"></a>iOS Kullanıcı arabirimi

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS Kullanıcı arabirimi

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Ardından, sınıfının içinde, `ViewController` `viewDidLoad()` yöntemini ile değiştirin:

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>MSAL kullanma

### <a name="initialize-msal"></a>MSAL Başlat

`initMSAL`Sınıfına aşağıdaki yöntemi ekleyin `ViewController` :

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Aşağıdaki After `initMSAL` yöntemini `ViewController` sınıfına ekleyin.

### <a name="ios-code"></a>iOS kodu:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS kodu:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Yalnızca iOS için, oturum açma geri aramasını işleyin

`AppDelegate.swift` dosyasını açın. Oturum açtıktan sonra geri çağırma işlemini işlemek için aşağıdaki `MSALPublicClientApplication.handleMSALResponse` `appDelegate` gibi sınıfa ekleyin:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Xcode 11** KULLANıYORSANıZ, msal geri çağırma işlemini yerine içine yerleştirmeniz gerekir `SceneDelegate.swift` .
Daha eski iOS ile uyumluluk için hem UISceneDelegate hem de Uıapplicationdelegate 'i destekediyorsanız, MSAL geri çağrısının her iki dosyaya da yerleştirilmesi gerekir.

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {

        guard let urlContext = URLContexts.first else {
            return
        }

        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication

        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

#### <a name="acquire-tokens"></a>Belirteçleri al

Şimdi, uygulamanın kullanıcı arabirimi işleme mantığını uygulayabiliriz ve belirteçleri MSAL üzerinden etkileşimli olarak alabilirsiniz.

MSAL belirteçleri almak için iki birincil yöntem sunar: `acquireTokenSilently()` ve `acquireTokenInteractively()` :

- `acquireTokenSilently()` bir hesap mevcut olduğu sürece Kullanıcı etkileşimi olmadan bir kullanıcıya oturum açma ve belirteçleri almaya çalışır. `acquireTokenSilently()` , `MSALAccount` msal hesap numaralandırma API 'lerinden biri kullanılarak alınabilecek geçerli bir sağlama gerektirir. Bu örnek, `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` geçerli hesabı almak için kullanır.

- `acquireTokenInteractively()` Kullanıcı oturumu açmaya çalışırken her zaman Kullanıcı ARABIRIMINI gösterir. Etkileşimli bir SSO deneyimi sağlamak için tarayıcıda oturum tanımlama bilgilerini veya Microsoft Authenticator içindeki bir hesabı kullanabilir.

Sınıfına aşağıdaki kodu ekleyin `ViewController` :

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Bir belirteci etkileşimli olarak alın

Aşağıdaki kod parçacığı bir nesne oluşturup çağırarak bir belirteci ilk kez alır `MSALInteractiveTokenParameters` `acquireToken` . Daha sonra şu kodu ekleyeceksiniz:

1. `MSALInteractiveTokenParameters`Kapsamlarla oluşturulur.
2. `acquireToken()`Oluşturulan parametrelerle çağırır.
3. Hataları işler. Daha fazla ayrıntı için [msal for iOS ve macOS hata işleme Kılavuzu](msal-error-handling-ios.md)' na bakın.
4. Başarılı durumu işler.

Aşağıdaki kodu `ViewController` sınıfına ekleyin.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

`promptType`Özelliği, `MSALInteractiveTokenParameters` kimlik doğrulama ve onay istemi davranışını yapılandırır. Aşağıdaki değerler desteklenir:

- `.promptIfNecessary` (varsayılan)-kullanıcıya yalnızca gerekli olduğunda sorulur. SSO deneyimi, WebView 'daki tanımlama bilgilerinin varlığına ve hesap türüne göre belirlenir. Birden çok kullanıcı oturum açmışsa, hesap seçimi deneyimi sunulur. *Bu, varsayılan davranıştır*.
- `.selectAccount` -Kullanıcı belirtilmediyse, kimlik doğrulama Web görünümü, kullanıcının seçim yapmak için şu anda oturum açmış olan hesapların bir listesini gösterir.
- `.login` -Kullanıcının WebView 'da kimlik doğrulamasını gerektirir. Bu değeri belirtirseniz tek seferde yalnızca bir hesap oturum açmış olabilir.
- `.consent` -Kullanıcının istek için geçerli kapsam kümesine onay vermesini gerektirir.

#### <a name="get-a-token-silently"></a>Belirteci sessizce al

Güncelleştirilmiş bir belirteci sessizce almak için, sınıfına aşağıdaki kodu ekleyin `ViewController` . Bir nesne oluşturur `MSALSilentTokenParameters` ve çağırır `acquireTokenSilent()` :

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>Microsoft Graph API 'sini çağırma

Belirteciniz olduktan sonra uygulamanız bunu HTTP üstbilgisinde kullanarak Microsoft Graph yetkili bir istek yapabilir:

| üst bilgi anahtarı    | değer                 |
| ------------- | --------------------- |
| Yetkilendirme | Taşıyıcı \<access-token> |

Sınıfına aşağıdaki kodu ekleyin `ViewController` :

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

Microsoft Graph API 'SI hakkında daha fazla bilgi edinmek için bkz. [MICROSOFT Graph API](https://graph.microsoft.com) .

### <a name="use-msal-for-sign-out"></a>Oturumu kapatma için MSAL kullanma

Ardından, oturum kapatma desteği ekleyin.

> [!Important]
> MSAL ile oturum açmak, bir kullanıcı hakkındaki bilinen tüm bilgileri uygulamadan kaldırır ve cihaz yapılandırması izin verildiğinde cihazındaki etkin bir oturumu kaldırır. Ayrıca, isteğe bağlı olarak kullanıcının tarayıcıdan oturum açmasını sağlayabilirsiniz.

Oturum kapatma özelliği eklemek için, sınıfının içine aşağıdaki kodu ekleyin `ViewController` .

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Belirteç önbelleğe almayı etkinleştir

Varsayılan olarak, MSAL, uygulamanızın belirteçlerini iOS veya macOS anahtarlığınızdan önbelleğe alır.

Belirteç önbelleğe almayı etkinleştirmek için:

1. Uygulamanızın düzgün şekilde imzalandığından emin olun
1. Xcode proje ayarlarınıza gidin > **Yetenekler sekmesi**  >  **Anahtarlık paylaşımını etkinleştir**
1. **+** Aşağıdaki **Anahtarlık gruplarından** birini seçin ve girin:
    - Işlemine `com.microsoft.adalcache`
    - MacOS `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Yardımcı yöntemler ekleme
`ViewController`Örneği gerçekleştirmek için aşağıdaki yardımcı yöntemleri sınıfına ekleyin.

### <a name="ios-ui"></a>iOS Kullanıcı arabirimi:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS Kullanıcı arabirimi:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Yalnızca iOS için, ek cihaz bilgileri alın

Cihazın paylaşılan olarak yapılandırılıp yapılandırılmadığını de içerecek şekilde, geçerli cihaz yapılandırmasını okumak için aşağıdaki kodu kullanın:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Çoklu hesap uygulamaları

Bu uygulama, tek bir hesap senaryosu için oluşturulmuştur. MSAL ayrıca çoklu hesap senaryolarını destekler, ancak uygulamalardan bazı ek işler gerektirir. Kullanıcıların belirteç gerektiren her eylem için kullanmak istedikleri hesabı seçmesini sağlamak için Kullanıcı arabirimi oluşturmanız gerekir. Alternatif olarak, uygulamanız MSAL ' dan tüm hesapları sorgulayarak kullanılacak hesabı seçmek için bir buluşsal yöntem uygulayabilir. Örneğin bkz. `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Uygulamanızı test etme

Uygulamayı derleyin ve bir test cihazına veya simülatör 'a dağıtın. Azure AD veya kişisel Microsoft hesapları için oturum açabiliyor ve belirteçleri alabilmesi gerekir.

Kullanıcı uygulamanızda ilk kez oturum açtığında, bu kullanıcılara istenen izinleri onaylaması için Microsoft kimliği sorulur. Birçok kullanıcı kabul etme yeteneğine sahip olsa da, bazı Azure AD kiracılar, yöneticilerin tüm kullanıcılar adına onay vermesini gerektiren Kullanıcı onayını devre dışı bırakmış olur. Bu senaryoyu desteklemek için, uygulamanızın kapsamlarını Azure portal kaydedin.

Oturum açtıktan sonra, uygulama Microsoft Graph uç noktasından döndürülen verileri görüntüler `/me` .

## <a name="next-steps"></a>Sonraki adımlar

Çok bölgeli senaryo serimizde korumalı Web API 'Lerini çağıran mobil uygulamalar oluşturma hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Senaryo: Web API 'Lerini çağıran mobil uygulama](scenario-mobile-overview.md)
