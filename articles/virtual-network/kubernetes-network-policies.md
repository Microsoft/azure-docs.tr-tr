---
title: Azure Kubernetes ağ ilkeleri | Microsoft Docs
description: Kubernetes kümenizi güvenli hale getirmek için Kubernetes ağ ilkeleri hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: a68e1a3f60930e290e97084ff2ec9350b18e2873
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594964"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes ağ Ilkelerine genel bakış

Ağ Ilkeleri, sanal makineler için ağ güvenlik grupları (NSG 'Ler) gibi mikro kesimleme olanağı sağlar. Azure ağ Ilkesi Yöneticisi (Azure NPM olarak da bilinir) uygulama standart Kubernetes ağ Ilkesi belirtimini destekler. Etiketler grubunu seçmek ve bu yığından gelen ve giden trafiği filtrelemek için giriş ve çıkış kurallarının bir listesini tanımlamak için Etiketler kullanabilirsiniz. Kubernetes [belgelerindeki](https://kubernetes.io/docs/concepts/services-networking/network-policies/)Kubernetes ağ ilkeleri hakkında daha fazla bilgi edinin.

![Kubernetes ağ ilkelerine genel bakış](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure NPM uygulamasının kapsayıcılar için VNet tümleştirmesi sağlayan Azure CNı ile birlikte çalışması. NPM yalnızca Linux 'ta desteklenir. Uygulama, tanımlanan ilkelere bağlı olarak Linux Iptablolarında izin ver ve Reddet IP kurallarını yapılandırarak trafik filtrelemeyi zorlar. Bu kurallar, Linux Ipsetleri kullanılarak birlikte gruplandırılır.

## <a name="planning-security-for-your-kubernetes-cluster"></a>Kubernetes kümeniz için güvenliği planlama
Kümeniz için güvenlik uygularken, ağ güvenlik grupları (NSG 'ler) kullanarak küme alt ağınızı girip bırakarak (Kuzey-Güney trafiği) trafiği filtreleyin. Kümenizdeki FID 'ler arasındaki trafik için Azure NPM kullanın (Doğu-Batı trafiği).

## <a name="using-azure-npm"></a>Azure NPM 'yi kullanma
Azure NPM, pods için mikro segmentasyon sağlamak üzere aşağıdaki yollarla kullanılabilir.

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
NPM, AKS 'de yerel olarak kullanılabilir ve küme oluşturma sırasında etkinleştirilebilir. [Azure Kubernetes Service (aks) içindeki ağ ilkelerini kullanarak Pod arasında güvenli trafik](../aks/use-network-policies.md)hakkında daha fazla bilgi edinin.

### <a name="aks-engine"></a>AKS-motor
AKS-Engine, Azure 'da bir Kubernetes kümesinin dağıtımı için Azure Resource Manager şablonu oluşturan bir araçtır. Küme yapılandırması, şablon oluşturma sırasında araca geçirilen bir JSON dosyasında belirtilir. Desteklenen küme ayarlarının tam listesi ve açıklamaları hakkında daha fazla bilgi edinmek için bkz. Microsoft Azure Container Service Engine - Küme Tanımı.

ACS-Engine kullanılarak dağıtılan kümelerdeki ilkeleri etkinleştirmek için, küme tanımı dosyasındaki networkPolicy ayarının değerini "Azure" olarak belirtin.

#### <a name="example-configuration"></a>Örnek yapılandırma

Aşağıdaki JSON örnek yapılandırması yeni bir sanal ağ ve alt ağ oluşturur ve Azure CNı ile buna bir Kubernetes kümesi dağıtır. JSON dosyasını düzenlemek için "Notepad" kullanmanızı öneririz. 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="do-it-yourself-diy-kubernetes-clusters-in-azure"></a>Azure 'da Kubernetes kümelerini kendiniz yapın
 DIY kümeleri için, önce CNı eklentisini yükleyip bir kümedeki her sanal makinede etkinleştirin. Ayrıntılı yönergeler için bkz. [Eklentiyi kendi dağıttığınız Kubernetes kümesi için dağıtma](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster).

Küme dağıtıldıktan sonra, `kubectl` kümeye ayarlanmış Azure NPM *arka plan programını* indirmek ve uygulamak için aşağıdaki komutu çalıştırın.

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
Çözüm Ayrıca açık kaynak olur ve kod [Azure Container Networking deposunda](https://github.com/Azure/azure-container-networking/tree/master/npm)kullanılabilir.

## <a name="monitor-and-visualize-network-configurations-with-azure-npm"></a>Azure NPM ile ağ yapılandırmasını izleyin ve görselleştirin
Azure NPM, konfigürasyonlarınızı izlemenize ve daha iyi anlamanıza imkan tanıyan bilgilendirici Prometheus ölçümlerini içerir. Azure portal veya Grafana laboratuvarlarında yerleşik görselleştirmeler sağlar. Azure Izleyici veya Prometheus sunucusunu kullanarak bu ölçümleri toplamaya başlayabilirsiniz.

### <a name="benefits-of-azure-npm-metrics"></a>Azure NPM ölçümlerinin avantajları
Kullanıcılar daha önce `iptables -L` , bir küme düğümünde çalıştırılan ve çıktıyı anlamak için ayrıntılı ve zor olan komut Ile ağ yapılandırmaları hakkında bilgi edindi. NPM ölçümleri, ağ Ilkeleri, Iptables kuralları ve ıpsets ile ilgili aşağıdaki avantajları sağlar.
- Bir yapılandırmada hata ayıklama için üç ve zaman boyutu arasındaki ilişki hakkında öngörüler sağlar.
- Tüm ıpsets ve her IPSet giriş sayısı.
- IPTable/IPSet Level parçalı yapısı ile ilke uygulamak için geçen süre.
 
### <a name="supported-metrics"></a>Desteklenen ölçümler
Desteklenen ölçümlerin listesi aşağıda verilmiştir:

|Ölçüm Adı |Description  |Prometheus ölçüm türü  |Etiketler  |
|---------|---------|---------|---------|
|`npm_num_policies`     |Ağ ilkelerinin sayısı          |Ölçer         |-         |
|`npm_num_iptables_rules`     | IPTables kuralları sayısı     | Ölçer        |-         |         
|`npm_num_ipsets`     |Ipset sayısı         |Ölçer            |-         |
|`npm_num_ipset_entries`     |Tüm ıpsets 'lerde IP adresi girişi sayısı         |Ölçer         |-         |
|`npm_add_policy_exec_time`     |ağ ilkesi ekleme çalışma zamanı         |Özet         |quantile (0,5, 0,9 veya 0,99)         |
|`npm_add_iptables_rule_exec_time`     |Iptables kuralı ekleme çalışma zamanı         |Özet         |quantile (0,5, 0,9 veya 0,99)         |
|`npm_add_ipset_exec_time`     |IPSet eklemek için çalışma zamanı         |Özet         |quantile (0,5, 0,9 veya 0,99)         |
|`npm_ipset_counts` ileri     |her bireysel IPSet içindeki giriş sayısı         |GaugeVec         |ad & karmasını ayarla         |

"Exec_time" ölçümlerinde farklı quantile düzeyleri, genel ve en kötü durum senaryolarını ayırt etmenize yardımcı olur.

Ayrıca, her "exec_time" özet ölçümü için bir "exec_time_count" ve "exec_time_sum" ölçümü vardır.

Ölçümler, kapsayıcılar için Azure Izleyici veya Prometheus aracılığıyla yapılandırılabilir.

### <a name="setup-for-azure-monitor"></a>Azure Izleyici için kurulum
İlk adım, Kubernetes kümeniz için Azure Izleyicisini etkinleştirmek içindir. Adımlar [Için Azure Izleyici 'ye genel bakış](../azure-monitor/containers/container-insights-overview.md)bölümünde yer bulabilirsiniz. Kapsayıcılar için Azure Izleyici 'yi etkinleştirdikten sonra, NPM tümleştirmesini ve Prometheus NPM ölçümlerini toplamayı etkinleştirmek için [kapsayıcılar ConfigMap Için Azure izleyicisini](https://aka.ms/container-azm-ms-agentconfig) yapılandırın. Kapsayıcılar için Azure izleyici ConfigMap, ```integrations``` NPM ölçümlerini toplama ayarlarına sahip bir bölüme sahiptir. Bu ayarlar ConfigMap içinde varsayılan olarak devre dışıdır. Temel ayarı etkinleştirmek ```collect_basic_metrics = true``` , temel NPM ölçümlerini toplar. Gelişmiş ayarı etkinleştirmek ```collect_advanced_metrics = true``` , temel ölçümlere ek olarak gelişmiş ölçümler toplar. 

ConfigMap 'i düzenledikten sonra yerel olarak kaydedin ve ConfigMap 'i kümenize aşağıdaki şekilde uygulayın.

```kubectl apply -f container-azm-ms-agentconfig.yaml``` Aşağıda, gelişmiş ölçüm koleksiyonuyla etkinleştirilmiş NPM tümleştirmesinin gösterildiği, [kapsayıcılar Için Azure Izleyici ConfigMap](https://aka.ms/container-azm-ms-agentconfig)' ten oluşan bir kod parçacığı verilmiştir.
```
integrations: |-
    [integrations.azure_network_policy_manager]
        collect_basic_metrics = false
        collect_advanced_metrics = true
```
Gelişmiş ölçümler isteğe bağlıdır ve açık olması temel ölçüm toplamayı otomatik olarak etkinleştirebilir. Şu anda yalnızca dahil olan gelişmiş ölçümler `npm_ipset_counts`

[Yapılandırma eşlemesindeki kapsayıcılar Için Azure izleyici koleksiyon ayarları](../azure-monitor/containers/container-insights-agent-config.md) hakkında daha fazla bilgi edinin

### <a name="visualization-options-for-azure-monitor"></a>Azure Izleyici için görselleştirme seçenekleri
NPM ölçüm koleksiyonu etkinleştirildikten sonra, Azure portal ölçümleri kapsayıcı öngörülerini kullanarak veya Grafana içinde görüntüleyebilirsiniz.

#### <a name="viewing-in-azure-portal-under-insights-for-the-cluster"></a>Küme için Öngörüler altında Azure portal görüntüleme
Azure portalını açın. Kümenizin öngörülerine bir kez "çalışma kitapları" na gidin ve "ağ Ilkesi Yöneticisi (NPM) yapılandırması" ' nı açın.

Çalışma kitabını (aşağıdaki resimler) görüntülemenin yanı sıra, Öngörüler bölümünde "Günlükler" içindeki Prometheus ölçümlerini de doğrudan sorgulayabilirsiniz. Örneğin, bu sorgu toplanmakta olan tüm ölçümleri döndürür.
| Zaman üretilen > önce (5h) | Burada ad "npm_" içerir

Ayrıca ölçümler için Log Analytics doğrudan sorgulama yapabilirsiniz. [Log Analytics sorguları Ile çalışmaya](../azure-monitor/containers/container-insights-log-search.md) başlama hakkında daha fazla bilgi edinin 

#### <a name="viewing-in-grafana-dashboard"></a>Grafana panosunda görüntüleme
Grafana sunucunuzu kurun ve [burada](https://grafana.com/grafana/plugins/grafana-azure-monitor-datasource)açıklandığı gibi bir Log Analytics veri kaynağı yapılandırın. Daha sonra, [Grafana panosunu Grafana Laboratuvarlarınızı Log Analytics arka ucu ile](https://grafana.com/grafana/dashboards/10956) içeri aktarın.

Panoda Azure çalışma kitabına benzer görseller vardır. Insightsölçümlerini tablosundan NPM ölçümlerini görselleştirmek & grafiğe paneller ekleyebilirsiniz.

### <a name="setup-for-prometheus-server"></a>Prometheus sunucusu kurulumu
Bazı kullanıcılar, kapsayıcılar için Azure Izleyici yerine bir Prometheus sunucusuyla ölçümleri toplamayı seçebilirler. Yalnızca, NPM ölçümlerini toplamak için, yalnızca bir pıspe yapılandırmasına iki iş eklemeniz gerekir.

Basit bir Prometheus sunucusu yüklemek için kümenize bu Held deposunu ekleyin
```
helm repo add stable https://kubernetes-charts.storage.googleapis.com
helm repo update
```
sonra bir sunucu ekleyin
```
helm install prometheus stable/prometheus -n monitoring \
--set pushgateway.enabled=false,alertmanager.enabled=false, \
--set-file extraScrapeConfigs=prometheus-server-scrape-config.yaml
```
`prometheus-server-scrape-config.yaml`aşağıdakilerden oluşur
```
- job_name: "azure-npm-node-metrics"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: node
  relabel_configs:
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
- job_name: "azure-npm-cluster-metrics"
  metrics_path: /cluster-metrics
  kubernetes_sd_configs:
  - role: service
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_service_name]
    regex: npm-metrics-cluster-service
    action: keep
# Comment from here to the end to collect advanced metrics: number of entries for each IPSet
  metric_relabel_configs:
  - source_labels: [__name__]
    regex: npm_ipset_counts
    action: drop
```


Ayrıca `azure-npm-node-metrics` işi aşağıdaki içerikle değiştirebilir veya Kubernetes pods için önceden var olan bir işe ekleyebilirsiniz:
```
- job_name: "azure-npm-node-metrics-from-pod-config"
  metrics_path: /node-metrics
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_namespace]
    regex: kube-system
    action: keep
  - source_labels: [__meta_kubernetes_pod_annotationpresent_azure_npm_scrapeable]
    action: keep
  - source_labels: [__address__]
    action: replace
    regex: ([^:]+)(?::\d+)?
    replacement: "$1:10091"
    target_label: __address__
```

### <a name="visualization-options-for-prometheus"></a>Prometheus için görselleştirme seçenekleri
Yalnızca bir Prometheus sunucusu kullanılırken Grafana panosu desteklenir. 

Henüz yapmadıysanız, Grafana sunucunuzu ayarlayıp bir Prometheus veri kaynağı yapılandırın. Daha sonra, [Grafana panonuzu bir Prometheus arka ucu Ile](https://grafana.com/grafana/dashboards/13000) Grafana laboratuvarlarınızın içine aktarın.

Bu panonun görselleri, bir kapsayıcı öngörüleri/Log Analytics arka ucu ile panoyla aynıdır.

### <a name="sample-dashboards"></a>Örnek Panolar
Aşağıda, Container Insights (CI) ve Grafana NPM ölçümleri için bazı örnek panolar verilmiştir

#### <a name="ci-summary-counts"></a>CI Özet sayıları
![Azure çalışma kitabı Özet sayıları](media/kubernetes-network-policies/workbook-summary-counts.png)

#### <a name="ci-counts-over-time"></a>Zaman içinde CI sayısı
[![Zaman içinde Azure çalışma kitabı sayıları](media/kubernetes-network-policies/workbook-counts-over-time.png)](media/kubernetes-network-policies/workbook-counts-over-time.png#lightbox)

#### <a name="ci-ipset-entries"></a>CI IPSet girdileri
[![Azure çalışma kitabı IPSet girdileri](media/kubernetes-network-policies/workbook-ipset-entries.png)](media/kubernetes-network-policies/workbook-ipset-entries.png#lightbox)

#### <a name="ci-runtime-quantiles"></a>CI çalışma zamanı quantiles
![Azure çalışma kitabı çalışma zamanı quantiles](media/kubernetes-network-policies/workbook-runtime-quantiles.png)

#### <a name="grafana-dashboard-summary-counts"></a>Grafana panosu Özet sayıları
![Grafana panosu Özet sayıları](media/kubernetes-network-policies/grafana-summary-counts.png)

#### <a name="grafana-dashboard-counts-over-time"></a>Zaman içinde Grafana Pano sayısı
[![Zaman içinde Grafana Pano sayısı](media/kubernetes-network-policies/grafana-counts-over-time.png)](media/kubernetes-network-policies/grafana-counts-over-time.png#lightbox)

#### <a name="grafana-dashboard-ipset-entries"></a>Grafana Dashboard IPSet girdileri
[![Grafana Dashboard IPSet girdileri](media/kubernetes-network-policies/grafana-ipset-entries.png)](media/kubernetes-network-policies/grafana-ipset-entries.png#lightbox)

#### <a name="grafana-dashboard-runtime-quantiles"></a>Grafana panosu çalışma zamanı quantiles
[![Grafana panosu çalışma zamanı quantiles](media/kubernetes-network-policies/grafana-runtime-quantiles.png)](media/kubernetes-network-policies/grafana-runtime-quantiles.png#lightbox)



## <a name="next-steps"></a>Sonraki adımlar
- [Azure Kubernetes hizmeti](../aks/intro-kubernetes.md)hakkında bilgi edinin.
-  [Kapsayıcı ağı](container-networking-overview.md)hakkında bilgi edinin.
- Kubernetes kümeleri veya Docker kapsayıcıları için [eklentiyi dağıtın](deploy-container-networking.md) .

