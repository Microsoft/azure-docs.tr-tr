---
title: Yönetilen bir kimlikle verilere erişim yetkisi verme
titleSuffix: Azure Storage
description: Azure VM 'Leri, işlev uygulamaları ve diğer uygulamalar üzerinde çalışan uygulamalardan blob ve kuyruk verilerine erişim yetkisi vermek için Azure kaynakları için Yönetilen kimlikler kullanın.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 80df7b85ec1ad9e273081f9a6a96b9a9d7ec8cd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791202"
---
# <a name="authorize-access-to-blob-and-queue-data-with-managed-identities-for-azure-resources"></a>Azure kaynakları için yönetilen kimliklerle blob ve kuyruk verilerine erişim yetkisi verme

Azure Blob ve kuyruk depolama, [Azure kaynakları için yönetilen kimliklerle](../../active-directory/managed-identities-azure-resources/overview.md)Azure Active Directory (Azure AD) kimlik doğrulamasını destekler. Azure kaynakları için Yönetilen kimlikler, Azure sanal makinelerinde (VM), işlev uygulamalarında, sanal makine ölçek kümelerinde ve diğer hizmetlerde çalışan uygulamalardan Azure AD kimlik bilgilerini kullanarak blob ve kuyruk verilerine erişim yetkisi verebilir. Azure AD kimlik doğrulamasıyla birlikte Azure kaynakları için Yönetilen kimlikler kullanarak, kimlik bilgilerini bulutta çalışan uygulamalarınızla depolamaktan kaçınabilirsiniz.  

Bu makalede, Azure kaynakları için Yönetilen kimlikler kullanarak bir Azure VM 'den blob veya kuyruk verilerine erişim yetkisi verme işlemleri gösterilmektedir. Ayrıca, kodunuzun geliştirme ortamında nasıl test edileceğini açıklar.

## <a name="enable-managed-identities-on-a-vm"></a>VM 'de yönetilen kimlikleri etkinleştirme

VM 'nizden blob 'lara ve kuyruklara erişim yetkisi vermek üzere Azure kaynakları için Yönetilen kimlikler kullanabilmeniz için önce VM 'de Azure kaynakları için yönetilen kimlikleri etkinleştirmeniz gerekir. Azure kaynakları için yönetilen kimliklerin nasıl etkinleştireceğinizi öğrenmek için şu makalelerden birine bakın:

- [Azure portalındaki](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager şablonu](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager istemci kitaplıkları](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Yönetilen kimlikler hakkında daha fazla bilgi için bkz. [Azure kaynakları Için Yönetilen kimlikler](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library"></a>Azure kimlik kitaplığı ile kimlik doğrulama

Azure Identity istemci kitaplığı, Azure [SDK](https://github.com/Azure/azure-sdk)Için Azure Azure AD belirteci kimlik doğrulama desteği sağlar. .NET, Java, Python ve JavaScript için Azure depolama istemci kitaplıklarının en son sürümleri, Azure depolama isteklerinin yetkilendirmesi için bir OAuth 2,0 belirteci elde etmek üzere basit ve güvenli bir yöntem sağlamak üzere Azure Identity Library ile tümleşir.

Azure Identity istemci kitaplığı 'nın bir avantajı, uygulamanızın geliştirme ortamında veya Azure 'da çalışıp çalışmadığını doğrulamak için aynı kodu kullanmanızı sağlar. .NET için Azure Identity istemci kitaplığı, bir güvenlik sorumlusunun kimliğini doğrular. Kodunuz Azure 'da çalışırken, güvenlik sorumlusu Azure kaynakları için yönetilen bir kimliktir. Geliştirme ortamında, yönetilen kimlik yok, bu nedenle istemci kitaplığı Kullanıcı veya test amaçları için bir hizmet sorumlusu kimliğini doğrular.

Kimlik doğrulamasından sonra Azure Identity istemci kitaplığı bir belirteç kimlik bilgisi alır. Bu belirteç kimlik bilgileri daha sonra Azure depolama 'ya karşı işlemleri gerçekleştirmek için oluşturduğunuz hizmet istemci nesnesinde kapsüllenir. Kitaplık, uygun belirteç kimlik bilgisini alarak sizin için sorunsuz bir şekilde işler.

.NET için Azure Identity istemci kitaplığı hakkında daha fazla bilgi için bkz. [.net Için Azure kimlik istemci kitaplığı](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity). Azure Identity istemci kitaplığı için başvuru belgeleri için bkz. [Azure. Identity Ad alanı](/dotnet/api/azure.identity).

### <a name="assign-azure-roles-for-access-to-data"></a>Verilere erişmek için Azure rolleri atama

Bir Azure AD güvenlik sorumlusu blob veya kuyruk verilerine erişmeyi denediğinde, bu güvenlik sorumlusunun kaynak için izinleri olması gerekir. Güvenlik sorumlusunun Azure 'da yönetilen bir kimlik veya geliştirme ortamında kod çalıştıran bir Azure AD Kullanıcı hesabı olup olmadığı, güvenlik sorumlusu, Azure depolama 'daki blob veya kuyruk verilerine erişim veren bir Azure rolü atanmalıdır. Azure RBAC aracılığıyla izin atama hakkında daha fazla bilgi için, [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)konusundaki **erişim hakları Için Azure rolleri atama** başlıklı bölüme bakın.

> [!NOTE]
> Bir Azure depolama hesabı oluşturduğunuzda, Azure AD aracılığıyla verilere erişim için otomatik olarak izinler atanmamıştır. Azure depolama için kendinize açık bir Azure rolü atamanız gerekir. Aboneliğiniz, kaynak grubunuz, depolama hesabınız veya Kapsayıcınız ya da kuyruğunuzun düzeyinde atayabilirsiniz.
>
> Veri erişimi için kendinize bir rol atamadan önce, Azure portal Azure portal aracılığıyla Depolama hesabınızdaki verilere erişebilirsiniz. bu nedenle, veri erişimi için hesap anahtarını da kullanabilir. Daha fazla bilgi için bkz. [Azure Portal blob verilerine erişimi yetkilendirmeyi seçme](../blobs/authorize-data-operations-portal.md).

### <a name="authenticate-the-user-in-the-development-environment"></a>Geliştirme ortamında kullanıcının kimliğini doğrulama

Kodunuz geliştirme ortamında çalışırken, kimlik doğrulaması otomatik olarak işlenebilir veya kullandığınız araçlara bağlı olarak bir tarayıcı oturum açma işlemi gerektirebilir. Örneğin, Microsoft Visual Studio çoklu oturum açmayı (SSO) destekler, böylece etkin Azure AD Kullanıcı hesabı kimlik doğrulaması için otomatik olarak kullanılır. SSO hakkında daha fazla bilgi için bkz. [uygulamalarda çoklu oturum açma](../../active-directory/manage-apps/what-is-single-sign-on.md).

Diğer geliştirme araçları, bir Web tarayıcısı aracılığıyla oturum açmanız istenebilir.

### <a name="authenticate-a-service-principal-in-the-development-environment"></a>Geliştirme ortamında hizmet sorumlusu kimlik doğrulaması

Geliştirme ortamınız bir Web tarayıcısı aracılığıyla çoklu oturum açmayı veya oturum açmayı desteklemiyorsa, geliştirme ortamından kimlik doğrulaması yapmak için bir hizmet sorumlusu kullanabilirsiniz.

#### <a name="create-the-service-principal"></a>Hizmet sorumlusunu oluşturma

Azure CLı ile bir hizmet sorumlusu oluşturmak ve bir Azure rolü atamak için, [az ad SP Create-for-RBAC](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) komutunu çağırın. Yeni hizmet sorumlusuna atamak için bir Azure depolama veri erişim rolü sağlayın. Ayrıca, rol atamasının kapsamını belirtin. Azure depolama için sunulan yerleşik roller hakkında daha fazla bilgi için bkz. [Azure yerleşik rolleri](../../role-based-access-control/built-in-roles.md).

Hizmet sorumlusuna rol atamak için yeterli izniniz yoksa, hesap sahibine veya yöneticiden rol atamasını gerçekleştirmesini isteyebilirsiniz.

Aşağıdaki örnek, yeni bir hizmet sorumlusu oluşturmak ve **Depolama Blobu veri okuyucusu** rolünü hesap kapsamı ile atamak IÇIN Azure CLI 'yi kullanır

```azurecli-interactive
az ad sp create-for-rbac \
    --name <service-principal> \
    --role "Storage Blob Data Contributor" \
    --scopes /subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

`az ad sp create-for-rbac`Komut, JSON biçiminde hizmet sorumlusu özelliklerinin bir listesini döndürür. Bir sonraki adımda gerekli ortam değişkenlerini oluşturmak için bunları kullanabilmeniz için bu değerleri kopyalayın.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```

> [!IMPORTANT]
> Azure rolü atamalarının yayılması birkaç dakika sürebilir.

#### <a name="set-environment-variables"></a>Ortam değişkenlerini belirleme

Azure Identity istemci kitaplığı, hizmet sorumlusunun kimliğini doğrulamak için çalışma zamanında üç ortam değişkeninden değerleri okur. Aşağıdaki tabloda her ortam değişkeni için ayarlanacak değer açıklanmaktadır.

|Ortam değişkeni|Değer
|-|-
|`AZURE_CLIENT_ID`|Hizmet sorumlusu için uygulama KIMLIĞI
|`AZURE_TENANT_ID`|Hizmet sorumlusunun Azure AD kiracı KIMLIĞI
|`AZURE_CLIENT_SECRET`|Hizmet sorumlusu için oluşturulan parola

> [!IMPORTANT]
> Ortam değişkenlerini ayarladıktan sonra konsol pencerenizi kapatıp yeniden açın. Visual Studio veya başka bir geliştirme ortamı kullanıyorsanız, yeni ortam değişkenlerini kaydedebilmesi için geliştirme ortamını yeniden başlatmanız gerekebilir.

Daha fazla bilgi için bkz. [portalda Azure uygulaması için kimlik oluşturma](../../active-directory/develop/howto-create-service-principal-portal.md).

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>.NET kod örneği: blok blobu oluşturma

`using`Azure kimlik ve Azure depolama istemci kitaplıklarını kullanmak için aşağıdaki yönergeleri kodunuza ekleyin.

```csharp
using Azure;
using Azure.Identity;
using Azure.Storage.Blobs;
using System;
using System.IO;
using System.Text;
using System.Threading.Tasks;
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
    catch (RequestFailedException e)
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

- [Azure RBAC ile depolama verilerine erişim haklarını yönetin](./storage-auth-aad-rbac-portal.md).
- [Azure AD 'yi depolama uygulamalarıyla kullanın](storage-auth-aad-app.md).
- [Blob verilerine erişmek için Azure AD kimlik bilgileriyle PowerShell komutlarını çalıştırma](../blobs/authorize-data-operations-powershell.md)
- [Öğretici: Yönetilen kimlikler kullanarak App Service depolama erişimi](../../app-service/scenario-secure-app-access-storage.md)
