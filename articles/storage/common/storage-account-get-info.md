---
title: .NET-Azure depolama ile depolama hesabı türü ve SKU adı edinme
description: .NET istemci kitaplığını kullanarak Azure depolama hesabı türü ve SKU adı alma hakkında bilgi edinin.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/06/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.openlocfilehash: 0008d8fa9a33db95b0695ce998d4fc267d6222da
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68985356"
---
# <a name="get-storage-account-type-and-sku-name-with-net"></a>.NET ile depolama hesabı türü ve SKU adı alın

Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage/client)'nı kullanarak bir blob Için Azure depolama hesabı türü ve SKU adının nasıl alınacağı gösterilmektedir.

Hesap bilgileri, sürüm 2018-03-28 ' den başlayarak hizmet sürümlerinde kullanılabilir.

## <a name="about-account-type-and-sku-name"></a>Hesap türü ve SKU adı hakkında

**Hesap türü**: Geçerli hesap türleri şunlardır `BlobStorage` `BlockBlobStorage` `FileStorage`,,, ve`StorageV2`. `Storage` [Azure depolama hesabına genel bakış](storage-account-overview.md) , çeşitli depolama hesaplarının açıklamaları dahil olmak üzere daha fazla bilgi içerir.

**SKU adı**: Geçerli SKU adları `Premium_LRS` `Premium_ZRS` ,`Standard_LRS` ,,`Standard_ZRS`,, ,`Standard_RAGZRS`,ve içerir. `Standard_RAGRS` `Standard_GRS` `Standard_GZRS` SKU adları büyük/küçük harfe duyarlıdır ve [Skuname sınıfında](/dotnet/api/microsoft.azure.management.storage.models.skuname?view=azure-dotnet)dize alanlarıdır.

## <a name="retrieve-account-information"></a>Hesap bilgilerini al

Depolama hesabı türünü ve bir blob ile ilişkili SKU adını almak için, [Getaccountproperties](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountproperties?view=azure-dotnet) veya [GetAccountPropertiesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getaccountpropertiesasync?view=azure-dotnet) yöntemini çağırın.

Aşağıdaki kod örneği, salt okunurdur hesap özelliklerini alır ve görüntüler.

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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

[Azure Portal](https://portal.azure.com) ve Azure REST API aracılığıyla bir depolama hesabında gerçekleştirebileceğiniz diğer işlemler hakkında bilgi edinin.

- [Depolama hesabını yönetme](storage-account-manage.md)
- [Depolama hesabını yükseltme](storage-account-upgrade.md)
- [Hesap bilgilerini al işlemi (REST)](/rest/api/storageservices/get-account-information)
