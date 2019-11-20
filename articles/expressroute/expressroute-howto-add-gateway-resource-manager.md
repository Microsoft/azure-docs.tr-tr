---
title: 'Azure ExpressRoute: sanal ağa ağ geçidi ekleme: PowerShell'
description: Bu makalede önceden oluşturulmuş bir Resource Manager Vnet'i ExpressRoute için VNet ağ geçidinin eklemenize yardımcı olur.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037421"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>PowerShell kullanarak ExpressRoute için sanal ağ geçidi yapılandırma
> [!div class="op_single_selector"]
> * [Resource Manager - Azure portalı](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasik - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Azure portalı](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Bu makalede, ekleme, yeniden boyutlandırma ve önceden var olan bir VNet için bir sanal ağ (VNet) ağ geçidini kaldırmak yardımcı olur. Bu yapılandırmaya yönelik adımlar, bir ExpressRoute yapılandırması için Kaynak Yöneticisi dağıtım modeli kullanılarak oluşturulan sanal ağlar için geçerlidir. Daha fazla bilgi için bkz. [ExpressRoute için sanal ağ geçitleri hakkında](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Başlamadan önce

### <a name="working-with-powershell"></a>PowerShell ile çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Yapılandırma başvuru listesi

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Sonraki adımlar
VNet ağ geçidinin oluşturduktan sonra ağınız bir ExpressRoute bağlantı hattına bağlayabilirsiniz. Bkz: [bir sanal ağı ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md).
