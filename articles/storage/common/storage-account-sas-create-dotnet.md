---
title: .NET ile hesap SAS oluşturma
titleSuffix: Azure Storage
description: .NET istemci kitaplığını kullanarak hesap paylaşılan erişim imzası (SAS) oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/12/2020
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2918b845430a6fc6dc59eca7041c114fc9d06515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092219"
---
# <a name="create-an-account-sas-with-net"></a>.NET ile hesap SAS oluşturma

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Bu makalede, [.net Için Azure Storage istemci kitaplığı](/dotnet/api/overview/azure/storage)Ile hesap SAS oluşturmak üzere depolama hesabı anahtarının nasıl kullanılacağı gösterilmektedir.

## <a name="create-an-account-sas"></a>Hesap SAS’si oluşturma

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Hesap erişim anahtarı ile bir hesap SAS imzalanır. SAS imzalamak için kullanılan kimlik bilgisini oluşturmak için [Storagesharedkeycredential](/dotnet/api/azure.storage.storagesharedkeycredential) sınıfını kullanın. Ardından, yeni bir [Accountsasbuilder](/dotnet/api/azure.storage.sas.accountsasbuilder) nesnesi oluşturun ve SAS belirteç dizesini almak için [ToSasQueryParameters](/dotnet/api/azure.storage.sas.accountsasbuilder.tosasqueryparameters) çağırın.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetAccountSASToken":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Bir kapsayıcı için hesap SAS oluşturmak için [Cloudstorageaccount. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.getsharedaccesssignature) metodunu çağırın.

Aşağıdaki kod örneği, blob ve dosya hizmetleri için geçerli olan bir hesap SAS oluşturur ve hizmet düzeyi API 'Lerine erişmek için istemci izinleri okuma, yazma ve listeleme izinleri verir. Hesap SAS, protokolü HTTPS olarak kısıtlar, bu nedenle isteğin HTTPS ile yapılması gerekir. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```csharp
static string GetAccountSASToken()
{
    // To create the account SAS, you need to use Shared Key credentials. Modify for your account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<storage-account>;AccountKey=<account-key>";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

    // Create a new access policy for the account.
    SharedAccessAccountPolicy policy = new SharedAccessAccountPolicy()
        {
            Permissions = SharedAccessAccountPermissions.Read | SharedAccessAccountPermissions.Write | SharedAccessAccountPermissions.List,
            Services = SharedAccessAccountServices.Blob | SharedAccessAccountServices.File,
            ResourceTypes = SharedAccessAccountResourceTypes.Service,
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Protocols = SharedAccessProtocol.HttpsOnly
        };

    // Return the SAS token.
    return storageAccount.GetSharedAccessSignature(policy);
}
```

---

## <a name="use-an-account-sas-from-a-client"></a>İstemciden hesap SAS kullanma

Blob hizmeti için hizmet düzeyi API 'Lerine erişmek üzere hesap SAS 'yi kullanmak için, depolama hesabınız için SAS ve BLOB depolama uç noktasını kullanarak bir blob hizmeti istemci nesnesi oluşturun.

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_UseAccountSAS":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Bu kod parçacığında, `<storage-account>` yer tutucuyu depolama hesabınızın adıyla değiştirin.

```csharp
static void UseAccountSAS(string sasToken)
{
    // Create new storage credentials using the SAS token.
    StorageCredentials accountSAS = new StorageCredentials(sasToken);
    // Use these credentials and the account name to create a Blob service client.
    CloudStorageAccount accountWithSAS = new CloudStorageAccount(accountSAS, "<storage-account>", endpointSuffix: null, useHttps: true);
    CloudBlobClient blobClientWithSAS = accountWithSAS.CreateCloudBlobClient();

    // Now set the service properties for the Blob client created with the SAS.
    blobClientWithSAS.SetServiceProperties(new ServiceProperties()
    {
        HourMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        MinuteMetrics = new MetricsProperties()
        {
            MetricsLevel = MetricsLevel.ServiceAndApi,
            RetentionDays = 7,
            Version = "1.0"
        },
        Logging = new LoggingProperties()
        {
            LoggingOperations = LoggingOperations.All,
            RetentionDays = 14,
            Version = "1.0"
        }
    });

    // The permissions granted by the account SAS also permit you to retrieve service properties.
    ServiceProperties serviceProperties = blobClientWithSAS.GetServiceProperties();
    Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
    Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
    Console.WriteLine(serviceProperties.HourMetrics.Version);
}
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md)
- [Hesap SAS’si oluşturma](/rest/api/storageservices/create-account-sas)
