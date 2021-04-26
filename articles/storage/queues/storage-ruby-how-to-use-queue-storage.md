---
title: Ruby-Azure depolama 'dan kuyruk depolama kullanma
description: Kuyrukları oluşturmak ve silmek ve ileti eklemek, almak ve silmek için Azure kuyruk depolama 'yı nasıl kullanacağınızı öğrenin. Ruby dilinde yazılan örnekler.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 257b435f0136884e8568f4201794a7ce5cf0c209
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275864"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Ruby 'den kuyruk depolama kullanma

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış

Bu kılavuzda, Microsoft Azure kuyruğu depolama hizmetini kullanarak yaygın senaryoların nasıl gerçekleştirileceği gösterilmektedir. Örnekler, Ruby Azure API 'SI kullanılarak yazılır. Kapsanan senaryolar sıra iletilerini **ekleme**, göz **atma**, **alma** ve **silme** , Ayrıca kuyruk **oluşturma ve silme** içerir.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby uygulaması oluşturma

Ruby uygulaması oluşturun. Yönergeler için bkz. [Linux üzerinde App Service Ruby uygulaması oluşturma](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Uygulamanızı depolamaya erişecek şekilde yapılandırma

Azure Storage 'ı kullanmak için, depolama REST hizmetleriyle iletişim kuran bir dizi kullanışlı kitaplık içeren Ruby Azure paketini indirmeniz ve kullanmanız gerekir.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Paketi edinmek için RubyGems kullanma

1. PowerShell (Windows), Terminal (Mac) veya Bash (Unix) gibi bir komut satırı arabirimi kullanın.
2. `gem install Azure`GED ve bağımlılıklarını yüklemek için komut penceresine yazın.

### <a name="import-the-package"></a>Paketi içeri aktarma

En sevdiğiniz metin düzenleyiciyi kullanarak, depolamayı kullanmayı düşündüğünüz Ruby dosyasının en üstüne aşağıdakileri ekleyin:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure depolama bağlantısı kurma

Azure modülü, ortam değişkenlerini `AZURE_STORAGE_ACCOUNT` ve `AZURE_STORAGE_ACCESS_KEY` Azure depolama hesabınıza bağlanmak için gereken bilgileri okuyacaktır. Bu ortam değişkenleri ayarlanmamışsa, aşağıdaki kodla kullanmadan önce hesap bilgilerini belirtmeniz gerekir `Azure::QueueService` :

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Bu değerleri Azure portalında bir klasik veya Kaynak Yöneticisi depolama hesabından edinmek için:

1. [Azure Portal](https://portal.azure.com)oturum açın.
2. Kullanmak istediğiniz depolama hesabına gidin.
3. Sağdaki **Ayarlar** dikey penceresinde **erişim anahtarları**' na tıklayın.
4. Görüntülenen **erişim tuşları** dikey penceresinde, erişim anahtarı 1 ve erişim anahtarı 2 ' yi görürsünüz. Bunlardan birini kullanabilirsiniz.
5. Anahtarı panoya kopyalamak için Kopyala simgesine tıklayın.

## <a name="how-to-create-a-queue"></a>Nasıl yapılır: kuyruk oluşturma

Aşağıdaki kod `Azure::QueueService` , kuyruklar ile çalışmanıza olanak sağlayan bir nesnesi oluşturur.

```ruby
azure_queue_service = Azure::QueueService.new
```

`create_queue()`Belirtilen ada sahip bir sıra oluşturmak için yöntemini kullanın.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Nasıl yapılır: bir kuyruğa ileti ekleme

Bir kuyruğa ileti eklemek için, `create_message()` yöntemini kullanarak yeni bir ileti oluşturun ve kuyruğa ekleyin.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Nasıl yapılır: bir sonraki iletiye göz atma

Yöntemini çağırarak bir kuyruğun ön tarafındaki iletiye göz atmayı sağlayabilirsiniz `peek_messages()` . Varsayılan olarak `peek_messages()` tek bir iletiye göz atar. Ayrıca, kaç tane ileti göz atmayı istediğinizi de belirtebilirsiniz.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Nasıl yapılır: sonraki iletiyi sıradan çıkarma

İki adımda kuyruktan bir ileti kaldırabilirsiniz.

1. `list_messages()`' İ çağırdığınızda bir kuyruktaki bir sonraki iletiyi varsayılan olarak alırsınız. Ayrıca, kaç tane ileti almak istediğinizi de belirtebilirsiniz. Öğesinden döndürülen iletiler, `list_messages()` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Görünürlük zaman aşımını bir parametre olarak Saniyeler içinde geçitirsiniz.
2. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini de çağırmanız gerekir `delete_message()` .

Bir iletiyi kaldırmanın bu iki adımlı işlemi, kodunuz, donanım veya yazılım arızasından kaynaklanan bir iletiyi işleyemediğinde, kodunuzun başka bir örneğinin aynı mesajı almasını ve yeniden denemesini sağlar. `delete_message()`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl yapılır: kuyruğa alınan iletinin içeriğini değiştirme

Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Aşağıdaki kod, `update_message()` bir iletiyi güncelleştirmek için yöntemini kullanır. Yöntemi, kuyruk iletisinin pop alındığını ve `DateTime` iletinin kuyrukta görünür olacağını temsil eden UTC değerini içeren bir kayıt düzeni döndürür.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Nasıl yapılır: sıradan sıraya alma iletileri için ek seçenekler

İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz.

1. Toplu bir ileti alabilirsiniz.
2. Daha uzun veya daha kısa görünürlük zaman aşımı ayarlayabilir, böylece her iletiyi tamamen işlemek için kodunuzun daha fazla veya daha az zaman aşımına uğramamasını sağlayabilirsiniz.

Aşağıdaki kod örneği, `list_messages()` tek bir çağrıda 15 ileti almak için yöntemini kullanır. Ardından her iletiyi yazdırır ve siler. Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Nasıl yapılır: kuyruk uzunluğunu alma

Kuyruktaki ileti sayısını tahmin edebilirsiniz. `get_queue_metadata()`Yöntemi, yaklaşık ileti sayısını ve diğer sıra meta verilerini döndürür.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Nasıl yapılır: kuyruğu silme

Bir kuyruğu ve içerdiği tüm iletileri silmek için, `delete_queue()` kuyruk nesnesi üzerinde yöntemini çağırın.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Sonraki adımlar

Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

- [Azure depolama ekibi blogu](/archive/blogs/windowsazurestorage/) ' nu ziyaret edin
- GitHub 'da [Ruby deposu Için Azure SDK 'sını](https://github.com/WindowsAzure/azure-sdk-for-ruby) ziyaret edin

Bu makalede ele alınan Azure kuyruğu depolama ve [Service Bus kuyruklarının nasıl kullanılacağına](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)ilişkin Azure Service Bus kuyrukları arasında bir karşılaştırma için bkz. [Azure kuyruk depolama ve Service Bus kuyrukları-karşılaştırılan ve](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md) benzerlikler
