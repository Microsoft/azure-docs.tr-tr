---
title: Bir kapsayıcı, dizin veya blob için Kullanıcı temsili SAS oluşturmak için .NET kullanın
titleSuffix: Azure Storage
description: Azure depolama için .NET istemci kitaplığı 'nı kullanarak Azure Active Directory kimlik bilgileriyle Kullanıcı temsili SAS oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 13491735f73cb1696f3c36f3434cc781a1e2b739
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526814"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>.NET ile bir kapsayıcı, dizin veya blob için Kullanıcı temsili SAS oluşturma

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Bu makalede, .NET için Azure depolama istemci kitaplığı ile bir kapsayıcı, dizin veya blob için Kullanıcı temsili SAS oluşturmak üzere Azure Active Directory (Azure AD) kimlik bilgilerinin nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Verilere erişmek için Azure rolleri atama

Bir Azure AD güvenlik sorumlusu blob verilerine erişmeyi denediğinde, bu güvenlik sorumlusunun kaynak için izinleri olması gerekir. Güvenlik sorumlusunun Azure 'da yönetilen bir kimlik veya geliştirme ortamında kod çalıştıran bir Azure AD Kullanıcı hesabı olup olmadığı, güvenlik sorumlusu, Azure depolama 'daki blob verilerine erişim izni veren bir Azure rolü atanmalıdır. Azure RBAC aracılığıyla izin atama hakkında daha fazla bilgi için, [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)konusundaki **erişim hakları Için Azure rolleri atama** başlıklı bölüme bakın.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Azure depolama 'dan Azure kimlik istemci kitaplığı ile kimlik doğrulama hakkında daha fazla bilgi edinmek için bkz. Azure **kimlik kitaplığıyla kimlik doğrulama** başlıklı Bölüm [Azure Active Directory ve Azure kaynakları için Yönetilen kimlikler ile blob 'lara ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).

## <a name="get-an-authenticated-token-credential"></a>Kimliği doğrulanmış belirteç kimlik bilgisi al

Kodunuzun Azure depolama 'ya istekleri yetkilendirmek için kullanabileceği bir belirteç kimlik bilgisi almak için, [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfının bir örneğini oluşturun.

Aşağıdaki kod parçacığı kimliği doğrulanmış belirteç kimlik bilgisinin nasıl alınacağını ve BLOB depolaması için bir hizmet istemcisi oluşturmak için nasıl kullanılacağını gösterir:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Kullanıcı temsili anahtarını al

Her SAS bir anahtarla imzalanır. Bir Kullanıcı temsili SAS oluşturmak için, önce SAS imzalamak için kullanılan bir Kullanıcı temsili anahtarı istemeniz gerekir. Kullanıcı temsili anahtarı, Azure AD kimlik bilgilerinizi temel aldığından, bir hizmet SAS 'sini veya bir hesap SAS 'sini imzalamak için kullanılan hesap anahtarına benzer. İstemci bir OAuth 2,0 belirteci kullanarak bir Kullanıcı temsili anahtarı istediğinde, Azure depolama Kullanıcı adına Kullanıcı temsili anahtarını döndürür.

Kullanıcı temsilciliğini aldıktan sonra, anahtarın ömrü boyunca herhangi bir sayıda kullanıcı temsili paylaşılan erişim imzası oluşturmak için bu anahtarı kullanabilirsiniz. Kullanıcı temsili anahtarı, bunu elde etmek için kullanılan OAuth 2,0 belirtecinden bağımsızdır, bu nedenle anahtar hala geçerli olduğu sürece belirtecin yenilenmesi gerekmez. Anahtarın 7 güne kadar bir dönem için geçerli olduğunu belirtebilirsiniz.

Kullanıcı temsili anahtarını istemek için aşağıdaki yöntemlerden birini kullanın:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

Aşağıdaki kod parçacığı, Kullanıcı temsili anahtarını alır ve özelliklerini Yazar:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Blob için Kullanıcı temsili SAS alın

Aşağıdaki kod örneği, güvenlik sorumlusunun kimliğini doğrulamak ve bir blob için Kullanıcı temsili SAS oluşturmak için tüm kodu gösterir:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

Aşağıdaki örnek, bir önceki örnekte oluşturulan kullanıcı temsili SAS 'ı sanal bir istemci uygulamasından sınar. SAS geçerliyse, istemci uygulama Blobun içeriğini okuyabilir. SAS geçersizse, örneğin süresi dolmuşsa Azure Storage 403 (yasak) hata kodunu döndürür.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Bir kapsayıcı için Kullanıcı temsili SAS alın

Aşağıdaki kod örneği bir kapsayıcı için Kullanıcı temsili SAS oluşturmayı göstermektedir:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

Aşağıdaki örnek, bir önceki örnekte oluşturulan kullanıcı temsili SAS 'ı sanal bir istemci uygulamasından sınar. SAS geçerliyse, istemci uygulama Blobun içeriğini okuyabilir. SAS geçersizse, örneğin süresi dolmuşsa Azure Storage 403 (yasak) hata kodunu döndürür.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Bir dizin için Kullanıcı temsili SAS alın

Aşağıdaki kod örneğinde, depolama hesabı için hiyerarşik bir ad alanı etkinleştirildiğinde bir dizin için Kullanıcı temsili SAS oluşturma işlemi gösterilmektedir:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

Aşağıdaki örnek, bir önceki örnekte oluşturulan kullanıcı temsili SAS 'ı sanal bir istemci uygulamasından sınar. SAS geçerliyse, istemci uygulaması bu dizinin dosya yollarını listeleyebilir. SAS geçersizse, örneğin süresi dolmuşsa Azure Storage 403 (yasak) hata kodunu döndürür.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Paylaşılan erişim imzalarını (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md)
- [Kullanıcı temsilciyi anahtar işlemi al](/rest/api/storageservices/get-user-delegation-key)
- [Kullanıcı temsilciliğini oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
