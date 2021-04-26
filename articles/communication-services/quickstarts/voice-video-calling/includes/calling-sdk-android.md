---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 45a772b4a1d65b67f918107fd33135a56f6302f2
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073633"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]


## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Dağıtılan bir Iletişim Hizmetleri kaynağı. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Bir `User Access Token` çağrı istemcisini etkinleştirmek için. [Nasıl yapılır `User Access Token` ](../../access-tokens.md) hakkında daha fazla bilgi edinmek için
- İsteğe bağlı: [uygulamanıza çağrı ekleme ile çalışmaya](../getting-started-with-calling.md) başlama için hızlı başlangıcı doldurun

## <a name="setting-up"></a>Ayarlanıyor

### <a name="install-the-package"></a>Paketi yükler

> [!NOTE]
> Bu belge, çağıran SDK 'nın 1.0.0-Beta. 8 sürümünü kullanır.

Proje seviyesi Build. Gradle ' i bulun ve `mavenCentral()` altındaki depolar listesine eklediğinizden emin olun. `buildscript``allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Ardından, modül düzeyi derlemeniz. Gradle, bağımlılıklar bölümüne aşağıdaki satırları ekleyin

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.8'
    ...
}

```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Azure Communication Service 'ın SDK 'yi çağıran bazı önemli özelliklerinden bazılarını işler:

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient, çağıran SDK 'ya ana giriş noktasıdır.|
| CallAgent | CallAgent, çağrıları başlatmak ve yönetmek için kullanılır. |
| CommunicationTokenCredential | CommunicationTokenCredential, CallAgent örneğini oluşturmak için belirteç kimlik bilgileri olarak kullanılır.|
| Communicationıdentifier | Communicationıdentifier, bir çağrının parçası olabilecek farklı katılımcı türü olarak kullanılır.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>CallClient 'ı başlatın, bir CallAgent oluşturun ve DeviceManager 'a erişin

Örnek oluşturmak için `CallAgent` `createCallAgent` bir örnek üzerinde yöntemini çağırmanız gerekir `CallClient` . Bu, zaman uyumsuz bir `CallAgent` örnek nesnesi döndürüyor.
`createCallAgent`Yöntemi bir `CommunicationUserCredential` [erişim belirtecini](../../access-tokens.md)kapsülleyen bir bağımsız değişken olarak alır.
Öğesine erişmek için `DeviceManager` önce bir callAgent örneği oluşturulmalıdır, sonra `CallClient.getDeviceManager` devicemanager 'ı almak için yöntemini kullanabilirsiniz.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```
Çağıranın görünen adını ayarlamak için şu alternatif yöntemi kullanın:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
DeviceManager deviceManager = callClient.getDeviceManager().get();
```


## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Bir giden çağrı yerleştirip bir grup çağrısına katın

Bir çağrı oluşturmak ve başlatmak için, `CallAgent.startCall()` yöntemini çağırmanız ve `Identifier` Aranan (ler) i sağlamanız gerekir.
Bir grup çağrısına katılabilmek için, `CallAgent.join()` yöntemini çağırmanız ve GroupID sağlamalısınız. Grup kimlikleri GUID veya UUID biçiminde olmalıdır.

Çağrı oluşturma ve başlatma zaman uyumludur. Çağrı örneği, çağrısındaki tüm olaylara abone olmanızı sağlar.

### <a name="place-a-11-call-to-a-user"></a>Kullanıcıya 1:1 çağrısı yerleştir
Başka bir Iletişim Hizmetleri kullanıcısına çağrı yerleştirmek için, `call` yöntemi çağırın `callAgent` ve anahtar ile bir nesne geçirin `communicationUserId` .
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Kullanıcılar ve PSTN ile 1: n çağrısı yerleştir
> [!WARNING]
> Şu anda PSTN çağırma kullanılamıyor

Bir kullanıcıya 1: n çağrısı ve bir PSTN numarası eklemek için, Aranan telefon numarasını belirtmeniz gerekir.
Iletişim Hizmetleri kaynağınız, PSTN çağırmaya izin verecek şekilde yapılandırılmalıdır:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Video kamera ile 1:1 çağrısı yerleştirme
> [!WARNING]
> Şu anda yalnızca bir giden yerel video akışı, bir video ile çağrı yerleştirmek Için desteklenir ve API 'yi kullanarak yerel kameraları listeleyebilirsiniz `deviceManager` `getCameras` .
İstenen bir kamerayı seçtikten sonra bir `LocalVideoStream` örnek oluşturmak ve dizideki bir öğe olarak bir yönteme geçirmek için onu kullanın `videoOptions` `localVideoStream` `call` .
Çağrı bağlandıktan sonra, seçili kameradan diğer katılımcılara otomatik olarak bir video akışı göndermeye başlar.

> [!NOTE]
> Gizlilik kaygıları nedeniyle, yerel olarak önizlenmediğinden video çağrıya paylaşılmaz.
Daha fazla bilgi için bkz. [yerel kamera önizlemesi](#local-camera-preview) .
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameras().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));
// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.startCall(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Grup çağrısına katılır
Yeni bir grup çağrısı başlatmak veya devam eden bir grup çağrısına katmak için, ' JOIN ' metodunu çağırmanız ve bir özelliği olan bir nesne geçirmeniz gerekir `groupId` . Değer bir GUID olmalıdır.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

### <a name="accept-a-call"></a>Çağrıyı kabul et
Bir çağrıyı kabul etmek için, çağırma nesnesinde ' Accept ' metodunu çağırın.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Üzerinde video kamera çağrısını kabul etmek için:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

Gelen çağrı, nesnedeki olaya abone olunarak elde edilebilir `onIncomingCall` `callAgent` :

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```


## <a name="push-notifications"></a>Anında iletme bildirimleri

### <a name="overview"></a>Genel Bakış
Mobil anında iletme bildirimleri, mobil cihazlarda gördüğünüz açılır bildirimlerdir. Çağırmak için VoIP (Internet Protokolü üzerinden ses) anında iletme bildirimleri üzerine odaklanacağız. Anında iletme bildirimleri için kaydolacağız, anında iletme bildirimlerini işleyecek ve sonra anında iletme bildirimlerinin kaydını sileceğiz.

### <a name="prerequisites"></a>Önkoşullar

Bulut mesajlaşma (FCM) ile birlikte ayarlanmış bir Firebase hesabı ve Azure Notification Hub örneğine bağlı olan Firebase bulut mesajlaşma hizmeti. Daha fazla bilgi için bkz. [Iletişim Hizmetleri bildirimleri](../../../concepts/notifications.md) .
Ayrıca öğreticide, uygulamanızı derlemek için Android Studio sürüm 3,6 veya üstünü kullandığınızı varsaymaktadır.

Firebase Cloud Messaging 'ten bildirim iletileri alabilmesi için Android uygulaması için bir izin kümesi gereklidir. `AndroidManifest.xml`Dosyanızda, *<manifest... >* veya etiketinin hemen arkasına aşağıdaki izin kümesini ekleyin *</application>*

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Anında iletme bildirimleri için kaydolun

Anında iletme bildirimlerine kaydolmak için uygulamanın bir `registerPushNotification()` cihaz kayıt belirtecine sahip bir *Callagent* örneğinde çağrı yapması gerekir.

Cihaz kayıt belirtecini almak için, aşağıdaki satırları henüz yoksa bölümüne ekleyerek Firebase SDK 'sını uygulama modülünüzün *Build. Gradle* dosyasına ekleyin `dependencies` :

```
    // Add the SDK for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

Proje düzeyinin *Build. Gradle* dosyasında, şu `dependencies` anda yoksa bölümüne aşağıdakileri ekleyin:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Aşağıdaki eklentiyi, zaten orada değilse dosyanın başına ekleyin:

```
apply plugin: 'com.google.gms.google-services'
```

Araç çubuğunda *Şimdi Eşitle* ' yi seçin. İstemci uygulama örneği için Firebase bulut mesajlaşma SDK 'Sı tarafından oluşturulan cihaz kayıt belirtecini almak için aşağıdaki kod parçacığını ekleyin. Örneğin, aşağıdaki içeri aktarmaları örnek için ana etkinliğin üstbilgisine eklediğinizden emin olun. Bu kod parçacığı, belirteci almak için gereklidir:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Belirteci almak için bu kod parçacığını ekleyin:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w("PushNotification", "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d("PushNotification", "Device Registration token retrieved successfully");
                    }
                });
```
Gelen çağrı anında iletme bildirimleri için cihaz kayıt belirtecini çağıran hizmetler SDK 'Sı ile kaydedin:

```java
String deviceRegistrationToken = "<Device Token from previous section>";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Anında iletme bildirimi işleme

Gelen çağrı anında iletme bildirimlerini almak için, bir *Callagent* örneğinde yük Ile *handlepushnotification ()* çağırın.

Firebase Cloud Messaging 'ten yük almak için, *Firebasemessagingservice* FIREBASE SDK sınıfını genişleten yeni bir hizmet (dosya > yeni > Service > Service) oluşturarak başlayın ve yöntemi geçersiz kılın `onMessageReceived` . Bu yöntem, Firebase Cloud Messaging, uygulamaya anında iletme bildirimi teslim edildiğinde çağrılan olay işleyicisidir.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageDataFromFCM;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d("PushNotification", "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageDataFromFCM = remoteMessage.getData();
        }
    }
}
```
Dosyasına aşağıdaki hizmet tanımını ekleyin `AndroidManifest.xml` <application> :

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

- Yük alındıktan sonra, bir *Callagent* örneği üzerinde *handlepushnotification* yöntemi çağırarak Işlenecek bir Iç *ıncomingcallınformation* nesnesine ayrıştırılabilmesi için *iletişim hizmetleri* SDK 'sına geçirilebilir. `CallAgent`Sınıfında yöntemi çağırarak bir örnek oluşturulur `createCallAgent(...)` `CallClient` .

```java
try {
    IncomingCallInformation notification = IncomingCallInformation.fromMap(pushNotificationMessageDataFromFCM);
    Future handlePushNotificationFuture = callAgent.handlePushNotification(notification).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Anında Iletme bildirimi iletisi işleme başarılı olduğunda ve tüm olay işleyicileri düzgün şekilde kaydedildiğinde, uygulama halkasını hallecektir.

### <a name="unregister-push-notifications"></a>Anında iletme bildirimlerinin kaydını sil

Uygulamalar, anında iletme bildiriminin kaydını dilediğiniz zaman açabilir. `unregisterPushNotification()`Kaydını silmek Için callAgent ' da yöntemi çağırın.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Arama Yönetimi
Arama özelliklerine erişebilir ve video ve sesle ilgili ayarları yönetme çağrısı sırasında çeşitli işlemler gerçekleştirebilirsiniz.

### <a name="call-properties"></a>Çağrı özellikleri

Bu çağrının benzersiz KIMLIĞINI al:

```java
String callId = call.getId();
```

Örnekteki çağrı İnceleme koleksiyonundaki diğer katılımcılar hakkında bilgi edinmek için `remoteParticipant` `call` :

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Çağıran, çağrı gelen kimliği:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Çağrının durumunu al: 

```java
CallState callState = call.getState();
```

Çağrının geçerli durumunu temsil eden bir dize döndürür:
* ' None '-ilk çağrı durumu
* ' Bağlanıyor '-çağrı yerleştirildiğinde veya kabul edildiğinde ilk geçiş durumu
* ' Çalıyor '-giden bir çağrı için-uzak katılımcılar için çağrı çalıyor olduğunu belirtir
* ' EarlyMedia '-çağrı bağlanmadan önce bir duyurunun yürütüldüğü durumu belirtir
* ' Connected '-çağrı bağlı
* ' LocalHold '-çağrı yerel katılımcı tarafından beklemeye koyuyor, Yerel uç nokta ve uzak katılımcı arasında medya akışı yok
* ' RemoteHold '-çağrı uzak bir katılımcı tarafından beklemeye koyuyor, Yerel uç nokta ve uzak katılımcı arasında medya akışı yok
* Çağrının ' bağlantısı kesildi ' durumuna geçmeden önce ' bağlantısı kesiliyor '-geçiş durumu
* ' Bağlantısı kesildi '-son çağrı durumu


Çağrının bitiş nedenini öğrenmek için, özelliği inceleyin `callEndReason` . Kod/alt kod içerir: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Geçerli çağrının gelen veya giden bir çağrı olup olmadığını görmek için, `callDirection` Özelliği araştırın:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.Incoming for incoming call
// callDirection == CallDirection.Outgoing for outgoing call
```

Geçerli mikrofonun kapalı olup olmadığını görmek için `muted` özelliği inceleyin:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Geçerli çağrının kaydedilip kaydedilmediğini görmek için, `isRecordingActive` özelliği inceleyin:

```java
boolean recordinggActive = call.getIsRecordingActive();
```

Etkin video akışlarını denetlemek için, koleksiyonu kontrol edin `localVideoStreams` :

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Sessiz ve aç

Yerel uç noktanın sesini kapatmak veya sesini açmak için `mute` ve `unmute` zaman uyumsuz API 'leri kullanabilirsiniz:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Yerel video göndermeyi başlatma ve durdurma

Bir videoyu başlatmak için nesne üzerindeki API 'yi kullanarak kameraları numaralandırabilirsiniz gerekir `getCameraList` `deviceManager` . Ardından `LocalVideoStream` istenen kamerayı geçirmek için yeni bir örnek oluşturun ve `startVideo` API 'de bir bağımsız değişken olarak geçirin:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);
Future startVideoFuture = call.startVideo(currentLocalVideoStream);
startVideoFuture.get();
```

Videoyu göndermeye başarıyla başladıktan sonra, `LocalVideoStream` `localVideoStreams` çağrı örneğindeki koleksiyona bir örnek eklenecektir.

```java
currentLocalVideoStream == call.getLocalVideoStreams().get(0);
```

Yerel videoyu durdurmak için, `LocalVideoStream` örneği koleksiyonda kullanılabilir olarak geçirin `localVideoStreams` :

```java
call.stopVideo(currentLocalVideoStream).get();
```

Bir örneği çağırarak video gönderilirken farklı bir kamera cihazına geçiş yapabilirsiniz `switchSource` `LocalVideoStream` :
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Uzak katılımcılar yönetimi

Tüm uzak katılımcılar türe göre temsil edilir `RemoteParticipant` ve `remoteParticipants` bir çağrı örneğindeki koleksiyon üzerinden kullanılabilir.

### <a name="list-participants-in-a-call"></a>Bir çağrıda katılımcıları listeleme
`remoteParticipants`Koleksiyon verilen çağrıda uzak katılımcılar listesini döndürür:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Uzak katılımcı özellikleri
Verilen herhangi bir uzak katılımcının, kendisiyle ilişkili bir özellikler ve koleksiyonlar kümesi vardır:

* Bu uzak katılımcının tanımlayıcısını alın.
Kimlik, ' Identifier ' türlerinden biridir
```java
CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
```

* Bu uzak katılımcının durumunu alın.
```java
ParticipantState state = remoteParticipant.getState();
```
Durum aşağıdakilerden biri olabilir
* ' Boşta '-ilk durum
* ' EarlyMedia '-katılımcı çağrıya bağlanmadan önce duyuru yürütülür
* ' Çalıyor '-katılımcı çağrısı çalıyor
* Katılımcı çağrıya bağlanırken ' bağlanıyor '-geçiş durumu
* ' Connected '-katılımcı çağrıya bağlı
* ' Hold '-katılımcı beklemeye açık
* ' Inlob'-katılımcı lobide kabul bekleniyor. Şu anda yalnızca takımlar birlikte çalışma senaryosunda kullanılır
* ' Bağlantısı kesildi '-son durum-katılımcının çağrı bağlantısı kesildi


* Bir katılımcının çağrıyı neden bıraktı olduğunu öğrenmek için, `callEndReason` Özelliği araştırın:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Bu uzak katılımcının kapalı olup olmadığını denetlemek için, `isMuted` özelliği inceleyin:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Bu uzak katılımcının konuşuyor olup olmadığını denetlemek için, `isSpeaking` özelliği inceleyin:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Belirli bir katılımcının Bu çağrıda gönderdiği tüm video akışlarını denetlemek için, koleksiyonu kontrol edin `videoStreams` :
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Çağrıya katılımcı ekleme

Çağrıya (bir kullanıcı veya telefon numarası) bir katılımcı eklemek için, çağırabilirsiniz `addParticipant` . Bu, uzak katılımcı örneğini zaman uyumlu olarak döndürür.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Katılımcıyı bir çağrıdan kaldır
Bir katılımcıyı bir çağrıdan (Kullanıcı veya telefon numarası) kaldırmak için, çağırabilirsiniz `removeParticipant` .
Katılımcı çağrıdan kaldırıldıktan sonra bu işlem zaman uyumsuz olarak çözümlenir.
Katılımcı de `remoteParticipants` Koleksiyondan kaldırılacak.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

## <a name="render-remote-participant-video-streams"></a>Uzak katılımcı video akışlarını işle
Uzak katılımcıların video akışlarını ve ekran paylaşım akışlarını listelemek için, `videoStreams` koleksiyonları inceleyin:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Bir uzak katılımcıdan bir oluşturmak için bir `RemoteVideoStream` olaya abone olmanız gerekir `OnVideoStreamsUpdated` .

Olay içinde, `isAvailable` özelliği true olarak değiştirme uzak katılımcının Şu anda bir akış gönderdiğini gösterir. Bu durumda, yeni bir örneğini oluşturun `Renderer` `RendererView` ve ardından zaman uyumsuz API kullanarak yeni bir `createView` uygulama oluşturun ve `view.target` Uygulamanızın Kullanıcı arabirimindeki her yerde ekleyin.

Bir uzak akış değişikliklerinin kullanılabilirliği her kullanıldığında, tüm oluşturucuyu yok edebilir veya tek tek seçebilirsiniz `RendererView` , ancak bu, boş video çerçevesinin görüntülenmesine neden olur.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Uzak video akışı özellikleri
Uzak video akışında birkaç özellik vardır

* `Id` -Uzak video akışının KIMLIĞI
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` -' Video ' veya ' ScreenSharing ' olabilir
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -Uzak katılımcı uç noktasının akışı etkin bir şekilde gönderiyor olduğunu belirtir
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>İşleyici yöntemleri ve özellikleri
API 'Leri izleyen işleyici nesnesi

* `RendererView`Uzak video akışını işlemek için uygulama kullanıcı arabirimine daha sonra eklenebilecek bir örnek oluşturun.
```java
// Create a view for a video stream
renderer.createView()
```
* Dispose oluşturucuyu ve tüm `RendererView` Bu işleyiciyle ilişkili. Tüm ilişkili görünümleri kullanıcı arabiriminden kaldırdığınızda çağrılabilir.
```java
renderer.dispose()
```

* `StreamSize` -uzak video akışının boyutu (genişlik/yükseklik)
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView yöntemleri ve özellikleri
Bir oluştururken `RendererView` , `scalingMode` `mirrored` Bu görünüme uygulanacak ve özelliklerini belirtebilirsiniz: ölçeklendirme modu ' Esnetme ' olabilir | ' Kırp ' | ' Fit ' `mirrored` olarak ayarlanırsa `true` , işlenen akış dikey olarak çevrilcektir.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

Oluşturulan RendererView daha sonra aşağıdaki kod parçacığı kullanılarak uygulama kullanıcı arabirimine iliştirilebilir:
```java
layout.addView(rendererView);
```

Daha sonra, `updateScalingMode` ScalingMode. Esnetden birine sahip RendererView nesnesinde API 'yi çağırarak ölçeklendirme modunu güncelleştirebilirsiniz | ScalingMode. Kırp | ScalingMode. bağımsız değişken olarak sığdırma.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Cihaz yönetimi

`DeviceManager` ses/video akışlarınızı iletmek için bir çağrıda kullanılabilecek yerel cihazları listelemenizi sağlar. Ayrıca, yerel tarayıcı API 'sini kullanarak bir kullanıcıdan mikrofona ve kameraya erişim izni talep etmenizi sağlar.

`deviceManager`Yöntemini çağırarak erişim sağlayabilirsiniz `callClient.getDeviceManager()` .
> [!WARNING]
> Şu anda `callAgent` DeviceManager 'a erişim kazanmak için önce bir nesne örneği oluşturulmalıdır

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Yerel cihazları listeleme

Yerel cihazlara erişmek için Device Manager numaralandırma yöntemlerini kullanabilirsiniz. Sabit listesi, zaman uyumlu bir işlemdir.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Varsayılan mikrofonu/konuşmacıyı ayarla

Aygıt Yöneticisi, bir çağrı başlatılırken kullanılacak varsayılan bir cihaz ayarlamanıza olanak sağlar.
İstemci Varsayılanları ayarlanmamışsa, Iletişim Hizmetleri işletim sistemi varsayılanlarına geri döner.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophones().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakers().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Yerel kamera önizlemesi

`DeviceManager` `Renderer` Yerel kameranızdan akışları işlemeye başlamak için ve kullanabilirsiniz. Bu akış diğer katılımcılara gönderilmez; Bu, yerel bir önizleme akışımıza sahiptir. Bu, zaman uyumsuz bir eylemdir.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Olay modeli
Değerler değiştiğinde bildirilmesi için özelliklerin ve koleksiyonların çoğuna abone olabilirsiniz.

### <a name="properties"></a>Özellikler
Olaylara abone olmak için `property changed` :

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Koleksiyonlar
Olaylara abone olmak için `collection updated` :

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
