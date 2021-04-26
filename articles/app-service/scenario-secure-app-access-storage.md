---
title: Öğretici-Web uygulaması, yönetilen kimlikleri kullanarak depolamaya erişir | Mavisi
description: Bu öğreticide, Yönetilen kimlikler kullanarak bir uygulama için Azure depolama 'ya nasıl erişebileceğinizi öğreneceksiniz.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a37b189ae98332b2d6c557b6bdfad98266002e9e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833913"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Öğretici: bir Web uygulamasından Azure depolama erişimi

Yönetilen kimlikler kullanarak Azure App Service çalıştıran bir Web uygulaması (oturum açmış bir kullanıcı değil) için Azure depolama 'ya erişmeyi öğrenin.

:::image type="content" alt-text="Depolama alanının nasıl erişebileceğini gösteren diyagram." source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Web uygulamanızdan Azure veri düzlemi 'ne (Azure depolama, Azure SQL veritabanı, Azure Key Vault veya diğer hizmetlere) erişim eklemek istiyorsunuz. Paylaşılan bir anahtar kullanabilirsiniz, ancak gizli dizi oluşturma, dağıtma ve yönetme ile ilgili işlemsel güvenlik konusunda endişelenmeniz gerekir. Anahtarın GitHub 'da denetlenmesi olasıdır ve bu da korsanların nasıl tarama yapılacağını bilir. Web uygulamanıza verilere erişim izni sağlamanın daha güvenli bir yolu, [Yönetilen kimlikler](../active-directory/managed-identities-azure-resources/overview.md)kullanmaktır.

Azure Active Directory (Azure AD) tarafından yönetilen bir kimlik, App Service rol tabanlı erişim denetimi (RBAC) aracılığıyla uygulama kimlik bilgileri gerekmeden kaynaklara erişmesine olanak sağlar. Web uygulamanıza yönetilen bir kimlik atadıktan sonra, Azure bir sertifikanın oluşturulmasını ve dağıtımını gerçekleştirir. Kişilerin gizli dizileri veya uygulama kimlik bilgilerini yönetme konusunda endişelenmek zorunda değildir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Bir Web uygulaması üzerinde sistem tarafından atanan yönetilen kimlik oluşturma.
> * Depolama hesabı ve Azure Blob depolama kapsayıcısı oluşturun.
> * Yönetilen kimlikler kullanarak bir Web uygulamasından depolamaya erişin.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

* [App Service kimlik doğrulama/yetkilendirme modülü etkinleştirilmiş](scenario-secure-app-authentication-app-service.md)Azure App Service üzerinde çalışan bir Web uygulaması.

## <a name="enable-managed-identity-on-an-app"></a>Bir uygulamada yönetilen kimliği etkinleştirme

Web uygulamanızı Visual Studio aracılığıyla oluşturup yayımlarsanız, yönetilen kimlik sizin için uygulamanızda etkinleştirilmiştir. App Service 'te, sol bölmedeki **kimlik** ' i seçin ve ardından **sistem atandı**' ı seçin. **Durumun** **Açık** olarak ayarlandığını doğrulayın. Aksi takdirde, **Kaydet** ' i seçin ve ardından, sistem tarafından atanan yönetilen kimliği etkinleştirmek için **Evet** ' i seçin. Yönetilen kimlik etkinleştirildiğinde, durum **Açık** olarak ayarlanır ve nesne kimliği kullanılabilir.

:::image type="content" alt-text="Sistem tarafından atanan kimlik seçeneğini gösteren ekran görüntüsü." source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Bu adım, **kimlik doğrulama/yetkilendirme** bölmesinde oluşturulan uygulama kimliğinden farklı yeni BIR nesne kimliği oluşturur. Sistem tarafından atanan yönetilen kimliğin nesne KIMLIĞINI kopyalayın. Buna daha sonra ihtiyacınız olacak.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Depolama hesabı ve BLOB depolama kapsayıcısı oluşturma

Artık bir depolama hesabı ve BLOB depolama kapsayıcısı oluşturmak için hazır olursunuz.

Her depolama hesabı bir Azure kaynak grubuna ait olmalıdır. Kaynak grubu, Azure hizmetlerinizi gruplandırmaya yönelik mantıksal bir kapsayıcıdır. Bir depolama hesabı oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olan bir kaynak grubunu kullanma seçeneğiniz vardır. Bu makalede, yeni bir kaynak grubu oluşturma gösterilmektedir.

Genel amaçlı v2 depolama hesabı, tüm Azure Depolama hizmetlerine erişim sağlar: blob'lar, dosyalar, kuyruklar, tablolar ve diskler. Burada özetlenen adımlarda genel amaçlı v2 depolama hesabı oluşturulur, ancak herhangi bir tür depolama hesabı oluşturma adımları benzerdir.

Azure depolama 'daki Bloblar kapsayıcılar halinde düzenlenir. Bu öğreticide daha sonra bir blobu karşıya yükleyebilmeniz için önce bir kapsayıcı oluşturmanız gerekir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal genel amaçlı v2 depolama hesabı oluşturmak için aşağıdaki adımları izleyin.

1. Azure portalı menüsünde **Tüm hizmetler**’i seçin. Kaynak listesinde **depolama hesapları**' nı girin. Yazmaya başladığınızda liste, girişinize göre filtrelenir. **Depolama hesapları**' nı seçin.

1. Görüntülenen **depolama hesapları** penceresinde **Ekle**' yi seçin.

1. Depolama hesabının oluşturulacağı aboneliği seçin.

1. **Kaynak grubu** alanında, açılan menüden Web uygulamanızı içeren kaynak grubunu seçin.

1. Ardından, depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir.

1. Depolama hesabınız için bir konum seçin veya varsayılan konumu kullanın.

1. Bu alanları varsayılan değerlerine ayarlanmış olarak bırakın:

    |Alan|Değer|
    |--|--|
    |Dağıtım modeli|Resource Manager|
    |Performans|Standart|
    |Hesap türü|StorageV2 (genel amaçlı v2)|
    |Çoğaltma|Okuma erişimli coğrafi olarak yedekli depolama (RA-GRS)|
    |Erişim katmanı|Sık Erişimli|

1. Depolama hesabı ayarlarınızı gözden geçirmek ve hesabı oluşturmak için **Gözden Geçir + Oluştur**’u seçin.

1. **Oluştur**’u seçin.

Azure depolama 'da bir BLOB depolama kapsayıcısı oluşturmak için aşağıdaki adımları izleyin.

1. Azure portal yeni depolama hesabınıza gidin.

1. Depolama hesabının sol menüsünde, **BLOB hizmeti** bölümüne gidin ve **kapsayıcılar**' ı seçin.

1. **+ Kapsayıcı** düğmesini seçin.

1. Yeni kapsayıcınız için bir ad yazın. Kapsayıcı adı küçük harflerden oluşmalı ve bir harf veya rakamla başlamalıdır; yalnızca harf, rakam ve tire (-) karakterini içerebilir.

1. Kapsayıcıya genel erişim düzeyini ayarlayın. Varsayılan düzey **Özel (anonim erişim yok)** şeklindedir.

1. Kapsayıcıyı oluşturmak için **Tamam**'ı seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Genel amaçlı v2 depolama hesabı ve BLOB depolama kapsayıcısı oluşturmak için aşağıdaki betiği çalıştırın. Web uygulamanızı içeren kaynak grubunun adını belirtin. Depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir.

Depolama hesabınızın konumunu belirtin. Aboneliğiniz için geçerli olan konumların bir listesini görmek için öğesini çalıştırın ```Get-AzLocation | select Location``` . Kapsayıcı adı küçük harflerden oluşmalı ve bir harf veya rakamla başlamalıdır; yalnızca harf, rakam ve tire (-) karakterini içerebilir.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Genel amaçlı v2 depolama hesabı ve BLOB depolama kapsayıcısı oluşturmak için aşağıdaki betiği çalıştırın. Web uygulamanızı içeren kaynak grubunun adını belirtin. Depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 ila 24 karakter uzunluğunda olmalı ve yalnızca rakamlar ve küçük harfler içerebilir. 

Depolama hesabınızın konumunu belirtin. Kapsayıcı adı küçük harflerden oluşmalı ve bir harf veya rakamla başlamalıdır; yalnızca harf, rakam ve tire (-) karakterini içerebilir.

Aşağıdaki örnek, kapsayıcıyı oluşturma işlemini yetkilendirmek için Azure AD hesabınızı kullanır. Kapsayıcıyı oluşturmadan önce, Depolama Blobu veri katılımcısı rolünü kendinize atayın. Hesap sahibi olsanız bile, depolama hesabında veri işlemleri gerçekleştirmek için açık izinlere sahip olmanız gerekir.

Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Depolama hesabına erişim izni verme

Blobları oluşturabilmeniz, okumanız veya silebilmeniz için önce, Web uygulamanıza depolama hesabına erişim izni vermeniz gerekir. Önceki bir adımda, App Service üzerinde çalışan Web uygulamasını yönetilen bir kimlikle yapılandırdınız. Azure RBAC kullanarak, herhangi bir güvenlik sorumlusu gibi başka bir kaynağa yönetilen kimlik erişimi verebilirsiniz. Depolama Blobu veri katılımcısı rolü, blob kapsayıcısına ve verilerine okuma, yazma ve silme erişimi Web uygulamasına (sistem tarafından atanan yönetilen kimlik tarafından temsil edilir) izin verir.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Azure Portal](https://portal.azure.com), Web uygulamanıza erişim sağlamak için depolama hesabınıza gidin. Sol bölmede **erişim denetimi (IAM)** öğesini seçin ve ardından **rol atamaları**' nı seçin. Depolama hesabına kimlerin erişebileceğini içeren bir liste görürsünüz. Şimdi, depolama hesabına erişmesi gereken bir robot, uygulama hizmeti olan bir rol ataması eklemek istiyorsunuz.   >  **Rol Ekle ataması** Ekle ' yi seçin.

**Rol**' de, Web uygulamanıza depolama Blobları okuma erişimi sağlamak Için **Depolama Blobu veri katılımcısı** ' nı seçin. **Erişim ata**' da **App Service**' yi seçin. **Abonelikte** aboneliğinizi seçin. Daha sonra erişim sağlamak istediğiniz App Service 'i seçin. **Kaydet**’i seçin.

:::image type="content" alt-text="Rol ataması ekleme ekranını gösteren ekran görüntüsü." source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Web uygulamanızın artık depolama hesabınıza erişimi vardır.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Web uygulamanızı (sistem tarafından atanan yönetilen kimlik ile temsil edilen), Depolama hesabınızdaki Depolama Blobu veri katılımcısı rolünü atamak için aşağıdaki betiği çalıştırın.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Web uygulamanızı (sistem tarafından atanan yönetilen kimlik ile temsil edilen), Depolama hesabınızdaki Depolama Blobu veri katılımcısı rolünü atamak için aşağıdaki betiği çalıştırın.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Blob depolamaya erişme (.NET)

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfı, Azure depolama 'ya istekleri yetkilendirmek üzere kodunuzun belirteç kimlik bilgilerini almak için kullanılır. Belirteçleri getirmek ve bunları hizmet istemcisine eklemek için yönetilen kimliği kullanan [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) sınıfının bir örneğini oluşturun. Aşağıdaki kod örneği, kimliği doğrulanmış belirteç kimlik bilgisini alır ve yeni bir blobu yükleyen bir hizmet istemci nesnesi oluşturmak için onu kullanır.

Bu kodu örnek bir uygulamanın parçası olarak görmek için [GitHub 'daki örneğe](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity)bakın.

### <a name="install-client-library-packages"></a>İstemci kitaplığı paketlerini yükler

BLOB depolama ile çalışmak için [BLOB depolama NuGet paketini](https://www.nuget.org/packages/Azure.Storage.Blobs/) ve Azure AD kimlik bilgileriyle kimlik doğrulaması yapmak Için [.net NuGet paketi için Azure kimlik istemci kitaplığını](https://www.nuget.org/packages/Azure.Identity/) yüklemek üzere yüklemeyi yapın. İstemci kitaplıklarını, .NET Core komut satırı arabirimini veya Visual Studio 'da Paket Yöneticisi konsolunu kullanarak yükler.

# <a name="command-line"></a>[Komut satırı](#tab/command-line)

Bir komut satırı açın ve proje dosyanızı içeren dizine geçiş yapın.

Install komutlarını çalıştırın.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Paket Yöneticisi](#tab/package-manager)

Visual Studio 'da projeyi veya çözümü açın ve **Araçlar**  >  **NuGet Paket Yöneticisi**  >  **Paket Yöneticisi konsolu** komutunu kullanarak konsolunu açın.

Install komutlarını çalıştırın.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Örnek

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
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
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticiyi bitirdiyseniz ve artık Web uygulamasına veya ilişkili kaynaklara ihtiyacınız yoksa, [oluşturduğunuz kaynakları temizleyin](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Sistem tarafından atanan yönetilen kimlik oluşturma.
> * Depolama hesabı ve BLOB depolama kapsayıcısı oluşturun.
> * Yönetilen kimlikler kullanarak bir Web uygulamasından depolamaya erişin.

> [!div class="nextstepaction"]
> [App Service Kullanıcı adına Microsoft Graph erişir](scenario-secure-app-access-microsoft-graph-as-user.md)
