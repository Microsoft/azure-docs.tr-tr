---
title: "Azure ExpressRoute: ExpressRoute Direct 'i yapılandırma: CLı"
description: Azure CLı kullanarak doğrudan Microsoft küresel ağa bağlanmak için Azure ExpressRoute Direct 'i nasıl yapılandıracağınızı öğrenin.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9f35a698510f8637c3fe66528e6d64e5cd87b693
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553830"
---
# <a name="configure-expressroute-direct-by-using-the-azure-cli"></a>Azure CLı kullanarak ExpressRoute Direct 'i yapılandırma

ExpressRoute Direct, dünya genelinde stratejik olarak dağıtılan eşleme konumları aracılığıyla Microsoft 'un global ağına doğrudan bağlanma olanağı sağlar. Daha fazla bilgi için bkz. [ExpressRoute Direct Connect hakkında](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a>Başlamadan önce

ExpressRoute Direct kullanmadan önce aboneliğinizi kaydetmeniz gerekir. ExpressRoute Direct kullanmadan önce aboneliğinizi kaydetmeniz gerekir. Kaydolmak için lütfen Azure PowerShell aracılığıyla şunları yapın:
1.  Azure 'da oturum açın ve kaydetmek istediğiniz aboneliği seçin.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Aşağıdaki komutu kullanarak aboneliğinizi genel önizleme için kaydedin:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Kaydolduktan sonra, **Microsoft. Network** kaynak sağlayıcısının aboneliğinize kayıtlı olduğunu doğrulayın. Kaynak sağlayıcısı kaydı, aboneliğinizi kaynak sağlayıcısıyla çalışacak şekilde yapılandırır.

## <a name="create-the-resource"></a><a name="resources"></a>Kaynağı oluşturma

1. Azure 'da oturum açın ve ExpressRoute içeren aboneliği seçin. ExpressRoute doğrudan kaynağı ve ExpressRoute Devreleriniz aynı abonelikte olmalıdır. Azure CLı 'da aşağıdaki komutları çalıştırın:

   ```azurecli
   az login
   ```

   Hesap için abonelikleri kontrol edin: 

   ```azurecli
   az account list 
   ```

   ExpressRoute bağlantı hattı oluşturmak istediğiniz aboneliği seçin:

   ```azurecli
   az account set --subscription "<subscription ID>"
   ```

2. Expressrouteportslocation ve expressrouteport API 'Lerine erişmek için aboneliğinizi Microsoft. Network 'e yeniden kaydedin

   ```azurecli
   az provider register --namespace Microsoft.Network
   ```
3. ExpressRoute Direct 'in desteklendiği tüm konumları listeleyin:
    
   ```azurecli
   az network express-route port location list
   ```

   **Örnek çıkış**
  
   ```output
   [
   {
    "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
    "location": null,
    "name": "Equinix-Ashburn-DC2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA3",
    "location": null,
    "name": "Equinix-Dallas-DA3",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "111 8th Avenue, New York, NY 10011",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-New-York-NY5",
    "location": null,
    "name": "Equinix-New-York-NY5",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "11 Great Oaks Blvd, SV1, San Jose, CA 95119",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-SV1",
    "location": null,
    "name": "Equinix-San-Jose-SV1",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   },
   {
    "address": "2001 Sixth Ave., Suite 350, SE2, Seattle, WA 98121",
    "availableBandwidths": [],
    "contact": "support@equinix.com",
    "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Seattle-SE2",
    "location": null,
    "name": "Equinix-Seattle-SE2",
    "provisioningState": "Succeeded",
    "tags": null,
    "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ]
   ```
4. Önceki adımda listelenen konumlardan birinin kullanılabilir bant genişliğine sahip olup olmadığını belirleme:

   ```azurecli
   az network express-route port location show -l "Equinix-Ashburn-DC2"
   ```

   **Örnek çıkış**

   ```output
   {
   "address": "21715 Filigree Court, DC2, Building F, Ashburn, VA 20147",
   "availableBandwidths": [
    {
      "offerName": "100 Gbps",
      "valueInGbps": 100
    }
   ],
   "contact": "support@equinix.com",
   "id": "/subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-DC2",
   "location": null,
   "name": "Equinix-Ashburn-DC2",
   "provisioningState": "Succeeded",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePortsLocations"
   }
   ```
5. Önceki adımlarda seçtiğiniz konumu temel alan bir ExpressRoute doğrudan kaynağı oluşturun.

   ExpressRoute Direct, QinQ ve Dot1Q kapsüllemeyi destekler. QinQ ' ı seçerseniz, her bir ExpressRoute bağlantı hattı dinamik olarak bir S-Tag atanır ve ExpressRoute doğrudan kaynağı genelinde benzersizdir. Devre üzerindeki her C etiketi, Devroute doğrudan kaynağında değil, devre üzerinde benzersiz olmalıdır.  

   Dot1Q Kapsüllemesi ' ı seçerseniz, tüm ExpressRoute doğrudan kaynağı genelinde C-Tag (VLAN) öğesinin benzersizliğini yönetmeniz gerekir.  

   > [!IMPORTANT]
   > ExpressRoute Direct yalnızca bir kapsülleme türü olabilir. ExpressRoute Direct kaynağını oluşturduktan sonra kapsülleme türünü değiştiremezsiniz.
   > 
 
   ```azurecli
   az network express-route port create -n $name -g $RGName --bandwidth 100 gbps  --encapsulation QinQ | Dot1Q --peering-location $PeeringLocationName -l $AzureRegion 
   ```

   > [!NOTE]
   > Ayrıca, **kapsülleme** özniteliğini **Dot1Q** olarak da ayarlayabilirsiniz. 
   >

   **Örnek çıkış**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Disabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "02ee21fe-4223-4942-a6bc-8d81daabc94f",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }  
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="resources"></a>Yetkilendirme (LOA) harfini oluşturma

En son oluşturulan ExpressRoute doğrudan kaynak adı, kaynak grubu adı ve bir müşteri adı ile LOA 'yı yazın ve (isteğe bağlı olarak) belgeyi depolamak için bir dosya konumu tanımlayın. Bir dosya yoluna başvurulmuyorsa, belge geçerli dizine indirilir.

```azurecli
az network express-route port generate-loa -n Contoso-Direct -g Contoso-Direct-rg --customer-name Contoso --destination C:\Users\SampleUser\Downloads\LOA.pdf
```

## <a name="change-adminstate-for-links"></a><a name="state"></a>Bağlantıların AdminState 'i Değiştir

Katman 1 testi yürütmek için bu işlemi kullanın. Her bir çapraz bağlantının, birincil ve ikincil bağlantı noktalarında her bir yönlendiricide düzgün bir şekilde düzeltme içinde olduğundan emin olun.

1. Bağlantıları **etkin** olarak ayarlayın. Her bağlantıyı **etkin** olarak ayarlamak için bu adımı tekrarlayın.

   [0] bağlantıları birincil bağlantı noktasıdır ve [1] bağlantıları ikincil bağlantı noktasıdır.

   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[0].adminState="Enabled"
   ```
   ```azurecli
   az network express-route port update -n Contoso-Direct -g Contoso-Direct-rg --set links[1].adminState="Enabled"
   ```
   **Örnek çıkış**

   ```output
   {
   "allocationDate": "Wednesday, October 17, 2018",
   "bandwidthInGbps": 100,
   "circuits": null,
   "encapsulation": "Dot1Q",
   "etag": "W/\"<etagnumber>\"",
   "etherType": "0x8100",
   "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
   "links": [
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link1",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link1",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-1",
      "type": "Microsoft.Network/expressRoutePorts/links"
    },
    {
      "adminState": "Enabled",
      "connectorType": "LC",
      "etag": "W/\"<etagnumber>\"",
      "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct/links/link2",
      "interfaceName": "HundredGigE2/2/2",
      "name": "link2",
      "patchPanelId": "PPID",
      "provisioningState": "Succeeded",
      "rackId": "RackID",
      "resourceGroup": "Contoso-Direct-rg",
      "routerName": "tst-09xgmr-cis-2",
      "type": "Microsoft.Network/expressRoutePorts/links"
    }
   ],
   "location": "westus",
   "mtu": "1500",
   "name": "Contoso-Direct",
   "peeringLocation": "Equinix-Ashburn-DC2",
   "provisionedBandwidthInGbps": 0.0,
   "provisioningState": "Succeeded",
   "resourceGroup": "Contoso-Direct-rg",
   "resourceGuid": "<resourceGUID>",
   "tags": null,
   "type": "Microsoft.Network/expressRoutePorts"
   }
   ```

   Kullanarak bağlantı noktalarını kapatmak için aynı yordamı kullanın `AdminState = "Disabled"` .

## <a name="create-a-circuit"></a><a name="circuit"></a>Bağlantı hattı oluşturma

Varsayılan olarak, ExpressRoute doğrudan kaynağını içeren abonelikte 10 devre oluşturabilirsiniz. Microsoft Desteği, varsayılan sınırı artırabilir. Sağlanan ve kullanılan bant genişliğini izlemenin sorumluluğundadır. Sağlanan bant genişliği, ExpressRoute doğrudan kaynağındaki tüm devrelerin bant genişliğinin toplamıdır. Kullanılan bant genişliği, temeldeki fiziksel arabirimlerin fiziksel kullanımdır.

Yalnızca burada özetlenen senaryoları desteklemek için ExpressRoute Direct üzerinde ek devre bant genişlikleri kullanabilirsiniz. Bant genişlikleri 40 Gbps ve 100 Gbps.

**Skutier** yerel, standart veya Premium olabilir.

**Skufamily** yalnızca MeteredData olabilir. ExpressRoute Direct 'te sınırsız desteklenmez.

ExpressRoute doğrudan kaynağında bir devre oluşturun:

  ```azurecli
  az network express-route create --express-route-port "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct" -n "Contoso-Direct-ckt" -g "Contoso-Direct-rg" --sku-family MeteredData --sku-tier Standard --bandwidth 100 Gbps
  ```

  Diğer bant genişlikleri 5 Gbps, 10 Gbps ve 40 GB/sn içerir.

  **Örnek çıkış**

  ```output
  {
  "allowClassicOperations": false,
  "allowGlobalReach": false,
  "authorizations": [],
  "bandwidthInGbps": 100.0,
  "circuitProvisioningState": "Enabled",
  "etag": "W/\"<etagnumber>\"",
  "expressRoutePort": {
    "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRoutePorts/Contoso-Direct",
    "resourceGroup": "Contoso-Direct-rg"
  },
  "gatewayManagerEtag": "",
  "id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/expressRouteCircuits/ERDirect-ckt-cli",
  "location": "westus",
  "name": "ERDirect-ckt-cli",
  "peerings": [],
  "provisioningState": "Succeeded",
  "resourceGroup": "Contoso-Direct-rg",
  "serviceKey": "<serviceKey>",
  "serviceProviderNotes": null,
  "serviceProviderProperties": null,
  "serviceProviderProvisioningState": "Provisioned",
  "sku": {
    "family": "MeteredData",
    "name": "Standard_MeteredData",
    "tier": "Standard"
  },
  "stag": null,
  "tags": null,
  "type": "Microsoft.Network/expressRouteCircuits"
  }  
  ```

## <a name="next-steps"></a>Sonraki adımlar

ExpressRoute Direct hakkında daha fazla bilgi için bkz. [genel bakış](expressroute-erdirect-about.md).
