---
title: .NET ile depolama hesabı türü ve SKU adı alın
titleSuffix: Azure Storage
description: .NET istemci kitaplığını kullanarak Azure depolama hesabı türü ve SKU adı alma hakkında bilgi edinin.
services: storage
author: twooley
ms.author: twooley
ms.date: 11/12/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 26ce4a77e02e62a1a204529ce8652fbba3aab2c9
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277156"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>.NET ile depolama hesabı türü ve SKU adı alın

Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage)'nı kullanarak bir blob Için Azure depolama hesabı türü ve SKU adının nasıl alınacağı gösterilmektedir.

## <a name="about-account-type-and-sku-name"></a>Hesap türü ve SKU adı hakkında

**Hesap türü**: geçerli hesap türleri,,, `BlobStorage` `BlockBlobStorage` ve içerir `FileStorage` `Storage` `StorageV2` . [Azure depolama hesabına genel bakış](storage-account-overview.md) , çeşitli depolama hesaplarının açıklamaları dahil olmak üzere daha fazla bilgi içerir.

**SKU adı**: geçerli SKU adları,,,,, `Premium_LRS` `Premium_ZRS` `Standard_GRS` `Standard_GZRS` `Standard_LRS` `Standard_RAGRS` , `Standard_RAGZRS` , ve içerir `Standard_ZRS` . SKU adları büyük/küçük harfe duyarlıdır ve [Skuname sınıfında](/dotnet/api/microsoft.azure.management.storage.models.skuname)dize alanlarıdır.

## <a name="retrieve-account-information"></a>Hesap bilgilerini al

Aşağıdaki kod örneği, salt okunurdur hesap özelliklerini alır ve görüntüler.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Depolama hesabı türünü ve bir blob ile ilişkili SKU adını almak için, [Getaccountınfo](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfo) veya [Getaccounınfoasync](/dotnet/api/azure.storage.blobs.blobserviceclient.getaccountinfoasync) yöntemini çağırın.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Account.cs" id="Snippet_GetAccountInfo":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Depolama hesabı türünü ve bir blob ile ilişkili SKU adını almak için, [Getaccountproperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties) veya [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync) yöntemini çağırın.

```csharp
private static async Task GetAccountInfoAsync(CloudBlob blob)
{
    try
    {
        // Get the blob's storage account properties.
        AccountProperties acctProps = await blob.GetAccountPropertiesAsync();

        // Display the properties.
        Console.WriteLine("Account properties");
        Console.WriteLine("  AccountKind: {0}", acctProps.AccountKind);
        Console.WriteLine("      SkuName: {0}", acctProps.SkuName);
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

## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal](https://portal.azure.com) ve Azure REST API aracılığıyla bir depolama hesabında gerçekleştirebileceğiniz diğer işlemler hakkında bilgi edinin.

- [Hesap bilgilerini al işlemi (REST)](/rest/api/storageservices/get-account-information)
