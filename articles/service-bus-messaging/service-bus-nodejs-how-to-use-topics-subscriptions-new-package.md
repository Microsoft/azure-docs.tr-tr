---
title: 'Hızlı başlangıç: node. js ile Azure Service Bus konuları ve abonelikleri kullanma'
description: "Hızlı başlangıç: bir Node. js uygulamasından Azure 'da Service Bus konuları ve abonelikleri nasıl kullanacağınızı öğrenin."
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: aschhab
ms.openlocfilehash: fa6f40eba02ffe171dc521f952e0d00fc35fc7e6
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721670"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azureservice-bus-package"></a>Hızlı başlangıç: node. js ve Azure/Service-Bus paketiyle Service Bus konuları ve abonelikleri kullanma
> [!div class="op_multi_selector" title1="Programlama dili" title2="Node. js 'a paketi ekleyin"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

Bu öğreticide, bir Service Bus konusuna ileti göndermek ve yeni [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketini kullanarak Service Bus aboneliğinden ileti almak Için bir Node. js programı yazmayı öğreneceksiniz. Bu paket, [rest çalışma zamanı API 'leri Service Bus](/rest/api/servicebus/service-bus-runtime-rest)kullanılan daha eski [Azure-SB](https://www.npmjs.com/package/azure-sb) paketi olan daha hızlı [AMQP 1,0 protokolünü](service-bus-amqp-overview.md) kullanır. Örnekler JavaScript 'e yazılır.

## <a name="prerequisites"></a>Ön koşullar
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir konu ve aboneliğiniz yoksa, bunları oluşturmak için [Service Bus konu ve abonelik oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-topics-subscriptions-portal.md) adımlarını izleyin. Service Bus örneğiniz için bağlantı dizesini ve oluşturduğunuz konunun ve aboneliğin adlarını göz önünde bulabilirsiniz. Bu değerleri örneklerde kullanacağız.

> [!NOTE]
> - Bu öğretici, [NodeJS](https://nodejs.org/)kullanarak kopyalayıp çalıştırabileceğiniz örneklerle çalışır. Node. js uygulamasının nasıl oluşturulacağı hakkında yönergeler için bkz. [bir Node. js uygulamasını Azure Web sitesine](../app-service/app-service-web-get-started-nodejs.md)veya [Node. js bulut hizmetini Windows PowerShell kullanarak](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)oluşturma ve dağıtma.
> - Yeni [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) paketi henüz topcıs ve abonelik oluşturmayı desteklemez. Program aracılığıyla bunları oluşturmak istiyorsanız lütfen [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) paketini kullanın.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Paketi yüklemek için Düğüm Paket Yöneticisi’ni (NPM) kullanma
Service Bus için NPM paketini yüklemek için, yolunda `npm` olan bir komut istemi açın, dizini örneklerinizin olmasını istediğiniz klasörle değiştirin ve ardından bu komutu çalıştırın.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme
Service Bus konusuyla etkileşim kurmak [Servicebusclient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) sınıfının örneğini oluşturma ve bunu [topicclient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/topicclient) sınıfının örneğini oluşturmak için kullanma ile başlar. Konu istemciye sahip olduktan sonra, ileti göndermek için bir gönderici oluşturabilir ve üzerinde [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ya da [sendbatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) metodunu kullanabilirsiniz.

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. `send.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın. Bu kod, konuya 10 ileti gönderir.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const topicClient = sbClient.createTopicClient(topicName);
      const sender = topicClient.createSender();
      
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

            await topicClient.close();
          } finally {
            await sbClient.close();
          }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Yukarıdaki koda sitenizin bağlantı dizesini ve adını girin.
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node send.js` çalıştırın. 

Tebrikler! Service Bus kuyruğuna ileti gönderdiniz.

İletiler, gönderirken ayarlayabileceğiniz `label` ve `messageId` gibi bazı standart özelliklere sahiptir. Herhangi bir özel özellik ayarlamak istiyorsanız, özel verilerinizin anahtar-değer çiftlerini tutabilecek bir JSON nesnesi olan `userProperties`kullanın.

Service Bus konu başlıkları, [Standart katmanda](service-bus-premium-messaging.md) maksimum 256 KB ve [Premium katmanda](service-bus-premium-messaging.md) maksimum 1 MB ileti boyutunu destekler. Bir konuda tutulan ileti sayısında sınır yoktur, ancak bir konu tarafından tutulan iletilerin toplam boyutu için bir sınır vardır. Bu konu başlığı boyutu, üst sınır 5 GB olacak şekilde oluşturulma zamanında belirlenir. Kotalar hakkında daha fazla bilgi için bkz. [Service Bus kotaları](service-bus-quotas.md).

## <a name="receive-messages-from-a-subscription"></a>Bir abonelikten ileti alma
Service Bus abonelikle etkileşim kurmak, [Servicebusclient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) sınıfının örneğini oluşturma ve bunu kullanarak [subscriptionclient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient) sınıfının örneğini oluşturmaya başlar. Abonelik istemcisini aldıktan sonra, bir alıcı oluşturabilir ve ileti almak için üzerinde [Receivemessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ya da [registermessagehandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) metodunu kullanabilirsiniz.

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. `recieve.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın. Bu kod, aboneliğinizden 10 ileti almaya çalışır. Aldığınız gerçek sayı, abonelik ve ağ gecikmesi içindeki ileti sayısına bağlıdır.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const topicName = ""; 
    const subscriptionName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const subscriptionClient = sbClient.createSubscriptionClient(topicName, subscriptionName);
      const receiver = subscriptionClient.createReceiver(ReceiveMode.receiveAndDelete);
      
      try {
        const messages = await receiver.receiveMessages(10);
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
        
        await subscriptionClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Yukarıdaki koda konu ve aboneliğinizin bağlantı dizesini ve adlarını girin.
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node receiveMessages.js` çalıştırın.

Tebrikler! Service Bus aboneliğinden yalnızca ileti aldınız.

[Createreceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/subscriptionclient#createreceiver-receivemode-) yöntemi, [receiveanddelete](message-transfers-locks-settlement.md#settling-receive-operations) ve [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)değerlerine sahip bir sabit listesi olan bir `ReceiveMode` alır. İleti üzerinde `complete()`, `abandon()`, `defer()`veya `deadletter()` yöntemlerinden birini kullanarak `PeekLock` modunu kullanıyorsanız, [iletilerinizi kapatmanız](message-transfers-locks-settlement.md#settling-receive-operations) gerektiğini unutmayın.

## <a name="subscription-filters-and-actions"></a>Abonelik filtreleri ve eylemleri
Service Bus, [aboneliklerdeki filtreleri ve eylemleri](topic-filters.md)destekler, bu da gelen iletileri bir aboneliğe filtrelemenizi ve özelliklerini düzenlemenizi sağlar.

Bir `SubscriptionClient` örneğine sahip olduktan sonra, filtre ve eylemleri denetlemek için abonelikte kuralları almak, eklemek ve kaldırmak için aşağıdaki yöntemleri kullanabilirsiniz.

- getRules
- addRule
- removeRule

Her aboneliğin, tüm gelen iletilere izin vermek için doğru filtreyi kullanan bir varsayılan kuralı vardır. Yeni bir kural eklediğinizde, yeni kuraldaki filtrenin çalışması için varsayılan filtreyi kaldırmayı unutmayın. Bir aboneliğin kuralları yoksa, hiçbir ileti almaz.

> [!NOTE]
> Service Bus kaynaklarını [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)ile yönetebilirsiniz. Service Bus gezgin, kullanıcıların bir Service Bus ad alanına bağlanmasına ve mesajlaşma varlıklarını kolay bir şekilde yönetmesine olanak tanır. Araç içeri/dışarı aktarma işlevselliği gibi gelişmiş özellikler ya da konu, kuyruk, abonelik, geçiş Hizmetleri, Bildirim Hub 'ları ve Olay Hub 'larını test etme yeteneği sağlar. 

## <a name="next-steps"></a>Sonraki Adımlar
Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın.

- [Kuyruklar, konu başlıkları ve abonelikler](service-bus-queues-topics-subscriptions.md)
- [GitHub üzerinde Service Bus için diğer NodeJS örneklerini](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript) kullanıma al
- [Node.js Geliştirici Merkezi](https://azure.microsoft.com/develop/nodejs/)


