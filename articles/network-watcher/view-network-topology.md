---
title: Azure sanal ağ topolojisini görüntüleme | Microsoft Docs
description: Bir sanal ağ içindeki kaynakları ve kaynaklar arasındaki ilişkileri görüntülemeyi öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: f20fa22dac3fba4d01cbc5e398bafa4113e94a96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780308"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>Bir Azure sanal ağının topolojisini görüntüleme

Bu makalede, Microsoft Azure sanal bir ağdaki kaynakları ve kaynaklar arasındaki ilişkileri görüntülemeyi öğreneceksiniz. Örneğin, bir sanal ağ alt ağlar içerir. Alt ağlar Azure sanal makineler (VM) gibi kaynaklar içerir. VM 'Lerde bir veya daha fazla ağ arabirimi vardır. Her alt ağ ile ilişkili bir ağ güvenlik grubu ve bir yol tablosu olabilir. Azure ağ Izleyicisi 'nin topoloji özelliği, bir sanal ağdaki tüm kaynakları, bir sanal ağdaki kaynaklarla ilişkili kaynakları ve kaynaklar arasındaki ilişkileri görüntülemenize olanak sağlar.

Bir topolojiyi görüntülemek için [Azure Portal](#azure-portal), [Azure CLI](#azure-cli)veya [PowerShell](#powershell) 'i kullanabilirsiniz.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topolojiyi görüntüle-Azure portal

1. [Azure Portal](https://portal.azure.com) , gerekli [izinlere](required-rbac-permissions.md)sahip bir hesapla oturum açın.
2. Portalın sol üst köşesinde, **tüm hizmetler**' i seçin.
3. **Tüm hizmetler** filtresi kutusunda *Ağ İzleyicisi*' ni girin. Sonuçlarda **Ağ İzleyicisi** göründüğünde seçin.
4. **Topoloji**’yi seçin. Topoloji oluşturmak için, topolojisi oluşturmak istediğiniz sanal ağın aynı bölgede bir ağ izleyicisi olması gerekir. Topolojisi oluşturmak istediğiniz sanal ağın bulunduğu bölgede etkin bir ağ izleyicisi yoksa, ağ izleyicileri tüm bölgelerde sizin için otomatik olarak oluşturulur. Ağ izleyicileri, **NetworkWatcherRG** adlı bir kaynak grubunda oluşturulur.
5. Bir abonelik, için topolojiyi görüntülemek istediğiniz bir sanal ağın kaynak grubunu seçin ve ardından sanal ağı seçin. Aşağıdaki resimde, *Myresourcegroup* adlı kaynak grubunda *myvnet* adlı bir sanal ağ için bir topoloji gösterilir:

    ![Topolojiyi görüntüle](./media/view-network-topology/view-topology.png)

    Önceki resimde görebileceğiniz gibi, sanal ağ üç alt ağ içerir. Bir alt ağda dağıtılan bir VM vardır. VM 'ye bağlı bir ağ arabirimi ve onunla ilişkili bir genel IP adresi vardır. Diğer iki alt ağın kendileriyle ilişkili bir yol tablosu vardır. Her yol tablosu iki yol içerir. Bir alt ağ ile ilişkili bir ağ güvenlik grubu vardır. Topoloji bilgileri yalnızca şu kaynaklar için gösterilir:
    
    - Aynı kaynak grubu ve bölge içinde *Myvnet* sanal ağı ile. Örneğin, *Myresourcegroup* dışında bir kaynak grubunda bulunan bir ağ güvenlik grubu, ağ güvenlik grubu *myvnet* sanal ağındaki bir alt ağla ilişkilendirilmiş olsa bile gösterilmez.
    - Veya içinde, *Myvnet* sanal ağı içindeki kaynaklarla ilişkilendirilir. Örneğin, ağ güvenlik grubu *Myresourcegroup* kaynak grubunda olsa bile, *myvnet* sanal ağındaki bir alt ağ veya ağ arabirimi ile ilişkilendirilmemiş bir ağ güvenlik grubu gösterilmez.

   Resimde gösterilen topoloji, [Azure CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)veya [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)kullanarak dağıtabileceğiniz **bir ağ sanal gereç betiği örneği aracılığıyla yönlendirme trafiği** dağıttıktan sonra oluşturulan sanal ağ içindir.

6. Görüntüyü, SVG biçiminde düzenlenebilir bir dosya olarak indirmek için **indirme topolojisi** ' ni seçin.

Diyagramda gösterilen kaynaklar, sanal ağdaki ağ bileşenlerinin bir alt kümesidir. Örneğin, bir ağ güvenlik grubu gösterilirken, içindeki güvenlik kuralları diyagramda gösterilmez. Diyagramda farklılaştırımasa da, satırlar iki ilişkilerden birini temsil eder: *içerme* veya *ilişkili*. Sanal ağdaki kaynakların tam listesini ve kaynaklar arasındaki ilişkinin türünü görmek için, [PowerShell](#powershell) veya [Azure CLI](#azure-cli)ile topoloji oluşturun.

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topolojiyi görüntüleme-Azure CLı

Aşağıdaki adımlarda komutları çalıştırabilirsiniz:
- Azure Cloud Shell, herhangi bir komutun sağ üst kısmında **deneyin** ' i seçin. Azure Cloud Shell, ortak Azure Araçları önceden yüklenmiş ve hesabınızla kullanılmak üzere yapılandırılmış olan ücretsiz bir etkileşimli kabuktur.
- CLı 'yi bilgisayarınızdan çalıştırın. CLı 'yı bilgisayarınızdan çalıştırırsanız, bu makaledeki adımlarda Azure CLı sürüm 2.0.31 veya sonraki bir sürümü gerekir. Yüklü sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli). Azure CLı 'yi yerel olarak çalıştırıyorsanız, `az login` Azure ile bir bağlantı oluşturmak için çalıştırmanız da gerekir.

Kullandığınız hesabın gerekli [izinlere](required-rbac-permissions.md)sahip olması gerekir.

1. İçin bir topoloji oluşturmak istediğiniz sanal ağ ile aynı bölgede bir ağ izleyiciniz varsa 3. adıma atlayın. [Az Group Create](/cli/azure/group)ile bir ağ izleyicisi içeren bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* bölgesinde kaynak grubunu oluşturur:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. [Az Network izleyici configure](/cli/azure/network/watcher#az_network_watcher_configure)ile bir ağ izleyicisi oluşturun. Aşağıdaki örnek *eastus* bölgesinde bir ağ izleyicisi oluşturur:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Topolojiyi [az Network izleyici Show-Topology](/cli/azure/network/watcher#az_network_watcher_show_topology)ile görüntüleyin. Aşağıdaki örnek, *Myresourcegroup* adlı bir kaynak grubu için topolojiyi görüntüler:

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topoloji bilgileri yalnızca *Myresourcegroup* kaynak grubu ile aynı kaynak grubunda ve ağ izleyicisiyle aynı bölge içinde olan kaynaklar için döndürülür. Örneğin, *Myresourcegroup* dışında bir kaynak grubunda bulunan bir ağ güvenlik grubu, ağ güvenlik grubu *myvnet* sanal ağındaki bir alt ağla ilişkilendirilmiş olsa bile gösterilmez.

   Döndürülen çıktıda ilişkiler ve [Özellikler](#properties) hakkında daha fazla bilgi edinin. İçin bir topolojiyi görüntülemek üzere mevcut bir sanal ağınız yoksa bir [ağ sanal gereç betiği örneği aracılığıyla yönlendirme trafiğini](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) kullanarak bir tane oluşturabilirsiniz. Topolojinin bir diyagramını görüntülemek ve düzenlenebilir bir dosyaya indirmek için [portalını](#azure-portal)kullanın.

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Görünüm topolojisi-PowerShell

Aşağıdaki adımlarda komutları çalıştırabilirsiniz:
- Azure Cloud Shell, herhangi bir komutun sağ üst kısmında **deneyin** ' i seçin. Azure Cloud Shell, ortak Azure Araçları önceden yüklenmiş ve hesabınızla kullanılmak üzere yapılandırılmış olan ücretsiz bir etkileşimli kabuktur.
- PowerShell 'i bilgisayarınızdan çalıştırın. PowerShell 'i bilgisayarınızdan çalıştırırsanız, bu makale Azure PowerShell `Az` modülünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell'i yerel olarak çalıştırıyorsanız Azure bağlantısı oluşturmak için `Connect-AzAccount` komutunu da çalıştırmanız gerekir.

Kullandığınız hesabın gerekli [izinlere](required-rbac-permissions.md)sahip olması gerekir.

1. İçin bir topoloji oluşturmak istediğiniz sanal ağ ile aynı bölgede bir ağ izleyiciniz varsa 3. adıma atlayın. [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup)ile bir ağ izleyicisi içeren bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* bölgesinde kaynak grubunu oluşturur:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. [New-Aznetworkizleyici](/powershell/module/az.network/new-aznetworkwatcher)ile bir ağ izleyicisi oluşturun. Aşağıdaki örnek eastus bölgesinde bir ağ izleyicisi oluşturur:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. [Get-Aznetworkizleyici](/powershell/module/az.network/get-aznetworkwatcher)Ile bir Ağ İzleyicisi örneği alın. Aşağıdaki örnekte Doğu ABD bölgesinde bir ağ izleyicisi alınır:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology)ile topoloji alma. Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubundaki bir sanal ağ için bir topoloji alır:

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topoloji bilgileri yalnızca *Myresourcegroup* kaynak grubu ile aynı kaynak grubunda ve ağ izleyicisiyle aynı bölge içinde olan kaynaklar için döndürülür. Örneğin, *Myresourcegroup* dışında bir kaynak grubunda bulunan bir ağ güvenlik grubu, ağ güvenlik grubu *myvnet* sanal ağındaki bir alt ağla ilişkilendirilmiş olsa bile gösterilmez.

   Döndürülen çıktıda ilişkiler ve [Özellikler](#properties) hakkında daha fazla bilgi edinin. İçin bir topolojiyi görüntülemek üzere mevcut bir sanal ağınız yoksa bir [ağ sanal gereç betiği örneği aracılığıyla yönlendirme trafiğini](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) kullanarak bir tane oluşturabilirsiniz. Topolojinin bir diyagramını görüntülemek ve düzenlenebilir bir dosyaya indirmek için [portalını](#azure-portal)kullanın.

## <a name="relationships"></a>İlişkiler

Bir topolojide döndürülen tüm kaynaklar, başka bir kaynakla aşağıdaki ilişki türlerinden birine sahiptir:

| İlişki türü | Örnek                                                                                                |
| ---               | ---                                                                                                    |
| Kapsama       | Bir sanal ağ, bir alt ağ içerir. Bir alt ağ, bir ağ arabirimi içerir.                            |
| İlişkili        | Bir ağ arabirimi, bir VM ile ilişkilendirilir. Genel IP adresi bir ağ arabirimiyle ilişkili. |

## <a name="properties"></a>Özellikler

Bir topolojide döndürülen tüm kaynaklar aşağıdaki özelliklere sahiptir:

- **Ad**: kaynağın adı
- **ID**: kaynağın URI 'si.
- **Konum**: kaynağın bulunduğu Azure bölgesi.
- **İlişkilendirmeler**: başvurulan nesneye yapılan ilişkilerin bir listesi. Her ilişkilendirme aşağıdaki özelliklere sahiptir:
    - **AssociationType**: alt nesne ve üst öğe arasındaki ilişkiye başvurur. Geçerli değerler *içeriyor* veya *ilişkili*.
    - **Ad**: başvurulan kaynağın adı.
    - **RESOURCEID**:-ilişkilendirmede başvurulan kaynağın URI 'si.

## <a name="next-steps"></a>Sonraki adımlar

- Ağ Izleyicisi 'nin IP akışı doğrulama özelliğini kullanarak [bir VM 'ye veya bir sanal makineye ağ trafiği Filtreleme sorununu tanılamayı](diagnose-vm-network-traffic-filtering-problem.md) öğrenin
- Ağ Izleyicisi 'nin sonraki atlama özelliğini kullanarak [BIR VM 'den ağ trafiği yönlendirme sorununu tanılamayı](diagnose-vm-network-routing-problem.md) öğrenin
