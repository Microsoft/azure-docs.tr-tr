---
title: Azure Kubernetes Service (AKS) içinde bir iç ağ için giriş denetleyicisi oluşturma
description: Azure Kubernetes Service (AKS) kümesinde iç, özel ağ için bir NGıNX giriş denetleyicisi yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 935b96bd553c9ae73b55086483baa0ea7c4aeaa4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615461"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde iç sanal ağa giriş denetleyicisi oluşturma

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Bu makalede bir Azure Kubernetes Service (AKS) kümesinde [NGINX giriş denetleyicisinin][nginx-ingress] nasıl dağıtılacağı gösterilir. Giriş denetleyicisi bir iç, özel sanal ağ ve IP adresi üzerinde yapılandırılır. Dış erişime izin verilmez. Her biri tek IP adresi üzerinden erişilebilen AKS kümesinde iki uygulama çalıştırılır.

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
- [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, NGıNX giriş denetleyicisini, CERT-Manager 'ı ve örnek bir Web uygulamasını yüklemek için Held kullanılmaktadır. AKS kümenizde helk başlatılmış ve Tiller için bir hizmet hesabı kullanmanız gerekir. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Giriş denetleyicisi oluşturma

Varsayılan olarak, bir NGıNX giriş denetleyicisi, dinamik bir genel IP adresi atamasıyla oluşturulur. Ortak bir yapılandırma gereksinimi, iç, özel ağ ve IP adresi kullanmaktır. Bu yaklaşım, hizmetlere erişimi dış erişim olmadan, iç kullanıcılarla kısıtlamanıza olanak sağlar.

Aşağıdaki örnek bildirim dosyasını kullanarak *iç ıningress. YAML* adlı bir dosya oluşturun. Bu örnek, *Loadbalancerıp* kaynağına *10.240.0.42* atar. Giriş denetleyicisi ile kullanmak için kendi iç IP adresinizi sağlayın. Bu IP adresinin sanal ağınızda zaten kullanımda olmadığından emin olun.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Şimdi *NGINX-ingress* grafiğini Held ile dağıtın. Önceki adımda oluşturulan bildirim dosyasını kullanmak için `-f internal-ingress.yaml` parametresini ekleyin. Ek artıklık için, NGINX giriş denetleyicilerinin iki yinelemesi `--set controller.replicaCount` parametresiyle dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

Giriş denetleyicisinin da bir Linux düğümünde zamanlanması gerekir. Windows Server düğümleri (Şu anda AKS 'deki önizlemede) giriş denetleyicisini çalıştırmamalıdır. Kubernetes Scheduler ' `--set nodeSelector` ın Linux tabanlı bir düğümde NGINX giriş denetleyicisini çalıştırmasını bildirmek için parametresi kullanılarak bir düğüm seçici belirtilir.

> [!TIP]
> Aşağıdaki örnek, *Giriş-Basic*adlı giriş kaynakları için bir Kubernetes ad alanı oluşturur. Gerektiğinde kendi ortamınız için bir ad alanı belirtin. Aks kümeniz RBAC etkinleştirilmemişse, helk komutlarına ekleyin `--set rbac.create=false` .

> [!TIP]
> Kümenizdeki kapsayıcılara yönelik [istemci kaynak IP korumasını][client-source-ip] etkinleştirmek istiyorsanız, Helm install komutuna ekleyin `--set controller.service.externalTrafficPolicy=Local` . İstemci kaynak IP 'si, *Için X-iletilen-için*istek üstbilgisinde depolanır. İstemci kaynak IP koruması etkinken bir giriş denetleyicisi kullanılırken, SSL geçişi çalışmaz.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Kubernetes yük dengeleyici hizmeti NGıNX giriş denetleyicisi için oluşturulduğunda, aşağıdaki örnek çıktıda gösterildiği gibi iç IP adresiniz atanır:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Henüz giriş kuralı oluşturulmadı, bu nedenle iç IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir. Giriş kuralları aşağıdaki adımlarda yapılandırılır.

## <a name="run-demo-applications"></a>Demo uygulamalarını çalıştırma

Giriş denetleyicisini çalışır durumda görmek için AKS kümenizde iki tanıtım uygulaması çalıştıralım. Bu örnekte, HELI basit bir ' Hello World ' uygulamasının iki örneğini dağıtmak için kullanılır.

Örnek HELI grafiklerini yükleyebilmeniz için önce aşağıdaki gibi, Azure Samples Repository 'yi Helu ortamınıza ekleyin:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Aşağıdaki komutla bir Held grafiğinden ilk demo uygulamayı oluşturun:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Şimdi tanıtım uygulamasının ikinci bir örneğini yükleyin. İkinci örnek için, iki uygulamanın görsel olarak benzersiz olması için yeni bir başlık belirtirsiniz. Ayrıca, benzersiz bir hizmet adı da belirtirsiniz:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Giriş yolu oluşturma

Her iki uygulama da artık Kubernetes kümenizde çalışıyor. Trafiği her bir uygulamaya yönlendirmek için bir Kubernetes giriş kaynağı oluşturun. Giriş kaynağı, trafiği iki uygulamadan birine yönlendiren kuralları yapılandırır.

Aşağıdaki örnekte, adrese `http://10.240.0.42/` olan trafik adlı `aks-helloworld`hizmete yönlendirilir. Adrese `http://10.240.0.42/hello-world-two` giden trafik `ingress-demo` hizmete yönlendirilir.

Adlı `hello-world-ingress.yaml` bir dosya oluşturun ve aşağıdaki örnekteki YAML 'yi kopyalayın.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

`kubectl apply -f hello-world-ingress.yaml` Komutunu kullanarak giriş kaynağını oluşturun.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Giriş denetleyicisini test etme

Giriş denetleyicisinin yollarını test etmek için, bir Web istemcisi ile iki uygulamaya gidin. Gerekirse, AKS kümesindeki bir pod 'dan yalnızca iç bu işlevselliği hızlıca test edebilirsiniz. Bir test Pod oluşturun ve buna bir terminal oturumu ekleyin:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Şunu `curl` kullanarak`apt-get`Pod 'a yüklensin:

```console
apt-get update && apt-get install -y curl
```

Şimdi, `curl` *http://10.240.0.42* kullanarak Kubernetes giriş denetleyicinizin adresine erişin. Bu makalenin ilk adımında giriş denetleyicisi 'ni dağıtırken belirtilen kendi iç IP adresini belirtin.

```console
curl -L http://10.240.0.42
```

Adresle birlikte ek bir yol sağlanmadı, bu nedenle giriş denetleyicisi */* rotayı varsayılan olarak alır. İlk demo uygulaması aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi döndürülür:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Şimdi adrese */Hello-World-iki* yolu ekleyin, örneğin *http://10.240.0.42/hello-world-two* . Aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi, özel başlığa sahip ikinci demo uygulaması döndürülür:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede giriş bileşenlerini ve örnek uygulamaları yüklemek için Held kullanılmaktadır. Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetler içerir. Bu kaynakları temizlemek için, tüm örnek ad alanını veya ayrı kaynakları silebilirsiniz.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Örnek ad alanını ve tüm kaynakları Sil

Tüm örnek ad alanını silmek için `kubectl delete` komutunu kullanın ve ad alanı adınızı belirtin. Ad alanındaki tüm kaynaklar silinir.

```console
kubectl delete namespace ingress-basic
```

Ardından, AKS Hello World uygulamasının Held deposunu kaldırın:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Kaynakları tek tek Sil

Alternatif olarak, oluşturulan kaynakları tek tek silmek daha ayrıntılı bir yaklaşımdır. `helm list` Komut ile Held sürümlerini listeleyin. Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX-ingress* ve *aks-HelloWorld*adlı grafikleri arayın:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

`helm delete` Komutuyla sürümleri silin. Aşağıdaki örnekte NGıNX giriş dağıtımı ve iki örnek AKS Hello World Apps de silinir.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Ardından, AKS Hello World uygulamasının Held deposunu kaldırın:

```console
helm repo remove azure-samples
```

Trafiği örnek uygulamalara yönlendiren giriş yolunu kaldırın:

```console
kubectl delete -f hello-world-ingress.yaml
```

Son olarak, kendi ad alanını silebilirsiniz. `kubectl delete` Komutunu kullanın ve ad alanı adınızı belirtin:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bazı dış bileşenler AKS 'ye eklenmiştir. Bu bileşenler hakkında daha fazla bilgi edinmek için aşağıdaki proje sayfalarına bakın:

- [Held CLı][helm-cli]
- [NGıNX giriş denetleyicisi][nginx-ingress]

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [Dinamik bir genel IP ile giriş denetleyicisi oluşturun ve otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi yapılandırın][aks-ingress-tls]
- [Statik bir genel IP adresi ile giriş denetleyicisi oluşturun ve otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi yapılandırın][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers