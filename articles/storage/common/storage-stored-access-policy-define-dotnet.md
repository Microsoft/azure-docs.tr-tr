---
title: .NET ile depolanan bir erişim ilkesi oluşturma
titleSuffix: Azure Storage
description: Depolanan bir erişim ilkesi oluşturmak için Azure Storage ve .NET kullanın. Sunucusunda hizmet düzeyi paylaşılan erişim imzaları üzerinde ek denetim düzeyleri yapın.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89010758"
---
# <a name="create-a-stored-access-policy-with-net"></a>.NET ile depolanan bir erişim ilkesi oluşturma

Depolanan erişim ilkesi, sunucu tarafında hizmet düzeyi paylaşılan erişim imzaları (SAS) üzerinde ek bir denetim düzeyi sağlar. Depolanan erişim ilkesini tanımlama, paylaşılan erişim imzalarını gruplamak ve ilke tarafından bağlanan paylaşılan erişim imzaları için ek kısıtlamalar sağlamak için kullanılır. Bir SAS için başlangıç zamanını, süre sonu zamanını veya izinleri değiştirmek ya da verildikten sonra iptal etmek için bir saklı erişim ilkesi kullanabilirsiniz.
  
Aşağıdaki Azure depolama kaynakları, depolanan erişim ilkelerini destekler:  
  
- Blob kapsayıcıları  
- Dosya paylaşımları  
- Kuyruklar  
- Tablolar  
  
> [!NOTE]
> Kapsayıcıda depolanan bir erişim ilkesi, kapsayıcının kendisine veya içerdiği bloblara izin veren bir paylaşılan erişim imzası ile ilişkilendirilebilir. Benzer şekilde, bir dosya paylaşımında depolanan erişim ilkesi, paylaşım için izinleri veren paylaşılan erişim imzasıyla veya içerdiği dosyalar ile ilişkilendirilebilir.  
>
> Depolanan erişim ilkeleri yalnızca bir hizmet SAS için desteklenir. Saklı erişim ilkeleri, hesap SAS veya Kullanıcı temsili SAS için desteklenmez.  

Saklı erişim ilkeleri hakkında daha fazla bilgi için bkz. [depolanan erişim Ilkesi tanımlama](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Depolanan erişim ilkesi oluşturma

Depolanan bir erişim ilkesi oluşturmak için temeldeki REST işlemi [kapsayıcı ACL 'si olarak ayarlanır](/rest/api/storageservices/set-container-acl). Bir bağlantı dizesindeki hesap erişim anahtarlarını kullanarak, paylaşılan anahtar aracılığıyla saklı bir erişim ilkesi oluşturma işlemini yetkilendirmelisiniz. Azure AD kimlik bilgileriyle **set Container ACL 'si** işlemini yetkilendirme desteklenmiyor. Daha fazla bilgi için bkz. [BLOB ve kuyruk verisi işlemlerini çağırma izinleri](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

Aşağıdaki kod örnekleri bir kapsayıcıda depolanan erişim ilkesi oluşturur. Kapsayıcıda veya Bloblarında bir hizmet sa 'Sı kısıtlamalarını belirtmek için erişim ilkesini kullanabilirsiniz.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Azure depolama için .NET istemci kitaplığı sürüm 12 ' ye sahip bir kapsayıcıda saklı erişim ilkesi oluşturmak için aşağıdaki yöntemlerden birini çağırın:

- [BlobContainerClient. SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient. SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

Aşağıdaki örnek, bir gün için geçerli olan ve okuma/yazma izinleri veren bir depolanmış erişim ilkesi oluşturur:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

Azure depolama için .NET istemci kitaplığı sürüm 12 ' ye sahip bir kapsayıcıda saklı erişim ilkesi oluşturmak için aşağıdaki yöntemlerden birini çağırın:

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

Aşağıdaki örnek, bir gün için geçerli olan ve okuma, yazma ve listeleme izinleri veren bir depolanmış erişim ilkesi oluşturur:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Ayrıca bkz.

- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md)
- [Depolanan erişim ilkesi tanımlama](/rest/api/storageservices/define-stored-access-policy)
- [Azure depolama bağlantı dizelerini yapılandırma](storage-configure-connection-string.md)
