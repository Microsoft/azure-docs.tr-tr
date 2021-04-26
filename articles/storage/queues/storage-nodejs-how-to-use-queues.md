---
title: Azure kuyruk depolama Node.js-Azure depolama 'dan nasıl kullanılır
description: Kuyrukları oluşturmak ve silmek için Azure kuyruk depolama alanını kullanmayı öğrenin. Node.js kullanarak ileti eklemeyi, almayı ve silmeyi öğrenin.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 12/21/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-september2019, devx-track-js
ms.openlocfilehash: 161000f2860a1153424a628a2b303f3717f828da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275949"
---
# <a name="how-to-use-azure-queue-storage-from-nodejs"></a>Node.js Azure kuyruk depolamayı kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Genel Bakış

Bu kılavuzda, Azure kuyruk depolama kullanarak genel senaryoları nasıl gerçekleştirebileceğiniz gösterilmektedir. Örnekler Node.js API kullanılarak yazılır. Kapsanan senaryolar sıra iletilerini ekleme, göz atma, alma ve silmeyi içerir. Kuyrukları oluşturmayı ve silmeyi de öğrenin.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node.js uygulaması oluşturma

Boş bir Node.js uygulaması oluşturmak için, bkz. [Azure App Service 'da Node.js Web uygulaması oluşturma](../../app-service/quickstart-nodejs.md), PowerShell veya [Cloud Services](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)kullanarak [Azure Visual Studio Code 'de bir Node.js uygulaması derleme ve dağıtma](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) .

## <a name="configure-your-application-to-access-storage"></a>Uygulamanızı depolamaya erişecek şekilde yapılandırma

[JavaScript Için Azure Storage istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#azure-storage-client-library-for-javascript) , depolama Rest hizmetleriyle iletişim kuran bir dizi kullanışlı kitaplık içerir.

<!-- docutune:ignore Terminal -->

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Paketi edinmek için düğüm Paket Yöneticisi 'ni (NPM) kullanın

1. PowerShell (Windows), Terminal (Mac) veya Bash (Unix) gibi bir komut satırı arabirimi kullanın, örnek uygulamanızı oluşturduğunuz klasöre gidin.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

1. `npm install @azure/storage-queue`Komut penceresine yazın.

1. Bir `node_modules` klasörün oluşturulduğunu doğrulayın. Bu klasörün içinde `@azure/storage-queue` , depolamaya erişmeniz gereken istemci kitaplığını içeren paketi bulacaksınız.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

1. `npm install azure-storage`Komut penceresine yazın.

1. Bir `node_modules` klasörün oluşturulduğunu doğrulayın. Bu klasörün içinde, `azure-storage` depolamaya erişmeniz gereken kitaplıkları içeren paketi bulacaksınız.

---

### <a name="import-the-package"></a>Paketi içeri aktarma

Kod düzenleyicinizi kullanarak, kuyrukları kullanmayı düşündüğünüz JavaScript dosyasına aşağıdakini ekleyin.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ImportStatements":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

```javascript
var azure = require('azure-storage');
```

---

## <a name="how-to-create-a-queue"></a>Kuyruk oluşturma

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Aşağıdaki kod, adlı bir ortam değişkeninin değerini alır `AZURE_STORAGE_CONNECTION_STRING` ve bunu bir nesne oluşturmak için kullanır [`QueueServiceClient`](/javascript/api/@azure/storage-queue/queueserviceclient) . Bu nesne daha sonra [`QueueClient`](/javascript/api/@azure/storage-queue/queueclient) belirli bir kuyruk ile çalışmanıza izin veren bir nesne oluşturmak için kullanılır.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_CreateQueue":::

Sıra zaten varsa, bir özel durum oluşturulur.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Azure modülü, ortam değişkenlerini `AZURE_STORAGE_ACCOUNT` ve `AZURE_STORAGE_ACCESS_KEY` ya da `AZURE_STORAGE_CONNECTION_STRING` Azure depolama hesabınıza bağlanmak için gereken bilgileri okuyacaktır. Bu ortam değişkenleri ayarlanmamışsa, çağırırken hesap bilgilerini belirtmeniz gerekir `createQueueService` .

Aşağıdaki kod `QueueService` , kuyruklar ile çalışmanıza olanak sağlayan bir nesnesi oluşturur.

```javascript
var queueSvc = azure.createQueueService();
```

`createQueueIfNotExists`Belirtilen ada sahip yeni bir sıra oluşturmak veya zaten varsa kuyruğu döndürmek için yöntemini çağırın.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Sıra oluşturulduysa, `result.created` doğru olur. Sıra varsa, `result.created` false olur.

---

## <a name="how-to-insert-a-message-into-a-queue"></a>Bir kuyruğa ileti ekleme

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Bir kuyruğa ileti eklemek için [`sendMessage`](/javascript/api/@azure/storage-queue/queueclient#sendmessage-string--queuesendmessageoptions-) yöntemini çağırın.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_AddMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Bir kuyruğa ileti eklemek için, `createMessage` Yeni bir ileti oluşturmak ve kuyruğa eklemek üzere yöntemini çağırın.

```javascript
queueSvc.createMessage('myqueue', "Hello, World", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

---

## <a name="how-to-peek-at-the-next-message"></a>Sonraki iletiye nasıl göz atın

Yöntemi çağırarak kuyruktaki iletilere, kuyruktan kaldırmadan göz atmayı sağlayabilirsiniz `peekMessages` .

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Varsayılan olarak [`peekMessages`](/javascript/api/@azure/storage-queue/queueclient#peekmessages-queuepeekmessagesoptions-) tek bir iletiye göz atar. Aşağıdaki örnekte kuyruktaki ilk beş ileti göz atar. Beşten az ileti görünür durumdaysa, yalnızca görünür iletiler döndürülür.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_PeekMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Varsayılan olarak `peekMessages` tek bir iletiye göz atar.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

`result`İletiyi içerir.

---

`peekMessages`Kuyrukta hiçbir ileti olmadığında çağrı bir hata döndürmez. Ancak, hiçbir ileti döndürülmez.

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Sıraya alınan iletinin içeriğini değiştirme

Aşağıdaki örnek bir iletinin metnini güncelleştirir.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Çağırarak kuyruktaki bir iletinin içeriğini değiştirme [`updateMessage`](/javascript/api/@azure/storage-queue/queueclient#updatemessage-string--string--string--number--queueupdatemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_UpdateMessage":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Çağırarak kuyruktaki bir iletinin içeriğini değiştirme `updateMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

---

## <a name="how-to-dequeue-a-message"></a>İletiyi sıradan çıkarma

Bir iletinin kuyruğa çıkarılması iki aşamalı bir işlemdir:

1. İletiyi alın.

1. İletiyi silin.

Aşağıdaki örnekte bir ileti alınır ve silinir.

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Bir ileti almak için [`receiveMessages`](/javascript/api/@azure/storage-queue/queueclient#receivemessages-queuereceivemessageoptions-) yöntemini çağırın. Bu çağrı, iletileri kuyrukta görünmez hale getirir, dolayısıyla başka hiçbir istemci bunları işleyemez. Uygulamanız bir iletiyi işledikten sonra sıradan silme çağrısı yapın [`deleteMessage`](/javascript/api/@azure/storage-queue/queueclient#deletemessage-string--string--queuedeletemessageoptions-) .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessage":::

Varsayılan olarak, bir ileti yalnızca 30 saniye gizli olur. 30 saniye sonra diğer istemcilere görünür olur. ' İ çağırdığınızda, farklı bir değer belirtebilirsiniz [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout) `receiveMessages` .

`receiveMessages`Kuyrukta hiçbir ileti olmadığında çağrı bir hata döndürmez. Ancak, hiçbir ileti döndürülmeyecektir.

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Bir ileti almak için `getMessages` yöntemini çağırın. Bu çağrı, iletileri kuyrukta görünmez hale getirir, dolayısıyla başka hiçbir istemci bunları işleyemez. Uygulamanız bir iletiyi işledikten sonra sıradan silme çağrısı yapın `deleteMessage` .

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

Varsayılan olarak, bir ileti yalnızca 30 saniye gizli olur. 30 saniye sonra diğer istemcilere görünür olur. İle kullanarak farklı bir değer belirtebilirsiniz `options.visibilityTimeout` `getMessages` .

`getMessages`Kuyrukta hiçbir ileti olmadığında kullanmak bir hata döndürmez. Ancak, hiçbir ileti döndürülmeyecektir.

---

## <a name="additional-options-for-dequeuing-messages"></a>Dequeuing iletileri için ek seçenekler

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Bir kuyruktan ileti alımını özelleştirmek için iki yol vardır:

- [`options.numberOfMessages`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#numberofmessages): Bir toplu ileti alın (32 'e kadar).
- [`options.visibilityTimeout`](/javascript/api/@azure/storage-queue/queuereceivemessageoptions#visibilitytimeout): Daha uzun veya daha kısa görünürlük zaman aşımı ayarlayın.

Aşağıdaki örnek, `receiveMessages` tek bir çağrıda beş ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `for` . Ayrıca, bu yöntemin döndürdüğü tüm iletiler için geçersiz kılma zaman aşımını beş dakikaya ayarlar.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DequeueMessages":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Bir kuyruktan ileti alımını özelleştirmek için iki yol vardır:

- `options.numOfMessages`: Bir toplu ileti alın (32 'e kadar).
- `options.visibilityTimeout`: Daha uzun veya daha kısa görünürlük zaman aşımı ayarlayın.

Aşağıdaki örnek, `getMessages` tek bir çağrıda 15 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `for` . Ayrıca, bu yöntemin döndürdüğü tüm iletiler için geçersiz kılma zaman aşımını beş dakikaya ayarlar.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in results){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

---

## <a name="how-to-get-the-queue-length"></a>Sıra uzunluğunu alma

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Yöntemi, sırada [`getProperties`](/javascript/api/@azure/storage-queue/queueclient#getproperties-queuegetpropertiesoptions-) bekleyen iletilerin yaklaşık sayısı dahil olmak üzere kuyruk hakkında meta veriler döndürür.

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_QueueLength":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Yöntemi, sırada `getQueueMetadata` bekleyen iletilerin yaklaşık sayısı dahil olmak üzere kuyruk hakkında meta veriler döndürür.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

---

## <a name="how-to-list-queues"></a>Kuyrukları listeleme

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Kuyrukların listesini almak için çağırın [`QueueServiceClient.listQueues`](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) . Belirli bir önekle filtrelenmiş bir listeyi almak için, çağrdaki [Options. Prefix](/javascript/api/@azure/storage-queue/servicelistqueuesoptions#prefix) ' i ayarlayın `listQueues` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_ListQueues":::

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Kuyrukların listesini almak için kullanın `listQueuesSegmented` . Belirli bir ön eke göre filtrelenmiş bir listeyi almak için kullanın `listQueuesSegmentedWithPrefix` .

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Tüm kuyruklar döndürülmezse, `result.continuationToken` `listQueuesSegmented` `listQueuesSegmentedWithPrefix` daha fazla sonuç almak için ilk parametresi olarak veya ikinci parametresi olarak geçirin.

---

## <a name="how-to-delete-a-queue"></a>Kuyruğu silme

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Bir kuyruğu ve içerdiği tüm iletileri silmek için [`DeleteQueue`](/javascript/api/@azure/storage-queue/queueclient#delete-queuedeleteoptions-) nesnesi üzerinde yöntemini çağırın `QueueClient` .

:::code language="javascript" source="~/azure-storage-snippets/queues/howto/JavaScript/JavaScript-v12/javascript-queues-v12.js" id="Snippet_DeleteQueue":::

Bir kuyruktan silmeden tüm iletileri temizlemek için çağırın [`ClearMessages`](/javascript/api/@azure/storage-queue/queueclient#clearmessages-queueclearmessagesoptions-) .

# <a name="javascript-v2"></a>[JavaScript v2](#tab/javascript2)

Bir kuyruğu ve içerdiği tüm iletileri silmek için, `deleteQueue` kuyruk nesnesi üzerinde yöntemini çağırın.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Bir kuyruktan silmeden tüm iletileri temizlemek için çağırın `clearMessages` .

---

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

- Yenilikleri öğrenmek için [Azure depolama ekibi blogu](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog) ' nu ziyaret edin
- GitHub 'da [JavaScript deposu Için Azure depolama istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage#Azure-storage-client-library-for-JavaScript) ' nı ziyaret edin
