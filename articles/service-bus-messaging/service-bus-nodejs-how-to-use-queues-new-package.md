---
title: "Hızlı başlangıç: node. js ' de Azure/Service-Bus kuyruklarını kullanma"
description: "Hızlı başlangıç: Azure 'da Azure/Service-Bus paketini kullanarak bir Node. js uygulamasından Azure 'da Service Bus kuyruklarını nasıl kullanacağınızı öğrenin."
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: 9901ccd6bb1abf27bb1141c618d0bfde167b9cc3
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721691"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Hızlı başlangıç: node. js ve Azure/Service-Bus paketiyle Service Bus kuyruklarını kullanma
> [!div class="op_multi_selector" title1="Programlama dili" title2="Node. js 'a paketi ekleyin"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Bu öğreticide, yeni [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketini kullanarak bir Service Bus kuyruğuna ileti göndermek ve ileti almak Için bir NodeJS programı yazmayı öğreneceksiniz. Bu paket, [rest çalışma zamanı API 'leri Service Bus](/rest/api/servicebus/service-bus-runtime-rest)kullanılan daha eski [Azure-SB](https://www.npmjs.com/package/azure-sb) paketi olan daha hızlı [AMQP 1,0 protokolünü](service-bus-amqp-overview.md) kullanır. Örnekler JavaScript 'e yazılır.

## <a name="prerequisites"></a>Ön koşullar
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin. Service Bus örneğiniz için bağlantı dizesini ve oluşturduğunuz kuyruğun adını aklınızda edin. Bu değerleri örneklerde kullanacağız.

> [!NOTE]
> - Bu öğretici, [NodeJS](https://nodejs.org/)kullanarak kopyalayıp çalıştırabileceğiniz örneklerle çalışır. Node. js uygulamasının nasıl oluşturulacağı hakkında yönergeler için bkz. [bir Node. js uygulamasını Azure Web sitesine](../app-service/app-service-web-get-started-nodejs.md)veya [Node. js bulut hizmetini Windows PowerShell kullanarak](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)oluşturma ve dağıtma.
> - Yeni [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketi henüz sıra oluşturmayı desteklemiyor. Program aracılığıyla bunları oluşturmak istiyorsanız lütfen [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) paketini kullanın.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Paketi yüklemek için Düğüm Paket Yöneticisi’ni (NPM) kullanma
Service Bus için NPM paketini yüklemek için, yolunda `npm` olan bir komut istemi açın, dizini örneklerinizin olmasını istediğiniz klasörle değiştirin ve ardından bu komutu çalıştırın.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme
Service Bus kuyrukla etkileşim kurmak [Servicebusclient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) sınıfının örneğini oluşturmaya ve [queueclient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) sınıfının örneğini oluşturmak için kullanılmasına başlar. Kuyruk istemcisini aldıktan sonra, ileti göndermek için bir gönderici oluşturabilir ve bunun üzerinde [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) veya [sendbatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) yöntemini kullanabilirsiniz.

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. `send.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın. Bu kod, kuyruğunuza 10 ileti gönderir.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Yukarıdaki kodda, kuyruğunuzun bağlantı dizesini ve adını girin.
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node send.js` çalıştırın.

Tebrikler! Service Bus kuyruğuna ileti gönderdiniz.

İletiler, gönderirken ayarlayabileceğiniz `label` ve `messageId` gibi bazı standart özelliklere sahiptir. Herhangi bir özel özellik ayarlamak istiyorsanız, özel verilerinizin anahtar-değer çiftlerini tutabilecek bir JSON nesnesi olan `userProperties`kullanın.

Service Bus kuyrukları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Bir kuyrukta tutulan ileti sayısında bir sınır yoktur ancak bir sıranın tuttuğu iletilerin toplam boyutunun bir üst sınırı vardır. Bu kuyruk boyutu, üst sınır 5 GB olacak şekilde oluşturulma zamanında belirlenir. Kotalar hakkında daha fazla bilgi için bkz. [Service Bus kotaları](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma
Service Bus kuyrukla etkileşim kurmak [Servicebusclient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) sınıfının örneğini oluşturmaya ve [queueclient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) sınıfının örneğini oluşturmak için kullanılmasına başlar. Sıra istemcisini aldıktan sonra, bir alıcı oluşturabilir ve ileti almak için üzerinde [Receivemessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) veya [registermessagehandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metodunu kullanabilirsiniz.

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. `recieve.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın. Bu kod, kuyruğunuzdan 10 ileti almaya çalışır. Aldığınız gerçek sayı, sıradaki ileti sayısına ve ağ gecikmesine bağlıdır.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Yukarıdaki kodda, kuyruğunuzun bağlantı dizesini ve adını girin.
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node receiveMessages.js` çalıştırın.

Tebrikler! Service Bus sırasından ileti aldınız.

[Createreceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) yöntemi, [receiveanddelete](message-transfers-locks-settlement.md#settling-receive-operations) ve [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)değerlerine sahip bir sabit listesi olan bir `ReceiveMode` alır. İleti üzerinde `complete()`, `abandon()`, `defer()`veya `deadletter()` yöntemlerinden birini kullanarak `PeekLock` modunu kullanıyorsanız, [iletilerinizi kapatmanız](message-transfers-locks-settlement.md#settling-receive-operations) gerektiğini unutmayın.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki adımlar
Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın.
- [Kuyruklar, konu başlıkları ve abonelikler](service-bus-queues-topics-subscriptions.md)
- [GitHub üzerinde Service Bus için diğer NodeJS örneklerini](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript) kullanıma al
- [Node.js Geliştirici Merkezi](https://azure.microsoft.com/develop/nodejs/)

