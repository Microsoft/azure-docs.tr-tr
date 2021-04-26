---
title: Kapsayıcı öngörüleri ile HPA ölçümleri & dağıtım | Microsoft Docs
description: Bu makalede, kapsayıcı öngörüleriyle birlikte hangi dağıtım & HPA (yatay Pod otomatik Scaler) ölçümleri toplanır açıklanmaktadır.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717715"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Container Insights ile HPA ölçümleri & dağıtım

*Ciprod08072020* aracı sürümü ile başlayarak, kapsayıcı öngörüleri ile tümleşik aracı artık hpas & dağıtımlar için ölçümleri toplar.

## <a name="deployment-metrics"></a>Dağıtım ölçümleri

Kapsayıcı öngörüleri, 60 sec aralıklarında aşağıdaki ölçümleri toplayarak ve bunları **ınsijizmetrik** tablosunda depolayarak dağıtımları otomatik olarak başlatır:

|Ölçüm adı |Ölçüm boyutu (Etiketler) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, Deploymentstrateji, k8sNamespace, spec_replicas, status_replicas_available status_replicas_updated (Status. Updatedçoğaltmalar) | Bu dağıtım tarafından hedeflenen toplam hazır Pod sayısı (Status. readyçoğaltmalar). Bu ölçümün boyutları aşağıda verilmiştir. <ul> <li> Dağıtım-dağıtımın adı </li> <li> dağıtım için k8sNamespace-Kubernetes ad alanı </li> <li> deploymentstrateji-Pod 'yi yeni olanlarla değiştirmek için kullanılacak dağıtım stratejisi (spec. strateji. Type)</li><li> creationTime-dağıtım oluşturma zaman damgası </li> <li> spec_replicas-istenen Pod sayısı (spec. çoğaltmalar) </li> <li>status_replicas_available-bu dağıtım tarafından hedeflenen (en az minreadyseconds için hazır) toplam kullanılabilir Pod sayısı (Status. kullanılabilirliği blereplicas)</li><li>status_replicas_updated-bu dağıtım tarafından hedeflenen, istenen şablon belirtimine sahip (Status. Updatedçoğaltmalar), sonlandırılamayan toplam sayı sayısı </li></ul>|

## <a name="hpa-metrics"></a>HPA ölçümleri

Kapsayıcı öngörüleri otomatik olarak, 60 sec aralıklarında aşağıdaki ölçümleri toplayarak ve bunları **ınsiizmetriler** tablosunda depolayarak HPAs 'yi izlemeye başlar:

|Ölçüm adı |Ölçüm boyutu (Etiketler) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, Targetkinleştirilen d, hedefadı | Bu otomatik Scaler tarafından yönetilen (Status. Currentçoğaltmalar) geçerli sayıda kopya. Bu ölçümün boyutları aşağıda verilmiştir. <ul> <li> hPa-HPA adı </li> <li> HPA için k8sNamespace-Kubernetes ad alanı </li> <li> lastscaletime-hPa 'nin Pod sayısını (Status. lastscaletime) ölçeklendirilmesi için geçen süre</li><li> creationTime-HPA oluşturma zaman damgası </li> <li> spec_max_replicas-otomatik Scaler (spec. Maxçoğaltmalar) tarafından ayarlanaben üst sınır sayısı </li> <li> spec_min_replicas-otomatik Scaler 'ın ölçeklendirebileceği kopyaların sayısı için alt sınır (spec. Minçoğaltmalar) </li><li>status_desired_replicas-bu otomatik Scaler tarafından yönetilen (Status. desiredçoğaltmalar), istenen sayıda Pod çoğaltması</li><li>Targetkinleştirilen TID-HPA 'nın hedefinin türü (spec. scaleTargetRef. Kind) </li><li>targetName-HPA hedefinin adı (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Dağıtım & HPA grafikleri 

Kapsayıcı öngörüleri, tabloda daha önce listelenen ölçümler için, her küme için bir çalışma kitabı olarak önceden yapılandırılmış grafikler içerir. Sol bölmedeki **çalışma kitapları** ' nı ve Öngörüler Içindeki **çalışma kitaplarını görüntüle** açılan listesinden doğrudan bir aks kümesinden & hpa çalışma kitabı **dağıtımlarını** & dağıtımlarını bulabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Kuin durumu ölçümleri hakkında daha fazla bilgi edinmek için [Kubernetes Içindeki kuin durumu ölçümlerini](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) gözden geçirin.