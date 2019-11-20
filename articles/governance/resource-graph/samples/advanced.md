---
title: Gelişmiş sorgu örnekleri
description: Sanal makine ölçek kümesi kapasitesi, kullanılan tüm etiketleri listeleme ve normal ifadelerle eşleşen sanal makineler dahil bazı gelişmiş sorguları çalıştırmak için Azure Kaynak Grafiği ' ni kullanın.
ms.date: 10/21/2019
ms.topic: quickstart
ms.openlocfilehash: e09db4630fd1ef8acc06538c7599feddbe991900
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958702"
---
# <a name="advanced-resource-graph-queries"></a>Gelişmiş Kaynak Grafiği sorguları

Azure Kaynak Grafiği ile sorguları anlamanın il adımı, [Sorgu Dili](../concepts/query-language.md)’nin temel bir anlayışıdır. [Azure Veri Gezgini](../../../data-explorer/data-explorer-overview.md)’ne önceden aşina değilseniz, aradığınız kaynaklar için isteklerin nasıl oluşturulacağını anlamak üzere temelleri gözden geçirmeniz önerilir.

Aşağıdaki gelişmiş sorguları inceleyeceğiz:

> [!div class="checklist"]
> - [Her kaynak türü için API sürümünü göster](#apiversion)
> - [Sanal makine ölçek kümesi kapasitesini ve boyutunu al](#vmss-capacity)
> - [Sütunları sonuçlardan kaldır](#remove-column)
> - [Tüm etiket adlarını listeleme](#list-all-tags)
> - [Normal ifade tarafından eşleştirilen sanal makineler](#vm-regex)
> - [Belirli yazma konumlarına sahip Cosmos DB listeleyin](#mvexpand-cosmosdb)
> - [Abonelik adı olan Anahtar Kasası](#join)
> - [SQL veritabanlarını ve bunların elastik havuzlarını listeleyin](#join-sql)
> - [Sanal makineleri ağ arabirimiyle ve genel IP 'si ile listeleyin](#join-vmpip)
> - [Kaynak grubunda belirli bir etikete sahip depolama hesaplarını bulma](#join-findstoragetag)
> - [İki sorgudan alınan sonuçları tek bir sonuç halinde birleştirin](#unionresults)
> - [Ad ile kiracı ve abonelik adlarını ekleyin](#displaynames)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="language-support"></a>Dil desteği

Azure CLI (bir uzantı yoluyla) ve Azure PowerShell (bir modül yoluyla), Azure Kaynak Grafiği’ni destekler. Aşağıdaki sorgulardan herhangi birini çalıştırmadan önce ortamınızın hazır olduğundan emin olun. Seçtiğiniz kabuk ortamını yükleme ve doğrulama adımları için, bkz. [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) ve [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="a-nameapiversion-show-resource-types-and-api-versions"></a><a name="apiversion" />kaynak türlerini ve API sürümlerini gösterme

Kaynak Grafiği öncelikle bir güncelleştirme sırasında kaynak özelliklerini `GET` için bir kaynak sağlayıcının API 'sinin en son Önizleme olmayan sürümünü kullanır. Bazı durumlarda kullanılan API sürümü, sonuçlarda daha güncel veya yaygın olarak kullanılan özellikler sağlamak için geçersiz kılındı. Aşağıdaki sorgu, her kaynak türü üzerinde Özellikler toplamak için kullanılan API sürümünün ayrıntılarını alır:

```kusto
Resources
| distinct type, apiVersion
| where isnotnull(apiVersion)
| order by type asc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | distinct type, apiVersion | where isnotnull(apiVersion) | order by type asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20distinct%20type%2C%20apiVersion%20%7C%20where%20isnotnull(apiVersion)%20%7C%20order%20by%20type%20asc" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namevmss-capacity-get-virtual-machine-scale-set-capacity-and-size"></a><a name="vmss-capacity" />sanal makine ölçek kümesi kapasitesini ve boyutunu al

Bu sorgu, sanal makine ölçek kümesi kaynaklarını arar ve sanal makine boyutu ve ölçek kümesinin kapasitesini içeren çeşitli ayrıntıları alır. Sorgu, sıralanabilmesi için ve kapasiteyi bir sayıya dönüştürmek amacıyla `toint()` işlevini kullanır. Son olarak sütunlar, özel olarak adlandırılmış özellikler kullanılarak yeniden adlandırılır.

```kusto
Resources
| where type=~ 'microsoft.compute/virtualmachinescalesets'
| where name contains 'contoso'
| project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name
| order by Capacity desc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~ 'microsoft.compute/virtualmachinescalesets' | where name contains 'contoso' | project subscriptionId, name, location, resourceGroup, Capacity = toint(sku.capacity), Tier = sku.name | order by Capacity desc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~%20'microsoft.compute%2Fvirtualmachinescalesets'%20%7C%20where%20name%20contains%20'contoso'%20%7C%20project%20subscriptionId%2C%20name%2C%20location%2C%20resourceGroup%2C%20Capacity%20%3D%20toint(sku.capacity)%2C%20Tier%20%3D%20sku.name%20%7C%20order%20by%20Capacity%20desc" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-nameremove-column-remove-columns-from-results"></a>sütunları sonuçlardan kaldırmak <a name="remove-column" />

Aşağıdaki sorgu, kaynakları aboneliğe göre saymak için `summarize` kullanır, _Resourcecontainers_ tablosundan abonelik ayrıntıları ile birleştirmek için `join` ve daha sonra bazı sütunları kaldırmak için `project-away`.

```kusto
Resources
| summarize resourceCount=count() by subscriptionId
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project-away subscriptionId, subscriptionId1
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize resourceCount=count() by subscriptionId | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId| project-away subscriptionId, subscriptionId1"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20resourceCount%3Dcount()%20by%20subscriptionId%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%7C%20project-away%20subscriptionId%2C%20subscriptionId1" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namelist-all-tags-list-all-tag-names"></a>Tüm etiket adlarını listelemek <a name="list-all-tags" />

Bu sorgu etiketle başlar ve tüm benzersiz etiket adlarını ve bunlara karşılık gelen türleri listeleyen bir JSON nesnesi oluşturur.

```kusto
Resources
| project tags
| summarize buildschema(tags)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project tags | summarize buildschema(tags)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project tags | summarize buildschema(tags)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20tags%20%7C%20summarize%20buildschema(tags)" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namevm-regex-virtual-machines-matched-by-regex"></a>Regex ile eşleşen sanal makineler <a name="vm-regex" />

Bu sorgu, [normal ifadeyle](/dotnet/standard/base-types/regular-expression-language-quick-reference) (_regex_ olarak bilinir) eşleşen sanal makineleri arar. **\@Regex** , eşleşen Regex tanımlamanızı sağlar, bu `^Contoso(.*)[0-9]+$`.
Bu normal ifade tanımı şöyle açıklanmıştır:

- `^` - Eşleşme dizenin başında başlamalıdır.
- `Contoso` - Büyük/küçük harfe duyarlı dize.
- `(.*)` - Bir alt ifade eşleşmesi:
  - `.` - Herhangi bir tek karakterle eşleşir (yeni bir satır hariç).
  - `*` - Önceki öğeyle sıfır veya daha fazla kez eşleşir.
- `[0-9]` - 0’dan 9’a kadar olan sayılar için karakter grubu eşleşmesi.
- `+` - Önceki öğeyle bir veya daha fazla kez eşleşir.
- `$` - Önceki öğenin eşleşmesi dizenin sonunda gerçekleşmelidir.

Ada göre eşleşmeden sonra sorgu, adı ve siparişleri ada göre artan şekilde yansıtır.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$'
| project name
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' and name matches regex @'^Contoso(.*)[0-9]+$' | project name | order by name asc"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20and%20name%20matches%20regex%20%40'%5EContoso(.*)%5B0-9%5D%2B%24'%20%7C%20project%20name%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namemvexpand-cosmosdb-list-cosmos-db-with-specific-write-locations"></a>belirli yazma konumlarına sahip <a name="mvexpand-cosmosdb" />listesi Cosmos DB

Aşağıdaki sorgu Cosmos DB kaynakları kısıtlar, **Properties. writelocations**özellik paketini genişletmek için `mv-expand` kullanır, ardından projeye özel alanlar ve sonuçları **Özellikler. Writelocations. locationname** değerleriyle sınırlayın ' Doğu ABD ' veya ' Batı ABD ' ile eşleşiyor.

```kusto
Resources
| where type =~ 'microsoft.documentdb/databaseaccounts'
| project id, name, writeLocations = (properties.writeLocations)
| mv-expand writeLocations
| project id, name, writeLocation = tostring(writeLocations.locationName)
| where writeLocation in ('East US', 'West US')
| summarize by id, name
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.documentdb/databaseaccounts' | project id, name, writeLocations = (properties.writeLocations) | mv-expand writeLocations | project id, name, writeLocation = tostring(writeLocations.locationName) | where writeLocation in ('East US', 'West US') | summarize by id, name"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.documentdb%2Fdatabaseaccounts'%20%7C%20project%20id%2C%20name%2C%20writeLocations%20%3D%20(properties.writeLocations)%20%7C%20mv-expand%20writeLocations%20%7C%20project%20id%2C%20name%2C%20writeLocation%20%3D%20tostring(writeLocations.locationName)%20%7C%20where%20writeLocation%20in%20('East%20US'%2C%20'West%20US')%20%7C%20summarize%20by%20id%2C%20name" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namejoin-key-vault-with-subscription-name"></a>Abonelik adı ile anahtar kasasını <a name="join" />

Aşağıdaki sorgu `join`karmaşık bir kullanımını gösterir. Sorgu, birleştirilmiş tabloyu abonelik kaynakları ile sınırlandırır ve `project` yalnızca özgün alan _SubscriptionID_ ve _ad_ alanı, _SubName_olarak yeniden adlandırılır. Alan yeniden adlandırma `join`, _kaynak_içinde zaten mevcut olduğundan, _name1_ olarak eklenmesini önler. Özgün tablo `where` ile filtrelenmiştir ve aşağıdaki `project` her iki tablodan sütun içerir. Sorgu sonucu, türünü, anahtar kasasının adını ve içindeki aboneliğin adını gösteren tek bir Anahtar Kasası.

```kusto
Resources
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| where type == 'microsoft.keyvault/vaults'
| project type, name, SubName
| limit 1
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId | where type == 'microsoft.keyvault/vaults' | project type, name, SubName| limit 1"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20join%20(ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions'%20%7C%20project%20SubName%3Dname%2C%20subscriptionId)%20on%20subscriptionId%20%7C%20where%20type%20%3D%3D%20'microsoft.keyvault%2Fvaults'%20%7C%20project%20type%2C%20name%2C%20SubName%7C%20limit%201" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namejoin-sql-list-sql-databases-and-their-elastic-pools"></a>SQL veritabanları ve elastik havuzlarını listelemek <a name="join-sql" />

Aşağıdaki sorgu, varsa, SQL veritabanı kaynaklarını ve bunlarla ilişkili elastik havuzları bir araya getirmek için **soltouter** `join` kullanır.

```kusto
Resources
| where type =~ 'microsoft.sql/servers/databases'
| project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId))
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.sql/servers/elasticpools'
    | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state)
on elasticPoolId
| project-away elasticPoolId1
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.sql/servers/databases' | project databaseId = id, databaseName = name, elasticPoolId = tolower(tostring(properties.elasticPoolId)) | join kind=leftouter ( Resources | where type =~ 'microsoft.sql/servers/elasticpools' | project elasticPoolId = tolower(id), elasticPoolName = name, elasticPoolState = properties.state) on elasticPoolId | project-away elasticPoolId1"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Fdatabases'%20%7C%20project%20databaseId%20%3D%20id%2C%20databaseName%20%3D%20name%2C%20elasticPoolId%20%3D%20tolower(tostring(properties.elasticPoolId))%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.sql%2Fservers%2Felasticpools'%20%7C%20project%20elasticPoolId%20%3D%20tolower(id)%2C%20elasticPoolName%20%3D%20name%2C%20elasticPoolState%20%3D%20properties.state)%20on%20elasticPoolId%20%7C%20project-away%20elasticPoolId1" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namejoin-vmpip-list-virtual-machines-with-their-network-interface-and-public-ip"></a>sanal makineleri ağ arabirimiyle ve genel IP ile listelemek <a name="join-vmpip" />

Bu sorgu, sanal makineleri, ilgili ağ arabirimlerini ve bu ağ arabirimleriyle ilgili genel IP adreslerini birlikte getirmek için iki **soltouter** `join` komutu kullanır.

```kusto
Resources
| where type =~ 'microsoft.compute/virtualmachines'
| extend nics=array_length(properties.networkProfile.networkInterfaces) 
| mvexpand nic=properties.networkProfile.networkInterfaces 
| where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) 
| project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) 
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/networkinterfaces'
    | extend ipConfigsCount=array_length(properties.ipConfigurations) 
    | mvexpand ipconfig=properties.ipConfigurations 
    | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true'
    | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id))
on nicId
| project-away nicId1
| summarize by vmId, vmName, vmSize, nicId, publicIpId
| join kind=leftouter (
    Resources
    | where type =~ 'microsoft.network/publicipaddresses'
    | project publicIpId = id, publicIpAddress = properties.ipAddress)
on publicIpId
| project-away publicIpId1
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.compute/virtualmachines' | extend nics=array_length(properties.networkProfile.networkInterfaces) | mvexpand nic=properties.networkProfile.networkInterfaces | where nics == 1 or nic.properties.primary =~ 'true' or isempty(nic) | project vmId = id, vmName = name, vmSize=tostring(properties.hardwareProfile.vmSize), nicId = tostring(nic.id) | join kind=leftouter ( Resources | where type =~ 'microsoft.network/networkinterfaces' | extend ipConfigsCount=array_length(properties.ipConfigurations) | mvexpand ipconfig=properties.ipConfigurations | where ipConfigsCount == 1 or ipconfig.properties.primary =~ 'true' | project nicId = id, publicIpId = tostring(ipconfig.properties.publicIPAddress.id)) on nicId | project-away nicId1 | summarize by vmId, vmName, vmSize, nicId, publicIpId | join kind=leftouter ( Resources | where type =~ 'microsoft.network/publicipaddresses' | project publicIpId = id, publicIpAddress = properties.ipAddress) on publicIpId | project-away publicIpId1"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.compute%2Fvirtualmachines'%20%7C%20extend%20nics%3Darray_length(properties.networkProfile.networkInterfaces)%20%7C%20mvexpand%20nic%3Dproperties.networkProfile.networkInterfaces%20%7C%20where%20nics%20%3D%3D%201%20or%20nic.properties.primary%20%3D~%20'true'%20or%20isempty(nic)%20%7C%20project%20vmId%20%3D%20id%2C%20vmName%20%3D%20name%2C%20vmSize%3Dtostring(properties.hardwareProfile.vmSize)%2C%20nicId%20%3D%20tostring(nic.id)%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworkinterfaces'%20%7C%20extend%20ipConfigsCount%3Darray_length(properties.ipConfigurations)%20%7C%20mvexpand%20ipconfig%3Dproperties.ipConfigurations%20%7C%20where%20ipConfigsCount%20%3D%3D%201%20or%20ipconfig.properties.primary%20%3D~%20'true'%20%7C%20project%20nicId%20%3D%20id%2C%20publicIpId%20%3D%20tostring(ipconfig.properties.publicIPAddress.id))%20on%20nicId%20%7C%20project-away%20nicId1%20%7C%20summarize%20by%20vmId%2C%20vmName%2C%20vmSize%2C%20nicId%2C%20publicIpId%20%7C%20join%20kind%3Dleftouter%20(%20Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fpublicipaddresses'%20%7C%20project%20publicIpId%20%3D%20id%2C%20publicIpAddress%20%3D%20properties.ipAddress)%20on%20publicIpId%20%7C%20project-away%20publicIpId1" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namejoin-findstoragetag-find-storage-accounts-with-a-specific-tag-on-the-resource-group"></a>kaynak grubunda belirli bir etikete sahip depolama hesaplarını bulma <a name="join-findstoragetag" />

Aşağıdaki sorgu, belirtilen etiket adı ve etiket değerine sahip kaynak gruplarıyla depolama hesaplarını bağlamak için bir **iç** `join` kullanır.

```kusto
Resources
| where type =~ 'microsoft.storage/storageaccounts'
| join kind=inner (
    ResourceContainers
    | where type =~ 'microsoft.resources/subscriptions/resourcegroups'
    | where tags['key1'] == 'value1'
    | project subscriptionId, resourceGroup)
on subscriptionId, resourceGroup
| project-away subscriptionId1, resourceGroup1
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.storage/storageaccounts' | join kind=inner ( ResourceContainers | where type =~ 'microsoft.resources/subscriptions/resourcegroups' | where tags['key1'] == 'value1' | project subscriptionId, resourceGroup) on subscriptionId, resourceGroup | project-away subscriptionId1, resourceGroup1"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.storage%2Fstorageaccounts'%20%7C%20join%20kind%3Dinner%20(%20ResourceContainers%20%7C%20where%20type%20%3D~%20'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20where%20tags%5B'key1'%5D%20%3D%3D%20'value1'%20%7C%20project%20subscriptionId%2C%20resourceGroup)%20on%20subscriptionId%2C%20resourceGroup%20%7C%20project-away%20subscriptionId1%2C%20resourceGroup1" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-nameunionresults-combine-results-from-two-queries-into-a-single-result"></a>iki sorgudan sonuçları tek bir sonuç halinde birleştirme <a name="unionresults" />

Aşağıdaki sorgu, _Resourcecontainers_ tablosundan sonuçları almak ve _kaynaklar_ tablosundan sonuçlara eklemek için `union` kullanır.

```kusto
ResourceContainers
| where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5
| union  (Resources | project name, type | limit 5)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where type=='microsoft.resources/subscriptions/resourcegroups' | project name, type  | limit 5 | union  (Resources | project name, type | limit 5)"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

![Kaynak Grafiği Gezgini simgesi](../media/resource-graph-small.png) Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%7C%20where%20type%3D%3D'microsoft.resources%2Fsubscriptions%2Fresourcegroups'%20%7C%20project%20name%2C%20type%20%20%7C%20limit%205%20%7C%20union%20%20(Resources%20%7C%20project%20name%2C%20type%20%7C%20limit%205)" target="_blank">portal.azure.com</a> ![bağlantıyı yeni pencere simgesinde aç](../../media/new-window.png)

---

## <a name="a-namedisplaynames-include-the-tenant-and-subscription-names-with-displaynames"></a><a name="displaynames" />, ad ile kiracı ve abonelik adlarını Içerir

Bu sorgu, sonuçlara **subscriptiondisplayname** ve **Tenantdisplayname** eklemek için _DisplayNames_ seçeneğiyle birlikte yeni **içerme** parametresini kullanır. Bu parametre yalnızca Azure CLı ve Azure PowerShell için kullanılabilir.

```azurecli-interactive
az graph query -q "limit 1" --include displayNames
```

```azurepowershell-interactive
Search-AzGraph -Query "limit 1" -Include DisplayNames
```

> [!NOTE]
> Sorgu döndürülen özellikleri belirtmek için **Proje** kullanmıyorsa, **Subscriptiondisplayname** ve **tenantdisplayname** , sonuçlara otomatik olarak eklenir.
> Sorgu **projeyi**kullanıyorsa, _DisplayName_ alanlarının her biri **projeye** açık bir şekilde eklenmelidir veya **Include** parametresi kullanılsa bile sonuçlarda döndürülmezler.

## <a name="next-steps"></a>Sonraki adımlar

- Bkz. [Başlangıç sorguları](starter.md)örnekleri.
- [Sorgu dili](../concepts/query-language.md)hakkında daha fazla bilgi edinin.
- [Kaynakları araştırma](../concepts/explore-resources.md)hakkında daha fazla bilgi edinin.