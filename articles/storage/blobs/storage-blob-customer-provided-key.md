---
title: .NET-Azure depolama ile BLOB depolama için istekte bir müşteri tarafından sağlanmış anahtar belirtin
description: .NET kullanarak BLOB depolama için bir istekte müşteri tarafından sağlanmış anahtar belirtmeyi öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693519"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>.NET ile BLOB depolama için istekte müşteri tarafından sağlanmış bir anahtar belirtin

Azure Blob depolamada istek yapan istemcilerin, tek bir istekte AES-256 şifreleme anahtarı sağlama seçeneği vardır. İstek üzerine şifreleme anahtarı dahil olmak üzere, BLOB depolama işlemleri için şifreleme ayarları üzerinde ayrıntılı denetim sağlar. Müşteri tarafından sunulan anahtarlar, Azure Key Vault veya başka bir anahtar deposunda depolanabilir.

Bu makalede, .NET ile bir istekte müşteri tarafından sağlanmış bir anahtarın nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure Identity istemci kitaplığı ile kimlik doğrulama hakkında daha fazla bilgi edinmek için bkz. [.net Için Azure kimlik istemci kitaplığı](/dotnet/api/overview/azure/identity-readme).

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Bir bloba yazmak için müşteri tarafından sağlanmış bir anahtar kullanın

Aşağıdaki örnek, BLOB depolama için V12 istemci kitaplığıyla blob yüklerken bir AES-256 anahtarı sağlar. Örnek, Azure AD ile yazma isteğini yetkilendirmek için [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) nesnesini kullanır, ancak Isteği paylaşılan anahtar kimlik bilgileriyle da yetkilendirebilirsiniz.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [BLOB depolama için istekte bir şifreleme anahtarı sağlayın](encryption-customer-provided-keys.md)
- [Bekleyen veri için Azure Depolama şifrelemesi](../common/storage-service-encryption.md)
