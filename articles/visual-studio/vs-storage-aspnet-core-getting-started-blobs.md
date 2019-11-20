---
title: Visual Studio kullanarak Azure Blob depolama ile çalışmaya başlama (ASP.NET Core)
description: Visual Studio 'ya bağlı hizmetleri kullanarak bir depolama hesabına bağlandıktan sonra Visual Studio 'da bir ASP.NET Core projesinde Azure Blob depolamayı kullanmaya başlama
services: storage
documentationcenter: ''
author: ghogen
manager: jillfra
editor: ''
ms.service: storage
ms.workload: web
ms.custom: vs-azure
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 91e95dcf7cfc46073e2fd752a0e7e5ae6fce4ee8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300069"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Azure Blob depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET Core)

> [!div class="op_single_selector"]
> - [ASP.NET](./vs-storage-aspnet-getting-started-blobs.md)
> - [ASP.NET Core](./vs-storage-aspnet-core-getting-started-blobs.md)

Azure Blob depolama, yapılandırılmamış verileri bulutta veya bloblarla depolayan bir hizmettir. Blob Storage belge, medya dosyası veya uygulama yükleyici gibi her tür metin veya ikili veri depolayabilir. Blob Storage aynı zamanda nesne depolama olarak adlandırılır. BLOB depolama hakkında daha fazla bilgi için bkz. [Azure Blob depolamaya giriş](../storage/blobs/storage-blobs-introduction.md).

Bu öğreticide, blob depolamayı kullanan bazı yaygın senaryolar için ASP.NET Core kodunun nasıl yazılacağı gösterilmektedir. Senaryolar, blob kapsayıcısı oluşturma ve Blobları karşıya yükleme, listeleme, indirme ve silmeyi içerir.

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

## <a name="prerequisites"></a>Önkoşullar

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

## <a name="set-up-the-development-environment"></a>Geliştirme ortamını ayarlama

Bu bölümde, geliştirme ortamının kurulması gösterilmektedir. Buna bir ASP.NET Model-View-Controller (MVC) uygulaması oluşturma, bağlı hizmetler bağlantısı ekleme, denetleyici ekleme ve gerekli ad alanı yönergelerini belirtme dahildir.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC uygulama projesi oluşturma

1. Visual Studio'yu açın.

1. Ana menüden **dosya** > **Yeni** > **Proje**' yi seçin.

1. **Yeni proje** iletişim kutusunda Web  > **ASP.NET Core Web uygulaması** > **aspnetcorestorage** **' i seçin**. Sonra **Tamam**’ı seçin.

    ![Visual Studio yeni proje iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/new-project.png)

1. **Yeni ASP.NET Core Web uygulaması** iletişim kutusunda **.NET Core** > **ASP.NET Core 2,0** > **Web uygulaması (Model-View-Controller)** seçeneğini belirleyin. Sonra **Tamam**’ı seçin.

    ![Yeni ASP.NET Core Web uygulaması iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/new-mvc.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Azure depolama hesabına bağlanmak için bağlı hizmetleri kullanma

1. **Çözüm Gezgini**, projeye sağ tıklayın.

2. Bağlam menüsünden  > **bağlı hizmet** **Ekle**' yi seçin.

1. **Bağlı hizmetler** iletişim kutusunda, **Azure depolama ile bulut depolama**' yı seçin ve ardından **Yapılandır**' ı seçin.

    ![Bağlı hizmetler iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/connected-services.png)

1. **Azure depolama** iletişim kutusunda, bu öğretici Için kullanılacak Azure Depolama hesabını seçin. Yeni bir Azure depolama hesabı oluşturmak için **Yeni depolama hesabı oluştur**' u seçin ve formu doldurun. Mevcut bir depolama hesabı seçtikten veya yeni bir tane oluşturduktan sonra **Ekle**' yi seçin. Visual Studio, Azure depolama için NuGet paketini ve **appSettings. JSON**' a bir depolama bağlantı dizesi ' ni yükleyerek.

> [!TIP]
> [Azure Portal](https://portal.azure.com)bir depolama hesabı oluşturmayı öğrenmek için bkz. [depolama hesabı oluşturma](../storage/common/storage-quickstart-create-account.md).
>
> [Azure PowerShell](../storage/common/storage-powershell-guide-full.md), [Azure CLI](../storage/common/storage-azure-cli.md)veya [Azure Cloud Shell](../cloud-shell/overview.md)kullanarak da bir depolama hesabı oluşturabilirsiniz.


### <a name="create-an-mvc-controller"></a>MVC denetleyicisi oluşturma 

1. **Çözüm Gezgini**, **denetleyiciler**öğesine sağ tıklayın.

2. Bağlam menüsünden  >  denetleyicisi **Ekle**' yi seçin.

    ![Çözüm Gezgini ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-menu.png)

1. **Yapı Iskelesi Ekle** Iletişim kutusunda **MVC denetleyici-boş**seçeneğini belirleyin ve **Ekle**' yi seçin.

    ![Yapı iskelesi Ekle iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller.png)

1. **Boş MVC denetleyicisi Ekle** iletişim kutusunda, denetleyiciyi *blobscontroller*olarak adlandırın ve **Ekle**' yi seçin.

    ![Boş MVC denetleyicisi Ekle iletişim kutusunun ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/add-controller-name.png)

1. Aşağıdaki `using` yönergelerini `BlobsController.cs` dosyasına ekleyin:

    ```csharp
    using System.IO;
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

## <a name="connect-to-a-storage-account-and-get-a-container-reference"></a>Bir depolama hesabına bağlanın ve bir kapsayıcı başvurusu alın

Blob kapsayıcısı, Blobların ve klasörlerin iç içe bir hiyerarşisidir. Bu belgedeki adımların geri kalanında bir blob kapsayıcısına başvuru gerekir, böylece kod yeniden kullanılabilirlik için kendi yöntemine yerleştirilmelidir.

Aşağıdaki adımlarda, **appSettings. JSON**' daki bağlantı dizesini kullanarak depolama hesabına bağlanmak için bir yöntem oluşturulur. Adımlar bir kapsayıcıya başvuru de oluşturur. **AppSettings. JSON** ' daki bağlantı dizesi ayarı `<storageaccountname>_AzureStorageConnectionString` biçimiyle adlandırılır. 

1. `BlobsController.cs` dosyasını açın.

1. **Cloudblobcontainer**döndüren **getcloudblobcontainer** adlı bir yöntem ekleyin. @No__t-0 ' ı, **Web. config**dosyasındaki anahtarın gerçek adıyla değiştirdiğinizden emin olun.
    
    ```csharp
    private CloudBlobContainer GetCloudBlobContainer()
    {
        var builder = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json");
        IConfigurationRoot Configuration = builder.Build();
        CloudStorageAccount storageAccount = 
            CloudStorageAccount.Parse(Configuration["ConnectionStrings:aspnettutorial_AzureStorageConnectionString"]);
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
    ViewBag.Success = container.CreateIfNotExistsAsync().Result;
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
        ViewBag.Success = container.CreateIfNotExistsAsync().Result;
        ViewBag.BlobContainerName = container.Name;

        return View();
    }
    ```

1. **Çözüm Gezgini**, **Görünümler** klasörüne sağ tıklayın.

2. Bağlam menüsünden  > **Yeni klasör** **Ekle**' yi seçin. Yeni klasör *bloblarını*adlandırın. 

1. **Çözüm Gezgini**, **Görünümler** klasörünü genişletin ve **Bloblar**' a sağ tıklayın.

4. Bağlam menüsünden  >  görünümü **Ekle**' yi seçin.

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

1. Şu şekilde görünen sırasız listeyi arayın: `<ul class="nav navbar-nav">`.  Listedeki son `<li>` öğesinden sonra, başka bir gezinti menüsü öğesi eklemek için aşağıdaki HTML 'yi ekleyin:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="CreateBlobContainer">Create blob container</a></li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **BLOB kapsayıcısı oluştur** ' u seçin:
  
    ![Blob kapsayıcısı oluştur ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/create-blob-container-results.png)

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
        blob.UploadFromStreamAsync(fileStream).Wait();
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
            blob.UploadFromStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Listedeki son `<li>` öğesinden sonra, başka bir gezinti menüsü öğesi eklemek için aşağıdaki HTML 'yi ekleyin:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="UploadBlob">Upload blob</a></li>
    ```

1. Uygulamayı çalıştırın ve **blobu karşıya yükle**' yi seçin. Başarı sözcüğü *!* görünmelidir.
    
    ![Başarı doğrulamanın ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/upload-blob.png)
  
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
   
1. Blob kapsayıcısındaki Blobları listelemek için `CloudBlobContainer.ListBlobsSegmentedAsync` yöntemini kullanın. @No__t-0 yöntemi `BlobResultSegment` döndürür. Bu, `CloudBlockBlob`, `CloudPageBlob` veya @no__t 3 nesnelerine kullanılabilecek `IListBlobItem` nesneleri içerir. Aşağıdaki kod parçacığı bir blob kapsayıcısındaki tüm Blobları numaralandırır. Her blob, türüne göre uygun nesneye yayın yapılır. Adı (veya `CloudBlobDirectory` olması durumunda URI) bir listeye eklenir.

    ```csharp
    List<string> blobs = new List<string>();
    BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
    foreach (IListBlobItem item in resultSegment.Results)
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
    Aşağıda, tamamlanan `ListBlobs` yöntemi gösterilmektedir:

    ```csharp
    public ActionResult ListBlobs()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        List<string> blobs = new List<string>();
        BlobResultSegment resultSegment = container.ListBlobsSegmentedAsync(null).Result;
        foreach (IListBlobItem item in resultSegment.Results)
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

1. Listedeki son `<li>` öğesinden sonra, başka bir gezinti menüsü öğesi eklemek için aşağıdaki HTML 'yi ekleyin:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="ListBlobs">List blobs</a></li>
    ```

1. Uygulamayı çalıştırın ve aşağıdaki ekran görüntüsüne benzer sonuçları görmek için **liste Blobları** ' nı seçin:
  
    ![Blobların listesi ekran görüntüsü](./media/vs-storage-aspnet-core-getting-started-blobs/listblobs.png)

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
        blob.DownloadToStreamAsync(fileStream).Wait();
    }
    ```
    
    Aşağıda, tamamlanan `ListBlobs` yöntemi gösterilmektedir (oluşturulan yerel dosyanın tam yolu ile):
    
    ```csharp
    public string DownloadBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        using (var fileStream = System.IO.File.OpenWrite(@"c:\src\downloadedBlob.txt"))
        {
            blob.DownloadToStreamAsync(fileStream).Wait();
        }
        return "success!";
    }
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Listedeki son `<li>` öğesinden sonra, başka bir gezinti menüsü öğesi eklemek için aşağıdaki HTML 'yi ekleyin:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DownloadBlob">Download blob</a></li>
    ```

1. Uygulamayı çalıştırın ve blobu indirmek için **blobu indir** ' i seçin. @No__t-0 Yöntem çağrısında belirtilen blob, `File.OpenWrite` Yöntem çağrısında belirtilen konuma indirilir. Metin *başarılı!* tarayıcıda görünmelidir. 

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
    blob.DeleteAsync().Wait();
    ```
    
    Tamamlanan `DeleteBlob` yöntemi aşağıdaki gibi görünmelidir:
    
    ```csharp
    public string DeleteBlob()
    {
        CloudBlobContainer container = GetCloudBlobContainer();
        CloudBlockBlob blob = container.GetBlockBlobReference("myBlob");
        blob.DeleteAsync().Wait();
        return "success!";
    }
    ```

1. **Çözüm Gezgini**' de, **Görünümler** > **paylaşılan** klasörünü genişletin ve `_Layout.cshtml` ' ü açın.

1. Listedeki son `<li>` öğesinden sonra, başka bir gezinti menüsü öğesi eklemek için aşağıdaki HTML 'yi ekleyin:

    ```html
    <li><a asp-area="" asp-controller="Blobs" asp-action="DeleteBlob">Delete blob</a></li>
    ```

1. Uygulamayı çalıştırın ve `CloudBlobContainer.GetBlockBlobReference` Yöntem çağrısında belirtilen blobu silmek için **blobu Sil** ' i seçin. Metin *başarılı!* tarayıcıda görünmelidir. Tarayıcının **geri** düğmesini seçin ve ardından Blobun artık kapsayıcıda bulunmadığından emin olmak için **Blobları Listele** ' yi seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, ASP.NET Core kullanarak Azure Storage 'da blob 'ları depolamayı, listeleyerek ve almayı öğrendiniz. Azure’da veri depolama ile ilgili ek seçenekler hakkında daha fazla bilgi edinmek için daha fazla özellik kılavuzu görüntüleyin.

  * [Azure Tablo depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
  * [Azure kuyruk depolama ve Visual Studio bağlı hizmetler 'i kullanmaya başlama (ASP.NET)](vs-storage-aspnet-getting-started-queues.md)
