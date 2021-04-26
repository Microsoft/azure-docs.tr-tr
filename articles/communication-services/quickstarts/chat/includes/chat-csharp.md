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
ms.openlocfilehash: 4c8bd66dde54ff90ea2191fba58f10c87c45cf68
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958338"
---
## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunları yaptığınızdan emin olun:
- Etkin abonelikle bir Azure hesabı oluşturun. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Visual Studio 'yu](https://visualstudio.microsoft.com/downloads/) yükler
- Bir Azure Iletişim Hizmetleri kaynağı oluşturun. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../create-communication-resource.md). Bu hızlı başlangıç için kaynak **uç** noktanızı kaydetmeniz gerekir.
- Bir [Kullanıcı erişim belirteci](../../access-tokens.md). Kapsamı "sohbet" olarak ayarladığınızdan emin olun ve belirteç dizesinin yanı sıra Kullanıcı kimliği dizesini de unutmayın.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-c-application"></a>Yeni bir C# uygulaması oluşturma

Konsol penceresinde (cmd, PowerShell veya Bash gibi), `dotnet new` adıyla yeni bir konsol uygulaması oluşturmak için komutunu kullanın `ChatQuickstart` . Bu komut, tek bir kaynak dosyası olan basit bir "Merhaba Dünya" C# projesi oluşturur: **program. cs**.

```console
dotnet new console -o ChatQuickstart
```

Dizininizi yeni oluşturulan uygulama klasörüyle değiştirin ve `dotnet build` uygulamanızı derlemek için komutunu kullanın.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Paketi yükler

.NET için Azure Iletişimi sohbet SDK 'sını yükler

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0
```

## <a name="object-model"></a>Nesne modeli

Aşağıdaki sınıflar, C# için Azure Communication Services sohbet SDK 'sının önemli özelliklerinden bazılarını idare edebilir.

| Ad                                  | Açıklama                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Bu sınıf, sohbet işlevselliği için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve iş parçacıklarını oluşturmak, almak ve silmek için kullanın. |
| ChatThreadClient | Bu sınıf, sohbet Iş parçacığı işlevselliği için gereklidir. ChatClient aracılığıyla bir örnek elde edersiniz ve bu örneği kullanarak ileti gönderme/alma/güncelleştirme/silme, katılımcıları ekleme/kaldırma/alma, yazma bildirimleri gönderme ve okundu bilgisi gönderme. |

## <a name="create-a-chat-client"></a>Sohbet istemcisi oluşturma

Bir sohbet istemcisi oluşturmak için, Iletişim Hizmetleri uç noktanızı ve önkoşul adımlarının bir parçası olarak oluşturulan erişim belirtecini kullanacaksınız. `CommunicationIdentityClient`Kullanıcı oluşturmak ve sohbet istemcinize geçirilecek bir belirteç vermek Için kimlik SDK 'sının sınıfını kullanmanız gerekir.

[Kullanıcı erişim belirteçleri](../../access-tokens.md)hakkında daha fazla bilgi edinin.

Bu hızlı başlangıç, sohbet uygulamanız için belirteçleri yönetmek üzere bir hizmet katmanı oluşturmayı kapsamaz, ancak önerilir. [Sohbet mimarisi](../../../concepts/chat/concepts.md) hakkında daha fazla bilgi edinin

Aşağıdaki kod parçacıklarını kopyalayın ve kaynak dosyasına yapıştırın: **program. cs**
```csharp
using Azure;
using Azure.Communication;
using Azure.Communication.Chat;
using System;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Sohbet iş parçacığı başlatma

`createChatThread`Sohbet iş parçacığı oluşturmak Için chatClient üzerindeki yöntemi kullanın
- `topic`Bu sohbete bir konu vermek için kullanın; Konu, işlevi kullanılarak sohbet iş parçacığı oluşturulduktan sonra güncelleştirilemeyebilir `UpdateTopic` .
- `participants` `ChatParticipant` Sohbet iş parçacığına eklenecek nesne listesini geçirmek için özelliğini kullanın. `ChatParticipant`Nesne bir nesne ile başlatılır `CommunicationIdentifier` . `CommunicationIdentifier` , veya türünde olabilir `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` `PhoneNumberIdentifier` . Örneğin, bir nesneyi almak için `CommunicationIdentifier` , bir [Kullanıcı oluşturmak](../../access-tokens.md#create-an-identity) için aşağıdaki yönergeyi uygulayarak oluşturduğunuz bir erişim kimliği geçirmeniz gerekir

Yönteminden yanıt nesnesi `createChatThread` `chatThread` ayrıntıları içerir. Katılımcı ekleme, ileti gönderme, ileti silme, vb. gibi sohbet iş parçacığı işlemleriyle etkileşimde bulunmak için istemci `chatThreadClient` örneğinin, `GetChatThreadClient` istemci üzerindeki yöntemi kullanılarak örneği oluşturulması gerekir `ChatClient` .

```csharp
var chatParticipant = new ChatParticipant(identifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Sohbet iş parçacığı istemcisi al
`GetChatThreadClient`Yöntemi, zaten var olan bir iş parçacığı için bir iş parçacığı istemcisi döndürür. Oluşturulan iş parçacığında işlem gerçekleştirmek için kullanılabilir: üye ekleme, ileti gönderme vb. ThreadId, mevcut sohbet iş parçacığının benzersiz KIMLIĞIDIR.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId: threadId);
```

## <a name="list-all-chat-threads"></a>Tüm sohbet iş parçacıklarını Listele
`GetChatThreads`Kullanıcının parçası olduğu tüm sohbet iş parçacıklarını almak için kullanın.

```csharp
AsyncPageable<ChatThreadItem> chatThreadItems = chatClient.GetChatThreadsAsync();
await foreach (ChatThreadItem chatThreadItem in chatThreadItems)
{
    Console.WriteLine($"{ chatThreadItem.Id}");
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Sohbet iş parçacığına ileti gönderin

`SendMessage`Bir iş parçacığına ileti göndermek için kullanın.

- `content`İleti için içerik sağlamak üzere kullanın, bu gereklidir.
- `type`İletinin içerik türü için ' text ' veya ' HTML ' gibi kullanın. Belirtilmemişse, ' text ' ayarlanır.
- `senderDisplayName`Gönderenin görünen adını belirtmek için kullanın. Belirtilmemişse boş dize ayarlanır.

```csharp
SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Sohbet iş parçacığından sohbet iletileri alma

Sohbet `GetMessages` iş parçacığı istemcisinde belirtilen aralıklarda yöntemi yoklayarak sohbet iletileri alabilirsiniz.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Content.Message}");
}
```

`GetMessages` isteğe bağlı bir `DateTimeOffset` parametre alır. Bu fark belirtilmişse, sonrasında alınan, güncellenen veya silinen iletileri alırsınız. Bu iletilerin, zaman içinde geçen ancak döndürülmeden sonra düzenlendiğini veya kaldırılmadan önce alındığını unutmayın.

`GetMessages` ve kullanarak iletide gerçekleşen tüm düzenleme veya silme işlemleri dahil olmak üzere iletinin en son sürümünü döndürür `UpdateMessage` `DeleteMessage` . Silinen iletiler için, `chatMessage.DeletedOn` iletinin silindiğini gösteren bir tarih saat değeri döndürür. Düzenlenen iletiler için, `chatMessage.EditedOn` iletinin ne zaman düzenlendiğini gösteren bir tarih saat döndürür. İleti oluşturma özgün zamanına kullanılarak erişilebilir `chatMessage.CreatedOn` ve iletileri sıralamak için kullanılabilir.

`GetMessages` tarafından tanımlanabilecek farklı ileti türlerini döndürür `chatMessage.Type` . Bu türler şunlardır:

- `Text`: Bir iş parçacığı üyesi tarafından gönderilen normal sohbet iletisi.

- `Html`: Biçimli bir SMS mesajı. Iletişim Hizmetleri kullanıcılarının şu anda zengin metin iletileri gönderemediğini unutmayın. Bu ileti türü, takımlar tarafından kullanıcılara, takımlar birlikte çalışma senaryolarında Iletişim Hizmetleri kullanıcılarına gönderilen iletiler tarafından desteklenir.

- `TopicUpdated`: Konunun güncelleştirildiğini belirten sistem iletisi. özelliğinin

- `ParticipantAdded`: Sohbet iş parçacığına bir veya daha fazla katılımcı eklendiğini belirten sistem iletisi. özelliğinin

- `ParticipantRemoved`: Bir katılımcının sohbet iş parçacığından kaldırıldığını belirten sistem iletisi.

Daha ayrıntılı bilgi için bkz. [Ileti türleri](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Sohbet iş parçacığına katılımcı olarak Kullanıcı ekleme

Bir iş parçacığı oluşturulduktan sonra, bundan sonra kullanıcı ekleyebilir ve kaldırabilirsiniz. Kullanıcıları ekleyerek, onlara iş parçacığına ileti gönderebilecek ve diğer katılımcı ekleyebilir/kaldırabilirler erişim izni verirsiniz. Çağrılmadan önce `AddParticipants` , bu kullanıcı için yeni bir erişim belirteci ve kimliği aldığınızdan emin olun. Kullanıcının sohbet istemcisini başlatması için bu erişim belirtecine ihtiyacı olacak.

`AddParticipants`Sohbet iş parçacığına bir veya daha fazla katılımcı eklemek için kullanın. Her iş parçacığı katılımcısı için desteklenen öznitelikler şunlardır:
- `communicationUser`, gerekli, iş parçacığı katılımcısının kimliğidir.
- `displayName`, isteğe bağlı, iş parçacığı katılımcısı için görünen addır.
- `shareHistoryTime`, isteğe bağlı, sohbet geçmişinin katılımcıyla paylaştığı zaman.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants: participants);
```

## <a name="get-thread-participants"></a>İş parçacığı katılımcıları al

`GetParticipants`Sohbet iş parçacığının katılımcılarını almak için kullanın.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-read-receipt"></a>Okundu bilgisi gönder

`SendReadReceipt`İletinin Kullanıcı tarafından okunduğunu diğer katılımcılara bildirmek için kullanın.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId: messageId);
```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı komut ile uygulama dizininizden çalıştırın `dotnet run` .

```console
dotnet run
```
