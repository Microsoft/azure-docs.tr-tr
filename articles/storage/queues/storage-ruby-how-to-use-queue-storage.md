---
title: Ruby-Azure depolama 'dan kuyruk depolama kullanma
description: Kuyrukları oluşturmak ve silmek için Azure Kuyruk hizmeti kullanmayı ve iletileri ekleme, alma ve silme hakkında bilgi edinin. Ruby dilinde yazılan örnekler.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721274"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Ruby’den Kuyruk depolama kullanma
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış
Bu kılavuzda, Microsoft Azure kuyruğu depolama hizmetini kullanarak yaygın senaryoların nasıl gerçekleştirileceği gösterilmektedir. Örnekler, Ruby Azure API 'SI kullanılarak yazılır.
Kapsanan senaryolar sıra iletilerini **ekleme**, göz **atma**, **alma**ve **silme** , Ayrıca kuyruk **oluşturma ve silme**içerir.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Ruby uygulaması oluşturma
Ruby uygulaması oluşturun. Yönergeler için bkz. [Linux üzerinde App Service Ruby uygulaması oluşturma](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Uygulamanızı depolamaya erişecek şekilde yapılandırma
Azure Storage 'ı kullanmak için, depolama REST hizmetleriyle iletişim kuran bir dizi kullanışlı kitaplık içeren Ruby Azure paketini indirmeniz ve kullanmanız gerekir.

### <a name="use-rubygems-to-obtain-the-package"></a>Paketi edinmek için RubyGems kullanma
1. **PowerShell** (Windows), **Terminal** (Mac) veya **Bash** (Unix) gibi bir komut satırı arabirimi kullanın.
2. GED ve bağımlılıklarını yüklemek için komut penceresine "GEA Install Azure" yazın.

### <a name="import-the-package"></a>Paketi içeri aktarma
En sevdiğiniz metin düzenleyiciyi kullanarak, depolamayı kullanmayı düşündüğünüz Ruby dosyasının en üstüne aşağıdakileri ekleyin:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Azure depolama bağlantısı kurma
Azure modülü, Azure depolama hesabına bağlanmak için gereken bilgiler için Azure **\_depolama\_hesabı** ve **\_Azure\_Storage ACCESS_KEY** ortam değişkenlerini okur. Bu ortam değişkenleri ayarlanmamışsa, aşağıdaki kodla **Azure:: QueueService** ' i kullanmadan önce hesap bilgilerini belirtmeniz gerekir:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Bu değerleri Azure portalında bir klasik veya Kaynak Yöneticisi depolama hesabından edinmek için:

1. [Azure Portal](https://portal.azure.com)’da oturum açın.
2. Kullanmak istediğiniz depolama hesabına gidin.
3. Sağdaki Ayarlar dikey penceresinde **Erişim Anahtarları**'na tıklayın.
4. Açılan Erişim anahtarları dikey penceresinde, 1. ve 2. erişim anahtarını göreceksiniz. Bunlardan birini kullanabilirsiniz. 
5. Anahtarı panoya kopyalamak için Kopyala simgesine tıklayın. 

## <a name="how-to-create-a-queue"></a>Nasıl Yapılır: Sıra oluşturma
Aşağıdaki kod, kuyruklarla çalışmanıza olanak sağlayan bir **Azure:: QueueService** nesnesi oluşturur.

```ruby
azure_queue_service = Azure::QueueService.new
```

Belirtilen ada sahip bir sıra oluşturmak için **create_queue ()** metodunu kullanın.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Nasıl Yapılır: Bir kuyruğa Ileti ekleme
Bir kuyruğa ileti eklemek için, **CREATE_MESSAGE ()** yöntemini kullanarak yeni bir ileti oluşturun ve kuyruğa ekleyin.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Nasıl Yapılır: Sonraki Iletiye göz atın
**İletileri göz at\_()** yöntemini çağırarak sıradan kaldırmadan bir kuyruğun önündeki iletiye göz atmayı sağlayabilirsiniz. Varsayılan olarak, **iletilere\_göz atın ()** tek bir iletiye göz atar. Ayrıca, kaç tane ileti göz atmayı istediğinizi de belirtebilirsiniz.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Nasıl Yapılır: Sonraki Iletiyi sıradan çıkarma
İki adımda kuyruktan bir ileti kaldırabilirsiniz.

1. **Liste\_iletilerini ()** çağırdığınızda, bir sıradaki bir sonraki iletiyi varsayılan olarak alırsınız. Ayrıca, kaç tane ileti almak istediğinizi de belirtebilirsiniz. **Liste\_iletilerinden ()** döndürülen iletiler, bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Görünürlük zaman aşımını bir parametre olarak Saniyeler içinde geçitirsiniz.
2. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, **delete_message ()** öğesini de çağırmanız gerekir.

Bir iletiyi kaldırmanın bu iki adımlı işlemi, kodunuz, donanım veya yazılım arızasından kaynaklanan bir iletiyi işleyemediğinde, kodunuzun başka bir örneğinin aynı mesajı almasını ve yeniden denemesini sağlar. Kodunuz, ileti işlendikten hemen sonra **silme\_iletisini ()** çağırır.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Nasıl Yapılır: Sıraya alınan Iletinin Içeriğini değiştirme
Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Aşağıdaki kod, bir iletiyi güncelleştirmek için **update_message ()** yöntemini kullanır. Yöntemi, kuyruk iletisinin pop alındığını ve iletinin kuyrukta görünür olacağını temsil eden UTC Tarih saat değerini içeren bir tanımlama grubu döndürür.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Nasıl Yapılır: Dequeuing Iletileri için ek seçenekler
İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz.

1. Toplu bir ileti alabilirsiniz.
2. Daha uzun veya daha kısa görünürlük zaman aşımı ayarlayabilir, böylece her iletiyi tamamen işlemek için kodunuzun daha fazla veya daha az zaman aşımına uğramamasını sağlayabilirsiniz.

Aşağıdaki kod örneği, bir çağrıda 15 ileti almak için **List\_Messages ()** yöntemini kullanır. Ardından her iletiyi yazdırır ve siler. Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Nasıl Yapılır: Sıra uzunluğunu al
Kuyruktaki ileti sayısını tahmin edebilirsiniz. **Alma\_kuyruğu\_meta verileri ()** yöntemi, kuyruk hizmetinden sıraya ilişkin yaklaşık ileti sayısını ve meta verileri döndürmesini ister.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Nasıl Yapılır: Kuyruğu silme
Bir kuyruğu ve içerdiği tüm iletileri silmek için, kuyruk nesnesi üzerindeki **Delete\_Queue ()** yöntemini çağırın.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Sonraki Adımlar
Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

* [Azure depolama ekibi blogu](https://blogs.msdn.com/b/windowsazurestorage/) ' nu ziyaret edin
* GitHub 'da [Ruby deposu Için Azure SDK 'sını](https://github.com/WindowsAzure/azure-sdk-for-ruby) ziyaret edin

Bu makalede ele alınan Azure kuyruğu hizmeti ve [Service Bus kuyrukları kullanma](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) makalesinde açıklanan kuyruklar Azure Service Bus bir karşılaştırma için bkz. [azure kuyrukları ve Service Bus kuyrukları-karşılaştırılan ve](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md) benzerlikler
