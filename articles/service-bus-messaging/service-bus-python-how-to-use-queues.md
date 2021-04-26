---
title: Python Azure-ServiceBus paketi sürüm 7.0.0 ile Azure Service Bus kuyrukları kullanma
description: Bu makalede, Python kullanarak Azure Service Bus kuyruklardan ileti gönderme ve iletileri alma işlemlerinin nasıl yapılacağı gösterilir.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: e982b3f36dbfe2144170d53d07473201abb014ef
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534971"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-python"></a>Azure Service Bus kuyruklarından ileti gönderme ve iletileri alma (Python)
Bu makalede, Python kullanarak Azure Service Bus kuyruklardan ileti gönderme ve iletileri alma işlemlerinin nasıl yapılacağı gösterilir. 

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliği. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Birlikte çalışmak için bir kuyruğunuz yoksa, bir kuyruk oluşturmak için [Service Bus kuyruğu oluşturmak üzere Azure Portal kullanma](service-bus-quickstart-portal.md) adımlarını izleyin. Service Bus ad alanınız ve oluşturduğunuz **kuyruğun** adı için **bağlantı dizesini** aklınızda edin.
- Python [Azure Service Bus](https://pypi.python.org/pypi/azure-servicebus) paketi yüklüyken Python 2,7 veya üzeri. Daha fazla bilgi için bkz. [Python Yükleme Kılavuzu](/azure/developer/python/azure-sdk-install). 

## <a name="send-messages-to-a-queue"></a>Kuyruğa ileti gönderme

1. Aşağıdaki içeri aktarma ifadesini ekleyin. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Aşağıdaki sabitleri ekleyin. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    QUEUE_NAME = "<QUEUE NAME>"
    ```

    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>`Service Bus ad alanınız için bağlantı dizesiyle değiştirin.
    > - `<QUEUE NAME>`Kuyruğun adıyla değiştirin. 
3. Tek bir ileti göndermek için bir yöntem ekleyin.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the queue
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Gönderen, oluşturduğunuz sıra için istemci görevi gören bir nesnedir. Daha sonra oluşturacaksınız ve bu işleve bağımsız değişken olarak gönderebilirsiniz. 
4. İleti listesi göndermek için bir yöntem ekleyin.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the queue
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. İleti toplu işi göndermek için bir yöntem ekleyin.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the queue
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. İleti göndermek için bir Service Bus istemcisi ve sonra bir kuyruk Gönderen nesnesi oluşturun.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Queue Sender object to send messages to the queue
        sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma
Print deyimden sonra aşağıdaki kodu ekleyin. Bu kod, 5 () saniye boyunca yeni iletiler almamaya kadar yeni iletileri sürekli alır `max_wait_time` . 

```python
with servicebus_client:
    # get the Queue Receiver object for the queue
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the queue
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Tam kod

```python
# import os
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
QUEUE_NAME = "<QUEUE NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_queue_sender(queue_name=QUEUE_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_queue_receiver(queue_name=QUEUE_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Uygulamayı çalıştırma
Uygulamayı çalıştırdığınızda aşağıdaki çıktıyı görmeniz gerekir: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

Azure portal, Service Bus ad alanına gidin. **Genel bakış** sayfasında **gelen** ve **giden** ileti sayılarının 16 olduğunu doğrulayın. Sayıları görmüyorsanız, birkaç dakika bekledikten sonra sayfayı yenileyin. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Gelen ve giden ileti sayısı":::

**Service Bus kuyruğu** sayfasına gitmek Için bu **genel bakış** sayfasında kuyruğu seçin. Bu sayfada **gelen** ve **giden** ileti sayısını da görebilirsiniz. Ayrıca, sıranın **geçerli boyutu** ve **etkin ileti sayısı** gibi diğer bilgileri de görürsünüz. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/queue-details.png" alt-text="Kuyruk ayrıntıları":::


## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki belgelere ve örneklere bakın: 

- [Python için Azure Service Bus istemci kitaplığı](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - **Sync_samples** klasörü, Service Bus ile zaman uyumlu şekilde nasıl etkileşim kuracağınızı gösteren örneklere sahiptir. Bu hızlı başlangıç bölümünde bu yöntemi kullandınız. 
    - **Async_samples** klasörü, zaman uyumsuz bir şekilde Service Bus ile nasıl etkileşim kuracağınızı gösteren örneklere sahiptir. 
- [Azure-ServiceBus başvuru belgeleri](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
