---
title: Azure API Management şirket içinde barındırılan ağ geçidi için yerel ölçümleri ve günlükleri yapılandırma | Microsoft Docs
description: Azure API Management bir Kubernetes noter üzerinde şirket içinde barındırılan ağ geçidine yönelik yerel ölçümleri ve günlükleri yapılandırma hakkında bilgi edinin
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100594166"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management şirket içinde barındırılan ağ geçidi için yerel ölçümleri ve günlükleri yapılandırma

Bu makalede, bir Kubernetes kümesinde dağıtılan şirket içinde [barındırılan ağ geçidi](./self-hosted-gateway-overview.md) için yerel ölçümleri ve günlükleri yapılandırmaya ilişkin ayrıntılar sağlanmaktadır. Bulut ölçümlerini ve günlüklerini yapılandırmak için [Bu makaleye](how-to-configure-cloud-metrics-logs.md)bakın. 

## <a name="metrics"></a>Ölçümler
Şirket içinde barındırılan ağ geçidi, ölçüm toplama ve toplama için ifkenme protokolü haline gelen [Statsd](https://github.com/statsd/statsd)'yi destekler. Bu bölümde, Kubernetes 'e StatsD dağıtma, ağ geçidini StatsD aracılığıyla ölçümleri yaymaya yönelik yapılandırma ve ölçümleri izlemek için [Prometheus](https://prometheus.io/) kullanma adımları anlatılmaktadır. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>Kümeye StatsD ve Prometheus dağıtma

Aşağıda, kendi kendine barındırılan bir ağ geçidinin dağıtıldığı Kubernetes kümesine StatsD ve Prometheus dağıtmaya yönelik örnek bir YAML yapılandırması verilmiştir. Ayrıca, her biri için bir [hizmet](https://kubernetes.io/docs/concepts/services-networking/service/) oluşturur. Şirket içinde barındırılan ağ geçidi, ölçümleri StatsD hizmetinde yayımlar. Prometheus panosuna kendi hizmeti aracılığıyla erişeceğiz.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Konfigürasyonları adlı bir dosyaya kaydedin `metrics.yaml` ve her şeyi kümeye dağıtmak için aşağıdaki komutu kullanın:

```console
kubectl apply -f metrics.yaml
```

Dağıtım tamamlandıktan sonra, pods 'nin çalıştığını denetlemek için aşağıdaki komutu çalıştırın. Pod adınızın farklı olacağını unutmayın. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Hizmetlerin çalıştığından emin olmak için aşağıdaki komutu çalıştırın. `CLUSTER-IP` `PORT` StatsD hizmetini ve daha sonra da bu hizmetin gerekli olduğunu bir yere göz atın. Prometheus panosunu ve ' u kullanarak ziyaret edebilirsiniz `EXTERNAL-IP` `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>Ölçümleri göstermek için şirket içinde barındırılan ağ geçidini yapılandırma

StatsD ve Prometheus öğelerinin her ikisi de dağıtıldığına göre, otomatik olarak barındırılan ağ geçidinin yapılandırmalarının StatsD aracılığıyla ölçümleri yayılmasına başlayabiliriz. Özellik, `telemetry.metrics.local` Şirket içinde barındırılan ağ geçidi dağıtımının ConfigMap 'teki anahtar kullanılarak, ek seçeneklerle etkinleştirilebilir veya devre dışı bırakılabilir. Kullanılabilir seçeneklerin bir dökümü aşağıda verilmiştir:

| Alan  | Varsayılan | Description |
| ------------- | ------------- | ------------- |
| Telemetri. ölçümler. yerel  | `none` | StatsD aracılığıyla günlüğe kaydetmeyi etkinleştirir. Değer, olabilir `none` `statsd` . |
| Telemetri. ölçümler. Local. statsd. Endpoint  | yok | StatsD uç noktasını belirtir. |
| Telemetri. ölçümler. Local. statsd. örnekleme  | yok | Ölçüm örnekleme hızını belirtir. Değer 0 ile 1 arasında olabilir. ör., `0.5`|
| Telemetri. ölçümler. yerel. statsd. Tag-Format  | yok | StatsD Dışarı Aktarıcı [etiketleme biçimi](https://github.com/prometheus/statsd_exporter#tagging-extensions). Değer,,,, olabilir `none` `librato` `dogStatsD` `influxDB` . |

Örnek bir yapılandırma aşağıda verilmiştir:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Şirket içinde barındırılan ağ geçidi dağıtımının YAML dosyasını yukarıdaki yapılandırmalarda güncelleştirin ve aşağıdaki komutu kullanarak değişiklikleri uygulayın: 

```console
kubectl apply -f <file-name>.yaml
 ```

En son yapılandırma değişikliklerini almak için aşağıdaki komutu kullanarak ağ geçidi dağıtımını yeniden başlatın:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>Ölçümleri görüntüleme

Şimdi dağıtılan ve yapılandırılmış her şey var ve şirket içinde barındırılan ağ geçidi, ölçümleri StatsD aracılığıyla raporlemelidir. Prometheus, StatsD 'daki ölçümleri alacak. Prometheus hizmetini kullanarak Prometheus panosuna gidin `EXTERNAL-IP` `PORT` . 

Şirket içinde barındırılan ağ geçidiyle bazı API çağrıları yapın, her şey doğru yapılandırılmışsa, aşağıdaki ölçümleri görüntüleyebilmelisiniz:

| Metric  | Açıklama |
| ------------- | ------------- |
| İstekler  | Dönemdeki API isteklerinin sayısı |
| Durationınms | Ağ geçidinin isteği aldığı andan, yanıtın tamamen gönderildiği ana kadar geçen milisaniye cinsinden süre |
| BackendDurationInMS | Genel arka uç GÇ (bağlanma, gönderme ve alma bayt’ları) için harcanan milisaniye sayısı  |
| ClientDurationInMS | Genel istemci G/Ç (bağlanma, gönderme ve alma bayt’ları) için harcanan milisaniye sayısı  |

## <a name="logs"></a>Günlükler

Şirket içinde barındırılan ağ geçidi, varsayılan olarak günlüklere çıkış verir `stdout` `stderr` . Aşağıdaki komutu kullanarak günlükleri kolayca görüntüleyebilirsiniz:

```console
kubectl logs <pod-name>
```

Şirket içinde barındırılan ağ geçidiniz Azure Kubernetes hizmetinde dağıtılmışsa, iş yüklerinizi toplamak ve Log Analytics günlükleri görüntülemek için [kapsayıcılar Için Azure izleyici](../azure-monitor/containers/container-insights-overview.md) 'yi etkinleştirebilirsiniz `stdout` `stderr` . 

Şirket içinde barındırılan ağ geçidi,, ve gibi çeşitli protokolleri de `localsyslog` destekler `rfc5424` `journal` . Aşağıdaki tabloda desteklenen tüm seçenekler özetlenmektedir. 

| Alan  | Varsayılan | Description |
| ------------- | ------------- | ------------- |
| Telemetri. logs. std  | `text` | Standart akışlara günlük kaydını sağlar. Değer `none` , `text` , `json` |
| Telemetri. logs. Local  | `none` | Yerel günlüğe kaydetmeye izin vermez. Değer,, `none` , `auto` , `localsyslog` `rfc5424``journal`  |
| Telemetri. logs. Local. localsyslog. Endpoint  | yok | Localsyslog uç noktasını belirtir.  |
| Telemetri. logs. Local. localsyslog. tesis  | yok | Localsyslog [tesis kodunu](https://en.wikipedia.org/wiki/Syslog#Facility)belirtir. ör., `7` 
| Telemetri. logs. Local. rfc5424. Endpoint  | yok | Rfc5424 uç noktasını belirtir.  |
| Telemetri. logs. Local. rfc5424. tesis  | yok | [Rfc5424](https://tools.ietf.org/html/rfc5424)başına tesis kodunu belirtir. ör., `7`  |
| Telemetri. logs. Local. Journal. Endpoint  | yok | Günlük uç noktasını belirtir.  |

Yerel günlüğe kaydetme işleminin örnek bir yapılandırması aşağıda verilmiştir:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Sonraki adımlar

* Şirket içinde barındırılan ağ geçidi hakkında daha fazla bilgi edinmek için bkz. [Azure API Management Self-barındırılan ağ geçidine genel bakış](self-hosted-gateway-overview.md)
* [Buluttaki günlükleri yapılandırma ve kalıcı](how-to-configure-local-metrics-logs.md) hale getirme hakkında bilgi edinin
