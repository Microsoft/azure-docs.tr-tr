---
title: Azure Kubernetes Service (AKS) ' de Istio 'yu kurma
description: Azure Kubernetes Service (AKS) kümesinde hizmet ağı oluşturma ve kullanma hakkında bilgi edinin
author: paulbouwer
ms.topic: article
ms.date: 10/02/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: c42e9c31397e9313898d7029366bc8de169d368e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94683837"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de Istio 'yu yükleyip kullanma

[Istio][istio-github] , bir Kubernetes kümesindeki mikro hizmetlerde anahtar bir işlevsellik kümesi sağlayan açık kaynaklı bir hizmet kafesidir. Bu özellikler trafik yönetimi, hizmet kimliği ve güvenlik, ilke zorlama ve Observability içerir. Istio hakkında daha fazla bilgi için resmi [nedir?][istio-docs-concepts] belgesine bakın.

Bu makalede, Istio 'un nasıl yükleneceği gösterilmektedir. Istio `istioctl` istemci ikilisi istemci makinenize yüklenir ve IKS bileşenleri aks 'deki bir Kubernetes kümesine yüklenir.

> [!NOTE]
> Aşağıdaki yönergeler, Istio sürümüne başvurur `1.7.3` .
>
> Bu sürümler, `1.7.x` Kubernetes sürümünde istio ekibi tarafından test edilmiştir `1.16+` . [GitHub-Istio yayımları][istio-github-releases]üzerinde ek istio sürümleri, [istio haberleri][istio-release-notes] ve desteklenen Kubernetes sürümlerinin her biri hakkında bilgi edinmek IÇIN bkz. [Genel SSS][istio-faq].

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Istio istioctl istemci ikilisini indirme ve yükleme
> * AKS 'e Istio 'yu yükler
> * Istio yüklemesini doğrulama
> * Eklentilere erişin
> * AKS 'ten Istio 'dan kaldırma

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede açıklanan adımlarda, Kubernetes RBAC etkin olan bir AKS kümesi (Kubernetes `1.16` ve üzeri) oluşturdunuz ve kümeyle bir bağlantı oluşturmuş olduğunuz varsayılır `kubectl` . Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, [aks hızlı başlangıç][aks-quickstart]bölümüne bakın.

AKS kümenizde Istio çalıştırmaya yönelik ek kaynak gereksinimlerini anlamak için, [Istio performans ve ölçeklenebilirlik](https://istio.io/docs/concepts/performance-and-scalability/) belgelerini okuduğunuzdan emin olun. Çekirdek ve bellek gereksinimleri, belirli iş yükünüze göre farklılık gösterecektir. Kuruluma uygun sayıda düğüm ve VM boyutu seçin.

Bu makale, Istio yükleme kılavuzunu çeşitli ayrı adımlara ayırır. Nihai sonuç, resmi Istio yükleme [kılavuzundaki][istio-install-istioctl]yapıda aynıdır.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-operator-on-aks"></a>AKS 'e Istio Işlecini yükler

İstio, AKS kümenizdeki Istio bileşenlerine yönelik yükleme ve güncelleştirmeleri yönetmek için bir [operatör][istio-install-operator] sağlar. İstemci ikilisini kullanarak Istio Işlecini yükleyeceğiz `istioctl` .

```bash
istioctl operator init
```

Istio Işlecinin yüklendiğini onaylamak için aşağıdaki çıktıya benzer bir şey görmeniz gerekir.

```console
Using operator Deployment image: docker.io/istio/operator:1.7.3
✔ Istio operator installed
✔ Installation complete
```

Istio Işleci `istio-operator` ad alanına yüklenir. Ad alanını sorgulayın.

```bash
kubectl get all -n istio-operator
```

Aşağıdaki bileşenlerin dağıtıldığını görmeniz gerekir.

```console
NAME                                  READY   STATUS    RESTARTS   AGE
pod/istio-operator-6d7958b7bf-wxgdc   1/1     Running   0          2m43s

NAME                     TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
service/istio-operator   ClusterIP   10.0.8.57    <none>        8383/TCP   2m43s

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-operator   1/1     1            1           2m43s

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-operator-6d7958b7bf   1         1         1       2m43s
```

Operatör düzeniyle ilgili daha fazla bilgi edinebilirsiniz ve [Kubernetes.io][kubernetes-operator]aracılığıyla karmaşık görevlerin otomatikleştirilmesine nasıl yardımcı olabileceğini öğrenebilirsiniz.


### <a name="install-istio-components"></a>Istio bileşenlerini yükler

Şimdi AKS kümenize Istio Işlecini başarıyla yüklediğimiz için, bu durumda Istio bileşenlerini yükleme zamanı. 

IBU `default` [işleç belirtimini][istio-control-plane]oluşturmak için, [istio yapılandırma profilini][istio-configuration-profiles] kullanacağız.

Bu `istioctl` yapılandırma profilinin yapılandırmasını görüntülemek için aşağıdaki komutu çalıştırabilirsiniz `default` .

```bash
istioctl profile dump default
```

> [!NOTE]
> IBU Şu anda Linux düğümlerinde çalıştırılmak üzere zamanlanmalıdır. Kümenizde Windows Server düğümleriniz varsa, Istio 'ların yalnızca Linux düğümlerinde çalışacak şekilde zamanlandığından emin olmanız gerekir. Düğümlerin doğru düğümlere zamanlandığından emin olmak için [düğüm seçicileri][kubernetes-node-selectors] kullanacağız.

> [!CAUTION]
> [İstio CNI][istio-feature-cni] istio 'nun özellikleri şu anda [Alpha][istio-feature-stages]' dir; bu nedenle bu, bu sayede bu şekilde düşünülmelidir. 

Aşağıdaki içerikle adlı bir dosya oluşturun `istio.aks.yaml` . Bu dosya, Istio 'yu yapılandırmak için ı, [Işleç belirtimini][istio-control-plane] tutacaktır.

```yaml
apiVersion: install.istio.io/v1alpha1
kind: IstioOperator
metadata:
  namespace: istio-system
  name: istio-control-plane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  # Enable the addons that we will want to use
  addonComponents:
    grafana:
      enabled: true
    prometheus:
      enabled: true
    tracing:
      enabled: true
    kiali:
      enabled: true
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
    kiali:
      dashboard:
        auth:
          strategy: anonymous 
```

`istio-system`Ad alanını oluşturun ve bu ad alanına Istio operatörü belirtimini dağıtın. İstio Işleci, Istio operatörü belirtimine göre izleniyor ve bu özelliği kullanarak AKS kümenize Istio 'yu yükleyip yapılandıracaksınız.

```bash
kubectl create ns istio-system

kubectl apply -f istio.aks.yaml 
```

Bu noktada, AKS kümenize Istio 'u dağıttık. Başarılı bir Istio dağıtımına sahip olduğunuzdan emin olmak için, bir sonraki bölüme ilerleyip bu [yüklemeyi doğrulayalım](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Istio yüklemesini doğrulama

Bu `istio-system` ad alanını sorgulayın, burada, istio ve eklenti bileşenlerinin Istio işleci tarafından yüklendiği yer:

```bash
kubectl get all -n istio-system
```

Aşağıdaki bileşenleri görmeniz gerekir:

- `istio*` -Istio bileşenleri
- `jaeger-*`, `tracing` , ve `zipkin` -izleme eklentisi
- `prometheus` -ölçüm eklentisi
- `grafana` -analiz ve izleme panosu eklentisi
- `kiali` -hizmet ağı Pano eklentisi

```console
NAME                                        READY   STATUS    RESTARTS   AGE
pod/grafana-7cf9794c74-mpfbp                1/1     Running   0          5m53s
pod/istio-ingressgateway-86b5dbdcb9-ndrp5   1/1     Running   0          5m57s
pod/istio-tracing-c98f4b8fc-zqklg           1/1     Running   0          82s
pod/istiod-6965c56995-4ph9h                 1/1     Running   0          6m15s
pod/kiali-7b44985d68-p87zh                  1/1     Running   0          81s
pod/prometheus-6868989549-5ghzz             1/1     Running   0          81s

NAME                                TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                                                      AGE
service/grafana                     ClusterIP      10.0.226.39    <none>         3000/TCP                                                     5m54s
service/istio-ingressgateway        LoadBalancer   10.0.143.56    20.53.72.254   15021:32166/TCP,80:31684/TCP,443:31302/TCP,15443:30863/TCP   5m57s
service/istiod                      ClusterIP      10.0.211.228   <none>         15010/TCP,15012/TCP,443/TCP,15014/TCP,853/TCP                6m16s
service/jaeger-agent                ClusterIP      None           <none>         5775/UDP,6831/UDP,6832/UDP                                   82s
service/jaeger-collector            ClusterIP      10.0.7.62      <none>         14267/TCP,14268/TCP,14250/TCP                                82s
service/jaeger-collector-headless   ClusterIP      None           <none>         14250/TCP                                                    82s
service/jaeger-query                ClusterIP      10.0.52.172    <none>         16686/TCP                                                    82s
service/kiali                       ClusterIP      10.0.71.179    <none>         20001/TCP                                                    82s
service/prometheus                  ClusterIP      10.0.171.151   <none>         9090/TCP                                                     82s
service/tracing                     ClusterIP      10.0.195.137   <none>         80/TCP                                                       82s
service/zipkin                      ClusterIP      10.0.136.111   <none>         9411/TCP                                                     82s

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/grafana                1/1     1            1           5m54s
deployment.apps/istio-ingressgateway   1/1     1            1           5m58s
deployment.apps/istio-tracing          1/1     1            1           83s
deployment.apps/istiod                 1/1     1            1           6m16s
deployment.apps/kiali                  1/1     1            1           83s
deployment.apps/prometheus             1/1     1            1           82s

NAME                                              DESIRED   CURRENT   READY   AGE
replicaset.apps/grafana-7cf9794c74                1         1         1       5m54s
replicaset.apps/istio-ingressgateway-86b5dbdcb9   1         1         1       5m58s
replicaset.apps/istio-tracing-c98f4b8fc           1         1         1       83s
replicaset.apps/istiod-6965c56995                 1         1         1       6m16s
replicaset.apps/kiali-7b44985d68                  1         1         1       82s
replicaset.apps/prometheus-6868989549             1         1         1       82s

NAME                                                       REFERENCE                         TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/istio-ingressgateway   Deployment/istio-ingressgateway   7%/80%    1         5         1          5m57s
horizontalpodautoscaler.autoscaling/istiod                 Deployment/istiod                 1%/80%    1         5         1          6m16s
```

Ayrıca, Istio Işlecinin günlüklerini izleyerek yükleme hakkında ek Öngörüler elde edebilirsiniz.

```bash
kubectl logs -n istio-operator -l name=istio-operator -f
```

, `istio-ingressgateway` Bir dış IP gösteriyorsa `<pending>` , Azure ağı tarafından bir IP adresi atanmadan birkaç dakika bekleyin.

Tüm FID 'ler durumunu göstermelidir `Running` . Ayırımlarınızın bu durumları yoksa, tamamlanana kadar bir dakika veya iki tane bekleyin. Herhangi bir pod bir sorun bildirirse, çıktısını ve durumlarını gözden geçirmek için [kubectl 'yi bir pod betimleyen][kubectl-describe] komutunu kullanın.

## <a name="accessing-the-add-ons"></a>Eklentilere erişme

Ek işlevsellik sağlayan bir dizi eklenti, Istio Işleci tarafından yüklendi. Eklentiler için Web uygulamaları, bir dış IP adresi aracılığıyla **herkese açık bir şekilde gösterilmez** . 

Eklenti kullanıcı arabirimlerine erişmek için `istioctl dashboard` komutunu kullanın. Bu komut, istemci makineniz ile AKS kümenizdeki ilgili Pod arasında güvenli bir bağlantı oluşturmak için [kubectl Port-Forward][kubectl-port-forward] ve rastgele bir bağlantı noktası kullanır. Daha sonra, eklenti Web uygulamasını varsayılan tarayıcınızda otomatik olarak açar.

### <a name="grafana"></a>Grafana

Istio için analiz ve izleme panoları [Grafana][grafana]tarafından sağlanır. İstendiğinde, Grafana gizli anahtarı aracılığıyla oluşturduğunuz kimlik bilgilerini kullanmayı unutmayın. Grafana panosunu güvenli şekilde açmak için aşağıdaki adımları izleyin:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

[Ceso ölçümleri Prometheus][prometheus]tarafından sağlanmaktadır. Prometheus panosunu şu şekilde güvenli şekilde açın:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Istio 'da izleme, [Jaeger][jaeger]tarafından sağlanır. Şu şekilde Jaeger panosunu güvenli şekilde açın:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Bir hizmet ağı Observability panosu [Kiali][kiali]tarafından sağlanır. İstendiğinde Kiali gizli dizisi aracılığıyla oluşturduğunuz kimlik bilgilerini kullanmayı unutmayın. Kiali panosunu güvenli şekilde aşağıdaki gibi açın:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

[Envoy][envoy] proxy 'lerine yönelik basit bir arabirim mevcuttur. Belirtilen Pod 'da çalışan bir Envoy proxy 'si için yapılandırma bilgileri ve ölçümler sağlar. Aşağıdaki gibi, Envoy arabirimini güvenli şekilde açın:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>AKS 'ten Istio 'dan kaldırma

> [!WARNING]
> Çalışan bir sistemden Istio 'un silinmesi, hizmetleriniz arasında trafik ile ilgili sorunlar oluşmasına neden olabilir. Devam etmeden önce sisteminizin ilerlemeden düzgün şekilde çalışmaya yönelik hükümler oluşturduğunuzdan emin olun.

### <a name="remove-istio"></a>Istio 'yu kaldır

AKS kümenizdeki ICU 'yi kaldırmak için, `IstioOperator` `istio-control-plane` daha önce eklediğimiz adlı kaynağı silin. IBU Işleci, Istio Işleci belirtiminin kaldırıldığını algılar ve ardından ilişkili tüm çalışan bileşenlerini siler.

```bash
kubectl delete istiooperator istio-control-plane -n istio-system
```

Tüm Istio bileşenlerinin silindiğini denetlemek için aşağıdakileri çalıştırabilirsiniz.

```bash
kubectl get all -n istio-system
```

### <a name="remove-istio-operator"></a>IO Işlecini kaldır

IBU Işleci başarıyla kaldırıldıktan sonra, Istio Işlecini de kaldırabilirsiniz.

```bash
istioctl operator remove
```

Son olarak, `istio-` ad alanlarını kaldırın.

```bash
kubectl delete ns istio-system
kubectl delete ns istio-operator
```

## <a name="next-steps"></a>Sonraki adımlar

Istio 'nun daha fazla yükleme ve yapılandırma seçeneklerini araştırmak için aşağıdaki resmi Istio yönergelerine bakın:

- [İstio-Yükleme Kılavuzu][istio-installation-guides]

Ayrıca şunları kullanarak ek senaryolar izleyebilirsiniz:

- [İstio BookInfo uygulama örneği][istio-bookinfo-example]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-install-operator]: https://istio.io/latest/docs/setup/install/operator/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[kubernetes-operator]: https://kubernetes.io/docs/concepts/extend-kubernetes/operator/
[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
