---
title: 'Hızlı başlangıç: Azure kuyruk depolama istemci kitaplığı V12-Python'
description: Bir kuyruk oluşturmak ve bu iletiye ileti eklemek için Azure kuyruk depolama istemci kitaplığı V12 for Python 'un nasıl kullanılacağını öğrenin. Sonra sıradan iletileri okumayı ve silmeyi öğrenin. Ayrıca, bir kuyruğu silmeyi de öğreneceksiniz.
author: twooley
ms.author: twooley
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 68f68c32b160757e82d59b1dd5ee4847a06ec698
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534353"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Hızlı başlangıç: Python için Azure kuyruk depolama istemci kitaplığı V12

Python için Azure kuyruk depolama istemci kitaplığı V12 ile çalışmaya başlayın. Azure kuyruk depolaması, daha sonra almak ve işlemek üzere çok sayıda ileti depolamaya yönelik bir hizmettir. Paketi yüklemek ve temel görevler için örnek kodu denemek üzere bu adımları izleyin.

Python için Azure kuyruk depolama istemci kitaplığı V12 şu şekilde kullanın:

- Bir kuyruk oluşturma
- Bir kuyruğa ileti ekleme
- Kuyruktaki iletilere göz atın
- Kuyruktaki bir iletiyi güncelleştirme
- Kuyruktan ileti alma
- Kuyruktaki iletileri silme
- Bir kuyruk silme

Ek kaynaklar:

- [API başvuru belgeleri](/python/api/azure-storage-queue/index)
- [Kitaplık kaynak kodu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Paket (Python paket dizini)](https://pypi.org/project/azure-storage-queue/)
- [Örnekler](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/)
- Azure depolama hesabı- [depolama hesabı oluşturma](../common/storage-account-create.md)
- İşletim sisteminiz için [Python](https://www.python.org/downloads/) -2,7 veya 3.6 +

## <a name="setting-up"></a>Ayarlanıyor

Bu bölüm, Python için Azure kuyruk depolama istemci kitaplığı V12 ile çalışmak üzere bir proje hazırlama konusunda size yol gösterir.

### <a name="create-the-project"></a>Proje oluşturma

Adlı bir Python uygulaması oluşturun `queues-quickstart-v12` .

1. Konsol penceresinde (cmd, PowerShell veya Bash gibi), proje için yeni bir dizin oluşturun.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Yeni oluşturulan dizine geçiş yapın `queues-quickstart-v12` .

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Paketi yükler

Komutunu kullanarak Python için Azure Blob Storage istemci kitaplığı 'nı paketi ' ni yükler `pip install` .

```console
pip install azure-storage-queue
```

Bu komut, Python paketi için Azure kuyruk depolama istemci kitaplığını ve bağımlı olduğu tüm kitaplıkları kurar. Bu durumda, yalnızca Python için Azure çekirdek kitaplığı vardır.

### <a name="set-up-the-app-framework"></a>Uygulama çerçevesini ayarlama

1. Kod Düzenleyicinizde yeni bir metin dosyası açın
1. `import`Deyim Ekle
1. Çok temel özel durum işleme dahil olmak üzere programın yapısını oluşturma

    Kod şu şekildedir:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Yeni dosyayı `queues-quickstart-v12.py` `queues-quickstart-v12` dizine kaydedin.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Nesne modeli

Azure Kuyruk Depolama, çok sayıda iletiyi depolamaya yönelik bir hizmettir. Kuyruk iletisi boyutu 64 KB 'ye kadar olabilir. Bir kuyruk, depolama hesabının toplam kapasite sınırına kadar milyonlarca ileti içerebilir. Kuyruklar genellikle zaman uyumsuz olarak işlenecek iş biriktirme listesi oluşturmak için kullanılır. Kuyruk depolama, üç tür kaynak sunar:

- Depolama hesabı
- Depolama hesabındaki bir kuyruk
- Kuyruktaki iletiler

Aşağıdaki diyagramda bu kaynaklar arasındaki ilişki gösterilmektedir.

![Kuyruk depolama mimarisi diyagramı](./media/storage-queues-introduction/queue1.png)

Şu kaynaklarla etkileşim kurmak için aşağıdaki Python sınıflarını kullanın:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient):, `QueueServiceClient` Depolama hesabınızdaki tüm kuyrukları yönetmenizi sağlar.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Sınıfı tek bir kuyruğu ve iletilerini yönetmenizi ve düzenlemenizi sağlar.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Sınıfı, bir sıraya çağrılırken döndürülen ayrı nesneleri temsil eder [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) .

## <a name="code-examples"></a>Kod örnekleri

Bu örnek kod parçacıkları, Python için Azure kuyruk depolama istemci kitaplığı ile aşağıdaki eylemleri nasıl yapılacağını gösterir:

- [Bağlantı dizesini alma](#get-the-connection-string)
- [Bir kuyruk oluşturma](#create-a-queue)
- [Bir kuyruğa ileti ekleme](#add-messages-to-a-queue)
- [Kuyruktaki iletilere göz atın](#peek-at-messages-in-a-queue)
- [Kuyruktaki bir iletiyi güncelleştirme](#update-a-message-in-a-queue)
- [Kuyruktan ileti alma](#receive-messages-from-a-queue)
- [Kuyruktaki iletileri silme](#delete-messages-from-a-queue)
- [Bir kuyruk silme](#delete-a-queue)

### <a name="get-the-connection-string"></a>Bağlantı dizesini alma

Aşağıdaki kod, depolama hesabı için bağlantı dizesini alır. Bağlantı dizesi, [depolama Bağlantı dizenizi yapılandırma](#configure-your-storage-connection-string) bölümünde oluşturulan ortam değişkenini saklı tutulur.

Bu kodu bloğunun içine ekleyin `try` :

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Bir kuyruk oluşturma

Yeni sıra için bir ad belirleyin. Aşağıdaki kod, benzersiz olduğundan emin olmak için kuyruk adına bir UUID değeri ekler.

> [!IMPORTANT]
> Kuyruk adları yalnızca küçük harf, sayı ve kısa çizgi içerebilir ve bir harf veya sayı ile başlamalıdır. Her kısa çizginin önünde ve arkasında kısa çizgi dışında bir karakter bulunmalıdır. Ad ayrıca 3 ila 63 karakter uzunluğunda olmalıdır. Daha fazla bilgi için bkz. [ad kuyrukları ve meta verileri](/rest/api/storageservices/naming-queues-and-metadata).

Sınıfının bir örneğini oluşturun [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) . Ardından, [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) depolama hesabınızda kuyruğu oluşturmak için yöntemini çağırın.

Bu kodu bloğunun sonuna ekleyin `try` :

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Bir kuyruğa ileti ekleme

Aşağıdaki kod parçacığı, yöntemini çağırarak kuyruğa ileti ekler [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) . Ayrıca, [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) üçüncü çağrıdan döndürülen öğesini de kaydeder `send_message` . , `saved_message` Programın ilerleyen kısımlarında ileti içeriğini güncelleştirmek için kullanılır.

Bu kodu bloğunun sonuna ekleyin `try` :

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Kuyruktaki iletilere göz atın

Yöntemi çağırarak kuyruktaki iletilere göz atın [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) . Bu yöntem, sıranın önüne bir veya daha fazla ileti alır ancak iletinin görünürlüğünü değiştirmez.

Bu kodu bloğunun sonuna ekleyin `try` :

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Kuyruktaki bir iletiyi güncelleştirme

Yöntemini çağırarak bir iletinin içeriğini güncelleştirin [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) . Bu yöntem, bir iletinin görünürlük zaman aşımını ve içeriğini değiştirebilir. İleti içeriği, boyutu 64 KB 'a kadar olan bir UTF-8 kodlu dize olmalıdır. Yeni içerikle birlikte, kodda daha önce kaydedilen iletiden değer geçirin. `saved_message`Değerler güncelleştirilecek iletiyi belirler.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Kuyruktan ileti alma

Yöntemini çağırarak önceden eklenmiş iletileri indirin [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) .

Bu kodu bloğunun sonuna ekleyin `try` :

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Kuyruktaki iletileri silme

İletileri alındıktan ve işlendikten sonra kuyruktan silin. Bu durumda, işleme yalnızca konsolda iletiyi görüntülüyor.

Uygulama, `input` iletileri işleyerek ve silmeden önce çağırarak kullanıcı girişi için duraklatılır. [Azure Portal](https://portal.azure.com) , kaynakların silinmeden önce doğru şekilde oluşturulduğunu doğrulayın. Açıkça silinmeyen tüm iletiler, daha sonra bu işlemleri işlemek için bir süre sonra sırada görünür hale gelir.

Bu kodu bloğunun sonuna ekleyin `try` :

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Bir kuyruk silme

Aşağıdaki kod, yöntemi kullanılarak sıranın silindiği, uygulamanın oluşturduğu kaynakları temizler [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) .

Bu kodu bloğunun sonuna ekleyin `try` ve dosyayı kaydedin:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Kodu çalıştırma

Bu uygulama, bir Azure kuyruğuna üç ileti oluşturur ve ekler. Kod kuyruktaki iletileri listeler, ardından kuyruğu silmeden önce bunları alır ve siler.

Konsol pencerenizde, dosyayı içeren dizine gidin `queues-quickstart-v12.py` ve `python` uygulamayı çalıştırmak için aşağıdaki komutu kullanın.

```console
python queues-quickstart-v12.py
```

Uygulamanın çıktısı aşağıdaki örneğe benzer:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Uygulama iletileri almadan önce durakladığında, [Azure Portal](https://portal.azure.com)depolama hesabınızı kontrol edin. İletilerin kuyrukta olduğunu doğrulayın.

`Enter`İletileri almak ve silmek için tuşuna basın. İstendiğinde, `Enter` kuyruğu silmek ve tanıtımı sona almak için tuşa basın.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir sıranın nasıl oluşturulacağını ve Python kodunu kullanarak iletiye nasıl ileti ekleneceğini öğrendiniz. Ardından iletileri göz atmayı, almayı ve silmeyi öğrendiniz. Son olarak, bir ileti sırasının nasıl silineceğini öğrendiniz.

Öğreticiler, örnekler, hızlı ve diğer belgeler için şu adresi ziyaret edin:

> [!div class="nextstepaction"]
> [Python geliştiricileri için Azure](/azure/python/)

- Daha fazla bilgi edinmek için bkz. [Python Için Azure depolama kitaplıkları](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- Daha fazla Azure kuyruk depolama örneği uygulaması için bkz. [Python örnekleri Için Azure kuyruk depolama istemci kitaplığı V12](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
