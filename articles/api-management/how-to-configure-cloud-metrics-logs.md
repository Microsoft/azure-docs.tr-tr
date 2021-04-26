---
title: Azure API Management şirket içinde barındırılan ağ geçidi için bulut ölçümlerini ve günlüklerini yapılandırma | Microsoft Docs
description: Azure API Management şirket içinde barındırılan ağ geçidi için bulut ölçümlerini ve günlüklerini yapılandırma hakkında bilgi edinin
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: c420c62e6f8f09a2b29398590cdb4ad410e5d296
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574064"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management şirket içinde barındırılan ağ geçidi için bulut ölçümlerini ve günlüklerini yapılandırma

Bu makalede, [Şirket içinde barındırılan ağ geçidi](./self-hosted-gateway-overview.md)için bulut ölçümlerini ve günlüklerini yapılandırmaya ilişkin ayrıntılar sağlanmaktadır.

Şirket içinde barındırılan ağ geçidinin bir API Management hizmeti ile ilişkilendirilmesi ve 443 numaralı bağlantı noktasında Azure 'a giden TCP/IP bağlantısı olması gerekir. Ağ Geçidi, için yapılandırılmışsa Azure 'a telemetri göndermek için giden bağlantıyı kullanır. 

## <a name="metrics"></a>Ölçümler
Varsayılan olarak, şirket içinde barındırılan ağ geçidi, [buluttaki](api-management-howto-use-azure-monitor.md)yönetilen ağ geçidiyle aynı şekilde [Azure izleyici](https://azure.microsoft.com/services/monitor/)aracılığıyla bir dizi ölçüm yayar. 

Özellik, `telemetry.metrics.cloud` ağ geçidi dağıtımının ConfigMap içindeki anahtar kullanılarak etkinleştirilebilir veya devre dışı bırakılabilir. Kullanılabilir yapılandırmaların bir dökümü aşağıda verilmiştir:

| Alan  | Varsayılan | Description |
| ------------- | ------------- | ------------- |
| telemetry.metrics.cloud  | `true` | Azure Izleyici aracılığıyla günlüğe kaydetmeyi etkinleştir. Değer, olabilir `true` `false` . |


Örnek bir yapılandırma aşağıda verilmiştir:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

Şirket içinde barındırılan ağ geçidi Şu anda Azure Izleyici aracılığıyla aşağıdaki ölçümleri yayar:

| Metric  | Açıklama |
| ------------- | ------------- |
| İstekler  | Dönemdeki API isteklerinin sayısı |
| Ağ Geçidi isteklerinin süresi | Ağ geçidinin isteği aldığı andan, yanıtın tamamen gönderildiği ana kadar geçen milisaniye cinsinden süre |
| Arka uç isteklerinin süresi | Genel arka uç GÇ (bağlanma, gönderme ve alma bayt’ları) için harcanan milisaniye sayısı  |

## <a name="logs"></a>Günlükler

Şirket içinde barındırılan ağ geçidi Şu anda buluta [tanılama günlükleri](./api-management-howto-use-azure-monitor.md#activity-logs) göndermiyor. Ancak, otomatik olarak barındırılan ağ geçidinin dağıtıldığı [günlükleri yerel olarak yapılandırmak ve sürdürmek](how-to-configure-local-metrics-logs.md) mümkündür. 

[Azure Kubernetes hizmetinde](https://azure.microsoft.com/services/kubernetes-service/)bir ağ geçidi dağıtılmışsa, kapsayıcılarınızdaki günlükleri toplamak ve bunları Log Analytics görüntülemek için [kapsayıcılar için Azure izleyici](../azure-monitor/containers/container-insights-overview.md) 'yi etkinleştirebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management Self-barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md)
* [Günlükleri yerel olarak yapılandırma ve kalıcı](how-to-configure-local-metrics-logs.md) hale getirme hakkında bilgi edinin
