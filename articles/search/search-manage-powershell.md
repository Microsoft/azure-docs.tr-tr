---
title: Az. Search modülünü kullanan PowerShell betikleri
titleSuffix: Azure Cognitive Search
description: PowerShell ile bir Azure Bilişsel Arama hizmeti oluşturun ve yapılandırın. Bir hizmeti yukarı veya aşağı ölçeklendirebilir, yönetici ve sorgu api anahtarlarını yönetebilir ve sistem bilgileri için sorgu yapabilirsiniz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: efb5d498c627a6731d2a90623c81eefabd0042a0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462788"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Azure Bilişsel Arama hizmetinizi PowerShell ile yönetme
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Azure Bilişsel Arama oluşturmak ve yapılandırmak için Windows, Linux veya [Azure Cloud Shell](../cloud-shell/overview.md) 'de PowerShell cmdlet 'leri ve betikleri çalıştırabilirsiniz. **Az. Search** modülü, [arama yönetimi REST API 'lerine](/rest/api/searchmanagement) tam eşlik ile [Azure PowerShell](/powershell/) genişletir ve aşağıdaki görevleri gerçekleştirebilir:

> [!div class="checklist"]
> * [Bir abonelikte arama hizmetlerini listeleme](#list-search-services)
> * [Hizmet bilgilerini döndür](#get-search-service-information)
> * [Hizmet oluşturma veya silme](#create-or-delete-a-service)
> * [Özel bir uç nokta ile hizmet oluşturma](#create-a-service-with-a-private-endpoint)
> * [Yönetici API anahtarlarını yeniden oluştur](#regenerate-admin-keys)
> * [Sorgu API 'si oluşturma veya silme](#create-or-delete-query-keys)
> * [Çoğaltmalar ve bölümlerle ölçeği artırma veya azaltma](#scale-replicas-and-partitions)
> * [Paylaşılan özel bağlantı kaynağı oluşturma](#create-a-shared-private-link-resource)

Bazen, yukarıdaki listede *olmayan* görevlerle ilgili soruların sorulması istenir. Şu anda, sunucu adını, bölgeyi veya katmanını değiştirmek için **az. Search** modülünü ya da yönetim REST API kullanamazsınız. Adanmış kaynaklar bir hizmet oluşturulduğunda ayrılır. Bu nedenle, temel alınan donanımın (konum veya düğüm türü) değiştirilmesi için yeni bir hizmet gerekir. Benzer şekilde, bir hizmetten diğerine bir dizin gibi içerik aktarmaya yönelik bir araç veya API yoktur.

Bir hizmette, içerik oluşturma ve Yönetim [Arama Hizmeti REST API](/rest/api/searchservice/) veya [.NET SDK](/dotnet/api/overview/azure/search.documents-readme)ile yapılır. İçerik için adanmış bir PowerShell komutu bulunmadığından, dizinler oluşturmak ve yüklemek için REST veya .NET API 'Leri çağıran PowerShell betiği yazabilirsiniz.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Sürümleri denetleme ve modülleri yükleme

Bu makaledeki örnekler etkileşimlidir ve yükseltilmiş izinler gerektirir. Azure PowerShell ( **az** Module) yüklü olmalıdır. Daha fazla bilgi için bkz. [ınstall Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>PowerShell sürüm denetimi (5,1 veya üzeri)

Yerel PowerShell, desteklenen herhangi bir işletim sisteminde 5,1 veya üzeri olmalıdır.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Azure PowerShell yükle

**Az** yüklü olup olmadığından emin değilseniz, doğrulama adımı olarak aşağıdaki komutu çalıştırın. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Bazı sistemler modülleri otomatik olarak yüklemez. Önceki komutta bir hata alırsanız, modülü yüklemeyi deneyin ve başarısız olursa, bir adım kaçırdığınızı görmek için yükleme yönergelerine geri dönün.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Tarayıcı oturum açma belirteciyle Azure 'a bağlanma

PowerShell 'deki bir aboneliğe bağlanmak için Portal oturum açma kimlik bilgilerinizi kullanabilirsiniz. Alternatif olarak, [hizmet sorumlusu ile etkileşimli olmayan kimlik doğrulaması](../active-directory/develop/howto-authenticate-service-principal-powershell.md)yapabilirsiniz.

```azurepowershell-interactive
Connect-AzAccount
```

Birden çok Azure aboneliği tutarsanız, Azure aboneliğinizi ayarlayın. Geçerli aboneliklerinizin listesini görmek için bu komutu çalıştırın.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aboneliği belirtmek için aşağıdaki komutu çalıştırın. Aşağıdaki örnekte, abonelik adı `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Bir abonelikteki hizmetleri listeleme

Aşağıdaki komutlar [**az. resources**](/powershell/module/az.resources), aboneliğinizde zaten sağlanmış olan mevcut kaynaklar ve hizmetler hakkında bilgi döndürüyor. Kaç tane arama hizmeti oluşturulduğunu bilmiyorsanız, bu komutlar bu bilgileri döndürür ve portala seyahat ister.

İlk komut tüm arama hizmetlerini döndürür.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Hizmetler listesinden belirli bir kaynakla ilgili bilgileri döndürür.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Sonuçlar aşağıdaki çıktıya benzer görünmelidir.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Al az. Search

[**Az. Search**](/powershell/module/az.search) komutları, modülü yüklenene kadar kullanılamaz.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Tüm az. Search komutlarını listeleyin

Doğrulama adımı olarak, modülde sunulan komutların bir listesini döndürün.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Sonuçlar aşağıdaki çıktıya benzer görünmelidir.

```
CommandType     Name                                               Version    Source                                                                
-----------     ----                                               -------    ------                                                                
Cmdlet          Get-AzSearchAdminKeyPair                           0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchPrivateLinkResource                    0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Get-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchAdminKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchQueryKey                               0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          New-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchPrivateEndpointConnection           0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchQueryKey                            0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchService                             0.8.0      Az.Search                                                             
Cmdlet          Remove-AzSearchSharedPrivateLinkResource           0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchPrivateEndpointConnection              0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchService                                0.8.0      Az.Search                                                             
Cmdlet          Set-AzSearchSharedPrivateLinkResource              0.8.0      Az.Search   
```

Paketin eski bir sürümüne sahipseniz, en son işlevselliği almak için modülü güncelleştirin.

```azurepowershell-interactive
Update-Module -Name Az.Search
```

## <a name="get-search-service-information"></a>Arama hizmeti bilgilerini al

**Az. Search** içeri aktarıldıktan sonra ve arama hizmetinizi içeren kaynak grubunu öğrendikten sonra, ad, bölge, katman, çoğaltma ve bölüm sayıları dahil olmak üzere hizmet tanımını döndürmek için [Get-azsearchservice](/powershell/module/az.search/get-azsearchservice) komutunu çalıştırın.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Sonuçlar aşağıdaki çıktıya benzer görünmelidir.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Hizmet oluşturma veya silme

New [**-AzSearchService**](/powershell/module/az.search/new-azsearchservice) , [Yeni bir arama hizmeti oluşturmak](search-create-service-portal.md)için kullanılır.

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3 -HostingMode Default
``` 
Sonuçlar aşağıdaki çıktıya benzer görünmelidir.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

### <a name="create-a-service-with-ip-rules"></a>IP kuralları ile hizmet oluşturma

Güvenlik gereksinimlerinize bağlı olarak, bir [IP güvenlik duvarı yapılandırılmış](service-configure-firewall.md)bir arama hizmeti oluşturmak isteyebilirsiniz. Bunu yapmak için önce IP kurallarını tanımlayın ve ardından bunları `IPRuleList` aşağıda gösterildiği gibi parametreye geçirin.

```azurepowershell-interactive
$ipRules = @([pscustomobject]@{Value="55.5.63.73"},
        [pscustomobject]@{Value="52.228.215.197"},
        [pscustomobject]@{Value="101.37.221.205"})

 New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IPRuleList $ipRules
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Sistem tarafından atanan yönetilen kimliğe sahip bir hizmet oluşturma

[Bir veri kaynağına bağlanmak için yönetilen kimlik kullanırken](search-howto-managed-identities-storage.md)olduğu gibi bazı durumlarda, [sistem tarafından atanan yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md)açmanız gerekir. Bu, komuta eklenerek yapılır `-IdentityType SystemAssigned` .

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName <resource-group-name> `
                      -Name <search-service-name> `
                      -Sku Standard `
                      -Location "West US" `
                      -PartitionCount 3 -ReplicaCount 3 `
                      -HostingMode Default `
                      -IdentityType SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Özel bir uç nokta ile hizmet oluşturma

Azure Bilişsel Arama [Özel uç noktaları](../private-link/private-endpoint-overview.md) , bir sanal ağdaki bir Istemcinin bir [özel bağlantı](../private-link/private-link-overview.md)üzerinden arama dizinindeki verilere güvenli bir şekilde erişmesini sağlar. Özel uç nokta, arama hizmetiniz için [sanal ağ adres alanından](../virtual-network/private-ip-addresses.md) bir IP adresi kullanır. İstemci ile arama hizmeti arasındaki ağ trafiği, sanal ağın ve Microsoft omurga ağındaki özel bir bağlantının üzerinde geçiş yaparken, genel İnternet 'ten etkilenme olasılığını ortadan kaldırır. Daha fazla ayrıntı için lütfen [Azure bilişsel arama özel bir uç noktası oluşturma](service-create-private-endpoint.md) belgelerine başvurun

Aşağıdaki örnek, özel bir uç nokta ile bir arama hizmeti oluşturmayı gösterir.

İlk olarak, olarak ayarlanmış bir arama hizmeti dağıtın `PublicNetworkAccess` `Disabled` .

```azurepowershell-interactive
$searchService = New-AzSearchService `
    -ResourceGroupName <resource-group-name> `
    -Name <search-service-name> `
    -Sku Standard `
    -Location "West US" `
    -PartitionCount 1 -ReplicaCount 1 `
    -HostingMode Default `
    -PublicNetworkAccess Disabled
```

Ardından, bir sanal ağ, özel ağ bağlantısı ve özel uç nokta oluşturun.

```azurepowershell-interactive
# Create the subnet
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
    -Name <subnet-name> `
    -AddressPrefix 10.1.0.0/24 `
    -PrivateEndpointNetworkPolicies Disabled 

# Create the virtual network
$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Name <virtual-network-name> `
    -AddressPrefix 10.1.0.0/16 `
    -Subnet $subnetConfig

# Create the private network connection
$privateLinkConnection = New-AzPrivateLinkServiceConnection `
    -Name <private-link-name> `
    -PrivateLinkServiceId $searchService.Id `
    -GroupId searchService

# Create the private endpoint
$privateEndpoint = New-AzPrivateEndpoint `
    -Name <private-endpoint-name> `
    -ResourceGroupName <resource-group-name> `
    -Location "West US" `
    -Subnet $virtualNetwork.subnets[0] `
    -PrivateLinkServiceConnection $privateLinkConnection
```

Son olarak, özel bir DNS bölgesi oluşturun. 

```azurepowershell-interactive
## Create private dns zone
$zone = New-AzPrivateDnsZone `
    -ResourceGroupName <resource-group-name> `
    -Name "privatelink.search.windows.net"

## Create dns network link
$link = New-AzPrivateDnsVirtualNetworkLink `
    -ResourceGroupName <resource-group-name> `
    -ZoneName "privatelink.search.windows.net" `
    -Name "myLink" `
    -VirtualNetworkId $virtualNetwork.Id

## Create DNS configuration 
$config = New-AzPrivateDnsZoneConfig `
    -Name "privatelink.search.windows.net" `
    -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group
New-AzPrivateDnsZoneGroup `
    -ResourceGroupName <resource-group-name> `
    -PrivateEndpointName <private-endpoint-name> `
    -Name 'myZoneGroup' `
    -PrivateDnsZoneConfig $config
```

PowerShell 'de özel uç noktalar oluşturma hakkında daha fazla bilgi için bu [özel bağlantı hızlı başlangıç](../private-link/create-private-endpoint-powershell.md) bölümüne bakın

### <a name="manage-private-endpoint-connections"></a>Özel uç nokta bağlantılarını yönetme

Özel bir uç nokta bağlantısı oluşturmaya ek olarak,, `Get` `Set` ve bağlantısı da yapabilirsiniz `Remove` .

[Get-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Get-AzSearchPrivateEndpointConnection) , özel bir uç nokta bağlantısını almak ve durumunu görmek için kullanılır.

```azurepowershell-interactive
Get-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name>
```

Bağlantıyı güncelleştirmek için [set-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Set-AzSearchPrivateEndpointConnection) kullanılır. Aşağıdaki örnek, reddedildi olarak bir özel uç nokta bağlantısı ayarlıyor:

```azurepowershell-interactive
Set-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name> -Status Rejected  -Description "Rejected"
```

Özel uç nokta bağlantısını silmek için [Remove-AzSearchPrivateEndpointConnection](/powershell/module/az.search/Remove-AzSearchPrivateEndpointConnection) kullanılır.

```azurepowershell-interactive
 Remove-AzSearchPrivateEndpointConnection -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <pe-connection-name>
```

## <a name="regenerate-admin-keys"></a>Yönetici anahtarlarını yeniden oluştur

[**New-AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey) , yönetici [API anahtarlarını](search-security-api-keys.md)almak için kullanılır. Kimliği doğrulanmış erişim için her bir hizmetle iki yönetici anahtarı oluşturulur. Her istekte anahtarlar gereklidir. Her iki yönetici anahtarı da işlevsel olarak eşdeğerdir, bir arama hizmetine tüm bilgileri alma veya herhangi bir nesne oluşturma ve silme imkanına tam yazma erişimi veriliyor. Diğerini değiştirirken bir tane kullanabilmeniz için iki anahtar mevcuttur. 

Ya da anahtarı olarak belirtilen tek seferde yalnızca bir tane oluşturabilirsiniz `primary` `secondary` . Kesintisiz hizmet için, birincil anahtar üzerinde çalışırken tüm istemci kodlarını ikincil anahtar kullanacak şekilde güncelleştirmeyi unutmayın. İşlemler uçuşdayken anahtarları değiştirmekten kaçının.

Beklenirken, istemci kodunu güncelleştirmeden anahtarları yeniden oluşturursanız, eski anahtarı kullanan istekler başarısız olur. Tüm yeni anahtarların yeniden oluşturulması, hizmetinizi kalıcı olarak kilitlemez ve Portal üzerinden hizmete erişmeye devam edebilirsiniz. Birincil ve ikincil anahtarları yeniden oluşturduktan sonra, yeni anahtarları kullanmak için istemci kodunu güncelleştirebilirsiniz ve işlemler buna uygun şekilde sürdürülür.

API anahtarlarının değerleri hizmet tarafından oluşturulur. Azure Bilişsel Arama kullanılmak üzere özel bir anahtar sağlayamezsiniz. Benzer şekilde, yönetici API anahtarları için Kullanıcı tanımlı ad yoktur. Anahtara yapılan başvurular veya ya da sabit dizelerdir `primary` `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Sonuçlar aşağıdaki çıktıya benzer görünmelidir. Her iki anahtar de tek seferde yalnızca birini değiştirdiğiniz halde döndürülür.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Sorgu anahtarları oluşturma veya silme

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey) , istemci uygulamalarından bir Azure bilişsel arama dizinine salt okuma erişimi Için sorgu [API anahtarları](search-security-api-keys.md) oluşturmak üzere kullanılır. Sorgu anahtarları, arama sonuçlarının alınması amacıyla belirli bir dizin için kimlik doğrulaması yapmak üzere kullanılır. Sorgu anahtarları, hizmette, dizin, veri kaynağı veya Dizin Oluşturucu gibi diğer öğelere salt okuma erişimi vermez.

Azure Bilişsel Arama kullanılmak üzere bir anahtar sağlayamezsiniz. API anahtarları hizmet tarafından oluşturulur.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Çoğaltmaları ve bölümleri ölçeklendirme

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice) , hizmet içinde yalnızca faturalanabilir kaynakları ayarlamak için [çoğaltmaları ve bölümleri artırmak veya azaltmak](search-capacity-planning.md) için kullanılır. Çoğaltmaları veya bölümleri, hem sabit hem de değişken ücretlerine sahip olan faturanızda artırır. Ek işlem gücü için geçici bir ihtiyacınız varsa, iş yükünü işlemek için çoğaltmaları ve bölümleri artırabilirsiniz. Genel Bakış portalı sayfasındaki izleme alanında sorgu gecikmesi üzerinde Kutucuklar, saniye başına sorgu ve kısıtlama, geçerli kapasitenin yeterli olup olmadığını gösterir.

Kaynak kaynağı ekleme veya kaldırma biraz zaman alabilir. Kapasite ayarlamaları arka planda gerçekleşir ve mevcut iş yüklerinin devam etmesine izin verir. Daha fazla yapılandırma gerekmeden, gelen istekler için ek kapasite kullanılır. 

Kapasitenin kaldırılması kesintiye uğratan olabilir. Atlanan isteklerin olmaması için kapasiteyi azaltmadan önce tüm dizin oluşturma ve Dizin Oluşturucu işlerinin durdurulması önerilir. Bu uygun değilse, yeni hedef düzeyleriniz ulaşılana kadar kapasiteyi artımlı olarak, bir çoğaltma ve bölüm olarak azaltmayı düşünebilirsiniz.

Komutu gönderdikten sonra, bu işlemi geçişli olarak sonlandırmak için bir yol yoktur. Sayıları yeniden ayarlamadan önce komutun bitmesini beklemeniz gerekir.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Sonuçlar aşağıdaki çıktıya benzer görünmelidir.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-a-shared-private-link-resource"></a>Paylaşılan özel bağlantı kaynağı oluşturma

Azure Bilişsel Arama API 'Leri aracılığıyla oluşturulan güvenli kaynakların özel uç noktaları, *paylaşılan özel bağlantı kaynakları* olarak adlandırılır. Bunun nedeni, [Azure özel bağlantı hizmeti](https://azure.microsoft.com/services/private-link/)ile tümleştirilmiş bir depolama hesabı gibi bir kaynağa "paylaşım" erişiminizin olması nedeniyle oluşur.

Azure Bilişsel Arama 'daki verileri indekslemek için bir Dizin Oluşturucu kullanıyorsanız ve veri kaynağınız özel bir ağ üzerinde ise, verilere ulaşmak için giden [Özel uç nokta bağlantısı](../private-link/private-endpoint-overview.md) oluşturabilirsiniz.

Azure Bilişsel Arama giden özel uç noktaları oluşturabileceğiniz Azure kaynaklarının tam bir listesi, ilgili **Grup Kimliği** değerleriyle birlikte [burada](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) bulunabilir.

[New-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/New-AzSearchSharedPrivateLinkResource) , paylaşılan özel bağlantı kaynağını oluşturmak için kullanılır. Bu komutu çalıştırmadan önce veri kaynağı için bazı yapılandırmanın gerekli olabileceğini aklınızda bulundurun.

```azurepowershell-interactive
New-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -PrivateLinkResourceId /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage -GroupId <group-id> -RequestMessage "Please approve" 
```

[Get-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Get-AzSearchSharedPrivateLinkResource) , paylaşılan özel bağlantı kaynaklarını almanızı ve durumlarını görüntülemenizi sağlar.

```azurepowershell-interactive
Get-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name>
```

Kullanılmadan önce aşağıdaki komutla bağlantıyı onaylamanız gerekir.

```azurepowershell-interactive
Approve-AzPrivateEndpointConnection `
    -Name <spl-name> `
    -ServiceName <search-service-name> `
    -ResourceGroupName <resource-group-name> `
    -Description = "Approved"
```

[Remove-AzSearchSharedPrivateLinkResource](/powershell/module/az.search/Remove-AzSearchSharedPrivateLinkResource) , paylaşılan özel bağlantı kaynağını silmek için kullanılır.

```azurepowershell-interactive
$job = Remove-AzSearchSharedPrivateLinkResource -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <spl-name> -Force -AsJob

$job | Get-Job
```

Paylaşılan özel bağlantı kaynaklarını ayarlamayla ilgili tam Ayrıntılar için, [özel bir uç nokta aracılığıyla Dizin Oluşturucu bağlantıları yapma](search-indexer-howto-access-private.md)hakkındaki belgelere bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bir [Dizin](search-what-is-an-index.md)oluşturun, PORTALı, REST API 'leri veya .NET SDK 'sını kullanarak [bir dizini sorgulayın](search-query-overview.md) .

* [Azure portal Azure Bilişsel Arama dizini oluşturma](search-get-started-portal.md)
* [Diğer hizmetlerden veri yüklemek için Dizin Oluşturucu ayarlama](search-indexer-overview.md)
* [Azure portal arama Gezgini 'ni kullanarak bir Azure Bilişsel Arama dizinini sorgulama](search-explorer.md)
* [.NET Bilişsel Arama Azure 'da kullanma](search-howto-dotnet-sdk.md)
