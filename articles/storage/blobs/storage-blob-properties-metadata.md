---
title: .NET-Azure depolama ile bir blob için özellikleri ve meta verileri yönetme
description: .NET istemci kitaplığını kullanarak Azure Storage hesabınızdaki bloblarda sistem özelliklerini ayarlama ve alma ve özel meta verileri depolama hakkında bilgi edinin.
services: storage
author: twooley
ms.author: twooley
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 24ec646c2928570c67a7f71481f2ca0191f1c8b9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280216"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>.NET ile blob özelliklerini ve meta verileri yönetme

Blob 'lar, içerdikleri verilere ek olarak sistem özelliklerini ve Kullanıcı tanımlı meta verileri destekler. Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage)ile sistem özelliklerinin ve Kullanıcı tanımlı meta verilerin nasıl yönetileceği gösterilmektedir.

## <a name="about-properties-and-metadata"></a>Özellikler ve meta veriler hakkında

- **Sistem Özellikleri**: her BLOB depolama kaynağında sistem özellikleri bulunur. Bazıları okunabilir veya ayarlanabilir, diğerleri salt okunurdur. Kapakların altında bazı sistem özellikleri bazı standart HTTP üst bilgilerine karşılık gelir. .NET için Azure depolama istemci kitaplığı, bu özellikleri sizin için korur.

- **Kullanıcı tanımlı meta veriler**: Kullanıcı tanımlı meta veriler, bir BLOB depolama kaynağı için belirlediğiniz bir veya daha fazla ad-değer çiftinden oluşur. Kaynak ile ek değerleri depolamak için meta verileri kullanabilirsiniz. Meta veri değerleri yalnızca kendi amacınıza yöneliktir ve kaynağın nasıl davranacağını etkilemez.

> [!NOTE]
> Blob dizini etiketleri Ayrıca, bir Azure Blob depolama kaynağıyla birlikte rastgele Kullanıcı tanımlı anahtar/değer özniteliklerini depolamanıza olanak sağlar. Meta verilere benzer olsa da, yalnızca blob dizin etiketleri otomatik olarak dizinlenir ve yerel blob hizmeti tarafından aranabilir hale getirilir. Azure Search gibi ayrı bir hizmet kullanmadığınız sürece meta veriler dizinlenemez ve sorgulanamaz.
>
> Bu özellik hakkında daha fazla bilgi edinmek için bkz. [blob dizini (Önizleme) Ile Azure Blob depolama üzerinde verileri yönetme ve bulma](storage-manage-find-blobs.md).

## <a name="set-and-retrieve-properties"></a>Özellikleri ayarlama ve alma

Aşağıdaki kod örneği `ContentType` `ContentLanguage` bir Blobun ve sistem özelliklerini ayarlar.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Blob üzerinde özellikleri ayarlamak için, [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) veya [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync)çağırın. Açıkça ayarlanmamış Özellikler temizlenir. Aşağıdaki kod örneği, önce blob üzerindeki mevcut özellikleri alır, ardından bunları güncelleştirilmemiş üst bilgileri doldurmak için kullanır.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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
---

Aşağıdaki kod örneği, bir Blobun sistem özelliklerini alır ve bazı değerleri görüntüler.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

BLOB depolama kaynağı için meta verilerin ve özellik değerlerinin alınması, iki adımlı bir işlemdir. Bu değerleri okuyabilmeniz için önce veya metodunu çağırarak açıkça almanız gerekir `FetchAttributes` `FetchAttributesAsync` . Bu kuralın istisnası, `Exists` ve `ExistsAsync` yöntemlerinin, kapsamakta olan uygun yöntemi çağırmasını sağlar `FetchAttributes` . Bu yöntemlerden birini çağırdığınızda, ' ı da çağırmanız gerekmez `FetchAttributes` .

> [!IMPORTANT]
> Bir depolama kaynağı için özellik veya meta veri değerlerinin doldurulmadığını fark ederseniz, kodunuzun veya yöntemini çağırmadığını kontrol edin `FetchAttributes` `FetchAttributesAsync` .

Blob özelliklerini almak için, `FetchAttributes` `FetchAttributesAsync` özelliği doldurmak üzere bloba veya yöntemini çağırın `Properties` .

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
---

## <a name="set-and-retrieve-metadata"></a>Meta verileri ayarlama ve alma

Meta verileri bir blob veya kapsayıcı kaynağında bir veya daha fazla ad-değer çifti olarak belirtebilirsiniz. Meta verileri ayarlamak için kaynaktaki koleksiyona ad-değer çiftleri ekleyin `Metadata` . Ardından, değerleri yazmak için aşağıdaki yöntemlerden birini çağırın:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [SetMetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

Meta veri adı/değer çiftleri geçerli HTTP başlıklardır ve HTTP üst bilgilerini yöneten tüm kısıtlamalara uymalıdır. Meta veri adları geçerli HTTP üst bilgisi adları ve geçerli C# tanımlayıcıları olmalıdır, yalnızca ASCII karakterler içerebilir ve büyük/küçük harfe duyarsız olarak değerlendirilmelidir. [Base64 kodlama](/dotnet/api/system.convert.tobase64string) veya [URL-](/dotnet/api/system.web.httputility.urlencode) ASCII olmayan karakterler içeren meta veri değerlerini kodla.

Meta verilerinizin adı, C# tanımlayıcıları için adlandırma kurallarına uygun olmalıdır. Meta veri adları, oluşturulduklarında kullanılan durumu korur, ancak ayarlandığında veya okurken büyük/küçük harfe duyarsızdır. Bir kaynak için aynı adı kullanan iki veya daha fazla meta veri üstbilgisi gönderilirse Azure Blob depolama, 400 (Hatalı Istek) HTTP hata kodunu döndürür.

Aşağıdaki kod örneği bir blob üzerinde meta verileri ayarlar. Bir değer, koleksiyonun yöntemi kullanılarak ayarlanır `Add` . Diğer değer örtük anahtar/değer sözdizimi kullanılarak ayarlanır.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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
---

Aşağıdaki kod örneği bir Blobun meta verileri okur.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Meta verileri almak için, blob veya kapsayıcınızda [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) veya [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) yöntemini çağırıp [meta veri](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) koleksiyonunu doldurun, sonra aşağıdaki örnekte gösterildiği gibi değerleri okuyun. **GetProperties** yöntemleri, tek bir çağrıda blob özelliklerini ve meta verilerini alır. Bu, [BLOB özelliklerini almak](/rest/api/storageservices/get-blob-properties) ve [BLOB meta verilerini almak](/rest/api/storageservices/get-blob-metadata)IÇIN ayrı çağrılar gerektiren REST API 'lerinden farklıdır.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Meta verileri almak için, `FetchAttributes` `FetchAttributesAsync` Blobu veya kapsayıcıdaki yöntemi, koleksiyonu doldurmak için çağırın `Metadata` , sonra aşağıdaki örnekte gösterildiği gibi değerleri okuyun.

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
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Blob özelliklerini ayarlama işlemi](/rest/api/storageservices/set-blob-properties)
- [Blob özelliklerini al işlemi](/rest/api/storageservices/get-blob-properties)
- [Blob meta veri işlemini ayarlama](/rest/api/storageservices/set-blob-metadata)
- [Blob meta verileri al işlemi](/rest/api/storageservices/get-blob-metadata)