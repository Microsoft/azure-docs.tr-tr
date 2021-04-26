---
title: Hızlı başlangıç-bir takım toplantısına katılarak
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 49f9bac40ae803f980a22c19fd5d44d85fa99e9e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564684"
---
## <a name="joining-the-meeting-chat"></a>Toplantı sohbetine katılma 

Takımlar birlikte çalışabilirliği etkinleştirildikten sonra, bir Iletişim Hizmetleri kullanıcısı, çağıran SDK 'Yı kullanarak takımlar çağrısını dış Kullanıcı olarak birleştirebilirler. Çağrının katılması, bunları, çağrı üzerinde diğer kullanıcılarla ileti gönderebilecekleri ve buradan toplantı sohbetine katılımcı olarak ekler. Kullanıcının, çağrıya katılmadan önce gönderilen sohbet iletilerine erişimi olmayacaktır. Toplantıya katılabilmek ve sohbet başlatmak için sonraki adımları izleyebilirsiniz.

## <a name="install-the-chat-packages"></a>Sohbet paketlerini yükler

`npm install`JavaScript için gerekli Iletişim Hizmetleri SDK 'larını yüklemek için komutunu kullanın.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

`--save`Seçeneği, kitaplığı dosyadaki **package.js** bir bağımlılık olarak listeler.

## <a name="add-the-teams-ui-controls"></a>Takımlar Kullanıcı arabirimi denetimlerini ekleme

index.html içindeki kodu aşağıdaki kod parçacığıyla değiştirin.
Sayfanın üst kısmındaki metin kutuları, Toplantı bağlamını ve toplantı iş parçacığı KIMLIĞINI girmek için kullanılacaktır. Belirtilen toplantıya katılması için ' takımları toplantıya katılarak ' düğmesi kullanılacaktır.
Sayfanın alt kısmında bir sohbet açılır penceresi görüntülenir. Bu, Toplantı iş parçacığına ileti göndermek için kullanılabilir ve ACS kullanıcısı üye olduğu sürece iş parçacığı üzerinde gönderilen herhangi bir ileti gerçek zamanlı olarak görüntülenir.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>Takımlar Kullanıcı arabirimi denetimlerini etkinleştir

client.js dosyasının içeriğini aşağıdaki kod parçacığıyla değiştirin.

Parçacığı içinde, Değiştir 
- `SECRET CONNECTION STRING` Iletişim hizmetinizin bağlantı dizesiyle 
- `ENDPOINT URL` Iletişim hizmetinizin uç nokta URL 'si ile

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Bir Iletişim Hizmetleri kullanıcısına yönelik sohbet iş parçacığını toplantıya yönelik bir takım alın

Toplantı bağlantısı ve sohbet, Graph API 'Leri kullanılarak, [grafik belgelerinde](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)ayrıntılı şekilde alınabilir. SDK 'Yı çağıran Iletişim Hizmetleri, bir tam takımlar toplantısı bağlantısını kabul eder. Bu bağlantı, `onlineMeeting` [grafiğin API 'leri](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)ile [ `joinWebUrl` özelliği](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) altında erişilebilen kaynağın bir parçası olarak döndürülür `threadId` . Yanıt, içeren bir nesnesine sahip olur `chatInfo` `threadID` . 

Gerekli toplantı bilgilerini ve iş parçacığı KIMLIĞINI, ekipteki **toplantıya katılarak** davet etme URL 'sinden de alabilirsiniz.
Bir ekip toplantısı bağlantısı şöyle görünür: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . , `threadId` Bağlantı nerede olacaktır `meeting_chat_thread_id` . `meeting_chat_thread_id`Kullanılmadan önce bunun atlanmadan emin olun. Aşağıdaki biçimde olmalıdır: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>Kodu çalıştırma

Web paketi kullanıcıları `webpack-dev-server` uygulamanızı derlemek ve çalıştırmak için kullanabilir. Uygulama ana bilgisayarınızı yerel bir Web sunucusuna paketetmek için aşağıdaki komutu çalıştırın:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Tarayıcınızı açın ve adresine gidin http://localhost:8080/ . Şunları görmeniz gerekir:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Tamamlanan JavaScript uygulamasının ekran görüntüsü.":::

Ekip bağlantısını ve iş parçacığı KIMLIĞINI toplantı bağlantısı ve metin kutularına ekleyin. Takımlar toplantısına katılması için *takımlar toplantısına katılarak* düğmesine basın. ACS kullanıcısı toplantıya alındıktan sonra Iletişim Hizmetleri uygulamanızın içinden sohbet edebilirsiniz. Sohbet başlatmak için sayfanın alt kısmındaki kutuya gidin.

> [!NOTE] 
> Yalnızca, takımlarla birlikte çalışabilirlik senaryolarında ileti gönderme, alma ve düzenlemeyle ilgili senaryolar desteklenir. Takım toplantılarından diğer kullanıcıları eklemek veya kaldırmak için Kullanıcı ekleme ve yazma göstergeleri ve Iletişim Hizmetleri gibi diğer özellikler henüz desteklenmemektedir.  
