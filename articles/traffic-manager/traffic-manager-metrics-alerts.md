---
title: Azure Traffic Manager ölçümler ve uyarılar
description: Bu makalede, Azure 'da Traffic Manager için kullanılabilen ölçümleri ve uyarıları öğrenin.
services: traffic-manager
author: asudbring
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 7e374e11053f0a1baabed72fdb240b34e2107b2a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038078"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Traffic Manager ölçümleri ve uyarıları

Traffic Manager, size birden çok yönlendirme yöntemi ve uç nokta izleme seçeneği içeren DNS tabanlı yük dengelemesi sağlar. Bu makalede, müşteriler tarafından kullanılabilen ölçümler ve ilişkili uyarılar açıklanmaktadır. 

## <a name="metrics-available-in-traffic-manager"></a>Traffic Manager kullanılabilen ölçümler 

Traffic Manager, müşterilerin Traffic Manager kullanımını ve bu profilde uç noktalarının durumunu anlamak için kullanabileceği her profil için aşağıdaki ölçümleri sağlar.  

### <a name="queries-by-endpoint-returned"></a>Bitiş noktasına göre sorgular döndürüldü
Bir Traffic Manager profilinin belirli bir süre boyunca işlediği sorgu sayısını görüntülemek için [Bu ölçümü](../azure-monitor/platform/metrics-supported.md) kullanın. Ayrıca, Traffic Manager bir uç noktanın sorgu yanıtlarına kaç kez döndürüldüğünü anlamanıza yardımcı olan bir uç nokta düzeyi ayrıntı düzeyiyle aynı bilgileri görüntüleyebilirsiniz.

Aşağıdaki örnekte Şekil 1 Traffic Manager profilin döndürdüğü tüm sorgu yanıtlarını görüntüler. 

  
![Tüm sorguların toplam görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Şekil 1: tüm sorgularla birleşik görünüm*
  
Şekil 2 ' de aynı bilgiler görüntülenir, ancak uç noktalara göre bölünür. Sonuç olarak, belirli bir uç noktanın döndürüldüğü sorgu yanıtlarının hacmini görebilirsiniz.

![Traffic Manager ölçümleri-uç nokta başına sorgu biriminin bölünmüş görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Şekil 2: bir uç nokta başına gösterilen sorgu birimi ile bölünmüş görünüm*

## <a name="endpoint-status-by-endpoint"></a>Uç nokta tarafından uç nokta durumu
Profildeki uç noktaların sistem durumunu anlamak için [Bu ölçümü](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) kullanın. İki değer alır:
 - uç nokta varsa **1** kullanın.
 - uç nokta kapalıysa **0** kullanın.

Bu ölçüm, tüm ölçümlerin durumunu temsil eden bir toplam değer olarak gösterilebilir (Şekil 3) veya belirli uç noktaların durumunu göstermek için bölünebilir (Şekil 4 ' e bakın). İlki, toplama düzeyi **AVG**olarak seçilirse, bu ölçümün değeri tüm uç noktaların durumunun aritmetik ortasıdır. Örneğin, bir profilde iki uç nokta varsa ve yalnızca bir tane sağlıklı ise, Şekil 3 ' te gösterildiği gibi bu ölçümün değeri **0,50** olur. 


![Traffic Manager ölçümleri-uç nokta durumunun bileşik görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Şekil 3: uç nokta durum ölçümünün bileşik görünümü – "Ort" toplama seçildi*


![Traffic Manager ölçümleri-uç nokta durumunun bölünmüş görünümü](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Şekil 4: uç nokta durum ölçümlerinin bölünmüş görünümü*

Bu ölçümleri [Azure izleyici hizmetinin](../azure-monitor/platform/metrics-supported.md)portalı, [REST API](https://docs.microsoft.com/rest/api/monitor/), [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)ve [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)veya Traffic Manager Portal deneyiminin ölçümler bölümünde kullanabilirsiniz.

## <a name="alerts-on-traffic-manager-metrics"></a>Traffic Manager ölçümlerinde uyarılar
Azure Izleyici, Traffic Manager ölçümleri işlemenin ve görüntülemenin yanı sıra müşterilerin bu ölçümler ile ilişkili uyarıları yapılandırmasına ve almasına olanak sağlar. Bir uyarının gerçekleşmesi, bu koşulların ne sıklıkta izlenmesi gerektiğini ve uyarıların size nasıl gönderilmesi gerektiğini belirlemek için bu ölçümlerde hangi koşulların karşılanması gerektiğini seçebilirsiniz. Daha fazla bilgi için bkz. [Azure izleyici uyarıları belgeleri](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure İzleyici hizmeti](../azure-monitor/platform/metrics-supported.md) hakkında daha fazla bilgi edinin
- [Azure izleyici kullanarak grafik oluşturmayı](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart) öğrenin
