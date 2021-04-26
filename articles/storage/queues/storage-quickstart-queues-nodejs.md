---
title: 'Hızlı başlangıç: Azure kuyruk depolama istemci kitaplığı V12-JavaScript'
description: Bir kuyruk oluşturmak ve buna ileti eklemek için Azure kuyruk depolama istemci kitaplığı V12 ' nı JavaScript için nasıl kullanacağınızı öğrenin. Sonra sıradan iletileri okumayı ve silmeyi öğrenin. Ayrıca, bir kuyruğu silmeyi de öğreneceksiniz.
author: twooley
ms.author: twooley
ms.date: 12/13/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 943678debc32116ff7a2e54810c4edcf2d331bd6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534434"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-javascript"></a>Hızlı başlangıç: JavaScript için Azure kuyruk depolama istemci kitaplığı V12

JavaScript için Azure kuyruk depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure kuyruk depolaması, daha sonra almak ve işlemek üzere çok sayıda ileti depolamaya yönelik bir hizmettir. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere bu adımları izleyin.

JavaScript için Azure kuyruk depolama istemci kitaplığı V12 ' nı kullanarak şunları yapın:

- Bir kuyruk oluşturma
- Bir kuyruğa ileti ekleme
- Kuyruktaki iletilere göz atın
- Kuyruktaki bir iletiyi güncelleştirme
- Kuyruktan ileti alma
- Kuyruktaki iletileri silme
- Bir kuyruk silme

Ek kaynaklar:

- [API başvuru belgeleri](/javascript/api/@azure/storage-queue/)
- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue)
- [Paket (NPM)](https://www.npmjs.com/package/@azure/storage-queue)
- [Örnekler](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Azure depolama hesabı- [depolama hesabı oluşturma](../common/storage-account-create.md)
- İşletim sisteminiz için geçerli [Node.js](https://nodejs.org/en/download/) .

## <a name="setting-up"></a>Ayarlanıyor

Bu bölümde, bir projeyi, JavaScript için Azure kuyruk depolama istemci kitaplığı V12 ile çalışacak şekilde hazırlama adımları gösterilmektedir.

### <a name="create-the-project"></a>Proje oluşturma

Adlı bir Node.js uygulaması oluşturun `queues-quickstart-v12`

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), proje için yeni bir dizin oluşturun.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Yeni oluşturulan dizine geçiş yapın `queues-quickstart-v12` .

    ```console
    cd queues-quickstart-v12
    ```

1. Adlı yeni bir metin dosyası oluşturun `package.json` . Bu dosya Node.js projesi tanımlar. Bu dosyayı `queues-quickstart-v12` dizine kaydedin. Dosyanın içeriği aşağıdadır:

    ```json
    {
        "name": "queues-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-queue SDK version 12 to interact with Azure Queue storage",
        "main": "queues-quickstart-v12.js",
        "scripts": {
            "start": "node queues-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-queue": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    İsterseniz alanı için kendi adınızı koyabilirsiniz `author` .

### <a name="install-the-package"></a>Paketi yükler

Yine de `queues-quickstart-v12` dizinde, komutunu kullanarak JavaScript Için Azure kuyruk depolama istemci kitaplığı 'nı yükleyebilirsiniz `npm install` .

```console
npm install
```

Bu komut dosyayı okur `package.json` ve JavaScript paketi Için Azure kuyruk depolama istemci kitaplığı V12 ve bağımlı olduğu tüm kitaplıkları kurar.

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

Proje dizininden:

1. Kod Düzenleyicinizde başka bir yeni metin dosyası açın
1. `require`Azure ve Node.js modüllerini yüklemek için çağrılar ekleme
1. Çok temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

    Kod şu şekildedir:

    ```javascript
    const { QueueClient } = require("@azure/storage-queue");
    const uuidv1 = require("uuid/v1");

    async function main() {
        console.log("Azure Queue Storage client library v12 - JavaScript quickstart sample");
        // Quick start code goes here
    }

    main().then(() => console.log("\nDone")).catch((ex) => console.log(ex.message));

    ```

1. Yeni dosyayı `queues-quickstart-v12.js` `queues-quickstart-v12` dizine kaydedin.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Kuyruk Depolama, çok sayıda iletiyi depolamaya yönelik bir hizmettir. Kuyruk iletisi boyutu 64 KB 'ye kadar olabilir. Bir kuyruk, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti içerebilir. Kuyruklar genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır. Kuyruk depolama, üç tür kaynak sunar:

- Depolama hesabı
- Depolama hesabındaki bir kuyruk
- Kuyruktaki iletiler

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![Kuyruk depolama mimarisi diyagramı](./media/storage-queues-introduction/queue1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki JavaScript sınıflarını kullanın:

- [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient):, `QueueServiceClient` Depolama hesabınızdaki tüm kuyrukları yönetmenizi sağlar.
- [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient): `QueueClient` Sınıfı tek bir kuyruğu ve iletilerini yönetmenizi ve düzenlemenizi sağlar.
- [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage): `QueueMessage` Sınıfı, bir sıraya çağrılırken döndürülen ayrı nesneleri temsil eder [`ReceiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) .

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, JavaScript için Azure kuyruk depolama istemci kitaplığı ile aşağıdaki eylemleri nasıl yapılacağını gösterir:

- [Bağlantı dizesini alma](#get-the-connection-string)
- [Bir kuyruk oluşturma](#create-a-queue)
- [Bir kuyruğa ileti ekleme](#add-messages-to-a-queue)
- [Kuyruktaki iletilere göz atın](#peek-at-messages-in-a-queue)
- [Kuyruktaki bir iletiyi güncelleştirme](#update-a-message-in-a-queue)
- [Kuyruktan ileti alma](#receive-messages-from-a-queue)
- [Kuyruktaki iletileri silme](#delete-messages-from-a-queue)
- [Bir kuyruk silme](#delete-a-queue)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama [bağlantı dizesini yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkeninden depolama hesabı için bağlantı dizesini alır.

Bu kodu işlevin içine ekleyin `main` :

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-queue"></a>Bir kuyruk oluşturma

Yeni sıra için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kuyruk adına bir UUID değeri ekler.

> [!IMPORTANT]
> Kuyruk adları yalnızca küçük harf, sayı ve kısa çizgi içerebilir ve bir harf veya sayı ile başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır. Ad ayrıca 3 ila 63 karakter uzunluğunda olmalıdır. Daha fazla bilgi için bkz. [ad kuyrukları ve meta verileri](/rest/api/storageservices/naming-queues-and-metadata).

Sınıfının bir örneğini oluşturun [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) . Ardından, [`create`](/javascript/api/@azure/storage-queue/queueclient#create-queuecreateoptions-) depolama hesabınızda kuyruğu oluşturmak için yöntemini çağırın.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
// Create a unique name for the queue
const queueName = "quickstart" + uuidv1();

console.log("\nCreating queue...");
console.log("\t", queueName);

// Instantiate a QueueClient which will be used to create and manipulate a queue
const queueClient = new QueueClient(AZURE_STORAGE_CONNECTION_STRING, queueName);

// Create the queue
const createQueueResponse = await queueClient.create();
console.log("Queue created, requestId:", createQueueResponse.requestId);
```

### <a name="add-messages-to-a-queue"></a>Bir kuyruğa ileti ekleme

Aşağıdaki kod parçacığı, yöntemini çağırarak kuyruğa ileti ekler [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) . Ayrıca, [`QueueMessage`](/javascript/api/@azure/storage-queue/queuemessage) üçüncü çağrıdan döndürülen öğesini de kaydeder `sendMessage` . Döndürülen `sendMessageResponse` ileti içeriğini programda daha sonra güncelleştirmek için kullanılır.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
console.log("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.sendMessage("First message");
await queueClient.sendMessage("Second message");
const sendMessageResponse = await queueClient.sendMessage("Third message");

console.log("Messages added, requestId:", sendMessageResponse.requestId);
```

### <a name="peek-at-messages-in-a-queue"></a>Kuyruktaki iletilere göz atın

Yöntemi çağırarak kuyruktaki iletilere göz atın [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) . Bu yöntem, sıranın önüne bir veya daha fazla ileti alır ancak iletinin görünürlüğünü değiştirmez.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
console.log("\nPeek at the messages in the queue...");

// Peek at messages in the queue
const peekedMessages = await queueClient.peekMessages({ numberOfMessages : 5 });

for (i = 0; i < peekedMessages.peekedMessageItems.length; i++) {
    // Display the peeked message
    console.log("\t", peekedMessages.peekedMessageItems[i].messageText);
}
```

### <a name="update-a-message-in-a-queue"></a>Kuyruktaki bir iletiyi güncelleştirme

Yöntemini çağırarak bir iletinin içeriğini güncelleştirin [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--undefined---number--queueupdatemessageoptions-) . Bu yöntem, bir iletinin görünürlük zaman aşımını ve içeriğini değiştirebilir. İleti içeriği, boyutu 64 KB 'a kadar olan bir UTF-8 kodlu dize olmalıdır. Yeni içerikle birlikte, `messageId` `popReceipt` kodda daha önce kaydedilen yanıtı ve yanıtı geçirin. `sendMessageResponse`Özellikler güncelleştirilecek iletiyi belirler.

```javascript
console.log("\nUpdating the third message in the queue...");

// Update a message using the response saved when calling sendMessage earlier
updateMessageResponse = await queueClient.updateMessage(
    sendMessageResponse.messageId,
    sendMessageResponse.popReceipt,
    "Third message has been updated"
);

console.log("Message updated, requestId:", updateMessageResponse.requestId);
```

### <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma

Yöntemini çağırarak önceden eklenmiş iletileri indirin [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) . `numberOfMessages`Alanında bu çağrı için alacak en fazla ileti sayısını geçirin.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
console.log("\nReceiving messages from the queue...");

// Get messages from the queue
const receivedMessagesResponse = await queueClient.receiveMessages({ numberOfMessages : 5 });

console.log("Messages received, requestId:", receivedMessagesResponse.requestId);
```

### <a name="delete-messages-from-a-queue"></a>Kuyruktaki iletileri silme

İletileri alındıktan ve işlendikten sonra kuyruktan silin. Bu durumda, işleme yalnızca konsolda iletiyi görüntülüyor.

Yöntemini çağırarak iletileri silin [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) . Açıkça silinmeyen tüm iletiler, daha sonra bu işlemleri işlemek için bir süre sonra sırada görünür hale gelir.

Bu kodu işlevin sonuna ekleyin `main` :

```javascript
// 'Process' and delete messages from the queue
for (i = 0; i < receivedMessagesResponse.receivedMessageItems.length; i++) {
    receivedMessage = receivedMessagesResponse.receivedMessageItems[i];

    // 'Process' the message
    console.log("\tProcessing:", receivedMessage.messageText);

    // Delete the message
    const deleteMessageResponse = await queueClient.deleteMessage(
        receivedMessage.messageId,
        receivedMessage.popReceipt
    );
    console.log("\tMessage deleted, requestId:", deleteMessageResponse.requestId);
}
```

### <a name="delete-a-queue"></a>Bir kuyruk silme

Aşağıdaki kod, yöntemi kullanılarak sıranın silindiği, uygulamanın oluşturduğu kaynakları temizler [`delete`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) .

Bu kodu işlevin sonuna ekleyin `main` ve dosyayı kaydedin:

```javascript
// Delete the queue
console.log("\nDeleting queue...");
const deleteQueueResponse = await queueClient.delete();
console.log("Queue deleted, requestId:", deleteQueueResponse.requestId);
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama, bir Azure kuyruğuna üç ileti oluşturur ve ekler. Kod kuyruktaki iletileri listeler, ardından kuyruğu silmeden önce bunları alır ve siler.

Konsol pencerenizde, dosyayı içeren dizine gidin `queues-quickstart-v12.js` ve `node` uygulamayı çalıştırmak için aşağıdaki komutu kullanın.

```console
node queues-quickstart-v12.js
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Queue Storage client library v12 - JavaScript quickstart sample

Creating queue...
         quickstartc095d120-1d04-11ea-af30-090ee231305f
Queue created, requestId: 5c0bc94c-6003-011b-7c11-b13d06000000

Adding messages to the queue...
Messages added, requestId: a0390321-8003-001e-0311-b18f2c000000

Peek at the messages in the queue...
         First message
         Second message
         Third message

Updating the third message in the queue...
Message updated, requestId: cb172c9a-5003-001c-2911-b18dd6000000

Receiving messages from the queue...
Messages received, requestId: a039036f-8003-001e-4811-b18f2c000000
        Processing: First message
        Message deleted, requestId: 4a65b82b-d003-00a7-5411-b16c22000000
        Processing: Second message
        Message deleted, requestId: 4f0b2958-c003-0030-2a11-b10feb000000
        Processing: Third message has been updated
        Message deleted, requestId: 6c978fcb-5003-00b6-2711-b15b39000000

Deleting queue...
Queue deleted, requestId: 5c0bca05-6003-011b-1e11-b13d06000000

Done
```

Hata Ayıklayıcıdaki kodda adım adım ilerleyin ve işlem boyunca [Azure Portal](https://portal.azure.com) denetleyin. Kuyruktaki iletilerin oluşturulup silindiğini doğrulamak için depolama hesabınızı kontrol edin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sıranın nasıl oluşturulacağını ve JavaScript kodunu kullanarak iletiye nasıl ileti ekleneceğini öğrendiniz. Ardından iletileri göz atmayı, almayı ve silmeyi öğrendiniz. Son olarak, bir ileti sırasının nasıl silineceğini öğrendiniz.

Öğreticiler, örnekler, hızlı ve diğer belgeler için şu adresi ziyaret edin:

> [!div class="nextstepaction"]
> [JavaScript için Azure belgeleri](/azure/developer/javascript/)

- Daha fazla bilgi edinmek için bkz. [JavaScript Için Azure kuyruk depolama istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue).
- Daha fazla Azure kuyruk depolama örneği uygulaması için bkz. [JavaScript Için Azure kuyruk depolama istemci kitaplığı V12](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-queue/samples).
