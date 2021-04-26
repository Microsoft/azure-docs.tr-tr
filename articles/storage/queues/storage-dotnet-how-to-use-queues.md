---
title: .NET-Azure Storage kullanarak Azure kuyruk depolama ile çalışmaya başlama
description: Azure kuyruk depolama, uygulama bileşenleri arasında güvenilir ve zaman uyumsuz mesajlaşma sağlar. Bulut mesajlaşma özelliği uygulama bileşenlerinizin bağımsız olarak ölçeklendirilmesini sağlar.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e32779e75480d365ec10e8c7f849fddd27c891c
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276000"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>.NET kullanarak Azure Kuyruk Depolamaya başlayın

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Genel Bakış

Azure kuyruk depolama, uygulama bileşenleri arasında bulut mesajlaşmasını sağlar. Uygulamalar ölçeklendirme için tasarlanırken uygulama bileşenleri genellikle birbirinden bağımsız olarak ölçeklendirilebilecek şekilde ayrılır. Kuyruk depolama, bulutta, masaüstünde, şirket içi sunucuda veya mobil cihazlarda çalışan uygulama bileşenleri arasında zaman uyumsuz mesajlaşma sağlar. Kuyruk depolama Ayrıca zaman uyumsuz görevlerin yönetilmesini ve işlem iş akışlarının oluşturulmasını destekler.

### <a name="about-this-tutorial"></a>Bu öğretici hakkında

Bu öğreticide, Azure kuyruk depolama kullanarak bazı yaygın senaryolar için .NET kodunun nasıl yazılacağı gösterilmektedir. Kapsanan senaryolara kuyruk oluşturma ve silme ile kuyruk iletileri ekleme, okuma ve silme dahildir.

**Tahmini tamamlanma süresi:** 45 dakika

### <a name="prerequisites"></a>Önkoşullar

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- Bir [Azure depolama hesabı](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Geliştirme ortamınızı kurma

Ardından, geliştirme ortamınızı Visual Studio’da ayarlayın; böylece bu kılavuzdaki kod örneklerini denemeye hazır olursunuz.

### <a name="create-a-windows-console-application-project"></a>Windows konsol uygulaması projesi oluşturma

Visual Studio'da yeni bir Windows konsol uygulaması oluşturun. Aşağıdaki adımlarda, Visual Studio 2019 ' de bir konsol uygulamasının nasıl oluşturulacağı gösterilmektedir. Adımlar Visual Studio’nun diğer sürümlerinde de benzerdir.

1. **Dosya**  >  **Yeni**  >  **Proje** ' yi seçin
2. **Platform**  >  **pencerelerini** seçin
3. **Konsol uygulaması Seç (.NET Framework)**
4. **İleri**’yi seçin
5. **Proje adı** alanına uygulamanız için bir ad girin
6. **Oluştur**’u seçin

Bu öğreticideki tüm kod örnekleri konsol uygulamanızın `Program.cs` dosyasındaki `Main()` yöntemine eklenebilir.

Azure depolama istemci kitaplıklarını, Azure bulut hizmeti veya Web uygulaması, masaüstü ve mobil uygulamalar dahil olmak üzere herhangi bir türde .NET uygulamasında kullanabilirsiniz. Bu kılavuzda, sadeleştirmek için konsol uygulaması kullanmaktayız.

### <a name="use-nuget-to-install-the-required-packages"></a>Gereken paketleri yüklemek için NuGet kullanma

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Bu öğreticiyi tamamlayabilmeniz için projenizde aşağıdaki dört pakete başvurmanız gerekir:

- [.Net Için Azure. Core kitaplığı](https://www.nuget.org/packages/azure.core/): Bu paket, modern .net Azure SDK 'sı istemci kitaplıkları için paylaşılan temel elemanlar, soyutlamalar ve yardımcıları sağlar.
- [Azure. Storage. .net Için ortak istemci kitaplığı](https://www.nuget.org/packages/azure.storage.common/): Bu paket, diğer Azure depolama istemci kitaplıkları tarafından paylaşılan altyapıyı sağlar.
- [.Net Için Azure. Storage. Queues istemci kitaplığı](https://www.nuget.org/packages/azure.storage.queues/): Bu paket, bir istemci tarafından erişilebilecek iletileri depolamak Için Azure kuyruk depolama ile çalışmaya izin vermez.
- [ .NET içinSystem.Configuration.ConfigurationManager kitaplığı](https://www.nuget.org/packages/system.configuration.configurationmanager/): Bu paket, istemci uygulamaları için yapılandırma dosyalarına erişim sağlar.

Bu paketleri edinmek için NuGet kullanabilirsiniz. Şu adımları izleyin:

1. **Çözüm Gezgini**' de projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.
1. **Gözatmayı** Seç
1. Çevrimiçi olarak arayın `Azure.Storage.Queues` ve Azure Storage istemci kitaplığı 'nı ve bağımlılıklarını yüklemek Için **yüklemeyi** seçin. Bu işlem, kuyruk kitaplığının bağımlılıkları olan Azure. Storage. Common ve Azure. Core kitaplıklarını da yükler.
1. Çevrimiçi olarak arayın `System.Configuration.ConfigurationManager` ve Configuration Manager yüklemek Için **yüklemeyi** seçin.

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Bu öğreticiyi tamamlayabilmeniz için projenizde aşağıdaki üç pakete başvurmanız gerekir:

- [.Net Için Microsoft. Azure. Storage. Common istemci kitaplığı](https://www.nuget.org/packages/microsoft.azure.storage.common/): Bu paket, Depolama hesabınızdaki veri kaynaklarına programlı erişim sağlar.
- [.NET için Microsoft Azure kuyruk depolama istemci kitaplığı](https://www.nuget.org/packages/microsoft.azure.storage.queue/): Bu istemci kitaplığı, bir istemci tarafından erişilebilecek iletileri depolamak Için Azure kuyruk depolama ile çalışmaya izin verebilir.
- [.NET için Microsoft Azure Configuration Manager Kitaplığı](https://www.nuget.org/packages/microsoft.azure.configurationmanager/): Bu paket, uygulamanızın nerede çalıştığına bakmaksızın yapılandırma dosyasından bağlantı dizesini ayrıştırmak için bir sınıf sağlar.

Bu paketleri edinmek için NuGet kullanabilirsiniz. Şu adımları izleyin:

1. **Çözüm Gezgini**' de projenize sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.
1. **Gözatmayı** Seç
1. Çevrimiçi olarak arayın `Microsoft.Azure.Storage.Queue` ve Azure Storage istemci kitaplığı 'nı ve bağımlılıklarını yüklemek Için **yüklemeyi** seçin. Bu, kuyruk kitaplığının bağımlılığı olan Microsoft. Azure. Storage. Common kitaplığını da yükler.
1. Çevrimiçi olarak arayın `Microsoft.Azure.ConfigurationManager` ve Azure Configuration Manager yüklemek Için **yüklemeyi** seçin.

---

### <a name="determine-your-target-environment"></a>Hedef ortamınızı saptama

Bu kılavuzdaki örnekleri çalıştırmak için iki ortam seçeneğiniz vardır:

- Kodunuzu buluttaki bir Azure Storage hesabına karşı çalıştırabilirsiniz.
- Kodunuzun Azurite depolama öykünücüsünde karşı çalışmasını sağlayabilirsiniz. Azurite, buluttaki bir Azure depolama hesabına öykünen yerel bir ortamdır. Azurite, uygulamanız geliştirme aşamasında olduğunda kodunuzda test ve hata ayıklama için ücretsiz bir seçenektir. Öykünücü iyi bilinen hesabı ve anahtarı kullanır. Daha fazla bilgi için bkz. [Yerel Azure depolama geliştirme ve test Için Azurite öykünücüsünü kullanma](../common/storage-use-azurite.md).

> [!NOTE]
> Azure Storage ile ilişkili maliyetlerin oluşmasını önlemek için depolama öykünücüsünü hedefleyebilirsiniz. Ancak, buluttaki bir Azure Depolama hesabını hedeflemesini seçerseniz, bu öğreticiyi gerçekleştirmeye yönelik maliyetler göz ardı edilir.

## <a name="get-your-storage-connection-string"></a>Depolama Bağlantı dizenizi alın

Depolama hizmetlerine erişim için uç noktaları ve kimlik bilgilerini yapılandırmak üzere bir depolama bağlantı dizesi kullanarak .NET için Azure depolama istemci kitaplıkları desteği. Daha fazla bilgi için bkz. [depolama hesabı erişim anahtarlarını yönetme](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure portalından kimlik bilgilerinizi kopyalama

Örnek kodun, depolama hesabınıza erişim için yetki vermesi gerekir. Yetki vermek için, depolama hesabınızın kimlik bilgilerini bir bağlantı dizesi şeklinde uygulamaya sağlarsınız. Depolama hesabınızın kimlik bilgilerini görüntülemek için:

1. [Azure Portal](https://portal.azure.com)gidin.
2. Depolama hesabınızı bulun.
3. Depolama hesabına genel bakışın **Ayarlar** bölümünde **Erişim anahtarları**’nı seçin. Hesap erişim anahtarlarınız ve her bir anahtar için tam bağlantı dizesi görüntülenir.
4. **key1** bölümünde **Bağlantı dizesi** değerini bulun ve **Kopyala** düğmesine tıklayarak bağlantı dizesini kopyalayın. Sonraki adımda bir ortam değişkenine bağlantı dizesini ekleyeceksiniz.

    ![Azure portalından bağlantı dizesinin kopyalanmasını gösteren ekran görüntüsü](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Bağlantı dizeleri hakkında daha fazla bilgi için bkz. [Azure depolama için bağlantı dizesi yapılandırma](../common/storage-configure-connection-string.md).

> [!NOTE]
> Depolama hesabı anahtarınız depolama hesabınızın kök parolasına benzer. Depolama hesabı anahtarınızı korumak için her zaman özen gösterin. Diğer kullanıcılara dağıtmaktan, sabit kodlamaktan ve başkalarının erişebileceği düz metin dosyasına kaydetmekten kaçının. Anahtarınızın tehlikede olduğunu düşünüyorsanız, Azure portalını kullanarak hesap anahtarınızı yeniden oluşturun.

Depolama bağlantı dizenizi korumanın en iyi yolu bir yapılandırma dosyasında tutmaktır. Bağlantı dizenizi yapılandırmak için, `app.config` dosyasını Visual Studio’daki Çözüm Gezgini'nden açın. `<appSettings>`Burada gösterilen öğenin içeriğini ekleyin. `connection-string`Portaldaki depolama hesabınızdan kopyaladığınız değerle değiştirin:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Örneğin, yapılandırma ayarınız şunun gibi görünür:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Azurite depolama öykünücüsünü hedeflemek için, iyi bilinen hesap adı ve anahtarıyla eşleşen bir kısayolu kullanabilirsiniz. Bu durumda, bağlantı dizesi ayarı şöyle olur:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Using yönergeleri ekleme

Aşağıdaki `using` yönergelerini `Program.cs` dosyasının üst tarafına ekleyin:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Kuyruk depolama istemcisini oluşturma

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

[`QueueClient`](/dotnet/api/azure.storage.queues.queueclient)Sınıfı, kuyruk depolamada depolanan kuyrukları almanızı sağlar. Hizmet istemcisini oluşturma yöntemlerinden biri aşağıda verilmiştir:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

[`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true)Sınıfı, kuyruk depolamada depolanan kuyrukları almanızı sağlar. Hizmet istemcisini oluşturma yöntemlerinden biri aşağıda verilmiştir:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Artık veri okuyan ve kuyruk depolamaya veri yazan kodu yazmaya hazırsınız.

## <a name="create-a-queue"></a>Bir kuyruk oluşturma

Bu örnek, bir sıranın nasıl oluşturulacağını gösterir:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Kuyruğa bir ileti yerleştirme

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Mevcut bir sıraya bir ileti eklemek için [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) yöntemini çağırın. İleti bir dize (UTF-8 biçiminde) ya da bir bayt dizisi olabilir. Aşağıdaki kod bir sıra (yoksa) oluşturur ve bir ileti ekler:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Mevcut bir kuyruğa ileti eklemek için ilk olarak yeni bir oluştur [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Sonra, yöntemini çağırın [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) . Bir `CloudQueueMessage` dizeden (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir. Burada bir sıra (yoksa) oluşturan ve iletiyi ekleyen bir kod verilmiştir `Hello, World` : var olan bir kuyruğa ileti eklemek için, önce yeni bir oluştur [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Sonra, yöntemini çağırın [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) . Bir `CloudQueueMessage` dizeden (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir. Bir sıra (yoksa) oluşturan ve iletiyi ekleyen kod aşağıda verilmiştir `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Sonraki iletiye gözatın

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Yöntemi çağırarak kuyruktaki iletilere, kuyruktan kaldırmadan göz atmayı sağlayabilirsiniz [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) . Parametresi için bir değer geçirmezseniz `maxMessages` , varsayılan olarak bir ileti göz atın.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Yöntemini çağırarak bir kuyruğun ön tarafındaki iletiye göz atmayı sağlayabilirsiniz [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Kuyruğa alınan iletinin içeriğini değiştirme

Kuyrukta yer alan bir iletinin içeriğini değiştirebilirsiniz. Eğer ileti bir iş görevini temsil ediyorsa, bu özelliği kullanarak iş görevinin durumunu güncelleştirebilirsiniz. Aşağıdaki kod kuyruk iletisini yeni içeriklerle güncelleştirir ve görünürlük zaman aşımını 60 saniye daha uzatır. Bu, ileti ile ilişkili işin durumunu kaydeder ve istemciye ileti üzerinde çalışmaya devam etmesi için bir dakika daha zaman verir. Bu tekniği, işlem adımı donanım veya yazılım arızası nedeniyle başarısız olursa baştan başlamak zorunda kalmadan, kuyruk iletilerinde çoklu adım iş akışlarını izlemek için kullanabilirsiniz. Genellikle bir yeniden deneme sayacı tutmanı gerekir ve bir ileti *n* seferden daha fazla yeniden denenirse, silebilirsiniz. Bu, her işlendiğinde bir uygulama hatası tetikleyen bir iletiye karşı koruma sağlar.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Sonraki iletiyi sıradan çıkarma

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

İki adımda kuyruktan bir iletiyi sıradan çıkarma. [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages)' İ çağırdığınızda bir kuyruktaki sonraki iletiyi alırsınız. Öğesinden döndürülen bir ileti, `ReceiveMessages` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini de çağırmanız gerekir [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. `DeleteMessage`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Kodunuz, iki adımda bir kuyruktan bir ileti sırasını kaldırır. [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true)' İ çağırdığınızda bir kuyruktaki sonraki iletiyi alırsınız. Öğesinden döndürülen bir ileti, `GetMessage` Bu kuyruktan gelen diğer kod okuma iletileri için görünmez hale gelir. Varsayılan olarak bu ileti 30 saniye görünmez kalır. İletiyi kuyruktan kaldırma işleminin tamamlanabilmesi için, öğesini de çağırmanız gerekir [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Bir iletinin iki adımlı kaldırılma süreci, donanım veya yazılım arızasından dolayı kodunuzun bir iletiyi işleyememesi durumunda kodunuzun başka bir örneğinin aynı iletiyi alıp yeniden denemesini sağlar. `DeleteMessage`İleti işlendikten sonra kodunuz doğru şekilde çağırır.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Ortak kuyruk depolama API 'Leri ile Async-Await modelini kullanın

Bu örnek, Async-Await deseninin ortak kuyruk depolama API 'Leri ile nasıl kullanılacağını gösterir. Örnek, her yöntemin sonekine göre gösterildiği gibi, verilen yöntemlerin her birinin zaman uyumsuz sürümünü çağırır `Async` . Zaman uyumsuz bir yöntem kullanıldığında, Async-Await model, çağrı tamamlanana kadar yerel yürütmeyi askıya alır. Bu davranış geçerli iş parçacığının başka işler yapmasını sağlar ve böylece performans sorunlarını engellemeye yardımcı olur, uygulamanızın genel yanıt hızını iyileştirir. .NET ' te Async-Await deseninin kullanımı hakkında daha fazla bilgi için bkz. [Async and await (C# and Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Dequeuing iletileri için ek seçenekler kullanma

İletilerin bir kuyruktan alınma şeklini iki yöntemle özelleştirebilirsiniz. İlk olarak toplu iletiler alabilirsiniz (en fazla 32). İkinci olarak daha uzun veya daha kısa bir görünmezlik süresi ayarlayarak kodunuzun her iletiyi tamamen işlemesi için daha az veya daha fazla zaman tanıyabilirsiniz.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Aşağıdaki kod örneği, [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) tek bir çağrıda 20 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `foreach` . Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır. Beş dakikalık tüm iletiler için aynı anda başlayacağını unutmayın. bu nedenle, çağrısından bu yana beş dakika geçtikten sonra `ReceiveMessages` , silinmemiş olan tüm iletiler yeniden görünür hale gelir.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Aşağıdaki kod örneği, [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) tek bir çağrıda 20 ileti almak için yöntemini kullanır. Ardından, her iletiyi bir döngü kullanarak işler `foreach` . Ayrıca her ileti için görünmezlik zaman aşımı beş dakika olarak ayarlanır. Beş dakikalık tüm iletiler için aynı anda başlayacağını unutmayın. bu nedenle, çağrısından bu yana beş dakika geçtikten sonra `GetMessages` , silinmemiş olan tüm iletiler yeniden görünür hale gelir.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Kuyruk uzunluğu alma

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties)Yöntemi, ileti sayısı dahil olmak üzere sıra özelliklerini döndürür. [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount)Özelliği kuyruktaki yaklaşık ileti sayısını içerir. Bu sayı kuyruktaki gerçek ileti sayısından daha düşük değil, ancak daha yüksek olabilir.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Bir kuyruktaki ileti sayısı ile ilgili bir tahmin alabilirsiniz. [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true)Yöntemi, ileti sayısı dahil olmak üzere sıra özniteliklerini döndürür. [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true)Özelliği, `FetchAttributes` kuyruk depolaması çağrılmadan yöntemi tarafından alınan son değeri döndürür.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Bir kuyruk silme

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Bir kuyruğu ve içerdiği tüm iletileri silmek için, [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) kuyruk nesnesi üzerinde yöntemini çağırın.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Bir kuyruğu ve içerdiği tüm iletileri silmek için, [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) kuyruk nesnesi üzerinde yöntemini çağırın.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Sonraki adımlar

Sıra depolamanın temellerini öğrendiğinize göre, daha karmaşık depolama görevleri hakkında bilgi edinmek için bu bağlantıları izleyin.

- Kullanılabilir API 'Lerle ilgili tüm ayrıntılar için kuyruk depolama başvurusu belgelerini görüntüleyin:
  - [.NET için Azure depolama istemci kitaplığı başvurusu](/dotnet/api/overview/azure/storage)
  - [Azure depolama REST API başvurusu](/rest/api/storageservices/)
- Azure’da veri depolama ile ilgili ek seçenekler hakkında daha fazla bilgi edinmek için daha fazla özellik kılavuzu görüntüleyin.
  - Yapılandırılmış verileri depolamak için [.NET kullanarak Azure Tablo depolama ile çalışmaya](../../cosmos-db/tutorial-develop-table-dotnet.md) başlayın.
  - Yapılandırılmamış verileri depolamak için [.NET kullanarak Azure Blob Storage](../blobs/storage-quickstart-blobs-dotnet.md) 'ı kullanmaya başlayın.
  - İlişkisel verileri depolamak için [.NET (C#) kullanarak SQL Veritabanı'na bağlanın](../../azure-sql/database/connect-query-dotnet-core.md).
- [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) kullanarak Azure Storage ile birlikte çalışmak üzere yazdığınız kodları nasıl sadeleştireceğinizi öğrenin.
