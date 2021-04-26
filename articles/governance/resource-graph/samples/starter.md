---
title: Başlangıç sorgu örnekleri
description: Kaynakları sayma, kaynakları sıralama veya belirli bir etiket gibi bazı başlangıç sorgularını çalıştırmak için Azure Kaynak Grafiği ' ni kullanın.
ms.date: 02/04/2021
ms.topic: sample
ms.openlocfilehash: f3ff78f52f84ad4fac74fa6e7b04de3e645a2fff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99557963"
---
# <a name="starter-resource-graph-query-samples"></a>Başlatıcı kaynak grafiği sorgu örnekleri

Azure Kaynak Grafiği ile sorguları anlamanın il adımı, [Sorgu Dili](../concepts/query-language.md)’nin temel bir anlayışıdır. [Kusto sorgu dili (KQL)](/azure/kusto/query/index)hakkında bilginiz yoksa, aradığınız kaynaklara yönelik isteklerin nasıl oluşturulduğunu anlamak için [KQL öğreticisini](/azure/kusto/query/tutorial) gözden geçirmeniz önerilir.

Aşağıdaki başlangıç sorgularını inceleyeceğiz:

- [Azure kaynaklarını sayma](#count-resources)
- [Anahtar Kasası kaynaklarını sayma](#count-keyvaults)
- [Ada göre sıralanan kaynakları Listele](#list-resources)
- [Ada göre sıralanmış tüm sanal makineleri azalan sırada göster](#show-vms)
- [Ada ve işletim sistemi türlerine göre ilk beş sanal makineyi göster](#show-sorted)
- [Sanal makineleri işletim sistemi türüne göre sayma](#count-os)
- [Depolama içeren kaynakları göster](#show-storage)
- [Tüm genel IP adreslerini listele](#list-publicip)
- [Aboneliğe göre yapılandırılmış IP adreslerine sahip kaynakları sayma](#count-resources-by-ip)
- [Belirli bir etiket değerine sahip kaynakları listeleyin](#list-tag)
- [Belirli bir etiket değerine sahip tüm depolama hesaplarını Listele](#list-specific-tag)
- [Tüm etiketleri ve bunların değerlerini listeleyin](#list-all-tag-values)
- [İlişkilendirilmemiş ağ güvenlik gruplarını göster](#unassociated-nsgs)
- [Azure Advisor 'dan maliyet tasarrufu Özeti alın](#advisor-savings)
- [Konuk yapılandırma ilkelerinin kapsamındaki makineleri say](#count-gcmachines)

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free) oluşturun.

## <a name="language-support"></a>Dil desteği

Azure CLI (bir uzantı yoluyla) ve Azure PowerShell (bir modül yoluyla), Azure Kaynak Grafiği’ni destekler. Aşağıdaki sorgulardan herhangi birini çalıştırmadan önce ortamınızın hazır olduğundan emin olun. Seçtiğiniz kabuk ortamını yükleme ve doğrulama adımları için, bkz. [Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) ve [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module).

## <a name="count-azure-resources"></a><a name="count-resources"></a>Azure kaynaklarını sayma

Bu sorgu, erişiminiz bulunan aboneliklerde var olan Azure kaynaklarının sayısını döndürür. Ayrıca, seçtiğiniz kabukta uygun Azure Kaynak Grafı bileşenlerinin yüklü ve çalışma düzeninde olduğunu doğrulamak için iyi bir sorgudur.

```kusto
Resources
| summarize count()
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20summarize%20count%28%29" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-key-vault-resources"></a><a name="count-keyvaults"></a>Anahtar Kasası kaynaklarını sayma

Bu sorgu `count` `summarize` döndürülen kayıt sayısını saymak için yerine kullanır. Sayıma yalnızca anahtar kasaları dahildir.

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27microsoft.keyvault%2Fvaults%27%0D%0A%7C%20count" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-resources-sorted-by-name"></a><a name="list-resources"></a>Ada göre sıralanmış kaynakları listeleme

Bu sorgu herhangi bir türdeki kaynakların yalnızca **name**, **type** ve **location** özelliklerini döndürür. `order by` kullanarak özellikleri **name** özelliğine göre artan (`asc`) düzende sıralar.

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20type%2C%20location%0D%0A%7C%20order%20by%20name%20asc" target="_blank">Portal.Azure.cn</a>

---

## <a name="show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms"></a>İsme göre sıralanmış tüm sanal makineleri azalan düzende göster

Yalnızca sanal makineleri (`Microsoft.Compute/virtualMachines` türündeki) listelemek için sonuçlarda **type** özelliğini eşleştirebiliriz. Önceki sorguya benzer şekilde `desc`, `order by`’yi azalan olması için değiştirir. Eşleme türündeki `=~`, Kaynak Grafiği’nin büyük/küçük harfe duyarlı olmadığını bildirir.

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20project%20name%2C%20location%2C%20type%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20order%20by%20name%20desc" target="_blank">Portal.Azure.cn</a>

---

## <a name="show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted"></a>Ada ve işletim sistemi türüne göre ilk beş sanal makineyi gösterme

Bu sorgu `top`’i yalnızca ada göre sıralanmış beş eşleşen kaydı almak için kullanır. Azure kaynağının türü `Microsoft.Compute/virtualMachines`’dir. `project`, Azure Kaynak Grafiği’ne hangi özelliklerin dahil edileceğini bildirir.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%0D%0A%7C%20top%205%20by%20name%20desc" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-virtual-machines-by-os-type"></a><a name="count-os"></a>Sanal makineleri işletim sistemi türüne göre sayma

Bir önceki sorguyu oluşturmada, hâlâ `Microsoft.Compute/virtualMachines` türündeki Azure kaynaklarına göre sınırlandırıyoruz, ancak geri gönderilen kayıtların sayısını sınırlandırmıyoruz.
Bunu yerine, değerleri (bu örnekte `properties.storageProfile.osDisk.osType`) özelliğe göre gruplandırma ve toplamayı tanımlamak için `summarize` ve `count()` kullandık. Bu dizenin tam nesnede nasıl göründüğüne ilişkin bir örnek için, bkz. [ kaynakları keşfetme - sanal makine bulma](../concepts/explore-resources.md#virtual-machine-discovery).

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28properties.storageProfile.osDisk.osType%29" target="_blank">Portal.Azure.cn</a>

---

Aynı sorguyu yazmanın farklı bir yolu da, bir özelliği `extend` yapmak ve sorgu içinde kullanılması için, bu durumda **os** olan geçici bir ad verilmesidir. **os** daha sonra, bir önceki örnekte olduğu gibi `summarize` ve `count()` tarafından kullanılır.

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Compute%2FvirtualMachines%27%0D%0A%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%0D%0A%7C%20summarize%20count%28%29%20by%20tostring%28os%29" target="_blank">Portal.Azure.cn</a>

---

> [!NOTE]
> `=~`, büyük/küçük harfe duyarlı olmayan eşleşmeye izin verirken, sorgudaki özelliklerin (örneğin, **properties.storageProfile.osDisk.osType** gibi) kullanımının, durumun doğru olmasını gerektirdiğini unutmayın. Özelliğin durumu yanlış ise, null veya hatalı bir değer döndürülür ve gruplandırma veya özetleme yanlış olur.

## <a name="show-resources-that-contain-storage"></a><a name="show-storage"></a>Depolama içeren kaynakları göster

Eşleştirilecek türü şekilde açık olarak tanımlamak yerine, bu örnek sorgu, **depo** sözcüğünü `contains` eden tüm Azure kaynaklarını bulur.

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27storage%27%20%7C%20distinct%20type" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-all-public-ip-addresses"></a><a name="list-publicip"></a>Tüm genel IP adreslerini listele

Önceki sorguya benzer şekilde, **publicIPAddresses** sözcüğünü içeren bir tür olan her şeyi bulun.
Bu sorgu yalnızca **Properties. IPAddress**' in yalnızca Properties. IPAddress 
 `isnotempty` ve  `limit` en üstteki sonuçları geri döndürecek sonuçları dahil etmek için bu modele genişletilir.
100. Seçtiğiniz kabuğa bağlı olarak tırnak işaretlerinin dışına çıkmanız gerekebilir.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20project%20properties.ipAddress%0D%0A%7C%20limit%20100" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip"></a>Aboneliğe göre yapılandırılmış IP adreslerine sahip kaynakları sayma

Önceki örnek sorguyu kullanarak ve `summarize` ile `count()` ekleyerek, yapılandırılmış IP adreslerine sahip kaynakların aboneliğe göre listesini elde edebiliriz.

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20contains%20%27publicIPAddresses%27%20and%20isnotempty%28properties.ipAddress%29%0D%0A%7C%20summarize%20count%20%28%29%20by%20subscriptionId" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-resources-with-a-specific-tag-value"></a><a name="list-tag"></a>Belirli bir etiket değerine sahip kaynakları listeleme

Sonuçları, etiket gibi Azure kaynak türünden başka özelliklere göre de sınırlandırabiliriz. Bu örnekte, Azure kaynaklarını **Dahili** değerine sahip **Ortam** etiket adıyla filtreliyoruz.

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name" target="_blank">Portal.Azure.cn</a>

---

Kaynağın sahip olduğu etiketleri ve değerlerini de sağlamak için `project` sözcüğüne **tags** özelliğini ekleyin.

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20tags.environment%3D~%27internal%27%0D%0A%7C%20project%20name%2C%20tags" target="_blank">Portal.Azure.cn</a>

---

## <a name="list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag"></a>Belirli bir değerine sahip tüm depolama hesaplarını listeleme

Önceki örneğin filtre işleviyle birleştirerek Azure kaynak türünü **type** özelliğine göre filtreleyin. Bu sorgu da aramayı belirli bir etiket adına ve değerine sahip olan belirli Azure kaynağı türleriyle sınırlar.

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%27Microsoft.Storage%2FstorageAccounts%27%0D%0A%7C%20where%20tags%5B%27tag%20with%20a%20space%27%5D%3D%3D%27Custom%20value%27" target="_blank">Portal.Azure.cn</a>

---

> [!NOTE]
> Bu örnek, eşleştirme için `=~` koşullu yerine `==` kullanır. `==` büyük küçük harfe duyarlı bir eşleşmedir.

## <a name="list-all-tags-and-their-values"></a><a name="list-all-tag-values"></a>Tüm etiketleri ve bunların değerlerini listeleyin

Bu sorgu, yönetim gruplarında, aboneliklerde ve kaynaklardaki etiketleri değerleriyle birlikte listeler.
Sorgu ilk olarak etiketlerin sınırlarını `isnotempty()` , dahil edilen alanları yalnızca içindeki _etiketleri_ dahil ederek ve `project` `mvexpand` `extend` özellik çantasından eşleştirilmiş verileri almak için kısıtlar. Daha sonra `union` , _kaynakları_ _resourcecontainers_ 'dan aynı sonuçlara birleştirmek için kullanır ve bu etiketlerin getirilmesi için geniş kapsamlı kapsam sağlar. Son olarak, sonuçları `distinct` eşleştirilmiş verilerle kısıtlar ve sistem gizli etiketlerini dışlar.

```kusto
ResourceContainers 
| where isnotempty(tags)
| project tags
| mvexpand tags
| extend tagKey = tostring(bag_keys(tags)[0])
| extend tagValue = tostring(tags[tagKey])
| union (
    resources
    | where isnotempty(tags)
    | project tags
    | mvexpand tags
    | extend tagKey = tostring(bag_keys(tags)[0])
    | extend tagValue = tostring(tags[tagKey])
)
| distinct tagKey, tagValue
| where tagKey !startswith "hidden-"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union (resources | where notempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "ResourceContainers | where isnotempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) | union (resources | where notempty(tags) | project tags | mvexpand tags | extend tagKey = tostring(bag_keys(tags)[0]) | extend tagValue = tostring(tags[tagKey]) ) | distinct tagKey, tagValue | where tagKey !startswith "hidden-""
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/ResourceContainers%20%0A%7C%20where%20isnotempty%28tags%29%0A%7C%20project%20tags%0A%7C%20mvexpand%20tags%0A%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%7C%20union%20%28%0A%20%20%20%20resources%0A%20%20%20%20%7C%20where%20isnotempty%28tags%29%0A%20%20%20%20%7C%20project%20tags%0A%20%20%20%20%7C%20mvexpand%20tags%0A%20%20%20%20%7C%20extend%20tagKey%20%3D%20tostring%28bag_keys%28tags%29%5B0%5D%29%0A%20%20%20%20%7C%20extend%20tagValue%20%3D%20tostring%28tags%5BtagKey%5D%29%0A%29%0A%7C%20distinct%20tagKey%2C%20tagValue%0A%7C%20where%20tagKey%20%21startswith%20%22hidden-%22" target="_blank">Portal.Azure.cn</a>

---

## <a name="show-unassociated-network-security-groups"></a><a name="unassociated-nsgs"></a>İlişkilendirilmemiş ağ güvenlik gruplarını göster

Bu sorgu, bir ağ arabirimiyle veya alt ağıyla ilişkilendirilmemiş ağ güvenlik gruplarını (NSG 'ler) döndürür.

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%0D%0A%7C%20where%20type%20%3D~%20%22microsoft.network%2Fnetworksecuritygroups%22%20and%20isnull%28properties.networkInterfaces%29%20and%20isnull%28properties.subnets%29%0D%0A%7C%20project%20name%2C%20resourceGroup%0D%0A%7C%20sort%20by%20name%20asc" target="_blank">Portal.Azure.cn</a>

---

## <a name="get-cost-savings-summary-from-azure-advisor"></a><a name="advisor-savings"></a>Azure Advisor 'dan maliyet tasarrufu Özeti alın

Bu sorgu, her [Azure Advisor](../../../advisor/advisor-overview.md) önerisi için maliyet tasarrufunu özetler.

```kusto
advisorresources
| where type == 'microsoft.advisor/recommendations'
| where properties.category == 'Cost'
| extend
    resources = tostring(properties.resourceMetadata.resourceId),
    savings = todouble(properties.extendedProperties.savingsAmount),
    solution = tostring(properties.shortDescription.solution),
    currency = tostring(properties.extendedProperties.savingsCurrency)
| summarize
    dcount(resources), 
    bin(sum(savings), 0.01)
    by solution, currency
| project solution, dcount_resources, sum_savings, currency
| order by sum_savings desc
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "advisorresources | where type == 'microsoft.advisor/recommendations' | where properties.category == 'Cost' | extend resources = tostring(properties.resourceMetadata.resourceId), savings = todouble(properties.extendedProperties.savingsAmount), solution = tostring(properties.shortDescription.solution), currency = tostring(properties.extendedProperties.savingsCurrency) | summarize dcount(resources), bin(sum(savings), 0.01) by solution, currency | project solution, dcount_resources, sum_savings, currency | order by sum_savings desc"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">portal.azure.com</a>
- Azure Kamu Portalı: <a href="https://portal.azure.us/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">Portal.Azure.us</a>
- Azure Çin 21Vianet Portalı: <a href="https://portal.azure.cn/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/advisorresources%0D%0A%7C%20where%20type%20%3D%3D%20%27microsoft.advisor%2Frecommendations%27%0D%0A%7C%20where%20properties.category%20%3D%3D%20%27Cost%27%0D%0A%7C%20extend%0D%0A%20%20%20%20resources%20%3D%20tostring%28properties.resourceMetadata.resourceId%29%2C%0D%0A%20%20%20%20savings%20%3D%20todouble%28properties.extendedProperties.savingsAmount%29%2C%0D%0A%20%20%20%20solution%20%3D%20tostring%28properties.shortDescription.solution%29%2C%0D%0A%20%20%20%20currency%20%3D%20tostring%28properties.extendedProperties.savingsCurrency%29%0D%0A%7C%20summarize%0D%0A%20%20%20%20dcount%28resources%29%2C%20%0D%0A%20%20%20%20bin%28sum%28savings%29%2C%200.01%29%0D%0A%20%20%20%20by%20solution%2C%20currency%0D%0A%7C%20project%20solution%2C%20dcount_resources%2C%20sum_savings%2C%20currency%0D%0A%7C%20order%20by%20sum_savings%20desc" target="_blank">Portal.Azure.cn</a>

---

## <a name="count-machines-in-scope-of-guest-configuration-policies"></a><a name="count-gcmachines"></a>Konuk yapılandırma ilkelerinin kapsamındaki makineleri say

[Azure Ilke Konuk yapılandırması](../../policy/concepts/guest-configuration.md) atamaları Için kapsamdaki Azure sanal makinelerinin ve Arc bağlantılı sunucuların sayısını görüntüler.

```kusto
GuestConfigurationResources
| extend vmid = split(properties.targetResourceId,'/')
| mvexpand properties.latestAssignmentReport.resources
| where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.'
| project machine = tostring(vmid[(-1)]),
    type = tostring(vmid[(-3)])
| distinct machine, type
| summarize count() by type
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "GuestConfigurationResources | extend vmid = split(properties.targetResourceId,'/') | mvexpand properties.latestAssignmentReport.resources | where properties_latestAssignmentReport_resources.resourceId != 'Invalid assignment package.' | project machine = tostring(vmid[(-1)]), type = tostring(vmid[(-3)]) | distinct machine, type | summarize count() by type"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

:::image type="icon" source="../media/resource-graph-small.png"::: Azure Kaynak Grafiği Gezgini 'nde bu sorguyu deneyin:

- Azure portalı: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/GuestConfigurationResources%20%7C%20extend%20vmid%20%3D%20split(properties.targetResourceId%2C%22%2F%22)%20%7C%20mvexpand%20properties.latestAssignmentReport.resources%20%7C%20where%20properties_latestAssignmentReport_resources.resourceId%20!%3D%20'Invalid%20assignment%20package.'%20%7C%20project%20machine%20%3D%20tostring(vmid%5B(-1)%5D)%2C%20type%20%3D%20tostring(vmid%5B(-3)%5D)%20%7C%20distinct%20machine%2C%20type%20%7C%20summarize%20count()%20by%20type" target="_blank">portal.azure.com</a>

---

## <a name="next-steps"></a>Sonraki adımlar

- [Sorgu dili](../concepts/query-language.md)hakkında daha fazla bilgi edinin.
- [Kaynakları araştırma](../concepts/explore-resources.md)hakkında daha fazla bilgi edinin.
- [Gelişmiş sorguların](advanced.md)örneklerine bakın.