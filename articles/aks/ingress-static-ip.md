---
title: Giriş denetleyicisini statik IP ile kullanma
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) kümesinde statik bir genel IP adresi ile NGıNX giriş denetleyicisi yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8de0d25bc30d53f11ddaed5ab7b53ff992a5c88c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779840"
---
# <a name="create-an-ingress-controller-with-a-static-public-ip-address-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service’te (AKS) statik bir genel IP adresiyle giriş denetleyicisi oluşturma

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Bu makalede bir Azure Kubernetes Service (AKS) kümesinde [NGINX giriş denetleyicisinin][nginx-ingress] nasıl dağıtılacağı gösterilir. Giriş denetleyicisi statik bir genel IP adresi ile yapılandırılır. [CERT-Manager][cert-manager] projesi, sertifikaları [şifreleyebilmesine][lets-encrypt] otomatik olarak oluşturmak ve yapılandırmak için kullanılır. Son olarak, her biri tek bir IP adresi üzerinden erişilebilen AKS kümesinde iki uygulama çalıştırılır.

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [Kendi TLS sertifikalarınızı kullanan bir giriş denetleyicisi oluşturun][aks-ingress-own-tls]
- [Dinamik bir genel IP adresi ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-tls]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Bu makalede NGıNX giriş denetleyicisini ve CERT-Manager 'ı yüklemek için [helm3][helm] kullanılmaktadır. Held 'nin en son sürümünü kullandığınızdan ve giriş *-NGINX* ve *jetstack* Held depolarına erişim sahibi olduğunuzdan emin olun. Yükseltme yönergeleri için, bkz. [hela Install docs][helm-install]. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme][azure-cli-install].

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

> [!NOTE]
> Yukarıdaki komutlar, AKS kümenizi silerseniz silinecek bir IP adresi oluşturur. Alternatif olarak, AKS kümeinizden ayrı olarak yönetilebilen farklı bir kaynak grubunda bir IP adresi oluşturabilirsiniz. Farklı bir kaynak grubunda bir IP adresi oluşturursanız, AKS kümesi tarafından kullanılan küme kimliğinin, *ağ katılımcısı* gibi diğer kaynak grubu için izin Temsilcili olduğundan emin olun. Daha fazla bilgi için bkz. [AKS yük dengeleyicisiyle statik bir genel IP adresi ve DNS etiketi kullanma][aks-static-ip].

Şimdi *NGINX-ingress* grafiğini Held ile dağıtın. Daha fazla yedeklilik sağlamak için `--set controller.replicaCount` parametresiyle iki NGINX giriş denetleyicisi çoğaltması dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

Giriş denetleyicisinin hem giriş denetleyicisi hizmetine ayrılacak yük dengeleyicinin statik IP adresini hem de genel IP adresi kaynağına uygulanmakta olan DNS adı etiketini bilmesi için, helk sürümüne iki ek parametre geçirmeniz gerekir. HTTPS sertifikalarının düzgün çalışması için, giriş denetleyicisi IP adresi için bir FQDN yapılandırmak üzere bir DNS ad etiketi kullanılır.

1. Parametresini ekleyin `--set controller.service.loadBalancerIP` . Önceki adımda oluşturulan kendi genel IP adresini belirtin.
1. Parametresini ekleyin `--set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"` . Önceki adımda oluşturulan genel IP adresine uygulanacak bir DNS ad etiketi belirtin.

Ayrıca giriş denetleyicisinin bir Linux düğümü üzerinde zamanlanması gerekir. Giriş denetleyicisi, Windows Server düğümlerinde çalıştırılmamalıdır. Kubernetes zamanlayıcısına NGINX giriş denetleyicisini Linux tabanlı bir düğümde çalıştırmasını söylemek için `--set nodeSelector` parametresi kullanılarak bir düğüm seçici belirtilir.

> [!TIP]
> Aşağıdaki örnek, *Giriş-Basic* adlı giriş kaynakları için bir Kubernetes ad alanı oluşturur. Gerektiğinde kendi ortamınız için bir ad alanı belirtin. AKS kümeniz Kubernetes RBAC etkin değilse, `--set rbac.create=false` helk komutlarına ekleyin.

> [!TIP]
> Kümenizdeki kapsayıcılara yönelik [istemci kaynak IP korumasını][client-source-ip] etkinleştirmek Istiyorsanız, `--set controller.service.externalTrafficPolicy=Local` Helm install komutuna ekleyin. İstemci kaynak IP 'si, *Için X-iletilen-için* istek üstbilgisinde depolanır. İstemci kaynak IP koruması etkinken bir giriş denetleyicisi kullanılırken, TLS geçişi çalışmaz.

Aşağıdaki betiği, giriş denetleyicinizin **IP adresiyle** ve FQDN ön eki için kullanmak istediğiniz **benzersiz bir adla** güncelleştirin.

> [!IMPORTANT]
> Komutu çalıştırırken, *STATIC_IP* ve *DNS_LABEL* kendi IP adresiniz ve benzersiz adınızla güncelleştirmeniz gerekir.

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
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.service.loadBalancerIP="STATIC_IP" \
    --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-dns-label-name"="DNS_LABEL"
```

Kubernetes yük dengeleyici hizmeti NGıNX giriş denetleyicisi için oluşturulduğunda, aşağıdaki örnek çıktıda gösterildiği gibi statik IP adresiniz atanır:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Henüz bir giriş kuralı oluşturulmadı, bu nedenle genel IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir. Giriş kuralları aşağıdaki adımlarda yapılandırılır.

Genel IP adresinde FQDN 'yi sorgulayarak DNS ad etiketinin uygulandığını aşağıdaki gibi doğrulayabilirsiniz:

```azurecli-interactive
az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query "[?ipAddress=='myAKSPublicIP'].[dnsSettings.fqdn]" -o tsv
```

Giriş denetleyicisine artık IP adresi veya FQDN üzerinden erişilebilir.

## <a name="install-cert-manager"></a>cert-manager denetleyicisini yükleme

NGINX giriş denetleyicisi, TLS sonlandırmayı destekler. HTTPS için birçok farklı sertifika alma ve yapılandırma yöntemi vardır. Bu makalede, sertifika oluşturma ve yönetim işlevlerinin [şifrelenmesi][lets-encrypt] için otomatik olarak sağlanan [CERT-Manager][cert-manager]kullanımı gösterilmektedir.

> [!NOTE]
> Bu makalede, `staging` şifreleme için ortamı kullanılmaktadır. Üretim dağıtımlarında, `letsencrypt-prod` `https://acme-v02.api.letsencrypt.org/directory` kaynak tanımlarında ve, Held grafiğini yüklerken öğesini kullanın.

Bir Kubernetes RBAC etkin kümesine CERT-Manager denetleyicisi yüklemek için şu `helm install` komutu kullanın:

```console
# Label the cert-manager namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.16.1 \
  --set installCRDs=true \
  --set nodeSelector."beta\.kubernetes\.io/os"=linux \
  jetstack/cert-manager
```

CERT Manager yapılandırması hakkında daha fazla bilgi için bkz. [CERT-Manager Projesi][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>CA kümesi veren oluşturma

Sertifikaların verilebilmesi için, CERT Manager bir [veren][cert-manager-issuer] veya [clusterıssuer][cert-manager-cluster-issuer] kaynağı gerektirir. Bu Kubernetes kaynakları işlevselliklerde aynıdır, ancak `Issuer` tek bir ad alanında çalışarak ve `ClusterIssuer` tüm ad alanları üzerinde çalışmaktadır. Daha fazla bilgi için bkz. [CERT-Manager veren][cert-manager-issuer] belgeleri.

Aşağıdaki örnek bildirimini kullanarak gibi bir küme veren oluşturun `cluster-issuer.yaml` . E-posta adresini kuruluşunuzdaki geçerli bir adresle güncelleştirin:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
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

```
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

Çıktının bu örneğe benzer olması gerekir:

```
clusterissuer.cert-manager.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Demo uygulamalarını çalıştırma

Bir giriş denetleyicisi ve bir sertifika yönetimi çözümü yapılandırıldı. Şimdi AKS kümenizde iki tanıtım uygulaması çalıştıralım. Bu örnekte, HELI basit bir ' Hello World ' uygulamasının iki örneğini dağıtmak için kullanılır.

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

Aşağıdaki örnekte, adrese `https://demo-aks-ingress.eastus.cloudapp.azure.com/` olan trafik adlı hizmete yönlendirilir `aks-helloworld` . Adrese giden trafik `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` `ingress-demo` hizmete yönlendirilir. *Ana bilgisayarları* ve *ana bilgisayarı* , önceki adımda oluşturduğunuz DNS adına güncelleştirin.

Adlı bir dosya oluşturun `hello-world-ingress.yaml` ve aşağıdaki örnekteki YAML 'yi kopyalayın.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
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

Komutunu kullanarak giriş kaynağını oluşturun `kubectl apply` .

```
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

Çıktının bu örneğe benzer olması gerekir:

```
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
apiVersion: cert-manager.io/v1alpha2
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

Sertifika kaynağını oluşturmak için `kubectl apply` komutunu kullanın.

```
$ kubectl apply -f certificates.yaml

certificate.cert-manager.io/tls-secret created
```

## <a name="test-the-ingress-configuration"></a>Giriş yapılandırmasını test etme

Kubernetes giriş denetleyicinizin FQDN 'sine bir Web tarayıcısı açın (örneğin,) *`https://demo-aks-ingress.eastus.cloudapp.azure.com`* .

Bu örneklerde kullanırken `letsencrypt-staging` , VERILEN TLS/SSL sertifikasına tarayıcı tarafından güvenilmiyor. Uygulamanıza devam etmek için uyarı isteğini kabul edin. Sertifika bilgileri, bu *sahte bir ara x1* sertifikasının, Şifreleyebilmesine göre verildiğini gösterir. Bu sahte sertifika `cert-manager` , isteğin doğru bir şekilde işlendiğini ve sağlayıcıdan bir sertifika alındığını gösterir:

![Hazırlama sertifikasını şifreleyelim](media/ingress/staging-certificate.png)

Yerine kullanmak için şifreleyelim `prod` `staging` ' i değiştirirseniz, aşağıdaki örnekte gösterildiği gibi, şifreleyebilmesine göre verilen güvenilir bir sertifika kullanılır:

![Sertifikayı şifreleyelim](media/ingress/certificate.png)

Demo uygulaması Web tarayıcısında gösterilir:

![Uygulama örneği bir](media/ingress/app-one.png)

Şimdi */Hello-World-iki* yolunu FQDN 'sine ekleyin, örneğin *`https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two`* . Özel başlığa sahip ikinci demo uygulaması gösterilir:

![Uygulama örneği iki](media/ingress/app-two.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede giriş bileşenleri, sertifikalar ve örnek uygulamalar yüklemek için Held kullanılmaktadır. Bir helk grafiği dağıttığınızda, bir dizi Kubernetes kaynağı oluşturulur. Bu kaynaklar, pods, dağıtımlar ve hizmetler içerir. Bu kaynakları temizlemek için, tüm örnek ad alanını veya ayrı kaynakları silebilirsiniz.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Örnek ad alanını ve tüm kaynakları Sil

Tüm örnek ad alanını silmek için `kubectl delete` komutunu kullanın ve ad alanı adınızı belirtin. Ad alanındaki tüm kaynaklar silinir.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Kaynakları tek tek Sil

Alternatif olarak, oluşturulan kaynakları tek tek silmek daha ayrıntılı bir yaklaşımdır. İlk olarak, sertifika kaynaklarını kaldırın:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Şimdi komutunu kullanarak Held sürümlerini listeleyin `helm list` . Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX-ingress* ve *CERT-Manager* adlı grafikleri arayın:

```
$ helm list --all-namespaces

NAME                    NAMESPACE       REVISION        UPDATED                        STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-11 14:51:03.454165006  deployed        nginx-ingress-1.28.2    0.26.2
cert-manager            ingress-basic   1               2020-01-06 21:19:03.866212286  deployed        cert-manager-v0.13.0    v0.13.0
```

Komutuyla yayınları kaldırın `helm uninstall` . Aşağıdaki örnek NGıNX giriş dağıtımı ve Sertifika Yöneticisi dağıtımlarını kaldırır.

```
$ helm uninstall nginx-ingress cert-manager -n ingress-basic

release "nginx-ingress" deleted
release "cert-manager" deleted
```

Sonra, iki örnek uygulamayı kaldırın:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Kendi ad alanını silin. Komutunu kullanın `kubectl delete` ve ad alanı adınızı belirtin:

```console
kubectl delete namespace ingress-basic
```

Son olarak, giriş denetleyicisi için oluşturulan statik genel IP adresini kaldırın. Bu makalenin ilk adımında elde edilen *MC_myResourceGroup_myAKSCluster_eastus* gibi *Mc_* Cluster kaynak grubu adınızı sağlayın:

```azurecli-interactive
az network public-ip delete --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP
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
- [Dinamik bir genel IP ile giriş denetleyicisi oluşturun ve otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi yapılandırın][aks-ingress-tls]

<!-- LINKS - external -->
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az_network_public_ip_create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-static-ip]: static-ip.md
