---
title: .NET (Önizleme) ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma-Azure depolama
description: .NET istemci kitaplığını kullanarak Azure Storage 'da Azure Active Directory kimlik bilgilerini kullanarak bir Kullanıcı temsili SAS oluşturmayı öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: c75a13a20c1dbb222db69145e24838deb111fb66
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595222"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-net-preview"></a>.NET ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturma (Önizleme)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Bu makalede, .NET için Azure depolama istemci kitaplığı ile bir kapsayıcı veya blob için Kullanıcı temsili SAS oluşturmak üzere Azure Active Directory (Azure AD) kimlik bilgilerinin nasıl kullanılacağı gösterilmektedir.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Azure kimlik kitaplığı ile kimlik doğrulama (Önizleme)

.NET için Azure Identity istemci kitaplığı (Önizleme) bir güvenlik sorumlusunun kimliğini doğrular. Kodunuz Azure 'da çalışırken, güvenlik sorumlusu Azure kaynakları için yönetilen bir kimliktir.

Kodunuz geliştirme ortamında çalışırken, kimlik doğrulaması otomatik olarak işlenebilir veya kullandığınız araçlara bağlı olarak bir tarayıcı oturum açma işlemi gerektirebilir. Microsoft Visual Studio çoklu oturum açmayı (SSO) destekler, böylece etkin Azure AD Kullanıcı hesabı kimlik doğrulaması için otomatik olarak kullanılır. SSO hakkında daha fazla bilgi için bkz. [uygulamalarda çoklu oturum açma](../../active-directory/manage-apps/what-is-single-sign-on.md).

Diğer geliştirme araçları, bir Web tarayıcısı aracılığıyla oturum açmanız istenebilir. Geliştirme ortamından kimlik doğrulaması yapmak için bir hizmet sorumlusu da kullanabilirsiniz. Daha fazla bilgi için bkz. [portalda Azure uygulaması için kimlik oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md).

Kimlik doğrulamasından sonra Azure Identity istemci kitaplığı bir belirteç kimlik bilgisi alır. Bu belirteç kimlik bilgileri daha sonra Azure depolama 'ya karşı işlemleri gerçekleştirmek için oluşturduğunuz hizmet istemci nesnesinde kapsüllenir. Kitaplık, uygun belirteç kimlik bilgisini alarak bunu sorunsuz bir şekilde işler.

Azure Identity istemci kitaplığı hakkında daha fazla bilgi için bkz. [.net Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Verilere erişmek için RBAC rolleri atama

Bir Azure AD güvenlik sorumlusu blob verilerine erişmeyi denediğinde, bu güvenlik sorumlusunun kaynak için izinleri olması gerekir. Güvenlik sorumlusunun Azure 'da yönetilen bir kimlik veya geliştirme ortamında kod çalıştıran bir Azure AD Kullanıcı hesabı olup olmadığı, güvenlik sorumlusu Azure Storage 'daki blob verilerine erişim izni veren bir RBAC rolü atanmalıdır. RBAC aracılığıyla izin atama hakkında daha fazla bilgi için, [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)konusundaki **ERIŞIM hakları Için RBAC rolleri atama** başlıklı bölüme bakın.

## <a name="install-the-preview-packages"></a>Önizleme paketlerini yükler

Bu makaledeki örneklerde, [BLOB depolama Için Azure Storage istemci kitaplığı](https://www.nuget.org/packages/Azure.Storage.Blobs)'nın en son önizleme sürümü kullanılmaktadır. Önizleme paketini yüklemek için, NuGet paket yöneticisi konsolundan aşağıdaki komutu çalıştırın:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Bu makaledeki örneklerde, Azure AD kimlik bilgileriyle kimlik doğrulaması yapmak için [.net Için Azure Identity istemci kitaplığı](https://www.nuget.org/packages/Azure.Identity/) 'nın en son önizleme sürümü de kullanılır. Önizleme paketini yüklemek için, NuGet paket yöneticisi konsolundan aşağıdaki komutu çalıştırın:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="add-using-directives"></a>Using yönergeleri ekleme

Azure kimlik ve Azure depolama istemci kitaplıklarının önizleme sürümlerini kullanmak için aşağıdaki `using` yönergelerini kodunuza ekleyin.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

## <a name="get-an-authenticated-token-credential"></a>Kimliği doğrulanmış belirteç kimlik bilgisi al

Kodunuzun Azure depolama 'ya istekleri yetkilendirmek için kullanabileceği bir belirteç kimlik bilgisi almak için, [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfının bir örneğini oluşturun.

Aşağıdaki kod parçacığı kimliği doğrulanmış belirteç kimlik bilgisinin nasıl alınacağını ve BLOB depolaması için bir hizmet istemcisi oluşturmak için nasıl kullanılacağını gösterir:

```csharp
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

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
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStart);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="create-the-sas-token"></a>SAS belirteci oluşturma

Aşağıdaki kod parçacığı, yeni bir [Blobsasbuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) oluşturma ve Kullanıcı temsili SAS için parametreler sağlama gösterilmektedir. Kod parçacığı daha sonra SAS belirteç dizesini almak için [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) çağırır. Son olarak, kod kaynak adresi ve SAS belirteci dahil olmak üzere tüm URI 'yi oluşturur.

```csharp
BlobSasBuilder builder = new BlobSasBuilder()
{
    ContainerName = containerName,
    BlobName = blobName,
    Permissions = "r",
    Resource = "b",
    StartTime = DateTimeOffset.UtcNow,
    ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
};

string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

UriBuilder fullUri = new UriBuilder()
{
    Scheme = "https",
    Host = string.Format("{0}.blob.core.windows.net", accountName),
    Path = string.Format("{0}/{1}", containerName, blobName),
    Query = sasToken
};
```

## <a name="example-get-a-user-delegation-sas"></a>Örnek: Kullanıcı temsilciliğini al SAS

Aşağıdaki örnek yöntemi, güvenlik sorumlusunun kimliğini doğrulamak ve Kullanıcı temsili SAS oluşturmak için tüm kodu gösterir:

```csharp
async static Task<Uri> GetUserDelegationSasBlob(string accountName, string containerName, string blobName)
{
    // Construct the blob endpoint from the account name.
    string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

    // Create a new Blob service client with Azure AD credentials.  
    BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint), 
                                                            new DefaultAzureCredential());

    // Get a user delegation key for the Blob service that's valid for seven days.
    // Use the key to generate any number of shared access signatures over the lifetime of the key.
    UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                       DateTimeOffset.UtcNow.AddDays(7));

    // Read the key's properties.
    Console.WriteLine("User delegation key properties:");
    Console.WriteLine("Key signed start: {0}", key.SignedStart);
    Console.WriteLine("Key signed expiry: {0}", key.SignedExpiry);
    Console.WriteLine("Key signed object ID: {0}", key.SignedOid);
    Console.WriteLine("Key signed tenant ID: {0}", key.SignedTid);
    Console.WriteLine("Key signed service: {0}", key.SignedService);
    Console.WriteLine("Key signed version: {0}", key.SignedVersion);

    // Create a SAS token that's valid a short interval.
    BlobSasBuilder sasBuilder = new BlobSasBuilder()
    {
        ContainerName = containerName,
        BlobName = blobName,
        Permissions = "r",
        Resource = "b",
        StartTime = DateTimeOffset.UtcNow,
        ExpiryTime = DateTimeOffset.UtcNow.AddMinutes(5)
    };

    // Use the key to get the SAS token.
    string sasToken = sasBuilder.ToSasQueryParameters(key, accountName).ToString();

    // Construct the full URI, including the SAS token.
    UriBuilder fullUri = new UriBuilder()
    {
        Scheme = "https",
        Host = string.Format("{0}.blob.core.windows.net", accountName),
        Path = string.Format("{0}/{1}", containerName, blobName),
        Query = sasToken
    };

    Console.WriteLine("User delegation SAS URI: {0}", fullUri);
    return fullUri.Uri;
}
```

## <a name="example-read-a-blob-with-a-user-delegation-sas"></a>Örnek: Kullanıcı temsilciliğini içeren bir blobu okuma

Aşağıdaki örnek, bir önceki örnekte oluşturulan kullanıcı temsili SAS 'ı sanal bir istemci uygulamasından sınar. SAS geçerliyse, istemci uygulama Blobun içeriğini okuyabilir. SAS geçersizse, örneğin süresi dolmuşsa Azure Storage 403 (yasak) hata kodunu döndürür.

```csharp
private static async Task ReadBlobWithSasAsync(Uri sasUri)
{
    // Try performing blob operations using the SAS provided.

    // Create a blob client object for blob operations.
    BlobClient blobClient = new BlobClient(sasUri, null);

    // Download and read the contents of the blob.
    try
    {
        // Download blob contents to a stream and read the stream.
        BlobDownloadInfo blobDownloadInfo = await blobClient.DownloadAsync();
        using (StreamReader reader = new StreamReader(blobDownloadInfo.Content, true))
        {
            string line;
            while ((line = reader.ReadLine()) != null)
            {
                Console.WriteLine(line);
            }
        }

        Console.WriteLine();
        Console.WriteLine("Read operation succeeded for SAS {0}", sasUri);
        Console.WriteLine();
    }
    catch (StorageRequestFailedException e)
    {
        // Check for a 403 (Forbidden) error. If the SAS is invalid, 
        // Azure Storage returns this error.
        if (e.Status == 403)
        {
            Console.WriteLine("Read operation failed for SAS {0}", sasUri);
            Console.WriteLine("Additional error information: " + e.Message);
            Console.WriteLine();
        }
        else
        {
            Console.WriteLine(e.Message);
            Console.ReadLine();
            throw;
        }
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Ayrıca bkz.

- [Kullanıcı temsilciyi anahtar işlemi al](/rest/api/storageservices/get-user-delegation-key)
- [Kullanıcı temsilciliğini oluşturma SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
