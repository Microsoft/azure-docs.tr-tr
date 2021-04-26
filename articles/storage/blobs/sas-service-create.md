---
title: Bir kapsayıcı veya blob için hizmet SAS oluşturma
titleSuffix: Azure Storage
description: Azure Blob depolama istemci kitaplığını kullanarak bir kapsayıcı veya blob için hizmet paylaşılan erişim imzası (SAS) oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 7bbe19de666fb167297de89e85bf302186a9145e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105024889"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Bir kapsayıcı veya blob için hizmet SAS oluşturma

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Bu makalede, BLOB depolama için Azure depolama istemci kitaplığı ile bir kapsayıcı veya blob için hizmet SAS oluşturmak üzere depolama hesabı anahtarının nasıl kullanılacağı gösterilmektedir.

## <a name="create-a-service-sas-for-a-blob-container"></a>Blob kapsayıcısı için bir hizmet SAS oluşturma

Aşağıdaki kod örneği bir kapsayıcı için SAS oluşturur. Varolan bir depolanmış erişim ilkesinin adı sağlanmışsa, bu ilke SAS ile ilişkilendirilir. Depolanmış erişim ilkesi sağlanmazsa, kod kapsayıcıda bir geçici SAS oluşturur.

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Bir hizmet SAS, hesap erişim anahtarıyla imzalanır. SAS imzalamak için kullanılan kimlik bilgisini oluşturmak için [Storagesharedkeycredential](/dotnet/api/azure.storage.storagesharedkeycredential) sınıfını kullanın. Ardından, yeni bir [Blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) nesnesi oluşturun ve SAS belirteç dizesini almak için [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) çağırın.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Bir kapsayıcı için bir hizmet SAS oluşturmak için [Cloudblobcontainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) metodunu çağırın.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Bir hizmet SAS, hesap erişim anahtarıyla imzalanır. SAS imzalamak için kullanılan kimlik bilgisini oluşturmak için [Storagesharedkeycredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) sınıfını kullanın. Sonra, SAS belirteç dizesini almak için gerekli parametreleri sağlayan [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) işlevini çağırın.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_ContainerSAS":::

---

## <a name="create-a-service-sas-for-a-blob"></a>Blob için hizmet SAS oluşturma

Aşağıdaki kod örneği bir blob üzerinde SAS oluşturur. Varolan bir depolanmış erişim ilkesinin adı sağlanmışsa, bu ilke SAS ile ilişkilendirilir. Depolanan erişim ilkesi sağlanmazsa, kod blob üzerinde bir geçici SAS oluşturur.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Bir hizmet SAS, hesap erişim anahtarıyla imzalanır. SAS imzalamak için kullanılan kimlik bilgisini oluşturmak için [Storagesharedkeycredential](/dotnet/api/azure.storage.storagesharedkeycredential) sınıfını kullanın. Ardından, yeni bir [Blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) nesnesi oluşturun ve SAS belirteç dizesini almak için [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) çağırın.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Blob için bir hizmet SAS oluşturmak için [Cloudblob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) metodunu çağırın.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Blob için bir hizmet SAS oluşturmak için [Cloudblob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) metodunu çağırın.

Blob için bir hizmet SAS oluşturmak için, gerekli parametreleri sağlayan [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) işlevini çağırın.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_BlobSAS":::

---

## <a name="create-a-service-sas-for-a-directory"></a>Bir dizin için hizmet SAS oluşturma

Hiyerarşik bir ad alanı etkin olan bir depolama hesabında, bir dizin için hizmet SAS oluşturabilirsiniz. Hizmet SAS oluşturmak için, [Azure. Storage. Files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) paketinin 12.5.0 veya sonraki bir sürümünü yüklediğinizden emin olun.

Aşağıdaki örnekte, .NET için V12 istemci kitaplığı ile bir dizin için nasıl hizmet SAS oluşturulacağı gösterilmektedir:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Sonraki adımlar

- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md)
- [Hizmet SAS’si oluşturma](/rest/api/storageservices/create-service-sas)
