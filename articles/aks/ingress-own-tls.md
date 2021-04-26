---
title: Inress için TLS sertifikalarınızı kullanma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) kümesinde kendi sertifikalarınızı kullanan bir NGıNX giriş denetleyicisi yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: d3d554fbbbbb16910b76e7eb56afd13c436f8cf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779948"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service'te (AKS) HTTPS giriş denetleyicisi oluşturma ve kendi TLS sertifikalarınızı kullanma

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Bu makalede bir Azure Kubernetes Service (AKS) kümesinde [NGINX giriş denetleyicisinin][nginx-ingress] nasıl dağıtılacağı gösterilir. Kendi sertifikalarınızı oluşturur ve giriş rotası ile kullanmak üzere bir Kubernetes gizli dizisi oluşturun. Son olarak, her biri tek bir IP adresi üzerinden erişilebilen AKS kümesinde iki uygulama çalıştırılır.

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede NGıNX giriş denetleyicisini yüklemek için [Held 3][helm] kullanılmaktadır. Held 'nin en son sürümünü kullandığınızdan ve giriş *-NGINX* Held deposuna erişiminizin olduğundan emin olun. Yükseltme yönergeleri için, bkz. [hela Install docs][helm-install]. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Giriş denetleyicisi oluşturma

Giriş denetleyicisini oluşturmak için, `Helm` *NGINX-giriş* yüklemek üzere kullanın. Daha fazla yedeklilik sağlamak için `--set controller.replicaCount` parametresiyle iki NGINX giriş denetleyicisi çoğaltması dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Yükleme sırasında, giriş denetleyicisi için bir Azure genel IP adresi oluşturulur. Bu genel IP adresi, giriş denetleyicisinin yaşam döngüsü için statiktir. Giriş denetleyicisini silerseniz, genel IP adresi ataması kaybedilir. Daha sonra ek bir giriş denetleyicisi oluşturursanız, yeni bir genel IP adresi atanır. Genel IP adresinin kullanımını sürdürmek istiyorsanız, bunun yerine [statik bir genel IP adresi olan bir giriş denetleyicisi oluşturabilirsiniz][aks-ingress-static-tls].

Genel IP adresini almak için `kubectl get service` komutunu kullanın.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

IP adresinin hizmete atanması birkaç dakika sürer.

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Dağıtımı test etmek için son adımda kullanıldığından, bu genel IP adresini bir yere unutmayın.

Henüz giriş kuralı oluşturulmadı. Genel IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir.

## <a name="generate-tls-certificates"></a>TLS sertifikaları oluşturma

Bu makalede, ile otomatik olarak imzalanan bir sertifika oluşturalım `openssl` . Üretim kullanımı için, bir sağlayıcı veya kendi sertifika yetkiliniz (CA) aracılığıyla güvenilir ve imzalı bir sertifika istemeniz gerekir. Bir sonraki adımda, bir Kubernetes *gizli* anahtarını, OpenSSL tarafından oluşturulan TLS sertifikasını ve özel anahtarı kullanarak oluşturabilirsiniz.

Aşağıdaki örnek, *aks-ingress-TLS. CRT* adlı 365 gün için geçerli 2048 BITLIK bir RSA x509 sertifikası üretir. Özel anahtar dosyası *aks-ingress-TLS. Key* olarak adlandırılmıştır. Bir Kubernetes TLS parolası, bu dosyaların her ikisini de gerektirir.

Bu makale, *demo.Azure.com* konu ortak adı ile birlikte çalışarak, değiştirilmesi gerekmez. Üretim kullanımı için, parametre için kendi kuruluş değerlerinizi belirtin `-subj` :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>TLS sertifikası için Kubernetes gizli dizisi oluşturma

Kubernetes 'in, giriş denetleyicisi için TLS sertifikasını ve özel anahtarı kullanmasına izin vermek için bir gizli dizi oluşturup kullanın. Gizli dizi bir kez tanımlanır ve önceki adımda oluşturulan sertifika ve anahtar dosyasını kullanır. Ardından, giriş yollarını tanımlarken bu gizliliğe başvurabilirsiniz.

Aşağıdaki örnek, bir gizli dizi adı ( *aks-ınress-TLS*) oluşturur:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Demo uygulamalarını çalıştırma

Bir giriş denetleyicisi ve sertifikanız ile gizli dizi yapılandırıldı. Şimdi AKS kümenizde iki tanıtım uygulaması çalıştıralım. Bu örnekte, HELI basit bir ' Hello World ' uygulamasının iki örneğini dağıtmak için kullanılır.

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

Her iki uygulama da artık Kubernetes kümenizde çalışıyor, ancak bunlar türünde bir hizmetle yapılandırılmış `ClusterIP` . Bu nedenle, uygulamalara internet 'ten erişilemez. Bunları herkese açık hale getirmek için bir Kubernetes giriş kaynağı oluşturun. Giriş kaynağı, trafiği iki uygulamadan birine yönlendiren kuralları yapılandırır.

Aşağıdaki örnekte, adrese `https://demo.azure.com/` olan trafik adlı hizmete yönlendirilir `aks-helloworld` . Adrese giden trafik `https://demo.azure.com/hello-world-two` `ingress-demo` hizmete yönlendirilir. Bu makalede, bu demo ana bilgisayar adlarını değiştirmeniz gerekmez. Üretim kullanımı için, sertifika isteği ve oluşturma sürecinin bir parçası olarak belirtilen adları sağlayın.

> [!TIP]
> Sertifika isteği işlemi sırasında belirtilen ana bilgisayar adı, CN adı, giriş rotasında tanımlanan konakla eşleşmiyorsa, denetleyici bir *Kubernetes ınress denetleyicisi sahte sertifika* uyarısı görüntüler. Sertifikanızın ve giriş yolu ana bilgisayar adlarının eşleştiğinden emin olun.

*TLS* bölümü, giriş yoluna, ana bilgisayar *demo.Azure.com* için *aks-giriş-TLS* adlı gizli dizi kullanımını söyler. Daha sonra, üretim kullanımı için kendi ana bilgisayar adresinizi belirtin.

Adlı bir dosya oluşturun `hello-world-ingress.yaml` ve aşağıdaki örnekteki YAML 'yi kopyalayın.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

Örnek çıkış, giriş kaynağının oluşturulduğunu gösterir.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Giriş yapılandırmasını test etme

Sahte *demo.Azure.com* barındırımızda sertifikaları test etmek için kullanın `curl` ve *--Resolve* parametresini belirtin. Bu parametre, *demo.Azure.com* adını giriş DENETLEYICINIZIN genel IP adresi ile eşlemenizi sağlar. Aşağıdaki örnekte gösterildiği gibi kendi giriş denetleyicinizin genel IP adresini belirtin:

```
curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com
```

Adresle birlikte ek bir yol sağlanmadı, bu nedenle giriş denetleyicisi rotayı varsayılan olarak alır */* . İlk demo uygulaması aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi döndürülür:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Komutumuz *-v* parametresi `curl` , alınan TLS sertifikası da dahil olmak üzere ayrıntılı bilgiler verir. Kıvrımlı çıkışınızla yarım yönlü, kendi TLS sertifikanızın kullanıldığını doğrulayabilirsiniz. Otomatik olarak imzalanan bir sertifika kullanıyor olsanız da *-k* parametresi sayfayı yüklemeye devam eder. Aşağıdaki örnek *veren: CN = demo. Azure. com; O = aks-ingress-TLS* sertifikası kullanıldı:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Şimdi adrese */Hello-World-iki* yolu ekleyin, örneğin `https://demo.azure.com/hello-world-two` . Aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi, özel başlığa sahip ikinci demo uygulaması döndürülür:

```
$ curl -v -k --resolve demo.azure.com:443:EXTERNAL_IP https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede giriş bileşenlerini ve örnek uygulamaları yüklemek için Held kullanılmaktadır. Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetleri içerir. Bu kaynakları temizlemek için, tüm örnek ad alanını veya ayrı kaynakları silebilirsiniz.

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

Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX-ingress* adlı grafiği arayın:

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

Sertifika gizliliğini silin:

```console
kubectl delete secret aks-ingress-tls --namespace ingress-basic
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
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
