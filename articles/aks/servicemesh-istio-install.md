---
title: Azure Kubernetes Service (AKS) ' de Istio 'yu kurma
description: Azure Kubernetes Service (AKS) kümesinde hizmet ağı oluşturma ve kullanma hakkında bilgi edinin
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 11/15/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2768c2d4cef68dcf25e25c047aaa69653af5e0b6
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170853"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de Istio 'yu yükleyip kullanma

[Istio][istio-github] , bir Kubernetes kümesindeki mikro hizmetlerde anahtar bir işlevsellik kümesi sağlayan açık kaynaklı bir hizmet kafesidir. Bu özellikler trafik yönetimi, hizmet kimliği ve güvenlik, ilke zorlama ve Observability içerir. Istio hakkında daha fazla bilgi için resmi [nedir?][istio-docs-concepts] belgesine bakın.

Bu makalede, Istio 'un nasıl yükleneceği gösterilmektedir. Istio `istioctl` istemci ikilisi istemci makinenize yüklenir ve ıKS bileşenleri AKS 'deki bir Kubernetes kümesine yüklenir.

> [!NOTE]
> Aşağıdaki yönergeler, Istio sürüm `1.4.0`referans.
>
> Istio `1.4.x` yayınları, Kubernetes sürümleriyle `1.13`, `1.14`, `1.15`karşı Istio ekibi tarafından test edilmiştir. [GitHub-Istio yayımları][istio-github-releases]üzerinde ek istio sürümleri, [istio haberleri][istio-release-notes] ve desteklenen Kubernetes sürümlerinin her biri hakkında bilgi edinmek IÇIN bkz. [Genel SSS][istio-faq].

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Istio istioctl istemci ikilisini indirme ve yükleme
> * AKS 'e Istio 'yu yükler
> * Istio yüklemesini doğrulama
> * Eklentilere erişin
> * AKS 'ten Istio 'dan kaldırma

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede açıklanan adımlarda, RBAC etkinleştirilmiş bir AKS kümesi (Kubernetes `1.13` ve üzeri) oluşturdunuz ve kümeyle `kubectl` bir bağlantı oluşturmuş olduğunuz varsayılır. Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, [aks hızlı başlangıç][aks-quickstart]bölümüne bakın.

AKS kümenizde Istio çalıştırmaya yönelik ek kaynak gereksinimlerini anlamak için, [Istio performans ve ölçeklenebilirlik](https://istio.io/docs/concepts/performance-and-scalability/) belgelerini okuduğunuzdan emin olun. Çekirdek ve bellek gereksinimleri, belirli iş yükünüze göre farklılık gösterecektir. Kuruluma uygun sayıda düğüm ve VM boyutu seçin.

Bu makale, Istio yükleme kılavuzunu çeşitli ayrı adımlara ayırır. Nihai sonuç, resmi Istio yükleme [kılavuzundaki][istio-install-istioctl]yapıda aynıdır.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>AKS 'e Istio bileşenlerini yükler

[Grafana][grafana] ve [kiali][kiali] 'nin bir parçası olarak ilerliyoruz. Grafana, analiz ve izleme panoları sağlar ve Kiali bir hizmet ağı Observability panosu sağlar. Kurulumumuzda, bu bileşenlerin her biri [gizli][kubernetes-secrets]olarak sağlanması gereken kimlik bilgilerini gerektirir.

Istio bileşenlerini yükleyebilmemiz için, hem Grafana hem de Kiali için gizli dizileri oluşturuyoruz. Bu gizli diziler, IBU tarafından kullanılacak `istio-system` ad alanına yüklenmelidir, bu nedenle ad alanını da oluşturmanız gerekir. `kubectl create` aracılığıyla ad alanını oluştururken `--save-config` seçeneğini kullandığımızda, bu nesnenin gelecekte bu nesne üzerinde `kubectl apply` çalıştırabilmesi gerekir.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Istio bileşenlerini yükler

AKS kümenizdeki Grafana ve Kiali gizli dizilerini başarıyla oluşturduğumuzdan, bu durumda Istio bileşenlerinin yüklenmesi zaman alabilir. 

Istio için [Helm][helm] yükleme yaklaşımı gelecekte kullanım dışı olacaktır. Yeni yükleme yaklaşımı, `istioctl` istemci ikilisini, [istio yapılandırma profillerini][istio-configuration-profiles]ve yeni [istio denetim düzlemi belirtimini ve API][istio-control-plane]'yi kullanır. Bu yeni yaklaşım, Istio 'yu yüklemek için kullanacağız.

> [!NOTE]
> IBU Şu anda Linux düğümlerinde çalıştırılmak üzere zamanlanmalıdır. Kümenizde Windows Server düğümleriniz varsa, Istio 'ların yalnızca Linux düğümlerinde çalışacak şekilde zamanlandığından emin olmanız gerekir. Düğümlerin doğru düğümlere zamanlandığından emin olmak için [düğüm seçicileri][kubernetes-node-selectors] kullanacağız.

> [!CAUTION]
> [SDS (gizli bulma hizmeti)][istio-feature-sds] ve [istio CNI][istio-feature-cni] istio özellikleri şu anda [Alpha][istio-feature-stages]içinde olduğundan, bunlar etkinleştirilmeden önce düşünülmelidir. Ayrıca, [Service Account Token Volume Projection][kubernetes-feature-sa-projected-volume] Kubernetes ÖZELLIĞI (SDS için bir gereksinim) geçerli aks sürümlerinde etkin değildir.

Aşağıdaki içerikle `istio.aks.yaml` adlı bir dosya oluşturun. Bu dosya, UBO 'ın yapılandırılması için [istio denetim düzlemi belirtim][istio-control-plane] ayrıntılarını tutacaktır.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

`istioctl apply` komutunu ve yukarıdaki `istio.aks.yaml` Istio denetim düzlemi özellikleri dosyasını kullanarak istio 'yu aşağıdaki şekilde yüklersiniz:

```console
istioctl manifest apply -f istio.aks.yaml
```

Yükleyici bir dizi [crds][kubernetes-crd] dağıtır ve ardından bu istio yapılandırması için tanımlanan tüm ilgili nesneleri yüklemek üzere bağımlılıkları yönetir. Aşağıdaki çıktı kod parçacığına benzer bir şey görmeniz gerekir.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

Bu noktada, AKS kümenize Istio 'u dağıttık. Başarılı bir Istio dağıtımına sahip olduğunuzdan emin olmak için, bir sonraki bölüme ilerleyip bu [yüklemeyi doğrulayalım](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Istio yüklemesini doğrulama

Önce beklenen hizmetlerin oluşturulduğunu onaylayın. Çalışan hizmetleri görüntülemek için [kubectl Get svc][kubectl-get] komutunu kullanın. `istio-system` ad alanını sorgulayın, burada, Istio ve eklenti bileşenlerinin `istio` Helm grafiği tarafından yüklendiği yer:

```console
kubectl get svc --namespace istio-system --output wide
```

Aşağıdaki örnek çıktıda Şu anda çalışıyor olması gereken hizmetler gösterilmektedir:

- `istio-*` Hizmetleri
- `jaeger-*`, `tracing`ve `zipkin` eklenti izleme hizmetleri
- `prometheus` eklentisi ölçüm hizmeti
- `grafana` eklentisi Analizi ve izleme panosu hizmeti
- `kiali` eklentisi hizmet ağı Pano hizmeti

`istio-ingressgateway` `<pending>`dış IP 'si gösteriyorsa, Azure ağ tarafından bir IP adresi atanmadan birkaç dakika bekleyin.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Ardından, gerekli yığınların oluşturulduğunu onaylayın. [Kubectl Get Pod][kubectl-get] komutunu kullanın ve `istio-system` ad alanını sorgulayın:

```console
kubectl get pods --namespace istio-system
```

Aşağıdaki örnek çıktı, çalıştıran Pod 'leri göstermektedir:

- `istio-*` Pod
- `prometheus-*` eklenti ölçümleri Pod
- `grafana-*` eklentisi Analizi ve izleme panosu Pod
- `kiali` eklentisi hizmet ağı panosu Pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Tüm FID 'ler `Running`durumunu göstermelidir. Ayırımlarınızın bu durumları yoksa, tamamlanana kadar bir dakika veya iki tane bekleyin. Herhangi bir pod bir sorun bildirirse, çıktısını ve durumlarını gözden geçirmek için [kubectl 'yi bir pod betimleyen][kubectl-describe] komutunu kullanın.

## <a name="accessing-the-add-ons"></a>Eklentilere erişme

Yukarıdaki kurulumla, daha fazla işlevsellik sağlayan bir dizi eklenti yüklenmiş. Eklentiler için Web uygulamaları, bir dış IP adresi aracılığıyla **herkese açık bir şekilde gösterilmez** . 

Eklenti kullanıcı arabirimlerine erişmek için `istioctl dashboard` komutunu kullanın. Bu komut, [kubectl bağlantı noktasından ileri][kubectl-port-forward] ve rastgele bir bağlantı noktasından yararlanarak istemci makineniz ile aks kümenizdeki ilgili Pod arasında güvenli bir bağlantı oluşturur. Daha sonra, eklenti Web uygulamasını varsayılan tarayıcınızda otomatik olarak açar.

Bu makalenin önceki kısımlarında yer aldığı için kimlik bilgilerini belirterek Grafana ve Kiali için ek bir güvenlik katmanı ekledik.

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

### <a name="remove-istio-components-and-namespace"></a>Istio bileşenlerini ve ad alanını kaldır

AKS kümenizdeki ICU 'yi kaldırmak için, `istio.aks.yaml` Istio denetim düzlemi belirtimi dosyası ile `istioctl manifest generate` komutunu kullanın. Bu, yüklü tüm bileşenleri ve `istio-system` ad alanını kaldırmak için `kubectl delete` yönelttireceğiz dağıtılan bildirimi oluşturur.

```console
istioctl manifest generate -f istio.aks.yaml | kubectl delete -f -
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRDs ve gizli dizileri kaldır

Yukarıdaki komutlar tüm Istio bileşenlerini ve ad alanını siler, ancak yine de üretilmiş olan Istio gizli dizileri ile hala ayrıldık. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki belgelerde, bir kanarya yayını kullanıma sunmak üzere akıllı yönlendirme sağlamak için istio 'yu nasıl kullanabileceğiniz açıklanmaktadır:

> [!div class="nextstepaction"]
> [AKS Istio akıllı yönlendirme senaryosu][istio-scenario-routing]

Istio 'nun daha fazla yükleme ve yapılandırma seçeneklerini araştırmak için aşağıdaki resmi Istio yönergelerine bakın:

- [İstio-Yükleme Kılavuzu][istio-installation-guides]

Ayrıca şunları kullanarak ek senaryolar izleyebilirsiniz:

- [İstio BookInfo uygulama örneği][istio-bookinfo-example]

Application Insights ve Istio kullanarak AKS uygulamanızı nasıl izleyeceğinizi öğrenmek için aşağıdaki Azure Izleyici belgelerine bakın:

- [Kubernetes barındırılan uygulamalar için sıfır izleme uygulaması izleme][app-insights]

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
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha12.pb/#IstioControlPlane
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
