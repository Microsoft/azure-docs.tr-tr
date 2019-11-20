---
title: 'Azure ExpressRoute: Global Reach yapılandırma'
description: Bu makalede, yardımcı birlikte özel ağ arasında şirket içi ağlarınız ve Global erişim etkinleştirme yapmak için ExpressRoute bağlantı hattına bağlayın.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083461"
---
# <a name="configure-expressroute-global-reach"></a>ExpressRoute Global Reach’i yapılandırma

Bu makalede PowerShell kullanarak ExpressRoute Global erişim yapılandırmanıza yardımcı olur. Daha fazla bilgi için [ExpressRouteRoute Global erişim](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Başlamadan önce

Yapılandırmaya başlamadan önce, aşağıdakileri onaylayın:

* ExpressRoute devre sağlama [iş akışlarını](expressroute-workflows.md)anlamış olursunuz.
* ExpressRoute devrelerinizi sağlanan bir durumda.
* Azure özel eşlemesi, ExpressRoute devrelerinizi yapılandırılmış.
* PowerShell 'i yerel olarak çalıştırmak istiyorsanız, Azure PowerShell en son sürümünün bilgisayarınızda yüklü olduğunu doğrulayın.

### <a name="working-with-azure-powershell"></a>Azure PowerShell ile çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Devreleri tanımla

1. Yapılandırmayı başlatmak için Azure hesabınızda oturum açın ve kullanmak istediğiniz aboneliği seçin.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Kullanmak istediğiniz ExpressRoute devreleri belirler. Desteklenen ülkelerde/bölgelerde bulundukları ve farklı eşleme konumlarında oluşturulan her iki ExpressRoute bağlantı hattı arasında ExpressRoute Global Reach etkinleştirebilirsiniz. 

   * Aboneliğiniz her iki devrede sahipse, aşağıdaki bölümlerde yapılandırmayı çalıştırmak için devre seçeneğini belirleyebilirsiniz.
   * İki devre farklı Azure aboneliklerdeyse, bir Azure aboneliğinden yetkilendirme yapmanız gerekir. Ardından, diğer Azure aboneliğinde yapılandırma komutunu çalıştırdığınızda yetkilendirme anahtarını geçirirsiniz.

## <a name="enable-connectivity"></a>Bağlantıyı etkinleştir

Şirket içi ağlarınız arasında bağlantıyı etkinleştirin. Aynı Azure aboneliğinde ve farklı abonelikler içeren devrelere yönelik ayrı yönergeler kümesi vardır.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Aynı Azure aboneliğindeki ExpressRoute devreleri

1. Bağlantı hattı 1 ve 2 bağlantı hattı almak için aşağıdaki komutları kullanın. İki bağlantı hatları, aynı abonelikte yer alan.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Devre 1 ' de aşağıdaki komutu çalıştırın ve devre 2 ' nin özel eşleme KIMLIĞINI geçirin. Komutu çalıştırırken şunları göz önünde edin:

   * Özel eşleme KIMLIĞI aşağıdaki örneğe benzer şekilde görünür: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-Addresspredüzeltmesini* bir/29 IPv4 alt ağı olmalıdır, örneğin, "10.0.0.0/29". İki ExpressRoute devreleri arasında bağlantı kurmak için bu alt ağdaki IP adreslerini kullanıyoruz. Bu alt ağdaki adresleri Azure sanal ağlarınızda veya şirket içi ağınızda kullanmamalısınız.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Yapılandırmayı şu şekilde devre 1 ' de kaydedin:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Önceki işlem tamamlandığında, iki ExpressRoute devreniz aracılığıyla şirket içi ağlarınızla her iki tarafa da bağlantınız olur.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Farklı Azure aboneliklerinde ExpressRoute işlem hatları

İki devrede aynı Azure aboneliğinde yoksa, yetkilendirme yapmanız gerekir. Aşağıdaki yapılandırmada, yetkilendirme devre 2 aboneliğinde oluşturulur ve yetkilendirme anahtarı devre 1 ' e geçirilir.

1. Bir yetkilendirme anahtar oluşturun.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Devre 2 ' nin özel eşleme KIMLIĞINI ve yetkilendirme anahtarını bir yere unutmayın.
2. 1 bağlantı hattı karşı aşağıdaki komutu çalıştırın. Devre 2 ' nin özel eşleme KIMLIĞINI ve yetkilendirme anahtarını geçirin.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Yapılandırmayı devre 1 ' de kaydedin.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Önceki işlem tamamlandığında, iki ExpressRoute devreniz aracılığıyla şirket içi ağlarınızla her iki tarafa da bağlantınız olur.

## <a name="verify-the-configuration"></a>Yapılandırmayı doğrulama

Yapılandırmanın gerçekleştiği devre üzerindeki yapılandırmayı doğrulamak için aşağıdaki komutu kullanın (örneğin, önceki örnekte devre 1).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

PowerShell 'de *$CKT 1* ' i çalıştırırsanız, çıkışta devre bağlantı *durumu* ' nu görürsünüz. Bağlantının oluşturulup yüklenmediğini, "bağlı" veya "bağlantısı kesik" olduğunu söyler. 

## <a name="disable-connectivity"></a>Bağlantıyı devre dışı bırak

Şirket içi ağlarınız arasında bağlantıyı devre dışı bırakmak için, yapılandırmanın yapıldığı devreye karşı komutları çalıştırın (örneğin, önceki örnekte devre 1).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Durumu doğrulamak için alma işlemini çalıştırabilirsiniz.

Önceki işlem tamamlandıktan sonra, ExpressRoute devrelerinizi kullanarak şirket içi ağınız arasında artık bağlantı kuramadınız.

## <a name="next-steps"></a>Sonraki adımlar
1. [ExpressRoute Global erişim hakkında daha fazla bilgi edinin](expressroute-global-reach.md)
2. [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
3. [Azure sanal ağına bir ExpressRoute bağlantı hattı bağlama](expressroute-howto-linkvnet-arm.md)
