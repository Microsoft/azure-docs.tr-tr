---
title: Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile bloblara ve kuyruklara erişim yetkisi verme-Azure depolama
description: Azure Blob ve kuyruk depolama, Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile kaynaklara erişim yetkisi verme desteği sağlar. Azure sanal makinelerinde, işlev uygulamalarında, sanal makine ölçek kümelerinde ve diğer uygulamalarda çalışan uygulamalardan bloblara ve kuyruklara erişim yetkisi vermek için Azure kaynakları için Yönetilen kimlikler kullanabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: d77ab142e227cfaa6533395cc256d992e698dd17
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495921"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Azure kaynakları için Azure Active Directory ve yönetilen kimlikler ile bloblara ve kuyruklara erişim yetkisi verme

Azure Blob ve kuyruk depolama, [Azure kaynakları için yönetilen kimliklerle](../../active-directory/managed-identities-azure-resources/overview.md)Azure Active Directory (Azure AD) kimlik doğrulamasını destekler. Azure kaynakları için Yönetilen kimlikler, Azure sanal makinelerinde (VM), işlev uygulamalarında, sanal makine ölçek kümelerinde ve diğer hizmetlerde çalışan uygulamalardan Azure AD kimlik bilgilerini kullanarak blob ve kuyruk verilerine erişim yetkisi verebilir. Azure AD kimlik doğrulamasıyla birlikte Azure kaynakları için Yönetilen kimlikler kullanarak, kimlik bilgilerini bulutta çalışan uygulamalarınızla depolamaktan kaçınabilirsiniz.  

Bu makalede, Azure kaynakları için Yönetilen kimlikler kullanarak bir Azure VM 'den blob veya kuyruk verilerine erişim yetkisi verme işlemleri gösterilmektedir. Ayrıca, kodunuzun geliştirme ortamında nasıl test edileceğini açıklar.

## <a name="enable-managed-identities-on-a-vm"></a>VM 'de yönetilen kimlikleri etkinleştirme

VM 'nizden blob 'lara ve kuyruklara erişim yetkisi vermek üzere Azure kaynakları için Yönetilen kimlikler kullanabilmeniz için önce VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirmeniz gerekir. Azure kaynakları için yönetilen kimliklerin nasıl etkinleştireceğinizi öğrenmek için şu makalelerden birine bakın:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonu](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager istemci kitaplıkları](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Azure kimlik kitaplığı ile kimlik doğrulama (Önizleme)

.NET için Azure Identity istemci kitaplığı (Önizleme) bir güvenlik sorumlusunun kimliğini doğrular. Kodunuz Azure 'da çalışırken, güvenlik sorumlusu Azure kaynakları için yönetilen bir kimliktir.

Kodunuz geliştirme ortamında çalışırken, kimlik doğrulaması otomatik olarak işlenebilir veya kullandığınız araçlara bağlı olarak bir tarayıcı oturum açma işlemi gerektirebilir. Microsoft Visual Studio çoklu oturum açmayı (SSO) destekler, böylece etkin Azure AD Kullanıcı hesabı kimlik doğrulaması için otomatik olarak kullanılır. SSO hakkında daha fazla bilgi için bkz. [uygulamalarda çoklu oturum açma](../../active-directory/manage-apps/what-is-single-sign-on.md).

Diğer geliştirme araçları, bir Web tarayıcısı aracılığıyla oturum açmanız istenebilir. Geliştirme ortamından kimlik doğrulaması yapmak için bir hizmet sorumlusu da kullanabilirsiniz. Daha fazla bilgi için bkz. [portalda Azure uygulaması için kimlik oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md).

Kimlik doğrulamasından sonra Azure Identity istemci kitaplığı bir belirteç kimlik bilgisi alır. Bu belirteç kimlik bilgileri daha sonra Azure depolama 'ya karşı işlemleri gerçekleştirmek için oluşturduğunuz hizmet istemci nesnesinde kapsüllenir. Kitaplık, uygun belirteç kimlik bilgisini alarak sizin için sorunsuz bir şekilde işler.

Azure Identity istemci kitaplığı hakkında daha fazla bilgi için bkz. [.net Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Verilere erişmek için RBAC rolleri atama

Bir Azure AD güvenlik sorumlusu blob veya kuyruk verilerine erişmeyi denediğinde, bu güvenlik sorumlusunun kaynak için izinleri olması gerekir. Güvenlik sorumlusunun Azure 'da yönetilen bir kimlik veya geliştirme ortamında kod çalıştıran bir Azure AD Kullanıcı hesabı olup olmadığı, güvenlik sorumlusu, Azure depolama 'daki blob veya kuyruk verilerine erişim izni veren bir RBAC rolü atanmalıdır. RBAC aracılığıyla izin atama hakkında daha fazla bilgi için, [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)konusundaki **ERIŞIM hakları Için RBAC rolleri atama** başlıklı bölüme bakın.

## <a name="install-the-preview-packages"></a>Önizleme paketlerini yükler

Bu makaledeki örneklerde, BLOB depolama için Azure Storage istemci kitaplığı 'nın en son önizleme sürümü kullanılmaktadır. Önizleme paketini yüklemek için, NuGet paket yöneticisi konsolundan aşağıdaki komutu çalıştırın:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Bu makaledeki örneklerde, Azure AD kimlik bilgileriyle kimlik doğrulaması yapmak için [.net Için Azure Identity istemci kitaplığı](https://www.nuget.org/packages/Azure.Identity/) 'nın en son önizleme sürümü de kullanılır. Önizleme paketini yüklemek için, NuGet paket yöneticisi konsolundan aşağıdaki komutu çalıştırın:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>.NET kod örneği: blok blobu oluşturma

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

Kodunuzun Azure depolama 'ya istekleri yetkilendirmek için kullanabileceği bir belirteç kimlik bilgisi almak için, [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfının bir örneğini oluşturun. Aşağıdaki kod örneği, kimliği doğrulanmış belirteç kimlik bilgisinin nasıl alınacağını ve bir hizmet istemci nesnesi oluşturmak için bu hizmetin nasıl kullanılacağını gösterir ve ardından yeni bir blobu yüklemek için hizmet istemcisini kullanır:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Azure AD ile blob veya kuyruk verilerinde istekleri yetkilendirmek için bu istekler için HTTPS kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- Azure depolama için RBAC rolleri hakkında daha fazla bilgi edinmek için bkz. [RBAC ile depolama verileri için erişim haklarını yönetme](storage-auth-aad-rbac.md).
- Depolama uygulamalarınızın içindeki kapsayıcılara ve kuyruklara erişimi yetkilendirmeyi öğrenmek için bkz. [Azure AD 'yi depolama uygulamalarıyla kullanma](storage-auth-aad-app.md).
- Azure AD kimlik bilgileriyle Azure CLı ve PowerShell komutlarının nasıl çalıştırılacağını öğrenmek için bkz. [BLOB veya kuyruk verilerine erişmek için Azure AD kimlik bilgileriyle Azure CLI veya PowerShell komutlarını çalıştırma](storage-auth-aad-script.md).
