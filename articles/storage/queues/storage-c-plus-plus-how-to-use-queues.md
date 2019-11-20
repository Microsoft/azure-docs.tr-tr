---
title: Kuyruk depolama (C++)-Azure Storage kullanma
description: Azure 'da kuyruk depolama hizmetini nasıl kullanacağınızı öğrenin. Örnekler içine C++yazılır.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: e268e30e8f8c512dd6efb5a50da45f173e526b62
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721649"
---
# <a name="how-to-use-queue-storage-from-c"></a>Kuyruk depolamayı kullanmaC++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış
Bu kılavuzda, Azure kuyruk depolama hizmetini kullanarak genel senaryoları nasıl gerçekleştireceğiniz gösterilmektedir. Örnekler C++ dilinde yazılmıştır ve [C++ için Azure Depolama İstemci Kitaplığı](https://github.com/Azure/azure-storage-cpp/blob/master/README.md)’nı kullanır. Kapsanan senaryolar sıra iletilerini **ekleme**, göz **atma**, **alma**ve **silme** , Ayrıca kuyruk **oluşturma ve silme**içerir.

> [!NOTE]
> Bu kılavuz C++ için Azure Depolama İstemci Kitaplığı sürüm 1.0.0 ve üzerini hedefler. Önerilen sürüm, [NuGet](https://www.nuget.org/packages/wastorage) ya da [GitHub](https://github.com/Azure/azure-storage-cpp/) üzerinden ulaşılabilen Depolama İstemci Kitaplığı 2.2.0’dır.
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++ uygulaması oluşturma
Bu kılavuzda, bir C++ uygulama içinde çalıştırılabilen depolama özelliklerini kullanacaksınız.

Bunu yapmak için, C++ için Azure Depolama İstemci Kitaplığı’nı yüklemeniz ve Azure aboneliğinizde bir Azure depolama hesabı oluşturmanız gerekir.

C++ için Azure Depolama İstemci Kitaplığı’nı aşağıdaki yöntemleri kullanarak yükleyebilirsiniz:

* **'Un** [Benioku Için C++ Azure Storage istemci kitaplığı](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sayfasında verilen yönergeleri izleyin.
* **Windows:** Visual Studio'da, **Araçlar > NuGet Paket Yöneticisi > Paket Yöneticisi Konsolu**’na tıklayın. [NuGet Paket Yöneticisi konsoluna](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) aşağıdaki komutu yazın ve **ENTER**tuşuna basın.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Uygulamanızı kuyruk depolamaya erişecek şekilde yapılandırma
Aşağıdaki Include deyimlerini, kuyruklara erişmek için Azure depolama API C++ 'lerini kullanmak istediğiniz dosyanın üst kısmına ekleyin:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure depolama bağlantı dizesi ayarlama
Azure depolama istemcisi, veri yönetimi hizmetlerine erişmek üzere uç noktaları ve kimlik bilgilerini depolamak için bir depolama bağlantı dizesi kullanır. Bir istemci uygulamasında çalışırken, depolama hesabınızın adını ve *AccountName* Için [Azure portalında](https://portal.azure.com) listelenen depolama hesabı için depolama erişim anahtarını kullanarak depolama bağlantı dizesini aşağıdaki biçimde sağlamanız gerekir ve *Accountkey* değerleri. Depolama hesapları ve erişim anahtarları hakkında daha fazla bilgi için bkz. [Azure depolama hesapları hakkında](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Bu örnekte bağlantı dizesini tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Uygulamanızı yerel Windows bilgisayarınızda test etmek için [Azure SDK](https://azure.microsoft.com/downloads/)ile birlikte yüklenen Microsoft Azure [depolama öykünücüsünü](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) kullanabilirsiniz. Depolama öykünücüsü, yerel geliştirme makinenizde Azure 'da bulunan BLOB, kuyruk ve tablo hizmetlerini taklit eden bir yardımcı programdır. Aşağıdaki örnekte bağlantı dizesini yerel depolama öykünücünüzde tutmak için nasıl statik bir alan bildirebileceğiniz gösterilmektedir:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Azure Storage öykünücüsü 'nü başlatmak için **Başlat** düğmesini seçin veya **Windows** tuşuna basın. **Azure Storage öykünücüsü**yazmaya başlayın ve uygulamalar listesinden **Microsoft Azure depolama öykünücüsü** ' yi seçin.

Aşağıdaki örnekler, depolama bağlantı dizesini almak için bu iki yöntemden birini kullandığınızı varsayar.

## <a name="retrieve-your-connection-string"></a>Bağlantı dizenizi alma
Depolama hesabı bilgilerinizi temsil etmek için **cloud_storage_account** sınıfını kullanabilirsiniz. Depolama bağlantı dizesinden depolama hesabı bilgilerini almak için **parse** yöntemini kullanabilirsiniz.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Nasıl yapılır: Kuyruk oluştur
Bir **cloud_queue_client** nesnesi, kuyruklar için başvuru nesneleri almanızı sağlar. Aşağıdaki kod, bir **cloud_queue_client** nesnesi oluşturur.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Kullanmak istediğiniz sıraya bir başvuru almak için **cloud_queue_client** nesnesini kullanın. Sıra yoksa kuyruğu oluşturabilirsiniz.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Nasıl yapılır: Kuyruğa bir ileti yerleştirme
Mevcut bir sıraya bir ileti eklemek için ilk olarak yeni bir **cloud_queue_message**oluşturun. Sonra, **add_message** yöntemini çağırın. Bir **cloud_queue_message** , bir dizeden veya bir **bayt** dizisinden oluşturulabilir. Burada, bir kuyruk oluşturan (eğer yoksa) ve 'Hello, World' iletisini yerleştiren bir kod yer almaktadır:

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

## <a name="how-to-peek-at-the-next-message"></a>Nasıl yapılır: Sonraki iletiye gözatın
**Peek_message** yöntemini çağırarak sıradan kaldırmadan bir kuyruğun önündeki iletiye göz atmayı sağlayabilirsiniz.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl yapılır: Kuyruğa alınan iletinin içeriğini değiştirme
Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Eğer ileti bir iş görevini temsil ediyorsa, bu özelliği kullanarak iş görevinin durumunu güncelleştirebilirsiniz. Aşağıdaki kod kuyruk iletisini yeni içeriklerle güncelleştirir ve görünürlük zaman aşımını 60 saniye daha uzatır. Bu, ileti ile ilişkili işin durumunu kaydeder ve istemciye ileti üzerinde çalışmaya devam etmesi için bir dakika daha zaman verir. Bir işleme adımı donanım veya yazılım arızasından dolayı başarısız olursa baştan başlamanıza gerek kalmadan kuyruk iletilerindeki çok adımlı iş akışlarını izlemek için bu yöntemi kullanabilirsiniz. Genellikle bir yeniden deneme sayısı da olur ve ileti n kereden fazla yeniden denense, onu silersiniz. Bu, her işlendiğinde bir uygulama hatası tetikleyen bir iletiye karşı koruma sağlar.

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

## <a name="how-to-de-queue-the-next-message"></a>Nasıl yapılır: Sonraki iletiyi sıradan çıkarmak
Kodunuz, bir iletiyi bir kuyruktan iki adımda çıkarır. **Get_message**çağırdığınızda sıradaki bir sonraki iletiyi alırsınız. **Get_message** tarafından döndürülen bir ileti, bu kuyruktaki diğer kod okuma iletileri için görünmez hale gelir. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, **delete_message**öğesini de çağırmanız gerekir. Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. İleti işlendikten sonra kodunuz **delete_message** doğru çağırır.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Nasıl yapılır: İletilerin kuyruktan çıkarılması için ek seçenekleri kullanma
İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz. İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkinci olarak daha uzun veya daha kısa bir görünmezlik süresi ayarlayarak kodunuzun her iletiyi tamamen işlemesi için daha az veya daha fazla zaman tanıyabilirsiniz. Aşağıdaki kod örneğinde, tek bir çağrıda 20 ileti almak için **get_messages** yöntemi kullanılmaktadır. Sonra her iletiyi bir **for** döngüsü kullanarak işler. Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır. 5 dakikalık tüm iletiler için aynı anda başlayacağını unutmayın. bu nedenle, **get_messages**çağrısından bu yana 5 dakika geçtikten sonra, silinmemiş olan tüm iletiler yeniden görünür hale gelir.

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

## <a name="how-to-get-the-queue-length"></a>Nasıl yapılır: Kuyruk uzunluğu alma
Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. **Download_attributes** yöntemi, ileti sayısı dahil olmak üzere kuyruk hizmeti kuyruk özniteliklerini almayı ister. **Approximate_message_count** yöntemi kuyruktaki iletilerin yaklaşık sayısını alır.

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

## <a name="how-to-delete-a-queue"></a>Nasıl yapılır: Bir kuyruk silme
Bir kuyruğu ve içerdiği tüm iletileri silmek için, kuyruk nesnesi üzerinde **delete_queue_if_exists** yöntemini çağırın.

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

* [Öğesinden blob depolamayı kullanmaC++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Öğesinden Tablo Depolamayı kullanmaC++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Azure depolama kaynaklarını listelemeC++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Başvuru için C++ depolama istemci kitaplığı](https://azure.github.io/azure-storage-cpp)
* [Azure Depolama Belgeleri](https://azure.microsoft.com/documentation/services/storage/)
