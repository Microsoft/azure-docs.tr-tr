---
title: Öğretici-Azure depolama kuyrukları ile çalışma-Azure depolama
description: Azure Kuyruk hizmeti kullanarak kuyruklar oluşturma ve ileti ekleme, alma ve silme hakkında bir öğretici.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: c8e1d5c1c11c4fdf902c7be7bc03be298e93a8b9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721137"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Öğretici: Azure depolama kuyrukları ile çalışma

Azure kuyruk depolama, dağıtılmış bir uygulamanın bileşenleri arasında iletişimi etkinleştirmek için bulut tabanlı kuyruklar uygular. Her kuyruk, bir gönderen bileşeni tarafından eklenebilen ve bir alıcı bileşeni tarafından işlenen iletilerin bir listesini tutar. Bir kuyruk ile uygulamanız, talebi karşılamak için hemen ölçeklendirebilir. Bu makalede, Azure depolama kuyruğu ile çalışmaya yönelik temel adımlar gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> - Azure Storage hesabı oluşturma
> - Uygulama oluşturma
> - Zaman uyumsuz kod desteği ekle
> - Kuyruk oluştur
> - Bir kuyruğa ileti ekleme
> - İletileri sıradan çıkarma
> - Boş bir kuyruğu silme
> - Komut satırı bağımsız değişkenlerini denetle
> - Uygulamayı derleme ve çalıştırma

## <a name="prerequisites"></a>Önkoşullar

- Platformlar arası [Visual Studio Code](https://code.visualstudio.com/download) düzenleyicisinin ücretsiz kopyasını alın.
- [.NET Core SDK](https://dotnet.microsoft.com/download)indirin ve yükleyin.
- Geçerli bir Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="create-an-azure-storage-account"></a>Azure Storage hesabı oluşturma

İlk olarak, bir Azure depolama hesabı oluşturun. Depolama hesabı oluşturmaya yönelik adım adım kılavuz için bkz. [depolama hesabı oluşturma](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) hızlı başlangıcı.

## <a name="create-the-app"></a>Uygulama oluşturma

**Queueapp**adlı bir .NET Core uygulaması oluşturun. Kolaylık olması için, bu uygulama sıra aracılığıyla ileti gönderir ve alır.

1. Konsol penceresinde (cmd, PowerShell veya Azure CLI gibi), **queueapp**adlı yeni bir konsol `dotnet new` uygulaması oluşturmak için komutunu kullanın. Bu komut, tek bir kaynak dosyası olan C# basit bir "Merhaba Dünya" projesi oluşturur: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Yeni oluşturulan **Queueapp** klasörüne geçin ve bunların tümünün iyi olduğunu doğrulamak için uygulamayı derleyin.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Aşağıdakilere benzer sonuçlar görmeniz gerekir:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Zaman uyumsuz kod desteği ekle

Uygulama bulut kaynaklarını kullandığından, kod zaman uyumsuz olarak çalışır. Ancak, C# **zaman uyumsuz** ve **await** , **ana** yöntemlerde 7,1 tarihine kadar C# geçerli anahtar sözcükler. **Csproj** dosyasındaki bir bayrak aracılığıyla kolayca bu derleyiciye geçiş yapabilirsiniz.

1. Proje dizinindeki komut satırından, geçerli dizinde Visual Studio Code açmak için `code .` yazın. Komut satırı penceresini açık tutun. Daha sonra yürütülecek daha fazla komut olacaktır. Derleme ve hata ayıklama için gereken C# varlıkları eklemeniz Istenirse, **Evet** düğmesine tıklayın.

2. Düzenleyicide **Queueapp. csproj** dosyasını açın.

3. Derleme `<LangVersion>7.1</LangVersion>` dosyasındaki ilk **PropertyGroup** 'a ekleyin. **TargetFramework** sizin yüklediğiniz .NET sürümüne bağlı olarak farklı olabileceğinden, yalnızca **langversion** etiketini eklediğinizden emin olun.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. **Queueapp. csproj** dosyasını kaydedin.

5. **Program.cs** kaynak dosyasını açın ve **ana** yöntemi zaman uyumsuz olarak çalışacak şekilde güncelleştirin. **Void** değerini **zaman uyumsuz bir görev** dönüş değeri ile değiştirin.

   ```csharp
   static async Task Main(string[] args)
   ```

6. **Program.cs** dosyasını kaydedin.

## <a name="create-a-queue"></a>Kuyruk oluştur

1. Komutuyla Microsoft **. Azure. Storage. Common** ve **Microsoft. Azure. Storage. Queue** paketlerini projeye yükler. `dotnet add package` Konsol penceresindeki proje klasöründen aşağıdaki DotNet komutlarını yürütün.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. **Program.cs** dosyasının en üstünde, `using System;` deyimden hemen sonra aşağıdaki ad alanlarını ekleyin. Bu uygulama, Azure depolama 'ya bağlanmak ve kuyruklarla çalışmak için bu ad alanlarındaki türleri kullanır.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. **Program.cs** dosyasını kaydedin.

### <a name="get-your-connection-string"></a>Bağlantı dizenizi alma

İstemci kitaplığı, bağlantınızı kurmak için bir bağlantı dizesi kullanır. Bağlantı dizeniz, Azure portal depolama hesabınızın **Ayarlar** bölümünde bulunur.

1. Web tarayıcınızda [Azure Portal](https://portal.azure.com/)oturum açın.

2. Azure portalda depolama hesabınıza gidin.

3. **Erişim tuşları**' nı seçin.

4. **Bağlantı dizesi** alanının sağ tarafındaki **Kopyala** düğmesine tıklayın.

![Bağlantı dizesi](media/storage-tutorial-queues/get-connection-string.png)

Bağlantı dizesi bu biçimdedir:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Bağlantı dizesini uygulamaya ekleme

Depolama hesabına erişebilmeleri için bağlantı dizesini uygulamaya ekleyin.

1. Visual Studio Code 'e geri dönün.

2. **Program** sınıfında, bağlantı dizesini tutacak bir `private const string connectionString =` üye ekleyin.

3. Eşittir işaretinden sonra, Azure portal daha önce kopyaladığınız dize değerini yapıştırın. **ConnectionString** değeri hesabınıza benzersiz olacaktır.

4. **Ana**bilgisayardan "Merhaba Dünya" kodunu kaldırın. Kodunuzun aşağıdakine benzer, ancak benzersiz bağlantı dizesi değeri ile aynı olması gerekir.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Daha sonra gönderme ve alma yöntemlerine geçirilen bir **Cloudqueue** nesnesi oluşturmak için **Main** 'i güncelleştirin.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Dosyayı kaydedin.

## <a name="insert-messages-into-the-queue"></a>Kuyruğa ileti ekleme

Kuyruğa ileti göndermek için yeni bir yöntem oluşturun. **Program** Sınıfınıza aşağıdaki yöntemi ekleyin. Bu yöntem bir kuyruk başvurusu alır, sonra [Createifnotexistsasync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync)çağırarak yeni bir kuyruk oluşturur. Ardından, [Addmessageasync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync)çağırarak iletiyi kuyruğa ekler.

1. Aşağıdaki **SendMessageAsync** yöntemini **Program** sınıfınıza ekleyin.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Dosyayı kaydedin.

Bir ileti UTF-8 kodlaması ile bir XML isteğine dahil edilebilir bir biçimde olmalıdır ve boyutu 64 KB 'ye kadar olabilir. Bir ileti ikili veriler içeriyorsa iletiyi Base64 kodlamanızı öneririz.

Varsayılan olarak, bir ileti için en uzun yaşam süresi 7 güne ayarlanır. İletinin yaşam süresi için herhangi bir pozitif sayı belirtebilirsiniz. Kullanım süreleri dolan bir ileti eklemek için `Timespan.FromSeconds(-1)` **addmessageasync**çağrısında kullanın.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>İletileri sıradan çıkarma

**Receivemessageasync**adlı yeni bir yöntem oluşturun. Bu yöntem, [Getmessageasync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync)çağırarak kuyruktan bir ileti alır. İleti başarıyla alındıktan sonra, bir kereden fazla işlenmemesi için kuyruktan silinmesi önemlidir. İleti alındıktan sonra [Deletemessageasync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync)çağırarak kuyruktan silin.

1. Aşağıdaki **Receivemessageasync** yöntemini **Program** sınıfınıza ekleyin.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Dosyayı kaydedin.

## <a name="delete-an-empty-queue"></a>Boş bir kuyruğu silme

Projenin sonunda oluşturduğunuz kaynaklara ihtiyacınız olup olmadığını belirlemek için en iyi uygulamadır. Çalışan kaynaklar sizin için ücret verebilir. Sıra varsa ancak boşsa, kullanıcıyı silmek isteyip istemediğini sorar.

1. Boş kuyruğu silmek için bir istem eklemek üzere **Receivemessageasync** yöntemini genişletin.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Dosyayı kaydedin.

## <a name="check-for-command-line-arguments"></a>Komut satırı bağımsız değişkenlerini denetle

Uygulamaya geçirilen herhangi bir komut satırı bağımsız değişkeni varsa, bu, kuyruğa eklenecek bir ileti olduğunu varsayın. Bir dize oluşturmak için bağımsız değişkenleri birleştirin. Daha önce eklediğimiz **SendMessageAsync** metodunu çağırarak bu dizeyi ileti kuyruğuna ekleyin.

Komut satırı bağımsız değişkeni yoksa, bir alma işlemi yürütün. Sıradaki ilk iletiyi almak için **Receivemessageasync** yöntemini çağırın.

Son olarak, **Console. ReadLine**'u çağırarak çıkmadan önce Kullanıcı girişini bekleyin.

1. Komut satırı bağımsız değişkenlerini denetlemek ve Kullanıcı girişini beklemek için **Main** yöntemini genişletin.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Dosyayı kaydedin.

## <a name="complete-code"></a>Tam kod

Bu proje için kod listesinin tamamı aşağıda verilmiştir.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Uygulamayı derleme ve çalıştırma

1. Proje dizinindeki komut satırından, projeyi derlemek için aşağıdaki DotNet komutunu çalıştırın.

   ```console
   dotnet build
   ```

2. Proje başarıyla yapılandırıldıktan sonra, sıraya ilk iletiyi eklemek için aşağıdaki komutu çalıştırın.

   ```console
   dotnet run First queue message
   ```

Şu çıktıyı görmeniz gerekir:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Kuyruktaki ilk iletiyi almak ve kaldırmak için komut satırı bağımsız değişkeni olmadan uygulamayı çalıştırın.

   ```console
   dotnet run
   ```

4. Tüm iletiler kaldırılana kadar uygulamayı çalıştırmaya devam edin. Daha sonra bir kez çalıştırırsanız, sıranın boş olduğunu belirten bir ileti ve kuyruğu silmek için bir istem alırsınız.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

1. Kuyruk oluştur
2. Kuyruktaki iletileri ekleme ve kaldırma
3. Azure depolama kuyruğunu silme

Daha fazla bilgi için Azure Kuyrukları hızlı başlangıç 'a göz atın.

> [!div class="nextstepaction"]
> [Kuyruklar hızlı başlangıcı](storage-quickstart-queues-portal.md)
