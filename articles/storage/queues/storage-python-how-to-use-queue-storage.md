---
title: Python 'da Azure kuyruk depolama kullanma
description: Sıraları oluşturup silmek ve iletileri eklemek, almak ve silmek için Python 'dan Azure kuyruk depolama 'yı kullanmayı öğrenin.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: 32dc54673674ac10f8dc1e3351133ca33ef7665c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277428"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Python 'da Azure kuyruk depolama kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Genel Bakış

Bu makalede, Azure kuyruk depolama hizmetini kullanan yaygın senaryolar gösterilmektedir. Kapsanan senaryolar sıra iletilerini ekleme, göz atma, alma ve silmeyi içerir. Sıra oluşturma ve silme için kod de ele alınmıştır.

Bu makaledeki örnekler Python 'da yazılmıştır ve [Python Için Azure kuyruk depolama istemci kitaplığı](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)' nı kullanır. Kuyruklar hakkında daha fazla bilgi için [sonraki adımlar](#next-steps) bölümüne bakın.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Python için Azure depolama SDK 'sını indirme ve yükleme

[Python Için Azure depolama SDK 'sı](https://github.com/azure/azure-storage-python) , Python v 2.7, v 3.3 veya üstünü gerektirir.

### <a name="install-via-pypi"></a>PyPI aracılığıyla yüklemesi

Python paket dizini (Pypı) aracılığıyla yüklemek için şunu yazın:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Python v 0.36 veya daha önceki bir sürümü için Azure Storage SDK 'dan yükseltiyorsanız, `pip uninstall azure-storage` en son paketi yüklemeden önce kullanarak eski SDK 'yı kaldırın.

Alternatif yükleme yöntemleri için bkz. [Python Için Azure SDK](https://github.com/Azure/Azure-SDK-for-Python).

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Uygulamanızı kuyruk depolamaya erişecek şekilde yapılandırma

# <a name="python-v12"></a>[Python V12](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient)Nesnesi bir kuyruk ile çalışmanıza olanak sağlar. Bir Azure kuyruğuna programlı bir şekilde erişmek istediğiniz herhangi bir Python dosyasının yanına aşağıdaki kodu ekleyin:

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true)Nesnesi kuyruklarla çalışmanıza olanak sağlar. Aşağıdaki kod bir nesnesi oluşturur `QueueService` . Azure Storage 'a programlı bir şekilde erişmek istediğiniz herhangi bir Python dosyasının yanına aşağıdaki kodu ekleyin:

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

`os`Paket, bir ortam değişkenini almak için destek sağlar. `uuid`Paket, bir kuyruk adı için benzersiz bir tanımlayıcı oluşturma desteği sağlar.

## <a name="create-a-queue"></a>Bir kuyruk oluşturma

Bağlantı dizesi, `AZURE_STORAGE_CONNECTION_STRING` daha önce ayarlanan ortam değişkeni üzerinden alınır.

# <a name="python-v12"></a>[Python V12](#tab/python)

Aşağıdaki kod, `QueueClient` depolama bağlantı dizesi kullanarak bir nesnesi oluşturur.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aşağıdaki kod, `QueueService` depolama bağlantı dizesi kullanarak bir nesnesi oluşturur.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

Azure kuyruk iletileri metin olarak depolanır. İkili verileri depolamak istiyorsanız, kuyruğa bir ileti koymadan önce Base64 kodlaması ve kod çözme işlevleri ayarlayın.

# <a name="python-v12"></a>[Python V12](#tab/python)

İstemci nesnesini oluştururken Base64 kodlamayı ve kod çözme işlevlerini yapılandırın.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Kuyruk depolama nesnesinde Base64 kodlamayı ve kod çözme işlevlerini yapılandırın.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>Kuyruğa bir ileti yerleştirme

# <a name="python-v12"></a>[Python V12](#tab/python)

Bir kuyruğa ileti eklemek için [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) yöntemini kullanın.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Bir kuyruğa ileti eklemek için, [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) yöntemini kullanarak yeni bir ileti oluşturun ve kuyruğa ekleyin.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>İletilere göz atın

# <a name="python-v12"></a>[Python V12](#tab/python)

Yöntemini çağırarak iletileri kuyruktan kaldırmadan göz atmayı sağlayabilirsiniz [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . Varsayılan olarak, bu yöntem tek bir iletiye göz atar.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Yöntemini çağırarak iletileri kuyruktan kaldırmadan göz atmayı sağlayabilirsiniz [`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) . Varsayılan olarak, bu yöntem tek bir iletiye göz atar.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Kuyruğa alınan iletinin içeriğini değiştirme

Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. İleti bir görevi temsil ediyorsa, görevin durumunu güncelleştirmek için bu özelliği kullanabilirsiniz.

# <a name="python-v12"></a>[Python V12](#tab/python)

Aşağıdaki kod, [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) bir iletiyi güncelleştirmek için yöntemini kullanır. Görünürlük zaman aşımı 0 olarak ayarlanır, yani ileti hemen görünür ve içerik güncellenir.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aşağıdaki kod, [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) bir iletiyi güncelleştirmek için yöntemini kullanır. Görünürlük zaman aşımı 0 olarak ayarlanır, yani ileti hemen görünür ve içerik güncellenir.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>Kuyruk uzunluğu alma

Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz.

# <a name="python-v12"></a>[Python V12](#tab/python)

[Get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) yöntemi, dahil Queue özelliklerini döndürür `approximate_message_count` .

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-)Yöntemi, dahil sıra özelliklerini döndürür `approximate_message_count` .

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

Sonuç yalnızca, hizmet isteğinize yanıt verdikten sonra iletiler eklenebildiğinden veya kaldırılacağından yaklaşık olur.

## <a name="dequeue-messages"></a>İletileri sıradan çıkarma

İki adımda kuyruktan bir ileti kaldırın. Kodunuz bir iletiyi işleyemezse, bu iki adımlı işlem aynı iletiyi almanızı ve yeniden denemeyi sağlar. `delete_message`İleti başarıyla işlendikten sonra çağırın.

# <a name="python-v12"></a>[Python V12](#tab/python)

[Receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)çağırdığınızda sıradaki bir sonraki iletiyi varsayılan olarak alırsınız. Öğesinden döndürülen bir ileti, `receive_messages` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-)de çağırmanız gerekir.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[Get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)çağırdığınızda sıradaki bir sonraki iletiyi varsayılan olarak alırsınız. Öğesinden döndürülen bir ileti, `get_messages` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-)de çağırmanız gerekir.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz. İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkinci olarak daha uzun veya daha kısa bir görünmezlik süresi ayarlayarak kodunuzun her iletiyi tamamen işlemesi için daha az veya daha fazla zaman tanıyabilirsiniz.

# <a name="python-v12"></a>[Python V12](#tab/python)

Aşağıdaki kod örneği, [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) toplu olarak iletileri almak için yöntemini kullanır. Ardından, iç içe geçmiş bir döngü kullanarak her bir yığın içindeki her iletiyi işler `for` . Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Aşağıdaki kod örneği, [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) tek bir çağrıda 16 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `for` . Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>Bir kuyruk silme

# <a name="python-v12"></a>[Python V12](#tab/python)

Bir kuyruğu ve içerdiği tüm iletileri silmek için [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) yöntemini çağırın.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Bir kuyruğu ve içerdiği tüm iletileri silmek için [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) yöntemini çağırın.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>Sonraki adımlar

Sıra depolamanın temellerini öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

- [Azure kuyruk depolama Python API başvurusu](/python/api/azure-storage-queue)
- [Python Geliştirici Merkezi](https://azure.microsoft.com/develop/python/)
- [Azure depolama REST API başvurusu](/rest/api/storageservices/)
