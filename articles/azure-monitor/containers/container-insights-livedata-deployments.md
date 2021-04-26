---
title: Container Insights dağıtımlarını görüntüleme (Önizleme) | Microsoft Docs
description: Bu makalede, kapsayıcı öngörülerine kubectl kullanmadan Kubernetes dağıtımlarının gerçek zamanlı görünümü açıklanmaktadır.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: c6eaac209234f7352395502c6761312cf258108f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713924"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Dağıtımı (Önizleme) gerçek zamanlı görüntüleme

Kapsayıcı öngörüleri ile, görünüm dağıtımları (Önizleme) özelliği, ve komutlarını ortaya çıkaran gerçek zamanlı olarak Kubernetes dağıtım nesnelerine doğrudan erişim `kubeclt get deployments` sağlar `kubectl describe deployment {your deployment}` .

>[!NOTE]
>Bu özellikle [özel kümeler](https://azure.microsoft.com/updates/aks-private-cluster/) olarak etkinleştirilen aks kümeleri desteklenmez. Bu özellik tarayıcınızdan bir ara sunucuyla doğrudan Kubernetes API'sine erişime bağlıdır. Ağ güvenliğini etkinleştirip bu ara sunucudan Kubernetes API'sinin engellenmesi bu trafiği engeller.

Daha fazla bilgi edinmek için [dağıtımlar](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)hakkındaki Kubernetes belgelerini gözden geçirin.

## <a name="how-it-works"></a>Nasıl çalışır?

Canlı veriler (Önizleme) özelliği, Kubernetes API 'sine doğrudan erişir ve kimlik doğrulama modeliyle ilgili ek bilgilere [buradan](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)ulaşabilirsiniz.

Dağıtımlar (Önizleme) özelliği, dağıtımlar uç noktasında bir kerelik (yenilenebilir) bir yük gerçekleştirir `/apis/apps/v1/deployments` . Bu, belirli bir dağıtımı seçmenizi ve dağıtım uç noktasına karşı ilgili dağıtımın ayrıntılarını yapılandırmanızı sağlar `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` .

Sayfanın sol üst kısmında **Yenile** seçeneği belirlendiğinde dağıtım listesi yenilenir. Bu, komutu yeniden çalıştırmaya benzetir `kubectl` .

>[!IMPORTANT]
>Bu özelliğin çalışması sırasında hiçbir veri kalıcı olarak depolanmaz. Tarayıcınızı kapattığınızda veya uygulamadan çıktığınızda, oturum sırasında yakalanan tüm bilgiler silinir.

>[!NOTE]
>Konsoldan bir Azure panosuna canlı veri (Önizleme) verileri sabitleyemezsiniz.

## <a name="deployments-describe"></a>Dağıtımlar şunları anlatmaktadır

İle eşdeğer olan bir dağıtımın ayrıntılarını görüntülemek için `kubectl describe deployment` aşağıdaki adımları gerçekleştirin.

1. Azure portal, AKS kümesi kaynak grubuna gidin ve AKS kaynağınızı seçin.

2. AKS kümesi panosunda, sol taraftaki **izleme** altında **Öngörüler**' i seçin.

3. **Dağıtımlar (Önizleme)** sekmesini seçin.

    ![Azure portal dağıtımlar görünümü](./media/container-insights-livedata-deployments/deployment-view.png)

Görünüm, çalışan tüm dağıtımların bir listesini, ad alanıyla birlikte ve diğer ayrıntılı bilgileri gösterir `kubectl get deployments –all-namespaces` . Sütunlardan birini seçerek sonuçları sıralayabilirsiniz.

![Dağıtımlar Özellikler bölmesi ayrıntıları](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Listeden bir dağıtım seçtiğinizde, sayfanın sağ tarafında otomatik olarak bir özellik bölmesi görüntülenir. Komutu çalıştırdığınız seçili dağıtımla ilgili bilgileri gösterir `kubectl describe deployment {deploymentName}` . Açıklama bilgilerinde bazı ayrıntılar eksik olduğunu fark etmiş olabilirsiniz. Çoğu özellikle **şablon** eksik. **Ham** sekmesinin seçilmesi, ayrıştırmayana ayrıntıları ayrıntılarına gitmenizi sağlar.

![Dağıtımlar Özellikler bölmesi ham ayrıntıları](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Dağıtım ayrıntılarını gözden geçirdikten sonra, kapsayıcı günlüklerini ve olayları gerçek zamanlı olarak görebilirsiniz. Canlı günlük verilerini sürekli bir akışta görüntüleyebileceğiniz dağıtım verileri kılavuzunun altında **canlı konsol göster** ve Canlı veriler (Önizleme) konsolu ' nu seçin. Getirme durumu göstergesi, bölmenin en sağında yer alan yeşil bir onay işareti gösteriyorsa, verilerin alınabilmesi ve konsolunuza akışa başlaması anlamına gelir.

Ayrıca, ad alanı veya küme düzeyindeki olaylara göre filtreleyebilirsiniz. Konsolunda verileri gerçek zamanlı görüntüleme hakkında daha fazla bilgi edinmek için bkz. [Container Insights Ile canlı verileri görüntüleme (Önizleme)](container-insights-livedata-overview.md).

![Dağıtımlar konsolundaki canlı verileri görüntüleme](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>Sonraki adımlar

- Azure Izleyici 'yi kullanmayı ve AKS kümenizin diğer yönlerini izlemeyi öğrenmeye devam etmek için bkz. [Azure Kubernetes hizmet durumunu görüntüleme](container-insights-analyze.md).

- Önceden tanımlanmış sorguları ve uyarı oluşturma, görselleştirmeler oluşturmak veya kümelerinizde daha fazla analiz yapmak için örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin.
