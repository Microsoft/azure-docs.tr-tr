---
title: Azure Kubernetes Service (AKS) kümesiyle giriş için kendi TLS sertifikalarınızı kullanın
description: Azure Kubernetes Service (AKS) kümesinde kendi sertifikalarınızı kullanan bir NGıNX giriş denetleyicisi yüklemeyi ve yapılandırmayı öğrenin.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: fc7f2180e4166070fe44863aed2b12135b0db8ee
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097854"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) üzerinde bir HTTPS giriş denetleyicisi oluşturun ve kendi TLS sertifikalarınızı kullanın

Giriş denetleyicisi, Kubernetes Hizmetleri için ters proxy, yapılandırılabilir trafik yönlendirme ve TLS sonlandırma sağlayan bir yazılım parçasıdır. Kubernetes giriş kaynakları, bağımsız bir Kubernetes hizmeti için giriş kurallarını ve rotaları yapılandırmak üzere kullanılır. Bir giriş denetleyicisi ve giriş kuralları kullanarak, bir Kubernetes kümesinde trafiği birden çok hizmete yönlendirmek için tek bir IP adresi kullanılabilir.

Bu makalede bir Azure Kubernetes Service (AKS) kümesinde [NGINX giriş denetleyicisinin][nginx-ingress] nasıl dağıtılacağı gösterilir. Kendi sertifikalarınızı oluşturur ve giriş rotası ile kullanmak üzere bir Kubernetes gizli dizisi oluşturun. Son olarak, her biri tek bir IP adresi üzerinden erişilebilen AKS kümesinde iki uygulama çalıştırılır.

Aşağıdakileri de yapabilirsiniz:

- [Dış ağ bağlantısı ile temel bir giriş denetleyicisi oluşturma][aks-ingress-basic]
- [HTTP uygulama yönlendirme eklentisini etkinleştirin][aks-http-app-routing]
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, NGıNX giriş denetleyicisini ve örnek bir Web uygulamasını yüklemek için Held kullanılmaktadır. AKS kümenizde helk başlatılmış ve Tiller için bir hizmet hesabı kullanmanız gerekir. Held 'nin en son sürümünü kullandığınızdan emin olun. Yükseltme yönergeleri için, bkz. [hela Install docs][helm-install]. Held 'yi yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [Azure Kubernetes hizmeti 'nde (AKS) Held ile uygulama yüklemesi][use-helm].

Bu makalede, Azure CLı sürüm 2.0.64 veya üstünü de çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Giriş denetleyicisi oluşturma

Giriş denetleyicisini oluşturmak için, `Helm` *NGINX-giriş*yüklemek üzere kullanın. Ek artıklık için, NGINX giriş denetleyicilerinin iki yinelemesi `--set controller.replicaCount` parametresiyle dağıtılır. Giriş denetleyicisinin Çoğaltmalarından tamamen yararlanmak için AKS kümenizde birden fazla düğüm olduğundan emin olun.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Yükleme sırasında, giriş denetleyicisi için bir Azure genel IP adresi oluşturulur. Bu genel IP adresi, giriş denetleyicisinin yaşam döngüsü için statiktir. Giriş denetleyicisini silerseniz, genel IP adresi ataması kaybedilir. Daha sonra ek bir giriş denetleyicisi oluşturursanız, yeni bir genel IP adresi atanır. Genel IP adresinin kullanımını sürdürmek istiyorsanız, bunun yerine [statik bir genel IP adresi olan bir giriş denetleyicisi oluşturabilirsiniz][aks-ingress-static-tls].

Genel IP adresini almak için `kubectl get service` komutunu kullanın. IP adresinin hizmete atanması birkaç dakika sürer.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Dağıtımı test etmek için son adımda kullanıldığından, bu genel IP adresini bir yere unutmayın.

Henüz giriş kuralı oluşturulmadı. Genel IP adresine gözattığınızda NGıNX giriş denetleyicisinin varsayılan 404 sayfası görüntülenir.

## <a name="generate-tls-certificates"></a>TLS sertifikaları oluşturma

Bu makalede, ile `openssl`otomatik olarak imzalanan bir sertifika oluşturalım. Üretim kullanımı için, bir sağlayıcı veya kendi sertifika yetkiliniz (CA) aracılığıyla güvenilir ve imzalı bir sertifika istemeniz gerekir. Bir sonraki adımda, bir Kubernetes *gizli* anahtarını, OpenSSL tarafından oluşturulan TLS sertifikasını ve özel anahtarı kullanarak oluşturabilirsiniz.

Aşağıdaki örnek, *aks-ingress-TLS. CRT*adlı 365 gün için geçerli 2048 BITLIK bir RSA x509 sertifikası üretir. Özel anahtar dosyası *aks-ingress-TLS. Key*olarak adlandırılmıştır. Bir Kubernetes TLS parolası, bu dosyaların her ikisini de gerektirir.

Bu makale, *demo.Azure.com* konu ortak adı ile birlikte çalışarak, değiştirilmesi gerekmez. Üretim kullanımı için, `-subj` parametre için kendi kuruluş değerlerinizi belirtin:

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

Her iki uygulama da artık Kubernetes kümenizde çalışıyor, ancak bunlar türünde `ClusterIP`bir hizmetle yapılandırılmış. Bu nedenle, uygulamalara internet 'ten erişilemez. Bunları herkese açık hale getirmek için bir Kubernetes giriş kaynağı oluşturun. Giriş kaynağı, trafiği iki uygulamadan birine yönlendiren kuralları yapılandırır.

Aşağıdaki örnekte, adrese `https://demo.azure.com/` olan trafik adlı `aks-helloworld`hizmete yönlendirilir. Adrese `https://demo.azure.com/hello-world-two` giden trafik `ingress-demo` hizmete yönlendirilir. Bu makalede, bu demo ana bilgisayar adlarını değiştirmeniz gerekmez. Üretim kullanımı için, sertifika isteği ve oluşturma sürecinin bir parçası olarak belirtilen adları sağlayın.

> [!TIP]
> Sertifika isteği işlemi sırasında belirtilen ana bilgisayar adı, CN adı, giriş rotasında tanımlanan konakla eşleşmiyorsa, denetleyici bir *Kubernetes ınress denetleyicisi sahte sertifika* uyarısı görüntüler. Sertifikanızın ve giriş yolu ana bilgisayar adlarının eşleştiğinden emin olun.

*TLS* bölümü, giriş yoluna, ana bilgisayar *demo.Azure.com*için *aks-giriş-TLS* adlı gizli dizi kullanımını söyler. Daha sonra, üretim kullanımı için kendi ana bilgisayar adresinizi belirtin.

Adlı `hello-world-ingress.yaml` bir dosya oluşturun ve aşağıdaki örnekteki YAML 'yi kopyalayın.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
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

## <a name="test-the-ingress-configuration"></a>Giriş yapılandırmasını test etme

Sahte *demo.Azure.com* barındırımızda sertifikaları test etmek için kullanın `curl` ve *--Resolve* parametresini belirtin. Bu parametre, *demo.Azure.com* adını giriş DENETLEYICINIZIN genel IP adresi ile eşlemenizi sağlar. Aşağıdaki örnekte gösterildiği gibi kendi giriş denetleyicinizin genel IP adresini belirtin:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Adresle birlikte ek bir yol sağlanmadı, bu nedenle giriş denetleyicisi */* rotayı varsayılan olarak alır. İlk demo uygulaması aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi döndürülür:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Komutumuz `curl` *-v* parametresi, alınan TLS sertifikası da dahil olmak üzere ayrıntılı bilgiler verir. Kıvrımlı çıkışınızla yarım yönlü, kendi TLS sertifikanızın kullanıldığını doğrulayabilirsiniz. Otomatik olarak imzalanan bir sertifika kullanıyor olsanız da *-k* parametresi sayfayı yüklemeye devam eder. Aşağıdaki örnek, *veren 'in şunları göstermektedir: CN = demo. Azure. com; O = aks-ingress-TLS* sertifikası kullanıldı:

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

Şimdi adrese */Hello-World-iki* yolu ekleyin, örneğin `https://demo.azure.com/hello-world-two`. Aşağıdaki sıkıştırılmış örnek çıktıda gösterildiği gibi, özel başlığa sahip ikinci demo uygulaması döndürülür:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

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

Ardından, AKS Hello World uygulamasının Held deposunu kaldırın:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Kaynakları tek tek Sil

Alternatif olarak, oluşturulan kaynakları tek tek silmek daha ayrıntılı bir yaklaşımdır. `helm list` Komut ile Held sürümlerini listeleyin. Aşağıdaki örnek çıktıda gösterildiği gibi *NGINX-ingress* ve *aks-HelloWorld*adlı grafikleri arayın:

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

`helm delete` Komutuyla sürümleri silin. Aşağıdaki örnekte NGıNX giriş dağıtımı ve iki örnek AKS Hello World Apps de silinir.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ardından, AKS Hello World uygulamasının Held deposunu kaldırın:

```console
helm repo remove azure-samples
```

Trafiği örnek uygulamalara yönlendiren giriş yolunu kaldırın:

```console
kubectl delete -f hello-world-ingress.yaml
```

Sertifika gizliliğini silin:

```console
kubectl delete secret aks-ingress-tls
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
- [İç, özel ağ ve IP adresi kullanan bir giriş denetleyicisi oluşturun][aks-ingress-internal]
- [Dinamik bir genel IP adresi][aks-ingress-tls] veya [STATIK bir genel IP adresi][aks-ingress-static-tls] ile otomatik olarak TLS sertifikaları oluşturmak için şifrelemeyi kullanan bir giriş denetleyicisi oluşturun

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
