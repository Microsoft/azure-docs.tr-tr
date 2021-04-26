---
title: BD izlemesini kapsayıcı öngörüleri ile yapılandırma | Microsoft Docs
description: Bu makalede, kapsayıcı öngörüleri ile kalıcı birimler ile Kubernetes kümelerini izlemeyi nasıl yapılandırabileceğiniz açıklanmaktadır.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 7c6ddd62bf06d313987289e444962378cea43fc8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627906"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>BD izlemesini kapsayıcı öngörüleri ile yapılandırma

Aracı sürümü *ciprod10052020* ile başlayarak, kapsayıcılar Için Azure izleyici tümleşik aracı artık BD (kalıcı birim) kullanımını izlemeyi destekliyor. Aracı sürümü *ciprod01112021* ile aracı, durum, depolama sınıfı, tür, erişim modları ve diğer ayrıntılar hakkında bilgi dahil olmak üzere BD envanterini izlemeyi destekler.
## <a name="pv-metrics"></a>BD ölçümleri

Kapsayıcı öngörüleri otomatik olarak, 60-sn aralıklarında aşağıdaki ölçümleri toplayarak ve bunları **ınsi\ölçüm** tablosunda depolayarak BD kullanımını izlemeye başlar.

| Ölçüm adı | Ölçüm boyutu (Etiketler) | Ölçüm açıklaması |
|-----|-----------|----------|
| `pvUsedBytes`| Poduıd, podName, pvcName, pvcNamespace, capacityBytes, Clusterıd, clusterName| Belirli bir pod tarafından kullanılan talebe sahip belirli bir kalıcı birim için bayt cinsinden kullanılan alan. `capacityBytes` , veri alma maliyetini azaltmak ve sorguları basitleştirmek için Etiketler alanında bir boyut olarak ikiye katlanır.|

Toplanan [BD ölçümlerini yapılandırma](./container-insights-agent-config.md)hakkında daha fazla bilgi edinin.

## <a name="pv-inventory"></a>BD stoku

Kapsayıcılar için Azure Izleyici, aşağıdaki bilgileri 60 sn aralıklarla toplayıp **Kubepvınventory** tablosunda depolayarak PVS 'yi izlemeye otomatik olarak başlar.

|Veriler |Veri Kaynağı| Veri Türü| Alanlar|
|-----|-----------|----------|-------|
|Bir Kubernetes kümesindeki kalıcı birimlerin envanterini oluşturma |Kuin API 'SI |`KubePVInventory` |    PVName, PVCapacityBytes, PVCName, PVCNamespace, Pvdurum, PVAccessModes, PVType, Pvtypeınfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, Clusterıd, ClusterName, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Kalıcı birimleri izleme

Kapsayıcılar için Azure Izleyici, bu kullanım ölçümü için önceden yapılandırılmış grafikleri ve her küme için çalışma kitabı şablonlarındaki envanter bilgilerini içerir. Ayrıca, BD kullanımı için önerilen uyarıyı etkinleştirebilir ve Log Analytics bu ölçümleri sorgulayabilirsiniz.  

### <a name="workload-details-workbook"></a>İş yükü ayrıntıları çalışma kitabı

Belirli iş yükleri için kullanım grafiklerini, **Iş yükü ayrıntıları** çalışma kitabının, Öngörüler bölmesindeki çalışma kitaplarını görüntüle açılır listesinden veya **Içgörüler** bölmesindeki **raporlar (Önizleme) sekmesinden** bulunan çalışma kitapları ' ndan doğrudan bir aks kümesinden


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Azure Izleyici BD iş yükü çalışma kitabı örneği":::

### <a name="persistent-volume-details-workbook"></a>Kalıcı birim ayrıntıları çalışma kitabı

Sol taraftaki bölmeden, Öngörüler bölmesindeki çalışma **kitaplarını görüntüle** açılır listesinden veya Öngörüler bölmesindeki **raporlar (Önizleme)** sekmesinden, kalıcı birim **ayrıntıları** çalışma kitabındaki kalıcı birim envanteri genel bakış ' ı doğrudan bir aks kümesinden bulabilirsiniz.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Azure Izleyici BD ayrıntıları çalışma kitabı örneği":::

### <a name="persistent-volume-usage-recommended-alert"></a>Kalıcı Birim kullanımı önerilir uyarısı
Bir pod için Ortalama BD kullanımı %80 üzerinde olduğunda sizi uyarmak için önerilen bir uyarı sağlayabilirsiniz. [Burada](./container-insights-metric-alerts.md) uyarma ve varsayılan eşiği [burada](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps)geçersiz kılma hakkında daha fazla bilgi edinin.
## <a name="next-steps"></a>Sonraki adımlar

- Toplanan [BD ölçümleri hakkında](./container-insights-agent-config.md)daha fazla bilgi edinin.