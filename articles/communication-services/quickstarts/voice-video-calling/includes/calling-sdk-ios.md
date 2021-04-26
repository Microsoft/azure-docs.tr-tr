---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498802"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Dağıtılmış bir Azure Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Arama istemcisini etkinleştirmek için bir Kullanıcı erişim belirteci. [Bir Kullanıcı erişim belirteci alın](../../access-tokens.md).
- İsteğe bağlı: [uygulamanıza yönelik sesli çağrı ekleme](../getting-started-with-calling.md) hızlı başlangıç ' i doldurun.

## <a name="set-up-your-system"></a>Sisteminizi ayarlama

### <a name="create-the-xcode-project"></a>Xcode projesi oluşturma

Xcode 'da yeni bir iOS projesi oluşturun ve **tek görünüm uygulama** şablonunu seçin. Bu hızlı başlangıç, [swiftuı çerçevesini](https://developer.apple.com/xcode/swiftui/)kullanır, bu nedenle **dili** **Swift** ve **Kullanıcı arabirimine** **swiftuı** olarak ayarlamanız gerekir. 

Bu hızlı başlangıç sırasında birim testleri veya UI testleri oluşturuyoruz. **Birim testlerini dahil et** ve **UI testlerini dahil et** metin kutularını temizleyebilirsiniz.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Xcode içinde proje oluşturmak için pencereyi gösteren ekran görüntüsü.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>CocoaPods ile paketi ve bağımlılıkları yükler

1. Uygulamanız için şöyle bir pod dosyası oluşturun:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. `pod install` öğesini çalıştırın.
3. `.xcworkspace`Xcode ile açın.

### <a name="request-access-to-the-microphone"></a>Mikrofona erişim isteyin

Cihazın mikrofonuna erişmek için uygulamanızın bilgi Özellik listesini ile güncelleştirmeniz gerekir `NSMicrophoneUsageDescription` . İlişkili değeri, `string` sistemin kullanıcıdan erişim istemek için kullandığı iletişim kutusunda yer alacak şekilde ayarlarsınız.

`Info.plist`Proje ağacının girişine sağ tıklayın ve kaynak kodu **olarak aç**' ı seçin  >  . Üst düzey bölümüne aşağıdaki satırları ekleyin `<dict>` ve dosyayı kaydedin.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Projenizin *ContentView. Swift* dosyasını açın ve `import` Kitaplığı İçeri aktarmak için dosyanın en üstüne bir bildirim ekleyin `AzureCommunicationCalling` . Buna ek olarak içeri aktarın `AVFoundation` . Bu, koddaki ses izin istekleri için gerekir.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>Nesne modelini öğrenme

Aşağıdaki sınıflar ve arabirimler, iOS için SDK 'Yı çağıran Azure Iletişim Hizmetleri 'nin bazı önemli özelliklerinden bazılarını işler.

> [!NOTE]
> Bu hızlı başlangıç, çağıran SDK 'nın 1.0.0-Beta. 8 sürümünü kullanır.


| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` , çağıran SDK 'ya ana giriş noktasıdır.|
| `CallAgent` | `CallAgent` çağrıları başlatmak ve yönetmek için kullanılır. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` , örneklendirilecek belirteç kimlik bilgileri olarak kullanılır `CallAgent` .| 
| `CommunicationIdentifier` | `CommunicationIdentifier` , kullanıcının kimliğini temsil etmek için kullanılır. Kimlik, veya olabilir `CommunicationUserIdentifier` `PhoneNumberIdentifier` `CallingApplication` . |

> [!NOTE]
> Uygulama olay temsilcileri uygularsa, olay abonelikleri gerektiren nesnelere güçlü bir başvuru tutması gerekir. Örneğin, `RemoteParticipant` yöntemi çağrılırken bir nesne döndürüldüğünde `call.addParticipant` ve uygulama, dinlemek üzere temsilciyi ayarlarsa `RemoteParticipantDelegate` , uygulamanın nesnesine bir güçlü başvuru tutması gerekir `RemoteParticipant` . Aksi takdirde, bu nesne toplanırsa, çağıran SDK nesneyi çağırmayı denediğinde temsilci önemli bir özel durum oluşturur.

## <a name="initialize-callagent"></a>CallAgent 'ı Başlat

`CallAgent`Öğesinden bir örnek oluşturmak için `CallClient` , başlatıldıktan sonra bir `callClient.createCallAgent` nesneyi zaman uyumsuz olarak döndüren bir yöntemi kullanmanız gerekir `CallAgent` .

Bir çağrı istemcisi oluşturmak için bir nesnesi geçirmeniz gerekir `CommunicationTokenCredential` .

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

`CommunicationTokenCredential`Oluşturduğunuz nesneyi geçirin `CallClient` ve görünen adı ayarlayın.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Giden bir çağrı yerleştir

Bir çağrı oluşturmak ve başlatmak için, üzerindeki API 'lerden birini çağırmanız `CallAgent` ve Iletişim Hizmetleri Yönetim SDK 'sını kullanarak sağladığınız bir kullanıcının Iletişim Hizmetleri kimliğini sağlamanız gerekir.

Çağrı oluşturma ve başlatma zaman uyumludur. Çağrıda tüm olaylara abone olmanızı sağlayan bir çağrı örneği alacaksınız.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Kullanıcılara 1:1 çağrısı veya Kullanıcı ve PSTN ile 1: n çağrısı koyun

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Kullanıcılar ve PSTN ile 1: n çağrısı yerleştir
Çağrıyı PSTN 'e yerleştirmek için, Iletişim hizmetleriyle elde edilen bir telefon numarası belirtmeniz gerekir.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>Video ile 1:1 çağrısı yerleştirme
Bir cihaz yöneticisi örneğini almak için [cihazları yönetme](#manage-devices)hakkında bölümüne bakın.

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Grup çağrısına katılır
Bir çağrıya katmak için, üzerindeki API 'lerden birini çağırmanız gerekir `CallAgent` .

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>Gelen bir çağrıya abone olma
Gelen çağrı olayına abone olma.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Gelen çağrıyı kabul et
Bir çağrıyı kabul etmek için `accept` bir çağrı nesnesi üzerinde yöntemini çağırın. İçin bir temsilci ayarlayın `CallAgent` .

```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Anında iletme bildirimlerini ayarlama

Mobil anında iletme bildirimi, mobil cihazda aldığınız açılır bildirimdir. Çağırmak için VoIP (Internet Protokolü üzerinden ses) anında iletme bildirimleri üzerine odaklanacağız. 

Aşağıdaki bölümlerde, anında iletme bildirimlerinin nasıl kaydedileceği, işleneceği ve silinkaydedileceği açıklanır. Bu görevleri başlatmaya başlamadan önce, şu önkoşulları doldurun:

1. Xcode 'da **imzalama & özellikleri**' ne gidin. **+ Capability**' yi seçip **anında iletme bildirimleri**' ni seçerek bir yetenek ekleyin.
2. **+ Capability**' yi seçerek başka bir yetenek ekleyin ve ardından **arka plan modlarını** seçin.
3. **Arka plan modları** altında, **IP üzerinden ses** ve **uzak bildirimler** onay kutularını seçin.

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Xcode 'da nasıl özellik ekleneceğini gösteren ekran görüntüsü." lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Anında iletme bildirimleri için kaydolun

Anında iletme bildirimlerine kaydolmak için bir `registerPushNotification()` `CallAgent` cihaz kayıt belirtecine sahip bir örneği çağırın.

Başarılı başlatmadan sonra anında iletme bildirimlerinin kaydının gerçekleşmesi gerekir. `callAgent`Nesne yok edildiğinde, bu, `logout` anında iletme bildirimlerinin kaydını otomatik olarak siler.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Anında iletme bildirimlerini işle
Gelen çağrılar için anında iletme bildirimleri almak için, `handlePushNotification()` `CallAgent` Sözlük yüküne sahip bir örneği çağırın.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>Anında iletme bildirimlerinin kaydını sil

Uygulamalar, anında iletme bildiriminin kaydını dilediğiniz zaman açabilir. Yalnızca metodunu çağırın `unregisterPushNotification` `CallAgent` .

> [!NOTE]
> Uygulamalar, oturum kapatma sırasında anında iletme bildirimlerinin kaydını otomatik olarak içermez.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>Orta çağrı işlemleri gerçekleştirin

Video ve ses ile ilgili ayarları yönetmek için bir çağrı sırasında çeşitli işlemler gerçekleştirebilirsiniz.

### <a name="mute-and-unmute"></a>Sessiz ve aç

Yerel uç noktanın sesini kapatmak veya sesini açmak için `mute` ve `unmute` zaman uyumsuz API 'leri kullanabilirsiniz.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Yerel uç noktayı zaman uyumsuz olarak açmak için aşağıdaki kodu kullanın.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Yerel video göndermeyi başlatma ve durdurma

Bir çağrıda bulunan diğer katılımcılara yerel video göndermeye başlamak için API 'yi kullanın `startVideo` ve ile geçiş yapın `localVideoStream` `camera` .

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Video göndermeye başladıktan sonra `LocalVideoStream` örnek, `localVideoStreams` koleksiyon bir çağrı örneğine eklenir.

```swift

call.localVideoStreams[0]

```

Yerel videoyu durdurmak için, `localVideoStream` çağrısından döndürülen örneği geçirin `call.startVideo` . Bu, zaman uyumsuz bir eylemdir.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Uzak katılımcıları yönetme

Tüm uzak katılımcılar tür tarafından temsil edilir `RemoteParticipant` ve `remoteParticipants` bir çağrı örneğindeki koleksiyon aracılığıyla kullanılabilir.

### <a name="list-participants-in-a-call"></a>Bir çağrıda katılımcıları listeleme

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Uzak katılımcı özelliklerini al

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Çağrıya katılımcı ekleme

Bir çağrıya (Kullanıcı veya telefon numarası) bir katılımcı eklemek için komutunu çağırabilirsiniz `addParticipant` . Bu komut, zaman uyumlu olarak uzak bir katılımcı örneğini döndürür.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Bir katılımcıyı çağrıdan kaldırma
Bir katılımcıyı bir çağrıdan (Kullanıcı veya telefon numarası) kaldırmak için API 'yi çağırabilirsiniz `removeParticipant` . Bu işlem zaman uyumsuz olarak çözümlenir.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Uzak katılımcı video akışlarını işle

Uzak katılımcılar, bir çağrı sırasında video veya ekran paylaşımı başlatabilir.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Uzaktan katılımcıların video paylaşma veya ekran paylaşma akışlarını işleme

Uzak katılımcıların akışlarını listelemek için, `videoStreams` koleksiyonları inceleyin.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>Uzak video akışı özelliklerini al

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>Uzak katılımcı akışlarını işle

Uzak katılımcı akışlarını işlemeye başlamak için aşağıdaki kodu kullanın.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Uzak video Oluşturucu yöntemlerini ve özelliklerini al

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>Cihazları yönetme

`DeviceManager` ses veya video akışlarını aktarmak için bir çağrıda kullanılabilecek yerel cihazları listelemenizi sağlar. Ayrıca, kullanıcıdan bir mikrofona veya kameraya erişmek için izin isteme olanağı da sağlar. `deviceManager` `callClient` Nesnesine erişebilirsiniz.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Yerel cihazları listeleme

Yerel cihazlara erişmek için, Cihaz Yöneticisi 'nde listeleme yöntemlerini kullanabilirsiniz. Sabit listesi, zaman uyumlu bir işlemdir.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>Varsayılan mikrofonu veya konuşmacıyı ayarlama

Bir arama başlatıldığında kullanılacak varsayılan bir cihaz ayarlamak için Cihaz Yöneticisi ' ni kullanabilirsiniz. Yığın Varsayılanları ayarlanmamışsa, Iletişim Hizmetleri işletim sistemi varsayılanlarına geri döner.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>Yerel kamera önizlemesi al

`Renderer`Yerel kameranızdan bir akış işlemeye başlamak için ' i kullanabilirsiniz. Bu akış diğer katılımcılara gönderilmez; Bu, yerel bir önizleme akışımıza sahiptir. Bu, zaman uyumsuz bir eylemdir.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>Yerel kamera önizleme özelliklerini al

İşleyici, işleme denetlemenizi sağlayan özellikler ve Yöntemler kümesine sahip.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>Bildirimlere abone olun

Değerler değiştiğinde bildirilmesi için özelliklerin ve koleksiyonların çoğuna abone olabilirsiniz.

### <a name="properties"></a>Özellikler
Olaylara abone olmak için `property changed` aşağıdaki kodu kullanın.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Koleksiyonlar
Olaylara abone olmak için `collection updated` aşağıdaki kodu kullanın.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
