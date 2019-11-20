---
title: Azure Kubernetes Service (AKS) üzerinde HTTP uygulama yönlendirmesi eklentisi
description: Azure Kubernetes Service (AKS) üzerinde HTTP uygulama yönlendirme eklentisini kullanın.
services: container-service
author: lachie83
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/06/2019
ms.author: laevenso
ms.openlocfilehash: f0975d0a60081b66d3d5a513954deb0c4fa1b978
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851545"
---
# <a name="http-application-routing"></a>HTTP uygulama yönlendirmesi

HTTP uygulama yönlendirme çözümü, Azure Kubernetes Service (AKS) kümenize dağıtılan uygulamalara erişimi kolaylaştırır. Çözüm etkinleştirildiğinde, AKS kümenizde bir giriş [denetleyicisi](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/) yapılandırır. Uygulamalar dağıtıldığında, çözüm uygulama uç noktaları için genel olarak erişilebilen DNS adları da oluşturur.

Eklenti etkinleştirildiğinde, aboneliğinizde bir DNS bölgesi oluşturur. DNS maliyeti hakkında daha fazla bilgi için bkz. [DNS fiyatlandırması][dns-pricing].

> [!CAUTION]
> HTTP uygulama yönlendirme eklentisi, hızlı bir şekilde giriş denetleyicisi oluşturup uygulamalarınıza erişim sağlamak için tasarlanmıştır. Bu eklenti, üretim kullanımı için önerilmez. Birden çok çoğaltma ve TLS desteği içeren üretime yönelik giriş dağıtımları için bkz. HTTPS giriş [denetleyicisi oluşturma](https://docs.microsoft.com/azure/aks/ingress-tls).

## <a name="http-routing-solution-overview"></a>HTTP Yönlendirme çözümüne genel bakış

Eklenti iki bileşeni dağıtır: bir [Kubernetes giriş denetleyicisi][ingress] ve BIR [dış DNS][external-dns] denetleyicisi.

- Giriş **denetleyicisi**: Giriş denetleyicisi, yük dengeleyici türünde bir Kubernetes hizmeti kullanılarak internet 'e açıktır. Giriş denetleyicisi, uygulama uç noktalarına yollar oluşturan [Kubernetes giriş kaynaklarını][ingress-resource]izler ve uygular.
- **Dış-DNS denetleyicisi**: Kubernetes giriş kaynaklarını izler ve kümeye özgü DNS bölgesinde DNS A kayıtları oluşturur.

## <a name="deploy-http-routing-cli"></a>HTTP yönlendirmeyi dağıtma: CLI

HTTP uygulama yönlendirme eklentisi, bir AKS kümesi dağıtımında Azure CLı ile etkinleştirilebilir. Bunu yapmak için, [az aks Create][az-aks-create] komutunu `--enable-addons` bağımsız değişkeniyle birlikte kullanın.

```azurecli
az aks create --resource-group myResourceGroup --name myAKSCluster --enable-addons http_application_routing
```

> [!TIP]
> Çoklu eklentileri etkinleştirmek istiyorsanız, bunları virgülle ayrılmış bir liste olarak sağlayın. Örneğin, HTTP uygulama yönlendirmeyi ve izlemeyi etkinleştirmek için biçimini `--enable-addons http_application_routing,monitoring`kullanın.

[Az aks Enable-addons][az-aks-enable-addons] komutunu kullanarak var olan bir aks kümesinde http yönlendirmeyi de etkinleştirebilirsiniz. Mevcut bir kümede http yönlendirmeyi etkinleştirmek için `--addons` parametreyi ekleyin ve aşağıdaki örnekte gösterildiği gibi *http_application_routing* belirtin:

```azurecli
az aks enable-addons --resource-group myResourceGroup --name myAKSCluster --addons http_application_routing
```

Küme dağıtıldıktan veya güncelleştirildikten sonra, DNS bölgesi adını almak için [az aks Show][az-aks-show] komutunu kullanın. Bu ad, uygulamaları AKS kümesine dağıtmak için gereklidir.

```azurecli
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query addonProfiles.httpApplicationRouting.config.HTTPApplicationRoutingZoneName -o table

Result
-----------------------------------------------------
9f9c1fe7-21a1-416d-99cd-3543bb92e4c3.eastus.aksapp.io
```

## <a name="deploy-http-routing-portal"></a>HTTP yönlendirmeyi dağıtma: Portal

HTTP uygulama yönlendirme eklentisi bir AKS kümesi dağıtıldığında Azure portal aracılığıyla etkinleştirilebilir.

![HTTP yönlendirme özelliğini etkinleştirme](media/http-routing/create.png)

Küme dağıtıldıktan sonra otomatik oluşturulan AKS kaynak grubuna gidin ve DNS bölgesini seçin. DNS bölgesi adı ' nı bir yere göz atın. Bu ad, uygulamaları AKS kümesine dağıtmak için gereklidir.

![DNS bölgesi adını al](media/http-routing/dns.png)

## <a name="use-http-routing"></a>HTTP yönlendirmeyi kullanma

HTTP uygulama yönlendirme çözümü, yalnızca aşağıdaki şekilde ek olarak açıklanan giriş kaynaklarında tetiklenebilir:

```yaml
annotations:
  kubernetes.io/ingress.class: addon-http-application-routing
```

**Samples-http-Application-Routing. YAML** adlı bir dosya oluşturun ve aşağıdaki YAML 'ye kopyalayın. Satır 43 ' de, `<CLUSTER_SPECIFIC_DNS_ZONE>` Bu makalenin önceki adımında toplanan DNS bölge adıyla güncelleştirin.


```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: party-clippy
spec:
  template:
    metadata:
      labels:
        app: party-clippy
    spec:
      containers:
      - image: r.j3ss.co/party-clippy
        name: party-clippy
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        tty: true
        command: ["party-clippy"]
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: party-clippy
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 8080
  selector:
    app: party-clippy
  type: ClusterIP
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: party-clippy
  annotations:
    kubernetes.io/ingress.class: addon-http-application-routing
spec:
  rules:
  - host: party-clippy.<CLUSTER_SPECIFIC_DNS_ZONE>
    http:
      paths:
      - backend:
          serviceName: party-clippy
          servicePort: 80
        path: /
```

Kaynakları oluşturmak için [kubectl Apply][kubectl-apply] komutunu kullanın.

```bash
$ kubectl apply -f samples-http-application-routing.yaml

deployment "party-clippy" created
service "party-clippy" created
ingress "party-clippy" created
```

Samples-http-Application-Routing. YAML dosyasının ana bilgisayar bölümünde belirtilen ana bilgisayar adına gitmek için kıvrımlı veya tarayıcı kullanın. Uygulama, internet üzerinden kullanılabilir hale gelmeden önce bir dakika sürebilir.

```bash
$ curl party-clippy.471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io

 _________________________________
/ It looks like you're building a \
\ microservice.                   /
 ---------------------------------
 \
  \
     __
    /  \
    |  |
    @  @
    |  |
    || |/
    || ||
    |\_/|
    \___/

```

## <a name="remove-http-routing"></a>HTTP yönlendirmeyi kaldır

HTTP yönlendirme çözümü, Azure CLı kullanılarak kaldırılabilir. Bunu yapmak için, AKS kümenizi ve kaynak grubu adını değiştirerek aşağıdaki komutu çalıştırın.

```azurecli
az aks disable-addons --addons http_application_routing --name myAKSCluster --resource-group myResourceGroup --no-wait
```

HTTP uygulama yönlendirme eklentisi devre dışı bırakıldığında, bazı Kubernetes kaynakları kümede kalabilir. Bu kaynaklar *Configmaps* ve *gizli*dizileri içerir ve *Kuto-System* ad alanında oluşturulur. Temiz bir kümeyi sürdürmek için bu kaynakları kaldırmak isteyebilirsiniz.

Aşağıdaki [kubectl Get][kubectl-get] komutlarını kullanarak *addon-http-Application-Routing* kaynaklarını arayın:

```console
kubectl get deployments --namespace kube-system
kubectl get services --namespace kube-system
kubectl get configmaps --namespace kube-system
kubectl get secrets --namespace kube-system
```

Aşağıdaki örnek çıktıda silinmesi gereken configMaps gösterilmektedir:

```
$ kubectl get configmaps --namespace kube-system

NAMESPACE     NAME                                                       DATA   AGE
kube-system   addon-http-application-routing-nginx-configuration         0      9m7s
kube-system   addon-http-application-routing-tcp-services                0      9m7s
kube-system   addon-http-application-routing-udp-services                0      9m7s
```

Kaynakları silmek için [kubectl Delete][kubectl-delete] komutunu kullanın. Kaynak türünü, kaynak adını ve ad alanını belirtin. Aşağıdaki örnek, önceki configmaps birini siler:

```console
kubectl delete configmaps addon-http-application-routing-nginx-configuration --namespace kube-system
```

Kümenizde kalan tüm `kubectl delete` *addon-http-Application-Routing* kaynakları için önceki adımı tekrarlayın.

## <a name="troubleshoot"></a>Sorun giderme

Dış DNS uygulamasının uygulama günlüklerini görüntülemek için [kubectl logs][kubectl-logs] komutunu kullanın. Günlükler bir ve TXT DNS kaydının başarıyla oluşturulduğunu onaylamasını sağlamalıdır.

```
$ kubectl logs -f deploy/addon-http-application-routing-external-dns -n kube-system

time="2018-04-26T20:36:19Z" level=info msg="Updating A record named 'party-clippy' to '52.242.28.189' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
time="2018-04-26T20:36:21Z" level=info msg="Updating TXT record named 'party-clippy' to '"heritage=external-dns,external-dns/owner=default"' for Azure DNS zone '471756a6-e744-4aa0-aa01-89c4d162a7a7.canadaeast.aksapp.io'."
```

Bu kayıtlar, Azure portal DNS bölge kaynağında de görülebilir.

![DNS kayıtlarını al](media/http-routing/clippy.png)

NGINX giriş denetleyicisinin uygulama günlüklerini görüntülemek için [kubectl logs][kubectl-logs] komutunu kullanın. Günlükler, bir giriş kaynağını `CREATE` ve denetleyicinin yeniden yüklenmesini doğrulayabilmelidir. Tüm HTTP etkinlikleri günlüğe kaydedilir.

```bash
$ kubectl logs -f deploy/addon-http-application-routing-nginx-ingress-controller -n kube-system

-------------------------------------------------------------------------------
NGINX Ingress controller
  Release:    0.13.0
  Build:      git-4bc943a
  Repository: https://github.com/kubernetes/ingress-nginx
-------------------------------------------------------------------------------

I0426 20:30:12.212936       9 flags.go:162] Watching for ingress class: addon-http-application-routing
W0426 20:30:12.213041       9 flags.go:165] only Ingress with class "addon-http-application-routing" will be processed by this ingress controller
W0426 20:30:12.213505       9 client_config.go:533] Neither --kubeconfig nor --master was specified.  Using the inClusterConfig.  This might not work.
I0426 20:30:12.213752       9 main.go:181] Creating API client for https://10.0.0.1:443
I0426 20:30:12.287928       9 main.go:225] Running in Kubernetes Cluster version v1.8 (v1.8.11) - git (clean) commit 1df6a8381669a6c753f79cb31ca2e3d57ee7c8a3 - platform linux/amd64
I0426 20:30:12.290988       9 main.go:84] validated kube-system/addon-http-application-routing-default-http-backend as the default backend
I0426 20:30:12.294314       9 main.go:105] service kube-system/addon-http-application-routing-nginx-ingress validated as source of Ingress status
I0426 20:30:12.426443       9 stat_collector.go:77] starting new nginx stats collector for Ingress controller running in namespace  (class addon-http-application-routing)
I0426 20:30:12.426509       9 stat_collector.go:78] collector extracting information from port 18080
I0426 20:30:12.448779       9 nginx.go:281] starting Ingress controller
I0426 20:30:12.463585       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-nginx-configuration", UID:"2588536c-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"559", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-nginx-configuration
I0426 20:30:12.466945       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-tcp-services", UID:"258ca065-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"561", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-tcp-services
I0426 20:30:12.467053       9 event.go:218] Event(v1.ObjectReference{Kind:"ConfigMap", Namespace:"kube-system", Name:"addon-http-application-routing-udp-services", UID:"259023bc-4990-11e8-a5e1-0a58ac1f0ef2", APIVersion:"v1", ResourceVersion:"562", FieldPath:""}): type: 'Normal' reason: 'CREATE' ConfigMap kube-system/addon-http-application-routing-udp-services
I0426 20:30:13.649195       9 nginx.go:302] starting NGINX process...
I0426 20:30:13.649347       9 leaderelection.go:175] attempting to acquire leader lease  kube-system/ingress-controller-leader-addon-http-application-routing...
I0426 20:30:13.649776       9 controller.go:170] backend reload required
I0426 20:30:13.649800       9 stat_collector.go:34] changing prometheus collector from  to default
I0426 20:30:13.662191       9 leaderelection.go:184] successfully acquired lease kube-system/ingress-controller-leader-addon-http-application-routing
I0426 20:30:13.662292       9 status.go:196] new leader elected: addon-http-application-routing-nginx-ingress-controller-5cxntd6
I0426 20:30:13.763362       9 controller.go:179] ingress backend successfully reloaded...
I0426 21:51:55.249327       9 event.go:218] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"default", Name:"party-clippy", UID:"092c9599-499c-11e8-a5e1-0a58ac1f0ef2", APIVersion:"extensions", ResourceVersion:"7346", FieldPath:""}): type: 'Normal' reason: 'CREATE' Ingress default/party-clippy
W0426 21:51:57.908771       9 controller.go:775] service default/party-clippy does not have any active endpoints
I0426 21:51:57.908951       9 controller.go:170] backend reload required
I0426 21:51:58.042932       9 controller.go:179] ingress backend successfully reloaded...
167.220.24.46 - [167.220.24.46] - - [26/Apr/2018:21:53:20 +0000] "GET / HTTP/1.1" 200 234 "" "Mozilla/5.0 (compatible; MSIE 9.0; Windows NT 6.1; Trident/5.0)" 197 0.001 [default-party-clippy-80] 10.244.0.13:8080 234 0.004 200
```

## <a name="clean-up"></a>Temizleme

Bu makalede oluşturulan ilişkili Kubernetes nesnelerini kaldırın.

```bash
$ kubectl delete -f samples-http-application-routing.yaml

deployment "party-clippy" deleted
service "party-clippy" deleted
ingress "party-clippy" deleted
```

## <a name="next-steps"></a>Sonraki adımlar

AKS 'de HTTPS güvenli giriş denetleyicisi 'nin nasıl yükleneceği hakkında bilgi için bkz. [Azure Kubernetes Service (aks) üzerinde https][ingress-https]girişi.

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[ingress-https]: ./ingress-tls.md
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons


<!-- LINKS - external -->
[dns-pricing]: https://azure.microsoft.com/pricing/details/dns/
[external-dns]: https://github.com/kubernetes-incubator/external-dns
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[ingress]: https://kubernetes.io/docs/concepts/services-networking/ingress/
[ingress-resource]: https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
