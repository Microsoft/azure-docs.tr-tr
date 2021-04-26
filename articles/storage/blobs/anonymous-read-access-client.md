---
title: .NET ile ortak kapsayıcılara ve bloblara anonim erişim
titleSuffix: Azure Storage
description: Ortak kapsayıcılara ve bloblara anonim olarak erişmek için .NET için Azure Storage istemci kitaplığı 'nı kullanın.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90088825"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>.NET ile ortak kapsayıcılara ve bloblara anonim erişim

Azure depolama, kapsayıcılar ve BLOB 'lar için isteğe bağlı genel okuma erişimini destekler. İstemciler, Azure depolama istemci kitaplıklarını kullanarak ve Azure depolama 'ya veri erişimini destekleyen diğer araçları ve yardımcı programları kullanarak, ortak kapsayıcılara ve bloblara anonim olarak erişebilir.

Bu makalede, .NET 'ten bir ortak kapsayıcıya veya bloba nasıl erişebileceğiniz gösterilmektedir. Kapsayıcıda anonim okuma erişimini yapılandırma hakkında daha fazla bilgi için bkz. [kapsayıcılar ve Bloblar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md). Bir depolama hesabına tüm anonim erişimi önleme hakkında daha fazla bilgi için bkz. [kapsayıcılara ve bloblara anonim genel okuma erişimini engelleme](anonymous-read-access-prevent.md).

Kapsayıcılara ve bloblara erişen bir istemci, kimlik bilgileri gerektirmeyen oluşturucuları kullanabilir. Aşağıdaki örneklerde, kapsayıcılara ve bloblara anonim olarak başvurmak için birkaç farklı yol gösterilmektedir.

## <a name="create-an-anonymous-client-object"></a>Anonim istemci nesnesi oluşturma

Hesap için BLOB depolama uç noktası sağlayarak anonim erişim için yeni bir hizmet istemci nesnesi oluşturabilirsiniz. Ancak, bu hesapta anonim erişim için kullanılabilen bir kapsayıcının adını da bilmeniz gerekir.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Kapsayıcıya anonim olarak başvuru

Anonim olarak kullanılabilen bir kapsayıcının URL 'SI varsa, kapsayıcıyı doğrudan başvuruda bulunmak için kullanabilirsiniz.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>Bir Blobun anonim olarak başvur

Anonim erişim için kullanılabilen bir Blobun URL 'SI varsa, bu URL 'YI kullanarak blob 'a doğrudan başvurabilirsiniz:

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Kapsayıcılar ve BLOB 'lar için anonim genel okuma erişimini yapılandırma](anonymous-read-access-configure.md)
- [Kapsayıcılara ve bloblara anonim genel okuma erişimini engelleyin](anonymous-read-access-prevent.md)
- [Azure depolama 'ya erişimi yetkilendirme](../common/storage-auth.md)
