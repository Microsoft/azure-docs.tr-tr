---
title: Visual Studio kullanarak Azure kuyruk depolama ile çalışmaya başlama (ASP.NET)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir ASP.NET projesinde Azure kuyruk depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5b57f171a9407acea8231d796a80c3a1cc1d9474
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300102"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Azure kuyruk depolama ve Visual Studio bağlı hizmetler 'i kullanmaya başlama (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Genel Bakış

Azure kuyruk depolama, uygulama bileşenleri arasında bulut mesajlaşmasını sağlar. Ölçeklendirmek üzere uygulama tasarlarken, uygulama bileşenleri birbirinden bağımsız şekilde ölçeklenebilmek için genellikle birbirinden ayrılır. Kuyruk depolama bulutta, masaüstünde, şirket içi sunucuda veya mobil bir cihazda çalışan uygulama bileşenleri arasındaki iletişim için zaman uyumsuz mesajlaşma sunar. Kuyruk depolama ayrıca zaman uyumsuz görevlerin yönetilmesini ve süreç iş akışlarının oluşturulmasını destekler.

Bu öğreticide, Azure kuyruk depolama varlıklarını kullanarak bazı yaygın senaryolar için ASP.NET kodu yazma gösterilmektedir. Bu senaryolar, Azure kuyruğu oluşturma ve sıra iletilerini ekleme, değiştirme, okuma ve kaldırma gibi genel görevleri içerir.

## <a name="prerequisites"></a>Önkoşullar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure depolama hesabı](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>MVC denetleyicisi oluşturma 

1. **Çözüm Gezgini**, **denetleyiciler**' e sağ tıklayın ve bağlam menüsünde, **Ekle-> denetleyicisi**' ni seçin.

    ![ASP.NET MVC uygulamasına denetleyici ekleme](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. **Yapı Iskelesi Ekle** Iletişim kutusunda **MVC 5 denetleyici-boş**öğesini seçin ve **Ekle**' yi seçin.

    ![MVC denetleyici türünü belirtin](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. **Denetleyici Ekle** iletişim kutusunda, denetleyiciyi *queuescontroller*olarak adlandırın ve **Ekle**' yi seçin.

    ![MVC denetleyicisini adlandırın](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Aşağıdaki *using* yönergelerini `QueuesController.cs` dosyasına ekleyin:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Bir kuyruk oluşturma

Aşağıdaki adımlarda bir sıranın nasıl oluşturulacağı gösterilmektedir:

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınızı varsayar. 

1. `QueuesController.cs` dosyasını açın. 

1. Bir **ActionResult**döndüren **CreateQueue** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. **CreateQueue** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. **Cloudqueueclient** nesnesi alma, bir kuyruk hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. İstenen kuyruk adına bir başvuruyu temsil eden bir **Cloudqueue** nesnesi alın. **Cloudqueueclient. GetQueueReference** yöntemi kuyruk depolamaya karşı bir istek yapmaz. Başvuru, sıranın var olup olmadığına bakılmaksızın döndürülür. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Henüz yoksa, sırayı oluşturmak için **Cloudqueue. CreateIfNotExists** yöntemini çağırın. **Cloudqueue. CreateIfNotExists** yöntemi, sıra yoksa **true** değerini döndürür ve başarıyla oluşturulur. Aksi takdirde, **false** döndürülür.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. **ViewBag** 'i kuyruğun adıyla güncelleştirin.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Kuyruklar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda Görünüm adı Için **CreateQueue** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **kuyruk oluştur** ' u seçin:
  
    ![Sıra oluştur](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Daha önce belirtildiği gibi **Cloudqueue. CreateIfNotExists** yöntemi yalnızca sıra mevcut olmadığında ve oluşturulduğunda **true** değerini döndürür. Bu nedenle, kuyruk varken uygulamayı çalıştırırsanız, yöntem **false**döndürür. Uygulamayı birden çok kez çalıştırmak için, uygulamayı yeniden çalıştırmadan önce kuyruğu silmeniz gerekir. Kuyruğun silinmesi **Cloudqueue. Delete** yöntemi aracılığıyla yapılabilir. Kuyruğu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) veya [Microsoft Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md)kullanarak da silebilirsiniz.  

## <a name="add-a-message-to-a-queue"></a>Kuyruğa ileti ekleme

[Bir kuyruğu](#create-a-queue)oluşturduktan sonra bu kuyruğa iletiler ekleyebilirsiniz. Bu bölüm, bir kuyruk *Test kuyruğuna*ileti ekleme konusunda size kılavuzluk eder. 

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınızı varsayar. 

1. `QueuesController.cs` dosyasını açın.

1. **ActionResult**döndüren **AddMessage** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **AddMessage** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **Cloudqueueclient** nesnesi alma, bir kuyruk hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Kuyruğa yönelik bir başvuruyu temsil eden bir **Cloudqueuecontainer** nesnesi alın. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Kuyruğa eklemek istediğiniz iletiyi temsil eden **Cloudqueuemessage** nesnesini oluşturun. **Cloudqueuemessage** nesnesi, bir DIZEDEN (UTF-8 biçiminde) ya da bir bayt dizisinden oluşturulabilir.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. İletiyi kuyruğa eklemek için **Cloudqueue. AddMessage** yöntemini çağırın.

    ```csharp
    queue.AddMessage(message);
    ```

1. Görünümde görüntülenmek üzere birkaç **ViewBag** özelliği oluşturun ve ayarlayın.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Kuyruklar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **AddMessage** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **Ileti Ekle** ' yi seçin:
  
    ![İleti Ekle](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

İki bölüm-bir kuyruktan [kaldırmadan iletiyi](#read-a-message-from-a-queue-without-removing-it) okuyun ve [kuyruktan bir ileti okuyup kaldırın](#read-and-remove-a-message-from-a-queue) -bir kuyruktaki iletilerin nasıl okunacağını gösterir.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Kuyruktan kaldırmadan bir iletiyi okuyun

Bu bölümde, sıraya alınan bir iletinin nasıl incelenmiştir (onu kaldırmadan ilk iletiyi okuyun) gösterilmektedir.  

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınızı varsayar. 

1. `QueuesController.cs` dosyasını açın.

1. **ActionResult**döndüren **PeekMessage** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **PeekMessage** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **Cloudqueueclient** nesnesi alma, bir kuyruk hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Kuyruğa yönelik bir başvuruyu temsil eden bir **Cloudqueuecontainer** nesnesi alın. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Kuyruktaki ilk iletiyi kuyruktan kaldırmadan okumak için **cloudqueue. PeekMessage** yöntemini çağırın. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. **ViewBag** 'i iki değerle güncelleştirin: kuyruk adı ve okunan ileti. **Cloudqueuemessage** nesnesi, nesnenin değerini almak için iki özellik sunar: **cloudqueuemessage. Asbytes** ve **Cloudqueuemessage. AsString**. **AsString** (Bu örnekte kullanılan) bir dize döndürür, ancak **asbytes** bir bayt dizisi döndürür.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Kuyruklar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı için **PeekMessage** girin ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **Iletiye Gözat** ' ı seçin:
  
    ![İletiye Gözat](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Kuyruktan bir ileti okuma ve kaldırma

Bu bölümde, bir kuyruktan bir ileti okumayı ve kaldırmayı öğrenirsiniz.   

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınızı varsayar. 

1. `QueuesController.cs` dosyasını açın.

1. Bir **ActionResult**döndüren **ReadMessage** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ReadMessage** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **Cloudqueueclient** nesnesi alma, bir kuyruk hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Kuyruğa yönelik bir başvuruyu temsil eden bir **Cloudqueuecontainer** nesnesi alın. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Sıradaki ilk iletiyi okumak için **cloudqueue. GetMessage** yöntemini çağırın. **Cloudqueue. GetMessage** yöntemi, iletiyi işlerken başka hiçbir kodun hiçbir kodu değiştirememesi veya silebilmesi için, iletiyi 30 saniye boyunca (varsayılan olarak) herhangi bir kod okuma iletisine görünmez hale getirir. İletinin görünmez olduğu süreyi değiştirmek için **Cloudqueue. GetMessage** metoduna geçirilen **visibilitytimeout** parametresini değiştirin.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. İletiyi kuyruktan silmek için **Cloudqueuemessage. Delete** yöntemini çağırın.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. **Görünüm paketini** silinen iletiyle ve kuyruğun adına güncelleştirin.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Kuyruklar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **ReadMessage** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **okuma/silme iletisi** ' ni seçin:
  
    ![İletiyi oku ve Sil](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Kuyruk uzunluğu alma

Bu bölüm, sıranın uzunluğunu (ileti sayısı) nasıl alınacağını gösterir. 

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınızı varsayar. 

1. `QueuesController.cs` dosyasını açın.

1. **ActionResult**döndüren **getqueuelength** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **ReadMessage** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **Cloudqueueclient** nesnesi alma, bir kuyruk hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Kuyruğa yönelik bir başvuruyu temsil eden bir **Cloudqueuecontainer** nesnesi alın. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Sıranın özniteliklerini (uzunluğu dahil) almak için **Cloudqueue. FetchAttributes** metodunu çağırın. 

    ```csharp
    queue.FetchAttributes();
    ```

1. Kuyruğun uzunluğunu almak için **Cloudqueue. yaklaşık Temessagecount** özelliğine erişin.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. **ViewBag** öğesini kuyruğun adı ve uzunluğu ile güncelleştirin.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Kuyruklar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda Görünüm adı Için **getqueuelength** girin ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **sıra uzunluğu al** ' ı seçin:
  
    ![Kuyruk uzunluğunu al](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Bir kuyruk silme
Bu bölümde bir kuyruğun nasıl silineceği gösterilmektedir. 

> [!NOTE]
> 
> Bu bölüm, [geliştirme ortamını ayarlama](#set-up-the-development-environment)adımlarını tamamladığınızı varsayar. 

1. `QueuesController.cs` dosyasını açın.

1. Bir **ActionResult**döndüren **DeleteQueue** adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. **DeleteQueue** yöntemi içinde, depolama hesabı bilgilerinizi temsil eden bir **cloudstorageaccount** nesnesi alın. Azure hizmet yapılandırmasından depolama bağlantı dizesi ve depolama hesabı bilgilerini almak için aşağıdaki kodu kullanın: ( *&lt;storage-Account-name >* ' i eriştiğiniz Azure Storage hesabının adına değiştirin.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. **Cloudqueueclient** nesnesi alma, bir kuyruk hizmeti istemcisini temsil eder.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Kuyruğa yönelik bir başvuruyu temsil eden bir **Cloudqueuecontainer** nesnesi alın. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. **Cloudqueue** nesnesi tarafından temsil edilen sırayı silmek Için **Cloudqueue. Delete** yöntemini çağırın.

    ```csharp
    queue.Delete();
    ```

1. **ViewBag** öğesini kuyruğun adı ve uzunluğu ile güncelleştirin.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin, **Kuyruklar**' a sağ tıklayın ve bağlam menüsünde, **Ekle-> görünümü**' nü seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **DeleteQueue** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. **Çözüm Gezgini**, **Görünümler-> paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' yi açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **sıra uzunluğu al** ' ı seçin:
  
    ![Kuyruğu Sil](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Sonraki adımlar
Azure’da veri depolama ile ilgili ek seçenekler hakkında daha fazla bilgi edinmek için daha fazla özellik kılavuzu görüntüleyin.

  * [Azure Blob depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Azure Tablo depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
