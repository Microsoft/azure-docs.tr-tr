---
title: Azure Traffic Manager ölçümler ve uyarılar
description: Bu makalede, Azure 'da Traffic Manager için kullanılabilen ölçümleri ve uyarıları öğrenin.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: b18e0329aeb4e95e021c3326b6b428c10edc0c6e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100586426"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager ölçümleri ve uyarıları

Traffic Manager, size birden çok yönlendirme yöntemi ve uç nokta izleme seçeneği içeren DNS tabanlı yük dengelemesi sağlar. Bu makalede, müşteriler tarafından kullanılabilen ölçümler ve ilişkili uyarılar açıklanmaktadır. 

## <a name="metrics-available-in-traffic-manager"></a>Traffic Manager kullanılabilen ölçümler 

Traffic Manager, müşterilerin Traffic Manager kullanımını ve bu profilde uç noktalarının durumunu anlamak için kullanabileceği her profil için aşağıdaki ölçümleri sağlar.  

### <a name="queries-by-endpoint-returned"></a>Bitiş noktasına göre sorgular döndürüldü
Bir Traffic Manager profilinin belirli bir süre boyunca işlediği sorgu sayısını görüntülemek için [Bu ölçümü](../azure-monitor/essentials/metrics-supported.md) kullanın. Ayrıca, Traffic Manager bir uç noktanın sorgu yanıtlarına kaç kez döndürüldüğünü anlamanıza yardımcı olan bir uç nokta düzeyi ayrıntı düzeyiyle aynı bilgileri görüntüleyebilirsiniz.

Aşağıdaki örnekte Şekil 1 Traffic Manager profilin döndürdüğü tüm sorgu yanıtlarını görüntüler. 

  
![Tüm sorguların toplam görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Şekil 1: tüm sorgularla birleşik görünüm*
  
Şekil 2 ' de aynı bilgiler görüntülenir, ancak uç noktalara göre bölünür. Sonuç olarak, belirli bir uç noktanın döndürüldüğü sorgu yanıtlarının hacmini görebilirsiniz.

![Traffic Manager ölçümleri-uç nokta başına sorgu biriminin bölünmüş görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Şekil 2: bir uç nokta başına gösterilen sorgu birimi ile bölünmüş görünüm*

## <a name="endpoint-status-by-endpoint"></a>Uç nokta tarafından uç nokta durumu
Profildeki uç noktaların sistem durumunu anlamak için [Bu ölçümü](../azure-monitor/essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) kullanın. İki değer alır:
 - uç nokta varsa **1** kullanın.
 - uç nokta kapalıysa **0** kullanın.

Bu ölçüm, tüm ölçümlerin durumunu temsil eden bir toplam değer olarak gösterilebilir (Şekil 3) veya belirli uç noktaların durumunu göstermek için bölünebilir (Şekil 4 ' e bakın). İlki, toplama düzeyi **AVG** olarak seçilirse, bu ölçümün değeri tüm uç noktaların durumunun aritmetik ortasıdır. Örneğin, bir profilde iki uç nokta varsa ve yalnızca bir tane sağlıklı ise, Şekil 3 ' te gösterildiği gibi bu ölçümün değeri **0,50** olur. 


![Traffic Manager ölçümleri-uç nokta durumunun bileşik görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Şekil 3: uç nokta durum ölçümünün bileşik görünümü – "Ort" toplama seçildi*


![Traffic Manager ölçümleri-uç nokta durumunun bölünmüş görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Şekil 4: uç nokta durum ölçümlerinin bölünmüş görünümü*

Bu ölçümleri [Azure izleyici hizmetinin](../azure-monitor/essentials/metrics-supported.md)portalı, [REST API](/rest/api/monitor/), [Azure CLI](/cli/azure/monitor)ve [Azure PowerShell](/powershell/module/az.applicationinsights)veya Traffic Manager Portal deneyiminin ölçümler bölümünde kullanabilirsiniz.

## <a name="alerts-on-traffic-manager-metrics"></a>Traffic Manager ölçümlerinde uyarılar
Azure Izleyici, Traffic Manager ölçümleri işlemenin ve görüntülemenin yanı sıra müşterilerin bu ölçümler ile ilişkili uyarıları yapılandırmasına ve almasına olanak sağlar. Bir uyarının gerçekleşmesi, bu koşulların ne sıklıkta izlenmesi gerektiğini ve uyarıların size nasıl gönderilmesi gerektiğini belirlemek için bu ölçümlerde hangi koşulların karşılanması gerektiğini seçebilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici uyarıları belgeleri](../azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure İzleyici hizmeti](../azure-monitor/essentials/metrics-supported.md) hakkında daha fazla bilgi edinin
- [Azure izleyici kullanarak grafik oluşturmayı](../azure-monitor/essentials/metrics-getting-started.md#create-your-first-metric-chart) öğrenin