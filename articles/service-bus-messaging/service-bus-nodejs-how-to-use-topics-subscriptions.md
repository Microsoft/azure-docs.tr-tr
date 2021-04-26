---
title: Konularda ve aboneliklerle Azure/Service-Bus önizleme JavaScript 'ı kullanma
description: '@azure/service-busService Bus bir konuya ileti göndermek ve bir aboneliğden konuya ileti almak için paketin en son önizleme sürümünü kullanan bir JavaScript programı yazmayı öğrenin.'
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 59f1bb29099f2b921e687ca9de46365bc34f1b91
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537256"
---
# <a name="quickstart-service-bus-topics-and-subscriptions-with-nodejs-and-the-preview-azureservice-bus-package"></a>Hızlı başlangıç: Node.js ve önizleme Azure/Service-Bus paketindeki Service Bus konuları ve abonelikleri
Bu öğreticide, bir [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) Service Bus konusuna ileti göndermek ve bu konuya Service Bus aboneliğinden ileti almak için bir JavaScript programında paketin nasıl kullanılacağını öğrenirsiniz.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Hızlı başlangıçtaki adımları izleyin [: konuya bir Service Bus konu ve abonelik oluşturmak için Azure Portal kullanın](service-bus-quickstart-topics-subscriptions-portal.md). Bağlantı dizesi, konu adı ve abonelik adı ' nı aklınızda edin. Bu hızlı başlangıç için yalnızca bir abonelik kullanacaksınız. 

> [!NOTE]
> - Bu öğretici, [NodeJS](https://nodejs.org/)kullanarak kopyalayıp çalıştırabileceğiniz örneklerle çalışır. Node.js uygulamasının nasıl oluşturulacağı hakkında yönergeler için, bkz. [Azure Web sitesinde bir Node.js uygulaması oluşturma ve dağıtma](../app-service/quickstart-nodejs.md)veya [ Windows PowerShell kullanarak bulut hizmetiNode.js](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Paketi yüklemek için Düğüm Paket Yöneticisi’ni (NPM) kullanma
Service Bus için NPM paketini yüklemek için, yolunda bulunan bir komut istemi açın `npm` , dizini örneklerinizin olmasını istediğiniz klasörle değiştirin ve ardından bu komutu çalıştırın.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme
Aşağıdaki örnek kod, Service Bus bir konuya bir ileti toplu işleminin nasıl gönderileceğini gösterir. Ayrıntılar için bkz. kod açıklamaları. 

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. Adlı bir dosya oluşturun `sendtotopic.js` ve içine aşağıdaki kodu yapıştırın. Bu kod, konuya bir ileti gönderir.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    
    const messages = [
        { body: "Albert Einstein" },
        { body: "Werner Heisenberg" },
        { body: "Marie Curie" },
        { body: "Steven Hawking" },
        { body: "Isaac Newton" },
        { body: "Niels Bohr" },
        { body: "Michael Faraday" },
        { body: "Galileo Galilei" },
        { body: "Johannes Kepler" },
        { body: "Nikolaus Kopernikus" }
     ];
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createSender() can also be used to create a sender for a queue.
        const sender = sbClient.createSender(topicName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the arry         
    
                // try to add the message to the batch
                if (!batch.tryAddMessage(messages[i])) {            
                    // if it fails to add the message to the current batch
                    // send the current batch as it is full
                    await sender.sendMessages(batch);
    
                    // then, create a new batch 
                    batch = await sender.createMessageBatch();
     
                    // now, add the message failed to be added to the previous batch to this batch
                    if (!batch.tryAddMessage(messages[i])) {
                        // if it still can't be added to the batch, the message is probably too big to fit in a batch
                        throw new Error("Message too big to fit in a batch");
                    }
                }
            }
    
            // Send the last created batch of messages to the topic
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the topic: ${topicName}`);
                    
            // Close the sender
            await sender.close();
        } finally {
            await sbClient.close();
        }
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>`Service Bus ad alanınız için bağlantı dizesiyle değiştirin.
1. `<TOPIC NAME>`Konunun adıyla değiştirin. 
1. Sonra bu dosyayı yürütmek için komut isteminde komutunu çalıştırın.

    ```console
    node sendtotopic.js 
    ```
1. Aşağıdaki çıkışı görmelisiniz.

    ```console
    Sent a batch of messages to the topic: mytopic
    ```

## <a name="receive-messages-from-a-subscription"></a>Bir abonelikten ileti alma
1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. **receivefromsubscription.js** adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın. Ayrıntılar için bkz. kod açıklamaları. 

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");
    
    const connectionString = "<SERVICE BUS NAMESPACE CONNECTION STRING>"
    const topicName = "<TOPIC NAME>";
    const subscriptionName = "<SUBSCRIPTION NAME>";
    
     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a queue.
        const receiver = sbClient.createReceiver(topicName, subscriptionName);
    
        // function to handle messages
        const myMessageHandler = async (messageReceived) => {
            console.log(`Received message: ${messageReceived.body}`);
        };
    
        // function to handle any errors
        const myErrorHandler = async (error) => {
            console.log(error);
        };
    
        // subscribe and specify the message and error handlers
        receiver.subscribe({
            processMessage: myMessageHandler,
            processError: myErrorHandler
        });
    
        // Waiting long enough before closing the sender to send messages
        await delay(5000);
    
        await receiver.close(); 
        await sbClient.close();
    }
    
    // call the main function
    main().catch((err) => {
        console.log("Error occurred: ", err);
        process.exit(1);
     });    
    ```
3. `<SERVICE BUS NAMESPACE CONNECTION STRING>`Ad alanına bağlantı dizesiyle değiştirin. 
1. `<TOPIC NAME>`Konunun adıyla değiştirin. 
1. `<SUBSCRIPTION NAME>`Konunun abonelik adıyla değiştirin. 
1. Sonra bu dosyayı yürütmek için komut isteminde komutunu çalıştırın.

    ```console
    node receivefromsubscription.js
    ```
1. Aşağıdaki çıkışı görmelisiniz.

    ```console
    Received message: Albert Einstein
    Received message: Werner Heisenberg
    Received message: Marie Curie
    Received message: Steven Hawking
    Received message: Isaac Newton
    Received message: Niels Bohr
    Received message: Michael Faraday
    Received message: Galileo Galilei
    Received message: Johannes Kepler
    Received message: Nikolaus Kopernikus
    ```

Azure portal, Service Bus ad alanına gidin ve konunun **Service Bus konu** sayfasını görmek için alt bölmedeki konuyu seçin. Bu sayfada, **iletiler** grafiğinde üç gelen ve üç giden ileti görmeniz gerekir. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Gelen ve giden iletiler":::

Yalnızca uygulamayı Gönder ' i bir dahaki sefer çalıştırırsanız, **Service Bus konu** sayfasında, altı gelen ileti (3 yeni), ancak üç giden ileti görürsünüz. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Konu başlığı sayfası güncelleştirildi":::

Bu sayfada, bir abonelik seçerseniz **Service Bus abonelik** sayfasına ulaşabilirsiniz. Etkin ileti sayısını, atılacak ileti sayısını ve daha fazlasını bu sayfada görebilirsiniz. Bu örnekte, henüz bir alıcı tarafından alınmayan üç etkin ileti vardır. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Etkin ileti sayısı":::

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki belgelere ve örneklere bakın: 

- [Python için Azure Service Bus istemci kitaplığı](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/README.md)
- [Örnekler](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples). **JavaScript** klasöründe JavaScript örnekleri ve **TypeScript** 'in TypeScript örnekleri vardır. 
- [Azure-ServiceBus başvuru belgeleri](/javascript/api/overview/azure/service-bus)
