---
title: Azure Blob depolama süresini yönetme
titleSuffix: Azure Content Delivery Network
description: Azure CDN önbelleğe alma işleminde blob 'lar için yaşam süresi denetleme seçenekleri hakkında bilgi edinin.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083151"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Azure CDN Azure Blob Storage 'ın kullanım süresini yönetme
> [!div class="op_single_selector"]
> * [Azure web içeriği](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Depolama](cdn-manage-expiration-of-blob-content.md)
> 
> 

Azure depolama 'daki [BLOB Storage hizmeti](../storage/common/storage-introduction.md#blob-storage) , Azure Content DELIVERY Network (CDN) ile tümleştirilmiş çeşitli Azure tabanlı kaynaklardan biridir. Herkese açık olarak erişilebilen tüm blob içerikleri, yaşam süresi (TTL) sona erene kadar Azure CDN önbelleğe alınabilir. TTL, kaynak sunucudan gelen HTTP yanıtında `Cache-Control` üst bilgisi tarafından belirlenir. Bu makalede, Azure depolama 'daki bir bloba `Cache-Control` üst bilgisini ayarlayabileceğiniz çeşitli yollar açıklanmaktadır.

Ayrıca, CDN önbelleğe alma kurallarını ayarlayarak Azure portal önbellek ayarlarını kontrol edebilirsiniz. Önbelleğe alma kuralı oluşturur ve önbelleği **geçersiz kılmak** veya **atlamak**için önbelleğe alma davranışını ayarlarsanız, bu makalede ele alınan kaynak tarafından belirtilen önbelleğe alma ayarları göz ardı edilir. Genel önbelleğe alma kavramları hakkında daha fazla bilgi için bkz. [önbelleğe alma nasıl kullanılır](cdn-how-caching-works.md).

> [!TIP]
> Blob üzerinde TTL ayarı ayarlamayı tercih edebilirsiniz. Bu durumda, Azure portal önbelleğe alma kurallarını ayarlamadığınız müddetçe, Azure CDN otomatik olarak yedi günlük bir varsayılan TTL uygular. Bu varsayılan TTL yalnızca genel Web teslim iyileştirmeleri için geçerlidir. Büyük dosya iyileştirmeleri için varsayılan TTL bir gündür ve medya akışı iyileştirmeleri için varsayılan TTL bir yıldır.
> 
> Azure CDN bloblara ve diğer dosyalara erişimi hızlandırmak için nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Azure Content Delivery Network genel bakış](cdn-overview.md).
> 
> Azure Blob depolama hakkında daha fazla bilgi için bkz. [BLOB depolamaya giriş](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN önbelleğe alma kurallarını kullanarak Cache-Control üst bilgilerini ayarlama
Bir Blobun `Cache-Control` üst bilgisini ayarlamak için tercih edilen yöntem, Azure portal önbelleğe alma kurallarını kullanmaktır. CDN önbelleğe alma kuralları hakkında daha fazla bilgi için bkz. [önbelleğe alma kurallarıyla denetim Azure CDN önbelleğe alma davranışı](cdn-caching-rules.md).

> [!NOTE] 
> Önbelleğe alma kuralları yalnızca Verizon **Azure CDN ve Akamai** profillerindeki standart **Azure CDN Standart** için kullanılabilir. **Verizon profillerden Azure CDN Premium** Için, **Manage** Portal içindeki [Azure CDN Rules altyapısını](cdn-rules-engine.md) benzer işlevler için kullanmanız gerekir.

**CDN önbelleğe alma kuralları sayfasına gitmek için**:

1. Azure portal, bir CDN profili seçin ve ardından blob uç noktasını seçin.

2. Ayarların altındaki sol bölmede **Önbelleğe alma kuralları**’nı seçin.

   ![CDN önbelleğe alma kuralları düğmesi](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   **Önbelleğe alma kuralları** sayfası görüntülenir.

   ![CDN önbelleğe alma sayfası](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**BLOB depolama hizmetinin önbellek denetimi üst bilgilerini genel önbelleğe alma kurallarını kullanarak ayarlamak için:**

1. **Genel önbelleğe alma kuralları**' nın altında sorgu **dizelerini yok say** ve **önbelleğe alma davranışını** **geçersiz kılmak**için **sorgu dizesi önbelleğe alma davranışını** ayarlayın.
      
2. **Önbellek sona erme süresi**Için, **saniye** kutusuna 3600 veya **saat** kutusuna 1 yazın. 

   ![CDN genel önbelleğe alma kuralları örneği](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Bu genel önbelleğe alma kuralı bir saatin önbellek süresini ayarlar ve uç noktaya yapılan tüm istekleri etkiler. Uç nokta tarafından belirtilen kaynak sunucu tarafından gönderilen tüm `Cache-Control` veya `Expires` HTTP üstbilgilerini geçersiz kılar.   

3. **Kaydet**’i seçin.
 
**Özel önbelleğe alma kurallarını kullanarak bir blob dosyasının Cache-Control üst bilgilerini ayarlamak için:**

1. **Özel önbelleğe alma kuralları**altında, iki eşleşme koşulu oluşturun:

     A. İlk eşleşme koşulu için **eşleştirme koşulu** ' nı **yol** olarak ayarlayın ve **eşleşme değeri**için `/blobcontainer1/*` girin. **Önbelleğe alma davranışını** **geçersiz kılmak** için ayarlayın ve **saat** kutusuna 4 girin.

    B. İkinci eşleşme koşulu için **eşleşme koşulu** ' nı **yol** olarak ayarlayın ve **eşleşme değeri**için `/blobcontainer1/blob1.txt` girin. **Önbelleğe alma davranışını** **geçersiz kılmak** için ayarlayın ve **saat** kutusuna 2 girin.

    ![CDN özel önbelleğe alma kuralları örneği](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    İlk özel önbelleğe alma kuralı, uç noktanız tarafından belirtilen kaynak sunucudaki `/blobcontainer1` klasöründeki tüm blob dosyaları için dört saatlik bir önbellek süresi ayarlar. İkinci kural yalnızca `blob1.txt` blob dosyası için ilk kuralı geçersiz kılar ve bunun için iki saatlik bir önbellek süresi ayarlar.

2. **Kaydet**’i seçin.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Azure PowerShell kullanarak Cache-Control üst bilgilerini ayarlama

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/overview) , Azure hizmetlerinizi yönetmenin en hızlı ve en güçlü yöntemlerinden biridir. Blob 'a başvuru almak için `Get-AzStorageBlob` cmdlet 'ini kullanın, sonra `.ICloudBlob.Properties.CacheControl` özelliğini ayarlayın. 

Örneğin:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> [CDN profillerini ve uç noktalarını yönetmek](cdn-manage-powershell.md)için PowerShell de kullanabilirsiniz.
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>.NET kullanarak Cache-Control üst bilgilerini ayarlama
.NET Code kullanarak bir Blobun `Cache-Control` üst bilgisini belirtmek için, [Cloudblob. Properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) özelliğini ayarlamak üzere [.net Için Azure depolama istemci kitaplığı](../storage/blobs/storage-dotnet-how-to-use-blobs.md) ' nı kullanın.

Örneğin:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> [.Net Için Azure Blob depolama örneklerinde](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)daha fazla .NET kod örneği mevcuttur.
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Diğer yöntemleri kullanarak Cache-Control üst bilgilerini ayarlama

### <a name="azure-storage-explorer"></a>Azure Depolama Gezgini
[Azure Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/)Ile, *CacheControl* özelliği gibi özellikler de dahil olmak üzere BLOB depolama kaynaklarınızı görüntüleyebilir ve düzenleyebilirsiniz. 

Blob 'un *CacheControl* özelliğini Azure Depolama Gezgini ile güncelleştirmek için:
   1. Bir blob seçin ve bağlam menüsünden **Özellikler** ' i seçin. 
   2. *CacheControl* özelliği için aşağı kaydırın.
   3. Bir değer girin ve ardından **Kaydet**' i seçin.


![Azure Depolama Gezgini özellikleri](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure Komut Satırı Arabirimi
[Azure komut satırı arabirimi](https://docs.microsoft.com/cli/azure) (CLI) ile Azure Blob kaynaklarını komut satırından yönetebilirsiniz. Azure CLı ile bir blob yüklediğinizde Cache-Control üst bilgisini ayarlamak için, `-p` anahtarını kullanarak *cacheControl* özelliğini ayarlayın. Aşağıdaki örnek, TTL 'nin bir saate nasıl ayarlanacağını gösterir (3600 saniye):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure depolama hizmetleri REST API
Bir istekte aşağıdaki işlemleri kullanarak *x-MS-blob-Cache-Control* özelliğini açık bir şekilde ayarlamak için [Azure depolama hizmetleri REST API](/rest/api/storageservices/) kullanabilirsiniz:
  
   - [Blobu koy](/rest/api/storageservices/Put-Blob)
   - [Öbek listesini yerleştirme](/rest/api/storageservices/Put-Block-List)
   - [Blob özelliklerini ayarla](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Cache-Control üst bilgisini test etme
Bloblarınızın TTL ayarlarını kolayca doğrulayabilirsiniz. Tarayıcınızın [Geliştirici araçlarıyla](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), blobun `Cache-Control` yanıt üst bilgisini içerdiğini test edin. Yanıt üst bilgilerini incelemek için [wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/)veya [Fiddler](https://www.telerik.com/fiddler) gibi bir araç da kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki Adımlar
* [Azure CDN bulut hizmeti içeriğinin süresinin dolmasının nasıl yönetileceğini öğrenin](cdn-manage-expiration-of-cloud-service-content.md)
* [Önbelleğe alma kavramlarını öğrenin](cdn-how-caching-works.md)

