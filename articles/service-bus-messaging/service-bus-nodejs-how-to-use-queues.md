---
title: JavaScript 'te Azure/Service-Bus kuyruklarını kullanma
description: '@azure/service-busService Bus kuyruğuna ileti göndermek ve ileti almak için paketin en son sürümünü kullanan bir JavaScript programı yazmayı öğrenin.'
author: spelluru
ms.author: spelluru
ms.date: 11/09/2020
ms.topic: quickstart
ms.devlang: nodejs
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: aee67becf7519f03839eafbd897838f938871307
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537225"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-javascript"></a>Azure Service Bus kuyruklarından ileti gönderme ve iletileri alma (JavaScript)
Bu öğreticide, [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) bir Service Bus kuyruğuna ileti göndermek ve ileti almak için bir JavaScript programında paketin nasıl kullanılacağını öğrenirsiniz.

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliği. Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. [MSDN abone avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin. Service Bus ad alanınız ve oluşturduğunuz **kuyruğun** adı için **bağlantı dizesini** aklınızda edin.

> [!NOTE]
> - Bu öğretici, [NodeJS](https://nodejs.org/)kullanarak kopyalayıp çalıştırabileceğiniz örneklerle çalışır. Node.js uygulamasının nasıl oluşturulacağı hakkında yönergeler için, bkz. [Azure Web sitesinde bir Node.js uygulaması oluşturma ve dağıtma](../app-service/quickstart-nodejs.md)veya [ Windows PowerShell kullanarak bulut hizmetiNode.js](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Paketi yüklemek için Düğüm Paket Yöneticisi’ni (NPM) kullanma
Service Bus için NPM paketini yüklemek için, yolunda bulunan bir komut istemi açın `npm` , dizini örneklerinizin olmasını istediğiniz klasörle değiştirin ve ardından bu komutu çalıştırın.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme
Aşağıdaki örnek kod, bir sıraya nasıl ileti gönderileceğini gösterir.

1. [Visual Studio Code](https://code.visualstudio.com/)gibi en sevdiğiniz düzenleyiciyi açın.
2. Adlı bir dosya oluşturun `send.js` ve içine aşağıdaki kodu yapıştırın. Bu kod, kuyruğunuza bir ileti gönderir. İletinin bir etiketi (Bilimcst) ve gövdesi (Einstein) vardır.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus");
    
    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"
    
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
     
        // createSender() can also be used to create a sender for a topic.
        const sender = sbClient.createSender(queueName);
     
        try {
            // Tries to send all messages in a single batch.
            // Will fail if the messages cannot fit in a batch.
            // await sender.sendMessages(messages);
     
            // create a batch object
            let batch = await sender.createMessageBatch(); 
            for (let i = 0; i < messages.length; i++) {
                // for each message in the array            
    
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
    
            // Send the last created batch of messages to the queue
            await sender.sendMessages(batch);
     
            console.log(`Sent a batch of messages to the queue: ${queueName}`);
                    
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
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>`Service Bus ad alanınız için bağlantı dizesiyle değiştirin.
1. `<QUEUE NAME>`Kuyruğun adıyla değiştirin. 
1. Sonra bu dosyayı yürütmek için komut isteminde komutunu çalıştırın.

    ```console
    node send.js 
    ```
1. Aşağıdaki çıkışı görmelisiniz.

    ```console
    Sent a batch of messages to the queue: myqueue
    ```

## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz düzenleyiciyi açın
2. Adlı bir dosya oluşturun `receive.js` ve içine aşağıdaki kodu yapıştırın.

    ```javascript
    const { delay, ServiceBusClient, ServiceBusMessage } = require("@azure/service-bus");

    // connection string to your Service Bus namespace
    const connectionString = "<CONNECTION STRING TO SERVICE BUS NAMESPACE>"

    // name of the queue
    const queueName = "<QUEUE NAME>"

     async function main() {
        // create a Service Bus client using the connection string to the Service Bus namespace
        const sbClient = new ServiceBusClient(connectionString);
     
        // createReceiver() can also be used to create a receiver for a subscription.
        const receiver = sbClient.createReceiver(queueName);
    
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
3. `<CONNECTION STRING TO SERVICE BUS NAMESPACE>`Service Bus ad alanınız için bağlantı dizesiyle değiştirin.
1. `<QUEUE NAME>`Kuyruğun adıyla değiştirin. 
1. Ardından, bu dosyayı yürütmek için komut isteminde komutunu çalıştırın.

    ```console
    node receive.js
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

Azure portal Service Bus ad alanının **genel bakış** sayfasında **gelen** ve **giden** ileti sayısını görebilirsiniz. Bir dakika beklemeniz gerekebilir ve en son değerleri görmek için sayfayı yenilemeniz gerekebilir. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Gelen ve giden ileti sayısı":::

**Service Bus kuyruğu** sayfasına gitmek Için bu **genel bakış** sayfasında kuyruğu seçin. Bu sayfada **gelen** ve **giden** ileti sayısını da görürsünüz. Ayrıca, sıranın **geçerli boyutu** , **en büyük boyut**, **etkin ileti sayısı** vb. gibi diğer bilgileri de görürsünüz. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Kuyruk ayrıntıları":::
## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki belgelere ve örneklere bakın: 

- [JavaScript için Azure Service Bus istemci kitaplığı](https://www.npmjs.com/package/@azure/service-bus)
- [JavaScript örnekleri](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [TypeScript örnekleri](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [API başvuru belgeleri](/javascript/api/overview/azure/service-bus)
