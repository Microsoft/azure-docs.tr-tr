---
title: Kapsayıcılar için Azure Izleyici dağıtımlarını görüntüleme (Önizleme) | Microsoft Docs
description: Bu makalede, Azure Izleyici 'de kapsayıcılar için kubectl kullanılmadan Kubernetes dağıtımlarının gerçek zamanlı görünümü açıklanır.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: c7135aec98936b5247b39ae659e21735168bd289
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514387"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Dağıtımı (Önizleme) gerçek zamanlı görüntüleme

Kapsayıcılar için Azure Izleyici ile, görünüm dağıtımları (Önizleme) özelliği, `kubeclt get deployments` ve `kubectl describe deployment {your deployment}` komutlarını kullanıma sunarak gerçek zamanlı olarak Kubernetes dağıtım nesnelerine doğrudan erişim sağlar. 

>[!NOTE]
>Bu özellikle [özel kümeler](https://azure.microsoft.com/updates/aks-private-cluster/) olarak etkinleştirilen aks kümeleri desteklenmez. Bu özellik, tarayıcınızdan bir ara sunucu aracılığıyla Kubernetes API 'sine doğrudan erişim sağlar. Bu proxy 'den Kubernetes API 'sini engellemek için ağ güvenliğinin etkinleştirilmesi, bu trafiği engeller. 

>[!NOTE]
>Bu özellik, Azure Çin dahil olmak üzere tüm Azure bölgelerinde kullanılabilir. Azure ABD kamu 'da Şu anda kullanılamıyor.

Daha fazla bilgi edinmek için [dağıtımlar](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)hakkındaki Kubernetes belgelerini gözden geçirin. 

## <a name="how-it-works"></a>Nasıl çalışır

Canlı veriler (Önizleme) özelliği, Kubernetes API 'sine doğrudan erişir ve kimlik doğrulama modeliyle ilgili ek bilgilere [buradan](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)ulaşabilirsiniz. 

Dağıtımlar (Önizleme) özelliği, dağıtımlar uç noktası `/apis/apps/v1/deployments`karşı bir kez (yenilenebilir) yük gerçekleştirir. Bu, belirli bir dağıtımı seçmenizi ve dağıtım uç noktası `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`karşı ilgili dağıtımın ayrıntılarını yapılandırmanızı sağlar. 

Sayfanın sol üst kısmında **Yenile** seçeneği belirlendiğinde dağıtım listesi yenilenir. Bu, `kubectl` komutunu yeniden çalıştırmaya benzetir. 

>[!IMPORTANT]
>Bu özelliğin çalışması sırasında hiçbir veri kalıcı olarak depolanmaz. Tarayıcınızı kapattığınızda veya uygulamadan çıktığınızda, oturum sırasında yakalanan tüm bilgiler silinir.  

>[!NOTE]
>Konsoldan bir Azure panosuna canlı veri (Önizleme) verileri sabitleyemezsiniz.

## <a name="deployments-describe"></a>Dağıtımlar şunları anlatmaktadır

`kubectl describe deployment`eşdeğer olan bir dağıtımın ayrıntılarını görüntülemek için aşağıdaki adımları gerçekleştirin.

1. Azure portal, AKS kümesi kaynak grubuna gidin ve AKS kaynağınızı seçin.

2. AKS kümesi panosunda, sol taraftaki **izleme** altında **Öngörüler**' i seçin. 

3. **Dağıtımlar (Önizleme)** sekmesini seçin.

    ![Azure portal dağıtımlar görünümü](./media/container-insights-livedata-deployments/deployment-view.png)

Görünüm, çalışan tüm dağıtımların bir listesini, ad alanıyla birlikte ve diğer ayrıntılı bilgilerle birlikte, komut `kubectl get deployments –all-namespaces`yürütmeye öykünürken gösterir. Sütunlardan birini seçerek sonuçları sıralayabilirsiniz. 

![Dağıtımlar Özellikler bölmesi ayrıntıları](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Listeden bir dağıtım seçtiğinizde, sayfanın sağ tarafında otomatik olarak bir özellik bölmesi görüntülenir. Komutu `kubectl describe deployment {deploymentName}`çalıştırdığınız seçili dağıtımla ilgili bilgileri gösterir. Açıklama bilgilerinde bazı ayrıntılar eksik olduğunu fark etmiş olabilirsiniz. Çoğu özellikle **şablon** eksik. **Ham** sekmesinin seçilmesi, ayrıştırmayana ayrıntıları ayrıntılarına gitmenizi sağlar.  

![Dağıtımlar Özellikler bölmesi ham ayrıntıları](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Dağıtım ayrıntılarını gözden geçirdikten sonra, kapsayıcı günlüklerini ve olayları gerçek zamanlı olarak görebilirsiniz. Canlı günlük verilerini sürekli bir akışta görüntüleyebileceğiniz dağıtım verileri kılavuzunun altında **canlı konsol göster** ve Canlı veriler (Önizleme) konsolu ' nu seçin. Getirme durumu göstergesi, bölmenin en sağında yer alan yeşil bir onay işareti gösteriyorsa, verilerin alınabilmesi ve konsolunuza akışa başlaması anlamına gelir.

Ayrıca, ad alanı veya küme düzeyindeki olaylara göre filtreleyebilirsiniz. Konsolunda verileri gerçek zamanlı görüntüleme hakkında daha fazla bilgi edinmek için bkz. [kapsayıcılar Için Azure izleyici Ile canlı verileri görüntüleme (Önizleme)](container-insights-livedata-overview.md). 

![Dağıtımlar konsolundaki canlı verileri görüntüleme](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Sonraki adımlar

- Azure Izleyici 'yi kullanmayı ve AKS kümenizin diğer yönlerini izlemeyi öğrenmeye devam etmek için bkz. [Azure Kubernetes hizmet durumunu görüntüleme](container-insights-analyze.md).

- Önceden tanımlanmış sorguları ve uyarı oluşturma, görselleştirmeler oluşturmak veya kümelerinizde daha fazla analiz yapmak için örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin.