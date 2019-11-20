---
title: Kapsayıcılar için Azure Izleyici ile Kubernetes küme durumunu izleme | Microsoft Docs
description: Bu makalede, kapsayıcılar için Azure Izleyici ile AKS ve AKS olmayan kümelerinizin sistem durumunu nasıl görüntüleyebileceğiniz ve çözümleyebileceğinizi anlatmaktadır.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/18/2019
ms.author: magoedte
ms.openlocfilehash: f748399b6b356e5f8655f59221e78acd0d98f51e
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173629"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Kapsayıcılar için Azure Izleyici ile Kubernetes küme durumunu anlayın

Kapsayıcılar için Azure Izleyici ile, yönetilen altyapı bileşenlerinin ve kapsayıcı için Azure Izleyici tarafından desteklenen herhangi bir Kubernetes kümesinde çalışan tüm düğümlerin sistem durumunu izler ve raporlar. Bu deneyim, [birden çok küme görünümünde](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)hesaplanan ve bildirilen küme durumu durumunun ötesine uzanır, burada kümede bir veya daha fazla düğüm kısıtlanmış olup olmadığını anlayabilir veya bir düğüm ya da Pod, seçilen ölçümleri temel alarak kümedeki çalışan bir uygulamayı etkileyebilecek bir düğüm veya Pod kullanılamaz. 

Kapsayıcılar için Azure Izleyicisini etkinleştirme hakkında daha fazla bilgi için bkz. [kapsayıcılar Için Azure izleyicisini](container-insights-onboard.md)ekleme.

>[!NOTE]
>AKS motoru kümelerini desteklemek için, aşağıdakileri karşıladığını doğrulayın:
>- Bu, [Held istemcisinin](https://helm.sh/docs/using_helm/)en son sürümünü kullanıyor.
>- Kapsayıcılı aracı sürümü *Microsoft/OMS: ciprod11012019*. Aracıyı yükseltmek için bkz. [Kubernetes kümesinde aracıyı yükseltme](container-insights-manage-agent.md#upgrading-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Genel Bakış

Kapsayıcılar için Azure Izleyici 'de sistem durumu özelliği, sorunları belirlemenize ve tanılamanıza yardımcı olması için Kubernetes kümenizin öngörülü durum izlemesini sağlar. Algılanan önemli sorunları görüntülemenize olanak sağlar. Kümenizdeki kapsayıcı aracısında kümenizin sistem durumunu değerlendiren izleyiciler ve sistem durumu verileri Log Analytics çalışma alanınızdaki **Kubehealth** tablosuna yazılır. 

Kubernetes küme durumu, aşağıdaki Kubernetes nesneleri ve soyutlamalar tarafından düzenlenen bir dizi izleme senaryosunu temel alır:

- Kubernetes altyapısı-CPU ve bellek kullanımı ve bir pods kullanılabilirliği değerlendirilirken, kümenizde dağıtılan düğümlerde çalışan Kubernetes API sunucusu, ReplicaSets ve DaemonSets için bir toplama sağlar

    ![Kubernetes altyapı durumu toplama görünümü](./media/container-insights-health/health-view-kube-infra-01.png)

- Düğümler-CPU ve bellek kullanımını değerlendirerek ve Kubernetes tarafından bildirilen bir düğümün durumunu değerlendirerek, her havuzdaki düğüm havuzlarının ve tek tek düğümlerin bir toplamını verir.

    ![Düğümler sistem durumu dökümü görünümü](./media/container-insights-health/health-view-nodes-01.png)

Şu anda yalnızca bir sanal kubelet 'in durumu destekleniyor. Bir sinyal alınmadığından, sanal kublet düğümlerinin CPU ve bellek kullanımının sistem durumu **bilinmiyor**olarak bildirilir.

Tüm izleyiciler sistem durumu hiyerarşisi bölmesindeki hiyerarşik düzende gösterilir. burada, Kubernetes nesnesini veya soyutlamayı (yani, Kubernetes altyapısı veya düğümleri) temsil eden bir toplam izleyici, tümünün Birleşik durumunu yansıtan en üstteki izleyicidir bağımlı alt izleyiciler. Sistem durumu türetmede kullanılan anahtar izleme senaryoları şunlardır:

* Düğüm ve kapsayıcıdan CPU kullanımını değerlendirin.
* Düğüm ve kapsayıcıdan bellek kullanımını değerlendirin.
* Kubernetes tarafından bildirilen hazır durumunun hesaplanmasına göre pods ve düğümlerin durumu.

Durumu göstermek için kullanılan simgeler aşağıdaki gibidir:

|Simge|Anlamı|  
|--------|-----------|  
|![Yeşil onay simgesi sağlıklı olduğunu belirtir](./media/container-insights-health/healthyicon.png)|Başarılı, sistem durumu Tamam (yeşil)|  
|![Sarı üçgen ve ünlem işareti uyarı](./media/container-insights-health/warningicon.png)|Uyarı (sarı)|  
|![Beyaz X bulunan kırmızı düğme kritik durumu gösterir](./media/container-insights-health/criticalicon.png)|Kritik (kırmızı)|  
|![Gri simgesi](./media/container-insights-health/grayicon.png)|Bilinmiyor (gri)|  

## <a name="monitor-configuration"></a>İzleyici yapılandırması

Kapsayıcılar için Azure Izleyici sistem durumu özelliğini destekleyen her monitörün davranışını ve yapılandırmasını anlamak için bkz. [sistem durumu İzleyicisi yapılandırma kılavuzu](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın. 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>AKS veya AKS olmayan kümenin durumunu görüntüleme

Kapsayıcılar için Azure Izleyici sistem durumu özelliğine erişim, Azure portal sol bölmeden **Öngörüler** ' i seçerek doğrudan bir aks kümesinden edinilebilir. Altında **Insights** bölümünden **kapsayıcıları**. 

Şirket içinde veya Azure Stack barındırılan bir AKS altyapısı kümesi olan AKS olmayan bir kümeden sistem durumunu görüntülemek için, Azure portal sol bölmeden **Azure izleyici** ' yi seçin. Altında **Insights** bölümünden **kapsayıcıları**.  Çoklu küme sayfasında, listeden AKS olmayan kümeyi seçin.

Kapsayıcılar için Azure Izleyici 'de, **küme** sayfasından **sistem durumu**' nu seçin.

![Küme durumu panosu örneği](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Küme durumunu gözden geçirme

Sistem durumu sayfası açıldığında, varsayılan olarak **Kubernetes altyapısı** , **sistem durumu en boy** kılavuzunda seçilidir.  Kılavuz, Kubernetes altyapısının ve küme düğümlerinin geçerli durum toplama durumunu özetler. Sistem durumu en boy oranını seçmek durum hiyerarşisi bölmesindeki (yani orta bölmedeki) sonuçları güncelleştirir ve tüm alt izleyicileri hiyerarşik bir düzende göstererek geçerli sistem durumunu görüntüler. Herhangi bir bağımlı izleyici hakkında daha fazla bilgi görüntülemek için bir seçim yapabilirsiniz ve sayfanın sağ tarafında otomatik olarak bir özellik bölmesi görüntülenir. 

![Küme durumu özellik bölmesi](./media/container-insights-health/health-view-property-pane.png)

Özellik bölmesinde, aşağıdakileri öğrenirsiniz:

- **Genel bakış** sekmesinde, izleyicinin seçili olan geçerli durumu, izleyicinin en son ne zaman hesaplandığı ve son durum değişikliği gerçekleştiği zaman görüntülenir. Ek bilgiler, hiyerarşide seçilen izleyici türüne bağlı olarak gösterilir.

    Sistem durumu hiyerarşisi bölmesinde bir toplam izleyici seçerseniz, Özellik bölmesinde **genel bakış** sekmesi altında, hiyerarşideki toplam alt izleyici sayısının ve kaç tane toplam izlemenin kritik, uyarı ve sağlıklı durumda olduğunu gösterir. 

    ![Toplu izleyici için sistem durumu Özellik bölmesine genel bakış sekmesi](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Sistem durumu hiyerarşisi bölmesinde bir birim İzleyicisi seçerseniz, son dört saat içinde kapsayıcı aracı tarafından hesaplanan ve bildirilen önceki örnekleri **değiştiren son durum** ' ın altında da gösterilir. Bu, kendi durumunu belirlemede birbirini izleyen birkaç değeri karşılaştırmak için, birim izleyicilerini temel alır. Örneğin, *Pod Ready durum* birimi izleyicisini seçtiyseniz, *ConsecutiveSamplesForStateTransition*parametresiyle denetlenen son iki örneği gösterir. Daha fazla bilgi için [birim izleyicilerinin](container-insights-health-monitors-config.md#unit-monitors)ayrıntılı açıklamasına bakın.
    
    ![Sağlık Özellik bölmesine genel bakış sekmesi](./media/container-insights-health/health-overview-unit-monitor.png)

    **Son durum değişikliğine** göre raporlanan süre bir gün veya daha eskiyse, izleyicinin durumunda değişiklik yapılmayacaktır. Ancak, bir birim İzleyicisi için alınan son örnek dört saatten daha eski ise, bu muhtemelen kapsayıcı aracının veri göndermediğini gösterir. Aracı belirli bir kaynağın var olduğunu (örneğin, bir düğüm) biliyorsa, ancak düğümün CPU veya bellek kullanımı izleyicilerinden (örnek olarak) veri almamışsa, izleyicinin sistem durumu **bilinmiyor**olarak ayarlanır.  

- **Yapılandırma sekmesinde,** varsayılan yapılandırma parametresi ayarlarını gösterir (yalnızca birim izleyicileri için, toplu izleyiciler için) ve bunların değerlerini gösterir.
- **Bilgi** sekmesinde, izleyicinin davranışını ve sağlıksız koşulu nasıl değerlendirdiği hakkında bilgi içerir.

Bu sayfadaki izleme verileri otomatik olarak yenilenmez ve kümeden alınan en son sistem durumunu görmek için sayfanın en üstündeki **Yenile** seçeneğini belirlemeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Önceden tanımlanmış sorguları ve kümelerinizi uyarmak, görselleştirmek veya analiz etmek üzere değerlendirilecek veya özelleştirecek örnekleri görmek için [günlük sorgusu örneklerini](container-insights-log-search.md#search-logs-to-analyze-data) görüntüleyin.