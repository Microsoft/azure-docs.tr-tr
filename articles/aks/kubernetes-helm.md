---
title: Azure 'da Kubernetes 'te Held ile kapsayıcılar dağıtma
description: Azure Kubernetes Service (AKS) kümesinde kapsayıcılar dağıtmak için Helmpaketleme aracının nasıl kullanılacağını öğrenin
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 05/23/2019
ms.author: zarhoads
ms.openlocfilehash: fc808fee66dee573aecd423e375d30bf3f5b696a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170703"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) ' de Held ile uygulama yükler

[Helk][helm] , Kubernetes uygulamalarının yaşam döngüsünü yüklemenize ve yönetmenize yardımcı olan bir açık kaynaklı paketleme aracıdır. *Apt* ve *yum*gibi Linux paket yöneticilerine benzer şekilde, Helm, önceden yapılandırılmış Kubernetes kaynakları paketleri olan Kubernetes grafiklerini yönetmek için kullanılır.

Bu makalede, AKS 'teki bir Kubernetes kümesinde Held 'yi yapılandırma ve kullanma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Ayrıca, geliştirme sisteminizde çalışan istemci olan Held CLı 'nin yüklü olması gerekir. Bu sayede, Held ile uygulamaları başlatabilir, durdurabilir ve yönetebilirsiniz. Azure Cloud Shell kullanıyorsanız, HELI CLı zaten yüklüdür. Yerel platformunuzun yükleme yönergeleri için bkz. [Held 'Yi yükleme][helm-install].

> [!IMPORTANT]
> Held, Linux düğümlerinde çalışmak üzere tasarlanmıştır. Kümenizde Windows Server düğümleri varsa, Held Pod 'nin yalnızca Linux düğümlerinde çalışacak şekilde zamanlandığından emin olmanız gerekir. Ayrıca yüklediğiniz herhangi bir Held grafiğinin da doğru düğümlerde çalışacak şekilde zamanlandığından emin olmanız gerekir. Bu makaledeki komutlar, düğümlerin doğru düğümlere zamanlandığından emin olmak için [düğüm seçicileri][k8s-node-selector] kullanır, ancak tüm HELI grafikleri bir düğüm seçici kullanıma sunmayabilir. Ayrıca, kümenizde yer alan diğer seçenekleri de kullanabilirsiniz (örneğin, [litre][taints]).

## <a name="create-a-service-account"></a>Hizmet hesabı oluşturma

Helm 'yi RBAC özellikli bir AKS kümesinde dağıtabilmeniz için, Tiller hizmeti için bir hizmet hesabı ve rol bağlaması olması gerekir. RBAC etkin bir kümede Held/Tiller güvenliğini sağlama hakkında daha fazla bilgi için bkz. [Tiller, namespaces ve RBAC][tiller-rbac]. AKS kümeniz RBAC etkinleştirilmemişse, bu adımı atlayın.

`helm-rbac.yaml` adlı bir dosya oluşturun ve aşağıdaki YAML 'de kopyalayın:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

`kubectl apply` komutuyla hizmet hesabı ve rol bağlama oluşturun:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>Güvenli Tiller ve Held

Helk istemcisi ve Tiller hizmeti, TLS/SSL kullanarak birbirleriyle kimlik doğrular ve birbirleriyle iletişim kurar. Bu kimlik doğrulama yöntemi, Kubernetes kümesinin güvenliğini sağlamaya ve hangi hizmetlerin dağıtılabileceklerini kolaylaştırır. Güvenliği artırmak için, kendi imzalı sertifikalarınızı oluşturabilirsiniz. Her helk Kullanıcı kendi istemci sertifikasını alır ve Tiller sertifikalar uygulanmış Kubernetes kümesinde başlatılır. Daha fazla bilgi için bkz. [helk ve Tiller arasında TLS/SSL kullanma][helm-ssl].

RBAC özellikli bir Kubernetes kümesi ile, Tiller erişim düzeyini kümeye göre kontrol edebilirsiniz. Tiller ' nin dağıtıldığı Kubernetes ad alanını tanımlayabilir ve Tiller ad alanlarını daha sonra kaynaklarını dağıtabileceğini kısıtlayabilirsiniz. Bu yaklaşım, farklı ad alanlarında Tiller örnekleri oluşturmanıza ve dağıtım sınırlarını sınırlandırmanıza ve hele istemcisi kullanıcılarını belirli ad alanlarına aktarmanıza olanak tanır. Daha fazla bilgi için bkz. [Helm rol tabanlı erişim denetimleri][helm-rbac].

## <a name="configure-helm"></a>Held 'yi yapılandırma

Bir AKS kümesine temel bir Tiller dağıtmak için [helk init][helm-init] komutunu kullanın. Kümeniz RBAC etkinleştirilmemişse, `--service-account` bağımsız değişkenini ve değerini kaldırın. Aşağıdaki örneklerde Ayrıca [History-Max][helm-history-max] değeri 200 olarak ayarlanır.

Tiller ve Held için TLS/SSL yapılandırdıysanız, bu temel başlatma adımını atlayın ve bunun yerine, sonraki örnekte gösterildiği gibi gerekli `--tiller-tls-` sağlayın.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Helk ve Tiller arasında TLS/SSL yapılandırdıysanız, aşağıdaki örnekte gösterildiği gibi, kendi sertifikalarınızın `--tiller-tls-*` parametrelerini ve adlarını sağlayın:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

## <a name="find-helm-charts"></a>Held grafiklerini bulma

Hele grafikleri, uygulamaları bir Kubernetes kümesine dağıtmak için kullanılır. Önceden oluşturulmuş Held grafiklerini aramak için [hele Search][helm-search] komutunu kullanın:

```console
helm search
```

Aşağıdaki sıkıştırılmış örnek çıktıda, kullanılabilecek bazı HELI grafikleri gösterilmektedir:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

Grafik listesini güncelleştirmek için [helmrepo Update][helm-repo-update] komutunu kullanın. Aşağıdaki örnekte başarılı bir depo güncelleştirmesi gösterilmektedir:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

## <a name="run-helm-charts"></a>Held grafiklerini çalıştırma

Held ile grafik yüklemek için [HELI install][helm-install] komutunu kullanın ve yüklenecek grafiğin adını belirtin. Bir Helm grafiğinin yükleme işlemini görmek için Helm grafiğini kullanarak temel bir NGINX dağıtımı yükleyelim. TLS/SSL yapılandırdıysanız, hele İstemci sertifikanızı kullanmak için `--tls` parametresini ekleyin.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Aşağıdaki sıkıştırılmış örnek çıktısı, helk grafiği tarafından oluşturulan Kubernetes kaynaklarının dağıtım durumunu gösterir:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

NGINX-ingress-Controller hizmetinin *dış IP* adresinin doldurulması ve bir Web tarayıcısı ile erişmenize izin vermek için bir dakika veya iki zaman alır.

## <a name="list-helm-releases"></a>Held yayınları listeleme

Kümenizde yüklü olan sürümlerin listesini görmek için [HELI List][helm-list] komutunu kullanın. Aşağıdaki örnekte, önceki adımda dağıtılan NGINX-ingress sürümü gösterilmektedir. TLS/SSL yapılandırdıysanız, hele İstemci sertifikanızı kullanmak için `--tls` parametresini ekleyin.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetleri içerir. Bu kaynakları temizlemek için, `helm delete` komutunu kullanın ve önceki `helm list` komutunda bulunan sürüm adınızı belirtin. Aşağıdaki örnek, *flamı-Alpaca*adlı yayını siler:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>Sonraki adımlar

Held ile Kubernetes uygulama dağıtımlarını yönetme hakkında daha fazla bilgi için helk belgelerine bakın.

> [!div class="nextstepaction"]
> [Hela belgeleri][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://v2.helm.sh/docs/
[helm-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm-install]: https://v2.helm.sh/docs/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
