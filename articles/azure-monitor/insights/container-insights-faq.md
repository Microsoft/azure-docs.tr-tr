---
title: Kapsayıcılar için Azure Izleyici sık sorulan sorular | Microsoft Docs
description: Kapsayıcılar için Azure Izleyici, AKS kümelerinizin ve Azure 'daki Container Instances sistem durumunu izleyen bir çözümdür. Bu makalede yaygın soruların yanıtları vardır.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: d3779a2d48db82bfccdc0f047119a36ef56c3bdf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477411"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Kapsayıcılar için Azure Izleyici sık sorulan sorular

Bu Microsoft SSS, kapsayıcılar için Azure Izleyici hakkında sık sorulan sorulardan oluşan bir listesidir. Çözümle ilgili başka sorularınız varsa, [tartışma forumuna](https://feedback.azure.com/forums/34192--general-feedback) gidin ve sorularınızı gönderin. Bir soru sıkça sorulduğunda, hızlı ve kolay bir şekilde bulunabilmesi için bu makaleye ekleyeceğiz.

## <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana ' de toplanan ölçümleri görüntüleyebilir miyim?

Kapsayıcılar için Azure Izleyici, Grafana panolar içinde Log Analytics çalışma alanınızda depolanan ölçümlerin görüntülenmesini destekler. Grafana 'in [Pano deposundan](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) indirebileceğiniz bir şablon sağladık. bu şekilde, izlenen kümelerinizdeki ek verileri nasıl sorgulacağınızı ve özel Grafana kesik baords 'de görselleştirmeyi öğreneceksiniz. 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>AKS-Engine kümenizi kapsayıcılar için Azure Izleyici ile izleyebilir miyim?

Kapsayıcılar için Azure Izleyici, Azure 'da barındırılan AKS-Engine (eski adıyla ACS-Engine) kümeleri için dağıtılan kapsayıcı iş yüklerini izlemeyi destekler. Bu senaryoya yönelik izlemeyi etkinleştirmek için gereken adımlara ilişkin daha fazla bilgi ve bir genel bakış için bkz. [AKS-Engine için kapsayıcılar Için Azure Izleyicisini kullanma](https://github.com/microsoft/OMS-docker/tree/aks-engine).

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Neden Log Analytics çalışma alanım 'da veri görmüyorum?

Log Analytics çalışma alanında belirli bir zamanda herhangi bir veri göremiyorsanız, günlük toplanacak veri miktarını denetlemek için varsayılan 500 MB sınırına veya belirtilen günlük ucuna ulaşmış olabilirsiniz. Gün için sınır karşılandığında, veri toplama işlemi yalnızca bir sonraki günde duraklar ve sürdürülür. Veri kullanımınızı gözden geçirmek ve tahmin edilen kullanım desenlerinize göre farklı bir fiyatlandırma katmanına güncelleştirmek için bkz. [günlük verisi kullanımı ve maliyeti](../platform/manage-cost-storage.md). 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Containerınventory tablosunda belirtilen kapsayıcı durumları nelerdir?

Containerınventory tablosu, hem durdurulan hem de çalışan kapsayıcılar hakkında bilgiler içerir. Tablo, tüm kapsayıcılar için Docker 'ı sorgulayan (çalışıyor ve durdurulmuş) ve bu verileri Log Analytics çalışma alanına ileten aracının içindeki bir iş akışı tarafından doldurulur.
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Nasıl yaparım? *eksik abonelik kayıt* hatası.

**Microsoft. operationsmanagement Için eksik abonelik kaydı**hatası alırsanız, çalışma alanının tanımlandığı abonelikte **Microsoft. operationsmanagement** kaynak sağlayıcısını kaydederek bu sorunu çözebilirsiniz. Bunun nasıl yapılacağını gösteren belgeler [burada](../../azure-resource-manager/resource-manager-register-provider-errors.md)bulunabilir.

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>RBAC etkin AKS kümeleri için destek var mı?

Kapsayıcı Izleme çözümü RBAC 'yi desteklemez, ancak kapsayıcılar için Azure Izleyici ile desteklenir. Çözüm ayrıntıları sayfası, bu kümelerin verilerini gösteren dikey pencerelerde doğru bilgileri göstermez.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Nasıl yaparım?, KUIN-System ad alanındaki kapsayıcılar için günlük toplamayı Held aracılığıyla etkinleştirmek istiyor musunuz?

Kuto-System ad alanındaki kapsayıcılardan günlük koleksiyonu varsayılan olarak devre dışıdır. Omsagent üzerinde bir ortam değişkeni ayarlanarak günlük toplama etkinleştirilebilir. Daha fazla bilgi için bkz. [kapsayıcılar Için Azure izleyici](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub sayfası. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Omsagent 'ı yayınlanan en son sürüme güncelleştirmek Nasıl yaparım? mı?

Aracıyı yükseltmeyi öğrenmek için bkz. [Aracı yönetimi](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>Çok satırlı günlüğe kaydetme etkinleştirilsin mi Nasıl yaparım??

Şu anda kapsayıcılar için Azure Izleyici çok satırlı günlüğe kaydetmeyi desteklemez, ancak kullanılabilir geçici çözümler vardır. Tüm hizmetleri JSON biçiminde yazacak şekilde yapılandırabilir ve ardından Docker/Moby, bunları tek bir satır olarak yazar.

Örneğin, bir örnek Node. js uygulaması için aşağıdaki örnekte gösterildiği gibi, günlüğünüz bir JSON nesnesi olarak sardırabilirsiniz:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Bu veriler, sorgulama yaptığınızda Günlükler için Azure Izleyici 'de aşağıdaki örnekteki gibi görünür:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Soruna ilişkin ayrıntılı bir bakış için aşağıdaki [GitHub bağlantısını](https://github.com/moby/moby/issues/22920)inceleyin.

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Canlı günlükleri etkinleştirdiğimde Azure AD hatalarını Nasıl yaparım? çözün misiniz? 

Şu hatayı görebilirsiniz: **istekte belirtilen yanıt URL 'si, uygulama için yapılandırılan yanıt URL 'leriyle eşleşmiyor: ' < Uygulama kimliği\>'** . Çözümü çözecek çözüm, [kapsayıcılar Için Azure izleyici ile kapsayıcı verilerinin gerçek zamanlı olarak nasıl görüntüleneceği](container-insights-livedata-setup.md#configure-ad-integrated-authentication)makalesinde bulunabilir. 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Ekleme işleminden sonra kümeyi neden yükseltemiyorum?

Bir AKS kümesi için Azure Izleyicisini etkinleştirdikten sonra, kümenin verilerini gönderdiği Log Analytics çalışma alanını sildiğinizde, kümeyi yükseltmeye çalışırken başarısız olur. Bu sorunu geçici olarak çözmek için, izlemeyi devre dışı bırakmanız ve ardından aboneliğinizdeki farklı bir geçerli çalışma alanına başvuruda bulunan yeniden etkinleştirmeniz gerekir. Küme yükseltmesini yeniden gerçekleştirmeye çalıştığınızda, başarıyla işlemeli ve tamamlanmalıdır.  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Aracı için hangi bağlantı noktalarına ve etki alanlarına açık/beyaz listeye ihtiyacım var?

Azure, Azure ABD kamu ve Azure Çin bulutları ile Kapsayıcılı aracı için gereken ara sunucu ve güvenlik duvarı yapılandırma bilgileri için [ağ güvenlik duvarı gereksinimlerine](container-insights-onboard.md#network-firewall-requirements) bakın.

## <a name="next-steps"></a>Sonraki adımlar

AKS kümenizi izlemeye başlamak için, izlemeyi etkinleştirmek üzere gereksinimleri ve kullanılabilir yöntemleri anlamak üzere [kapsayıcılar Için Azure izleyicisini](container-insights-onboard.md) ekleme konusunu gözden geçirin. 
