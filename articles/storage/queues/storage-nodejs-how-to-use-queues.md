---
title: Node. js ' den Azure kuyruk depolaması kullanma-Azure depolama
description: Kuyrukları oluşturmak ve silmek için Azure Kuyruk hizmeti kullanmayı ve iletileri ekleme, alma ve silme hakkında bilgi edinin. Node. js içinde yazılan örnekler.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/08/2016
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 9eee7f6b321a5ba660e1a0a5a48771c599746bcb
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673089"
---
# <a name="use-azure-queue-service-to-create-and-delete-queues-from-nodejs"></a>Node. js ' den kuyruk oluşturmak ve silmek için Azure kuyruk hizmeti 'ni kullanma
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Genel Bakış
Bu kılavuzda, Microsoft Azure Kuyruk hizmeti kullanarak nasıl yaygın senaryolar gerçekleştirebileceğiniz gösterilmektedir. Örnekler Node. js API 'SI kullanılarak yazılır. Kapsanan senaryolar sıra iletilerini **ekleme**, göz **atma**, **alma**ve **silme** , Ayrıca kuyruk **oluşturma ve silme**içerir.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Node. js uygulaması oluşturma
Boş bir Node. js uygulaması oluşturun. Bir Node. js uygulaması oluşturma yönergeleri için, bkz. [Azure App Service bir Node. js web uygulaması oluşturma](../../app-service/app-service-web-get-started-nodejs.md), Windows PowerShell kullanarak bir [Azure bulut hizmetinde Node. js uygulaması oluşturma ve dağıtma](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) veya [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Uygulamanızı depolamaya erişecek şekilde yapılandırma
Azure Storage 'ı kullanmak için, depolama REST hizmetleriyle iletişim kuran bir dizi kullanışlı kitaplık içeren node. js için Azure depolama SDK 'sına ihtiyacınız vardır.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Paketi edinmek için düğüm Paket Yöneticisi 'Ni (NPM) kullanın
1. **PowerShell** (Windows,) **Terminal** (Mac,) veya **Bash** (Unix) gibi bir komut satırı arabirimi kullanın, örnek uygulamanızı oluşturduğunuz klasöre gidin.
2. Komut penceresine **npm install azure-storage** yazın. Komutun çıktısı aşağıdaki örneğe benzer.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Bir **düğüm\_modülleri** klasörünün oluşturulduğunu doğrulamak için **ls** komutunu el ile çalıştırabilirsiniz. Bu klasörün içinde, depolama alanına erişmek için ihtiyaç duyduğunuz kitaplıkları içeren **azure-storage** paketini bulacaksınız.

### <a name="import-the-package"></a>Paketi içeri aktarma
Not defteri veya başka bir metin düzenleyicisi kullanarak aşağıdakini, depolamayı kullanmayı düşündüğünüz uygulamanın **Server. js** dosyasına ekleyin:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Azure depolama bağlantısı kurma
Azure\_modülü, Azure depolama\_hesabı ve Azure\_\_\_depolama erişim anahtarı ya da Azure\_depolama\_bağlantısıortamdeğişkenleriniokur\_ Azure depolama hesabınıza bağlanmak için gereken bilgiler için DIZE. Bu ortam değişkenleri ayarlanmamışsa, **Createqueueservice**çağrılırken hesap bilgilerini belirtmeniz gerekir.

## <a name="how-to-create-a-queue"></a>Nasıl Yapılır: Sıra oluşturma
Aşağıdaki kod, kuyruklarla çalışmanıza olanak sağlayan bir **QueueService** nesnesi oluşturur.

```javascript
var queueSvc = azure.createQueueService();
```

Zaten varsa belirtilen kuyruğu döndüren **Createqueueifnotexists** yöntemini kullanın veya zaten mevcut değilse belirtilen ada sahip yeni bir kuyruk oluşturur.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Sıra oluşturulduysa, `result.created` doğru olur. Sıra varsa, `result.created` false olur.

### <a name="filters"></a>Filtreler
İsteğe bağlı filtreleme işlemleri, **QueueService**kullanılarak gerçekleştirilen işlemlere uygulanabilir. Filtreleme işlemleri, günlüğe kaydetme, otomatik yeniden deneme vb. içerebilir. Filtreler, imza ile bir yöntem uygulayan nesnelerdir:

```javascript
function handle (requestOptions, next)
```

İstek seçeneklerinde ön işleme gerçekleştirildikten sonra, yöntemin aşağıdaki imzayla bir geri çağırma işlemi "Next" çağrısını yapması gerekir:

```javascript
function (returnObject, finalCallback, next)
```

Bu geri çağırmada ve returnObject (istekten sunucuya olan yanıt) işlendikten sonra geri aramanın, diğer filtreleri işlemeye devam etmek veya hizmeti sonlandırmak için sonlandırılma çağırmak için varsa bir sonrakini çağırması gerekir çağrılması.

Yeniden deneme mantığı uygulayan iki filtre (**ExponentialRetryPolicyFilter** ve **LinearRetryPolicyFilter**), Node.js için Azure SDK’sına dahil edilir. Aşağıdaki, **üs Alretrypolicyfilter**kullanan bir **QueueService** nesnesi oluşturur:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Nasıl Yapılır: Bir kuyruğa Ileti ekleme
Bir kuyruğa ileti eklemek için **CreateMessage** metodunu kullanarak yeni bir ileti oluşturun ve kuyruğa ekleyin.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Nasıl Yapılır: Sonraki Iletiye göz atın
**PeekMessages** yöntemini çağırarak sıradan kaldırmadan bir kuyruğun önündeki iletiye göz atmayı sağlayabilirsiniz. Varsayılan olarak, **peekMessages** , tek bir iletiye bakar.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

İletiyi `result` içerir.

> [!NOTE]
> Kuyruktaki hiçbir ileti olmadığında **peekMessages** kullanılması bir hata döndürmez, ancak hiçbir ileti döndürülmez.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Nasıl Yapılır: Sonraki Iletiyi sıradan çıkarma
Bir iletiyi işlemek iki aşamalı bir işlemdir:

1. İleti sıradan çıkar.
2. İletiyi silin.

Bir iletiyi sıradan çıkarma için **GetMessages**kullanın. Bu, iletileri kuyrukta görünmez hale getirir, dolayısıyla başka hiçbir istemci bunları işleyemez. Uygulamanız bir iletiyi işledikten sonra sıradan silmek için **deleteMessage** ' ı çağırın. Aşağıdaki örnek bir ileti alır ve siler:

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

> [!NOTE]
> Varsayılan olarak, bir ileti yalnızca 30 saniye gizlidir, bu da diğer istemcilere görünür. `options.visibilityTimeout` **GetMessages**ile kullanarak farklı bir değer belirtebilirsiniz.
> 
> [!NOTE]
> Kuyruktaki hiçbir ileti olmadığında **GetMessages** kullanılması bir hata döndürmez, ancak hiçbir ileti döndürülmez.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl Yapılır: Sıraya alınan Iletinin Içeriğini değiştirme
**Updatemessage**kullanarak kuyruktaki bir iletinin içeriğini yerinde değiştirebilirsiniz. Aşağıdaki örnek bir iletinin metnini güncelleştirir:

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

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Nasıl Yapılır: Dequeuing Iletileri için ek seçenekler
Bir kuyruktan ileti alımını özelleştirmek için iki yol vardır:

* `options.numOfMessages`-Bir ileti toplu işi alın (32 'e kadar.)
* `options.visibilityTimeout`-Daha uzun veya daha kısa görünürlük zaman aşımı ayarlayın.

Aşağıdaki örnek, bir çağrıda 15 ileti almak için **GetMessages** yöntemini kullanır. Sonra her iletiyi bir for döngüsü kullanarak işler. Ayrıca, bu yöntemin döndürdüğü tüm iletiler için geçersiz kılma zaman aşımını beş dakikaya ayarlar.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
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

## <a name="how-to-get-the-queue-length"></a>Nasıl Yapılır: Sıra uzunluğunu al
**Getqueuemetadata** , sırada bekleyen iletilerin yaklaşık sayısı dahil olmak üzere kuyruk hakkında meta veriler döndürür.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Nasıl Yapılır: Sıraları listeleme
Kuyrukların listesini almak için **Listqueueskesimli**öğesini kullanın. Belirli bir ön eke göre filtrelenmiş bir listeyi almak için **listQueuesSegmentedWithPrefix**kullanın.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Tüm kuyruklar döndürülmezse, `result.continuationToken` daha fazla sonuç almak için **listqueuessegmentinin** ilk parametresi olarak veya **listQueuesSegmentedWithPrefix** ikinci parametresi olarak kullanılabilir.

## <a name="how-to-delete-a-queue"></a>Nasıl Yapılır: Kuyruğu silme
Bir kuyruğu ve içerdiği tüm iletileri silmek için, kuyruk nesnesi üzerinde **DeleteQueue** yöntemini çağırın.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Sıradan tüm iletileri silmeden temizlemek için **ClearMessages**kullanın.

## <a name="how-to-work-with-shared-access-signatures"></a>Nasıl yapılır: Paylaşılan Erişim İmzaları ile çalışma
Paylaşılan erişim Imzaları (SAS), depolama hesabı adınızı veya anahtarlarınızı sağlamamanız gerekmeden kuyruklara ayrıntılı erişim sağlamanın güvenli bir yoludur. SAS, genellikle bir mobil uygulamanın ileti göndermesine izin verme gibi kuyruklara sınırlı erişim sağlamak için kullanılır.

Bulut tabanlı bir hizmet gibi güvenilir bir uygulama, **Queuesharedaccesssignature** öğesini kullanarak bir SAS oluşturur ve bunu güvenilmeyenveya yarı güvenilir bir uygulamaya sağlar. Örneğin, bir mobil uygulama. SAS’ın geçerli olduğu başlangıç ve bitiş tarihlerini ve SAS sahibine verilen erişim düzeyini açıklayan bir ilke kullanılarak SAS oluşturulur.

Aşağıdaki örnek, SAS sahibinin sıraya ileti eklemesine ve oluşturulma zamanından sonra 100 dakika süre sonu sağlayacak yeni bir paylaşılan erişim ilkesi oluşturur.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

SAS tutucusu sıraya erişmeyi denediğinde gerekli olduğundan, ana bilgisayar bilgilerinin de sağlanması gerektiğini unutmayın.

Daha sonra istemci uygulama, kuyruğa yönelik işlemler gerçekleştirmek için **Queueservicewithsas** ile SAS kullanır. Aşağıdaki örnek kuyruğa bağlanır ve bir ileti oluşturur.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

SAS, erişim Ekle ile oluşturulduğundan, iletileri okuma, güncelleştirme veya silme girişiminde bulunulduğunda bir hata döndürülür.

### <a name="access-control-lists"></a>Erişim denetimi listeleri
Ayrıca SAS için erişim ilkesini ayarlamak istediğinizde de bir Erişim Denetim Listesi (ACL) kullanabilirsiniz. Bu, birden çok istemcinin sıraya erişmesine izin vermek istiyorsanız, ancak her istemci için farklı erişim ilkeleri sağlamak istediğinizde yararlıdır.

Her politikayla ilişkilendirilmiş bir kimlik ile, bir erişim ilkeleri dizisi kullanılarak ACL uygulanır. Aşağıdaki örnek iki ilkeyi tanımlar; biri ' Kullanıcı1 ' ve diğeri ' kullanıcı2 ' için:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Aşağıdaki örnek, **myQueue**IÇIN geçerli ACL 'yi alır, ardından **setqueueacl**kullanarak yeni ilkeleri ekler. Bu yaklaşım aşağıdakilere olanak sağlar:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

ACL ayarlandıktan sonra, bir ilkenin KIMLIĞINE göre bir SAS oluşturabilirsiniz. Aşağıdaki örnek, 'user2' için yeni bir SAS oluşturur:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Sonraki Adımlar
Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

* [Azure Storage ekip blogunu][Azure Storage Team Blog] ziyaret edin.
* GitHub 'daki [düğüm deposu Için Azure depolama SDK 'sını][Azure Storage SDK for Node] ziyaret edin.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Azure App Service bir Node. js web uygulaması oluşturma](../../app-service/app-service-web-get-started-nodejs.md)

[Bir Node.js uygulaması derleme ve Azure Bulut Hizmeti’ne dağıtma](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
