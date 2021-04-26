---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590193"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Çağıran istemciyi etkinleştirmek için bir Kullanıcı erişim belirteci. Daha fazla bilgi için bkz. [erişim belirteçleri oluşturma ve yönetme](../../access-tokens.md).
- İsteğe bağlı: [uygulamanıza sesli çağrı eklemek](../getting-started-with-calling.md)için hızlı başlangıcı doldurun.

## <a name="install-the-sdk"></a>SDK Yükleme

> [!NOTE]
> Bu belge, ACS çağıran Web SDK 'sını kullanır.

`npm install`JavaScript için çağrı ve ortak SDK 'ları çağıran Azure Iletişim hizmetlerini yüklemek için komutunu kullanın.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Communication Service 'ın SDK 'yi çağıran bazı önemli özelliklerinden bazılarını işler:

| Ad                             | Açıklama                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Çağıran SDK 'ya ana giriş noktası.                                                                       |
| `CallAgent`                        | Çağrıları başlatmak ve yönetmek için kullanılır.                                                                                            |
| `DeviceManager`                    | Medya cihazlarını yönetmek için kullanılır.                                                                                           |
| `AzureCommunicationTokenCredential` | `CommunicationTokenCredential`Örneğini oluşturmak için kullanılan arabirimini uygular `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Bir CallClient örneği başlatın, CallAgent örneği oluşturun ve deviceManager 'a erişin

Yeni bir `CallClient` örnek oluşturun. Bunu, bir günlükçü örneği gibi özel seçeneklerle yapılandırabilirsiniz.

Bir `CallClient` örneğiniz olduğunda, örnek `CallAgent` üzerinde yöntemini çağırarak bir örnek oluşturabilirsiniz `createCallAgent` `CallClient` . Bu, zaman uyumsuz bir `CallAgent` örnek nesnesi döndürüyor.

`createCallAgent`Yöntemi `CommunicationTokenCredential` bir bağımsız değişken olarak kullanır. Bir [Kullanıcı erişim belirtecini](../../access-tokens.md)kabul eder.

`getDeviceManager` `CallClient` Uygulamasına erişmek için örneğinde yöntemini kullanabilirsiniz `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Bir çağrı yerleştir

Bir çağrı oluşturup başlatmak için, üzerindeki API 'lerden birini kullanın `callAgent` ve Iletişim Hizmetleri kimlik SDK 'sı aracılığıyla oluşturduğunuz bir Kullanıcı sağlayın.

Çağrı oluşturma ve başlatma zaman uyumludur. Çağrı örneği, çağrı olaylarına abone olmanızı sağlar.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Bir kullanıcı veya PSTN 'e 1: n çağrısı yerleştir

Başka bir Iletişim Hizmetleri kullanıcısını çağırmak için, `startCall` üzerinde yöntemini kullanın `callAgent` ve `CommunicationUserIdentifier` [iletişim Hizmetleri yönetim kitaplığıyla oluşturduğunuz](../../access-tokens.md)alıcıyı geçirin.

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Bir ortak anahtarlı telefon ağına (PSTN) bir çağrı yerleştirmek için, `startCall` üzerinde yöntemini kullanın `callAgent` ve alıcıyı geçirin `PhoneNumberIdentifier` . Iletişim Hizmetleri kaynağınız, PSTN çağırmaya izin verecek şekilde yapılandırılmalıdır.

Bir PSTN numarası çağırdığınızda, alternatif arayan KIMLIĞINIZI belirtin. Alternatif bir arayan KIMLIĞI, bir PSTN çağrısında çağrıyı belirleyen bir telefon numarasıdır (E. 164 standardını temel alır). Bu, arama alıcısının gelen bir çağrı için gördüğü telefon numarasıdır.

> [!NOTE]
> PSTN çağrısı şu anda özel önizlemededir. Erişim için, [erken benimseyen programına uygulayın](https://aka.ms/ACS-EarlyAdopter).

1:1 çağrısı için aşağıdaki kodu kullanın:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

1: n çağrısı için aşağıdaki kodu kullanın:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Video kamera ile 1:1 çağrısı yerleştirme

> [!IMPORTANT]
> Şu anda birden fazla giden yerel video akışı yok.

Bir video araması yerleştirmek için, içindeki yöntemi kullanarak yerel kameraları listeleyebilirsiniz `getCameras()` `deviceManager` .

Bir kamerayı seçtikten sonra bir örnek oluşturmak için bunu kullanın `LocalVideoStream` . `videoOptions`Yöntemine dizi içindeki bir öğe olarak geçirin `localVideoStream` `startCall` .


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Çağrınızdan bağlantı yapıldığında, otomatik olarak seçilen kameradan diğer katılımcıya bir video akışı göndermeye başlar. Bu, `Call.Accept()` video seçenekleri ve video seçenekleri için de geçerlidir `CallAgent.join()` .

### <a name="join-a-group-call"></a>Grup çağrısına katılır

> [!NOTE]
> `groupId`Parametresi sistem meta verileri olarak değerlendirilir ve Microsoft tarafından sistemi çalıştırmak için gereken işlemler için kullanılabilir. Değerde kişisel verileri eklemeyin `groupId` . Microsoft bu parametreyi kişisel veriler olarak kabul etmez ve içeriği Microsoft çalışanlarına görünebilir veya uzun süreli olarak depolanabilir.
>
> `groupId`Parametresi, VERILERIN GUID biçiminde olmasını gerektirir. Sistemlerinizde kişisel verileri kabul etmeyen rastgele oluşturulmuş GUID 'Ler kullanmanızı öneririz.
>

Yeni bir grup çağrısı başlatmak veya devam eden bir grup çağrısına katmak için yöntemini kullanın `join` ve bir özelliği olan bir nesne geçirin `groupId` . `groupId`Değer BIR GUID olmalıdır.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Takımlar toplantısına katılarak
> [!NOTE]
> Bu API, geliştiriciler için önizleme olarak sunulmuştur ve alacağımız geri bildirimler doğrultusunda değiştirilebilir. Bu API’yi üretim ortamında kullanmayın. Bu API 'yi kullanmak için lütfen ACS çağıran Web SDK ' nın ' Beta ' sürümünü kullanın

Bir ekip toplantısına katmak için `join` yöntemini kullanın ve bir toplantı bağlantısı veya toplantının koordinatlarını geçirin.

Toplantı bağlantısı kullanarak birleştirin:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Toplantı koordinatlarını kullanarak birleştirin:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Gelen bir çağrı al

`callAgent`Örnek, `incomingCall` oturum açma kimliği gelen bir çağrı aldığında olay yayar. Bu olayı dinlemek için aşağıdaki seçeneklerden birini kullanarak abone olun:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`Olay, `incomingCall` kabul edebilir veya reddedebileceğiniz bir örnek içerir.

## <a name="manage-calls"></a>Çağrıları Yönet

Çağrı sırasında, çağrı özelliklerine erişebilir ve video ve ses ayarlarını yönetebilirsiniz.

### <a name="check-call-properties"></a>Çağrı özelliklerini denetle

Bir çağrının benzersiz KIMLIĞINI (dize) al:

   ```js
    const callId: string = call.id;
   ```
Çağrı hakkında bilgi alın:
> [!NOTE]
> Bu API, geliştiriciler için önizleme olarak sunulmuştur ve alacağımız geri bildirimler doğrultusunda değiştirilebilir. Bu API’yi üretim ortamında kullanmayın. Bu API 'yi kullanmak için lütfen ACS çağıran Web SDK ' nın ' Beta ' sürümünü kullanın
   ```js
   const callInfo = call.info;
   ```

' Call ' örneğindeki koleksiyonu inceleyerek, çağrı içindeki diğer katılımcılar hakkında bilgi edinin `remoteParticipants` :

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Gelen çağrının çağrısını belirler:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier``CommunicationIdentifier`türlerden biridir.

Bir çağrının durumunu al:

   ```js
   const callState = call.state;
   ```

   Bu, çağrının geçerli durumunu temsil eden bir dize döndürür:

  - `None`: İlk çağrı durumu.
  - `Connecting`: Bir çağrı yerleştirildiğinde veya kabul edildiğinde ilk geçiş durumu.
  - `Ringing`: Giden bir çağrı için, uzak katılımcılar için bir çağrının çaldırılacağını belirtir. Bu `Incoming` , tarafındadır.
  - `EarlyMedia`: Çağrı bağlanmadan önce bir duyurunun yürütüldüğü durumu gösterir.
  - `Connected`: Çağrının bağlı olduğunu gösterir.
  - `LocalHold`: Çağrının bir yerel katılımcı tarafından beklemeye yerleştirileceğini belirtir. Yerel uç nokta ve uzak katılımcılar arasında medya akışı yapılmaz.
  - `RemoteHold`: Çağrının uzak katılımcı tarafından beklemeye alındığı anlamına gelir. Yerel uç nokta ve uzak katılımcılar arasında medya akışı yapılmaz.
  - `InLobby`: Kullanıcının lobide olduğunu gösterir.
  - `Disconnecting`: Çağrının durumuna geçmeden önce geçiş durumu `Disconnected` .
  - `Disconnected`: Son çağrı durumu. Ağ bağlantısı kaybolursa, durum `Disconnected` iki dakika sonra olarak değişir.

Özelliği inceleyerek bir çağrının neden bitmediğini öğrenin `callEndReason` :

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Özelliği inceleyerek geçerli çağrının gelen veya giden olduğunu öğrenin `direction` . Döndürür `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Geçerli mikrofonun kapalı olup olmadığını denetle. Döndürür `Boolean` .

   ```js
   const muted = call.isMuted;
   ```

Özelliği denetleyerek, ekran paylaşım akışının belirli bir uç noktadan gönderilip gönderilmediğini öğrenin `isScreenSharingOn` . Döndürür `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Koleksiyonu denetleyerek etkin video akışlarını inceleyin `localVideoStreams` . Nesneleri döndürür `LocalVideoStream` .

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Sessiz ve aç

Yerel uç noktanın sesini kapatmak veya sesini açmak için `mute` ve `unmute` zaman uyumsuz API 'leri kullanabilirsiniz:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Yerel video göndermeyi başlatma ve durdurma

Bir videoyu başlatmak için nesne üzerindeki metodunu kullanarak kameraları numaralandırabilirsiniz gerekir `getCameras` `deviceManager` . Ardından, istenen kamerayla yeni bir örneğini oluşturun `LocalVideoStream` ve sonra `LocalVideoStream` nesneyi `startVideo` yöntemine geçirin:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Videoyu göndermeye başarıyla başladıktan sonra, bir `LocalVideoStream` `localVideoStreams` çağrı örneği üzerinde koleksiyona bir örnek eklenir.

```js
call.localVideoStreams[0] === localVideoStream;
```

Yerel videoyu durdurmak için, koleksiyonda bulunan `localVideoStream` örneği geçirin `localVideoStreams` :

```js
await call.stopVideo(localVideoStream);
```

Bir örnek üzerinde çağırarak video gönderirken farklı bir kamera cihazına geçiş yapabilirsiniz `switchSource` `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Uzak katılımcıları yönetme

Tüm uzak katılımcılar, tür ile temsil edilir `RemoteParticipant` ve `remoteParticipants` bir çağrı örneği üzerinde koleksiyon aracılığıyla kullanılabilir.

### <a name="list-the-participants-in-a-call"></a>Bir çağrıda katılımcıları listeleyin

`remoteParticipants`Koleksiyon, bir çağrıda uzak katılımcılar listesini döndürür:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Uzaktan katılımcı özelliklerine erişin

Uzak katılımcılar, ilişkili özellikler ve koleksiyonlar kümesine sahiptir:

- `CommunicationIdentifier`: Uzak katılımcı için tanımlayıcıyı alın. Kimlik `CommunicationIdentifier` türlerden biridir:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Aşağıdaki türlerden biri olabilir `CommunicationIdentifier` :

  - `{ communicationUserId: '<ACS_USER_ID'> }`: ACS kullanıcısını temsil eden nesne.
  - `{ phoneNumber: '<E.164>' }`: E. 164 biçimindeki telefon numarasını temsil eden nesne.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Takımlar kullanıcısını temsil eden nesne.
  - `{ id: string }`: nesne, diğer tanımlayıcı türlerine uymayan tanımlayıcıyı yeniden denerek

- `state`: Uzak bir katılımcının durumunu alın.

  ```js
  const state = remoteParticipant.state;
  ```

  Durum şu olabilir:

  - `Idle`: İlk durum.
  - `Connecting`: Katılımcı çağrıya bağlanırken geçiş durumu.
  - `Ringing`: Katılımcı çalıyor.
  - `Connected`: Katılımcı çağrıya bağlı.
  - `Hold`: Katılımcı tutuluyor.
  - `EarlyMedia`: Bir katılımcının çağrıya bağlanmadan önce oynadığı duyuru.
  - `InLobby`: Uzak katılımcının lobide olduğunu gösterir.
  - `Disconnected`: Son durum. Katılımcının çağrı bağlantısı kesildi. Uzak katılımcı ağ bağlantılarını kaybederse, durumu `Disconnected` iki dakika sonra olarak değişir.

- `callEndReason`: Bir katılımcının çağrıyı neden bıraktı hakkında bilgi edinmek için, özelliği kontrol edin `callEndReason` :

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` durum: uzak bir katılımcının kapalı olup olmadığını öğrenmek Için, özelliği kontrol edin `isMuted` . Döndürür `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` durum: uzak bir katılımcının konuşuyor olup olmadığını öğrenmek Için `isSpeaking` özelliği denetleyin. Döndürür `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Belirli bir katılımcının Bu çağrıda gönderdiği tüm video akışlarını denetlemek için, koleksiyonu kontrol edin `videoStreams` . Nesneleri içerir `RemoteVideoStream` .

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Bu uzak katılımcının görünen adını almak için, `displayName` dizeyi döndüren özelliği inceleyin. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Çağrıya katılımcı ekleme

Çağrıya bir katılımcı (Kullanıcı veya telefon numarası) eklemek için kullanabilirsiniz `addParticipant` . Türlerden birini sağlayın `Identifier` . Zaman uyumlu olarak `remoteParticipant` örneği döndürür. Çağrıya yapılan `remoteParticipantsUpdated` olay, bir katılımcı başarıyla çağrıya eklendiğinde tetiklenir.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Bir katılımcıyı çağrıdan kaldırma

Bir katılımcıyı (Kullanıcı veya telefon numarası) bir çağrıdan kaldırmak için, çağırabilirsiniz `removeParticipant` . Türlerden birini geçirmeniz gerekir `Identifier` . Bu, katılımcı çağrıdan kaldırıldıktan sonra zaman uyumsuz olarak çözümlenir. Katılımcı de `remoteParticipants` koleksiyondan kaldırılır.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Uzak katılımcı video akışlarını işle

Uzak katılımcıların video akışlarını ve ekran paylaşım akışlarını listelemek için, `videoStreams` koleksiyonları inceleyin:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

İşlemek için `RemoteVideoStream` , bu etkinliğe abone olmanız gerekir `isAvailableChanged` . `isAvailable`Özelliği olarak değişirse `true` , uzak katılımcı bir akış gönderiyor. Bu durumda, yeni bir örneğini oluşturun `VideoStreamRenderer` ve ardından `VideoStreamRendererView` zaman uyumsuz yöntemi kullanarak yeni bir örnek oluşturun `createView` .  Daha sonra `view.target` herhangi bir kullanıcı arabirimi öğesine ekleyebilirsiniz.

Uzak bir akış değişikliklerinin kullanılabilirliği her kullanıldığında, belirli bir veya tek tek yok etme seçeneğini belirleyebilir `VideoStreamRenderer` `VideoStreamRendererView` , ancak bu, boş video çerçevesinin görüntülenmesine neden olur.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Uzak video akışı özellikleri

Uzak video akışları aşağıdaki özelliklere sahiptir:

- `id`: Uzak video akışının KIMLIĞI.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Veya olabilir `Video` `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Uzak katılımcı uç noktasının etkin bir şekilde akış gönderiyor olup olmadığı.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer yöntemleri ve özellikleri
`VideoStreamRendererView`Uygulama kullanıcı arabirimine eklenebilecek bir örnek oluşturma uzak video akışını işleyebilir, zaman uyumsuz `createView()` yöntemi kullanın, Stream işleme için ne zaman kullanılabilir olduğunda ÇÖZÜMLENIR ve `target` `video` Dom ağacının herhangi bir yerinden eklenebilir öğeyi temsil eden özelliği olan bir nesne döndürür

  ```js
  videoStreamRenderer.createView()
  ```

`videoStreamRenderer`Ve tüm ilişkili örnekleri atma `VideoStreamRendererView` :

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView yöntemleri ve özellikleri

Bir oluşturduğunuzda `VideoStreamRendererView` , `scalingMode` ve `isMirrored` özelliklerini belirtebilirsiniz. `scalingMode``Stretch`, veya olabilir `Crop` `Fit` . `isMirrored`Belirtilmişse, işlenen akış dikey olarak çevrilmiş.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Her `VideoStreamRendererView` örnek, `target` işleme yüzeyini temsil eden bir özelliğe sahiptir. Bu özelliği uygulama kullanıcı arabirimine ekleyin:

```js
htmlElement.appendChild(view.target);
```

`scalingMode`Yöntemini çağırarak güncelleştirebilirsiniz `updateScalingMode` :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Cihaz yönetimi

`deviceManager`' De, ses ve video akışlarınızı bir çağrıda aktarabilecek yerel cihazları sıralayabilirsiniz. Ayrıca, yerel cihazın mikrofonlarını ve kameralara erişim izni istemek için de kullanabilirsiniz.

`deviceManager`Yöntemini çağırarak erişebilirsiniz `callClient.getDeviceManager()` :

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Yerel cihazları al

Yerel cihazlara erişmek için, üzerinde listeleme yöntemlerini kullanabilirsiniz `deviceManager` . Sabit listesi, zaman uyumsuz bir işlemdir

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Varsayılan mikrofonu ve konuşmacıyı ayarlama

`deviceManager`' De, bir çağrı başlatmak için kullanacağınız varsayılan bir cihaz ayarlayabilirsiniz. İstemci Varsayılanları ayarlanmamışsa, Iletişim Hizmetleri işletim sistemi varsayılanlarını kullanır.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Yerel kamera önizlemesi

`deviceManager` `VideoStreamRenderer` Yerel kameranızdan akışları işlemeye başlamak için ve kullanabilirsiniz. Bu akış diğer katılımcılara gönderilmez; Bu, yerel bir önizleme akışımıza sahiptir.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Kamera ve mikrofona izin iste

Kullanıcıdan kamera ve mikrofon izinleri vermesini iste:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Bu `audio` , ve izinlerinin verilip verilmediğini belirten bir nesne ile çözümlenir `video` :

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Çağrıları Kaydet
> [!NOTE]
> Bu API, geliştiriciler için önizleme olarak sunulmuştur ve alacağımız geri bildirimler doğrultusunda değiştirilebilir. Bu API’yi üretim ortamında kullanmayın. Bu API 'yi kullanmak için lütfen ACS çağıran Web SDK ' nın ' Beta ' sürümünü kullanın

Çağrı kaydı, çekirdek API 'nin genişletilmiş bir özelliğidir `Call` . Önce kayıt özelliği API nesnesini edinmeniz gerekir:

```js
const callRecordingApi = call.api(Features.Recording);
```

Sonra, çağrının kaydedilip kaydedilmediğini denetlemek için `isRecordingActive` özelliğini inceleyin `callRecordingApi` . Döndürür `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Ayrıca, değişiklikleri kaydetmek için abone olabilirsiniz:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="transfer-calls"></a>Çağrıları aktar
> [!NOTE]
> Bu API, geliştiriciler için önizleme olarak sunulmuştur ve alacağımız geri bildirimler doğrultusunda değiştirilebilir. Bu API’yi üretim ortamında kullanmayın. Bu API 'yi kullanmak için lütfen ACS çağıran Web SDK ' nın ' Beta ' sürümünü kullanın

Çağrı aktarımı, çekirdek API 'nin genişletilmiş bir özelliğidir `Call` . Önce Aktarım özelliği API nesnesini almanız gerekir:

```js
const callTransferApi = call.api(Features.Transfer);
```

Çağrı aktarımları üç taraf içerir:

- *Transferor*: aktarım isteğini başlatan kişi.
- *Transferee*: aktarılmakta olan kişi.
- *Aktarım hedefi*: aktarılmakta olan kişi.

Aktarımlar aşağıdaki adımları izler:

1. *Transferor* ve *transferee* arasında bağlı bir çağrı zaten var. Transfere, *transferee* öğesinden *Aktarım hedefine* çağrıyı *aktarmaya karar verir* .
1. *Transfer veya* `transfer` API çağırır.
1. *Transferee* , `accept` `reject` bir olay kullanarak *Aktarım hedefine* veya aktarım isteğine ne olduğunu karar verir `transferRequested` .
1. *Aktarım hedefi* yalnızca *transferee* aktarım isteğini kabul ediyorsa gelen bir çağrı alır.

Geçerli bir çağrıyı aktarmak için API 'yi kullanabilirsiniz `transfer` . `transfer``transferCallOptions`, bir bayrak ayarlamanıza olanak sağlayan isteğe bağlı alır `disableForwardingAndUnanswered` :

- `disableForwardingAndUnanswered = false`: Aktarım *hedefi* aktarım çağrısını yanıtlamadıysa aktarım, aktarım *hedefi* iletme ve yanıtsız ayarları izler.
- `disableForwardingAndUnanswered = true`: Aktarım *hedefi* aktarım çağrısını yanıtmazsa, aktarım denemesi sonlanır.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer`API, ve olaylarına abone olmanızı sağlar `transferStateChanged` `transferRequested` . Bir `transferRequested` olay `call` örneğinden gelir; `transferStateChanged` olay ve aktarım `state` ve `error` bir `transfer` örnekten gelir.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

*Transferee* , veya ' de kullanarak *transferden veya* olayda başlatılan aktarım isteğini kabul edebilir veya reddedebilir `transferRequested` `accept()` `reject()` `transferRequestedEventArgs` . `targetParticipant`İçindeki bilgilere ve yöntemlere erişebilirsiniz `accept` `reject` `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="learn-about-eventing-models"></a>Olay modelleri hakkında bilgi edinin

Geçerli değerleri inceleyin ve gelecekteki değerler için olayları güncelleştirmek üzere abone olun.

### <a name="properties"></a>Özellikler

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Koleksiyonlar

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
