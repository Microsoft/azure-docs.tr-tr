---
title: Visual Studio kullanarak Azure Blob depolama kullanmaya başlama (ASP.NET)
description: Visual Studio bağlı hizmetleri kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir ASP.NET projesinde Azure Blob depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d6591cdb8f19484c87bb05f9007521adc34778f9
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298875"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet"></a>Azure Blob depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob depolama, yapılandırılmamış verileri bulutta veya bloblarla depolayan bir hizmettir. Blob Storage belge, medya dosyası veya uygulama yükleyici gibi her tür metin veya ikili veri depolayabilir. Blob Storage aynı zamanda nesne depolama olarak adlandırılır. BLOB depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolamaya giriş](../storage/blobs/storage-blobs-introduction.md).

Bu öğreticide, blob depolamayı kullanan bazı yaygın senaryolar için ASP.NET kodu yazma gösterilmektedir. Senaryolar, blob kapsayıcısı oluşturma ve Blobları karşıya yükleme, listeleme, indirme ve silmeyi içerir.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

## <a name="create-an-mvc-controller"></a>MVC denetleyicisi oluşturma 

1. **Çözüm Gezgini**, **denetleyiciler**öğesine sağ tıklayın.

2. Bağlam menüsünden  >  denetleyicisi **Ekle**' yi seçin.

    ![Ekleme ve denetleyici vurgulanmış Çözüm Gezgini ekran görüntüsü](./media/vs-storage-aspnet-getting-started-blobs/add-controller-menu.png)

1. **Yapı Iskelesi Ekle** Iletişim kutusunda **MVC 5 denetleyici-boş**öğesini seçin ve **Ekle**' yi seçin.

    ![Yapı iskelesi Ekle iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-getting-started-blobs/add-controller.png)

1. **Denetleyici Ekle** iletişim kutusunda, denetleyiciyi *blobscontroller*olarak adlandırın ve **Ekle**' yi seçin.

    ![Denetleyici Ekle iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-getting-started-blobs/add-controller-name.png)

1. Aşağıdaki `using` yönergelerini `BlobsController.cs` dosyasına ekleyin:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Bir depolama hesabına bağlanın ve bir kapsayıcı başvurusu alın

Blob kapsayıcısı, Blobların ve klasörlerin iç içe bir hiyerarşisidir. Bu belgedeki adımların geri kalanında bir blob kapsayıcısına başvuru gerekir, böylece kod yeniden kullanılabilirlik için kendi yöntemine yerleştirilmelidir.

Aşağıdaki adımlarda, **Web. config**dosyasındaki bağlantı dizesini kullanarak depolama hesabına bağlanmak için bir yöntem oluşturulur. Adımlar bir kapsayıcıya başvuru de oluşturur.  **Web. config** dosyasındaki bağlantı dizesi ayarı `<storageaccountname>_AzureStorageConnectionString` biçimiyle adlandırılır. 

1. `BlobsController.cs` dosyasını açın.

1. **Cloudblobcontainer**döndüren **getcloudblobcontainer** adlı bir yöntem ekleyin.  @No__t-0 ' ı, **Web. config**dosyasındaki anahtarın gerçek adıyla değiştirdiğinizden emin olun.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
                CloudConfigurationManager.GetSetting("<storageaccountname>_AzureStorageConnectionString"));
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer container = blobClient.GetContainerReference("test-blob-container");
        return container;
    }
    ```

> [!NOTE]
> *Test-blob-kapsayıcısı* henüz mevcut olmasa bile, bu kod buna bir başvuru oluşturur. Bu, kapsayıcının bir sonraki adımda gösterilen `CreateIfNotExists` yöntemiyle oluşturulabilmesini sağlayacak.

## <a name="create-a-blob-container"></a>Blob kapsayıcısı oluşturma

Aşağıdaki adımlarda bir blob kapsayıcısının nasıl oluşturulacağı gösterilmektedir:

1. @No__t-1 döndüren `CreateBlobContainer` adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. İstenen blob kapsayıcısı adına bir başvuruyu temsil eden `CloudBlobContainer` nesnesi alır. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Henüz yoksa, kapsayıcıyı oluşturmak için `CloudBlobContainer.CreateIfNotExists` yöntemini çağırın. @No__t-0 yöntemi, kapsayıcı yoksa **doğru** değerini döndürür ve başarıyla oluşturulur. Aksi takdirde, yöntem **false**döndürür.    

    ```csharp
    ViewBag.Success = container.CreateIfNotExists();
    ```

1. @No__t-0 ' yı blob kapsayıcısının adıyla güncelleştirin.

    ```csharp
    ViewBag.BlobContainerName = container.Name;
    ```
    
    Aşağıda, tamamlanan `CreateBlobContainer` yöntemi gösterilmektedir:

    ```csharp
    public ActionResult CreateBlobContainer()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        ViewBag.Success = container.CreateIfNotExists();
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörüne sağ tıklayın.

1. **Bloblar** klasörü yoksa, bir tane oluşturun. Bağlam menüsünden  > **Yeni klasör** **Ekle**' yi seçin. Yeni klasör *bloblarını*adlandırın. 
 
1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin ve **Bloblar**' a sağ tıklayın.

1. Bağlam menüsünden  >  görünümü **Ekle**' yi seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı Için **createblobcontainer** yazın ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve aşağıdaki kod parçacığı gibi görünmesi için değiştirin:

    ```csharp
    @{
        ViewBag.Title = "Create Blob Container";
    }
    
    <h2>Create Blob Container results</h2>

    Creation of @ViewBag.BlobContainerName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Create blob container", "CreateBlobContainer", "Blobs")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **BLOB kapsayıcısı oluştur** ' u seçin:
  
    ![Blob kapsayıcısı oluştur ekran görüntüsü](./media/vs-storage-aspnet-getting-started-blobs/create-blob-container-results.png)

    Daha önce belirtildiği gibi, `CloudBlobContainer.CreateIfNotExists` yöntemi yalnızca kapsayıcı yoksa ve oluşturulduğunda **true** değerini döndürür. Bu nedenle, kapsayıcı varken uygulama çalıştırılır, yöntem **false**döndürür.

## <a name="upload-a-blob-into-a-blob-container"></a>Blob kapsayıcısına blob yükleme

[BLOB kapsayıcısı oluşturulduğunda](#create-a-blob-container), dosyaları bu kapsayıcıya yükleyin. Bu bölüm bir blob kapsayıcısına yerel bir dosya yüklemeyi açıklar. Adımlarda, *Test-blob-Container*adlı bir blob kapsayıcısı olduğu varsayılır. 

1. `BlobsController.cs` dosyasını açın.

1. String döndüren `UploadBlob` adlı bir yöntem ekleyin.

    ```csharp
    public string UploadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. @No__t-0 yönteminde, istenen blob kapsayıcısı adına bir başvuruyu temsil eden bir `CloudBlobContainer` nesnesi alın. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. Azure depolama farklı blob türlerini destekler. Bu öğretici blok bloblarını kullanır. Bir blok blobuna başvuru almak için `CloudBlobContainer.GetBlockBlobReference` yöntemini çağırın.

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```
    
    > [!NOTE]
    > Blob adı bir blobu almak için kullanılan URL 'nin bir parçasıdır ve dosyanın adı da dahil olmak üzere herhangi bir dize olabilir.

1. Blob başvurusu olduktan sonra, blob başvuru nesnesinin `UploadFromStream` yöntemini çağırarak herhangi bir veri akışını ona yükleyebilirsiniz. @No__t-0 yöntemi, mevcut değilse blobu oluşturur veya varsa üzerine yazar. ( *@No__t-1 dosya yükleme-karşıya* yüklenecek bir dosyanın tam yoluna >.)

    ```csharp
    using (var fileStream = System.IO.File.OpenRead(@"<file-to-upload>"))
    {
        blob.UploadFromStream(fileStream);
    }
    ```
    
    Aşağıda, tamamlanan `UploadBlob` yöntemi gösterilmektedir (karşıya yüklenecek dosyanın tam yolu ile):

    ```csharp
    public string UploadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenRead(@"c:\src\sample.txt"))
        {
            blob.UploadFromStream(fileStream);
        }
        return "success!";
    }
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Upload blob", "UploadBlob", "Blobs")</li>
    ```

1. Uygulamayı çalıştırın ve **blobu karşıya yükle**' yi seçin.  Başarı sözcüğü *!* görünmelidir.
    
    ![Başarı doğrulamanın ekran görüntüsü](./media/vs-storage-aspnet-getting-started-blobs/upload-blob.png)
  
## <a name="list-the-blobs-in-a-blob-container"></a>Blob kapsayıcısındaki Blobları listeleme

Bu bölüm bir blob kapsayıcısındaki Blobların nasıl ekleneceğini gösterir. Örnek kod, bölümünde oluşturulan *Test-blob-kapsayıcısına* başvurur, [bir blob kapsayıcısı oluşturur](#create-a-blob-container).

1. `BlobsController.cs` dosyasını açın.

1. @No__t-1 döndüren `ListBlobs` adlı bir yöntem ekleyin.

    ```csharp
    public ActionResult ListBlobs()
    {
        // The code in this section goes here.

    }
    ```
 
1. @No__t-0 yönteminde, blob kapsayıcısına bir başvuruyu temsil eden bir `CloudBlobContainer` nesnesi alın. 
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```
   
1. Blob kapsayıcısındaki Blobları listelemek için `CloudBlobContainer.ListBlobs` yöntemini kullanın. @No__t-0 yöntemi, bir `CloudBlockBlob`, `CloudPageBlob` veya `CloudBlobDirectory` nesnesine tür bir `IListBlobItem` nesnesi döndürür. Aşağıdaki kod parçacığı bir blob kapsayıcısındaki tüm Blobları numaralandırır. Her blob, türüne göre uygun nesneye yayın yapılır. Adı (veya **Cloudblobdirectory**söz konusu olduğunda URI) bir listeye eklenir.

    ```csharp
    List<string> blobs = new List<string>();

    foreach (IListBlobItem item in container.ListBlobs())
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob blob = (CloudPageBlob)item;
            blobs.Add(blob.Name);
        }
        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory dir = (CloudBlobDirectory)item;
            blobs.Add(dir.Uri.ToString());
        }
    }

    return View(blobs);
    ```

    Blob kapsayıcıları, bloblara ek olarak dizinler içerebilir. Aşağıdaki hiyerarşi ile *Test-blob-Container*adlı bir blob kapsayıcısı olduğunu varsayalım:

        foo.png
        dir1/bar.png
        dir2/baz.png

    Önceki kod örneğini kullanarak, **Bloblar** dize listesi aşağıdakine benzer değerler içerir:

        foo.png
        <storage-account-url>/test-blob-container/dir1
        <storage-account-url>/test-blob-container/dir2

    Gösterildiği gibi liste, iç içe geçmiş olanları değil yalnızca en üst düzey varlıkları içerir (*çubuk. png* ve *baz. png*). Blob kapsayıcısı içindeki tüm varlıkları listelemek için, kodu değiştirin, böylece **cloudblobcontainer. listblobları** yönteminin **Useyataybloblist** parametresi için **true** olarak geçirilmesi gerekir.    

    ```csharp
    //...
    foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing:true))
    //...
    ```

    **Useyataybloblisteleme** parametresinin **true** olarak ayarlanması, blob kapsayıcısındaki tüm varlıkların düz bir listesini döndürür. Bu, aşağıdaki sonuçları verir:

        foo.png
        dir1/bar.png
        dir2/baz.png
    
    Tamamlanan **Listbloblar** yöntemi aşağıda gösterilmiştir:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        foreach (IListBlobItem item in container.ListBlobs(useFlatBlobListing: true))
        {
            if (item.GetType() == typeof(CloudBlockBlob))
            {
                CloudBlockBlob blob = (CloudBlockBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudPageBlob))
            {
                CloudPageBlob blob = (CloudPageBlob)item;
                blobs.Add(blob.Name);
            }
            else if (item.GetType() == typeof(CloudBlobDirectory))
            {
                CloudBlobDirectory dir = (CloudBlobDirectory)item;
                blobs.Add(dir.Uri.ToString());
            }
        }

        return View(blobs);
    }
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin ve **Bloblar**' a sağ tıklayın.

2. Bağlam menüsünden  >  görünümü **Ekle**' yi seçin.

1. **Görünüm Ekle** iletişim kutusunda, görünüm adı için `ListBlobs` girin ve **Ekle**' yi seçin.

1. @No__t-0 ' ı açın ve içeriği şu kodla değiştirin:

    ```html
    @model List<string>
    @{
        ViewBag.Title = "List blobs";
    }
    
    <h2>List blobs</h2>
    
    <ul>
        @foreach (var item in Model)
        {
        <li>@item</li>
        }
    </ul>
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("List blobs", "ListBlobs", "Blobs")</li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **liste Blobları** ' nı seçin:
  
    ![Blobların listesi ekran görüntüsü](./media/vs-storage-aspnet-getting-started-blobs/listblobs.png)

## <a name="download-blobs"></a>Blob’ları indirme

Bu bölümde bir Blobun nasıl indirileceği gösterilmektedir. Yerel depolamaya devam edebilir veya içeriği bir dizeye okuyabilirsiniz. Örnek kod, bölümünde oluşturulan *Test-blob-kapsayıcısına* başvurur, [bir blob kapsayıcısı oluşturur](#create-a-blob-container).

1. `BlobsController.cs` dosyasını açın.

1. String döndüren `DownloadBlob` adlı bir yöntem ekleyin.

    ```csharp
    public string DownloadBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```
 
1. @No__t-0 yönteminde, blob kapsayıcısına bir başvuruyu temsil eden bir `CloudBlobContainer` nesnesi alın.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. @No__t-0 yöntemini çağırarak bir blob başvurusu nesnesi alın. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Bir blobu indirmek için `CloudBlockBlob.DownloadToStream` yöntemini kullanın. Aşağıdaki kod, bir Blobun içeriğini Stream nesnesine aktarır. Bu nesne daha sonra yerel bir dosyaya kalıcı hale getirilir. ( *@No__t-1 yerel dosya adı >* blob 'un indirileceği yeri temsil eden tam dosya adına değiştirin.) 

    ```csharp
    using (var fileStream = System.IO.File.OpenWrite(<local-file-name>))
    {
        blob.DownloadToStream(fileStream);
    }
    ```
    
    Aşağıda, tamamlanan `DownloadBlob` yöntemi gösterilmektedir (oluşturulan yerel dosyanın tam yolu ile):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStream(fileStream);
        }
        return "success!";
    }
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Download blob", "DownloadBlob", "Blobs")</li>
    ```

1. Uygulamayı çalıştırın ve blobu indirmek için **blobu indir** ' i seçin. @No__t-0 Yöntem çağrısında belirtilen blob, `File.OpenWrite` Yöntem çağrısında belirtilen konuma indirilir.  Metin *başarılı!* tarayıcıda görünmelidir. 

## <a name="delete-blobs"></a>Blob’ları silme

Aşağıdaki adımlarda bir Blobun nasıl silineceği gösterilmektedir:

1. `BlobsController.cs` dosyasını açın.

1. String döndüren `DeleteBlob` adlı bir yöntem ekleyin.

    ```csharp
    public string DeleteBlob()
    {
        // The code in this section goes here.

        return "success!";
    }
    ```

1. @No__t-0 yönteminde, blob kapsayıcısına bir başvuruyu temsil eden bir `CloudBlobContainer` nesnesi alın.
   
    ```csharp
    CloudBlobContainer container = GetCloudBlobContainer();
    ```

1. @No__t-0 yöntemini çağırarak bir blob başvurusu nesnesi alın. 

    ```csharp
    CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
    ```

1. Bir blobu silmek için `Delete` yöntemini kullanın.

    ```csharp
    blob.Delete();
    ```
    
    Tamamlanan `DeleteBlob` yöntemi aşağıdaki gibi görünmelidir:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.Delete();
        return "success!";
    }
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Son **HTML. ActionLink**sonra, aşağıdaki **HTML. ActionLink**öğesini ekleyin:

    ```html
    <li>@Html.ActionLink("Delete blob", "DeleteBlob", "Blobs")</li>
    ```

1. Uygulamayı çalıştırın ve `CloudBlobContainer.GetBlockBlobReference` Yöntem çağrısında belirtilen blobu silmek için **blobu Sil** ' i seçin. Metin *başarılı!* tarayıcıda görünmelidir. Tarayıcının **geri** düğmesini seçin ve ardından Blobun artık kapsayıcıda bulunmadığından emin olmak için **Blobları Listele** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ASP.NET kullanarak Azure Storage 'da blob 'ları depolamayı, listeleyerek ve almayı öğrendiniz. Azure’da veri depolama ile ilgili ek seçenekler hakkında daha fazla bilgi edinmek için daha fazla özellik kılavuzu görüntüleyin.

  * [Azure Tablo depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Azure kuyruk depolama ve Visual Studio bağlı hizmetler 'i kullanmaya başlama (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
