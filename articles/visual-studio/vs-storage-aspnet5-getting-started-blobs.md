---
title: Visual Studio kullanarak BLOB depolama ile çalışmaya başlama (ASP.NET Core)
description: Visual Studio bağlı hizmetler 'i kullanarak bir depolama hesabı oluşturduktan sonra Visual Studio ASP.NET Core projesinde Azure Blob depolamayı kullanmaya başlama
services: storage
author: ghogen
manager: jillfra
ms.assetid: 094b596a-c92c-40c4-a0f5-86407ae79672
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 81df41470c893f569fd17345e8bdf4b29641ec64
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298838"
---
# <a name="get-started-with-azure-blob-storage-and-visual-studio-connected-services-aspnet-core"></a>Azure Blob depolama ve Visual Studio bağlı hizmetlerini kullanmaya başlama (ASP.NET Core)

[!INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]

Bu makalede, Visual Studio **bağlı hizmetler** özelliğini kullanarak bir ASP.NET Core projesinde Azure Storage hesabı oluşturduktan veya başvurduktan sonra Visual Studio 'Da Azure Blob Storage 'ı kullanmaya nasıl başlacağınız açıklanır. **Bağlı hizmetler** işlemi, projenizde Azure depolama 'ya erişmek ve depolama hesabı için bağlantı dizesini proje yapılandırma dosyalarınıza eklemek üzere uygun NuGet paketlerini yükleyerek ekler. (Azure depolama hakkında genel bilgi için bkz. [depolama belgeleri](https://azure.microsoft.com/documentation/services/storage/) .)

Azure Blob depolama, dünyanın her yerinden HTTP veya HTTPS aracılığıyla erişilebilen büyük miktarda yapılandırılmamış veriyi depolamaya yönelik bir hizmettir. Tek bir blob herhangi bir boyutta olabilir. Blob 'lar görüntüler, ses ve video dosyaları, ham veriler ve belge dosyaları gibi şeyler olabilir. Bu makalede, bir ASP.NET Core projesindeki Visual Studio **bağlı hizmetlerini** kullanarak bir Azure depolama hesabı oluşturduktan sonra BLOB depolama ile çalışmaya başlama işlemi açıklanır.

Klasörlerde canlı olan dosyalar gibi, depolama Blobları kapsayıcılar içinde de vardır. Blob oluşturduktan sonra, o bloba bir veya daha fazla kapsayıcı oluşturursunuz. Örneğin, "Scrapbook" adlı bir blob 'da, resimleri depolamak için "resimler" adlı kapsayıcılar ve ses dosyalarını depolamak için "ses" olarak adlandırılan bir kapsayıcı oluşturabilirsiniz. Kapsayıcıları oluşturduktan sonra tek tek dosyaları bunlara yükleyebilirsiniz. Blob 'ları programlama hakkında daha fazla bilgi için bkz. [hızlı başlangıç: .NET kullanarak Blobları karşıya yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-dotnet.md) .

Bazı Azure depolama API 'Leri zaman uyumsuzdur ve bu makaledeki kodda zaman uyumsuz yöntemlerin kullanıldığı varsayılır. Daha fazla bilgi için bkz. [zaman uyumsuz programlama](https://docs.microsoft.com/dotnet/csharp/async) .

## <a name="access-blob-containers-in-code"></a>Koddaki blob kapsayıcılarına erişin

ASP.NET Core projelerindeki bloblara programlı bir şekilde erişmek için, zaten mevcut değilse aşağıdaki kodu eklemeniz gerekir:

1. Gerekli `using` deyimlerini ekleyin:

    ```cs
    using Microsoft.Extensions.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Extensions.Logging.LogLevel;
    ```

1. Depolama hesabı bilgilerinizi temsil eden `CloudStorageAccount` nesnesi alın. Azure hizmet yapılandırmasından depolama Bağlantı dizenizi ve depolama hesabı bilgilerinizi almak için aşağıdaki kodu kullanın:

    ```cs
     CloudStorageAccount storageAccount = new CloudStorageAccount(
        new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(
        "<storage-account-name>",
        "<access-key>"), true);
    ```

1. Depolama hesabınızdaki mevcut bir kapsayıcıya `CloudBlobContainer` başvurusu almak için `CloudBlobClient` nesnesi kullanın:

    ```cs
    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");
    ```

## <a name="create-a-container-in-code"></a>Kodda kapsayıcı oluşturma

@No__t-1 ' i çağırarak depolama hesabınızda bir kapsayıcı oluşturmak için `CloudBlobClient` ' yı da kullanabilirsiniz:

```cs
// Create a blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Get a reference to a container named "my-new-container."
CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

// If "mycontainer" doesn't exist, create it.
await container.CreateIfNotExistsAsync();
```

Kapsayıcıdaki dosyaları herkese açık hale getirmek için kapsayıcıyı genel olacak şekilde ayarlayın:

```cs
await container.SetPermissionsAsync(new BlobContainerPermissions
{
    PublicAccess = BlobContainerPublicAccessType.Blob
});
```

## <a name="upload-a-blob-into-a-container"></a>Bir kapsayıcıya bir blob yükleme

Bir blob dosyasını kapsayıcıya yüklemek için bir kapsayıcı başvurusu alın ve bunu bir blob başvurusu almak için kullanın. Sonra `UploadFromStreamAsync` yöntemini çağırarak herhangi bir veri akışını bu başvuruya yükleyin. Bu işlem zaten orada değilse blobu oluşturur ve var olan bir Blobun üzerine yazar. 

```cs
// Get a reference to a blob named "myblob".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

// Create or overwrite the "myblob" blob with the contents of a local file
// named "myfile".
using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
{
    await blockBlob.UploadFromStreamAsync(fileStream);
}
```

## <a name="list-the-blobs-in-a-container"></a>Blob’ları bir kapsayıcıda listeleme

Bir kapsayıcıdaki Blobları listelemek için, önce bir kapsayıcı başvurusu alın, ardından içindeki Blobları ve/veya dizinleri almak için `ListBlobsSegmentedAsync` yöntemini çağırın. Döndürülen bir @no__t için zengin özellik ve Yöntem kümesine erişmek için, bir `CloudBlockBlob`, `CloudPageBlob` veya `CloudBlobDirectory` nesnesine atayın. Blob türünü bilmiyorsanız, hangisinin üzerine ekleneceğini öğrenmek için bir tür denetimi kullanın.

```cs
BlobContinuationToken token = null;
do
{
    BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
    token = resultSegment.ContinuationToken;

    foreach (IListBlobItem item in resultSegment.Results)
    {
        if (item.GetType() == typeof(CloudBlockBlob))
        {
            CloudBlockBlob blob = (CloudBlockBlob)item;
            Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
        }

        else if (item.GetType() == typeof(CloudPageBlob))
        {
            CloudPageBlob pageBlob = (CloudPageBlob)item;

            Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
        }

        else if (item.GetType() == typeof(CloudBlobDirectory))
        {
            CloudBlobDirectory directory = (CloudBlobDirectory)item;

            Console.WriteLine("Directory: {0}", directory.Uri);
        }
    }
} while (token != null);
```

Bkz. hızlı başlangıç: blob kapsayıcısının içeriğini listeetmenin diğer yolları için [.NET kullanarak Blobları karşıya yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container) .

## <a name="download-a-blob"></a>Blob indirme

Bir blobu indirmek için, önce blob 'a bir başvuru alın, sonra `DownloadToStreamAsync` yöntemini çağırın. Aşağıdaki örnek, blob içeriğini daha sonra yerel bir dosya olarak kaydedebilmeniz için bir Stream nesnesine aktarmak üzere `DownloadToStreamAsync` yöntemini kullanır.

```cs
// Get a reference to a blob named "photo1.jpg".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

// Save the blob contents to a file named "myfile".
using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
{
    await blockBlob.DownloadToStreamAsync(fileStream);
}
```

Bkz. hızlı başlangıç: blob 'ları dosya olarak kaydetme yolları için [.NET kullanarak Blobları yükleme, indirme ve listeleme](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs) .

## <a name="delete-a-blob"></a>Blob silme

Bir blobu silmek için, önce blob 'a bir başvuru alın, sonra `DeleteAsync` yöntemini çağırın:

```cs
// Get a reference to a blob named "myblob.txt".
CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

// Delete the blob.
await blockBlob.DeleteAsync();
```

## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]
