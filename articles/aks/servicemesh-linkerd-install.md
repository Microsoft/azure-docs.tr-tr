---
title: Azure Kubernetes Service (AKS) içinde Linkerd 'yi kurma
description: Azure Kubernetes Service (AKS) kümesinde hizmet ağı oluşturmak için Linkerd 'yi yüklemeyi ve kullanmayı öğrenin
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f70052a62152a20f808c1e491a663d1406fbd407
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747720"
---
# <a name="install-linkerd-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde Linkerd 'yi kurma

[Linkerd][linkerd-github] , açık kaynaklı bir hizmet ağı ve [cncf ınubating projem][linkerd-cncf]. Linkerd, trafik yönetimi, hizmet kimliği ve güvenlik, güvenilirlik ve Observability dahil olmak üzere özellikler sağlayan bir ultralight hizmet ağı. Linkerd hakkında daha fazla bilgi için bkz. resmi [LINKERD SSS][linkerd-faq] ve [Linkerd mimari][linkerd-architecture] belgeleri.

Bu makalede, Linkerd 'yi nasıl yükleyeceğiniz gösterilmektedir. Linkerd `linkerd` istemci ikilisi istemci makinenize yüklenir ve Linkerd bileşenleri AKS üzerinde bir Kubernetes kümesine yüklenir.

> [!NOTE]
> Bu yönergeler, Linkerd sürümüne `stable-2.6.0`başvurur.
>
> Linkerd `stable-2.6.x`, Kubernetes sürümleriyle `1.13+`çalıştırılabilir. [GitHub-Linkerd yayınlarına][linkerd-github-releases]ek kararlı ve kenar linkerd sürümlerini bulabilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Linkerd linkerd istemci ikilisini indirin ve yükleyin
> * AKS üzerinde Linkerd 'yi yükler
> * Linkerd yüklemesini doğrulama
> * Panoya erişin
> * AKS 'ten Linkerd 'yi kaldırma

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede açıklanan adımlarda, RBAC etkinleştirilmiş bir AKS kümesi (Kubernetes `1.13` ve üzeri) oluşturdunuz ve kümeyle `kubectl` bir bağlantı oluşturmuş olduğunuz varsayılır. Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, [aks hızlı başlangıç][aks-quickstart]bölümüne bakın.

Tüm Linkerd 'lerin Linux düğümlerinde çalışacak şekilde zamanlanması gerekir. Bu kurulum, aşağıda açıklanan yükleme yönteminde varsayılandır ve ek yapılandırma gerektirmez.

Bu makale, Linkerd yükleme kılavuzunu çeşitli ayrı adımlara ayırır. Sonuç, resmi Linkerd Başlarken [kılavuzundaki][linkerd-getting-started]yapıda aynıdır.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/linkerd/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/linkerd/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/linkerd/install-client-binary-windows.md)]

::: zone-end

## <a name="install-linkerd-on-aks"></a>AKS üzerinde Linkerd 'yi yükler

Linkerd 'yi yüklemeden önce, denetim düzlemi 'nin AKS kümenize yüklenip yüklenmediğini öğrenmek için yükleme öncesi denetimleri çalıştıracağız:

```console
linkerd check --pre
```

AKS kümenizin Linkerd için geçerli bir yükleme hedefi olduğunu göstermek için aşağıdakine benzer bir şey görmeniz gerekir:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

pre-kubernetes-setup
--------------------
√ control plane namespace does not already exist
√ can create Namespaces
√ can create ClusterRoles
√ can create ClusterRoleBindings
√ can create CustomResourceDefinitions
√ can create PodSecurityPolicies
√ can create ServiceAccounts
√ can create Services
√ can create Deployments
√ can create CronJobs
√ can create ConfigMaps
√ no clock skew detected

pre-kubernetes-capability
-------------------------
√ has NET_ADMIN capability
√ has NET_RAW capability

pre-linkerd-global-resources
----------------------------
√ no ClusterRoles exist
√ no ClusterRoleBindings exist
√ no CustomResourceDefinitions exist
√ no MutatingWebhookConfigurations exist
√ no ValidatingWebhookConfigurations exist
√ no PodSecurityPolicies exist

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

Status check results are √
```

Şimdi Linkerd bileşenlerini yüklemeye zaman atalım. AKS kümenize Linkerd bileşenlerini yüklemek için `linkerd` ve `kubectl` ikili dosyaları kullanın. `linkerd` bir ad alanı otomatik olarak oluşturulur ve bileşenler bu ad alanına yüklenir.

```console
linkerd install | kubectl apply -f -
```

Linkerd, bir dizi nesneyi dağıtır. Yukarıdaki `linkerd install` komutu çıktısından listeyi görürsünüz. Linkerd bileşenlerinin dağıtımı, küme ortamınıza bağlı olarak yaklaşık 1 dakika sürer.

Bu noktada, AKS kümenize Linkerd 'yi dağıttık. Linkerd 'nin başarılı bir dağıtımına sahip olduğunuzdan emin olmak için, [linkerd yüklemesini doğrulamak](#validate-the-linkerd-installation)üzere bir sonraki bölüme geçeceğiz.

## <a name="validate-the-linkerd-installation"></a>Linkerd yüklemesini doğrulama

Kaynakların başarıyla oluşturulduğunu doğrulayın. [Kubectl Get svc][kubectl-get] ve [kubectl Get Pod][kubectl-get] komutlarını kullanarak, linkerd bileşenlerinin `linkerd install` komutu tarafından yüklendiği `linkerd` ad alanını sorgulayın:

```console
kubectl get svc --namespace linkerd --output wide
kubectl get pod --namespace linkerd --output wide
```

Aşağıdaki örnek çıktı, şu anda çalışıyor olması gereken hizmetleri ve (Linux düğümlerinde zamanlanan) Hizmetleri gösterir:

```console
NAME                     TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE  SELECTOR
linkerd-controller-api   ClusterIP   10.0.110.67    <none>        8085/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-destination      ClusterIP   10.0.224.29    <none>        8086/TCP            66s  linkerd.io/control-plane-component=controller
linkerd-dst              ClusterIP   10.0.225.148   <none>        8086/TCP            66s  linkerd.io/control-plane-component=destination
linkerd-grafana          ClusterIP   10.0.61.124    <none>        3000/TCP            65s  linkerd.io/control-plane-component=grafana
linkerd-identity         ClusterIP   10.0.6.104     <none>        8080/TCP            67s  linkerd.io/control-plane-component=identity
linkerd-prometheus       ClusterIP   10.0.27.168    <none>        9090/TCP            65s  linkerd.io/control-plane-component=prometheus
linkerd-proxy-injector   ClusterIP   10.0.100.133   <none>        443/TCP             64s  linkerd.io/control-plane-component=proxy-injector
linkerd-sp-validator     ClusterIP   10.0.221.5     <none>        443/TCP             64s  linkerd.io/control-plane-component=sp-validator
linkerd-tap              ClusterIP   10.0.18.14     <none>        8088/TCP,443/TCP    64s  linkerd.io/control-plane-component=tap
linkerd-web              ClusterIP   10.0.37.108    <none>        8084/TCP,9994/TCP   66s  linkerd.io/control-plane-component=web

NAME                                      READY   STATUS    RESTARTS   AGE   IP            NODE                            NOMINATED NODE   READINESS GATES
linkerd-controller-66ddc9f94f-cm9kt       3/3     Running   0          66s   10.240.0.50   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-destination-c94bc454-qpkng        2/2     Running   0          66s   10.240.0.78   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-grafana-6868fdcb66-4cmq2          2/2     Running   0          65s   10.240.0.69   aks-linux-16165125-vmss000002   <none>           <none>
linkerd-identity-74d8df4b85-tqq8f         2/2     Running   0          66s   10.240.0.48   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-prometheus-699587cf8-k8ghg        2/2     Running   0          65s   10.240.0.41   aks-linux-16165125-vmss000001   <none>           <none>
linkerd-proxy-injector-6556447f64-n29wr   2/2     Running   0          64s   10.240.0.32   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-sp-validator-56745cd567-v4x7h     2/2     Running   0          64s   10.240.0.6    aks-linux-16165125-vmss000000   <none>           <none>
linkerd-tap-5cd9fc566-ct988               2/2     Running   0          64s   10.240.0.15   aks-linux-16165125-vmss000000   <none>           <none>
linkerd-web-774c79b6d5-dhhwf              2/2     Running   0          65s   10.240.0.70   aks-linux-16165125-vmss000002   <none>           <none>
```

Linkerd, Linkerd denetim düzlemi 'nin başarıyla yüklenip yapılandırıldığını doğrulamak için `linkerd` istemci ikili dosyası aracılığıyla bir komut sağlar.

```console
linkerd check
```

Yüklemenizin başarılı olduğunu belirtmek için aşağıdakine benzer bir şey görmeniz gerekir:

```console
kubernetes-api
--------------
√ can initialize the client
√ can query the Kubernetes API

kubernetes-version
------------------
√ is running the minimum Kubernetes API version
√ is running the minimum kubectl version

linkerd-config
--------------
√ control plane Namespace exists
√ control plane ClusterRoles exist
√ control plane ClusterRoleBindings exist
√ control plane ServiceAccounts exist
√ control plane CustomResourceDefinitions exist
√ control plane MutatingWebhookConfigurations exist
√ control plane ValidatingWebhookConfigurations exist
√ control plane PodSecurityPolicies exist

linkerd-existence
-----------------
√ 'linkerd-config' config map exists
√ heartbeat ServiceAccount exist
√ control plane replica sets are ready
√ no unschedulable pods
√ controller pod is running
√ can initialize the client
√ can query the control plane API

linkerd-api
-----------
√ control plane pods are ready
√ control plane self-check
√ [kubernetes] control plane can talk to Kubernetes
√ [prometheus] control plane can talk to Prometheus
√ no invalid service profiles

linkerd-version
---------------
√ can determine the latest version
√ cli is up-to-date

control-plane-version
---------------------
√ control plane is up-to-date
√ control plane and cli versions match

Status check results are √
```

## <a name="access-the-dashboard"></a>Panoya erişin

Linkerd, hizmet kafesi ve iş yüklerine ilişkin Öngörüler sağlayan bir panoyla birlikte gelir. Panoya erişmek için `linkerd dashboard` komutunu kullanın. Bu komut, [kubectl bağlantı noktasından][kubectl-port-forward] yararlanarak, istemci makineniz ile aks kümenizdeki ilgili Pod arasında güvenli bir bağlantı oluşturur. Daha sonra panoyu varsayılan tarayıcınızda otomatik olarak açar.

```console
linkerd dashboard
```

Bu komut ayrıca bir bağlantı noktası İleri oluşturacak ve Grafana panoları için bir bağlantı döndürüyor.

```console
Linkerd dashboard available at:
http://127.0.0.1:50750
Grafana dashboard available at:
http://127.0.0.1:50750/grafana
Opening Linkerd dashboard in the default browser
```

## <a name="uninstall-linkerd-from-aks"></a>AKS 'ten Linkerd 'yi kaldırma

> [!WARNING]
> Çalışan bir sistemden Linkerd silme, hizmetleriniz arasında trafik ile ilgili sorunlar oluşmasına neden olabilir. Devam etmeden önce, sisteminizin Linkerd olmadan hala düzgün şekilde çalışmaya yönelik bir sağlama gerçekleştirdiğinizden emin olun.

İlk olarak, veri düzlemi proxy 'lerini kaldırmanız gerekecektir. İş yükü ad alanlarından otomatik ara sunucu ekleme [ek açıklamalarını][linkerd-automatic-proxy-injection] kaldırın ve iş yükü dağıtımlarınızı kullanıma alın. İş yükleriniz artık ilişkili hiçbir veri düzlemi bileşenine sahip olmamalıdır.

Son olarak, denetim düzlemi 'ni aşağıdaki gibi kaldırın:

```console
linkerd install --ignore-cluster | kubectl delete -f -
```

## <a name="next-steps"></a>Sonraki adımlar

Linkerd için daha fazla yükleme ve yapılandırma seçeneği araştırmak için aşağıdaki resmi Linkerd yönergelerine bakın:

- [Linkerd-Helm yüklemesi][linkerd-install-with-helm]
- [Rol ayrıcalıkları için karşılamak 'ya linkerd-çok aşamalı yükleme][linkerd-multi-stage-installation]

Ayrıca şunları kullanarak ek senaryolar izleyebilirsiniz:

- [Linkerd emojivoto tanıtımı][linkerd-demo-emojivoto]
- [Linkerd kitaplar tanıtımı][linkerd-demo-books]

<!-- LINKS - external -->

[linkerd]: https://linkerd.io/
[linkerd-cncf]: https://landscape.cncf.io/selected=linkerd
[linkerd-faq]: https://linkerd.io/2/faq/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-getting-started]: https://linkerd.io/2/getting-started/
[linkerd-overview]: https://linkerd.io/2/overview/
[linkerd-github]: https://github.com/linkerd/linkerd2
[linkerd-github-releases]: https://github.com/linkerd/linkerd2/releases/

[linkerd-install-with-helm]: https://linkerd.io/2/tasks/install-helm/
[linkerd-multi-stage-installation]: https://linkerd.io/2/tasks/install/#multi-stage-install
[linkerd-automatic-proxy-injection]: https://linkerd.io/2/features/proxy-injection/

[linkerd-demo-emojivoto]: https://linkerd.io/2/getting-started/#step-5-install-the-demo-app
[linkerd-demo-books]: https://linkerd.io/2/tasks/books/

[helm]: https://helm.sh

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
