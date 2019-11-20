---
title: 'Azure ExpressRoute: devre eşlemesini sıfırlama'
description: Nasıl devre dışı bırakın ve ExpressRoute devre eşlemeleri etkinleştirin.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: charwen
ms.openlocfilehash: 2810dfc4cb41dcf11eb59ce3c87e6f7d6b2d5f65
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083493"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute devre eşlemeleri Sıfırla

Bu makalede devre dışı bırakma ve PowerShell kullanarak ExpressRoute bağlantı hattının eşlemeler etkinleştirin. Bir eşleme devre dışı bıraktığınızda, birincil bağlantı ve, ExpressRoute devresinin ikincil bağlantısı BGP oturumu kapatılır. Microsoft eşlemesi üzerinden bağlantınız kesilir. Bir eşleme etkinleştirdiğinizde, birincil bağlantı ve, ExpressRoute devresinin ikincil bağlantısı BGP oturumu getirdiği. Microsoft eşlemesi üzerinden bağlantı yeniden elde. Etkinleştirme ve Microsoft Peering ve Azure özel eşleme ExpressRoute devresi bağımsız olarak devre dışı bırakabilirsiniz. Eşlemeler, ExpressRoute devreniz eşlikleri ilk kez yapılandırırken, varsayılan olarak etkindir.

Burada, ExpressRoute eşlemeleri sıfırlama yararlı birkaç senaryo vardır.
* Olağanüstü durum kurtarma tasarımı ve uygulaması test edin. Örneğin, iki ExpressRoute devreniz vardır. Bir devrenin eşlikleri devre dışı bırakabilir ve ağ trafiğinizin diğer devreye yük devretmek için zorla.
* Azure özel eşleme veya ExpressRoute devrenizin Microsoft eşlemesi üzerinde çift yönlü Iletme algılamasını (BFD) etkinleştirin. ExpressRoute bağlantı hattı 1 2018 Ağustos 'Tan sonra, ExpressRoute bağlantı hattı ' den sonra 1 2019 oluşturulduysa, BFD varsayılan olarak Azure özel eşleme 'de etkinleştirilir. Bağlantı hattınız önce oluşturulduysa BFD etkin değildi. Eşlemeyi devre dışı bırakma ve bu bırakılmış BFD etkinleştirebilirsiniz. 

### <a name="working-with-azure-powershell"></a>Azure PowerShell ile çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Bir eşleme Sıfırla

1. PowerShell'i yerel olarak çalıştırıyorsanız, PowerShell Konsolunuzu yükseltilmiş ayrıcalıklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```azurepowershell
   Connect-AzAccount
   ```
2. Birden çok Azure aboneliğiniz varsa, hesabın aboneliklerini denetleyin.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
3. Kullanmak istediğiniz aboneliği belirtin.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
4. ExpressRoute devreniz almak için aşağıdaki komutları çalıştırın.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Devre dışı bırakmak veya etkinleştirmek istediğiniz eşlemeyi tanımlar. *Eşlemeler* bir dizidir. Aşağıdaki örnekte, Azure özel eşdüzey hizmet sağlama ve eşlemeler [1] Microsoft Peering eşlemeleri [0] olduğu.

   ```azurepowershell-interactive
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"cd011bef-dc79-49eb-b4c6-81fb6ea5d178"
   ProvisioningState                : Succeeded
   Sku                              : {
                                     "Name": "Standard_MeteredData",
                                     "Tier": "Standard",
                                     "Family": "MeteredData"
                                   }
   CircuitProvisioningState         : Enabled
   ServiceProviderProvisioningState : Provisioned
   ServiceProviderNotes             :
   ServiceProviderProperties        : {
                                     "ServiceProviderName": "Coresite",
                                     "PeeringLocation": "Los Angeles",
                                     "BandwidthInMbps": 50
                                   }
   ServiceKey                       : ########-####-####-####-############
   Peerings                         : [
                                     {
                                       "Name": "AzurePrivatePeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/AzurePrivatePeering",
                                       "PeeringType": "AzurePrivatePeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "10.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "10.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 789,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "NotConfigured",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     },
                                     {
                                       "Name": "MicrosoftPeering",
                                       "Etag": "W/\"cd011bef-dc79-49eb-b4c6-81fb6ea5d178\"",
                                       "Id": "/subscriptions/########-####-####-####-############/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit/peerings/MicrosoftPeering",
                                       "PeeringType": "MicrosoftPeering",
                                       "State": "Enabled",
                                       "AzureASN": 12076,
                                       "PeerASN": 123,
                                       "PrimaryPeerAddressPrefix": "3.0.0.0/30",
                                       "SecondaryPeerAddressPrefix": "3.0.0.4/30",
                                       "PrimaryAzurePort": "",
                                       "SecondaryAzurePort": "",
                                       "VlanId": 345,
                                       "MicrosoftPeeringConfig": {
                                         "AdvertisedPublicPrefixes": [
                                           "3.0.0.3/32"
                                         ],
                                         "AdvertisedCommunities": [],
                                         "AdvertisedPublicPrefixesState": "ValidationNeeded",
                                         "CustomerASN": 0,
                                         "LegacyMode": 0,
                                         "RoutingRegistryName": "NONE"
                                       },
                                       "ProvisioningState": "Succeeded",
                                       "GatewayManagerEtag": "",
                                       "LastModifiedBy": "Customer",
                                       "Connections": []
                                     }
                                   ]
   Authorizations                   : []
   AllowClassicOperations           : False
   GatewayManagerEtag               :
   ```
6. Eşleme durumunu değiştirmek için aşağıdaki komutları çalıştırın.

   ```azurepowershell-interactive
   $ckt.Peerings[0].State = "Disabled"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```
   Eşleme, ayarladığınız bir durumda olması gerekir. 

## <a name="next-steps"></a>Sonraki adımlar
ExpressRoute sorunu gidermek için yardıma ihtiyacınız varsa şu makalelere bakın:
* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Ağ performansı sorunlarını giderme](expressroute-troubleshooting-network-performance.md)
