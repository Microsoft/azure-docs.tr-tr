---
title: Az Search modülünü kullanarak Azure CLı betikleri
titleSuffix: Azure Cognitive Search
description: Azure CLı ile bir Azure Bilişsel Arama hizmeti oluşturun ve yapılandırın. Bir hizmeti yukarı veya aşağı ölçeklendirebilir, yönetici ve sorgu api anahtarlarını yönetebilir ve sistem bilgileri için sorgu yapabilirsiniz.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 456aaf20c0b6d198ae353490d961a69a319b6601
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045119"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Azure CLı ile Azure Bilişsel Arama hizmetinizi yönetme
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Azure Bilişsel Arama oluşturmak ve yapılandırmak için Windows, macOS, Linux veya [Azure Cloud Shell](../cloud-shell/overview.md) ' de Azure CLI komutları ve betikleri çalıştırabilirsiniz. [**Az Search**](/cli/azure/search) modülü, [Azure CLI](/cli/) 'Yı [arama yönetimi REST API 'lerine](/rest/api/searchmanagement) tam eşlik ile genişletir ve aşağıdaki görevleri gerçekleştirebilir:

> [!div class="checklist"]
> * [Bir abonelikte arama hizmetlerini listeleme](#list-search-services)
> * [Hizmet bilgilerini döndür](#get-search-service-information)
> * [Hizmet oluşturma veya silme](#create-or-delete-a-service)
> * [Özel bir uç nokta ile hizmet oluşturma](#create-a-service-with-a-private-endpoint)
> * [Yönetici API anahtarlarını yeniden oluştur](#regenerate-admin-keys)
> * [Sorgu API 'si oluşturma veya silme](#create-or-delete-query-keys)
> * [Çoğaltmalar ve bölümlerle ölçeği artırma veya azaltma](#scale-replicas-and-partitions)
> * [Paylaşılan özel bağlantı kaynağı oluşturma](#create-a-shared-private-link-resource)

Bazen, yukarıdaki listede *olmayan* görevlerle ilgili soruların sorulması istenir. Şu anda, sunucu adını, bölgeyi veya katmanını değiştirmek için **az Search** modülünü ya da yönetim REST API kullanamazsınız. Adanmış kaynaklar bir hizmet oluşturulduğunda ayrılır. Bu nedenle, temel alınan donanımın (konum veya düğüm türü) değiştirilmesi için yeni bir hizmet gerekir. Benzer şekilde, bir hizmetten diğerine bir dizin gibi içerik aktarmaya yönelik bir araç veya API yoktur.

Bir hizmette, içerik oluşturma ve Yönetim [Arama Hizmeti REST API](/rest/api/searchservice/) veya [.NET SDK](/dotnet/api/overview/azure/search.documents-readme)'lardır. İçerik için adanmış bir PowerShell komutu bulunmadığından, dizin oluşturmak ve yüklemek için REST veya .NET API 'Leri çağıran betikler yazabilirsiniz.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Bir abonelikteki hizmetleri listeleme

Aşağıdaki komutlar [**az kaynaktır**](/cli/azure/resource)ve aboneliğinizde zaten sağlanmış olan mevcut kaynaklar ve hizmetler hakkında bilgi döndürüyor. Kaç tane arama hizmeti oluşturulduğunu bilmiyorsanız, bu komutlar bu bilgileri döndürür ve portala seyahat ister.

İlk komut tüm arama hizmetlerini döndürür.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Hizmetler listesinden belirli bir kaynakla ilgili bilgileri döndürür.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Tümünü Listele az Search Commands

[**Az arama**](/cli/azure/search) ' da CLI içinden kullanılabilen alt gruplar ve komutlar hakkında bilgi görüntüleyebilirsiniz. Alternatif olarak, [belgeleri](/cli/azure/search)gözden geçirebilirsiniz.

İçinde kullanılabilen alt grupları görüntülemek için `az search` aşağıdaki komutu çalıştırın.

```azurecli-interactive
az search --help
```

Yanıt aşağıdaki çıktıya benzer görünmelidir.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Her alt grup içinde birden çok komut vardır. Aşağıdaki satırı çalıştırarak alt grup için kullanılabilir komutları görebilirsiniz `service` .

```azurecli-interactive
az search service --help
```

Ayrıca belirli bir komut için kullanılabilen bağımsız değişkenleri görebilirsiniz.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Arama hizmeti bilgilerini al

Arama hizmetinizi içeren kaynak grubunu biliyorsanız, ad, bölge, katman ve çoğaltma ve bölüm sayıları dahil olmak üzere hizmet tanımını döndürmek için [**az Search SERVICE Show**](/cli/azure/search/service#az_search_service_show) ' i çalıştırın.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Hizmet oluşturma veya silme

[Yeni bir arama hizmeti oluşturmak](search-create-service-portal.md)için [**az Search Service Create**](/cli/azure/search/service#az_search_service_show) komutunu kullanın.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Sonuçlar aşağıdaki çıktıya benzer görünmelidir:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>IP kuralları ile hizmet oluşturma

Güvenlik gereksinimlerinize bağlı olarak, bir [IP güvenlik duvarı yapılandırılmış](service-configure-firewall.md)bir arama hizmeti oluşturmak isteyebilirsiniz. Bunu yapmak için, genel IP (v4) adreslerini veya CıDR aralıklarını `ip-rules` aşağıda gösterildiği gibi bağımsız değişkene geçirin. Kurallar virgül ( `,` ) veya noktalı virgül () ile ayrılmalıdır `;` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimliğe sahip bir hizmet oluşturma

[Bir veri kaynağına bağlanmak için yönetilen kimlik kullanırken](search-howto-managed-identities-storage.md)olduğu gibi bazı durumlarda, [sistem tarafından atanan yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md)açmanız gerekir. Bu, komuta eklenerek yapılır `--identity-type SystemAssigned` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Özel bir uç nokta ile hizmet oluşturma

Azure Bilişsel Arama [Özel uç noktaları](../private-link/private-endpoint-overview.md) , bir sanal ağdaki bir Istemcinin bir [özel bağlantı](../private-link/private-link-overview.md)üzerinden arama dizinindeki verilere güvenli bir şekilde erişmesini sağlar. Özel uç nokta, arama hizmetiniz için [sanal ağ adres alanından](../virtual-network/private-ip-addresses.md) bir IP adresi kullanır. İstemci ile arama hizmeti arasındaki ağ trafiği, sanal ağın ve Microsoft omurga ağındaki özel bir bağlantının üzerinde geçiş yaparken, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. Daha fazla ayrıntı için lütfen [Azure bilişsel arama özel bir uç noktası oluşturma](service-create-private-endpoint.md) belgelerine başvurun

Aşağıdaki örnek, özel bir uç nokta ile bir arama hizmeti oluşturmayı gösterir.

İlk olarak, olarak ayarlanmış bir arama hizmeti dağıtın `PublicNetworkAccess` `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Ardından, bir sanal ağ ve özel uç nokta oluşturun.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Son olarak, özel bir DNS bölgesi oluşturun. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

PowerShell 'de özel uç noktalar oluşturma hakkında daha fazla bilgi için bkz. bu [özel bağlantı hızlı başlangıç](../private-link/create-private-endpoint-cli.md)

### <a name="manage-private-endpoint-connections"></a>Özel uç nokta bağlantılarını yönetme

Özel bir uç nokta bağlantısı oluşturmaya ek olarak,, `show` `update` ve bağlantısı da yapabilirsiniz `delete` .

Özel bir uç nokta bağlantısı almak ve durumunu görmek için [**az Search Private-Endpoint-Connection Show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show)kullanın.

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Bağlantıyı güncelleştirmek için [**az Search Private-Endpoint-Connection Update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update)kullanın. Aşağıdaki örnek, reddedildi olarak bir özel uç nokta bağlantısı ayarlıyor:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Özel uç nokta bağlantısını silmek için [**az Search Private-Endpoint-Connection Delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete)kullanın.

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Yönetici anahtarlarını yeniden oluştur

Yönetici [API anahtarlarını](search-security-api-keys.md)almak için [**az Search admin-Key yenilemeyi**](/cli/azure/search/admin-key#az_search_admin_key_renew)kullanın. Kimliği doğrulanmış erişim için her bir hizmetle iki yönetici anahtarı oluşturulur. Her istekte anahtarlar gereklidir. Her iki yönetici anahtarı da işlevsel olarak eşdeğerdir, bir arama hizmetine tüm bilgileri alma veya herhangi bir nesne oluşturma ve silme imkanına tam yazma erişimi veriliyor. Diğerini değiştirirken bir tane kullanabilmeniz için iki anahtar mevcuttur. 

Ya da anahtarı olarak belirtilen tek seferde yalnızca bir tane oluşturabilirsiniz `primary` `secondary` . Kesintisiz hizmet için, birincil anahtar üzerinde çalışırken tüm istemci kodlarını ikincil anahtar kullanacak şekilde güncelleştirmeyi unutmayın. İşlemler uçuşdayken anahtarları değiştirmekten kaçının.

Beklenirken, istemci kodunu güncelleştirmeden anahtarları yeniden oluşturursanız, eski anahtarı kullanan istekler başarısız olur. Tüm yeni anahtarların yeniden oluşturulması, hizmetinizi kalıcı olarak kilitlemez ve Portal üzerinden hizmete erişmeye devam edebilirsiniz. Birincil ve ikincil anahtarları yeniden oluşturduktan sonra, yeni anahtarları kullanmak için istemci kodunu güncelleştirebilirsiniz ve işlemler buna uygun şekilde sürdürülür.

API anahtarlarının değerleri hizmet tarafından oluşturulur. Azure Bilişsel Arama kullanılmak üzere özel bir anahtar sağlayamezsiniz. Benzer şekilde, yönetici API anahtarları için Kullanıcı tanımlı ad yoktur. Anahtara yapılan başvurular veya ya da sabit dizelerdir `primary` `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Sonuçlar aşağıdaki çıktıya benzer görünmelidir. Her iki anahtar de tek seferde yalnızca birini değiştirdiğiniz halde döndürülür.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Sorgu anahtarları oluşturma veya silme

İstemci uygulamalarından bir Azure Bilişsel Arama dizinine salt okuma erişimi için sorgu [API 'si anahtarları](search-security-api-keys.md) oluşturmak için [**az Search Query-Key Create**](/cli/azure/search/query-key#az_search_query_key_create)kullanın. Sorgu anahtarları, arama sonuçlarının alınması amacıyla belirli bir dizin için kimlik doğrulaması yapmak üzere kullanılır. Sorgu anahtarları, hizmette, dizin, veri kaynağı veya Dizin Oluşturucu gibi diğer öğelere salt okuma erişimi vermez.

Azure Bilişsel Arama kullanılmak üzere bir anahtar sağlayamezsiniz. API anahtarları hizmet tarafından oluşturulur.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Çoğaltmaları ve bölümleri ölçeklendirme

[Çoğaltmaları ve bölümleri artırmak veya azaltmak](search-capacity-planning.md) için [**az Search Service Update**](/cli/azure/search/service#az_search_service_update)kullanın. Çoğaltmaları veya bölümleri, hem sabit hem de değişken ücretlerine sahip olan faturanızda artırır. Ek işlem gücü için geçici bir ihtiyacınız varsa, iş yükünü işlemek için çoğaltmaları ve bölümleri artırabilirsiniz. Genel Bakış portalı sayfasındaki izleme alanında sorgu gecikmesi üzerinde Kutucuklar, saniye başına sorgu ve kısıtlama, geçerli kapasitenin yeterli olup olmadığını gösterir.

Kaynak kaynağı ekleme veya kaldırma biraz zaman alabilir. Kapasite ayarlamaları arka planda gerçekleşir ve mevcut iş yüklerinin devam etmesine izin verir. Daha fazla yapılandırma gerekmeden, gelen istekler için ek kapasite kullanılır. 

Kapasitenin kaldırılması kesintiye uğratan olabilir. Atlanan isteklerin olmaması için kapasiteyi azaltmadan önce tüm dizin oluşturma ve Dizin Oluşturucu işlerinin durdurulması önerilir. Bu uygun değilse, yeni hedef düzeyleriniz ulaşılana kadar kapasiteyi artımlı olarak, bir çoğaltma ve bölüm olarak azaltmayı düşünebilirsiniz.

Komutu gönderdikten sonra, bu işlemi geçişli olarak sonlandırmak için bir yol yoktur. Sayıları yeniden ayarlamadan önce komutun bitmesini beklemeniz gerekir.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Çoğaltma ve bölüm sayılarını güncelleştirmenin yanı sıra,, ve ' ı da `ip-rules` güncelleştirebilirsiniz `public-access` `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Paylaşılan özel bağlantı kaynağı oluşturma

Azure Bilişsel Arama API 'Leri aracılığıyla oluşturulan güvenli kaynakların özel uç noktaları, *paylaşılan özel bağlantı kaynakları* olarak adlandırılır. Bunun nedeni, [Azure özel bağlantı hizmeti](https://azure.microsoft.com/services/private-link/)ile tümleştirilmiş bir depolama hesabı gibi bir kaynağa "paylaşım" erişiminizin olması nedeniyle oluşur.

Azure Bilişsel Arama 'daki verileri indekslemek için bir Dizin Oluşturucu kullanıyorsanız ve veri kaynağınız özel bir ağ üzerinde ise, verilere ulaşmak için giden [Özel uç nokta bağlantısı](../private-link/private-endpoint-overview.md) oluşturabilirsiniz.

Azure Bilişsel Arama giden özel uç noktaları oluşturabileceğiniz Azure kaynaklarının tam bir listesi, ilgili **Grup Kimliği** değerleriyle birlikte [burada](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) bulunabilir.

Paylaşılan özel bağlantı kaynağını oluşturmak için [**az Search Shared-Private-link-Resource Create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)seçeneğini kullanın. Bu komutu çalıştırmadan önce veri kaynağı için bazı yapılandırmanın gerekli olabileceğini aklınızda bulundurun.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Paylaşılan özel bağlantı kaynaklarını almak ve durumlarını görüntülemek için [**az Search Shared-Private-link-Resource listesini**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list)kullanın.

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Kullanılmadan önce aşağıdaki komutla bağlantıyı onaylamanız gerekir. Özel uç nokta bağlantısının KIMLIĞININ alt kaynaktan alınması gerekir. Bu durumda, az Storage ' dan bağlantı KIMLIĞI alırız.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Paylaşılan özel bağlantı kaynağını silmek için [**az Search Shared-Private-link-Resource Delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete)kullanın.

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Paylaşılan özel bağlantı kaynaklarını ayarlamayla ilgili tam Ayrıntılar için, [özel bir uç nokta aracılığıyla Dizin Oluşturucu bağlantıları yapma](search-indexer-howto-access-private.md)hakkındaki belgelere bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bir [Dizin](search-what-is-an-index.md)oluşturun, PORTALı, REST API 'leri veya .NET SDK 'sını kullanarak [bir dizini sorgulayın](search-query-overview.md) .

* [Azure portal Azure Bilişsel Arama dizini oluşturma](search-get-started-portal.md)
* [Diğer hizmetlerden veri yüklemek için Dizin Oluşturucu ayarlama](search-indexer-overview.md)
* [Azure portal arama Gezgini 'ni kullanarak bir Azure Bilişsel Arama dizinini sorgulama](search-explorer.md)
* [.NET Bilişsel Arama Azure 'da kullanma](search-howto-dotnet-sdk.md)