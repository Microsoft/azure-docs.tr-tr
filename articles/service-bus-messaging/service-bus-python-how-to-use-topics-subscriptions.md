---
title: Python Azure-ServiceBus paketi sürüm 7.0.0 ile Azure Service Bus konuları ve abonelikleri kullanma
description: Bu makalede, bir konuya ileti göndermek ve abonelikten ileti almak için Python 'un nasıl kullanılacağı gösterilmektedir.
documentationcenter: python
author: spelluru
ms.author: spelluru
ms.date: 11/18/2020
ms.topic: quickstart
ms.devlang: python
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 49e80e277c6df5372341293861d5bda0580f3e8c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537164"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Azure Service Bus konuya ileti gönderin ve aboneliklerden konuya (Python) ileti alın
Bu makalede, bir Service Bus konuya ileti göndermek ve bir aboneliğden konuya ileti almak için Python 'un nasıl kullanılacağı gösterilmektedir. 

## <a name="prerequisites"></a>Önkoşullar
- Azure aboneliği. [Visual Studio veya MSDN abonesi avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)için kaydolabilirsiniz.
- Hızlı başlangıçtaki adımları izleyin [: konuya bir Service Bus konu ve abonelik oluşturmak için Azure Portal kullanın](service-bus-quickstart-topics-subscriptions-portal.md). Bağlantı dizesi, konu adı ve abonelik adı ' nı aklınızda edin. Bu hızlı başlangıç için yalnızca bir abonelik kullanacaksınız. 
- Python 2,7 veya üzeri, [Azure Python SDK] [Azure Python paketi] paketi yüklendi. Daha fazla bilgi için bkz. [Python Yükleme Kılavuzu](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Konu başlığına ileti gönderme

1. Aşağıdaki içeri aktarma ifadesini ekleyin. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Aşağıdaki sabitleri ekleyin. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - `<NAMESPACE CONNECTION STRING>`Ad alanınız için bağlantı dizesiyle değiştirin.
    > - `<TOPIC NAME>`Konunun adıyla değiştirin.
    > - `<SUBSCRIPTION NAME>`Konunun abonelik adıyla değiştirin. 
3. Tek bir ileti göndermek için bir yöntem ekleyin.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Gönderen, oluşturduğunuz konu için istemci görevi gören bir nesnedir. Daha sonra oluşturacaksınız ve bu işleve bağımsız değişken olarak gönderebilirsiniz. 
4. İleti listesi göndermek için bir yöntem ekleyin.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
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
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. İleti göndermek için bir Service Bus istemcisi ve sonra bir konu Gönderen nesnesi oluşturun.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
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
 
## <a name="receive-messages-from-a-subscription"></a>Bir abonelikten ileti alma
Print deyimden sonra aşağıdaki kodu ekleyin. Bu kod, 5 () saniye boyunca yeni iletiler almamaya kadar yeni iletileri sürekli alır `max_wait_time` . 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Tam kod

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

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
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
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

Konunun **Service Bus konu** sayfasını görmek için alt bölmedeki konuyu seçin. Bu sayfada, **iletiler** grafiğinde üç gelen ve üç giden ileti görmeniz gerekir. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Gelen ve giden iletiler":::

Bu sayfada, bir abonelik seçerseniz **Service Bus abonelik** sayfasına ulaşabilirsiniz. Etkin ileti sayısını, atılacak ileti sayısını ve daha fazlasını bu sayfada görebilirsiniz. Bu örnekte, tüm iletiler alındı, bu nedenle etkin ileti sayısı sıfırdır. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Etkin ileti sayısı":::

Alma kodunu açıklama olarak belirlerseniz, etkin ileti sayısını 16 olarak görürsünüz. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Etkin ileti sayısı-alma yok":::

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki belgelere ve örneklere bakın: 

- [Python için Azure Service Bus istemci kitaplığı](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Örnekler](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - **Sync_samples** klasörü, Service Bus ile zaman uyumlu şekilde nasıl etkileşim kuracağınızı gösteren örneklere sahiptir. Bu hızlı başlangıç bölümünde bu yöntemi kullandınız. 
    - **Async_samples** klasörü, zaman uyumsuz bir şekilde Service Bus ile nasıl etkileşim kuracağınızı gösteren örneklere sahiptir. 
- [Azure-ServiceBus başvuru belgeleri](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)
