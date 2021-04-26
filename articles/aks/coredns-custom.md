---
title: Azure Kubernetes hizmeti (AKS) için CoreDNS 'i özelleştirme
description: Azure Kubernetes Service (AKS) kullanarak alt etki alanları eklemek veya özel DNS uç noktalarını genişletmek için CoreDNS 'i özelleştirmeyi öğrenin
services: container-service
author: palma21
ms.topic: article
ms.date: 03/15/2019
ms.author: jpalma
ms.openlocfilehash: 5b13931bc6a13d988c21f728b996c51270769e0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368690"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Azure Kubernetes Service ile CoreDNS hizmetini özelleştirme

Azure Kubernetes hizmeti (AKS), küme DNS yönetimi için [coredns][coredns] projesini ve tüm *1.12. x* ve üzeri kümeleriyle çözümü kullanır. Daha önce kuin-DNS projesi kullanıldı. Bu Kuto-DNS projesi artık kullanım dışıdır. CoreDNS özelleştirmesi ve Kubernetes hakkında daha fazla bilgi için bkz. [resmi yukarı akış belgeleri][corednsk8s].

AKS yönetilen bir hizmet olduğundan, CoreDNS (bir *Corefile*) ana yapılandırmasını değiştiremezsiniz. Bunun yerine, varsayılan ayarları geçersiz kılmak için bir Kubernetes *Configmap* kullanın. Varsayılan AKS CoreDNS ConfigMaps öğesini görmek için `kubectl get configmaps --namespace=kube-system coredns -o yaml` komutunu kullanın.

Bu makalede, AKS 'de CoreDNS 'in temel özelleştirme seçenekleri için ConfigMaps 'ın nasıl kullanılacağı gösterilir. Bu yaklaşım, CoreFile 'ı kullanarak diğer bağlamlarda CoreDNS 'in yapılandırılmasını farklılık gösterir. Yapılandırma değerleri sürümler arasında değişolabileceğinden, çalıştırdığınız CoreDNS sürümünü doğrulayın.

> [!NOTE]
> `kube-dns` bir Kubernetes yapılandırma eşlemesi aracılığıyla farklı [özelleştirme seçenekleri][kubednsblog] sunuluyor. CoreDNS, KUIN-DNS ile geriye dönük olarak uyumlu **değildir** . Daha önce kullandığınız tüm özelleştirmeler CoreDNS ile kullanım için güncelleştirilmeleri gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede, mevcut bir AKS kümeniz olduğunu varsaymaktadır. AKS kümesine ihtiyacınız varsa bkz. [Azure CLI kullanarak][aks-quickstart-cli] aks hızlı başlangıç veya [Azure Portal kullanımı][aks-quickstart-portal].

Aşağıdaki örneklerde olduğu gibi bir yapılandırma oluştururken, *veri* bölümündeki adlarınızın *. Server* veya *. override* ile bitmesi gerekir. Bu adlandırma kuralı, komutunu kullanarak görüntüleyebileceğiniz varsayılan AKS CoreDNS Configmap ' de tanımlanmıştır `kubectl get configmaps --namespace=kube-system coredns -o yaml` .

## <a name="what-is-supportedunsupported"></a>Desteklenir/desteklenmez

Tüm yerleşik CoreDNS eklentileri desteklenir. Eklenti/üçüncü taraf eklentileri desteklenmez.

## <a name="rewrite-dns"></a>DNS 'i yeniden yaz

Sahip olduğunuz bir senaryo, anlık DNS adını yeniden yazar işlemini gerçekleştirmenizi sağlar. Aşağıdaki örnekte, `<domain to be written>` kendi tam etki alanı adınızla değiştirin. Adlı bir dosya oluşturun `corednsms.yaml` ve aşağıdaki örnek yapılandırmayı yapıştırın:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> CoreDNS hizmet IP 'si gibi bir DNS sunucusuna yönlendirirseniz, DNS sunucusu, yeniden yazan etki alanı adını çözümleyebilmelidir.

[Kubectl Apply configmap][kubectl-apply] komutunu kullanarak configmap 'i oluşturun ve YAML bildiriminizde adı belirtin:

```console
kubectl apply -f corednsms.yaml
```

Özelleştirmelerin uygulandığını doğrulamak için [kubectl Get configmaps][kubectl-get] ' i kullanın ve *coredns-Custom* configmap 'nizi belirtin:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Şimdi CoreDNS 'i ConfigMap 'i yeniden yüklemeye zorlayın. [Kubectl Delete Pod][kubectl delete] komutu bozucu değil ve zamana neden olmaz. `kube-dns`Pod 'ler silinir ve Kubernetes Zamanlayıcı bunları yeniden oluşturur. Bu yeni FID 'ler TTL değerindeki değişikliği içerir.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Yukarıdaki komutu doğru. Değişiklik yaptığımız sırada `coredns` dağıtım **kuin-DNS** adı altındadır.

## <a name="custom-forward-server"></a>Özel ileri sunucu

Ağ trafiğiniz için bir iletme sunucusu belirtmeniz gerekiyorsa, DNS 'yi özelleştirmek için bir ConfigMap oluşturabilirsiniz. Aşağıdaki örnekte, `forward` ad ve adresi kendi ortamınızın değerleriyle güncelleştirin. Adlı bir dosya oluşturun `corednsms.yaml` ve aşağıdaki örnek yapılandırmayı yapıştırın:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Önceki örneklerde olduğu gibi, [kubectl Apply configmap][kubectl-apply] komutunu kullanarak configmap 'i oluşturun ve YAML bildirimin adını belirtin. Ardından, CoreDNS 'yi, Kubernetes Zamanlayıcı için [kubectl Delete Pod][kubectl delete] kullanarak configmap 'i yeniden yüklemeye zorlayın:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Özel etki alanlarını kullanma

Yalnızca dahili olarak çözümlenebileceğiniz özel etki alanlarını yapılandırmak isteyebilirsiniz. Örneğin, geçerli bir üst düzey etki alanı olmayan, *poglife. Local* özel etki alanını çözümlemek isteyebilirsiniz. Özel bir etki alanı ConfigMap olmadan AKS kümesi adresi çözemiyor.

Aşağıdaki örnekte, özel etki alanını ve IP adresini kendi ortamınızın değerleriyle doğrudan trafiğe yönlendirmek için güncelleştirin. Adlı bir dosya oluşturun `corednsms.yaml` ve aşağıdaki örnek yapılandırmayı yapıştırın:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Önceki örneklerde olduğu gibi, [kubectl Apply configmap][kubectl-apply] komutunu kullanarak configmap 'i oluşturun ve YAML bildirimin adını belirtin. Ardından, CoreDNS 'yi, Kubernetes Zamanlayıcı için [kubectl Delete Pod][kubectl delete] kullanarak configmap 'i yeniden yüklemeye zorlayın:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Saplama etki alanları

CoreDNS, saplama etki alanlarını yapılandırmak için de kullanılabilir. Aşağıdaki örnekte, özel etki alanlarını ve IP adreslerini kendi ortamınızın değerleriyle güncelleştirin. Adlı bir dosya oluşturun `corednsms.yaml` ve aşağıdaki örnek yapılandırmayı yapıştırın:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Önceki örneklerde olduğu gibi, [kubectl Apply configmap][kubectl-apply] komutunu kullanarak configmap 'i oluşturun ve YAML bildirimin adını belirtin. Ardından, CoreDNS 'yi, Kubernetes Zamanlayıcı için [kubectl Delete Pod][kubectl delete] kullanarak configmap 'i yeniden yüklemeye zorlayın:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Ana bilgisayarlar eklentisi

Tüm yerleşik eklentiler desteklenmekle birlikte, CoreDNS [ana bilgisayarları][coredns hosts] eklentisinin özelleştirmek için kullanılabilir olduğu anlamına gelir:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>DNS sorgusu hata ayıklaması için günlüğe kaydetmeyi etkinleştirme 

DNS sorgu günlüğünü etkinleştirmek için, coredns-Custom ConfigMap ' e aşağıdaki yapılandırmayı uygulayın:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, CoreDNS özelleştirmesi için bazı örnek senaryolar gösterildi. CoreDNS projesi hakkında daha fazla bilgi için bkz. [coredns yukarı akış projesi sayfası][coredns].

Çekirdek ağ kavramları hakkında daha fazla bilgi için bkz. [AKS 'teki uygulamalar Için ağ kavramları][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
