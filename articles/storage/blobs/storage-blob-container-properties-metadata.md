---
title: .NET-Azure depolama ile bir blob kapsayıcısı için özellikleri ve meta verileri yönetme
description: .NET istemci kitaplığı 'nı kullanarak, sistem özelliklerini ayarlama ve alma ve Azure Depolama hesabınızdaki blob kapsayıcılarında özel meta verileri depolama hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: tamram
ms.openlocfilehash: d63c78fedb8dbd48655d36fecc3544fd512072e3
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673370"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>.NET ile kapsayıcı özelliklerini ve meta verileri yönetme

Blob kapsayıcıları, içerdikleri verilere ek olarak sistem özelliklerini ve Kullanıcı tanımlı meta verileri destekler. Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage/client)ile sistem özelliklerinin ve Kullanıcı tanımlı meta verilerin nasıl yönetileceği gösterilmektedir.

## <a name="about-properties-and-metadata"></a>Özellikler ve meta veriler hakkında

- **Sistem Özellikleri**: Her blob depolama kaynağında sistem özellikleri var. Bazıları okunabilir veya ayarlanabilir, diğerleri salt okunurdur. Kapakların altında bazı sistem özellikleri bazı standart HTTP üst bilgilerine karşılık gelir. .NET için Azure depolama istemci kitaplığı, bu özellikleri sizin için korur.

- **Kullanıcı tanımlı meta veriler**: Kullanıcı tanımlı meta veriler, bir BLOB depolama kaynağı için belirlediğiniz bir veya daha fazla ad-değer çiftinden oluşur. Kaynak ile ek değerleri depolamak için meta verileri kullanabilirsiniz. Meta veri değerleri yalnızca kendi amacınıza yöneliktir ve kaynağın nasıl davranacağını etkilemez.

BLOB depolama kaynağı için özellik ve meta veri değerlerinin alınması, iki adımlı bir işlemdir. Bu değerleri okuyabilmeniz için, **Fetchattributes** veya **Fetchattributesasync** yöntemini çağırarak açıkça bunları almanız gerekir. Bu kuralın özel durumu **var ve mevcut** **tsasync** yöntemlerinin, kapsamakta olan uygun **fetchattributes** metodunu çağırmasını sağlar. Bu yöntemlerden birini çağırdığınızda, **Fetchattributes**' i de çağırmanız gerekmez.

> [!IMPORTANT]
> Bir depolama kaynağı için özellik veya meta veri değerlerinin doldurulmadığını fark ederseniz, kodunuzun **Fetchattributes** veya **Fetchattributesasync** yöntemini çağırmadığını kontrol edin.

Meta veri adı/değer çiftleri geçerli HTTP başlıklardır ve bu nedenle HTTP üst bilgilerini yöneten tüm kısıtlamalara uymalıdır. Meta veri adları geçerli HTTP üst bilgisi adları ve geçerli C# tanımlayıcılar olmalıdır, yalnızca ASCII karakterler içerebilir ve büyük/küçük harfe duyarsız olarak değerlendirilmelidir. ASCII olmayan karakterler içeren meta veri değerleri Base64 kodlamalı veya URL kodlu olmalıdır.

## <a name="retrieve-container-properties"></a>Kapsayıcı özelliklerini al

Kapsayıcı özelliklerini almak için aşağıdaki yöntemlerden birini çağırın:

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

Aşağıdaki kod örneği bir kapsayıcının sistem özelliklerini getirir ve bazı özellik değerlerini konsol penceresine yazar:

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

Meta verileri bir blob veya kapsayıcı kaynağında bir veya daha fazla ad-değer çifti olarak belirtebilirsiniz. Meta verileri ayarlamak için kaynaktaki **meta veri** koleksiyonuna ad-değer çiftleri ekleyin ve ardından değerleri yazmak için aşağıdaki yöntemlerden birini çağırın:

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

Meta verilerinizin adı, C# tanımlayıcıların adlandırma kurallarına uygun olmalıdır. Meta veri adları, oluşturulduğu durumu korur, ancak ayarlandığında veya okurken büyük/küçük harf duyarsız olur. Bir kaynak için aynı ada sahip iki veya daha fazla meta veri üst bilgisi gönderilirse, BLOB depolama HTTP hata kodu 400 (Hatalı Istek) döndürür.

Aşağıdaki kod örneği bir kapsayıcıda meta verileri ayarlar. Bir değer, koleksiyonun **Add** yöntemi kullanılarak ayarlanır. Diğer değer örtük anahtar/değer sözdizimi kullanılarak ayarlanır. Her ikisi de geçerlidir.

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

Meta verileri almak için blob veya kapsayıcınızda **Fetchattributes** veya **Fetchattributesasync** yöntemini çağırın ve ardından aşağıdaki örnekte gösterildiği gibi değerleri okuyun.

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
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

- [Kapsayıcı özelliklerini al işlemi](/rest/api/storageservices/get-container-properties)
- [Kapsayıcı meta verileri ayarlama işlemi](/rest/api/storageservices/set-container-metadata)
- [Kapsayıcı meta verilerini al işlemi](/rest/api/storageservices/set-container-metadata)
