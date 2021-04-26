---
title: İç ağda giriş denetleyicisi
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) kümesinde iç, özel ağ için bir NGıNX giriş denetleyicisi yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 3201f510db9970b7db548ee6a3348fa68d278248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104601474"
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

Bu makalede NGıNX giriş denetleyicisini yüklemek için [Held 3][helm] kullanılmaktadır. Held 'nin en son sürümünü kullandığınızdan ve giriş *-NGINX* Held deposuna erişiminizin olduğundan emin olun. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Giriş denetleyicisi oluşturma

Varsayılan olarak, bir NGıNX giriş denetleyicisi, dinamik bir genel IP adresi atamasıyla oluşturulur. Ortak bir yapılandırma gereksinimi, iç, özel ağ ve IP adresi kullanmaktır. Bu yaklaşım, hizmetlere erişimi dış erişim olmadan, iç kullanıcılarla kısıtlamanıza olanak sağlar.

Aşağıdaki örnek bildirim dosyasını kullanarak *iç ıningress. YAML* adlı bir dosya oluşturun. Bu örnek, *Loadbalancerıp* kaynağına *10.240.0.42* atar. Giriş denetleyicisi ile kullanmak için kendi iç IP adresinizi sağlayın. Bu IP adresinin sanal ağınızda zaten kullanımda olmadığından emin olun. Ayrıca, var olan bir sanal ağ ve alt ağ kullanıyorsanız, sanal ağı ve alt ağı yönetmek için AKS kümenizi doğru izinlerle yapılandırmanız gerekir. Daha fazla bilgi için bkz. [Azure Kubernetes Service (AKS) IÇINDEKI IP adresi aralıklarınız ile Kubernetes kullanan ağı kullanma][aks-configure-kubenet-networking] veya Azure [Kubernetes HIZMETI (aks) içindeki Azure CNI ağını yapılandırma][aks-configure-advanced-networking] .

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Şimdi *NGINX-ingress* grafiğini Held ile dağıtın. Önceki adımda oluşturulan bildirim dosyasını kullanmak için `-f internal-ingress.yaml` parametresini ekleyin. Daha fazla yedeklilik sağlamak için `--set controller.replicaCount` parametresiyle iki NGINX giriş denetleyicisi çoğaltması dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

Ayrıca giriş denetleyicisinin bir Linux düğümü üzerinde zamanlanması gerekir. Giriş denetleyicisi, Windows Server düğümlerinde çalıştırılmamalıdır. Kubernetes zamanlayıcısına NGINX giriş denetleyicisini Linux tabanlı bir düğümde çalıştırmasını söylemek için `--set nodeSelector` parametresi kullanılarak bir düğüm seçici belirtilir.

> [!TIP]
> Aşağıdaki örnek, *Giriş-Basic* adlı giriş kaynakları için bir Kubernetes ad alanı oluşturur. Gerektiğinde kendi ortamınız için bir ad alanı belirtin. AKS kümeniz Kubernetes RBAC etkin değilse, `--set rbac.create=false` helk komutlarına ekleyin.

> [!TIP]
> Kümenizdeki kapsayıcılara yönelik [istemci kaynak IP korumasını][client-source-ip] etkinleştirmek Istiyorsanız, `--set controller.service.externalTrafficPolicy=Local` Helm install komutuna ekleyin. İstemci kaynak IP 'si, *Için X-iletilen-için* istek üstbilgisinde depolanır. İstemci kaynak IP koruması etkinken bir giriş denetleyicisi kullanılırken, TLS geçişi çalışmaz.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Kubernetes yük dengeleyici hizmeti NGıNX giriş denetleyicisi için oluşturulduğunda, iç IP adresiniz atanır. Genel IP adresini almak için `kubectl get service` komutunu kullanın.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Aşağıdaki örnek çıktıda gösterildiği gibi, IP adresinin hizmete atanması birkaç dakika sürer:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Henüz giriş kuralı oluşturulmadı, bu nedenle iç IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir. Giriş kuralları aşağıdaki adımlarda yapılandırılır.

## <a name="run-demo-applications"></a>Demo uygulamalarını çalıştırma

Giriş denetleyicisini çalışır durumda görmek için AKS kümenizde iki tanıtım uygulaması çalıştırın. Bu örnekte, `kubectl apply` basit bir *Hello World* uygulamasının iki örneğini dağıtmak için kullanırsınız.

Bir *aks-HelloWorld. YAML* dosyası oluşturun ve aşağıdaki örnekte bulunan YAML 'yi kopyalayın:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Bir *ınress-demo. YAML* dosyası oluşturun ve aşağıdaki örnekte bulunan YAML 'yi kopyalayın:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Kullanarak iki demo uygulamayı çalıştırın `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Giriş yolu oluşturma

Her iki uygulama da artık Kubernetes kümenizde çalışıyor. Trafiği her bir uygulamaya yönlendirmek için bir Kubernetes giriş kaynağı oluşturun. Giriş kaynağı, trafiği iki uygulamadan birine yönlendiren kuralları yapılandırır.

Aşağıdaki örnekte, adrese `http://10.240.0.42/` olan trafik adlı hizmete yönlendirilir `aks-helloworld` . Adrese giden trafik `http://10.240.0.42/hello-world-two` `ingress-demo` hizmete yönlendirilir.

Adlı bir dosya oluşturun `hello-world-ingress.yaml` ve aşağıdaki örnekteki YAML 'yi kopyalayın.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Komutunu kullanarak giriş kaynağını oluşturun `kubectl apply -f hello-world-ingress.yaml` .

```console
kubectl apply -f hello-world-ingress.yaml
```

Aşağıdaki örnek çıktı, giriş kaynağının oluşturulduğunu gösterir.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Giriş denetleyicisini test etme

Giriş denetleyicisinin yollarını test etmek için, bir Web istemcisi ile iki uygulamaya gidin. Gerekirse, AKS kümesindeki bir pod 'dan yalnızca iç bu işlevselliği hızlıca test edebilirsiniz. Bir test Pod oluşturun ve buna bir terminal oturumu ekleyin:

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

Şunu `curl` kullanarak Pod 'a yüklensin `apt-get` :

```console
apt-get update && apt-get install -y curl
```

Şimdi, kullanarak Kubernetes giriş denetleyicinizin adresine erişin `curl` *http://10.240.0.42* . Bu makalenin ilk adımında giriş denetleyicisi 'ni dağıtırken belirtilen kendi iç IP adresini belirtin.

```console
curl -L http://10.240.0.42
```

Adresle birlikte ek bir yol sağlanmadı, bu nedenle giriş denetleyicisi rotayı varsayılan olarak alır */* . İlk demo uygulaması aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi döndürülür:

```
$ curl -L http://10.240.0.42

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

Bu makalede giriş bileşenlerini yüklemek için Held kullanılmaktadır. Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetler içerir. Bu kaynakları temizlemek için, tüm örnek ad alanını veya ayrı kaynakları silebilirsiniz.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Örnek ad alanını ve tüm kaynakları Sil

Tüm örnek ad alanını silmek için `kubectl delete` komutunu kullanın ve ad alanı adınızı belirtin. Ad alanındaki tüm kaynaklar silinir.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Kaynakları tek tek Sil

Alternatif olarak, oluşturulan kaynakları tek tek silmek daha ayrıntılı bir yaklaşımdır. Komut ile Held sürümlerini listeleyin `helm list` . 

```console
helm list --namespace ingress-basic
```

Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX-ingress* ve *aks-HelloWorld* adlı grafikleri arayın:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Komutuyla yayınları kaldırın `helm uninstall` .

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

Aşağıdaki örnek NGıNX giriş dağıtımını kaldırır.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Sonra, iki örnek uygulamayı kaldırın:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Trafiği örnek uygulamalara yönlendiren giriş yolunu kaldırın:

```console
kubectl delete -f hello-world-ingress.yaml
```

Son olarak, kendi ad alanını silebilirsiniz. Komutunu kullanın `kubectl delete` ve ad alanı adınızı belirtin:

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
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
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
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md