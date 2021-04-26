---
title: Hızlı başlangıç-Azure Iletişim hizmetlerini kullanarak bir iOS uygulamasına çağrı ekleme
description: Bu hızlı başlangıçta, iOS için SDK 'Yı çağıran Azure Iletişim hizmetlerini kullanmayı öğreneceksiniz.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e1eed3f9449843e6c2dd8c77719402e709fdeb23
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327621"
---
Bu hızlı başlangıçta, iOS için SDK 'Yı çağıran Azure Iletişim Hizmetleri 'ni kullanarak bir çağrı başlatmayı öğreneceksiniz.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Bu belge, çağıran SDK 'nın 1.0.0-Beta. 9 sürümünü kullanır.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Anahtarınıza yüklenmiş geçerli bir geliştirici sertifikasıyla birlikte [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)çalıştıran bir Mac.
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Azure Iletişim hizmetiniz için bir [Kullanıcı erişim belirteci](../../access-tokens.md) .

## <a name="setting-up"></a>Ayarlanıyor

### <a name="creating-the-xcode-project"></a>Xcode projesi oluşturma

Xcode 'da yeni bir iOS projesi oluşturun ve **tek görünüm uygulama** şablonunu seçin. Bu öğretici [swiftuı çerçevesini](https://developer.apple.com/xcode/swiftui/)kullanır, bu nedenle **dili** **Swift** ve **Kullanıcı arabirimine** **swiftuı** olarak ayarlamanız gerekir. Bu hızlı başlangıç sırasında test oluşturuyoruz. **Testleri dahil etme** seçeneğinin işaretini kaldırın.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Xcode 'da yeni proje penceresini gösteren ekran görüntüsü.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods ile paketi ve bağımlılıkları yükler

1. Uygulamanız için bir pod dosyası oluşturmak için terminali açın ve proje klasörüne gidip şunu çalıştırın ```pod init```
3. Aşağıdaki kodu Pod dosyasına ekleyin ve kaydedin ("target" öğesinin projenizin adıyla eşleştiğinden emin olun):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.9'
     pod 'AzureCommunication', '~> 1.0.0-beta.9'
     pod 'AzureCore', '~> 1.0.0-beta.9'
   end
   ```

3. `pod install` öğesini çalıştırın.
3. Öğesini `.xcworkspace` Xcode ile açın.

### <a name="request-access-to-the-microphone"></a>Mikrofona erişim isteyin

Cihazın mikrofonuna erişmek için uygulamanızın bilgi Özellik listesini bir ile güncelleştirmeniz gerekir `NSMicrophoneUsageDescription` . İlişkili değeri, `string` sistemin kullanıcıdan erişim istemek için kullandığı iletişim kutusuna dahil edilecek bir öğesine ayarlarsınız.

`Info.plist`Proje ağacının girişine sağ tıklayın ve kaynak kodu **olarak aç**' ı seçin  >  . Aşağıdaki satırları en üst düzey bölümüne ekleyin `<dict>` ve dosyayı kaydedin.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Projenizin **ContentView. Swift** dosyasını açın ve `import` dosyanın en üstüne içeri aktarmak için bir bildirim ekleyin `AzureCommunicationCalling library` . Bunlara ek olarak, `AVFoundation` Bu kodda ses izni isteği için de ihtiyacımız olacak.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Yapının uygulamasını, bir `ContentView` kullanıcının bir çağrıyı başlatmasını ve bitibilmesini sağlayan bazı basıt UI denetimleriyle değiştirin. Bu hızlı başlangıçta bu denetimlere iş mantığı ekleyeceğiz.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Communication Service 'ın SDK 'yi çağıran bazı önemli özelliklerinden bazılarını işler:

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient, çağıran SDK 'ya ana giriş noktasıdır.|
| CallAgent | CallAgent, çağrıları başlatmak ve yönetmek için kullanılır. |
| CommunicationTokenCredential | CommunicationTokenCredential, CallAgent örneğini oluşturmak için belirteç kimlik bilgileri olarak kullanılır.| 
| Communicationuserıdentifier | Communicationuserıdentifier, aşağıdakilerin biri olabilecek Kullanıcı kimliğini temsil etmek için kullanılır: Communicationuserıdentifier/Phonenumberıdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>İstemcinin kimliğini doğrulama

Bir `CallAgent` Kullanıcı erişim belirtecine sahip bir örnek başlatın, bu da çağrı yapıp almamızı sağlar. Aşağıdaki kodu `onAppear` **ContentView. Swift** içindeki geri aramaya ekleyin:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

`<USER ACCESS TOKEN>`Kaynağınız için geçerli bir Kullanıcı erişim belirteci ile değiştirmeniz gerekir. Kullanılabilir bir belirteciniz yoksa, [Kullanıcı erişim belirteci](../../access-tokens.md) belgelerine başvurun.

## <a name="start-a-call"></a>Bir çağrı başlatın

`startCall`Yöntemi, *çağrıya başla* düğmesine dokunduğunda gerçekleştirilecek eylem olarak ayarlanır. Şunu içeren bir çağrı başlatmak için uygulamayı güncelleştirin `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.startCall(participants: callees, options: StartCallOptions())
        }
    }
}
```

Ayrıca, `StartCallOptions` çağrının başlangıç seçeneklerini ayarlamak için içindeki özellikleri de kullanabilirsiniz (yani, mikrofonun sesini kapalı olarak başlatmaya izin verir).

## <a name="end-a-call"></a>Çağrıyı Sonlandır

`endCall` *Son çağrı* düğmesine dokunduğunda geçerli çağrıyı sonlandırmak için yöntemini uygulayın.

```swift
func endCall()
{    
    self.call!.hangUp(HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Kodu çalıştırma

**Ürün**  >  **çalıştırması** ' nı seçerek veya (&#8984;-R) klavye kısayolunu kullanarak uygulamanızı iOS simülatörü üzerinde oluşturabilir ve çalıştırabilirsiniz.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Hızlı başlangıç uygulamasına yönelik nihai görünüm":::

Metin alanına bir kullanıcı KIMLIĞI sağlayarak ve **çağrıya başla** düğmesine dokunarak gıden bir VoIP çağrısı yapabilirsiniz. Çağırmak `8:echo123` sizi bir Echo bot ile bağlar, bu, başlamak ve ses cihazlarınızın çalıştığını doğrulamak için harika bir yoldur. 

> [!NOTE]
> İlk kez bir çağrı yaptığınızda sistem mikrofona erişim ister. Bir üretim uygulamasında izin `AVAudioSession` [durumunu denetlemek](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) ve izin verilmediği zaman uygulamanızın davranışını dikkatlice güncelleştirmek için API 'yi kullanmanız gerekir.

## <a name="sample-code"></a>Örnek Kod

Örnek uygulamayı [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling) 'dan indirebilirsiniz
