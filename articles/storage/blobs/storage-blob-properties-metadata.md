---
title: .NET-Azure depolama ile bir blob için özellikleri ve meta verileri yönetme
description: .NET istemci kitaplığını kullanarak Azure Storage hesabınızdaki bloblarda sistem özelliklerini ayarlama ve alma ve özel meta verileri depolama hakkında bilgi edinin.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: 03171e395bb23da05ee5420f60abca9a16ffb774
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600299"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>.NET ile blob özelliklerini ve meta verileri yönetme

Blob 'lar, içerdikleri verilere ek olarak sistem özelliklerini ve Kullanıcı tanımlı meta verileri destekler. Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage/client)ile sistem özelliklerinin ve Kullanıcı tanımlı meta verilerin nasıl yönetileceği gösterilmektedir.

## <a name="about-properties-and-metadata"></a>Özellikler ve meta veriler hakkında

- **Sistem Özellikleri**: her BLOB depolama kaynağında sistem özellikleri bulunur. Bazıları okunabilir veya ayarlanabilir, diğerleri salt okunurdur. Kapakların altında bazı sistem özellikleri bazı standart HTTP üst bilgilerine karşılık gelir. .NET için Azure depolama istemci kitaplığı, bu özellikleri sizin için korur.

- **Kullanıcı tanımlı meta veriler**: Kullanıcı tanımlı meta veriler, bir BLOB depolama kaynağı için belirlediğiniz bir veya daha fazla ad-değer çiftinden oluşur. Kaynak ile ek değerleri depolamak için meta verileri kullanabilirsiniz. Meta veri değerleri yalnızca kendi amacınıza yöneliktir ve kaynağın nasıl davranacağını etkilemez.

BLOB depolama kaynağı için meta verilerin ve özellik değerlerinin alınması, iki adımlı bir işlemdir. Bu değerleri okuyabilmeniz için önce `FetchAttributes` veya `FetchAttributesAsync` yöntemini çağırarak bunları açıkça almanız gerekir. Bu kuralın istisnası, `Exists` ve `ExistsAsync` yöntemlerinin, kapsamakta olan uygun `FetchAttributes` yöntemini çağırmakta olduğu durumdur. Bu yöntemlerden birini çağırdığınızda de `FetchAttributes` çağırmanız gerekmez.

> [!IMPORTANT]
> Bir depolama kaynağı için özellik veya meta veri değerlerinin doldurulmadığını fark ederseniz, kodunuzun `FetchAttributes` veya `FetchAttributesAsync` yöntemini çağırmadığını kontrol edin.

## <a name="set-and-retrieve-properties"></a>Özellikleri ayarlama ve alma

Aşağıdaki kod örneği bir blob üzerinde `ContentType` ve `ContentLanguage` sistem özelliklerini ayarlar.

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Blob özelliklerini almak için `Properties` özelliğini doldurmak üzere bloba `FetchAttributes` veya `FetchAttributesAsync` yöntemini çağırın. Aşağıdaki kod örneği, bir Blobun sistem özelliklerini alır ve bazı değerleri görüntüler:

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

## <a name="set-and-retrieve-metadata"></a>Meta verileri ayarlama ve alma

Meta verileri bir blob veya kapsayıcı kaynağında bir veya daha fazla ad-değer çifti olarak belirtebilirsiniz. Meta verileri ayarlamak için kaynaktaki `Metadata` koleksiyonuna ad-değer çiftleri ekleyin. Ardından, değerleri yazmak için aşağıdaki yöntemlerden birini çağırın:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

Meta veri adı/değer çiftleri geçerli HTTP başlıklardır ve HTTP üst bilgilerini yöneten tüm kısıtlamalara uymalıdır. Meta veri adları geçerli HTTP üst bilgisi adları ve geçerli C# tanımlayıcılar olmalıdır, yalnızca ASCII karakterler içerebilir ve büyük/küçük harfe duyarsız olarak değerlendirilmelidir. [Base64 kodlama](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) veya [URL-](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) ASCII olmayan karakterler içeren meta veri değerlerini kodla.

Meta verilerinizin adı, C# tanımlayıcıların adlandırma kurallarına uygun olmalıdır. Meta veri adları, oluşturulduklarında kullanılan durumu korur, ancak ayarlandığında veya okurken büyük/küçük harfe duyarsızdır. Bir kaynak için aynı adı kullanan iki veya daha fazla meta veri üstbilgisi gönderilirse Azure Blob depolama, 400 (Hatalı Istek) HTTP hata kodunu döndürür.

Aşağıdaki kod örneği bir blob üzerinde meta verileri ayarlar. Bir değer, koleksiyonun `Add` yöntemi kullanılarak ayarlanır. Diğer değer örtük anahtar/değer sözdizimi kullanılarak ayarlanır.

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

Meta verileri almak için blob veya `Metadata` kapsayıcınızda `FetchAttributes` veya `FetchAttributesAsync` yöntemini çağırın ve ardından aşağıdaki örnekte gösterildiği gibi değerleri okuyun.

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Blob özelliklerini ayarlama işlemi](/rest/api/storageservices/set-blob-properties)
- [Blob özelliklerini al işlemi](/rest/api/storageservices/get-blob-properties)
- [Blob meta veri işlemini ayarlama](/rest/api/storageservices/set-blob-metadata)
- [Blob meta verileri al işlemi](/rest/api/storageservices/set-blob-metadata)
