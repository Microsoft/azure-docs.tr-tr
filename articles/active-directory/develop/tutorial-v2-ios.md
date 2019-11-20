---
title: İOS ve macOS ile çalışmaya başlama-Microsoft Identity platform | Mavisi
description: İOS ve macOS (Swift) uygulamaları, Microsoft Identity platform kullanarak erişim belirteçleri gerektiren bir API 'YI nasıl çağırabilir
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/30/2019
ms.author: jmprieur
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e3892a03ffe097a51f294e698168f00e1359f92
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960656"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-from-an-ios-or-macos-app"></a>Kullanıcıları oturum açın ve iOS veya macOS uygulamasından Microsoft Graph çağırın

Bu öğreticide, bir iOS veya macOS uygulamasını Microsoft Identity platformu ile tümleştirmeyi öğreneceksiniz. Uygulama bir kullanıcıya oturum açacaktır, Microsoft Graph API 'sini çağırmak için bir erişim belirteci alır ve Microsoft Graph API 'sine bir istek yapar.  

Kılavuzu tamamladığınızda, uygulamanız kişisel Microsoft hesaplarının (outlook.com, live.com ve diğerleri dahil) ve Azure Active Directory kullanan herhangi bir şirketten veya kuruluştan iş veya okul hesapları için oturum açma işlemlerini kabul eder.

## <a name="how-this-tutorial-works"></a>Bu öğreticinin nasıl çalıştığı

![Bu öğretici tarafından oluşturulan örnek uygulamanın nasıl çalıştığını gösterir](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Bu öğreticideki uygulama, kullanıcıların oturum açmasını ve adına veri almasını sağlayacaktır.  Bu verilere, yetkilendirme gerektiren ve Microsoft Identity platform tarafından korunan korumalı bir API (Bu durumda Microsoft Graph API) aracılığıyla erişilir.

Daha ayrıntılı belirtmek gerekirse:

* Uygulamanız kullanıcı tarafından bir tarayıcı veya Microsoft Authenticator aracılığıyla oturum açacaktır.
* Son Kullanıcı, uygulamanızın istediği izinleri kabul eder.
* Uygulamanıza Microsoft Graph API 'SI için bir erişim belirteci verilmeyecektir.
* Erişim belirteci, Web API 'sine HTTP isteğine dahil edilir.
* Microsoft Graph yanıtını işleyin.

Bu örnek, kimlik doğrulamasını uygulamak için Microsoft kimlik doğrulama kitaplığı 'nı (MSAL) kullanır. MSAL, belirteçleri otomatik olarak yenileyecek, cihazdaki diğer uygulamalar arasında çoklu oturum açma (SSO) sunacaktır ve hesapları yönetir.

Bu öğretici hem iOS hem de macOS uygulamaları için geçerlidir. Bazı adımların bu iki platform arasında farklı olduğunu unutmayın. 

## <a name="prerequisites"></a>Önkoşullar

- Bu kılavuzda uygulamayı derlemek için XCode sürüm 10. x veya üzeri gereklidir. , [İTunes web sitesinden](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode Indirme URL 'SI")Xcode indirebilirsiniz.
- Microsoft kimlik doğrulama kitaplığı ([msal. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)). Bir bağımlılık Yöneticisi kullanabilir veya kitaplığı el ile ekleyebilirsiniz. Aşağıdaki yönergelerde nasıl yapılacağı gösterilmektedir.

Bu öğretici, yeni bir proje oluşturur. Bunun yerine tamamlanan öğreticiyi indirmek isterseniz, kodu indirin:
- [iOS örnek kodu](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS örnek kodu](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

## <a name="create-a-new-project"></a>Yeni bir proje oluşturma

1. Xcode ' u açın ve **Yeni bir Xcode projesi oluştur**' u seçin.
2. İOS uygulamaları için **ios** > **tek görünüm uygulaması** ' nı seçin ve **İleri ' yi**seçin.
3. MacOS uygulamaları için **macos** > **Cocoa uygulaması** ' nı seçin ve **İleri ' yi**seçin.
4. Bir ürün adı belirtin.
5. **Dili** **Swift** olarak ayarlayın ve ileri ' **yi**seçin.
6. Uygulamanızı oluşturmak için bir klasör seçin ve **Oluştur**' a tıklayın.

## <a name="register-your-application"></a>Uygulamanızı kaydetme

1. [Azure portal](https://aka.ms/MobileAppReg)'a gidin
2. [Uygulama kayıtları dikey penceresini](https://ms.portal.azure.com/?feature.broker=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) açın ve **+ Yeni kayıt**' ye tıklayın.
3. Uygulamanız için bir **ad** girin ve yeniden yönlendirme URI 'Sini ayarlamadan **Kaydet**' e tıklayın.
4. Görüntülenen bölmenin **Yönet** bölümünde **kimlik doğrulaması**' nı seçin.

5. Yeni uygulama kayıt deneyimini açmak için ekranın üst kısmındaki **Yeni deneyimi deneyin** ' e tıklayın ve ardından **+ Yeni kayıt** >  **+ bir platform** > **iOS**ekleyin.
    - Projenizin paket KIMLIĞINI girin. Kodu indirdiyseniz bu `com.microsoft.identitysample.MSALiOS`. Kendi projenizi oluşturuyorsanız, Xcode 'da projenizi seçin ve **genel** sekmesini açın. Paket tanımlayıcısı **kimlik** bölümünde görünür.
    - MacOS için iOS deneyimini de kullanmanız gerektiğini unutmayın. 
6. `Configure` ' a tıklayın ve **iOS yapılandırma** sayfasında görüntülenen **msal yapılandırmasını** kaydederek uygulamanızı daha sonra yapılandırdığınızda girebilmenizi sağlayabilirsiniz.  **Bitti**’ye tıklayın.

## <a name="add-msal"></a>MSAL Ekle

Uygulamanıza MSAL kitaplığını yüklemek için aşağıdaki yöntemlerle birini seçin:

### <a name="cocoapods"></a>CocoaPods

1. [Cocoapods](https://cocoapods.org/)kullanıyorsanız, `MSAL` ' yi önce projenizin `.xcodeproj` dosyası ile aynı klasörde `podfile` adlı boş bir dosya oluşturarak yükleyebilirsiniz. Aşağıdakileri `podfile`ekleyin:

   ```
   use_frameworks!
   
   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. `<your-target-here>`, projenizin adıyla değiştirin.
3. Bir Terminal penceresinde, MSAL kitaplığını yüklemek için oluşturduğunuz `podfile` içeren klasöre gidin ve `pod install` çalıştırın.
4. Projeyi Xcode 'da yeniden yüklemek için Xcode 'u kapatın ve `<your project name>.xcworkspace` açın.

### <a name="carthage"></a>Carthage

[Carthage](https://github.com/Carthage/Carthage)kullanıyorsanız, `MSAL` ' yi `Cartfile`ekleyerek yükleyebilirsiniz:

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Bir terminal penceresinden, güncelleştirilmiş `Cartfile`aynı dizinde, Carthage 'in projenizdeki bağımlılıkları güncelleştirmesi için aşağıdaki komutu çalıştırın.

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

İlk olarak, aşağıdaki Import ifadesini `ViewController.swift` ve `AppDelegate.swift` dosyalarının üst kısmına ekleyin:

```swift
import MSAL
```

Ardından, `viewDidLoad()`önce `ViewController.swift` aşağıdaki kodu ekleyin:

```swift
let kClientID = "Your_Application_Id_Here"

// Additional variables for Auth and Graph API
let kGraphURI = "https://graph.microsoft.com/v1.0/me/" // the Microsoft Graph endpoint
let kScopes: [String] = ["https://graph.microsoft.com/user.read"] // request permission to read the profile of the signed-in user
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization’s Azure AD tenant to sign in
var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
```

Yukarıda değiştirmeniz gereken tek değer, [Uygulama Kimliğiniz](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-id-client-id)için `kClientID`atanan değerdir. Bu değer, uygulamayı Azure portal kaydetmek için Bu öğreticinin başındaki adım sırasında kaydettiğiniz MSAL yapılandırma verilerinin bir parçasıdır.

## <a name="for-ios-only-configure-url-schemes"></a>Yalnızca iOS için, URL düzenlerini yapılandırın

Bu adımda, oturum açtıktan sonra kullanıcının uygulamaya yeniden yönlendirilmesi için `CFBundleURLSchemes` kaydedeceğinizi caksınız. `LSApplicationQueriesSchemes`, uygulamanızın Microsoft Authenticator kullanmasına de olanak tanır.

Xcode 'da, kaynak kodu dosyası olarak `Info.plist` açın ve `<dict>` bölümünün içine aşağıdakini ekleyin. `[BUNDLE_ID]`, kodu indirdiğiniz `com.microsoft.identitysample.MSALiOS`Azure portal kullandığınız değerle değiştirin. Kendi projenizi oluşturuyorsanız, Xcode 'da projenizi seçin ve **genel** sekmesini açın. Paket tanımlayıcısı **kimlik** bölümünde görünür.

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

1. Xcode proje ayarlarınıza > **yetenekler sekmesine** gidin > **uygulama korumalı alanı**
2. **Giden bağlantılar (istemci)** onay kutusunu seçin. 

## <a name="create-your-apps-ui"></a>Uygulamanızın Kullanıcı arabirimini oluşturma

Şimdi Microsoft Graph API 'sini çağırmak için bir düğme, başka bir oturum açmak için bir kullanıcı arabirimi ve aşağıdaki kodu `ViewController`sınıfına ekleyerek bazı çıktıları görmek için bir metin görünümü oluşturun:

### <a name="ios-ui"></a>iOS Kullanıcı arabirimi

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = UIButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
        callGraphButton.setTitleColor(.blue, for: .normal)
        callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
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
        signOutButton.isEnabled = false
        
        // Add logging textfield
        loggingText = UITextView()
        loggingText.isUserInteractionEnabled = false
        loggingText.translatesAutoresizingMaskIntoConstraints = false
        
        self.view.addSubview(loggingText)
        
        loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
        loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
        loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: 10.0).isActive = true
        loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
    }
```

### <a name="macos-ui"></a>macOS Kullanıcı arabirimi

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

func initUI() {
        // Add call Graph button
        callGraphButton  = NSButton()
        callGraphButton.translatesAutoresizingMaskIntoConstraints = false
        callGraphButton.title = "Call Microsoft Graph API"
        callGraphButton.target = self
        callGraphButton.action = #selector(callGraphAPI(_:))
        callGraphButton.bezelStyle = .rounded
        self.view.addSubview(callGraphButton)
        
        callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
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
```

Sonra, `ViewController` sınıfının içinde, `viewDidLoad()` yöntemini ile değiştirin:

```swift
    override func viewDidLoad() {
        super.viewDidLoad()
        initUI()
        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }
    }
```

## <a name="use-msal"></a>MSAL kullanma

### <a name="initialize-msal"></a>MSAL Başlat

Aşağıdaki `initMSAL` yöntemini `ViewController` sınıfına ekleyin:

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

Aşağıdaki `initMSAL` yönteminden sonra `ViewController` sınıfına ekleyin.

### <a name="ios-code"></a>iOS kodu:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(parentViewController: self)
    }
```

### <a name="macos-code"></a>macOS kodu:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
        self.webViewParameters?.webviewType = .wkWebView
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Yalnızca iOS için, oturum açma geri aramasını işleyin

`AppDelegate.swift` dosyasını açın. Oturum açtıktan sonra geri aramayı işlemek için, `appDelegate` sınıfına aşağıdaki gibi `MSALPublicClientApplication.handleMSALResponse` ekleyin:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Xcode 11**kullanıyorsanız, bunun yerine msal geri çağırma işlemini `SceneDelegate.swift` yerleştirmeniz gerekir.
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

MSAL belirteçleri almak için iki birincil yöntem sunar: `acquireTokenSilently()` ve `acquireTokenInteractively()`: 

- `acquireTokenSilently()` bir Kullanıcı oturumu açma ve bir hesap var olduğu sürece herhangi bir kullanıcı etkileşimi olmadan belirteçleri almaya çalışır.

- `acquireTokenInteractively()`, Kullanıcı oturum açmaya çalışırken her zaman Kullanıcı ARABIRIMINI gösterir. Etkileşimli bir SSO deneyimi sağlamak için tarayıcıda oturum tanımlama bilgilerini veya Microsoft Authenticator içindeki bir hesabı kullanabilir.

`ViewController` sınıfına aşağıdaki kodu ekleyin:

```swift
    @objc func callGraphAPI(_ sender: AnyObject) {
        
        guard let currentAccount = self.currentAccount() else {
            // We check to see if we have a current logged in account.
            // If we don't, then we need to sign someone in.
            acquireTokenInteractively()
            return
        }
        
        acquireTokenSilently(currentAccount)
    }

    func currentAccount() -> MSALAccount? {
        
        guard let applicationContext = self.applicationContext else { return nil }
        
        // We retrieve our current account by getting the first account from cache
        // In multi-account applications, account should be retrieved by home account identifier or username instead
        
        do {
            let cachedAccounts = try applicationContext.allAccounts()
            if !cachedAccounts.isEmpty {
                return cachedAccounts.first
            }
        } catch let error as NSError {
            self.updateLogging(text: "Didn't find any accounts in cache: \(error)")
        }
        
        return nil
    }
```

#### <a name="get-a-token-interactively"></a>Bir belirteci etkileşimli olarak alın

Aşağıdaki kod, bir `MSALInteractiveTokenParameters` nesnesi oluşturup `acquireToken`çağırarak, ilk kez bir belirteç alır. Daha sonra şu kodu ekleyeceksiniz:

1. Kapsamlarla `MSALInteractiveTokenParameters` oluşturur.
2. Oluşturulan parametrelerle `acquireToken()` çağırır.
3. Hataları işler. Daha fazla ayrıntı için [msal for iOS ve macOS hata işleme Kılavuzu](msal-handling-exceptions.md)' na bakın.
4. Başarılı durumu işler.

Aşağıdaki kodu `ViewController` sınıfına ekleyin.

```swift
func acquireTokenInteractively() {
        
    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }
        
    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
        
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
        self.updateSignOutButton(enabled: true)
        self.getContentWithToken()
    }
}    
```


#### <a name="get-a-token-silently"></a>Belirteci sessizce al

Güncelleştirilmiş bir belirteci sessizce almak için, `ViewController` sınıfına aşağıdaki kodu ekleyin. Bir `MSALSilentTokenParameters` nesnesi oluşturur ve `acquireTokenSilent()`çağırır:

```swift
    
    func acquireTokenSilently(_ account : MSALAccount!) {
        guard let applicationContext = self.applicationContext else { return }
        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
        
        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in    
            if let error = error {
                let nsError = error as NSError
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
| Yetkilendirme | Taşıyıcı \<erişim-belirteç > |

`ViewController` sınıfına aşağıdaki kodu ekleyin:

```swift
    func getContentWithToken() {        
        // Specify the Graph API endpoint
        let url = URL(string: kGraphURI)
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
> MSAL ile oturum açmak, bir kullanıcıyla ilgili tüm bilinen bilgileri uygulamadan kaldırır, ancak kullanıcının cihazında etkin bir oturumu olmaya devam edecektir. Kullanıcı yeniden oturum açmayı denerse, oturum açma kullanıcı arabirimini görebilir, ancak cihaz oturumu hala etkin olduğundan kimlik bilgilerini yeniden girmeniz gerekebilir.

Oturum kapatma özelliği eklemek için, `ViewController` sınıfının içine aşağıdaki kodu ekleyin. Bu yöntem tüm hesaplar boyunca geçiş yapar ve bunları kaldırır:

```swift 
@objc func signOut(_ sender: AnyObject) {
        
        guard let applicationContext = self.applicationContext else { return }
        
        guard let account = self.currentAccount() else { return }
        
        do {
            
            /**
             Removes all tokens from the cache for this application for the provided account
             
             - account:    The account to remove from the cache
             */
            
            try applicationContext.remove(account)
            self.updateLogging(text: "")
            self.updateSignOutButton(enabled: false)
            self.accessToken = ""
            
        } catch let error as NSError {
            
            self.updateLogging(text: "Received error signing account out: \(error)")
        }
    }
```

### <a name="enable-token-caching"></a>Belirteç önbelleğe almayı etkinleştir

Varsayılan olarak, MSAL, uygulamanızın belirteçlerini iOS veya macOS anahtarlığınızdan önbelleğe alır. 

Belirteç önbelleğe almayı etkinleştirmek için:
1. Uygulamanızın düzgün şekilde imzalandığından emin olun
2. **Anahtarlık paylaşımını etkinleştirmek** > Xcode proje ayarları > **özellikleri sekmesine** gidin
3. **+** ' a tıklayın ve aşağıdaki **Anahtarlık grupları** girişini girin: 3. a IOS Için, MacOS için `com.microsoft.adalcache` 3. b girin `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Yardımcı yöntemler ekleme
Örneği gerçekleştirmek için aşağıdaki yardımcı yöntemleri `ViewController` sınıfına ekleyin.

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
```

### <a name="macos-ui"></a>macOS Kullanıcı arabirimi:

```swift
func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }
    
    func updateLogging(text : String) {
        
        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }
```



### <a name="multi-account-applications"></a>Çoklu hesap uygulamaları

Bu uygulama, tek bir hesap senaryosu için oluşturulmuştur. MSAL ayrıca çoklu hesap senaryolarını destekler, ancak uygulamalardan bazı ek işler gerektirir. Kullanıcıların belirteç gerektiren her eylem için kullanmak istedikleri hesabı seçmesini sağlamak için Kullanıcı arabirimi oluşturmanız gerekir. Alternatif olarak, uygulamanız `getAccounts()` yöntemi aracılığıyla kullanılacak hesabı seçmek için buluşsal yöntem uygulayabilir.

## <a name="test-your-app"></a>Uygulamanızı test etme

### <a name="run-locally"></a>Yerel olarak çalıştırma

Uygulamayı derleyin ve bir test cihazına veya simülatör 'a dağıtın. Azure AD veya kişisel Microsoft hesapları için oturum açabiliyor ve belirteçleri alabilmesi gerekir.

Kullanıcı uygulamanızda ilk kez oturum açtığında, bu kullanıcılara istenen izinleri onaylaması için Microsoft kimliği sorulur.  Birçok kullanıcı kabul etme yeteneğine sahip olsa da, bazı Azure AD kiracılar, yöneticilerin tüm kullanıcılar adına onay vermesini gerektiren Kullanıcı onayını devre dışı bırakmış olur. Bu senaryoyu desteklemek için, uygulamanızın kapsamlarını Azure portal kaydedin.

Oturum açtıktan sonra, uygulama Microsoft Graph `/me` uç noktasından döndürülen verileri görüntüler.

## <a name="get-help"></a>Yardım alın

Bu öğreticiyle veya Microsoft Identity platformu ile ilgili sorun yaşıyorsanız [Yardım ve destek](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options) bölümünü ziyaret edin.

Microsoft Identity platformunu geliştirmemize yardımcı olun. Kısa bir iki sorulık anketi tamamlayarak düşüncelerinizi bize söyleyin.

> [!div class="nextstepaction"]
> [Microsoft Identity platform Anketi](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
