---
title: 'Hızlı başlangıç: Python kullanarak olay gönderme ve alma-Azure Event Hubs'
description: 'Hızlı başlangıç: Bu izlenecek yol, Azure Event Hubs olay gönderen veya olayları alan Python betikleri oluşturmayı ve çalıştırmayı gösterir.'
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 9b6c3fb03f696f4142721284a14001eb51153a77
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720556"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python"></a>Hızlı başlangıç: Python kullanarak Event Hubs olay gönderme ve alma

Azure Event Hubs, saniye başına milyonlarca olayı alabilen ve işleyesağlayan büyük bir veri akışı platformu ve olay alma hizmetidir. Event Hubs, dağıtılmış yazılım ve cihazlardan olayları, verileri veya Telemetriyi işleyebilir ve saklayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Event Hubs hakkında daha fazla bilgi için Azure Event Hubs 'de [azure Event Hubs](event-hubs-about.md) ve [özellikleri ve terimleri](event-hubs-features.md)bölümüne bakın.

Bu hızlı başlangıçta, Olay Hub 'ından olayları gönderen ve olayları alan Python uygulamalarının nasıl oluşturulacağı gösterilmektedir. 

> [!NOTE]
> Hızlı başlangıç üzerinden çalışmak yerine, GitHub 'dan [örnek uygulamaları](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) indirebilir ve çalıştırabilirsiniz. `EventHubConnectionString` ve `EventHubName` dizelerini Olay Hub değerlerinizle değiştirin. 

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlayabilmeniz için aşağıdaki önkoşullara sahip olmanız gerekir:

- Azure aboneliği. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).
- [Hızlı başlangıç: Azure Portal kullanarak bir olay hub 'ı oluşturmak](event-hubs-create.md)için bir Active Event Hubs ad alanı ve Olay Hub 'ı oluşturulur. Bu izlenecek yolda daha sonra kullanmak üzere ad alanı ve Olay Hub 'ı adlarını bir yere unutmayın. 
- Event Hubs ad alanınız için paylaşılan erişim anahtarı adı ve birincil anahtar değeri. [Bağlantı dizesini al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)bölümündeki yönergeleri izleyerek erişim anahtarı adını ve değerini alın. Varsayılan erişim anahtarı adı **RootManageSharedAccessKey**' dir. Bu izlenecek yolda daha sonra kullanmak için anahtar adını ve birincil anahtar değerini kopyalayın. 
- `pip` yüklenip güncelleştirildiğinden Python 3,4 veya sonraki bir sürümü.
- Event Hubs için Python paketi. Paketi yüklemek için bu komutu, yolunda Python içeren bir komut isteminde çalıştırın: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Bu hızlı başlangıçtaki kod, Event Hubs SDK 'sının geçerli kararlı sürüm 1.3.1 kullanır. SDK 'nın önizleme sürümünü kullanan örnek kod için bkz. [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Olayları gönderme

Olayları bir olay hub 'ına gönderen bir Python uygulaması oluşturmak için:

1. [Visual Studio Code](https://code.visualstudio.com/) gibi en sevdiğiniz Python düzenleyicisini açın
2. *Send.py*adlı yeni bir dosya oluşturun. Bu betik, 100 olaylarını Olay Hub 'ınıza gönderir.
3. Aşağıdaki *kodu, Event Hubs*\<ad alanı >, \<eventhub >, \<accesskeyname > ve \<birincil anahtar değeri > değerlerinizle değiştirin: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Dosyayı kaydedin. 

Betiği çalıştırmak için *Send.py*kaydettiğiniz dizinden şu komutu çalıştırın:

```cmd
start python send.py
```

Tebrikler! Bir olay hub'ına ileti gönderdiniz.

## <a name="receive-events"></a>Olayları alma

Olay Hub 'ından olayları alan bir Python uygulaması oluşturmak için:

1. Python Düzenleyicinizde *recv.py*adlı bir dosya oluşturun.
2. Aşağıdaki *kodu, Event Hubs*\<ad alanı >, \<eventhub >, \<accesskeyname > ve \<birincil anahtar değeri > değerlerinizle değiştirin: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Dosyayı kaydedin.

Betiği çalıştırmak için *recv.py*kaydettiğiniz dizinden şu komutu çalıştırın:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Sonraki adımlar
Event Hubs hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs'ın özellikleri ve terminolojisi](event-hubs-features.md)
- [Event Hubs ile ilgili SSS](event-hubs-faq.md)

