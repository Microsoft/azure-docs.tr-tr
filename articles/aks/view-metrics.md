---
title: Azure Kubernetes hizmeti (AKS) için küme ölçümlerini görüntüleme
description: Azure Kubernetes hizmeti (AKS) için küme ölçümlerini görüntüleyin.
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969511"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) için küme ölçümlerini görüntüleme

AKS, API sunucusu ve küme otomatik Scaler ve küme düğümleri dahil olmak üzere denetim düzlemi için bir dizi ölçüm sağlar. Bu ölçümler, kümenizin durumunu izlemenize ve sorunları gidermenize olanak sağlar. Azure portal kullanarak kümenizin ölçümlerini görüntüleyebilirsiniz.

> [!NOTE]
> Bu AKS kümesi ölçümleri, [Kubernetes tarafından sunulan ölçümlerin][kubernetes-metrics]bir alt kümesiyle örtüşüyor.

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Azure portal kullanarak AKS kümeniz için ölçümleri görüntüleme

AKS kümenizin ölçümlerini görüntülemek için:

1. [Azure Portal][azure-portal] oturum açın ve aks kümenize gidin.
1. *İzleme* bölümünün sol tarafında *ölçümler*' i seçin.
1. Görüntülemek istediğiniz ölçümler için bir grafik oluşturun. Örneğin, bir grafik oluşturun:
    1. *Kapsam* için kümenizi seçin.
    1. *Ölçüm ad alanı* için *kapsayıcı hizmeti (yönetilen) standart ölçümleri*' ni seçin.
    1. *Ölçüm* Için, *pods* altında *aşamasına göre pods sayısını* seçin.
    1. *Toplama* için *Ort*' ı seçin.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alternatif-metin}":::

Yukarıdaki örnek, *myakscluster* için Ortalama sayıda pod için ölçümleri gösterir.

## <a name="available-metrics"></a>Mevcut ölçümler

Aşağıdaki küme ölçümleri kullanılabilir:

| Name | Grup | ID | Description |
| --- | --- | --- | ---- |
| Esnek Istekler | API sunucusu (Önizleme) |apiserver_current_inflight_requests | İstek türü başına API sunucusu üzerinde şu anda etkin olan esnek istek sayısı üst sınırı. |
| Küme Durumu | Küme otomatik yüklemesi (Önizleme) | cluster_autoscaler_cluster_safe_to_autoscale | Küme otomatik Scaler 'ın kümede işlem yapıp olmayacağını belirler. |
| Ölçeği aşağı genişlet | Küme otomatik yüklemesi (Önizleme) | cluster_autoscaler_scale_down_in_cooldown | Ölçeğin aşağı doğru olup olmadığını belirler. bu zaman çerçevesi sırasında hiçbir düğüm kaldırılmaz. |
| Gereksiz düğümler | Küme otomatik yüklemesi (Önizleme) | cluster_autoscaler_unneeded_nodes_count | Küme auotscaler, bu düğümleri silinmek üzere aday olarak işaretler ve sonunda silinir. |
| Unschedulable pods | Küme otomatik yüklemesi (Önizleme) | cluster_autoscaler_unschedulable_pods_count | Kümede Şu anda unschedulable olan Pod sayısı. |
| Yönetilen kümede toplam kullanılabilir CPU çekirdeği sayısı | Düğümler | kube_node_status_allocatable_cpu_cores | Yönetilen kümede toplam kullanılabilir CPU çekirdeği sayısı. |
| Yönetilen kümede toplam kullanılabilir bellek miktarı | Düğümler | kube_node_status_allocatable_memory_bytes | Yönetilen kümede toplam kullanılabilir bellek miktarı. |
| Çeşitli düğüm koşullarına yönelik durumlar | Düğümler | kube_node_status_condition | Çeşitli düğüm koşullarına yönelik durumlar |
| CPU kullanımı Miliçekirdekler | Düğümler (Önizleme) | node_cpu_usage_millicores | Küme genelinde miliçekirdekte CPU kullanımının toplu ölçümü. |
| CPU kullanım yüzdesi | Düğümler (Önizleme) | node_cpu_usage_percentage | Küme genelinde yüzde cinsinden ölçülen ortalama CPU kullanımı. |
| Bellek RSS baytları | Düğümler (Önizleme) | node_memory_rss_bytes | Bayt cinsinden kullanılan kapsayıcı RSS belleği. |
| Bellek RSS yüzdesi | Düğümler (Önizleme) | node_memory_rss_percentage | Kapsayıcıda kullanılan kapsayıcı RSS belleği yüzdesi. |
| Bellek çalışma kümesi baytları | Düğümler (Önizleme) | node_memory_working_set_bytes | Bayt cinsinden kullanılan kapsayıcı çalışma kümesi belleği. |
| Bellek çalışma kümesi yüzdesi | Düğümler (Önizleme) | node_memory_working_set_percentage | Kapsayıcı çalışma kümesi belleği yüzde olarak kullanıldı. |
| Kullanılan disk bayt sayısı | Düğümler (Önizleme) | node_disk_usage_bytes | Cihaza göre bayt cinsinden kullanılan disk alanı. |
| Kullanılan disk yüzdesi | Düğümler (Önizleme) | node_disk_usage_percentage | Cihaz tarafından yüzde olarak kullanılan disk alanı. |
| Bayt cinsinden ağ | Düğümler (Önizleme) | node_network_in_bytes | Alınan ağ bayt sayısı. |
| Ağ çıkış baytları | Düğümler (Önizleme) | node_network_out_bytes | Ağ tarafından aktarılan baytlar. |
| Hazırlık durumundaki Pod sayısı | Pod | kube_pod_status_ready | *Hazırlık* durumundaki Pod sayısı. |
| Aşamasına göre Pod sayısı | Pod | kube_pod_status_phase | Aşamasına göre Pod sayısı. |

> [!IMPORTANT]
> Önizlemedeki ölçümler, önizleme aşamasında, adları ve açıklamaları dahil olmak üzere güncelleştirilebilirler veya değiştirilebilir.

## <a name="next-steps"></a>Sonraki adımlar

AKS için küme ölçümlerine ek olarak, Azure Izleyici 'yi AKS kümeniz ile de kullanabilirsiniz. AKS ile Azure Izleyici kullanma hakkında daha fazla bilgi için bkz. [kapsayıcılar Için Azure izleyici][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/