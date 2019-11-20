---
title: Kapsayıcılar için Azure Izleyici ile canlı verileri (Önizleme) görüntüleme | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de kapsayıcılar için kubectl kullanmadan Kubernetes günlüklerinin, olaylarının ve pod ölçümlerinin gerçek zamanlı görünümü açıklanmaktadır.
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
ms.openlocfilehash: 9c497c73d42e1fb8851c5293010098d843297250
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514426"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Kubernetes günlüklerini, olayları ve pod ölçümlerini gerçek zamanlı olarak görüntüleme

Kapsayıcılar için Azure Izleyici, Azure Kubernetes Service (AKS) kapsayıcı günlüklerine (stdout/stderror), olaylara ve pod ölçümlere doğrudan erişim sağlayan gelişmiş bir tanılama özelliği olan canlı veriler (Önizleme) özelliğini içerir. `kubectl logs -c`, `kubectl get` olaylarına ve `kubectl top pods`doğrudan erişim sunar. Konsol bölmesi, sorun giderme sorunlarını gerçek zamanlı olarak daha fazla yardımcı olmak için kapsayıcı altyapısı tarafından oluşturulan günlükleri, olayları ve ölçümleri gösterir.

Bu makale ayrıntılı bir genel bakış sağlar ve bu özelliğin nasıl kullanılacağını anlamanıza yardımcı olur. 

>[!NOTE]
>Bu özellikle [özel kümeler](https://azure.microsoft.com/updates/aks-private-cluster/) olarak etkinleştirilen aks kümeleri desteklenmez. Bu özellik, tarayıcınızdan bir ara sunucu aracılığıyla Kubernetes API 'sine doğrudan erişim sağlar. Bu proxy 'den Kubernetes API 'sini engellemek için ağ güvenliğinin etkinleştirilmesi, bu trafiği engeller. 

>[!NOTE]
>Bu özellik, Azure Çin dahil olmak üzere tüm Azure bölgelerinde kullanılabilir. Azure ABD kamu 'da Şu anda kullanılamıyor.

Canlı veri (Önizleme) özelliğini ayarlama veya sorunlarını gidermeyle ilgili yardım için [Kurulum kılavuzumuzu](container-insights-livedata-setup.md)gözden geçirin. Bu özellik, Kubernetes API 'sine doğrudan erişir ve kimlik doğrulama modeliyle ilgili ek bilgilere [buradan](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)ulaşabilirsiniz. 

## <a name="live-data-preview-functionality-overview"></a>Canlı veri (Önizleme) işlevlerine genel bakış

### <a name="search"></a>Arama

![Canlı veri konsol bölmesi filtre örneği](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

Canlı veriler (Önizleme) özelliği, arama işlevlerini içerir. **Arama** alanında, önemli bir sözcük veya terim yazarak sonuçları filtreleyebilirsiniz ve tüm eşleşen sonuçlar hızlı incelemeye izin verecek şekilde vurgulanır. Olayları görüntülerken, arama çubuğunun sağında bulunan **filtre** hap 'i kullanarak sonuçları da sınırlayabilirsiniz. Seçtiğiniz kaynağa bağlı olarak, hap, arasından seçim yapmak için bir pod, Namespace veya Cluster listeler.  

### <a name="scroll-lock-and-pause"></a>Scroll Lock ve Duraklat 

Otomatik kaydırmayı askıya almak ve bölmenin davranışını denetlemek için, yeni verileri okuma bölümünde el ile kaydırabilmenizi sağlayan **kaydırma** seçeneğini kullanabilirsiniz. Otomatik kaydırmayı yeniden etkinleştirmek için **kaydırma** seçeneğini tekrar seçmeniz yeterlidir. Ayrıca, **Duraklat** seçeneğini belirleyerek günlük veya olay verilerinin alınmasını da duraklatabilirsiniz ve devam etmek Için hazırsanız **oynat**' ı seçmeniz yeterlidir.  

![Canlı veri konsol bölmesi canlı görünümü Duraklat](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>Bir sorunu giderirken kısa bir süre boyunca yalnızca otomatik kaydırma askıya alıp duraklamasını öneririz. Bu istekler, kümenizde Kubernetes API 'sinin kullanılabilirliğini ve azaltmasını etkileyebilir. 

>[!IMPORTANT]
>Bu özelliğin çalışması sırasında hiçbir veri kalıcı olarak depolanmaz. Tarayıcınızı kapattığınızda veya uygulamadan çıktığınızda, oturum sırasında yakalanan tüm bilgiler silinir. Veriler yalnızca ölçüm özelliğinin beş dakikalık penceresinde görselleştirme için mevcut olmaya devam eder; beş dakikadan daha eski olan ölçümler de silinir. Canlı veriler (Önizleme), makul bellek kullanım limitlerinin (burada daha belirgin olması gerekir, makul mi olmalıdır). 

## <a name="view-logs"></a>Günlükleri görüntüleme

Gerçek zamanlı günlük verilerini **düğümler**, **denetleyiciler**ve **kapsayıcılar** görünümünden kapsayıcı altyapısı tarafından oluşturulan şekilde görüntüleyebilirsiniz. Günlük verilerini görüntülemek için aşağıdaki adımları gerçekleştirin.

1. Azure portal, AKS kümesi kaynak grubuna gidin ve AKS kaynağınızı seçin.

2. AKS kümesi panosunda, sol taraftaki **izleme** altında **Öngörüler**' i seçin. 

3. **Düğümler**, **denetleyiciler**veya **kapsayıcılar** sekmesinden birini seçin.

4. Performans kılavuzundan bir nesne seçin ve sağ tarafta bulunan Özellikler bölmesinde **canlı verileri görüntüle (Önizleme)** seçeneğini belirleyin. AKS kümesi, Azure AD kullanarak çoklu oturum açma ile yapılandırıldıysa, bu tarayıcı oturumu sırasında ilk kullanımda kimlik doğrulaması yapmanız istenir. Hesabınızı seçin ve Azure ile kimlik doğrulamayı doldurun.  

    >[!NOTE]
    >Özellikler bölmesinde **Analytics 'Te görünüm** seçeneğini belirleyerek Log Analytics çalışma alanınızdaki verileri görüntülerken, günlük araması sonuçları muhtemelen **düğümleri**, **Daemon kümelerini**, **çoğaltma kümelerini**, **işleri**ve  **Artık mevcut olmayan cron Işleri**, **pods**ve **kapsayıcılar** . `kubectl` ' de kullanılamayan bir kapsayıcı için günlüklere arama girişimi de burada başarısız olur. Geçmiş günlükleri, olayları ve ölçümleri görüntüleme hakkında daha fazla bilgi edinmek için [Analytics özelliğindeki görünümü](container-insights-log-search.md#search-logs-to-analyze-data) gözden geçirin.  

Başarıyla kimlik doğrulamasından geçtikten sonra, canlı veriler (Önizleme) konsol bölmesi, günlük verilerini sürekli bir akışta görüntüleyebileceğiniz performans verileri kılavuzunun altında görüntülenir. Getirme durumu göstergesi, bölmenin en sağında yer alan yeşil bir onay işareti gösteriyorsa, verilerin alınabilmesi ve konsolunuza akışa başlaması anlamına gelir.  

![Düğüm özellikleri bölmesi görünüm verileri seçeneği](./media/container-insights-livedata-overview/node-properties-pane.png)  

Bölme başlığı, kapsayıcının gruplandırıldığı Pod 'ın adını gösterir.

## <a name="view-events"></a>Etkinlikleri görüntüleme

Bir kapsayıcı, Pod, düğüm, ReplicaSet, DaemonSet, iş, CronJob ya da olduğunda, kapsayıcı motoru tarafından **düğümler**, **denetleyiciler**, **kapsayıcılar**ve **dağıtımlar (Önizleme)** görünümünden oluşturulan gerçek zamanlı olay verilerini görüntüleyebilirsiniz. Dağıtım seçildi. Olayları görüntülemek için aşağıdaki adımları gerçekleştirin.

1. Azure portal, AKS kümesi kaynak grubuna gidin ve AKS kaynağınızı seçin.

2. AKS kümesi panosunda, sol taraftaki **izleme** altında **Öngörüler**' i seçin. 

3. **Düğümler**, **denetleyiciler**, **kapsayıcılar**ya da **dağıtımlar (Önizleme)** sekmesini seçin.

4. Performans kılavuzundan bir nesne seçin ve sağ tarafta bulunan Özellikler bölmesinde **canlı verileri görüntüle (Önizleme)** seçeneğini belirleyin. AKS kümesi, Azure AD kullanarak çoklu oturum açma ile yapılandırıldıysa, bu tarayıcı oturumu sırasında ilk kullanımda kimlik doğrulaması yapmanız istenir. Hesabınızı seçin ve Azure ile kimlik doğrulamayı doldurun.  

    >[!NOTE]
    >Özellikler bölmesinde **Analytics 'Te görünüm** seçeneğini belirleyerek Log Analytics çalışma alanınızdaki verileri görüntülerken, günlük araması sonuçları muhtemelen **düğümleri**, **Daemon kümelerini**, **çoğaltma kümelerini**, **işleri**ve  **Artık mevcut olmayan cron Işleri**, **pods**ve **kapsayıcılar** . `kubectl` ' de kullanılamayan bir kapsayıcı için günlüklere arama girişimi de burada başarısız olur. Geçmiş günlükleri, olayları ve ölçümleri görüntüleme hakkında daha fazla bilgi edinmek için [Analytics özelliğindeki görünümü](container-insights-log-search.md#search-logs-to-analyze-data) gözden geçirin.  

Başarıyla kimlik doğrulamasından geçtikten sonra, canlı veriler (Önizleme) konsol bölmesi performans verileri kılavuzunun altında görüntülenir. Getirme durumu göstergesi, bölmenin en sağında yer alan yeşil bir onay işareti gösteriyorsa, verilerin alınabilmesi ve konsolunuza akışa başlaması anlamına gelir. 
    
Seçtiğiniz nesne bir kapsayıcı ise, bölmedeki **Olaylar** seçeneğini belirleyin. Bir düğüm, Pod veya denetleyici seçtiyseniz, olayları görüntülemek otomatik olarak seçilir. 

    ![Controller properties pane view events](./media/container-insights-livedata-overview/controller-properties-live-events.png)  

Bölme başlığı, kapsayıcının gruplandırıldığı Pod 'ın adını gösterir.

### <a name="filter-events"></a>Olayları filtreleme 

Olayları görüntülerken, arama çubuğunun sağında bulunan **filtre** hap 'i kullanarak sonuçları da sınırlayabilirsiniz. Seçtiğiniz kaynağa bağlı olarak, hap, arasından seçim yapmak için bir pod, Namespace veya Cluster listeler.  

## <a name="view-metrics"></a>Ölçümleri görüntüle 

Gerçek zamanlı ölçüm verilerini, **düğüm** veya **denetleyiciler** görünümünden yalnızca bir **Pod** seçildiğinde kapsayıcı motoru tarafından oluşturulan şekilde görüntüleyebilirsiniz. Ölçümleri görüntülemek için aşağıdaki adımları gerçekleştirin.

1. Azure portal, AKS kümesi kaynak grubuna gidin ve AKS kaynağınızı seçin.

2. AKS kümesi panosunda, sol taraftaki **izleme** altında **Öngörüler**' i seçin. 

3. **Düğümler** ya da **denetleyiciler** sekmesini seçin.

4. Performans kılavuzundan bir **Pod** nesnesi seçin ve sağ tarafta bulunan Özellikler bölmesinde **canlı verileri görüntüle (Önizleme)** seçeneğini belirleyin. AKS kümesi, Azure AD kullanarak çoklu oturum açma ile yapılandırıldıysa, bu tarayıcı oturumu sırasında ilk kullanımda kimlik doğrulaması yapmanız istenir. Hesabınızı seçin ve Azure ile kimlik doğrulamayı doldurun.  

    >[!NOTE]
    >Özellikler bölmesinde **Analytics 'Te görünüm** seçeneğini belirleyerek Log Analytics çalışma alanınızdaki verileri görüntülerken, günlük araması sonuçları muhtemelen **düğümleri**, **Daemon kümelerini**, **çoğaltma kümelerini**, **işleri**ve  **Artık mevcut olmayan cron Işleri**, **pods**ve **kapsayıcılar** . `kubectl` ' de kullanılamayan bir kapsayıcı için günlüklere arama girişimi de burada başarısız olur. Geçmiş günlükleri, olayları ve ölçümleri görüntüleme hakkında daha fazla bilgi edinmek için [Analytics özelliğindeki görünümü](container-insights-log-search.md#search-logs-to-analyze-data) gözden geçirin.  

Başarıyla kimlik doğrulamasından geçtikten sonra, canlı veriler (Önizleme) konsol bölmesi performans verileri kılavuzunun altında görüntülenir. Ölçüm verileri alınır ve iki grafikte sunum için konsolunuza akışa başlar. Bölme başlığı, kapsayıcının gruplandırıldığı Pod 'ın adını gösterir.

![Pod ölçümleri örneğini görüntüle](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>Sonraki adımlar

- Azure Izleyici 'yi kullanmayı ve AKS kümenizin diğer yönlerini izlemeyi öğrenmeye devam etmek için bkz. [Azure Kubernetes hizmet durumunu görüntüleme](container-insights-analyze.md).

- Önceden tanımlanmış sorguları ve uyarı oluşturma, görselleştirmeler oluşturmak veya kümelerinizde daha fazla analiz yapmak için örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin.