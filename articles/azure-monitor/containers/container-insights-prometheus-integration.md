---
title: Container Insights 'ı yapılandırma Prometheus tümleştirmesi | Microsoft Docs
description: Bu makalede, Kubernetes kümenizle birlikte Prometheus 'tan kapsayıcı içgörüleri aracısını nasıl yapılandırabileceğinizi açıklanmaktadır.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 149cdc8613d5034989c7660608a29309353cdabe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109650"
---
# <a name="configure-scraping-of-prometheus-metrics-with-container-insights"></a>Kapsayıcı öngörüleri ile Prometheus ölçümlerinin korumasını yapılandırın

[Prometheus](https://prometheus.io/) , popüler bir açık kaynaklı ölçüm izleme çözümüdür ve [bulut Yerel işlem altyapısı](https://www.cncf.io/)'nın bir parçasıdır. Kapsayıcı öngörüleri, Prometheus ölçümlerini toplamak için sorunsuz bir ekleme deneyimi sağlar. Genellikle, Prometheus 'yi kullanmak için bir depolama ile bir Prometheus sunucusu ayarlamanız ve yönetmeniz gerekir. Azure Izleyici ile tümleştirerek bir Prometheus sunucusu gerekli değildir. Tek yapmanız gereken, dışarı aktardığınız veya yığınlarınızın (uygulamanızın) yanı sıra kapsayıcı içgörüleri için kapsayıcılı bir aracı sizin yerinize hurdaya çıkarabilirsiniz. 

![Prometheus için kapsayıcı izleme mimarisi](./media/container-insights-prometheus-integration/monitoring-kubernetes-architecture.png)

>[!NOTE]
>Scraping Prometheus ölçümleri için desteklenen en düşük aracı sürümü ciprod07092019 veya üzeri ve tablodaki yapılandırma ve aracı hatalarını yazmak için desteklenen aracı sürümü `KubeMonAgentEvents` ciprod10112019. Azure Red Hat OpenShift ve Red Hat OpenShift v4, aracı sürümü ciprod04162020 veya üzeri için. 
>
>Aracı sürümleri ve her sürüme dahil olanlar hakkında daha fazla bilgi için bkz. [Aracı sürüm notları](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod). 
>Aracı sürümünüzü doğrulamak için **düğüm** sekmesinde bir düğüm seçin ve Özellikler bölmesinde **Aracı görüntüsü etiketi** özelliğinin değeri.

Prometheus ölçümlerinin scraping değeri, üzerinde barındırılan Kubernetes kümeleriyle desteklenir:

- Azure Kubernetes Service (AKS)
- Azure Stack veya şirket içi
- Azure Red Hat OpenShift sürüm 3. x
- Azure Red Hat OpenShift ve Red Hat OpenShift sürüm 4. x

### <a name="prometheus-scraping-settings"></a>Prometheus scraping ayarları

Prometheus ölçülerinin etkin bir şekilde kullanılması, iki perspektiften birinden gerçekleştirilir:

* Küme genelinde HTTP URL 'SI ve bir hizmetin listelenen bitiş noktalarından hedefleri bulur. Örneğin, kuas-DNS ve KUIN-eyalet-ölçümleri gibi k8s Hizmetleri ve bir uygulamaya özgü Pod ek açıklamaları. Bu bağlamda toplanan ölçümler ConfigMap bölümünde *[Prometheus data_collection_settings. Cluster]* tanımlanacaktır.
* Düğüm genelinde HTTP URL 'SI ve bir hizmetin listelenen bitiş noktalarından hedefleri bulur. Bu bağlamda toplanan ölçümler ConfigMap bölümünde *[Prometheus_data_collection_settings. Node]* tanımlanacaktır.

| Uç Nokta | Kapsam | Örnek |
|----------|-------|---------|
| Pod ek açıklaması | Küme genelinde | açıklamaları <br>`prometheus.io/scrape: "true"` <br>`prometheus.io/path: "/mymetrics"` <br>`prometheus.io/port: "8000"` <br>`prometheus.io/scheme: "http"` |
| Kubernetes hizmeti | Küme genelinde | `http://my-service-dns.my-namespace:9100/metrics` <br>`https://metrics-server.kube-system.svc.cluster.local/metrics` |
| URL/uç nokta | Düğüm başına ve/veya küme genelinde | `http://myurl:9101/metrics` |

Bir URL belirtildiğinde, kapsayıcı öngörüleri yalnızca uç noktayı Scrapes. Kubernetes hizmeti belirtildiğinde, IP adresini almak için hizmet adı küme DNS sunucusu ile çözümlenir ve sonra çözümlenen hizmet, daha sonra çözülür.

|Kapsam | Anahtar | Veri türü | Değer | Açıklama |
|------|-----|-----------|-------|-------------|
| Küme genelinde | | | | Ölçümler için atık uç noktalarına aşağıdaki üç yöntemden birini belirtin. |
| | `urls` | Dize | Virgülle ayrılmış dizi | HTTP uç noktası (IP adresi veya geçerli URL yolu belirtildi). Örneğin: `urls=[$NODE_IP/metrics]`. ($NODE _IP, belirli bir kapsayıcı öngörüleri parametresidir ve düğüm IP adresi yerine kullanılabilir. Tümü büyük harf olmalıdır.) |
| | `kubernetes_services` | Dize | Virgülle ayrılmış dizi | Kuin-State-ölçümlerini kullanarak bir Kubernetes hizmeti dizisi. Tam etki alanı adlarının burada kullanılması gerekir. Örneğin `kubernetes_services = ["https://metrics-server.kube-system.svc.cluster.local/metrics",http://my-service-dns.my-namespace.svc.cluster.local:9100/metrics]`.|
| | `monitor_kubernetes_pods` | Boole | true veya false | `true`, Küme genelinde ayarlar halinde olarak ayarlandığında, kapsayıcı öngörüleri Aracısı aşağıdaki Prometheus ek açıklamaları için tüm küme genelinde atık olarak çalışır:<br> `prometheus.io/scrape:`<br> `prometheus.io/scheme:`<br> `prometheus.io/path:`<br> `prometheus.io/port:` |
| | `prometheus.io/scrape` | Boole | true veya false | Pod 'un scraping öğesini sunar. `monitor_kubernetes_pods` olarak ayarlanmalıdır `true` . |
| | `prometheus.io/scheme` | Dize | http veya https | Varsayılan olarak HTTP üzerinden atık yapılır. Gerekirse, olarak ayarlayın `https` . | 
| | `prometheus.io/path` | Dize | Virgülle ayrılmış dizi | Ölçümlerinin alınacağı HTTP kaynak yolu. Ölçüm yolu yoksa `/metrics` , bu ek açıklama ile tanımlayın. |
| | `prometheus.io/port` | Dize | 9102 | Iskartaya çıkış için bir bağlantı noktası belirtin. Bağlantı noktası ayarlanmamışsa, varsayılan olarak 9102 olur. |
| | `monitor_kubernetes_pods_namespaces` | Dize | Virgülle ayrılmış dizi | Kubernetes pods 'den ıskartaya at ölçümleri için ad alanları listesi.<br> Örneğin, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]` |
| Düğüm genelinde | `urls` | Dize | Virgülle ayrılmış dizi | HTTP uç noktası (IP adresi veya geçerli URL yolu belirtildi). Örneğin: `urls=[$NODE_IP/metrics]`. ($NODE _IP, belirli bir kapsayıcı öngörüleri parametresidir ve düğüm IP adresi yerine kullanılabilir. Tümü büyük harf olmalıdır.) |
| Düğüm genelinde veya küme genelinde | `interval` | Dize | 60s | Koleksiyon aralığı varsayılan değeri bir dakikadır (60 saniye). Koleksiyonu, *[prometheus_data_collection_settings. Node]* ve/veya *[prometheus_data_collection_settings. Cluster]* için s, m, h gibi zaman birimlerine göre değiştirebilirsiniz. |
| Düğüm genelinde veya küme genelinde | `fieldpass`<br> `fielddrop`| Dize | Virgülle ayrılmış dizi | İzin ver ( `fieldpass` ) ve Allow () listesini ayarlayarak uç noktada toplanacak veya değil, belirli ölçümleri belirtebilirsiniz `fielddrop` . Önce izin verilenler listesini ayarlamanız gerekir. |

ConfigMaps genel bir liste ve aracıya yalnızca bir ConfigMap uygulanmış olabilir. Koleksiyonlar üzerine başka bir ConfigMaps olamaz.

## <a name="configure-and-deploy-configmaps"></a>ConfigMaps yapılandırma ve dağıtma

Aşağıdaki kümeler için ConfigMap yapılandırma dosyanızı yapılandırmak üzere aşağıdaki adımları uygulayın:

* Azure Kubernetes Service (AKS)
* Azure Stack veya şirket içi
* Azure Red Hat OpenShift sürüm 4. x ve Red Hat OpenShift sürüm 4. x

1. ConfigMap YAML dosyasını şablon olarak [indirin](https://aka.ms/container-azm-ms-agentconfig) ve kapsayıcı-AZM-MS-agentconfig. YAML olarak kaydedin.

   >[!NOTE]
   >ConfigMap şablonu kümede zaten mevcut olduğundan, bu adım Azure Red Hat OpenShift ile çalışırken gerekli değildir.

2. ConfigMap YAML dosyasını, özelleştirmelerinizle birlikte bulunan ve bunları hurdaya, Prometheus ölçümleriyle düzenleyin.

    >[!NOTE]
    >Azure Red Hat OpenShift için ConfigMap YAML dosyasını düzenliyorsanız, önce `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` dosyayı bir metin düzenleyicisinde açmak için komutunu çalıştırın.

    >[!NOTE]
    >`openshift.io/reconcile-protect: "true"`Mutabakatın önlenmesi için- *AZM-MS-agentconfig* configmap meta verilerinin altına aşağıdaki ek açıklamanın eklenmesi gerekir. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Kubernetes Services kümesini toplamak için aşağıdaki örneği kullanarak ConfigMap dosyasını yapılandırın.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Küme genelinde belirli bir URL 'den Prometheus ölçümlerinin bir listesini yapılandırmak için, aşağıdaki örneği kullanarak ConfigMap dosyasını yapılandırın.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Kümedeki her bir düğüm için bir aracının DaemonSet Prometheus ölçümlerinin scraping 'ini yapılandırmak için ConfigMap içinde aşağıdakileri yapılandırın:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP, belirli bir kapsayıcı öngörüleri parametresidir ve düğüm IP adresi yerine kullanılabilir. Tamamen büyük harf olmalıdır. 

    - Bir pod ek açıklaması belirterek Prometheus ölçümlerinin korumasını yapılandırmak için aşağıdaki adımları uygulayın:

       1. ConfigMap ' te şunları belirtin:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Pod ek açıklamaları için aşağıdaki yapılandırmayı belirtin:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Ek açıklamaları olan Pod 'ler için izlemeyi belirli ad alanlarına kısıtlamak istiyorsanız, örneğin yalnızca üretim iş yükleri için ayrılmış Pod dahil, öğesini `monitor_kubernetes_pod` `true` configmap içinde olarak ayarlayın ve ad alanlarını, atık olarak bulunan ad alanlarını belirten ad alanı filtresini ekleyin `monitor_kubernetes_pods_namespaces` . Örneğin, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

3. Şu kubectl komutunu çalıştırın: `kubectl apply -f <configmap_yaml_file.yaml>` .
    
    Örnek: `kubectl apply -f container-azm-ms-agentconfig.yaml`. 

Yapılandırma değişikliğinin, yürürlüğe girmeden önce tamamlanması birkaç dakika sürebilir ve kümedeki tüm omsagent 'lar yeniden başlatılır. Yeniden başlatma, tüm omsagent pods için aynı anda yeniden başlatma işlemi için bir yeniden başlatma işlemi yapılır. Yeniden başlatmalar tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonucu içerir: `configmap "container-azm-ms-agentconfig" created` .

## <a name="configure-and-deploy-configmaps---azure-red-hat-openshift-v3"></a>ConfigMaps yapılandırma ve dağıtma-Azure Red Hat OpenShift v3

Bu bölüm, Azure Red Hat OpenShift v3. x kümesine yönelik ConfigMap yapılandırma dosyanızı başarıyla yapılandırmaya yönelik gereksinimleri ve adımları içerir.

>[!NOTE]
>Azure Red Hat OpenShift v3. x için, *OpenShift-Azure-Logging* ad alanında bir şablon configmap dosyası oluşturulur. Aracıdan etkin bir şekilde hurdaya, ölçüm veya veri koleksiyonu için yapılandırılmamış.

### <a name="prerequisites"></a>Önkoşullar

Başlamadan önce Kapsayıcılı aracıyı ve Prometheus scraping ayarlarını yapılandırmak için Azure Red Hat Openshıft kümenizin müşteri kümesi Yöneticisi rolünün bir üyesi olduğunu doğrulayın. *OSA-müşteri-Yöneticiler* grubunun bir üyesi olduğunuzu doğrulamak için şu komutu çalıştırın:

``` bash
  oc get groups
```

Çıktı aşağıdakine benzeyecektir:

``` bash
NAME                  USERS
osa-customer-admins   <your-user-account>@<your-tenant-name>.onmicrosoft.com
```

*OSA-Customer-Admins* grubunun üyesiyseniz, `container-azm-ms-agentconfig` aşağıdaki komutu kullanarak configmap ' i listeleyemezsiniz:

``` bash
oc get configmaps container-azm-ms-agentconfig -n openshift-azure-logging
```

Çıktı aşağıdakine benzeyecektir:

``` bash
NAME                           DATA      AGE
container-azm-ms-agentconfig   4         56m
```

### <a name="enable-monitoring"></a>İzlemeyi etkinleştirme

Azure Red Hat Openshıft v3. x kümeniz için ConfigMap yapılandırma dosyanızı yapılandırmak üzere aşağıdaki adımları gerçekleştirin.

1. ConfigMap YAML dosyasını, özelleştirmelerinizle birlikte bulunan ve bunları hurdaya, Prometheus ölçümleriyle düzenleyin. ConfigMap şablonu Red Hat OpenShift v3 kümesinde zaten var. `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging`Dosyayı bir metin düzenleyicisinde açmak için komutunu çalıştırın.

    >[!NOTE]
    >`openshift.io/reconcile-protect: "true"`Mutabakatın önlenmesi için- *AZM-MS-agentconfig* configmap meta verilerinin altına aşağıdaki ek açıklamanın eklenmesi gerekir. 
    >```
    >metadata:
    >   annotations:
    >       openshift.io/reconcile-protect: "true"
    >```

    - Kubernetes Services kümesini toplamak için aşağıdaki örneği kullanarak ConfigMap dosyasını yapılandırın.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        kubernetes_services = ["http://my-service-dns.my-namespace:9102/metrics"]
        ```

    - Küme genelinde belirli bir URL 'den Prometheus ölçümlerinin bir listesini yapılandırmak için, aşağıdaki örneği kullanarak ConfigMap dosyasını yapılandırın.

        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings
        [prometheus_data_collection_settings.cluster] 
        interval = "1m"  ## Valid time units are s, m, h.
        fieldpass = ["metric_to_pass1", "metric_to_pass12"] ## specify metrics to pass through 
        fielddrop = ["metric_to_drop"] ## specify metrics to drop from collecting
        urls = ["http://myurl:9101/metrics"] ## An array of urls to scrape metrics from
        ```

    - Kümedeki her bir düğüm için bir aracının DaemonSet Prometheus ölçümlerinin scraping 'ini yapılandırmak için ConfigMap içinde aşağıdakileri yapılandırın:
    
        ```
        prometheus-data-collection-settings: |- 
        # Custom Prometheus metrics data collection settings 
        [prometheus_data_collection_settings.node] 
        interval = "1m"  ## Valid time units are s, m, h. 
        urls = ["http://$NODE_IP:9103/metrics"] 
        fieldpass = ["metric_to_pass1", "metric_to_pass2"] 
        fielddrop = ["metric_to_drop"] 
        ```

        >[!NOTE]
        >$NODE _IP, belirli bir kapsayıcı öngörüleri parametresidir ve düğüm IP adresi yerine kullanılabilir. Tamamen büyük harf olmalıdır. 

    - Bir pod ek açıklaması belirterek Prometheus ölçümlerinin korumasını yapılandırmak için aşağıdaki adımları uygulayın:

       1. ConfigMap ' te şunları belirtin:

            ```
            prometheus-data-collection-settings: |- 
            # Custom Prometheus metrics data collection settings
            [prometheus_data_collection_settings.cluster] 
            interval = "1m"  ## Valid time units are s, m, h
            monitor_kubernetes_pods = true 
            ```

       2. Pod ek açıklamaları için aşağıdaki yapılandırmayı belirtin:

           ```
           - prometheus.io/scrape:"true" #Enable scraping for this pod 
           - prometheus.io/scheme:"http" #If the metrics endpoint is secured then you will need to set this to `https`, if not default ‘http’
           - prometheus.io/path:"/mymetrics" #If the metrics path is not /metrics, define it with this annotation. 
           - prometheus.io/port:"8000" #If port is not 9102 use this annotation
           ```
    
          Ek açıklamaları olan Pod 'ler için izlemeyi belirli ad alanlarına kısıtlamak istiyorsanız, örneğin yalnızca üretim iş yükleri için ayrılmış Pod dahil, öğesini `monitor_kubernetes_pod` `true` configmap içinde olarak ayarlayın ve ad alanlarını, atık olarak bulunan ad alanlarını belirten ad alanı filtresini ekleyin `monitor_kubernetes_pods_namespaces` . Örneğin, `monitor_kubernetes_pods_namespaces = ["default1", "default2", "default3"]`

2. Değişikliklerinizi düzenleyicide kaydedin.

Yapılandırma değişikliğinin, yürürlüğe girmeden önce tamamlanması birkaç dakika sürebilir ve kümedeki tüm omsagent 'lar yeniden başlatılır. Yeniden başlatma, tüm omsagent pods için aynı anda yeniden başlatma işlemi için bir yeniden başlatma işlemi yapılır. Yeniden başlatmalar tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonucu içerir: `configmap "container-azm-ms-agentconfig" created` .

Komutunu çalıştırarak güncelleştirilmiş ConfigMap 'i görüntüleyebilirsiniz `oc describe configmaps container-azm-ms-agentconfig -n openshift-azure-logging` . 

## <a name="applying-updated-configmap"></a>Güncelleştirilmiş ConfigMap uygulanıyor

Kümenize zaten bir ConfigMap dağıttıysanız ve daha yeni bir yapılandırmayla güncelleştirmek istiyorsanız, daha önce kullandığınız ConfigMap dosyasını düzenleyebilir ve daha sonra aynı komutları kullanarak uygulayabilirsiniz.

Aşağıdaki Kubernetes ortamları için:

- Azure Kubernetes Service (AKS)
- Azure Stack veya şirket içi
- Azure Red Hat OpenShift ve Red Hat OpenShift sürüm 4. x

komutunu çalıştırın `kubectl apply -f <configmap_yaml_file.yaml` . 

Bir Azure Red Hat OpenShift v3. x kümesi için, `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` dosyayı değiştirmek ve kaydetmek üzere varsayılan Düzenleyicinizde açmak için komutunu çalıştırın.

Yapılandırma değişikliğinin, yürürlüğe girmeden önce tamamlanması birkaç dakika sürebilir ve kümedeki tüm omsagent 'lar yeniden başlatılır. Yeniden başlatma, tüm omsagent pods için aynı anda yeniden başlatma işlemi için bir yeniden başlatma işlemi yapılır. Yeniden başlatmalar tamamlandığında, aşağıdakine benzer bir ileti görüntülenir ve sonucu içerir: `configmap "container-azm-ms-agentconfig" updated` .

## <a name="verify-configuration"></a>Yapılandırmayı Doğrula

Yapılandırmanın bir kümeye başarıyla uygulandığını doğrulamak için, bir aracı Pod 'dan günlükleri gözden geçirmek üzere aşağıdaki komutu kullanın: `kubectl logs omsagent-fdf58 -n=kube-system` . 

>[!NOTE]
>Bu komut, Azure Red Hat OpenShift v3. x kümesi için geçerli değildir.
> 

Omsagent pods 'den yapılandırma hataları varsa, çıktıda aşağıdakine benzer hatalar gösterilir:

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Yapılandırma değişikliklerini uygulamayla ilgili hatalar İnceleme için de kullanılabilir. Aşağıdaki seçenekler, yapılandırma değişiklikleri için ek sorun giderme ve Prometheus ölçümlerinin korlama işlemlerini gerçekleştirmek için kullanılabilir:

- Aynı komutu kullanan bir aracı Pod günlüklerinden `kubectl logs` 
    >[!NOTE]
    >Bu komut, Azure Red Hat OpenShift kümesi için geçerli değildir.
    > 

- Canlı verilerden (Önizleme). Canlı veriler (Önizleme) günlüklerinde şuna benzer hatalar gösterilmektedir:

    ```
    2019-07-08T18:55:00Z E! [inputs.prometheus]: Error in plugin: error making HTTP request to http://invalidurl:1010/metrics: Get http://invalidurl:1010/metrics: dial tcp: lookup invalidurl on 10.0.0.10:53: no such host
    ```

- Log Analytics çalışma alanınızdaki **KubeMonAgentEvents** tablosundan. Veriler, atık hata ve yapılandırma hataları için *hata* önem derecesi ile her saat için *Uyarı* önem derecesine sahip olarak gönderilir. Herhangi bir hata yoksa, tablodaki *girişte, hiçbir hata raporlayan önem derecesine* sahip veriler olur. **Etiketler** özelliği, hatanın oluştuğu Pod ve kapsayıcı kimliği ve ayrıca ilk oluşum, son oluşum ve Son saatteki sayı hakkında daha fazla bilgi içerir.

- Azure Red Hat OpenShift v3. x ve v4. x için, OpenShift-Azure-Logging günlük koleksiyonunun etkinleştirilip etkinleştirilmediğini doğrulamak üzere **ContainerLog** tablosunu arayarak omsagent günlüklerini denetleyin.

Hatalar omsagent 'ın dosyayı ayrıştırmasını önler, yeniden başlatılmasına ve varsayılan yapılandırmayı kullanmasına neden olur. Azure Red Hat OpenShift v3. x dışındaki kümelerde ConfigMap 'teki hataları düzelttikten sonra, YAML dosyasını kaydedin ve şu komutu çalıştırarak güncelleştirilmiş ConfigMaps 'leri uygulayın: `kubectl apply -f <configmap_yaml_file.yaml` . 

Azure Red Hat OpenShift v3. x için, şu komutu çalıştırarak güncelleştirilmiş ConfigMaps 'ı düzenleyin ve kaydedin: `oc edit configmaps container-azm-ms-agentconfig -n openshift-azure-logging` .

## <a name="query-prometheus-metrics-data"></a>Sorgu Prometheus ölçüm verileri

Azure Izleyici tarafından ve aracı tarafından bildirilen yapılandırma/Koruma hatalarından oluşan Prometheus ölçümlerini görüntülemek için [sorgu Prometheus ölçüm verilerini](container-insights-log-search.md#query-prometheus-metrics-data) ve [sorgu yapılandırması ya da veri yapılandırma hatalarını](container-insights-log-search.md#query-config-or-scraping-errors)gözden geçirin.

## <a name="view-prometheus-metrics-in-grafana"></a>Grafana 'de Prometheus ölçümlerini görüntüleme

Kapsayıcı öngörüleri, Grafana panolar içinde Log Analytics çalışma alanınızda depolanan ölçümlerin görüntülenmesini destekler. Grafana 'in [Pano deposundan](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) indirebileceğiniz bir şablon sağladık ve özel Grafana panolarında görselleştirmek üzere izlenen kümelerinizdeki ek verileri sorgulama hakkında bilgi edinmenize yardımcı olur. 

## <a name="review-prometheus-data-usage"></a>Prometheus veri kullanımını gözden geçirin

Her ölçüm boyutunun, yüksek olup olmadığını anlamak için günde GB cinsinden giriş hacmini belirlemek için aşağıdaki sorgu sağlanır.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize VolumeInGB = (sum(_BilledSize) / (1024 * 1024 * 1024)) by Name
| order by VolumeInGB desc
| render barchart
```

Çıktı aşağıdakine benzer sonuçları gösterir:

![Ekran görüntüsü veri alma biriminin günlük sorgu sonuçlarını gösterir](./media/container-insights-prometheus-integration/log-query-example-usage-03.png)

Her ölçüm boyutunun GB cinsinden ne kadar olduğunu tahmin etmek için, çalışma alanında alınan veri hacminin yüksek olup olmadığını anlamak için aşağıdaki sorgu sağlanır.

```
InsightsMetrics
| where Namespace contains "prometheus"
| where TimeGenerated > ago(24h)
| summarize EstimatedGBPer30dayMonth = (sum(_BilledSize) / (1024 * 1024 * 1024)) * 30 by Name
| order by EstimatedGBPer30dayMonth desc
| render barchart
```

Çıktı aşağıdakine benzer sonuçları gösterir:

![Veri alma biriminin günlük sorgu sonuçları](./media/container-insights-prometheus-integration/log-query-example-usage-02.png)

Veri kullanımını izleme ve maliyeti çözümleme hakkında daha fazla bilgi, [Azure Izleyici günlükleriyle kullanımı ve maliyetleri yönetme](../logs/manage-cost-storage.md)bölümünde bulunabilir.

## <a name="next-steps"></a>Sonraki adımlar

Stdout, stderr ve ortam değişkenlerinin aracı koleksiyonu ayarlarını [buradan](container-insights-agent-config.md)kapsayıcı iş yüklerinden yapılandırma hakkında daha fazla bilgi edinin.
