---
title: 'Azure ExpressRoute: devre eşlemesini sıfırlama'
description: Azure PowerShell kullanarak bir Azure ExpressRoute bağlantı hattı için eşayarları etkinleştirme ve devre dışı bırakma hakkında bilgi edinin.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: 0bde96ae5f4a9aff6f4a16a4f1544d9b39e5cb66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97559582"
---
# <a name="reset-expressroute-circuit-peerings"></a>ExpressRoute devre eşayarlarını sıfırlama

Bu makalede, PowerShell kullanarak bir ExpressRoute bağlantı hattının nasıl etkinleştirileceği ve devre dışı bırakılacağı açıklanır. Eşlemeler, oluşturduğunuzda varsayılan olarak etkinleştirilir. Bir eşlemeyi devre dışı bıraktığınızda, ExpressRoute devreniz için hem birincil hem de ikincil bağlantı üzerinde BGP oturumu kapatılır. Bu eşleme için Microsoft bağlantısını kaybedersiniz. Bir eşlemeyi etkinleştirdiğinizde, ExpressRoute bağlantı hattının hem birincil hem de ikincil bağlantısı üzerinde BGP oturumu oluşturulur. Bu eşleme için Microsoft bağlantısı geri alınacaktır. ExpressRoute devresine bağımsız olarak Microsoft eşlemesi ve Azure özel eşleme için eşlemeyi etkinleştirebilir ve devre dışı bırakabilirsiniz.

ExpressRoute eşlerinizi sıfırlamanıza yardımcı olabilecek iki senaryo vardır.
* Olağanüstü durum kurtarma tasarımınızı ve uygulamanızı test etmek istiyorsanız. Örneğin, iki ExpressRoute devreniz vardır. Bir bağlantı devresini devre dışı bırakabilir ve ağ trafiğinizi diğer devreye devretmek için zorlayabilirsiniz.
* Azure özel eşleme veya ExpressRoute devrenizin Microsoft eşlemesi üzerinde çift yönlü Iletme algılamayı (BFD) etkinleştirin. ExpressRoute bağlantı hattını 1 Ağustos 2018 ' den sonra ve 10 Ocak 2020 ' den sonra Microsoft eşlemesi için oluşturduysanız, BFD varsayılan olarak Azure özel eşleme 'de etkinleştirilir. Devreniz listelenen tarihten önce oluşturulduysa, BFD 'yi etkinleştirmek için eşlemeyi sıfırlamanız gerekir. 

### <a name="working-with-azure-powershell"></a>Azure PowerShell çalışma

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="reset-a-peering"></a>Eşlemeyi sıfırlama

1. PowerShell 'i yerel olarak çalıştırıyorsanız, PowerShell konsolunuzu yükseltilmiş ayrıcalıklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

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
4. ExpressRoute devrenizi almak için aşağıdaki komutları çalıştırın.

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```
5. Devre dışı bırakmak veya etkinleştirmek istediğiniz eşlemeyi belirler.  Eşlemeler bir dizidir. Aşağıdaki örnekte, [0] eşlemeleri Azure özel eşleme ve eşleme [1] Microsoft eşlemesi.

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
   Eşleme, ayarladığınız bir durumda olmalıdır. 

## <a name="next-steps"></a>Sonraki adımlar
ExpressRoute sorununu gidermeye yönelik yardıma ihtiyacınız varsa aşağıdaki makalelere bakın:
* [ExpressRoute bağlantısını doğrulama](expressroute-troubleshooting-expressroute-overview.md)
* [Ağ performansı sorunlarını giderme](expressroute-troubleshooting-network-performance.md)
