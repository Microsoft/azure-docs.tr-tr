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
ms.openlocfilehash: e5b5433be4a95a9df9d3b3527473c3004d24acac
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327161"
---
## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) sürüm 8 veya üzeri.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Dağıtılan bir Iletişim Hizmetleri kaynağı ve bağlantı dizesi. [Iletişim Hizmetleri kaynağı oluşturun](../../create-communication-resource.md).
- Bir [Kullanıcı erişim belirteci](../../access-tokens.md). Kapsamı "sohbet" olarak ayarladığınızdan emin olun ve belirteç dizesinin yanı sıra Kullanıcı kimliği dizesini de unutmayın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-java-application"></a>Yeni bir Java uygulaması oluşturma

Terminal veya komut pencerenizi açın ve Java uygulamanızı oluşturmak istediğiniz dizine gidin. Maven-,-hızlı başlangıç şablonundan Java projesi oluşturmak için aşağıdaki komutu çalıştırın.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

' Oluştur ' hedefinin, ArtifactId ile aynı ada sahip bir dizin oluşturduğunu fark edeceksiniz. Bu dizin altında, `src/main/java directory` Proje kaynak kodunu içerir, `src/test/java` Dizin test kaynağını içerir ve pom.xml dosya projenin proje nesne modeli veya [Pod](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)olur.

Uygulamanızın Pod dosyasını Java 8 veya üstünü kullanacak şekilde güncelleştirin:

```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
</properties>
```

### <a name="add-the-package-references-for-the-chat-sdk"></a>Sohbet SDK 'Sı için paket başvurularını ekleme

Pod dosyanızda, `azure-communication-chat` sohbet API 'leriyle pakete başvurun:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-chat</artifactId>
    <version>1.0.0</version>
</dependency>
```

Kimlik doğrulaması için, istemcinizin pakete başvurması gerekir `azure-communication-common` :

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-common</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar ve arabirimler, Java için Azure Communication Services sohbet SDK 'sının önemli özelliklerinden bazılarını idare edebilir.

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Bu sınıf, sohbet işlevselliği için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve iş parçacıklarını oluşturmak, almak ve silmek için kullanın. |
| ChatAsyncClient | Bu sınıf, zaman uyumsuz sohbet işlevselliği için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve iş parçacıklarını oluşturmak, almak ve silmek için kullanın. |
| ChatThreadClient | Bu sınıf, sohbet Iş parçacığı işlevselliği için gereklidir. ChatClient aracılığıyla bir örnek edinirsiniz ve bu örneği kullanarak ileti gönderebilir/alabilir/güncelleştirebilir/silebilirsiniz, kullanıcıları ekleyin/kaldırın/alın, yazma bildirimleri gönderin ve okundu bilgilerini okuyun. |
| ChatThreadAsyncClient | Bu sınıf, zaman uyumsuz sohbet Iş parçacığı işlevselliği için gereklidir. ChatAsyncClient aracılığıyla bir örnek edinirsiniz ve bu örneği kullanarak ileti gönderebilir/alabilir/güncelleştirebilir/silebilirsiniz, kullanıcıları ekleyin/kaldırın/alın, yazma bildirimleri gönderin ve okundu bilgilerini okuyun. |

## <a name="create-a-chat-client"></a>Sohbet istemcisi oluşturma
Bir sohbet istemcisi oluşturmak için, Iletişim hizmeti uç noktasını ve önkoşul adımlarının bir parçası olarak oluşturulan erişim belirtecini kullanacaksınız. Kullanıcı erişimi belirteçleri, Azure Iletişim hizmetlerinde doğrudan kimlik doğrulayan istemci uygulamaları oluşturmanızı sağlar. Bu belirteçleri sunucunuzda oluşturduktan sonra bunları bir istemci cihazına geri geçirin. Belirteci sohbet istemcinize geçirmek için ortak SDK 'daki CommunicationTokenCredential sınıfını kullanmanız gerekir.

[Sohbet mimarisi](../../../concepts/chat/concepts.md) hakkında daha fazla bilgi edinin

İçeri aktarma deyimlerini eklerken, com. Azure. Communication. chat. Implementation ad alanından değil, com. Azure. Communication. chat ve com. Azure. Communication. chat. model ad alanlarından yalnızca içeri aktarmalar eklediğinizden emin olun. Maven aracılığıyla oluşturulan app. Java dosyasında, şununla başlamak için aşağıdaki kodu kullanabilirsiniz:

```Java
package com.communication.quickstart;

import com.azure.communication.chat.*;
import com.azure.communication.chat.models.*;
import com.azure.communication.common.*;
import com.azure.core.http.rest.PagedIterable;

import java.io.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Chat Quickstart");

        // Your unique Azure Communication service endpoint
        String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

        // User access token fetched from your trusted service
        String userAccessToken = "<USER_ACCESS_TOKEN>";

        // Create a CommunicationTokenCredential with the given access token, which is only valid until the token is valid
        CommunicationTokenCredential userCredential = new CommunicationTokenCredential(userAccessToken);

        // Initialize the chat client
        final ChatClientBuilder builder = new ChatClientBuilder();
        builder.endpoint(endpoint)
            .credential(userCredential);
        ChatClient chatClient = builder.buildClient();
    }
}
```

## <a name="start-a-chat-thread"></a>Sohbet iş parçacığı başlatma

`createChatThread`Bir sohbet iş parçacığı oluşturmak için yöntemini kullanın.
`createChatThreadOptions` iş parçacığı isteğini tanımlamakta kullanılır.

- `topic`Bu sohbete bir konu vermek için oluşturucunun parametresini kullanın; Konu, işlevi kullanılarak sohbet iş parçacığı oluşturulduktan sonra güncelleştirilemeyebilir `UpdateThread` .
- `participants`İş parçacığına eklenecek iş parçacığı katılımcılarını listelemek için kullanın. `ChatParticipant`[Kullanıcı erişim belirteci](../../access-tokens.md) hızlı başlangıç bölümünde oluşturduğunuz kullanıcıyı alır.

`CreateChatThreadResult` Yanıt, sohbet iş parçacığı oluşturmaktan döndü.
`getChatThread()` `ChatThread` Oluşturulan iş parçacığında işlemleri gerçekleştirmek için kullanabileceğiniz iş parçacığı istemcisini almak için kullanılabilecek nesneyi döndüren bir yöntem içerir `ChatThreadClient` : katılımcı ekleme, ileti gönderme vb. `ChatThread`Nesnesi ayrıca `getId()` iş PARÇACıĞıNıN benzersiz kimliğini alan yöntemini içerir.

```Java
CommunicationUserIdentifier identity1 = new CommunicationUserIdentifier("<USER_1_ID>");
CommunicationUserIdentifier identity2 = new CommunicationUserIdentifier("<USER_2_ID>");

ChatParticipant firstThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity1)
    .setDisplayName("Participant Display Name 1");

ChatParticipant secondThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity2)
    .setDisplayName("Participant Display Name 2");

CreateChatThreadOptions createChatThreadOptions = new CreateChatThreadOptions("Topic")
    .addParticipant(firstThreadParticipant)
    .addParticipant(secondThreadParticipant);

CreateChatThreadResult result = chatClient.createChatThread(createChatThreadOptions);
String chatThreadId = result.getChatThread().getId();
```

## <a name="list-chat-threads"></a>Sohbet iş parçacıklarını Listele

`listChatThreads`Mevcut sohbet iş parçacıklarının listesini almak için yöntemini kullanın.

```java
PagedIterable<ChatThreadItem> chatThreads = chatClient.listChatThreads();

chatThreads.forEach(chatThread -> {
    System.out.printf("ChatThread id is %s.\n", chatThread.getId());
});
```

## <a name="get-a-chat-thread-client"></a>Sohbet iş parçacığı istemcisi al

`getChatThreadClient`Yöntemi, zaten var olan bir iş parçacığı için bir iş parçacığı istemcisi döndürür. Oluşturulan iş parçacığında işlem gerçekleştirmek için kullanılabilir: katılımcı ekleme, ileti gönderme vb. `chatThreadId` , mevcut sohbet iş parçacığının benzersiz KIMLIĞIDIR.

```Java
ChatThreadClient chatThreadClient = chatClient.getChatThreadClient(chatThreadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Sohbet iş parçacığına ileti gönderin

`sendMessage`ChatThreadId tarafından tanımlanan, yeni oluşturduğunuz iş parçacığına bir ileti göndermek için yöntemini kullanın.
`sendChatMessageOptions` Sohbet iletisi isteğini tanımlamakta kullanılır.

- `content`Sohbet iletisi içeriğini sağlamak için kullanın.
- `type`Sohbet iletisi içerik türünü, metnini veya HTML 'yi belirtmek için kullanın.
- `senderDisplayName`Gönderenin görünen adını belirtmek için kullanın.

Yanıt, `sendChatMessageResult` `id` ILETININ benzersiz kimliği olan bir içerir.

```Java
SendChatMessageOptions sendChatMessageOptions = new SendChatMessageOptions()
    .setContent("Message content")
    .setType(ChatMessageType.TEXT)
    .setSenderDisplayName("Sender Display Name");

SendChatMessageResult sendChatMessageResult = chatThreadClient.sendMessage(sendChatMessageOptions);
String chatMessageId = sendChatMessageResult.getId();
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Sohbet iş parçacığından sohbet iletileri alma

Sohbet `listMessages` iş parçacığı istemcisinde belirtilen aralıklarda yöntemi yoklayarak sohbet iletileri alabilirsiniz.

```Java
chatThreadClient.listMessages().forEach(message -> {
    System.out.printf("Message id is %s.\n", message.getId());
});
```

`listMessages` ileti için. editMessage () ve. deleteMessage () kullanılarak gerçekleşen tüm düzenleme veya silme işlemleri dahil olmak üzere iletinin en son sürümünü döndürür. Silinen iletiler için, `chatMessage.getDeletedOn()` iletinin silindiğini gösteren bir tarih saat değeri döndürür. Düzenlenen iletiler için, `chatMessage.getEditedOn()` iletinin ne zaman düzenlendiğini gösteren bir tarih saat döndürür. İleti oluşturma özgün zamanına kullanılarak erişilebilir `chatMessage.getCreatedOn()` ve iletileri sıralamak için kullanılabilir.

İleti türleri hakkında buradan daha fazla bilgi edinin: [Ileti türleri](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Okundu bilgisi gönder

`sendReadReceipt`Kullanıcı adına bir sohbet iş parçacığına okundu bilgisi olayı göndermek için yöntemini kullanın.
`chatMessageId` okunan sohbet iletisinin benzersiz KIMLIĞIDIR.

```Java
String chatMessageId = message.getId();
chatThreadClient.sendReadReceipt(chatMessageId);
```

## <a name="list-chat-participants"></a>Sohbet katılımcıları listeleyin

`listParticipants`ChatThreadId tarafından tanımlanan sohbet iş parçacığının katılımcılarını içeren bir disk belleğine alma koleksiyonunu almak için kullanın.

```Java
PagedIterable<ChatParticipant> chatParticipantsResponse = chatThreadClient.listParticipants();
chatParticipantsResponse.forEach(chatParticipant -> {
    System.out.printf("Participant id is %s.\n", ((CommunicationUserIdentifier) chatParticipant.getCommunicationIdentifier()).getId());
});
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Sohbet iş parçacığına katılımcı olarak Kullanıcı ekleme

Sohbet iş parçacığı oluşturulduktan sonra, bundan sonra kullanıcı ekleyebilir ve kaldırabilirsiniz. Kullanıcıları ekleyerek, onlara sohbet iş parçacığına ileti gönderme ve başka katılımcılar ekleme/kaldırma erişimi verirsiniz. Bu Kullanıcı için yeni bir erişim belirteci ve kimliği alarak başlamanız gerekir. Addkatılımcılar yöntemini çağırmadan önce, bu kullanıcı için yeni bir erişim belirteci ve kimliği aldığınızdan emin olun. Kullanıcının sohbet istemcisini başlatması için bu erişim belirtecine ihtiyacı olacak.

`addParticipants`İş parçacığına katılımcılar eklemek için yöntemini kullanın.

- `communicationIdentifier`, gerekli, [Kullanıcı erişim belirteci](../../access-tokens.md) hızlı başlangıç bölümünde Communicationıdentityclient tarafından oluşturduğunuz Communicationıdentifier.
- `displayName`, isteğe bağlı, iş parçacığı katılımcısı için görünen addır.
- `shareHistoryTime`, isteğe bağlı, sohbet geçmişinin katılımcının paylaştığı süredir. Sohbet iş parçacığının başlatılmasından bu yana geçmişi paylaşmak için, bu özelliği, iş parçacığı oluşturma zamanından daha küçük veya ona eşit bir tarih olarak ayarlayın. Katılımcının eklendiği tarihten önce geçmiş paylaşmak için, geçerli tarih olarak ayarlayın. Kısmi geçmişi paylaşmak için, bunu gerekli tarihe ayarlayın.

```Java
List<ChatParticipant> participants = new ArrayList<ChatParticipant>();

CommunicationUserIdentifier identity3 = new CommunicationUserIdentifier("<USER_3_ID>");
CommunicationUserIdentifier identity4 = new CommunicationUserIdentifier("<USER_4_ID>");

ChatParticipant thirdThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity3)
    .setDisplayName("Display Name 3");

ChatParticipant fourthThreadParticipant = new ChatParticipant()
    .setCommunicationIdentifier(identity4)
    .setDisplayName("Display Name 4");

participants.add(thirdThreadParticipant);
participants.add(fourthThreadParticipant);

chatThreadClient.addParticipants(participants);
```

## <a name="run-the-code"></a>Kodu çalıştırma

*pom.xml* dosyasını içeren dizine gidin ve aşağıdaki komutu kullanarak projeyi derleyin `mvn` .

```console
mvn compile
```

Ardından, paketini oluşturun.

```console
mvn package
```

`mvn`Uygulamayı yürütmek için aşağıdaki komutu çalıştırın.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
