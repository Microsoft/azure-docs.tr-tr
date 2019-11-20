---
title: 'Hızlı başlangıç: PHP ile Service Bus kuyruklarını kullanma'
description: "Hızlı başlangıç: Azure 'da Service Bus kuyruklarını nasıl kullanacağınızı öğrenin. PHP 'de yazılan kod örnekleri."
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: d576c269f4178c7543327c6b75f46f5487d7a205
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719194"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Hızlı başlangıç: PHP ile Service Bus kuyruklarını kullanma
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Bu öğreticide, Service Bus kuyruğuna ileti göndermek ve ileti almak için PHP uygulamaları oluşturmayı öğreneceksiniz. 

## <a name="prerequisites"></a>Ön koşullar
1. Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
2. Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin.
    1. Service Bus **kuyruklara**hızlı **genel bakış** konusunu okuyun. 
    2. Service Bus **ad alanı**oluşturun. 
    3. **Bağlantı dizesini**alın. 

        > [!NOTE]
        > Bu öğreticide PHP 'yi kullanarak Service Bus ad alanında bir **sıra** oluşturacaksınız. 
3. [PHP için Azure SDK](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>PHP uygulaması oluşturma
Azure Blob hizmetine erişen bir PHP uygulaması oluşturmaya yönelik tek gereksinim, kodunuzun içinden [php Için Azure SDK](https://github.com/Azure/azure-sdk-for-php) 'sindeki sınıfların başvuridir. Uygulamanızı veya Not defterini oluşturmak için herhangi bir geliştirme aracı kullanabilirsiniz.

> [!NOTE]
> PHP yüklemenizin Ayrıca [OpenSSL uzantısının](https://php.net/openssl) yüklü ve etkin olması gerekir.

Bu kılavuzda, yerel olarak bir PHP uygulaması içinden veya bir Azure Web rolü, çalışan rolü veya Web sitesi içinde çalışan kodda çağrılabilen hizmet özelliklerini kullanacaksınız.

## <a name="get-the-azure-client-libraries"></a>Azure istemci kitaplıklarını al
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Uygulamanızı kullanmak için yapılandırma Service Bus
Service Bus kuyruğu API 'Lerini kullanmak için şunları yapın:

1. [Require_once][require_once] ifadesini kullanarak otomatik yükleyici dosyasına başvurun.
2. Kullanabileceğiniz tüm sınıflara başvurun.

Aşağıdaki örnek, otomatik yükleyici dosyasının nasıl ekleneceğini ve `ServicesBuilder` sınıfına nasıl başvurululacağını gösterir.

> [!NOTE]
> Bu örnek (ve bu makaledeki diğer örnekler), Oluşturucu aracılığıyla Azure için PHP Istemci kitaplıklarını yüklediğinizi varsayar. Kitaplıkları el ile veya bir PEAR paketi olarak yüklediyseniz, **windowsazure. php** otomatik yükleyici dosyasına başvurmanız gerekir.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Aşağıdaki örneklerde `require_once` deyimin her zaman gösterilmesi gerekir, ancak yalnızca yürütülecek örnek için gereken sınıflara başvurulur.

## <a name="set-up-a-service-bus-connection"></a>Service Bus bağlantısını ayarlama
Service Bus istemcisinin örneğini oluşturmak için, önce bu biçimde geçerli bir bağlantı dizesine sahip olmanız gerekir:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Burada `Endpoint` genellikle `[yourNamespace].servicebus.windows.net`biçimindedir.

Herhangi bir Azure hizmeti istemcisi oluşturmak için `ServicesBuilder` sınıfını kullanmanız gerekir. Yapabilecekleriniz:

* Bağlantı dizesini doğrudan ona geçirin.
* Bağlantı dizesinin birden çok dış kaynağını denetlemek için **Cloudconfigurationmanager (CCM)** kullanın:
  * Varsayılan olarak, bir dış kaynak-ortam değişkeni desteğiyle birlikte gelir
  * `ConnectionStringSource` sınıfını genişleterek yeni kaynaklar ekleyebilirsiniz

Burada özetlenen örnekler için bağlantı dizesi doğrudan geçirilir.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Kuyruk oluşturma
`ServiceBusRestProxy` sınıfı aracılığıyla Service Bus kuyrukları için yönetim işlemleri gerçekleştirebilirsiniz. Bir `ServiceBusRestProxy` nesnesi, bunu yönetmek için belirteç izinlerini kapsülleyen uygun bir bağlantı dizesiyle `ServicesBuilder::createServiceBusService` Factory yöntemi aracılığıyla oluşturulur.

Aşağıdaki örnek, bir `ServiceBusRestProxy` örneği oluşturma ve bir `MySBNamespace` hizmeti ad alanı içinde `myqueue` adlı bir sıra oluşturmak için `ServiceBusRestProxy->createQueue` çağırma gösterilmektedir:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Bir ad alanında belirtilen ada sahip bir kuyruğun zaten var olup olmadığını denetlemek için `ServiceBusRestProxy` nesnelerinde `listQueues` yöntemini kullanabilirsiniz.
> 
> 

## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme
Service Bus kuyruğuna ileti göndermek için, uygulamanız `ServiceBusRestProxy->sendQueueMessage` yöntemini çağırır. Aşağıdaki kod, `MySBNamespace` hizmeti ad alanı içinde daha önce oluşturulan `myqueue` kuyruğuna nasıl ileti gönderileceğini gösterir.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Service Bus sıralara gönderilen (ve alınan) iletiler, [Brokeredmessage][BrokeredMessage] sınıfının örnekleridir. [Brokeredmessage][BrokeredMessage] nesneleri, uygulamaya özgü özel özellikleri ve rastgele uygulama verilerinin gövdesini tutmak için kullanılan standart yöntemler ve özellikler kümesine sahiptir.

Service Bus kuyrukları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Standart ve özel uygulama özelliklerini içeren üst bilginin maksimum dosya boyutu 64 KB olabilir. Kuyrukta tutulan ileti sayısına ilişkin bir sınır yoktur ancak kuyruk tarafından tutulan iletilerin toplam boyutu için uç sınır vardır. Sıra boyutu için bu üst sınır 5 GB 'dir.

## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma

Bir kuyruktan ileti almanın en iyi yolu `ServiceBusRestProxy->receiveQueueMessage` yöntemi kullanmaktır. İletiler iki farklı modda alınabilir: [*Receiveanddelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) ve [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** varsayılan değerdir.

[Receiveanddelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modunu kullanırken alma işlemi tek bir görüntü işlemidir; diğer bir deyişle, Service Bus kuyruktaki bir ileti için okuma isteği aldığında, iletiyi tüketildiği gibi işaretler ve uygulamaya döndürür. [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) modu, en basit modeldir ve uygulamanın hata oluştuğunda bir iletinin işlenmemesine izin verebileceği senaryolarda en iyi şekilde çalışır. Bu durumu daha iyi anlamak için müşterinin bir alma isteği bildirdiğini ve bu isteğin işlenmeden çöktüğünü varsayın. Service Bus iletiyi tüketildiği gibi işaretlediği için, uygulama yeniden başlatıldığında ve iletileri yeniden kullanmaya başladığında, kilitlenmeden önce tüketilen iletiyi kaçırmış olur.

Varsayılan [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) modunda bir ileti almak iki aşamalı bir işlem haline gelir, bu da eksik iletilere izin verilmeyen uygulamaları desteklemeyi olanaklı kılar. Service Bus bir istek aldığında, kullanılacak sonraki iletiyi bulur, diğer tüketicilerin bunu almasını engellemek için onu kilitler ve ardından uygulamaya döndürür. Uygulama iletiyi işlemeyi tamamladıktan (veya gelecekteki işlemler için güvenilir bir şekilde depolar), alınan iletiyi `ServiceBusRestProxy->deleteMessage`geçirerek alma işleminin ikinci aşamasını tamamlar. Service Bus `deleteMessage` çağrısını gördüğünde, iletiyi tüketildiği gibi işaretleyecek ve kuyruktan kaldıracak.

Aşağıdaki örnek, [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) Mode (varsayılan mod) kullanarak bir iletinin nasıl alınacağını ve işleyeceğini gösterir.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Uygulama çökmelerini ve okunmayan iletileri giderme

Service Bus, uygulamanızda gerçekleşen hataları veya ileti işlenirken oluşan zorlukları rahat bir şekilde ortadan kaldırmanıza yardımcı olmak için işlevsellik sağlar. Bir alıcı uygulamasının iletiyi bazı nedenlerle işleyemeyeceği takdirde, alınan iletide `unlockMessage` yöntemi çağırabilir (`deleteMessage` yöntemi yerine). Bu, Service Bus kuyruktaki iletinin kilidini açmasına ve aynı uygulama ya da başka bir uygulama tarafından yeniden alınabilir olmasını sağlar.

Ayrıca, kuyruk içinde kilitlenen bir iletiyle ilişkili bir zaman aşımı vardır ve uygulamanın kilit zaman aşımı dolmadan önce iletiyi işleyemezse (örneğin, uygulama çökerse), Service Bus otomatik olarak iletinin kilidini açar ve bunu yapar yeniden alınmak üzere kullanılabilir.

Uygulamanın iletiyi işledikten sonra, ancak `deleteMessage` isteği verilmeden önce çöktüğü durumda, yeniden başlatıldığında ileti uygulamaya yeniden gönderilir. Bu genellikle *en az bir kez* işleme olarak adlandırılır; diğer bir deyişle, her ileti en az bir kez işlenir ancak belirli durumlarda aynı ileti yeniden teslim edilebilir. Senaryo yinelenen işleme tolerans vermezse, yinelenen ileti teslimini işlemek için uygulamalara ek mantık eklemek önerilir. Bu, genellikle ileti `getMessageId` yöntemi kullanılarak gerçekleştirilir ve bu durum, teslim girişimleri arasında sabit kalır.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
Service Bus kuyrukların temellerini öğrendiğinize göre, daha fazla bilgi için bkz. [Kuyruklar, konular ve abonelikler][Queues, topics, and subscriptions] .

Daha fazla bilgi için [php geliştirici merkezini](https://azure.microsoft.com/develop/php/)de ziyaret edin.

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


