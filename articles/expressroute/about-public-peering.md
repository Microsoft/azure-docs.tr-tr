---
title: Azure ExpressRoute ortak eşlemesi oluşturma ve yönetme
description: Azure genel eşlemesini öğrenin ve yönetin
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: duau
ms.openlocfilehash: 477145619e1b4d8b41c422389b57a46615597478
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202557"
---
# <a name="create-and-manage-expressroute-public-peering"></a>ExpressRoute ortak eşlemesini oluşturma ve yönetme

> [!div class="op_single_selector"]
> * [Makale-genel eşleme](about-public-peering.md)
> * [Video-genel eşleme](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Makale-Microsoft eşleme](expressroute-circuit-peerings.md#microsoftpeering)
>

Bu makale, bir ExpressRoute devresi için ortak eşleme Yönlendirme yapılandırması oluşturmanıza ve yönetmenize yardımcı olur. Ayrıca, durumu denetleyebilir, güncelleştirebilir veya silebilir ve yinelenenleri kaldırabilirsiniz. Bu makale, genel eşleme kullanım dışı olmadan önce oluşturulan Kaynak Yöneticisi devreler için geçerlidir. Önceden var olan bir devreniz varsa (genel eşleme kullanım dışı bırakılmadan önce oluşturulmuş), [Azure PowerShell](#powershell), [Azure CLI](#cli)ve [Azure Portal](#portal)kullanarak genel eşlemeyi yönetebilir/yapılandırabilirsiniz.

>[!NOTE]
>Ortak eşleme kullanım dışıdır. Yeni ExpressRoute devrelerine genel eşleme oluşturamazsınız. Yeni bir ExpressRoute devreniz varsa, Azure hizmetleriniz için [Microsoft eşlemesini](expressroute-circuit-peerings.md#microsoftpeering) kullanın.
>

## <a name="connectivity"></a>Bağlantı

Bağlantı, WAN 'dan Microsoft Azure hizmetlerine her zaman başlatılır. Microsoft Azure hizmetler, bu yönlendirme etki alanı üzerinden ağınıza bağlantı başlatamaz. ExpressRoute devreniz Azure genel eşleme için etkinleştirildiyse, devre üzerinden [Azure 'da kullanılan genel IP aralıklarına](../virtual-network/public-ip-addresses.md#public-ip-addresses) erişebilirsiniz.

Ortak eşleme etkinleştirildikten sonra, çoğu Azure hizmetine bağlanabilirsiniz. Yolların tanıtıldığı Hizmetleri seçmeli olarak seçmenizi vermedik.

* Azure depolama, SQL veritabanları ve Web siteleri gibi hizmetler genel IP adreslerinde sunulur.
* Ortak eşleme yönlendirme etki alanı aracılığıyla, bulut hizmetlerinizin VIP 'leri dahil olmak üzere genel IP adreslerinde barındırılan hizmetlere özel olarak bağlanabilirsiniz.
* Genel eşleme etki alanını DMZ ' e bağlanabilir ve internet üzerinden bağlanmak zorunda kalmadan, genel IP adreslerinde bulunan tüm Azure hizmetlerine bağlanabilirsiniz.

## <a name="services"></a><a name="services"></a>Hizmetler

Bu bölümde, genel eşleme üzerinden kullanılabilen hizmetler gösterilmektedir. Ortak eşleme kullanım dışı olduğundan, ortak eşlemeye yeni veya ek hizmetler ekleme planı yoktur. Ortak eşlemeyi kullanıyorsanız ve kullanmak istediğiniz hizmet yalnızca Microsoft eşlemesi üzerinden destekleniyorsa, Microsoft eşlemesi ' ne geçmeniz gerekir. Desteklenen hizmetlerin listesi için bkz. [Microsoft eşlemesi](expressroute-faqs.md#microsoft-peering) .

**Destek**

* Power BI
* Azure hizmetlerinin çoğu desteklenir. Desteği doğrulamak için kullanmak istediğiniz hizmeti doğrudan denetleyin.

**Desteklenmez:**
  * CDN
  * Azure Front Door
  * Multi-Factor Authentication sunucusu (eski)
  * Traffic Manager

Belirli bir hizmet için kullanılabilirliği doğrulamak üzere bu hizmetin belgelerini denetleyerek, bu hizmet için yayımlanmış bir Aralık olup olmadığını görebilirsiniz. Ardından, hedef hizmetin IP aralıklarını arayabilir ve [Azure IP aralıkları ve hizmet etiketleri – genel bulut XML dosyasında](https://www.microsoft.com/download/details.aspx?id=56519)listelenen aralıklardan karşılaştırırsınız. Alternatif olarak, açıklama için söz konusu hizmet için bir destek bileti açabilirsiniz.

## <a name="peering-comparison"></a><a name="compare"></a>Eşleme karşılaştırması

[!INCLUDE [peering comparison](../../includes/expressroute-peering-comparison.md)]

> [!NOTE]
> Azure genel eşleme, her BGP oturumuyla ilişkili 1 NAT IP adresine sahiptir. 2 ' den fazla NAT IP adresi için Microsoft eşlemesi ' ne geçin. Microsoft eşleme, kendi NAT ayırmalarınızı yapılandırmanıza ve seçmeli ön ek tanıtımları için yol filtrelerini kullanmanıza olanak sağlar. Daha fazla bilgi için bkz. [Microsoft eşlemesi 'Ne taşıma](./how-to-move-peering.md).
>

## <a name="custom-route-filters"></a>Özel yol filtreleri

Ağınız içindeki özel yol filtrelerini yalnızca ihtiyacınız olan yolları kullanacak şekilde tanımlayabilirsiniz. Yönlendirme yapılandırması hakkında ayrıntılı bilgi için [yönlendirme](expressroute-routing.md) sayfasına bakın.

## <a name="azure-powershell-steps"></a><a name="powershell"></a>Azure PowerShell adımları


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

Ortak eşleme kullanım dışı olduğundan, yeni bir ExpressRoute devresinde ortak eşlemeyi yapılandıramazsınız.

1. Sağlanan ve ayrıca etkinleştirilen bir ExpressRoute devreniz olduğunu doğrulayın. Şu örneği kullanın:

   ```azurepowershell-interactive
   Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
   ```

   Yanıt aşağıdaki örneğe benzer:

   ```
   Name                             : ExpressRouteARMCircuit
   ResourceGroupName                : ExpressRouteResourceGroup
   Location                         : westus
   Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
   Etag                             : W/"################################"
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
                                       "ServiceProviderName": "Equinix",
                                       "PeeringLocation": "Silicon Valley",
                                       "BandwidthInMbps": 200
                                     }
   ServiceKey                       : **************************************
   Peerings                         : []
   ```
2. Bağlantı hattı için Azure ortak eşlemesini yapılandırın. Devam etmeden önce aşağıdaki bilgilere sahip olduğunuzdan emin olun.

   * Birincil bağlantı için bir /30 alt ağı. Bu geçerli bir ortak IPv4 öneki olmalıdır.
   * İkincil bağlantı için bir /30 alt ağı. Bu geçerli bir ortak IPv4 öneki olmalıdır.
   * Bu eşlemenin kurulacağı geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.
   * İsteğe bağlı:
   * Kullanmayı seçerseniz bir MD5 karma değeri.

   Devreniz için Azure ortak eşlemesini yapılandırmak üzere aşağıdaki örneği çalıştırın

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   Bir MD5 karması kullanmayı seçerseniz, aşağıdaki örneği kullanın:

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "12.0.0.0/30" -SecondaryPeerAddressPrefix "12.0.0.4/30" -VlanId 100  -SharedKey "A1B2C3D4"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

   > [!IMPORTANT]
   > AS numaranızı müşteri ASN’si değil eşleme ASN’si olarak belirttiğinizden emin olun.
   > 
   >

### <a name="to-get-azure-public-peering-details"></a><a name="getpublic"></a>Azure genel eşleme ayrıntılarını almak için

Aşağıdaki cmdlet 'i kullanarak yapılandırma ayrıntılarını alabilirsiniz:

```azurepowershell-interactive
  $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -Circuit $ckt
  ```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure ortak eşleme yapılandırmasını güncelleştirmek için

Aşağıdaki örneği kullanarak yapılandırmanın herhangi bir bölümünü güncelleştirebilirsiniz. Bu örnekte, devrenin VLAN KIMLIĞI 200 ' den 600 ' e güncelleştiriliyor.

```azurepowershell-interactive
Set-AzExpressRouteCircuitPeeringConfig  -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt -PeeringType AzurePublicPeering -PeerASN 100 -PrimaryPeerAddressPrefix "123.0.0.0/30" -SecondaryPeerAddressPrefix "123.0.0.4/30" -VlanId 600

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure ortak eşlemesini silmek için

Aşağıdaki örneği çalıştırarak, eşleme yapılandırmanızı kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="azure-cli-steps"></a><a name="cli"></a>Azure CLı adımları


[!INCLUDE [CloudShell](../../includes/expressroute-cloudshell-powershell-about.md)]

1. Sağlanan ve ayrıca etkinleştirildiğinden emin olmak için ExpressRoute bağlantı hattını denetleyin. Şu örneği kullanın:

   ```azurecli-interactive
   az network express-route list
   ```

   Yanıt aşağıdaki örneğe benzer:

   ```output
   "allowClassicOperations": false,
   "authorizations": [],
   "circuitProvisioningState": "Enabled",
   "etag": "W/\"1262c492-ffef-4a63-95a8-a6002736b8c4\"",
   "gatewayManagerEtag": null,
   "id": "/subscriptions/81ab786c-56eb-4a4d-bb5f-f60329772466/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit",
   "location": "westus",
   "name": "MyCircuit",
   "peerings": [],
   "provisioningState": "Succeeded",
   "resourceGroup": "ExpressRouteResourceGroup",
   "serviceKey": "1d05cf70-1db5-419f-ad86-1ca62c3c125b",
   "serviceProviderNotes": null,
   "serviceProviderProperties": {
    "bandwidthInMbps": 200,
    "peeringLocation": "Silicon Valley",
    "serviceProviderName": "Equinix"
   },
   "serviceProviderProvisioningState": "Provisioned",
   "sku": {
    "family": "UnlimitedData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
   },
   "tags": null,
   "type": "Microsoft.Network/expressRouteCircuits]
   ```

2. Bağlantı hattı için Azure ortak eşlemesini yapılandırın. Devam etmeden önce aşağıdaki bilgilere sahip olduğunuzdan emin olun.

   * Birincil bağlantı için bir /30 alt ağı. Bu geçerli bir ortak IPv4 öneki olmalıdır.
   * İkincil bağlantı için bir /30 alt ağı. Bu geçerli bir ortak IPv4 öneki olmalıdır.
   * Bu eşlemenin kurulacağı geçerli bir VLAN kimliği. Bağlantı hattındaki başka bir eşlemenin aynı VLAN kimliğini kullanmadığından emin olun.
   * Eşleme için AS numarası. 2 bayt ve 4 bayt AS numaralarını kullanabilirsiniz.
   * **Isteğe bağlı-** Kullanmayı seçerseniz bir MD5 karma değeri.

   Devreniz için Azure ortak eşlemesini yapılandırmak üzere aşağıdaki örneği çalıştırın:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering
   ```

   Bir MD5 karması kullanmayı seçerseniz, aşağıdaki örneği kullanın:

   ```azurecli-interactive
   az network express-route peering create --circuit-name MyCircuit --peer-asn 100 --primary-peer-subnet 12.0.0.0/30 -g ExpressRouteResourceGroup --secondary-peer-subnet 12.0.0.4/30 --vlan-id 200 --peering-type AzurePublicPeering --SharedKey "A1B2C3D4"
   ```

   > [!IMPORTANT]
   > AS numaranızı müşteri ASN’si değil eşleme ASN’si olarak belirttiğinizden emin olun.

### <a name="to-view-azure-public-peering-details"></a><a name="getpublic"></a>Azure ortak eşleme ayrıntılarını görüntülemek için

Yapılandırma ayrıntılarını aşağıdaki örneği kullanarak edinebilirsiniz:

```azurecli
az network express-route peering show -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

Çıktı aşağıdaki örneğe benzer:

```output
{
  "azureAsn": 12076,
  "etag": "W/\"2e97be83-a684-4f29-bf3c-96191e270666\"",
  "gatewayManagerEtag": "18",
  "id": "/subscriptions/9a0c2943-e0c2-4608-876c-e0ddffd1211b/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/MyCircuit/peerings/AzurePublicPeering",
  "lastModifiedBy": "Customer",
  "microsoftPeeringConfig": null,
  "name": "AzurePublicPeering",
  "peerAsn": 7671,
  "peeringType": "AzurePublicPeering",
  "primaryAzurePort": "",
  "primaryPeerAddressPrefix": "",
  "provisioningState": "Succeeded",
  "resourceGroup": "ExpressRouteResourceGroup",
  "routeFilter": null,
  "secondaryAzurePort": "",
  "secondaryPeerAddressPrefix": "",
  "sharedKey": null,
  "state": "Enabled",
  "stats": null,
  "vlanId": 100
}
```

### <a name="to-update-azure-public-peering-configuration"></a><a name="updatepublic"></a>Azure ortak eşleme yapılandırmasını güncelleştirmek için

Aşağıdaki örneği kullanarak yapılandırmanın herhangi bir bölümünü güncelleştirebilirsiniz. Bu örnekte, devrenin VLAN KIMLIĞI 200 ' den 600 ' e güncelleştiriliyor.

```azurecli-interactive
az network express-route peering update --vlan-id 600 -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

### <a name="to-delete-azure-public-peering"></a><a name="deletepublic"></a>Azure ortak eşlemesini silmek için

Aşağıdaki örneği çalıştırarak, eşleme yapılandırmanızı kaldırabilirsiniz:

```azurecli-interactive
az network express-route peering delete -g ExpressRouteResourceGroup --circuit-name MyCircuit --name AzurePublicPeering
```

## <a name="azure-portal-steps"></a><a name="portal"></a>Azure portal adımları

Eşlemeyi yapılandırmak için bu makalede yer alan PowerShell veya CLı adımlarını kullanın. Bir eşlemeyi yönetmek için aşağıdaki bölümleri kullanabilirsiniz. Bu adımlar, başvuru için [portalda Microsoft eşlemesini](expressroute-howto-routing-portal-resource-manager.md#msft)yönetmeye benzer şekilde görünür.

### <a name="to-view-azure-public-peering-details"></a><a name="get"></a>Azure ortak eşleme ayrıntılarını görüntülemek için

Portalda eşlemeyi seçerek Azure ortak eşleme özelliklerini görüntüleyin.

### <a name="to-update-azure-public-peering-configuration"></a><a name="update"></a>Azure ortak eşleme yapılandırmasını güncelleştirmek için

Eşleme için satırı seçin ve ardından eşleme özelliklerini değiştirin.

### <a name="to-delete-azure-public-peering"></a><a name="delete"></a>Azure ortak eşlemesini silmek için

Eşleme yapılandırmanızı kaldırın ve Sil simgesini seçin.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki adım, [bir sanal ağı ExpressRoute devresine bağlama](expressroute-howto-linkvnet-arm.md).

* ExpressRoute iş akışları hakkında daha fazla bilgi için bkz. [ExpressRoute iş akışları](expressroute-workflows.md).
* Bağlantı hattı eşlemesi hakkında daha fazla bilgi için bkz. [ExpressRoute bağlantı hattı ve yönlendirme etki alanları](expressroute-circuit-peerings.md).
* Sanal ağlarla çalışma hakkında daha fazla bilgi için bkz. [Sanal ağa genel bakış](../virtual-network/virtual-networks-overview.md).