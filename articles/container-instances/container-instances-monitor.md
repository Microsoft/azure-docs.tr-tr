---
title: Azure Container Instances’taki kapsayıcıları izleme
description: Azure Container Instances kapsayıcılarında, CPU ve bellek gibi işlem kaynaklarının tüketimini izleme.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: a26789bb41fb3fb1e7dec376b7e187f45745ea65
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172249"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Azure Container Instances’taki kapsayıcı kaynaklarını izleme

[Azure izleyici][azure-monitoring] , kapsayıcılar örneklerinizin kullandığı işlem kaynakları hakkında öngörüler sağlar. Bu kaynak kullanım verileri, kapsayıcı gruplarınız için en iyi kaynak ayarlarını belirlemenize yardımcı olur. Azure Izleyici, kapsayıcı örneklerindeki ağ etkinliğini izleyen ölçümleri de sağlar.

Bu belgede, hem Azure portal hem de Azure CLı kullanan kapsayıcı örnekleri için Azure Izleyici ölçümleri toplanıyor.

> [!IMPORTANT]
> Azure Container Instances Azure Izleyici ölçümleri Şu anda önizleme aşamasındadır ve bazı [sınırlamalar geçerlidir](#preview-limitations). Önizlemeler, [ek kullanım koşullarını][terms-of-use] kabul etmeniz şartıyla kullanımınıza sunulur. Bu özelliğin bazı yönleri genel kullanıma açılmadan önce değişebilir.

## <a name="preview-limitations"></a>Önizleme sınırlamaları

Azure Izleyici ölçümleri Şu anda yalnızca Linux kapsayıcıları için kullanılabilir.

## <a name="available-metrics"></a>Mevcut ölçümler

Azure Izleyici [Azure Container Instances için aşağıdaki ölçümleri][supported-metrics]sağlar. Bu ölçümler bir kapsayıcı grubu ve bağımsız kapsayıcılar için kullanılabilir.

* **CPU kullanımı** - **miliçekirdekte**ölçülür. Bir milincil bir CPU Core 'un 1/1000 ' inden biridir, bu nedenle 500 milicore (veya 500 GB) bir CPU çekirdeğinin% 50 kullanımını temsil eder. Tüm çekirdekler genelinde **Ortalama kullanım** olarak toplanır.

* **Bellek kullanımı** - **ortalama bayt**olarak toplanır.

* Saniyede **alınan ağ baytları** ve saniye başına **aktarılan ağ baytları** / **saniye başına ortalama bayt**olarak toplanır. 

## <a name="get-metrics---azure-portal"></a>Ölçümleri alma - Azure portal

Kapsayıcı grubu oluşturulduğunda, Azure portalda Azure İzleyici verileri sağlanır. Bir kapsayıcı grubuna yönelik ölçümleri görmek için, kapsayıcı grubu için **genel bakış** sayfasına gidin. Burada, kullanılabilir ölçümlerin her biri için önceden oluşturulmuş grafikleri görebilirsiniz.

![çift grafik][dual-chart]

Birden çok kapsayıcı içeren bir kapsayıcı grubunda, ölçümleri kapsayıcıya göre sunmak için bir [Boyut][monitor-dimension] kullanın. Tek bir kapsayıcının ölçümlerinin yer aldığı bir grafik oluşturmak için aşağıdaki adımları izleyin:

1. **Genel bakış** sayfasında, **CPU**gibi ölçüm grafiklerinden birini seçin. 
1. **Bölmeyi Uygula** düğmesini seçin ve **kapsayıcı adı**' nı seçin.

![boyut][dimension]

## <a name="get-metrics---azure-cli"></a>Ölçümleri alma - Azure CLI

Kapsayıcı örnekleri ölçümleri de Azure CLı kullanılarak toplanabilir. Önce, aşağıdaki komutu kullanarak kapsayıcı grubunun kimliğini alın. `<resource-group>` değerini kaynak grubunuzun adıyla ve `<container-group>` değerini kapsayıcı grubunuzun adıyla değiştirin.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Aşağıdaki komutu kullanarak **CPU** kullanım ölçümlerini alın.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

[Desteklenen diğer ölçümleri][supported-metrics]almak için `--metric` komutundaki parametresinin değerini değiştirin. Örneğin, **bellek** kullanım ölçümlerini almak için aşağıdaki komutu kullanın. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Çok kapsayıcılı bir grup için, `containerName` boyut, kapsayıcı başına dönüş ölçümlerine eklenebilir.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Sonraki adımlar

Azure izlemeye [genel bakış][azure-monitoring]konusunda daha fazla bilgi edinin.

Azure Container Instances için bir ölçüm eşiğe ulaşıldığında bildirim almak için [ölçüm uyarıları][metric-alert] oluşturmayı öğrenin.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
