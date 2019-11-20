---
title: .NET-Azure depolama ile depolanan bir erişim ilkesi tanımlama
description: .NET istemci kitaplığını kullanarak depolanan bir erişim ilkesini nasıl tanımlayacağınızı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/06/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 272d676d0a5a55262b1c68d0bae9a9ab229df72c
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990747"
---
# <a name="define-a-stored-access-policy-with-net"></a>.NET ile depolanan bir erişim ilkesi tanımlama

Depolanan erişim ilkesi, sunucu tarafında hizmet düzeyi paylaşılan erişim imzaları (SAS) üzerinde ek bir denetim düzeyi sağlar. Depolanan erişim ilkesini tanımlama, paylaşılan erişim imzalarını gruplamak ve ilke tarafından bağlanan paylaşılan erişim imzaları için ek kısıtlamalar sağlamak için kullanılır. Bir SAS için başlangıç zamanını, süre sonu zamanını veya izinleri değiştirmek ya da verildikten sonra iptal etmek için bir saklı erişim ilkesi kullanabilirsiniz.
  
 Aşağıdaki depolama kaynakları, depolanan erişim ilkelerini destekler:  
  
- Blob kapsayıcıları  
- Dosya paylaşımları  
- Sıralar  
- Tablolar  
  
> [!NOTE]
> Kapsayıcıda depolanan bir erişim ilkesi, kapsayıcının kendisine veya içerdiği bloblara izin veren bir paylaşılan erişim imzası ile ilişkilendirilebilir. Benzer şekilde, bir dosya paylaşımında depolanan erişim ilkesi, paylaşım için izinleri veren paylaşılan erişim imzasıyla veya içerdiği dosyalar ile ilişkilendirilebilir.  
>
> Depolanan erişim ilkeleri yalnızca bir hizmet SAS için desteklenir. Saklı erişim ilkeleri, hesap SAS veya Kullanıcı temsili SAS için desteklenmez.  

## <a name="create-a-stored-access-policy"></a>Depolanan erişim ilkesi oluşturma

Aşağıdaki kod, bir kapsayıcıda depolanan erişim ilkesi oluşturur. Kapsayıcıda veya Bloblarında bir hizmet sa 'Sı kısıtlamalarını belirtmek için erişim ilkesini kullanabilirsiniz.

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
            SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Create | SharedAccessBlobPermissions.Delete
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

## <a name="see-also"></a>Ayrıca bkz.

- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](storage-sas-overview.md)
- [Depolanan erişim ilkesi tanımlama](/rest/api/storageservices/define-stored-access-policy)

