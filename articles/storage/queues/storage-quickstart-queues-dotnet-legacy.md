---
title: 'Hızlı başlangıç: bir kuyruğu yönetmek için .NET için Azure Storage v11 kullanma'
description: Bu hızlı başlangıçta, bir kuyruk oluşturmak ve bu iletiye ileti eklemek için .NET için Azure depolama istemci kitaplığı 'nı nasıl kullanacağınızı öğreneceksiniz. Ardından, sıradaki iletileri okumayı ve işlemeyi öğreneceksiniz.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: df2f3593f904c5c6c6c9af0ae635a2e152f9eb82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871084"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Hızlı başlangıç: bir kuyruğu yönetmek için .NET için Azure depolama SDK v11 kullanın

Bu hızlı başlangıçta, bir kuyruk oluşturmak ve buna ileti eklemek için .NET için Azure depolama istemci kitaplığı v11 nasıl kullanacağınızı öğrenirsiniz. Ardından, sıradaki iletileri okumayı ve işlemeyi öğreneceksiniz.

> [!NOTE]
> Bu hızlı başlangıç, Azure kuyruk depolama istemci kitaplığı 'nın eski bir sürümünü kullanır. En son sürümü kullanmaya başlamak için bkz. [hızlı başlangıç: .net Için Azure kuyruk depolama istemci kitaplığı V12](storage-quickstart-queues-dotnet.md).

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Ardından, işletim sisteminiz için .NET Core 2.0’ı indirip yükleyin. Windows kullanıyorsanız, isterseniz Visual Studio yükleyip .NET Framework kullanabilirsiniz. Ayrıca, işletim sisteminizle birlikte kullanmak için bir düzenleyici yüklemeyi seçebilirsiniz.

### <a name="windows"></a>Windows

- Windows veya [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) [Için .NET Core SDK](https://dotnet.microsoft.com/download) (Windows için Visual Studio ile birlikte) yükler
- [Windows için Visual Studio](https://www.visualstudio.com/) yükleyin. .NET Core kullanıyorsanız, Visual Studio yüklenmesi isteğe bağlıdır.

.NET Core ve .NET Framework arasında seçim yapma hakkında bilgi için bkz: [Sunucu uygulamaları için .NET Framework ve .NET Core arasında seçim yapma](/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- [Linux için .NET Core SDK](/dotnet/core/install/linux) 'i yükler
- İsteğe bağlı olarak [Visual Studio Code](https://www.visualstudio.com/) ve [C# uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) yükleyin

### <a name="macos"></a>Mac OS

- [MacOS için .NET Core SDK](https://dotnet.microsoft.com/download)'i yükler.
- İsteğe bağlı olarak [Mac için Visual Studio](https://www.visualstudio.com/vs/visual-studio-mac/) yükleyin

## <a name="download-the-sample-application"></a>Örnek uygulamayı indirin:

Bu hızlı başlangıçta kullanılan örnek uygulama, temel bir konsol uygulamasıdır. [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart) üzerindeki örnek uygulamayı inceleyebilirsiniz.

Uygulamanın bir kopyasını geliştirme ortamınıza indirmek için [Git](https://git-scm.com/) ' i kullanın.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Bu komut, depoyu yerel git klasörünüze klonlar. Visual Studio çözümünü açmak için `storage-queues-dotnet-quickstart` klasörü bulun, açın ve üzerine çift tıklayın `storage-queues-dotnet-quickstart.sln` .

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Depolama bağlantı dizelerinizi yapılandırma

Uygulamayı çalıştırmak istiyorsanız, depolama hesabınız için bağlantı dizesi sağlamanız gerekir. Örnek uygulama, bağlantı dizesini ortam değişkeninden okur ve Azure Depolama’ya yönelik istekleri yetkilendirmek için bu dizeyi kullanır.

Bağlantı dizenizi kopyaladıktan sonra uygulamayı çalıştıran yerel makine üzerindeki yeni bir ortam değişkenine yazın. Ortam değişkenini ayarlamak için bir konsol penceresi açın ve işletim sisteminizin yönergelerini izleyin. `<yourconnectionstring>` değerini gerçek bağlantı dizenizle değiştirin:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Ortam değişkenini ekledikten sonra, konsol penceresi de dahil olmak üzere ortam değişkenini okumak için gereken tüm çalışan programları yeniden başlatmanız gerekebilir. Örneğin, düzenleyici olarak Visual Studio kullanıyorsanız, örneği çalıştırmadan önce Visual Studio’yu yeniden başlatın.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source ~/.bashrc` çalıştırın.

### <a name="macos"></a>Mac OS

.bash_profile dosyanızı düzenleyin ve ortam değişkenini ekleyin:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Ortam değişkenini ekledikten sonra değişiklikleri uygulamak için konsol pencerenizden `source .bash_profile` çalıştırın.

## <a name="run-the-sample"></a>Örneği çalıştırma

Örnek uygulama bir kuyruk oluşturur ve ona bir ileti ekler. Uygulama öncelikle iletiyi kuyruktan kaldırmadan iletiye bakar, sonra iletiyi alır ve kuyruktan siler.

### <a name="windows"></a>Windows

Düzenleyiciniz olarak Visual Studio kullanıyorsanız, `F5` çalıştırmak için tuşuna basabilirsiniz.

Aksi takdirde uygulama dizininize gidip `dotnet run` komutuyla uygulamayı çalıştırın.

```
dotnet run
```

### <a name="linux"></a>Linux

Uygulama dizininize gidip `dotnet run` komutuyla uygulamayı çalıştırın.

```
dotnet run
```

### <a name="macos"></a>Mac OS

Uygulama dizininize gidip `dotnet run` komutuyla uygulamayı çalıştırın.

```
dotnet run
```

Örnek uygulama çıktısı aşağıdaki örneğe benzer:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Örnek kodu anlama

Ardından, nasıl çalıştığını anlayabilmeniz için örnek kodu inceleyin.

### <a name="try-parsing-the-connection-string"></a>Bağlantı dizesini ayrıştırmayı deneyin

Örnek öncelikle ortam değişkeninin [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) depolama hesabına işaret eden bir nesne oluşturmak için ayrıştırılabilen bir bağlantı dizesi içerdiğini denetler. Bağlantı dizesinin geçerli olup olmadığını denetlemek için, örnek [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) yöntemini kullanır. `TryParse`Başarılı olursa, `storageAccount` değişkenini başlatır ve döndürür `true` .

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Kuyruk oluşturma

Birincisi, örnek bir kuyruk oluşturur ve ona bir ileti ekler.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>İleti ekleme

Ardından örnek, sıranın arkasına bir ileti ekler.

Bir ileti UTF-8 kodlaması ile bir XML isteğine dahil edilebilir bir biçimde olmalıdır ve boyutu 64 KB 'ye kadar olabilir. Bir ileti ikili veriler içeriyorsa iletiyi Base64 kodlamanızı öneririz.

Varsayılan olarak, bir ileti için en uzun yaşam süresi 7 güne ayarlanır. İletinin yaşam süresi için herhangi bir pozitif sayı belirtebilirsiniz.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Kullanım süreleri dolan bir ileti eklemek için çağrımda ' i kullanın `Timespan.FromSeconds(-1)` [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Kuyruktaki bir iletiye göz atın

Örnek, bir kuyruktan bir ileti özeti gösterir. Bir iletiye göz aldığınızda iletinin içeriğini okuyabilirsiniz. Ancak, ileti diğer istemcilere görünür kalır, böylece daha sonra iletiyi alabilir ve işleyebilir.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Bir iletiyi kuyruktan çıkarma

Örnek ayrıca bir iletinin nasıl sıradan alınacağını gösterir. Bir iletiyi sıradan kaldırdığınızda, iletiyi kuyruğun önünden alır ve diğer istemcilere geçici olarak görünmez olarak işleyebilirsiniz. Varsayılan olarak, bir ileti 30 saniye boyunca görünmez kalır. Bu süre boyunca, kodunuz iletiyi işleyebilir. İletiyi sıradan çıkarmayı son olarak, başka bir istemcinin aynı iletiyi sıradan çıkarmaması için iletiyi işlemeden hemen sonra silersiniz.

Bir donanım veya yazılım arızası nedeniyle kodunuz bir iletiyi işleyemezse ileti, Invisibility döneminden sonra yeniden görünür hale gelir. Başka bir istemci aynı iletiyi alıp yeniden deneyebilir.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Örnek, sırayı silerek oluşturduğu kaynakları temizler. Sıra silindiğinde, içerdiği tüm iletiler de silinir.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Kuyruklarla .NET uygulamaları geliştirmeye yönelik kaynaklar

Azure kuyruk depolaması ile .NET geliştirme için şu ek kaynaklara bakın:

### <a name="binaries-and-source-code"></a>İkili dosyalar ve kaynak kodu

- [.Net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage) 'nın en son sürümüne yönelik NuGet paketlerini indirin
  - [Common](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Kuyruklar](https://www.nuget.org/packages/Azure.Storage.Queues/)
- GitHub üzerinde [.NET istemci kitaplığı kaynak kodunu](https://github.com/Azure/azure-storage-net) görüntüleyin.

### <a name="azure-storage-client-library-reference-and-samples"></a>Azure depolama istemci kitaplığı başvurusu ve örnekleri

- .NET istemci kitaplıkları hakkında daha fazla bilgi için bkz. [.net Için Azure Storage istemci kitaplıkları başvurusu](/dotnet/api/overview/azure/storage) .
- .NET istemci kitaplığı kullanılarak yazılmış [kuyruk depolama örneklerini](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) keşfet.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir kuyruğa ileti eklemeyi, sıradaki iletilere göz atmayı ve .NET kullanarak iletileri sıradan çıkarmayı ve işlemeyi öğrendiniz.

> [!div class="nextstepaction"]
> [Azure kuyruk depolama ile uygulamalar arasında iletişim kurma](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- .NET Core hakkında daha fazla bilgi için bkz. [10 dakika içinde .NET kullanmaya başlama](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro).
