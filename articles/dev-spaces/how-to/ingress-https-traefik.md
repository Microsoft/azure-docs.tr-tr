---
title: Özel bir traefik giriş denetleyicisi kullanma ve https 'yi yapılandırma
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Azure Dev Spaces özel bir traefik ingınress denetleyicisi kullanmak ve bu giriş denetleyicisini kullanarak HTTPS 'yi yapılandırmak için nasıl yapılandırılacağını öğrenin
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes hizmeti, kapsayıcılar, Held, hizmet ağı, hizmet kafesi yönlendirme, kubectl, k8s
ms.openlocfilehash: c015fe8e7108f07d66d2464c4f8b6287e8f54446
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582318"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Özel bir traefik giriş denetleyicisi kullanma ve https 'yi yapılandırma

Bu makalede, Azure Dev Spaces özel bir traefik ingınress denetleyicisi kullanmak üzere nasıl yapılandırılacağı gösterilmektedir. Bu makalede ayrıca, bu özel giriş denetleyicisinin HTTPS kullanmak üzere nasıl yapılandırılacağı gösterilmektedir.

## <a name="prerequisites"></a>Ön koşullar

* Azure aboneliği. Hesabınız yoksa [ücretsiz bir hesap][azure-account-create] oluşturabilirsiniz.
* [Yüklü Azure CLI][az-cli].
* [Azure dev Spaces etkinleştirilmiş Azure Kubernetes hizmeti (AKS) kümesi][qs-cli].
* [kubectl][kubectl] yüklendi.
* [Held 2,13 veya üzeri yüklü][helm-installed].
* AKS kümeniz ile aynı kaynak grubunda [DNS bölgesi][dns-zone] olan [özel bir etki alanı][custom-domain] .

## <a name="configure-a-custom-traefik-ingress-controller"></a>Özel bir traefik giriş denetleyicisi yapılandırma

Kubernetes komut satırı istemcisi olan [kubectl][kubectl]kullanarak kümenize bağlanın. Kubernetes kümenize bağlanmak üzere `kubectl` yapılandırmak için [az aks Get-Credentials][az-aks-get-credentials] komutunu kullanın. Bu komut, kimlik bilgilerini indirir ve Kubernetes CLı 'yi bunları kullanacak şekilde yapılandırır.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Kümenize bağlantıyı doğrulamak için [kubectl get][kubectl-get] komutunu kullanarak küme düğümleri listesini alın.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Traefik giriş denetleyicisi için bir Kubernetes ad alanı oluşturun ve `helm`kullanarak yüklemeyi yapın.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

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

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Yukarıdaki örnek, *MY_CUSTOM_DOMAIN* DNS bölgesine *bir* kayıt ekler.

Bu makalede, Azure Dev Spaces kullanmayı göstermek için [Azure dev Spaces bisiklet paylaşımı örnek uygulamasını](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) kullanacaksınız. Uygulamayı GitHub 'dan kopyalayıp dizinine gidin:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

[Values. YAML][values-yaml] dosyasını açın ve tüm *< REPLACE_ME_WITH_HOST_SUFFIX >* örneklerini traefik ile değiştirin *. MY_CUSTOM_DOMAIN* *için etki*alanınızı kullanma. Ayrıca, *Kubernetes.io/ingress.class: traefik # özel*giriş ile *Kubernetes.io/ingress.class: traefik-azds # dev Spaces öğesine özgüdür* . Güncelleştirilmiş `values.yaml` dosyasına bir örnek aşağıda verilmiştir:

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

Örnek uygulamayı `helm install`kullanarak dağıtın.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

Yukarıdaki örnek, örnek uygulamayı *dev* ad alanına dağıtır.

`azds space select` kullanarak örnek uygulamanıza *geliştirme* alanını seçin ve `azds list-uris`kullanarak örnek uygulamaya erişmek Için URL 'leri görüntüleyin.

```console
azds space select -n dev
azds list-uris
```

Aşağıdaki çıktıda `azds list-uris`örnek URL 'Ler gösterilmektedir.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

`azds list-uris` komutundan ortak URL 'yi açarak *bıkesharingweb* hizmetine gidin. Yukarıdaki örnekte, *bıkesharingweb* hizmeti IÇIN genel URL `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

*Geliştirici* altında bir alt alan oluşturmak ve alt geliştirme alanına erişmek Için URL 'leri listelemek üzere `azds space select` komutunu kullanın.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Aşağıdaki çıktı, *azureuser1* alt geliştirme alanındaki örnek uygulamaya erişmek için `azds list-uris` ' den örnek URL 'leri gösterir.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

`azds list-uris` komutundan ortak URL 'yi açarak *azureuser1* alt geliştirme alanında *bıkesharingweb* hizmetine gidin. Yukarıdaki örnekte, *azureuser1* alt dev Space 'teki *bıkesharingweb* hizmeti için genel URL `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Traefik giriş denetleyicisini HTTPS kullanacak şekilde yapılandırma

Aşağıdaki örneğe benzer bir `dev-spaces/samples/BikeSharingApp/traefik-values.yaml` dosyası oluşturun. *E* -posta değerini, kendi e-postanız ile güncelleştirin ve bu, sertifikayı oluşturmak için kullanılır.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

`helm repo update` kullanarak *traefik* hizmetinizi güncelleştirin ve oluşturduğunuz *traefik-values. YAML* dosyasını dahil edin.

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

Yukarıdaki komut, *traefik-values. YAML* değerlerini kullanarak traefik hizmetinin yeni bir sürümünü çalıştırır ve önceki hizmeti kaldırır. Ayrıca, traefik hizmeti şifrelemeyi kullanarak bir TLS sertifikası oluşturur ve HTTPS kullanmak için Web trafiğini yeniden yönlendirmeye başlar.

> [!NOTE]
> Traefik hizmetinin yeni sürümünün başlatılması birkaç dakika sürebilir. İlerlemeyi `kubectl get pods --namespace traefik --watch`kullanarak kontrol edebilirsiniz.

*Geliştirme/azureuser1* alt alanındaki örnek uygulamaya gıdın ve HTTPS kullanmak üzere yönlendirildiğini unutmayın. Ayrıca sayfanın yüklendiğine, ancak tarayıcıda bazı hataların gösterildiğine dikkat edin. Tarayıcı konsolu 'nu açmak, HTTP kaynaklarını yüklemeye çalışan bir HTTPS sayfasıyla ilgili hatayı gösterir. Örneğin:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Bu hatayı onarmak için [Bıkesharingweb/azds. YAML][azds-yaml] 'yi, *Kubernetes.io/ingress.Class* için *Traefik* ve *$ (hostsuffix)* özel etki alanınızı kullanacak şekilde güncelleştirin. Örneğin:

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

[Bıkesharingweb/Package. JSON][package-json] ' i, *URL* paketi için bir bağımlılık ile güncelleştirin.

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

[Bıkesharingweb/Pages/yardımcılar. js][helpers-js] ' de bulunan *Getapihostasync* yöntemini HTTPS kullanacak şekilde güncelleştirin:

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

`BikeSharingWeb` dizinine gidin ve güncelleştirilmiş BikeSharingWeb hizmetinizi çalıştırmak için `azds up` kullanın.

```console
cd BikeSharingWeb/
azds up
```

*Geliştirme/azureuser1* alt alanındaki örnek uygulamaya gidin ve herhangi bir hata olmadan https kullanmaya yönlendirildiğini görürsünüz.

## <a name="next-steps"></a>Sonraki adımlar

Azure Dev Spaces birden çok kapsayıcı genelinde daha karmaşık uygulamalar geliştirmenize nasıl yardımcı olduğunu ve farklı alanlarda kodunuzun farklı sürümleriyle veya dallarıyla çalışarak işbirliğine dayalı geliştirmeyi nasıl kolaylaştırabileceğinizi öğrenin.

> [!div class="nextstepaction"]
> [Azure Dev Spaces 'de takım geliştirme][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml