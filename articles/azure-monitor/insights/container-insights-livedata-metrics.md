---
title: Kapsayıcılar için Azure Izleyici ile ölçümleri gerçek zamanlı olarak görüntüleyin | Microsoft Docs
description: Bu makalede, kapsayıcılar için Azure Izleyici ile kubectl kullanılmadan ölçümlerin gerçek zamanlı görünümü açıklanır.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 4608058d347888ab77bd4303600f00b270d6fed5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514608"
---
# <a name="how-to-view-metrics-in-real-time"></a>Ölçümleri gerçek zamanlı görüntüleme

Kapsayıcılar için Azure Izleyici canlı veriler (Önizleme) özelliği, bir kümede gerçek zamanlı olarak düğüm ve pod durumu hakkında ölçümleri görselleştirmenize olanak tanır. Bu öngörüye dahil edilen performans grafiklerindeki verileri çağırmak, ayrıştırmak ve görselleştirmek için `kubectl top nodes`, `kubectl get pods –all-namespaces`ve `kubectl get nodes` komutlarına doğrudan erişim sağlar. 

Bu makale ayrıntılı bir genel bakış sağlar ve bu özelliğin nasıl kullanılacağını anlamanıza yardımcı olur.  

>[!NOTE]
>Bu özellikle [özel kümeler](https://azure.microsoft.com/updates/aks-private-cluster/) olarak etkinleştirilen aks kümeleri desteklenmez. Bu özellik, tarayıcınızdan bir ara sunucu aracılığıyla Kubernetes API 'sine doğrudan erişim sağlar. Bu proxy 'den Kubernetes API 'sini engellemek için ağ güvenliğinin etkinleştirilmesi, bu trafiği engeller. 

>[!NOTE]
>Bu özellik, Azure Çin dahil olmak üzere tüm Azure bölgelerinde kullanılabilir. Azure ABD kamu 'da Şu anda kullanılamıyor.

Canlı veri (Önizleme) özelliğini ayarlama veya sorunlarını gidermeyle ilgili yardım için [Kurulum kılavuzumuzu](container-insights-livedata-setup.md)gözden geçirin.

## <a name="how-it-works"></a>Nasıl çalışır? 

Canlı veriler (Önizleme) özelliği, Kubernetes API 'sine doğrudan erişir ve kimlik doğrulama modeliyle ilgili ek bilgilere [buradan](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)ulaşabilirsiniz. 

Bu özellik, varsayılan olarak her beş saniyede bir olan ölçüm uç noktalarına (`/api/v1/nodes`, `/apis/metrics.k8s.io/v1beta1/nodes`ve `/api/v1/pods`dahil) karşı bir yoklama işlemi gerçekleştirir. Bu veriler tarayıcınızda önbelleğe alınır ve **canlı çalış (Önizleme)** seçeneği belirlenerek **küme** sekmesindeki kapsayıcılar için Azure izleyici 'de yer alan dört Performans grafiklerinde görüntülenir. Sonraki her yoklama, beş dakikalık bir görselleştirme penceresine göre yapılır. 

![Küme görünümündeki canlı git seçeneği](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

Yoklama aralığı, 1, 5, 15 ve 30 saniyede bir yeni veri yoklamasını ayarlamanıza olanak sağlayan **set Interval** açılır listesinden yapılandırılır. 

![Canlı açılan yoklama aralığına git](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.ping.png)

>[!IMPORTANT]
>Kısa bir süre boyunca sorun giderirken yoklama aralığını bir saniye olarak ayarlamayı öneririz. Bu istekler, kümenizde Kubernetes API 'sinin kullanılabilirliğini ve azaltmasını etkileyebilir. Daha sonra, daha uzun bir yoklama aralığına yeniden yapılandırın. 

>[!IMPORTANT]
>Bu özelliğin çalışması sırasında hiçbir veri kalıcı olarak depolanmaz. Bu oturum sırasında yakalanan tüm bilgiler, tarayıcınızı kapattığınızda veya özellikten ayrıldığınızda hemen silinir. Veriler yalnızca beş dakikalık bir pencere içinde görselleştirme için mevcut olmaya devam eder; beş dakikadan daha eski olan ölçümler de kalıcı olarak silinir.

Bu grafikler, canlı modda görüntülediğiniz son Azure panosuna sabitlenemez.

## <a name="metrics-captured"></a>Yakalanan ölçümler

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Düğüm CPU kullanımı%/düğüm bellek kullanımı% 

Bu iki performans grafiği, `kubectl top nodes` çağırma ve% **CPU** ve **bellek%** sütunlarının sonuçlarını ilgili grafiğe yakalamanın bir eşdeğerine eşlenir. 

![Kubectl üst düğümleri örnek sonuçları](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Düğümler CPU kullanım yüzdesi grafiği](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Düğüm belleği kullanım yüzdesi grafiği](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

Yüzdebirlik hesaplamaları, kümenizdeki aykırı değerleri belirlemenize yardımcı olmak için daha büyük kümeler halinde çalışır. Örneğin, düğümlerin ölçeği azaltma amaçlarıyla kullanıldığını anlamak için. **En az** toplamayı kullanarak hangi düğümlerin kümede düşük kullanım olduğunu görebilirsiniz. Daha fazla araştırma yapmak için **düğümler** sekmesini seçin ve Kılavuzu CPU veya bellek kullanımına göre sıralayın.

Bu Ayrıca, hangi düğümlerin sınırlarına itilmekte olduğunu ve ölçek genişletme için gerekli olabileceğini anlamanıza yardımcı olur. **Maksimum** ve **P95** toplamaların kullanımı, kümede yüksek kaynak kullanımıyla düğüm olup olmadığını görmenizi sağlayabilir. Daha fazla araştırma için **düğümler** sekmesine geçiş yapmanız gerekir.

### <a name="node-count"></a>Düğüm sayısı

Bu performans grafiği `kubectl get nodes` çağırma ve **durum** sütununu durum türlerine göre gruplanmış bir grafikle eşleme ile eşlenir.

![Kubectl düğüm al örnek sonuçları](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Düğüm sayısı grafiği](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

Düğümler, **Ready** ya da **Ready** durumunda bildirilir. Bunlar sayılır (ve toplam bir sayı oluşturulur) ve bu iki toplamaların sonuçları gösterilir.
Örneğin, düğümlerinizin başarısız durumlara düşmesini anlamak için. Hazırlama dışı **toplamayı kullanarak** kümenizdeki düğüm sayısını, şu anda **henüz Ready** durumunda görebilirsiniz.

### <a name="active-pod-count"></a>Etkin Pod sayısı

Bu performans grafiği `kubectl get pods –all-namespaces` çağırma ile eşleşir ve grafiği durum türlerine göre gruplandırılan **durum** sütununu eşler.

![Kubectl Get Pod örnek sonuçları](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Düğüm Pod sayısı grafiği](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>`kubectl` tarafından yorumlanan durum adları, grafikte tam olarak eşleşmeyebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Önceden tanımlanmış sorguları ve uyarı oluşturma, görselleştirmeler oluşturmak veya kümelerinizde daha fazla analiz yapmak için örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin.