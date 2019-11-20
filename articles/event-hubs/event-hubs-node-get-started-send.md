---
title: 'Hızlı başlangıç: node. js kullanarak olay gönderme ve alma-Azure Event Hubs'
description: 'Hızlı başlangıç: Bu makale, Azure Event Hubs olayları gönderen bir Node. js uygulaması oluşturmak için bir yol sağlar.'
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: ded2c83bc648e509c8cf00236cdf453b9c61af53
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720564"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Hızlı başlangıç: node. js kullanarak Azure Event Hubs olay gönderme veya olayları alma

Azure Event Hubs, saniye başına milyonlarca olayı alabilen ve işleyesağlayan büyük bir veri akışı platformu ve olay alma hizmetidir. Event Hubs dağıtılan yazılımlar ve cihazlar tarafından oluşturulan olayları, verileri ve telemetrileri işleyebilir ve depolayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Olay Hub’larının ayrıntılı genel bakışı için bkz. [Olay Hub’larına genel bakış](event-hubs-about.md) ve [Olay Hub’ları özellikleri](event-hubs-features.md).

Bu öğreticide, Olay Hub 'ından olayları gönderme veya olayları alma için Node. js uygulamalarının nasıl oluşturulacağı açıklanmaktadır.

> [!NOTE]
> Bu hızlı başlangıcı [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client)’dan örnek olarak indirebilir, `EventHubConnectionString` ve `EventHubName` dizelerini olay hub’ınızdaki değerlerle değiştirebilir ve çalıştırabilirsiniz. Alternatif olarak bu öğreticideki adımları izleyerek kendi çözümünüzü de oluşturabilirsiniz.

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için aşağıdaki önkoşulları karşılamanız gerekir:

- Etkin bir Azure hesabı. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun.
- Node. js sürüm 8. x ve üzeri. [https://nodejs.org](https://nodejs.org)'den en son LTS sürümünü indirin.
- Visual Studio Code (önerilir) veya diğer IDE
- **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. İlk adımda [Azure portalını](https://portal.azure.com) kullanarak Event Hubs türünde bir ad alanı oluşturun, ardından uygulamanızın olay hub’ı ile iletişim kurması için gereken yönetim kimlik bilgilerini edinin. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin, ardından bu öğreticide aşağıdaki adımlarla devam edin. Ardından, makaledeki yönergeleri izleyerek Olay Hub 'ı ad alanı için bağlantı dizesini alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Bu öğreticide daha sonra bağlantı dizesi kullanılır.


### <a name="install-npm-package"></a>NPM paketini yükler
[Event Hubs için NPM paketini](https://www.npmjs.com/package/@azure/event-hubs)yüklemek için, yolunda `npm` olan bir komut istemi açın, dizini örneklerinizin olmasını istediğiniz klasörle değiştirin ve ardından bu komutu çalıştırın

```shell
npm install @azure/event-hubs
```

[Olay Işleyicisi ana bilgisayarı için NPM paketini](https://www.npmjs.com/package/@azure/event-processor-host)yüklemek için, bunun yerine aşağıdaki komutu çalıştırın

```shell
npm install @azure/event-processor-host
```

## <a name="send-events"></a>Olayları gönderme

Bu bölümde, Olay Hub 'ına olayları gönderen bir Node. js uygulamasının nasıl oluşturulacağı gösterilmektedir. 

1. [Visual Studio Code](https://code.visualstudio.com)gibi en sevdiğiniz düzenleyiciyi açın. 
2. `send.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın. Makaledeki yönergeleri izleyerek Olay Hub 'ı ad alanı için bağlantı dizesini alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). 

    ```javascript
    const { EventHubClient } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);

      for (let i = 0; i < 100; i++) {
        const eventData = {body: `Event ${i}`};
        console.log(`Sending message: ${eventData.body}`);
        await client.send(eventData);
      }

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Yukarıdaki koda bağlantı dizesini ve Olay Hub 'ınızın adını girin
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node send.js` çalıştırın. Bu işlem, Olay Hub 'ınıza 100 olay gönderir

Tebrikler! Olayları artık bir olay hub 'ına gönderdiniz.


## <a name="receive-events"></a>Olayları alma

Bu bölümde, bir olay hub 'ında varsayılan tüketici grubunun tek bir bölümünden olayları alan bir Node. js uygulamasının nasıl oluşturulacağı gösterilmektedir. 

1. [Visual Studio Code](https://code.visualstudio.com)gibi en sevdiğiniz düzenleyiciyi açın. 
2. `receive.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın.
    ```javascript
    const { EventHubClient, delay } = require("@azure/event-hubs");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const connectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubsName = "<EVENT HUB NAME>";

    async function main() {
      const client = EventHubClient.createFromConnectionString(connectionString, eventHubsName);
      const allPartitionIds = await client.getPartitionIds();
      const firstPartitionId = allPartitionIds[0];

      const receiveHandler = client.receive(firstPartitionId, eventData => {
        console.log(`Received message: ${eventData.body} from partition ${firstPartitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await receiveHandler.stop();

      await client.close();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });
    ```
3. Yukarıdaki kodda, bağlantı dizesini ve Olay Hub 'ınızın adını girin.
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node receive.js` çalıştırın. Bu, Olay Hub 'ınızdaki varsayılan tüketici grubunun bölümlerinden birindeki olayları alır

Tebrikler! Olay Hub 'ından olayları aldınız.

## <a name="receive-events-using-event-processor-host"></a>Olay İşlemcisi Konağı kullanarak olay alma

Bu bölümde, bir Node. js uygulamasında Azure [Eventprocessorhost](event-hubs-event-processor-host.md) kullanarak bir olay hub 'ından nasıl olay alınacağı gösterilmektedir. EventProcessorHost (EPH), bir olay hub 'ının tüketici grubundaki tüm bölümler genelinde alıcılar oluşturarak bir olay hub 'ından olayları etkin bir şekilde almanıza yardımcı olur. Alınan iletilerde meta verileri bir Azure Depolama Blobu düzenli aralıklarla kontrol ediyor. Bu yaklaşım, daha sonra kaldığınız yerden iletileri almaya devam etmeyi kolaylaştırır.

1. [Visual Studio Code](https://code.visualstudio.com)gibi en sevdiğiniz düzenleyiciyi açın. 
2. `receiveAll.js` adlı bir dosya oluşturun ve içine aşağıdaki kodu yapıştırın.
    ```javascript
    const { EventProcessorHost, delay } = require("@azure/event-processor-host");

    // Connection string - primary key of the Event Hubs namespace. 
    // For example: Endpoint=sb://myeventhubns.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    const eventHubConnectionString = "Endpoint=sb://<EVENT HUBS NAMESPACE NAME>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<SHARED ACCESS KEY>";

    // Name of the event hub. For example: myeventhub
    const eventHubName = "<EVENT HUB NAME>";

    // Azure Storage connection string
    const storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=<STORAGE ACCOUNT NAME>;AccountKey=<STORAGE ACCOUNT KEY>;EndpointSuffix=core.windows.net";

    async function main() {
      const eph = EventProcessorHost.createFromConnectionString(
        "my-eph",
        storageConnectionString,
        "my-storage-container-name",
        eventHubConnectionString,
        {
          eventHubPath: eventHubName,
          onEphError: (error) => {
            console.log("[%s] Error: %O", error);
          }
        }
      );


      eph.start((context, eventData) => {
        console.log(`Received message: ${eventData.body} from partition ${context.partitionId}`);
      }, error => {
        console.log('Error when receiving message: ', error)
      });

      // Sleep for a while before stopping the receive operation.
      await delay(15000);
      await eph.stop();
    }

    main().catch(err => {
      console.log("Error occurred: ", err);
    });

    ```
3. Azure Blob depolama için bağlantı dizesiyle birlikte yukarıdaki koda ve Olay Hub 'ınızın adını girin
4. Sonra bu dosyayı yürütmek için komut isteminde komut `node receiveAll.js` çalıştırın.

Tebrikler! Olay Işlemcisi konağını kullanarak Olay Hub 'ından olayları aldınız. Bu, Olay Hub 'ınızdaki varsayılan tüketici grubunun tüm bölümlerinden gelen olayları alır

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleleri okuyun:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs'ın özellikleri ve terminolojisi](event-hubs-features.md)
- [Event Hubs ile ilgili SSS](event-hubs-faq.md)
- GitHub 'da [Event Hubs](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) ve [olay işlemcisi Konağı](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-processor-host/samples) için diğer Node. js örneklerine göz atın
