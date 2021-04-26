---
title: Azure ağ Izleyicisi örneği oluşturma | Microsoft Docs
description: Azure bölgesinde Azure portal veya diğer teknolojileri kullanarak bir Azure ağ Izleyicisi oluşturmayı ve bir ağ Izleyicisi 'ni silmeyi öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: c308824afdcae5f5c04a316c199bad71ad84a429
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962017"
---
# <a name="create-an-azure-network-watcher-instance"></a>Azure Ağ İzleyicisi örneği oluşturma

Ağ Izleyicisi, Azure 'daki ve Azure 'dan bir ağ senaryosu düzeyinde koşulları izlemenizi ve tanılamanıza olanak tanıyan bölgesel bir hizmettir. Senaryo düzeyi izleme, bir uçtan uca ağ düzeyi görünümündeki sorunları tanılamanıza olanak sağlar. Ağ Izleyicisi ile kullanılabilen ağ tanılama ve görselleştirme araçları, Azure 'da ağınızı anlamanıza, tanılamanıza ve elde etmenize yardımcı olur. Ağ Izleyicisi, bir ağ Izleyicisi kaynağı oluşturma yoluyla etkinleştirilir. Bu kaynak, ağ Izleyicisi yeteneklerini kullanmanızı sağlar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Ağ Izleyicisi otomatik olarak etkinleştirilir
Aboneliğinizde sanal ağı oluşturur veya güncelleştirirken, Sanal Ağınızın bölgesinde Ağ İzleyicisi otomatik olarak etkinleştirilir. Ağ İzleyicisi'nin otomatik olarak etkinleştirmesi kaynaklarınızı veya ilişkili ücretleri hiçbir şekilde etkilemez.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Ağ Izleyicisi otomatik etkinleştirme devre dışı
Ağ Izleyicisi Otomatik etkinleştirmeyi devre dışı bırakmak isterseniz, aşağıdaki komutları çalıştırarak bunu yapabilirsiniz:

> [!WARNING]
> Ağ Izleyicisi otomatik etkinleştirme, kalıcı bir değişiklikdir. Bu işlemi yaptıktan sonra, [Destek ile iletişim](https://azure.microsoft.com/support/options/) kurmadan kabul edilemez

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Portalda ağ Izleyicisi oluşturma

**Tüm hizmetler**  >  **ağ**  >  **İzleyicisi ağ** izleyicisine gidin. Ağ İzleyicisini etkinleştirmek istediğiniz tüm abonelikleri seçebilirsiniz. Bu eylem, kullanılabilir her bölgede bir ağ Izleyicisi oluşturur.

![Ağ İzleyicisi oluşturma](./media/network-watcher-create/figure1.png)

Portal 'ı kullanarak Ağ İzleyicisini etkinleştirdiğinizde, ağ Izleyicisi örneğinin adı otomatik olarak *NetworkWatcher_region_name* olarak ayarlanır ve *region_name* , örneğin etkin olduğu Azure bölgesine karşılık gelir. Örneğin, Orta Batı ABD bölgesinde etkinleştirilmiş bir ağ Izleyicisi *NetworkWatcher_westcentralus* olarak adlandırılır.

Ağ Izleyicisi örneği, *NetworkWatcherRG* adlı bir kaynak grubunda otomatik olarak oluşturulur. Kaynak grubu zaten yoksa oluşturulur.

Bir ağ Izleyicisi örneğinin adını ve yerleştirildiği kaynak grubunu özelleştirmek isterseniz, aşağıdaki bölümlerde açıklanan PowerShell, Azure CLı, REST API veya ARMClient yöntemlerini kullanabilirsiniz. Her seçenekte, içinde bir ağ Izleyicisi oluşturmadan önce kaynak grubunun mevcut olması gerekir.  

## <a name="create-a-network-watcher-with-powershell"></a>PowerShell ile ağ Izleyicisi oluşturma

Ağ Izleyicisi 'nin bir örneğini oluşturmak için aşağıdaki örneği çalıştırın:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Azure CLı ile ağ Izleyicisi oluşturma

Ağ Izleyicisi 'nin bir örneğini oluşturmak için aşağıdaki örneği çalıştırın:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>REST API bir ağ Izleyicisi oluşturun

ARMclient, REST API PowerShell kullanarak çağırmak için kullanılır. ARMClient, [Chocolatey üzerinde](https://chocolatey.org/packages/ARMClient) Chocolatey konumunda bulunur

### <a name="log-in-with-armclient"></a>ARMClient ile oturum açma

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Ağ İzleyicisi oluşturma

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="create-a-network-watcher-using-azure-quickstart-template"></a>Azure hızlı başlangıç şablonunu kullanarak ağ Izleyicisi oluşturma

Ağ Izleyicisi 'nin bir örneğini oluşturmak için bu [hızlı başlangıç şablonunu](https://azure.microsoft.com/resources/templates/101-networkwatcher-create/) inceleyin

## <a name="delete-a-network-watcher-in-the-portal"></a>Portalda bir ağ izleyicisini silme

**Tüm hizmetler**  >  **ağ**  >  **İzleyicisi ağ** izleyicisine gidin.

Henüz orada değilseniz genel bakış sekmesini seçin. ' De Ağ İzleyicisini devre dışı bırakmak istediğiniz aboneliği seçmek için açılan menüyü kullanın.
Ok simgesine tıklayarak seçtiğiniz abonelik için bölgelerin listesini genişletin. Verilen her türlü için, bağlam menüsüne erişmek için sağdaki 3 noktayı kullanın.
Devre dışı bırakmaya başlamak için "Ağ İzleyicisini devre dışı bırak" seçeneğine tıklayın. Bu adımı onaylamanız istenecektir. Devam etmek için Evet’e tıklayın.
Portalda, her abonelikteki her bölge için tek tek yapmanız gerekecektir.


## <a name="delete-a-network-watcher-with-powershell"></a>PowerShell ile ağ Izleyicisi silme

Bir ağ Izleyicisi örneğini silmek için aşağıdaki örneği çalıştırın:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Sonraki adımlar

Artık bir ağ Izleyicisi örneğine sahip olduğunuza göre, kullanılabilir özellikler hakkında bilgi edinin:

* [Topoloji](./view-network-topology.md)
* [Paket yakalama](network-watcher-packet-capture-overview.md)
* [IP akışı doğrulama](network-watcher-ip-flow-verify-overview.md)
* [Sonraki atlama](network-watcher-next-hop-overview.md)
* [Güvenlik grubu görünümü](network-watcher-security-group-view-overview.md)
* [NSG akış günlüğü](network-watcher-nsg-flow-logging-overview.md)
* [Sanal ağ geçidi sorunlarını giderme](network-watcher-troubleshoot-overview.md)