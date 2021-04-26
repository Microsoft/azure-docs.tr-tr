---
title: Kuyruk depolama (C++)-Azure depolama kullanma
description: Azure 'da kuyruk depolama hizmetini nasıl kullanacağınızı öğrenin. Örnekler C++ dilinde yazılır.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 07/16/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 0e8274378ed321dc7c3d6ddcfb85f5bb0a2f7993
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276289"
---
# <a name="how-to-use-queue-storage-from-c"></a>C++’tan Kuyruk Depolama kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış

Bu kılavuzda, Azure kuyruk depolama hizmetini kullanarak genel senaryoları nasıl gerçekleştireceğiniz gösterilmektedir. Örnekler C++ dilinde yazılır ve [c++ Için Azure Storage istemci kitaplığını](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)kullanır. Kapsanan senaryolar sıra iletilerini **ekleme**, göz **atma**, **alma** ve **silme** , Ayrıca kuyruk **oluşturma ve silme** içerir.

> [!NOTE]
> Bu kılavuz, C++ v 1.0.0 ve üzeri için Azure Storage istemci kitaplığı 'nı hedefler. Önerilen sürüm, [NuGet](https://www.nuget.org/packages/wastorage) veya [GitHub](https://github.com/Azure/azure-storage-cpp/)aracılığıyla kullanılabilen Azure Storage istemci kitaplığı v 2.2.0 ' dir.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ uygulaması oluşturma

Bu kılavuzda, bir C++ uygulaması içinde çalıştırılabilen depolama özelliklerini kullanacaksınız.

Bunu yapmak için, C++ için Azure Storage istemci kitaplığı 'nı yüklemeniz ve Azure aboneliğinizde bir Azure depolama hesabı oluşturmanız gerekir.

<!-- docutune:casing "Getting Started on Linux" -->

C++ için Azure Storage istemci kitaplığı 'nı yüklemek için aşağıdaki yöntemleri kullanabilirsiniz:

- **Linux:** [C++ Için Azure depolama istemci KITAPLıĞı Benioku dosyasında verilen yönergeleri izleyin: Linux üzerinde çalışmaya](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) başlama sayfası.
- **Windows:** Windows üzerinde, Dependency Manager olarak [vcpkg](https://github.com/microsoft/vcpkg) kullanın. Başlatmak için [hızlı](https://github.com/microsoft/vcpkg#quick-start) başlangıcı izleyin `vcpkg` . Ardından, kitaplığı yüklemek için aşağıdaki komutu kullanın:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Kaynak kodu oluşturma ve [Benioku](https://github.com/Azure/azure-storage-cpp#download--install) dosyasında NuGet 'e aktarma hakkında bir kılavuz bulabilirsiniz.

## <a name="configure-your-application-to-access-queue-storage"></a>Uygulamanızı kuyruk depolamaya erişecek şekilde yapılandırma

Kuyruklara erişmek için Azure depolama API 'Lerini kullanmak istediğiniz C++ dosyasının en üstüne aşağıdaki Include deyimlerini ekleyin:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Depolama bağlantı dizesini ayarlama

Azure Storage istemcisi, veri yönetimi hizmetlerine erişim için uç noktaları ve kimlik bilgilerini depolamak üzere bir depolama bağlantı dizesi kullanır. Bir istemci uygulamasında çalışırken, depolama hesabınızın adını ve değerleri için [Azure Portal](https://portal.azure.com) listelenen depolama hesabı için depolama erişim anahtarı ' nı kullanarak depolama bağlantı dizesini aşağıdaki biçimde sağlamanız gerekir `AccountName` `AccountKey` . Depolama hesapları ve erişim anahtarları hakkında daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Bu örnekte bağlantı dizesini tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Uygulamanızı yerel Windows bilgisayarınızda test etmek için [Azurite depolama öykünücüsünü](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)kullanabilirsiniz. Azurite, yerel geliştirme makinenizde Azure Blob depolama ve kuyruk depolamayı taklit eden bir yardımcı programdır. Aşağıdaki örnekte bağlantı dizesini yerel depolama öykünücünüzde tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azurite 'yi başlatmak için bkz. [Yerel Azure depolama geliştirmesi Için Azurite öykünücüsünü kullanma](../common/storage-use-azurite.md).

Aşağıdaki örnekler, depolama bağlantı dizesini almak için bu iki yöntemden birini kullandığınızı varsayar.

## <a name="retrieve-your-connection-string"></a>Bağlantı dizenizi alma

`cloud_storage_account`Depolama hesabı bilgilerinizi temsil etmek için sınıfını kullanabilirsiniz. Depolama hesabı bilgilerinizi depolama bağlantı dizesinden almak için `parse` yöntemini kullanabilirsiniz.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Nasıl yapılır: kuyruk oluşturma

Bir `cloud_queue_client` nesne, kuyruklar için başvuru nesneleri almanızı sağlar. Aşağıdaki kod bir nesnesi oluşturur `cloud_queue_client` .

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Kullanmak istediğiniz `cloud_queue_client` kuyruğa bir başvuru almak için nesnesini kullanın. Sıra yoksa kuyruğu oluşturabilirsiniz.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Nasıl yapılır: bir kuyruğa ileti ekleme

Mevcut bir kuyruğa ileti eklemek için ilk olarak yeni bir oluştur `cloud_queue_message` . Sonra, yöntemini çağırın `add_message` . Bir `cloud_queue_message` dizeden (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir. Bir sıra (yoksa) oluşturan ve iletiyi ekleyen kod aşağıda verilmiştir `Hello, World` :

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Nasıl yapılır: bir sonraki iletiye göz atma

Yöntemini çağırarak bir kuyruğun ön tarafındaki iletiye göz atmayı sağlayabilirsiniz `peek_message` .

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl yapılır: kuyruğa alınan iletinin içeriğini değiştirme

Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Eğer ileti bir iş görevini temsil ediyorsa, bu özelliği kullanarak iş görevinin durumunu güncelleştirebilirsiniz. Aşağıdaki kod kuyruk iletisini yeni içeriklerle güncelleştirir ve görünürlük zaman aşımını 60 saniye daha uzatır. Bu, ileti ile ilişkili işin durumunu kaydeder ve istemciye ileti üzerinde çalışmaya devam etmesi için bir dakika daha zaman verir. Bu tekniği, işlem adımı donanım veya yazılım arızası nedeniyle başarısız olursa baştan başlamak zorunda kalmadan, kuyruk iletilerinde çoklu adım iş akışlarını izlemek için kullanabilirsiniz. Genellikle bir yeniden deneme sayacı tutmanı gerekir ve bir ileti n seferden daha fazla yeniden denenirse, silebilirsiniz. Bu, her işlendiğinde bir uygulama hatası tetikleyen bir iletiye karşı koruma sağlar.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-dequeue-the-next-message"></a>Nasıl yapılır: sonraki iletiyi sıradan çıkarma

Kodunuz, iki adımda bir kuyruktan bir ileti sırasını kaldırır. `get_message`' İ çağırdığınızda bir kuyruktaki sonraki iletiyi alırsınız. Öğesinden döndürülen bir ileti, `get_message` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini de çağırmanız gerekir `delete_message` . Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. `delete_message`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-use-additional-options-for-dequeuing-messages"></a>Nasıl yapılır: dequeuing iletileri için ek seçenekler kullanma

İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz. İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkinci olarak daha uzun veya daha kısa bir görünmezlik süresi ayarlayarak kodunuzun her iletiyi tamamen işlemesi için daha az veya daha fazla zaman tanıyabilirsiniz. Aşağıdaki kod örneği, `get_messages` tek bir çağrıda 20 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `for` . Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır. Beş dakikalık tüm iletiler için aynı anda başlayacağını unutmayın. bu nedenle, çağrısından bu yana beş dakika geçtikten sonra `get_messages` , silinmemiş olan tüm iletiler yeniden görünür hale gelir.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Nasıl yapılır: kuyruk uzunluğunu alma

Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. `download_attributes`Yöntemi, ileti sayısı dahil olmak üzere sıra özelliklerini döndürür. `approximate_message_count`Yöntemi kuyruktaki iletilerin yaklaşık sayısını alır.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Nasıl yapılır: kuyruğu silme

Bir kuyruğu ve içerdiği tüm iletileri silmek için, `delete_queue_if_exists` kuyruk nesnesi üzerinde yöntemini çağırın.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Sonraki adımlar

Sıra depolamanın temellerini öğrendiğinize göre, Azure Storage hakkında daha fazla bilgi edinmek için bu bağlantıları izleyin.

- [C++ ' dan blob depolamayı kullanma](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [C++ ' dan Tablo Depolamayı kullanma](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Azure Depolama Kaynaklarını C++ dilinde listeleme](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [C++ için Azure depolama istemci kitaplığı başvurusu](https://azure.github.io/azure-storage-cpp)
- [Azure Depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/)
