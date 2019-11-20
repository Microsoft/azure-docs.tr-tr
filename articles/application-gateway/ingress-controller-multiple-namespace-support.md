---
title: Application Gateway giriş denetleyicisi için birden çok ad alanı desteği etkinleştir
description: Bu makalede, bir Kubernetes kümesinde Application Gateway giriş denetleyicisi ile birden çok ad alanı desteğinin nasıl etkinleştirileceği hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 83650e7cf46ec1dede5f25e32114d6469bab24be
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795552"
---
# <a name="enable-multiple-namespace-support-in-an-aks-cluster-with-application-gateway-ingress-controller"></a>Application Gateway Ingress denetleyicisi ile bir AKS kümesinde birden çok ad alanı desteğini etkinleştirme

## <a name="motivation"></a>Amacı
Kubernetes [ad alanları](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) , bir Kubernetes kümesinin bölümlenmesi ve daha büyük bir ekibin alt grupları için ayrılmasını mümkün kılar. Daha sonra bu alt takımlar daha ayrıntılı kaynak, güvenlik, yapılandırma vb. denetimlerle altyapıyı dağıtabilir ve yönetebilir. Kubernetes, bir veya daha fazla giriş kaynaklarının her bir ad alanı içinde bağımsız olarak tanımlanmasını sağlar.

Sürüm 0,7 itibariyle [Azure Application Gateway Kubernetes IngressController](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/README.md) (AGIC), içindeki olayları alabilir ve birden çok ad alanını gözlemleyebilirsiniz. AKS Yöneticisi, [uygulama ağ geçidini](https://azure.microsoft.com/services/application-gateway/) bir giriş olarak kullanmaya karar verirse, tüm ad alanları Application Gateway aynı örneğini kullanır. Tek bir giriş denetleyicisi yüklemesi, erişilebilir ad alanlarını izleyecek ve ilişkilendirildiği Application Gateway yapılandıracaktır.

AGIC 'nin 0,7 sürümü, Helm yapılandırmasındaki bir veya daha fazla farklı ad alanına açıkça değiştirilmediği sürece `default` ad alanını özel olarak gözlemlemeye devam eder (aşağıdaki bölümüne bakın).

## <a name="enable-multiple-namespace-support"></a>Birden çok ad alanı desteğini etkinleştir
Birden çok ad alanı desteğini etkinleştirmek için:
1. [Helm-config. YAML](#sample-helm-config-file) dosyasını aşağıdaki yollarla değiştirin:
   - `watchNamespace` anahtarını tamamen [helk-config konumundan silin. YAML](#sample-helm-config-file) -agic tüm ad alanlarını gözlemleyecek
   - `watchNamespace` boş bir dizeye ayarla-AGIC, tüm ad alanlarını gözlemleyecek
   - virgülle ayrılmış birden çok ad alanı Ekle (`watchNamespace: default,secondNamespace`)-AGIC bu ad alanlarını özel olarak gözlemleyecek
2. Heln şablon değişikliklerini Uygula: `helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure`

Birden çok ad alanını gözlemleyebilme özelliği ile dağıtıldıktan sonra, AGIC şunları sağlar:
  - tüm erişilebilir ad alanlarından giriş kaynaklarını listeleyin
  - `kubernetes.io/ingress.class: azure/application-gateway` açıklanmakta olan giriş kaynaklarına Filtre Uygula
  - Birleşik [Application Gateway yapılandırması](https://github.com/Azure/azure-sdk-for-go/blob/37f3f4162dfce955ef5225ead57216cf8c1b2c70/services/network/mgmt/2016-06-01/network/models.go#L1710-L1744) oluştur
  - yapılandırmayı [ARM](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) aracılığıyla ilişkili Application Gateway uygulama

## <a name="conflicting-configurations"></a>Çakışan yapılandırma
Birden çok gösterilemez [ınress kaynağı](https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource) , tek bir Application Gateway için çakışan yapılandırmaların oluşturulmasını sağlayabilir. (Örnek için aynı etki alanını talep eden iki giriş.)

Hiyerarşinin en **üstünde (IP** adresi, bağlantı noktası ve ana bilgisayar) ve **yönlendirme kuralları** (bağlama dinleyicisi, arka uç havuzu ve http ayarları), birden çok ad alanı/giriş tarafından oluşturulup paylaşılabilir.

Diğer yandan yollar, arka uç havuzları, HTTP ayarları ve TLS sertifikaları yalnızca bir ad alanı tarafından oluşturulabilir ve yinelemeler kaldırılır.

Örneğin, aşağıdaki yinelenen giriş kaynakları tanımlı `staging` ad alanlarını göz önünde bulundurun ve `www.contoso.com`için `production`:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: staging
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: websocket-ingress
  namespace: production
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: www.contoso.com
      http:
        paths:
          - backend:
              serviceName: web-service
              servicePort: 80
```

İki giriş kaynağı, ilgili Kubernetes ad alanlarına yönlendirilmek için `www.contoso.com` trafiği talep ettiğinden, trafiğe yalnızca bir arka uç hizmet verebilir. AGIC, kaynaklardan biri için "ilk olarak ilk kez sunulan" temelinde bir yapılandırma oluşturur. Aynı anda iki giriş kaynağı oluşturulursa, alfabede daha önceki bir işlem öncelikli olur. Yukarıdaki örnekte yalnızca `production` giriş için ayarları oluşturabileceksiniz. Application Gateway, aşağıdaki kaynaklarla yapılandırılacak:

  - Dinleyici: `fl-www.contoso.com-80`
  - Yönlendirme kuralı: `rr-www.contoso.com-80`
  - Arka uç havuzu: `pool-production-contoso-web-service-80-bp-80`
  - HTTP ayarları: `bp-production-contoso-web-service-80-80-websocket-ingress`
  - Durum araştırması: `pb-production-contoso-web-service-80-websocket-ingress`

*Dinleyici* ve *yönlendirme kuralı*haricinde oluşturulan Application Gateway kaynakları, oluşturuldukları ad alanının (`production`) adını içerir.

İki giriş kaynağı AKS kümesi içinde farklı noktalarda tanıtıldığında, AGC 'nin Application Gateway yeniden yapılandırdığı ve `namespace-B` trafiği `namespace-A`'e yeniden yönlendirdiği bir senaryoya karşı bitmesi olasıdır.

Örneğin, önce `staging` eklediyseniz, AGIC, trafiği hazırlama arka uç havuzuna yönlendirmek için Application Gateway yapılandırır. Daha sonraki bir aşamada `production` giriş girişi, Application Gateway yeniden programlamamasına neden olur ve bu da trafiği `production` arka uç havuzuna yönlendirmeye başlar.

## <a name="restrict-access-to-namespaces"></a>Ad alanlarına erişimi kısıtlama
Varsayılan olarak AGIC, Application Gateway herhangi bir ad alanı içinde açıklamalı giriş temelli olarak yapılandırır. Bu davranışı sınırlandırmak istiyorsanız aşağıdaki seçeneklere sahip olursunuz:
  - ad alanlarını, açıkça tanımlayarak ad alanlarını sınırlayın, [helk-config. YAML](#sample-helm-config-file) içinde `watchNamespace` YAML anahtarı aracılığıyla gözlemelidir
  - AGIC 'i belirli ad alanlarıyla sınırlamak için [rol/RoleBinding](https://docs.microsoft.com/azure/aks/azure-ad-rbac) kullanın

## <a name="sample-helm-config-file"></a>Örnek HELI yapılandırma dosyası
```yaml
    # This file contains the essential configs for the ingress controller helm chart

    # Verbosity level of the App Gateway Ingress Controller
    verbosityLevel: 3
    
    ################################################################################
    # Specify which application gateway the ingress controller will manage
    #
    appgw:
        subscriptionId: <subscriptionId>
        resourceGroup: <resourceGroupName>
        name: <applicationGatewayName>
    
        # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
        # This prohibits AGIC from applying config for any host/path.
        # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
        shared: false
    
    ################################################################################
    # Specify which kubernetes namespace the ingress controller will watch
    # Default value is "default"
    # Leaving this variable out or setting it to blank or empty string would
    # result in Ingress Controller observing all acessible namespaces.
    #
    # kubernetes:
    #   watchNamespace: <namespace>
    
    ################################################################################
    # Specify the authentication with Azure Resource Manager
    #
    # Two authentication methods are available:
    # - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
    armAuth:
        type: aadPodIdentity
        identityResourceID: <identityResourceId>
        identityClientID:  <identityClientId>
    
    ## Alternatively you can use Service Principal credentials
    # armAuth:
    #    type: servicePrincipal
    #    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>
    
    ################################################################################
    # Specify if the cluster is RBAC enabled or not
    rbac:
        enabled: false # true/false
    
    # Specify aks cluster related information. THIS IS BEING DEPRECATED.
    aksClusterConfiguration:
        apiServerAddress: <aks-api-server-address>
    ```

