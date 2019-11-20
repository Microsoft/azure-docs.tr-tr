---
title: Azure Kubernetes Service (AKS) içinde statik IP adresi ile HTTP giriş denetleyicisi oluşturma
description: Azure Kubernetes Service (AKS) kümesinde statik bir genel IP adresi ile NGıNX giriş denetleyicisi yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 9d53f8eff53a875a7a37d1ee1986e94405a24485
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144972"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde statik bir genel IP adresi ile giriş denetleyicisi oluşturma

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Bu makalede bir Azure Kubernetes Service (AKS) kümesinde [NGINX giriş denetleyicisinin][nginx-ingress] nasıl dağıtılacağı gösterilir. Giriş denetleyicisi statik bir genel IP adresi ile yapılandırılır. [CERT-Manager][cert-manager] projesi, sertifikaları [şifreleyebilmesine][lets-encrypt] otomatik olarak oluşturmak ve yapılandırmak için kullanılır. Son olarak, her biri tek bir IP adresi üzerinden erişilebilen AKS kümesinde iki uygulama çalıştırılır.

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
- [Dinamik bir genel IP adresi ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-tls]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Bu makalede, NGıNX giriş denetleyicisini, CERT-Manager 'ı ve örnek bir Web uygulamasını yüklemek için Held kullanılmaktadır. AKS kümenizde helk başlatılmış ve Tiller için bir hizmet hesabı kullanmanız gerekir. Held 'nin en son sürümünü kullandığınızdan emin olun. Yükseltme yönergeleri için, bkz. [hela Install docs][helm-install]. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Giriş denetleyicisi oluşturma

Varsayılan olarak, bir NGıNX giriş denetleyicisi yeni bir genel IP adresi atamasıyla oluşturulur. Bu genel IP adresi yalnızca giriş denetleyicisinin yaşam döngüsü için statiktir ve denetleyici silinip yeniden oluşturulduysa kaybedilir. Ortak bir yapılandırma gereksinimi, NGıNX giriş denetleyicisine mevcut bir statik genel IP adresi sağlamaktır. Giriş denetleyicisi silinirse statik genel IP adresi kalır. Bu yaklaşım, mevcut DNS kayıtlarını ve ağ yapılandırmasını uygulamalarınızın yaşam döngüsü boyunca tutarlı bir şekilde kullanmanıza olanak sağlar.

Statik bir genel IP adresi oluşturmanız gerekiyorsa, önce [az aks Show][az-aks-show] komutuyla aks kümesinin kaynak grubu adını alın:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

Ardından, [az Network public-IP Create][az-network-public-ip-create] komutunu kullanarak *statik* AYıRMA yöntemiyle genel bir IP adresi oluşturun. Aşağıdaki örnek, önceki adımda elde edilen AKS kümesi kaynak grubundaki *Myakspublicıp* adlı BIR genel IP adresi oluşturur:

```azurecli-interactive
az network public-ip create --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --sku Standard --allocation-method static --query publicIp.ipAddress -o tsv
```

Şimdi *NGINX-ingress* grafiğini Held ile dağıtın. `--set controller.service.loadBalancerIP` parametresini ekleyin ve önceki adımda oluşturulan kendi genel IP adresini belirtin. Ek artıklık için NGıNX giriş denetleyicilerinin iki yinelemesi `--set controller.replicaCount` parametresiyle dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

Giriş denetleyicisinin da bir Linux düğümünde zamanlanması gerekir. Windows Server düğümleri (Şu anda AKS 'deki önizlemede) giriş denetleyicisini çalıştırmamalıdır. Kubernetes Scheduler ' ın Linux tabanlı bir düğümde NGıNX giriş denetleyicisini çalıştırmasını bildirmek için `--set nodeSelector` parametresi kullanılarak bir düğüm seçici belirtilir.

> [!TIP]
> Aşağıdaki örnek, *Giriş-Basic*adlı giriş kaynakları için bir Kubernetes ad alanı oluşturur. Gerektiğinde kendi ortamınız için bir ad alanı belirtin. AKS kümeniz RBAC etkinleştirilmemişse, helk komutlarına `--set rbac.create=false` ekleyin.

> [!TIP]
> Kümenizdeki kapsayıcılara yönelik [istemci kaynak IP korumasını][client-source-ip] etkinleştirmek Istiyorsanız, Helm install komutuna `--set controller.service.externalTrafficPolicy=Local` ekleyin. İstemci kaynak IP 'si, *Için X-iletilen-için*istek üstbilgisinde depolanır. İstemci kaynak IP koruması etkinken bir giriş denetleyicisi kullanılırken, SSL geçişi çalışmaz.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="40.121.63.72"
```

Kubernetes yük dengeleyici hizmeti NGıNX giriş denetleyicisi için oluşturulduğunda, aşağıdaki örnek çıktıda gösterildiği gibi statik IP adresiniz atanır:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                        TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
dinky-panda-nginx-ingress-controller        LoadBalancer   10.0.232.56   40.121.63.72   80:31978/TCP,443:32037/TCP   3m
dinky-panda-nginx-ingress-default-backend   ClusterIP      10.0.95.248   <none>         80/TCP                       3m
```

Henüz bir giriş kuralı oluşturulmadı, bu nedenle genel IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir. Giriş kuralları aşağıdaki adımlarda yapılandırılır.

## <a name="configure-a-dns-name"></a>DNS adı yapılandırma

HTTPS sertifikalarının düzgün çalışması için, giriş denetleyicisi IP adresi için bir FQDN yapılandırın. Aşağıdaki betiği, giriş denetleyicinizin IP adresi ve FQDN için kullanmak istediğiniz benzersiz bir ad ile güncelleştirin:

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="40.121.63.72"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Giriş denetleyicisine artık FQDN üzerinden erişilebilir.

## <a name="install-cert-manager"></a>CERT-Manager 'ı yükler

NGıNX giriş denetleyicisi TLS sonlandırmasını destekler. HTTPS için Sertifika almanın ve yapılandırmanın birkaç yolu vardır. Bu makalede, sertifika oluşturma ve yönetim işlevlerinin [şifrelenmesi][lets-encrypt] için otomatik olarak sağlanan [CERT-Manager][cert-manager]kullanımı gösterilmektedir.

> [!NOTE]
> Bu makalede, şifrelemem için `staging` ortamı kullanılmaktadır. Üretim dağıtımlarında, kaynak tanımlarındaki `letsencrypt-prod` ve `https://acme-v02.api.letsencrypt.org/directory`, Held grafiğini yüklerken kullanın.

CERT-Manager denetleyicisini RBAC özellikli bir kümeye yüklemek için aşağıdaki `helm install` komutunu kullanın:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.11/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.11.0 \
  jetstack/cert-manager
```

CERT Manager yapılandırması hakkında daha fazla bilgi için bkz. [CERT-Manager Projesi][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>CA kümesi veren oluşturma

Sertifikaların verilebilmesi için, CERT Manager bir [veren][cert-manager-issuer] veya [clusterıssuer][cert-manager-cluster-issuer] kaynağı gerektirir. Bu Kubernetes kaynakları işlevselliklerde aynıdır, ancak `Issuer` tek bir ad alanında ve `ClusterIssuer` tüm ad alanları üzerinde çalışmaktadır. Daha fazla bilgi için bkz. [CERT-Manager veren][cert-manager-issuer] belgeleri.

Aşağıdaki örnek bildirimini kullanarak `cluster-issuer.yaml`gibi bir küme veren oluşturun. E-posta adresini kuruluşunuzdaki geçerli bir adresle güncelleştirin:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Veren oluşturmak için `kubectl apply -f cluster-issuer.yaml` komutunu kullanın.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Demo uygulamalarını çalıştırma

Bir giriş denetleyicisi ve bir sertifika yönetimi çözümü yapılandırıldı. Şimdi AKS kümenizde iki tanıtım uygulaması çalıştıralım. Bu örnekte, HELI basit bir ' Hello World ' uygulamasının iki örneğini dağıtmak için kullanılır.

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

Her iki uygulama da artık Kubernetes kümenizde çalışıyor, ancak bunlar `ClusterIP`türünde bir hizmetle yapılandırılmıştır. Bu nedenle, uygulamalara internet 'ten erişilemez. Bunları herkese açık hale getirmek için bir Kubernetes giriş kaynağı oluşturun. Giriş kaynağı, trafiği iki uygulamadan birine yönlendiren kuralları yapılandırır.

Aşağıdaki örnekte, Address `https://demo-aks-ingress.eastus.cloudapp.azure.com/` trafiği `aks-helloworld`adlı hizmete yönlendirilir. Adres `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` trafik `ingress-demo` hizmetine yönlendirilir. *Ana bilgisayarları* ve *ana bilgisayarı* , önceki adımda oluşturduğunuz DNS adına güncelleştirin.

`hello-world-ingress.yaml` adlı bir dosya oluşturun ve aşağıdaki örnekte bulunan YAML 'yi kopyalayın.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
    http:
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

`kubectl apply -f hello-world-ingress.yaml` komutunu kullanarak giriş kaynağını oluşturun.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="create-a-certificate-object"></a>Sertifika nesnesi oluşturma

Daha sonra, bir sertifika kaynağı oluşturulmalıdır. Sertifika kaynağı istenen X. 509.952 sertifikasını tanımlar. Daha fazla bilgi için bkz. [CERT-Manager sertifikaları][cert-manager-certificates].

CERT-Manager, v 0.2.2 sürümünden itibaren otomatik olarak CERT-Manager ile dağıtılan giriş dolgusu 'nı kullanarak sizin için otomatik olarak bir sertifika nesnesi oluşturdu. Daha fazla bilgi için bkz. giriş [dolgusu belgeleri][ingress-shim].

Sertifikanın başarıyla oluşturulduğunu doğrulamak için `kubectl describe certificate tls-secret --namespace ingress-basic` komutunu kullanın.

Sertifika verildiyse aşağıdakine benzer bir çıktı görürsünüz:
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Ek bir sertifika kaynağı oluşturmanız gerekiyorsa, bunu aşağıdaki örnek bildirimle yapabilirsiniz. *Dnsnames* ve *etki alanlarını* , önceki adımda oluşturduğunuz DNS adıyla güncelleştirin. Yalnızca iç giriş denetleyicisi kullanıyorsanız, hizmetiniz için iç DNS adını belirtin.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Sertifika kaynağını oluşturmak için `kubectl apply -f certificates.yaml` komutunu kullanın.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Giriş yapılandırmasını test etme

*https://demo-aks-ingress.eastus.cloudapp.azure.com* gibi Kubernetes GIRIŞ denetleyicinizin FQDN 'sine bir Web tarayıcısı açın.

Bu örneklerde `letsencrypt-staging`kullanıldığı için, verilen SSL sertifikasına tarayıcı tarafından güvenilmiyor. Uygulamanıza devam etmek için uyarı isteğini kabul edin. Sertifika bilgileri, bu *sahte bir ara x1* sertifikasının, Şifreleyebilmesine göre verildiğini gösterir. Bu sahte sertifika, `cert-manager` isteği doğru bir şekilde işledi ve sağlayıcıdan bir sertifika almış olduğunu belirtir:

![Hazırlama sertifikasını şifreleyelim](media/ingress/staging-certificate.png)

`staging`yerine `prod` kullanmak için şifreleyelim ' i değiştirirseniz, aşağıdaki örnekte gösterildiği gibi, Şifreleyebilmesine göre verilen güvenilir bir sertifika kullanılır:

![Sertifikayı şifreleyelim](media/ingress/certificate.png)

Demo uygulaması Web tarayıcısında gösterilir:

![Uygulama örneği bir](media/ingress/app-one.png)

Şimdi, *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* gıbı, FQDN 'ye */Hello-World-Two* yolunu ekleyin. Özel başlığa sahip ikinci demo uygulaması gösterilir:

![Uygulama örneği iki](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede giriş bileşenleri, sertifikalar ve örnek uygulamalar yüklemek için Held kullanılmaktadır. Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetler içerir. Bu kaynakları temizlemek için, tüm örnek ad alanını veya ayrı kaynakları silebilirsiniz.

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

Alternatif olarak, oluşturulan kaynakları tek tek silmek daha ayrıntılı bir yaklaşımdır. İlk olarak, sertifika kaynaklarını kaldırın:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Şimdi `helm list` komutuyla Held sürümlerini listeleyin. Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX-ingress*, *CERT-Manager*ve *aks-HelloWorld*adlı grafikleri arayın:

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
waxen-hamster           1           Wed Mar  6 23:16:00 2019    DEPLOYED    nginx-ingress-1.3.1   0.22.0        kube-system
alliterating-peacock    1           Wed Mar  6 23:17:37 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
mollified-armadillo     1           Wed Mar  6 23:26:04 2019    DEPLOYED    aks-helloworld-0.1.0                default
wondering-clam          1           Wed Mar  6 23:26:07 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

`helm delete` komutuyla yayınları silin. Aşağıdaki örnekte NGıNX giriş dağıtımı, Sertifika Yöneticisi ve iki örnek AKS Hello World Apps de silinir.

```
$ helm delete waxen-hamster alliterating-peacock mollified-armadillo wondering-clam

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Ardından, AKS Hello World uygulamasının Held deposunu kaldırın:

```console
helm repo remove azure-samples
```

Trafiği örnek uygulamalara yönlendiren giriş yolunu kaldırın:

```console
kubectl delete -f hello-world-ingress.yaml
```

Kendi ad alanını silin. `kubectl delete` komutunu kullanın ve ad alanı adınızı belirtin:

```console
kubectl delete namespace ingress-basic
```

Son olarak, giriş denetleyicisi için oluşturulan statik genel IP adresini kaldırın. Bu makalenin ilk adımında elde edilen *MC_myResourceGroup_myAKSCluster_eastus*gibi *Mc_* Cluster kaynak grubu adınızı sağlayın:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bazı dış bileşenler AKS 'ye eklenmiştir. Bu bileşenler hakkında daha fazla bilgi edinmek için aşağıdaki proje sayfalarına bakın:

- [Held CLı][helm-cli]
- [NGıNX giriş denetleyicisi][nginx-ingress]
- [CERT-Manager][cert-manager]

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
- [Dinamik bir genel IP ile giriş denetleyicisi oluşturun ve otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi yapılandırın][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
