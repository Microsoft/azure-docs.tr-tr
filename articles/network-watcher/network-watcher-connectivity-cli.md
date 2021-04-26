---
title: Bağlantı sorunlarını giderme-Azure CLı
titleSuffix: Azure Network Watcher
description: Azure CLı kullanarak Azure ağ Izleyicisi 'nde bağlantı sorunlarını giderme özelliğini nasıl kullanacağınızı öğrenin.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e93a9b7ba2c7f801761d33ccacfa3ecdb3c48503
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019797"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-the-azure-cli"></a>Azure CLı kullanarak Azure ağ Izleyicisi ile bağlantı sorunlarını giderme

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure CLI](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

Bir sanal makineden belirli bir uç noktaya doğrudan TCP bağlantısının kurulabildiğini doğrulamak için bağlantı sorunlarını giderme hakkında bilgi edinin.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede aşağıdaki kaynaklara sahip olduğunuz varsayılır:

* Bir bağlantı sorunlarını gidermek istediğiniz bölgedeki ağ Izleyicisi örneği.
* İle bağlantı sorunlarını gidermek için sanal makineler.

> [!IMPORTANT]
> Bağlantı sorunlarını gidermek için, üzerinde çalıştığınız VM 'nin `AzureNetworkWatcherExtension` VM uzantısının yüklü olması gerekir. Windows VM 'ye uzantı yüklemek için bkz. [Windows Için Azure ağ Izleyicisi Aracısı sanal makine uzantısı](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) ve Linux VM Için [Azure Ağ İzleyicisi Aracısı sanal makine uzantısı](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)' nı ziyaret edin. Uzantı hedef uç noktada gerekli değil.

## <a name="check-connectivity-to-a-virtual-machine"></a>Bir sanal makineye bağlantıyı denetle

Bu örnek, 80 bağlantı noktası üzerinden bir hedef sanal makineye bağlantıyı denetler.

### <a name="example"></a>Örnek

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-resource Database0 --dest-port 80
```

### <a name="response"></a>Yanıt

Aşağıdaki yanıt, önceki örnekteki bir örnektir.  Bu yanıtta, `ConnectionStatus` **ulaşılamaz** olur. Tüm yoklamaların başarısız olduğunu görebilirsiniz. `NetworkSecurityRule`Bağlantı noktası 80 ' de gelen trafiği engellemek için yapılandırılmış, Kullanıcı tarafından yapılandırılan adlandırılmış **UserRule_Port80** nedeniyle bağlantı Sanal Gereç üzerinde başarısız oldu. Bu bilgiler, bağlantı sorunlarını araştırmak için kullanılabilir.

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "bb01d336-d881-4808-9fbc-72f091974d68",
      "issues": [],
      "nextHopIds": [
        "f8b074e9-9980-496b-a35e-619f9bcbf648"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "10.1.2.4",
      "id": "f8b074e9-9980-496b-a35e-619f9bcbf648",
      "issues": [],
      "nextHopIds": [
        "8a5857f3-6ab8-4b11-b9bf-a046d66b8696"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/fw
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.3.4",
      "id": "8a5857f3-6ab8-4b11-b9bf-a046d66b8696",
      "issues": [
        {
          "context": [
            {
              "key": "RuleName",
              "value": "UserRule_Port80"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "NetworkSecurityRule"
        }
      ],
      "nextHopIds": [
        "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/au
Nic/ipConfigurations/ipconfig1",
      "type": "VirtualAppliance"
    },
    {
      "address": "10.1.4.4",
      "id": "6ce2f7a2-ceb4-4145-80e8-5d9f661655d6",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/db
Nic0/ipConfigurations/ipconfig1",
      "type": "VnetLocal"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="validate-routing-issues"></a>Yönlendirme sorunlarını doğrulama

Bu örnek, bir sanal makine ile uzak uç nokta arasındaki bağlantıyı denetler.

### <a name="example"></a>Örnek

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address 13.107.21.200 --dest-port 80
```

### <a name="response"></a>Yanıt

Aşağıdaki örnekte,, `connectionStatus` **ulaşılamaz** olarak gösterilir. `hops`Ayrıntılarda, `issues` trafiğin bir nedeniyle engellenmiş olduğunu görebilirsiniz `UserDefinedRoute` .

```json
{
  "avgLatencyInMs": null,
  "connectionStatus": "Unreachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "f2cb1868-2049-4839-b8ed-57a480d06f95",
      "issues": [
        {
          "context": [
            {
              "key": "RouteType",
              "value": "User"
            }
          ],
          "origin": "Outbound",
          "severity": "Error",
          "type": "UserDefinedRoute"
        }
      ],
      "nextHopIds": [
        "da4022db-0ab0-48c4-a507-dd4c03561ca5"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "13.107.21.200",
      "id": "da4022db-0ab0-48c4-a507-dd4c03561ca5",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Unknown",
      "type": "Destination"
    }
  ],
  "maxLatencyInMs": null,
  "minLatencyInMs": null,
  "probesFailed": 100,
  "probesSent": 100
}
```

## <a name="check-website-latency"></a>Web sitesi gecikmesini denetle

Aşağıdaki örnek bir Web sitesine olan bağlantıyı denetler.

### <a name="example"></a>Örnek

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://bing.com --dest-port 80
```

### <a name="response"></a>Yanıt

Aşağıdaki yanıtta, `connectionStatus` programları **erişilebilir** olarak görebilirsiniz. Bir bağlantı başarılı olduğunda, gecikme süresi değerleri sağlanır.

```json
{
  "avgLatencyInMs": 2,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "639c2d19-e163-4dfd-8737-5018dd1168ae",
      "issues": [],
      "nextHopIds": [
        "fd43a6e7-c758-4f48-90aa-8db99105a4a3"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/ap
pNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "204.79.197.200",
      "id": "fd43a6e7-c758-4f48-90aa-8db99105a4a3",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Depolama uç noktası bağlantısını denetle

Aşağıdaki örnek, bir sanal makineden bir blog depolama hesabına olan bağlantıyı denetler.

### <a name="example"></a>Örnek

```azurecli
az network watcher test-connectivity --resource-group ContosoRG --source-resource MultiTierApp0 --dest-address https://contosoexamplesa.blob.core.windows.net/
```

### <a name="response"></a>Yanıt

Aşağıdaki JSON, önceki cmdlet 'i çalıştırmanın örnek yanıtı örneğidir. Denetim başarılı olduğu için `connectionStatus` özelliği **erişilebilir** olarak gösterilir.  Depolama Blobu ve gecikme süresine ulaşmak için gereken atlama sayısıyla ilgili ayrıntılar verilmiştir.

```json
{
  "avgLatencyInMs": 1,
  "connectionStatus": "Reachable",
  "hops": [
    {
      "address": "10.1.1.4",
      "id": "5136acff-bf26-4c93-9966-4edb7dd40353",
      "issues": [],
      "nextHopIds": [
        "f8d958b7-3636-4d63-9441-602c1eb2fd56"
      ],
      "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
      "type": "Source"
    },
    {
      "address": "1.2.3.4",
      "id": "f8d958b7-3636-4d63-9441-602c1eb2fd56",
      "issues": [],
      "nextHopIds": [],
      "resourceId": "Internet",
      "type": "Internet"
    }
  ],
  "maxLatencyInMs": 7,
  "minLatencyInMs": 0,
  "probesFailed": 0,
  "probesSent": 100
}
```

## <a name="next-steps"></a>Sonraki adımlar

[Bir uyarı tetikledi bildiren paket yakalamayı](network-watcher-alert-triggered-packet-capture.md) görüntüleyerek sanal makine uyarıları ile paket yakalamaları otomatikleştirmeyi öğrenin

IP 'de belirli trafiğe izin verilip verilmeyeceğini [denetle onay IP Flow doğrula](diagnose-vm-network-traffic-filtering-problem.md) ' yı ziyaret ederek bulun