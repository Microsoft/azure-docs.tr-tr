---
title: PHP 'den kuyruk depolama kullanma-Azure Storage
description: Kuyrukları oluşturmak ve silmek ve iletileri eklemek, almak ve silmek için Azure kuyruk depolama hizmetini nasıl kullanacağınızı öğrenin. Örnekler PHP 'de yazılmıştır.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 01/11/2018
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 0d559fd52cffe893b3f7d61842982fdc2ecd0a48
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275932"
---
# <a name="how-to-use-queue-storage-from-php"></a>PHP 'den kuyruk depolama kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

Bu kılavuzda, Azure kuyruk depolama hizmetini kullanarak genel senaryoları nasıl gerçekleştireceğiniz gösterilmektedir. Örnekler, [php Için Azure Storage istemci kitaplığından](https://github.com/Azure/azure-storage-php)sınıflar aracılığıyla yazılır. Kapsanan senaryolar sıra iletilerini ekleme, göz atma, alma ve silme, Ayrıca kuyruk oluşturma ve silme gibi bir deyişle.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>PHP uygulaması oluşturma

Azure kuyruk depolamaya erişen bir PHP uygulaması oluşturmak için tek gereksinim, kodunuzun içinden [php Için Azure Storage istemci kitaplığı](https://github.com/Azure/azure-storage-php) 'ndaki sınıfların başvuridir. Uygulamanızı oluşturmak için Not Defteri dahil olmak üzere tüm geliştirme araçlarını kullanabilirsiniz.

Bu kılavuzda, yerel olarak bir PHP uygulaması içinde veya Azure 'da bir Web uygulaması içinde çalışan kodda çağrılabilen kuyruk depolama hizmeti özelliklerini kullanacaksınız.

## <a name="get-the-azure-client-libraries"></a>Azure istemci kitaplıklarını al

### <a name="install-via-composer"></a>Besteci aracılığıyla Install

1. Projenizin kökünde adlı bir dosya oluşturun `composer.json` ve bu dosyaya aşağıdaki kodu ekleyin:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. [`composer.phar`](https://getcomposer.org/composer.phar)Proje kökünde indirin.

3. Bir komut istemi açın ve proje kökünde aşağıdaki komutu çalıştırın:

    ```console
    php composer.phar install
    ```

Alternatif olarak, kaynak kodu kopyalamak için GitHub 'daki [Azure Storage php istemci kitaplığına](https://github.com/Azure/azure-storage-php) gidin.

## <a name="configure-your-application-to-access-queue-storage"></a>Uygulamanızı kuyruk depolamaya erişecek şekilde yapılandırma

Azure kuyruk depolaması için API 'Leri kullanmak üzere şunları yapmanız gerekir:

1. İfadesini kullanarak otomatik yükleyici dosyasına başvurun [`require_once`](https://www.php.net/manual/en/function.require-once.php) .
2. Kullanabileceğiniz tüm sınıflara başvurun.

Aşağıdaki örnek, otomatik yükleyici dosyasının nasıl ekleneceğini ve sınıfına nasıl başvurululacağını gösterir `QueueRestProxy` .

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

Aşağıdaki örneklerde, `require_once` ifade her zaman gösterilir, ancak yalnızca örneği çalıştırmak için gereken sınıflara başvurulur.

## <a name="set-up-an-azure-storage-connection"></a>Azure depolama bağlantısı kurma

Azure kuyruk depolama istemcisini başlatmak için, önce geçerli bir bağlantı dizesine sahip olmalısınız. Kuyruk depolama bağlantı dizesinin biçimi aşağıdaki gibidir.

Canlı bir hizmete erişmek için:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Öykünücü depolamaya erişmek için:

```php
UseDevelopmentStorage=true
```

Bir Azure kuyruk depolama istemcisi oluşturmak için sınıfını kullanmanız gerekir `QueueRestProxy` . Aşağıdaki tekniklerden birini kullanabilirsiniz:

- Bağlantı dizesini doğrudan ona geçirin.
- Bağlantı dizesini depolamak için Web uygulamanızdaki ortam değişkenlerini kullanın. Bağlantı dizelerini yapılandırmak için bkz. [Azure Web uygulaması yapılandırma ayarları](../../app-service/configure-common.md) belgesi.

Burada özetlenen örnekler için bağlantı dizesi doğrudan geçirilir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Bir kuyruk oluşturma

`QueueRestProxy`Nesnesi, yöntemini kullanarak bir kuyruk oluşturmanıza olanak sağlar `CreateQueue` . Kuyruk oluştururken kuyruktaki seçenekleri ayarlayabilirsiniz, ancak bunu yapmanız gerekmez. Bu örnekte, bir kuyruktaki meta verilerin nasıl ayarlanacağı gösterilmektedir.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Meta veri anahtarları için büyük/küçük harf duyarlılığı kullanmamalısınız. Tüm anahtarlar hizmetten küçük harfle okunurdur.

## <a name="add-a-message-to-a-queue"></a>Kuyruğa ileti ekleme

Bir kuyruğa ileti eklemek için kullanın `QueueRestProxy->createMessage` . Yöntemi sıra adını, ileti metnini ve ileti seçeneklerini (isteğe bağlı) alır.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello, World");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Sonraki iletiye gözatın

' İ çağırarak bir veya daha fazla iletiyi kuyruktan kaldırmadan önyükleyebilirsiniz `QueueRestProxy->peekMessages` . Varsayılan olarak, `peekMessage` yöntemi tek bir ileti döndürür, ancak yöntemini kullanarak bu değeri değiştirebilirsiniz `PeekMessagesOptions->setNumberOfMessages` .

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Sonraki iletiyi sıradan çıkarmak

Kodunuz bir iletiyi iki adımda kuyruktan kaldırır. İlk olarak, `QueueRestProxy->listMessages` iletiyi kuyruktan okunarak diğer herhangi bir koda görünmez hale getiren çağırın. Varsayılan olarak bu ileti 30 saniye görünmez kalır. (İleti bu zaman diliminde silinmeziyorsa, kuyrukta yeniden görünür hale gelir.) İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini çağırmanız gerekir `QueueRestProxy->deleteMessage` . Bir iletiyi kaldırmanın bu iki adımlı işlemi, kodunuz, donanım veya yazılım arızasından kaynaklanan bir iletiyi işleyemediğinde, kodunuzun başka bir örneğinin aynı mesajı almasını ve yeniden denemesini sağlar. `deleteMessage`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Kuyruğa alınan iletinin içeriğini değiştirme

' İ çağırarak kuyruktaki bir iletinin içeriğini değiştirebilirsiniz `QueueRestProxy->updateMessage` . Eğer ileti bir iş görevini temsil ediyorsa, bu özelliği kullanarak iş görevinin durumunu güncelleştirebilirsiniz. Aşağıdaki kod kuyruk iletisini yeni içerikle güncelleştirir ve görünürlük zaman aşımını başka bir 60 saniye uzatmak üzere ayarlar. Bu, iletiyle ilişkili çalışmanın durumunu kaydeder ve istemciye ileti üzerinde çalışmaya devam etmesi için başka bir dakika verir. Bu tekniği, işlem adımı donanım veya yazılım arızası nedeniyle başarısız olursa baştan başlamak zorunda kalmadan, kuyruk iletilerinde çoklu adım iş akışlarını izlemek için kullanabilirsiniz. Genellikle bir yeniden deneme sayacı tutmanı gerekir ve bir ileti *n* seferden daha fazla yeniden denenirse, silebilirsiniz. Bu, her işlendiğinde bir uygulama hatası tetikleyen bir iletiye karşı koruma sağlar.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-dequeuing-messages"></a>Dequeuing iletileri için ek seçenekler

Bir kuyruktan ileti alımını özelleştirmek için iki yol vardır. İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkincisi, daha uzun veya daha kısa bir görünürlük zaman aşımı ayarlayabilir, bu da kodunuzun her iletiyi tamamen işlemesi için daha fazla veya daha az zamana izin verebilirsiniz. Aşağıdaki kod örneği, `getMessages` tek bir çağrıda 16 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngüsü kullanarak işler `for` . Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Kuyruk uzunluğunu al

Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. `QueueRestProxy->getQueueMetadata`Yöntemi kuyrukla ilgili meta verileri alır. `getApproximateMessageCount`Döndürülen nesnede yönteminin çağrılması, bir kuyrukta kaç ileti olduğunu gösteren bir sayı sağlar. Bu sayı yalnızca, kuyruk depolama alanı isteğinize yanıt verdikten sonra iletiler eklenebildiğinden veya kaldırılacağından yaklaşık olur.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Bir kuyruk silme

Bir kuyruğu ve içindeki tüm iletileri silmek için `QueueRestProxy->deleteQueue` yöntemini çağırın.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Sonraki adımlar

Azure kuyruk depolama hakkında temel bilgileri öğrendiğinize göre, daha karmaşık depolama görevleri hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları izleyin:

- [Azure Storage php istemci kitaplığı Için API başvurusunu](https://azure.github.io/azure-storage-php/) ziyaret edin
- [Gelişmiş kuyruk örneğine](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php)bakın.

Daha fazla bilgi için bkz. [php Geliştirici Merkezi](https://azure.microsoft.com/develop/php/).
