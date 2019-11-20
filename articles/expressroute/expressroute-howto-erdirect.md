---
title: "Azure ExpressRoute: ExpressRoute Direct 'i yapılandırma"
description: Bu sayfa, ExpressRoute Direct 'i yapılandırmanıza yardımcı olur.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: jaredro
ms.openlocfilehash: ba7bddb2f11732f7de5f1dfa68b66be1204722cc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083480"
---
# <a name="how-to-configure-expressroute-direct"></a>ExpressRoute Direct 'i yapılandırma

ExpressRoute doğrudan, Microsoft'un küresel ağı dünya genelindeki stratejik dağıtılmış eşleme konumlarda doğrudan bağlanma özelliği sağlar. Daha fazla bilgi için [hakkında ExpressRoute doğrudan bağlanma](expressroute-erdirect-about.md).

## <a name="resources"></a>Kaynak Oluştur

1. Azure'da oturum açın ve aboneliği seçin. ExpressRoute bağlantı hatları ve ExpressRoute doğrudan kaynak aynı abonelikte olması gerekir.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
2. ExpressRoute doğrudan'ın desteklendiği tüm konumların listesi.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Örnek çıktı**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
3. Yukarıda listelenen bir konum kullanılabilir bant genişliğini olup olmadığını belirleyin

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Örnek çıktı**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
4. Yukarıda seçilen konumuna bağlı olarak bir ExpressRoute doğrudan kaynak oluştur

   ExpressRoute doğrudan QinQ hem Dot1Q kapsülleme destekler. QinQ seçili ise, her bir ExpressRoute bağlantı hattında S etiketi dinamik olarak atanır ve ExpressRoute doğrudan kaynak benzersiz olacaktır. Her C-Tag devredeki benzersiz olmalıdır. bağlantı hattını, ancak ExpressRoute doğrudan arasında değil.  

   Dot1Q sarması'nı seçtiyseniz, tüm ExpressRoute doğrudan kaynak arasında benzersizlik, C etiketleme (VLAN) yönetmeniz gerekir.  

   > [!IMPORTANT]
   > ExpressRoute doğrudan yalnızca tek bir saklama türü olabilir. Kapsülleme ExpressRoute doğrudan oluşturulduktan sonra değiştirilemez.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Kapsülleme öznitelik Dot1Q için de ayarlayabilirsiniz. 
   >

   **Örnek çıktı:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="state"></a>Bağlantı Yöneticisi durumunu değiştir

  Bu işlem, her bir çapraz bağlantı doğru birincil site için her yönlendirici olarak düzeltme eki uygulama ve ikincil olduğundan emin olmanın bir katman 1 testi yürütmek için kullanılmalıdır.
1. ExpressRoute doğrudan ayrıntıları alın.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Etkin bağlantı ayarlayın. Etkin her bir bağlantı ayarlamak için bu adımı yineleyin.

   Bağlantılar [0] birincil bağlantı ve bağlantılar [1] ikincil bağlantı noktası.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Örnek çıktı:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   İle aynı yordamı `AdminState = "Disabled"` bağlantı noktalarını açmak için.

## <a name="circuit"></a>Bir bağlantı hattı oluşturma

Varsayılan olarak 10 bağlantı hatları ExpressRoute doğrudan kaynak olduğu abonelikte oluşturabilirsiniz. Bu destek birimi tarafından artırılabilir. Sağlanan hem kullanılan bant genişliğini izlemek için sorumlu olursunuz. Bant genişliğiniz ExpressRoute doğrudan kaynaktaki tüm devreler bant genişliği toplamını ve kullanılan bant genişliği fiziksel kullanımı temel alınan fiziksel arabirim.

Yalnızca yukarıda özetlenen senaryoları desteklemek için ExpressRoute doğrudan üzerinde kullanılan ek bağlantı hattı bant genişlikleri vardır. Bunlar: 40Gbps ve 100Gbps.

**Skutier** yerel, standart veya Premium olabilir.

**Skufamily** , doğrudan ExpressRoute Direct üzerinde desteklenmediği için MeteredData olmalıdır.

Bir devreyi ExpressRoute doğrudan kaynağı oluşturun.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Diğer bant genişlikleri içerir: 5.0, 10.0 ve 40.0

  **Örnek çıktı:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute doğrudan hakkında daha fazla bilgi için bkz: [genel bakış](expressroute-erdirect-about.md).
