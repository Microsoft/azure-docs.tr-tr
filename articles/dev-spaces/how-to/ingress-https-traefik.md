---
title: Özel bir traefik giriş denetleyicisi kullanma ve https 'yi yapılandırma
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Azure Dev Spaces özel bir traefik ingınress denetleyicisi kullanmak ve bu giriş denetleyicisini kullanarak HTTPS 'yi yapılandırmak için nasıl yapılandırılacağını öğrenin
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
ms.custom: devx-track-js
ms.openlocfilehash: 76a89545b8edc700928c1c2fe0e91dfc5d3127b9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777500"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Özel bir traefik giriş denetleyicisi kullanma ve https 'yi yapılandırma

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Bu makalede, Azure Dev Spaces özel bir traefik ingınress denetleyicisi kullanmak üzere nasıl yapılandırılacağı gösterilmektedir. Bu makalede ayrıca, bu özel giriş denetleyicisinin HTTPS kullanmak üzere nasıl yapılandırılacağı gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Hesabınız yoksa [ücretsiz bir hesap][azure-account-create] oluşturabilirsiniz.
* [Yüklü Azure CLI][az-cli].
* [Azure Dev Spaces etkinleştirilmiş Azure Kubernetes hizmeti (AKS) kümesi [QS-CLI].
* [kubectl][kubectl] yüklendi.
* [Held 3 yüklendi][helm-installed].
* [DNS bölgesi][dns-zone]olan [özel bir etki alanı][custom-domain] . Bu makalede, özel etki alanı ve DNS bölgesinin AKS kümeniz ile aynı kaynak grubunda olduğu varsayılır, ancak farklı bir kaynak grubunda özel bir etki alanı ve DNS bölgesi kullanmak mümkündür.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Özel bir traefik giriş denetleyicisi yapılandırma

Kubernetes komut satırı istemcisi olan [kubectl][kubectl]kullanarak kümenize bağlanın. `kubectl` istemcisini Kubernetes kümenize bağlanacak şekilde yapılandırmak için [az aks get-credentials][az-aks-get-credentials] komutunu kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Kümenize bağlantıyı doğrulamak için [kubectl get][kubectl-get] komutunu kullanarak küme düğümleri listesini alın.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Traefik giriş denetleyicisi HELI grafiğini içeren [resmi kararlı Held deposunu][helm-stable-repo]ekleyin.

```console
helm repo add stable https://charts.helm.sh/stable
```

Traefik giriş denetleyicisi için bir Kubernetes ad alanı oluşturun ve öğesini kullanarak çalıştırın `helm` .

> [!NOTE]
> AKS kümenizde Kubernetes RBAC etkinleştirilmemişse, *--set RBAC. Enabled = true* parametresini kaldırın.

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> Yukarıdaki örnek, giriş denetleyiciniz için genel bir uç nokta oluşturur. Bunun yerine giriş denetleyicinizde özel bir uç nokta kullanmanız gerekiyorsa, *--Set Service. açıklamalarını ekleyin. " Service \\ . Beta \\ . Kubernetes \\ . IO/Azure-Load-dengeleyici-Internal "= true* parametresi, *helk install* komutuna.
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Bu özel uç nokta, AKS kümesinin dağıtıldığı sanal ağ içinde kullanıma sunulur.

[Kubectl Get][kubectl-get]kullanarak traefik giriş denetleyici hizmetinin IP adresini alın.

```console
kubectl get svc -n traefik --watch
```

Örnek çıktı, *traefik* ad alanındaki tüm hizmetlerin IP adreslerini gösterir.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

[Az Network DNS Record-set A Add-Record][az-network-dns-record-set-a-add-record]kullanılarak DNS bölgenize, traefik HIZMETININ dış IP adresini kullanarak *bir* kayıt ekleyin.

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Yukarıdaki örnek *MY_CUSTOM_DOMAIN* DNS bölgesine *bir* kayıt ekler.

Bu makalede, Azure Dev Spaces kullanmayı göstermek için [Azure dev Spaces bisiklet paylaşımı örnek uygulamasını](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) kullanacaksınız. Uygulamayı GitHub 'dan kopyalayıp dizinine gidin:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

[Values. YAML][values-yaml] dosyasını açın ve aşağıdaki güncelleştirmeleri yapın:
* *<REPLACE_ME_WITH_HOST_SUFFIX>* tüm örneklerini traefik ile değiştirin *. MY_CUSTOM_DOMAIN* *MY_CUSTOM_DOMAIN* için etki alanınızı kullanma. 
* *Kubernetes.io/ingress.class: traefik # özel* giriş ile *Kubernetes.io/ingress.class: traefik-azds # dev Spaces* ile değiştirin. 

Aşağıda güncelleştirilmiş bir dosyaya örnek verilmiştir `values.yaml` :

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Değişikliklerinizi kaydedin ve dosyayı kapatın.

Kullanarak örnek uygulamanızla *geliştirme* alanı oluşturun `azds space select` .

```console
azds space select -n dev -y
```

Kullanarak örnek uygulamayı dağıtın `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Yukarıdaki örnek, örnek uygulamayı *dev* ad alanına dağıtır.

Kullanarak örnek uygulamaya erişmek için URL 'Leri görüntüleyin `azds list-uris` .

```console
azds list-uris
```

Aşağıdaki çıktıda, ' den örnek URL 'Ler gösterilmektedir `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Komuttan ortak URL 'yi açarak *bıkesharingweb* hizmetine gidin `azds list-uris` . Yukarıdaki örnekte, *bıkesharingweb* hizmeti 'nin Genel URL 'si `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

> [!NOTE]
> *Bıkesharingweb* hizmeti yerine bir hata sayfası görürseniz, *values. yaml* dosyasındaki *Kubernetes.io/ingress.Class* ek açıklamasını ve **konağını güncelleştirdiğinizi** doğrulayın.

`azds space select` *Geliştirme* altında bir alt alan oluşturmak için komutunu kullanın ve alt geliştirme alanına erişmek için URL 'leri listeleyin.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Aşağıdaki çıktı, `azds list-uris` *azureuser1* alt geliştirme alanındaki örnek uygulamaya erişmek için ' den örnek URL 'leri gösterir.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Komuttan ortak URL 'yi açarak *azureuser1* alt geliştirme alanında *bıkesharingweb* hizmetine gidin `azds list-uris` . Yukarıdaki örnekte, *azureuser1* alt dev Space 'teki *bıkesharingweb* hizmetinin genel URL 'si `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Traefik giriş denetleyicisini HTTPS kullanacak şekilde yapılandırma

Traefik ınress denetleyicinizi HTTPS kullanacak şekilde yapılandırırken TLS sertifikasının yönetimini otomatikleştirmek için [CERT-Manager][cert-manager] kullanın. `helm` *Certmanager* grafiğini yüklemek için kullanın.

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Bir dosya oluşturun ve e-posta `letsencrypt-clusterissuer.yaml` alanını e-posta adresiniz ile güncelleştirin.

```yaml
apiVersion: cert-manager.io/v1alpha2
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
            class: traefik
```

> [!NOTE]
> Test için, *Kümevereninizi* için kullanabileceğiniz bir [hazırlama sunucusu][letsencrypt-staging-issuer] da vardır.

`kubectl`Uygulamak için kullanın `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Önceki *traefik* *Clusterrole* ve *clusterrolebinding*' i KALDıRıN, sonra traefik öğesini kullanarak https kullanacak şekilde yükseltin `helm` .

> [!NOTE]
> AKS kümenizde Kubernetes RBAC etkinleştirilmemişse, *--set RBAC. Enabled = true* parametresini kaldırın.

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

[Kubectl Get][kubectl-get]kullanarak traefik giriş denetleyici hizmetinin güncelleştirilmiş IP adresini alın.

```console
kubectl get svc -n traefik --watch
```

Örnek çıktı, *traefik* ad alanındaki tüm hizmetlerin IP adreslerini gösterir.

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

[Az Network DNS Record-bir Add-Record ayarla][az-network-dns-record-set-a-add-record] ve önceki *A* kaydını kaldır az Network DNS [Record-set a Remove-Record][az-network-dns-record-set-a-remove-record]kullanılarak DNS bölgenize yeni *bir kayıt ekleyin* .

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

Yukarıdaki örnek, *PREVIOUS_EXTERNAL_IP* kullanmak için *MY_CUSTOM_DOMAIN* DNS bölgesindeki *bir* kaydı güncelleştirir.

, *CERT-Manager* ve https kullanımına ilişkin ayrıntıları dahil etmek için [values. YAML][values-yaml] 'yi güncelleştirin. Aşağıda güncelleştirilmiş bir dosyaya örnek verilmiştir `values.yaml` :

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Şunu kullanarak örnek uygulamayı yükseltin `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

*Geliştirme/azureuser1* alt alanındaki örnek uygulamaya gıdın ve HTTPS kullanmak üzere yönlendirildiğini unutmayın.

> [!IMPORTANT]
> DNS değişikliklerinin tamamlanması ve örnek uygulamanızın erişilebilir olması 30 dakika veya daha uzun sürebilir.

Ayrıca sayfanın yüklendiğine, ancak tarayıcıda bazı hataların gösterildiğine dikkat edin. Tarayıcı konsolu 'nu açmak, HTTP kaynaklarını yüklemeye çalışan bir HTTPS sayfasıyla ilgili hatayı gösterir. Örnek:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Bu hatayı onarmak için [Bıkesharingweb/azds. YAML][azds-yaml] 'yi, *Kubernetes.io/ingress.Class* için *Traefik* ve *$ (hostsuffix)* özel etki alanınızı kullanacak şekilde güncelleştirin. Örnek:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

[Bıkesharingweb/package.js][package-json] *'yi URL* paketine yönelik bir bağımlılık ile güncelleştirin.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

[Bıkesharingweb/lib/helpers.js][helpers-js] Içindeki *Getapihostasync* yöntemini HTTPS kullanacak şekilde güncelleştirin:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

`BikeSharingWeb`Dizinine gidin ve `azds up` güncelleştirilmiş BikeSharingWeb hizmetinizi çalıştırmak için kullanın.

```console
cd ../BikeSharingWeb/
azds up
```

*Geliştirme/azureuser1* alt alanındaki örnek uygulamaya gidin ve herhangi bir hata olmadan https kullanmaya yönlendirildiğini görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces nasıl çalıştığı hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces nasıl çalışır?](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az_network_dns_record_set_a_add_record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a#az_network_dns_record_set_a_remove_record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
