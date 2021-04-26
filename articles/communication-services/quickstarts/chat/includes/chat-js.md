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
ms.openlocfilehash: 322f54e4fa2e8096f68d5bbc216032a5b4e53c22
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726710"
---
## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunları yaptığınızdan emin olun:

- Etkin abonelikle bir Azure hesabı oluşturun. Ayrıntılar için bkz. [ücretsiz hesap oluşturma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/download/) etkin LTS ve bakım LTS sürümlerini yükler.
- Bir Azure Iletişim Hizmetleri kaynağı oluşturun. Ayrıntılar için bkz. [Azure Iletişim kaynağı oluşturma](../../create-communication-resource.md). Bu hızlı başlangıç için **Kaynak uç noktanızı kaydetmeniz** gerekir.
- *Üç* ACS kullanıcısı oluşturun ve bu kullanıcılara bir Kullanıcı erişim belirteci [Kullanıcı erişim belirteci](../../access-tokens.md)verin. Kapsamı **sohbet** olarak ayarladığınızdan emin olun ve **belirteç dizesinin yanı sıra Kullanıcı kimliği dizesini de unutmayın**. Tam demo, iki ilk katılımcı içeren bir iş parçacığı oluşturur ve sonra iş parçacığına üçüncü bir katılımcı ekler.

## <a name="setting-up"></a>Ayarlanıyor

### <a name="create-a-new-web-application"></a>Yeni bir Web uygulaması oluşturma

İlk olarak, Terminal veya komut pencerenizi açın, uygulamanız için yeni bir dizin oluşturun ve bu uygulamaya gidin.

```console
mkdir chat-quickstart && cd chat-quickstart
```

`npm init -y`Dosya **üzerinde** varsayılan ayarlarla birpackage.jsoluşturmak için öğesini çalıştırın.

```console
npm init -y
```

### <a name="install-the-packages"></a>Paketleri yükler

`npm install`JavaScript için aşağıdaki Communication Services SDK 'larını yüklemek için komutunu kullanın.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

`--save`Seçeneği, kitaplığı dosyadaki **package.js** bir bağımlılık olarak listeler.

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Bu hızlı başlangıç, uygulama varlıklarını paketleyip Web Pack kullanır. WebPack, WebPack-CLI ve WebPack-dev-Server NPM paketlerini yüklemek için aşağıdaki komutu çalıştırın ve bunları **üzerindepackage.js** geliştirme bağımlılıkları olarak listeleyin:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

`webpack.config.js`Kök dizinde bir dosya oluşturun. Aşağıdaki yapılandırmayı bu dosyaya kopyalayın:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Uygulamanıza bir `start` betik ekleyin `package.json` , bunu uygulamayı çalıştırmak için kullanacağız. Bölümünün içinde `scripts` `package.json` aşağıdakileri ekleyin:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Projenizin kök dizininde bir **index.html** dosyası oluşturun. JavaScript için Azure Iletişim sohbeti SDK 'sını kullanarak bu dosyayı, sohbet özelliği eklemek için şablon olarak kullanacağız.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Bu hızlı başlangıç için uygulama mantığını içeren **client.js** adlı projenizin kök dizininde bir dosya oluşturun.

### <a name="create-a-chat-client"></a>Sohbet istemcisi oluşturma

Web uygulamanızda bir sohbet istemcisi oluşturmak için, Iletişim hizmeti **uç noktasını** ve önkoşul adımlarının bir parçası olarak oluşturulan **erişim belirtecini** kullanacaksınız.

Kullanıcı erişimi belirteçleri, Azure Iletişim hizmetlerinde doğrudan kimlik doğrulayan istemci uygulamaları oluşturmanızı sağlar. Bu hızlı başlangıç, sohbet uygulamanız için belirteçleri yönetmek üzere bir hizmet katmanı oluşturmayı kapsamaz. Sohbet mimarisi ve erişim belirteçleri hakkında daha fazla bilgi için [Kullanıcı erişim belirteçleri](../../access-tokens.md) hakkında daha fazla bilgi için [sohbet kavramlarına](../../../concepts/chat/concepts.md) bakın.

İçinde **client.js** JavaScript Için Azure iletişim sohbeti SDK 'sını kullanarak sohbet yeteneği eklemek için aşağıdaki kodda uç nokta ve erişim belirtecini kullanın.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- **EndpointUrl** değerini iletişim Hizmetleri kaynak uç noktasıyla değiştirin, daha önce yapmadıysanız [Azure iletişim kaynağı oluşturma](../../create-communication-resource.md) bölümüne bakın.
- **Useraccesstoken** değerini, verilen belirteç ile değiştirin.


### <a name="run-the-code"></a>Kodu çalıştırma

' Deki uygulama konağını yerel bir Web sunucusuna paketetmek için aşağıdaki komutu çalıştırın:
```console
npm run start
```
Tarayıcınızı açın ve adresine gidin http://localhost:8080/ .
Tarayıcınızın içindeki geliştirici araçları konsolunda aşağıdakileri görmeniz gerekir:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Nesne modeli
Aşağıdaki sınıflar ve arabirimler, JavaScript için Azure Communication Services sohbet SDK 'sının önemli özelliklerinden bazılarını idare edebilir.

| Ad                                   | Açıklama                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Bu sınıf, sohbet işlevselliği için gereklidir. Bunu Abonelik bilgileriniz ile birlikte başlatır ve iş parçacıklarını oluşturmak, almak ve silmek için kullanın. |
| ChatThreadClient | Bu sınıf, sohbet Iş parçacığı işlevselliği için gereklidir. ChatClient aracılığıyla bir örnek edinirsiniz ve bu örneği kullanarak ileti gönderebilir/alabilir/güncelleştirebilir/silebilirsiniz, kullanıcıları ekleyin/kaldırın/alın, yazma bildirimleri gönderin ve okundu bilgilerini okuyun, sohbet etkinliklerine abone olun. |


## <a name="start-a-chat-thread"></a>Sohbet iş parçacığı başlatma

`createThread`Bir sohbet iş parçacığı oluşturmak için yöntemini kullanın.

`createThreadRequest` iş parçacığı isteğini anlatmak için kullanılır:

- `topic`Bu sohbete bir konu vermek için kullanın. Konular, sohbet iş parçacığı işlevi kullanılarak oluşturulduktan sonra güncelleştirilir `UpdateThread` .
- `participants`Sohbet iş parçacığına eklenecek katılımcıları listelemek için kullanın.

Çözümlendiğinde, `createChatThread` Yöntem bir döndürür `CreateChatThreadResult` . Bu model `chatThread` , `id` Yeni oluşturulan iş parçacığının öğesine erişebileceğiniz bir özelliği içerir. Daha sonra ' `id` a bir örneğini almak için kullanabilirsiniz `ChatThreadClient` . `ChatThreadClient`Daha sonra, ileti gönderme veya katılımcıları listeleme gibi iş parçacığı içinde işlem yapmak için kullanılabilir.

```JavaScript
async function createChatThread() {
  const createChatThreadRequest = {
    topic: "Hello, World!"
  };
  const createChatThreadOptions = {
    participants: [
      {
        id: '<USER_ID>',
        displayName: '<USER_DISPLAY_NAME>'
      }
    ]
  };
  const createChatTtreadResult = await chatClient.createChatThread(
    createChatThreadRequest,
    createChatThreadOptions
  );
  const threadId = createChatThreadResult.chatThread.id;
  return threadId;
}

createChatThread().then(async threadId => {
  console.log(`Thread created:${threadId}`);
  // PLACEHOLDERS
  // <CREATE CHAT THREAD CLIENT>
  // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
  // <SEND MESSAGE TO A CHAT THREAD>
  // <LIST MESSAGES IN A CHAT THREAD>
  // <ADD NEW PARTICIPANT TO THREAD>
  // <LIST PARTICIPANTS IN A THREAD>
  // <REMOVE PARTICIPANT FROM THREAD>
  });
```

Tarayıcı sekmelerinizi yenilediğinizde konsolunda aşağıdakileri görmeniz gerekir:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Sohbet iş parçacığı istemcisi al

`getChatThreadClient`Yöntemi, `chatThreadClient` zaten var olan bir iş parçacığı için bir döndürür. Oluşturulan iş parçacığında işlem gerçekleştirmek için kullanılabilir: katılımcı ekleme, ileti gönderme vb. ThreadId, mevcut sohbet iş parçacığının benzersiz KIMLIĞIDIR.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Bu kodu `<CREATE CHAT THREAD CLIENT>` **client.js** açıklamanın yerine ekleyin, tarayıcı sekmesini yenileyin ve konsolu kontrol edin, şunları görmeniz gerekir:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="list-all-chat-threads"></a>Tüm sohbet iş parçacıklarını Listele

`listChatThreads`Yöntemi bir türünde döndürür `PagedAsyncIterableIterator` `ChatThreadItem` . Tüm sohbet iş parçacıklarını listelemek için kullanılabilir.
Bir yineleyici, `[ChatThreadItem]` listeleme iş parçacıklarından döndürülen yanıttır

```JavaScript
const threads = chatClient.listChatThreads();
for await (const thread of threads) {
   // your code here
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Sohbet iş parçacığına ileti gönderin

`sendMessage`ThreadID tarafından tanımlanan bir iş parçacığına ileti göndermesi için yöntemini kullanın.

`sendMessageRequest` ileti isteğini anlatmak için kullanılır:

- `content`Sohbet iletisi içeriğini sağlamak için kullanın;

`sendMessageOptions` işlem isteğe bağlı params parametreleri için kullanılır:

- `senderDisplayName`Gönderenin görünen adını belirtmek için kullanın;
- `type`' Text ' veya ' HTML ' gibi ileti türünü belirtmek için kullanın;

`SendChatMessageResult` yanıt ileti göndermekten döndürülen yanıt, iletinin benzersiz KIMLIĞI olan bir ID içeriyor.

```JavaScript
const sendMessageRequest =
{
  content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
  senderDisplayName : 'Jack',
  type: 'text'
};
const sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
const messageId = sendChatMessageResult.id;
```

Bu kodu `<SEND MESSAGE TO A CHAT THREAD>` **client.js** açıklamanın yerine ekleyin, tarayıcı sekmesini yenileyin ve konsolu denetleyin.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Sohbet iş parçacığından sohbet iletileri alma

Gerçek zamanlı sinyalle, yeni gelen iletileri dinlemek ve bellekteki geçerli iletileri uygun şekilde güncelleştirmek üzere abone olabilirsiniz. Azure Iletişim Hizmetleri, [abone olabileceğiniz olayların bir listesini](../../../concepts/chat/concepts.md#real-time-notifications)destekler.

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
  console.log("Notification chatMessageReceived!");
  // your code here
});

```
Bu kodu `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` **client.js** yorum yerine ekleyin.
Tarayıcı sekmesini yenileyin, konsolda bir ileti görürsünüz `Notification chatMessageReceived` ;

Alternatif olarak, belirtilen aralıklarda yöntemi yoklayarak sohbet iletileri alabilirsiniz `listMessages` .

```JavaScript

const messages = chatThreadClient.listMessages();
for await (const message of messages) {
   // your code here
}

```
Bu kodu `<LIST MESSAGES IN A CHAT THREAD>` **client.js** açıklamanın yerine ekleyin.
Sekmeden yenileyin, konsolunda bu sohbet iş parçacığında gönderilen iletilerin listesini bulmanız gerekir.

`listMessages` tarafından tanımlanabilecek farklı ileti türlerini döndürür `chatMessage.type` . 

Daha ayrıntılı bilgi için bkz. [Ileti türleri](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Sohbet iş parçacığına katılımcı olarak Kullanıcı ekleme

Sohbet iş parçacığı oluşturulduktan sonra, bundan sonra kullanıcı ekleyebilir ve kaldırabilirsiniz. Kullanıcıları ekleyerek, onlara sohbet iş parçacığına ileti gönderme ve başka katılımcılar ekleme/kaldırma erişimi verirsiniz.

Yöntemi çağırmadan önce `addParticipants` , bu kullanıcı için yeni bir erişim belirteci ve kimliği aldığınızdan emin olun. Kullanıcının sohbet istemcisini başlatması için bu erişim belirtecine ihtiyacı olacak.

`addParticipantsRequest``participants`sohbet iş parçacığına eklenecek katılımcıları listeleyen istek nesnesini açıklar;
- `id`, gerekli, sohbet iş parçacığına eklenecek olan iletişim tanımlayıcısıdır.
- `displayName`, isteğe bağlı, iş parçacığı katılımcısı için görünen addır.
- `shareHistoryTime`, isteğe bağlı, sohbet geçmişinin katılımcının paylaştığı süredir. Sohbet iş parçacığının başlatılmasından bu yana geçmişi paylaşmak için, bu özelliği, iş parçacığı oluşturma zamanından daha küçük veya ona eşit bir tarih olarak ayarlayın. Katılımcının eklendiği tarihten önce geçmiş paylaşmak için, geçerli tarih olarak ayarlayın. Kısmi geçmişi paylaşmak için, bunu tercih ettiğiniz tarihe ayarlayın.

```JavaScript

const addParticipantsRequest =
{
  participants: [
    {
      id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
      displayName: 'Jane'
    }
  ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
**NEW_PARTICIPANT_USER_ID** [yenı bir kullanıcı kimliğiyle](../../access-tokens.md) değiştirin `<ADD NEW PARTICIPANT TO THREAD>` **client.js** yorum yerine bu kodu ekleyin

## <a name="list-users-in-a-chat-thread"></a>Sohbet iş parçacığında kullanıcıları listeleme
```JavaScript
const participants = chatThreadClient.listParticipants();
for await (const participant of participants) {
   // your code here
}
```
client.jsaçıklama yerine bu kodu ekleyin `<LIST PARTICIPANTS IN A THREAD>` , tarayıcı sekmesini **** yenileyin ve konsolu kontrol edin, bir iş parçacığında kullanıcılar hakkında bilgi görmeniz gerekir.

## <a name="remove-user-from-a-chat-thread"></a>Kullanıcı sohbet iş parçacığından kaldır

Katılımcı eklemeye benzer şekilde, bir sohbet iş parçacığından katılımcıları kaldırabilirsiniz. ' I kaldırmak için, eklediğiniz katılımcıların kimliklerini izlemeniz gerekir.

`removeParticipant` `participant` İş parçacığından kaldırılacak iletişim kullanıcısının olduğu yöntemi kullanın.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
**PARTICIPANT_ID** , önceki adımda kullanılan BIR kullanıcı kimliğiyle değiştirin (<NEW_PARTICIPANT_USER_ID>).
Bu kodu `<REMOVE PARTICIPANT FROM THREAD>` **client.js** açıklamanın yerine ekleyin,
