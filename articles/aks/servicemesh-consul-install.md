---
title: Azure Kubernetes Service (AKS) ' de Tüketil 'yi kurma
description: Azure Kubernetes Service (AKS) kümesinde hizmet ağı oluşturmak için Tüketil 'yi yüklemeyi ve kullanmayı öğrenin
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d5d0a575c3fb662df034b66a48135ac33393f95c
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885401"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de Tüketil 'ı yükleyip kullanma

[Tüketil][consul-github] , bir Kubernetes kümesindeki mikro hizmetlerde anahtar bir işlevsellik kümesi sağlayan açık kaynaklı bir hizmet kafesidir. Bu özellikler hizmet bulma, sistem durumu denetimi, hizmet segmentleme ve Observability içerir. Tüketil hakkında daha fazla bilgi için resmi [nedir?][consul-docs-concepts] belgelerine bakın.

Bu makalede, Tüketil 'nin nasıl yükleneceği gösterilmektedir. Tüketil bileşenleri AKS 'teki bir Kubernetes kümesine yüklenir.

> [!NOTE]
> Bu yönergeler, Tüketil sürüm `1.6.0`referans ve en az Helm sürüm `2.14.2`kullanır.
>
> Tüketil `1.6.x` sürümleri, Kubernetes sürümleriyle `1.13+`karşı çalıştırılabilir. [GitHub-Tüketil yayınlarına][consul-github-releases] ek tüketil sürümleri ve her bir yayın hakkında bilgi edinmek için, her zaman [Tüketim l-sürüm notları][consul-release-notes]bulabilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * AKS 'e Tüketil bileşenlerini yükler
> * Tüketil yüklemesini doğrulama
> * AKS 'ten Tüketil 'yi kaldırma

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede açıklanan adımlarda, RBAC etkinleştirilmiş bir AKS kümesi (Kubernetes `1.13` ve üzeri) oluşturdunuz ve kümeyle `kubectl` bir bağlantı oluşturmuş olduğunuz varsayılır. Bu öğelerin herhangi biriyle ilgili yardıma ihtiyacınız varsa, [aks hızlı başlangıç][aks-quickstart]bölümüne bakın. Kümenizin Linux düğüm havuzunda en az 3 düğüm olduğundan emin olun.

Bu yönergeleri izlemek ve Tüketil 'yi yüklemek için [Helm][helm] gerekir. En son kararlı sürümü kümenize doğru yüklenip yapılandırılmış olmalıdır. Held 'yi yüklemeyle ilgili yardıma ihtiyacınız varsa bkz. [aks helk Yükleme Kılavuzu][helm-install]. Tüm Tüketil 'lerin da Linux düğümlerinde çalışacak şekilde zamanlanması gerekir.

Bu makale, Tüketil yükleme kılavuzunu çeşitli ayrı adımlara ayırır. Nihai sonuç, resmi sarf l yükleme [kılavuzundaki][consul-install-k8]yapıda aynıdır.

### <a name="install-the-consul-components-on-aks"></a>AKS 'e Tüketil bileşenlerini yükler

Tüketil Helm grafiğinin sürüm `v0.10.0` indirerek başlayacağız. Grafiğin bu sürümü, `1.6.0`Tüketil sürümünü içerir.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

AKS kümenizdeki `consul` ad alanına tüketim l bileşenlerini yüklemek için Helm ve indirilen `consul-helm` grafiğini kullanın. 

> [!NOTE]
> **Yükleme seçenekleri**
> 
> Yüklememizin kapsamında aşağıdaki seçenekleri kullanıyoruz:
> - `connectInject.enabled=true`-ara sunucu 'ların Pod 'ye eklenmesi
> - `client.enabled=true`-Tüketil istemcilerini her düğümde çalışacak şekilde etkinleştirin
> - `client.grpc=true`-Connectınject için gRPC dinleyicisini etkinleştirin
> - `syncCatalog.enabled=true`-Kubernetes ve Tüketil hizmetlerini eşitleme
>
> **Düğüm seçicileri**
>
> Tüketil 'nin şu anda Linux düğümlerinde çalışacak şekilde zamanlanması gerekir. Kümenizde Windows Server düğümleri varsa, Tüketil 'lerin yalnızca Linux düğümlerinde çalışacak şekilde zamanlandığından emin olmanız gerekir. Düğümlerin doğru düğümlere zamanlandığından emin olmak için [düğüm seçicileri][kubernetes-node-selectors] kullanacağız.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

`Consul` Held grafiği bir dizi nesne dağıtır. Listeyi yukarıdaki `helm install` komutunun çıktısından görebilirsiniz. Tüketil bileşenlerinin dağıtımı, küme ortamınıza bağlı olarak 3 dakika kadar sürebilir.

Bu noktada, AKS kümenize Tüketil 'yi dağıttık. Tüketil 'nin başarılı bir şekilde dağıtımına sahip olduğunuzdan emin olmak için, tüketim l yüklemesini doğrulamak üzere bir sonraki bölüme geçeceğiz.

## <a name="validate-the-consul-installation"></a>Tüketil yüklemesini doğrulama

Kaynakların başarıyla oluşturulduğunu doğrulayın. `consul` ad alanını sorgulamak için [kubectl Get svc][kubectl-get] ve [kubectl al][kubectl-get] komutunu kullanın, burada, tüketil bileşenleri `helm install` komutu tarafından yüklenir:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

Aşağıdaki örnek çıktı, şu anda çalışıyor olması gereken hizmetleri ve (Linux düğümlerinde zamanlanan) Hizmetleri gösterir:

```console
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Tüm FID 'ler `Running`durumunu göstermelidir. Ayırımlarınızın bu durumları yoksa, tamamlanana kadar bir dakika veya iki tane bekleyin. Herhangi bir pod bir sorun bildirirse, çıktısını ve durumlarını gözden geçirmek için [kubectl 'yi bir pod betimleyen][kubectl-describe] komutunu kullanın.

## <a name="accessing-the-consul-ui"></a>Tüketil Kullanıcı arabirimine erişme

Tüketimizdeki Kullanıcı arabirimi, yukarıdaki kurulumla yüklendi ve Tüketil için Kullanıcı arabirimi tabanlı yapılandırma sağlar. Tüketil için Kullanıcı arabirimi, bir dış IP adresi aracılığıyla herkese açık bir şekilde gösterilmez. Tüketil Kullanıcı arabirimine erişmek için [kubectl Port-Forward][kubectl-port-forward] komutunu kullanın. Bu komut, istemci makineniz ile AKS kümenizdeki ilgili Pod arasında güvenli bir bağlantı oluşturur.

```azurecli
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Artık bir tarayıcı açabilir ve `http://localhost:8080/ui`, Tüketil Kullanıcı arabirimini açmak için bu uygulamayı bırakabilirsiniz. Kullanıcı arabirimini açtığınızda şunları görmeniz gerekir:

![Tüketil Kullanıcı arabirimi](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>AKS 'ten Tüketil 'yi kaldırma

> [!WARNING]
> Çalışan bir sistemden Tüketil 'nin silinmesi, hizmetleriniz arasında trafik ile ilgili sorunlar oluşmasına neden olabilir. Devam etmeden önce sisteminizin Tüketil olmadan doğru şekilde çalışmaya yönelik bir sağlama gerçekleştirdiğinizden emin olun.

### <a name="remove-consul-components-and-namespace"></a>Tüketil bileşenlerini ve ad alanını kaldır

AKS kümenizdeki Tüketil 'yi kaldırmak için aşağıdaki komutları kullanın. `helm delete` komutları `consul` grafiği kaldırır ve `kubectl delete namespace` komutu `consul` ad alanını kaldırır.

```azurecli
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>Sonraki adımlar

Tüketil için daha fazla yükleme ve yapılandırma seçeneği araştırmak için aşağıdaki resmi tüketim ve makaleleri inceleyin:

- [Tüketil-Helm Yükleme Kılavuzu][consul-install-k8]
- [Tüketil-Helm yükleme seçenekleri][consul-install-helm-options]

Ayrıca şunları kullanarak ek senaryolar izleyebilirsiniz:

- [Tüketil örnek uygulaması][consul-app-example]

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
