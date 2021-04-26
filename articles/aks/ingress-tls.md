---
title: Otomatik TLS ile giriş oluşturma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) kümesinde otomatik TLS sertifikası oluşturma için şifrelemem ' i kullanan bir NGıNX giriş denetleyicisini yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: a3f78c0777d1377c8afdc5b43b457873de395cd6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779804"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Azure Kubernetes hizmeti (AKS) üzerinde bir HTTPS giriş denetleyicisi oluşturma

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Bu makalede bir Azure Kubernetes Service (AKS) kümesinde [NGINX giriş denetleyicisinin][nginx-ingress] nasıl dağıtılacağı gösterilir. [CERT-Manager][cert-manager] projesi, sertifikaları [şifreleyebilmesine][lets-encrypt] otomatik olarak oluşturmak ve yapılandırmak için kullanılır. Son olarak, her biri tek bir IP adresi üzerinden erişilebilen AKS kümesinde iki uygulama çalıştırılır.

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
- [Statik bir genel IP adresi ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Bu makalede ayrıca AKS kümeniz ile aynı kaynak grubunda bir [DNS bölgesi][dns-zone] olan [özel bir etki alanınız][custom-domain] olduğunu varsaymaktadır.

Bu makalede NGıNX giriş denetleyicisini ve CERT-Manager 'ı yüklemek için [helm3][helm] kullanılmaktadır. Held 'nin en son sürümünü kullandığınızdan ve giriş *-NGINX* ve *jetstack* Held depolarına erişim sahibi olduğunuzdan emin olun. Yükseltme yönergeleri için, bkz. [hela Install docs][helm-install]. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Giriş denetleyicisi oluşturma

Giriş denetleyicisini oluşturmak için `helm` komutunu kullanarak *NGINX-giriş*' yi kullanın. Daha fazla yedeklilik sağlamak için `--set controller.replicaCount` parametresiyle iki NGINX giriş denetleyicisi çoğaltması dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

Ayrıca giriş denetleyicisinin bir Linux düğümü üzerinde zamanlanması gerekir. Giriş denetleyicisi, Windows Server düğümlerinde çalıştırılmamalıdır. Kubernetes zamanlayıcısına NGINX giriş denetleyicisini Linux tabanlı bir düğümde çalıştırmasını söylemek için `--set nodeSelector` parametresi kullanılarak bir düğüm seçici belirtilir.

> [!TIP]
> Aşağıdaki örnek, *Giriş-Basic* adlı giriş kaynakları için bir Kubernetes ad alanı oluşturur. Gerektiğinde kendi ortamınız için bir ad alanı belirtin.

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

Genel IP adresini almak için `kubectl get service` komutunu kullanın. IP adresinin hizmete atanması birkaç dakika sürer.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Henüz giriş kuralı oluşturulmadı. Genel IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir.

## <a name="add-an-a-record-to-your-dns-zone"></a>DNS bölgenize bir kayıt ekleyin

[Az Network DNS Record-set A Add-Record][az-network-dns-record-set-a-add-record]kullanılarak, NGINX HIZMETININ dış IP adresiyle DNS bölgenize *bir* kayıt ekleyin.

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> İsteğe bağlı olarak, özel bir etki alanı yerine giriş denetleyicisi IP adresi için bir FQDN yapılandırabilirsiniz. Bu örneğin bir bash kabuğu için olduğunu unutmayın.
> 
> ```bash
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>cert-manager denetleyicisini yükleme

NGINX giriş denetleyicisi, TLS sonlandırmayı destekler. HTTPS için birçok farklı sertifika alma ve yapılandırma yöntemi vardır. Bu makalede, sertifika oluşturma ve yönetim işlevlerinin [şifrelenmesi][lets-encrypt] için otomatik olarak sağlanan [CERT-Manager][cert-manager]kullanımı gösterilmektedir.

CERT-Manager denetleyicisi 'ni yüklemek için:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

CERT Manager yapılandırması hakkında daha fazla bilgi için bkz. [CERT-Manager Projesi][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>CA kümesi veren oluşturma

Sertifikaların verilebilmesi için, CERT Manager bir [veren][cert-manager-issuer] veya [clusterıssuer][cert-manager-cluster-issuer] kaynağı gerektirir. Bu Kubernetes kaynakları işlevselliklerde aynıdır, ancak `Issuer` tek bir ad alanında çalışarak ve `ClusterIssuer` tüm ad alanları üzerinde çalışmaktadır. Daha fazla bilgi için bkz. [CERT-Manager veren][cert-manager-issuer] belgeleri.

Aşağıdaki örnek bildirimini kullanarak gibi bir küme veren oluşturun `cluster-issuer.yaml` . E-posta adresini kuruluşunuzdaki geçerli bir adresle güncelleştirin:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Veren oluşturmak için `kubectl apply` komutunu kullanın.

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Demo uygulamalarını çalıştırma

Bir giriş denetleyicisi ve bir sertifika yönetimi çözümü yapılandırıldı. Şimdi AKS kümenizde iki tanıtım uygulaması çalıştıralım. Bu örnekte, HELI basit bir *Hello World* uygulamasının iki örneğini dağıtmak için kullanılır.

Giriş denetleyicisini çalışır durumda görmek için AKS kümenizde iki tanıtım uygulaması çalıştırın. Bu örnekte, `kubectl apply` basit bir *Hello World* uygulamasının iki örneğini dağıtmak için kullanırsınız.

*Aks-HelloWorld-One. YAML* dosyası oluşturun ve aşağıdaki örnekte bulunan YAML 'yi kopyalayın:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Bir *aks-HelloWorld-Two. YAML* dosyası oluşturun ve aşağıdaki örnekte bulunan YAML 'yi kopyalayın:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Kullanarak iki demo uygulamayı çalıştırın `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Giriş yolu oluşturma

Her iki uygulama da artık Kubernetes kümenizde çalışıyor. Ancak bunlar türünde bir hizmetle yapılandırılır `ClusterIP` ve internet 'ten erişilemez. Bunları herkese açık hale getirmek için bir Kubernetes giriş kaynağı oluşturun. Giriş kaynağı, trafiği iki uygulamadan birine yönlendiren kuralları yapılandırır.

Aşağıdaki örnekte, *Hello-World-ınress adresine giden trafik. MY_CUSTOM_DOMAIN* *aks-HelloWorld-One* hizmetine yönlendirilir. *Hello-World-ınress adresine giden trafik. MY_CUSTOM_DOMAIN/Hello-World-Two* , *aks-HelloWorld-Two* Service 'e yönlendirilir. *Hello-World-ınress için trafik. MY_CUSTOM_DOMAIN/static* , statik varlıklar için *aks-HelloWorld-One* adlı hizmete yönlendirilir.

> [!NOTE]
> Özel bir etki alanı yerine giriş denetleyicisi IP adresi için bir FQDN yapılandırdıysanız, *Hello-World-giriş yerine FQDN 'yi kullanın. MY_CUSTOM_DOMAIN*. Örneğin, FQDN 'niz *demo-aks-ingress.eastus.cloudapp.Azure.com* ise, *Hello-i-ınress ' ı değiştirin.* *Demo-aks-ingress.eastus.cloudapp.azure.com* ile MY_CUSTOM_DOMAIN `hello-world-ingress.yaml` .

`hello-world-ingress.yaml`Aşağıdaki örnek YAML kullanılarak adlı bir dosya oluşturun. *Ana bilgisayarları* ve *ana bilgisayarı* , önceki adımda oluşturduğunuz DNS adına güncelleştirin.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Komutunu kullanarak giriş kaynağını oluşturun `kubectl apply` .

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Bir sertifika nesnesinin oluşturulduğunu doğrulama

Daha sonra, bir sertifika kaynağı oluşturulmalıdır. Sertifika kaynağı istenen X. 509.952 sertifikasını tanımlar. Daha fazla bilgi için bkz. [CERT-Manager sertifikaları][cert-manager-certificates]. CERT-Manager, v 0.2.2 bu yana otomatik olarak CERT-Manager ile dağıtılan giriş dolgusu 'nı kullanarak sizin için otomatik olarak bir sertifika nesnesi oluşturdu. Daha fazla bilgi için bkz. giriş [dolgusu belgeleri][ingress-shim].

Sertifikanın başarıyla oluşturulduğunu doğrulamak için `kubectl get certificate --namespace ingress-basic` komutunu kullanın ve *hazırlanın* seçeneğinin *doğru* olduğunu doğrulayın. Bu işlem birkaç dakika sürebilir.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Giriş yapılandırmasını test etme

Merhaba-Dünya girişi için bir Web tarayıcısı açın *.* Kubernetes giriş denetleyicinizin MY_CUSTOM_DOMAIN. HTTPS kullanmaya yönlendirirsiniz, sertifikaya güvenildiğini ve tanıtım uygulamasının Web tarayıcısında gösterildiğini görebilirsiniz. */Hello-World-Two* yolunu ekleyin ve ikinci demo uygulamasının özel başlığa sahip olduğuna dikkat edin.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede giriş bileşenleri, sertifikalar ve örnek uygulamalar yüklemek için Held kullanılmaktadır. Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetler içerir. Bu kaynakları temizlemek için, tüm örnek ad alanını veya ayrı kaynakları silebilirsiniz.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Örnek ad alanını ve tüm kaynakları Sil

Tüm örnek ad alanını silmek için `kubectl delete` komutunu kullanın ve ad alanı adınızı belirtin. Ad alanındaki tüm kaynaklar silinir.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Kaynakları tek tek Sil

Alternatif olarak, oluşturulan kaynakları tek tek silmek daha ayrıntılı bir yaklaşımdır. İlk olarak, küme verenin kaynaklarını kaldırın:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Komut ile Held sürümlerini listeleyin `helm list` . Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX* ve *CERT-Manager* adlı grafikleri arayın:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Komutuyla yayınları kaldırın `helm uninstall` . Aşağıdaki örnek NGıNX giriş ve CERT Manager dağıtımlarını kaldırır.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Sonra, iki örnek uygulamayı kaldırın:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Trafiği örnek uygulamalara yönlendiren giriş yolunu kaldırın:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Son olarak, kendi ad alanını silebilirsiniz. Komutunu kullanın `kubectl delete` ve ad alanı adınızı belirtin:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bazı dış bileşenler AKS 'ye eklenmiştir. Bu bileşenler hakkında daha fazla bilgi edinmek için aşağıdaki proje sayfalarına bakın:

- [Held CLı][helm-cli]
- [NGıNX giriş denetleyicisi][nginx-ingress]
- [cert-manager][cert-manager]

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
- [Statik bir genel IP adresi ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az_network_dns_record_set_a_add_record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
