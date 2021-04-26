---
title: include dosyası
description: include dosyası
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: b2c5237f3f7e949edbfb5486a3a17cc6e0a008a4
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106178546"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-chat.md)]

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yaptığınızdan emin olun:

- Etkin abonelikle bir Azure hesabı oluşturun. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio)yüklemek için Android Studio kullanacağız, böylece hızlı başlangıç Için bir Android uygulaması oluşturun.
- Bir Azure Iletişim Hizmetleri kaynağı oluşturun. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../create-communication-resource.md). Bu hızlı başlangıç için **Kaynak uç noktanızı kaydetmeniz** gerekir.
- **İki** iletişim hizmeti kullanıcısı oluşturun ve kullanıcılara bir Kullanıcı erişim belirteci [Kullanıcı erişim belirteci](../../access-tokens.md)verin. Kapsamı **sohbet** olarak ayarladığınızdan emin olun ve **belirteç dizesini ve Kullanıcı kimliği dizesini unutmayın**. Bu hızlı başlangıçta, bir ilk katılımcıya sahip bir iş parçacığı oluşturacağız ve sonra iş parçacığına ikinci bir katılımcı ekleyeceğiz.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-android-application"></a>Yeni bir Android uygulaması oluşturma

1. Android Studio açın ve öğesini seçin `Create a new project` . 
2. Sonraki pencerede `Empty Activity` proje şablonu olarak öğesini seçin.
3. Seçenekleri seçerken `ChatQuickstart` Proje adı olarak girin.
4. İleri ' ye tıklayın ve projenin oluşturulmasını istediğiniz dizini seçin.

### <a name="install-the-libraries"></a>Kitaplıkları yükler

Gerekli Iletişim Hizmetleri bağımlılıklarını yüklemek için Gradle kullanacağız. Komut satırında, projenin kök dizininin içine gidin `ChatQuickstart` . Uygulamanın Build. Gradle dosyasını açın ve şu bağımlılıkları `ChatQuickstart` hedefe ekleyin:

```
implementation 'com.azure.android:azure-communication-common:1.0.0-beta.8'
implementation 'com.azure.android:azure-communication-chat:1.0.0-beta.8'
```

#### <a name="exclude-meta-files-in-packaging-options-in-root-buildgradle"></a>Kök derleme. Gradle içindeki paketleme seçeneklerinde meta dosyaları hariç tutun
```
android {
   ...
    packagingOptions {
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/license'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/notice'
        exclude 'META-INF/ASL2.0'
        exclude("META-INF/*.md")
        exclude("META-INF/*.txt")
        exclude("META-INF/*.kotlin_module")
    }
}
```

#### <a name="alternative-to-install-libraries-through-maven"></a>Yapıyı Maven aracılığıyla kitaplıkları yüklemek için
[Maven](https://maven.apache.org/) derleme sistemini kullanarak kitaplığı projenize aktarmak için, `dependencies` `pom.xml` Yapı kimliğini ve kullanmak istediğiniz sürümü belirterek onu uygulamanızın dosyasının bölümüne ekleyin:

```xml
<dependency>
  <groupId>com.azure.android</groupId>
  <artifactId>azure-communication-chat</artifactId>
  <version>1.0.0-beta.8</version>
</dependency>
```


### <a name="setup-the-placeholders"></a>Yer tutucuları ayarlama

Dosyayı açın ve düzenleyin `MainActivity.java` . Bu hızlı başlangıçta, kodumuzu ' a ekleyecek `MainActivity` ve çıktıyı konsolda görüntülemesi gerekir. Bu hızlı başlangıç, bir kullanıcı arabirimi oluşturmayı gidermez. Dosyanın en üstünde, ve kitaplıklarını içeri aktarın `Communication common` `Communication chat` :

```
import com.azure.android.communication.chat.*;
import com.azure.android.communication.common.*;
```

Aşağıdaki kodu dosyasına kopyalayın `MainActivity` :

```java
    private String secondUserId = "<second_user_id>";
    private String threadId = "<thread_id>";
    private String chatMessageId = "<chat_message_id>";
    private final String sdkVersion = "1.0.0-beta.8";
    private static final String APPLICATION_ID = "Chat Quickstart App";
    private static final String SDK_NAME = "azure-communication-com.azure.android.communication.chat";
    private static final String TAG = "--------------Chat Quickstart App-------------";

    private void log(String msg) {
        Log.i(TAG, msg);
        Toast.makeText(this, msg, Toast.LENGTH_LONG).show();
    }
    
   @Override
    protected void onStart() {
        super.onStart();
        try {
            // <CREATE A CHAT CLIENT>

            // <CREATE A CHAT THREAD>

            // <CREATE A CHAT THREAD CLIENT>

            // <SEND A MESSAGE>

            // <ADD A USER>

            // <LIST USERS>

            // <REMOVE A USER>
            
            // <<SEND A TYPING NOTIFICATION>>
            
            // <<SEND A READ RECEIPT>>
               
            // <<LIST READ RECEIPTS>>
        } catch (Exception e){
            System.out.println("Quickstart failed: " + e.getMessage());
        }
    }
```

Aşağıdaki adımlarda, Azure Iletişim Hizmetleri sohbet kitaplığı 'nı kullanarak yer tutucuları örnek kodla değiştireceksiniz.


### <a name="create-a-chat-client"></a>Sohbet istemcisi oluşturma

Yorumu `<CREATE A CHAT CLIENT>` aşağıdaki kodla değiştirin (içeri aktarma deyimlerini dosyanın en üstüne koyun):

```java
import com.azure.android.communication.chat.ChatAsyncClient;
import com.azure.android.communication.chat.ChatClientBuilder;
import com.azure.android.core.credential.AccessToken;
import com.azure.android.core.http.HttpHeader;
import com.azure.android.core.http.okhttp.OkHttpAsyncClientProvider;
import com.azure.android.core.http.policy.BearerTokenAuthenticationPolicy;
import com.azure.android.core.http.policy.UserAgentPolicy;

final String endpoint = "https://<resource>.communication.azure.com";
final String userAccessToken = "<user_access_token>";

ChatAsyncClient chatAsyncClient = new ChatClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .buildAsyncClient();

```

1. `ChatClientBuilder`Bir örneğini yapılandırmak ve oluşturmak için öğesini kullanın `ChatAsyncClient` .
2. `<resource>`Iletişim Hizmetleri kaynağı ile değiştirin.
3. `<user_access_token>`Geçerli bir Iletişim Hizmetleri erişim belirteciyle değiştirin.

## <a name="object-model"></a>Nesne modeli
Aşağıdaki sınıflar ve arabirimler, JavaScript için Azure Communication Services sohbet SDK 'sının önemli özelliklerinden bazılarını idare edebilir.

| Ad                                   | Açıklama                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient/ChatAsyncClient | Bu sınıf, sohbet işlevselliği için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve iş parçacıklarını oluşturmak, almak ve silmek için kullanın. |
| ChatThreadClient/ChatThreadAsyncClient | Bu sınıf, sohbet Iş parçacığı işlevselliği için gereklidir. ChatClient aracılığıyla bir örnek edinirsiniz ve bu örneği kullanarak ileti gönderebilir/alabilir/güncelleştirebilir/silebilirsiniz, kullanıcıları ekleyin/kaldırın/alın, yazma bildirimleri gönderin ve okundu bilgilerini okuyun, sohbet etkinliklerine abone olun. |

## <a name="start-a-chat-thread"></a>Sohbet iş parçacığı başlatma

`ChatAsyncClient`İlk Kullanıcı ile yeni bir iş parçacığı oluşturmak için sitemizi kullanacağız.

`<CREATE A CHAT THREAD>` açıklamasını aşağıdaki kodla değiştirin:

```java
// A list of ChatParticipant to start the thread with.
List<ChatParticipant> participants = new ArrayList<>();
// The communication user ID you created before, required.
String id = "<user_id>";
// The display name for the thread participant.
String displayName = "initial participant";
participants.add(new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(id))
    .setDisplayName(displayName));

// The topic for the thread.
final String topic = "General";
// Optional, set a repeat request ID.
final String repeatabilityRequestID = "";
// Options to pass to the create method.
CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions()
    .setTopic(topic)
    .setParticipants(participants)
    .setIdempotencyToken(repeatabilityRequestID);

CreateChatThreadResult createChatThreadResult =
    chatAsyncClient.createChatThread(createChatThreadOptions).get();
ChatThreadProperties chatThreadProperties = createChatThreadResult.getChatThreadProperties();
threadId = chatThreadProperties.getId();

```

`<user_id>`Geçerli bir Iletişim Hizmetleri Kullanıcı kimliğiyle değiştirin. `threadId`Sonraki adımlarda tamamlama işleyicisine döndürülen yanıttan öğesini kullanacağız, `<thread_id>` Bu nedenle sınıfındaki içindeki öğesini `threadId` Bu istekten aldığımız ile değiştirin ve uygulamayı yeniden çalıştırın.

## <a name="get-a-chat-thread-client"></a>Sohbet iş parçacığı istemcisi al

Artık bir sohbet iş parçacığı oluşturduğumuz `ChatThreadAsyncClient` , iş parçacığı içinde işlem gerçekleştirmek için bir de elde eteceğiz. `<CREATE A CHAT THREAD CLIENT>` açıklamasını aşağıdaki kodla değiştirin:

```
ChatThreadAsyncClient chatThreadAsyncClient = new ChatThreadClientBuilder()
    .endpoint(endpoint)
    .credentialPolicy(new BearerTokenAuthenticationPolicy((request, callback) ->
        callback.onSuccess(new AccessToken(userAccessToken, OffsetDateTime.now().plusDays(1)))))
    .addPolicy(new UserAgentPolicy(APPLICATION_ID, SDK_NAME, sdkVersion))
    .httpClient(new OkHttpAsyncClientProvider().createInstance())
    .chatThreadId(threadId)
    .buildAsyncClient();

```

## <a name="send-a-message-to-a-chat-thread"></a>Sohbet iş parçacığına ileti gönderin

`<thread_id>`Geçerli bir iş parçacığı kimliğiyle değiştirildiğimiz emin olun, şimdi bu iş parçacığına ileti göndereceğiz.

`<SEND A MESSAGE>` açıklamasını aşağıdaki kodla değiştirin:

```java
// The chat message content, required.
final String content = "Test message 1";
// The display name of the sender, if null (i.e. not specified), an empty name will be set.
final String senderDisplayName = "An important person";
SendChatMessageOptions chatMessageOptions = new SendChatMessageOptions()
    .setType(ChatMessageType.TEXT)
    .setContent(content)
    .setSenderDisplayName(senderDisplayName);

// A string is the response returned from sending a message, it is an id, which is the unique ID of the message.
chatMessageId = chatThreadAsyncClient.sendMessage(chatMessageOptions).get().getId();

```

Aldıktan sonra `chatMessageId` , `<chat_message_id>` `chatMessageId` Hızlı Başlangıç bölümünde daha sonra yöntem kullanımı için ile ve uygulamayı yeniden çalıştırmaya başladık.

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Sohbet iş parçacığına katılımcı olarak Kullanıcı ekleme

`<ADD A USER>` açıklamasını aşağıdaki kodla değiştirin:

```java
// The display name for the thread participant.
displayName = "a new participant";
ChatParticipant participant = new ChatParticipant()
    .setCommunicationIdentifier(new CommunicationUserIdentifier(secondUserId))
    .setDisplayName(secondUserDisplayName);
        
chatThreadAsyncClient.addParticipant(participant);

```

`<second_user_id>`Sınıfında, eklenecek kullanıcının Iletişim Hizmetleri Kullanıcı kimliği ile değiştirin. 

## <a name="list-users-in-a-thread"></a>İş parçacığında kullanıcıları listeleme

`<LIST USERS>` açıklamasını aşağıdaki kodla değiştirin:

```java
// The maximum number of participants to be returned per page, optional.
final int maxPageSize = 10;

// Skips participants up to a specified position in response.
final int skip = 0;

// Options to pass to the list method.
ListParticipantsOptions listParticipantsOptions = new ListParticipantsOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatParticipant> firstPageWithResponse =
    chatThreadAsyncClient.getParticipantsFirstPageWithResponse(listParticipantsOptions, Context.NONE).get();

for (ChatParticipant participant : firstPageWithResponse.getValue()) {
    // You code to handle participant
}

listParticipantsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Aşağıdaki yardımcı yöntemi sınıfına koyun:

```java
void listParticipantsNextPage(String continuationToken, int pageNumber) {
if (continuationToken != null) {
    PagedResponse<ChatParticipant> nextPageWithResponse =
        chatThreadAsyncClient.getParticipantsNextPageWithResponse(continuationToken, Context.NONE).get();
        for (ChatParticipant participant : nextPageWithResponse.getValue()) {
            // You code to handle participant
        }
            
        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="remove-user-from-a-chat-thread"></a>Kullanıcı sohbet iş parçacığından kaldır

`<second_user_id>`Geçerli bir kullanıcı kimliği ile değiştirdiğinizden emin olun, ikinci kullanıcıyı iş parçacığından şimdi kaldıracağız.

`<REMOVE A USER>` açıklamasını aşağıdaki kodla değiştirin:

```java
// Using the unique ID of the participant.
chatThreadAsyncClient.removeParticipant(new CommunicationUserIdentifier(secondUserId)).get();

```

## <a name="send-a-typing-notification"></a>Yazma bildirimi gönder

`<SEND A TYPING NOTIFICATION>` açıklamasını aşağıdaki kodla değiştirin:

```java
chatThreadAsyncClient.sendTypingNotification().get();
```

## <a name="send-a-read-receipt"></a>Okundu bilgisi gönder

`<chat_message_id>`Geçerli bir sohbet ILETISI kimliğiyle değiştirdiğinizden emin olun, şimdi bu ileti için okundu bilgisi göndereceğiz.

`<SEND A READ RECEIPT>` açıklamasını aşağıdaki kodla değiştirin:

```java
chatThreadAsyncClient.sendReadReceipt(chatMessageId).get();
```

## <a name="list-read-receipts"></a>Okundu bilgilerini Listele

`<READ RECEIPTS>` açıklamasını aşağıdaki kodla değiştirin:

```java
// The maximum number of participants to be returned per page, optional.
maxPageSize = 10;
// Skips participants up to a specified position in response.
skip = 0;
// Options to pass to the list method.
ListReadReceiptOptions listReadReceiptOptions = new ListReadReceiptOptions()
    .setMaxPageSize(maxPageSize)
    .setSkip(skip);

PagedResponse<ChatMessageReadReceipt> firstPageWithResponse =
    chatThreadAsyncClient.getReadReceiptsFirstPageWithResponse(listReadReceiptOptions, Context.NONE).get();

for (ChatMessageReadReceipt readReceipt : firstPageWithResponse.getValue()) {
    // You code to handle readReceipt
}

listReadReceiptsNextPage(firstPageWithResponse.getContinuationToken(), 2);

```

Aşağıdaki yardımcı yöntemi sınıfına koyun:
```java
void listReadReceiptsNextPage(String continuationToken, int pageNumber) {
    if (continuationToken != null) {
        PagedResponse<ChatMessageReadReceipt> nextPageWithResponse =
            chatThreadAsyncClient.getReadReceiptsNextPageWithResponse(continuationToken, Context.NONE).get();

        for (ChatMessageReadReceipt readReceipt : nextPageWithResponse.getValue()) {
            // You code to handle readReceipt
        }

        listParticipantsNextPage(nextPageWithResponse.getContinuationToken(), ++pageNumber);
    }
}

```


## <a name="run-the-code"></a>Kodu çalıştırma

Android Studio, projeyi derlemek ve çalıştırmak için Çalıştır düğmesine basın. Konsolunda, koddan çıktıyı ve günlükçü çıkışını ChatClient ' dan görüntüleyebilirsiniz.
